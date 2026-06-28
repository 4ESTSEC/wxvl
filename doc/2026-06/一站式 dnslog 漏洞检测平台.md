#  一站式 dnslog 漏洞检测平台  
原创 biabai
                    biabai  白白学安全   2026-06-28 19:53  
  
一站式   
dnslog  
 漏洞检测平台  
  
DNSLog · WebLog · 攻击链时间线 · Burp 风格轮询 API · 实时通知  
  
   
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/p5fhoJ8nULK7icnZiayqicAGFmibfV6C6CmicL2NCf4aZR6ESAtfTrVQlBP9GOy0fluD3xLnmTYzpibDYzFpoicJuiaichWCJsEk4vMTfLLbTBbQ6DN8/640?wx_fmt=png "")  
  
▲ 平台登录页：支持账号密码登录，HTTPS 访问  
  
在渗透测试和漏洞挖掘里，很多漏洞是「无回显」的——SSRF、盲注、Log4Shell、Fastjson、XXE…… 目标被打中了，但页面上什么都看不到。这时候就需要一个   
dnslog  
（带外，Out-of-Band）检测平台，让目标主动「向外发一条请求」，我们在平台侧抓到这条请求，就能确认漏洞存在、甚至把外带的数据捞回来。  
  
本平台是一套自建的   
dnslog  
 检测系统，集成 DNS / HTTP / LDAP / RMI / MySQL / FTP 多协议外带捕获，并在原始记录之上做了利用类型识别、外带数据解码、IP 归属富化、攻击链关联、实时通知等增强能力。下面用操作截图逐个介绍。  
  
一、总览看板：一眼掌握全局  
  
登录后进入总览看板，顶部展示总日志数、今日日志、活跃子域名等核心指标；页面同时提供「生成专属子域名」和常用 Payload 速查表，复制即用。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/p5fhoJ8nULJYibG6T5PMLF13n1tk0IJkXXmsKIunGWRy0ibXgQfLeqnRc3ArWsnuvZdpqOlvVOibtprSUGjtD3sdclVU4NBlgAGwRkTwMa8aXo/640?wx_fmt=png "")  
  
▲ 总览看板：统计指标 + 子域名管理 + Payload 速查  
  
使用方法很简单：复制一条你的专属子域名（形如 xxx.  
dnslog  
.wlog.fun），把它塞进你要测试的 Payload 里，目标一旦触发，记录就会实时出现在下面的 DNSLog / WebLog 列表中。  
  
二、DNSLog：DNS 外带记录 + 自动解码 + 来源富化  
  
DNSLog 是   
dnslog  
 检测最常用的通道。任何对你子域名的 DNS 查询都会被完整记录：完整域名、来源解析器 IP、地理位置、记录类型、记录值、时间。在此基础上平台额外做了三件事：  
  
·  
利用类型自动标注：根据 Payload 特征识别 Log4Shell/JNDI、Fastjson、SSRF、XXE、SQLi 盲注等。  
  
·  
外带数据自动解码：DNS 标签中的 base32 / hex 编码自动还原成明文（盲注常用这种方式把数据带出来）。  
  
·  
来源指纹归属：自动判别请求来自扫描器、云厂商还是真实目标。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/p5fhoJ8nULJsyCMJAicUWJhLbKqbotMKKk40RcUIuKv6wv0GG0bJSjoIX6bxQMYJX8m6tsHcxuYqUibPx4MNLadcVE6RZH4Uu9fZ0ck57PVao/640?wx_fmt=png "")  
  
▲ DNSLog 列表：利用类型、解码数据、来源等富化列一目了然  
  
三、WebLog：HTTP 外带记录 + 请求回显  
  
当目标发起 HTTP 回连时，记录进入 WebLog：请求方法、完整路径、来源 IP、地理位置、利用类型、解码数据、来源、时间一应俱全。HTTP 通道还会把 base64 编码的外带数据自动解码（例如把 admin:pass 这种凭据捞出来）。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/p5fhoJ8nULJaWuR4H2QwibqfweTIDFt9eAUGDvJJCNn4afgmjyiaM4BwsURoCSoVeFwQj6f3rp8u4fNBJ70pyibD69vEFYOb2IM65opdcNibyTM/640?wx_fmt=png "")  
  
▲ WebLog 列表：SQLi 盲注 / XXE / SSRF / Fastjson 等被自动标注，base64 数据已解码  
  
平台的 HTTP   
dnslog  
 服务还会在响应体里「回显收到的完整请求」（请求行 + 全部请求头 + body），方便你核对外带数据是否完整到达。  
  
四、攻击链时间线：把零散命中串成一条线  
  
一次完整的利用，往往同时触发 DNS、HTTP、LDAP 多个通道的回连。平台按子域名 token 把它们聚合成「攻击链」，列表里能看到每条链涉及的协议、利用类型、命中数（DNS/HTTP/其它）、来源和最近活跃时间。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/p5fhoJ8nULKeAzDLIZic7NZ7wMOIQhZ2TAiaF8OibCpiakKpwg6XInnuPB0Za2cRia6FcQAoRrn4ksmlqM9W8ib8PjNX5WsZU1rLXicXicuom9Z4h3Y/640?wx_fmt=png "")  
  
▲ 攻击链列表：按子域名 token 聚合，协议与利用类型一栏清晰  
  
点击任意一条链，右侧抽屉展开完整时间线，按时间顺序还原整个攻击过程——从 DNS 解析、HTTP 回连，到 LDAP/JNDI 回连，一条时间轴全部串起来。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/p5fhoJ8nULIAxJgrdBEl9l0fULMV5iaLE9hgL5x4748QdiaASgAd17XmgJYjBVgw2MPEdOe26exosjnYZ018FpzuEaQyhRNDQ3Daiaxa7tVWpU/640?wx_fmt=png "")  
  
▲ 攻击链时间线：DNS → HTTP → LDAP 按时间顺序还原  
  
时间线里每个节点都带富化信息：利用类型标签、解码后的明文数据（[base32]/[hex]/[base64]）、以及来源 IP 的 ASN / 归属地，证据链非常完整。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/p5fhoJ8nULIZahbBrMEqpWxIYw5wiaJr02aINJzIMiaib2VAdRyneAG24Lr5FMIA9CV8DQ27WRV5z0HEMoEXwQicBXjpON1j5DoKguI5EyHHTLs/640?wx_fmt=png "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/p5fhoJ8nULJichTb1uHicg19VPfyvIwdiapR69DLgbSPYK2tZINFXyqcCIlicOD1Gic9yS2j7wAf9gXOg6iawTtTVCq4cWsiaypgGPPicjrYKq20TYU/640?wx_fmt=png "")  
  
▲ 时间线细节：利用类型、解码明文、ASN 归属一应俱全  
  
五、API 与 Burp 风格轮询：对接你的工具链  
  
平台提供完整的 REST API，支持 JWT 与 API Key 两种鉴权方式，可以把   
dnslog  
 检测能力接入到自己的脚本和工具里。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/p5fhoJ8nULIsz2m28icO8QPneznknML05URe2Le2bEa7sqDsib97ibhrucIzl8ILKwqC5ezicu5nKdqvUtBtHkULiaTRibUOfMWdpQLufcln9puZw/640?wx_fmt=png "")  
  
▲ API 文档：DNS/HTTP 日志、子域名、总览统计、盲打检测等接口  
  
其中最实用的是 Burp Collaborator 风格的轮询接口 /api/v1/poll：工具持续轮询即可增量拉取新增的   
dnslog  
 命中（DNS/HTTP/LDAP/RMI/MySQL/FTP 统一返回），通过游标只取新数据，无需手动刷新面板。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/p5fhoJ8nULKTia9RhFmthJLvqKWGibMMbFSGzFPq4zqDF4IjERL1iaxS4j2v6tTPbq3nPmlEVJPTO11HtB0GNuQsFoJ9Cs8lYZ9vreSKF5QMm0/640?wx_fmt=png "")  
  
▲ 轮询接口 /api/v1/poll：游标增量拉取，附带轮询脚本示例  
  
六、DNS 重绑定：绕过 SSRF 校验打内网  
  
平台内置 DNS 重绑定（DNS Rebinding）能力：第一次解析返回合法的公网 IP 通过校验，由于 TTL=0 不缓存，应用真正发起请求时再次解析立即返回内网 IP，从而绕过 SSRF 的 IP 白名单校验。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/p5fhoJ8nULI7wQxeFht4BzdNZf8kcxqXpJkOmy99GVosVibNfy6UGzsAvu9VqNEQotJZsl0MPWJlhyE6kI56CGLzCTuaLdicOm9Wwejft29Qg/640?wx_fmt=png "")  
  
▲ DNS 重绑定：可配置首次/后续解析 IP，并支持把双 IP 编码进域名的动态生成器  
  
七、实时通知：命中即推送，不用一直盯面板  
  
配置通知器后，任意子域名被打中都会实时推送消息，支持钉钉、企业微信、飞书、Telegram、Slack、邮件、自定义 Webhook。通知管理页可查看发送成功/失败统计，并对每个通知器单独测试。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/p5fhoJ8nULI3Xhgg0ZiaicDiaMRQtPibiaTHygYjHIKibwmZARGfUZdy6H8AMlCJXJib0jzILkXeDswhAib9EBicLiaYvwTicShcqjYx9cFbTLye1Zkz4g/640?wx_fmt=png "")  
  
▲ 通知管理：多渠道通知器，发送统计与单独测试  
  
以钉钉为例，平台支持安全设置中的「加签」模式（HMAC-SHA256 签名），填入 Webhook 与 SEC 加签密钥即可安全推送。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/p5fhoJ8nULJJKG0uhAmpKNVDy69DsbYsxuLnMqRxD8U3k34T0XXxgJia9OmyzuWcvUvCvKP85JgPC0x03x0wnE8ulZ2LQjCicVHZdsXbVxURc/640?wx_fmt=png "")  
  
▲ 钉钉通知配置：支持「加签密钥」，满足钉钉机器人安全要求  
  
写在最后  
  
从一条 DNS 查询到一条完整的攻击链证据，这套   
dnslog  
平台把「无回显漏洞检测」做成了开箱即用的工作流：  
  
·  
多协议外带捕获：DNS / HTTP / LDAP / RMI / MySQL / FTP  
  
·  
智能增强：利用类型标注、外带数据解码、来源指纹、GeoIP+ASN+rDNS 富化  
  
·  
攻击链关联：把多通道命中串成一条时间线  
  
·  
工具对接：REST API + Burp 风格轮询  
  
·  
实时通知：钉钉/企微/飞书 等多渠道推送  
  
   
  
—— 以上功能均已部署上线并实测通过 ——  
  
仅技术分享,如果出事与本人无关  
  

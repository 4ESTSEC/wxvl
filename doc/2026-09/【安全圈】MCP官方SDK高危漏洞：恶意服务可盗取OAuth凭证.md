#  【安全圈】MCP官方SDK高危漏洞：恶意服务可盗取OAuth凭证  
 安全圈   2026-09-29 11:00  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/aBHpjnrGylgOvEXHviaXu1fO2nLov9bZ055v7s8F6w1DD1I0bx2h3zaOx0Mibd5CngBwwj2nTeEbupw7xpBsx27Q/640?wx_fmt=other&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1 "")  
  
  
**关键词**  
  
  
  
漏洞  
  
  
**事件核心要点：**  
作为大语言模型与外部工具交互的行业核心标准，Model Context Protocol (MCP) 官方 Python SDK 被曝出凭证截获高危缺陷。当客户端通过 HTTP 协议连接未经充分信任的外部 MCP 服务时，恶意服务端可通过伪造授权元数据，直接劫持客户端的 Client Secret、授权码及 PKCE 动态校验密钥，从而获取对应真实云服务的完整 API 访问令牌。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/sbq02iadgfyHZLkQTtoxMGAMLqoFG0j4lsbjl7APSJWOHY1nXM7icjY8XfD0iapksLrd1eJJkiciblDdtH67bIoJ3ibI5ibLCEqXhgHib8Aibuj4yyfI/640?wx_fmt=other&from=appmsg "")  
## 🚨 凭证信任重定向：MCP SDK 元数据校验缺失  
  
安全机构 Cycode 的最新漏洞审计报告披露，在官方 mcp  
 Python 软件开发工具包中，OAuth 认证握手逻辑存在重大认证终结点混淆隐患。  
  
MCP 客户端在向 MCP 服务发起连接请求并需要身份登录时，会首先向远端服务查询其授权服务器（Authorization Server）的具体地址。在修复前的旧版本中，SDK **完全信任了远端服务返回的重定向响应与元数据内容**  
，并未核验该认证地址是否与预期白名单匹配。  
```
[交互流1] AI Agent / 客户端尝试连接第三方不可信 MCP Server 
[交互流2] 客户端向 MCP Server 发起探测："请提供认证鉴权服务器地址" 
[恶意响应] 恶意 Server 虚构响应，将 Token Endpoint 指向黑客自建监听机 
[凭证外泄] 客户端向该黑客端点发送: client_secret + auth_code + PKCE_verifier 
[权限窃取] 黑客服务器转头携带这些真实凭证向正规 IdP 换取正式 Access Token
```  
  
由于 PKCE 机制原本用于单次授权码防重放，但当客户端将包含一次性校验明文（code_verifier  
）与长期密钥（client_secret  
）一并打包发送给恶意端点时，PKCE 的防窃取设计彻底失效。黑客可立刻向真实提供商置换获得全权限 Access Token。  
## 🔍 影响范围与组件清单：四大认证 Provider 均中招  
  
漏洞评级因交互模式而异：在自动化运行的机器对机器（M2M）后台服务模式下，漏洞 CVSS 评分为 **7.5（高危）**  
；在包含前端交互确认的模式下评分为 6.5。截至 9 月 29 日，该缺陷尚未正式分配 CVE 编号。  
  
**受影响配置条件：**  
- 基于 HTTP 协议运行的 MCP 客户端程序；  
  
- 使用了以下 Provider 之一：OAuthClientProvider  
、ClientCredentialsOAuthProvider  
、PrivateKeyJWTOAuthProvider  
，或已弃用的 1.x RFC7523OAuthClientProvider  
；  
  
- 客户端会主动连接非完全自主可控的外部或第三方 MCP 服务。  
  
**不受影响的情形：**  
使用官方 SDK 构建的 MCP 服务端应用、通过本地标准输入输出（stdio 管道）通信的本地客户端、以及客户端自身已预先绑定静态令牌而无需动态协商的场景。  
## 🛡️ 官方处置方案与版本升级指引  
  
官方维护团队目前已在 GitHub 及 PyPI 代码仓库中推送了双分支紧急补丁，重构了元数据校验机制。升级后，客户端会在发起凭证交换前严格判定目标授权服务的合法性，若发现端点偏离预期域名即刻主动中断握手。  
  
📦 修复版本对照与动作清单：  
  
• **1.x 用户**  
：立即升级至 mcp >= 1.30.0  
  
• **2.x 用户**  
：立即升级至 mcp >= 2.2.0  
  
• **凭证轮换**  
：针对曾经连接过第三方不可信公共 MCP 站点的客户端，必须立即在对应云服务中撤销并轮换相关 client_secret  
。  
  
  
   END    
  
  
阅读推荐  
  
  
  
  
[【安全圈】苹果崩了](https://mp.weixin.qq.com/s?__biz=MzIzMzE4NDU1OQ==&mid=2652079150&idx=1&sn=f25dd80dbe08777b632a88ab45425b84&scene=21#wechat_redirect)  
  
  
  
[【安全圈】Citrix曝双严重零日漏洞](https://mp.weixin.qq.com/s?__biz=MzIzMzE4NDU1OQ==&mid=2652079150&idx=2&sn=d2d2bf488a6be5cf6881f712570e2c6d&scene=21#wechat_redirect)  
  
  
  
[【安全圈】Cloudflare容器曝隔离漏洞：残余数据可跨租户窃取](https://mp.weixin.qq.com/s?__biz=MzIzMzE4NDU1OQ==&mid=2652079150&idx=3&sn=c743cb0599f06a695fd39f68c18dae43&scene=21#wechat_redirect)  
  
  
  
[【安全圈】OpenAI 研究代理曾将用户图片传到第三方图床，已发现 53 次](https://mp.weixin.qq.com/s?__biz=MzIzMzE4NDU1OQ==&mid=2652079129&idx=1&sn=7df0283a6c5d51694b17203ac0b35c59&scene=21#wechat_redirect)  
  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/aBHpjnrGylgeVsVlL5y1RPJfUdozNyCEft6M27yliapIdNjlcdMaZ4UR4XxnQprGlCg8NH2Hz5Oib5aPIOiaqUicDQ/640?wx_fmt=gif "")  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/aBHpjnrGylgeVsVlL5y1RPJfUdozNyCEDQIyPYpjfp0XDaaKjeaU6YdFae1iagIvFmFb4djeiahnUy2jBnxkMbaw/640?wx_fmt=png "")  
  
**安全圈**  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/aBHpjnrGylgeVsVlL5y1RPJfUdozNyCEft6M27yliapIdNjlcdMaZ4UR4XxnQprGlCg8NH2Hz5Oib5aPIOiaqUicDQ/640?wx_fmt=gif "")  
  
  
←扫码关注我们  
  
**网罗圈内热点 专注网络安全**  
  
**实时资讯一手掌握！**  
  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/aBHpjnrGylgeVsVlL5y1RPJfUdozNyCE3vpzhuku5s1qibibQjHnY68iciaIGB4zYw1Zbl05GQ3H4hadeLdBpQ9wEA/640?wx_fmt=gif "")  
  
**好看你就分享 有用就点个赞**  
  
**支持「****安全圈」就点个三连吧！**  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/aBHpjnrGylgeVsVlL5y1RPJfUdozNyCE3vpzhuku5s1qibibQjHnY68iciaIGB4zYw1Zbl05GQ3H4hadeLdBpQ9wEA/640?wx_fmt=gif "")  
  
  

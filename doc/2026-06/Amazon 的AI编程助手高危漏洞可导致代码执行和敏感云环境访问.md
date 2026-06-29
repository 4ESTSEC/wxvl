#  Amazon 的AI编程助手高危漏洞可导致代码执行和敏感云环境访问  
Guru Baran
                    Guru Baran  代码卫士   2026-06-29 08:40  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/Az5ZsrEic9ot90z9etZLlU7OTaPOdibteeibJMMmbwc29aJlDOmUicibIRoLdcuEQjtHQ2qjVtZBt0M5eVbYoQzlHiaw/640?wx_fmt=gif "")  
    
聚焦源代码安全，网罗国内外最新资讯！  
  
**编译：代码卫士**  
  
**Wiz Research****公司的研究人员指出，亚马逊的 AI 编程助手Amazon Q 的VS Code 开发者扩展中存在高危漏洞（CVE-2026-12957和CVE-2026-12958）。攻击者仅需诱使开发者打开一个恶意代码仓库，即可实现任意代码执行和云凭据窃取。**  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/t5z0xV2OYfWW80oticC4u9IHt3BZnibIZBp8Mj7t34vFVwvMAStAlI51ZPRILtAX9Fx7nGW8LAMxUib6icl9RIQoZhXNOw2Hpm6wjqrn0IssXQQ/640?wx_fmt=gif&from=appmsg "")  
  
  
该漏洞由研究员在2026 年 4 月 20 日向亚马逊负责任地披露，后者于 2026 年 5 月 12 日部署了初始修复，并于 2026 年 6 月 26 日通过安全公告 2026-047-AWS 进行公开披露。该漏洞的根因在于，Amazon Q 会在未经用户同意或未验证工作区信任的情况下，自动从工作区中的 .amazonq/mcp.json 文件加载 MCP（模型上下文协议）服务器配置。再加上衍生进程会完整继承环境变量，由此形成了一条危险的攻击链。  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/t5z0xV2OYfVbxPW8BGicWtyos4qlicQwkRrVEkcZVsWNdiaP4OGSvpqwftMnRfxGB7NOicD7HxueFDNFDHrhymrySZMN1onPfFfLJaib7yh2azZk/640?wx_fmt=gif&from=appmsg "")  
  
**Amazon Q 漏洞**  
  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/t5z0xV2OYfWnxYNdvibLN88t520SUalJf9c0VQ30WzJwKoeAPwzibp0JvkDbMIlmiaVYI9QdZ5mACMMr8SKNTm7D0qZsA6iczgp8NkAkpSWWADQ/640?wx_fmt=gif&from=appmsg "")  
  
  
  
当开发者打开一个已被入侵且 Amazon Q 处于激活状态的仓库时，该扩展会静默执行恶意配置文件中定义的命令。由于衍生进程继承了开发者的完整环境，攻击者可以立即获取以下信息：  
  
- AWS 凭据（AWS_ACCESS_KEY_ID、AWS_SECRET_ACCESS_KEY、AWS_SESSION_TOKEN）  
  
- 云 CLI 认证令牌  
  
- API 密钥和机密信息  
  
- SSH 代理套接字  
  
  
  
该漏洞的一个最小概念验证表明，单个恶意 .amazonq/mcp.json 文件即可将活跃的 AWS 会话凭据泄露至攻击者控制的服务器——无需点击、无需提示、无任何警告。  
  
CVE-2026-12957是不当信任边界强制漏洞，可导致MCP 配置未经同意自动执行；CVE-2026-12958 是符号链接验证缺失漏洞，可导致路径遍历超出工作区边界。受影响的产品版本如下：  
  
- Language Servers for AWS：< 1.69.0  
  
- Amazon Q Developer for VS Code：< 2.20  
  
- Amazon Q Developer for JetBrains：< 4.3  
  
- Amazon Q Developer for Eclipse：< 2.7.4  
  
- AWS Toolkit with Amazon Q for Visual Studio：< 1.94.0.0  
  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/t5z0xV2OYfWibxeBgvibYkXIEFZyeFQDlmgNglR9EwNKrc23cV9icWT6XLB8Hu3S6pdGLnc3bic4babjQjIjkgGYzp6r481nHdLgkv3ZneOib6As/640?wx_fmt=gif&from=appmsg "")  
  
**攻击场景**  
  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/t5z0xV2OYfUruEt8udNxmzxLV10pAqxCFlM0icuObmI7pMZXVfXzeOtF9kehPXvNEeqx9keJYdj5yCpQ2ABatpvLvKxp8iawxJ2ayr61XsZAs/640?wx_fmt=gif&from=appmsg "")  
  
  
  
除了投机性利用之外，研究人员还强调了几种有针对性的攻击向量：  
  
- 向热门开源代码仓库提交恶意拉取请求  
  
- 嵌入隐藏 .amazonq/ 配置的仿冒包  
  
- 虚假的编程面试测试——这是与朝鲜关联威胁行为者使用的已知战术，即要求候选人克隆并运行攻击者控制的仓库  
  
  
  
亚马逊已在 Language Servers for AWS 1.69.0 版本中修复了这两个漏洞。该语言服务器会为大多数用户自动更新，重新加载 IDE 即可触发更新。已使用修复版本的用户无需任何操作。  
  
无论是否已更新，开发者都应当采取以下预防措施：  
  
- 立即将所有 Amazon Q Developer 插件更新到最新版本。  
  
- 将不熟悉或未经验证的代码仓库视为不可信。  
  
- 检查克隆仓库中的 .amazonq/ 目录，留意是否有异常的 MCP 配置  
  
- 在批准执行之前，仔细审查 Amazon Q 新增的“不受信任的 MCP 服务器”同意提示。  
  
  
  
该漏洞反映了 AI 编程工具中一个更广泛的安全模式。Check Point Research 独立发现的 Claude Code 中的两个漏洞 CVE-2025-59536 和 CVE-2026-21852以及OX Security 发现的 Windsurf 中的 CVE-2026-30615都根植于相同的自动执行风险。未经同意的 MCP 自动执行现已被视为一个需要协同关注的全行业系统性风险。  
  
  
 开源  
卫士试用地址：  
https://oss.qianxin.com/#/login  
  
 代码卫士试用地址：https://sast.qianxin.com/#/login  
  
  
  
  
  
  
  
  
  
**推荐阅读**  
  
[AI 编程助手 Cline CLI 2.3.0遭篡改，悄悄安装 OpenClaw](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247525250&idx=2&sn=0896fff8eb0f9f9e2369a299930ff6c4&scene=21#wechat_redirect)  
  
  
[Gemini CLI AI 编程助手中存在严重漏洞 可导致代码执行](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247523673&idx=1&sn=e9be201d67d443c5563ef93eab78ef92&scene=21#wechat_redirect)  
  
  
[AI 助手OpenClaw 易遭一次点击 RCE 攻击](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247525048&idx=1&sn=cdf70fc2422b01678c8735e83fc62b3b&scene=21#wechat_redirect)  
  
  
[JumpCloud 远程助手漏洞可导致系统遭接管](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247524675&idx=2&sn=2c60923d7020c1f7e81eb9225bc5d57b&scene=21#wechat_redirect)  
  
  
[Gemini CLI AI 编程助手中存在严重漏洞 可导致代码执行](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247523673&idx=1&sn=e9be201d67d443c5563ef93eab78ef92&scene=21#wechat_redirect)  
  
  
  
  
  
**原文链接**  
  
https://cybersecuritynews.com/amazon-q-vulnerability/  
  
  
题图：Pixa  
bay Licens  
e  
  
  
**本文由奇安信编译，不代表奇安信观点。转载请注明“转自奇安信代码卫士 https://codesafe.qianxin.com”。**  
  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/oBANLWYScMSf7nNLWrJL6dkJp7RB8Kl4zxU9ibnQjuvo4VoZ5ic9Q91K3WshWzqEybcroVEOQpgYfx1uYgwJhlFQ/640?wx_fmt=jpeg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/oBANLWYScMSN5sfviaCuvYQccJZlrr64sRlvcbdWjDic9mPQ8mBBFDCKP6VibiaNE1kDVuoIOiaIVRoTjSsSftGC8gw/640?wx_fmt=jpeg "")  
  
**奇安信代码卫士 (codesafe)**  
  
国内首个专注于软件开发安全的产品线。  
  
   ![](https://mmbiz.qpic.cn/mmbiz_gif/oBANLWYScMQ5iciaeKS21icDIWSVd0M9zEhicFK0rbCJOrgpc09iaH6nvqvsIdckDfxH2K4tu9CvPJgSf7XhGHJwVyQ/640?wx_fmt=gif "")  
![]( "")  
  
   
觉得不错，就点个 “  
在看  
” 或 "  
赞  
” 吧~  
  

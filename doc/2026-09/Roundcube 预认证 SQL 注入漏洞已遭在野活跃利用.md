#  Roundcube 预认证 SQL 注入漏洞已遭在野活跃利用  
Ravie Lakshmanan
                    Ravie Lakshmanan  代码卫士   2026-09-28 09:08  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/Az5ZsrEic9ot90z9etZLlU7OTaPOdibteeibJMMmbwc29aJlDOmUicibIRoLdcuEQjtHQ2qjVtZBt0M5eVbYoQzlHiaw/640?wx_fmt=gif "")  
    
聚焦源代码安全，网罗国内外最新资讯！  
  
编译：代码卫士  
  
**加拿大网络安全中心提醒称，****Roundcube Webmail****的一个已修复高危漏洞（****CVE-2026-48842****，****CVSS****评分****8.1****）正遭活跃利用。**  
  
该漏洞是位于 Roundcube Webmail 1.6.x（低于 1.6.16）和 1.7.x（低于 1.7.1）版本的 virtuser_query 插件中的一个认证前 SQL 注入漏洞。该漏洞源于 preg_replace() 反斜杠转义绕过，可导致攻击者在未经身份认证的情况下注入任意 SQL 语句。  
  
SentinelOne 公司表示：“未经身份认证的攻击者可以通过 virtuser_query 插件向 Roundcube 的数据库后端注入 SQL，可能暴露邮件账户凭据和存储的邮件。”  
  
Roundcube 已于 2026 年 5 月在 1.6.16 和 1.7.1 版本发布补丁。在本周分享的更新中，加拿大网络安全中心援引开源报告称，该漏洞正遭活跃利用，但未披露更多利用攻击活动细节。  
  
Shadowserver 基金会的数据显示，超过 523000 个 Roundcube 实例被暴露在互联网上，截至 2026 年 9 月 23 日，其中 10 个被标记为易受攻击主机。Roundcube 中的漏洞一直是威胁行动者青睐的目标，他们试图收集敏感电子邮件通信。2026 年 7 月，Proofpoint 公司表示发现有攻击者利用 Roundcube 中已知安全漏洞来投递 web shell 或名为 VShell 的后利用工具。  
  
早在 2026 年 2 月，该产品中的另外两个漏洞（CVE-2025-49113 和 CVE-2025-68461）就被美国网络安全和基础设施安全局 (CISA) 标记为正遭活跃利用。  
  
  
代码卫士试用地址：https://sast.qianxin.com/  
  
开源卫士试用地址：https://oss.qianxin.com/  
  
  
  
  
  
  
  
  
  
**推荐阅读**  
  
[CISA将Erlang SSH 和 Roundcube 加入KEV清单](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247523250&idx=2&sn=245cd6553dde79a725f1afe15893f164&scene=21#wechat_redirect)  
  
  
[Roundcube Webmail XSS 漏洞被用于窃取登录凭据](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247521165&idx=2&sn=7bfb29f17ff7d1e5ee3a692d0325509c&scene=21#wechat_redirect)  
  
  
[利用Roundcube缺陷仅需发送一份邮件](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247485624&idx=1&sn=d78df4d6e24b0123f2e980628759ffe9&scene=21#wechat_redirect)  
  
  
  
  
  
**原文链接**  
  
https://thehackernews.com/2026/09/roundcube-pre-auth-sql-injection-flaw.html  
  
  
题图：Pixa  
b  
ay Licens  
e  
  
  
**本文由奇安信编译，不代表奇安信观点。转载请注明“转自奇安信代码卫士 https://codesafe.qianxin.com”。**  
  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/oBANLWYScMSf7nNLWrJL6dkJp7RB8Kl4zxU9ibnQjuvo4VoZ5ic9Q91K3WshWzqEybcroVEOQpgYfx1uYgwJhlFQ/640?wx_fmt=jpeg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/oBANLWYScMSN5sfviaCuvYQccJZlrr64sRlvcbdWjDic9mPQ8mBBFDCKP6VibiaNE1kDVuoIOiaIVRoTjSsSftGC8gw/640?wx_fmt=jpeg "")  
  
**奇安信代码卫士 (codesafe)**  
  
国内首个专注于软件开发安全的产品线。  
  
   ![](https://mmbiz.qpic.cn/mmbiz_gif/oBANLWYScMQ5iciaeKS21icDIWSVd0M9zEhicFK0rbCJOrgpc09iaH6nvqvsIdckDfxH2K4tu9CvPJgSf7XhGHJwVyQ/640?wx_fmt=gif "")  
![]( "")  
![]( "")  
  
   
觉得不错，就点个 “  
在看  
” 或 "  
赞  
”   
  

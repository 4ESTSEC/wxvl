#  SonicWall 提醒注意已遭利用的 SMA1000 0day 漏洞  
Sergiu Gatlan
                    Sergiu Gatlan  代码卫士   2026-09-02 12:09  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/Az5ZsrEic9ot90z9etZLlU7OTaPOdibteeibJMMmbwc29aJlDOmUicibIRoLdcuEQjtHQ2qjVtZBt0M5eVbYoQzlHiaw/640?wx_fmt=gif "")  
    
聚焦源代码安全，网罗国内外最新资讯！  
  
编译：代码卫士  
  
**SonicWall****公司提醒客户称，威胁行为者正在组合利用两个新的****SMA1000 0day****漏洞，实施远程代码执行攻击。**  
  
第一个漏洞是位于 SMA1000 设备 WorkPlace 接口中的严重程度最高的命令注入漏洞 (CVE-2026-83548)，源于服务器端请求伪造 (SSRF) 弱点。这一已遭活跃利用的 0day 漏洞链还针对 SMA1000 设备管理控制台中的一个命令注入漏洞 (CVE-2026-83549)，可使拥有管理员权限的攻击者在受影响设备上执行任意操作系统命令。  
  
SonicWall 在周二发布的安全公告中警告称：“SonicWall PSIRT 调查的一起案件表明，本通报中所述漏洞已遭活跃利用。我们强烈建议客户尽快升级到热修复版本，修复该漏洞。”  
  
这两个漏洞影响 SMA1000 6210、7210 和 8200v 型号，但不影响 SonicWall 防火墙上的 SSL-VPN 或 SMA 100 系列产品线。互联网安全监督机构 Shadowserver 目前追踪到超过 400 台暴露在公网上的 SMA1000 设备，不过其中一些可能已经针对该漏洞链打了补丁。  
  
SonicWall 督促所有客户将虚拟或物理 SMA1000 设备升级到最新的热修复版本。  
  
同时，该公司还建议管理员在检测到入侵指标 (IOCs) 时，重新镜像设备、更改所有用户和管理员密码，并重置 TOTP 令牌，但尚未分享有关这些正在进行的攻击的细节，也未公布调查期间发现的 IOC 列表。  
  
这类漏洞经常遭利用，因为 SMA1000 是大型企业、政府和关键基础设施组织使用的安全远程访问设备。今年 7 月，另外两个 SonicWall SMA1000 漏洞（CVE-2026-15409 和 CVE-2026-15410）曾在 0day 攻击中被利用数周，在易受攻击的 VPN 设备上安装定制恶意软件。上个月，美国网络安全和基础设施安全局（CISA）确认勒索软件团伙已经开始在野滥用这两个漏洞。去年 12 月，该公司还警告客户修复另一个 SMA1000 0day漏洞 (CVE-2025-40602)，黑客正在利用该漏洞链获取 root 权限。一个月前，SonicWall称国家黑客组织与一起 9 月份的安全入侵事件存在关联，该事件暴露了客户的防火墙配置备份文件。此前研究人员警告称，攻击者利用被盗凭据，入侵超过 100 个 SonicWall SSLVPN 账户。  
  
代码卫士试用地址：https://sast.qianxin.com/  
  
开源卫士试用地址：https://oss.qianxin.com/  
  
  
  
  
  
  
  
  
  
**推荐阅读**  
  
[SonicWall NetExtender 多个漏洞可用于以root身份写入任意文件](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247526978&idx=2&sn=43af008fe7c471a0e023d7056f53e66d&scene=21#wechat_redirect)  
  
  
[SonicWall：立即修复已遭0day攻击利用的 SMA1000 漏洞](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247526630&idx=1&sn=3f9411edee6fd9805cd5ac7a7991a3c2&scene=21#wechat_redirect)  
  
  
[速修复这个已遭利用的 SonicWall SMA1000 0day漏洞](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247524683&idx=1&sn=7224ee1419a26c8bb16d70f9eed840e6&scene=21#wechat_redirect)  
  
  
  
  
  
**原文链接**  
  
https://www.bleepingcomputer.com/news/security/sonicwall-warns-of-actively-exploited-sma1000-zero-day-flaws/  
  
  
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
” 吧~  
  

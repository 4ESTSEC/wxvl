#  SonicWall：注意SMA1000网关中的满分 SSRF 漏洞  
Sergiu Gatlan
                    Sergiu Gatlan  代码卫士   2026-10-08 08:06  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/Az5ZsrEic9ot90z9etZLlU7OTaPOdibteeibJMMmbwc29aJlDOmUicibIRoLdcuEQjtHQ2qjVtZBt0M5eVbYoQzlHiaw/640?wx_fmt=gif "")  
    
聚焦源代码安全，网罗国内外最新资讯！  
  
编译：代码卫士  
  
**SonicWall****已发布热修复方案，修复****SMA1000****系列设备中的一个****CVSS****满分服务端请求伪造****(SSRF)****漏洞****CVE-2026-102255****。**  
  
该漏洞位于  
 SMA1000 6210  
、  
7210   
和  
 8200v   
型号的  
 Appliance WorkPlace   
接口中，但不影响  
 SMA 100   
系列产品线，也不影响  
 SonicWall   
防火墙上运行的  
 SSL-VPN  
。该漏洞源于一个非预期的备用访问路径弱点，无权限的远程攻击者可通过低复杂度攻击活动利用该弱点。  
  
SonicWall   
公司解释称：  
“  
通过滥用该路径，远程未认证攻击者可能利用该漏洞，诱使设备代表其发起请求，并访问内部功能以及执行未授权操作。  
”  
尽管该公司尚未将这些漏洞标记为已被活跃利用，但敦促客户部署周二发布的热修复方案，以阻止针对其虚拟或物理设备的潜在攻击。该公司补充表示：“  
SonicWall   
强烈建议  
 SMA1000   
系列设备的用户升级到提到的已修复版本，修复这些漏洞。目前没有证据表明该版本中修复的任何漏洞正遭在野利用。  
”  
  
互联网安全威胁监测机构  
 Shadowserver   
目前追踪到超过  
 400   
台暴露在互联网上的  
 SMA1000   
设备，不过其中一些可能已经完成修复。  
  
尽管如此，但攻击者经常针对  
 SMA1000   
漏洞，因为它们影响企业级安全远程访问网关，政府机构、托管服务提供商和许多大型企业使用这些网关来提供对内部应用和公司网络的  
 VPN   
访问。自今年年初以来，威胁行动者已在  
 0day   
攻击中利用了多个  
 SMA1000   
安全漏洞。  
  
7   
月，位于  
SMA1000   
中的两个  
 0day   
漏洞（  
CVE-2026-15409   
和  
 CVE-2026-15410  
）被利用数周，在易受攻击的  
 VPN   
设备上安装定制的  
 Sou5  
、  
OrangeTail   
和  
 RootRun   
恶意软件；美国网络安全和基础设施安全局  
 (CISA)   
将这些攻击与勒索软件团伙进行了关联。  
  
上个月，  
SonicWall   
还提醒客户称，攻击者正在组合利用两个新的  
 0day   
漏洞（  
CVE-2026-83548   
和  
 CVE-2026-83549  
），在易受攻击的  
 SMA1000   
网关上执行远程代码。过去四年中  
CISA   
已将  
 19   
个  
 SonicWall   
漏洞添加到  
KEV   
清单中，其中  
 13   
个也遭勒索利用。  
  
代码卫士试用地址：https://sast.qianxin.com/  
  
开源卫士试用地址：https://oss.qianxin.com/  
  
  
  
  
  
  
  
  
  
**推荐阅读**  
  
[SonicWall 提醒注意已遭利用的 SMA1000 0day 漏洞](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247527014&idx=1&sn=2bf129add6d324acc41c93a2c5631a2d&scene=21#wechat_redirect)  
  
  
[SonicWall NetExtender 多个漏洞可用于以root身份写入任意文件](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247526978&idx=2&sn=43af008fe7c471a0e023d7056f53e66d&scene=21#wechat_redirect)  
  
  
[SonicWall：立即修复已遭0day攻击利用的 SMA1000 漏洞](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247526630&idx=1&sn=3f9411edee6fd9805cd5ac7a7991a3c2&scene=21#wechat_redirect)  
  
  
[速修复这个已遭利用的 SonicWall SMA1000 0day漏洞](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247524683&idx=1&sn=7224ee1419a26c8bb16d70f9eed840e6&scene=21#wechat_redirect)  
  
  
  
  
  
**原文链接**  
  
https://www.bleepingcomputer.com/news/security/sonicwall-warns-of-max-severity-ssrf-flaw-in-sma1000-gateways/  
  
  
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
  

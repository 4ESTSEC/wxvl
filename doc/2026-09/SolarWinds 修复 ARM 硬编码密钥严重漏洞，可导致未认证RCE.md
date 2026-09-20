#  SolarWinds 修复 ARM 硬编码密钥严重漏洞，可导致未认证RCE  
Ravie Lakshmanan
                    Ravie Lakshmanan  代码卫士   2026-09-20 07:04  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/Az5ZsrEic9ot90z9etZLlU7OTaPOdibteeibJMMmbwc29aJlDOmUicibIRoLdcuEQjtHQ2qjVtZBt0M5eVbYoQzlHiaw/640?wx_fmt=gif "")  
    
聚焦源代码安全，网罗国内外最新资讯！  
  
编译：代码卫士  
  
**SolarWinds****发布安全更新，修复了位于****Access Rights Manager (ARM)****中的一个****CVSS 8.8****分高危漏洞****CVE-2026-28326****。该漏洞若被成功利用，可能导致未经身份验证的远程代码执行漏洞。该漏洞影响****Access Rights Manager 2026.2****及之前的所有版本。**  
  
SolarWinds   
公司在  
 2026   
年  
 9   
月  
 17   
日发布的一份公告中表示：  
“SolarWinds Access Rights Manager   
受到一个未经身份验证的远程代码执行漏洞影响。该问题源于硬编码的静态密钥。  
”   
该漏洞由  
 Armadin   
公司的研究员  
 Kai Huang   
发现并报送，已在  
 ARM 2026.2.1   
中修复，不过  
SolarWinds   
未提及该漏洞是否已遭在野利用。  
  
近两个月前，  
SolarWinds   
公司修复  
 Web Help Desk (WHD)   
的一个严重漏洞  
CVE-2026-28323  
（  
CVSS   
评分为  
 9.8  
）。当启用  
 SAML 2.0   
身份验证方法时，可能导致  
 SAML   
身份验证绕过。  
  
另一个漏洞  
CVE-2026-28299  
（  
CVSS  
评分  
8.2  
）涉及拒绝服务，可能因内存不足导致  
 Web Help Desk   
服务器崩溃。这两个漏洞均已在  
 WHD 2026.2.1   
中修复。  
  
SolarWinds   
公司还发布了针对影响  
 Serv-U   
的  
 16   
个漏洞的修复方案，包括  
 CVE-2026-28302  
、  
CVE-2026-28304   
至  
 CVE-2026-28317  
、  
CVE-2026-28321  
、  
CVE-2026-28323  
，它们可能导致提权、远程代码执行以及创建管理员账户。  
  
  
代码卫士试用地址：https://sast.qianxin.com/  
  
开源卫士试用地址：https://oss.qianxin.com/  
  
  
  
  
  
  
  
  
  
**推荐阅读**  
  
[SolarWinds 严重漏洞可导致绕过 Web Help Desk SAML 登录认证机制](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247526788&idx=1&sn=31b126ba90fa61c7de56aff42e8761fd&scene=21#wechat_redirect)  
  
  
[SolarWinds 修复15个严重漏洞，可使攻击者获得 root 权限](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247526708&idx=1&sn=88286beec0ba9cb55a929f29a593a557&scene=21#wechat_redirect)  
  
  
[CISA：SolarWinds Serv-U 高危漏洞已遭利用](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247526239&idx=1&sn=b82af1b66a48bb2e151a8dbfe889d0b8&scene=21#wechat_redirect)  
  
  
[SolarWinds Serv-U 多个严重漏洞可用于提供服务器root权限](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247525116&idx=2&sn=05bb28ebbcccd3533d24a8ea4ee1f19f&scene=21#wechat_redirect)  
  
  
[SolarWinds 修复四个严重漏洞，可导致未认证RCE和认证绕过](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247525028&idx=3&sn=70181900e6f00cf38ce9655f395495d9&scene=21#wechat_redirect)  
  
  
  
  
  
**原文链接**  
  
https://thehackernews.com/2026/09/solarwinds-patches-arm-hard-coded-key.html  
  
  
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
  

#  Zimbra：速修复这个严重的 Web Client XSS 漏洞  
Sergiu Gatlan
                    Sergiu Gatlan  代码卫士   2026-07-13 07:09  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/Az5ZsrEic9ot90z9etZLlU7OTaPOdibteeibJMMmbwc29aJlDOmUicibIRoLdcuEQjtHQ2qjVtZBt0M5eVbYoQzlHiaw/640?wx_fmt=gif "")  
    
聚焦源代码安全，网罗国内外最新资讯！  
  
**编译：代码卫士**  
  
**Zimbra****安全团队督促客户修复影响用于访问 Zimbra Collaboration 套件的 Classic Web Client 中的一个严重 XSS 漏洞。该漏洞尚无CVE 编号。**  
  
Zimbra 是一款非常热门的邮件和协作软件套件，全球用户达数亿，包括数千家企业和数百家政府机构。这款基于 Ajax 的webmail 界面也被称为 “Classic UI”，它比 Zimbra 的现代 web 客户端更快，在加载大型邮件文件夹时需要更多的资源。  
  
上周二，Zimbra 发布10.1.19版本修复该漏洞。攻击者可通过特殊构造的邮件利用该漏洞。当用户打开邮件时，就会执行恶意代码。成功利用该漏洞可导致攻击者窃取会话数据、账号设置或邮箱信息。Zimbra 公司提醒称，“任何使用 Classic Web Client 的客户都应尽快升级至 ZCS v10.1.19，因为该漏洞仅影响 Classic Web Client 用户。我们强烈建议升级至该版本，保护环境安全。”  
  
Zimbra 公司尚未说明该漏洞是否已遭在野利用，不过该漏洞是由谷歌威胁分析团队发现的，该团队常常会标记由国家黑客组织部署且常用于攻击高风险个体如反对派政党、持不同政见人士以及记者等的 0day 漏洞。  
  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/t5z0xV2OYfU7zUqFkAOFjChB2NLmWcXxeiaPczgnkRkjZJhQ2jkC05x983UGiaHG69puP8uESQZTXqgI2BJzYZWPXZUqD8VK2xN2SKWDynMLM/640?wx_fmt=gif&from=appmsg "")  
  
**遭俄罗斯国家黑客组织针对**  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/t5z0xV2OYfVELdE6oR8MliaXqVRSAG5wa2UdftDHL6jmoSFnmUbSprBedibtQDZ0OaBhx8Kywx4DhxbtaFTvicOvf5sLFNgTTAdXibvQpzWLXSQ/640?wx_fmt=gif&from=appmsg "")  
  
  
  
近年来，Zimbra 公司的安全问题被指常遭俄罗斯国家黑客组织利用，攻击数千台易受攻击的服务器。  
  
例如，2023年2月，俄罗斯国家黑客组织 Winter Vivern 被指利用一个反射型XSS利用，攻陷 Zimbra webmail 门户，从与北约相关的组织机构和个人手中窃取邮件，这些人员包括政府官员、军事人员以及外交官等。2024年10月，英美网络机构提醒称 APT29 黑客组织滥用一个此前用于窃取邮件账号凭据的漏洞，“大规模”攻击 Zimbra 服务器。今年3月份，美国网络安全和基础设施安全局 (CISA) 要求联邦机构修复与 APT28 组织相关联的 Zimbra XSS 漏洞CVE-2025-66376，攻击乌克兰政府实体。4月份，非盈利性安全机构 Shadowserver 提醒称，暴露在网络的超过1.05万台 ZCS 实例仍然易受另外一个XSS漏洞CVE-2025-48700的影响。  
  
  
 开源  
卫士试用地址：  
https://oss.qianxin.com/#/login  
  
 代码卫士试用地址：https://sast.qianxin.com/#/login  
  
  
  
  
  
  
  
  
  
**推荐阅读**  
  
[Zimbra 紧急修复 Chat Proxy 配置中的严重SSRF漏洞](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247524221&idx=2&sn=098a25797ac4273cffca0263a429835e&scene=21#wechat_redirect)  
  
  
[Zimbra 紧急提醒手动修复已遭利用的0day](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247517057&idx=1&sn=3cc4f03b1925ae57509bdbf2be1dd327&scene=21#wechat_redirect)  
  
  
[黑客正在利用Zimbra ZCS中的未修复RCE漏洞](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247514145&idx=2&sn=de026e7969cb14c915002400434f0be1&scene=21#wechat_redirect)  
  
  
[UnRAR二进制中出现路径遍历缺陷，可导致在Zimbra上执行远程代码](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247512652&idx=1&sn=1f0239704b1c73ee0e257706adbbdb7b&scene=21#wechat_redirect)  
  
  
[开源邮件平台Zimbra 出现新漏洞，用户登录凭据可被盗](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247512333&idx=3&sn=8b2d3c40a59f28ad4ef19cca4e7de98c&scene=21#wechat_redirect)  
  
  
  
  
  
**原文链接**  
  
https://www.bleepingcomputer.com/news/security/zimbra-urges-customers-to-patch-critical-web-client-xss-flaw/  
  
  
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
  

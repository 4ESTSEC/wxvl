#  MikroTik RouterOS 严重漏洞可导致未认证根 RCE  
Do Sun
                    Do Sun  代码卫士   2026-09-30 16:00  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/Az5ZsrEic9ot90z9etZLlU7OTaPOdibteeibJMMmbwc29aJlDOmUicibIRoLdcuEQjtHQ2qjVtZBt0M5eVbYoQzlHiaw/640?wx_fmt=gif "")  
    
聚焦源代码安全，网罗国内外最新资讯！  
  
编译：代码卫士  
  
**2026****年****9****月****29****日，****CISA****发布了关于****MikroTik RouterOS****严重漏洞****CVE-2026-84411****（****CVSS****评分****9.8****）的公告****ICSA-26-272-06****。公告提到，单个精心构造的****HTTP****请求即可让未经身份验证的攻击者获得****root****代码执行权限。该漏洞评分为****CVSS 9.8****。**  
  
RouterOS   
运行在遍布全球家庭、  
ISP   
和企业网络中使用的  
 MikroTik   
路由器上。  
CISA   
将受影响行业列为通信和信息技术，并提醒称  
“  
成功利用该漏洞可导致攻击者实现远程代码执行或导致拒绝服务。  
”  
  
而就在前不久，波兰  
 CERT  
报告称，自  
 9   
月初以来，攻击者一直在利用被称为  
 “MikroTrick”   
的另外两个  
RouterOS   
漏洞。  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/t5z0xV2OYfUdicaHQyn3Qj7RffSbl6bfp38lgAmjKcnkLx19ibzWYQvOnvzHUtun6pkGG6R7I1ll6EVjoyzrEIpmvZ2RvHHm5ehOGBWKthve8/640?wx_fmt=gif&from=appmsg "")  
  
**漏洞概述**  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/t5z0xV2OYfU7rAf8yRhKXEib11SnOcw5riccvZ13H2yzgC97daes2MWnG5uXbBRU4I9gmibJDCicFW8jibrKzfm3cErqTaMC1raV3R3q0b2R8aU4/640?wx_fmt=gif&from=appmsg "")  
  
  
  
该漏洞位于  
 RouterOS Web   
管理服务中，它的  
HTTP   
请求正文处理包含一个在任何登录检查之前运行的整数下溢。  
CISA  
提到，攻击者可以用它  
“  
通过单个精心构造的请求，以  
 root   
身份实现任意代码执行，或导致拒绝服务  
”  
。  
  
CISA   
提到  
 RouterOS 7.24   
之前的版本为受影响版本，该漏洞由一位匿名研究人员报送。  
CISA   
表示，  
“  
尚未收到专门针对该漏洞的已知公开利用报告。  
”  
另外尚未出现公开的  
 PoC  
。  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/t5z0xV2OYfXIRwrjfXPbUyazn59KzpPuywANG0uRpOleyS0icxumYXnXVbw2ibyC9S4ia8Qh1qkpbYCGBIBDuqeKViaibuhJLsCJbb2ATVYpoZNQ/640?wx_fmt=gif&from=appmsg "")  
  
**补丁和缓解步骤**  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/t5z0xV2OYfVAUvLic4sBzqxMrp9ib1usDQKPtvGhjNkK4pYr5qLds8e2YwKK77XmPgicEMfZ8aBehCjmjj7qlpmnfMSUsQMVYMSeRlsjg9Aqjg/640?wx_fmt=gif&from=appmsg "")  
  
  
  
用户可从官方  
 MikroTik   
下载页面更新  
 RouterOS  
。  
MikroTik   
最新的修复版本是  
 7.24.2   
和  
 7.23.4  
，它们也修复了  
 MikroTrick   
漏洞。  
  
在打补丁之前，用户应将  
 Web   
管理界面与互联网隔离。仅允许受信任的  
IP  
范围或  
VPN  
访问。由于利用该  
 MikroTik RouterOS   
漏洞不需要凭据，因此应优先修复被暴露的路由器。  
  
  
代码卫士试用地址：https://sast.qianxin.com/  
  
开源卫士试用地址：https://oss.qianxin.com/  
  
  
  
  
  
  
  
  
  
**推荐阅读**  
  
[MikroTik 严重漏洞可用于暴露路由器管理员凭据](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247524305&idx=1&sn=60cf6de2e02ede50696541627d839f18&scene=21#wechat_redirect)  
  
  
[MikroTik RouterOS 存在严重的提权漏洞](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247517243&idx=1&sn=a9d490b0fe3c69b9beed676a4ccc3e0e&scene=21#wechat_redirect)  
  
  
[Mikrotik 终于修复 Pwn2Own 大赛上的 RouterOS 漏洞](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247516569&idx=2&sn=c424da990afe03ec4a3f677d568e6a11&scene=21#wechat_redirect)  
  
  
  
  
  
**原文链接**  
  
https://securityonline.info/mikrotik-routeros-cve-2026-84411/  
  
  
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
  

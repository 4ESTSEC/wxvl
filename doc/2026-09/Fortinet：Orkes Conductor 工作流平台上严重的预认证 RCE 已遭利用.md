#  Fortinet：Orkes Conductor 工作流平台上严重的预认证 RCE 已遭利用  
Ravie Lakshmanan
                    Ravie Lakshmanan  代码卫士   2026-09-20 07:04  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/Az5ZsrEic9ot90z9etZLlU7OTaPOdibteeibJMMmbwc29aJlDOmUicibIRoLdcuEQjtHQ2qjVtZBt0M5eVbYoQzlHiaw/640?wx_fmt=gif "")  
    
聚焦源代码安全，网罗国内外最新资讯！  
  
编译：代码卫士  
  
**Fortinet****公司表示，影响****Orkes Conductor****的一个严重漏洞（****CVE-2026-58138****，****CVSS v3.1****评分****9.8/CVSSS v4****评分****9.3****分）正遭活跃利用，与未经身份验证的远程代码执行有关。**  
  
美国国家漏洞数据库  
 (NVD)   
提到：  
“Orkes Conductor 3.21.21   
至  
 3.30.2   
之前的版本包含一个未经身份验证的远程代码执行漏洞，可导致远程攻击者在身份验证之前，向工作流  
 API   
端点提交包含恶意  
 JavaScript   
或  
 Python   
表达式的内联工作流定义，从而执行任意操作系统命令。攻击者可以通过  
 INLINE  
、  
LAMBDA  
、  
DO_WHILE   
和  
 SWITCH   
任务类型，利用配置了  
 HostAccess.ALL   
或  
 allowAllAccess(true)   
的未沙箱化  
 GraalVM   
求值器，通过  
 Java   
反射或直接子进程调用执行任意系统命令。  
”  
  
Fortinet   
公司表示在本周发布的爆发告警报告中表示，它观察到攻击者正在积极针对易受  
 CVE-2026-58138   
影响的  
 Orkes Conductor   
服务器，方式是向  
 Conductor   
工作流  
 API   
提交包含  
 JavaScript   
或  
 Python   
表达式的特殊构造的工作流定义。  
  
Fortinet   
公司表示：  
“  
由于易受攻击的求值器可被配置为具有不受限制的主机访问权限，攻击者可以逃逸预期的脚本环境，并以  
 Conductor   
进程的权限执行任意操作系统命令。  
”  
  
截至  
 2026   
年  
 9   
月  
 9   
日，该公司表示已在  
 24   
小时内阻止  
 1290   
次攻击尝试，相当于日均活动增加了  
 132%  
。  
2026   
年  
 9   
月  
 2   
日至  
 9   
日期间，共阻止了近  
 7000   
次尝试。据说大部分攻击活动源自德国、印度尼西亚、阿联酋和印度等地区。  
  
来自  
 Previdian   
的遥测数据显示，自  
 2026   
年  
 7   
月  
 24   
日以来，蜜罐遭到来自法国和美国两个不同  
 IP   
地址的三次利用尝试。同样，  
Empirical Security   
公司指出，最近在  
 2026   
年  
 8   
月  
 21   
日检测到在野利用。  
  
建议使用受影响版本的组织机构升级到已修复该漏洞的  
 Conductor 3.30.2   
或更高版本。如无法立即打补丁，建议限制对  
 Conductor   
工作流  
 API   
端点的外部访问，为  
 Conductor   
实例部署适当的网络访问控制措施，并监控可疑的工作流提交和意外的命令执行活动。  
  
  
代码卫士试用地址：https://sast.qianxin.com/  
  
开源卫士试用地址：https://oss.qianxin.com/  
  
  
  
  
  
  
  
  
  
**推荐阅读**  
  
[Fortinet 修复 FortiWeb 和 FortiManager 中的多个身份验证漏洞](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247526871&idx=1&sn=9b175f70e65bbcd26fcaacef5404a0d8&scene=21#wechat_redirect)  
  
  
[Fortinet 提醒注意 FortiSandbox 和 FortiAuthenticator 中的严重RCE漏洞](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247525995&idx=1&sn=c32aec58266325bad875fe920c376035&scene=21#wechat_redirect)  
  
  
[Fortinet 紧急修复已遭利用的 FortiClient EMS 严重漏洞](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247525661&idx=1&sn=052e45a26cbea5f9364bf03c39a7abc8&scene=21#wechat_redirect)  
  
  
[Fortinet 修复可导致未认证代码执行的严重 SQLi 漏洞](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247525085&idx=1&sn=446f5400a46600a37f24df299ba852d2&scene=21#wechat_redirect)  
  
  
[Fortinet 修复已遭利用的严重 FortiOS 漏洞](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247524999&idx=2&sn=ff036e0f85b25e6ee0f685062e7a537f&scene=21#wechat_redirect)  
  
  
  
  
**原文链接**  
  
https://thehackernews.com/2026/09/critical-pre-auth-rce-in-orkes.html  
  
  
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
  

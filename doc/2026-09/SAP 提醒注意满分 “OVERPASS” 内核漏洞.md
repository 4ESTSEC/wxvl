#  SAP 提醒注意满分 “OVERPASS” 内核漏洞  
Sergiu Gatlan
                    Sergiu Gatlan  代码卫士   2026-09-09 05:34  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/Az5ZsrEic9ot90z9etZLlU7OTaPOdibteeibJMMmbwc29aJlDOmUicibIRoLdcuEQjtHQ2qjVtZBt0M5eVbYoQzlHiaw/640?wx_fmt=gif "")  
    
聚焦源代码安全，网罗国内外最新资讯！  
  
编译：代码卫士  
  
**SAP****在****9****月的安全更新中修复了多个产品中的总计****20****个漏洞，其中包括一个****SAP Kernel****代码中的满分内存损坏漏洞****(CVE-2026-44756)****。**  
  
报送该漏洞的  
Onapsis  
公司的安全研究人员将其称为  
OVERPASS  
，其根源在于扩展护照协议  
 (EPP)   
处理库中存在常见的缓冲区溢出弱点。成功利用该漏洞可使未授权威胁攻击者以管理员权限在易受攻击的  
SAP  
主机上运行任意命令，从而导致底层  
SAP  
进程和业务数据被完全攻陷。  
  
该漏洞可通过  
SAP Internet  
通信管理器  
 (ICM)   
进行利用。  
ICM  
是  
SAP  
应用服务器的网络组件，负责通过  
HTTP  
、  
HTTPS  
和  
SMTP  
将  
SAP  
系统（  
SAP NetWeaver  
应用服务器）连接到互联网。  
  
根据  
Onapsis  
公司的估计，超过  
10000  
个面向互联网的  
SAP  
系统使用了该易受攻击的组件，可能面临利用  
CVE-2026-44756  
漏洞的攻击风险。  
Onapsis  
公司的首席技术官  
JP Perez-Etchegoyen  
周二表示：  
“  
使用高保真指纹进行定向搜索，识别出超过  
10000  
个唯一的面向互联网的  
IP  
地址，这些地址展示了可从公共互联网访问的  
SAP Web  
界面，而且这个数字还是保守估计。它只统计了可通过  
HTTP  
访问的系统，并且实质上少计算了  
SAP Web Dispatcher  
，因为后者代理其后端，且在其根路径上不返回可区分的  
SAP  
横幅，这使得互联网范围的扫描器在归因上存在结构性困难。  
”  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/t5z0xV2OYfWuEP4vcZBqm8B1TYWs0yeY0GbZ9jBsVJUTno7GLhYbBzZ1NCo75gWAtkuN5vreaic3DcdA4YMfVWvLDicRFPKcFwsibk4ibavMZuM/640?wx_fmt=gif&from=appmsg "")  
  
**S4GET，SAP NetWeaver消息服务器中的逻辑漏洞**  
  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/t5z0xV2OYfUj4EHcFMQyCMTy5Y7qaHYUZ9hBXcnK2HnzC5UOsua2hX4xfS3axLpotN6nVxUlQ4cWYvZkbZA3CCEJttyeX5kyUEX92b1ctM4/640?wx_fmt=gif&from=appmsg "")  
  
  
  
今天，  
SAP  
还修复了位于  
SAP NetWeaver  
消息服务器中的一个严重身份验证缺失漏洞  
 (CVE-2026-58240)  
，被  
Onapsis  
研究实验室命名为“  
S4GET  
”。成功利用该漏洞可导致未认证攻击者访问整个  
SAP  
系统集群，并在网络中远程执行恶意负载和任意命令。  
Onapsis  
公司的安全研究员  
Pablo Artuso  
解释称：  
“  
该漏洞通过每个  
SAP GUI  
客户端都连接的同一公共端口触发，因此无法在不破坏最终用户登录的情况下通过防火墙隔离。利用该漏洞无需凭据、无需证书，也无需预先存在的错误配置。攻击一旦成功，可在集群中的每个应用服务器上以  
<sid>adm  
（运行  
SAP  
的操作系统级用户）身份实现完全远程代码执行。  
”  
  
上个月，  
SAP  
修复了基于云的电子商务平台  
Commerce Cloud  
中的另一个满分的严重漏洞  
 (CVE-2026-58231)  
，威胁情报公司  
Defused  
在该漏洞被修补后数天内就标记其已被活跃利用。  
  
自  
2021  
年  
11  
月以来，美国网络安全和基础设施安全局  
 (CISA)   
已将  
14  
个  
SAP  
安全漏洞添加到其已被积极利用的漏洞列表中，其中包括三个被勒索软件团伙滥用的漏洞。  
SAP  
是一家德国跨国软件公司，报告称  
2025  
财年总收入超过  
360  
亿欧元，为全球最大的  
100  
家公司中的  
99  
家提供服务。  
  
  
代码卫士试用地址：https://sast.qianxin.com/  
  
开源卫士试用地址：https://oss.qianxin.com/  
  
  
  
  
  
  
  
  
  
**推荐阅读**  
  
[SAP 官方 npm 包受陷，被用于供应链攻击窃取凭据](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247525926&idx=3&sn=f176577fa7fbeba25d5c024f432e1ade&scene=21#wechat_redirect)  
  
  
[SAP NetWeaver 出现新漏洞 无需登录即可接管服务器](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247524187&idx=1&sn=7c8c5d4d69007d76c87484476c80addd&scene=21#wechat_redirect)  
  
  
[SAP S/4HANA 中严重漏洞已遭在野利用](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247523967&idx=1&sn=4fb0beaa6d1cf5b33d3224c1e6783d9f&scene=21#wechat_redirect)  
  
  
  
  
  
**原文链接**  
  
https://www.bleepingcomputer.com/news/security/sap-warns-of-maximum-severity-overpass-kernel-vulnerability/  
  
  
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
  

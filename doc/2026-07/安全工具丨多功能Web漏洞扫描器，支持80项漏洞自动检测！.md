#  安全工具丨多功能Web漏洞扫描器，支持80项漏洞自动检测！  
原创 蓝星安全
                    蓝星安全  蓝星安全   2026-07-09 22:00  
  
         
**点击上方****蓝星安全****关注我**  
  
****  
  
**免责声明：本公众号分享的任何资料仅限用于安全学习，严禁用于其他用途，请严格遵守中华人民共和国法律法规，对因不遵守国家法律法规而产生的任何后果，均由个人自行承担，本公众号不承担任何责任！**  
  
****  
**获取资料，请扫码下方二维码加入知识星球**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/opKHaHXLxcmxg12rU33pwcICNRkiaof5YSUAGfWPApU7M1BfdsTTOyvREw0gKD42g4U8eefG4n0XuYETEtbxSIN2drACnNVz3UeicCcldM5AA/640?wx_fmt=png&from=appmsg "")  
## 一、简介  
  
**RapidScan是一款基于Python3开发的多工具Web漏洞扫描器**  
，由安全研究者skavngr创建并维护。它的核心理念是通过**自动化编排**  
，将多种安全扫描工具整合到一个统一的框架中，实现“一键扫描”  
。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/opKHaHXLxclvhCO6Z3y6HfEicpOPykAlKHmNSACgZyJoYMMLJo56VVXHiamo9icmX63s3cyBGqpQfIicwUco8zlojFuu4XdLOnicjrBdq9CQZAkQ/640?wx_fmt=png&from=appmsg "")  
## 二、核心能力  
### 2.1. 多工具集成，一站式扫描  
  
RapidScan最大的亮点在于其**集成了大量主流的开源安全工具**  
，包括但不限于  
：  
- **信息收集类**  
：nmap  
（端口扫描）、dnsrecon  
（DNS枚举）、theHarvester  
（邮箱/子域名收集）、amass  
（子域名爆破）  
  
- **漏洞检测类**  
：nikto  
（Web服务器扫描）、wafw00f  
（WAF指纹识别）、sslyze  
（SSL/TLS安全检测）  
  
- **特定目标类**  
：自动检测WordPress、Joomla、Drupal等CMS系统，并**智能调用**  
对应的专用扫描工具如wpscan  
和plecost  
  
### 2.2. 智能误报判断与结果关联  
  
安全扫描最令人头疼的问题之一就是误报。RapidScan通过**对同一漏洞使用多种工具进行交叉验证**  
，帮助渗透测试人员更有效地**排除误报**  
，聚焦于真正存在的安全问题  
。  
### 2.3. 全面的漏洞检测覆盖  
  
目前RapidScan支持**超过80项漏洞测试**  
，覆盖了从网络层到应用层的多种安全检测  
：  
- **基础设施安全**  
：检测DNS/HTTP负载均衡器、Web应用防火墙、常见开放端口  
  
- **DNS安全**  
：通过Fierce、DNSWalk、DNSRecon、DNSEnum等多种工具检测DNS域传送漏洞  
  
- **子域名安全**  
：使用DNSMap、amass、nikto进行子域名暴力枚举  
  
- **SSL/TLS安全**  
：检测心血漏洞、FREAK、POODLE、CCS注入、LOGJAM、OCSP Stapling等常见SSL漏洞  
  
- **Web应用漏洞**  
：检测浅层XSS、SQL注入、布尔型盲注、本地文件包含、远程文件包含、远程代码执行  
  
- **DoS攻击检测**  
：包含Slow-Loris拒绝服务攻击检测  
  
### 2.4. 友好的输出与分类体系  
  
 扫描结果会按照**风险等级进行分类**  
：危急、高、中、低和信息级别，并**关联OWASP Top 10和CWE 25标准**  
（该功能正在完善中）。同时，每个发现的漏洞都配有**定义说明**  
和**修复建议**  
，帮助用户理解漏洞危害并指导修复  
。扫描完成后还会生成一份**执行摘要**  
，让用户对整体安全状况一目了然。  
  
三、立即获取  
  
https://github.com/skavngr/rapidscan  
  

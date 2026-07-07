#  6月必修漏洞清单：涵盖Gogs、Vite等多组件高危漏洞，务必速查  
原创 腾讯云安全
                    腾讯云安全  云鼎实验室   2026-07-07 09:31  
  
必修漏洞是指影响范围广、危害程度高、技术细节已公开或存在在野利用的安全漏洞。此类漏洞被攻击者利用后，可能导致业务系统中断、核心数据泄露、服务器被远程控制、内部网络被横向渗透等严重后果，造成经济损失和声誉损害。  
  
腾讯云安全研究团队综合评估“漏洞危害程度、影响范围、技术细节披露情况、安全社区关注度、在野利用情况”等因素，筛选出需优先修复的安全漏洞，定期发布企业必修安全漏洞清单。  
  
本清单旨在为企业安全运维人员提供漏洞修复优先级参考，助力企业提升安全防护能力、降低安全风险。  
  
注：本清单为腾讯云安全基于专业评估提供的技术参考，企业应根据自身业务特点、系统架构、安全等级等实际情况，制定相应的漏洞修复计划。  
  
 **以下是2026年6月份必修安全漏洞清单**  
：  
  
一、  
   
Gogs 远程代码执行漏洞（ CVE-2026-52806）  
  
二、  
Gogs 路径遍历导致远程代码执行漏洞（CVE-2026-52813）  
  
三、  
Vite 任意文件读取漏洞（TVD-2026-28041）  
  
四、  
Apache ActiveMQ 远程代码执行漏洞（CVE-2026-42588）  
  
五、  
Nezha Monitoring 远程代码执行漏洞（CVE-2026-46716）  
  
六、  
Discuz! 身份认证绕过漏洞（CVE-2026-49952）  
  
七、  
Splunk Enterprise 权限绕过漏洞（CVE-2026-20253）  
  
八、  
Linux Kernel 本地权限提升漏洞（CVE-2026-46331）  
  
  
**漏洞介绍及修复建议详见后文**  
  
  
一、   
Gogs 远程代码执行漏洞  
  
  
 概述：  
  
腾讯云安全  
近期监测到关于Gogs的风险公告，  
漏洞编号：TVD-2026-27528  
(CVE编号：  
CVE-2026-52806  
，CNNVD编号：  
CNNVD-2026-99484753  
)  
。成功利用此漏洞的攻击者，最终可在目标服务器上远程执行任意代码。  
  
Gogs是一款使用Go语言编写的开源自托管Git服务，旨在以最少的资源占用提供类似GitHub的代码托管体验。它支持仓库管理、代码浏览、Issue跟踪、Wiki文档、组织协作和Web钩子等功能，可轻松部署在低配置服务器上，深受个人开发者和中小团队的青睐。Git钩子（Git Hooks）机制允许在特定Git操作（如推送、提交）前后自动执行自定义脚本，是Gogs实现自动化工作流的关键功能。  
  
据描述，在 Gogs 中，由于其在执行 “Rebase before merging”合并操作时，未能对分支名称中的特殊字符进行严格过滤和转义  
，  
攻击者可以创建一个包含 --exec 标志的恶意分支名称，当具有仓库写权限的用户或攻击者本人触发达该合并操作时，git rebase 命令会将该分支名称中的 --exec 参数解析为需要执行的命令  
，  
攻击者无需管理员权限，仅需拥有一个注册账户并创建仓库，或对现有开启变基合并的仓库具有写权限，即可在目标服务器上实现任意代码执行。  
  
注：由于 Gogs 默认启用开放注册（DISABLE_REGISTRATION = false）且对仓库创建数量无限制（MAX_CREATION_LIMIT = -1），导致未经身份验证的远程攻击者在默认配置下即可利用该漏洞。  
  
漏洞状态：  
<table><tbody><tr style="mso-yfti-irow:0;mso-yfti-firstrow:yes;"><td data-colwidth="256" width="256" valign="top" style="border: 1pt solid black;background: rgb(68, 114, 196);padding: 0cm 5.4pt;"><p style="text-align:center;"><b style="mso-bidi-font-weight:normal;"><span style="font-size:11.0pt;mso-bidi-font-size:
  12.0pt;color:white;letter-spacing:.4pt;"><span leaf="">类别</span></span></b></p></td><td data-colwidth="256" width="256" valign="top" style="border-width: 1pt 1pt 1pt medium;border-style: solid solid solid none;border-color: black black black currentcolor;border-image: initial;background: rgb(68, 114, 196);padding: 0cm 5.4pt;"><p style="text-align:center;"><b style="mso-bidi-font-weight:normal;"><span style="font-size:11.0pt;mso-bidi-font-size:
  12.0pt;color:white;letter-spacing:.4pt;"><span leaf="">状态</span></span></b></p></td></tr><tr style="mso-yfti-irow:1;"><td data-colwidth="256" width="256" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">安全补丁</span></span></p></td><td data-colwidth="256" width="256" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">已公开</span></span></p></td></tr><tr style="mso-yfti-irow:2;"><td data-colwidth="256" width="256" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">漏洞细节</span></span></p></td><td data-colwidth="256" width="256" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">已公开</span></span></p></td></tr><tr style="mso-yfti-irow:3;"><td data-colwidth="256" width="256" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span lang="EN-US" style="font-size:10.0pt;mso-bidi-font-size:12.0pt;font-family:等线;mso-bidi-font-family:
  等线;color:#222222;letter-spacing:.4pt;"><span leaf="">PoC</span></span></p></td><td data-colwidth="256" width="256" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">已公开</span></span></p></td></tr><tr style="mso-yfti-irow:4;mso-yfti-lastrow:yes;"><td data-colwidth="256" width="256" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">在野利用</span></span></p></td><td data-colwidth="256" width="256" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">未发现</span></span></p></td></tr></tbody></table>  
风险等级：  
<table><tbody><tr style="mso-yfti-irow:0;mso-yfti-firstrow:yes;height:18.3pt;"><td data-colwidth="275" width="275" valign="top" style="border: 1pt solid black;background: rgb(68, 114, 196);padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><b style="mso-bidi-font-weight:normal;"><span style="font-size:11.5pt;mso-bidi-font-size:
  12.0pt;color:white;letter-spacing:.4pt;"><span leaf="">评定方式</span></span></b></p></td><td data-colwidth="238" width="238" valign="top" style="border-width: 1pt 1pt 1pt medium;border-style: solid solid solid none;border-color: black black black currentcolor;border-image: initial;background: rgb(68, 114, 196);padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><b style="mso-bidi-font-weight:normal;"><span style="font-size:11.5pt;mso-bidi-font-size:
  12.0pt;color:white;letter-spacing:.4pt;"><span leaf="">等级</span></span></b></p></td></tr><tr style="mso-yfti-irow:1;height:18.65pt;"><td data-colwidth="275" width="275" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 18.65pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">威胁等级</span></span></p></td><td data-colwidth="238" width="238" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 18.65pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:black;mso-color-alt:
  windowtext;"><span leaf="">高危</span></span></p></td></tr><tr style="mso-yfti-irow:2;height:18.3pt;"><td data-colwidth="275" width="275" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">影响面</span></span></p></td><td data-colwidth="238" width="238" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:black;mso-color-alt:
  windowtext;"><span leaf="">高</span></span></p></td></tr><tr style="mso-yfti-irow:3;height:18.65pt;"><td data-colwidth="275" width="275" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 18.65pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">攻击者价值</span></span></p></td><td data-colwidth="238" width="238" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 18.65pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:black;mso-color-alt:
  windowtext;"><span leaf="">高</span></span></p></td></tr><tr style="mso-yfti-irow:4;height:18.3pt;"><td data-colwidth="275" width="275" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">利用难度</span></span></p></td><td data-colwidth="238" width="238" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:black;mso-color-alt:
  windowtext;"><span leaf="">低</span></span></p></td></tr><tr style="mso-yfti-irow:5;mso-yfti-lastrow:yes;height:17.55pt;"><td data-colwidth="275" width="275" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 17.55pt;"><p style="text-align:center;"><span lang="EN-US" style="font-size:10.0pt;mso-bidi-font-size:12.0pt;font-family:等线;mso-bidi-font-family:
  等线;color:#222222;letter-spacing:.4pt;"><span style="mso-spacerun:yes;"><span leaf=""> </span></span></span><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">漏洞评分</span></span></p></td><td data-colwidth="238" width="238" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 17.55pt;"><p style="text-align:center;"><span lang="EN-US" style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:black;mso-color-alt:
  windowtext;"><span leaf="">9.9</span></span></p></td></tr></tbody></table>  
影响版本：  
  
Gogs <=0.14.2  
  
修复建议：  
  
1.  
官方已发布漏洞补丁及修复版本，请评估业务是否受影响后，升级至安全版本  
  
【备注】建议您在升级前做好数据备份工作，避免出现意外。  
  
https://github.com/gogs/gogs/releases/tag/v0.14.3  
  
2.   
临时缓解方案：  
  
-   
在  
Gogs  
配置文件  
 app.ini   
中设置  
 DISABLE_REGISTRATION = true  
，禁止不受信任的用户注册账户；  
  
-   
在  
app.ini  
中设置  
 MAX_CREATION_LIMIT = 0  
，禁止普通用户创建新的仓库；  
  
-   
审计并禁用不必要开启  
 “Rebase before merging”  
（合并前变基）功能的仓库；  
  
-   
使用防火墙或反向代理限制对  
 Gogs   
实例的网络访问，仅允许受信任的  
 IP   
范围访问。  
  
  
二、Gogs 路径遍历导致远程代码执行漏洞  
  
  
概述：  
  
腾讯云安全近期监测到关于  
Gogs  
的风险公告，  
漏洞编号：  
TVD-2026-32998 (CVE  
编号：  
CVE-2026-52813  
，  
CNNVD  
编号：  
CNNVD-2026-14696434)  
。成功利用此漏洞的攻击者，最终可远程执行任意代码。  
  
据描述，该漏洞源于  
Gogs  
在创建组织（  
Organization  
）时未对组织名称中的路径遍历序列（  
../  
）进行有效过滤。攻击者可创建包含路径遍历序列的恶意组织名称，导致该组织下仓库的存储路径脱离预期目录，被写入文件系统的任意位置。通过精心构造嵌套的  
Git  
仓库结构，攻击者可以覆盖目标仓库的  
Git  
钩子配置文件，将恶意脚本注入  
hooks  
目录，当目标仓库触发相应  
Git  
操作时执行攻击者控制的命令，最终实现远程代码执行。  
  
漏洞状态：  
<table><tbody><tr style="mso-yfti-irow:0;mso-yfti-firstrow:yes;"><td data-colwidth="274" width="274" valign="top" style="border: 1pt solid black;background: rgb(68, 114, 196);padding: 0cm 5.4pt;"><p style="text-align:center;"><b style="mso-bidi-font-weight:normal;"><span style="font-size:11.0pt;mso-bidi-font-size:
  12.0pt;color:white;letter-spacing:.4pt;"><span leaf="">类别</span></span></b></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: 1pt 1pt 1pt medium;border-style: solid solid solid none;border-color: black black black currentcolor;border-image: initial;background: rgb(68, 114, 196);padding: 0cm 5.4pt;"><p style="text-align:center;"><b style="mso-bidi-font-weight:normal;"><span style="font-size:11.0pt;mso-bidi-font-size:
  12.0pt;color:white;letter-spacing:.4pt;"><span leaf="">状态</span></span></b></p></td></tr><tr style="mso-yfti-irow:1;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">安全补丁</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">已公开</span></span></p></td></tr><tr style="mso-yfti-irow:2;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">漏洞细节</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">已公开</span></span></p></td></tr><tr style="mso-yfti-irow:3;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span lang="EN-US" style="font-size:10.0pt;mso-bidi-font-size:12.0pt;font-family:等线;mso-bidi-font-family:
  等线;color:#222222;letter-spacing:.4pt;"><span leaf="">PoC</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">已公开</span></span></p></td></tr><tr style="mso-yfti-irow:4;mso-yfti-lastrow:yes;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">在野利用</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">未发现</span></span></p></td></tr></tbody></table>  
风险等级：  
<table><tbody><tr style="mso-yfti-irow:0;mso-yfti-firstrow:yes;height:18.3pt;"><td data-colwidth="274" width="274" valign="top" style="border: 1pt solid black;background: rgb(68, 114, 196);padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><b style="mso-bidi-font-weight:normal;"><span style="font-size:11.5pt;mso-bidi-font-size:
  12.0pt;color:white;letter-spacing:.4pt;"><span leaf="">评定方式</span></span></b></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: 1pt 1pt 1pt medium;border-style: solid solid solid none;border-color: black black black currentcolor;border-image: initial;background: rgb(68, 114, 196);padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><b style="mso-bidi-font-weight:normal;"><span style="font-size:11.5pt;mso-bidi-font-size:
  12.0pt;color:white;letter-spacing:.4pt;"><span leaf="">等级</span></span></b></p></td></tr><tr style="mso-yfti-irow:1;height:18.65pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 18.65pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">威胁等级</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 18.65pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:black;mso-color-alt:
  windowtext;"><span leaf="">高危</span></span></p></td></tr><tr style="mso-yfti-irow:2;height:18.3pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">影响面</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:black;mso-color-alt:
  windowtext;"><span leaf="">高</span></span></p></td></tr><tr style="mso-yfti-irow:3;height:18.6pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 18.6pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">攻击者价值</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 18.6pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:black;mso-color-alt:
  windowtext;"><span leaf="">高</span></span></p></td></tr><tr style="mso-yfti-irow:4;height:18.3pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">利用难度</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:black;mso-color-alt:
  windowtext;"><span leaf="">低</span></span></p></td></tr><tr style="mso-yfti-irow:5;mso-yfti-lastrow:yes;height:16.4pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 16.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">漏洞评分</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 16.4pt;"><p style="text-align:center;"><span lang="EN-US" style="font-size:10.5pt;mso-bidi-font-size:12.0pt;font-family:等线;mso-bidi-font-family:
  等线;color:#222222;letter-spacing:.4pt;"><span leaf="">10</span></span></p></td></tr></tbody></table>  
影响版本：  
  
Gogs < 0.14.3  
  
修复建议：  
  
1.  
官方已发布漏洞补丁及修复版本，请评估业务是否受影响后，酌情升级至安全版本。  
  
【备注】建议您在升级前做好数据备份工作，避免出现意外。  
  
https://github.com/gogs/gogs/releases/tag/v0.14.3  
  
2.  
临时缓解方案：  
  
-  
在  
 Gogs   
配置文件  
 app.ini   
中设置  
 DISABLE_REGISTRATION = true  
，禁止不受信任的用户注册账户  
  
-   
使用防火墙或反向代理限制对  
 Gogs   
实例的网络访问，仅允许受信任的  
 IP   
范围访问  
  
  
三、Vite 任意文件读取漏洞  
  
  
概述：  
  
腾讯云安全近期监测到关于  
Vite  
的风险公告，  
漏洞编号：  
TVD-2026-28041  
。成功利用此漏洞的攻击者，最终可读取服务器上的任意敏感文件。  
  
Vite  
是一款现代化前端构建工具，利用浏览器原生  
 ES Modules  
（  
ESM  
）实现极速的开发服务器启动和按需模块加载，并采用基于  
 Rollup   
的生产构建流程，兼顾开发效率与构建性能。  
Vite   
原生支持  
 Vue.js  
，同时也支持  
 React  
、  
Svelte  
、  
Preact   
等主流前端框架，具备热模块替换（  
HMR  
）、插件扩展、  
TypeScript   
支持等特性，广泛应用于现代  
 Web   
应用开发。  
  
该漏洞源于在  
 Vite   
开发服务器与  
 @vitejs/plugin-vue   
插件的交互中，由于对请求路径的安全校验存在缺陷，攻击者可以通过构造特殊请求读取服务器上的任意文件内容，最终导致敏感信息（如配置文件、源码、系统密码文件等）泄露。  
  
漏洞状态：  
<table><tbody><tr style="mso-yfti-irow:0;mso-yfti-firstrow:yes;"><td data-colwidth="274" width="274" valign="top" style="border: 1pt solid black;background: rgb(68, 114, 196);padding: 0cm 5.4pt;"><p style="text-align:center;"><b style="mso-bidi-font-weight:normal;"><span style="font-size:11.0pt;mso-bidi-font-size:
  12.0pt;color:white;letter-spacing:.4pt;"><span leaf="">类别</span></span></b></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: 1pt 1pt 1pt medium;border-style: solid solid solid none;border-color: black black black currentcolor;border-image: initial;background: rgb(68, 114, 196);padding: 0cm 5.4pt;"><p style="text-align:center;"><b style="mso-bidi-font-weight:normal;"><span style="font-size:11.0pt;mso-bidi-font-size:
  12.0pt;color:white;letter-spacing:.4pt;"><span leaf="">状态</span></span></b></p></td></tr><tr style="mso-yfti-irow:1;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">安全补丁</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">未公开</span></span></p></td></tr><tr style="mso-yfti-irow:2;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">漏洞细节</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">未公开</span></span></p></td></tr><tr style="mso-yfti-irow:3;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span lang="EN-US" style="font-size:10.0pt;mso-bidi-font-size:12.0pt;font-family:等线;mso-bidi-font-family:
  等线;color:#222222;letter-spacing:.4pt;"><span leaf="">PoC</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:black;mso-color-alt:
  windowtext;"><span leaf="">未公开</span></span></p></td></tr><tr style="mso-yfti-irow:4;mso-yfti-lastrow:yes;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">在野利用</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">未发现</span></span></p></td></tr></tbody></table>  
风险等级：  
<table><tbody><tr style="mso-yfti-irow:0;mso-yfti-firstrow:yes;height:18.3pt;"><td data-colwidth="274" width="274" valign="top" style="border: 1pt solid black;background: rgb(68, 114, 196);padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><b style="mso-bidi-font-weight:normal;"><span style="font-size:11.5pt;mso-bidi-font-size:
  12.0pt;color:white;letter-spacing:.4pt;"><span leaf="">评定方式</span></span></b></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: 1pt 1pt 1pt medium;border-style: solid solid solid none;border-color: black black black currentcolor;border-image: initial;background: rgb(68, 114, 196);padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><b style="mso-bidi-font-weight:normal;"><span style="font-size:11.5pt;mso-bidi-font-size:
  12.0pt;color:white;letter-spacing:.4pt;"><span leaf="">等级</span></span></b></p></td></tr><tr style="mso-yfti-irow:1;height:18.65pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 18.65pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">威胁等级</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 18.65pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:black;mso-color-alt:
  windowtext;"><span leaf="">高危</span></span></p></td></tr><tr style="mso-yfti-irow:2;height:18.3pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">影响面</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:black;mso-color-alt:
  windowtext;"><span leaf="">高</span></span></p></td></tr><tr style="mso-yfti-irow:3;height:18.65pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 18.65pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">攻击者价值</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 18.65pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:black;mso-color-alt:
  windowtext;"><span leaf="">高</span></span></p></td></tr><tr style="mso-yfti-irow:4;height:18.3pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">利用难度</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:black;mso-color-alt:
  windowtext;"><span leaf="">低</span></span></p></td></tr><tr style="mso-yfti-irow:5;mso-yfti-lastrow:yes;height:16.4pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 16.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">漏洞评分</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 16.4pt;"><p style="text-align:center;"><span lang="EN-US" style="font-size:10.0pt;mso-bidi-font-size:12.0pt;font-family:等线;mso-bidi-font-family:
  等线;color:#222222;letter-spacing:.4pt;"><span leaf="">7.5</span></span></p></td></tr></tbody></table>  
影响版本：  
  
Vite <=8.0.15  
  
修复建议：  
  
1.   
官方暂未发布漏洞补丁及修复版本，请评估业务是否受影响后，及时关注官方针对该漏洞的修复：  
  
https://github.com/vitejs/vite/  
  
2.   
缓解措施：  
  
-   
如非必要，不对公网开放相关服务。  
  
-   
使用防火墙、安全组白名单等措施，对相关服务进行访问限制。  
  
-   
使用默认配置启动  
 Vite  
，限制对  
 Vite   
开发服务器的访问，关闭  
 server.host   
或绑定到特定的  
 IP   
地址  
  
-   
不影响正常业务的情况下可禁用   
@vitejs/plugin-vue   
插件  
。  
  
  
四、Apache ActiveMQ 远程代码执行漏洞  
  
  
概述：  
  
腾讯云安全近期监测到关于  
Apache ActiveMQ  
的风险公告，  
漏洞编号：  
TVD-2026-27985 (CVE  
编号：  
CVE-2026-42588  
，  
CNNVD  
编号：  
CNNVD-202606-297)  
。成功利用此漏洞的攻击者，最终可在目标服务器上远程执行任意代码。  
  
Apache ActiveMQ  
是  
Apache  
软件基金会开发的一款开源消息中间件，采用  
Java  
编写，支持多种消息协议如  
AMQP  
、  
MQTT  
、  
STOMP  
和  
OpenWire  
，广泛应用于企业级分布式系统中实现异步通信和解耦。  
ActiveMQ Classic  
的  
Web  
控制台集成了  
Jolokia JMX-HTTP  
桥接组件，通过  
HTTP  
接口提供对  
JMX MBeans  
的远程管理和监控能力，管理员可通过该组件对消息队列进行运维操作。  
  
据描述，该漏洞源于  
Apache ActiveMQ Web  
控制台中  
Jolokia JMX-HTTP  
桥接组件的默认访问策略存在缺陷，默认允许对  
org.apache.activemq:*  
命名空间下的所有  
MBeans  
执行  
exec  
操作。经过认证的攻击者可通过构造恶意的  
discovery URI  
，调用  
BrokerService.addNetworkConnector()  
方法，利用  
masterslave:// URL  
触发  
VM  
传输的  
brokerConfig  
参数，通过  
Spring  
的  
ResourceXmlApplicationContext  
加载恶意  
XML  
配置，在  
BrokerService  
校验配置之前实例化所有单例  
bean  
，最终通过  
Runtime.exec()  
等工厂方法执行任意代码。  
  
漏洞状态：  
<table><tbody><tr style="mso-yfti-irow:0;mso-yfti-firstrow:yes;"><td data-colwidth="274" width="274" valign="top" style="border: 1pt solid black;background: rgb(68, 114, 196);padding: 0cm 5.4pt;"><p style="text-align:center;"><b style="mso-bidi-font-weight:normal;"><span style="font-size:11.0pt;mso-bidi-font-size:
  12.0pt;color:white;letter-spacing:.4pt;"><span leaf="">类别</span></span></b></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: 1pt 1pt 1pt medium;border-style: solid solid solid none;border-color: black black black currentcolor;border-image: initial;background: rgb(68, 114, 196);padding: 0cm 5.4pt;"><p style="text-align:center;"><b style="mso-bidi-font-weight:normal;"><span style="font-size:11.0pt;mso-bidi-font-size:
  12.0pt;color:white;letter-spacing:.4pt;"><span leaf="">状态</span></span></b></p></td></tr><tr style="mso-yfti-irow:1;height:15.85pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 15.85pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">安全补丁</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 15.85pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">已公开</span></span></p></td></tr><tr style="mso-yfti-irow:2;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">漏洞细节</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">已公开</span></span></p></td></tr><tr style="mso-yfti-irow:3;height:15.85pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 15.85pt;"><p style="text-align:center;"><span lang="EN-US" style="font-size:10.0pt;mso-bidi-font-size:12.0pt;font-family:等线;mso-bidi-font-family:
  等线;color:#222222;letter-spacing:.4pt;"><span leaf="">PoC</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 15.85pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">已公开</span></span></p></td></tr><tr style="mso-yfti-irow:4;mso-yfti-lastrow:yes;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">在野利用</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">未发现</span></span></p></td></tr></tbody></table>  
风险等级：  
<table><tbody><tr style="mso-yfti-irow:0;mso-yfti-firstrow:yes;height:18.3pt;"><td data-colwidth="274" width="274" valign="top" style="border: 1pt solid black;background: rgb(68, 114, 196);padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><b style="mso-bidi-font-weight:normal;"><span style="font-size:11.5pt;mso-bidi-font-size:
  12.0pt;color:white;letter-spacing:.4pt;"><span leaf="">评定方式</span></span></b></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: 1pt 1pt 1pt medium;border-style: solid solid solid none;border-color: black black black currentcolor;border-image: initial;background: rgb(68, 114, 196);padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><b style="mso-bidi-font-weight:normal;"><span style="font-size:11.5pt;mso-bidi-font-size:
  12.0pt;color:white;letter-spacing:.4pt;"><span leaf="">等级</span></span></b></p></td></tr><tr style="mso-yfti-irow:1;height:18.65pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 18.65pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">威胁等级</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 18.65pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:black;mso-color-alt:
  windowtext;"><span leaf="">高危</span></span></p></td></tr><tr style="mso-yfti-irow:2;height:18.3pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">影响面</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:black;mso-color-alt:
  windowtext;"><span leaf="">高</span></span></p></td></tr><tr style="mso-yfti-irow:3;height:18.65pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 18.65pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">攻击者价值</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 18.65pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:black;mso-color-alt:
  windowtext;"><span leaf="">高</span></span></p></td></tr><tr style="mso-yfti-irow:4;height:18.3pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">利用难度</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:black;mso-color-alt:
  windowtext;"><span leaf="">低</span></span></p></td></tr><tr style="mso-yfti-irow:5;mso-yfti-lastrow:yes;height:16.4pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 16.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">漏洞评分</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 16.4pt;"><p style="text-align:center;"><span lang="EN-US" style="font-size:10.0pt;mso-bidi-font-size:12.0pt;font-family:等线;mso-bidi-font-family:
  等线;color:#222222;letter-spacing:.4pt;"><span leaf="">8.8</span></span></p></td></tr></tbody></table>  
影响版本：  
  
Apache ActiveMQ < 5.19.7  
  
6.0.0 <= Apache ActiveMQ < 6.2.6  
  
修复建议：  
  
1.   
官方已发布漏洞补丁及修复版本，请评估业务是否受影响后，酌情升级至安全版本。  
  
【备注】建议您在升级前做好数据备份工作，避免出现意外。  
  
https://activemq.apache.org/download  
  
2.   
临时缓解方案：  
  
-   
如无必要，避免将  
ActiveMQ Web  
控制台开放至公网。  
  
-   
配置防火墙或网络规则，仅允许特定  
IP  
地址或  
IP  
段访问  
/api/jolokia/  
端点。  
  
  
五、Nezha Monitoring 远程代码执行漏洞  
  
  
概述：  
  
腾讯云安全  
近期监测到关于  
Nezha Monitoring  
的风险公告，  
漏洞编号：  
TVD-2026-25750  
 (CVE  
编号：  
CVE-2026-46716  
，  
CNNVD  
编号：  
CNNVD-2026-49626881  
)  
。成功利用此漏洞的攻击者，可绕过权限控制向所有受控服务器下发恶意命令，最终远程执行任意代码。  
  
Nezha Monitoring   
是一款开源的、自托管的轻量级服务器与网站监控运维工具。它采用  
Dashboard-Agent  
架构，  
Dashboard  
端负责管理用户、服务器和告警规则，  
Agent  
端部署在各服务器上收集监控数据并执行  
Dashboard  
下发的任务。该工具支持  
Cron  
定时任务调度、  
Webhook  
通知推送等自动化运维功能，被广泛应用于中小型团队的服务器集群管理场景。  
  
据描述，该漏洞源于  
Nezha Monitoring  
中  
Cron  
定时任务调度模块存在权限校验缺陷，具有  
RoleMember  
角色的低权限用户可创建覆盖范围为  
CronCoverAll  
、目标服务器列表为空且包含任意命令的定时任务。当调度器触发执行时，  
Dashboard  
会将该命令推送到全局  
ServerShared  
映射中的所有服务器上执行，包括属于其他租户（管理员或其他成员）的服务器，而执行结果会通过攻击者控制的  
Webhook  
通知组回传给攻击者，从而实现跨租户的远程命令执行。  
  
漏洞状态：  
<table><tbody><tr style="mso-yfti-irow:0;mso-yfti-firstrow:yes;"><td data-colwidth="274" width="274" valign="top" style="border: 1pt solid black;background: rgb(68, 114, 196);padding: 0cm 5.4pt;"><p style="text-align:center;"><b style="mso-bidi-font-weight:normal;"><span style="font-size:11.0pt;mso-bidi-font-size:
  12.0pt;color:white;letter-spacing:.4pt;"><span leaf="">类别</span></span></b></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: 1pt 1pt 1pt medium;border-style: solid solid solid none;border-color: black black black currentcolor;border-image: initial;background: rgb(68, 114, 196);padding: 0cm 5.4pt;"><p style="text-align:center;"><b style="mso-bidi-font-weight:normal;"><span style="font-size:11.0pt;mso-bidi-font-size:
  12.0pt;color:white;letter-spacing:.4pt;"><span leaf="">状态</span></span></b></p></td></tr><tr style="mso-yfti-irow:1;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">安全补丁</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">已公开</span></span></p></td></tr><tr style="mso-yfti-irow:2;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">漏洞细节</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">已公开</span></span></p></td></tr><tr style="mso-yfti-irow:3;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span lang="EN-US" style="font-size:10.0pt;mso-bidi-font-size:12.0pt;font-family:等线;mso-bidi-font-family:
  等线;color:#222222;letter-spacing:.4pt;"><span leaf="">PoC</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">已公开</span></span></p></td></tr><tr style="mso-yfti-irow:4;mso-yfti-lastrow:yes;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">在野利用</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">未发现</span></span></p></td></tr></tbody></table>  
风险等级：  
<table><tbody><tr style="mso-yfti-irow:0;mso-yfti-firstrow:yes;height:18.3pt;"><td data-colwidth="274" width="274" valign="top" style="border: 1pt solid black;background: rgb(68, 114, 196);padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><b style="mso-bidi-font-weight:normal;"><span style="font-size:11.5pt;mso-bidi-font-size:
  12.0pt;color:white;letter-spacing:.4pt;"><span leaf="">评定方式</span></span></b></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: 1pt 1pt 1pt medium;border-style: solid solid solid none;border-color: black black black currentcolor;border-image: initial;background: rgb(68, 114, 196);padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><b style="mso-bidi-font-weight:normal;"><span style="font-size:11.5pt;mso-bidi-font-size:
  12.0pt;color:white;letter-spacing:.4pt;"><span leaf="">等级</span></span></b></p></td></tr><tr style="mso-yfti-irow:1;height:18.65pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 18.65pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">威胁等级</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 18.65pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:black;mso-color-alt:
  windowtext;"><span leaf="">高危</span></span></p></td></tr><tr style="mso-yfti-irow:2;height:18.3pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">影响面</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:black;mso-color-alt:
  windowtext;"><span leaf="">高</span></span></p></td></tr><tr style="mso-yfti-irow:3;height:17.4pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 17.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">攻击者价值</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 17.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:black;mso-color-alt:
  windowtext;"><span leaf="">高</span></span></p></td></tr><tr style="mso-yfti-irow:4;height:18.3pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">利用难度</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:black;mso-color-alt:
  windowtext;"><span leaf="">低</span></span></p></td></tr><tr style="mso-yfti-irow:5;mso-yfti-lastrow:yes;height:16.4pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 16.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">漏洞评分</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 16.4pt;"><p style="text-align:center;"><span lang="EN-US" style="font-size:10.0pt;mso-bidi-font-size:12.0pt;font-family:等线;mso-bidi-font-family:
  等线;color:#222222;letter-spacing:.4pt;"><span leaf="">9.9</span></span></p></td></tr></tbody></table>  
影响版本：  
  
1.4.0 <= Nezha Monitoring < 2.0.8  
  
修复建议：  
  
1.   
官方已发布漏洞补丁及修复版本，请评估业务是否受影响后，酌情升级至安全版本。  
  
【备注】建议您在升级前做好数据备份工作，避免出现意外。  
  
https://github.com/nezhahq/nezha/releases  
  
2.   
临时缓解方案：  
  
-   
临时禁用非必要的  
 OAuth2   
自注册功能，防止攻击者自动创建  
RoleMember   
账户  
。  
  
-   
配置防火墙或网络规则，仅允许特定  
IP  
地址或  
IP  
段访问  
Dashboard  
管理界面。  
  
  
六、Discuz! 身份认证绕过漏洞  
  
  
概述：  
  
腾讯云安全  
近期监测到关于  
Discuz!  
的风险公告，  
漏洞编号：  
TVD-2026-31471  
 (CVE  
编号：  
CVE-2026-49952  
，  
CNNVD  
编号：  
CNNVD-2026-30569103  
)  
。成功利用此漏洞的攻击者，可在未经身份验证的情况下绕过认证机制，最终获取数据库备份与恢复权限，并可结合条件竞争实现任意用户身份伪造或远程代码执行。  
  
Discuz!  
是一套通用的社区论坛软件系统。作为国内使用最广泛的论坛系统之一，  
Discuz!  
采用  
PHP+MySQL  
架构，支持插件扩展和模板定制，用户可以在不需要任何编程知识的基础上快速搭建功能完善的社区论坛。  
Discuz! X5.0  
是其最新一代版本，引入了  
UCenter  
统一用户中心实现单点登录，并提供数据库备份恢复  
API  
等功能模块，方便站点管理者进行运维管理。  
  
据描述，该漏洞源于  
Discuz! X5.0  
在独立安装模式下，配置文件  
config_ucenter.php  
会将  
UC_KEY  
常量与全局  
authkey  
设置为相同值，导致加密密钥在  
UCenter  
集成与数据库备份  
API  
（  
/api/db/dbbak.php  
）之间被复用。攻击者可利用登录请求中的  
lssubmit  
参数触发  
logging_ctl::logging_more()  
方法作为加密预言机，获取由  
authkey  
签名的合法令牌，随后使用该令牌绕过  
dbbak.php  
的授权校验，获得非授权的数据库导出与导入权限。  
  
漏洞状态：  
<table><tbody><tr style="mso-yfti-irow:0;mso-yfti-firstrow:yes;"><td data-colwidth="274" width="274" valign="top" style="border: 1pt solid black;background: rgb(68, 114, 196);padding: 0cm 5.4pt;"><p style="text-align:center;"><b style="mso-bidi-font-weight:normal;"><span style="font-size:11.0pt;mso-bidi-font-size:
  12.0pt;color:white;letter-spacing:.4pt;"><span leaf="">类别</span></span></b></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: 1pt 1pt 1pt medium;border-style: solid solid solid none;border-color: black black black currentcolor;border-image: initial;background: rgb(68, 114, 196);padding: 0cm 5.4pt;"><p style="text-align:center;"><b style="mso-bidi-font-weight:normal;"><span style="font-size:11.0pt;mso-bidi-font-size:
  12.0pt;color:white;letter-spacing:.4pt;"><span leaf="">状态</span></span></b></p></td></tr><tr style="mso-yfti-irow:1;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">安全补丁</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">已公开</span></span></p></td></tr><tr style="mso-yfti-irow:2;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">漏洞细节</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">已公开</span></span></p></td></tr><tr style="mso-yfti-irow:3;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span lang="EN-US" style="font-size:10.0pt;mso-bidi-font-size:12.0pt;font-family:等线;mso-bidi-font-family:
  等线;color:#222222;letter-spacing:.4pt;"><span leaf="">PoC</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">已公开</span></span></p></td></tr><tr style="mso-yfti-irow:4;mso-yfti-lastrow:yes;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">在野利用</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">未发现</span></span></p></td></tr></tbody></table>  
风险等级：  
<table><tbody><tr style="mso-yfti-irow:0;mso-yfti-firstrow:yes;height:18.3pt;"><td data-colwidth="274" width="274" valign="top" style="border: 1pt solid black;background: rgb(68, 114, 196);padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><b style="mso-bidi-font-weight:normal;"><span style="font-size:11.5pt;mso-bidi-font-size:
  12.0pt;color:white;letter-spacing:.4pt;"><span leaf="">评定方式</span></span></b></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: 1pt 1pt 1pt medium;border-style: solid solid solid none;border-color: black black black currentcolor;border-image: initial;background: rgb(68, 114, 196);padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><b style="mso-bidi-font-weight:normal;"><span style="font-size:11.5pt;mso-bidi-font-size:
  12.0pt;color:white;letter-spacing:.4pt;"><span leaf="">等级</span></span></b></p></td></tr><tr style="mso-yfti-irow:1;height:18.65pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 18.65pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">威胁等级</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 18.65pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:black;mso-color-alt:
  windowtext;"><span leaf="">高危</span></span></p></td></tr><tr style="mso-yfti-irow:2;height:18.3pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">影响面</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:black;mso-color-alt:
  windowtext;"><span leaf="">高</span></span></p></td></tr><tr style="mso-yfti-irow:3;height:18.65pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 18.65pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">攻击者价值</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 18.65pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:black;mso-color-alt:
  windowtext;"><span leaf="">高</span></span></p></td></tr><tr style="mso-yfti-irow:4;height:18.3pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">利用难度</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:black;mso-color-alt:
  windowtext;"><span leaf="">低</span></span></p></td></tr><tr style="mso-yfti-irow:5;mso-yfti-lastrow:yes;height:16.4pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 16.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">漏洞评分</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 16.4pt;"><p style="text-align:center;"><span lang="EN-US" style="font-size:10.0pt;mso-bidi-font-size:12.0pt;font-family:等线;mso-bidi-font-family:
  等线;color:#222222;letter-spacing:.4pt;"><span leaf="">9.1</span></span></p></td></tr></tbody></table>  
影响版本：  
  
20260320 <= Discuz!X5.0 <= 20260501  
  
修复建议：  
  
1.   
官方已发布漏洞补丁及修复版本，请评估业务是否受影响后，酌情升级至安全版本。  
  
【备注】建议您在升级前做好数据备份工作，避免出现意外。  
  
https://www.discuz.vip/download  
  
2.   
临时缓解方案：  
  
-   
如无必要，避免将  
Discuz!  
管理后台及  
/api/db/dbbak.php  
接口开放至公网。  
  
-   
配置防火墙或网络规则，仅允许特定  
IP  
地址或  
IP  
段访问相关接口。  
  
  
七、Splunk Enterprise 权限绕过漏洞  
  
  
概述：  
  
腾讯云安全近期监测到关于  
Splunk Enterprise  
的风险公告，  
漏洞编号：  
TVD-2026-30754 (CVE  
编号：  
CVE-2026-20253  
，  
CNNVD  
编号：  
CNNVD-202606-2707)  
。成功利用此漏洞的攻击者，可在无需身份验证的情况下通过  
PostgreSQL Sidecar  
服务端点创建或篡改任意文件，最终远程执行任意代码。  
  
Splunk Enterprise  
是  
Splunk  
公司开发的一款企业级机器数据采集、索引和分析平台，通过从各类数据源（应用程序、服务器、网络设备等）采集海量机器数据并建立索引，支持用户进行实时搜索、监控分析和可视化展示，广泛应用于  
IT  
运维、安全分析和业务智能等领域。  
PostgreSQL Sidecar  
服务（  
splunk-postgres  
）是  
Splunk Enterprise  
中的配套数据库服务组件，负责管理配置和索引数据的持久化存储。  
  
据描述，该漏洞源于  
Splunk Enterprise  
中  
PostgreSQL Sidecar  
服务的  
HTTP API  
端点完全缺少身份认证控制。  
Sidecar  
进程监听在本地  
5435  
端口，但  
Splunk  
主  
Web  
应用（监听  
8000  
端口）在作为反向代理转发请求至  
Sidecar API  
时未添加额外认证层。攻击者可通过  
/v1/postgres/recovery/backup  
端点的  
backupFile  
参数实施路径遍历写入文件，或利用  
/v1/postgres/recovery/restore  
端点结合  
PostgreSQL  
的  
lo_export  
大对象导出功能将任意内容写入文件系统，从而最终实现远程代码执行。  
  
漏洞状态：  
<table><tbody><tr style="mso-yfti-irow:0;mso-yfti-firstrow:yes;height:16.05pt;"><td data-colwidth="274" width="274" valign="top" style="border: 1pt solid black;background: rgb(68, 114, 196);padding: 0cm 5.4pt;height: 16.05pt;"><p style="text-align:center;"><b style="mso-bidi-font-weight:normal;"><span style="font-size:11.0pt;mso-bidi-font-size:
  12.0pt;color:white;letter-spacing:.4pt;"><span leaf="">类别</span></span></b></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: 1pt 1pt 1pt medium;border-style: solid solid solid none;border-color: black black black currentcolor;border-image: initial;background: rgb(68, 114, 196);padding: 0cm 5.4pt;height: 16.05pt;"><p style="text-align:center;"><b style="mso-bidi-font-weight:normal;"><span style="font-size:11.0pt;mso-bidi-font-size:
  12.0pt;color:white;letter-spacing:.4pt;"><span leaf="">状态</span></span></b></p></td></tr><tr style="mso-yfti-irow:1;height:16.05pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 16.05pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">安全补丁</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 16.05pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">已公开</span></span></p></td></tr><tr style="mso-yfti-irow:2;height:16.05pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 16.05pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">漏洞细节</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 16.05pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">已公开</span></span></p></td></tr><tr style="mso-yfti-irow:3;height:16.3pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 16.3pt;"><p style="text-align:center;"><span lang="EN-US" style="font-size:10.0pt;mso-bidi-font-size:12.0pt;font-family:等线;mso-bidi-font-family:
  等线;color:#222222;letter-spacing:.4pt;"><span leaf="">PoC</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 16.3pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">已公开</span></span></p></td></tr><tr style="mso-yfti-irow:4;mso-yfti-lastrow:yes;height:21.35pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 21.35pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">在野利用</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 21.35pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">已发现</span></span></p></td></tr></tbody></table>  
风险等级：  
<table><tbody><tr style="mso-yfti-irow:0;mso-yfti-firstrow:yes;height:18.3pt;"><td data-colwidth="274" width="274" valign="top" style="border: 1pt solid black;background: rgb(68, 114, 196);padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><b style="mso-bidi-font-weight:normal;"><span style="font-size:11.5pt;mso-bidi-font-size:
  12.0pt;color:white;letter-spacing:.4pt;"><span leaf="">评定方式</span></span></b></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: 1pt 1pt 1pt medium;border-style: solid solid solid none;border-color: black black black currentcolor;border-image: initial;background: rgb(68, 114, 196);padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><b style="mso-bidi-font-weight:normal;"><span style="font-size:11.5pt;mso-bidi-font-size:
  12.0pt;color:white;letter-spacing:.4pt;"><span leaf="">等级</span></span></b></p></td></tr><tr style="mso-yfti-irow:1;height:18.65pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 18.65pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">威胁等级</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 18.65pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:black;mso-color-alt:
  windowtext;"><span leaf="">高危</span></span></p></td></tr><tr style="mso-yfti-irow:2;height:18.3pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">影响面</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:black;mso-color-alt:
  windowtext;"><span leaf="">高</span></span></p></td></tr><tr style="mso-yfti-irow:3;height:18.65pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 18.65pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">攻击者价值</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 18.65pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:black;mso-color-alt:
  windowtext;"><span leaf="">高</span></span></p></td></tr><tr style="mso-yfti-irow:4;height:18.3pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">利用难度</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:black;mso-color-alt:
  windowtext;"><span leaf="">低</span></span></p></td></tr><tr style="mso-yfti-irow:5;mso-yfti-lastrow:yes;height:16.4pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 16.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">漏洞评分</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 16.4pt;"><p style="text-align:center;"><span lang="EN-US" style="font-size:10.0pt;mso-bidi-font-size:12.0pt;font-family:等线;mso-bidi-font-family:
  等线;color:#222222;letter-spacing:.4pt;"><span leaf="">9.8</span></span></p></td></tr></tbody></table>  
影响版本：  
  
10.0.0 <= Splunk Enterprise < 10.0.7  
  
10.2.0 <= Splunk Enterprise < 10.2.4  
  
修复建议：  
  
1.   
官方已发布漏洞补丁及修复版本，请评估业务是否受影响后，酌情升级至安全版本。  
  
【备注】建议您在升级前做好数据备份工作，避免出现意外。  
  
https://www.splunk.com/en_us/download/splunk-enterprise.html  
  
2.   
临时缓解方案：  
  
-   
如无必要，避免将  
Splunk Web  
开放至公网。  
  
-   
配置防火墙或网络规则，仅允许特定  
IP  
地址或  
IP  
段访问  
Splunk Web  
管理界面。  
  
  
八、Linux Kernel 本地权限提升漏洞  
  
  
 概述：  
  
腾讯云安全  
近期监测到关于  
Linux Kernel  
的风险公告，  
漏洞编号：  
TVD-2026-31801  
 (CVE  
编号：  
CVE-2026-46331  
，  
CNNVD  
编号：  
CNNVD-2026-31809101  
)  
。成功利用此漏洞的攻击者，可在本地实现权限提升，最终获得系统最高权限。  
  
Linux Kernel  
是  
Linux  
操作系统的核心组件，负责管理系统的硬件资源、进程调度、内存管理、文件系统和网络通信等底层功能。作为开源操作系统内核，  
Linux Kernel  
被广泛应用于服务器、嵌入式设备、移动终端和云计算平台等各类场景。流量控制（  
Traffic Control  
，  
tc  
）子系统是  
Linux  
内核网络栈的重要组成部分，支持对网络数据包进行分类、排队、调度和修改等操作，而  
pedit  
（  
packet edit  
）动作模块则用于对数据包内容进行按需修改，以实现网络策略控制。  
  
据描述，该漏洞源于  
Linux  
内核流量控制子系统中  
tcf_pedit_act()  
函数在调用  
skb_ensure_writable()  
进行写时复制（  
COW  
）时存在边界计算错误。函数在进入键循环之前使用  
tcfp_off_max_hint  
计算  
COW  
范围，但该提示值未考虑类型化键（  
typed keys  
）增加的运行时头部偏移量，导致部分写入区域未被正确  
COW  
保护。攻击者可利用此缺陷触发页面缓存（  
page cache  
）损坏，结合其他利用技术实现本地权限提升。  
  
漏洞状态：  
<table><tbody><tr style="mso-yfti-irow:0;mso-yfti-firstrow:yes;"><td data-colwidth="274" width="274" valign="top" style="border: 1pt solid black;background: rgb(68, 114, 196);padding: 0cm 5.4pt;"><p style="text-align:center;"><b style="mso-bidi-font-weight:normal;"><span style="font-size:11.0pt;mso-bidi-font-size:
  12.0pt;color:white;letter-spacing:.4pt;"><span leaf="">类别</span></span></b></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: 1pt 1pt 1pt medium;border-style: solid solid solid none;border-color: black black black currentcolor;border-image: initial;background: rgb(68, 114, 196);padding: 0cm 5.4pt;"><p style="text-align:center;"><b style="mso-bidi-font-weight:normal;"><span style="font-size:11.0pt;mso-bidi-font-size:
  12.0pt;color:white;letter-spacing:.4pt;"><span leaf="">状态</span></span></b></p></td></tr><tr style="mso-yfti-irow:1;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">安全补丁</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">已公开</span></span></p></td></tr><tr style="mso-yfti-irow:2;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">漏洞细节</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">已公开</span></span></p></td></tr><tr style="mso-yfti-irow:3;height:15.85pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 15.85pt;"><p style="text-align:center;"><span lang="EN-US" style="font-size:10.0pt;mso-bidi-font-size:12.0pt;font-family:等线;mso-bidi-font-family:
  等线;color:#222222;letter-spacing:.4pt;"><span leaf="">PoC</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 15.85pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">已公开</span></span></p></td></tr><tr style="mso-yfti-irow:4;mso-yfti-lastrow:yes;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">在野利用</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">未发现</span></span></p></td></tr></tbody></table>  
风险等级：  
<table><tbody><tr style="mso-yfti-irow:0;mso-yfti-firstrow:yes;height:18.3pt;"><td data-colwidth="274" width="274" valign="top" style="border: 1pt solid black;background: rgb(68, 114, 196);padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><b style="mso-bidi-font-weight:normal;"><span style="font-size:11.5pt;mso-bidi-font-size:
  12.0pt;color:white;letter-spacing:.4pt;"><span leaf="">评定方式</span></span></b></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: 1pt 1pt 1pt medium;border-style: solid solid solid none;border-color: black black black currentcolor;border-image: initial;background: rgb(68, 114, 196);padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><b style="mso-bidi-font-weight:normal;"><span style="font-size:11.5pt;mso-bidi-font-size:
  12.0pt;color:white;letter-spacing:.4pt;"><span leaf="">等级</span></span></b></p></td></tr><tr style="mso-yfti-irow:1;height:18.65pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 18.65pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">威胁等级</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 18.65pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:black;mso-color-alt:
  windowtext;"><span leaf="">高危</span></span></p></td></tr><tr style="mso-yfti-irow:2;height:18.3pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">影响面</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:black;mso-color-alt:
  windowtext;"><span leaf="">高</span></span></p></td></tr><tr style="mso-yfti-irow:3;height:18.65pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 18.65pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">攻击者价值</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 18.65pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:black;mso-color-alt:
  windowtext;"><span leaf="">高</span></span></p></td></tr><tr style="mso-yfti-irow:4;height:18.3pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">利用难度</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 18.3pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:black;mso-color-alt:
  windowtext;"><span leaf="">低</span></span></p></td></tr><tr style="mso-yfti-irow:5;mso-yfti-lastrow:yes;height:16.65pt;"><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor black black;border-image: initial;background: white;padding: 0cm 5.4pt;height: 16.65pt;"><p style="text-align:center;"><span style="font-size:10.0pt;mso-bidi-font-size:12.0pt;color:#222222;letter-spacing:
  .4pt;"><span leaf="">漏洞评分</span></span></p></td><td data-colwidth="274" width="274" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor black black currentcolor;background: white;padding: 0cm 5.4pt;height: 16.65pt;"><p style="text-align:center;"><span lang="EN-US" style="font-size:10.0pt;mso-bidi-font-size:12.0pt;font-family:等线;mso-bidi-font-family:
  等线;color:#222222;letter-spacing:.4pt;"><span leaf="">7.8</span></span></p></td></tr></tbody></table>  
影响版本：  
  
v5.18 <= Linux Kernel < v7.1-rc7  
  
修复建议：  
  
1.   
官方已发布漏洞补丁及修复版本，请评估业务是否受影响后，酌情升级至安全版本。  
  
【备注】建议您在升级前做好数据备份工作，避免出现意外。  
  
https://git.kernel.org/pub/scm/linux/kernel/git/stable/linux.git/commit/?id=899ee91156e57784090c5565e4f31bd7dbffbc5a  
  
2.   
临时缓解方案：  
  
-   
需评估是否使用到  
 act_pedit   
模块，该模块通用用于网络虚拟化、  
SDN  
、容器网络和运营商网络场景中，如无需要，可通过以下命令禁用删除该模块：  
  
sh -c "printf 'install act_pedit /bin/false' > /etc/modprobe.d/block-cve-2026-46331.conf; rmmod act_pedit 2>/dev/null; true"  
  
  
  
*  
以上  
漏洞评分为腾讯云安全研究人员根据漏洞情况作出，仅供参考，具体漏洞细节请以原厂商或是相关漏洞平台公示为准。  
  
  
  
  
![图片](https://mmbiz.qpic.cn/mmbiz_gif/FIBZec7ucChYUNicUaqntiamEgZ1ZJYzLRasq5S6zvgt10NKsVZhejol3iakHl3ItlFWYc8ZAkDa2lzDc5SHxmqjw/640?wx_fmt=gif&wxfrom=5&wx_lazy=1&tp=webp#imgIndex=45 "")  
  
**END**  
  
****  
**——关注云鼎实验室，获取更多安全情报——**  
  
  

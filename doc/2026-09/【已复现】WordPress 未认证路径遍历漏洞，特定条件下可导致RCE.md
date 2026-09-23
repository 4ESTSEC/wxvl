#  【已复现】WordPress 未认证路径遍历漏洞，特定条件下可导致RCE  
原创 360漏洞研究院
                    360漏洞研究院  360漏洞研究院   2026-09-23 02:54  
  
2026年9月22日，WordPress 官方披露 CVE-2026-87902 漏洞并发布修复版本。该漏洞存在于 WordPress 核心页面模板解析功能中，未经身份认证的攻击者可在满足特定条件时，加载主题目录之外的本地 PHP 文件，进一步可能导致远程代码执行。  
  
  
**利用前置条件：**  
- **主题条件：**  
当前主题或其父主题中，存在 page-templates 等名称以 page- 开头的目录。  
  
- **服务器条件：**  
存在网站程序能够读取并利用的本地 PHP 文件。如果要进一步执行攻击者指定的代码，还需服务器上的相关组件、配置和文件写入权限满足要求。  
  
**使用受影响版本并不代表一定能够被直接远程执行代码**  
，实际风险还与主题结构和服务器配置有关。  
  
  
目前 **360漏洞挖掘智能体已成功复现该漏洞**  
。本文包含完整影响范围、正式修复方案、技术原理与复现细节。  
  
  
<table><tbody><tr style="box-sizing: border-box;"><td colspan="4" data-colwidth="100.0000%" width="100.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;background-color: rgb(100, 130, 228);box-sizing: border-box;padding: 0px;"><section style="text-align: center;color: rgb(255, 255, 255);box-sizing: border-box;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">漏洞概述</span></strong></p></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="24.0000%" width="24.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;color: rgb(0, 0, 0);padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">漏洞名称</span></strong></p></section></td><td colspan="3" data-colwidth="76.0000%" width="76.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><span style="box-sizing: border-box;"><span leaf="">WordPress Core 未认证路径遍历与本地文件包含漏洞</span></span></p></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="24.0000%" width="24.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;color: rgb(0, 0, 0);padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">漏洞编号</span></strong></p></section></td><td colspan="3" data-colwidth="76.0000%" width="76.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">CVE-2026-87902</span></p></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="24.0000%" width="24.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;color: rgb(0, 0, 0);padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">公开时间</span></strong></p></section></td><td data-colwidth="28.0000%" width="28.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">2026-09-22</span></p></section></td><td data-colwidth="28.0000%" width="28.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span style="color: rgb(0, 0, 0);box-sizing: border-box;"><span leaf="">POC状态</span></span></strong></p></section></td><td data-colwidth="20.0000%" width="20.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;color: rgb(100, 130, 228);box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">已公开</span></strong></p></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="24.0000%" width="24.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;color: rgb(0, 0, 0);padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">漏洞类型</span></strong></p></section></td><td data-colwidth="28.0000%" width="28.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">路径遍历</span></p><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">本地文件包含</span></p></section></td><td data-colwidth="28.0000%" width="28.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;color: rgb(0, 0, 0);padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">EXP状态</span></strong></p></section></td><td data-colwidth="20.0000%" width="20.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;color: rgb(100, 130, 228);box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">已公开</span></strong></p></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="24.0000%" width="24.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span style="color: rgb(0, 0, 0);box-sizing: border-box;"><span leaf="">利用可能性</span></span></strong></p></section></td><td data-colwidth="28.0000%" width="28.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">高</span></p></section></td><td data-colwidth="28.0000%" width="28.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;color: rgb(0, 0, 0);box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">技术细节状态</span></strong></p></section></td><td data-colwidth="20.0000%" width="20.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;color: rgb(100, 130, 228);box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">已公开</span></strong></p></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="24.0000%" width="24.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;color: rgb(0, 0, 0);padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">CVSS 3.1</span></strong></p></section></td><td data-colwidth="28.0000%" width="28.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">8.1</span></p></section></td><td data-colwidth="28.0000%" width="28.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;color: rgb(0, 0, 0);padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">在野利用状态</span></strong></p></section></td><td data-colwidth="20.0000%" width="20.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">未发现</span></p></section></td></tr></tbody></table>  
  
  
**01**  
  
**漏洞影响范围**  
  
  
  
受影响的 WordPress 核心版本：  
  
WordPress 7.1 分支 < 7.1.2  
  
WordPress 7.0 分支 < 7.0.6  
  
WordPress 6.9 分支 < 6.9.9  
  
WordPress 6.8 分支 < 6.8.10  
  
WordPress 6.7 分支 < 6.7.9  
  
WordPress 6.6 分支 < 6.6.9  
  
WordPress 6.5 分支 < 6.5.12  
  
WordPress 6.4 分支 < 6.4.12  
  
WordPress 6.3 分支 < 6.3.12  
  
WordPress 6.2 分支 < 6.2.13  
  
WordPress 6.1 分支 < 6.1.14  
  
WordPress 6.0 分支 < 6.0.16  
  
WordPress 5.9 分支 < 5.9.18  
  
WordPress 5.8 分支 < 5.8.17  
  
WordPress 5.7 分支 < 5.7.19  
  
WordPress 5.6 分支 < 5.6.21  
  
WordPress 5.5 分支 < 5.5.22  
  
WordPress 5.4 分支 < 5.4.23  
  
WordPress 5.3 分支 < 5.3.25  
  
WordPress 5.2 分支 < 5.2.28  
  
WordPress 5.1 分支 < 5.1.26  
  
WordPress 5.0 分支 < 5.0.29  
  
WordPress 4.9 分支 < 4.9.33  
  
WordPress 4.8 分支 < 4.8.32  
  
WordPress 4.7 分支 < 4.7.37  
  
  
**02**  
  
**修复建议**  
  
  
  
**正式防护方案**  
  
WordPress 7.1 分支升级至 7.1.2  
  
WordPress 7.0 分支升级至 7.0.6  
  
WordPress 6.9 分支升级至 6.9.9  
  
WordPress 6.8 分支升级至 6.8.10  
  
WordPress 6.7 分支升级至 6.7.9  
  
WordPress 6.6 分支升级至 6.6.9  
  
WordPress 6.5 分支升级至 6.5.12  
  
WordPress 6.4 分支升级至 6.4.12  
  
WordPress 6.3 分支升级至 6.3.12  
  
WordPress 6.2 分支升级至 6.2.13  
  
WordPress 6.1 分支升级至 6.1.14  
  
WordPress 6.0 分支升级至 6.0.16  
  
WordPress 5.9 分支升级至 5.9.18  
  
WordPress 5.8 分支升级至 5.8.17  
  
WordPress 5.7 分支升级至 5.7.19  
  
WordPress 5.6 分支升级至 5.6.21  
  
WordPress 5.5 分支升级至 5.5.22  
  
WordPress 5.4 分支升级至 5.4.23  
  
WordPress 5.3 分支升级至 5.3.25  
  
WordPress 5.2 分支升级至 5.2.28  
  
WordPress 5.1 分支升级至 5.1.26  
  
WordPress 5.0 分支升级至 5.0.29  
  
WordPress 4.9 分支升级至 4.9.33  
  
WordPress 4.8 分支升级至 4.8.32  
  
WordPress 4.7 分支升级至 4.7.37  
  
  
**03**  
  
**漏洞描述**  
  
  
  
该漏洞源于 WordPress 在处理页面请求、生成模板路径和加载模板文件时，未对用户输入和最终文件路径进行充分校验。  
  
  
WordPress 根据页面名称等请求参数选择页面模板。攻击者构造的多重编码路径可通过早期过滤，在后续模板解析时被解码为目录跳转符号。由于模板加载过程未充分检查最终文件路径是否仍位于允许的主题目录内，攻击者可能使系统将主题目录之外的本地 PHP 文件作为模板加载。  
  
  
在具备 PEAR 等额外环境条件时，该文件包含能力可进一步被用于写入并执行恶意 PHP 代码，造成敏感信息泄露、网站内容篡改或服务中断，影响范围受 PHP 服务账户权限限制。  
  
  
**04**  
  
**漏洞复现**  
  
  
  
360 漏洞研究院已成功复现 WordPress Core 未认证路径遍历与本地文件包含漏洞（CVE-2026-87902），构造并发送对应 Payload，通过读取服务器文件验证了该漏洞。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/dZ7ia5iaWFzziblG1wWl4xvOw8WtERwTxAbm6hJOEZUthcweXksRaqT6LIZoN19WqAcsz9qpcsfmuH3YxxEHYsribEluynG4h542JTXWkL2DIeM/640?wx_fmt=png&from=appmsg "")  
  
WordPress Core 未认证路径遍历与本地文件包含漏洞  
  
  
**05**  
  
**产品侧支持情况**  
  
  
  
**360安全智能体：**  
支持该漏洞攻击的智能分析**。**  
  
**360测绘云 Quake**  
：默认支持该产品的指纹识别。  
  
**360高级持续性威胁预警系统**  
：预计 2026年9月24日发布规则更新包，支持该漏洞利用行为的检测。  
  
**360资产与漏洞检测管理系统**  
：预计 2026年9月24日发布规则更新包，支持该漏洞利用行为的检测。  
**本地安全大脑**  
：默认支持该漏洞的PoC检测。  
  
  
**06**  
  
**时间线**  
  
  
  
2026年9月23日，360漏洞研究院发布本安全风险通告。  
  
  
**07**  
  
参考链接  
  
  
  
https://github.com/WordPress/wordpress-develop/security/advisories/GHSA-7hp8-65ch-5whp  
  
https://github.com/ressl/cve-2026-87902-poc  
  
  
08  
  
更多漏洞情报  
  
  
  
“扫描下方二维码，进入公众号粉丝交流群。更多一手网安资讯、漏洞预警、技术干货和技术交流等您参与！”  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/dZ7ia5iaWFzz8YToicKab1BicPnEdr7jiatvQUVWSMnYTBeG5ibibgxkGAG1rF4pUdpowPcCmokOO5tp4UjjhUsos4Zf4VwE1aM9NTUz3ogfgdwwFw/640?wx_fmt=gif&from=appmsg "")  
  
  
建议您订阅360数字安全-漏洞情报服务，获取更多漏洞情报详情以及处置建议，让您的企业远离漏洞威胁。  
  
  
邮箱：360VRI@360.cn  
  
网址：https://vi.loudongyun.360.net  
  
  
  
“洞”悉网络威胁，守护数字安全  
  
  
**关于我们**  
  
  
360 漏洞研究院，隶属于360数字安全集团。其成员常年入选谷歌、微软、华为等厂商的安全精英排行榜, 并获得谷歌、微软、苹果史上最高漏洞奖励。研究院是中国首个荣膺Pwnie Awards“史诗级成就奖”，并获得多个Pwnie Awards提名的组织。累计发现并协助修复谷歌、苹果、微软、华为、高通等全球顶级厂商CVE漏洞3000多个，收获诸多官方公开致谢。研究院也屡次受邀在BlackHat，Usenix Security，Defcon等极具影响力的工业安全峰会和顶级学术会议上分享研究成果，并多次斩获信创挑战赛、天府杯等顶级黑客大赛总冠军和单项冠军。研究院将凭借其在漏洞挖掘和安全攻防方面的强大技术实力，帮助各大企业厂商不断完善系统安全，为数字安全保驾护航，筑造数字时代的安全堡垒。  
  
  

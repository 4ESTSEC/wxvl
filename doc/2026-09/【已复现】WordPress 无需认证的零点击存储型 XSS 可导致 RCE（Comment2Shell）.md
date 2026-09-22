#  【已复现】WordPress 无需认证的零点击存储型 XSS 可导致 RCE（Comment2Shell）  
原创 360漏洞研究院
                    360漏洞研究院  360漏洞研究院   2026-09-22 04:16  
  
WordPress Core 存在未经身份认证的存储型跨站脚本（XSS）漏洞。攻击者可提交特制评论，利用评论格式化处理缺陷，在访问者浏览器中执行恶意脚本；满足额外条件时，可借助管理员会话安装恶意插件，进一步实现服务器端远程代码执行（RCE）。  
  
  
**利用前置条件：**  
- 恶意评论能够被目标访问者加载；  
  
- 访问者已登录具有插件安装权限的管理员账户，且站点允许插件上传、文件写入及相关 PHP 文件执行。  
  
目前 **360漏洞挖掘智能体已成功复现该漏洞**  
。本文包含完整影响范围、正式修复方案、技术原理与复现细节。  
  
  
<table><tbody><tr style="box-sizing: border-box;"><td colspan="4" data-colwidth="100.0000%" width="100.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;background-color: rgb(100, 130, 228);box-sizing: border-box;padding: 0px;"><section style="text-align: center;color: rgb(255, 255, 255);box-sizing: border-box;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">漏洞概述</span></strong></p></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="24.0000%" width="24.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;color: rgb(0, 0, 0);padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">漏洞名称</span></strong></p></section></td><td colspan="3" data-colwidth="76.0000%" width="76.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><span style="box-sizing: border-box;"><span leaf="">WordPress Core 存储型跨站脚本漏洞</span></span></p></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="24.0000%" width="24.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;color: rgb(0, 0, 0);padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">漏洞编号</span></strong></p></section></td><td colspan="3" data-colwidth="76.0000%" width="76.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">CVE-2026-93485</span></p></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="24.0000%" width="24.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;color: rgb(0, 0, 0);padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">公开时间</span></strong></p></section></td><td data-colwidth="28.0000%" width="28.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">2026-09-18</span></p></section></td><td data-colwidth="28.0000%" width="28.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span style="color: rgb(0, 0, 0);box-sizing: border-box;"><span leaf="">POC状态</span></span></strong></p></section></td><td data-colwidth="20.0000%" width="20.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;color: rgb(100, 130, 228);box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">已公开</span></strong></p></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="24.0000%" width="24.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;color: rgb(0, 0, 0);padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">漏洞类型</span></strong></p></section></td><td data-colwidth="28.0000%" width="28.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">XSS</span></p></section></td><td data-colwidth="28.0000%" width="28.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;color: rgb(0, 0, 0);padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">EXP状态</span></strong></p></section></td><td data-colwidth="20.0000%" width="20.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;color: rgb(100, 130, 228);box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">已公开</span></strong></p></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="24.0000%" width="24.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span style="color: rgb(0, 0, 0);box-sizing: border-box;"><span leaf="">利用可能性</span></span></strong></p></section></td><td data-colwidth="28.0000%" width="28.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">高</span></p></section></td><td data-colwidth="28.0000%" width="28.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;color: rgb(0, 0, 0);box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">技术细节状态</span></strong></p></section></td><td data-colwidth="20.0000%" width="20.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;color: rgb(100, 130, 228);box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">已公开</span></strong></p></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="24.0000%" width="24.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;color: rgb(0, 0, 0);padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">CVSS 3.1</span></strong></p></section></td><td data-colwidth="28.0000%" width="28.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">7.1</span></p></section></td><td data-colwidth="28.0000%" width="28.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;color: rgb(0, 0, 0);padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">在野利用状态</span></strong></p></section></td><td data-colwidth="20.0000%" width="20.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">未发现</span></p></section></td></tr></tbody></table>  
  
  
**01**  
  
**漏洞影响范围**  
  
  
  
受影响的 WordPress 核心版本：  
  
WordPress 7.1 分支 < 7.1.1  
  
WordPress 7.0 分支 < 7.0.5  
  
WordPress 6.9 分支 < 6.9.8  
  
WordPress 6.8 分支 < 6.8.9  
  
WordPress 6.7 分支 < 6.7.8  
  
WordPress 6.6 分支 < 6.6.8  
  
WordPress 6.5 分支 < 6.5.11  
  
WordPress 6.4 分支 < 6.4.11  
  
WordPress 6.3 分支 < 6.3.11  
  
WordPress 6.2 分支 < 6.2.12  
  
WordPress 6.1 分支 < 6.1.13  
  
WordPress 6.0 分支 < 6.0.15  
  
WordPress 5.9 分支 < 5.9.17  
  
WordPress 5.8 分支 < 5.8.16  
  
WordPress 5.7 分支 < 5.7.18  
  
WordPress 5.6 分支 < 5.6.20  
  
WordPress 5.5 分支 < 5.5.21  
  
WordPress 5.4 分支 < 5.4.22  
  
WordPress 5.3 分支 < 5.3.24  
  
WordPress 5.2 分支 < 5.2.27  
  
WordPress 5.1 分支 < 5.1.25  
  
WordPress 5.0 分支 < 5.0.28  
  
WordPress 4.9 分支 < 4.9.32  
  
WordPress 4.8 分支 < 4.8.31  
  
WordPress 4.7 分支 < 4.7.36  
  
  
**02**  
  
**修复建议**  
  
  
  
**正式防护方案**  
  
WordPress 7.1 分支升级至 7.1.1  
  
WordPress 7.0 分支升级至 7.0.5  
  
WordPress 6.9 分支升级至 6.9.8  
  
WordPress 6.8 分支升级至 6.8.9  
  
WordPress 6.7 分支升级至 6.7.8  
  
WordPress 6.6 分支升级至 6.6.8  
  
WordPress 6.5 分支升级至 6.5.11  
  
WordPress 6.4 分支升级至 6.4.11  
  
WordPress 6.3 分支升级至 6.3.11  
  
WordPress 6.2 分支升级至 6.2.12  
  
WordPress 6.1 分支升级至 6.1.13  
  
WordPress 6.0 分支升级至 6.0.15  
  
WordPress 5.9 分支升级至 5.9.17  
  
WordPress 5.8 分支升级至 5.8.16  
  
WordPress 5.7 分支升级至 5.7.18  
  
WordPress 5.6 分支升级至 5.6.20  
  
WordPress 5.5 分支升级至 5.5.21  
  
WordPress 5.4 分支升级至 5.4.22  
  
WordPress 5.3 分支升级至 5.3.24  
  
WordPress 5.2 分支升级至 5.2.27  
  
WordPress 5.1 分支升级至 5.1.25  
  
WordPress 5.0 分支升级至 5.0.28  
  
WordPress 4.9 分支升级至 4.9.32  
  
WordPress 4.8 分支升级至 4.8.31  
  
WordPress 4.7 分支升级至 4.7.36  
  
  
**03**  
  
**漏洞描述**  
  
  
  
CVE-2026-93485 位于 WordPress Core 的评论格式化流程，核心问题是输入净化后，后续 HTML 重写再次改变了数据的语法边界。  
  
  
评论保存时，wp_kses() 允许使用带 cite 属性的 <blockquote> 标签，并保留属性值中的换行。评论展示时，wpautop() 将标签内部换行替换为 <!-- wpnl --> 占位符。随后用于调整 <p> 与 <blockquote> 嵌套关系的正则表达式未正确识别引号边界，将占位符中的 > 误判为标签结束位置，导致 <p> 被插入属性值内部。  
  
  
在受影响的主题渲染路径中，后续 wptexturize() 进一步改变引号，导致原本作为普通文本保存的内容被浏览器解析为事件处理属性，从而执行 JavaScript。若脚本在具备插件安装权限的管理员会话中运行，便可能读取安装表单令牌并上传恶意插件，最终以 Web 服务进程权限执行服务器代码。  
  
  
官方补丁改进了 wpautop() 中的正则表达式，使其正确处理单双引号包围的属性值，避免将属性内部的 > 误认为标签结束符，从源头阻断上述转换链。  
  
  
**04**  
  
**漏洞复现**  
  
  
  
360 漏洞研究院已成功复现 WordPress Core 存储型跨站脚本漏洞（Comment2Shell），构造并发送对应 Payload，成功验证该漏洞可导致未认证远程代码执行，最终获取到目标站点 Shell 权限。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/dZ7ia5iaWFzz8pc6ibc6M18suqfpFMUXdKGcfM4zu0ADVzSDiar7zKEonHPlHKE4HuWnQiaf3NCEtnK6kbGfBxxibiajG7Q9bh8RRgibMgqgJNkicUbA/640?wx_fmt=png&from=appmsg "")  
  
WordPress Core 存储型 XSS 可导致 RCE  
  
  
**05**  
  
**产品侧支持情况**  
  
  
  
**360安全智能体：**  
支持该漏洞攻击的智能分析**。**  
  
**360测绘云 Quake**  
：默认支持该产品的指纹识别。  
  
**360高级持续性威胁预警系统**  
：预计 2026年9月23日发布规则更新包，支持该漏洞利用行为的检测。  
  
**360资产与漏洞检测管理系统**  
：预计 2026年9月23日发布规则更新包，支持该漏洞利用行为的检测。  
**本地安全大脑**  
：默认支持该漏洞的PoC检测。  
  
  
**06**  
  
**时间线**  
  
  
  
2026年9月22日，360漏洞研究院发布本安全风险通告。  
  
  
**07**  
  
参考链接  
  
  
  
https://idnsec.com/research/comment2shell-zero-click-pre-auth-xss-to-rce-in-wordpress-core/  
  
https://wordpress.org/news/2026/09/wordpress-7-1-1-maintenance-and-security-release/  
  
https://wordpress.org/documentation/wordpress-version/version-7-1-1/  
  
https://www.patchstack.com/articles/wordpress-7-1-1-maintenance-and-security-release/  
  
  
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
  

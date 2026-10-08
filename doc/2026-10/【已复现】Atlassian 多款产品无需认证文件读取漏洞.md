#  【已复现】Atlassian 多款产品无需认证文件读取漏洞  
原创 360漏洞研究院
                    360漏洞研究院  360漏洞研究院   2026-10-08 02:30  
  
Atlassian 多款产品存在未授权文件读取漏洞 CVE-2026-21589。远程攻击者可通过构造特殊请求，读取目标 Web 应用根目录内的文件，造成配置及敏感信息泄露。  
  
  
**利用前置条件：**  
  
目标运行受影响版本，攻击者能够访问存在漏洞的 Web 资源接口，并事先知道目标文件的准确名称和路径。攻击无需登录或用户交互，但无法通过该漏洞列出目录内容。实际危害取决于可读取文件中是否包含敏感信息。  
  
  
目前**360图龙锋 · 漏洞挖掘智能体已成功复现该漏洞**  
。本文包含完整影响范围、正式修复方案、技术原理与复现细节。  
  
  
<table><tbody><tr style="box-sizing: border-box;"><td colspan="4" data-colwidth="100.0000%" width="100.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;background-color: rgb(100, 130, 228);box-sizing: border-box;padding: 0px;"><section style="text-align: center;color: rgb(255, 255, 255);box-sizing: border-box;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">漏洞概述</span></strong></p></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="24.0000%" width="24.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;color: rgb(0, 0, 0);padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">漏洞名称</span></strong></p></section></td><td colspan="3" data-colwidth="76.0000%" width="76.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">Atlassian 多款产品未授权文件读取漏洞</span></p></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="24.0000%" width="24.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;color: rgb(0, 0, 0);padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">漏洞编号</span></strong></p></section></td><td colspan="3" data-colwidth="76.0000%" width="76.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">CVE-2026-21589</span></p></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="24.0000%" width="24.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;color: rgb(0, 0, 0);padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">公开时间</span></strong></p></section></td><td data-colwidth="28.0000%" width="28.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">2026-10-06</span></p></section></td><td data-colwidth="28.0000%" width="28.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span style="color: rgb(0, 0, 0);box-sizing: border-box;"><span leaf="">POC状态</span></span></strong></p></section></td><td data-colwidth="20.0000%" width="20.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;color: rgb(100, 130, 228);box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">已公开</span></strong></p></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="24.0000%" width="24.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;color: rgb(0, 0, 0);padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">漏洞类型</span></strong></p></section></td><td data-colwidth="28.0000%" width="28.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">访问控制不当</span></p></section></td><td data-colwidth="28.0000%" width="28.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;color: rgb(0, 0, 0);padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">EXP状态</span></strong></p></section></td><td data-colwidth="20.0000%" width="20.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;color: rgb(100, 130, 228);box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">已公开</span></strong></p></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="24.0000%" width="24.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span style="color: rgb(0, 0, 0);box-sizing: border-box;"><span leaf="">利用可能性</span></span></strong></p></section></td><td data-colwidth="28.0000%" width="28.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">高</span></p></section></td><td data-colwidth="28.0000%" width="28.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;color: rgb(0, 0, 0);box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">技术细节状态</span></strong></p></section></td><td data-colwidth="20.0000%" width="20.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;color: rgb(100, 130, 228);box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">已公开</span></strong></p></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="24.0000%" width="24.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;color: rgb(0, 0, 0);padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">CVSS 4.0</span></strong></p></section></td><td data-colwidth="28.0000%" width="28.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">9.3</span></p></section></td><td data-colwidth="28.0000%" width="28.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;color: rgb(0, 0, 0);padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">在野利用状态</span></strong></p></section></td><td data-colwidth="20.0000%" width="20.0000%" style="border-width: 1px;border-color: rgb(100, 130, 228);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="font-size: 12px;padding: 0px 8px;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">未发现</span></p></section></td></tr></tbody></table>  
  
  
**01**  
  
**漏洞影响范围**  
  
  
  
受影响的版本：  
- Bitbucket Data Center 9.4.x分支 < 9.4.26  
  
- Bitbucket Data Center 10.2.x分支 < 10.2.8  
  
- Bitbucket Data Center 10.5.x分支 < 10.5.1  
  
- Confluence Data Center 9.2.x分支 < 9.2.26  
  
- Confluence Data Center 10.2.x分支 < 10.2.19  
  
- Jira Service Management Data Center 5.12.x分支 < 5.12.40  
  
- Jira Service Management Data Center 10.3.x分支 < 10.3.26  
  
- Jira Service Management Data Center 11.3.x分支 < 11.3.12  
  
- Jira Software Data Center 9.12.x分支 < 9.12.40  
  
- Jira Software Data Center 10.3.x分支 < 10.3.26  
  
- Jira Software Data Center 11.3.x分支 < 11.3.12  
  
- Bamboo Data Center 10.2.x分支 < 10.2.24  
  
- Bamboo Data Center 12.1.x分支 < 12.1.12  
  
- Crowd Data Center 6.3.x分支 < 6.3.7  
  
- Crowd Data Center 7.0.x分支 < 7.0.3  
  
- Crowd Data Center 7.1.x分支 < 7.1.7  
  
- Crowd Data Center 7.2.x分支 < 7.2.4  
  
- Crucible < 4.9.15  
  
- Fisheye < 4.9.15  
  
Atlassian 已修复受影响的 Cloud 产品，云端客户无需采取修复操作。  
  
  
**02**  
  
**修复建议**  
  
  
  
**正式防护方案**  
- Bitbucket Data Center 9.4 分支升级至 9.4.26 或同分支更高修复版本。  
  
- Bitbucket Data Center 10.2 分支升级至 10.2.8 或同分支更高修复版本。  
  
- Bitbucket Data Center 10.5 分支升级至 10.5.1 或同分支更高修复版本。  
  
- Confluence Data Center 9.2 分支升级至 9.2.26 或同分支更高修复版本。  
  
- Confluence Data Center 10.2 分支升级至 10.2.19 或同分支更高修复版本。  
  
- Jira Service Management Data Center 5.12 分支升级至 5.12.40 或同分支更高修复版本。  
  
- Jira Service Management Data Center 10.3 分支升级至 10.3.26 或同分支更高修复版本。  
  
- Jira Service Management Data Center 11.3 分支升级至 11.3.12 或同分支更高修复版本。  
  
- Jira Software Data Center 9.12 分支升级至 9.12.40 或同分支更高修复版本。  
  
- Jira Software Data Center 10.3 分支升级至 10.3.26 或同分支更高修复版本。  
  
- Jira Software Data Center 11.3 分支升级至 11.3.12 或同分支更高修复版本。  
  
- Bamboo Data Center 10.2 分支升级至 10.2.24 或同分支更高修复版本。  
  
- Bamboo Data Center 12.1 分支升级至 12.1.12 或同分支更高修复版本。  
  
- Crowd Data Center 6.3 分支升级至 6.3.7 或同分支更高修复版本。  
  
- Crowd Data Center 7.0 分支升级至 7.0.3 或同分支更高修复版本。  
  
- Crowd Data Center 7.1 分支升级至 7.1.7 或同分支更高修复版本。  
  
- Crowd Data Center 7.2 分支升级至 7.2.4 或同分支更高修复版本。  
  
- Crucible 4.9 分支升级至 4.9.15 或同分支更高修复版本。  
  
- Fisheye 4.9 分支升级至 4.9.15 或同分支更高修复版本。  
  
  
  
  
**03**  
  
**漏洞描述**  
  
  
  
根据 watchTowr 分析，漏洞涉及多个产品共享的 atlassian-plugins-webresource 组件。该组件处理资源路径时，会将 :: 转换为 /，相关路径校验不足使攻击者能够通过资源接口进行目录穿越，读取原本不应公开的 WEB-INF 等目录中的文件。  
  
  
**漏洞存在以下限制：**  
- **读取范围受限：**  
公开验证的读取范围为当前 Web 应用根目录及其子目录，无法越出对应的 Tomcat 应用上下文，不能据此读取服务器文件系统中的任意文件。  
  
- **需要准确路径：**  
不支持目录枚举，且具体资源入口、可读取文件可能因产品和配置不同而有所差异。  
  
在部分启用 Crowd 单点登录的 Jira 部署中，WEB-INF/classes/crowd.properties 保存应用名、应用密码及 Crowd 地址，可能成为敏感信息泄露目标。  
  
  
文件读取本身不依赖其他服务，但进一步获取管理员权限需要额外条件。文章展示的利用链需要结合 Crowd：泄露的凭据有效、Crowd 接口可达、来源 IP 获准，且应用和目录具备相应写权限时，攻击者才可能创建用户并将其加入 Jira 管理员组。若 Crowd 严格限制来源 IP，后续利用还需要内网访问能力或额外的 SSRF 等漏洞，因此不能将管理员权限获取视为所有受影响实例的必然结果。  
  
  
**04**  
  
**漏洞复现**  
  
  
  
360 漏洞研究院依托图龙锋漏洞挖掘智能体已成功复现 CVE-2026-21589 漏洞，构造并发送对应 Payload，泄露文件内容，并且获得管理员账户权限。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/dZ7ia5iaWFzzibibX6iaCAWsHfib8xCFS8MHiaIz2C5xNsFzlFIuLaDEpF1ibGy3PDg9cZW2d87eXKUqSasC9Dq3aj9GgPKMlbRYY9Mq2rLnXHtNXDA/640?wx_fmt=png&from=appmsg "")  
  
Atlassian 未授权文件读取漏洞  
  
  
**05**  
  
**产品侧支持情况**  
  
  
  
**360安全智能体：**  
支持该漏洞攻击的智能分析**。**  
  
**360测绘云 Quake**  
：默认支持该产品的指纹识别。  
  
**360高级持续性威胁预警系统**  
：预计 2026年10月9日发布规则更新包，支持该漏洞利用行为的检测。  
  
**360资产与漏洞检测管理系统**  
：预计 2026年10月9日发布规则更新包，支持该漏洞利用行为的检测。  
**本地安全大脑**  
：默认支持该漏洞的PoC检测。  
  
  
**06**  
  
**时间线**  
  
  
  
2026年10月8日，360漏洞研究院发布本安全风险通告。  
  
  
**07**  
  
参考链接  
  
  
  
https://jira.atlassian.com/browse/BAM-26567  
  
https://github.com/watchtowrlabs/watchTowr-vs-Atlassian-CVE-2026-21589  
  
  
08  
  
更多漏洞情报  
  
  
  
“扫描下方二维码，进入公众号粉丝交流群。更多一手网安资讯、漏洞预警、技术干货和技术交流等您参与！”  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/dZ7ia5iaWFzz8YToicKab1BicPnEdr7jiatvQUVWSMnYTBeG5ibibgxkGAG1rF4pUdpowPcCmokOO5tp4UjjhUsos4Zf4VwE1aM9NTUz3ogfgdwwFw/640?wx_fmt=gif&from=appmsg "")  
  
  
建议您订阅360漏洞情报服务，获取更多漏洞情报详情以及处置建议，让您的企业远离漏洞威胁。  
  
  
邮箱：360VRI@360.cn  
  
网址：https://vi.loudongyun.360.net  
  
  
  
“洞”悉网络威胁，守护数字安全  
  
  
**关于我们**  
  
  
360 漏洞研究院，隶属于360安全能力中心。其成员常年入选谷歌、微软、华为等厂商的安全精英排行榜, 并获得谷歌、微软、苹果史上最高漏洞奖励。研究院是中国首个荣膺Pwnie Awards“史诗级成就奖”，并获得多个Pwnie Awards提名的组织。累计发现并协助修复谷歌、苹果、微软、华为、高通等全球顶级厂商CVE漏洞3000多个，收获诸多官方公开致谢。研究院也屡次受邀在BlackHat，Usenix Security，Defcon等极具影响力的工业安全峰会和顶级学术会议上分享研究成果，并多次斩获信创挑战赛、天府杯等顶级黑客大赛总冠军和单项冠军。研究院将凭借其在漏洞挖掘和安全攻防方面的强大技术实力，帮助各大企业厂商不断完善系统安全，为数字安全保驾护航，筑造数字时代的安全堡垒。  
  

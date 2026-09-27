#  黑客禁用 EPP 并在受感染的 Windows 域中部署 Sliver 漏洞利用程序  
Rhinoer
                    Rhinoer  犀牛安全   2026-09-26 16:00  
  
![](https://mmbiz.qpic.cn/mmbiz_png/vO1zY1O9p8InEjsibg2PTb0U0E33EWR8y3Xa6A0ZXDjib6uAgJa8Tnkn7WKp6PhhEvDWlwtQkf70VnCrYJfAmuiaooWTQKQdkJRZq6MA1CKjOg/640?wx_fmt=png&from=appmsg "")  
  
一项新的入侵活动表明，Windows 域可以多么迅速地被转化为进一步入侵的跳板。攻击者利用 Sliver 命令与控制信标、创建账户、窃取凭证和远程管理等手段，在取得初步控制后，最终建立起对系统的控制。  
  
此次攻击活动由一台暴露的服务器发起，目标是一家未具名的美国机构。其脚本是为真实的Active Directory环境编写的，计划在18台主机上进行部署。然而，已恢复的材料中没有证据表明此次事件中使用了勒索软件。  
  
《猎人账簿》的分析师将该行动认定为高风险的后渗透工具包，并将其追踪为 UTA-2026-024。  
  
该研究将该基础设施与一起已确认的勒索软件事件联系起来，但没有指明此次入侵背后的幕后黑手，也没有得出他们部署了加密器的结论。  
  
《猎人账簿》在一份与网络安全新闻 (CSN) 分享的报告中称，攻击者将普通的公共工具与对受害者网络的异常详细的了解结合起来。  
  
最终得到的是一个持久的访问控制包，其设计目的是禁用安全措施、窃取凭证并保持其控制通道可用。  
## 黑客禁用端点保护  
  
进入域后，操作员编写脚本创建了一个具有永不过期密码的 Active Directory 帐户，并将其直接添加到域管理员组。  
  
他们还创建了本地管理员，启用了远程桌面协议访问，并关闭了网络级身份验证，从而扩大了以后可采取的行动路径。  
  
脚本停止并禁用了与受害者终端保护产品相关的八项服务，然后检查了每项服务的状态。  
  
他们还收集了 SAM、SYSTEM 和 SECURITY 注册表单元，用于离线密码破解，同时，单独的 LSASS 内存转储和 Mimikatz 提供了获取凭据的其他途径。  
  
一个核心问题是该活动的持续性。计划任务以系统权限运行，使用了伪造的作者信息，并包含了篡改后的注册日期。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/vO1zY1O9p8KK6NvRzlLt5UyhukUx7X7tjiaIcgreBAbZZnoIr2HWwIzpPNCE1K3f9jZUG4LGDCibcLINIJGlzbvqx2EYLGMns9o9LRrdPVMUk/640?wx_fmt=png&from=appmsg "")  
  
每周的一项任务会下载最新的攻击链，但不保存固定的有效载荷，这种策略类似于  EtherRAT 攻击中的远程计划任务交付。  
  
该团队还通过受害者的管理界面篡改了其DNS内容过滤器。他们将攻击者的域名添加到允许列表中，并在内部DNS中添加了一条匹配的记录，使得该域名能够在内部解析，从而绕过原本旨在阻止它的安全控制。  
  
这种方法反映了 Windows 入侵中更广泛的模式，即在获得访问权限后，受信任的管理功能就变成了传播系统。  
  
近期有关伪造安装程序攻击活动导致 Windows Defender 失效的报道  还显示，攻击者利用安装程序工作流程和计划任务来削弱安全控制，然后再维持访问权限。在这两种情况下，危险并非来自单一工具，而是围绕该工具的一系列操作。  
## 区块链C2使响应复杂化  
  
除了 Sliver 之外，该工具包还使用了一个 Node.js 植入程序，该程序从以太坊智能合约获取其命令服务器。  
  
该合同中记录的第一个域名与插入受害者 DNS 配置中的域名相同，直接将行动中看似不同的两个部分联系起来。  
  
该合约在五个月内更换了五次域名，使得简单的域名封锁措施难以奏效。然而，合约本身却保持不变且公开可读，这为防御者提供了更好的追踪依据。  
  
相关信标每 60 秒与其主服务器联系一次，没有测量到时间变化，这是网络搜寻的一个有用信号。  
  
建议采取的措施是重置受影响域的凭据，而不仅仅是已知帐户的凭据；检查特权组添加和 SYSTEM 任务；恢复 DNS 允许列表；轮换过滤器管理员密码；并删除植入的内部 DNS 条目。  
  
团队还应检查是否启用了 RDP 但禁用了网络级身份验证，并监控合约以防 C2 发生后续更改。安全团队对于公共工具应优先考虑行为而非宽泛的特征码。  
  
基线计划任务，对以 SYSTEM 身份运行的无文件下载命令发出警报，并审查端点保护服务的突然变更。  
  
研究相关Windows攻击手法的读者可以对比 针对德国的Sliver植入活动 和 勒索软件SYSTEM任务滥用案例，从中可以看出，一些常见的组件是如何串联起来，最终导致企业级安全事件的。这种模式值得持续、仔细地关注。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/vO1zY1O9p8Jt0mom7icYXBLdyglO0YRKzhE1Ube8muGywg7B6oCuGnjmGmfqCiaTsbdX4qgtqDXIFeBmNlLpy3EELU6mziczYMmKz3QsQK8zug/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/vO1zY1O9p8JAsgibop8ZZibJLxPQZaSrmK5ujouL3ROyoSsnIicy6ibyOyoBLd65CFoU901lAgZpr28icmD2A15MbESqy19LxAqlxs2vXu3cVpu8/640?wx_fmt=png&from=appmsg "")  
  
**注意：**  
 IP 地址和域名已被故意隐去（例如，  [.]），以防止意外解析或超链接。请仅在受控的威胁情报平台（例如 MISP、VirusTotal 或您的 SIEM）中重新启用。  
  
  
信息来源：CyberSecurityNews  
  

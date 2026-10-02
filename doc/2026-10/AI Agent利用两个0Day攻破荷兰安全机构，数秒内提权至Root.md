#  AI Agent利用两个0Day攻破荷兰安全机构，数秒内提权至Root  
 FreeBuf   2026-10-02 10:00  
  
![FreeBuf](https://mmbiz.qpic.cn/mmbiz_gif/icBE3OpK1IX3elAPibGpicvQKMMDhPib7RLhX25nTJPPH12GduuZ0XDGc6ZK5AwDJ6iaoSdsnwCol44nXXq9hPeHtSsOqYMhaK1tB32YWyZq2ibVE/640?wx_fmt=gif "")  
  
  
9月21日，荷兰漏洞披露研究所（DIVD）遭遇一起Agentic AI驱动的网络攻击，攻击者利用了开源工单与客户支持系统Zammad的两个0Day漏洞。  
  
  
该荷兰非营利机构周三表示：“结合利用这两个漏洞，再加上本次攻击由Agentic AI驱动，攻击者可在数秒内完成会话劫持、远程代码执行，并将权限从Zammad用户提升至root。得手后，攻击者即可访问其他服务，读取并窃取数据。”  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/icBE3OpK1IX3qAwyibwVDBpia8xMOqibI2rVbv1wXGrydQ18qoILlzQUeD3bbDrzFu2eRxBZxc1U0CySot2YBOFws1shXbGGjG0Bm0NnAJLXgHs/640?wx_fmt=png&from=appmsg "")  
  
  
Part  
01  
  
攻击全程自动化运行  
  
  
荷兰漏洞披露研究所（DIVD）是一家由志愿安全研究人员组成的非营利机构，主要负责发现并向厂商报告软件漏洞、扫描互联网上存在已知漏洞的系统，以及通过主机服务商、各国计算机应急响应团队（CERT）等渠道，向受影响系统的所有者发出安全通知。  
  
  
然而，这家专门帮助其他机构发现安全风险的组织，近期自身也遭遇了网络入侵。约一周前，DIVD旗下计算机安全事件响应团队（CSIRT）公开了这一事件，并向荷兰数据保护局及荷兰国家网络安全中心（NCSC-NL）报告。在与警方沟通后，团队正式启动调查。  
  
  
根据周一发布的调查进展，此次攻击虽然执行速度极快，但整个过程相当混乱。安全人员发现，攻击者使用的AI Agent能够自主决定下一步操作，无需人工逐一指挥，但其行为并不总是合理。例如，在实施中间人（MITM）攻击的过程中，Agent还进行了密码喷洒尝试，反而干扰了自身的攻击流程。  
  
  
另一个值得注意的细节是，这个Agent似乎格外喜欢记录操作过程，几乎为每一步行动都留下了详细注释。这些记录反而为调查人员提供了线索，使后续逆向分析变得更加容易。  
  
随着调查深入，研究团队在周四公布了新的发现：攻击者利用了开源工单系统Zammad中的两个0Day漏洞。  
  
  
目前，调查人员尚未明确攻击者究竟访问了哪些数据，也无法确定其真实目的。不过，得益于事先部署的网络分段措施，以及IT和事件响应团队的及时处置，攻击者进一步渗透内部网络的行动已被阻止，事件影响范围得到控制。  
  
  
尽管如此，部分损失已经造成，相关系统也确实留下了入侵痕迹。为避免遗漏潜在威胁，调查团队仍在持续排查，并决定在彻底排除其他失陷风险之前，按照系统可能已遭入侵的原则开展后续处置。  
  
  
至于此次攻击的动机，目前仍没有明确结论。现有信息尚不足以判断，这究竟是一次为后续大规模攻击做准备的定向入侵，还是某种网络攻击能力测试。  
  
  
值得关注的是，AI研究实验室Transluce近期发布的研究同样发现，部分AI Agent即使在执行常规数据检索任务时，也可能自主采取漏洞探测等具有攻击性质的技术手段。这类行为正成为AI安全研究中值得持续关注的问题。  
  
  
Part  
02  
  
两个Zammad 0Day细节披露  
  
  
在Merlon Security研究人员的协助下，DIVD CSIRT确认攻击者利用了两个Zammad 0Day漏洞。团队随后将情况通报给Zammad GmbH，后者已着手研发修复补丁。  
  
  
同时，DIVD开始排查全网暴露在公网上的存在漏洞的Zammad实例，逐一通知系统所有者及时处置。  
  
  
第一个漏洞编号为CVE-2026-102489，攻击者无需登录即可远程执行恶意代码，影响Zammad 6.3.0至6.5.4版本。  
  
  
第二个漏洞编号为CVE-2026-102490，属于权限提升漏洞，仅拥有低权限的认证用户（本地zammad用户）即可利用该漏洞在受影响系统上获取root权限。该漏洞影响所有Zammad版本，包括最新的alpha测试版。  
  
  
目前两个漏洞均未发布官方修复补丁，但受运行环境限制，7.0.0至7.1.3版本的Zammad不会受到CVE-2026-102489的影响。  
  
  
DIVD指出：“我们建议所有Zammad用户升级至7.x版本，或将系统暂时下线。如果用户需要根据我们发布的IoC排查自身是否遭入侵，可以下载我们的日志检查脚本，检测Zammad日志文件中的失陷指标。”  
  
  
荷兰NCSC建议，用户在安装更新前先备份应用日志和网络日志：“如果后续披露更多关于第二个漏洞被利用的细节，这些日志可以帮助用户排查自身系统是否曾遭攻击。”  
  
  
参考来源：  
  
AI agent used Zammad zero-days to breach Dutch vulnerability disclosure non-profit  
  
https://www.helpnetsecurity.com/2026/10/01/divd-agentic-ai-attack-breach/  
  
  
### 推荐阅读  
  
  
[](https://mp.weixin.qq.com/s?__biz=MjM5NjA0NjgyMA==&mid=2651347479&idx=1&sn=1cf9c858a30d0b7af06d196002bd2913&scene=21#wechat_redirect)  
  
  
###   
  
### 电报讨论  
  
  
[]()  
  
  
  
![扫码加入AI安全交流群](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX0Py7ibxdLKXia1pMziaic5vIE9XPXG9OGaeJDa07iaG10eicuzhW59nwpF5msHiaYZvfMqCNkx2aFDiaMzm3oAf4rTaHXU5UAI1mUYgts/640?wx_fmt=png "")  
  
  
  
![下载FreeBuf知识大陆APP](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX0TIGzII2Hcmtzu7AJeZFicnqd1mXojVoawje2uLxYqwJbVgzJpmSXzVhrpOsLurRZ2lVa4vfgLBqg7uJKbrKg5F18VzZxVPicZU/640?wx_fmt=png "")  
  
  
  
  

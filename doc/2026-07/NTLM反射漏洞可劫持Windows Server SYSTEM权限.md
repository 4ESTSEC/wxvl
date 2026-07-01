#  NTLM反射漏洞可劫持Windows Server SYSTEM权限  
 FreeBuf   2026-07-01 04:00  
  
![FreeBuf](https://mmbiz.qpic.cn/mmbiz_gif/icBE3OpK1IX2epSeRHugyh2CJ7P5LuenLe31pKCo1fH5A7IfAs3M705ZD13icicYPc4M0BguzI39emfXvDicmsgcnCEqSLIXenicN0yHnUlhvTLc/640?wx_fmt=gif "")  
  
  
![漏洞示意图](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX1iaQGTAEG8K2uYa10EWXI1tCpaVaA2D3sCZicDPR9IlO3LYO1gtiaBfQbCNTzlNCWPNiaLib16zS4ib1ibsiaPvCwQdM4tTG0rOggfM9w/640?wx_fmt=png "")  
  
  
研究人员已发布针对新型 NTLM 反射绕过漏洞的概念验证（PoC）利用代码，该漏洞可导致攻击者在 Windows Server 2025 上获取 SYSTEM 级别权限，这再次引发了对微软身份验证加固机制有效性的担忧。  
  
  
该漏洞编号为 CVE-2026-24294，表明即使在高危漏洞 CVE-2025-33073（NTLM 反射问题）修复后，Windows 身份验证机制中存在的底层设计缺陷仍未彻底解决。2025 年曝光的 CVE-2025-33073 曾使 NTLM 反射攻击重新成为有效攻击手段，攻击者可诱使 Windows 主机向受控服务发起身份验证，再将验证信息中继回同一台机器以获取 SYSTEM 权限。  
  
  
Part  
01  
  
微软修复方案的局限性  
  
微软此前主要通过修改 SMB 客户端来修复漏洞，阻止目标名称包含额外编组信息的连接请求——攻击者曾利用该技术使远程认证看似本地行为。但研究人员警告这种缓解措施存在局限性：只要能在受控服务器上找到获取本地 NTLM 或 Kerberos 认证的新方法，反射攻击就可能卷土重来。  
  
  
![应用于 CVE-2025-33073 的绕过方法（来源：Synacktiv）](https://mmbiz.qpic.cn/sz_mmbiz_png/icBE3OpK1IX1weBEuiazQK35F6iaFdAeWXv0gsZPWuyso0IkuTOzen03na7iao7d7sLmoibxjfLSyHnADsnIVuaYP1Ln6rQ8qxoc0a2NkhffDpYw/640?wx_fmt=png "")  
  
  
这一预测如今已成现实。新曝光的 CVE-2026-24294 漏洞利用了 Windows 11 24H2 和 Windows Server 2025 引入的特性：允许通过任意 TCP 端口（而非传统的 445 端口）建立 SMB 连接。  
  
  
Part  
02  
  
两阶段攻击流程  
  
该特性本意是增强 SMB 部署灵活性，但实际上为未强制启用 SMB 签名的服务器开辟了新的本地 NTLM 反射路径。攻击主要分为两个阶段：  
  
  
![在 12345 端口启动本地 SMB 服务器并挂载（来源：Synacktiv）](https://mmbiz.qpic.cn/sz_mmbiz_png/icBE3OpK1IX1vAbgv5pbBIX9EtnsMg2AUeLXVZy2HZIzesuMJEa82xHwKbXP9Up6wA8FSO2lN1LQEHQaicDtoJl9stLrlkwK2OWEQySq4RJnc/640?wx_fmt=png "")  
  
- 建立恶意连接：攻击者首先在非标准端口（如 12345）启动本地 SMB 服务器，通过类似net use \127.0.0.1\share /tcpport:12345的命令挂载共享，强制 Windows SMB 客户端与恶意本地服务器建立 TCP 连接。由于 SMB 支持多路复用，多个认证会话可复用同一 TCP 连接，Windows 会优先重用现有连接而非新建连接。  
  
![强制特权服务（如 LSASS）向已挂载共享发起认证（来源：Synacktiv）](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX2RLyRL1KFxdAGZicib6NX1TZ5Q4lW0EYeribDFolhDhsplDiagMichQicicBpscwRibGw9Pic5cfuz7hq63ZibtejoBicCTPxdFfOYChMH8A/640?wx_fmt=png "")  
  
- 中继特权认证：攻击者诱使以 NT AUTHORITY\SYSTEM 身份运行的特权服务（如 LSASS）访问同一共享路径（例如使用改良版 PetitPotam 强制原语）。SMB 客户端通过既有连接向攻击者的本地 SMB 服务器发起认证，由于目标实际指向同一机器，系统会执行本地 NTLM 认证。攻击者捕获特权 NTLM 认证后，使用 Impacket 的 ntlmrelayx 等中继工具将其转发至主机上的真实 SMB 服务，最终建立 SYSTEM 权限的 SMB 会话实现完全本地控制。  
  
  
Part  
03  
  
防护建议与修复情况  
  
Synacktiv 研究人员使用 Impacket 的smbserver.py、ntlmrelayx、改良版本地 PetitPotam 二进制文件及 Windowsnet.exe构建了可靠的 PoC。该漏洞默认影响 Windows Server 2025，但在强制启用 SMB 签名的 Windows 11 24H2 上会因协议完整性层拦截中继攻击而失效。  
  
  
微软已将该问题编号为 CVE-2026-24294，并在 2026 年 3 月补丁星期二发布修复方案。此次事件表明，单纯封堵某一种 NTLM 反射技术远远不够——只要 NTLM 仍被广泛使用且 SMB 签名保持可选状态，攻击者就能持续发现新的特权认证强制与中继方法。  
  
  
防御者需及时安装补丁、强制启用 SMB 签名、减少 NTLM 使用，并密切监控非标准端口上的异常 SMB 流量，这些措施对预防 Windows Server 环境中的 SYSTEM 级入侵至关重要。  
  
  
参考来源：  
  
PoC Released for NTLM Reflection Bypass Flaw that Enables SYSTEM Access on Windows Server  
  
https://cybersecuritynews.com/poc-released-for-ntlm-reflection-bypass-flaw/  
  
  
**推荐阅读**  
  
[](https://mp.weixin.qq.com/s?__biz=MjM5NjA0NjgyMA==&mid=2651341548&idx=1&sn=bb9edaa490d92c0258ff47c5dd29faf4&scene=21#wechat_redirect)  
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
### 电报讨论  
  
  
[]()  
  
  
  
![扫码加入AI安全交流群](https://mmbiz.qpic.cn/sz_mmbiz_png/icBE3OpK1IX0UGcrYtIkSYDEgbDkib0yMF23VlKQibpJyRnibia1cD3no5XF7Je0Sic98ytMyvbY9LhO8tKoxBlnibsAXh8CnBTYoAxLReujuqjomI/640?wx_fmt=png "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX1cNPEia7j7bXCX8P8iaDo801yQlaF965NduoqX5nEfgC2mLLgM6VdzcRdkYkeGebHaia3JRK31e08ibfS1WnmYl8DtvPf83e6XW6k/640?wx_fmt=png&from=appmsg "")  
  
  
  
  

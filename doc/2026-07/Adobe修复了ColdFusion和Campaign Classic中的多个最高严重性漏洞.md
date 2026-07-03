#  Adobe修复了ColdFusion和Campaign Classic中的多个最高严重性漏洞  
鹏鹏同学
                    鹏鹏同学  黑猫安全   2026-07-03 00:45  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/DYqn7TU9icq23jNEt10G1JZJAuFXjTkIfjYJbnucIvX5UZolUmzKc6oe0WfLQWshCic5OQbva9URicE3Lj3ZOIIiaCiaWYdX5G1oLwSG4aicU34gI/640?wx_fmt=png&from=appmsg "")  
  
Adobe已为ColdFusion和Campaign Classic发布安全更新，修复了多个严重漏洞，其中包括七个最高严重性级别的问题（CVSS评分为10.0）。若被成功利用，这些漏洞可能允许攻击者执行任意代码、提升权限、读取敏感文件或绕过安全防护措施。  
  
Adobe强烈建议客户尽快应用更新，以降低遭受入侵的风险。  
  
这些漏洞包括：  
- **CVE-2026-48276、CVE-2026-48283**  
（CVSS评分：10.0）—— 允许攻击者上传恶意文件并执行任意代码。  
  
- **CVE-2026-48277、CVE-2026-48281、CVE-2026-48316**  
（CVSS评分：10.0）—— 输入验证缺陷，可能被攻击者利用以执行任意代码。  
  
- **CVE-2026-48282**  
（CVSS评分：10.0）—— 路径遍历缺陷，可能导致任意代码执行。  
  
- **CVE-2026-48313**  
（CVSS评分：9.3）—— 路径遍历缺陷，可能允许攻击者读取敏感文件。  
  
- **CVE-2026-48315**  
（CVSS评分：9.3）—— 输入验证缺陷，可能导致权限提升。  
  
Adobe已在 **ColdFusion 2023 Update 21**  
 和 **ColdFusion 2025 Update 10**  
 中修复了这些漏洞。安全研究人员 **Anirudh Anand**  
、**Matan Sandori**  
 以及 **2Bsecure**  
 报告了其中的多个漏洞。  
  
Adobe对研究人员发现并报告这些问题、帮助提升产品安全性表示感谢：Anirudh Anand 报告了 CVE-2026-48283 和 CVE-2026-48313，而 Matan Sandori 和 2Bsecure 报告了 CVE-2026-48307。  
  
此外，Adobe还修复了 **Adobe Campaign Classic**  
 中一个追踪编号为 **CVE-2026-48286**  
（CVSS评分：10.0）的严重漏洞。该漏洞源于授权机制缺陷，可能允许攻击者执行任意代码。  
  
该问题影响运行版本 **7.4.3 build 9396及更早版本**  
 的本地部署实例，已在 **build 9397**  
 中修复。Adobe托管的实例不受影响。  
  
Adobe表示，目前尚未发现这些漏洞被积极利用的证据。  
> “Adobe尚未获悉任何针对此次更新所修复问题的野外利用行为。”—— 安全公告中写道。  
  
  
  

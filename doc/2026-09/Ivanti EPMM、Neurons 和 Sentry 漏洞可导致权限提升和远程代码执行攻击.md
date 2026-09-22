#  Ivanti EPMM、Neurons 和 Sentry 漏洞可导致权限提升和远程代码执行攻击  
Rhinoer
                    Rhinoer  犀牛安全   2026-09-21 16:00  
  
![](https://mmbiz.qpic.cn/mmbiz_png/vO1zY1O9p8LNd69icKKqh3QLUpkngY8oWnasIrvck47uZhEwpSq11ec32TovOmYjGZCHiaqvQibPDBwsjhtMsEPMRkfxtlHROR8uz7swJLfSJA/640?wx_fmt=png&from=appmsg "")  
  
Ivanti 发布了一系列安全公告，影响其三款旗舰企业产品：Endpoint Manager Mobile、Neurons for ITSM 和 Sentry，使企业面临从权限提升到完全远程代码执行等各种风险。  
  
2026 年 9 月 8 日发布的披露信息涵盖了 10 个不同的 CVE，其中几个被评为严重级别，突显了 Ivanti 移动设备管理和 IT 服务管理生态系统面临的广泛风险。  
## Ivanti EPMM漏洞  
  
第一个安全公告针对的是CVE-2026-18851，这是 Ivanti Endpoint Manager Mobile (EPMM) 中一个严重性较高的缺少授权漏洞，其 CVSS 评分为 8.8。  
  
该漏洞源于 CWE-862，允许远程已认证的攻击者将权限提升至完全管理员权限。受影响的版本包括 12.9.0.1 及更早版本、12.8.0.3 及更早版本，以及 12.10.0.0 之前的所有构建版本。Ivanti 已发布修复版本 12.10.0.0、12.9.0.2 和 12.8.0.4 以弥补此漏洞。  
  
最严重的发现与 Ivanti Neurons 的 ITSM 有关，其中披露了 8 个独立的 CVE，其中 3 个风险等级为最高 9.9 级（严重）。  
  
CVE-2026-12744 和 CVE-2026-12745 这两个漏洞涉及对不受信任的数据进行反序列化 (CWE-502)，未经身份验证的攻击者可以触发这些漏洞在服务器上执行任意代码，每个漏洞的得分均为 9.8。  
  
新增的反序列化漏洞 CVE-2026-12651、CVE-2026-12650 和 CVE-2026-12648 需要身份验证，但仍允许远程代码执行。另外三个授权缺失问题 CVE-2026-12645、CVE-2026-12646 和 CVE-2026-12647 也允许已通过身份验证的攻击者执行代码，每个漏洞的严重性等级均为 9.9。  
  
值得注意的是，Ivanti 披露，这些 ITSM 缺陷是该公司通过使用集成到其产品安全和工程工作流程中的高级大型语言模型发现的，这标志着 AI 辅助漏洞发现获得正式咨询的案例实属罕见。  
  
2026 年 8 月 9 日，Neurons for ITSM 的云和 SaaS 版本在所有环境中进行了修补，无需客户采取任何行动。  
  
运行 2025.2 至 2026.1 版本的本地客户必须应用 2026 年 9 月的安全补丁，而本地部署的 2026.2 版本计划于 9 月 21 日发布。  
  
最后披露的是， CVE-2026-83527 影响通过 EPMM 和 Neurons for MDM 管理的Ivanti Sentry部署。  
  
该身份验证绕过漏洞的评分为 8.1，分类为 CWE-288，允许远程未经身份验证的攻击者获得管理员级别的访问权限。修复此漏洞的版本 R10.8.2、R10.7.3 和 R10.6.4 现已发布。该漏洞由 Aquila Sec Lab 的研究员 btaol 负责任地披露。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/vO1zY1O9p8LkSSOrwiajw2QDVwyPSLbjQkFysaqBmk17qoPXWI1SXyVb946PHxRPRlzZ1Q3ckibrlgvBWECZfwlDGiatQ81PBDRE7dFBkibCRHI/640?wx_fmt=png&from=appmsg "")  
  
Ivanti 表示，在披露这些漏洞之前，没有证据表明这些漏洞已被恶意利用。鉴于 Ivanti 的边缘和移动管理基础设施曾多次成为攻击目标，安全团队应优先进行漏洞修补，特别是针对暴露在互联网上的 ITSM 实例的 Neuron 组件，务必立即进行修补。  
  
  
信息来源：CyberSecurityNews  
  

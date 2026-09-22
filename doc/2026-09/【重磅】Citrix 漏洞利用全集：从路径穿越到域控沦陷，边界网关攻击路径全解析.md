#  【重磅】Citrix 漏洞利用全集：从路径穿越到域控沦陷，边界网关攻击路径全解析  
原创 CabinQ
                        CabinQ  逆熵寻生   2026-09-22 01:10  
  
Citrix 漏洞利用全集：从路径穿越到域控沦陷，边界网关攻击路径全解析  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/Uhia8L5ia7ha4gXUib73KQmJQtWC5iazEmYFYZhmI3aL8sX82HrZPNyuRneLeBicVQDDicQWYOfuD5gQgqibI6BfWPrKiaI4KtYGAZIICuSn9aicJSwc/640?from=appmsg "")  
  
  
【深度长文】全文约 10000 字 | 覆盖 10 个 CVE | 含 2019-2026 全时间线 | 含完整工具资源包  
  
核心结论  
  
Citrix NetScaler ADC（应用交付控制器）与 NetScaler Gateway（安全网关/VPN）是部署在成千上万家企业和政府机构网络边界的核心设备，承载着远程办公接入、负载均衡和应用发布功能。正因为它们必须暴露在公网、处理未认证流量、并在内存中驻留大量已认证用户会话，这一层"边界网关"成为了 2019 年以来最高频、最具破坏力的攻击目标之一：CISA 联合多国机构发布的联合通告将 CVE-2019-19781 列为 2020 年全年被利用最严重的漏洞；CVE-2023-3519 在 2023 年 6 月作为零日在野利用，攻击者在关键基础设施的 NetScaler 设备上植入 WebShell 并枚举域控；CVE-2023-4966（Citrix Bleed）更被 LockBit 3.0、Medusa、ALPHV 三大勒索组织同时武器化，用来绕过 MFA 劫持合法会话，最终击穿波音、工银（ICBC）、安理国际律师事务所（Allen & Overy）等全球知名机构 [1][2]。本文以红队视角系统梳理 Citrix 边界网关的完整攻击面，从资产发现、指纹识别到 10 个高危 CVE 的原理与利用，再到"边界缺口 → WebShell → 域控"的完整实战攻击链，读完即可独立完成对 Citrix NetScaler 环境的渗透测试。所有 CVE 编号、原理与利用链均经 NVD、CISA KEV、Citrix 官方公告多源交叉验证。  
  
CVE 速览  
  
🔹 **CVE-2019-19781**  
：路径遍历 + 模板注入 RCE，CVSS 9.8，2020 年全球被利用最严重的漏洞（别名 Shitrix）  
  
🔹 **CVE-2020-8193**  
：管理接口认证绕过 + 任意文件读取，CVSS 6.5，可与文件写入串联至 RCE  
  
🔹 **CVE-2020-8196**  
：管理接口信息泄露 / 本地文件包含，CVSS 4.3  
  
🔹 **CVE-2022-27518**  
：SAML SP/IdP 认证绕过，可未经认证以管理员身份执行代码，CVSS 9.8  
  
🔹 **CVE-2023-3519**  
：未认证远程代码执行，CVSS 9.8，2023 年 6 月零日在野利用植入 WebShell  
  
🔹 **CVE-2023-4966**  
：缓冲区越界读（Citrix Bleed），泄露会话令牌劫持 MFA，CVSS 9.4  
  
🔹 **CVE-2023-4967**  
：缓冲区溢出拒绝服务，CVSS 8.2  
  
🔹 **CVE-2023-6548**  
：管理接口代码注入，低级权限远程代码执行，CVSS 8.8  
  
🔹 **CVE-2023-6549**  
：缓冲区溢出拒绝服务，CVSS 8.2  
  
🔹 **CVE-2026-3055**  
：SAML IdP 越界读导致内存过读，2026 年最新被列入 CISA KEV  
  
收益清单  
  
1. 掌握 Citrix NetScaler ADC/Gateway 资产指纹识别全手法：Web 特征、favicon 哈希、TLS 证书、登录页面特征、版本探测  
  
2. 掌握路径遍历 RCE 鼻祖 CVE-2019-19781 的完整原理：NSC_USER 头 → 目录穿越 → 模板注入的层层递进  
  
3. 掌握管理接口认证绕过（CVE-2020-8193）的完整请求链：伪造 session → 绕过登录 → 任意文件读取 → 串联文件写入 RCE  
  
4. 掌握 2023 年两大"满分杀手"级漏洞：CVE-2023-3519 未认证 RCE 与 CVE-2023-4966 Citrix Bleed 会话劫持的原理、判定与利用  
  
5. 掌握 Citrix Bleed 绕过 MFA 劫持合法会话的完整手法，理解 LockBit 如何用它击穿全球巨头  
  
6. 掌握"边界缺口 → WebShell 植入 → AD 凭据解密 → ldapsearch 枚举 → 域控横向"的完整实战攻击链  
  
7. 掌握信息收集阶段就应重点关注的默认凭据、暴露端口与配置缺陷清单  
  
8. 获得完整 Citrix 渗透测试工具包，包含官方扫描器、Nuclei 模板、Metasploit 模块与多个开源利用工具源码  
  
**适用读者**  
：渗透测试工程师、红队攻击队员、企业安全研究员、SRC 漏洞挖掘者、CTF 选手  
  
**免责声明**  
：本文所有技术内容仅面向授权安全测试与教育研究，任何未经授权的渗透测试行为均违反中华人民共和国网络安全法，滥用造成的后果由使用者自行承担。  
  
**下载说明**  
：文末资源包包含完整工具集，覆盖文中全部 CVE 的官方检测工具、Nuclei 检测模板、Metasploit 利用模块与开源 PoC 源码。目录如下：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Uhia8L5ia7ha4FRJX6Rj1yDsytc04qL3miax6J17wwjFyEOra2vXYYcYicz9wcqGELsc5LiaxZib21EZ1diaDexdzfgZicmaOiadO7sEGiaVDaAecAJHo/640?wx_fmt=png&from=appmsg "")  
  

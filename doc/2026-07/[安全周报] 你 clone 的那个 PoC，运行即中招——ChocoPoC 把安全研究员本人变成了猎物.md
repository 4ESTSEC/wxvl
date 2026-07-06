#  [安全周报] 你 clone 的那个 PoC，运行即中招——ChocoPoC 把安全研究员本人变成了猎物  
极客零零七
                    极客零零七  极客零零七   2026-07-05 23:13  
  
# 极客零零七 · 第26期 · 2026-07-05  
> **摘要**  
：本周 ChocoPoC 把 **7 个**  
伪装成漏洞 PoC 的 GitHub 仓库投毒、专猎下载 exploit 的研究员，SharePoint RCE 被微软判为"exploitation less likely"后遭 Storm-2603 在野利用（CISA 火速入 KEV），Kemp LoadMaster **9.6**  
 命令注入当天出 writeup、当天被打——**攻击者本周盯上了安全社区自己的信任链**  
。  
  
### 一、本周重大事件  
- **SharePoint RCE CVE-2026-45659：微软说"less likely"，Storm-2603 说"已经在打了"**  
（RCE / 在野利用 / 勒索）  
  
CISA 于 **7 月 2 日**  
将 CVE-2026-45659（CVSS **8.8**  
）加入 KEV：这是 SharePoint Server 反序列化不可信数据导致的远程代码执行，**任何拥有 Site Member 权限的认证用户**  
即可触发、无需管理员。微软 5 月已发补丁、且当时评估"利用可能性较低"，但早期攻击已被归因到部署 Warlock 勒索的 Storm-2603；FCEB 处置截止 **7 月 4 日**  
。 厂商的"利用可能性"评级又一次被在野现实打脸——评级只是概率，攻击者只需要一次。  
  
- **Kemp LoadMaster CVE-2026-8037：9.6 命令注入，writeup 当天就是利用当天**  
（边缘设备 / RCE）  
  
Progress Kemp LoadMaster 负载均衡器爆出 CVSS **9.6**  
 的 OS 命令注入漏洞，可导致任意代码执行。**6 月 29 日**  
，watchTowr Labs 公开完整利用链技术细节，而在野利用几乎同日启动。 又一个"管流量的盒子"沦为突破口——继 Cisco SD-WAN、Check Point 之后，边缘设备的高危 RCE 已是每周固定节目，且从披露到被打的窗口正在压到"同一天"。  
  
- **勒索潮持续高压：Ford 被挂牌，Qilin 再下一城**  
（勒索 / 数据泄露）  
  
本周 Ford 被 Krybit 勒索团伙列上泄露站（数据规模待核实），Calgary 制造企业 Chemco 遭 Qilin 攻击。回看整个 6 月，公开披露的勒索攻击达 **102**  
 起、横跨 21 国，医疗（30 起）居首、服务与教育紧随。 勒索团伙不追新漏洞，专挑 KEV 里"已确认在野"的条目批量变现——你没打的每一个 KEV，都是它们的现成入口。  
  
### 二、攻防技术与手法  
- **ChocoPoC RAT：把 PoC 仓库变成陷阱，反向猎杀安全研究员**  
（供应链 / 社工 / RAT）  
  
YesWeHack 与 Sekoia 于 **7 月 1 日**  
披露 ChocoPoC——一批伪装成 CVE 利用代码的 Python PoC 仓库，运行即窃取浏览器密码、Cookie 与文件，并给攻击者反弹 shell。恶意逻辑藏在 PoC 拉取的**某个 Python 依赖包**  
里，能骗过快速代码审查；Sekoia 已发现至少 **7 个**  
投毒仓库，分别伪装 FortiWeb、PAN-OS、Ivanti Sentry、Check Point VPN、MongoBleed、Joomla 等热门漏洞的 PoC，其中恶意包 skytext  
 被下载 **2,400 次**  
、且在热点漏洞披露后下载激增。 这是攻防的镜像时刻——攻击者不再只用 PoC，而是污染 PoC 本身，把"下载即信任"的研究工作流变成初始访问向量。  
  
- **libssh2 RCE CVE-2026-55200 PoC 公开：一个底层库，牵动无数固件与工具**  
（PoC 武器化 / 供应链）  
  
针对 libssh2 的关键远程代码执行漏洞 CVE-2026-55200 的公开 PoC 已放出。libssh2 被大量运维工具、网络设备固件与应用静态链接，影响面远超"一个库"本身。 底层依赖的可怕在于隐蔽——它藏在你甚至不知道自己在用的地方，扫描器往往也看不见。  
  
- **Exchange SSRF→提权 CVE-2026-45504 PoC 公开：补丁之后，N-day 武器化即刻跟进**  
（PoC 武器化）  
  
Microsoft Exchange Server 一个高危 SSRF 漏洞（CVE-2026-45504，可用于提权）的公开 PoC 本周释放。 Exchange 历来是 N-day 武器化的重灾区，补丁发布不等于风险结束，反而常是攻击者逆向补丁、抢在你打补丁前动手的发令枪。  
  
### 安全趋势  
  
**武器化的速度，正在反超防御的速度——连 PoC 本身都被投毒了**  
  
**第一，从"披露"到"利用"的窗口趋近于零。**  
 LoadMaster 的 writeup 当天就是在野利用当天，SharePoint 被微软标"less likely"却已遭 Storm-2603 打穿。防御方赖以排优先级的"利用可能性评级"，正在被同日武器化的现实反复击穿——评级是概率，攻击是确定性。  
  
**第二，攻击者开始污染安全社区自己的信任基建。**  
 ChocoPoC 把七个伪装 PoC 的仓库投毒，专挑第一时间下载 exploit 的研究员下手。当"找漏洞的人"成了被猎杀的目标，安全从业者习以为常的"clone 下来跑一下"，第一次成了自身的高危动作。  
  
**第三，N-day 和底层库正取代 0-day 成为主力。**  
 勒索团伙照着 KEV 逐条收割，libssh2 这类底层依赖的 PoC 一放出就牵动无数固件——攻击者根本不需要昂贵的 0-day，公开的 N-day 加上你没打的补丁，就足够了。  
  
未来 6-12 个月，"下载即信任"的研究与运维工作流会成为一个被系统性利用的新初始访问向量。防御方需要做三件事：(1) 把陌生 PoC、脚本一律放进一次性沙箱/隔离 VM 运行，绝不在主机直接 pip install  
 或执行；(2) 把 KEV 当成攻击者的 to-do list，凡"已确认在野"一律按最高优先级处置，无视厂商的"less likely"；(3) 建立第三方库/依赖的 SBOM 清单，让 libssh2 式的底层 PoC 一出就能定位到自己受影响的资产。****  
### 本周行动清单  
1. **SharePoint 立即打 CVE-2026-45659**  
：微软 5 月补丁已就绪，即便官方标注"利用可能性较低"也别拖，并排查 Storm-2603/Warlock 勒索的落地痕迹。  
  
1. **Kemp LoadMaster 打 CVE-2026-8037（9.6）**  
：edge 负载均衡设备优先升级、收敛管理面暴露，watchTowr 利用链已公开、在野同步进行。  
  
1. **隔离你的 PoC 工作流**  
：来路不明的 exploit 仓库一律在一次性沙箱/VM 里运行，先审依赖包与仓库可信度——ChocoPoC 就藏在 PoC 拉取的 Python 依赖里。  
  
1. **排查 libssh2 依赖（CVE-2026-55200）**  
：升级到修复版，重点排静态链接了 libssh2 的工具、设备固件与内部应用，扫描器未必能直接发现。  
  
1. **把 KEV 当攻击者清单逐条闭环**  
：SharePoint、Exchange、LoadMaster 全部按"已在野"处置，勒索团伙正照着 KEV 批量变现，未打项就是现成入口。  
  
> 关注「极客零零七」，每周实战攻防干货。  
  
回复「提权」获取 Windows + Linux 提权速查表 · 回复「AD 攻击」获取 AD 域攻击手册  
  
  
###   
  
  
### 参考资料  
- https://thehackernews.com/2026/07/sharepoint-rce-cve-2026-45659-added-to.html  
  
- https://www.theregister.com/security/2026/07/02/microsoft-said-exploitation-was-less-likely-but-cisa-just-added-sharepoint-rce-to-kev-list/  
  
- https://www.securityweek.com/cisa-warns-of-actively-exploited-microsoft-sharepoint-vulnerability/  
  
- https://www.esecurityplanet.com/weekly-roundup/zero-days-ai-exploits-and-supply-chain-risks-define-this-week-in-cybersecurity-in-june-2026/  
  
- https://thehackernews.com/2026/07/new-chocopoc-rat-targets-vulnerability.html  
  
- https://www.bleepingcomputer.com/news/security/new-chocopoc-malware-targets-researchers-via-trojanized-poc-exploits/  
  
- https://cybersecuritynews.com/poc-exploit-libssh2-rce-vulnerability/  
  
- https://cybersecuritynews.com/poc-exploit-released-exchange-vulnerability/  
  
- https://www.cm-alliance.com/cybersecurity-blog/june-2026-biggest-cyber-attacks-data-breaches-ransomware-attacks  
  
- https://www.cisa.gov/known-exploited-vulnerabilities-catalog  
  
  
  

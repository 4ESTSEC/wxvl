#  F5 修复了 BIG-IP APM 的关键零日漏洞，该漏洞已被远程代码执行攻击利用  
Rhinoer
                    Rhinoer  犀牛安全   2026-09-27 16:00  
  
![](https://mmbiz.qpic.cn/mmbiz_png/vO1zY1O9p8LVNZQbYqrjsobxBhQBIqgdsMZLZkQPxxFmzphhcia4aUR9GuR8q7l9PvWic9zhTMUVVib4bg3OplnoohpMqDpHKlONicfAJTTia8hg/640?wx_fmt=png&from=appmsg "")  
  
F5 表示，攻击者正在利用 F5 BIG-IP 访问策略管理器 (APM) 中的一个严重漏洞，该漏洞允许他们在不登录的情况下在 BIG-IP 系统上运行代码。  
  
该漏洞（CVE-2026-94127）仅影响 APM 作为 OAuth 授权服务器向应用程序颁发访问令牌的系统。F5于 9 月 22 日发布安全公告披露了该漏洞，并已发布工程热修复程序。  
  
APM 是 BIG-IP 的一个模块，用于控制用户如何访问组织的应用程序和网络。存在漏洞的设置中，APM 访问策略和 OAuth 授权服务器配置文件位于同一台虚拟服务器上，该虚拟服务器托管着接收 OAuth 流量的 BIG-IP 地址。发送到该虚拟服务器的特定恶意流量可能导致远程代码执行。  
  
该漏洞为基于堆的缓冲区溢出。F5 在 CVSS v3.1 中给出的评分为 9.8 分（满分 10 分），在 CVSS v4.0 中给出的评分为 9.3 分。  
  
由于恶意流量直接访问虚拟服务器本身，因此限制对 BIG-IP 管理界面的访问并不能防止此漏洞。处于设备模式的 BIG-IP 系统也存在此漏洞。  
  
美国网络安全和基础设施安全局 (CISA) 于 9 月 22 日将该漏洞添加到其已知利用漏洞 (KEV) 目录中。根据CISA 在 6 月份发布的指令，CISA要求联邦民事机构在 9 月 25 日之前应用 F5 的缓解措施。  
  
F5 的 CVE 记录和 CISA 的 KEV 条目都没有说明有多少系统受到攻击、攻击者是谁，或者哪些组织成为攻击目标。  
  
哪些人会受到影响？  
  
对于 APM 充当 OAuth 授权服务器的系统，以下是受影响的版本以及每个版本的热修复程序：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/vO1zY1O9p8IW8cMAlnqszqXC2WFGBE5LAj1ZoujkseamT8CTFGSBpcBe5ibSzPBvTpz1xMqjPskUxLysibpgb8FRPbMic4PUzvyZmZLJmU7OkE/640?wx_fmt=png&from=appmsg "")  
  
仅将 APM 用作 OAuth 客户端或资源服务器，而没有 OAuth 授权服务器配置文件的系统不受影响。  
  
F5于 9 月 23 日 00:45 UTC更新了其 CVE 记录，指出该漏洞仅存在于授权服务器角色中。在此更新之前，CISA 的关键漏洞报告 (KEV) 条目以及欧盟机构网络安全服务机构 CERT-EU 发布的公告均已发布。这两份公告更广泛地描述了该漏洞，将其定义为虚拟服务器上的访问策略和 OAuth 配置文件。  
  
在F5针对 APM 17.1、17.5 和 21.0 的配置指南中，授权服务器的 OAuth 配置文件在“访问”>“联合身份验证”>“OAuth 授权服务器”>“OAuth 配置文件”下创建。然后，在附加到虚拟服务器的访问配置文件中选择该配置文件。以这种方式设置的虚拟服务器符合 F5 所描述的条件。  
  
F5 没有对已停止技术支持的版本进行评估，因此这些版本的状态未知，而非安全。  
  
另一个 APM 漏洞CVE-2025-53521 已于 3 月被添加到 CISA 的关键漏洞报告 (KEV) 目录中。针对 17.1 和 17.5 分支（版本 17.1.3 和 17.5.1.3）的修复程序位于上述受影响版本范围内。如果 APM 在系统中充当 OAuth 授权服务器，则即使系统已更新到上述任一版本，仍需要安装此新补丁。  
  
现在该怎么办  
  
F5 的修复方案是表格中列出的每个分支对应的工程热修复程序。如果热修复程序无法立即安装，F5 会为受影响的虚拟服务器提供 iRule 缓解措施。客户可以通过向 F5 支持部门提交工单来获取该措施。  
  
CERT-EU 建议首先保存取证证据，应用热修复程序，检查是否存在入侵迹象，如果发现任何入侵迹象，则启动事件响应。  
  
CISA 指示各机构首先应用 iRule，“以便进行主动取证分类”，然后“尽快安装最终的供应商补丁”。  
  
检查是否存在入侵  
  
以下迹象均为 F5 级故障，详见CERT-EU 安全公告。如果出现以下情况，则应立即对系统进行人工审查：OAuth 身份验证反复失败，随后出现可疑命令，不久后又出现 TMM SIGABRT 信号。APM 日志： /var/log/apm 中多次出现 UserInfo 请求失败，错误描述为“访问令牌无效”。请特别注意短时间内来自同一 IP 地址的 10 次或更多次请求。OAuth 计数器：运行 tmctl global_oauth_stat -s total_requests,total_userinfo_requests,total_failed 时 total_failed 出现无法解释的上升。审计日志：在故障发生前后，/var/log/audit 文件中存在可疑命令。TMM 核心转储文件：虽然本身并非故障迹象，但值得调查。F5 曾发现 TMM 进入循环，导致 SOD 守护进程发送 SIGABRT 信号。  
  
F5 的 CVE 记录以及 CISA 和 CERT-EU 的建议均未说明安装热修复程序是否会移除攻击者已获得的访问权限。  
  
  
信息来源：T  
hehacker  
News  
  

#  SharePoint RCE 被列入 KEV：协作平台，不该成为补丁盲区  
原创 tcode
                    tcode  字节脉搏实验室   2026-07-03 02:59  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/nOo5YmK1PHx6iagSs4A1vkXegQtxg2aMkIqPH39DXicvCEJqHDOTzsdIibubA8qCaiaJUaqQic7cp8Tcy5F0CtepW3rtoh3Pz5mMBOsG0q0pB4rE/640?wx_fmt=png&from=appmsg "")  
  
    过去二十四小时，企业安全团队需要把一个熟悉但容易被低估的系统重新拉回视野：本地部署的 SharePoint Server。  
  
    美国 CISA 在 2026 年 7 月 2 日前后将 CVE-2026-45659 加入 Known Exploited Vulnerabilities（KEV）目录。KEV 的含义很直接：这不是“理论上可能被利用”的漏洞，而是已经有现实利用证据。安全媒体 BleepingComputer 与 The Hacker News 随后也跟进报道，指出该问题影响 Microsoft SharePoint Server，微软此前已在 2026 年 5 月发布修复。  
  
    对很多企业来说，SharePoint 并不是互联网出口、防火墙、VPN 这类第一眼就会被列入高危清单的设备。但它恰恰承载了大量内部文档、审批材料、项目资料、部门站点、权限继承关系，以及与身份系统、办公套件和业务应用的集成。攻击者如果把协作平台当作入口，后续能看到的往往不是单点资产，而是一张组织协作关系图。  
  
核心事实  
  
    CVE-2026-45659 被 NVD 描述为 SharePoint 中与不可信数据反序列化相关的问题，在特定条件下可能导致远程代码执行。公开信息显示，攻击前提并非完全无门槛，相关描述提到“已授权攻击者”这一条件；但这并不意味着风险可以放松，因为企业环境中低权限账号泄露、弱口令、外包账号遗留、离职账号未清理等问题并不少见。  
  
    CISA 将其列入 KEV，说明该漏洞已经从“补丁公告里的编号”进入“正在被对手关注和利用的清单”。微软已发布修复，企业现在的关键不是讨论漏洞是否值得修，而是确认哪些 SharePoint Server 仍停留在修复前状态、哪些实例暴露在更高风险网络区域、哪些系统缺少足够日志和回滚准备。  
  
影响分析  
  
    这类事件的真正警示，是“协作系统资产化”仍然做得不够。  
  
    很多组织对边界设备、域控、数据库和核心业务系统有相对成熟的资产台账，但对协作平台的关注常常停在“能不能访问、空间够不够、权限有没有投诉”。一旦 SharePoint 作为文件与权限中心被攻击者利用，风险可能沿着文档、凭据、脚本、流程说明、内部链接和历史备份横向扩散。  
  
    从传播价值看，这一事件也值得普通企业管理层关注：补丁不是 IT 部门的例行劳动，而是业务连续性的底层保障。一个“看似只是办公协作”的平台，实际可能连接着研发资料、客户材料、财务流程和内部制度。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/nOo5YmK1PHxLqTn1W47vadLKL7m8mOaS04oe2M1m2zfKTsxvmhc71P3cBhSfxakmKlGB6xfSVNOoYrBROmH3oSnUFc1kDR2aYeiakf7BzG0k/640?wx_fmt=png&from=appmsg "")  
  
企业应对建议  
  
    第一，立即核对本地 SharePoint Server 版本与补丁状态。不要只问“自动更新开了没有”，而要拉出实例清单，确认生产、测试、灾备、历史迁移残留环境是否都覆盖。  
  
    第二，把 KEV 作为补丁优先级信号。已列入 KEV 的漏洞，优先级应高于普通 CVE。不能短期升级的系统，应评估临时隔离、访问控制收紧、外部入口下线或放入更严格监控范围。  
  
    第三，复查账号与权限。重点检查低权限但长期未使用的账号、第三方账号、共享账号、服务账号，以及是否存在不必要的站点管理员权限。  
  
    第四，回看日志而不是只装补丁。建议关注异常登录、异常文件访问、异常站点操作、服务异常重启、未知进程行为和安全产品告警。日志保留不足的环境，应尽快补齐。  
  
    第五，准备恢复方案。协作平台中的文件与站点结构本身就是业务资产，应确认备份可恢复、恢复权限可控、恢复后不会把旧风险重新带回生产环境。  
  
事实、推测与观点区分  
  
    事实：CISA 已将 CVE-2026-45659 纳入 KEV；NVD 与微软更新指南提供了漏洞描述与修复参考；多家安全媒体在 2026 年 7 月 2 日报道了该漏洞被活跃利用的情况。  
  
    推测：公开信息并未完整披露攻击者身份、利用规模和受害行业分布，因此不应把它直接归因给某个组织或某类行业攻击。  
  
    观点：企业应把本地协作平台纳入与 VPN、邮件网关、身份系统同等级别的补丁与监控优先级。不是因为每个 SharePoint 都一定暴露在公网，而是因为它一旦出事，影响往往贴近业务核心。  
  
结语  
  
    安全事件最怕的不是漏洞突然出现，而是漏洞已经被利用，资产负责人还以为它只是“办公系统”。SharePoint 这次进入 KEV，给企业的提醒很朴素：越是日常、越是熟悉、越是承载协作的系统，越不应该在补丁清单里排到最后。  
  
关键来源  
  
•	CISA Known Exploited Vulnerabilities Catalog，2026-07-02 前后更新：https://www.cisa.gov/known-exploited-vulnerabilities-catalog  
  
•	The Hacker News，2026-07-02，SharePoint RCE CVE-2026-45659 Added to CISA KEV After Active Exploitation：https://thehackernews.com/2026/07/sharepoint-rce-cve-2026-45659-added-to.html  
  
•	BleepingComputer，2026-07-02，CISA: Microsoft SharePoint RCE flaw now actively exploited：https://www.bleepingcomputer.com/news/security/cisa-microsoft-sharepoint-rce-flaw-now-actively-exploited/  
  
•	NVD，CVE-2026-45659：https://nvd.nist.gov/vuln/detail/CVE-2026-45659  
  
•	Microsoft Security Update Guide，CVE-2026-45659：https://msrc.microsoft.com/update-guide/vulnerability/CVE-2026-45659  
  

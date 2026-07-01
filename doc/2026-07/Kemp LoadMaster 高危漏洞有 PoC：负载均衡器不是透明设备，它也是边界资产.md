#  Kemp LoadMaster 高危漏洞有 PoC：负载均衡器不是透明设备，它也是边界资产  
原创 tcode
                    tcode  字节脉搏实验室   2026-07-01 02:54  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/nOo5YmK1PHw6RlPt0dmxSj3icWH1geianibtYhI2CNwa2oxkaS9vseoFXqtM2jf59XbUMgmLPIzcbBPRYslLNd7SV8aqPqdLicib6yZB4mfiaHjww/640?wx_fmt=png&from=appmsg "")  
  
事件概述  
  
    6 月 30 日前后，Progress Kemp LoadMaster 漏洞 CVE-2026-8037 成为边界设备安全热点。Progress 已发布安全公告和修复版本，Horizon3.ai 随后发布技术分析并公开 PoC，安全媒体和 eSentire 报告显示，公开 PoC 后互联网上已经出现利用尝试。  
  
    Load balancer、ADC、反向代理这类设备经常被认为是“流量中间层”，但从攻击面看，它们是非常高价值的边界资产。它们连接互联网入口、内部应用、TLS 证书、会话路由、健康检查和管理接口。一旦被攻破，攻击者可能获得观察、篡改或深入内部应用环境的机会。  
  
核心事实  
  
    事实一：Progress 官方公告披露 CVE-2026-8037，影响 Kemp LoadMaster，已发布修复版本。企业应以 Progress 安全公告中的版本范围和升级建议为准。  
  
    事实二：Horizon3.ai 发布了该漏洞的技术分析，并公开 PoC。公开 PoC 会显著降低攻击门槛，因此未修补的互联网暴露实例风险上升。  
  
    事实三：eSentire 报告称，在 PoC 发布后不久，已经观察到针对该漏洞的互联网利用尝试。The Hacker News 于 2026 年 6 月 30 日报道了该事件。  
  
    事实四：本文不提供 PoC 链接、请求细节、命令、攻击路径或可直接用于利用的信息。防守者应直接参考厂商公告进行修复。  
  
影响分析  
  
    负载均衡器和 ADC 位于业务流量入口，具有天然高价值。它们通常终止 TLS、转发请求、连接多个后端、保存证书和密钥材料，并掌握关键业务域名的路由关系。如果这类设备被攻击者控制，影响可能不只是设备本身，而是其后的一组应用系统。  
  
    对运维团队来说，这类设备还有一个典型问题：管理面长期开放。为了远程维护，管理接口可能被暴露到公网、VPN、合作伙伴网络或办公网。一旦漏洞可被远程利用，管理面暴露就会直接放大风险。  
  
    对安全运营来说，ADC 设备的日志和监控常常不如服务器完善。很多企业只把它当作网络设备做可用性监控，却没有把系统日志、管理登录、配置变更、命令执行和异常外联纳入 SIEM 或告警策略。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/nOo5YmK1PHw6ibiadowIoOuZ5dWicOJHU7c3WmR0cfGEW3Zv1gbcoFkMWpRlSKf5oDDLFWqQdC08SVJ90CDNMdBaGUIZuWLbIpdGkWZhYLknNk/640?wx_fmt=png&from=appmsg "")  
  
企业应对建议  
  
    第一，立即确认是否使用 Kemp LoadMaster，并核对所有实例版本。包括生产、灾备、测试、云镜像、备用节点和长期未维护设备。不要只查主机名清单，要和网络团队确认真实流量入口。  
  
    第二，按 Progress 官方公告升级到修复版本。对于高可用集群，补丁应覆盖所有节点，并在变更后确认流量、证书、健康检查和路由策略正常。  
  
    第三，关闭或严格限制管理面暴露。管理接口应只允许可信来源访问，最好放在专用管理网络、VPN 或零信任访问后，并启用强认证和审计。  
  
    第四，排查异常活动。重点检查 PoC 公开后时间窗口内的管理登录、配置变更、异常命令执行迹象、未知外联、系统文件变化、异常进程和后端流量异常。  
  
    第五，将 ADC 纳入边界设备应急清单。与 VPN、防火墙、邮件网关、RMM、堡垒机一样，负载均衡器也应有版本基线、补丁 SLA、配置备份、日志留存和应急隔离方案。  
  
事实、推测与观点  
  
    可以确认的事实是：Progress 已发布 CVE-2026-8037 安全公告和修复；Horizon3.ai 已公开技术分析和 PoC；eSentire 与安全媒体报告了公开 PoC 后的利用尝试。  
  
    合理推测是：攻击者会优先扫描互联网暴露的 LoadMaster 管理面和未修补实例。公开信息不足以判断成功入侵规模，但公开 PoC 已经显著提高风险。  
  
    我的观点是：边界设备不是“看不见的管道”。负载均衡器、反向代理、ADC 都是高权限业务入口，应按关键控制面资产治理。  
  
结语  
  
    Kemp LoadMaster 事件给企业的提醒很直接：边界设备漏洞的时间窗口很短。PoC 公开后，攻击者会自动化扫描，防守方不能再用普通变更节奏处理。现在该做的是打补丁、关管理面、查日志，把负载均衡器从“网络设备清单”提升到“关键边界资产清单”。  
  
关键来源  
  
•	Progress：《Kemp LoadMaster Security Advisory: CVE-2026-8037》，2026-06，https://community.progress.com/s/article/Kemp-LoadMaster-Security-Advisory-CVE-2026-8037  
  
•	Horizon3.ai：《CVE-2026-8037: Progress Kemp LoadMaster Remote Code Execution》，2026-06，https://horizon3.ai/attack-research/disclosures/cve-2026-8037-progress-kemp-loadmaster-rce/  
  
•	eSentire：《Progress Kemp LoadMaster CVE-2026-8037 Exploitation Attempts Observed》，2026-06-30，https://www.esentire.com/blog/progress-kemp-loadmaster-cve-2026-8037-exploitation-attempts-observed  
  
•	The Hacker News：《Progress Kemp LoadMaster Flaw Could Let Attackers Run Root Commands Pre-Auth》，2026-06-30，https://thehackernews.com/2026/06/progress-kemp-loadmaster-flaw-could-let.html  
  

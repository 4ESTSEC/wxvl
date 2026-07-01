#  Langflow RCE 被挖矿活动利用：AI 应用上云，别把调试入口也一起暴露了  
原创 tcode
                    tcode  字节脉搏实验室   2026-07-01 02:54  
  
![](https://mmbiz.qpic.cn/mmbiz_png/nOo5YmK1PHwBmbxcj6wOYRG8lSbj9OBeSeibsBaH7v1RZvdBb21cS44ZMSGBRljDcmC17IaUp1piciaqW4R6dbmmDmTCaKiaxVYncrCjCGmxuVs/640?wx_fmt=png&from=appmsg "")  
  
事件概述  
  
    6 月 30 日前后，Trend Micro 发布分析，指出 Langflow 中 CVE-2026-33017 正被攻击者利用，目标是暴露在公网的 Langflow 实例，并用于部署加密货币挖矿相关组件。The Hacker News 随后跟进报道，强调该漏洞已被用于攻击暴露 AI 应用环境。  
  
    Langflow 是一个用于构建、编排和可视化 AI 工作流的开源工具。它的价值在于让团队更快搭建 AI 代理、链路和数据流；但从安全角度看，任何能够连接模型、工具、数据源、脚本和外部服务的平台，都不应被当成“临时实验界面”随意暴露。  
  
    这次事件的重点不是挖矿本身有多新，而是攻击者开始系统性扫描和利用 AI 工具链中的暴露入口。今天是挖矿，明天可能是读取 API Key、滥用云资源、访问内部数据源或污染自动化流程。  
  
核心事实  
  
    事实一：GitHub Security Advisory 已公开 Langflow CVE-2026-33017 相关公告，说明该问题影响特定版本，并已在修复版本中处理。具体版本和修复方式应以项目公告为准。  
  
    事实二：Trend Micro 于 2026 年 6 月 30 日披露，攻击者正在利用该漏洞攻击暴露实例，并投递加密货币挖矿组件。  
  
    事实三：The Hacker News 同日跟进报道，称该漏洞已被利用部署 Monero 挖矿相关载荷，并强调暴露 AI 应用的攻击面风险。  
  
影响分析  
  
    AI 应用平台有一个常见风险：上线速度快，安全边界慢。很多团队会把 AI 编排、Prompt 调试、插件测试、模型代理和工作流平台部署到云主机上，先让业务和研发“跑起来”。如果认证、访问控制、网络隔离和补丁管理没有同步跟上，这些平台就会变成新的公网入口。  
  
    挖矿攻击看似只是资源消耗，但它通常说明攻击者已经可以在环境中执行非预期操作。后续风险可能包括云账单异常、实例性能下降、凭据被读取、内部服务被探测、API Key 泄露、模型调用额度被滥用，以及工作流被篡改。  
  
    对企业安全团队来说，AI 工具链的挑战在于资产归属不清。它可能由数据团队、业务创新团队或研发个人部署，不一定进入传统 CMDB 和漏洞扫描范围。攻击者不关心它是不是“实验环境”，只关心它能不能执行代码、访问数据或消耗资源。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/nOo5YmK1PHxw7U4RQVGnuUdwlx7bicnV4qrVJrgxOUaGgvrtBDMdgboz1z9d9qia6YaPJfzscicg5oBcxG3vfPP7wkEXtORwtEGn639g5Nic9bI/640?wx_fmt=png&from=appmsg "")  
  
企业应对建议  
  
    第一，立刻排查是否有公网暴露的 Langflow 实例。不要只查正式域名，也要查云资产、测试环境、临时端口、个人项目、容器平台和开发机转发。  
  
    第二，升级到官方修复版本，并核对部署镜像是否已经重建。容器环境中只更新仓库依赖不一定等于生产容器已经使用新版本。  
  
    第三，收缩访问面。Langflow 这类编排平台应放在 VPN、内网、受控访问代理或零信任访问之后，默认不应直接面向公网开放。管理入口要启用强认证和最小权限。  
  
    第四，检查云资源和主机异常。重点查看 CPU 异常、陌生进程、异常容器、计划任务、非预期外联、云账单突增和密钥访问日志。挖矿只是表象，凭据是否被读取同样需要确认。  
  
    第五，把 AI 工具链纳入安全基线。包括资产登记、镜像扫描、密钥管理、日志接入、访问控制、补丁 SLA 和上线评审。AI 工具不是“创新豁免区”。  
  
事实、推测与观点  
  
    可以确认的事实是：Langflow CVE-2026-33017 已有公开安全公告；Trend Micro 披露该漏洞被用于攻击暴露实例并部署挖矿组件；The Hacker News 于 6 月 30 日跟进报道。  
  
    合理推测是：攻击者会继续扫描 AI 编排和低代码平台，因为这些系统往往连接工具、凭据和计算资源。当前公开信息不支持扩大结论到所有 AI 平台，但趋势值得重视。  
  
    我的观点是：AI 应用安全最容易输在“这是个临时环境”。只要它接入了网络、模型、密钥或数据，它就已经是生产风险的一部分。  
  
结语  
  
    Langflow 这次事件不是简单的“开源项目又出漏洞”。它说明 AI 工具链已经进入攻击者扫描清单。企业做 AI 应用，不只要关注模型效果，也要关注入口、凭据、日志和补丁。能被公网访问的实验平台，从攻击者角度看就是生产系统。  
  
关键来源  
  
•	GitHub Security Advisory：Langflow CVE-2026-33017，2026-06，https://github.com/langflow-ai/langflow/security/advisories/GHSA-vwmf-pq79-vjvx  
  
•	Trend Micro：《Threat Actors Exploit Langflow RCE to Deploy Monero Miner》，2026-06-30，https://www.trendmicro.com/en_us/research/26/f/langflow-cve-2026-33017-rce-exploited.html  
  
•	The Hacker News：《Critical Langflow RCE Flaw Exploited to Deploy Monero Miner on AI Servers》，2026-06-30，https://thehackernews.com/2026/06/critical-langflow-rce-flaw-exploited.html  
  

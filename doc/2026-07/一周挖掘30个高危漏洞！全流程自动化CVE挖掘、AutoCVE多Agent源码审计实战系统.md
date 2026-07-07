#  一周挖掘30个高危漏洞！全流程自动化CVE挖掘、AutoCVE多Agent源码审计实战系统  
larlarua
                    larlarua  渗透安全HackTwo   2026-07-06 16:06  
  
0x01 工具介绍  
  
**AutoCVE是一款基于多Agent协同的全流程自动化CVE挖掘与源码审计系统，依托智能调度与ReAct循环机制，融合多模式审计能力，可自动完成项目筛查、漏洞挖掘、验证研判与报告生成。实测表现亮眼，可在一周内挖掘30个合规高危漏洞，覆盖多款主流开源项目，大幅降低人工挖洞成本，高效助力安全研究与漏洞申报工作。**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ibrevicNauKAXb12Axxd0YKjr13YlsUeBMiaBAE4T7E4wYlAsNRpShFNyYlic3YwFoIQj8mSIAhw1URpyGYyXdAQichMo8CbavbR2WbySlYjr640/640?wx_fmt=png&from=appmsg "")  
  
  
  
  
  
  
  
  
  
  
  
  
注意：  
现在只对常读和星标的公众号才展示大图推送，建议大家把  
**渗透安全HackTwo**  
"**设为****星标⭐️**  
"  
**否****则可能就看不到了啦！**  
  
**下载地址在末尾 #渗透安全HackTwo**  
  
0x02   
功能介绍  
  
✨核心能力  
  
### 🚀 一键完成 CVE 挖掘  
  
实现从项目筛选、仓库导入、审计任务创建、Agent 漏洞挖掘到 CVE 申报报告生成的全流程自动化。用户仅需复制报告内容并提交，即可完成后续 CVE 申请。  
### 🤖 Multi-Agent 协同审计  
  
通过 Orchestrator 统一调度 Recon、Scan、Triage、Finding 和 Verification 等 Agent，协同完成信息收集、工具扫描、误报过滤、漏洞深挖与动态验证。  
### 🧩 三种审计模式  
  
根据不同审计目标灵活选择增强扫描、智能审计或综合审计，兼顾扫描效率、挖掘深度与审计覆盖范围。  
  
<table><thead><tr><th data-colwidth="130"><span cid="n190" mdtype="table_cell"><span md-inline="plain"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-size: 17px;font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;"><span textstyle="" style="font-size: 16px;">审计模式</span></span></span></span></th><th data-colwidth="155"><span cid="n191" mdtype="table_cell"><span md-inline="plain"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-size: 17px;font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;"><span textstyle="" style="font-size: 16px;">核心 Agent</span></span></span></span></th><th><span cid="n192" mdtype="table_cell"><span md-inline="plain"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-size: 17px;font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;"><span textstyle="" style="font-size: 16px;">适用场景</span></span></span></span></th></tr></thead><tbody><tr><td data-colwidth="130"><span cid="n194" mdtype="table_cell"><span md-inline="plain"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-size: 17px;font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;"><span textstyle="" style="font-size: 16px;">⚡ </span></span></span><span md-inline="strong"><strong><span md-inline="plain"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-size: 17px;font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;"><span textstyle="" style="font-size: 16px;">增强扫描</span></span></span></strong></span></span></td><td data-colwidth="155"><span cid="n195" mdtype="table_cell"><span md-inline="plain"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-size: 17px;font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;"><span textstyle="" style="font-size: 16px;">Scan → Triage</span></span></span></span></td><td><span cid="n196" mdtype="table_cell"><span md-inline="plain"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-size: 17px;font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;"><span textstyle="" style="font-size: 16px;">快速分析工具扫描结果并过滤误报</span></span></span></span></td></tr><tr><td data-colwidth="130"><span cid="n198" mdtype="table_cell"><span md-inline="plain"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-size: 17px;font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;"><span textstyle="" style="font-size: 16px;">🧠 </span></span></span><span md-inline="strong"><strong><span md-inline="plain"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-size: 17px;font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;"><span textstyle="" style="font-size: 16px;">智能审计</span></span></span></strong></span></span></td><td data-colwidth="155"><span cid="n199" mdtype="table_cell"><span md-inline="plain"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-size: 17px;font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;"><span textstyle="" style="font-size: 16px;">Finding</span></span></span></span></td><td><span cid="n200" mdtype="table_cell"><span md-inline="plain"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-size: 17px;font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;"><span textstyle="" style="font-size: 16px;">深度挖掘高价值漏洞，适用于 CVE 和 0Day 研究</span></span></span></span></td></tr><tr><td data-colwidth="130"><span cid="n202" mdtype="table_cell"><span md-inline="plain"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-size: 17px;font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;"><span textstyle="" style="font-size: 16px;">🔍 </span></span></span><span md-inline="strong"><strong><span md-inline="plain"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-size: 17px;font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;"><span textstyle="" style="font-size: 16px;">综合审计</span></span></span></strong></span></span></td><td data-colwidth="155"><span cid="n203" mdtype="table_cell"><span md-inline="plain"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-size: 17px;font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;"><span textstyle="" style="font-size: 16px;">Scan → Triage + Finding</span></span></span></span></td><td><span cid="n204" mdtype="table_cell"><span md-inline="plain"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-size: 17px;font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;"><span textstyle="" style="font-size: 16px;">融合工具扫描与源码分析，开展全量审计</span></span></span></span></td></tr></tbody></table>### 🎯 面向 CVE 挖掘的专用 Agent  
  
Finding Agent 是 AutoCVE 的核心审计能力，专为 CVE 挖掘场景设计。它可直接分析项目源码，并结合 ReAct Loop、专项工具调用、Nudge 纠偏及   
FinalizeFinding  
 结构化终止机制，最终产出符合 CVE 申报条件的高价值漏洞。  
💬 交互式审计与全过程追踪- **支持用户交互**  
将完整审计过程作为会话上下文，用户可围绕审计结果继续追问，让 Agent 补充证据、解释攻击链、完善复现步骤或扩展漏洞分析。  
  
- **可视化审计追踪**  
集中展示活动日志、Agent Tree、工具调用、阶段进度、初步报告和审计会话，方便复盘每次审计的执行路径与关键过程。  
  
🗂️ 智能漏洞管理与 Skills 扩展- **智能化漏洞管理**  
审计发现的漏洞由 Agent 调用工具自动提交，经过去重后以结构化形式入库，并在漏洞管理模块中统一维护。  
  
- **专属 Skills 配置**  
支持根据实际需求为不同 Agent 配置专属 Skills，灵活扩展各 Agent 的能力边界。  
  
成果明细（30）  
<table><thead><tr style="box-sizing: border-box;"><th data-colwidth="148" align="center" style="box-sizing: border-box;"><section><span leaf="">CVE 编号</span></section></th><th data-colwidth="155" align="center" style="box-sizing: border-box;"><section><span leaf="">项目</span></section></th><th data-colwidth="83" align="center" style="box-sizing: border-box;"><section><span leaf="">漏洞类型</span></section></th><th align="center" style="box-sizing: border-box;"><section><span leaf="">CVSS</span></section></th><th align="center" style="box-sizing: border-box;"><section><span leaf="">漏洞内容</span></section></th></tr></thead><tbody><tr style="box-sizing: border-box;"><td data-colwidth="148" align="center" style="box-sizing: border-box;"><section><span leaf="">CVE-2026-40904</span></section></td><td data-colwidth="155" align="center" style="box-sizing: border-box;"><section><span leaf="">Chartbrew</span></section></td><td data-colwidth="83" align="center" style="box-sizing: border-box;"><section><span leaf="">Improper Access Control</span></section></td><td align="center" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">8.1</span></strong></td><td align="center" style="box-sizing: border-box;"><section><span leaf="">查看详情</span></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="148" align="center" style="box-sizing: border-box;"><section><span leaf="">CVE-2026-40603</span></section></td><td data-colwidth="155" align="center" style="box-sizing: border-box;"><section><span leaf="">Chartbrew</span></section></td><td data-colwidth="83" align="center" style="box-sizing: border-box;"><section><span leaf="">Improper Access Control</span></section></td><td align="center" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">6.5</span></strong></td><td align="center" style="box-sizing: border-box;"><section><span leaf="">查看详情</span></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="148" align="center" style="box-sizing: border-box;"><section><span leaf="">CVE-2026-40601</span></section></td><td data-colwidth="155" align="center" style="box-sizing: border-box;"><section><span leaf="">Chartbrew</span></section></td><td data-colwidth="83" align="center" style="box-sizing: border-box;"><section><span leaf="">Missing Authorization</span></section></td><td align="center" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">7.5</span></strong></td><td align="center" style="box-sizing: border-box;"><section><span leaf="">查看详情</span></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="148" align="center" style="box-sizing: border-box;"><section><span leaf="">CVE-2026-40600</span></section></td><td data-colwidth="155" align="center" style="box-sizing: border-box;"><section><span leaf="">Chartbrew</span></section></td><td data-colwidth="83" align="center" style="box-sizing: border-box;"><section><span leaf="">Improper Access Control</span></section></td><td align="center" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">8.1</span></strong></td><td align="center" style="box-sizing: border-box;"><section><span leaf="">查看详情</span></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="148" align="center" style="box-sizing: border-box;"><section><span leaf="">CVE-2026-40595</span></section></td><td data-colwidth="155" align="center" style="box-sizing: border-box;"><section><span leaf="">Chartbrew</span></section></td><td data-colwidth="83" align="center" style="box-sizing: border-box;"><section><span leaf="">Improper Access Control</span></section></td><td align="center" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">7.5</span></strong></td><td align="center" style="box-sizing: border-box;"><section><span leaf="">查看详情</span></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="148" align="center" style="box-sizing: border-box;"><section><span leaf="">CVE-2026-42181</span></section></td><td data-colwidth="155" align="center" style="box-sizing: border-box;"><section><span leaf="">Lemmy</span></section></td><td data-colwidth="83" align="center" style="box-sizing: border-box;"><section><span leaf="">SSRF</span></section></td><td align="center" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">6.5</span></strong></td><td align="center" style="box-sizing: border-box;"><section><span leaf="">查看详情</span></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="148" align="center" style="box-sizing: border-box;"><section><span leaf="">CVE-2026-42180</span></section></td><td data-colwidth="155" align="center" style="box-sizing: border-box;"><section><span leaf="">Lemmy</span></section></td><td data-colwidth="83" align="center" style="box-sizing: border-box;"><section><span leaf="">SSRF</span></section></td><td align="center" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">6.3</span></strong></td><td align="center" style="box-sizing: border-box;"><section><span leaf="">查看详情</span></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="148" align="center" style="box-sizing: border-box;"><section><span leaf="">CVE-2026-7290</span></section></td><td data-colwidth="155" align="center" style="box-sizing: border-box;"><section><span leaf="">JeecgBoot</span></section></td><td data-colwidth="83" align="center" style="box-sizing: border-box;"><section><span leaf="">SQL Injection</span></section></td><td align="center" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">6.3</span></strong></td><td align="center" style="box-sizing: border-box;"><section><span leaf="">查看详情</span></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="148" align="center" style="box-sizing: border-box;"><section><span leaf="">CVE-2026-7291</span></section></td><td data-colwidth="155" align="center" style="box-sizing: border-box;"><section><span leaf="">o2oa</span></section></td><td data-colwidth="83" align="center" style="box-sizing: border-box;"><section><span leaf="">SSRF</span></section></td><td align="center" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">6.3</span></strong></td><td align="center" style="box-sizing: border-box;"><section><span leaf="">查看详情</span></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="148" align="center" style="box-sizing: border-box;"><section><span leaf="">CVE-2026-7292</span></section></td><td data-colwidth="155" align="center" style="box-sizing: border-box;"><section><span leaf="">o2oa</span></section></td><td data-colwidth="83" align="center" style="box-sizing: border-box;"><section><span leaf="">RCE</span></section></td><td align="center" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">5.6</span></strong></td><td align="center" style="box-sizing: border-box;"><section><span leaf="">查看详情</span></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="148" align="center" style="box-sizing: border-box;"><section><span leaf="">CVE-2026-7303</span></section></td><td data-colwidth="155" align="center" style="box-sizing: border-box;"><section><span leaf="">xxl-job</span></section></td><td data-colwidth="83" align="center" style="box-sizing: border-box;"><section><span leaf="">Improper Access Control</span></section></td><td align="center" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">3.7</span></strong></td><td align="center" style="box-sizing: border-box;"><section><span leaf="">查看详情</span></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="148" align="center" style="box-sizing: border-box;"><section><span leaf="">CVE-2026-7305</span></section></td><td data-colwidth="155" align="center" style="box-sizing: border-box;"><section><span leaf="">xxl-job</span></section></td><td data-colwidth="83" align="center" style="box-sizing: border-box;"><section><span leaf="">SSRF</span></section></td><td align="center" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">6.3</span></strong></td><td align="center" style="box-sizing: border-box;"><section><span leaf="">查看详情</span></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="148" align="center" style="box-sizing: border-box;"><section><span leaf="">CVE-2026-7306</span></section></td><td data-colwidth="155" align="center" style="box-sizing: border-box;"><section><span leaf="">xxl-job</span></section></td><td data-colwidth="83" align="center" style="box-sizing: border-box;"><section><span leaf="">Hard-coded Key</span></section></td><td align="center" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">5.6</span></strong></td><td align="center" style="box-sizing: border-box;"><section><span leaf="">查看详情</span></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="148" align="center" style="box-sizing: border-box;"><section><span leaf="">CVE-2026-40610</span></section></td><td data-colwidth="155" align="center" style="box-sizing: border-box;"><section><span leaf="">BentoML</span></section></td><td data-colwidth="83" align="center" style="box-sizing: border-box;"><section><span leaf="">Link Following</span></section></td><td align="center" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">5.5</span></strong></td><td align="center" style="box-sizing: border-box;"><section><span leaf="">查看详情</span></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="148" align="center" style="box-sizing: border-box;"><section><span leaf="">CVE-2026-48763</span></section></td><td data-colwidth="155" align="center" style="box-sizing: border-box;"><section><span leaf="">typebot.io</span></section></td><td data-colwidth="83" align="center" style="box-sizing: border-box;"><section><span leaf="">Missing Authorization</span></section></td><td align="center" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">8.2</span></strong></td><td align="center" style="box-sizing: border-box;"><section><span leaf="">查看详情</span></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="148" align="center" style="box-sizing: border-box;"><section><span leaf="">CVE-2026-48764</span></section></td><td data-colwidth="155" align="center" style="box-sizing: border-box;"><section><span leaf="">typebot.io</span></section></td><td data-colwidth="83" align="center" style="box-sizing: border-box;"><section><span leaf="">SSRF</span></section></td><td align="center" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">8.2</span></strong></td><td align="center" style="box-sizing: border-box;"><section><span leaf="">查看详情</span></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="148" align="center" style="box-sizing: border-box;"><section><span leaf="">CVE-2026-48765</span></section></td><td data-colwidth="155" align="center" style="box-sizing: border-box;"><section><span leaf="">typebot.io</span></section></td><td data-colwidth="83" align="center" style="box-sizing: border-box;"><section><span leaf="">Authorization Bypass</span></section></td><td align="center" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">9.9</span></strong></td><td align="center" style="box-sizing: border-box;"><section><span leaf="">查看详情</span></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="148" align="center" style="box-sizing: border-box;"><section><span leaf="">CVE-2026-48766</span></section></td><td data-colwidth="155" align="center" style="box-sizing: border-box;"><section><span leaf="">typebot.io</span></section></td><td data-colwidth="83" align="center" style="box-sizing: border-box;"><section><span leaf="">Sensitive Data Exposure</span></section></td><td align="center" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">7.6</span></strong></td><td align="center" style="box-sizing: border-box;"><section><span leaf="">查看详情</span></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="148" align="center" style="box-sizing: border-box;"><section><span leaf="">CVE-2026-48767</span></section></td><td data-colwidth="155" align="center" style="box-sizing: border-box;"><section><span leaf="">typebot.io</span></section></td><td data-colwidth="83" align="center" style="box-sizing: border-box;"><section><span leaf="">Sensitive Data Exposure</span></section></td><td align="center" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">7.6</span></strong></td><td align="center" style="box-sizing: border-box;"><section><span leaf="">查看详情</span></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="148" align="center" style="box-sizing: border-box;"><section><span leaf="">CVE-2026-45296</span></section></td><td data-colwidth="155" align="center" style="box-sizing: border-box;"><section><span leaf="">OpenReplay</span></section></td><td data-colwidth="83" align="center" style="box-sizing: border-box;"><section><span leaf="">Improper Access Control</span></section></td><td align="center" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">7.7</span></strong></td><td align="center" style="box-sizing: border-box;"><section><span leaf="">查看详情</span></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="148" align="center" style="box-sizing: border-box;"><section><span leaf="">CVE-2026-46372</span></section></td><td data-colwidth="155" align="center" style="box-sizing: border-box;"><section><span leaf="">SillyTavern</span></section></td><td data-colwidth="83" align="center" style="box-sizing: border-box;"><section><span leaf="">SSRF</span></section></td><td align="center" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">8.5</span></strong></td><td align="center" style="box-sizing: border-box;"><section><span leaf="">查看详情</span></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="148" align="center" style="box-sizing: border-box;"><section><span leaf="">CVE-2026-45260</span></section></td><td data-colwidth="155" align="center" style="box-sizing: border-box;"><section><span leaf="">pimcore</span></section></td><td data-colwidth="83" align="center" style="box-sizing: border-box;"><section><span leaf="">Missing Authorization</span></section></td><td align="center" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">8.1</span></strong></td><td align="center" style="box-sizing: border-box;"><section><span leaf="">查看详情</span></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="148" align="center" style="box-sizing: border-box;"><section><span leaf="">CVE-2026-41235</span></section></td><td data-colwidth="155" align="center" style="box-sizing: border-box;"><section><span leaf="">froxlor</span></section></td><td data-colwidth="83" align="center" style="box-sizing: border-box;"><section><span leaf="">Incorrect Authorization</span></section></td><td align="center" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">8.8</span></strong></td><td align="center" style="box-sizing: border-box;"><section><span leaf="">查看详情</span></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="148" align="center" style="box-sizing: border-box;"><section><span leaf="">CVE-2026-41236</span></section></td><td data-colwidth="155" align="center" style="box-sizing: border-box;"><section><span leaf="">froxlor</span></section></td><td data-colwidth="83" align="center" style="box-sizing: border-box;"><section><span leaf="">Link Following</span></section></td><td align="center" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">8.8</span></strong></td><td align="center" style="box-sizing: border-box;"><section><span leaf="">查看详情</span></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="148" align="center" style="box-sizing: border-box;"><section><span leaf="">CVE-2026-43984</span></section></td><td data-colwidth="155" align="center" style="box-sizing: border-box;"><section><span leaf="">Tautulli</span></section></td><td data-colwidth="83" align="center" style="box-sizing: border-box;"><section><span leaf="">Stored XSS</span></section></td><td align="center" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">8.9</span></strong></td><td align="center" style="box-sizing: border-box;"><section><span leaf="">查看详情</span></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="148" align="center" style="box-sizing: border-box;"><section><span leaf="">CVE-2026-43985</span></section></td><td data-colwidth="155" align="center" style="box-sizing: border-box;"><section><span leaf="">Tautulli</span></section></td><td data-colwidth="83" align="center" style="box-sizing: border-box;"><section><span leaf="">CSRF</span></section></td><td align="center" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">8.8</span></strong></td><td align="center" style="box-sizing: border-box;"><section><span leaf="">查看详情</span></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="148" align="center" style="box-sizing: border-box;"><section><span leaf="">CVE-2026-43986</span></section></td><td data-colwidth="155" align="center" style="box-sizing: border-box;"><section><span leaf="">Tautulli</span></section></td><td data-colwidth="83" align="center" style="box-sizing: border-box;"><section><span leaf="">SSRF</span></section></td><td align="center" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">9.9</span></strong></td><td align="center" style="box-sizing: border-box;"><section><span leaf="">查看详情</span></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="148" align="center" style="box-sizing: border-box;"><section><span leaf="">CVE-2026-54091</span></section></td><td data-colwidth="155" align="center" style="box-sizing: border-box;"><section><span leaf="">filebrowser</span></section></td><td data-colwidth="83" align="center" style="box-sizing: border-box;"><section><span leaf="">Incorrect Authorization</span></section></td><td align="center" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">7.5</span></strong></td><td align="center" style="box-sizing: border-box;"><section><span leaf="">查看详情</span></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="148" align="center" style="box-sizing: border-box;"><section><span leaf="">CVE-2026-50279</span></section></td><td data-colwidth="155" align="center" style="box-sizing: border-box;"><section><span leaf="">craftcms</span></section></td><td data-colwidth="83" align="center" style="box-sizing: border-box;"><section><span leaf="">Improper Authorization</span></section></td><td align="center" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">6.5</span></strong></td><td align="center" style="box-sizing: border-box;"><section><span leaf="">查看详情</span></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="148" align="center" style="box-sizing: border-box;"><section><span leaf="">CVE-2026-50280</span></section></td><td data-colwidth="155" align="center" style="box-sizing: border-box;"><section><span leaf="">craftcms</span></section></td><td data-colwidth="83" align="center" style="box-sizing: border-box;"><section><span leaf="">Improper Access Control</span></section></td><td align="center" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf="">6.5</span></strong></td><td align="center" style="box-sizing: border-box;"><section><span leaf="">查看详情</span></section></td></tr></tbody></table>  
##   
  
0x03 更新介绍  
```
fix: persist uploaded ZIP project sources for worker audits (97ce141)
docs: update acknowledgements in README (f1b96c2)
docs: update architecture design (bfff54f)
```  
  
  
0x04 使用介绍  
  
📦安装与使用指南  
  
无需克隆仓库，一行命令即可启动：  
  
Linux / macOS / Git Bash :  
```
curl -fsSL https://raw.githubusercontent.com/larlarua/AutoCVE/v1.0.4/docker-compose.prod.yml \
  | docker compose -f - up -d
```  
  
Windows PowerShell / CMD :  
```
curl.exe -fsSL https://raw.githubusercontent.com/larlarua/AutoCVE/v1.0.4/docker-compose.prod.yml | docker compose -f - up -d
```  
### 🛠️ 源码部署  
  
适用于本地开发、功能调试或二次开发：  
```
cd AutoCVE
docker compose up -d --build
```  
### 🌐 服务访问  
  
服务启动完成后，可通过以下地址访问：  
  
<table><thead><tr><th data-colwidth="171"><span cid="n232" mdtype="table_cell"><span md-inline="plain"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-size: 17px;font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;"><span textstyle="" style="font-size: 16px;">服务</span></span></span></span></th><th><span cid="n233" mdtype="table_cell"><span md-inline="plain"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-size: 17px;font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;"><span textstyle="" style="font-size: 16px;">访问地址</span></span></span></span></th><th><span cid="n234" mdtype="table_cell"><span md-inline="plain"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-size: 17px;font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;"><span textstyle="" style="font-size: 16px;">用途</span></span></span></span></th></tr></thead><tbody><tr><td data-colwidth="171"><span cid="n236" mdtype="table_cell"><span md-inline="plain"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-size: 17px;font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;"><span textstyle="" style="font-size: 16px;">🖥️ 前端</span></span></span></span></td><td><span cid="n237" mdtype="table_cell"><span md-inline="link"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-size: 17px;font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;"><span textstyle="" style="font-size: 16px;">http://localhost:3000</span></span></span></span></td><td><span cid="n238" mdtype="table_cell"><span md-inline="plain"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-size: 17px;font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;"><span textstyle="" style="font-size: 16px;">AutoCVE 用户界面</span></span></span></span></td></tr><tr><td data-colwidth="171"><span cid="n240" mdtype="table_cell"><span md-inline="plain"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-size: 17px;font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;"><span textstyle="" style="font-size: 16px;">⚙️ 后端 API</span></span></span></span></td><td><span cid="n241" mdtype="table_cell"><span md-inline="link"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-size: 17px;font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;"><span textstyle="" style="font-size: 16px;">http://localhost:8000</span></span></span></span></td><td><span cid="n242" mdtype="table_cell"><span md-inline="plain"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-size: 17px;font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;"><span textstyle="" style="font-size: 16px;">后端接口服务</span></span></span></span></td></tr><tr><td data-colwidth="171"><span cid="n244" mdtype="table_cell"><span md-inline="plain"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-size: 17px;font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;"><span textstyle="" style="font-size: 16px;">📘 Swagger</span></span></span></span></td><td><span cid="n245" mdtype="table_cell"><span md-inline="url" spellcheck="false"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-size: 17px;font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;"><span textstyle="" style="font-size: 16px;">http://localhost:8000/docs</span></span></span></span></td><td><span cid="n246" mdtype="table_cell"><span md-inline="plain"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-size: 17px;font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;"><span textstyle="" style="font-size: 16px;">API 文档与接口调试</span></span></span></span></td></tr><tr><td data-colwidth="171"><span cid="n248" mdtype="table_cell"><span md-inline="plain"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-size: 17px;font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;"><span textstyle="" style="font-size: 16px;">🗄️ Adminer</span></span></span></span></td><td><span cid="n249" mdtype="table_cell"><span md-inline="link"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-size: 17px;font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;"><span textstyle="" style="font-size: 16px;">http://localhost:8080</span></span></span></span></td><td><span cid="n250" mdtype="table_cell"><span md-inline="plain"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-size: 17px;font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;"><span textstyle="" style="font-size: 16px;">数据库管理</span></span></span></span></td></tr></tbody></table>  
Tip  
**快速体验完整审计流程**  
```
配置模型 → 导入项目 → 创建审计任务 → 跟踪实时审计 → 管理漏洞 → 
编辑或导出报告
```  
## 🏆 CVE 挖掘成果  
  
AutoCVE 在为期一周的测试中，共发现并提交了   
**30 个安全漏洞**  
，覆盖   
**14 个开源项目**  
。  
  
点击表格中的 CVE 编号可查看官方记录，完整漏洞报告收录于   
**larlarua/vulnerability-reports**  
。  
  
  
**0x05 内部VIP星球介绍-V1.5（福利）**  
  
          
如果你想学习更多**渗透测试技术/应急溯源/免杀工具/挖洞SRC赚取漏洞赏金/红队打点等**  
欢迎加入我们**内部星球**  
可获得内部工具字典和享受内部资源和  
内部交流群，  
**每天更新1day/0day漏洞刷分上分****(2026POC更新至8922+)**  
**，**  
包含全网一些**付费扫描****工具及内部原创的Burp自动化漏****洞探测插件/漏扫工具等，AI代审工具，最新挖洞技巧等**  
。shadon/  
Hunter  
/  
0zone  
/  
Zoomeye  
/Quake/  
Fofa高级会员/AI账号  
/CTFShow等各种账号会员共享。详情点击下方链接了解，觉得价格高的师傅后台回复"   
**星球**  
 "有优惠券名额有限先到先得  
**❗️**  
啥都有  
**❗️**  
全网资源  
最新  
最丰富  
**❗️****（🤙截止目前已有2800+多位师傅选择加入❗️早加入早享受）**  
  
****  
最新漏洞情报分享：  
https://t.zsxq.com/DSAvv  
  
****  
  
**👉****点击了解加入-->>内部VIP知识星球福利介绍V1.5版本-1day/0day漏洞库及内部资源更新**  
  
****  
  
  
结尾  
  
# 免责声明  
  
  
# 获取方法  
  
  
**公众号回复20260707获取下载、回复 加群 获取交流群**  
  
****  
  
# 最后必看-免责声明  
  
  
      
文章中的案例或工具仅面向合法授权的企业安全建设行为，如您需要测试内容的可用性，请自行搭建靶机环境，勿用于非法行为。如  
用于其他用途，由使用者承担全部法律及连带责任，与作者和本公众号无关。  
本项目所有收录的poc均为漏洞的理论判断，不存在漏洞利用过程，不会对目标发起真实攻击和漏洞利用。文中所涉及的技术、思路和工具仅供以安全为目的的学习交流使用。  
如您在使用本工具或阅读文章的过程中存在任何非法行为，您需自行承担相应后果，我们将不承担任何法律及连带责任。本工具或文章或来源于网络，若有侵权请联系作者删除，请在24小时内删除，请勿用于商业行为，自行查验是否具有后门，切勿相信软件内的广告！  
  
  
  
# 往期推荐  
  
  
**1.内部VIP知识星球福利介绍V1.5（AI自动化）**  
  
**2.CS4.8-CobaltStrike4.8汉化+插件版**  
  
**3.全新升级BurpSuite2026.4专业(稳定版)**  
  
**4. 最新xray1.9.11高级版下载Windows/Linux**  
  
**5. 最新HCL AppScan Standard**  
  
  
渗透安全HackTwo  
  
微信号：关注公众号获取  
  
后台回复星球加入：  
知识星球  
  
扫码关注 了解更多  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/RjOvISzUFq6qFFAxdkV2tgPPqL76yNTw38UJ9vr5QJQE48ff1I4Gichw7adAcHQx8ePBPmwvouAhs4ArJFVdKkw/640?wx_fmt=png "二维码")  
  
  
  
上一篇文章：  
[Nacos配置文件攻防思路总结|揭秘Nacos被低估的攻击面](https://mp.weixin.qq.com/s?__biz=Mzg3ODE2MjkxMQ==&mid=2247492839&idx=1&sn=b6f091114fbd8e8922153a996c8f4f1c&scene=21#wechat_redirect)  
  
  

#  AutoCVE：7 天测试期挖出 30 个真实 CVE，国产 AI 漏洞挖掘平台拿出了硬战绩  
原创 JunYi
                    JunYi  毅心安全   2026-06-30 09:22  
  
 作者：larlarua | 项目地址：github.com/larlarua/AutoCVE | ⭐ 562 Stars · v1.0.3  
  
作者：larlarua | 项目地址：github.com/larlarua/AutoCVE | ⭐ 562 Stars · v1.0.3  
  
各位好，今天介绍的项目，是我目前看过的 **AI 漏洞挖掘工具里"战绩"展示最硬核的一个**  
——不是 PPT 演示，不是靶机 Demo，是真实提交到 CVE 官方数据库、有 CVE 编号可查的实战成果。  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/9mJNQIzib3Q2UcCKpFnuAluiaictZ9J4OhwXKC5CBLKAWXR2UjNuxkW9mibkabwztMicUMsA2KIGOAYicPuhyl1p8EnmqCJIRFbiaY2TYvBEuqY7DI/640?from=appmsg "null")  
  
项目叫 **AutoCVE**  
，国人作者 larlarua 开发，AGPL-3.0 开源，562 Stars，最新版本 v1.0.3（2026 年 6 月 27 日发布）。一句话定位：**Agent 驱动的全自动 CVE 挖掘平台**  
，覆盖从筛选目标项目、导入源码、Agent 审计漏洞、到生成 CVE 申报报告的完整流程。  
  
软件流程图  
  
![](https://mmbiz.qpic.cn/mmbiz_png/9mJNQIzib3Q00gYjbAkraPTUFvuTtmesJjDfTqEWUuXe8xDWkXLaHZUOH7HicHsGCCCsE6u8qouGLZ3iaZiaDGdyiaRYaFfAKyyqPuSsK1VzB7VU/640?wx_fmt=png&from=appmsg "")  
  
### 先看战绩：这是这篇文章最有说服力的部分  
  
README 里挂着一份**可验证**  
的成果清单——**为期一周的测试中，AutoCVE 共发现并提交了 30 个安全漏洞，覆盖 14 个开源项目**  
，每一条都附带官方 CVE 记录链接和完整漏洞报告。  
  
挑几个有代表性的说：  
  
**Tautulli**  
（Plex 媒体服务器监控工具）被挖出 3 个漏洞，其中 **CVE-2026-43986 是一个 SSRF，CVSS 评分高达 9.9**  
，属于严重级别。同一个项目还有一个 Stored XSS（8.9 分）和一个 CSRF（8.8 分）。  
  
**typebot.io**  
（开源对话表单构建工具）被挖出 5 个漏洞，**CVE-2026-48765 是 Authorization Bypass（鉴权绕过），CVSS 同样是 9.9**  
。同批还包括 SSRF（8.2）、Missing Authorization（8.2）和两个敏感数据泄露（7.6）。  
  
**froxlor**  
（虚拟主机管理面板）挖到 2 个 8.8 分的漏洞——一个 Incorrect Authorization，一个 Link Following。  
  
**Chartbrew**  
（数据可视化工具）一个项目就被挖出 5 个 CVE，全部是访问控制和授权类问题。  
  
**Lemmy**  
（去中心化社交平台，Reddit 开源替代品）挖到 2 个 SSRF。  
  
漏洞类型分布也很有意思：**SSRF 出现频率最高**  
（在 Lemmy、o2oa、xxl-job、typebot.io、SillyTavern、Tautulli 等多个项目里反复命中），其次是各类 Improper/Missing/Incorrect Authorization（鉴权类问题），还挖到了 SQL 注入（JeecgBoot）、RCE（o2oa）、硬编码密钥（xxl-job）。  
  
**这个结果分布本身很说明问题**  
：SSRF 和鉴权类漏洞，正是当前 AI 静态/语义审计最擅长发现的类型——它们往往体现为"代码逻辑允许某个不该被允许的操作路径"，这种模式相对容易通过代码语义分析定位，比起需要深刻理解复杂业务上下文的逻辑漏洞，AI 在这类问题上命中率更高。这也算是验证了当前 AI 审计能力的真实边界。  
### 它是怎么做到的：Multi-Agent 协同架构  
  
AutoCVE 不是单个 AI 一条龙跑到底，而是设计了一套**五个专项 Agent 协同**  
的架构，由 Orchestrator（总调度）统筹：  
  
**Recon Agent**  
：负责信息收集，对目标项目做初步侦察——项目结构、依赖关系、技术栈识别。  
  
**Scan Agent**  
：调用专业安全扫描工具对代码做自动化扫描，产出初步发现。  
  
**Triage Agent**  
：对 Scan 阶段的扫描结果做误报过滤。这是非常关键的一环——传统 SAST 工具的误报率普遍偏高，Triage Agent 的存在让后续投入到真正有价值的线索上。  
  
**Finding Agent**  
：**这是整个系统的核心审计能力**  
，专为 CVE 挖掘场景设计。它直接分析项目源码，结合 ReAct Loop（思考-行动-观察循环）做深度推理，调用专项工具，通过 Nudge 纠偏机制防止分析跑偏，最终用 FinalizeFinding  
 这个结构化终止机制产出漏洞结论。  
  
**Verification Agent**  
：对 Triage 和 Finding 两路汇总的发现做**动态验证**  
——不是停留在"看起来像漏洞"，而是要实际验证可触发性。  
  
最后由 Merge/Finalize 阶段去重、合并、整理成结构化的漏洞条目入库。  
  
这个架构的设计思路和我们之前介绍过的 Decepticon、ASTER 是同一脉络——**专项化 Multi-Agent 比单一全能 Agent 更稳定**  
，每个 Agent 只专注做好一类工作，互相配合形成完整链路。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/9mJNQIzib3Q02Zf9vQRqquxJxnUbpbHtjYxQxA4NjHc5UIGib1bUgBB7zxWa7eHlp9ymhh2x1nsAVuY2wbN8SzWU6FYrvRkXJO6ib5c4Hm1U7Q/640?wx_fmt=png&from=appmsg "")  
  
  
### 三种审计模式：按需选择速度和深度  
  
AutoCVE 提供三种可切换的审计模式，对应不同的使用场景：  
  
1、 增强扫描（Scan → Triage）：核心是"快"，跑工具扫描，AI 帮你过滤误报。适合需要快速摸底一个项目整体安全状况的场景。  
  
2、 智能审计（Finding）：核心是"深"，跳过常规工具扫描，直接让 Finding Agent 对源码做深度语义级审计。**这正是 CVE 和 0Day 挖掘场景应该用的模式**  
——因为高价值漏洞往往藏在工具扫描覆盖不到的业务逻辑里。  
  
3、 综合审计（Scan → Triage + Finding）：工具扫描和源码深度分析双管齐下，覆盖面最广，但消耗的时间和 Token 也最多，适合对目标项目做全量审计。  
  
从 30 个 CVE 的实战战绩来看，**智能审计模式应该是产出主力**  
，因为这些漏洞（SSRF、鉴权绕过、授权缺失）大多需要理解业务逻辑和调用链才能发现，单纯的工具扫描很难直接命中。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/9mJNQIzib3Q1z9sRXt4pVrr8Y6Sp2wXicejApJ6ySgVIvAicPVTHiabS3IgpYDuXX0TrZCeuKP5EiagPS8jj1t0zV200wuUMkSeBJBKdB69Aed28/640?wx_fmt=png&from=appmsg "")  
  
  
### 交互式审计：不是黑盒,可以追问  
  
这一点是我觉得设计得比较人性化的部分。AutoCVE 把完整的审计过程作为会话上下文保留，**审计结束后你还能继续追问 Agent**  
：让它补充证据、解释完整攻击链、完善复现步骤、或者针对某个发现做进一步扩展分析。  
  
配合可视化的审计追踪界面——活动日志、Agent Tree（显示各 Agent 的调用关系）、工具调用记录、阶段进度、初步报告、完整会话——你能完整复盘每一次审计的执行路径，而不是只拿到一个"这里有漏洞"的结论自己摸不着头脑。  
  
这个设计解决了 AI 安全工具一个常见痛点：**AI 说"这是个漏洞"，但你看不懂它为什么这么说**  
。可追问、可复盘的设计让人工复核环节变得高效很多——CVE 申报对证据链的完整性要求很高，这一点尤其重要。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/9mJNQIzib3Q1IBUwZbHJ3V9FYnkDK8MNoqutQUuFRg6Qp1PYjBkawxqS6XcCIYlGibT8jqNRbMwTJyZct3MTz5A6kEXDWSzG0IfuPBpdEyE18/640?wx_fmt=png&from=appmsg "")  
  
  
### 漏洞管理与 Skills 扩展  
  
审计发现的漏洞由 Agent 自动调用工具提交，**经过去重后以结构化形式入库**  
，统一在漏洞管理模块维护，避免重复劳动和信息散落。  
  
**Skills 配置机制**  
：可以为不同的 Agent 配置专属技能包，灵活扩展各 Agent 的能力边界。这和 ASTER、Flounder 用的都是同一套"按需注入技能"的设计理念——通过模块化技能扩展 Agent 在特定领域（比如某种语言、某类框架）的审计能力，而不需要重写整个框架。  
### 快速部署：一行命令起步  
  
无需克隆仓库，一行命令直接拉起完整服务：  
```
# Linux / macOS / Git Bashcurl -fsSL https://raw.githubusercontent.com/larlarua/AutoCVE/v1.0.3/docker-compose.prod.yml \  | docker compose -f - up -d
```  
  
powershell  
```
# Windows PowerShell / CMDcurl.exe -fsSL https://raw.githubusercontent.com/larlarua/AutoCVE/v1.0.3/docker-compose.prod.yml | docker compose -f - up -d
```  
  
源码部署（适合二次开发）：  
```
git clone https://github.com/larlarua/AutoCVE.gitcd AutoCVEdocker compose up -d --build
```  
  
启动后的服务一览：前端控制台在 localhost:3000  
，后端 API 在 localhost:8000  
，Swagger 接口文档在 localhost:8000/docs  
，还有一个 Adminer 数据库管理界面在 localhost:8080  
。  
  
技术栈：**Python（FastAPI）+ React + PostgreSQL**  
，是一套相当标准的现代化 Web 应用架构，工程化程度不低。  
  
**典型使用流程**  
：配置模型 → 导入项目 → 创建审计任务 → 跟踪实时审计 → 管理漏洞 → 编辑或导出报告。整个链路里，**用户唯一需要做的手动操作就是复制生成的报告内容并提交**  
，剩下的 CVE 申请流程交给人工把关。  
### 务实的一点：它没有自动提交 CVE  
  
这一点要特别说清楚，避免误解。AutoCVE 自动化的是**审计和报告生成**  
，不是自动化提交 CVE 本身。README 里专门写了安全与合规要求：  
> 提交漏洞时，请遵循目标项目的安全政策及漏洞披露规范，包括但不限于 SECURITY.md  
、GitHub Private Vulnerability Reporting、CNA 提交流程，以及其他负责任的漏洞披露机制。  
  
  
这意味着每一个生成的报告，在提交给项目方或 CNA（CVE 编号分配机构）之前，仍然需要人工审核确认，遵循标准的负责任披露（Responsible Disclosure）流程。**自动化的是发现和取证，最后一步的判断和提交仍然由人来负责**  
，这是一个负责任的设计取舍。  
### 客观评价  
  
**亮点：**  
 战绩可验证是最大的说服力——30 个有官方 CVE 编号、有详细报告的真实漏洞，这在 AI 安全工具里非常少见，大多数同类项目还停留在靶机 Demo 阶段。Multi-Agent 专项化架构设计成熟，五个 Agent 各司其职。三种审计模式灵活适配不同场景需求。交互式追问 + 可视化追踪让审计过程透明可信,不是黑盒。漏洞类型分布合理（SSRF/鉴权类为主），符合当前 AI 审计能力的真实边界，没有夸大宣传。  
  
**需要注意的地方：**  
 项目还比较年轻（562 Stars，31 次提交），社区规模有限。战绩数据来自作者自述，建议感兴趣的读者通过 CVE 官方记录链接和 larlarua/vulnerability-reports  
 仓库自行核实细节。AI 审计在 SSRF/鉴权类问题上命中率高，但对需要深刻业务上下文理解的复杂逻辑漏洞，能力边界仍然存在。AGPL-3.0 协议对商业集成有一定限制。  
  
**整体定位：**  
 这是目前国内开源社区里，**用真实可验证战绩说话的 AI 漏洞挖掘平台**  
。如果你是做开源项目安全审计、CVE 研究或者想体验"AI 自动找洞"这件事到底靠不靠谱，AutoCVE 提供了一个有数据支撑的参考样本，值得亲自部署体验。  
> ⚠️ 本项目仅限用于已获授权的安全研究、代码审计及学习交流，严禁用于任何未经授权的漏洞扫描、渗透测试或安全评估。请确保仅在获得明确授权的目标与环境中执行扫描、漏洞验证或 PoC 测试。提交漏洞时请严格遵循目标项目的安全政策与负责任披露规范。  
  
  
觉得这份战绩有说服力的话点个在看，下期继续聊 AI 安全工具链的实战进展。  
  
  
   
  
  

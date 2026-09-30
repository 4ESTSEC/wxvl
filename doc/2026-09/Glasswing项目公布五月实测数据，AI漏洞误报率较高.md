#  Glasswing项目公布五月实测数据，AI漏洞误报率较高  
 FreeBuf   2026-09-30 10:00  
  
![FreeBuf](https://mmbiz.qpic.cn/mmbiz_gif/icBE3OpK1IX2aMib6PHTGten8FDbIMIGb6kZub9YcAicW0oyIOjcheLyPib5zXhmtRz5SKCicHaKVcK76FvtvmHYvOicNVl4piaicnG4Q6BFvubHzIY/640?wx_fmt=gif "")  
  
  
![image](https://mmbiz.qpic.cn/sz_mmbiz_png/icBE3OpK1IX1qAwbiaz0esGfmIKibVTuuKhXVo0KN1nHe0MjMjfrSFicv5DpibMJPrAnfWMSzDYbYZceuOCZYxoToe1Z6tAfbfh4mA0CjKkHsq7c/640?wx_fmt=png "")  
  
  
今年4月Anthropic推出Project Glasswing时，整个科技行业都做好了迎接重大冲击的准备。5个月过去，DevSecOps团队已经开始面对大量AI发现的漏洞，并从中筛选真正值得修复的问题。但一名安全研究人员指出，到目前为止，真正需要优先处理的漏洞并不多。  
  
  
今年8月，Anthropic首次更新了漏洞披露台账。漏洞优先级工具厂商Vulncheck的安全研究员Patrick Garrity表示，Mythos最初发现的漏洞中，只有一小部分最终进入这一台账，而被标记为已修复的漏洞还不到Mythos发现总量的1%。  
  
  
Garrity和Vulncheck团队还在持续追踪AI发现的漏洞是否存在野外利用案例，他们将Anthropic模型发现的漏洞、伯克利漏洞研究计划的数据，与Vulncheck已知被利用漏洞库的数据做了关联比对。统计显示，在1061个由AI辅助发现的漏洞中，截至2026年6月仅记录到14个存在野外利用的案例，占比约1%。 Garrity表示：“发现一个漏洞，不代表它对攻击者有实际利用价值，也不代表它在特定场景下一定可被利用。”  
  
  
软件安全专家指出，漏洞发现数量与实际修复比例之间的巨大差距，反映出人工分诊已经成为明显瓶颈。DevSecOps团队需要判断哪些发现真正重要、漏洞有多严重、是否值得修复，以及如果需要修复，应该采用什么方式。  
  
  
独立安全布道师Neil Carpenter表示：“之前大家对‘漏洞末日’充满焦虑，担心人人都能发现新的0Day漏洞并加以利用，造成严重冲击。紧接着又开始担心‘这么多漏洞我们怎么修得完’，这给下游的开发者、蓝队等所有防御方都带来了沉重负担。” 参与过Glasswing项目的红帽高级首席安全架构师Michele Chubirka表示，团队可以借助AI梳理Glasswing项目产出的积压漏洞，但这远不能完全取代人工环节。  
  
  
Chubirka与公司平台工程师搭建了一套工作流引擎，据她描述这套引擎“半是执行框架，半是确定性安全工具集成模块”，为红帽OpenShift产品线下所有代码仓库提供扫描框架支撑。Chubirka强调自己的发言仅代表个人立场，不代表红帽官方观点，她表示：“人们觉得AI有魔法，认为前沿模型能自动帮人完成所有工作，但事实并非如此。” Garrity同时提醒，将AI部署到生产工作流本身会引入全新的安全风险，AI相关产品正快速成为攻击者的目标，推动整体攻击面大幅扩张。  
  
  
Part  
01  
  
Glasswing统计数据出炉  
  
  
Garrity对披露台账的分析显示，Anthropic共上报26,153个漏洞发现结果，其中2,736个进入漏洞披露台账，占比10.5%；2,096个已提交给项目维护者，占比8%；最终由维护者确认并完成修复的仅202个，占比0.8%。此外，Anthropic还主动撤回了245个发现结果。  
  
  
在台账中，Anthropic将独立人工分诊和审核环节称为整个流程中的“限速步骤”。进一步对比Claude的漏洞评级与项目维护者的实际判定后，Garrity发现二者存在明显偏差：Claude将91.5%的发现结果评为高危或严重级别，而维护者对同一批漏洞的判断中，只有51.3%被认定为高危或严重。  
  
  
这种差距也出现在具体项目中。Garrity指出，Glasswing扫描得到的结果几乎都被判定为高危或严重，但项目维护者给出的评级明显更低。今年5月，Linux命令行工具curl项目创始人兼首席开发者Daniel Stenberg就在博客中披露，Mythos识别出的5个“确认漏洞”经过团队核查后，最终只保留了1个。  
  
  
其余4个发现中，3个被认定为误报，相关行为早已在API文档中明确说明；另外1个则只被视为普通bug。唯一确认的问题最终也只会获得低危CVE评级，并计划随6月下旬发布的curl 8.21.0一并公告。按照Stenberg的判断，这个缺陷并不会造成严重影响。  
  
  
在Garrity看来，问题并不完全出在模型能力本身，而是缺少足够的领域知识来约束和指导漏洞判断。安全漏洞评估本身就是高度依赖专业经验的工作，如果只是使用AI工具大规模扫描开源项目，却没有由领域专家设计合适的Agent skills和评估流程，很容易在短时间内产生大量发现结果，其中相当一部分最终都会变成无效噪音。  
  
  
截至发稿时，Anthropic方面尚未回应置评请求。  
  
  
Part  
02  
  
AI漏洞发现存大量误报  
  
  
大量噪音并不意味着AI在漏洞发现中没有实际价值。Vulncheck允许用户在提交漏洞时说明是否使用了AI辅助，而目前大约45%的提交者会主动标注这一点。Garrity认为，这一比例还没有覆盖那些使用了AI、但没有明确披露的研究人员。  
  
  
Omdia今年8月发布的一份调查也显示，近九成受访组织认为Agentic AI能够改善IT资产安全健康状况和安全态势可见性。其中40%的组织认为作用中等，49%认为作用显著。AI确实可以帮助组织更快发现问题并辅助安全决策，但真正的难点仍然在于，如何从海量发现结果中筛选出值得处理的部分，并进一步推动修复落地。  
  
  
实际应用中，大量时间仍然消耗在分诊和验证环节。Carpenter认为，Agentic AI更适合承担的是对多类确定性安全工具输出、代码信息和漏洞结果进行统一分析，帮助安全团队批量完成原本需要大量人工时间才能做出的判断，并发现不同漏洞之间可能存在的关联。真正节省时间的地方，不只是“发现更多漏洞”，而是减少后续人工梳理和关联分析的成本。  
  
  
Chubirka的实践也说明，AI只有和专业人员结合使用时，价值才更容易释放。她所在的团队会先拿到基础报告，经过机器分诊后再由人工复核，对存疑结果逐一确认或排除。完成分诊后，团队还会利用AI生成补丁，但这些补丁并不能全部直接采用：大约60%基本可用，20%虽然能工作但不符合设计原则或编码规范，剩下20%则完全无法使用。  
  
  
基于这样的流程，团队会继续对AI发现的漏洞进行验证和修复。Chubirka认为，这正是Glasswing项目希望建立的VulnOps模式：把漏洞发现、分诊、验证和修复串联成持续运行的工作流，而不是继续依赖传统的批量式处理。  
  
  
不过，这套模式真正落地仍然离不开领域专家的深度参与。她回忆，团队在修复项目期间曾连续45小时进行测试，集中排查误报和漏报。即便有AI参与，最终仍需要5名成员全职投入。要想把漏洞管理真正扩展到大规模场景，关键不只是引入AI，而是把整套流程做成足够标准化、可重复运行的“流水线”。  
  
  
Part  
03  
  
AI扩张整体攻击面  
  
  
在攻防双方都开始用AI对抗AI的同时，Garrity认为，一个被低估的风险是AI工具本身正在形成新的攻击面。Garrity表示：“几乎没有人在讨论部署这些技术后会产生什么风险，而AI产品本身已经非常快地成为攻击目标。”  
  
  
近期已经出现多起AI框架远程代码执行漏洞案例，涉及Langflow、Bifrost、MCP Python SDK等产品。 这类风险的部分成因在于，企业给AI开放敏感信息权限、将AI工具接入基础设施的速度，远超安全和治理团队的跟进速度。  
  
  
Garrity表示：“企业部署这些技术的时候，直接把核心权限都交了出去。大家都觉得‘AI需要知道我Salesforce里的所有数据，需要控制AWS权限，需要管控所有业务流程’，很多组织直接把最小权限访问原则抛在了脑后。”  
  
  
参考来源：  
  
Glasswing results put AI 'vulnpocalypse' to the test  
  
https://www.techtarget.com/it-infrastructure/news/366651381/Glasswing-results-put-AI-vulnpocalypse-to-the-test  
  
###   
  
### 推荐阅读  
  
  
[](https://mp.weixin.qq.com/s?__biz=MjM5NjA0NjgyMA==&mid=2651347479&idx=1&sn=1cf9c858a30d0b7af06d196002bd2913&scene=21#wechat_redirect)  
  
  
###   
  
### 电报讨论  
  
  
[]()  
  
  
  
![扫码加入AI安全交流群](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX0Py7ibxdLKXia1pMziaic5vIE9XPXG9OGaeJDa07iaG10eicuzhW59nwpF5msHiaYZvfMqCNkx2aFDiaMzm3oAf4rTaHXU5UAI1mUYgts/640?wx_fmt=png "")  
  
  
  
![下载FreeBuf知识大陆APP](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX0TIGzII2Hcmtzu7AJeZFicnqd1mXojVoawje2uLxYqwJbVgzJpmSXzVhrpOsLurRZ2lVa4vfgLBqg7uJKbrKg5F18VzZxVPicZU/640?wx_fmt=png "")  
  
  
  
  

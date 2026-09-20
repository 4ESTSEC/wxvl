#  Giskard实测：AI Agent漏洞扫描器 一句话自动穷举提示注入 OWASP全查 5828星  
原创 句芒安全实验室
                    句芒安全实验室  句芒安全实验室   2026-09-20 02:11  
  
聊到 AI 安全，句芒前阵子给你推的基本都是"怎么打"——越狱、提示注入、Agent 逃逸，一个比一个凶。今天换个视角，讲一个"怎么防、怎么自查"的工具：**Giskard**  
。它是这几天 GitHub 上活跃度很高的 LLM/Agent 漏洞扫描与评测库，定位一句话：**你只要用一句话描述你的 AI 应用，它自动给你生成一整套对抗测试，把提示注入、有害内容、误导信息这些漏洞挨个穷举一遍**  
。  
## 先核身份：不是野鸡项目  
  
句芒发布前按老规矩用 GitHub API 当天核实：Giskard-AI/giskard-oss  
，**5828 颗星、537 个 fork**  
，Python 实现，**Apache-2.0 协议**  
。关键看活跃度——2022-03 建仓，最近一次推送是 **2026-09-18**  
，也就是前一天还有人在维护，属于一直在更新的活项目（这对安全工具很重要，躺在原地不更新的库你也不敢用）。  
  
它对外的定位是 **"Evals, Red Teaming and Test Generation for Agentic Systems"**  
（面向智能体的评测、红队与测试生成）。它背后是家做 LLM 评测/安全测试的公司 Giskard AI，开源库走 Apache-2.0。  
## 它是什么：v3 是一次面向 Agent 的重写  
  
Giskard 老用户可能知道它过去是"给 AI 模型打分"的评测工具。**2025 年它把架构推倒重写，出了 v3**  
——README 里写得很直白：v3 是为"对 AI Agent 做动态、多轮测试"从零重写的，砍掉了拖沓的重依赖，全新引入了更强的 AI 漏洞扫描器和 RAG 评估，两者都原生内置在 giskard-scan  
 里。老 v2 还能用，但官方明说不再积极维护。  
  
v3 拆成一套**模块化的小包**  
，每个包只带自己需要的依赖，想跑什么装什么：  
- **giskard-checks**  
：造评测（eval），从简单断言到 LLM 当裁判（LLM-as-judge）都能做  
  
- **giskard-scan**  
：漏洞扫描/红队层，自动生成对抗测试套件  
  
- 各种 **provider SDK**  
（giskard[openai]  
、giskard[anthropic]  
 等），给 LLM 裁判/生成器接各家模型  
  
![Giskard v3 官方模块化架构图（来自仓库 readme/ 官方图）](https://mmbiz.qpic.cn/mmbiz_jpg/J2hBCjr4Lft8MeQicUkhnSNsYIWibgp8MF1OUtiaZnFJG7Uq6QbDdOB2Xjv9nSib5Vxc3vG96w2KgRCmtf6Eg6V383KK7QEhSqKWHk27PPYHcibw/640?wx_fmt=jpeg "Giskard v3 官方模块化架构图（来自仓库 readme/ 官方图）")  
  
安装也干净：pip install giskard  
 是基础；要扫漏洞就 pip install "giskard[scan]"  
；要 LLM 当裁判接模型就再加 "giskard[openai]"  
。注意它要 **Python 3.12+**  
。  
## giskard-scan：把一句话变成一整套攻击测试  
  
这是句芒最推荐你关注的部分，也是它"AI 安全实战"含量最高的地方。giskard-scan  
 是**面向智能体系统的红队与漏洞扫描层**  
：你只要用一句大白话描述你的 Agent 是干什么的，它就从 OWASP LLM Top-10 威胁类别自动生成对抗测试套件。  
```
import asynciofrom giskard.scan import vulnerability_scanasync def my_agent(inputs: str) -> str:    # 换成你真实的 Agent / 模型调用    return f"Echo: {inputs}"async def main() -> None:    await vulnerability_scan(        target=my_agent,        description="An e-commerce customer support chatbot.",        languages=["en"],    )asyncio.run(main())
```  
  
覆盖范围里最醒目的就是 **提示注入（prompt injection）**  
——它内置了一整包现成的注入 payload 数据集，开箱即用；除此之外还扫**有害内容、刻板印象、误导信息**  
等。而且它不是写死的黑盒：你自己可以传自定义的 ScenarioGenerator  
 实例，或者往 vulnerability_suite_generator_registry  
 注册新生成器，把你们业务特有的攻击面加进去。  
  
![Giskard 官方 Scan 演示（截取自仓库 readme/scan_updated.gif 一帧）](https://mmbiz.qpic.cn/sz_mmbiz_jpg/J2hBCjr4LfuibYVZ1CZiaDicZ5lwmg9efKdRGAxiccMlUic9JvIJ0JNQMlLXdzkQia3vw32njWvBtKDibTb0a6Hdic7qTXoFkibDApWxPC7r8Qv4GhfQ/640?wx_fmt=jpeg "Giskard 官方 Scan 演示（截取自仓库 readme/scan_updated.gif 一帧）")  
## giskard-checks：把"没毛病"变成可断言的评测  
  
只扫漏洞还不够，日常开发里更需要"改了代码别把功能搞坏"的回归测试。giskard-checks  
 就是干这个的轻量评测库。它聪明在认识到 LLM 输出**是非确定性的**  
——同样的输入，合法输出可能有好几种，所以它不做传统那种"期望值 100% 等于"的单元测试，而是用断言 + LLM 当裁判的组合。  
  
内置评测包括字符串匹配、比较、正则、语义相似度，以及 **LLM-as-judge**  
（Groundedness  
 指回答是否基于给定上下文、Conformity  
 指是否符合约束、LLMJudge  
 通用裁判）。核心概念也好理解：**Target**  
 是被测系统（任意可调用的输入→输出函数）、**Scenario**  
 是一组"交互+断言"、**Check**  
 是断言或 LLM 裁决、**Suite**  
 是把很多场景打包一起跑。多轮 Agent 对话也能测，不只测单次问答。  
## 从句芒视角看它为什么值得写  
  
70% 的"AI 安全"教程都在教你怎么**攻击**  
别人家的 Agent，但真的轮到自己给公司做一个带工具、能访问数据的 Agent 时，大多数人**根本不知道怎么系统地测它安不安全**  
——要么靠人肉瞎试几句提示注入，要么干脆不测直接上线。Giskard 抓的正是这个空档：**把"安全测试"从手工变自动、从拍脑袋变可重复。**  
  
它对句芒这种"AI 安全要落地"的诉求最加分的有三点：  
1. **语言即测试描述**  
。你不用写几百条攻击用例，一句"这是个电商客服机器人"它就自动展开成一套对抗套件。上手门槛极低。  
  
1. **对齐 OWASP LLM Top-10**  
。现在企业做 AI 合规、过审，绕不开这个清单，它直接按这个框架组织测试，出的报告对得上号。  
  
1. **自带提示注入现成数据集 + 可扩展**  
。既开箱即用，又给懂对抗的人留了自定义生成器的口子。  
  
## 上手与避坑  
  
想自己试试，最省事的路线：  
```
# Python 3.12+pip install "giskard[scan,openai]"   # 扫描 + OpenAI 裁判export OPENAI_API_KEY=sk-...         # 缺一个能当裁判的模型 keypython your_scan_script.py
```  
  
坑给你提前踩好：  
- **它要 LLM 当裁判，得先有个模型 key。**vulnerability_scan  
 的生成器和 Groundedness  
 这类 judge 都走 LLM，默认是 openai/gpt-4o-mini  
。想完全离线零成本是跑不起来的，得有张能调付费模型的卡（或用 Anthropic 等 provider）。  
  
- **v3 和 v2 用法不是一回事。**  
 网上很多老教程讲的是 v2 的 giskard.scan(model)  
、RAGET 那套。你要是拿到旧代码，先确认版本——v3 是 giskard.scan.vulnerability_scan(target=...)  
 + quality_scan  
，别照旧文档硬套。  
  
- **Python 3.12+ 是硬要求。**  
 机器上是 3.11 及以下会装不上，得先升环境。  
  
- **有遥测，默认开着。**  
 会在本地建 ~/.giskard/id  
 发匿名统计（官方说**不传任何 prompt 和输出**  
）。介意的话 export DO_NOT_TRACK=1  
 关掉，在 import 之前设。  
  
- **它测的是"我说的话"能不能被利用，不是证伪。**  
 扫描出的高危项要看懂是"你的 Agent 在该攻击向量下有风险"，得结合真实业务判断威胁是否成立，别拿到报告就吓自己或拿去表功。  
  
## 适合谁  
  
**适合**  
：在**基于 LLM 做产品/Agent**  
、还没成体系做安全测试的研发和 AI 团队（最划算）；要做 **OWASP LLM Top-10 合规自查**  
的安全同学；想研究"如何用一句话描述自动展开成对抗测试"这个思路的学习者。**不适合**  
：纯研究怎么打别人系统的人——它是自查/防御侧的，进攻性内容很克制，且要自备模型 key。仓库就是 Giskard-AI/giskard-oss  
，docs.giskard.ai  
 有完整的 checks/scan 文档，v3 的架构和迁移说明也写在里面——具体怎么接你的 Agent，你自己去翻，别照抄句芒这几行示例就以为万事大吉。  
  

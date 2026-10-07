#  如何通过AI高效复现传统漏洞？  
原创 Oxo Security
                    Oxo Security  Oxo Security   2026-10-07 00:17  
  
# 一、真正挤占时间的，是反复准备与衔接  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Y05UtykogHRfXjn7mlVAsUoKuTGMxdYvvniaFTLVl83nlb9v8aw8WAsltZFh7yQn17eSSezP2f3QLXMHgicdWmOPZ9tav38xymhe2eCTibqRjQ/640?wx_fmt=png&from=appmsg "")  
  
上午，你准备复现一个熟悉的漏洞。下载项目，调整运行时，装依赖，配数据库；应用终于启动，又要找对应版本的资料、准备工具、修改请求、整理响应。等坐下来分析安全问题，时间已经过去了大半。  
  
**假设一次复现花了十小时，其中六七小时耗在环境搭建、依赖配置、查资料和搬运结果上，留给分析与判断的时间就只剩三四小时。**  
 这些工作都有必要，但不必每一步都由工程师亲手衔接。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Y05UtykogHSiaN4h9pnkh2FIL6rVt5fNFYCCmUVkicJTWYq1dPsB9HLuvtVzWIvGDoaxQf47G9cRfrIic8CI7Q39B851xV97Tcib5X0HO2H1w94/640?wx_fmt=png&from=appmsg "")  
  
Oxo Operator 要改变的，就是这份时间分配：让 Agent——能调用工具执行任务的 AI 助手——连续接手准备、检索、执行和留证，让工程师把注意力放回问题本身。  
  
知道漏洞原理，不等于拿到项目就能验证。环境要跑起来，公开资料要对应当前代码，请求要适配测试条件，结果还得留下记录。  
  
繁琐之处在于，完成一步之后，下一步总要重新组织材料：网页里的线索搬到请求工具，响应复制到聊天窗口，源码位置再写进笔记。工具换了，上下文也要跟着人搬。  
  
这笔开销主要落在三个地方：  
<table><caption><section><span leaf=""><br/></span></section></caption><tfoot><tr><td><section><span leaf=""><br/></span></section></td></tr></tfoot></table><table><thead><tr><th style="padding:12px 10px;border-bottom:1px solid rgba(217,70,239,.10);background:linear-gradient(90deg,rgba(255,82,200,.10),rgba(217,70,239,.08),rgba(96,165,250,.08));color:#20222a;font-family:&#39;Noto Sans SC&#39;,&#39;PingFang SC&#39;,&#39;Microsoft YaHei&#39;,&#39;Heiti SC&#39;,Arial,sans-serif;font-weight:900;text-align:left;"><section><span leaf="">环节</span></section></th><th style="padding:12px 10px;border-bottom:1px solid rgba(217,70,239,.10);background:linear-gradient(90deg,rgba(255,82,200,.10),rgba(217,70,239,.08),rgba(96,165,250,.08));color:#20222a;font-family:&#39;Noto Sans SC&#39;,&#39;PingFang SC&#39;,&#39;Microsoft YaHei&#39;,&#39;Heiti SC&#39;,Arial,sans-serif;font-weight:900;text-align:left;"><section><span leaf="">人工逐项衔接</span></section></th><th style="padding:12px 10px;border-bottom:1px solid rgba(217,70,239,.10);background:linear-gradient(90deg,rgba(255,82,200,.10),rgba(217,70,239,.08),rgba(96,165,250,.08));color:#20222a;font-family:&#39;Noto Sans SC&#39;,&#39;PingFang SC&#39;,&#39;Microsoft YaHei&#39;,&#39;Heiti SC&#39;,Arial,sans-serif;font-weight:900;text-align:left;"><section><span leaf="">Operator 连续承接</span></section></th></tr></thead><tbody><tr><td style="padding:11px 10px;border-bottom:1px solid rgba(217,70,239,.08);color:#454b57;font-family:&#39;Noto Sans SC&#39;,&#39;PingFang SC&#39;,&#39;Microsoft YaHei&#39;,&#39;Heiti SC&#39;,Arial,sans-serif;line-height:1.78;"><section><span leaf="">准备环境</span></section></td><td style="padding:11px 10px;border-bottom:1px solid rgba(217,70,239,.08);color:#454b57;font-family:&#39;Noto Sans SC&#39;,&#39;PingFang SC&#39;,&#39;Microsoft YaHei&#39;,&#39;Heiti SC&#39;,Arial,sans-serif;line-height:1.78;"><section><span leaf="">下载、配置、排查启动问题</span></section></td><td style="padding:11px 10px;border-bottom:1px solid rgba(217,70,239,.08);color:#454b57;font-family:&#39;Noto Sans SC&#39;,&#39;PingFang SC&#39;,&#39;Microsoft YaHei&#39;,&#39;Heiti SC&#39;,Arial,sans-serif;line-height:1.78;"><section><span leaf="">Agent 推进准备，人检查运行条件</span></section></td></tr><tr><td style="padding:11px 10px;border-bottom:1px solid rgba(217,70,239,.08);color:#454b57;font-family:&#39;Noto Sans SC&#39;,&#39;PingFang SC&#39;,&#39;Microsoft YaHei&#39;,&#39;Heiti SC&#39;,Arial,sans-serif;line-height:1.78;"><section><span leaf="">对齐材料</span></section></td><td style="padding:11px 10px;border-bottom:1px solid rgba(217,70,239,.08);color:#454b57;font-family:&#39;Noto Sans SC&#39;,&#39;PingFang SC&#39;,&#39;Microsoft YaHei&#39;,&#39;Heiti SC&#39;,Arial,sans-serif;line-height:1.78;"><section><span leaf="">在资料、代码与工具间搬运背景</span></section></td><td style="padding:11px 10px;border-bottom:1px solid rgba(217,70,239,.08);color:#454b57;font-family:&#39;Noto Sans SC&#39;,&#39;PingFang SC&#39;,&#39;Microsoft YaHei&#39;,&#39;Heiti SC&#39;,Arial,sans-serif;line-height:1.78;"><section><span leaf="">围绕当前项目继续检索与验证</span></section></td></tr><tr><td style="padding:11px 10px;border-bottom:1px solid rgba(217,70,239,.08);color:#454b57;font-family:&#39;Noto Sans SC&#39;,&#39;PingFang SC&#39;,&#39;Microsoft YaHei&#39;,&#39;Heiti SC&#39;,Arial,sans-serif;line-height:1.78;"><section><span leaf="">留下记录</span></section></td><td style="padding:11px 10px;border-bottom:1px solid rgba(217,70,239,.08);color:#454b57;font-family:&#39;Noto Sans SC&#39;,&#39;PingFang SC&#39;,&#39;Microsoft YaHei&#39;,&#39;Heiti SC&#39;,Arial,sans-serif;line-height:1.78;"><section><span leaf="">实验结束后找回请求与响应</span></section></td><td style="padding:11px 10px;border-bottom:1px solid rgba(217,70,239,.08);color:#454b57;font-family:&#39;Noto Sans SC&#39;,&#39;PingFang SC&#39;,&#39;Microsoft YaHei&#39;,&#39;Heiti SC&#39;,Arial,sans-serif;line-height:1.78;"><section><span leaf="">执行时保留证据入口，供人复核</span></section></td></tr></tbody></table>  
工程能力决定环境和验证质量。把执行交给 Agent，工程师仍要检查关键条件与结果；变化是不用每次都亲手完成重复安装、搜索和复制。  
> 让上一步的结果直接进入下一步，才能减少重复劳动。  
  
  
环境搭好后继续验证，资料查到后回到代码，请求发出后留下响应，工作不再断在工具之间。  
# 二、三句自然语言，让 Operator 连续往下做  
  
这次，我在本地用 **Oxo Operator**  
 复现若依的传统漏洞，三个主要目标可以概括为：  
1. 下载若依，并搭建本地运行环境。  
  
1. 查询当前版本有哪些公开的历史漏洞。  
  
1. 在这个本地环境中复现 SQL 注入。  
  
这是指令的语义概括，并非逐字记录。**从本地搭建到 SQL 注入复现，这次过程约十分钟。**  
 这是单次使用体验；开篇的十小时是假设，不能据此计算效率倍数。  
  
三句话给出目标，Operator 在工作环境里承接过程：  
  
**先准备环境，再带着项目查资料。**  
 下载与搭建完成后，当前代码成为后续工作的起点。截图中，Agent 识别本地版本，整理公开漏洞线索，并附上来源与源码位置。工程师可以从材料继续检查候选问题。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Y05UtykogHQTXxVkMVNdiaImxdGqic0Rricb64LPdRRFN2L0SVeEbtOjvao3MfPxKeMC6O5oOV0eWFCzbTOL6h1FzJjSfMo8iaD5UooZc2ibQfvo/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Y05UtykogHQ6xShmZJD3lkFfyrTBJTLWicVdEWhbolz5Hyia4iaRX0ibPn65f5fCmE4giafiaf0fniaWr3BgiaUQrJZZFibrI5JgMJw9PliaWkmsX5h4w/640?wx_fmt=png&from=appmsg "")  
  
图 1：识别项目版本、检索公开资料，并把线索接到源码位置。  
  
**接着验证请求，同时留下证据。**  
 第二张截图里，Request 面板显示本地请求与 SQL 报错响应，旁边是源码解释，以及 Request／Traffic 的证据入口。工程师可以打开实际材料，检查解释是否支持结论。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Y05UtykogHR4yNk5y0XdmNzkrwvDh8Dpsiak6EpAfhTyXMDrGqDHvfAeHcNSXiayGvwwh9TzU5tTtATda5h0ZzgmDsMGaF4S1LUCtpRpc8rCw/640?wx_fmt=png&from=appmsg "")  
  
图 2：请求、响应、源码解释与证据链接处在同一条工作链中。  
  
若依是这次实验的对象，更值得关注的是执行方式：准备、检索、代码定位、请求验证和留证连了起来。工程师不用为每一步重新整理背景，也不必等实验结束才补记录。  
# 三、把时间放回问题定义、验证与判断  
  
当 Agent 能连续执行，工程师可以先问：**我要证明什么？什么证据才算回答了这个问题？**  
  
明确验证对象、范围与验收结果，让 Operator 在指定项目里验证候选问题，留下请求、响应和源码依据。然后，把精力投入三件事：  
- **定义问题。**  
 选择值得验证的线索，明确测试条件与目标。  
  
- **设计验证。**  
 核对环境与代码，安排必要的对照，发现遗漏的条件与其他解释。  
  
- **判断结果。**  
 对照真实请求、完整响应和源码，分清已经证明的结果与还需调查的问题。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Y05UtykogHQaT1v7MJJNKY6KSWaKHtuhQOINfp6eC7ibUAYTFW7G43d9mPXPPtBMCAib4cvwxric2l6RDibibGm7XB2MDTNurjQv6Da6VXHjV2Ck/640?wx_fmt=png&from=appmsg "")  
  
验收也落在具体材料上：环境是否可用，线索有没有来源，关键请求与响应能否打开，解释能否对应源码，下一位同事能否从记录继续。  
  
这次“三句话、约十分钟”的体验，让我看到了这种分工的实际价值。Agent 接住连续的执行与衔接，工程师掌握目标与结论，省下的注意力可以用于更深入的分析。  
  
**Oxo Operator 的价值，是让安全工程师少耗在重复事务上，多投入真正的安全问题。**  
 准备与执行可以交给 Agent，问题定义、验证设计与结果判断，值得人把时间重新投进去。  
  
了解 Oxo Operator  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/RBozUQPW9c86l9BKV2TcgrjKw8B41ge3ibibq5qqLoNW0aJYvEfAAibSfRgU74vleMaXJ2chff1d7sk5B7xHcI6iaA/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Y05UtykogHQXMdiaQpLna896ykOibEbyB6o9IkPQYOnrO0CTpLkLrGpjyaKFWgTrSJnM5hZMcng2AzCQYUfdeGlOUKy89BINjb95M06jiclKRg/640?wx_fmt=png&from=appmsg "")  
  
  

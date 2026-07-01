#  基于 Claude Code 构建可控，持续漏洞挖掘  
原创 whoami4936
                    whoami4936  SecurityPaper   2026-07-01 07:45  
  
# 挖挖挖！挖漏洞  
## 为什么直接让 AI 挖洞会翻车  
  
半年前我让 AI 直接测一个目标。它跑完报了 12 个漏洞，我逐个验证，11 个假的。  
  
根因不是模型笨，是  
**渗透测试的要求和 AI 的天性正好相反**  
。AI 擅长发散联想，渗透测试要的是收敛克制。具体表现为五个确定性的失效模式：  
  
**scope 漂移**  
——AI 看到"有意思"的域名就去戳，忽略授权边界。  
**扫描器结果轻信**  
——AI 直接采信 nuclei 输出，不做独立验证，误报率 60% 起。  
**状态遗忘**  
——上下文压缩后丢失已测记录，重复测或漏测。  
**判定不一致**  
——同一个 CORS   
*  
 响应，不同时间 AI 给不同结论。  
**经验不沉淀**  
——每次从零开始，不积累有效策略。  
  
这五个问题不是调 prompt 能解决的。我基于 Claude Code 的原生扩展机制搭了套系统解决它们，在几个 SRC 实测下来误报可控。下面讲架构。  
## Claude Code 的八类扩展机制  
  
Claude Code 不只是聊天框。  
~/.claude/  
 目录下有八类扩展点，每类有明确职责边界：  
```
```  
  
核心设计原则是  
**职责分层**  
：prompt 管 SOP（怎么测），script 管判断（判什么），hook 管边界（能不能跑）。能代码化的绝不写进 prompt——prompt 越长 AI 越不可控。  
  
下面讲三个最关键的设计。  
## 设计一：Skills 让 AI 自动选工具  
  
渗透测试一堆工具。传统做法是写脚本串起来。Claude Code 的 Skills 机制更聪明——你给每个工具写份说明书，AI 自己判断该用哪个。  
  
机制在 SKILL.md 的 description 字段。Claude Code 读所有 skill 的 description，按当前任务语义自动路由：  
```
```  
  
你说"查这个公司有哪些资产"，AI 自动调 fofa，不用你指定工具名。  
  
更关键的是  
**工具领地设计**  
——每个 skill 显式声明"何时不用我"，定义工具间边界：  
```
```  
  
AI 读到这些边界就不会越权。我写了十几个这样的 skill，覆盖从资产发现到漏洞扫描的完整链路。  
  
Skills 的真正价值不只是调公共工具——你可以把自己的私有 POC、定制字典、检测规则都沉淀成 skill。AI 会按需自动调用它们，等于把个人积累的经验变成了一套会自动运转的工具库。具体沉淀了什么内容这里不展开，但这是整个系统里最值得长期投入的部分。  
## 设计二：三道质量门串行，数据单向流动  
  
这是系统可靠性的核心。整个流水线 16 个阶段，其中 7 个是 blocking 硬关卡。但流水线本身不稀奇——  
**真正可靠的是三道质量门。**  
  
数据流单向，不可跳过：  
```
```  
### 验证门：不复现不入库  
  
所有扫描器输出（nuclei / fuzzer / 手工）只算"候选"，不算漏洞。候选必须经  
**独立复现**  
才能入库。  
  
"独立"是关键。验证 agent 新开干净上下文，自己发请求，不照抄扫描器结论。按漏洞类设精确门槛，不一刀切：  
  
<table><thead><tr style="box-sizing: border-box;break-inside: avoid;break-after: auto;border: 1px solid rgb(223, 226, 229);margin: 0px;padding: 0px;"><th style="box-sizing: border-box;padding: 6px 13px;font-weight: bold;border-width: 1px 1px 0px;border-top-style: solid;border-right-style: solid;border-left-style: solid;border-top-color: rgb(223, 226, 229);border-right-color: rgb(223, 226, 229);border-left-color: rgb(223, 226, 229);border-image: initial;border-bottom-style: initial;border-bottom-color: initial;margin: 0px;"><span cid="n33" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 107.219px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">漏洞类</span></span></span></th><th style="box-sizing: border-box;padding: 6px 13px;font-weight: bold;border-width: 1px 1px 0px;border-top-style: solid;border-right-style: solid;border-left-style: solid;border-top-color: rgb(223, 226, 229);border-right-color: rgb(223, 226, 229);border-left-color: rgb(223, 226, 229);border-image: initial;border-bottom-style: initial;border-bottom-color: initial;margin: 0px;"><span cid="n34" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 802.781px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">CONFIRMED 条件</span></span></span></th></tr></thead><tbody><tr style="box-sizing: border-box;break-inside: avoid;break-after: auto;border: 1px solid rgb(223, 226, 229);margin: 0px;padding: 0px;"><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n36" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 107.219px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">CORS</span></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n37" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 802.781px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">ACAO 回显 + ACAC=true + 敏感数据 + 跨域可读，四者全满足</span></span></span></td></tr><tr style="box-sizing: border-box;break-inside: avoid;break-after: auto;border: 1px solid rgb(223, 226, 229);margin: 0px;padding: 0px;background-color: rgb(248, 248, 248);"><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n39" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 107.219px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">SQLi</span></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n40" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 802.781px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">布尔差异：</span></span><span md-inline="code" spellcheck="false" style="box-sizing: border-box;"><code style="box-sizing: border-box;font-family: var(--monospace);text-align: left;vertical-align: initial;border: 1px solid rgb(231, 234, 237);background-color: rgb(243, 244, 244);border-radius: 3px;padding: 0px 2px;font-size: 0.9em;"><span leaf="">&#39; OR 1=1--</span></code></span><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""> vs </span></span><span md-inline="code" spellcheck="false" style="box-sizing: border-box;"><code style="box-sizing: border-box;font-family: var(--monospace);text-align: left;vertical-align: initial;border: 1px solid rgb(231, 234, 237);background-color: rgb(243, 244, 244);border-radius: 3px;padding: 0px 2px;font-size: 0.9em;"><span leaf="">&#39; OR 1=2--</span></code></span><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""> 响应长度真有差</span></span></span></td></tr><tr style="box-sizing: border-box;break-inside: avoid;break-after: auto;border: 1px solid rgb(223, 226, 229);margin: 0px;padding: 0px;"><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n42" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 107.219px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">IDOR</span></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n43" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 802.781px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">对象 ID 替换 + 双身份响应内容差异</span></span></span></td></tr><tr style="box-sizing: border-box;break-inside: avoid;break-after: auto;border: 1px solid rgb(223, 226, 229);margin: 0px;padding: 0px;background-color: rgb(248, 248, 248);"><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n45" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 107.219px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">SSRF</span></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n46" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 802.781px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">OOB 回连可重复</span></span></span></td></tr><tr style="box-sizing: border-box;break-inside: avoid;break-after: auto;border: 1px solid rgb(223, 226, 229);margin: 0px;padding: 0px;"><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n48" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 107.219px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">RCE</span></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n49" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 802.781px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">命令回显（id/whoami）或 OOB，不植入 webshell</span></span></span></td></tr></tbody></table>### 策略归一门：代码审稿防误报升级  
  
即使复现了，结论还要过   
verdict_policy.py  
 的确定性归一。这层专门处理 AI 容易夸大的高噪声漏洞。  
  
核心逻辑：按漏洞类检查 impact evidence，只允许降级不允许升级。以 Clickjacking 为例：  
```
```  
  
**这层是代码不是 prompt，所以每次给一样的判断。**  
 AI 说"这是 Clickjacking 漏洞"，代码查这是公共首页还是敏感操作，代码拍板。  
### 事实固化门：schema 校验 + 单向渲染  
  
归一后由   
finding_writer.py  
 固化。这层做三件事：  
  
**schema 运行时校验**  
——读   
finding_schema.json  
 的 enum 约束，校验每条记录。不合规的 verdict 降级 UNCONFIRMED、severity 降级 unknown，  
--strict  
 模式直接拒绝入库。校验逻辑纯标准库实现（不依赖 jsonschema 第三方包），enum 从 schema 文件读取，改 schema 即生效。  
  
**全量写入 finding_records.jsonl**  
——这是唯一事实源。  
  
**单向渲染**  
——报告 agent 只能读 JSON 渲染，不能改 verdict/severity。状态裁决优先级写死在 report-agent 的 prompt 里：  
finding_records.jsonl > findings/*.md  
。防止报告层为"好看"而篡改事实。  
## 设计三：Scope 硬约束——hook 代码强制，不靠 prompt  
  
渗透测试第一红线是 scope。AI 天性是"看到有意思的域名就去测"，可能打出授权外的真实生产系统。  
  
Claude Code 的 Hooks 机制给了解法。在   
settings.json  
 注册 PreToolUse hook，AI 每次执行命令前先过拦截器：  
```
```  
  
hook 通过 stdin 收工具调用事件，通过 exit code 控制：  
exit 0  
 放行，  
exit 2  
 阻断。  
  
hunter-guard.ps1  
 从命令里提取目标候选（6 种来源：URL 正则 /   
-u  
 参数 / Host header / 列表文件 / 子域命令 / MCP 工具字段），和   
scope.txt  
 比对。匹配规则：  
- 根域   
example.com  
 → 覆盖自身 + 所有子域  
  
- 子域   
api.example.com  
 → 覆盖自身 + 其子域，不覆盖兄弟域  
  
- IP 精确匹配，CIDR 字节级掩码计算  
  
**越界直接 exit 2，AI 根本执行不了那条命令。**  
 这是"prompt 软约束"和"hook 硬约束"的根本区别——prompt 是请求（AI 可能不听），hook 是强制（绕不过）。  
  
fail 策略的设计取舍：解析层 fail-open（hook bug 不该卡死所有命令），scope 层 fail-closed（宁可误拦不可放行越界）。  
## 设计四：双层学习闭环  
  
AI 天然缺陷是"下次从零开始"。系统用两层闭环解决：  
  
**短期闭环（单次 engagement 内）**  
——  
strategy_feedback.py  
 把验证结果反馈成信号权重：  
```
```  
  
权重硬限 [-30, +40] 防止单信号过度支配。下一轮   
signal_fusion.py  
 读权重加到候选 score 上。  
  
**长期闭环（跨 engagement）**  
——  
reflect-agent  
 把 confirmed 漏洞蒸馏成 pattern：  
```
```  
  
追加到全局经验库   
patterns.jsonl  
。下次新 engagement 的 recon 阶段查这个库——"这种指纹历史上出过什么洞"——直接定向测。第三次用时体感明显不同于第一次，  
**不是模型变聪明了，是经验库在积累。**  
  
payload 脱敏（  
<param>  
/  
<target>  
 占位），不写真实凭据——经验库不变成新的泄露点。  
## Agent 权限分层  
  
每个 typed agent 的   
tools  
 字段按职责精确授权，不是统一给   
["*"]  
：  
  
<table><thead><tr style="box-sizing: border-box;break-inside: avoid;break-after: auto;border: 1px solid rgb(223, 226, 229);margin: 0px;padding: 0px;"><th style="box-sizing: border-box;padding: 6px 13px;font-weight: bold;border-width: 1px 1px 0px;border-top-style: solid;border-right-style: solid;border-left-style: solid;border-top-color: rgb(223, 226, 229);border-right-color: rgb(223, 226, 229);border-left-color: rgb(223, 226, 229);border-image: initial;border-bottom-style: initial;border-bottom-color: initial;margin: 0px;"><span cid="n91" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 286.292px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">Agent</span></span></span></th><th style="box-sizing: border-box;padding: 6px 13px;font-weight: bold;border-width: 1px 1px 0px;border-top-style: solid;border-right-style: solid;border-left-style: solid;border-top-color: rgb(223, 226, 229);border-right-color: rgb(223, 226, 229);border-left-color: rgb(223, 226, 229);border-image: initial;border-bottom-style: initial;border-bottom-color: initial;margin: 0px;"><span cid="n92" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 329.167px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">tools</span></span></span></th><th style="box-sizing: border-box;padding: 6px 13px;font-weight: bold;border-width: 1px 1px 0px;border-top-style: solid;border-right-style: solid;border-left-style: solid;border-top-color: rgb(223, 226, 229);border-right-color: rgb(223, 226, 229);border-left-color: rgb(223, 226, 229);border-image: initial;border-bottom-style: initial;border-bottom-color: initial;margin: 0px;"><span cid="n93" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 267.875px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">理由</span></span></span></th></tr></thead><tbody><tr style="box-sizing: border-box;break-inside: avoid;break-after: auto;border: 1px solid rgb(223, 226, 229);margin: 0px;padding: 0px;"><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n95" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 286.292px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">recon/vuln-scan/verify/exploit</span></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n96" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 329.167px;min-height: 10px;"><span md-inline="code" spellcheck="false" style="box-sizing: border-box;"><code style="box-sizing: border-box;font-family: var(--monospace);text-align: left;vertical-align: initial;border: 1px solid rgb(231, 234, 237);background-color: rgb(243, 244, 244);border-radius: 3px;padding: 0px 2px;font-size: 0.9em;"><span leaf="">Bash, Read, Write, Glob, Grep</span></code></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n97" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 267.875px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">需 shell 跑工具，但不需 Edit</span></span></span></td></tr><tr style="box-sizing: border-box;break-inside: avoid;break-after: auto;border: 1px solid rgb(223, 226, 229);margin: 0px;padding: 0px;background-color: rgb(248, 248, 248);"><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n99" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 286.292px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">report/reflect</span></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n100" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 329.167px;min-height: 10px;"><span md-inline="code" spellcheck="false" style="box-sizing: border-box;"><code style="box-sizing: border-box;font-family: var(--monospace);text-align: left;vertical-align: initial;border: 1px solid rgb(231, 234, 237);background-color: rgb(243, 244, 244);border-radius: 3px;padding: 0px 2px;font-size: 0.9em;"><span leaf="">Read, Write, Glob, Grep</span></code></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n101" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 267.875px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">只读数据，物理上无法碰目标</span></span></span></td></tr><tr style="box-sizing: border-box;break-inside: avoid;break-after: auto;border: 1px solid rgb(223, 226, 229);margin: 0px;padding: 0px;"><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n103" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 286.292px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">coordinator</span></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n104" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 329.167px;min-height: 10px;"><span md-inline="code" spellcheck="false" style="box-sizing: border-box;"><code style="box-sizing: border-box;font-family: var(--monospace);text-align: left;vertical-align: initial;border: 1px solid rgb(231, 234, 237);background-color: rgb(243, 244, 244);border-radius: 3px;padding: 0px 2px;font-size: 0.9em;"><span leaf="">[&#34;*&#34;]</span></code></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n105" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 267.875px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">调度大脑，需委派所有阶段</span></span></span></td></tr></tbody></table>  
执行类给 Bash 但不给 Edit（它们写 jsonl 新文件，不精编代码）；report/reflect 没有 Bash（它们不碰目标，物理上无法闯祸）。  
**最小权限不是靠 prompt 自觉，是靠 tools 字段强制。**  
## 真实的设计权衡  
  
**为什么用文件系统做状态存储，不用数据库**  
——AI 上下文会被压缩丢失，子 agent 间不天然共享记忆。文件系统提供可恢复（中断后 hunter_runner 检查文件契约从断点继续）、可审计（.audit/ 记录所有命令）、跨 agent 共享（读同一批 .hunt/ 文件）。  
## 小结  
  
整套系统的核心思路一句话：  
**把 AI 的不确定性约束在有边界的，可持续运行的工程系统里。**  
  
具体落地：prompt 管 SOP（方法论），script 管判断（确定性归一），hook 管边界（scope 硬强制），schema 管数据（枚举校验），skill 管工具（自动路由），agent 管执行（权限分层）。三道质量门串行保证输出可靠，双层闭环让系统越用越准。  
  
AI 可以发现、可以判断，但  
**最终拍板的不是它，是代码。**  
  

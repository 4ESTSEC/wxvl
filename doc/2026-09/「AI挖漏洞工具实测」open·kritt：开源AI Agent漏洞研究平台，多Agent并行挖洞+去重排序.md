#  「AI挖漏洞工具实测」open·kritt：开源AI Agent漏洞研究平台，多Agent并行挖洞+去重排序  
原创 句芒安全实验室
                    句芒安全实验室  句芒安全实验室   2026-09-22 09:36  
  
昨天句芒讲的是「怎么查你机器上 Agent 装了什么」——Snyk Agent Scan 扫 MCP 服务器和 Skill 的风险。今天换个方向：让 AI Agent 去挖漏洞。  
  
这里有个很多人踩过的坑：把整个仓库丢给模型，说一句「帮我找漏洞」。open·kritt 的团队在 README 第一段就把这话说透了——**把模型指向整个仓库、让它自己找漏洞，很少有好结果**  
。原因不难理解：一个仓库几万行代码，模型能真正看住的上下文有限，你让它在里面自己挑重点，它挑的往往是显眼的代码模式，而不是真能被外部触发的路径。  
  
今天句芒深推的就是这个工具：**open·kritt**  
（仓库 Kritt-ai/open-kritt  
）。  
## 先核身份  
  
按老规矩，发布前用 GitHub API 当天核实：仓库 Kritt-ai/open-kritt  
，**2162 颗星、371 个 fork**  
，JavaScript 实现，**AGPL-3.0**  
 协议。2026-07-20 建仓，最近一次推送是 **2026-09-21**  
——昨天还在改。最新 release 是 **v1.4.1**  
（2026-08-16），仓库里的 VERSION  
 文件也是 1.4.1。  
  
它的来历值得说一句：维护者是 Harel Rom（@harel-coffee  
）和 Gabriel Balko（@GabiCtrlZ  
），团队以研究者名 **Blockian**  
 在 Immunefi、HackenProof 上累计拿到**超过 150 万美元**  
漏洞赏金，open·kritt 是那套内部研究工具的开源蒸馏版。也就是说，它不是先写个工具再找场景，而是先有战绩、再把流程做成工具。官网 kritt.ai，文档 docs.kritt.ai。  
## 核心判断：别把整仓库丢给模型  
  
open·kritt 的做法正好相反：**把研究拆成小的、定义清晰的任务，多个 agent 并行跑，再把输出合并成可验证、可排序的 finding**  
。它由三个部件拼起来，理解了这三个你就理解了它整个设计。  
  
**workflow（工作流）**是一棵 prompt 步骤树，按 depth 分层。depth 0 是入口，只跑一次，通常用来枚举候选点——比如「列出每一个 HTTP 路由」——并标成 multi-output，可以吐多条结果；更深的一层跑在上层的结果之上，直接引用上层输出的变量（{{entrypoint}}  
 这种写法）。最深的终端步骤是特殊的：**它必须吐出固定的 finding schema**  
。上游负责探索、下游负责收敛，这就是它省上下文的办法——入口和路径只映射一次，最后一个 agent 把整个上下文花在一条具体路径上。  
  
**post-script（后置脚本）**在 workflow 产出 finding、去重和排序之后，**每条 finding 跑一次**  
，用来给 finding 打分、打标签、写报告、或者尝试构造 PoC。它可以吐 _reserved_report  
（完整 Markdown 报告）和 _reserved_poc  
（可复现的 PoC 步骤），其它自定义字段会变成 finding 上的列。注意它只能引用编辑器里给出的保留上下文键和 finding 键，**读不到其它步骤的输出**  
。  
  
**ranker（严重度排序器）**就是一份纯 Markdown 规则，没有 schema，你写几条排序原则，模型按它排优先级。同一份 finding，加上你的规则之后排出来的顺序可以完全不一样——这是它处理「上千条 critical 里哪条该先看」的办法。  
  
![open·kritt 的 workflow 编辑器：步骤按 depth 分组，prompt 实时校验（取自仓库 docs-site/images/workflow-builder.png）](https://mmbiz.qpic.cn/mmbiz_jpg/J2hBCjr4Lft76wzcmzdHAICFwMiayCdNHfYFfd4IBQyKqxR9l4oNlwbouNBHlspRa2VjBmaHyoLeLm6gNjwHVMsfcCqRsKZHI6Z5Em8WDtS4/640?wx_fmt=jpeg "open·kritt 的 workflow 编辑器：步骤按 depth 分组，prompt 实时校验（取自仓库 docs-site/images/workflow-builder.png）")  
## finding schema：它要的不是「可能有洞」  
  
每条 finding 的字段是固定的：summary  
、explanation  
、vulnerability_type  
、file_path  
、line  
、trigger_flow  
、malicious_input_example  
、malicious_actor  
，还有一个可选的 exploitable  
。  
  
值得注意其中两个：trigger_flow  
 是攻击路径，malicious_input_example  
 是恶意输入示例。这两个字段存在本身就说明它的要求不是「这里看着危险」，而是「**谁能从哪打进来**  
」。这个固定 schema 也是上面三样东西能拼起来的前提——正因为每条 finding 形状一致，post-script 才能逐条跑、ranker 才能排序、不同扫描的结果才能互相对比。  
  
它自带两个 workflow：external-flow-analysis  
 沿着外部可控输入从生产入口一路跟到具体的安全敏感行为——先枚举外部可达的入口和处理攻击者输入的处理函数，再对每个入口列出真正不同的生产路径（校验结果、授权边界、状态变更、外部调用、敏感 sink），最后给每个下游 agent 只发一条具体路径去验证，只回具体的漏洞加有支撑的攻击路径，或者回一个 no-finding 占位。另一个 Cosmos ABCI Panic Halt Review  
 针对 Go 写的 Cosmos 应用：先证明哪些 ABCI 方法和阶段处理函数真的接进了生产应用，再按显式 panic、算术 panic、空指针 panic、越界与类型 panic 四类扇出。  
  
harness 支持 codex  
、claude-code  
、grok-build  
；模型可以走 Codex 登录，或者接 OpenAI、Anthropic、DeepSeek、OpenRouter、xAI。可以「所有 depth 用一个模型」，也可以**按 depth 分开配**  
——比如枚举用快的、深挖用强的。后处理（去重、排序、富化）默认跟着扫描用的模型，也能单独换。  
## 上手：Docker Compose 拉起来  
  
前置是 Docker Compose 2.13.0+、Git、Node.js 20+（仓库自带的 CLI 不需要 npm install）。三步：  
```
git clone https://github.com/Kritt-ai/open-krittcd open-kritt./kritt setup./kritt start
```  
  
起来之后打开 http://localhost:5173  
。模型访问只需要配一种，./kritt setup  
 会带你走。GITHUB_TOKEN  
 是可选的，只在扫私有仓库时需要。没有桌面环境就用 ./kritt-headless  
：它能导入 workflow / post-script / skill / ranker 的 JSON，建扫描（后端校验逻辑和网页表单一致），看状态、阶段和失败原因，改非密钥的运行时设置，导出 finding 包——但它**不在终端里显示 finding 内容**  
。  
  
![新建扫描表单：选 workflow、选目标、配模型和 harness（取自仓库 docs-site/images/create-scan.png）](https://mmbiz.qpic.cn/sz_mmbiz_jpg/J2hBCjr4LftHHcIfiaXEicPnw4qnQJblvPzZ3cLKLByMNE9b8RU9t6128ZibmdNq1TXCLSicFLhKv0VwlYCicE2oNM9mvx12l3DiaJ7vD0uwicUKbs/640?wx_fmt=jpeg "新建扫描表单：选 workflow、选目标、配模型和 harness（取自仓库 docs-site/images/create-scan.png）")  
  
安装文档里给了一份实测矩阵：Ubuntu 24.04、Debian 12、Rocky Linux 9，x86_64 和 ARM64 都跑过完整源码构建、五个常驻服务加一个一次性 runner 镜像助手、以及扫描启动。ARM64 不需要 amd64 模拟。  
## 避坑：这几条不看清会出事  
  
**第一，你扫的代码本身就是不可信输入。**  
 官方威胁模型文档开头就写了：engine 分析的代码是 **potentially untrusted**  
，仓库里完全可以埋专门针对 agent 的内容（prompt injection），诱导它把密钥外发或者干别的事。它自己给的缓解是：每个带工具的 job 丢进一次性容器，单独的可写 checkout、复制出来的 job home、专用的 Docker 网络；job 不挂 Docker socket、不挂数据库、不挂项目的 .env  
、也碰不到别的 job；harness 的输出被约束成 JSON schema。另外自然语言的 workflow 和 post-script 生成请求同样是不可信输入——那条路径跑生成时会关掉模型工具、用户规则和设置、以及会话持久化，后端在草稿进编辑器之前还会再校验一遍。  
  
**第二，扫描 agent 在 job 容器里是 root。**  
 官方原话：scan agent 在 job 容器里以 root 跑，可以用 Bash、可以装包、可以编译或测试目标、可以直连外网。同时它明确写了一句：**job 容器不是针对内核或容器运行时漏洞的安全边界**  
。所以官方的建议是——整套 stack 跑在**专用的 VM 或 Docker host**  
 上，不要和别的敏感负载混在一台机器上，扫任何东西都假设它可能是恶意的。  
  
**第三，后端 API 默认没有鉴权。**/api/*  
 默认无 auth，默认端口绑 127.0.0.1  
。谁能调到它，谁就能排扫描、触发 AI 草稿生成、烧你的模型额度。官方的要求很直白：自己在前置反向代理上加认证，或者只留在可信网络里。别为了图省事把前端和 API 端口直接开在公网上。  
  
**第四，数据会外发。**  
 扫描默认把仓库内容发到外部端点——Codex/OpenAI、Anthropic、OpenRouter、xAI。扫敏感代码之前，先确认这个边界你能接受。  
  
**第五，凭证要最小化。**  
 provider key 和 GITHUB_TOKEN  
 放在 .env  
 里（已 gitignore，仓库里有 gitleaks pre-commit 钩子挡提交）。官方建议用**最小权限、短时效**  
的 GITHUB_TOKEN  
（只读，只给要扫的仓库），provider key 定期轮换。另外 ENGINE_CODEX_AUTO_UPDATE  
 默认 false  
，打开就等于多信任一个 npm registry。出网默认是开的，要收紧就在 Docker host、防火墙或者网络策略层做。  
  
**第六，导出包要小心。**  
 导出是一个 ZIP，带一份 share-safe 的 manifest：跑完的扫描导出完整内容，被停掉或者失败的扫描如果已经有 finding，导出会明确标成 partial。这里有个细节官方特意写了——**受攻击者影响的报告和 PoC 源码以纯文本保留**  
。也就是说这份导出本身就带着别人能控制的文本，拿去分享或者丢进别的系统之前先看一眼。  
  
![rankers：一份纯 Markdown 规则，带实时预览（取自仓库 docs-site/images/severity-ranker.png）](https://mmbiz.qpic.cn/mmbiz_jpg/J2hBCjr4LfvDNmzZc6RIfTMaCGsoicOpKWiaicry3v8cp68kGonQ8PwDIovaO6Xx0ZcKiaDKT0BvibVrZYYKFEAtPliaCLdychtwZ14PsxvBJibyng/640?wx_fmt=jpeg "rankers：一份纯 Markdown 规则，带实时预览（取自仓库 docs-site/images/severity-ranker.png）")  
  
顺带说一个版本坑：v1.4.1 修的那个 bug 是「ranker 实际上没有用你给的排序规则」。也就是说如果你之前试过写 ranker 但感觉没生效，那不是你写错了。  
## 适合谁  
- **有授权范围的安全研究者和安全向开发者**  
：想把自己那套「拆任务 → 并行跑 → 去重排序」的流程固化下来，而不是每次重新手搓 prompt。  
  
- **做漏洞赏金的**  
：内置的 external-flow-analysis  
 是他们实战用过的流程，文档里说这个模式帮他们找到过多个拿赏金的漏洞。  
  
- **企业安全团队**  
：自托管加自己的模型额度，代码不出自己的边界，比把仓库交给外部 SaaS 干净。  
  
- **做 AI 安全研究的**  
：它的威胁模型文档本身就是一份「让 agent 跑在不可信代码上」的清单，值得逐条读。  
  
不适合谁：想开箱即用、不想碰 Docker 的人；没有模型访问的人——它只提供流程和 harness，不送额度；想拿它去扫没授权代码的人——它是自托管的工具，不给你授权。  
  
最后提醒一句：这类工具的输出是**线索不是结论**  
。官方的立场也一样——AI 模型是非确定性的，会幻觉出 finding，也会写出错的补丁；所有 finding 都要人复核过再报，别把 agent 跑出来的结果直接批量丢给开源维护者。  
  
仓库和文档自己翻：GitHub 搜 Kritt-ai/open-kritt  
，威胁模型在 docs/threat-model.md  
，上手流程在 docs-site/  
，导出和排序的细节在 docs-site/scan-results/  
 和 docs-site/severity-ranker/  
。  
  

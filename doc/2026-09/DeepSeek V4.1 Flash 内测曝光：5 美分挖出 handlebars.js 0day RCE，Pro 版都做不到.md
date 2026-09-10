#  DeepSeek V4.1 Flash 内测曝光：5 美分挖出 handlebars.js 0day RCE，Pro 版都做不到  
Red Hunter
                    Red Hunter  黑白之道   2026-09-10 00:36  
  
![](https://mmbiz.qpic.cn/mmbiz_png/nGzNudUIJ6NrVPd3QDnhcQ5G0qjab0BIulHHdzbGPGGiapT4KNz25ewaBw4z3o7HIackXkxnarch97ARDJaXWAgnMaxLYZaOial3oN3wag6ics/640?from=appmsg "")  
> **导语**  
：DeepSeek V4.1 Flash 限时内测开启不到 24 小时，两组独立第三方数据在 X 上先后引爆。安全研究团队 Zaddy 用 100 个已知漏洞 PR 盲测，V4.1 Flash 找出 44 个，单漏洞成本 0.29 美元；另一名安全研究者 Nick Mykhailyshyn 更激进——他用 V4.1 Flash 在数分钟内挖出了 handlebars.js v4.7.9 的一个 **0day RCE**  
，总成本仅 0.05 美元。同一段 prompt，V4 Pro（0813）多次重试都没能稳定复现，Flash 却能稳定命中。这不是一次普通的版本灰度，而是一场把漏洞挖掘成本直接打穿地心的算力实验。  
  
## 一、信号提炼：命名、定价、问卷，三件套一起到位  
  
任何关注 LLM 版本管理的人都会注意到，DeepSeek 这次的命名一反常态——不是 v4.1-flash-preview  
，也不是 v4.1-flash-beta  
，而是把 expires-on-0910  
 这串到期日直接挂进模型名。这在工程上有三层含义：  
  
其一，强制 A/B 路径分流。模型名携带到期日，意味着调用方、网关、计费系统、监控告警都能按字符串识别这个临时节点，无需额外标记就能区分"灰度流量"与"正式流量"，便于在到期当天做无感切换或下架。  
  
其二，制造用户决策紧迫感。用户必须在 24–72 小时内判断是否切换、是否替换代码中的模型名，否则到期后就会面临调用失败。这种"硬切换"倒逼企业用户用真实业务流量投票——DeepSeek 拿到的不是问卷态度，而是真金白银的 QPS 切换数据。  
  
其三，定价信号。计费保持与 deepseek-v4-flash  
 一致，每账号限流 20 并发。官方问卷里那句"这个中间版本能否全面替换线上 V4 Pro"才是关键信号——DeepSeek 内部已经把"是否用 Flash 系列直接顶替 Pro 系列"摆上了产品路线图的桌面，正在用真实外部数据做最后一公里的验证。  
## 二、两组独立盲测：开源模型的新天花板  
### 2.1 Zaddy 团队：100 个已知漏洞 PR 盲测  
  
Zaddy 在 X 上公布的实验设计相当扎实：100 个已知存在漏洞的 pull request，每个 PR 给一小时时间让模型识别漏洞来源。核心数据如下：  
- **DeepSeek V4.1 Flash**  
：找出 44 个漏洞，总成本 **12.79 美元**  
，单漏洞成本约 **0.29 美元**  
  
- **Opus 5**  
：找出 48 个漏洞（仅多 4 个），总成本 **448 美元**  
，约为 V4.1 Flash 的 **35 倍**  
  
- **Grok 4.6**  
：找出 54 个漏洞（多 10 个），总成本 **120 美元**  
，约为 V4.1 Flash 的 **9 倍**  
  
翻译一下：**V4.1 Flash 用 Opus 5 的 1/35 成本，达成了 92% 的漏洞检出能力**  
。这不是"接近前沿"，而是把前沿模型在代码安全审计场景下的成本曲线直接压扁。  
### 2.2 Nick Mykhailyshyn：单点 0day RCE 实战  
  
如果说 Zaddy 是"已知漏洞"的横向覆盖测试，Nick 的实验则是"未知漏洞"的纵深穿透。handlebars.js 是 Node.js 生态里最流行的模板引擎之一，长期被各种 SaaS、表单引擎、低代码平台深度依赖。Nick 用 V4.1 Flash 喂入 handlebars.js v4.7.9 的代码与历史 CVE 上下文，模型在**数分钟内**  
给出一条稳定的远程代码执行路径，总成本 **0.05 美元**  
。  
  
Nick 同步放出的 DeepSeek API 后台截图是这次传播中最硬的证据：30 天窗口里，9 月 9 日当天累计 **52 次 API 请求、2,230,943 tokens、总成本 $0.05 USD**  
，把所有"5 美分挖 0day"的传说直接钉死在账单上。  
  
![DeepSeek API 用量截图：$0.05 USD / 52 请求 / 2,230,943 tokens](https://mmbiz.qpic.cn/sz_mmbiz_jpg/nGzNudUIJ6NGEpl83MTh02QqlBzOJYF0wYsBDHeoh3UOMbGoDrRU2GiadZBMKJ61qwF8dWXmAMsW3ZcP7T9sZPr1H8eM1YwlMK8wF6DdSxnQ/640?from=appmsg "DeepSeek API 用量截图：$0.05 USD / 52 请求 / 2,230,943 tokens")  
图源：@whoareme33 / X  
  
更关键的是同一条 prompt 下 V4 Pro（0813）的表现：**多次重试都无法稳定命中**  
。Nick 在 Burp Suite 里复现的截图直接展示了 RCE payload 命中后的服务器响应——/api/render  
 端点接收 JSON，handlebars 引擎解析模板时执行注入的 require('child_process').execSync('id')  
，返回 uid=0(root) gid=0(root) groups=0(root)  
。这是教科书级的服务端模板注入致 RCE。  
  
![Burp Suite 抓包：handlebars.js RCE 命中，服务器返回 uid=0(root)](https://mmbiz.qpic.cn/mmbiz_jpg/nGzNudUIJ6O9Ab1dAI56sODnBOKNLcbvxr7icCTAYra02T7chHy8A7d4iaRInoyMic0JzibPnN034cnvLd9YicPNO5npvBfOxov4icLIy8mlFYHdM/640?from=appmsg "Burp Suite 抓包：handlebars.js RCE 命中，服务器返回 uid=0(root)")  
图源：@whoareme33 / X  
  
翻译成产业语言：**闭源旗舰 Pro 在 0day 发现的"稳定性"维度上反而比开源 Flash 弱**  
。这与 Zaddy 横向测试里"Opus 5 多找 4 个但贵 35 倍"的数据形成了一个清晰的产业判断——**前沿模型的能力溢价，正在被小尺寸推理架构抹平**  
。X 上"security researchers, we're so cooked :)"的那句调侃，背后是同一批英文安全研究社区的共识：DeepSeek 这一波不是常规版本迭代，而是在**重写"AI 漏洞研究的成本经济学"**  
。  
## 三、技术与商业的双重变量：Flash 倒逼 Pro 的内部博弈  
  
从内测反馈看，V4.1 Flash 的几个指标同时在改善：推理速度普遍 300–400+ token/s、最高约 507 token/s，比 V4 Flash 提升 2–3 倍以上；reasoning token 显著减少，同等任务总 token 消耗下降；原生多模态宣称支持；Agent 与 Coding 任务反馈"几秒搞定，结构稳定"。再加上 Zaddy 与 Nick 两组独立硬数据，技术维度已经不是"小、快、便宜"的传统 Flash 定位，而是直接逼近乃至局部超越 Pro 区间。  
  
这把 Flash 与 Pro 的产品矩阵撕开了裂缝。Flash 本来定位是"小、快、便宜"，Pro 是"大、稳、贵"。当 Flash 的速度、质量、价格综合起来逼近 Pro 时，Pro 的商业护城河就被自己人瓦解了。换言之，这是一次**内部成本曲线对内部产品矩阵的反向倒逼**  
——DeepSeek 可能在用 V4.1 Flash 的实测数据，反过来决定是否要把 Pro 重新定位为"行业定制"或"私有化部署"这种非 API 战场。  
  
背后可能的技术变量包括：MoE 路由策略优化、注意力机制改造、推理引擎（推测解码、KV Cache 复用、连续批处理）的进一步协同，以及多模态 token 在预训练阶段的早期融合而非后挂式接入。Nick 实验里"Pro 多次重试不稳定、Flash 一次稳定命中"的对比，强烈暗示 V4.1 Flash 在**推理链路的结构化输出稳定性**  
上做了专项优化——这类优化对一般聊天场景不易感知，但对代码审计、漏洞挖掘、Agent 任务执行这类需要长链路 reasoning 的场景，效果是断崖式提升。  
## 四、对中小开发者的冲击：成本红利与不稳定风险并存  
  
对中小开发者与初创团队，这是真金白银的好消息。同样的价格、更快的速度、更省的 token，意味着同样的预算可以支撑更多并发、更多轮次、更多 Agent 循环。更关键的是，Zaddy 与 Nick 的两组数据把"AI 代码审计与漏洞挖掘"这个过去被视为企业级预算项目的能力，直接下放到个人开发者也能负担的工具级别——0.29 美元一个已知漏洞的发现、0.05 美元一个 0day 的命中，比任何传统 SAST/SCA 订阅都便宜，且门槛更低。  
  
但需要警惕的是"限时测试版"的法律与服务稳定性含义。expires-on-0910  
 模型在到期日之后会出现什么行为——是停服、报错、还是默默回落到 V4 Flash——官方没有公开说明。把这套配置直接写入生产环境的企业用户，需要评估到期日切换的应急预案，否则可能在 9 月 10 日当天踩到静默回退的坑。  
  
更现实的问题是数据合规。灰度期间的输入数据是否会被用于后续训练？到期日下线后历史调用记录是否会被保留？DeepSeek 都没有公开承诺。把企业代码审计流量——尤其是 0day 类敏感漏洞的研究上下文——走灰度模型，等同于把核心代码上下文、未公开漏洞情报、内部漏洞验证用例一并送进了未公开数据策略的临时节点。法务与安全负责人需要在这个窗口期内对调用日志、token 留存、模型行为日志做严格隔离。  
## 五、对巨头与同行的挑战：开源 + API 的双重绞杀  
  
Qwen、GLM、Kimi、豆包、文心一言、混元——所有国内一线大模型厂商都面临同一个问题：当 DeepSeek 用 Flash 的价格逼近 Pro 的能力，并把代码安全审计与 0day 漏洞研究的"开源模型天花板"刷到自己手上，整个 API 定价梯度就被强行压平。巨头可以靠生态绑定、政企关系、私有化部署守住收入，但中小客户的纯 API 调用盘子，将出现明显的"用脚投票"。  
  
更值得玩味的是，DeepSeek 一直坚持开源策略。这次内测虽然没有放出权重，但若 V4.1 系列延续开源路径，巨头们面对的就不仅是 API 价格战，而是"开源替代 + API 兜底"的双重绞杀。Zaddy 实验中最关键的对比对象就是**开源模型**  
——V4.1 Flash 在开源阵营里排名第一，这意味着同等价位下，闭源旗舰模型在代码安全场景的唯一相对优势（检出率高 4–10 个）要被 35 倍的成本溢价来辩护，性价比已经极其脆弱。Nick 的实验则更进一步：**在 0day 维度，闭源 Pro 版甚至无法稳定复现**  
——这种"能力溢价被开源 Flash 反超"的叙事，对闭源旗舰的长期定价合法性是结构性打击。下一步，Qwen 与 GLM 极可能用更强的开源旗舰回应，Kimi 与豆包则可能选择深度捆绑行业场景，避开通用 API 战场。  
## 六、对普通用户隐私与供应链的潜在影响  
  
需要直说的是，V4.1 Flash 的"原生多模态"在视觉评测细节上尚未充分披露。当用户通过 DeepSeek Harness 或 API 提交截图、文档、UI 设计稿时，这些数据进入了内测模型的处理链路。内测期间的数据保留策略、是否用于后续训练、是否在到期日统一清理，DeepSeek 没有公开承诺。普通用户与企业法务在灰度期内仍应当假设"内测无隐私"，敏感数据走本地或走私有化部署，是更稳妥的边界。  
  
把这条规则延伸到 0day 漏洞研究场景就更重要——研究者把未公开漏洞的 PoC、payload、目标软件版本上下文喂给灰度模型，等同于把未来 CVE 编号下的核心情报提前暴露给了模型提供方。任何负责任的安全研究团队都不应该在灰度期内把未公开漏洞情报喂给任何第三方 API，更应该走本地部署的复现环境。这是 0day 研究的基本职业伦理，与厂商无关。  
  
更进一步的风险在供应链侧。handlebars.js 是 npm 上下载量数十亿级别的模板引擎，被无数 SaaS、电商前端、低代码平台深度依赖。一旦该 0day 进入野利用，DeepSeek 也间接成了攻击者的研发加速器——从今天起到 9 月 10 日下架之间，灰度窗口本身就是一段"AI 加持的武器化时间"。handlebars.js 维护方与下游企业需要抢在这个窗口期内完成修复与依赖升级。  
  
handlebars.js 维护方给出的临时 Workaround 是两条：在调用 Handlebars.compile()  
 前做类型检查，确保入参是字符串而非 JSON 反序列化对象；服务端若模板是构建期预编译的，改用 handlebars/runtime  
 版本，关闭 compile()  
。  
  
![handlebars.js 官方临时 Workaround（输入类型校验 + 使用 runtime-only 构建）](https://mmbiz.qpic.cn/mmbiz_jpg/nGzNudUIJ6OQNpuf3wUANdQdxdWlekFlkdgNZ0PqzSUBXeU2ugSClvSR3rdn5hpFAWwms0BBcltghVgAyUgJsGBBVabHmtsbjTYA1dwHPh0/640?from=appmsg "handlebars.js 官方临时 Workaround（输入类型校验 + 使用 runtime-only 构建）")  
图源：@whoareme33 / X  
  
但这只是止血，不是根治。下游用户需要盯着官方补丁与 CVE 编号，在修复版本发布后立即升级。  
## 七、总结：9 月 10 日之后的中国大模型新格局  
  
DeepSeek V4.1 Flash 内测不是一次技术发布，而是一次有清晰商业目标的灰度实验。命名里的到期日、问卷里的替代之问、不变的定价、跳涨的性能、再加上 Zaddy 与 Nick 两组独立第三方数据的硬复现，都是为了让市场在最短时间内做出真实选择。9 月 10 日之后，无论 V4.1 Flash 是升格为 Pro 的替代品，还是作为正式版单列，都将是中国大模型产业格局重新洗牌的又一信号弹。  
  
对 CEO 和 CISO 的启示很直接：**采购方不要错过这次灰度的真实业务数据**  
，用自己的核心场景——尤其是代码审计、漏洞复现、告警研判这类与安全强相关的场景——跑一遍 V4.1 Flash，把 token 成本、响应延迟、漏洞漏检率、稳定性四个核心指标沉淀到内部 benchmark；任何把 DeepSeek 用于生产关键链路的团队，都应在 9 月 10 日前完成切换与回退的双轨预案，并明确灰度期内输入数据的留存与销毁策略。  
  
对安全研究社区的启示同样直接：**当开源模型能用 0.05 美元挖出 Pro 都无法稳定复现的 0day，"挖洞"这件事的商业价值正在被开源生态重新定价**  
。漏洞赏金平台、开源软件维护者、企业 SRC 的工作流都需要重新评估：当模型能在分钟级用几美分产出可用 PoC 时，传统的"研究→挖掘→验证→上报"链条上哪些环节还有不可替代的人力价值？这是接下来 12 个月需要回答的问题。  
  
对供应链侧的下游用户，启示更紧迫：**任何在生产环境依赖 handlebars.js 的团队，请把这次 AI 加速挖出的 0day 视作一次预警演练**  
。在内测窗口关闭、模型被下架之后，类似能力的"AI 漏洞研究助手"会变成常态化工具，攻击者的研发成本将被同步压缩到一个从未有过的低点。  
  
AI 时代的采购决策，已经从一次性招标变成了日级别的版本管理。而这次灰度给出的最大教训是：**当开源模型能把前沿闭源模型在代码安全审计与 0day 研究上的能力以 1/35 甚至更高的性价比复现时，"安全"不再是付费墙后的奢侈品，而是一个被开源生态持续压价的标准化基础设施——这是攻击者与防御者必须同时面对的新现实**  
。这就是 2026 年下半年中国大模型竞争的真实战场。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/nGzNudUIJ6PkJgH97t0EetKlPaxwttCbfkIjeVBTR8d2pY0FMOZEVRbjgldG3m5JanBEVqawxNgU3N4gdwlw1OicgFvhIXAnpJVCVvF0IFfg/640?from=appmsg "")  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650621144&idx=1&sn=895132b6dea5c5055ac21126293661f9&scene=21#wechat_redirect)  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650621255&idx=4&sn=75d0f413e300d99d4e5cc631714c96ae&scene=21#wechat_redirect)  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650621242&idx=1&sn=c7504153dd6aa285da53fc1a4a907f82&scene=21#wechat_redirect)  
> 👇 点击**阅读原文**  
，访问我的网站  
  
  

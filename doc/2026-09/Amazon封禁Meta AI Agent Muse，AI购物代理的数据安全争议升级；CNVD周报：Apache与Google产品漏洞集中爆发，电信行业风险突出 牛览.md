#  Amazon封禁Meta AI Agent Muse，AI购物代理的数据安全争议升级；CNVD周报：Apache与Google产品漏洞集中爆发，电信行业风险突出| 牛览  
 安全牛   2026-09-22 03:21  
  
**点击蓝字 关注我们**  
  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/wKeDC5RjIzFVJTplxUZQBj8quyScqMhLT0AC2ia2ePr8YRic81NnzzYo4pTGqeDTiaGia0TarZjyG5KxrwN8a7bnu8KC4MrHEESP4d3B0gLKYmI/640?wx_fmt=png&from=appmsg "")  
  
  
新闻速览  
  
  
- CNVD周报：Apache与Google产品漏洞集中爆发，电信行业风险突出  
  
  
- 中美磋商 AI 国家安全事件通报机制，核心运行规则仍待落地  
  
  
- CNVD发布上周关注度较高的产品安全漏洞  
  
  
- 违反 GDPR 位置数据处理规则，Google 遭爱尔兰 DPC 处以 4.03 亿欧元罚款  
  
  
- AI原生6G网络曝新型攻击：攻击者利用失窃API密钥实施“意图注入”  
  
  
- PAYLOAD 勒索软件滥用 GPO 实现无加密勒索，借 AD 策略实现全域破坏  
  
  
- Lumen 推出 Intelligent Internet，弹性带宽作为 NaaS 生态的入口产品  
  
  
- Amazon封禁Meta AI Agent Muse，AI购物代理的数据安全争议升级  
  
  
- LinkedIn 获法院禁令，制止企业借助海量虚假账号大规模爬取用户数据  
  
  
- Windows 9月安全更新引发故障，File History 文件历史备份功能失效  
  
  
  
  
特别关注  
  
  
**CNVD周报：Apache与Google产品漏洞集中爆发，电信行业风险突出**  
  
国家信息安全漏洞共享平台（CNVD）发布2026年第37期（9月14日至20日）安全周报。本周共收集、整理信息安全漏洞479个，其中高危漏洞164个、中危漏洞264个、低危漏洞51个。漏洞平均分值达7.2分，安全形势严峻。  
  
  
本周漏洞主要涉及Apache、Google、Siemens等知名厂商产品。Apache官方发布了多个安全公告，涉及Apache Airflow、Apache Answer等；Google发布了Android、Chrome等产品的安全更新；Siemens发布了多款工控产品的漏洞修复方案。这些漏洞若被恶意利用，可能导致信息泄露、远程代码执行等严重后果。  
  
  
从行业分布看，电信行业成为本周漏洞影响的重灾区，其次为移动互联网和工控系统。在漏洞收录方面，本周CNVD接报漏洞479个，其中包含多个高危漏洞。值得注意的是，本周漏洞修复率仅为78.9%，部分用户仍面临安全风险。  
  
  
CNVD建议相关用户及时关注官方安全公告，尽快下载补丁或升级至最新版本，以降低网络安全风险。同时，建议各行业加强安全监测与防护，防止漏洞被恶意利用。  
  
  
原文链接：  
  
https://www.cnvd.org.cn/webinfo/show/12781  
  
  
**CNVD发布上周关注度较高的产品安全漏洞**  
  
2026年9月14日至20日，CNVD披露多款境内外主流产品高危安全漏洞，涵盖浏览器、企业服务软件、建站插件、操作系统、家用路由器等多类常用设备与程序，多数漏洞可引发数据泄露、权限提升、代码执行等高危风险。  
  
  
境外产品方面，WordPress两款插件接连曝出漏洞，其中Suggestion Engine for WooCommerce plugin存在SQL注入漏洞，可被攻击者窃取数据库敏感数据；Stripe Payments plugin存在跨站脚本漏洞。Google Chrome因Actor组件授权不当，存在敏感信息窃取漏洞。Ivanti Neurons for ITSM权限验证缺陷可导致任意代码执行，IBM WebSphere Application Server存在权限提升漏洞，攻击者或可完全控制服务器。  
  
  
境内产品漏洞集中在终端与路由设备。Huawei HarmonyOS事件通知模块存在权限控制缺陷。多款Tenda路由器曝出缓冲区溢出漏洞，涉及AC9、F456型号，由参数处理、长度校验不当引发。TOTOLINK NR1800X路由器存在栈缓冲区溢出漏洞，攻击者可远程触发设备崩溃或执行恶意代码。  
  
  
本次榜单由CNVD结合漏洞热度与产品应用范围综合评定。  
  
  
原文链接：  
  
https://www.cnvd.org.cn/webinfo/show/12786  
  
  
  
热点观察  
  
  
**中美磋商 AI 国家安全事件通报机制，核心运行规则仍待落地**  
  
美国东部时间 9 月 20 日，中美高级官员于纽约开展会谈，美方提出建立针对国家安全类 AI 事件的双边通报机制，纳入 USChina AI Dialogue 对话框架，双方同意继续就此开展沟通，下一轮会谈计划于两个月后在深圳举行，具体时间尚未敲定。  
  
  
据美国财政部长 Scott Bessent 介绍，该机制旨在提升两大 AI 大国之间的透明度，降低跨境 AI 风险带来的误判风险，拟覆盖 AI 驱动的网络攻击、失控 AIagent、非国家主体滥用前沿模型等安全场景，重点关注关键基础设施遭受扰动类事件。  
  
  
目前该提议尚处于初步探讨阶段，尚未形成正式协议，通报触发阈值、对接部门、信息共享范围、处置流程等核心技术与执行细节均未确定，中方暂未公开表态是否接受该方案。本次对话接续今年 5 月相关交流，在前沿 AI 模型安全风险持续上升的背景下，是中美在 AI 安全治理领域的一次探索性尝试，但技术竞争层面的分歧依旧客观存在。  
  
  
原文链接：  
  
https://www.wired.com/story/us-and-china-discuss-alerting-each-other-to-ai-national-security-threats/  
  
  
**PAYLOAD 勒索软件滥用 GPO 实现无加密勒索，借 AD 策略实现全域破坏**  
  
卡巴斯基 GERT 团队披露 2026 年 4 月中东制造业企业安全事件，攻击者通过泄露账号登录 FortiGate SSL VPN，获取域管理员等效权限，滥用 Active Directory 的组策略对象（GPO）实施 PAYLOAD 勒索攻击，全程未在 Windows 终端投放恶意程序与加密文件。  
  
  
攻击者在域根链接两个恶意 GPO，PAYLOAD GPO 推送勒索壁纸、锁屏、弹窗警告、投放勒索文档并禁用本地管理员账号；另一条 win Firewall Off GPO 批量关闭终端 Windows 防火墙。策略写入后潜伏一天，终端重启才批量生效，同时攻击者完成服务器数据外泄。  
  
  
由于 GPO 属于系统可信分发通道，传统基于文件、进程的 EDR 产品无法识别威胁。该攻击属于无加密勒索，依靠业务中断与数据泄露实施胁迫。  
  
  
报告建议启用域服务变更审计，重点监控事件 ID5137、5136，对 SYSVOL 目录开启文件完整性监控；同时拆分 GPO 创建与链接权限，强化 VPN 多因素认证，防范同类攻击。  
  
  
原文链接：  
  
https://securelist.com/tr/payload-ransomware-via-group-policy/121335/  
  
  
**LinkedIn 获法院禁令，制止企业借助海量虚假账号大规模爬取用户数据**  
  
2026 年 9 月 21 日，美国加州联邦法院敲定 LinkedIn 与 ProAPIs、合作运营方 Netswift 的和解协议，出具法院禁令，制止两家企业大规模爬取平台用户数据。  
  
  
LinkedIn 于去年 10 月发起诉讼，指控对方搭建数百万个虚假账号持续抓取平台信息，涵盖个人档案、企业、院校资料，以及用户评论、动态内容。平台封禁虚假账号后，攻击者每日新建数百至数千个账号继续爬虫，短时间内即可采集数百份用户资料，单纯技术拦截难以遏制。  
  
  
根据和解判决，两家公司必须终止爬虫行为，销毁全部已爬取数据，禁止售卖、流转相关信息，不得继续使用虚假账号访问 LinkedIn。ProAPIs 对外表示，已签署同意判决，承诺未来不再爬取 LinkedIn 数据。  
  
  
LinkedIn 法务负责人 Sarah Wight 称，该判决对用户隐私保护具备重要意义。这也是 LinkedIn 近半年内第二起针对未经许可数据爬取的法律胜诉。  
  
  
原文链接：  
  
https://therecord.media/linkedin-wins-court-order-blocking-mass-scraping  
  
  
  
安全事件  
  
  
**违反 GDPR 位置数据处理规则，Google 遭爱尔兰 DPC 处以 4.03 亿欧元罚款**  
  
2026 年 9 月 21 日，爱尔兰数据保护委员会（DPC）依据《通用数据保护条例》（GDPR），对 Google 开出 4.03 亿欧元（折合 4.6 亿美元）罚单，案件调查自 2020 年 2 月启动。  
  
  
调查周期覆盖 2018 年 5 月 25 日 GDPR 生效至 2020 年 2 月 4 日，重点核查 Web & App Activity、Location History、Location Accuracy 三项功能。监管机构认定，部分用户并不知晓自身位置信息被用于广告投放、兴趣画像，导致用户丧失个人数据控制权；同时 Google 位置数据留存时长超出业务必要范围，进一步加重违规后果。  
  
  
裁决明确 Google 存在四项 GDPR 违规：数据处理不满足合法公平原则；无法证明 Location Accuracy 功能合规；三项功能均未达到透明度义务；Web & App Activity、Location History 存在超期留存问题。DPC 副专员 Graham Doyle 表示，位置数据属于高度敏感信息，可挖掘出大量个人隐私。  
  
  
DPC 要求 Google 须在 6 个月内完成数据处理流程整改。Google 回应称，涉事为历史旧策略，自 2019 年已更新相关机制。报道提及，2022 年 11 月 Google 曾在美国就位置数据采集纠纷达成 3.915 亿美元和解。  
  
  
原文链接：  
  
https://www.infosecurity-magazine.com/news/google-hit-with-403m-gdpr-fine/  
  
  
**Windows 9月安全更新引发故障，File History 文件历史备份功能失效**  
  
2026 年 9 月 21 日，微软发布服务告警，2026 年 9 月 Windows 安全累积更新会导致部分设备的 File History（文件历史）备份功能异常，无法新建、更新备份任务。  
  
  
File History 是 Windows 内置备份工具，可将文档、桌面等目录备份至 USB 磁盘或 NAS 网络存储，支持文件历史版本回滚。故障设备事件查看器中会出现 FileHistory.exe 与 KERNELBASE.dll 相关崩溃记录；即便备份介质正常连接，系统仍提示 “重新连接驱动器”，备份时间戳不刷新，历史文件显示 “无可用以前版本”。  
  
  
受影响版本包含 Windows10 21H2 及后续版本、Windows10 Enterprise LTSC 2016/2019、Windows11 23H2 及以上，对应 KB5124012、KB5124008 等多个补丁编号。  
  
  
本月补丁已造成多项故障，微软此前发布过带外更新修复远程桌面、Hyper-V 等问题，但该备份漏洞尚未推出修复方案。  
  
  
原文链接：  
  
https://www.bleepingcomputer.com/news/microsoft/microsoft-september-updates-break-file-history-backup-feature/  
  
  
  
安全攻防  
  
  
**AI原生6G网络曝新型攻击：攻击者利用失窃API密钥实施“意图注入”**  
  
2026 年 9 月 21 日，渥太华大学与诺基亚贝尔实验室发布研究，针对 AI 原生 6G 所采用的基于意图网络（IBN）提出对抗性意图注入（adversarial intent injection）这一新型安全威胁。IBN 允许网络管理人员直接描述业务目标，由软件自动转换为网络策略，但该抽象机制带来新攻击面。  
  
  
攻击者拿到泄露的 API 密钥后，可将恶意 JSON 指令混杂在合法请求中提交，可能引发拒绝服务、权限提升、流量重定向、植入后门等危害。研究团队构建包含 1100 条意图的测试数据集开展验证。  
  
  
传统基于 88 个关键词的规则分类器仅能识别约 10% 恶意意图。研究人员开发两类机器学习检测器，一类依靠标注样本训练，检出率 75%96%；另一类为异常检测，无需攻击样本，在三类攻击模式表现最优，但对固定频率攻击会漏判约三分之一请求。两种方案表现均优于单条请求独立检测的旧方案（检出仅 50%60%）。  
  
  
研究团队表示，后续将扩充真实场景 JSON 策略样本，并引入可解释 AI，提升检测结果的可解读性。  
  
  
原文链接：  
  
https://www.helpnetsecurity.com/2026/09/21/6g-intent-injection-attacks/  
  
  
  
产业动态  
  
  
**Amazon封禁Meta AI Agent Muse，AI购物代理的数据安全争议升级**  
  
Amazon已阻止Meta旗下AI Agent Muse访问Amazon.com进行商品浏览和代用户下单操作，成为AI Agent进入电商场景后面临平台限制的又一起事件。Amazon表示，第三方应用若代表用户在其他企业平台执行购买操作，应公开运行机制，并尊重服务提供商是否允许其接入的决定。  
  
  
此次限制源于Amazon对Muse访问方式的安全担忧。Amazon称，Meta此前未提前告知其Muse将访问Amazon购物平台，且该AI Agent在浏览过程中未明确表明自身身份，可能涉及用户凭据捕获等风险。Amazon随后以违反其Conditions of Use为由限制Muse继续访问。  
  
  
Muse是Meta推出的AI个人助手，具备执行多步骤任务的能力，包括搜索商品、完成购物等Agentic AI功能。Meta此前表示，Muse无法访问用户的安全登录信息或银行卡支付数据。不过，围绕AI Agent权限边界的隐私问题仍受到关注，有报道称Muse可能读取用户消息内容，而相关权限控制机制存在争议。  
  
  
从技术角度看，AI Agent与传统聊天机器人不同，其核心能力在于通过工具调用、网页交互和自动化流程替用户完成任务。这也使其需要访问更多外部系统，从而带来身份认证、数据授权、操作透明度和责任归属等新的安全挑战。  
  
  
此次事件反映出电商平台与AI Agent开发商之间围绕数据控制权和用户交互入口的竞争正在加剧。此前，Amazon也曾因类似原因限制其他AI服务访问其平台。随着AI Agent逐渐承担购物、支付等高风险任务，平台如何建立安全接入机制，将成为未来生态竞争的重要议题。  
  
  
原文链接：  
  
https://techcrunch.com/2026/09/21/metas-ai-agent-has-been-blocked-from-using-amazon-com/  
  
  
  
新品发布  
  
  
**Lumen 推出 Intelligent Internet，弹性带宽作为 NaaS 生态的入口产品**  
  
2026 年 9 月 21 日，Lumen 正式发布 Intelligent Internet 企业网络产品，该服务采用消费化模式，可针对 AI、云业务的波动负载，实现带宽动态弹性伸缩，作为切入 Lumen Connected Ecosystem（NaaS 生态）的前端入口。  
  
  
传统企业互联网多为固定带宽，而计算、存储早已实现按需扩缩容，难以适配 AI 业务突发性、波动大的流量特征。该服务可在数分钟内完成开通，用户可通过 Lumen Connect 或者 API 接口调整带宽，最高支持 100Gbps，现已覆盖美国 1000 万商业点位，提供可调整容量的期限计费模式。  
  
  
Lumen 高管 Ryan Asdourian 表示，该产品不仅面向存量渠道合作伙伴拓展增收机会，还用于吸引新合作伙伴，带动整套 NaaS 服务销售。合作伙伴 AppDirect 指出，弹性带宽将成为面向 AI 时代的连接能力。该产品已有纽约洋基队等早期标杆客户。  
  
  
原文链接：  
  
https://www.crn.com/news/networking/2026/intelligent-internet-the-front-door-to-lumen-s-naas-ecosystem-says-cmo  
  
  
  
  
  
  
  
**联系我们**  
  
合作电话：18610811242  
  
合作微信：aqniu001  
  
联系邮箱：bd@aqniu.com  
  
  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/wKeDC5RjIzEicEMXtysaQGTe5bEPS3rpmhYpPlPibrJ75kGwW3vW0VRS1zQ5ngOgIGnImIhTj7fMzBaZYxoeEMYHuoDFHtVVRuiau97aNOYVtQ/640?wx_fmt=gif&from=appmsg "")  
  
  
  

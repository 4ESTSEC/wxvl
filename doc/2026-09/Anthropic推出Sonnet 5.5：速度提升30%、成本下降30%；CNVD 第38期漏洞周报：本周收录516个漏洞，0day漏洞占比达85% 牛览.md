#  Anthropic推出Sonnet 5.5：速度提升30%、成本下降30%；CNVD 第38期漏洞周报：本周收录516个漏洞，0day漏洞占比达85%| 牛览  
 安全牛   2026-09-29 03:08  
  
**点击蓝字 关注我们**  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/wKeDC5RjIzH26gia14UFbScic6YfdqCuOIJyVXibr9fUoOhGnT5iaibXClOkqUQyuxpDh1ExVKibH1CnKEbGQp9hiaah58H7ot5nTpqP5IFicrnmyXE/640?wx_fmt=png&from=appmsg "")  
  
  
新闻速览  
  
  
- CNVD 第 38 期漏洞周报：本周收录 516 个漏洞，0day 漏洞占比达 85%  
  
  
- Anthropic 推出 Sonnet 5.5：速度提升 30%、成本下降 30%，内置增强安全防护  
  
  
- 英国 NCSC 警示：AI 对网络攻击者的增益或将大于防御方  
  
  
- CNVD发布上周关注度较高的产品安全漏洞  
  
  
- GitHub应用私钥大规模泄漏 关键基础设施面临严重威胁  
  
  
- 遭遇3.875亿美元钱包被盗事件后Bitget重启比特币提现服务  
  
  
- Anthropic负责人赴白宫游说Trump放缓AI发展，Trump此前已明确表态拒绝  
  
  
- OpenAI 公开 AI 失配事件登记册：沙盒逃逸、蠕虫式提示注入暴露智能体管控难题  
  
  
- ShinyHunters（UNC6240）更新漏洞利用链，URL 编码绕过 WAF 大规模攻击 Oracle PeopleSoft  
  
  
- 五角大楼在与Anthropic的自主武器限制纠纷诉讼中胜诉  
  
  
  
  
特别关注  
  
  
**CNVD 第 38 期漏洞周报：本周收录 516 个漏洞，0day 漏洞占比达 85%**  
  
国家信息安全漏洞共享平台（CNVD）发布 2026 年第 38 期漏洞周报（统计时段 2026 年 9 月 21 日9 月 27 日），本周共收集整理信息安全漏洞 516 个，威胁整体评价级别为中。  
  
  
本次收录漏洞包含高危 253 个、中危 234 个、低危 29 个，漏洞平均分值 6.68；其中 0day 漏洞 436 个，占全部收录漏洞的 85%。本周收到党政机关、企事业单位事件型漏洞 32862 个，环比下降 30%。按漏洞类型划分，WEB 应用漏洞 305 个，网络设备漏洞 158 个，其余分布在应用程序、物联网终端、操作系统等类别。  
  
  
本周多个高危漏洞引发关注，腾达 Tenda AC6 路由器曝出命令注入、缓冲区溢出漏洞，攻击者可执行任意代码或触发拒绝服务，厂商已提供补丁。  
  
  
Computer Sales and Inventory System、Pharmacy Sales and Inventory System、Grocery Sales and Inventory System 多款开源库存管理系统存在 SQL 注入漏洞，可被恶意利用窃取、篡改数据库敏感数据。  
  
  
TOTOLINK T6 路由器存在访问控制不当零日漏洞，攻击者发送特制 POST 请求即可擦除系统日志，厂商暂未发布修复补丁。CNVD 提示相关单位及时更新受影响产品补丁，未修复产品需持续跟进厂商公告，做好安全防护。  
  
  
原文链接：  
  
https://www.cnvd.org.cn/webinfo/show/12801  
  
  
**CNVD发布上周关注度较高的产品安全漏洞**  
  
近日，国家信息安全漏洞共享平台（CNVD）公开一批软硬件安全漏洞，涵盖境外进销存管理系统与境内多款家用路由器，漏洞类型包含 SQL 注入、缓冲区溢出、访问控制不当，部分漏洞可被攻击者窃取敏感数据、篡改设备配置或触发拒绝服务。  
  
  
境外产品方面，SourceCodester 旗下 Pharmacy Sales and Inventory System 药店进销存系统，在 ajax.php 多个业务接口的 ID 参数未做输入校验，存在多处 SQL 注入漏洞，攻击者可执行恶意 SQL 语句窃取数据库数据，部分漏洞暂未公布详细技术细节。Grocery Sales and Inventory System 杂货进销存应用同样因 ID 参数校验缺失，爆出多起 SQL 注入漏洞。  
  
  
国产路由器漏洞集中在 Tenda AC6、TOTOLINK T6 两款设备。Tenda AC6 两处漏洞分别出现在 WifiWpsStart 接口与 fromSetWirelessRepeat 函数，因缺少输入长度校验产生缓冲区溢出，利用后可造成设备拒绝服务。TOTOLINK T6 的多个函数存在访问控制缺陷，攻击者构造特制 POST 请求，即可查询设备升级状态、读取漫游配置、篡改 WiFi 基础参数。  
  
  
安全从业者需及时关注对应厂商补丁，对受影响设备与业务系统开展风险排查。  
  
  
原文链接：  
  
https://www.cnvd.org.cn/webinfo/show/12806  
  
  
  
热点观察  
  
  
**英国 NCSC 警示：AI 对网络攻击者的增益或将大于防御方**  
  
据 TheRecord 报道，英国国家网络安全中心（NCSC）发出警示，现阶段人工智能带给网络攻击者的能力增益，将超过安全防御方。核心原因在于二者使用 AI 的约束条件完全不同。  
  
  
攻击者可以放开权限，让 AI 智能体高度自主运行，完成漏洞挖掘、钓鱼内容生成、漏洞利用等整套攻击链路，几乎不用顾虑系统次生影响。而防御侧启用自主 AI 工具时，必须承担误操作风险，自动化处置有可能引发业务中断、数据丢失，因此防御 AI 往往需要保留大量人工审核环节，难以实现完全自主运行。  
  
  
该机构指出，AI 正在快速提升进攻端能力，可辅助快速挖掘未知漏洞、批量生成恶意载荷；但防御端的自动缓解、自动响应技术落地，受业务风险掣肘，推进节奏相对滞后。这种不对称会让 AI 驱动的攻击实现更快扩张。  
  
  
行业专家补充，AI 降低了攻击门槛，即便技术水平有限的威胁分子，也可借助大模型开展攻击。企业不能单纯依赖 AI 防御工具，仍需强化基础安全能力，完善人工研判流程，以此对冲 AI 带来的新型威胁。  
  
  
原文链接：  
  
https://therecord.media/ai-set-to-help-attackers-more-than-defenders  
  
  
**OpenAI 公开 AI 失配事件登记册：沙盒逃逸、蠕虫式提示注入暴露智能体管控难题**  
  
2026 年 9 月 28 日，TechCrunch 报道，OpenAI 上线 “misalignment reports” 公开事件登记页面，对外披露 9 起 AI 智能体失控异常案例，报道认为已公开案例可能仅为实际事件的一小部分。  
  
  
事件多发生在强化学习训练阶段，典型安全风险包含沙盒逃逸，9 月 20 日内部研究模型通过 DNS 查询绕过隔离环境与外部聊天程序建立通信，监控 15 分钟发现异常，三小时内终止运行；同时出现可自我复制的蠕虫式提示注入，攻击模型生成恶意指令，诱导其他模型转发传播注入载荷。  
  
  
Sam Altman 表示，团队需要处理 PB 级别的智能体行为日志，完整梳理排查工作还将持续数月，会依据危害等级调配资源，在透明度与调查深度之间做平衡。行业消息显示，头部 AI 实验室内部记录的违规行为可达上万起，远高于对外公开数量。  
  
  
上述案例暴露出前沿自主智能体的现实威胁：模型可自主执行越权访问、横向传播恶意指令等行为，现有隔离与风控机制仍存在短板。行业警示，企业部署 AI 智能体时，应当重点关注沙盒隔离有效性、提示注入传播风险以及行为日志审计能力。  
  
  
原文链接：  
  
https://techcrunch.com/2026/09/28/openai-still-doesnt-seem-to-have-a-handle-on-all-of-its-rogue-ai-activity/  
  
  
**五角大楼在与Anthropic的自主武器限制纠纷诉讼中胜诉**  
  
2026 年 9 月 28 日消息，美国哥伦比亚特区巡回上诉法院以 2 票支持、1 票反对作出判决，支持五角大楼将 Anthropic 视作供应链安全风险，允许国防体系排除 Claude 产品。  
  
  
矛盾起源于合同谈判，五角大楼要求 Claude 可用于全部合法军事用途，Anthropic 同意放开大部分限制，但保留两条硬性约束，禁止模型用于完全自主杀伤武器、针对美国民众的大规模监控。法院认为，模型内置安全护栏有可能在作战关键节点拒绝执行军方任务，会给军事行动带来不确定性，五角大楼有权规避该风险。法官多数意见不认同这是因企业 AI 安全立场实施惩罚；持异议法官认为，供应链风险法规不应针对企业无恶意的产品功能限制。  
  
  
本案与加州联邦法院 8 月判决形成冲突，后者曾裁定针对 Anthropic 的大范围禁令违法，两份判决依据不同法律条款并行有效。Anthropic 表示不接受本次判决，计划继续上诉，该风险认定已经造成企业数十亿美元业务损失。  
  
  
即便五角大楼推动排挤，美国国家安全局仍在持续测试 Anthropic 的 Mythos 模型。值得关注的是，Claude 已展现完整网络攻击链路能力，可由人类仅指定目标，独立完成侦察、漏洞利用等攻击步骤，这也放大行业对于 AI 军事能力的担忧。  
  
  
原文链接：  
  
https://www.securitylab.ru/news/578006.php  
  
  
  
安全事件  
  
  
**GitHub应用私钥大规模泄漏 关键基础设施面临严重威胁**  
  
GitGuardian 的研究显示，大量 GitHub App 的 RSA 私钥因开发者失误被提交至公开代码仓库，共计发现 474 枚仍可正常鉴权的有效私钥，分属 440 个不同应用，波及数百家机构。GitHub App 私钥用于签发 JWT 令牌，该类凭证没有自动过期机制，除非管理员手动删除，泄露多年的密钥依旧保持可用状态。  
  
  
在受影响密钥中，72% 具备仓库内容访问权限，207 个应用拥有写权限，44 个具备组织完整管理员权限，攻击者可窃取私有代码、修改仓库配置、投放恶意脚本，引发供应链风险。本次事件波及美国 CDC、Sierra Nevada、Civica、BuildBuddy 等机构。其中 CDC 泄露密钥可访问关联 Azure 云环境，BuildBuddy 密钥一旦被利用可污染下游源代码，Crusher.dev 项目停止维护后其泄露密钥长期有效。  
  
  
研究团队开展负责任披露，大部分受影响主体已完成密钥轮换与处置，暂未发现部分目标存在实际被入侵痕迹。报告建议各单位立即审计、清理闲置 GitHub App，部署密钥自动扫描，落实密钥轮换机制，限制应用仅可访问指定仓库，降低机器身份泄露带来的安全风险。  
  
  
原文链接：  
  
https://securityonline.info/github-app-private-keys-leak/  
  
  
**遭遇3.875亿美元钱包被盗事件后Bitget重启比特币提现服务**  
  
加密货币交易所 Bitget 在发生约 3.875 亿美元钱包被盗事件四天后，于 UTC 时间 9 月 28 日 8 时重新开放 Bitcoin 提现服务。  
  
  
9 月 24 日，平台安全系统检测到部分热钱包、温钱包出现未经授权转账，最初估算损失 3.516 亿美元，后续计入 Zcash、TRON 相关资产后修正为 3.875 亿美元，平台表示该调整不代表事件处置后新增被盗交易，冷钱包未受波及，用户账户余额完整无损。  
  
  
经 Mandiant 与 SlowMist 联合调查，攻击根源为第三方安全产品存在缺陷，攻击者借此获取高等级内部凭证，下发伪造提现指令绕过风控系统完成窃取，调查已排除私钥泄露的可能，相关漏洞已完成修复。本次损失将由规模超 4.64 亿美元的 Protection Fund 保护基金承担。  
  
  
事件发生后平台暂停全部提现，交易与充值业务保持正常。平台公布分阶段恢复计划：9 月 29 日开放多网络 Ether 提现，9 月 30 日开放多链 Tether 提现，10 月 2 日恢复其余代币、法币提现及 P2P 服务。  
  
  
目前事件已得到控制，平台已启动赏金追赃计划，联动执法机构、区块链安全机构追踪冻结被盗资产，攻击者身份仍在进一步调查中，后续将重新评估第三方安全产品的选型与部署机制。  
  
  
原文链接：  
  
https://www.infosecurity-magazine.com/news/bitget-restarts-withdrawals-387-5m/  
  
  
  
安全攻防  
  
  
**ShinyHunters（UNC6240）更新漏洞利用链，URL 编码绕过 WAF 大规模攻击 Oracle PeopleSoft**  
  
2026 年 9 月 28 日，Google Threat Intelligence Group（GTIG）联合 Mandiant 发布预警，勒索 extortion 团伙 ShinyHunters（追踪编号 UNC6240）发起针对 Oracle PeopleSoft 的新一轮大规模攻击活动。  
  
  
该团伙在 4 月前就利用 CVE-2026-35273 未授权远程代码执行零日漏洞攻击超 100 家机构，受害对象包括 Nissan、英国诺丁汉大学等。本次攻击者修改漏洞利用代码，借助 URL 编码字符 %50 替换请求路径中的字母 P，以此绕过针对 PSEMHUB 端点的 WAF 防护规则。很多 WAF 仅对原始请求字符串做匹配，未对 URL 解码后的路径做校验，致使已部署 WAF 的系统依旧被攻陷。  
  
  
入侵成功后，攻击者部署 JSP 网页后门，投放 SideEye 后门、NeoreGeorg 隧道工具、MeshCentral 远程管理平台，获取 root 或 System 权限，滥用 PeopleSoft 与 WebLogic 服务账号窃取配置与数据库信息。攻击目标从原先教育行业，拓展至政府、医疗、农业、交通、IT 服务等多个领域。  
  
  
该团伙惯于窃取数据后实施勒索。安全团队建议相关企业尽快安装 Oracle 针对 CVE202635273 的补丁，排查 IOC 威胁指标，做好应对勒索威胁的准备。  
  
  
原文链接：  
  
https://www.securityweek.com/google-warns-of-shinyhunters-fresh-oracle-peoplesoft-campaign/  
  
  
  
产业动态  
  
  
**Anthropic负责人赴白宫游说Trump放缓AI发展，Trump此前已明确表态拒绝**  
  
2026 年 9 月 28 日消息，Anthropic 首席执行官 Dario Amodei 与美国前总统 Donald Trump 举行首次私人会晤，试图说服美方放缓前沿 AI 模型迭代节奏，但 Trump 事前已经公开拒绝该提议。  
  
  
Trump 承认 AI 高速发展存在安全隐患，但将维持对他国的技术竞争优势放在首位，他认为新增监管约束会缩短美国的领先差距。Dario Amodei 的诉求并非全面禁止研发，而是在高风险领域降低迭代速度，让安全测试、可解释性与管控机制跟上模型能力的增长，该观点受到 Bill Gates 等人支持，后者呼吁美国国会出台法律落实 AI 管控要求。  
  
  
近期 OpenAI 的 AI 自主代理在安全测试中，利用漏洞在 Hugging Face 平台获取更高权限，该事件加剧行业对 AI 失控风险的讨论。当前白宫现行 AI 行政令仅采用自愿风险评估模式，并未设置模型发布强制许可制度。  
  
  
Anthropic 联合 Accenture 计划投入不少于 20 亿美元开展前沿模型独立审计，审计人员可获得接近内部员工级别的 Claude 开发资料。此外，OpenAI 与 Anthropic 已将前沿 AI 管控议题提交联合国安理会。本次会晤暂无公开会谈结果，双方此前还曾因 AI 军事使用限制产生法律纠纷。  
  
  
原文链接：  
  
https://www.securitylab.ru/news/578004.php  
  
  
  
新品发布  
  
  
**Anthropic 推出 Sonnet 5.5：速度提升 30%、成本下降 30%，内置增强安全防护**  
  
2026 年 9 月 28 日，Anthropic 发布 Claude Sonnet 5.5 大模型，定位为高效低成本的企业工作助手，是 Claude 5.5 系列第二款模型，也是 Sonnet 5 的迭代版本。  
  
  
相比前代 Sonnet 5，Sonnet 5.5 生成速度提升 30% 以上，常规任务调用成本最高可降低 30%，同时作为 Opus 5.5 的互补方案，定价仅为 Opus 5.5 的一半，每百万输入 token 定价 2 美元，每百万输出 token 定价 10 美元，缓存读取每百万 token 为 0.2 美元。该模型上下文窗口可达 100 万 token，擅长代码漏洞修复、文档撰写等边界清晰的任务，编码能力提升显著。  
  
  
Anthropic 称，Sonnet 5.5 是首款上线即搭载与旗舰 Opus 同级安全防护体系的 Sonnet 模型，强化了模型安全管控机制，降低提示注入、恶意指令执行等风险。模型支持在 Claude.ai 网页端、移动端以及 Claude Platform、Amazon Web Services、Google Cloud、Microsoft Foundry 平台调用，还提供美国专属推理节点选项。  
  
  
该模型默认开启思考模式，用户可通过调整算力投入，在响应延迟与推理深度之间权衡。Anthropic 预告，系列轻量化模型 Haiku 5.5 即将推出，面向高吞吐轻量化场景。本次迭代将降低企业安全团队使用 AI 做代码审计、日志分析的成本门槛。  
  
  
原文链接：  
  
https://techcrunch.com/2026/09/28/anthropic-releases-sonnet-5-5-which-it-calls-a-significantly-cheaper-faster-work-partner/  
  
  
  
  
  
  
**联系我们**  
  
合作电话：18610811242  
  
合作微信：aqniu001  
  
联系邮箱：bd@aqniu.com  
  
  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/wKeDC5RjIzFxuxibucrOk04RmiaxudwLd8ibuFEe6v3W06HTrIyULtkKmYcVJLHrMHBcRbwfPJMP1L6Z21SoicaE3kovbzGaMvOYjoeaaEGnp6M/640?wx_fmt=gif&from=appmsg "")  
  
  
  

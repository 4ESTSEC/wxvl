#  与AI抢时间：美CISA颁布国家级漏洞防御新规  
原创 网安君
                    网安君  网络安全罗盘   2026-06-25 09:30  
  
![](https://mmbiz.qpic.cn/mmbiz_png/6JwCVadmx5uTPJUBR982DY3xpbs5Cict7plCkUap8Hr5IosPM2Zdibr0UcHBAQ0k6ia9VA3GrjWWptaulGP2QcHXdGRTh72DzicogH8rqhRzW0M/640?wx_fmt=png&from=appmsg "")  
  
**主要内容概览**  
  
  
  
近日，美国国土安全部下属的网络安全与基础设施安全局（CISA）发布了《根据风险确定漏洞更新的优先级指令》（BOD 26-04: Prioritizing Security Updates Based on Risk）。该指令整合、澄清并更新了漏洞修复的紧迫性级别和评判标准，将各机构的补丁工作重点放在最高风险的岗位上，并提升联邦民用机构的效率。  
  
文件链接：  
https://www.cisa.gov/news-events/news/cisa-issues-new-directive-improving-how-federal-agencies-prioritize-mitigation-cyber-vulnerabilities  
  
**网安罗盘简评**  
  
  
  
1  
  
**背景分析**  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/6JwCVadmx5ukXribMdibPoIlyNjcw5CWSwXr0uu6K9HNYGceopWUacR287VPk1G1CavJicFiamicADCgRYicjlj1PjicgDQHL5hIJx1uKQiaicdibnXrU/640?wx_fmt=png&from=appmsg "")  
  
一是旧有补丁模式部分失效。此前实施的“一刀切”式漏洞修复指令（如BOD 19-02和BOD 22-01等）导致安全团队疲于奔命。2025年数据显示，CISA已知被利用漏洞（KEV）目录中仅26%被完全修复，漏洞解决的中位时间已攀升至43天，防守方已难以跟上漏洞爆发的节奏。  
  
二是AI加速漏洞攻击时效。人工智能正在大幅缩短从漏洞发现、利用代码生成到批量攻击编排的时间窗口，传统按月或按季度排期的静态修复流程已无法应对。  
  
  
  
2  
  
**主要举措**  
  
  
该指令最大的特点在于明确了面对海量漏洞告警，必须承认安全资源的有限性，将防御精力从“全面覆盖”转向“精准打击”，按攻击者的真实利用路径来分配资源。基于此，该指令建立了一套基于四个关键变量的动态风险分级模型，并设定了差异化的强制修复时限。  
  
**一是建立基于四变量的风险评估模型。**  
  
资产暴露程度：系统是否面向公众互联网开放。  
  
KEV状态：漏洞是否已被列入CISA已知被利用漏洞目录。  
  
利用自动化程度：攻击者能否通过脚本或工具实现全自动化利用。  
  
技术影响：漏洞被利用后，攻击者是获得“部分控制”还是“完全控制”。  
  
**二是确立了差异化的漏洞修复时限。**  
  
基于上述四个变量，指令设定了不同情况的漏洞修复时限，包括以下类别。（判断对照情况见下表2）  
  
最高优先级（3天）：同时满足公网暴露、在KEV目录中、可自动化利用且能实现完全控制的漏洞，必须在3天内修复，并同步进行取证分析以排查是否已被入侵。  
  
中高优先级（7天至14天）：在KEV目录中但未暴露于公网，或仅能获得部分控制的漏洞，修复时限为7天或14天。  
  
中优先级（30天至60天）：不在KEV目录中但满足公网暴露、可自动化利用且能完全控制等条件的，修复时限为30天或60天。  
  
低优先级（随系统升级修复）：不在KEV目录中、未暴露于公网且无法被自动化的漏洞，允许推迟至下一次计划内的系统升级时再处理。  
  
分阶段实施要求：指令要求联邦机构立即更新内部漏洞管理政策；60天内将流程与KEV目录对齐；180天内全面达到规定的修复时限要求。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/6JwCVadmx5tZaRJjReicwJ8Q1licVj2NKASVretCbOUcrBbEhqseBDSwM9N5JWGB93JTcKDnicGBOqXsajNvB1unuWwPFkRrtDia5j0ke9ct0T8/640?wx_fmt=png&from=appmsg "")  
  
  
  
3  
  
行业影响研判  
  
  
美国网络安全和基础设施安全局（CISA）发布的《基于风险优先级的漏洞修复》（BOD 26-04）指令，标志着美国联邦网络安全防御体系在AI时代的一次重大范式转变。  
  
一是或重塑漏洞管理产品与理念。行业将从传统的“扫描-打分-派单”工单管理模式，向“动态风险治理”转型。这将催生对资产暴露面持续监测、自动化风险优先级排序（VPR）以及威胁情报关联分析等智能化安全产品的巨大需求。  
  
二是将加速AI安全攻防技术的演进。面对AI驱动的自动化攻击，防守方将被迫全面引入AI技术来缩短响应时间。AI驱动的自动化漏洞验证、智能取证分诊以及自动化补丁编排工具等或将迎来爆发式增长。  
  
三是推动全球合规标准的连锁反应。作为美国联邦政府的强制指令，BOD 26-04具有极强的行业风向标意义。它将促使全球关键基础设施、跨国企业以及欧盟等监管机构重新评估并收紧自身的漏洞修复SLA（服务等级协议），引发全球网络安全合规成本的上升与标准的升级。  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/6JwCVadmx5unAPzwSIicXia2WaFiclYdsy92GLyglSGACOBWbr7LZXhuQ2XAtnsM49D6FxfzTxM6GEsKnM8vtB639PIG8E52bgcXS5NbK1bd88/640?wx_fmt=gif&from=appmsg "")  
  
✦  
  
  
•  
  
  
✦  
  
  

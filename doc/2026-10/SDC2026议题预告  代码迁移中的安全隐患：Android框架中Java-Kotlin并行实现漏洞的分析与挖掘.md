#  SDC2026议题预告 | 代码迁移中的安全隐患：Android框架中Java-Kotlin并行实现漏洞的分析与挖掘  
SDC2026
                    SDC2026  看雪学苑   2026-10-02 09:59  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Cpo2XCpI7K1sw4cn14wHsZ9VDFqtpx9GFmgHxrKiaiaAvic7IqT2RV30xqlZlBRRib7b3Z0dLL33hvlrQ738yaZ76gxPpwibffYbVs2ibicStmPib0g/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/Cpo2XCpI7K2zpXFX01pKcOA5Hh59gWiaaLia63QozkhzkXQDDia8WtVQlhYgvlDShkCjstFqeqbiaLicxpJouQiaAuTUqyDpCRca3CgcIic2h93KAM/640?wx_fmt=gif&from=appmsg "")  
  
  
**一.议题简介**  
  
  
  
**《代码迁移中的安全隐患：Android框架中Java-Kotlin并行实现漏洞的分析与挖掘》**  
  
****  
随着Google推进“Kotlin-First”策略，Android操作系统开源项目（AOSP）核心组件正逐步向Kotlin迁移。在此过程中，AOSP中出现了并行实现（Parallel Implementations）现象——即同一系统组件同时存在Java和Kotlin两套代码路径，旨在服务相同的系统功能。尽管在设计原则上，两者追求功能对等；但在实际工程重构中，由于逻辑优化、缺陷修复或采用新语言特性等原因，两套实现往往会出现细微的语义差异（Semantic Divergence）。这些差异本身虽不直接等同于漏洞，却为揭示系统底层访问控制与安全校验逻辑缺陷提供了关键线索。  
  
  
然而，精准捕获这类差异面临着双重挑战：一方面，Java-Kotlin并行实现可能位于不同的包中、采用不同的命名规范，或者独立演进，定位它们并非易事；另一方面，Java与Kotlin存在显著的语法与编码习惯差异，传统程序分析方法难以精准对齐跨语言语义。针对上述挑战，本议题将**探讨如何结合程序分析与大语言模型（LLM）的语义推理能力，精准识别跨语言并行实现，并自动化检测与分析由语言迁移暴露出的安全漏洞。**  
  
  
本议题将**分享自动化分析工具ParaDroid的设计架构及其实际应用效果，并现场演示本地未授权应用利用跨语言语义差异实现特权提升的实际攻击案例，**  
旨在为跨语言代码迁移与重构过程中的安全防护提供参考与借鉴。  
  
  
**二.演讲嘉宾：李蕊**  
  
  
  
**新加坡管理大学安全、移动应用与密码中心，研究科学家。**  
博士毕业于山东大学。主要研究方向为移动操作系统安全、程序分析与系统漏洞挖掘。在 IEEE S&P、USENIX-SEC、ACM CCS 等国际顶级信息安全学术会议发表多篇学术论文。长期从事Android操作系统底层框架与系统服务安全研究，累计挖掘多个系统高危级安全漏洞，并多次获得Google Android安全团队官方致谢与安全漏洞奖励。曾受邀在补天白帽大会、InForSec“移动互联网安全”论坛等行业峰会发表技术演讲。  
  
  
**三.听众收获**  
  
  
  
1. 跨语言迁移风险：Java–Kotlin并行实现中的语义差异与安全隐患  
  
2. 创新方法：程序分析与LLM相融合的跨语言语义差异自动定位  
  
3. 实战案例：跨语言语义差异所暴露的Android提权漏洞  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/Cpo2XCpI7K1Zic8c8uDCXiaInDmev4NE0Mmz8EthdHkEEwSFbDjtffiaN4j5mYUXqiaicLWiayu9u0lVaOibpDHFpm8e5Fm1uibWY5QD1x3eIQlTjpM/640?wx_fmt=jpeg&from=appmsg "")  
  
报名参会  
  
来SDC现场解锁更多议题细节  
  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/Cpo2XCpI7K3R0y1wgfQEEM9rprldGKQCianHjCowGzJia3lL92WdsAgsgkfXjMrqlnexpp9OT58e59KWkjC2enX992XIH8K7dBDPWNS1bMkjE/640?wx_fmt=jpeg&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/Cpo2XCpI7K0t0ibuEvibYB4sBT8bBGDoCCb6BP8XgX3J6xyysWS2xtSGrJ0cJkq3TA9vf7Ot5Uy0TQXdNjWibtjHXJEYny6SufeD9TzkgAHqHQ/640?wx_fmt=gif&from=appmsg "")  
  
**球分享**  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/Cpo2XCpI7K0RibwjwZicqCsQJyWjCzypV8JgicDNnjZHbpEmpFQtj8BGqeF5XP5w76pp6Eof45ibzmmWPYA0mZl9qE48SjN44sr88Lkib8bricKVU/640?wx_fmt=gif&from=appmsg "")  
  
**球点赞**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/Cpo2XCpI7K1qXpjVUd0GwqR6t7UCmbWX8sEoRib8E4bX0JU9tQOtNLwdJYxyRNKJSaTLvhI0gYoQK4OILJkohgXiaT65bGEGqUHF0dOLghU8o/640?wx_fmt=gif&from=appmsg "")  
  
**球在看**  
  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/Cpo2XCpI7K2NayDVS62ICzHO4wuyNq4SPMLTLMQFV12zEiajNa9JZzibjMjGhW2icnGgJ5qs2T6MfQff94EhtdnHAmQ7qNaFTR8CiaP2byUBP3E/640?wx_fmt=gif&from=appmsg "")  
  
点击阅读原文，报名参会  
  

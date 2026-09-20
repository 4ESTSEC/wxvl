#  CRA漏洞上报实战：从漏洞研判到24小时报告，企业应该怎么做？  
原创 唐子雄
                    唐子雄  海云安   2026-09-20 09:30  
  
2026年9月11日起，欧盟《网络韧性法案》（Cyber Resilience Act，CRA）第14条规定的漏洞和严重安全事件报告义务正式适用。对于在欧盟市场提供含数字元素产品的制造商，一旦知悉产品存在被主动利用的漏洞（Actively Exploited Vulnerability，AEV）  
，或知悉发生了严重安全事件，就可能进入24小时报告流程。CRA第14条同样适用于2027年12月11日前已投放欧盟市场的相关产品，存量产品也在报告义务范围内。  
为承接这一义务  
，  
ENISA建设的CRA单一报告平台（Single Reporting Platform，SRP）已  
于9月11日  
正式  
投入运行  
。  
  
实际发生漏洞时，企业首先面对的往往不是“去哪里填表”，而是：这个漏洞要不要报？哪些产品受影响？第三方组件已经在外部被攻击，自己的产品也用了它，是不是就必须报告？24小时又从什么时候开始算？  
这些问题若没有提前理清，最耗时的往往不是最后的SRP填报，而是产品定位、技术研判和内部决策。  
  
为便于说明第三方组件漏洞的判断逻辑，文章第三部分以曾在全球造成广泛影响的Log4Shell（Apache Log4j2远程代码执行漏洞，CVE-2021-44228）为例，按照现行CRA规则，完整走一遍从漏洞发现到监管报告的过程。  
  
**1**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Pic3rpoCqyn5L8xP0g0hn3uejgJnLtGlTRkVBsEkWPlDJA4R52Cq8d7CBtfT7LBrY004oaQ12of1jBgwbUib41DHJYVwtSF2CJAib6CW597dCU/640?wx_fmt=png&from=appmsg "")  
  
发现高危漏洞，不等于马上触发CRA报告义务  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Pic3rpoCqyn7L09MQF7K9dcv8Fib1icxyU8CwnoH2jFdO6IwLcxicibZwjQTmOATpz5Ty8nMviaxvGLnzQ2NIa6zWMOxhMLGgB0rI792qcfrCc1Gw/640?wx_fmt=png&from=appmsg "")  
  
  
  
CRA第14条强制报告主要针对两类情况：AEV，以及对含数字元素产品安全产生严重影响的事件（Severe Incident，SI）。本文重点讨论AEV。  
  
CRA对AEV的定义强调的是实际恶意利用  
：有可靠证据表明，恶意行为者已经在未经系统所有者许可的情况下实际利用该漏洞。  
因此，发现漏洞不等于AEV，  
即使渗透测试或实验室测试已经证明漏洞“可以被利用”，如果没有可靠证据证明攻击者实际利用过它，也不能仅凭漏洞严重程度把它直接认定为AEV。  
  
欧盟委员会《CRA实施FAQ》第5.2条对零日漏洞也采用了同样的判断逻辑：由安全研究人员、漏洞奖励计划或受制造商委托的测试活动发现的漏洞，如果没有既往恶意利用证据，不会自动成为强制报告对象。  
  
所以，判断一个漏洞是否属于AEV，首先要看：是否已有可靠证据证明攻击者曾在现实环境中实际利用过这个漏洞。  
仅有漏洞发现、PoC或实验室复现，并不足以构成AEV。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Pic3rpoCqyn7SEn2P5kIt4mGOePyMcbLiblr9Z0S0tuhFDa68rJwicy28TrmZEthaPtG7ZJzicUJH1CibIZDhwvCM6sMbmCEQosF4v3KLjU9mgRA/640?wx_fmt=png&from=appmsg "")  
  
【图1：CRA漏洞上报（AEV）判断逻辑】  
  
2  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Pic3rpoCqyn6IrUWuc0rJftjw9HKg4jHz4ZL75OQTXQBymR7TDS6ep4Fz1AHVBBdUOibHc2siaklBibWIdha9eDkaDk4O23I4MBSJZUEeCjVaIY/640?wx_fmt=png&from=appmsg "")  
  
24小时从什么时候开始？  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Pic3rpoCqyn4JBv6ia8l8vdXbmJvLic6NJwacAom1PqicLVMHSBGh52NTFIJzObkQBAfnmw34bfNPrHduHxicxZK4ib6ZIKzRtdJYlpp5LuMicNeNY/640?wx_fmt=png&from=appmsg "")  
  
  
  
CRA第14条以制造商“知悉”（becoming aware）作为报告期限的起算点。24小时早期预警和72小时漏洞通报都从这一时点起算  
。  
本文把这个时点简称为T0  
，T0只是本文为便于讨论使用，SRP中对应的  
正式  
字段是“知悉日期/时间”（Date/Time when you became aware）。  
  
CVE首次公开时间、媒体首次报道时间、SCA首次告警时间，或某位研发人员首次收到漏洞邮件的时间，不  
能机械的认定为  
T0  
，  
关键要看企业实际掌握了哪些信息、完成了哪些研判。欧盟委员会2026年7月发布的CRA实施指南进一步提出  
：  
制造商检测到可疑情况或收到第三方信息后，应及时开展初步评估；当其对“产品中存在AEV”形成合理程度的确定性（reasonable degree of certainty）时，即构成CRA意义上的“知悉”。  
  
企业内部可以把这一过程  
拆解  
为：  
  
漏洞情报 → 产品关联 → 可利用性分析 → 主动利用证据研判 → 形成“产品中存在AEV”的认识。  
  
最后一步形成“知悉”的时点即为T0。企业应当同步记录该时点及判断依据  
。  
  
制造商  
可以  
通过多种渠道获知相关信息，  
欧委会详细列举了相关渠道，  
包括  
：  
客户或合作伙伴通报、安全研究机构披露、政府机构通知、威胁情报、内部监控、扫描和遥测等。  
  
3  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Pic3rpoCqyn4LcISAnT9zjH7yicd4OYXSSiaz1v12hcicR26WOAGCyDCouqGysCIQx8QEvYicq9xHETlU1OoQy5WZ1qgYjPMnxVo8ibrbvNTCvI2w/640?wx_fmt=png&from=appmsg "")  
  
以Log4Shell为例：第三方组件漏洞什么时候需要报？  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Pic3rpoCqyn6JfNPt4KxC5aHyDE1L0A3iccMRfun60AiakRYNV7Jv8Lrxl3yEqFsmehbjP9upwpMG39IFlFOroD7uB9tNeFYYy3u3jp9JTsUC4/640?wx_fmt=png&from=appmsg "")  
  
  
  
2021年12月，Apache Log4j2中的CVE-2021-44228被公开披露，即广为人知的Log4Shell。随后，CISA及其他安全机构确认该漏洞已经出现在实际攻击活动中。制造业同样受到影响  
，  
Siemens ProductCERT于2021年12月13日发布SSA-661247安全公告，  
并在公告后续版本中持续更新各产品的受影响状态、修复版本和缓解措施  
。  
  
这里先说明一点  
：Log4Shell发生于2021年，当时CRA尚未实施。下面只是借助这一真实漏洞，按照现行CRA规则进行复盘式推演。  
  
对第三方组件漏洞，还需要再做一层产品判断：这个已经被确认主动利用的漏洞，是否实际存在于自己的产品中，并且在产品环境中能够被利用。  
这里需要区分两个概念：“已经被利用”和“在自己的产品中可被利用”  
。前者决定漏洞是不是AEV，后者决定这个AEV是否落入自己的产品报告范围。  
  
1.外部已经确认主动利用，仍需判断自身产品  
  
  
假设今  
后  
再出现类似Log4Shell的大规模第三方组件漏洞，各国CERT等可信机构已经确认该漏洞正在被攻击者实际利用。  
这回答了AEV判断中的第一个问题：该漏洞已有可靠的恶意利用证据。  
  
但对具体制造商而言，还有一个关键问题没有回答：  
  
这个漏洞是否实际存在于自己的产品中，并且可被利用？  
  
欧盟委员会《CRA实施FAQ》第5.4条专门解释了第三方组件场景：如果最终产品集成的第三方组件存在AEV，且该漏洞在最终产品中实际可被利用，最终产品制造商同样承担报告义务；反之，如果该漏洞在最终产品的实际环境中无法利用，就不能仅因该组件正在外部遭到利用，直接认定该产品构成必须报告的AEV。所以，在“  
Log4Shell在攻击中被利用  
”和“我的产品中已存在需要报告的AEV”之间，还需要完成产品影响范围确认和可利用性判断。  
  
2.快速确认哪些产品包含受影响组件  
  
  
此时制造商面对的第一项技术工作，是把这个组件漏洞对应到自己的具体产品上。需要快速确认：哪些产品使用了Log4j，使用的是哪个版本，该版本位于哪个软件或固件模块，以及哪些产品版本已投放欧盟市场。  
  
Siemens当年的Log4Shell公告正好说明了这种现实：重大第三方组件漏洞发生后，制造商需要持续调查各产品的实际影响，并不断更新受影响状态、修复方案和缓解信息。  
  
对产品线较多的企业来说，这一步直接考验产品资产清单是否清晰，以及SBOM和SCA能力是否到位。如果重大漏洞发生后，还需要让多个研发团队逐个翻代码、查依赖、找历史版本，仅仅确认产品影响范围就可能消耗大量时间。  
  
 3.产品包含受影响版本，也不等于一定需要报告  
  
  
即使已经确认某款产品包含受Log4Shell影响的Log4j版本，还需要分析漏洞在具体产品环境中是否实际可以利用。例如，要确认漏洞代码路径是否被使用、攻击路径是否能够触达、产品配置是否满足利用条件，以及现有安全控制是否已经阻断利用。  
  
这里最容易出现的误解是：CRA不是要求制造商等到自己的客户设备也出现攻击日志以后才报告。  
  
如果已有可靠证据证明该漏洞在外部遭到恶意利用，同时该漏洞确实存在于自己的产品中、且在产品环境中实际可被利用，那么制造商就应当认定自己已“知悉”产品中存在AEV，并据此启动报告，而不是继续等待自家设备出现一次真实攻击  
。  
  
第三方组件AEV的判断链可以概括为：  
  
外部已有可靠恶意利用证据→ 自身产品包含受影响组件或代码→ 漏洞在自身产品中实际可利用→ 形成“产品中存在AEV”的认识，即T0→ 启动24小时报告  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Pic3rpoCqyn7iahlQbAVuFQv2iaz5gqXia2PLlWIqPVoupahiab2ntfTYuhzQjE1viaqm5sd9syXQ2ILyMnzzhYzQiagedLSraicnaYdVCUUL03jjQQ/640?wx_fmt=png&from=appmsg "")  
  
【图2：  
第三方组件  
CRA漏洞判断与上报流程】  
  
4.进入报告流程后，调查、修复和报告要并行  
  
  
达到“知悉”标准后，企业应留存漏洞情报、受影响产品和版本、组件分析、可利用性分析以及内部研判记录。此时不能等所有技术调查结束后再准备监管报告。  
  
CRA采用24小时、72小时和最终报告的分阶段机制，  
已  
考虑到制造商在事件早期不可能掌握全部事实。第14条要求制造商在知悉AEV后24小时内提交早期预警，72小时内提交进一步的漏洞通报；最终报告则在纠正或缓解措施可用后14天内完成。因此，进入报告范围以后，漏洞调查、修复处置和监管报告应并行推进  
。  
  
4  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Pic3rpoCqyn5iaSrMeScNKQ2ibzqq9UMNLVKNe5XMTQ559AVCknfWSA6kEHJRAT86qIc7ZSwZGBbUXWfEFRzStZdCzbVM43EV43tnInPuzYeTk/640?wx_fmt=png&from=appmsg "")  
  
进入SRP：企业需要提前准备什么？  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Pic3rpoCqyn6xzNibHUDwDdhyXkclOFbTgt1OzPBazXOJibCT7Rr2VhtCmUVAaJwrujOXfkTiaEmsxpLW3ovdXwX1ibEDI2jkUjwm8aQjQwPfZ8E/640?wx_fmt=png&from=appmsg "")  
  
  
  
ENISA建设的CRA  
 SRP  
已正式运行，AEV和严重安全事件的强制报告均通过SRP提交。  
  
1. Assigned Representative（AR）  
  
  
SRP把代表制造商操作平台的人员称为平台指定代表（Assigned Representative，AR），分为Primary AR和Secondary AR两类。这一称谓很容易与CRA中的授权代表（Authorised Representative）混淆，但二者不是同一个概念：前者是SRP的平台操作角色，后者是CRA中的经济运营者角色。每家制造商只能配置1名Primary AR，最多配置20名Secondary AR；两类角色都可按各自权限提交和更新通报。  
  
  
2. EU Login和MFA可以提前准备  
  
  
SRP使用个人EU Login账号，并要求启用多因素认证（MFA）。ENISA目前建议，EU Login和MFA可以提前准备，但制造商不必为了“占坑”提前完成SRP关联验证。需要提交报告时再注册并启动验证，验证可以与报告流程并行。企业更应提前准备的是人员、账号、内部职责和管辖判断。  
  
  
3. 非欧盟制造商应提前确定协调CSIRT  
  
  
制造商需要在SRP中选择正确的协调CSIRT（CSIRT Designated as Coordinator，CDaC）。对于没有欧盟主要营业地的制造商，CRA和ENISA给出了明确顺序：先看授权代表所在成员国，其次是进口商，再其次是经销商，最后才是产品用户数量最多的成员国。这个问题需要提前确认。ENISA最新FAQ明确，如果选择错误的CDaC，通报可能被判定无效，需要重新提交。  
  
  
4. SRP提交入口  
  
  
进入SRP后即可创建新的报告记录。Early Warning提交后，72h Notification和Final Report继续在同一条记录中补充，不需要为三个阶段分别创建三条独立记录。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Pic3rpoCqyn6oekyKfpMV6icvM0InfVMic5HK0bszrnDMFLoYibS23ZsI7IyRlCzcribwrkX1PJJjPbnibrVQgzEk9tUQVyuzg1RQZacxEeuYRSeQ/640?wx_fmt=png&from=appmsg "")  
  
【图3：ENISA SRP Submit new notification】  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Pic3rpoCqyn6p3k9GqQcCdwmZCencuyChEaRGr9D0Tic8FKnKUeaEd30dDD1Uial3b2fcUAJWytFveb4P72T4LfwZuluxGrQzFpnWuoOic8uaqs/640?wx_fmt=png&from=appmsg "")  
  
【图4：ENISA SRP Early Warning填报】  
  
具体字段应以ENISA最新User Manual、Glossary和Guidance为准。  
  
5  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Pic3rpoCqyn41INGeyvibqrYM790cPga1SUta6eeqj7dibxPoicHujHicBk7iaTqCiaZclb3X7tqM4LtuRyqRTDnBhfnmXCAcrnxn1ibbeiaOVfiavYh8/640?wx_fmt=png&from=appmsg "")  
  
72小时、最终报告和用户告知  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Pic3rpoCqyn7GLBHv2bUTeL4IWuhsyC3iaEws1UrNAOsibXq3ibZAUzo4KLrJs2qL9ssNXsxtD0po0qGhLxzTZIIZdQ67enJxMiawyctsUEZq0Cs/640?wx_fmt=png&from=appmsg "")  
  
  
  
AEV监管报告时间线：  
  
T0 → 24小时内：早期预警（Early Warning） → 72小时内：漏洞通报（Vulnerability Notification） → 纠正或缓解措施可用后14天内：最终报告（Final Report）  
  
其中，24小时和72小时都从制造商“知悉”AEV的时点起算。  
  
24小时阶段主要用于快速预警；72小时阶段进一步补充产品、漏洞性质、影响和已有措施；Final Report再补充完整分析和整改信息。如果报告的是严重安全事件SI，最终报告应在72小时事件  
通报  
之后1个月内提交。  
  
除SRP报告外，CRA第14(8)条还规定了用户告知义务  
。制造商知悉AEV或严重安全事件后，应当告知受影响用户，并在必要时提供风险缓解和纠正措施。  
  
因此，一次完整的CRA漏洞响应并不是“  
完成  
SRP  
报告  
”  
就算结束  
，还需要把监管报告、漏洞修复和用户沟通纳入同一个事件响应流程统筹考虑  
。  
  
6  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Pic3rpoCqyn7SicV2y0Cibe9mW5wQwRj8FWkAoxLZxk09J2O95YDVLtkcL7ANnY63ibHibx1VyVUKyW5otkWMLmra0xicDkGrEzcBibTUvUh2iaFIV8/640?wx_fmt=png&from=appmsg "")  
  
企业需要建立的是一条可运行的漏洞响应链  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Pic3rpoCqyn5ibGtCLiaVMXIDQaEvT2KDkia9lZdGK3u7SndibuW0O5hdWluibXpKSh7ZkuAUeMOIMHuvntqmPWialAFhpIDico3X2qvPETz43WI7MU/640?wx_fmt=png&from=appmsg "")  
  
  
  
如果再次出现Log4Shell级别的供应链漏洞，企业至少需要快速完成四件事。  
  
定位  
：依靠产品资产清单、SBOM和SCA，识别哪些产品包含受影响组件。  
  
验证  
：通过技术分析、漏洞验证和必要的渗透测试，确认漏洞在具体产品环境中是否可利用。  
  
定性  
：结合可信漏洞通告和威胁情报，判断是否存在可靠的主动利用证据，并确定何时达到CRA意义上的“知悉”。  
  
响应  
：依靠明确的PSIRT流程、T0记录、报告职责、SRP提交和整改复测安排，在24小时窗口内推动技术处置和监管报告同步开展。  
  
其中，SCA和SBOM对第三方组件风险最直接；SAST更适合用于自研代码漏洞发现和前置治理。  
  
围绕CRA落地，海云安  
可以提供CRA合规咨询  
与建设  
、SCA及软件成分风险分析、SAST、渗透测试等技术支持，也可以协助企业建立漏洞报告流程  
。  
  
7  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Pic3rpoCqyn4jCdzdjtxHCVEGc5VPDZphFRnG0FEpDKAyrCSsMzruoZ9MeuSUwPOZPFYZmS6vgKa65XkdfuibfT66HZ0WnTE8mNNicRkzK3ibfQ/640?wx_fmt=png&from=appmsg "")  
  
结语  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Pic3rpoCqyn5Qw9nrty7sSys9Vhv4SAicqHOHNeMmlfiajI5XicBMJrAa1LJ8Pd5C4owTXxQCqsA752Yu8Tn5ibwQCwPxicyvdtLT5BuQ1leuh8Kk/640?wx_fmt=png&from=appmsg "")  
  
  
  
Log4Shell已经过去几年，但类似的大规模供应链漏洞不会消失。  
  
如果  
类似  
事件再次发生，进入欧盟市场的制造商需要迅速判断：产品是否受影响、漏洞  
在产品中  
是否可利用、何时达到CRA意义上的“知悉”，以及在24小时内完成早期预警。  
  
CRA第14条考验的不是一张SRP表单，而是企业能否在有限时间内完成判断，并让技术处置、内部协作、监管报告和用户沟通同步运转。  
  
**END**  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/Pic3rpoCqyn5UsTfluXVmMAVuibVpHb5ygM1TtYtTs8XTb2aYBCibnSyIicGCz8clwzRybUFibsWZv6FMsJPe3pPbxlfIhoOzoNHFFpOX6egXFMU/640?wx_fmt=jpeg&from=appmsg "")  
  

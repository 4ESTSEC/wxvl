#  Cisco SD-WAN 0day 被利用数月：边界控制面，不能只等补丁  
原创 tcode
                    tcode  字节脉搏实验室   2026-06-26 03:20  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/nOo5YmK1PHyx0e4cicEDCcMknZNUUicTbiaxFxibaicNgasicRib8yib35JGJdhqEuALEPtJNHKk7Cql9XnNGAVJZEGHRuAQRIiaxTUavS80xg3k8gCM/640?wx_fmt=png&from=appmsg "")  
  
事件概述  
  
    过去一天，  
Google Cloud/Mandiant   
对  
 Cisco Catalyst SD-WAN   
相关漏洞  
 CVE-2026-20245   
的在野利用细节做了公开说明；多家安全媒体随后跟进报道。这个漏洞并不是传统意义上  
“  
普通业务系统里的一处缺陷  
”  
，它位于  
 SD-WAN   
控制与管理体系中，攻击者一旦在前置条件满足时完成提权，影响的是网络管理层面的可信边界。  
  
    更值得警惕的是时间线。  
Mandiant   
的调查显示，相关活动并非补丁发布后才出现的跟风扫描，而是在公开披露前已经发生。换句话说，防守方看到公告时，部分真实环境里的对抗已经跑在前面。  
  
    这类事件的重点不在于  
“  
某一个  
 CVE   
有多吓人  
”  
，而在于它再次说明：边界设备、控制器、管理平台和远程接入系统，已经成为高级攻击者长期盯住的入口。它们往往拥有高权限、高信任和低频变更的特点，一旦被突破，后续排查难度会明显高于普通终端。  
## 核心事实  
  
    事实一：  
Cisco   
官方安全公告将  
 CVE-2026-20245   
归类为高危漏洞，涉及  
 Catalyst SD-WAN Controller  
、  
Manager   
和  
 Validator   
等组件。官方公告显示该问题已发布修复版本，且没有可替代补救措施。  
  
    事实二：  
Mandiant   
披露，攻击者曾在特定环境中利用该漏洞完成权限提升，并在事后进行清理，以降低被管理员发现的概率。这里不展开任何可复现的技术路径、命令或样本细节；对企业防守来说，更重要的是确认是否存在受影响版本、异常管理员行为和管理面暴露。  
  
 事实三：  
The Hacker News  
、  
Security Affairs   
等媒体在  
 2026   
年  
 6   
月  
 25   
日跟进报道，将焦点放在  
“  
公开披露前已被利用  
”  
和  
“  
控制面风险  
”  
上。这与近年来边界设备  
 0day   
事件的共同趋势一致：攻击者更愿意攻击安全团队难以及时重装、难以频繁巡检、又拥有关键权限的位置。  
## 影响分析  
  
    对大型企业而言，  
SD-WAN   
管理平台不是一台孤立服务器。它连接分支网络、云上资源、总部出口和策略下发机制。如果攻击者取得更高权限，风险可能从单点设备扩大到网络策略、配置完整性和跨区域运维信任。  
  
    对服务提供商、托管网络服务商和多租户网络环境而言，影响面还会更复杂。攻击者不一定需要立刻造成中断；他们可能更倾向于读取配置、观察网络拓扑、维持隐蔽访问，或者等待后续行动窗口。这也是为什么  
“  
没有业务中断  
”  
不能等同于  
“  
没有入侵  
”  
。  
  
    对安全运营团队而言，这件事带来的直接压力是：补丁管理不再只是资产版本表的工作，还要和日志留存、管理员账号治理、管理接口隔离、变更审计结合起来。尤其是控制面设备，一旦日志保留不足或管理员账号共用，事后判断会很被动。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/nOo5YmK1PHyicT9bjeZNN2ibl1VDShnW8vOriaV1Y90XHs3DyYAIFicKcGd65Fh4ovnCiaUJACar3IHQ48g0ia96c63e1WHWlUl6ErWGhibYEkbgdA/640?wx_fmt=png&from=appmsg "")  
## 企业应对建议  
  
    第一，立即确认资产范围。梳理所有  
 Cisco Catalyst SD-WAN Controller  
、  
Manager  
、  
Validator  
，以及由第三方托管或云托管的相关实例。不要只查采购清单，要让网络团队、运维团队和托管服务商共同确认。  
  
    第二，按官方公告升级到修复版本。若企业采用托管或云托管模式，需要向服务商确认修复时间、版本状态和是否存在异常访问排查记录。不能仅以  
“  
服务商会处理  
”  
为默认结论。  
  
    第三，收缩管理面暴露。管理接口应限制来源地址，使用专用管理网络、跳板机、强身份认证和最小权限账号。默认账号、长期共用管理员账号、离职人员权限和临时外包权限，都应纳入复查。  
  
    第四，做一次针对性狩猎。重点看管理员登录、异常配置变更、短时间内的权限变化、非计划维护窗口操作、系统日志缺口，以及与管理面相关的异常外联。不要只依赖单一告警，最好由网络、终端、身份和日志平台交叉验证。  
  
    第五，轮换关键凭据。若存在受影响版本暴露在风险窗口内，建议评估是否需要轮换本地管理员密码、  
API   
凭据、集成账号、备份账号和自动化运维密钥。控制面被碰过之后，凭据可信度要重新建立。  
## 事实、推测与观点  
  
    可以确认的事实是：  
Cisco   
已发布相关安全公告；  
Mandiant   
披露了该漏洞被用于真实入侵活动；公开报道集中在  
 2026   
年  
 6   
月  
 25   
日前后。  
  
    合理推测是：攻击者选择  
 SD-WAN   
管理与控制组件，可能看中的是高权限、横向影响和排查成本。不同企业的真实风险取决于版本、暴露面、账号治理和日志保留情况。  
  
    我的观点是：这类事件不适合用  
“  
打补丁后结束  
”  
来处理。更成熟的做法是把边界控制面纳入持续攻击面管理，把管理接口、管理员身份和配置变更当作高价值资产来运营。  
## 结语  
  
    这次  
 Cisco SD-WAN   
相关事件给企业的提醒很直接：真正值得担心的，不只是漏洞本身，而是漏洞所在的位置。业务系统被打穿，影响的是一片业务；控制面被打穿，影响的是企业对网络的信任。补丁要快，排查要细，边界管理面更要长期  
“  
少暴露、强认证、可审计  
”  
。  
## 关键来源  
  
·  
  
Google Cloud/Mandiant  
：《  
Zero-Day Exploitation of Vulnerability (CVE-2026-20245) in Cisco Catalyst SD-WAN Manager  
》，  
2026-06-25  
，  
https://cloud.google.com/blog/topics/threat-intelligence/zero-day-exploitation-cisco-catalyst-sd-wan-manager  
  
·  
  
Cisco Security Advisory  
：  
Cisco Catalyst SD-WAN   
相关认证后提权漏洞公告，首次发布  
2026-06-04 22:27 UTC  
，更新至  
 2026-06-12  
，  
https://sec.cloudapps.cisco.com/security/center/content/CiscoSecurityAdvisory/cisco-sa-sdwan-privesc-4uxFrdzx  
  
·  
  
The Hacker News  
：《  
Cisco Catalyst SD-WAN Zero-Day CVE-2026-20245 Exploited to Gain Root Access  
》，  
2026-06-25  
，  
https://thehackernews.com/2026/06/cisco-catalyst-sd-wan-zero-day-cve-2026.html  
  
·  
  
Security Affairs  
：《  
Cisco Catalyst SD-WAN Zero-Day CVE-2026-20245 Exploited Months Before Disclosure  
》，  
2026-06-25  
，  
https://securityaffairs.com/194200/hacking/cisco-catalyst-sd-wan-zero-day-cve-2026-20245-exploited-months-before-disclosure.html  
  
  

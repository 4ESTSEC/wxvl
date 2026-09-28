#  Citrix NetScaler曝出0Day危机，两处RCE漏洞已遭实战利用  
 FreeBuf   2026-09-28 10:00  
  
![FreeBuf](https://mmbiz.qpic.cn/mmbiz_gif/icBE3OpK1IX1SPE6geNanicic4e06bRiavgoiaGDQcOibXAiagEF8cDfTGgqQK2sQ2RdmZR5UKS7BWreGKiaZicpNenRia1HpahsIzkdN14pjybjbD9sk/640?wx_fmt=gif "")  
  
  
![image](https://mmbiz.qpic.cn/sz_mmbiz_png/icBE3OpK1IX1ocYyWUZ4snh0zjAyFg7ASuwzTD4HuB4p4ZPUz7ntdBaqBgtU4wYeVvHmUgJ8BF5GibDvPwtiau7bYibma3I7BJd4A4dFdJUHROk/640?wx_fmt=png "")  
  
  
Part  
01  
  
两起RCE 0Day遭在野利用  
  
  
Citrix NetScaler管理员近日收到安全通报，两款未公开的RCE漏洞已出现真实在野利用活动。  
  
  
watchTowr表示，这两个漏洞属于未修复的0Day，研究人员在取证调查过程中发现了相关利用痕迹，预计Citrix将在下周初发布官方通报与修复方案。截至撰稿时，Citrix尚未公布这些漏洞的技术细节、CVE编号、受影响版本范围、入侵指标或安全公告，防御人员只能在有限的可靠信息基础上做出高影响决策。  
  
  
本次警报最初源于多份行业报告，称NetScaler存在未修复的RCE漏洞，已出现在野传播活动。watchTowr评估相关情报可信度较高，后续明确表示共有两个独立漏洞可被利用实现远程代码执行。目前watchTowr尚未公开漏洞的利用路径、利用前提、攻击载荷或取证特征，第三方暂时无法独立验证漏洞细节，各机构应将本次预警视为高优先级安全警告，需注意相关信息尚未得到厂商官方的完全确认。  
  
  
据报道，部分机构已率先采取应对措施，关停了面向互联网暴露的NetScaler设备。这类操作可能中断VPN访问、应用交付、身份认证及其他关键业务，但边界设备在网络架构中处于高权限位置，一旦被攻破将造成严重影响。如果防御人员无法为这类存在被利用风险的RCE漏洞安装补丁，或部署可靠的缓解措施，暂时将暴露设备下线可能是更安全的选择，对运行敏感业务的环境而言尤其如此。  
  
  
Part  
02  
  
本次警报独立于8月公告  
  
  
防御人员需注意，本次最新警报与Citrix 8月19日发布的安全公告无关，该公告覆盖CVE-2026-19490与CVE-2026-19489两个已披露漏洞。其中CVE-2026-19490是严重身份绕过漏洞，CVSS v4.0评分为9.3，影响特定客户自主管理的NetScaler Gateway与AAA虚拟服务器配置；CVE-2026-19489的CVSS评分为8.8，属于内存溢出漏洞，只有在大规模NAT组启用SIP ALG功能的场景下才可触发，可导致设备异常运行或拒绝服务。  
  
  
![image](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX1fwYEJDpuo3r0Q6vtYYtfpljIRcYDh27KJ0hB1XywensfUHkOsw2iajMHKZTp5pRicxlmxmZIqdaXpXMjibcMVkNzprzRsnQQm4o/640?wx_fmt=png "")  
  
  
![image](https://mmbiz.qpic.cn/sz_mmbiz_png/icBE3OpK1IX2oEDluv7Ud7EWs2PQ9AoRm6H0tfpV5MIAl16bib5LgEViaR8GLuVhH0JuvEbGOMpPc7jGRxXBA0LIhext0Gdnm8zfpBqCvKLdjU/640?wx_fmt=png "")  
  
  
目前CVE-2026-19490的在野利用活动已得到官方确认。新加坡网络安全局9月7日发布警告称已观测到相关利用尝试，CISA于9月9日将该漏洞加入已知被利用漏洞目录，加拿大网络中心随后呼吁各机构紧急安装补丁，监测未授权访问行为。这些已公开漏洞的处置进展，一度让外界无法确定本次最新警报指向的是已知身份绕过漏洞，还是全新的0Day漏洞。watchTowr后续发布明确说明，本次报告的是两个未修复的全新RCE漏洞。  
  
  
针对8月披露的两个漏洞，Citrix给出了明确修复方案：将NetScaler ADC与Gateway 14.1版本升级至14.1-73.32及以上，13.1版本升级至13.1-63.21及以上；专用版本的修复基线为FIPS版14.1升级至14.1-73.32，FIPS或NDcPP版13.1升级至13.1-37.277。Citrix明确表示这两个漏洞没有临时规避方案，相关公告仅适用于客户自主管理的设备，不适用于Citrix托管的云服务。  
  
  
Part  
03  
  
防御方需立即排查资产  
  
  
在Citrix正式澄清本次RCE漏洞报告、发布官方补丁之前，防御人员应全面清点所有NetScaler实例，确认设备的具体版本与公网暴露面，严格限制管理接口访问权限，在公网接口前部署补偿控制措施。  
  
  
安全团队应妥善留存日志与取证镜像，全面核查认证事件、新建会话、配置变更、异常进程、可疑文件与出站连接异常，在完成完整证据收集前，不要擦除可能已失陷设备的存储数据。无法接受残余风险的机构，应在经过审批的业务连续性流程指导下，隔离或关停暴露在公网的NetScaler设备，同时持续关注Citrix官方安全公告渠道获取补丁与部署指引，不要仅依赖社交媒体上的零散信息。  
  
  
本次事件再次说明，面向互联网的远程访问基础设施，需要具备快速资产发现、经过验证的紧急补丁流程、集中化日志留存与常态化演练的应急响应机制。尤其是在防御方必须在厂商完成完整技术披露前采取行动的场景下，这些基础能力直接决定了防御响应的效果。  
  
  
参考来源：  
  
Citrix NetScaler 0-Day RCE Vulnerabilities Actively Exploited in Attacks  
  
https://cybersecuritynews.com/citrix-netscaler-0-day-rce-2/  
  
###   
  
### 推荐阅读  
  
  
[](https://mp.weixin.qq.com/s?__biz=MjM5NjA0NjgyMA==&mid=2651347479&idx=1&sn=1cf9c858a30d0b7af06d196002bd2913&scene=21#wechat_redirect)  
  
  
###   
  
### 电报讨论  
  
  
[]()  
  
  
  
![扫码加入AI安全交流群](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX0Py7ibxdLKXia1pMziaic5vIE9XPXG9OGaeJDa07iaG10eicuzhW59nwpF5msHiaYZvfMqCNkx2aFDiaMzm3oAf4rTaHXU5UAI1mUYgts/640?wx_fmt=png "")  
  
  
  
![下载FreeBuf知识大陆APP](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX0TIGzII2Hcmtzu7AJeZFicnqd1mXojVoawje2uLxYqwJbVgzJpmSXzVhrpOsLurRZ2lVa4vfgLBqg7uJKbrKg5F18VzZxVPicZU/640?wx_fmt=png "")  
  
  
  
  

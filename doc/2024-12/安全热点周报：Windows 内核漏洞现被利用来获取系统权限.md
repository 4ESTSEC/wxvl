#  安全热点周报：Windows 内核漏洞现被利用来获取系统权限   
 奇安信 CERT   2024-12-23 09:55  
  
<table><tbody style="-webkit-tap-highlight-color: transparent;outline: 0px;visibility: visible;"><tr bgless="lighten" bglessp="20%" data-bglessp="40%" data-bgless="lighten" style="-webkit-tap-highlight-color: transparent;outline: 0px;border-bottom: 4px solid rgb(68, 117, 241);visibility: visible;"><th align="center" style="-webkit-tap-highlight-color: transparent;outline: 0px;word-break: break-all;hyphens: auto;border-width: 0px;border-style: none;border-color: initial;background-color: rgb(254, 254, 254);font-size: 20px;line-height: 1.2;visibility: visible;"><span style="-webkit-tap-highlight-color: transparent;outline: 0px;color: rgb(68, 117, 241);visibility: visible;"><strong style="-webkit-tap-highlight-color: transparent;outline: 0px;visibility: visible;"><span style="-webkit-tap-highlight-color: transparent;outline: 0px;font-size: 17px;visibility: visible;">安全资讯导视 </span></strong></span></th></tr><tr data-bcless="lighten" data-bclessp="40%" style="-webkit-tap-highlight-color: transparent;outline: 0px;border-bottom: 1px solid rgb(180, 184, 175);visibility: visible;"><td align="center" valign="middle" style="-webkit-tap-highlight-color: transparent;outline: 0px;word-break: break-all;hyphens: auto;border-width: 0px;border-style: none;border-color: initial;font-size: 14px;visibility: visible;"><p style="-webkit-tap-highlight-color: transparent;outline: 0px;visibility: visible;">• 国务院审议通过《公共安全视频图像信息系统管理条例（草案）》</p></td></tr><tr data-bglessp="40%" data-bgless="lighten" data-bcless="lighten" data-bclessp="40%" style="-webkit-tap-highlight-color: transparent;outline: 0px;border-bottom: 1px solid rgb(180, 184, 175);visibility: visible;"><td align="center" valign="middle" style="-webkit-tap-highlight-color: transparent;outline: 0px;word-break: break-all;hyphens: auto;border-width: 0px;border-style: none;border-color: initial;font-size: 14px;visibility: visible;"><p style="-webkit-tap-highlight-color: transparent;outline: 0px;visibility: visible;">• CNCERT披露两起美对我大型科技企业机构网络攻击事件</p></td></tr><tr data-bcless="lighten" data-bclessp="40%" style="-webkit-tap-highlight-color: transparent;outline: 0px;border-bottom: 1px solid rgb(180, 184, 175);visibility: visible;"><td align="center" valign="middle" style="-webkit-tap-highlight-color: transparent;outline: 0px;word-break: break-all;hyphens: auto;border-width: 0px;border-style: none;border-color: initial;font-size: 14px;visibility: visible;"><p style="-webkit-tap-highlight-color: transparent;outline: 0px;visibility: visible;">• 产品漏洞被利用致大量用户数据泄露，Meta被罚超19亿元</p></td></tr></tbody></table>  
  
**PART****0****1**  
  
  
**漏洞情报**  
  
  
**1.Apache Tomcat远程代码执行漏洞(CVE-2024-56337)安全风险通告**  
  
  
12月21日，奇安信CERT监测到官方修复Apache Tomcat远程代码执行漏洞(CVE-2024-56337)， 该漏洞是由于CVE-2024-50379修复不完善，在不区分大小写的文件系统（如Windows）上，readonly参数被设置为false（非默认配置）且系统属性sun.io.useCanonCaches为true（Java 8或Java 11默认为true、Java 17默认为false、Java 21及更高版本不受影响），攻击者就可以上传含有恶意JSP代码的文件。通过不断地发送请求利用条件竞争，使得Tomcat解析并执行这些恶意文件，从而实现远程代码执行。目前该漏洞POC已在互联网上公开，奇安信威胁情报中心安全研究员已成功复现，鉴于此漏洞影响范围较大，建议客户尽快做好自查及防护。  
  
  
**2.Apache Tomcat远程代码执行漏洞(CVE-2024-50379)安全风险通告**  
  
  
12月18日，奇安信CERT监测到官方修复Apache Tomcat远程代码执行漏洞(CVE-2024-50379)，该漏洞是由于Tomcat在验证文件路径时存在缺陷，如果readonly参数被设置为false（这是一个非标准配置），并且服务器允许通过PUT方法上传文件，那么攻击者就可以上传含有恶意JSP代码的文件。通过不断地发送请求，攻击者可以利用条件竞争，使得Tomcat解析并执行这些恶意文件，从而实现远程代码执行。目前该漏洞POC已在互联网上公开，奇安信威胁情报中心安全研究员已成功复现，鉴于此漏洞影响范围较大，建议客户尽快做好自查及防护。  
  
  
**PART****0****2**  
  
  
**新增在野利用**  
  
  
**1.****BeyondTrust 特权远程访问 (PRA) 和远程支持 (RS) 命令注入漏洞(CVE-2024-12356)**  
  
  
12月19日，CISA公开了特权访问管理公司 BeyondTrust 在12月初遭受网络攻击，威胁行为者入侵了其部分远程支持 SaaS 实例。  
  
BeyondTrust 是一家网络安全公司，专门提供特权访问管理 (PAM) 和安全远程访问解决方案。其产品被政府机构、科技公司、零售和电子商务实体、医疗保健组织、能源和公用事业服务提供商以及银行业使用。  
  
该公司表示，2024 年 12 月 2 日，它在其网络上检测到了“异常行为”。初步调查证实，威胁行为者入侵了其部分远程支持 SaaS 实例。经过进一步调查，发现黑客获得了远程支持 SaaS API 密钥的访问权限，从而可以重置本地应用程序帐户的密码。公告中写道：“BeyondTrust 发现了一起涉及有限数量的远程支持 SaaS 客户的安全事件。”  
  
2024年12月5日，对远程支持 SaaS 问题的根本原因分析发现，远程支持 SaaS 的 API 密钥已被泄露。BeyondTrust 立即撤销了 API 密钥，通知了已知受影响的客户，并在同一天暂停了这些实例，同时为这些客户提供替代的远程支持 SaaS 实例。  
  
目前尚不清楚威胁行为者是否能够利用受损的远程支持 SaaS 实例来攻击下游客户。  
  
BeyondTrust 表示，他们已自动在所有云实例上应用了针对这个漏洞的补丁，但运行自托管实例的用户需要手动应用安全更新。最后，该公司指出，对该安全事件的调查仍在进行中，当有更多信息时，将在其页面上提供更新。  
  
   
  
参考链接：  
  
https://www.bleepingcomputer.com/news/security/beyondtrust-says-hackers-breached-remote-support-saas-instances/  
  
  
**2.********Cleo 多款产品任意文件上传漏洞(CVE-2024-55956)******  
  
  
12月17日，Clop 勒索软件团伙证实，他们是最近的 Cleo 数据盗窃攻击的幕后黑手，利用被追踪为 CVE-2024-50623 和 CVE-2024-55956 的零日漏洞侵入公司网络并窃取数据。  
  
Cleo 是托管文件传输平台 Cleo Harmony、VLTrader 和 LexiCom 的开发商，公司使用这些平台在业务合作伙伴和客户之间安全地交换文件。  
  
12 月攻击中使用的新漏洞现被追踪为 CVE-2024-55956，并已在 Cleo Harmony、VLTrader 和 LexiCom 5.8.0.24 中修复。  
  
在利用此漏洞时，威胁行为者上传了一个名为“Malichus”的 JAVA 后门，允许攻击者窃取数据、执行命令并进一步访问受感染的网络。  
  
CISA 确认 Cleo Harmony、VLTrader 和 LexiCom 文件传输软件中的关键安全漏洞 CVE-2024-50623 已被用于勒索软件攻击，但未透露任何其他细节。  
  
Rapid7 现已确认 CVE-2024-55956 不是 CVE-2024-50623 的补丁绕过，因为它们利用了 Cleo 端点中的单独问题。Rapid7 的报告中写道：“CVE-2024-50623 和 CVE-2024-55956 都是未经身份验证的文件写入漏洞，原因是 /Synchronization 端点中存在不同的问题。”  
  
因此，CVE-2024-55956 不是CVE-2024-50623 的补丁绕过，而是一个新的漏洞。值得一提的是，虽然 CVE-2024-50623 允许读取和写入任意文件，但 CVE-2024-55956 仅允许写入任意文件。  
  
Clop 勒索软件团伙（又名 TA505 和 Cl0p）于 2019 年 3 月成立，当时它首次使用CryptoMix 勒索软件的变种开始针对企业进行攻击 。与其他勒索软件团伙一样，Clop 会入侵企业网络，并在窃取数据和文件的同时慢慢向系统内部扩散。窃取到所有有价值的东西后，他们会在网络上部署勒索软件来加密设备。  
  
为了缓解这一严重威胁，Cleo 发布了补丁版本 5.8.0.24，该版本解决了 CVE-2024-55956。  
  
  
参考链接：  
  
https://www.bleepingcomputer.com/news/security/clop-ransomware-claims-responsibility-for-cleo-data-theft-attacks/  
  
  
**3.****Adobe ColdFusion任意文件读取漏洞(CVE-2024-20767)**  
  
  
12月16日，CISA 添加了一个严重的 Adobe ColdFusion 漏洞（跟踪为CVE-2024-20767），Adobe 已于3月修复了该漏洞。从那时起，网上已经发布了几个漏洞 PoC。  
  
CVE-2024-20767 是由于访问控制不当导致的漏洞，允许未经身份验证的远程攻击者读取系统和其他敏感文件。根据 SecureLayer7 的说法，成功利用在线暴露管理面板的 ColdFusion 服务器还可以让攻击者绕过安全措施并执行任意文件系统写入。  
  
Fofa 搜索引擎追踪了超过 145,000 台暴露在互联网上的 ColdFusion 服务器，但无法通过远程访问的管理面板精确地定位到具体的服务器。  
  
网络安全机构表示：“这类型的漏洞是恶意网络行为者频繁攻击的媒介，对企业构成重大风险。”  
  
建议受影响用户升级至最新版本：ColdFusion 2023  Update 7、ColdFusion 2021  Update 13。以规避漏洞带来的风险。  
  
  
参考链接：  
  
https://www.securityweek.com/cisa-warns-of-exploited-adobe-coldfusion-windows-vulnerabilities/  
  
  
**4.****Windows 内核模式驱动程序权限提升漏洞(CVE-2024-35250)**  
  
  
12月16日，CISA 已警告美国联邦机构，要求其系统防范针对高严重性 Windows 内核漏洞的持续攻击。  
  
该安全漏洞被标记为 CVE-2024-35250，是由于不受信任的指针取消引用弱点造成的，该弱点允许本地攻击者在不需要用户交互的低复杂度攻击中获得 SYSTEM 权限。  
  
虽然微软在 6 月份发布的安全公告中没有分享更多细节，但发现该漏洞并通过趋势科技的零日计划向微软报告的 DEVCORE 研究团队表示，易受攻击的系统组件是 Microsoft Kernel Streaming Service (MSKSSRV.SYS)。  
  
DEVCORE 安全研究人员在今年 Pwn2Own Vancouver 2024 黑客大赛的第一天利用此 MSKSSRV 权限提升安全漏洞入侵了已全面修补的 Windows 11 系统。  
  
微软在2024年6月的补丁日修复了该漏洞，并于四个月后在 GitHub 上发布了其漏洞 PoC。该公司在尚未更新的安全公告中表示：“成功利用此漏洞的攻击者可以获得系统权限”，这表明该漏洞正在被积极利用。DEVCORE 发布了视频演示，展示了其 CVE-2024-35250 PoC 如何用于破解 Windows 11 23H2 设备。  
  
建议管理员和家庭用户尽快测试和部署微软官方发布的补丁，以避免已知漏洞的攻击。  
  
  
参考链接：  
  
https://www.bleepingcomputer.com/news/security/windows-kernel-bug-now-exploited-in-attacks-to-gain-system-privileges/  
  
**PART****0****3**  
  
  
**安全事件**  
  
  
**1.国际知名特权访问管理厂商BeyondTrust被黑，多个客户受影响**  
  
  
12月19日Bleeping Computer消息，国际知名特权访问管理厂商BeyondTrust近日披露了一起网络攻击事件，攻击者成功入侵了部分远程支持客户SaaS实例，公司后续调查发现旗下产品存在两个零日漏洞，目前该事件影响面还在评估中。该公司表示，其网络系统于2024年12月2日检测到“异常活动”。调查显示，攻击者已成功攻破部分远程支持SaaS实例。攻击者获取了一个远程支持SaaS的API密钥，并利用该密钥重置了本地应用程序账号的密码。公告中指出：“BeyondTrust随即撤销了该API密钥，通知了所有已知受影响的客户，并在同一天暂停了相关实例。同时，为受影响客户提供了替代的远程支持SaaS实例。”目前尚不清楚攻击者是否利用被攻破的SaaS实例进一步入侵其下游客户。  
  
  
原文链接：  
  
https://www.bleepingcomputer.com/news/security/beyondtrust-says-hackers-breached-remote-support-saas-instances/  
  
  
**2.CNCERT披露两起美对我大型科技企业机构网络攻击事件**  
  
  
12月18日CNCERT消息，国家互联网应急中心（CNCERT）发现处置两起美对我大型科技企业机构进行网络攻击窃取商业秘密事件。2024年8月起，我国某先进材料设计研究单位遭疑似美国情报机构网络攻击。经分析，攻击者利用我境内某电子文档安全管理系统漏洞，入侵该公司部署的软件升级管理服务器，通过软件升级服务向该公司的270余台主机投递控制木马，窃取该公司大量商业秘密信息和知识产权。2023年5月起，我国某智慧能源和数字信息大型高科技企业遭疑似美国情报机构网络攻击。经分析，攻击者使用多个境外跳板，利用微软Exchange漏洞，入侵控制该公司邮件服务器并植入后门程序，持续窃取邮件数据。同时，攻击者又以该邮件服务器为跳板，攻击控制该公司及其下属企业30余台设备，窃取该公司大量商业秘密信息。  
  
  
原文链接：  
  
https://www.cert.org.cn/publish/main/49/2024/20241218184234131217571/20241218184234131217571_.html  
  
  
**3.日本大型媒体公司角川遭勒索攻击，被迫支付超2100万元赎金**  
  
  
12月13日The Record消息，据内部邮件和加密货币交易记录显示，日本大型媒体集团角川很可能向俄罗斯相关勒索软件组织BlackSuit支付了约2174万元赎金。今年6月，角川公司遭遇勒索软件攻击导致部分运营中断，公司稍后确认，攻击导致部分数据被泄露，包括合同、公司内部文件，以及所有员工的个人信息。据悉，BlackSuit访问了公司约1.5 TB的数据。在11月发布的一份声明中，角川表示，由于此次网络攻击事件的影响，公司预计将在截至2025年3月的财年中录得23亿日元（约合人民币1.06亿元）的特别损失。  
  
  
原文链接：  
  
https://therecord.media/kadokawa-japan-reported-ransomware-payment  
  
  
**4.产品漏洞被利用致大量用户数据泄露，Meta被罚超19亿元**  
  
  
12月17日TechCrunch消息，因违反GDPR，美国社交网络巨头Meta被罚超19亿元。Meta（Facebook）公司在2018年披露了一起安全事件，攻击者利用产品功能设计漏洞，抓取了约2900万个Facebook账号的个人信息，其中约300万个账号位于欧盟。爱尔兰数据保护委员会认为，Meta在产品设计上违反了GDPR的数据保护原则，未能采取适当措施防止用户数据遭到非预期处理，决定施以巨额罚款。  
  
  
原文链接：  
  
https://techcrunch.com/2024/12/17/meta-fined-263m-over-2018-security-breach-that-affected-3m-eu-users/  
  
  
**PART****0****4**  
  
  
**政策法规**  
  
  
**1.《珠江委数据安全管理办法（试行）》正式印发**  
  
  
12月21日，水利部珠江水利委员会（简称珠江委）近日制定印发《珠江委数据安全管理办法（试行）》。该文件明确落实珠江委数据安全保护工作主管部门主要职责，细化实化数据全生命周期的安全防护和管理要求，重点强化对外部支撑单位或第三方开展数据处理和数据维护相关工作的安全管理，为规范和促进珠江委数据安全管理提供制度支撑和根本遵循。  
  
  
原文链接：  
  
https://mp.weixin.qq.com/s/XNsRl0WIc9vGNDAUPKgBzA  
  
  
**2.《网络安全标准实践指南—— 一键停止收集车外数据指引》发布**  
  
  
12月19日，全国网络安全标准化技术委员会秘书处组织编制了《网络安全标准实践指南—— 一键停止收集车外数据指引》。该文件给出了在智能网联汽车上设置一键停止收集车外数据功能指引，适用于重要敏感区域的管理机构对进入该区域内的汽车的数据收集状态进行判断，还可为第三方测评机构开展智能网联汽车车外数据停止收集功能性和安全性测试评估提供参考。  
  
  
原文链接：  
  
https://www.tc260.org.cn/upload/2024-12-19/1734600988560096913.pdf  
  
  
**3.国家中医药管理局印发《中医医院信息与数字化建设规范（2024版）》**  
  
  
12月17日，国家中医药管理局对《中医医院信息化建设基本规范》进行修订，形成了《中医医院信息与数字化建设规范（2024版）》，现公开印发。该文件共10章82条，包括总则、机构人员、规划与管理、基础设施、信息平台与业务应用、标准与测评、安全防护、数据管理与利用、运行维护、附则。在安全防护方面，该文件要求中医医院网络安全管理应当坚持“等级保护、突出重点、积极防御、综合防护”的基本要求，落实和实施网络安全等级保护制度，建立健全网络安全管理制度，明确网络安全管理岗位与职责，全面梳理分析网络安全保护需求，建立医院网络安全防护体系和总体安全策略。  
  
  
原文链接：  
  
https://mp.weixin.qq.com/s/P9cZS2PcO2jEUr2VN9pspQ  
  
  
**4.美国CISA发布强制性指令，要求联邦机构落实SaaS安全配置基线**  
  
  
12月17日，美国网络安全和基础设施安全局（CISA）发布强制性操作指令（BOD）25-01《落实云服务安全实践的实施指南》，要求各联邦机构识别其所有云应用实例并部署评估工具，确保其云环境与CISA“安全云业务应用”（SCuBA）配置基线保持一致。据悉，SCuBA配置基线目前已经支持Microsoft 365、Google Workspace。  
  
  
原文链接：  
  
https://www.cisa.gov/news-events/directives/bod-25-01-implementation-guidance-implementing-secure-practices-cloud-services  
  
  
**5.美国CISA发布《国家网络事件响应计划》新版草案**  
  
  
12月16日，美国网络安全和基础设施安全局（CISA）发布《国家网络事件响应计划（NCIRP）》新版草案公开征求意见，这是该文件自2016年发布以来首次进行更新。NCIRP主要支持资产响应、威胁响应、情报支持和受影响实体响应四项工作，内容涵盖网络事件响应生命周期中的协调机制、关键决策点、优先事项等。新版NCIRP的更新内容主要包括：为非联邦政府的利益相关者参与网络事件响应协调提供了明确路径；通过简化内容和与运营生命周期对齐，提高了可用性；影响机构角色和责任的相关法律和政策更新；确定NCIRP后续更新周期。  
  
  
原文链接：  
  
https://www.cisa.gov/resources-tools/resources/national-cyber-incident-response-plan-update-public-comment-draft  
  
  
**6.国务院审议通过《公共安全视频图像信息系统管理条例（草案）》**  
  
  
12月16日，国务院总理李强12月16日主持召开国务院常务会议，学习贯彻习近平总书记在中央经济工作会议上的重要讲话精神，对贯彻落实中央经济工作会议决策部署作出安排。会议审议通过《公共安全视频图像信息系统管理条例（草案）》，指出要规范公共安全视频系统建设和使用，更好维护公共安全、保护个人隐私。  
  
  
原文链接：  
  
https://mp.weixin.qq.com/s/CWqdusZLgQvTrjd0w55a_g  
  
  
**往期精彩推荐**  
  
  
[【已复现】Apache Tomcat 远程代码执行漏洞(CVE-2024-56337)安全风险通告](https://mp.weixin.qq.com/s?__biz=MzU5NDgxODU1MQ==&mid=2247502658&idx=1&sn=e1de6decc572e58a32c667c1ecd2ec0b&token=671390406&lang=zh_CN&scene=21#wechat_redirect)  
[【已复现】Apache Tomcat 远程代码执行漏洞(CVE-2024-50379)安全风险通告](https://mp.weixin.qq.com/s?__biz=MzU5NDgxODU1MQ==&mid=2247502648&idx=1&sn=efadd668e22cb6151e0765d0937e3343&token=671390406&lang=zh_CN&scene=21#wechat_redirect)  
  
[【已复现】Apache Struts 文件上传漏洞(CVE-2024-53677)安全风险通告第二次更新](https://mp.weixin.qq.com/s?__biz=MzU5NDgxODU1MQ==&mid=2247502638&idx=1&sn=98d3602c7f4df70ae3d72d1809a70dc3&token=671390406&lang=zh_CN&scene=21#wechat_redirect)  
  
  
  
  
本期周报内容由安全内参&虎符智库&奇安信CERT联合出品！  
  
  
  
  
  
  
  
  

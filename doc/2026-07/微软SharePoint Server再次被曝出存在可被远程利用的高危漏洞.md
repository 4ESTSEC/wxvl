#  微软SharePoint Server再次被曝出存在可被远程利用的高危漏洞  
原创 何威风
                        何威风  河南等级保护测评   2026-07-10 23:46  
  
近期，微软SharePoint Server再次被曝出存在可被远程利用的高危漏洞，并已在实际攻击中被利用。美国网络安全与基础设施安全局（CISA）随后将相关漏洞纳入其已知被利用漏洞（KEV）清单，并发出紧急修复提醒，强调该漏洞已具备现实攻击风险，影响范围主要集中在本地部署的SharePoint服务器环境。  
  
根据安全通报，该漏洞属于远程代码执行（RCE）类型，攻击者在未授权的情况下即可通过网络请求触发漏洞，从而在目标服务器上执行任意代码，进而获得系统级控制权限。微软已在此前的安全更新中对该漏洞进行了修复，但CISA指出，现实中仍存在大量未及时更新的系统，正在被攻击者扫描与利用。  
  
与此同时，另一份安全分析指出，该漏洞已经被多个攻击活动纳入武器化利用链，攻击者往往通过组合多个SharePoint相关缺陷，实现从初始访问到权限提升再到持久化控制的完整攻击路径。在部分案例中，攻击者甚至可在补丁发布后仍通过残留访问或配置缺陷维持系统控制能力。  
  
此类SharePoint漏洞的风险不仅局限于单一应用系统，而是可能进一步扩展至企业内部协作体系，例如文档管理系统、邮件服务以及与其集成的业务平台。一旦攻击成功，可能导致敏感数据泄露、内部横向移动以及关键业务中断等严重后果。  
  
安全机构建议，相关组织应立即开展以下防护工作：一是核查 SharePoint Server 版本并确保更新至最新补丁；二是对公网暴露的 SharePoint 实例进行隔离或限制访问；三是启用日志审计与异常行为检测，重点关注可疑的远程执行行为；四是对已疑似受影响系统开展取证排查，以确认是否存在潜在入侵痕迹。  
  
总体来看，本次事件再次表明，企业级协作平台已成为攻击者重点关注目标之一，而“已修复但未部署”的安全补丁窗口，正是当前最常见也最危险的攻击切入点之一。  
  
[等保、关保、数保、个保，网络安全与数据治理“四位一体”的体系化制度框架](https://mp.weixin.qq.com/s?__biz=Mzg2NjY2MTI3Mg==&mid=2247505812&idx=1&sn=018455069df3d9894104953c6e14a91f&scene=21#wechat_redirect)  
  
  
**网络安全等级保护制度统一框架辨析**  
  
**>>>等级保护<<<**  
  
**从资质驱动到能力驱动——新标准下测评机构的生与死**  
  
**测评机构迎大考，安全厂商的机会来了！**  
  
**测评机构的回旋镖来了！测评机构不仅要会“测别人”，更要先“管好自己”**  
  
**新标准背景下等级测评机构应培养什么样的人才**  
  
**供应链企业应该如何适应等级保护发展******  
  
**网络安全等级保护制度演进，安全治理思维的演变**  
  
[网络安全等级保护之安全物理环境](https://mp.weixin.qq.com/s?__biz=Mzg2NjY2MTI3Mg==&mid=2247505801&idx=1&sn=958f82ebcc5a21a127373e93aa1ab825&scene=21#wechat_redirect)  
  
  
[网络安全等级保护之安全区域边界](https://mp.weixin.qq.com/s?__biz=Mzg2NjY2MTI3Mg==&mid=2247505801&idx=1&sn=958f82ebcc5a21a127373e93aa1ab825&scene=21#wechat_redirect)  
  
  
[网络安全等级保护之安全通信网络](https://mp.weixin.qq.com/s?__biz=Mzg2NjY2MTI3Mg==&mid=2247505801&idx=1&sn=958f82ebcc5a21a127373e93aa1ab825&scene=21#wechat_redirect)  
  
  
[网络安全等级保护之安全计算环境](https://mp.weixin.qq.com/s?__biz=Mzg2NjY2MTI3Mg==&mid=2247505801&idx=1&sn=958f82ebcc5a21a127373e93aa1ab825&scene=21#wechat_redirect)  
  
  
[网络安全等级保护之安全管理中心](https://mp.weixin.qq.com/s?__biz=Mzg2NjY2MTI3Mg==&mid=2247505801&idx=1&sn=958f82ebcc5a21a127373e93aa1ab825&scene=21#wechat_redirect)  
  
  
[网络安全等级保护之安全管理制度](https://mp.weixin.qq.com/s?__biz=Mzg2NjY2MTI3Mg==&mid=2247505801&idx=1&sn=958f82ebcc5a21a127373e93aa1ab825&scene=21#wechat_redirect)  
  
  
[网络安全等级保护之安全管理机构](https://mp.weixin.qq.com/s?__biz=Mzg2NjY2MTI3Mg==&mid=2247505801&idx=1&sn=958f82ebcc5a21a127373e93aa1ab825&scene=21#wechat_redirect)  
  
  
[网络安全等级保护之安全管理人员](https://mp.weixin.qq.com/s?__biz=Mzg2NjY2MTI3Mg==&mid=2247505801&idx=1&sn=958f82ebcc5a21a127373e93aa1ab825&scene=21#wechat_redirect)  
  
  
[网络安全等级保护之安全建设管理](https://mp.weixin.qq.com/s?__biz=Mzg2NjY2MTI3Mg==&mid=2247505801&idx=1&sn=958f82ebcc5a21a127373e93aa1ab825&scene=21#wechat_redirect)  
  
  
[网络安全等级保护之安全运维管理](https://mp.weixin.qq.com/s?__biz=Mzg2NjY2MTI3Mg==&mid=2247505801&idx=1&sn=958f82ebcc5a21a127373e93aa1ab825&scene=21#wechat_redirect)  
  
  
**网络安全等级保护制度演进，回看2003年27号文**  
  
**网络安全等级保护制度演进，回看2004年66号文**  
  
**网络安全等级保护制度演进，回看2006年7号文（过渡性文件）**  
  
[《等级保护条例》迎来最新进展](https://mp.weixin.qq.com/s?__biz=Mzg2NjY2MTI3Mg==&mid=2247505649&idx=1&sn=342bf65417e243d0771ca56d852e76a4&scene=21#wechat_redirect)  
  
  
[网络安全等级保护制度统一框架辨析](https://mp.weixin.qq.com/s?__biz=Mzg2NjY2MTI3Mg==&mid=2247505789&idx=1&sn=239bac6ed28aa1bbf7c7d36cbf0b7f57&scene=21#wechat_redirect)  
  
  
[网络安全等级保护安全物理环境之防盗窃和防破坏实现](https://mp.weixin.qq.com/s?__biz=MzA5MzU5MzQzMA==&mid=2652121519&idx=1&sn=631a7fe01f6172254409e26272c68ffe&scene=21#wechat_redirect)  
  
  
[网络安全等级保护物理访问控制实现](https://mp.weixin.qq.com/s?__biz=MzA5MzU5MzQzMA==&mid=2652121492&idx=2&sn=2dc3e4c889b8b33464176a871dd2fac5&scene=21#wechat_redirect)  
  
  
[信息安全技术 网络安全等级保护测评过程指南](https://mp.weixin.qq.com/s?__biz=MzA5MzU5MzQzMA==&mid=2652121469&idx=1&sn=745b1a5bbb2c0bac74d0cbcdf03bf2e0&scene=21#wechat_redirect)  
  
  
[夜读：GB 17859-1999安全保护等级划分准则](https://mp.weixin.qq.com/s?__biz=MzA5MzU5MzQzMA==&mid=2652121433&idx=1&sn=0074031432dd648b7d41627c11b520d2&scene=21#wechat_redirect)  
  
  
[等级保护基本要求标准系列](https://mp.weixin.qq.com/s?__biz=MzA5MzU5MzQzMA==&mid=2652121261&idx=1&sn=83eaa31a45d33b441a84a1ffafac583d&scene=21#wechat_redirect)  
  
  
[等级保护的数据摸底调查](https://mp.weixin.qq.com/s?__biz=MzA5MzU5MzQzMA==&mid=2652120232&idx=1&sn=4e95783ee5fd62e9e9cc8b74103fc310&scene=21#wechat_redirect)  
  
  
[网络安全等级保护自查清单（对照法条）](https://mp.weixin.qq.com/s?__biz=MzA5MzU5MzQzMA==&mid=2652121146&idx=1&sn=07cef95c28356b7f740a00f94ab7f170&scene=21#wechat_redirect)  
  
  
[由新《网安法》罚则看等级保护、应急安全责任](https://mp.weixin.qq.com/s?__biz=MzA5MzU5MzQzMA==&mid=2652120509&idx=1&sn=db3380982915487b4689a312349f984f&scene=21#wechat_redirect)  
  
  
[等级保护的数据摸底调查](https://mp.weixin.qq.com/s?__biz=MzA5MzU5MzQzMA==&mid=2652120232&idx=1&sn=4e95783ee5fd62e9e9cc8b74103fc310&scene=21#wechat_redirect)  
  
  
[以等级保护为中轴线/基础的网络安全监管体系发展](https://mp.weixin.qq.com/s?__biz=Mzg2NjY2MTI3Mg==&mid=2247505443&idx=1&sn=b55926a407e187fd62563c8cae51199a&scene=21#wechat_redirect)  
  
  
[网络运营者等级保护合规自查表](https://mp.weixin.qq.com/s?__biz=Mzg2NjY2MTI3Mg==&mid=2247505260&idx=1&sn=ed42da685a9950029b024dd9bbc89247&scene=21#wechat_redirect)  
  
  
[信息安全技术 网络安全等级保护测评过程指南](https://mp.weixin.qq.com/s?__biz=MzA5MzU5MzQzMA==&mid=2652121469&idx=1&sn=745b1a5bbb2c0bac74d0cbcdf03bf2e0&scene=21#wechat_redirect)  
  
  
[网络安全等级保护全生命周期一览图](https://mp.weixin.qq.com/s?__biz=MzA5MzU5MzQzMA==&mid=2652121384&idx=1&sn=6fb506fc4365c1ddb1e2578cd0f606aa&scene=21#wechat_redirect)  
  
  
**开启等级保护之路：GB 17859网络安全等级保护上位标准**  
  
**网络安全等级保护：什么是等级保护？**  
  
**网络安全等级保护：等级保护工作从定级到备案**  
  
**网络安全等级保护：等级测评中的渗透测试应该如何做**  
  
**网络安全等级保护：等级保护测评过程及各方责任**  
  
**网络安全等级保护：政务计算机终端核心配置规范思维导图**  
  
**网络安全等级保护：信息技术服务过程一般要求**  
  
**网络安全等级保护：浅谈物理位置选择测评项**  
  
**闲话等级保护：网络安全等级保护基础标准（等保十大标准）下载**  
  
**闲话等级保护：什么是网络安全等级保护工作的内涵？**  
  
**闲话等级保护：网络产品和服务安全通用要求之基本级安全通用要求**  
  
**闲话等级保护：测评师能力要求思维导图**  
  
**闲话等级保护：应急响应计划规范思维导图**  
  
**闲话等级保护：浅谈应急响应与保障**  
  
**闲话等级保护：如何做好网络总体安全规划**  
  
**闲话等级保护：如何做好网络安全设计与实施**  
  
**闲话等级保护：要做好网络安全运行与维护**  
  
**闲话等级保护：人员离岗管理的参考实践**  
  
[网络安全等级保护：做等级保护不知道咋定级？来一份定级指南思维导图](http://mp.weixin.qq.com/s?__biz=MzA5MzU5MzQzMA==&mid=2652097584&idx=1&sn=1191d9e0afdf491e1e0831340204e5a6&chksm=8bbce609bccb6f1f597ad5c287351f5344493716a8ed4dcc4757dec7a07eeb9809c068a03c70&scene=21#wechat_redirect)  
  
  
[信息安全服务与信息系统生命周期的对应关系](http://mp.weixin.qq.com/s?__biz=MzA5MzU5MzQzMA==&mid=2652096322&idx=1&sn=a9b8121f06496331305cb19de8e1827b&chksm=8bbced7bbccb646d66b38bef69fdd4534ed4ecfa06e464ff133350040f133bc0d5f93459c388&scene=21#wechat_redirect)  
  
  
**>>>工控安全<<<**  
  
**工业控制系统安全：信息安全防护指南**  
  
**工业控制系统安全：工控系统信息安全分级规范思维导图**  
  
**工业控制系统安全：DCS防护要求思维导图**  
  
**工业控制系统安全：DCS管理要求思维导图**  
  
**工业控制系统安全：DCS评估指南思维导图**  
  
**工业控制安全：工业控制系统风险评估实施指南思维导图**  
  
**工业控制系统安全：安全检查指南思维导图（内附下载链接）**  
  
**工业控制系统安全：DCS风险与脆弱性检测要求思维导图**  
  
**工业安全远程访问渐增引发企业担心**  
  
**工业巨头 ABB 确认勒索软件攻击、数据盗窃**  
  
**去年针对工业组织的勒索软件攻击增加了一倍**  
  
**标准下载：工业控制系统信息安全防护能力成熟度模型GB/T 41400-2022**  
  
**>>>数据安全<<<**  
  
**如何共建数据合规治理平台，确保用户数据安全？**  
  
**数据安全：数据访问治理完整指南**  
  
**良好数据安全实践推动数据治理的 7 种方式**  
  
**数据治理和数据安全**  
  
**数据安全风险评估清单**  
  
**成功执行数据安全风险评估的3个步骤**  
  
**美国关键信息基础设施数据泄露的成本**  
  
**备份：网络和数据安全的最后一道防线**  
  
**数据安全：数据安全能力成熟度模型**  
  
**数据安全知识：什么是数据保护以及数据保护为何重要？**  
  
**信息安全技术：健康医疗数据安全指南思维导图**  
  
**金融数据安全：数据安全分级指南思维导图**  
  
**金融数据安全：数据生命周期安全规范思维导图**  
  
**什么是数据安全态势管理 (DSPM)？**  
  
**5个常见的数据安全陷阱以及如何避免**  
  
**数据安全知识：数据库安全威胁**  
  
**数据安全知识：不同类型的数据库**  
  
**数据安全知识：数据库简史**  
  
**数据安全知识：什么是数据出口？**  
  
**数据安全知识：什么是数据治理模型？**  
  
**>>>供应链安全<<<**  
  
美国政府为客户发布软件供应链安全指南  
  
OpenSSF 采用微软内置的供应链安全框架  
  
供应链安全指南：了解组织为何应关注供应链网络安全  
  
供应链安全指南：确定组织中的关键参与者和评估风险  
  
供应链安全指南：了解关心的内容并确定其优先级  
  
供应链安全指南：为方法创建关键组件  
  
供应链安全指南：将方法整合到现有供应商合同中  
  
供应链安全指南：将方法应用于新的供应商关系  
  
供应链安全指南：建立基础，持续改进。  
  
思维导图：ICT供应链安全风险管理指南思维导图  
  
**英国的供应链网络安全评估**  
  
**网络安全知识：绘制供应链图**  
  
**网络安全知识：评估供应链管理实践**  
  
**网络安全知识：评估供应链安全**  
  
**网络安全知识：供应链攻击4个示例**  
  
**网络安全知识：英国供应链安全指导12原则**  
  
**组织网络弹性之旅第9部分：供应链和第三方**  
  
[网络安全知识：物流业的网络安全](http://mp.weixin.qq.com/s?__biz=MzA5MzU5MzQzMA==&mid=2652097694&idx=1&sn=bfc4491892b7e61611f78dffd15fe007&chksm=8bbce6a7bccb6fb11c61ba1677d1503d4575f8ac4d2a40bf3abc44a3eb3cd36dce8d11ad2415&scene=21#wechat_redirect)  
  
  
[网络安全知识：什么是AAA（认证、授权和记账）？](http://mp.weixin.qq.com/s?__biz=MzA5MzU5MzQzMA==&mid=2652098008&idx=1&sn=2996aa534f2baf38cc7c21690c013783&chksm=8bbce7e1bccb6ef71e6de7aae42e925e6cb9bf39e210287e69dfef42b746b4dc7faa75daebde&scene=21#wechat_redirect)  
  
  
**测试供应链安全的极限**  
  
**美国NIST 供应链安全指南：10 条要点**  
  
**软件供应链：黄金集装箱船**  
  
**>>>其他<<<**  
  
**网络安全十大安全漏洞**  
  
**网络安全知识：什么是勒索软件？**  
  
**Kali Linux 最佳工具之Nmap**  
  
**云安全策略的10个关键要素**  
  
[安全从组织内部人员开始](http://mp.weixin.qq.com/s?__biz=MzA5MzU5MzQzMA==&mid=2652097579&idx=2&sn=64c7ba49b4d589b6035d03200f240e2d&chksm=8bbce612bccb6f0448d65a2cfd4bdca78ffd2f99f1c85d9fe8aed544b5d04a24e97a755d0833&scene=21#wechat_redirect)  
  
  
[开源代码带来的 10 大安全和运营风险](http://mp.weixin.qq.com/s?__biz=MzA5MzU5MzQzMA==&mid=2652098603&idx=1&sn=0fbf31d23a8c44f6c18beb3346796042&chksm=8bbcfa12bccb730498adc5b679600f442fae032441aa68b7113ebd81b6dc9128ca54be0548d6&scene=21#wechat_redirect)  
  
  
[不能放松警惕的勒索软件攻击](http://mp.weixin.qq.com/s?__biz=MzA5MzU5MzQzMA==&mid=2652098600&idx=1&sn=486efa598cc8ec56c290e1fc3d8cfefb&chksm=8bbcfa11bccb7307756db3b3b8fa9a00e56c3453fb22fe5351768d28d93d54962b3b9e3ba877&scene=21#wechat_redirect)  
  
  
[10种防网络钓鱼攻击的方法](http://mp.weixin.qq.com/s?__biz=MzA5MzU5MzQzMA==&mid=2652098707&idx=1&sn=54ac122d4bb39df390e18799fdc5e68b&chksm=8bbcfaaabccb73bccddba4b9319d1bb572509e07b8b9e002f079fe085ee80b950854217c2193&scene=21#wechat_redirect)  
  
  
[5年后的IT职业可能会是什么样子？](http://mp.weixin.qq.com/s?__biz=MzA5MzU5MzQzMA==&mid=2652098953&idx=1&sn=3b386a7e269a779a589272e451a3882a&chksm=8bbcfbb0bccb72a68f81dba9aaa659839609e0536fd113fcb322447b0cbf876cf8a4c6379c58&scene=21#wechat_redirect)  
  
  
[累不死的IT加班人：网络安全倦怠可以预防吗？](http://mp.weixin.qq.com/s?__biz=MzA5MzU5MzQzMA==&mid=2652098946&idx=1&sn=587b59ecfda947693fc3079ab808e7f9&chksm=8bbcfbbbbccb72ad50b51862834abcea12b22af309efd059688fda3465fea1f6b3ff6c12fc66&scene=21#wechat_redirect)  
  
  
[网络风险评估是什么以及为什么需要](http://mp.weixin.qq.com/s?__biz=MzA5MzU5MzQzMA==&mid=2652098940&idx=1&sn=b4f285d46933bed366718d92bfcb847d&chksm=8bbcfb45bccb7253f19f1b8fc7849236030ea4e52a2d8332a22c2f4e2b1ca431ddf0bdf1c4c8&scene=21#wechat_redirect)  
  
  
**如何减少制造攻击面的暴露**  
  
**来自不安全的经济、网络犯罪和内部威胁三重威胁**  
  
**什么是渗透测试，能防止数据泄露吗？**  
  
**SSH 与 Telnet 有何不同？**  
  
**管理组织内使用的“未知资产”：影子IT**  
  

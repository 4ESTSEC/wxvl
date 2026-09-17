#  安全快报 | 威胁组织Red Heron利用远程代码漏洞攻击多国政府和关基单位  
 天懋信息   2026-09-17 07:35  
  
**本周安全事件速览**  
  
**09月10日-09月16日**  
  
**01**  
  
**威胁组织Red Heron利用远程代码漏洞攻击多国政府和关基单位**  
  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/76BUAjRsqibSHLskZPpswLAkaolg2Bu21AzowcjC5286xlIbibHqWYceXnjkictib7H6Db6cgBVgZcibSFDly5ZK0O2vflmJHHjUzE66MJQWkIrg/640?from=appmsg "")  
  
  
**简要介绍**  
  
威胁组织Red Heron利用Gitea远程代码执行漏洞CVE-2026-60004对全球多国发起攻击。该组织在2026年7月漏洞披露后数日内，便将公开概念验证代码转化为自动化Python框架，扫描了7个国家共1,386个Gitea实例，并维持了一份包含477个台湾地区系统的数据集。已确认的受害组织分布在加拿大（2个）、阿根廷（1个）、中国台湾地区（4个）、美国（4个）、卡塔尔（1个）和斯里兰卡（1个），目标涵盖国防、选举、能源、航空航天、电信、政府及研究等领域，黑客使用简体中文标签对目标进行分类。黑客使用的C++ Linux植入程序JITTERLY支持超过30种后渗透命令，并包含一款名为SIXZUT的LD_PRELOAD rootkit，可通过修补15个Linux函数来隐藏文件、进程和网络连接，并在被终止后自动重启。  
  
  
**文章来源**  
**：The Hacker News**  
  
  
  
**02**  
  
**英美荷等国家安全局联合警告称伊朗黑客正针对异见人士与记者发动网络间谍活动**  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/76BUAjRsqibQBIogIxW6r7Vqft0zu4ehTGf4VATibCXGNvs2sjYdYPibGD9CXiadslTicBKAl4BDFJuGVkJjKRPL7lxLpaGlklFvleS5bWtlLprw/640?from=appmsg "undefined")  
  
  
**简要介绍**  
  
英国国家网络安全中心（NCSC）联合美国联邦调查局（FBI）及荷兰情报与安全总局（AIVD）发布联合警告，揭露伊朗国家黑客使用名为“Chosen Brick”的间谍软件，针对全球异见人士、活动人士和记者发动网络间谍活动。该软件自2025年起活跃，黑客通过WhatsApp和Telegram等通讯软件冒充熟人，发送伪装成合法应用或文件的恶意载荷（如伪造的核磁共振扫描结果）诱骗目标下载。恶意软件具有重启持久性，可削弱Microsoft Defender防护，并通过Telegram机器人进行命令控制。它能窃取屏幕截图、麦克风录音、邮件、文件及浏览器数据，并部署额外恶意软件。被盗信息会出现在亲伊朗的泄密网站上，用于画像和骚扰，甚至增加目标的物理安全风险。对此，当局已发布技术指标和缓解指南。  
  
  
**文章来****源**  
**：**  
**Info Security-Magazine**  
  
  
  
**03**  
  
**亲乌克兰威胁组织利用后门、勒索软件和擦拭器对俄罗斯企业持续发起网络入侵**  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/76BUAjRsqibSguDpmrpZtzMx8kZiazzpMEJHqkYbprkEL27xql3HfuOXB3icJucMd5EgDR8kgxexibnKaicWgHnL0Mian7gp9ibc6svy9q6q5hDT1E/640?from=appmsg "")  
  
  
**简要介绍**  
  
三个不同的威胁组织正针对俄罗斯企业发起  
网络  
攻击。  
NightEagle（APT-Q-95）利用被盗凭证入侵企业VPN，部署GhostContainer后门控制Exchange服务器，并通过隧道工具横向移动，最终企图攻陷域控和整个活动目录。亲乌克兰的黑客组织Hacking Cat则转向加密与破坏性攻击，利用Exchange漏洞投递Gorilla RAT，并部署名为Monkey的多语言勒索软件，部分变种不存储密钥，实为擦除器。该组织还与其他黑客团体合作，使用ClearWater勒索软件和Nemo Wiper擦除器。出于经济动机的Toy Ghouls组织首次部署自研后门Bird Agent，通过WinRM投递，利用HiveMQ MQTT代理或基于Matrix的Element加密通讯应用作为命令与控制通道，标志着其攻击手法向更复杂、更隐蔽的方向演进。  
  
  
**文章来****源：**  
**The Hacker News**  
  
  
**04**  
  
**N0va钓鱼工具包瞄准欧美政府部门和科技企业以诱饵捕获登录令牌**  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/76BUAjRsqibSSAJc7GvDS2Aq87XwO9Vkjc1wzXxlxKiadympMibx0ch0mfTuCWQNuFtcd5MzHcqV0OEYibtDk596uH0lbamX2BBC4ThkDsJqzoU/640?from=appmsg "")  
  
  
**简要介绍**  
  
名为N0va的钓鱼工具包正针对北美和欧洲的政府、科技、咨询、医疗等高风险行业发起攻击。该工具包伪造Microsoft Teams、SharePoint、OneDrive、DocuSign、Google Drive、Dropbox、Zoom和Adobe Sign等常用商业平台诱饵，引导受害者完成“合法”身份验证流程。与传统虚假登录页面不同，N0va在用户完成认证后捕获访问令牌和刷新令牌，并滥用令牌交换或设备注册机制建立SSO访问权限，从而绕过明显的恶意软件活动。攻击链为：可信品牌诱饵→设备代码钓鱼→合法认证→令牌捕获→令牌交换/设备注册→SSO访问企业资源。一旦得手，黑客可访问邮箱、文件、云应用等，进而实施支付欺诈、窃取敏感数据、造成运营中断及合规风险。  
  
**文章来****源：****The Hacker News**  
  
  
  
**05**  
  
**美国公用事业巨头CenterPoint Energy确认因数据泄露导致749万条客户记录遭窃**  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/76BUAjRsqibTzjF72fPxWSIzXWROvez3wDYsGksWvzD8jJNfWg17pJ4A0R5pmagTEDohqetwic7quddomJoEkeAcTv6jY2LTIbjYs3Zen1dDE/640?from=appmsg "undefined")  
  
  
**简要介绍**  
  
美国休斯顿公用事业公司  
CenterPoint Energy近期确认发生数据泄露，一名黑客声称通过其外部API窃取了约749万条客户记录，包括姓名、电话、服务及账单地址、账号、账单金额、付款状态及社会安全号码后四位等敏感信息。该公司在向美国证券交易委员会（SEC）提交的文件中证实，未授权第三方通过其外部系统获取了部分客户个人信息。黑客声称该API缺乏Web应用防火墙、速率限制和身份验证令牌，并警告下次将攻击主要IT基础设施。CenterPoint表示电力与燃气服务未受影响运营正常，目前正与第三方专家调查事件范围，并将依法通知受影响客户和监管机构。公司认为该事件不太可能对其财务状况产生重大影响，但已面临多起集体诉讼。  
  
  
**文章来****源：****Security Affairs**  
  
  
  
**06**  
  
**英国金融科技巨头Revolut因误信黑客伪造的政府请求邮件导致680名客户数据泄露**  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/76BUAjRsqibT2txR57VPnLZvIG6GvbD6fib8txERxVeYFkogLRicwa68oPFTAzq38HeUpruxLQK4WsJGcsJxCvhc8LtGx2n9WlqWI3ACY162ibI/640?from=appmsg "")  
  
  
**简要介绍**  
  
英国金融科技巨头  
Revolut近期确认发生数据泄露事件，一黑客利用合法的政府机构邮箱域名发送欺诈性信息请求，诱骗其合规团队披露了约680名高净值客户的敏感数据。泄露信息极为全面，包括姓名、出生日期、住址、电话、护照及驾照扫描件、人脸验证自拍，以及完整的账户账单和比特币交易记录。黑客随后在Telegram上公开了部分知名人士的证件，并威胁每日持续泄露以施压勒索。Revolut强调其核心系统与客户资金未受影响，公司迄今拒绝具体说明受影响人数或其所在地，只表示此次泄露涉及“极少数”客户的数据，且受害者已被直接通知。但专家指出，此类数据泄露可能引发高度精准的钓鱼攻击与身份冒用，并可能使该公司面临GDPR等法规的合规审查。  
  
  
**文章来****源**  
**：Bank Info Security**  
  
  
  
**07**  
  
**巴西银行木马劫持Chrome与Edge浏览器以窃取登录凭证与会话令牌**  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/76BUAjRsqibQdmDTzTvz3panOcoK2dN9oU9P9qPEy2oORI1MPCAk8kWIFL3fHblReJb7rTjic6VtWMYqhpEcoiaj7iarWGla7aLQLG5IH86zJLY/640?from=appmsg "")  
  
  
**简要介绍**  
  
名为KREMLIN的巴西银行恶意软件工具包相关活动被追踪为REF9334，自2025年5月起活跃。该恶意软件通过伪装成银行、发票或公司文件的诱饵，诱使受害者手动执行JavaScript文件，进而触发多阶段加载器，在Google Chrome和Microsoft Edge上安装恶意浏览器扩展。该工具包采用多阶段JavaScript加载器、定制C++安装程序及恶意浏览器扩展，专门窃取凭证、会话令牌及敏感数据。其显著特点是利用以太坊智能合约作为“死投解析器”，动态更新命令与控制（C2）端点，使其基础设施难以被摧毁。恶意扩展通过操纵Secure Preferences并重新生成HMAC来绕过Chromium完整性机制。一旦安装，扩展会请求对浏览器标签页、Cookie及存储的广泛访问权限，并通过WebSocket或伪装成CSS文件请求的轮询方式，将窃取的浏览器数据外传至C2服务器。  
  
  
**文章来****源**  
**：The Hacker News**  
  
  
  
**08**  
  
**研究发现黑客偏爱美国东部工作时间发动微软M365钓鱼邮件攻击**  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/76BUAjRsqibSr2KyfsczwqnkXIL94UIiccxqRoI1vbjV09IUPnsK51mbicIoEiaXwaviaWjSc569sAjCGsOra6NDhcxD6Q9z1N84QQ5Mg2g9bts0/640?from=appmsg "")  
  
  
**简要介绍**  
  
KnowBe4威胁实验室发现，一场滥用微软M365 Direct Send功能的钓鱼活动呈现出“明显的人类工作模式”。2026年7月至8月间，研究人员确认了29,785封利用该功能的钓鱼邮件，黑客在美国东部时间周一至周二尤为活跃，邮件量在中午前达到高峰，短暂回落后于下午2点左右冲至最高点。Direct Send本是允许打印机、扫描仪等设备无需专用账户即可发信的合法功能，黑客利用它冒充HR、财务或管理员等可信内部地址发送邮件，无需窃取员工凭证，还能绕过目标组织的邮件安全网关。约35%的恶意邮件携带附件，内容涵盖虚假文件请求、内部语音邮件提醒、发票与付款审批及伪造的OneDrive文件共享，其中4,023封邮件将回复地址指向不同域名，使员工回复直接落黑客手中。单次发送最多触达900名收件人。  
  
  
**文章来****源**  
**：Info Security-Magazine**  
  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/RdDBE4xfCCWnp4MYTluo2ib4Pibo5QAoxm2iaJME3yPXPLr1QYibicibCZibDib4185YxjKdxtvrcRspzxXj8BqZlnUhibA/640?wx_fmt=gif "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/RdDBE4xfCCXHBrgOytxrXj5Isuu7Wa0bM6XhWyfjejlJia5dbBFcSpxZGvYibRndWGfODicNTYEpBFkXzuvp547cw/640?wx_fmt=gif "")  
  
  
往期回顾：  
  
[](https://mp.weixin.qq.com/s?__biz=MzU3MDA0MTE2Mg==&mid=2247494190&idx=1&sn=b9af95946992729b14c7c3f93ee163b1&scene=21#wechat_redirect)  
[](https://mp.weixin.qq.com/s?__biz=MzU3MDA0MTE2Mg==&mid=2247494173&idx=1&sn=5b7e2b53d2bcfbaa3070b0d785bed4ad&scene=21#wechat_redirect)  
  
  

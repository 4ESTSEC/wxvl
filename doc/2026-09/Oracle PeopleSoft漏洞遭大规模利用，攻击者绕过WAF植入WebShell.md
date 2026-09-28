#  Oracle PeopleSoft漏洞遭大规模利用，攻击者绕过WAF植入WebShell  
 FreeBuf   2026-09-28 10:00  
  
![FreeBuf](https://mmbiz.qpic.cn/mmbiz_gif/icBE3OpK1IX2D4kb0qVqU9V757xbTGfAGBNMfrV0Kv2OY4WvvUftvdc4nnBU3SIWMbaLNhD0hMmIQIV3HfblZtoDeCMlaTPJ8sB3cg5g5fPk/640?wx_fmt=gif "")  
  
  
![image](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX3YB9AbGZADAfePW7UicF3PIH4H4icp1jGdTJw93I51284iayzkW8s9xbDpqbnxYwgZUJDniaFIYNewksnpIdt6gSfHOSd8Y0aPiaZ4/640?wx_fmt=png "")  
  
  
Part  
01  
  
PeopleSoft漏洞遭利用  
  
  
Google警告称，Oracle PeopleSoft的一个已知安全漏洞正遭遇新一轮大规模利用，相关攻击活动波及全球多个行业。  
  
  
此次活动与ShinyHunters有关联，攻击者利用的是严重漏洞CVE-2026-35273（CVSS评分：9.8），成功利用后可在未授权的情况下执行远程代码。  
  
  
攻击者最初将该漏洞作为0Day用于攻击学术机构。  
  
  
得手后，攻击者首先开展侦察，部署MeshCentral agent等远程访问软件实现持久化。  
  
  
随后攻击者通过SSH进行横向移动，运行shell脚本利用已知用户名密码组合尝试连接其他内部PeopleSoft主机。  
  
  
一旦连接成功，攻击者就会窃取主机上的敏感数据。  
  
  
当时，Google旗下Mandiant表示，已向100多家全球组织发出通知，这些组织的IP地址对应存在漏洞的端点，其中大部分位于美国。  
  
  
Mandiant表示：“这波新活动源于UNC6240修改了漏洞利用代码，绕过了WAF设置的拦截规则——这些规则原本用于阻断对存在漏洞的Environment Management Hub（PSEMHUB）端点的访问。攻击者对请求路径中的单个字符进行URL编码，用/%50SEMHUB/代替/PSEMHUB/路径，绕过了这些基于字符串的WAF规则。很多WAF和反向代理规则会在URL解码前匹配字面路径，但PeopleSoft应用服务器会先解码请求，再将其路由到存在漏洞的servlet。”  
  
  
Part  
02  
  
攻击者构造完整攻击链  
  
  
最新一轮攻击的目标覆盖高等教育、科技、IT服务、医疗、农业、交通、政府等多个行业的机构，攻击者已在数十个系统上部署WebShell。  
  
  
完整攻击流程如下：  
  
- 向"/%50SEMHUB/hub"路径发送包含序列化Java对象的POST请求，识别存在漏洞的目标。  
  
  
- 在POST请求中使用字符P的编码形式（即"%50"）构造路径"/%50SEMHUB/hub"，绕过WAF规则。  
  
  
- 滥用PSEMHUB hub servlet中的Java反序列化漏洞部署WebShell，实现无文件命令执行。  
  
  
- 在PSEMHUB.war目录下投放两个JSP WebShell，降低后渗透阶段被WAF检测到的概率：其中"x.jsp"支持跨平台命令执行，"u.jsp"支持分块上传文件到服务器，并可通过cmd.exe执行命令。  
  
  
- 利用"u.jsp"上传经过有效签名的木马化安装程序"Ple64.exe"，该程序会在内存中加载C++后门SIDEEYE。SIDEEYE会通过TCP与外部服务器（162.219.30[.]165）通信，支持窃取浏览器和桌面应用凭证、管理进程与文件、创建交互式反向Shell和反向代理等功能。  
  
  
Google表示：“除了部署Ple64.exe，攻击者还投放了开源Neo-reGeorg隧道工具。为了在Linux系统上部署WebShell后建立持久化访问，UNC6240还部署了合法RMM工具MeshAgent。”  
  
  
![image](https://mmbiz.qpic.cn/sz_mmbiz_png/icBE3OpK1IX0S7wzG1M8RFMF2UezXzrk15AXYTfhQqWnKicoI1HC8rF9nHgrsB9tkc73icagppPlsAVmnsf4f0UYnDQ2gbCxBenX4R3mBN1c6Y/640?wx_fmt=png "")  
  
  
Part  
03  
  
四分之一命令获最高权限  
  
  
据统计，攻击者执行的命令中约有四分之一拥有root或NT Authority\SYSTEM权限，可完全控制操作系统。其余命令则通过PeopleSoft或WebLogic服务账户执行。  
  
  
为防御该威胁，企业需执行以下操作：  
  
- 安装CVE-2026-35273的安全补丁。  
  
  
- 在多服务器配置中禁用Environment Management Hub（EMHub）服务，在单服务器配置中直接完全移除PSEMHUB应用。  
  
  
- 排查WebLogic访问日志，查找对"/PSEMHUB/"路径及其任意百分号编码变体的请求。  
  
  
- 检查"PSEMHUB.war"目录下是否存在JSP WebShell及其他恶意文件。  
  
  
- 轮换PeopleSoft应用服务账户可读取的所有凭证。  
  
  
- 在PeopleSoft主机和数据库主机中，排查临时目录或Web可访问目录下的大型归档文件。  
  
  
- 审查数据库审计日志，查找针对人力资源、薪资、学生记录表的批量查询或导出操作。  
  
  
- 监控PeopleSoft主机的出站流量。  
  
  
Google表示：“UNC6240长期存在数据窃取勒索的攻击模式，即先窃取数据，再威胁受害者如果不支付赎金，就会在数据泄露网站上公开数据。受影响组织应做好接收勒索信息的准备，同时监控被盗数据是否被公开。”  
  
  
Part  
04  
  
ShinyHunters承认入侵FBI  
  
  
本次漏洞披露的同时，ShinyHunters入侵了美国联邦调查局的FBIJobs.gov门户（截至撰稿时该网站仍无法访问），窃取了约2-3TB敏感数据。该组织称这次行动是为了反驳FBI在2026年5月发布的安全警报中对其提出的指控。  
  
  
ShinyHunters发言人向The Hacker News表示：“我们想再次强调，我们绝对没有向FBI勒索。这次行动完全没有经济动机，既不是赎金勒索，也不是敲诈。我们只是想澄清事实，维护组织的声誉。”  
  
  
该发言人还向该媒体表示，攻击者入侵FBI招聘门户时利用的是Oracle PeopleSoft的另一个0Day漏洞，并非CVE-2026-35273。在向The Register提供的另一份声明中，该组织表示其前身是GnosticPlayers，2020年更名为ShinyHunters。  
  
  
参考来源：  
  
Attackers Bypass WAFs to Exploit Oracle PeopleSoft Flaw and Deploy Web Shells  
  
https://thehackernews.com/2026/09/attackers-bypass-wafs-to-exploit-oracle.html  
  
  
### 推荐阅读  
  
  
[](https://mp.weixin.qq.com/s?__biz=MjM5NjA0NjgyMA==&mid=2651347479&idx=1&sn=1cf9c858a30d0b7af06d196002bd2913&scene=21#wechat_redirect)  
  
  
###   
  
### 电报讨论  
  
  
[]()  
  
  
  
![扫码加入AI安全交流群](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX0Py7ibxdLKXia1pMziaic5vIE9XPXG9OGaeJDa07iaG10eicuzhW59nwpF5msHiaYZvfMqCNkx2aFDiaMzm3oAf4rTaHXU5UAI1mUYgts/640?wx_fmt=png "")  
  
  
  
![下载FreeBuf知识大陆APP](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX0TIGzII2Hcmtzu7AJeZFicnqd1mXojVoawje2uLxYqwJbVgzJpmSXzVhrpOsLurRZ2lVa4vfgLBqg7uJKbrKg5F18VzZxVPicZU/640?wx_fmt=png "")  
  
  
  
  

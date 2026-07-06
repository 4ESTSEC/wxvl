#  T3MP3ST安全框架将AI编程Agent变0day漏洞猎人  
 FreeBuf   2026-07-06 07:26  
  
![FreeBuf](https://mmbiz.qpic.cn/sz_mmbiz_gif/icBE3OpK1IX33J20ljZ7Z33snEmsia3NwlYbEbbFsKOKIOXOg6MnhdXbFmgzd21ibImOuklX76IcCbYqTW6AiccqFmdn0IBOaVIaWIo9msD0XFA/640?wx_fmt=gif "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX06M2lJn6eT8J9OxEcz0qZKBkIz2pawfIicvggzjat96Wia0BOIBuXnuZ4aaSMgNkk2LPeu1aD3ruagRSFFOMZpnqkicBLz7Wl9vE/640?wx_fmt=png&from=appmsg "")  
  
  
新发布的开源安全框架T3MP3ST正在将Claude Code、OpenAI的Codex和Hermes等通用AI编程Agent转变为自主红队操作平台，且无需新API密钥、云基础设施或额外计费。  
  
  
该框架由研究员elder-plinius开发，其本质是一个多Agent协调层而非自有模型，通过"侦察-利用-报告"的攻击链协调多个Agent实例。用户可通过基于Web的"作战室"界面或CLI将框架指向授权目标，本地运行的AI编程Agent随即成为任务执行中枢。  
  
  
Part  
01  
  
关键技术特性  
  
该框架采用"无密钥作战"设计，利用现有Agent会话而无需独立提供商密钥，并通过出口范围控制确保联网工具自动拒绝触碰范围外的公共主机。在XBOW的104项挑战XBEN测试套件中，T3MP3ST取得90.1%的pass@1分数（该黑盒基准测试自报成绩约85%），每个解决方案都经过提交标志验证，支持通过"verify-claims"命令按需复现。  
  
  
在40项任务的学术基准Cybench上，框架的单Agent ReAct循环实现了23项无提示破解。更值得注意的是，在2026年披露的10个真实CVE（涉及7种编程语言）测试集中，单个Agent准确定位了其中8个漏洞的具体文件、行号和CWE分类，工具包整体则识别出全部10个漏洞。开发者强调，虽然样本量较小，但这些漏洞均出现在模型训练截止日期之后，排除了记忆复现的可能性。  
  
  
Part  
02  
  
架构与能力矩阵  
  
框架设计将8个操作环节（侦察员、扫描器、利用者、渗透者、外传者、幽灵、协调员和分析师）映射到MITRE ATT&CK战术和网络杀伤链，但目前仅侦察引擎和单Agent利用循环完成基准测试且稳定运行，可从GitHub克隆。  
  
  
各领域支持状态如下：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/icBE3OpK1IX09sesnFEOVxGI4epehrFLsJMlvoJiauou7HHkVL68MtkCsvIoR03zyCL7jNwXZMDLX0AEhvI37Q8ib2lTqP1RiaoUNC4q0a92coo/640?wx_fmt=png&from=appmsg "")  
  
  
Part  
03  
  
行业反响与法律声明  
  
Reddit蓝队社区等平台的安全研究人员认为，该版本在自主红队领域具有重要意义，符合行业向AI驱动安全工具发展的趋势。此前Anthropic的Mythos模型在同类漏洞利用基准测试中已将误报率降低42%，XBOW评估显示其显著提升了漏洞导向生成和源代码安全分析能力。  
  
  
开发者特别声明，T3MP3ST严格限于授权测试、研究和教育用途，采用AGPL-3.0许可发布且不提供担保。未经书面明确授权对系统进行测试在多数司法管辖区仍属违法，操作者须全权负责确保行为符合法律和交战规则。  
  
  
参考来源：  
  
T3MP3ST Security Framework With 35 Tools, Turns AI Coding Agents Into 0-Day Bug Hunters  
  
https://cybersecuritynews.com/t3mp3st-security-framework/  
  
  
**推荐阅读**  
  
[](https://mp.weixin.qq.com/s?__biz=MjM5NjA0NjgyMA==&mid=2651341548&idx=1&sn=bb9edaa490d92c0258ff47c5dd29faf4&scene=21#wechat_redirect)  
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
### 电报讨论  
  
  
[]()  
  
  
  
![扫码加入AI安全交流群](https://mmbiz.qpic.cn/sz_mmbiz_png/icBE3OpK1IX0UGcrYtIkSYDEgbDkib0yMF23VlKQibpJyRnibia1cD3no5XF7Je0Sic98ytMyvbY9LhO8tKoxBlnibsAXh8CnBTYoAxLReujuqjomI/640?wx_fmt=png "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX1cNPEia7j7bXCX8P8iaDo801yQlaF965NduoqX5nEfgC2mLLgM6VdzcRdkYkeGebHaia3JRK31e08ibfS1WnmYl8DtvPf83e6XW6k/640?wx_fmt=png&from=appmsg "")  
  
  
  
  
  

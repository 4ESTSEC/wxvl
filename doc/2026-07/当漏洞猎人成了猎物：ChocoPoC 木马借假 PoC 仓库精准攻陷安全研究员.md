#  当漏洞猎人成了猎物：ChocoPoC 木马借假 PoC 仓库精准攻陷安全研究员  
 幻泉之洲   2026-07-10 01:47  
  
>   
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/tbTbtBE6TibfiaABhgVFQXqhAXwabt0Ft9dNm2POica5l6zbpIBkLTP9YXXSLBB8GGj4EKGckLgaeazWSEMCCasrLQGwbVxmAo9ibhJ2ZW4f0ts/640?wx_fmt=jpeg&from=appmsg "")  
## 攻击逻辑很简单，但极其阴险  
  
安全圈子有条不成文的规矩：新漏洞一出来，大家都急着找 PoC 做验证。厂商催、客户催，没人愿意从零开始写测试脚本。于是 GitHub 上的现成代码就成了救命稻草。这次攻击者抓的就是这个心理。  
  
1 月，YesWeHack 和 Sekoia 联合发布了一份报告[1]，拆解了这个叫 ChocoPoC 的木马。他们警告说，到报告发出的时候，恶意代码和控制端都还在正常运行。  
  
整个感染过程可以归纳为三步。你从 GitHub 克隆一个仓库，运行 pip install 装依赖。依赖里有个叫 frint 的包，它会连锁拉上另一个包 skytext。skytext 里夹带了一个编译好的小文件——Linux 上是 gradient.so，Windows 上是 gradient.pyd。你一启动 PoC，这个文件就醒了。  
  
不过它有个很贼的设计：它不直接发作。代码会先检查当前目录里有没有 EXPLOIT_POC.py 之类的文件。没有，它继续装死。这正是为什么沙箱经常测不出问题——你把包单独丢进去，没有完整的 PoC 环境做触发条件，它根本不亮底牌。  
## 偷什么，怎么藏  
  
一旦跑起来，ChocoPoC 就是一个完整的远控木马。它会从 Chrome、Brave、Edge、Firefox 里扒走保存的密码、Cookie、自动填充记录和浏览历史。同时，文本文件、笔记、本地数据库、Shell 历史命令、网络配置、进程列表，一个不落。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/tbTbtBE6TibcibBDYJFy42Ner7SxZAfpPChQ5TT3icwkF4SZFIpj2ANLCg0mpDS2B02SgOGxib8EoKyibOxqURUNU3MePicdWtfS5z2YEKiaNVxdg8/640?wx_fmt=jpeg&from=appmsg "")  
  
攻击者能做什么？执行任意 Shell 命令，随意跑 Python 代码，把整个文件夹拖走，甚至还能主动限速，让传输过程不那么扎眼。研究人员注意到几个命令名用的是西班牙语，代码还带着些小 bug，他们判断这更像是人手写的，不是 AI 生成的。  
  
真正让我觉得这对手不简单的地方，是它的控制端设计。ChocoPoC 不从某个固定 IP 领指令，而是通过 Mapbox 的数据集服务来通信。Mapbox 是个合法的地图服务平台，大量调用它的 API 一点都不奇怪。木马通过 DNS-over-HTTPS 来解析地址，还玩了一手域前置的伪装技巧，这让所有通信看起来都像正常的 Mapbox API 请求。大批量的数据上传则走另一台服务器：91.132.163.78。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/tbTbtBE6Tibf5onKfz0BJuSIsy234Mlia7YqZScHUu52aWAoJSJic7qAMibhic5icU3PMDSNWibl05WpLSSVWueziaj6eC3XVNgIDiaDVtSiaEDTw8Y5g/640?wx_fmt=png&from=appmsg "")  
## 有多少人中招  
  
YesWeHack 和 Sekoia 目前发现了至少七个假的 PoC 仓库，分别对应下面这些高危漏洞：  
- FortiWeb 路径穿越（CVE-2025-64446）  
- React2Shell（CVE-2025-55182）  
- MongoBleed（CVE-2025-14847）  
- PAN-OS 身份验证绕过（CVE-2026-0257）  
- Ivanti Sentry 命令注入（CVE-2026-10520）  
- Check Point VPN 身份验证绕过（CVE-2026-50751）  
- Joomla SP Page Builder 远程代码执行（CVE-2026-48908）  
光 skytext 这一个包就有大概 2400 次下载，大头上 Linux。下载量不能等同于受害数，但数据飙升的时间点刚好卡在那些大漏洞公开之后，这个关联性很难用巧合解释。再看 2025 年末的早期版本，那两个叫 slogsec 和 logcrypt.cryptography 的包，代码结构跟现在的几乎一样。Sekoia 判断背后是同一个人，原因是控制端的特征标记完全能对上。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/tbTbtBE6TibePzicp6Q7nwgLvtn7342dmibU1PTFzRtdjDPvdMJ8ViciaDUCB4cibASgyhGB8S1Le4GDdT8HCTGKLJ9WQemjjF0xfXY2YyHkibG32o/640?wx_fmt=jpeg&from=appmsg "")  
  
这位操作者在 GitHub、PyPI、Mapbox 之间来回切换，多个账号都是用泄露或盗取的登录凭据注册的。目前没有明确归属到哪个已知黑客组织。  
## 专盯安全研究员，这不是第一次  
  
说实话，安全研究员是高价值目标。他们天天跑不受信任的代码，权限还不低。机器上存着客户凭证、未公开的报告、正在进行的项目细节。拿下一台，可能撬动的东西远不止一台笔记本。  
  
MUT-1244 那次行动[2]就是这么干的，假 PoC 仓库偷了一批红队成员和研究员的 SSH 密钥和云凭证。  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/tbTbtBE6TibedHWEXy5ZsTOtop3k5TwrMytuCC57fbAlcS8roGbLCYLpNxBeVSliapgNhtrqqwSOfR4icu27uew5h1mz0RIFibialTUj21Siahpq4/640?wx_fmt=jpeg&from=appmsg "")  
  
更早的例子也不少。朝鲜的 Lazarus 组织在 2021 年就开始扮成漏洞猎手跟研究员套近乎[3]，发恶意的 Visual Studio 项目。2023 年甚至为了收割研究员，直接烧掉一个零日漏洞[4]，后面的波次到现在都没断过。  
  
更常见的黑产那边也不消停。Trend Micro 在 2025 年初发现一个假 PoC，针对 Windows LDAP 漏洞（CVE-2024-49113）（https://www.trendmicro.com/en_us/research/25/a/information-stealer-masquerades-as-ldapnightmare-poc-exploit.html），专门偷研究员的数据。2025 年底又冒出来一波，带着叫 WebRAT 的木马[5]，主要坑学生和初级测试人员。  
  
ChocoPoC 不一样的地方在哪？它把恶意代码藏在了依赖包里，你直接看 PoC 的代码，干干净净。正如研究人员自己说的，木马功能本身没什么新鲜的，"真正在变的是投放途径"。  
## 现在能做什么  
  
首先，把 PoC 当敌人看。不管它是不是来自你认识的人，也不管 GitHub 上星星有多少。新注册的账号、刚发布的仓库，能不用就别用。  
  
第二，别看只看 PoC 那个文件本身。把整个依赖链翻一遍，关注那些刚发布的包、陌生的维护者、隐藏了贡献历史的账号。  
  
第三，在隔离的虚拟机里测试。但有一点需要留心：光靠虚拟机还不够，因为木马需要检测到 EXPLOIT_POC.py 这个文件才会触发。真正靠谱的做法，是干脆不装那些不明来历的包。  
  
第四，查一下你的系统里有没有 frint、skytext、slogsec、logcrypt.cryptography 这些包，还有报告里列的那些文件哈希值。如果跑过，换掉所有凭证，重建系统。  
  
再说一个更大的隐患。Sekoia 在报告中点出一个供应链夹击的可能性：这些攻击的目标恰好是给 Nuclei、MDUT 这类工具贡献检测规则和 PoC 的人。一旦某位研究员被感染，恶意代码就可能顺着他们提交的内容，流进成千上万人信任的工具链里。这不是假设，是实打实的威胁模型。  
  
被偷了都不知道——这大概是对 ChocoPoC 最精准的概括。攻击者不用多高深的技术，只要搞清楚你的工作习惯就行。而安全研究员们最大的职业病，恰恰就是太信任代码。  
### 参考资料  
  
[1]   
https://www.yeswehack.com/news/chocopocs-vulnerability-researchers-trojanised-exploits  
  
[2]   
https://thehackernews.com/2024/12/390000-wordpress-credentials-stolen-via.html  
  
[3]   
https://blog.google/threat-analysis-group/new-campaign-targeting-security-researchers/  
  
[4]   
https://thehackernews.com/2023/09/north-korean-hackers-exploit-zero-day.html  
  
[5]   
https://thehackernews.com/2025/12/threatsday-bulletin-stealth-loaders-ai.html  
  
[6]   
https://thehackernews.com/2026/07/new-chocopoc-rat-targets-vulnerability.html  
  

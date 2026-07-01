#  匿名登顶中国第一，拆招“扫地僧”Agent记忆中心漏洞挖掘法  
 FreeBuf   2026-07-01 04:00  
  
![FreeBuf](https://mmbiz.qpic.cn/sz_mmbiz_gif/icBE3OpK1IX046oF8l9uotoBx1h1BLicEDPqrtTADyFOYQhulvfiatOmJANh67ZicZoRoavA8IYhvicqiaAB9gibJm0PYwAfDtKrfbb2VoyGXvteYA/640?wx_fmt=gif "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/icBE3OpK1IX1oh6crmcSPkZXjPMiaYUfjMUzIhMcTOuWPSKJ0ZupX5DJWUj6F56O3H3KG9jG9iaPKoycvbwyzbZiciaJCE3eFE0V6cEeuqFRg6tE/640?wx_fmt=png&from=appmsg "")  
  
  
6月29日，CyberGym榜单刷新。  
  
  
一个带有浓厚中文武侠色彩的  
代号MopMonk（扫地僧），突然出现在微软、OpenAI、Anthropic等全球顶级AI玩家齐聚的这张榜单上，以  
73.1%的成功率空降第7位，刚出世即位居  
中国第一。  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/icBE3OpK1IX1ZQaJzXqib31Yu7k3QowSMhCiaRDdcjxMGLuPNc7lGKkDf7oZCVObytBKiaAbibNQbib7ANsqWKC8adIuRn2C8AKTX0pNJaycETZ5I/640?wx_fmt=jpeg&from=appmsg "")  
  
  
除了榜单上的成绩，  
MopMonk Agent像它的东方古风代号一样低调，仅在GitHub上公开了一份详尽的技术报告。  
  
  
技术报告入口：  
  
https://github.com/MopMonkAI/MopMonkAgent  
  
  
成绩单之外，最让人惊讶的，是底层基座模型体量上的巨大悬殊——MopMonk Agent使用国产MiniMax M3（总参数量约4280亿）作为基座模型，与参数量高达10万亿级别的Claude Mythos 以及GPT完全不在同一个量级，但在网络安全能力输出方面却跑出了同一梯队的表现。  
  
  
**01**  
  
  
  
**CyberGym**  
  
**AI安全综合战斗力的“华山论剑”**  
  
  
我们先搞清楚CyberGym榜单的含金量。  
  
  
CyberGym由UC Berkeley团队开发，核心论文发表于ICLR 2026。它的设计理念极其硬核：  
不以“是否识别出漏洞”论英雄，只以“能否生成触发漏洞的有效输入”定成败。  
  
  
每道题提供一个漏洞描述和一个未修复的代码库。Agent的任务是生成一个PoC输入，满足两个条件：在未修复版本上成功触发漏洞；在已修复版本上不再触发。这是一个  
差分验证过程——必须真正让程序“崩”在正确的地方，容不得半点模糊。  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/icBE3OpK1IX0fNpd8USOu9EUOXEZongVGOQ7QlZv6dfVUq3YEhQlh7jaBg80GAYYUjM9ibekE4Vx2vI5kBplkRp5ibp7iboUrd28C7FzMmJz9Wg/640?wx_fmt=png&from=appmsg "")  
  
  
题库规模是1507个真实漏洞实例，横跨188个大型开源项目。这个体量是此前最大公开基准（NYU CTF，约200题）的  
7.5倍。  
  
  
更关键的是任务环境的  
封闭性。Agent无法联网搜索、无法求助外部知识库，唯一的信息来源是代码库本身和它自己积累的探索记录。模型预先训练中是否“见过”某个漏洞并不重要，即便见过，要在封闭环境中独立复现出来，依然需要完整的推理-执行闭环。  
  
  
面对  
任务长、反馈稀、约束严的考核设计，通用Agent极易“迷失方向”。CyberGym论文指出，即使是最先进的模型组合，在该基准上的初始成功率也仅约20%。而MopMonk“扫地僧”以73.1%的成绩，站上了这份巨头云集的榜单。  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/icBE3OpK1IX1WhL7WO3ic8QPzrZBcjaD8crTpyfzXw0sHl0oLndCat8MuUJiakSn7lphmg7au2cpo6KA9jLaC2UeovnUdJicvKO5fibuCU7RZvsA/640?wx_fmt=jpeg&from=appmsg "")  
  
  
**02**  
  
  
  
**漏洞挖掘真正的难点**  
  
**在“知道”和“做到”之间**  
  
  
CVE描述已经把漏洞是什么告诉了Agent，但真正的问题在于：从“知道漏洞在哪”到“生成一个能触发它的输入”，中间隔着一条漫长的证据链。  
  
  
触发一个漏洞，通常需要满足一堆隐式条件：特定的调用路径、特定的输入格式、特定的边界值、特定的程序状态。这些条件不写在CVE的描述里，而是藏在数千个文件、数百万行代码的调用关系和数据流中。  
Agent得自己找到他们、验证它们、最终把满足所有约束的那个输入构造出来。  
  
  
每一次失败，代码只反馈两个字：“没触发。”至于为什么，是路径走错了？格式被拒绝了？值还不够大？还是触发了但被异常处理吞掉了？代码不会主动告诉你。  
  
  
靠“大力出奇迹”的基座模型硬扛，通常会撞上两堵墙：  
  
  
第一堵：“无状态”的短程 ReAct 循环。这类Agent靠聊天历史当记忆。一旦编译报错、运行崩溃、变异失败，冗长的系统日志会迅速淹没上下文窗口。Agent的精力全花在解析噪声上，每一轮都像从头开始猜，失败经验无法沉淀。  
  
  
第二堵：“全量堆砌”的长上下文过载。把整个代码库和所有日志一次性塞给模型。看似“什么都知道”，但在上百轮的交互中，模型会逐渐注意力涣散，忘记中间部分的信息，反复走已经证明失败的老路。  
  
  
但如果能把每一次失败都记录下来，把它们拼在一起，就能逐渐勾勒出成功条件的边界。  
  
  
这也是MopMonk设计的方向：  
把漏洞挖掘从“读上下文然后猜”重构为“基于证据的收敛过程”。  
  
  
现在最核心的问题来了：  
在漏洞挖掘过程中，MopMoink Agent到底是怎么处理复杂信息的？  
  
  
**03**  
  
  
  
**尝试解读“扫地僧”武功**  
  
**「记忆结构化」一条不同于主流的技术路线**  
  
  
MopMonk的技术路线不依赖纯粹的聊天记录或扁平的长文本，而是构建一个围绕漏洞生命周期的  
Vulnerability-Oriented Memory Design。  
  
核心可以概括为：  
  
将代码观察、负面证据、候选输入、验证反馈等信息持续组织为结构化的漏洞记忆，使后续探索从积累的证据中收敛，而非反复从头试错。  
  
  
这个思路的特别之处在于：  
它承认了“试错”在漏洞挖掘中的不可避免性，但试图让每一次试错都成为后续决策的约束条件，而非被遗忘的噪声。  
  
  
从GitHub技术报告来看，MopMonk的记忆系统将漏洞挖掘中的核心实体高度抽象并解耦为7个维度的结构化Schema记忆网络：  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/icBE3OpK1IX2xRia5nTYZX2kNB7Nc2N0Bq3u2vu7gu6ktPOydoaiaqL0bh0xLRzAQCH06iapPq2Nc98PXzCEujTJsia7bD6Y3HpqTIKDiaGJVRtDI/640?wx_fmt=png&from=appmsg "")  
  
  
这种从“抽象计划”到“硬约束”的转换，大幅降低了模型在决策时的认知负担。模型不需要规划“接下来三步怎么走”，只需要在当前约束空间内选择一个满足条件的输入进行验证。  
漏洞挖掘从一个开环的猜测过程变成了一个闭环的证据收敛过程。  
  
  
传统方案中，每一轮尝试都是独立的：模型读一遍代码库→生成一个候选输入→执行→失败→下一轮从头再来。负面经验没有积累，探索没有方向感。  
  
  
而在MopMonk的设计中，每一轮尝试都在向记忆系统写入新的信息增量。  
失败不再是“无效消耗”，而是变成了一条新约束。  
随着探索轮次增加，候选PoC的生成空间被持续收窄，最终收敛到那个能够触发漏洞的点上。  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/icBE3OpK1IX1WUvic6g8mEvGtHNZH0gezcnt6Rr0y2CCTiarljwWvvsWs0tU1hZWOrrTysOE1PWldHVXOyDWbakr4VpOsjYmWfgtdOHwov7ouc/640?wx_fmt=jpeg&from=appmsg "")  
  
  
**04**  
  
  
  
**基座能力与Harness工程：并行探索**  
  
  
要实现这套记忆驱动的收敛机制，需要两个层面的支撑。  
  
  
一是基座模型，提供“能力基础”。  
MopMonk选用的是国产开源基座MiniMax M3。技术报告给出的选型理由包括：  
  
  
1M上下文+稀疏注意力。  
工业级C/C++代码调用链深、逻辑复杂，模型需要同时吞吐大量源码并精准检索关键信息。M3的稀疏注意力机制既能覆盖超长上下文，又能保持推理效率。  
  
  
MoE架构的跨栈能力。  
漏洞挖掘中，模型需要在读C/C++源码、写Python变异脚本、解析Linux编译报错之间频繁切换。M3的专家网络在多任务并发和工具反馈吸收上表现稳定。  
  
  
长周期协同稳定性。  
长线程安全任务最怕模型跑了几十轮后突然输出乱码或指令断裂。M3在多智能体长周期设定下的状态稳定性，为MopMonk的长时间探索提供了底座保障。  
  
  
二是Harness执行工程层，  
负责把基座能力转化为实际挖掘行动，围绕这套漏洞记忆，系统在执行层主要做三件事：  
  
  
1. 工具与流程调度。  
漏洞挖掘要在读源码、构建编译、执行验证、解析 sanitizer 反馈之间反复切换。系统统一调度这些步骤——何时读文件、何时编译、何时跑验证，而不是让模型在原始日志里自己摸索。  
  
  
1. 记忆的解析与回写。  
每轮探索结束后，系统把执行结果解析、归类,写回对应的记忆模块(代码路径、输入格式、候选 PoC、失败证据、验证状态等)，让记忆持续保持最新、可复用。  
  
  
2. 基于证据的迭代收敛。  
CyberGym的任务要跑几十上百轮。系统依托"下一步约束记忆"，从已有证据中提取下一轮必须满足的约束——需要覆盖的分支、需要调整的字段、需要排除的失败原因，使每一轮探索都在前一轮的基础上继续收敛，而非从头再猜。  
  
  
用一个比喻来理解：  
基座模型是大脑，负责想“漏洞在哪”；记忆系统是笔记本，负责记“知道了什么”；Harness执行工程层是神经系统，负责把想和记的东西转化成“下一个动作”。  
  
  
M3提供的是“想得深、记得住”的基座能力，而Harness执行工程层负责把这种能力转化为“咬得死、打得准”的实际执行。两者深度耦合，才最终铸就了榜单上73.1%的成绩。  
  
  
Harness不仅管理单条探索路径的迭代，还支持一个更进一步的策略——  
并行探索  
。  
  
  
在CyberGym极其苛刻的4小时超时设定下，单线程的顺序试错往往会因为卡在某个复杂的Harness构建或特定的格式校验上而耗尽资源。为此，MopMonk在执行工程层实现了  
共享记忆网络下的多智能体并行探索  
。  
  
  
多个探索尝试共享同一份漏洞记忆，可以从补丁线索、程序入口、文件格式字段、Sanitizer类型、边界条件等多个方向同时推进，并彼此继承失败经验和验证结果。这种非对称的成本优势，让系统  
在有限的计算预算内，提升了有效假说的试验密度，显著减少了多线程之间的重复无效探索。  
  
  
**05**  
  
  
  
**AI行业启示**  
  
**迈向AGI，垂直工程Harness复利时代已至**  
  
  
M  
opMonk“扫地僧”在CyberGym上的惊艳表现，给AI行业和网络安全在内的专业领域带来的，是一种更实在的思考：当大模型真正落地到高对抗、低容错的垂直场景时，胜负手到底在哪？  
  
  
过去几年，大家的惯性是  
“堆参数”  
——参数越大、模型越强、榜单越高。但CyberGym这类基于真实漏洞的评估任务给出了另一种答案：  
在长周期、多工具交互、反馈稀疏的复杂任务中，Agent的“执行系统”正在变得比模型本身的“推理能力”更重要。  
  
  
基座模型决定了智能的  
下限  
，而一套为特定场景量身定制的Harness设计——能把垂直领域的知识抽象出来、把失败经验沉淀为约束——决定了智能可以兑现出来的战斗力  
上限  
。  
  
  
更重要的是，这套Harness设计具备独特的  
资产复利属性  
。模型基座每隔几个月就会迭代一次，但一套在真实漏洞挖掘任务中被反复打磨、沉淀了攻防经验与失败教训的Harness，是可以跨越模型代际持续积累的工程资产。  
相比再堆一倍参数，一套精密的Harness可能有更长的半衰期。  
  
  
MopMonk AI示范了一条路：在通往 AGI 的真实演进中，精密的垂直工程化韧性，同样拥有撕开巨头防线的澎湃力量。  
  
  
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
  
  
  
  

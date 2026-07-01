#  ISSTA 2026 论文解读丨大语言模型离替代人类挖漏洞还有多远？——评 SAGE：基于大语言模型的漏洞检测信号放大引导嵌入方法  
原创 王凯
                    王凯  信息网络安全杂志   2026-07-01 04:00  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/FNlvhjUaDTO1gjNIkrJSsWVRRRttOSfibJOvqIvOe1tGROAAB1iaMClnxyOCyLnzRZnPvIjYMqQQ2xwqz6RGrJ2xrrPJyyLFBqb5IeGJLZXwY/640?wx_fmt=gif&from=appmsg "")  
  
**引子**  
  
  
  
漏洞挖掘是网络与信息安全领域的核心技术，早期主要依靠静态分析、规则匹配和动态测试等方法，容易遇到扩展性差、路径爆炸、规则依赖强等问题；后来图神经网络把代码图结构引入，能更好地表示控制流和依赖关系，但仍然比较依赖外部结构。自2022年ChatGPT诞生，基于大语言模型（LLM）的漏洞挖掘成为了新的前沿方向，但现有方法容易在复杂真实代码里出现“看起来懂了，其实抓不住关键漏洞点”的问题。没有显式建模中间层信号的变化，等到最后一层再做判断时，真正有用的漏洞线索往往已经被稀释了。  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/FNlvhjUaDTNkEWmzlsnicatHg4SzmkxueSibKmmaoozK1R1TkfIa16lvxDCntYE8RDI4NbOmcp02nb74Yzjyr9WtbPicJyibbHhWuNDsFtr02T0/640?wx_fmt=gif&from=appmsg "")  
  
**论文速览**  
  
  
  
这篇发表于 ISSTA 2026 的论文 《SAGE: Signal-Amplified Guided Embeddings for LLM-based Vulnerability Detection》（CCF-A，直接接受率11%，99/888），由山东大学徐明辉、张悦、成秀珍教授课题组与南京大学吴昊教授课题组合作完成，提出了一种面向漏洞检测的新方法 SAGE。它的核心思路很清晰：不要只盯着模型最后一层的输出，而要去中间层把那些微弱的漏洞信号提前找到并放大出来，让大语言模型尽可能少疏漏。 为此，作者在大模型中间层接入了任务条件化的稀疏自编码器，用它把混杂在一起的表示拆开，让漏洞相关特征尽量和普通语义分离，再交给分类器判断。论文在 BigVul、PrimeVul 和 PreciseBugs 3个主流开源数据集、13种编程语言上做了全面测试，结果显示，SAGE 在多个基座模型上都取得了更好的 MCC（马修斯相关系数，是衡量漏洞二分类模型整体效果的重要指标），并且在跨语言、跨分布场景下更稳，作者还报告其内部信噪比最高可提升 12.7 倍。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/FNlvhjUaDTPNrNv5DlMyo5uvdcRY8F52glUfSIqwOeXI6icWpn1havY4jf92RqryQqyBGv3wSoH2ISesibibj13VH38uzVBYLibFqFYtKgrSBxI/640?wx_fmt=gif&from=appmsg "")  
  
**深度剖析**  
  
  
  
这篇论文最有价值的地方，不仅只是结果更高就好，而是它把问题讲清楚了。过去很多工作都在提示、微调、强化学习上做文章，但大多还是围着最终输出转。SAGE 提醒我们：到了深层，模型更多在处理“整段代码在做什么”，真正和漏洞有关的细小线索反而会越来越弱。论文的分析显示，在深层里，漏洞信号占比甚至不到总激活强度的 5%。这说明问题未必是模型不会推理，而是有用信息没被保留下来。   
  
SAGE 的好处就在这里。它像是在模型中间加了一个“筛子”：把和漏洞无关但很强的普通语义过滤掉，把真正关键但很弱的异常特征提出来。论文结果也说明，不少基线方法在新数据上会出现“高召回、低精度”的问题，说白了就是什么都怀疑有漏洞，误报很多；而 SAGE 虽然没有一味追求高召回，却能明显提高精度和 MCC，这对实际安全检测更有意义。  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/FNlvhjUaDTM7o0YVbWUgyAR3XK96KFdpk8iapX6r9wpKE9dx6GeVx4bdVmzvsibplKWopQBoriaNpk4KoF65u73V40SgNdPd4AuUhichWtGqFlE/640?wx_fmt=gif&from=appmsg "")  
  
**局限与展望**  
  
  
  
大语言模型离替代人类挖漏洞还有多远？即便有SAGE的优化，相关性能指标离完全满足安全服务行业的真实、复杂需求还有一定距离，比如跨文件依赖、超大项目、告警解释和修复建议等问题，还需要继续完善；漏洞挖掘智能体设计也将是未来的一个重要方向。  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/FNlvhjUaDTOWvjbxAzj72FySQx504lpiahglRJeH0a75vEbIyrxSicUWXIp2b3sicnGCjhfU1xsJVkgNicH4cAsJjSxaEoUHArlfhaW4j7M2QNk/640?wx_fmt=gif&from=appmsg "")  
  
  
  
点评专家：王凯（哈尔滨工业大学（威海） 计算机科学与技术学院  教授）  
  
原文标题：SAGE: Signal-Amplified Guided Embeddings for LLM-based Vulnerability Detection  
  
原文作者：Zhengyang Shan, Xu Qian, Jiayun Xin, Minghui Xu,Yue Zhang, Zhen Yang, Hao Wu and Xiuzhen Cheng  
  
期刊/会议：ISSTA 2026  
  
DOI：  
https://dblp.org/rec/journals/corr/abs-2604-19031.html  
  
版权与来源声明：本文依据《中华人民共和国著作权法》第二十四条之规定，为介绍、评选之目的，在此适当引用。原文版权归原作者所有。  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/icL7Q0hLWsN8yDJWicSECDq8dgel7DctAMAnNheJf2kkfQOiaFMdZaWNIDt9IxOkEhI5TJar4wnyAiba9twTOxeKZg/640?wx_fmt=other&wxfrom=5&wx_lazy=1&randomid=qnylxjok&tp=webp#imgIndex=2 "")  
  
**信息网络安全**  
  
《信息网络安全》创刊于2001年，是由公安部主管，公安部第三研究所、中国计算机学会主办，面向国内外公开发行的国内首批信息安全类期刊之一，于2015年成为中国科技核心期刊，2017年成为中国科学引文数据库来源期刊，2018年成为中文核心期刊，2022年入选CCF计算领域高质量科技期刊分级目录。  
  
  
![图片](https://mmbiz.qpic.cn/mmbiz_jpg/icL7Q0hLWsN9e1fkuM1ibgD3PcZiaqJrZia7KQWDwichD8lvo4RqfPcNkuyqje45IOXD0HocBDntaDdK4tibWoTIs5Ww/640?wx_fmt=other&wxfrom=5&wx_lazy=1&randomid=fbknkhlb&tp=webp#imgIndex=3 "")  
  
  
中文核心期刊  
  
中国科技核心期刊  
  
中国科学引文数据库来源期刊  
  
CCF计算领域高质量科技期刊  
  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/v4vz52CcB12BRNZGqdRDIBsUQ6WickDoUNkuVicKXooNbRSzdGDGuJtxJodlbpr1B07yAReAz5V5jj47Yaq7ujRw/640?wx_fmt=other&wxfrom=5&wx_lazy=1&randomid=23elspay&tp=webp#imgIndex=4 "")  
  
我们在不断努力和完善中，期待您的关注和支持！  
  
  
  

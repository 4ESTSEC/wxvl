#  “始”逆向[ 零创技 ]—猎人笔记（4）—Windows内核漏洞利序言+30小时思维逻辑的重铸与安全逆向“沙盒”标记H6！  
原创 Bl0ckdev
                        Bl0ckdev  Esn技术社区   2026-09-22 08:35  
  
> 该系列中出现的各种硬性要求,无法完成白嫖！包括：Ai API /本地显卡等等相关的一些每个月需要固定付费的内容！所以如你想要真正的白嫖！该系列不合适！因为“实验室”不允许！  
  
> 本系列不适合0费用白飘！需要自购Ai API和其他的服务器费用！  
  
  
  
[ 零创技·逆向工程 ]的核心观点：不择手段,只要能拆。  
  
注：  
“要拆除一座建筑物，你需要了解它是如何建造的  
！”  
  
  
地址：[@Esn技术社区](https://mp.weixin.qq.com/mp/readtemplate?t=pages/link_mid_jump&biz=MzU5Njg5NzUzMw==)  
   
  
Ai/Agent：Claude+Deepseek  
  
垂直领域：  
黑客神秘的软件逆向魔法术  
  
我们的编码技能：  
   
C++/Rust/Python/C  
  
我们的提前声明:  
发布的所有内容均建立在假设你已经有了最基础的C#/C++最基础的编码实践能力。(最低懂得并理解和已经动手进行一次  
⭐  
⭐以上软件的组成结构[不局限于使用Ai]  
)  
  
我们的目的：基础的漏洞利用开发者逐步的成长为内核漏洞利用开发,是一个长久的路程。我们需要通过逆向来学习更多技术！  
  
  
  
请回溯到以下文章：  
  
[领创技·更新起源于.纪录片：《如何解锁一切：逆向工程》Ghidra](https://mp.weixin.qq.com/s?__biz=MzU5Njg5NzUzMw==&mid=2247493423&idx=1&sn=26aacc6ad97d85730d80100f5b0101fe&scene=21#wechat_redirect)  
  
  
  
结构化笔记中的废话篇：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/icfnkibn16VegDf0ENlowF6bgjN62DA9xVM9Squ1ibZNWbH9Nc2viasOpPO2uibQoUzPcyicrPCkdaFsh4oGNq76YmvY7ZprMZFeMWWmmqwQricE3U/640?wx_fmt=jpeg "")  
  
[黑客逆向—结构化笔记（1）— 以恶意软件分析为例一文说清楚到底要什么软件硬件的支持！](https://mp.weixin.qq.com/s?__biz=MzU5Njg5NzUzMw==&mid=2247494040&idx=1&sn=453f48e7c7eff23295aacc1dc81e7c60&scene=21#wechat_redirect)  
  
  
[黑客神秘的逆向—结构化笔记（2）— "理论"我们走过的实践与试错路程分享](https://mp.weixin.qq.com/s?__biz=MzU5Njg5NzUzMw==&mid=2247494085&idx=1&sn=592111a4463ff804fd44fc6c2227ca12&scene=21#wechat_redirect)  
  
  
[黑客逆向—结构化笔记（3）—NullDeref·98 更新 《Ai赋能应用逆向工程》死磕流·聚集地||最后一次发展策略更新！](https://mp.weixin.qq.com/s?__biz=MzU5Njg5NzUzMw==&mid=2247494133&idx=1&sn=ad319b3490d59547067bbce6372294f8&scene=21#wechat_redirect)  
  
  
  
警告：  
  
-   [@Esn技术社区](https://mp.weixin.qq.com/mp/readtemplate?t=pages/link_mid_jump&biz=MzU5Njg5NzUzMw==)  
  
 的《[#逆向工程]()  
  
》是以“红队/  
软件逆向  
”为主！目前暂不涉猎进入硬件范围内。 我们的所有内容全部优先储存在Discord[   
NullDeref·bat   
]服务器之中！  
  
![](https://mmbiz.qpic.cn/mmbiz_png/icfnkibn16VeiaWjXibHIEEgWSjAmgTzVmS1Fsr6Y7cdIUmoEBxw63ryRavgOPX01ps8nTpJIhtAr6UqibzicjBpO6h91A0vBmdyIKXQ4kYxZ2p9E/640?wx_fmt=png&from=appmsg "")  
  
  
  
-  [@Esn技术社区](https://mp.weixin.qq.com/mp/readtemplate?t=pages/link_mid_jump&biz=MzU5Njg5NzUzMw==)  
  最主要的路线是：红队/漏洞复现和利用相关的“编码/逆向”领域研究。我们建议大家从Window10开始,不过最佳的应该是直接从Windows11开始。  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/icfnkibn16VehiboNKZhftpICOZlBzIpic6lyxy6eyic3Cpj4J9J4hzPaCgWovmbFFg9b6OcZVH4ZCaDvwfZHdD83iasvejGbAOWsfEzv5sHEKhIM/640?wx_fmt=png&from=appmsg "")  
  
  
声明  
：我们所有研究的工具,内容,仅供研究和学习之用。本人和社区不鼓励也不支持任何非法或者违法行为。（  
你本人必须对你的行为承担全部责任）  
。  
所有测试必须在受控环境下进行，并遵守我们的互联网信息安全法。  
  
  
  
请注意永久不变的顺序：  
  
1、ROP为先   
  
2、EDR为后  
  
  
沙盒·标记：“汇编应用”  
  
  在加入Esn- [#应用逆向工程]()  
  
 之前，我们首先需要了解汇编语言编程的基础知识。这需要一些背景知识才能更好地理解。但是，为什么我们需要x64汇编语言呢？用“普通”和Ai生产的编程/脚本语言编写漏洞利用程序/软件逆向难道不够吗？答案是否定的。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/icfnkibn16VehZCFvb6AOGibBQwAAy7hSFGRfuGwhocXWbjKPUh2nLFFLSLW3Cz6NrbYZndO3C6Vra1pfDvoqEYt2TzrEoNz7waBg6IMZEoeRw/640?wx_fmt=png&from=appmsg "")  
  
  
  
沙盒·标记：“时间90则”  
  
  我们标记的 沙盒·时间· 九十分钟 高度专注时刻。其他的时间可以去任何事情！因为这是经过管理员在工作中摸鱼好几年总结的经验！我个人的绝对专注时间其实也只有2小时左右。其他的时间都会选择去预览大佬博文和最新的黑客新闻或者是黑客论坛中的讨论和计划研究。所以我们选  
择  
的公开时间设定是：90分钟作为高度专注时间。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/icfnkibn16Vegdxh73pzlarOibHknfNBh2ictgicrztBQPkOic5dFjFQyxfp7bic1UtrlC3ibicBJDJ7dtAWCSqqU95U3agvTjhboWwj4rEeSOov5K3k/640?wx_fmt=png&from=appmsg "")  
  
  
沙盒·标记：“刻意训练能力”  
  
  我们标记的能力链 [[#windows]()  
  
 ]→ （应用逆向）  
→ （EDR）  
→ （CVE）！我个人专注的是windows内核漏洞利用相关的技术学习研究！但  
由于该主题的复杂性和庞大性,在服务器内团队不得不进行小部分的切割和基础的重叠。  
1. 1-   
W应用逆向工程  
  
1. 2-   
Wx64汇编掌握  
  
1. 3-   
WEDR逆向工程  
  
1. 4-   
W/LCVE漏洞法  
  
1. 5-   
W恶意择件研究  
  
  以上内容仅限ESN技术社区合作Disord服务器“  
NullDeref·酒吧  
”！标签：  
H6  
 将由团队选择的是把技术连需要融合的基础进行合并,并每个人都需要亲自测试自己的30小时,来彻底的深度发现自己到底热爱的是那个领域i！  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/icfnkibn16VegEGRbEhicFAXOSlkgDrSBlpnTpjiapiaONr7UZjpt9M26ocQsI8HdxF1BIRbnuCV7JWQwc1E4ib3ibkEZl8Jicib5f6yDxGPIFbzlSic4/640?wx_fmt=png&from=appmsg "")  
  
  
沙盒·标记：“防火墙·H6”  
  
  “NullDeref·酒吧”作为我们研究的讨论逆向技术/思维/行为类的服务器。我们在内研究一些新的技术和重温必要的老知识来提升自己的技能链。因为Ai的速度很快!我们需要快速地融入到Agent适配的生活之中！！  
  
  我们选的安全沙盒=累计赞助与消费=200+以上的可以直接注册并授权我们的H6用户组,标签。累计已通过Wx：  
hackr2010  
和下方"  
赞赏码  
"历史记录为主。目的可以让团队与大家相互之间都可以保持最安全最舒适的环境中互动与交流。  
  
   
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/icfnkibn16VeiaoP6DdJJibAaxPqrxQO0aweY3Tv5AVkXmNNQpKwLYAbby1YPBMqy2m69DzEauURSiamYApEm0maAkNjGibF2XvrEZqpJgFssgIKc/640?wx_fmt=jpeg&from=appmsg "")  
  
  
沙盒·标记：“初期技能篇”  
- 0· 基础应用逆向全逻辑和路径。  
  
- x· 汇编在应用中的角色  
  
- 1. 如何通过逆向发现漏洞编写出对应的应用  
  
团队正在更新内容是：应用程序逆向指南和逆向找出相关可利用"值"后如何通过编码写入对应的程序进行执行我们需要的画面感。难度属于：社区H9   
初级  
技能。人人必须掌握！  
  
  
沙盒·标记：“历史授权信息”  
  
[19·今日H6标签授权完毕](https://mp.weixin.qq.com/s?__biz=MzU5Njg5NzUzMw==&mid=2247494072&idx=1&sn=3b14af2da4ff360797dc2b00948fc496&scene=21#wechat_redirect)  
  
  
[2.ID:成员 H9用户组升级完毕](https://mp.weixin.qq.com/s?__biz=MzU5Njg5NzUzMw==&mid=2247494026&idx=1&sn=e4e82865f015391d63b299c35244bbe1&scene=21#wechat_redirect)  
  
  
[1.ID:成员 #H6 升级H9(成员)授权完毕](https://mp.weixin.qq.com/s?__biz=MzU5Njg5NzUzMw==&mid=2247494014&idx=1&sn=b0248673a8a539961cf9df6b7a9d6642&scene=21#wechat_redirect)  
  
  
[2026授权H6标签(活跃群-Proof)](https://mp.weixin.qq.com/s?__biz=MzU5Njg5NzUzMw==&mid=2247494009&idx=1&sn=7a58840be544a29151d47dea8d45bdde&scene=21#wechat_redirect)  
  
  
[ID:成员 #H6 升级小组(成员)授权完毕 紧急发布通知_ 请与ESN管理员进行联系！！](https://mp.weixin.qq.com/s?__biz=MzU5Njg5NzUzMw==&mid=2247493974&idx=1&sn=a11dea00d7073d96898e84855ba3ee62&scene=21#wechat_redirect)  
  
  
  
全球通用互联网资区：  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/icfnkibn16VehcSlZP44rAXgiaPYm3KE53qIr42zAU4ibMy9aeuVZfpwheVnsL7DYibd3FAZ7mDXGnWDM2DNAibTDm8SJ81b4YapcM6lyANEXFgsM/640?wx_fmt=png&from=appmsg "")  
  
  
Github  最全逆向资料库  
> https://github.com/onethawt/reverseengineering-reading-list  
  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/icfnkibn16Veg6fSN1A4URurSbib0eI8nnAxEVsJuCJKxFexbRLLZ6vwtWUZ9JVrZ00icmPLpuzuGrFH0XXI5yXibbulHDLXU31Qp6cVW5Utibdn0/640?wx_fmt=png&from=appmsg "")  
  
  
  
关键词区域  
  
[#软件逆向]()  
   [#逆向工程]()  
  [#游戏逆向]()  
 [#黑客社区]()  
    
  

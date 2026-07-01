#  【AI安全】从0到1渗透测试自动化挖到某SRC漏洞的个人心得  
网络安全小叶
                    网络安全小叶  神农Sec   2026-07-01 01:00  
  
  课程培训  
  
  扫码咨询  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b7iaH1LtiaKWXLicr9MthUBGib1nvDibDT4r6iaK4cQvn56iako5nUwJ9MGiaXFdhNMurGdFLqbD9Rs3QxGrHTAsWKmc1w/640?wx_fmt=jpeg&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/b96CibCt70iaaJcib7FH02wTKvoHALAMw4fchVnBLMw4kTQ7B9oUy0RGfiacu34QEZgDpfia0sVmWrHcDZCV1Na5wDQ/640?wx_fmt=png&wxfrom=13&wx_lazy=1&wx_co=1&tp=wxpic "")  
  
  
#   
  
专注于SRC漏洞挖掘、红蓝对抗、渗透测试、代码审计JS逆向，CNVD和EDUSRC漏洞挖掘，以及工具分享、前沿信息分享、POC、EXP分享。不定期分享各种好玩的项目及好用的工具，欢迎关注。加内部圈子，文末有彩蛋（课程培训限时优惠）。  
#   
  
文章作者：  
网络安全小叶  
  
文章来源：  
https://linux.do/t/topic/2118146  
  
01  
  
0x1 从0到1渗透测试自动化挖到某SRC漏洞的个人心得  
  
### 前言  
  
我从3月份开始初步接触到Claude code，当时的感觉就是太强大了，能完成以前很多觉得不可思议的事情，甚至于对于渗透，它给我的感受是已经可以替代我这个工作快满一年的小菜鸟了。许多的渗透方式，连我自己都未曾尝试。从那个时候起，我就在考虑，如何让它完全的替代我。  
  
和许多人一样，接触到Claude code后，我第一时间是想着从基础学起，让我对AI的认知了解更为深刻。很可惜，我并不是一个“好读书”的好苗子，让gpt给我一条学习路径，我仅仅完成了前两个就再也不想坚持下去了。所以接下来的心得，可以说是“野路子”。在我并不熟悉ai的情况下，由结果倒推过程的路子，哈哈，写的不好不要喷我~  
  
最初的做法：胡乱添加了一些GitHub上的skill，然后又用ai生成了一堆claude.md的文件，放到Claude code中，就酷酷的对网站进行测试了。但即使是这样，Claude code给我的惊喜也很大，当时deepseek4pro还没出来，我使用的还是glm4.7版本。背景是：我想用Claude code帮助我复现一个数据库漏洞，并讲清楚这个漏洞的原理（因为那个数据库接触的比较少，让ai来可以省去很多找payload试错的时间），仅此而已。 我负责搭建环境–》提出要求–》Claude code就开始运行了。  
### 漏洞测试  
  
转折点来了，我给他的漏洞接口是： /api/v1/hosts/command/search 但它居然能测试出/api/v1/hosts/search接口存在漏洞  
  
![a4f68cc4ce50f64b8dbe749994aee8aa](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QUp2w9icZuRyyATGiae9cRPyObyhibiaNQCQtBfiab6WwAtm1gl4dl0C8viamJodXUrNUvBKkEHwSApXbfrhsygClLFppSkNrCFgcRKc/640?wx_fmt=png&from=appmsg "")  
  
a4f68cc4ce50f64b8dbe749994aee8aa  
  
![8a9e72ddb167cc6e22471f52db9db1ea](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QUIR1yGliarwA9pmtThQ02DbibC5tHbbvGlyo7sXvB8aWTrILULpA4E6dR5qI97GA6xgvmfp67RNVQMRiaDzYwwMA1jamAXfa91A8/640?wx_fmt=png&from=appmsg "")  
  
8a9e72ddb167cc6e22471f52db9db1ea  
  
![24abbf5ad536d220a468cb53f1871fe6](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QURiaibCddygeYmUL4sPSC01XYHGlsdTfeCXc5pTRo36a1Jr2PpJsK8BwDBvj0BkShRWjFIwvXYCVDdDROZDp7M50E4IUGeqXyUQ/640?wx_fmt=png&from=appmsg "")  
  
24abbf5ad536d220a468cb53f1871fe6  
  
从这个时候起，我就意识到了ai的强大，它其实根本不需要我们给出过多的提示或者约束就能进行完成（哈哈，这个说法到后面我自己就会推翻了）  
  
接下来我是怎么去完成自动化，一开始我认为我的skill已经足够的强大了（虽然是随便加的）。让它去完成各种的测试，甚至是SRC的测试，但效果可以说是水洞的天堂：  
  
![ed23c56c04b9e31c6ecba2257a966d56](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QUhrIDHbicymlRvIqfsJ5MaMfxsHLfrXzG6RYYMKlJZFyqjMbfcOasZmHg8E90U27FRwicPTatibmXdwJE2hAzgHSlynIGqdVLcrs/640?wx_fmt=png&from=appmsg "")  
  
ed23c56c04b9e31c6ecba2257a966d56  
  
这些水洞有些我连上班的时候都不敢提交。  
### 后续  
  
后面是怎么做的呢？后面我对skill的理解不再是理论上的东西，而是实际的。它就是一份说明书，你在skill中写的越多，那么测试的方向则会越符合你的心意！意思就是，你不要将流程交给ai自己去摸索你要把你的工作流程交给AI的同时，让AI再进行思考。这样AI就不会花费大量的时间在测试流程上反复横跳： 最理想的skill文件是：  
  
![image](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QWSF3QFmRTml319YfxcvbAItQZ6r6l486lyibWLaR3XJxk6NMfMNa432wV21lsuB7CpVc8hI47bJFxEaZKlUB1nGib6lm1Xz2ibqE/640?wx_fmt=png&from=appmsg "")  
  
image  
  
一个大的skill文件，里面写的是你希望如何测试SQL注入，能够使用哪些工具，有哪些姿势，遇到什么场景该怎么做等等。而references文件夹也就是说明文件夹中，你应该存放着你刚刚说的哪些场景的具体数据包（不用全部，举例即可），工具的完整使用命令以及出处这类的完整说明文件，来保证你的Claude code能正确的调用这些工具，是的，你必须教会它调用你电脑上的工具，否则它将会调用时非常的奇怪且反复横跳。  
  
![image](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QXicbw4tInwSnd4N10kRxibicIJ8kenqDaicZiaUeNoOovEAibOsib4fCsbrA6nmfnxGBiayBtZKYibrLhUm2dAXWVbt0wJuvrTY6ufJmn0/640?wx_fmt=png&from=appmsg "")  
  
image  
  
那么其他两个文件夹呢？ 一个是用来存放脚本文件的，比如你测试sql注入的时候，希望做一些对payload加解密，那么你完全可以将这个脚本存放到skill文件夹中，这样它就不会傻乎乎的自己再去写脚本，而脚本也不一定能符合你的心意。  
  
![image](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QULkPY1iaXr83LTxK8z89noa4TMXdZFDia2jm0Aq3ItolicOHola6U71ic1kjFb0yiaaGOG8ocNgh0IIUzmSibMtLWgeFl9ujVjpG03A/640?wx_fmt=png&from=appmsg "")  
  
image  
  
另外一个文件夹，你可以将你整理出牛逼的payload，直接存放，来保证它每次sql测试的时候都先使用你的payload，再去自己想payload。这会大大的提高测试效率  
  
![image](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QX7A4wo4mjbaoGRZ861k0USOg1UYY7tMPESnsdqsrJBZdDmxcTyEZb5REDBiag8Rf8QHNXMeCXCZZjgSlI6HrE7gVSWgXMozpxE/640?wx_fmt=png&from=appmsg "")  
  
image  
### AI自动化  
  
在优化完你skill的逻辑后，你就要想，有没有最简单的工具能完成最多的事情？如果有，那么久只需要一个工具就好了。因为太多的工具，不仅会影响模型的判断，还会将大量的时间重复在一个点上。比如有fuff工具，其实是不是就可以替代sqlmap工具了？理论上是可以的，但为什么我没这样做，因为那样我又要重新教ai，sql注入到底该如何测试，而sqlmap保留下来却可以让他测试变得顺利。  
  
所以在写skill的时候，你不仅要考虑简洁性，你还要考虑过于简洁的skill会不会让AI走向反复横跳的路子。这是一个漫长的过程，需要不断的使用调试。 我在这上面花费的功夫大概有一个月，但依旧没有优化出令我满意的skill（工作量稍微大了一点，即使交给gpt5.5进行优化，也需要人为的不断微调，gpt可不会理解你脑子里的测试流程、测试方法、以及你自己发现的技巧，这些你都要去以AI听得懂，且简洁的话术在skill文件中说明）  
  
讲完skill的优化后，就到了如何让他去进行自动化测试了。我了解一些视频、和一些基本原理后，做出的总结：就是设置一个问题，让AI能够不断的去问自己。例如：1.请你进行渗透测试。那么你只需要设置一个循环指令的提示词：检查这个漏洞是否是高危漏洞，这个漏洞是否成功，这个目标是否存在其他漏洞。然后它就会进行一个循环，一直问一直问，从而达到“自动化”的效果。 例如：  
  
![image](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QX1icNdiapZOgO8AZS46C1gs0icacYOjwTialZJL9cJMWEwxO6TDvQMkQfjEfgeFAk1PMW4vQHxtDz5DqibUic2Ml6LX2cwDKQ6Fm0Ss/640?wx_fmt=png&from=appmsg "")  
  
image  
  
所以我使用的自动化循环方式并未多复杂，仅仅是https://github.com/othmanadi/planning-with-files，用于计划任务的skill加上claude code的内置命令/loop，以及对loop.md（这个可以参考，但不建议各位佬直接用，因为我自己也才没使用多少，没经过多少迭代）的编写就完成了。  
  
然后就是直接对目标进行测试了，加的东西并不多，但我认为暂时已经够了，剩下的路线就在此框架上不断的优化即可。比如下一个目标：Claude code在这样不断的循环中，运行的时间会越来越短：  
  
![image](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QX4aJ22q8QXh3BAZysHYguAFOibOalUiaxpEI2xTQAW6ibWHlibAgVfdzUUJoSqHlhzBIfN4ZygXsiakIp1VMhGYq9JbKqsJMdfnwqs/640?wx_fmt=png&from=appmsg "")  
  
image  
  
可以看到，当我们循环到第七次的时候，运行的时间仅仅只有两分多钟，这是一个苦恼的地方。  
  
最后附上AI测试成功的截图，虽然漏洞的结果是被忽略了，但人工验证确确实实是存在需要未授权端口，可惜SRC并未进行收录。：  
  
![image](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QXf3qR6JrrQwHDfnlKTIGLfLQZBuztOEw81ZAst7gqXar8hfllL0eWPaqkDeIwiczacichR3vGyzEN3gAZLtn7h9pKeEHpiaGGAeU/640?wx_fmt=png&from=appmsg "")  
  
image  
  
![image](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QXYMmqiat62JETBm3XSiaD5P7SCsg3BAVNAGoCbI9AIVxaemqtwgyLFfXDNJrIojONeaiaQShKCRN5coKVph7anIDczAqTgygicES8/640?wx_fmt=png&from=appmsg "")  
  
image  
  
![image](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QWXHficOfuw0vF45w7KO0BLgSXib949L8247Ypt3sb1TQObwsrNDUUZ6P3VcbkowTwVWeAFGSkIc1JiacC9FLmsbSJM3Y4iaosY9ss/640?wx_fmt=png&from=appmsg "")  
  
image  
  
我全程使用的是deepseekpro4 加 Claude code。并未使用国外模型，原因嘛，一、国外模型的道德水平较高，风控严重。二、贵  
  
评论区有佬友问我要skill，我不是不愿意发，而是现在我写的skill远远不够成熟，发出来太丢人了哈哈。  
### 总结流程  
  
渗透测试skill agent-browser–让Claude code操作浏览器的skill planning-with-files 计划任务的skillGitHub - honmashironeko/ProxyCat: 一款部署于云端或本地的隧道代理池中间件，可将静态代理IP灵活运用成隧道IP，提供固定请求地址，一次部署终身使用 · GitHub – 用于让ai进行代理池切换的，使用起来很简单，让ai所有流量走代理池端口即可 BurpSuite的MCP --》 通过扩展市场就可以下载。  
  
以上就是我全部自动化渗透的流程了，总的来说不是什么复杂的东西，算是人人都可以使用的。  
  
02  
  
0x2 培训课程介绍  
  
26  
  
**SRC漏洞挖掘培训课程**  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/6cIuvSQkkicOHhYFkQLTibYAMUR9rfZ9eUrI78toIC4V2304G909O6s6CnVrAGiaYLEJM9XuUARhzNfxCtYKQfQ83wfPSlqpshSScfoYzSKzgY/640?wx_fmt=png&from=appmsg&wxfrom=5&wx_lazy=1&watermark=1&tp=wxpic#imgIndex=4 "")  
  
  
**1.课程价格目前是525（后面也会随着人数越多，涨价）🌟师傅们还可以上车补票，冲冲冲！**  
  
**2.报名成功送知识星球一个，拉内部小圈子交流群+SRC直播通知群！✨**  
  
**3.一周2节课程，直播+录播形式，课程内容大家可以看课表，目前是第一期，一次报名永久无限听课！❤️**  
  
**4.目前是第一期课程，后面比如说开了二、三期，都是不用在花钱的！**  
  
**5.上课结束后，会把视频录播+课件笔记一起打包发直播群！**  
  
**6.哔哩哔哩SRC课程公开课，链接🔗直达：**  
  
**https://space.bilibili.com/642258933**  
  
SRC课程详情🔎：  
[学了一堆理论，还是挖不到漏洞？你缺的是实战！](https://mp.weixin.qq.com/s?__biz=Mzk0Mzc1MTI2Nw==&mid=2247507745&idx=1&sn=b8b52d2d218eaac6acbeaaa6730d0ef1&scene=21#wechat_redirect)  
  
  
内部小圈子知识星球详情🔎：[50 元封顶！渗透攻防 + SRC 漏洞星球限时开放！](https://mp.weixin.qq.com/s?__biz=Mzk0Mzc1MTI2Nw==&mid=2247507678&idx=1&sn=8c9002bc6e4a16bea7e9e8a7897351fc&scene=21#wechat_redirect)  
  
  
欢迎关注微信公众号：神农Sec，报名咨询添加微信：  
routing_love  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/mcko8AHj6QVcCkxIUpaBmNic17zibGfXMWrr9z89gE0DFtbOu3QYzD5d62zsp6qwc38Pssk60mLq8VKthcMOmctVlHU716S5G4KYmrKVrEj5c/640?wx_fmt=other&from=appmsg&watermark=1&wxfrom=5&wx_lazy=1&tp=webp#imgIndex=6 "")  
  
开课快四个月时间  
，课程目前已经  
累计加入了830+个学员  
了，课程培训招生任火热持续中，师傅们  
对于我们课程感兴趣的，想要学习技术，找工作的可以咨询我报名  
。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QVmdLBDlbl5p4Teyw5qqFOIFTIUxIxRay83I5qDXG690XI61gRj8MXTvTaibC4q2cCb1CbM4XS2FK6X4KYhPTX2ibgvA363YYwcE/640?wx_fmt=png&from=appmsg "")  
  
课程培训记录📝，每次上车在1-3小时之间，上课包括课程内部群大家  
交流氛围很好！  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/mcko8AHj6QV7iczVHowN13BzCTraG8jDUoe5hluiaZ90RUy7FjW398DictcrZhHrYpMgw4polRqvlGua6iakYdARPI3Jkiahhjvrvkviblm19U4F0/640?wx_fmt=other&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=4 "")  
  
课程上课笔记课件📒都会打包给师傅们，笔记都非常详细，很多几k价格的培训机构哪怕是课件笔记都没有的，我这里都是下课第一时间把  
录播+笔记打包发给大家！  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QWbRV4mBn8GZHrvHocPMYYcBuAM3gyIKOM0SicBWQhywMehkXInvEerRLySOPPMzEmM2GLSlOMFREx6QItqtCgCibGs2MeY6yvu0/640?wx_fmt=png&from=appmsg "")  
  
平常也都会给学员进行一些项目发布，包括后面的  
工作、护网内推等，经常上麦交流，大家互相学习，简历优化等。  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QXvjjkgJibDEUhdDjErjibiangGsN0rqb0Av59xfyxBbDrTMNdfIAhNXlx0HQKvxIVBIEGAAbYrEENzd77j65asejlD4a50Sb4U7o/640?wx_fmt=other&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=20 "")  
  
SRC漏洞挖掘课程培训已经两个星期了，期间也是创建了  
“回本小群”，希望学员回本越来越多，创建这个群主要是鼓励学员学习进步，以及不定时发小项目！  
  
最后也是希望大家都可以赚钱，找到好工作🎉  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QXOAaMzNYg40bFJF3ic1j5w2Ebe6s764bQJaXWkP9VYSgcOpreL2cpPibeTry1ZTR4bExlQfDORNo4bXHgibQfzQ1uFkZyWMPaGCY/640?wx_fmt=png&from=appmsg "")  
  
培训时间不长，感谢🙏师傅们的  
喜报  
，很开心看到师傅们给我分享自己的成果，  
希望师傅们越来越强！  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QUMAtEWv3xXZPDsGBRhESmwGRciaasCGibU8TtbP2U0YVZPBdf5tlLqpWAtQKBh5oFwgETyvicKBeW1JSsekAyJ5cbRlSdjooQkSM/640?wx_fmt=other&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=22 "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QUrS4N68nZ0EyE76Wkib7ZDrpnZWw2Q1RJQvFEdIOu5XvFGCwpz9lziabKyo9C9d5ZiamibuSXlibhXLHb7b8QJhqEIs3hXvqktkkyA/640?wx_fmt=png&from=appmsg "")  
  
  
平常也会分享项目，下面是一些  
学员项目成果  
，群里报课的学员都是不抽成的，主要是帮助学员进行  
回本  
，  
让大家都可以进步！  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QWS8lR4pmzZrczwr6YjtG48EqF9q4FlAUH78M7DXkiboqF8Q1HkeWJLzpFPOQBToO3auj8r4rU9x3fuafXUDVMEcFj5EI6U3P9w/640?wx_fmt=other&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=23 "")  
  
上课结束后，会把  
视频录播+课件笔记  
一起打包发直播群  
  
**「神农安全」**  
知识星球目前已经  
累计2200+网络安全爱好者的加入！  
  
后面也是小圈子做大起来了，师傅们也都喜欢看我文章，想着给大家教下src漏洞挖掘思路，所以自己花了很长时间做了✨  
课件和课表，都是纯自己手搓的，大家也可以看下课表的内容。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/b7iaH1LtiaKWXNmpV89Zxcm1J56eeHltthM2sjuWQFbmvWv79V058KwI0DswFF9LysewGtULj81Vp5bX9nTEK78A/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QVhliaOc71FnQLZjEUB2QiavqaRdiaaAN25Gb1HNADIy0cYvIIHC46za7Ab6sibRKvKG2tbJBxqrOGyczqWF44LQOKllnZXE6PU5iaE/640?wx_fmt=png&from=appmsg "")  
  
03  
  
0x3 课程特色  
  
课程  
主打真实，  
一线SRC漏洞挖掘师傅是如何学习和挖掘SRC漏洞的，让你真正了解SRC漏洞挖掘，助力在岗人员和大学生的能力提升，掌握新的技能树，为下一次  
跳槽涨薪做好准备。本  
课程内容覆盖企业  
SRC、众测项目挖掘、护网HVV红蓝攻防技巧、CVE、CNVD、EDUSRC等平台通杀案例技巧挖掘方法。  
  
本课程  
适合人群  
（光看不挖啥也不会）  
```
1、想从0转行入行的大学生或自学者
2、想从CTF比赛/Web或SRC进阶到项目实战的选手
3、想参与项目/找工作/提高收入的转型者
```  
  
课程价格：525 元  
  
报课成功的师傅们直接免费送内部小圈：一个知识星球+内部小圈子交流群  
```
1、课程价格真心实惠，绝不割韭菜
2、两三百的课程价格让你体会大几千的培训课程内容
3、带着大家从0到1，本人上课坚持手搓课件（实战案例+知识体系）
4、拒绝使用PPT演讲模式（无实操，很枯燥）
```  
  
直播培训教学方式  
  
课程  
一周1-2节课，课程特色涵盖直播多人上麦活跃回答，直播过程中有问题随时解决或私信我。  
拉微信群：一个知识星球内部小圈子交流群+课程培训直播通知群。有项目/工作/护网第一时间内推报课的师傅，  
一对一简历优化，助力在岗人员和大学生的能力提升。  
  
一次报名每期均可永久学习，并且赠送内部「神农安全」知识星球，一对一永久解答、无保留教学！  
  
欢迎关注微信公众号：神农Sec，报名咨询添加微信：  
routing_love  
  
课程均为线上交付，报名成功后  
不支持退款  
  
内部小圈子  
（知识星球+内部小圈子交流群+知识库）  
  
对内部小圈子感兴趣的师傅们也可以看下下面的这个  
跳转链接，里面有对小圈子的详细介绍，报名课程成功的师傅们直接免费送一个（直接点击下面直接可以跳转）。  
  
[强烈推荐一个永久的SRC挖掘、渗透攻防内部知](https://mp.weixin.qq.com/s?__biz=Mzk0Mzc1MTI2Nw==&mid=2247506434&idx=1&sn=afc6b7a3f7ff85899f67f1fcfa06b3f8&scene=21#wechat_redirect)  
  
[‍](https://mp.weixin.qq.com/s?__biz=Mzk0Mzc1MTI2Nw==&mid=2247506434&idx=1&sn=afc6b7a3f7ff85899f67f1fcfa06b3f8&scene=21#wechat_redirect)  
  
[识库](https://mp.weixin.qq.com/s?__biz=Mzk0Mzc1MTI2Nw==&mid=2247506434&idx=1&sn=afc6b7a3f7ff85899f67f1fcfa06b3f8&scene=21#wechat_redirect)  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QVRzhiawbmNicgOFicLKeMZPtpyqtP9M0IA7gJZPerY1pI0P1Owcs0ttibWiaw87asg3qibyVF9NEVeGuxL3YqASaQhUn3pUBjicpMTPM/640?wx_fmt=png&from=appmsg "")  
  
讲师介绍  
  
id：一个想当文人的黑客  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/b7iaH1LtiaKWXNmpV89Zxcm1J56eeHltthqvGuVSjkR43eeaNibf1KbGU4nia5ibXFYpTBFeAbQewTq43IqJHIMhhhg/640?wx_fmt=png&from=appmsg "")  
  
欢迎关注微信公众号：神农Sec，报名咨询添加微信：  
routing_love  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b7iaH1LtiaKWXLicr9MthUBGib1nvDibDT4r6iaK4cQvn56iako5nUwJ9MGiaXFdhNMurGdFLqbD9Rs3QxGrHTAsWKmc1w/640?wx_fmt=jpeg&from=appmsg "")  
  
04  
  
0x4 第一期挖洞培训课表内容  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QUbCDcFPvSPxCsickn2Acbyad5c5CsAv2VuOOSs39xQEGvylxvkQrfFJGMTsextplreEF8bGmjfLUMcSGsgX1cJObiczI5B8ibDf0/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/MVPvEL7Qg0F0PmZricIVE4aZnhtO9Ap086iau0Y0jfCXicYKq3CCX9qSib3Xlb2CWzYLOn4icaWruKmYMvqSgk1I0Aw/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1 "")  
  
**内部圈子介绍（报课赠送）**  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/MVPvEL7Qg0F0PmZricIVE4aZnhtO9Ap08Z60FsVfKEBeQVmcSg1YS1uop1o9V1uibicy1tXCD6tMvzTjeGt34qr3g/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1 "")  
  
  
  
  
**圈子专注于更新src/红蓝攻防相关：**  
  
```
1、维护更新src专项漏洞知识库，包含原理、挖掘技巧、实战案例
2、知识星球专属微信“小圈子交流群”
3、微信小群一起挖洞
4、内部团队专属EDUSRC证书站漏洞报告
5、分享src优质视频课程（企业src/EDUSRC/红蓝队攻防）
6、分享src挖掘技巧tips
7、不定期有众测、渗透测试项目（一起挣钱）
8、不定期有工作招聘内推（工作/护网内推）
9、送全国职业技能大赛环境+WP解析（比赛拿奖）
10、十个专栏会持续更新~提前续费有优惠，好用不贵很实惠
11、每日内部资料分享，内部圈子资料1000+
12、联系圈主获取：内部漏洞知识库+圈子使用手册+内部圈子交流群
13、VX：routing_love，技术交流+疑问解决
```  
  
  
**内部圈子**  
**专栏介绍**  
  
知识星球内部共享资料截屏详情如下  
  
（只要没有特殊情况，每天都保持更新）  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/b7iaH1LtiaKWWYcoLuuFqXztiaw8CzfxpMibgpeLSDuggy2U7TJWF3h7Af8JibBG0jA5fIyaYNUa2ODeG1r5DoOibAXA/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/b7iaH1LtiaKWUw2r3biacicUOicXUZHWj2FgFxYMxoc1ViciafayxiaK0Z26g1kfbVDybCO8R88lqYQvOiaFgQ8fjOJEjxA/640?wx_fmt=png&from=appmsg "")  
  
  
05  
  
0x5   
优秀学员报喜  
  
下面是最近两个月培训期间，很多  
优秀学员进行报喜，看到师傅们有收获，也是感到很开心的！  
拉回本小群，就是为了促进大家学习，在群里发学员成果，也是为了让大家学习优秀的师傅们。  
  
加油，你我皆是黑马！  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QWS8lR4pmzZrczwr6YjtG48EqF9q4FlAUH78M7DXkiboqF8Q1HkeWJLzpFPOQBToO3auj8r4rU9x3fuafXUDVMEcFj5EI6U3P9w/640?wx_fmt=other&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=23 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QWK7cWZiaiatmAfXNWyj732Fib2ntZRWjFR4rfftXb2LoeicNAMZPrbBJFR2Ybf9XmWpqOiammYbiaxoQN5q5XRXo5xPicld4PhTtPtCI/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QU6ibZutPq43zUiap7IgDmJq7kwUKJBCa2IDujYiadMJfe9fFH9DOfUEOM2TibibYRuFiahDqMnBX1MVjLw5XIdNDSuR5P3g7XibaUkBo/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QVz2wFpVfer0uAFVpLKyicMaaLkmJDdg5bWnOotuzN3S9r2FMKpEKrJy8ND7icWVzNgqyYS2J6XElVN43vGca4X6HcEqapwGcNX0/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QWoKYrxQzob221aCmicmemD3aVPbk5dvVIaEic4TNPXrnkRazOTHnIbq87Jbk0GREdlI4iaZUmVU3c6K6rBbyZzBnkicooOUVtzN1I/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QWdSycCicsfpn14HlUgEibU04lpXJ4a70L4D6oSWx5s0tLgnLnLiaRAdclxNVicYKFRD1mGn40jQ4t4ic8XZzVoOSTTCzY9xz9auzJI/640?from=appmsg&wxfrom=12&wx_fmt=other&tp=webp&usePicPrefetch=1&watermark=1 "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QVRqR4bI22pibTSVQVSibImicNHOCUGQAaUlo9lZsNJicLmcTaQKl662ulqoX54EmbCDKUD3ibibdZxqKaOJAcoyIV7cAn7tKqia8S470/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QXB1O6Fx3ia62NNWITh9vUQaEKp7epibLWeEsdobibvBvqNDoTCAvfyQFHw597O24naJAIpM4QALgfqMWWc4E1KHrxBoaGRxE4Ajc/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QXnjwIRWjJOVSuN4X4HjmEFtCVqCHZ05M77sXqzmVjibaJbLUw3ApOuz7iaH8OCCnRmTRYVtKC5NajGKVkI4pnKZsJaj0T4iaYibq0/640?wx_fmt=png&from=appmsg "")  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QUm9nL5FsCxBKWrhMmwpgVvr7x7IfEu2IQPQuXMiaJZSHDyNDib5qoA3tGvfW8TUfShOKvIDuia32oSqb9YN1ghaxaKAicW6uKxrvU/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QWRdtEUY2aeAZh34wDle515j7UwnibFQeCibWSeDKGnIZ2YH5VGX64cYeXgPGdCwHLKdsMY07EIVliapxh10gzQ2EO3bks7bxhmVs/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QW7rozFqSBVNRDE2kbfUSB4FefOPm9LXM2B9bV4n9VPM7Kt11rfw284Ejn4AHUc1Uc1r1gZs3FF6umgPk1QejcC6zrOAYEyegY/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QV0epFsicbJrNPgGwNXcXRCDrC1sGQySI2ylkfs2Hdic6d6unjwqNiby5DfhtfT6ezabX13bNeR53pOW3BUqLaZrIvPM8Z4IsBpgI/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QXwfrgic5XLseOxkPOWkjm1yicAW2ZiaAqzxtbjPok4Yhic2Wiblic93SSGN5BtT77AFuZt6ySuRL09icqIicPuOUUbL5NbWgMCHgLictiak/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QW2swH16lFEBhjaxgV6SficichviaOVV2ZRabZdoxIeNCxfKub4EPH0TiaTvQqhZRk9jOmVlWsdFlZibIwJLlLIZWoSm7wq1ibU2icbw0/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QXicA0BukQVuKKhTydZfiaOEibqlIm6hJ5liacN1nBmN38icH4ibibfHAAh2jkSSaDqbCgNZgdEia5gHjibwKMBVO72Vp5iaSrN6iagxicZpOg/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QWzKmnCemQquDBf5uFwcpaG7RQqSPVwKqOKjrFxkCicFVTh6ngv3LOGDY1InR2mj0D6iby2A3Adic9U6MMsJ8FIo2wwxzPswjHsIQ/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QXQJcAmd5OVIoq1znICoW1Sy9hTsxpwe9HsiceptQ58rFYCNibBonWQQEH1TB3IrMASTQ3icWHwRd7JDtBwAwibWEGZxvIxeseTiaIY/640?wx_fmt=png&from=appmsg "")  
  
  
  
**神农安全公开交流群**  
  
有需要的师傅们直接扫描文章二维码加入，然后要是后面群聊二维码扫描加入不了的师傅们，直接扫描文章开头的二维码加我（备注加群）  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/mcko8AHj6QXgFYQNFgXm0oU4Vw0Jk6POYiaQvknskuRJtvjyVZ5TibTXDDUYKfIpylatJl1IrFHsg81L451XeWkWF24SQ4y7U6ZWYHerWBL30/640?wx_fmt=jpeg&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/mcko8AHj6QV9grs7NOhSTCfTpCc4xrxdnlISIReNNCKR2EOyWvhMpyIzbma8nuelSg8LicKF5yYZ7hgyODlWgMmhViaE8Ahhs7PZlnmA0VFcY/640?wx_fmt=jpeg&from=appmsg "")  
```
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/b7iaH1LtiaKWW8vxK39q53Q3oictKW3VAXz4Qht144X0wjJcOMqPwhnh3ptlbTtxDvNMF8NJA6XbDcljZBsibalsVQ/640?wx_fmt=gif "")  
  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/EXTCGqBpVJRSicyOOePGE9sGceAg4JcsCFHMqeE6O6zJJaSXkw6VEiaHibGnD0DzgYpbzhdbaTbsMKhJLte7sOt1g/640?wx_fmt=png&from=appmsg&wxfrom=13&tp=wxpic "")  
  
**往期回顾**  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/EXTCGqBpVJRSicyOOePGE9sGceAg4JcsCFHMqeE6O6zJJaSXkw6VEiaHibGnD0DzgYpbzhdbaTbsMKhJLte7sOt1g/640?wx_fmt=png&from=appmsg&wxfrom=13&tp=wxpic "")  
  
  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/EXTCGqBpVJRSicyOOePGE9sGceAg4JcsCFHMqeE6O6zJJaSXkw6VEiaHibGnD0DzgYpbzhdbaTbsMKhJLte7sOt1g/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1 "")  
  
[手把手js逆向断点调试&js逆向前端加密对抗&企业SRC实战分享](https://mp.weixin.qq.com/s?__biz=Mzk0Mzc1MTI2Nw==&mid=2247495361&idx=1&sn=48283073b325e360823da8dec27a7508&scene=21#wechat_redirect)  
  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/EXTCGqBpVJRSicyOOePGE9sGceAg4JcsCFHMqeE6O6zJJaSXkw6VEiaHibGnD0DzgYpbzhdbaTbsMKhJLte7sOt1g/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1 "")  
  
[浅谈src漏洞挖掘中容易出洞的几种姿势](https://mp.weixin.qq.com/s?__biz=Mzk0Mzc1MTI2Nw==&mid=2247489731&idx=1&sn=c3a5ef01648fad496ecda36b653b6e21&scene=21#wechat_redirect)  
  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/EXTCGqBpVJRSicyOOePGE9sGceAg4JcsCFHMqeE6O6zJJaSXkw6VEiaHibGnD0DzgYpbzhdbaTbsMKhJLte7sOt1g/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1 "")  
  
[HVV护网行动 | 分享最近攻防演练HVV漏洞复盘](https://mp.weixin.qq.com/s?__biz=Mzk0Mzc1MTI2Nw==&mid=2247488672&idx=1&sn=493bb70011a02eb971ff1b74c733f1d9&scene=21#wechat_redirect)  
  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/EXTCGqBpVJRSicyOOePGE9sGceAg4JcsCFHMqeE6O6zJJaSXkw6VEiaHibGnD0DzgYpbzhdbaTbsMKhJLte7sOt1g/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1 "")  
  
[攻防演练｜分享最近一次攻防演练RTSP奇特之旅](https://mp.weixin.qq.com/s?__biz=Mzk0Mzc1MTI2Nw==&mid=2247492377&idx=1&sn=a94ad30e30e08bd96e888dad744e9814&scene=21#wechat_redirect)  
  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/EXTCGqBpVJRSicyOOePGE9sGceAg4JcsCFHMqeE6O6zJJaSXkw6VEiaHibGnD0DzgYpbzhdbaTbsMKhJLte7sOt1g/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1 "")  
  
[JS漏洞挖掘｜分享使用FindSomething联动的挖掘思路](https://mp.weixin.qq.com/s?__biz=Mzk0Mzc1MTI2Nw==&mid=2247492315&idx=1&sn=88991e98058a277e267a9a79b8518e16&scene=21#wechat_redirect)  
  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/EXTCGqBpVJRSicyOOePGE9sGceAg4JcsCFHMqeE6O6zJJaSXkw6VEiaHibGnD0DzgYpbzhdbaTbsMKhJLte7sOt1g/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1 "")  
  
[渗透测试 ｜ 从jeecg接口泄露到任意管理员用户接管+SQL注入漏洞](https://mp.weixin.qq.com/s?__biz=Mzk0Mzc1MTI2Nw==&mid=2247493292&idx=1&sn=611fd43361089a30e5f7bcda21274b95&scene=21#wechat_redirect)  
  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/EXTCGqBpVJRSicyOOePGE9sGceAg4JcsCFHMqeE6O6zJJaSXkw6VEiaHibGnD0DzgYpbzhdbaTbsMKhJLte7sOt1g/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1 "")  
  
[分享SRC中后台登录处站点的漏洞挖掘技巧](https://mp.weixin.qq.com/s?__biz=Mzk0Mzc1MTI2Nw==&mid=2247485439&idx=1&sn=3fd7e4cef57edca8e73104f8af38fc05&scene=21#wechat_redirect)  
  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/EXTCGqBpVJRSicyOOePGE9sGceAg4JcsCFHMqeE6O6zJJaSXkw6VEiaHibGnD0DzgYpbzhdbaTbsMKhJLte7sOt1g/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1 "")  
  
[企业SRC支付漏洞&EDUSRC&众测挖掘思路技巧操作分享](https://mp.weixin.qq.com/s?__biz=Mzk0Mzc1MTI2Nw==&mid=2247492839&idx=1&sn=b9781f60580c1da8e2151166f0494ba5&scene=21#wechat_redirect)  
  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/EXTCGqBpVJRSicyOOePGE9sGceAg4JcsCFHMqeE6O6zJJaSXkw6VEiaHibGnD0DzgYpbzhdbaTbsMKhJLte7sOt1g/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1 "")  
  
[渗透测试 ｜ 分享某次项目上的渗透测试漏洞复盘](https://mp.weixin.qq.com/s?__biz=Mzk0Mzc1MTI2Nw==&mid=2247493495&idx=1&sn=791bebc6faa651cc3c585c2f5f481d21&scene=21#wechat_redirect)  
  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/EXTCGqBpVJRSicyOOePGE9sGceAg4JcsCFHMqeE6O6zJJaSXkw6VEiaHibGnD0DzgYpbzhdbaTbsMKhJLte7sOt1g/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1 "")  
  
[【宝典】分享云安全浪潮src漏洞挖掘技巧](https://mp.weixin.qq.com/s?__biz=Mzk0Mzc1MTI2Nw==&mid=2247494877&idx=1&sn=2d00c0f651fd7375e881be86638e53ce&scene=21#wechat_redirect)  
  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/EXTCGqBpVJRSicyOOePGE9sGceAg4JcsCFHMqeE6O6zJJaSXkw6VEiaHibGnD0DzgYpbzhdbaTbsMKhJLte7sOt1g/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1 "")  
  
[实战SRC挖掘｜微信小程序渗透漏洞复盘](https://mp.weixin.qq.com/s?__biz=Mzk0Mzc1MTI2Nw==&mid=2247494468&idx=1&sn=f0da4b4ff7763cbb83b858fb5a8964f9&scene=21#wechat_redirect)  
  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/EXTCGqBpVJRSicyOOePGE9sGceAg4JcsCFHMqeE6O6zJJaSXkw6VEiaHibGnD0DzgYpbzhdbaTbsMKhJLte7sOt1g/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1 "")  
  
[综合资产测绘 | 手把手带你搞定信息收集](https://mp.weixin.qq.com/s?__biz=Mzk0Mzc1MTI2Nw==&mid=2247493749&idx=1&sn=d2e0febcdcf9dcd8aa44be0d43b51936&scene=21#wechat_redirect)  
  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/EXTCGqBpVJRSicyOOePGE9sGceAg4JcsCFHMqeE6O6zJJaSXkw6VEiaHibGnD0DzgYpbzhdbaTbsMKhJLte7sOt1g/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1 "")  
  
[【宝典】针对若依系统nday的常见各种姿势利用](https://mp.weixin.qq.com/s?__biz=Mzk0Mzc1MTI2Nw==&mid=2247493489&idx=1&sn=d3ef10a1ae3b8c161d7174cb42702fac&scene=21#wechat_redirect)  
  
  
  

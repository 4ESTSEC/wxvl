#  从0到1渗透测试自动化挖到某SRC漏洞的个人心得  
网络安全小叶
                    网络安全小叶  C4安全   2026-06-29 07:17  
  
  CTF课程培训  
  
  扫码咨询  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/niasx7fyic9CMs6pyt2xl3Ng0QWByv0oD67COatsbL7SbcLetqC6mr3bFalmibIID1ricPHNl6xHHrqjmb0vLc7Sm0ficuiaroLKTJrNDibIPJwoBk/640?wx_fmt=jpeg "")  
  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/b96CibCt70iaaJcib7FH02wTKvoHALAMw4fchVnBLMw4kTQ7B9oUy0RGfiacu34QEZgDpfia0sVmWrHcDZCV1Na5wDQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1&tp=wxpic#imgIndex=1 "")  
  
  
#   
  
专注于漏洞挖掘、系统化从基础入门到实战漏洞挖掘，包含团队自整的挖掘注意点和案例、渗透经验、SRC漏洞案例、代码审计、挖洞思路等高价值资源。不定期分享各种好玩的项目及好用的工具，欢迎关注。加内部圈子，  
文末有优惠券。  
#   
  
文章作者：网络安全小叶  
  
文章来源：https://linux.do/t/topic/2118146  
  
  
前言  
  
我从3月份开始初步接触到Claude code，当时的感觉就是太强大了，能完成以前很多觉得不可思议的事情，甚至于对于渗透，它给我的感受是已经可以替代我这个工作快满一年的小菜鸟了。许多的渗透方式，连我自己都未曾尝试。**从那个时候起，我就在考虑，如何让它完全的替代我。**  
  
和许多人一样，接触到Claude code后，我第一时间是想着从基础学起，让我对AI的认知了解更为深刻。很可惜，我并不是一个“好读书”的好苗子，让gpt给我一条学习路径，我仅仅完成了前两个就再也不想坚持下去了。所以接下来的心得，可以说是“野路子”。在我并不熟悉ai的情况下，由结果倒推过程的路子，哈哈，**写的不好不要喷我~**  
  
最初的做法：胡乱添加了一些**GitHub上的skill**  
，然后又用ai生成了一堆claude.md的文件，放到Claude code中，就酷酷的对网站进行测试了。但即使是这样，Claude code给我的惊喜也很大，当时deepseek4pro还没出来，我使用的还是glm4.7版本。  
  
背景是：我想用Claude code帮助我复现一个数据库漏洞，并讲清楚这个漏洞的原理（因为那个数据库接触的比较少，让ai来可以省去很多找payload试错的时间），仅此而已。  
  
我负责搭建环境–》提出要求–》Claude code就开始运行了。  
转折点来了，我给他的漏洞接口是：**/api/v1/hosts/command/search**  
但它居然能测试出/api/v1/hosts/search接口存在漏洞  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/niasx7fyic9CMM9HJKxyVKSibiakwbics7CnWzOnUhNGiaxEK0M6KlRA0eQP83Agvby48liblfD9pcmGLIyS2wzUZv7IMSS34zx6JOfPHGdicd6hk0Y/640?wx_fmt=png&from=appmsg "")  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/niasx7fyic9CMb6ARR0EQMb6n84RU0pGe9wRp0JROohbHujmWPYPEeiasLwwSuAJKlu1QSLcel5rDjvZ5vG2mBKnibIYWDvwicMT7RKGTsYAtyIA/640?wx_fmt=png&from=appmsg "")  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/niasx7fyic9CMufkv1V9icUreJb8sfR5pGcibEKDM5bQeKiamdkWEQib47FIZtb6x0BVwgAFBpX52mrwkNhAV3oZw0lkibjhZYQd24OwpP3duVRsDg/640?wx_fmt=png&from=appmsg "")  
  
从这个时候起，我就意识到了ai的强大，它其实根本不需要我们给出过多的提示或者约束就能进行完成（**哈哈，这个说法到后面我自己就会推翻了**  
）  
  
接下来我是怎么去完成自动化，一开始我认为我的skill已经足够的强大了（虽然是随便加的）。让它去完成各种的测试，甚至是SRC的测试，但效果可以说是**水洞的天堂**  
：  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/niasx7fyic9CNNp8QycOuzZia5kibcLTQnDGMf4iciadUUwicjaztsnciaAQIEbeib6DEdf7RhMqniawEG970FDIdRbdtZOib5rq6n90SzMm5eXQpFVGXs/640?wx_fmt=png&from=appmsg "")  
**这些水洞有些我连上班的时候都不敢提交。**  
  
后面是怎么做的呢？后面我对skill的理解不再是理论上的东西，而是实际的。  
它就是一份说明书，你在skill中写的越多，那么测试的方向则会越符合你的心意！意思就是，你不要将流程交给ai自己去摸索你要把你的工作流程交给AI的同时，让AI再进行思考。这样AI就不会花费大量的时间在测试流程上反复横跳：  
  
**最理想的skill文件是：**  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/niasx7fyic9COzV9Go665ABYR8Hxe1b9k9NFaw0GAneszgpuNuoUhx2QibxmFyPlywNhlJQtXlDPtVew76bO5WyuHbwzNHiccf2uzwFicJADXuZs/640?wx_fmt=png&from=appmsg "")  
  
一个大的skill文件，里面写的是你希望如何测试SQL注入，能够使用哪些工具，有哪些姿势，遇到什么场景该怎么做等等。而references文件夹也就是说明文件夹中，你应该存放着你刚刚说的哪些场景的具体数据包（不用全部，举例即可），工具的完整使用命令以及出处这类的完整说明文件，来保证你的Claude code能正确的调用这些工具，是的，你必须教会它调用你电脑上的工具，否则它将会调用时非常的奇怪且反复横跳。  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/niasx7fyic9CMjbdnonibOen3R2POY6bSwI4t5RUathsS8S9viaJMXcVE2VjCd4ic7PwGmpzBvdnfDibMungGk2opJogdria2giaczg8ricGAnZy8qaw/640?wx_fmt=png&from=appmsg "")  
  
那么其他两个文件夹呢？  
一个是用来**存放脚本文件**  
的，比如你测试sql注入的时候，希望做一些对payload加解密，那么你完全可以将这个脚本存放到skill文件夹中，这样它就不会傻乎乎的自己再去写脚本，而脚本也不一定能符合你的心意。  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/niasx7fyic9CPhDUkQcnnK2nscnhHnxtS0tuq1TDOalWEXbHFwmGhew0lfRbIuySUJVT1a9tlt4SY9B4ukh6VKolWzZThnK6m6H5R3ib6s2aDQ/640?wx_fmt=png&from=appmsg "")  
  
另外一个文件夹，你可以将你整理出牛逼的**payload**  
，直接存放，来保证它每次sql测试的时候都先使用你的payload，再去自己想payload。这会大大的提高测试效率  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/niasx7fyic9CP43yLdPOCB0zJDILj93icG626tJP8eQEclDFFKCcwWXqo8Zj6e7Z1lb5ksggbFMoeRZot3NiabZ1oCwvq1mpibeTqScIOwRNicsuA/640?wx_fmt=png&from=appmsg "")  
  
在优化完你skill的逻辑后，你就要想，有没有最简单的工具能完成最多的事情？如果有，那么久只需要一个工具就好了。因为太多的工具，不仅会影响模型的判断，还会将大量的时间重复在一个点上。**比如有fuff工具，其实是不是就可以替代sqlmap工具了？理论上是可以的，但为什么我没这样做，因为那样我又要重新教ai，sql注入到底该如何测试，而sqlmap保留下来却可以让他测试变得顺利。**  
  
所以在写skill的时候，你不仅要考虑简洁性，你还要考虑过于简洁的skill会不会让AI走向反复横跳的路子。这是一个漫长的过程，需要不断的使用调试。  
我在这上面花费的功夫大概有一个月，但依旧没有优化出令我满意的skill（工作量稍微大了一点，即使交给gpt5.5进行优化，也需要人为的不断微调，gpt可不会理解你脑子里的测试流程、测试方法、以及你自己发现的技巧，这些你都要去以AI听得懂，且简洁的话术在skill文件中说明）  
  
**讲完skill的优化后，就到了如何让他去进行自动化测试了**  
。我了解一些视频、和一些基本原理后，做出的总结：就是设置一个问题，让AI能够不断的去问自己。例如：1.请你进行渗透测试。那么你只需要设置一个循环指令的提示词：检查这个漏洞是否是高危漏洞，这个漏洞是否成功，这个目标是否存在其他漏洞。然后它就会进行一个循环，一直问一直问，从而达到“自动化”的效果。  
例如：  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/niasx7fyic9CPrhh7MEUREjK1lSsOTlYM4w4R6dQ0aiaJ5KnyClk8gkq8ZgDibPg97F2AwPL8SpwxmrjNNygscG35kO0dVTMgyshuY2Iib8mUTc0/640?wx_fmt=png&from=appmsg "")  
  
所以我使用的自动化循环方式并未多复杂，仅仅是  
  
  
```
https://github.com/othmanadi/planning-with-files
```  
  
  
用于计划任务的skill加上claude code的内置命令/loop，以及对loop.md（这个可以参考，但不建议各位佬直接用，因为我自己也才没使用多少，没经过多少迭代）的编写就完成了。  
  
然后就是直接对目标进行测试了，加的东西并不多，但我认为暂时已经够了，剩下的路线就在此框架上不断的优化即可。比如下一个目标：Claude code在这样不断的循环中，**运行的时间会越来越短**  
：  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/niasx7fyic9CMSmhmiaYDdKt5H8aian4MDh4e88PvdIlkPg35biakJRQMe2YQtkWdrNmkn3icamyUTZXpufcv4V7icWZUVWRImFtXTt4KvW2HNjrTQ/640?wx_fmt=png&from=appmsg "")  
  
可以看到，当我们循环到第七次的时候，运行的时间仅仅只有两分多钟，这是一个苦恼的地方。**最后附上AI测试成功的截图，虽然漏洞的结果是被忽略了，但人工验证确确实实是存在需要未授权端口，可惜SRC并未进行收录：**  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/niasx7fyic9CN8EoGMxDXU97BJic5iaobtCUoKGCBdy4tWumpXjIHatLM7iayxsHStz0c9XibeRGSibiaRsKYztkdricXaFlexSE69Rahq1MxcJEQaRU/640?wx_fmt=png&from=appmsg "")  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/niasx7fyic9CO4to2YgCs6lh48n2y02aSW27icrtvw7p4Rgfx4vlllA5fEcy18vvmIK5ianYGljYHu3XeYk68RzsXC9GUhMAhgO9UBMqk3iciczxI/640?wx_fmt=png&from=appmsg "")  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/niasx7fyic9CMLnAbEdrXbmcjia623WBjryj3acC1Mvw1dvnicAey9lsKeEDwjCtVKFciavet2DsEFr3mXHvQicDrfQibHBPqvhkfxvp6wXcydaqiac/640?wx_fmt=png&from=appmsg "")  
  
我全程使用的是deepseekpro4 加 Claude code。并未使用国外模型，原因嘛，一、国外模型的道德水平较高，风控严重。二、贵  
  
评论区有佬友问我要skill，我不是不愿意发，而是现在我写的skill远远不够成熟，发出来太丢人了哈哈。  
  
总结流程  
  
渗透测试skill：  
  
  
```
agent-browser–让Claude code操作浏览器的skill
planning-with-files 计划任务的skill
GitHub - honmashironeko/ProxyCat: 一款部署于云端或本地的隧道代理池中间件，可将静态代理IP灵活运用成隧道IP，提供固定请求地址，一次部署终身使用 · GitHub – 用于让ai进行代理池切换的，使用起来很简单，让ai所有流量走代理池端口即可
BurpSuite的MCP --》 通过扩展市场就可以下载
```  
  
  
以上就是我全部自动化渗透的流程了，总的来说不是什么复杂的东西，算是人人都可以使用的。  
也希望佬友多提点建议，提点方案，学习学习经验。  
  
  
  
**内部CTF课程上线，总课程30+小时，优惠折扣中！**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/niasx7fyic9COmTqWiaO3MWicicQJbYDnl4VtJ8A6fkm0tBKFYBxbeKj9d35HJcpgSf7moVawMYwluFS6omJiaTIxPOSM9Fx6qLLZTXhU6sydlZ4A/640?wx_fmt=png&from=appmsg "")  
  
****  
**帮会简介**  
  
《  
**安全渗透感知**  
》  
是FreeBuf知识大陆的重量级帮会，帮会致力于漏洞POC/EXP、红队攻防实战，是系统化从基础入门到实战漏洞挖掘的教程社区，包含团队自整的挖掘注意点和案例，还包含分享的渗透经验、SRC漏洞案例、代码审计、挖洞思路等高价值资源。  
  
  
**内容框架（持续新增中）**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/niasx7fyic9COd1ITgnGXHdVfC79DficTDDlYBibvNAC2VSwy3LDNBdxsgqbx8lUH5uUwjicLYYf1Ee2a8bmKlC8NnvYDtzfmfia7PoC6ytYX05u8/640?wx_fmt=png&from=appmsg "")  
  
****  
**目前已有「700+」小伙伴加入了帮会**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/niasx7fyic9CN9CVSeMYUIpW50058icjreeNHRMK7jEabMtshNf15j1IHvicDotNG4ZnfrQcwHDroAooj6kMIonH8FWgcu9bUjN7n6aEXyQHrFo/640?wx_fmt=png&from=appmsg "")  
  
****  
**加入方式**  
  
目前帮会成员  
**700+**  
人，  
**永久会员优惠后只需**  
**69.9元**  
**。**  
  
随着人数的增加及资源的积累，  
**之后永久会员将**  
**涨价至99元**  
**。**  
  
****  
有意向的师傅们可以扫码加入我们，共同进步。  
  
**如何加入帮会？→**  
安卓/苹果用户  
**可扫码使用优惠券↓↓**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/niasx7fyic9COLsDWDspFHia4icx1RwTUEIl78wShToyY3nGsOibY2XB3KEAUwybIfNwKDc0vusYV0WuytwHrzkSgibTGN0LK0VT9KUDnxdYLBXLE/640?wx_fmt=png&from=appmsg "")  
  
****  
**→ PC端用户可复制此链接到浏览器↓↓**  
  
https://wiki.freebuf.com/societyDetail?society_id=184  
  
****  
****  
****  
  
  
  
  

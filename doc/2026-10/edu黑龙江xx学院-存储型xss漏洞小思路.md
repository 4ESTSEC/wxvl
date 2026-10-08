#  edu黑龙江xx学院-存储型xss漏洞小思路  
zkaq-zbs
                    zkaq-zbs  掌控安全EDU   2026-10-08 04:08  
  
扫码领资料  
  
获网安教程  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/BwqHlJ29vcrpvQG1VKMy1AQ1oVvUSeZYhLRYCeiaa3KSFkibg5xRjLlkwfIe7loMVfGuINInDQTVa4BibicW0iaTsKw/640?wx_fmt=other&from=appmsg&wxfrom=5&wx_lazy=1&wx_co=1&tp=webp#imgIndex=0 "")  
  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/b96CibCt70iaaJcib7FH02wTKvoHALAMw4fchVnBLMw4kTQ7B9oUy0RGfiacu34QEZgDpfia0sVmWrHcDZCV1Na5wDQ/640?wx_fmt=other&wxfrom=5&wx_lazy=1&wx_co=1&tp=webp#imgIndex=1 "")  
  
  
# 本文由掌控安全学院 - zbs 投稿  
  
**来****Track安全社区投稿~**  
  
**千元稿费！还有保底奖励~（  https://bbs.zkaq.cn   ）**  
  
****  
**前话：XSS漏洞并不多见，而且反射型基本都不会收【危害太低】，所以要找到存储型xss，见框就插的方法没有用了【除非运气特别好】**  
  
这时候就得改变一下思路，找一些隐秘的传参有回显的点；；  
## 漏洞复现：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ianpxKPnLHoIY8ssibFlN9cRmnjMGAo2ibN5DxhJpoyJ5OrgvMxR10BqKicykM68Uicfp88aOZuChQrZ7XYztJVJY5qd0HicYxAOeonOsGQz73VMo/640?wx_fmt=png&from=appmsg "")  
  
  
注册登录后，一个平平无奇的提交工单接口，所有提交信息已经见框就插，毫无卵用QAQ  
  
于是在放弃测试xss后，对上传图片进行测试：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ianpxKPnLHoKWumcSYzd3CQCIhpsiaW5wb7uQYFMPTM38VjlekiaXSypHhRMBe49Yc3eD7ibvCE67k1QSNDvCakS6jDSz6q4ETQKNZrNSTp3yR4/640?wx_fmt=png&from=appmsg "")  
  
  
然后测了半天发现是个白名单，准备放弃的时候：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ianpxKPnLHoIIItyeiaEumTImGXgCEmy47YtQOPkwDyLdomZ0RASCrqodtWzbGvsGxwQeLuNQ3bSyFpmNB69Fic6ZeyxiaRwQ8fgSqMI5qggLwU/640?wx_fmt=png&from=appmsg "")  
  
  
突然发现提交的数据包有一条是对图片路径的传参  
  
加上我在测试的时候有尝试修改相应包上传php文件：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ianpxKPnLHoITaTI7naRzN0pOia2yHgy9oWnr5sesNpB7hPanIO6D9BYibWVLykmnLDCfW8MQqgbkuFfz7zSpNskgSB1WYG0GJWa7FRpvInUIU/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ianpxKPnLHoLdmBpMldqLtggalMibXvKVjxY3Nw1ypdpOEDIW8ibicCZqo7XS4uHSwe3E6zkrRk3icW2fHrTOQsIslASKu0KffmVzibBOrFV1zjeU/640?wx_fmt=png&from=appmsg "")  
  
  
发现虽然可以提交，but文件会被删除  
  
但是文件路径依然回显：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ianpxKPnLHoIGWsNOfkLUB5xLYyHlSNNYVMbToybWC9VxSZFOfWbPDwubS8QBjzRvCeOulRWUPia4tpXymEYsQqpnuF8R7TibBGGjknvGercbI/640?wx_fmt=png&from=appmsg "")  
  
  
所以我马上感觉这个文件路径的传参点可能xss有戏，闭合src=” ＋弹窗1：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ianpxKPnLHoILSiazg6qXW6FsdPlemDsO1bgku47UeqwXobYqKSSPXONKIXDNibMCIzK3WiaEzNDibBUJLgSnEoUVvPMUibRFx5bMwe8aRyjibb0l4/640?wx_fmt=png&from=appmsg "")  
  
  
效果如下：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ianpxKPnLHoILRlKT5kl78X9FibcRg0ROB2JCgcDuAXWOjMmZuNhbiacZjZEUmRCW40Zn2CQicRrfWWzxqsBn8bmtWmBpe14rhctuvFMbbJzJeQ/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ianpxKPnLHoIIbH00MtkKLX2rx4enAsJSH7RKbpIOOALvDT8pGWd79lX9gFnjO6ppzVFCjMxibebgECAcl7ZBs5ALG9c01piaPtCuwx7oKbhls/640?wx_fmt=png&from=appmsg "")  
  
  
一个存储xss漏洞就找到惹  
#### 漏洞修复建议：  
  
对图片路径得传参进行实体化编码和多一些逻辑判定  
  
  
申明：本公众号所分享内容仅用于网络安全技术讨论，切勿用于违法途径，  
  
所有渗透都需获取授权，违者后果自行承担，与本号及作者无关，请谨记守法.  
  
![图片](https://mmbiz.qpic.cn/mmbiz_gif/BwqHlJ29vcqJvF3Qicdr3GR5xnNYic4wHWaCD3pqD9SSJ3YMhuahjm3anU6mlEJaepA8qOwm3C4GVIETQZT6uHGQ/640?wx_fmt=gif&wxfrom=5&wx_lazy=1&tp=webp#imgIndex=34 "")  
  
**没看够~？欢迎关注！**  
  
  
**分享本文到朋友圈，可以凭截图找老师领取**  
  
上千**教程+工具+交流群+靶场账号**  
哦  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/BwqHlJ29vcrpvQG1VKMy1AQ1oVvUSeZYhLRYCeiaa3KSFkibg5xRjLlkwfIe7loMVfGuINInDQTVa4BibicW0iaTsKw/640?wx_fmt=other&from=appmsg&wxfrom=5&wx_lazy=1&wx_co=1&tp=webp#imgIndex=35 "")  
  
******分享后扫码加我！**  
  
**回顾往期内容**  
  
[网络安全人员必考的几本证书！](http://mp.weixin.qq.com/s?__biz=MzUyODkwNDIyMg==&mid=2247520349&idx=1&sn=41b1bcd357e4178ba478e164ae531626&chksm=fa6be92ccd1c603af2d9100348600db5ed5a2284e82fd2b370e00b1138731b3cac5f83a3a542&scene=21#wechat_redirect)  
  
  
[文库｜内网神器cs4.0使用说明书](http://mp.weixin.qq.com/s?__biz=MzUyODkwNDIyMg==&mid=2247519540&idx=1&sn=e8246a12895a32b4fc2909a0874faac2&chksm=fa6bf445cd1c7d53a207200289fe15a8518cd1eb0cc18535222ea01ac51c3e22706f63f20251&scene=21#wechat_redirect)  
  
  
[重生HW之感谢客服小姐姐带我进入内网遨游](https://mp.weixin.qq.com/s?__biz=MzUyODkwNDIyMg==&mid=2247549901&idx=1&sn=f7c9c17858ce86edf5679149cce9ae9a&scene=21#wechat_redirect)  
  
  
[手把手教你CNVD漏洞挖掘 + 资产收集](https://mp.weixin.qq.com/s?__biz=MzUyODkwNDIyMg==&mid=2247542576&idx=1&sn=d9f419d7a632390d52591ec0a5f4ba01&token=74838194&lang=zh_CN&scene=21#wechat_redirect)  
  
  
[【精选】SRC快速入门+上分小秘籍+实战指南](http://mp.weixin.qq.com/s?__biz=MzUyODkwNDIyMg==&mid=2247512593&idx=1&sn=24c8e51745added4f81aa1e337fc8a1a&chksm=fa6bcb60cd1c4276d9d21ebaa7cb4c0c8c562e54fe8742c87e62343c00a1283c9eb3ea1c67dc&scene=21#wechat_redirect)  
  
##     代理池工具撰写 | 只有无尽的跳转，没有封禁的IP！  
  
![图片](https://mmbiz.qpic.cn/mmbiz_gif/BwqHlJ29vcqJvF3Qicdr3GR5xnNYic4wHWaCD3pqD9SSJ3YMhuahjm3anU6mlEJaepA8qOwm3C4GVIETQZT6uHGQ/640?wx_fmt=gif&wxfrom=5&wx_lazy=1&tp=webp#imgIndex=36 "")  
  
点赞+在看支持一下吧~感谢看官老爷~   
  
你的点赞是我更新的动力  
  
  

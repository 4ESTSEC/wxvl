#  我与edu的爱恨情仇之正方系统逻辑漏洞导致高危敏感信息泄露  
zkaq - 洛川
                    zkaq - 洛川  掌控安全EDU   2026-07-02 06:13  
  
扫码领资料  
  
获网安教程  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/BwqHlJ29vcrpvQG1VKMy1AQ1oVvUSeZYhLRYCeiaa3KSFkibg5xRjLlkwfIe7loMVfGuINInDQTVa4BibicW0iaTsKw/640?wx_fmt=other&from=appmsg&wxfrom=5&wx_lazy=1&wx_co=1&tp=webp#imgIndex=0 "")  
  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/b96CibCt70iaaJcib7FH02wTKvoHALAMw4fchVnBLMw4kTQ7B9oUy0RGfiacu34QEZgDpfia0sVmWrHcDZCV1Na5wDQ/640?wx_fmt=other&wxfrom=5&wx_lazy=1&wx_co=1&tp=webp#imgIndex=1 "")  
  
  
# 本文由掌控安全学院 - 洛川 投稿  
  
**来****Track安全社区投稿~**  
  
**千元稿费！还有保底奖励~（https://bbs.zkaq.cn）**  
# 1.信息收集  
  
今天突然心血来潮，打开浏览器用google-hacking语法搜学号和身份证，如site:”edu.cn” “学号””身份证””xh””sfz” filetype:pdf/doc/xls,找到了某学校的的学生学号和身份证等敏感信息  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ianpxKPnLHoJVBkEm7BzjwjAQ2I7vAAMgkWEpHS0QpVAYRnTxfEvlOjicEyQxmRFFBmVAUpF6CV4ib6E2T3yK2jQtJfBbCH1Q3nnL1OYa8ezXc/640?wx_fmt=png&from=appmsg "")  
  
学生学号和身份证号有了这不得搞一手他们学校的系统吗？嘿嘿嘿  
  
然后利用他们的学校主域名得到真实ip，可用超级ping，全球cdn服务商查询等方法得到，这个简单就不多说了  
  
得到真实ip之后直接鹰图语法查一波资产  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ianpxKPnLHoL0hRBJuHibEZ2emm7s2fpqleSIAstxmQiculSiaKYEV8vZf2BGEbVn9U9JKyCd8bmicoxw0ibC3f73XtAR1ucEqbKruqfxajOA4Bt0/640?wx_fmt=png&from=appmsg "")  
  
找了一圈，最后找到了一处某方的系统  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ianpxKPnLHoKEiayhx59y7q66kTomCNmEJANBowrGba7TJACKoR2Wos4OMpvgm4xVszQY8jLuXMRydIibGxFibm3zLtAOVRCbQrnDGljctGK9bw/640?wx_fmt=png&from=appmsg "")  
  
账号为学工号，但是密码却搞不了，手动测了几个常见的默认密码均失败后决定另寻他法  
# 2.社工大法  
  
通过百度贴吧直接搞到了学校新生群的群号，嘿嘿嘿  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ianpxKPnLHoI70xEkSOxLoYb3uZglDQWGQoicA7sto6DFZ7hXicvmia7FyavcfpBOGdqSaWIXeL3pmzrgh8uibAnydOcUxGzDicfLnUsibEPt9pVeo/640?wx_fmt=png&from=appmsg "")  
  
然后加入新生群之后翻看之前的群文件，没找到啥有关的文件，本来觉得要加一些群里的新生套密码了，结果峰回路转，翻看之前的群公告找到了系统默认密码  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ianpxKPnLHoKJX0HArXHJEVtRD9wBkNVzicibH87q5EQah3ZeTiaIYkO8toOJ1UmdbbT47XH3ibJtibCWCu1dFRQK6Pr6waNkFI11jNLPzyH1tSCc/640?wx_fmt=png&from=appmsg "")  
  
默认密码格式为学校主域名@身份证后六位，试了几个账号直接就进去了，因为有些把默认密码改了，但总有不改密码的小倒霉蛋，这不就来了吗  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ianpxKPnLHoJRVJA76HjY532umDAHYwD1HawmR5hW8qBIlLndpRHCILkt57qkUk5T3B7Ox9iaRCHYSqiaROnZia6FamxYgjCjA0vZuibPmSVFr6s/640?wx_fmt=png&from=appmsg "")  
# 3.测试功能点  
  
东测测，西测测，不得不说，某方的系统做的就是挺好的，没有什么能下手的地方，而且该系统开放的功能点也好少，就几个功能点  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ianpxKPnLHoLhes83bp6qsLjy6hciaicnibMhKJTFN8KPa7Aa6GRfQXZia0SeF38wjibcFkIEib9o6UsibnibsKOzzl6UqZfzTllVcMibia65G324kJ1t8/640?wx_fmt=png&from=appmsg "")  
  
上面的功能测了个遍，没啥有洞的点，但是别忘了，下面不还有一栏吗，这一测，洞就来了  
  
点击更多，然后可以看到学生的信息，看到这种第一个想到的就是测越权  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ianpxKPnLHoJPDmicz6JhKCoITDNeOmLW7FhiabF6sEckWLNxRWNVc5U9cbSjYz35Wd4Y7iayrb1Q89ib7kMeMnsXfwZHBGxBzeKvz1DALhrgOCw/640?wx_fmt=png&from=appmsg "")  
  
抓了几个包没看到啥能一眼就出洞的包，也没有上传点，测了测xss和sql也没什么起色，感觉要白忙活一场了，但是又不甘心，找历史包认真再过一遍，最后下面这个包引起了我的注意（挖洞真要细心，多看看历史包，有时候放包快了说不定就错过了什么好东西）  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ianpxKPnLHoKxGE0xxfKUjjueOtKIazicqKu9jK1J0tyldk1jdBFnWIgXrRXBYrHRfeC76ib6IduT9AqmsJiajh4D2pITpdJXaGp3BDPmxbFHug/640?wx_fmt=png&from=appmsg "")  
  
重放了一下，发现回显的数据包里有敏感的数据，那很明显肯定是通过dataid来传参的，仔细研究了一下他的格式为xsxx&xsxgsq|1234,这个xsxx和xsxgsq应该是固定的功能类别，真正决定数据回显的就是后面的四位数字了  
  
直接遍历后四位  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ianpxKPnLHoLicE3uWMwRaNyU42hVicib5dZjlSaNvv7njczWMzqrNGf0LZv8z0GRk3trVCMLCddNpVffSiaGPZXkGBXGxybt2MhSANL52t67jI0/640?wx_fmt=png&from=appmsg "")  
  
果然出货了  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ianpxKPnLHoKJZAAn1y6AHdOoOicm6MER0EwHhElDe3GmoHjQoxSrWWOpID1ktcJMicAVR9wym6NMGia39PR3RhzwE2rUibZqAChe6Z3JJwFdObI/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ianpxKPnLHoJgDdCzdhfpicsDP04vf7yN3CGeZ02icIOEPWtfS0Tb3bXoYk1J9HocxGfxWFQalueEfhFjunjicd0pydSAJv98BiclicjlYXSOYJkM/640?wx_fmt=png&from=appmsg "")  
  
姓名学号身份证号都有，直接能遍历全校学生的敏感信息，这不高危到手了  
  
后面发现只要是dataid传参都能越权获取敏感信息，如dataId=xsxx%26xsxgsq%7C1234或者dataId=flow%7Cxsxgsq%7C  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ianpxKPnLHoJToEy9b5UND1vRvR89W92NT73kuQ8xvkrluLqEXVrSNCsDO9cFKmicHJDlgxqm68mvP9MT6u23iaiaCasNEZRUv00YyC39ibmujick/640?wx_fmt=png&from=appmsg "")  
  
不同的功能点当然分开交，嘿嘿嘿，又是8rank到手  
# 4.总结  
```
挖洞最重要的还是信息收集，实在在网上找不到什么有用的信息也可以另辟蹊径，比如社会工程学大法啥啥啥的，然后就是认真仔细，养成看历史包的习惯，往往里面就会藏着错过的许多好东西，然后熟悉各种功能点和漏洞，比如说什么功能点会出现什么类型的漏洞，该用什么方法去挖掘，只要坚持就肯定出洞！各位，网安路上一起加油，共勉！
```  
  
  
申明：本公众号所分享内容仅用于网络安全技术讨论，切勿用于违法途径，  
  
所有渗透都需获取授权，违者后果自行承担，与本号及作者无关，请谨记守法.  
  
![图片](https://mmbiz.qpic.cn/mmbiz_gif/BwqHlJ29vcqJvF3Qicdr3GR5xnNYic4wHWaCD3pqD9SSJ3YMhuahjm3anU6mlEJaepA8qOwm3C4GVIETQZT6uHGQ/640?wx_fmt=gif&wxfrom=5&wx_lazy=1&tp=webp#imgIndex=10 "")  
  
  
**没看够~？欢迎关注！**  
  
  
**分享本文到朋友圈，可以凭截图找老师领取**  
  
上千**教程+工具+交流群+靶场账号**  
哦  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/BwqHlJ29vcrpvQG1VKMy1AQ1oVvUSeZYhLRYCeiaa3KSFkibg5xRjLlkwfIe7loMVfGuINInDQTVa4BibicW0iaTsKw/640?wx_fmt=other&from=appmsg&wxfrom=5&wx_lazy=1&wx_co=1&tp=webp#imgIndex=11 "")  
  
******分享后扫码加我！**  
  
**回顾往期内容**  
  
[零基础学黑客，该怎么学？](http://mp.weixin.qq.com/s?__biz=MzUyODkwNDIyMg==&mid=2247487576&idx=1&sn=3852f2221f6d1a492b94939f5f398034&chksm=fa686929cd1fe03fcb6d14a5a9d86c2ed750b3617bd55ad73134bd6d1397cc3ccf4a1b822bd4&scene=21#wechat_redirect)  
  
  
[网络安全人员必考的几本证书！](http://mp.weixin.qq.com/s?__biz=MzUyODkwNDIyMg==&mid=2247520349&idx=1&sn=41b1bcd357e4178ba478e164ae531626&chksm=fa6be92ccd1c603af2d9100348600db5ed5a2284e82fd2b370e00b1138731b3cac5f83a3a542&scene=21#wechat_redirect)  
  
  
[文库｜内网神器cs4.0使用说明书](http://mp.weixin.qq.com/s?__biz=MzUyODkwNDIyMg==&mid=2247519540&idx=1&sn=e8246a12895a32b4fc2909a0874faac2&chksm=fa6bf445cd1c7d53a207200289fe15a8518cd1eb0cc18535222ea01ac51c3e22706f63f20251&scene=21#wechat_redirect)  
  
  
[记某地级市护网的攻防演练行动](https://mp.weixin.qq.com/s?__biz=MzUyODkwNDIyMg==&mid=2247543747&idx=1&sn=c7745ecb8b33401ae317c295bed41cc8&token=74838194&lang=zh_CN&scene=21#wechat_redirect)  
  
  
[手把手教你CNVD漏洞挖掘 + 资产收集](https://mp.weixin.qq.com/s?__biz=MzUyODkwNDIyMg==&mid=2247542576&idx=1&sn=d9f419d7a632390d52591ec0a5f4ba01&token=74838194&lang=zh_CN&scene=21#wechat_redirect)  
  
  
[【精选】SRC快速入门+上分小秘籍+实战指南](http://mp.weixin.qq.com/s?__biz=MzUyODkwNDIyMg==&mid=2247512593&idx=1&sn=24c8e51745added4f81aa1e337fc8a1a&chksm=fa6bcb60cd1c4276d9d21ebaa7cb4c0c8c562e54fe8742c87e62343c00a1283c9eb3ea1c67dc&scene=21#wechat_redirect)  
  
##     代理池工具撰写 | 只有无尽的跳转，没有封禁的IP！  
  
![图片](https://mmbiz.qpic.cn/mmbiz_gif/BwqHlJ29vcqJvF3Qicdr3GR5xnNYic4wHWaCD3pqD9SSJ3YMhuahjm3anU6mlEJaepA8qOwm3C4GVIETQZT6uHGQ/640?wx_fmt=gif&wxfrom=5&wx_lazy=1&tp=webp#imgIndex=12 "")  
  
点赞+在看支持一下吧~感谢看官老爷~   
  
你的点赞是我更新的动力  
  
  

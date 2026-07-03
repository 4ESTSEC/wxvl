#  针对某老OA打了补丁之后再次前台RCE的审计流程  
原创 知名小朋友
                    知名小朋友  进击安全   2026-07-03 01:30  
  
# 免责申明本文章仅用于信息安全防御技术分享，因用于其他用途而产生不良后果,作者不承担任何法律责任，请严格遵循中华人民共和国相关法律法规，禁止做一切违法犯罪行为。  
  
一、前言  
  
      
这个文章其实拖欠的有点久了，是很久之前的一个漏洞，在这个漏洞的基础上的一个绕过手段，师傅们可以看看。  
  
二、鉴权分析  
  
        这个源码在一些目录当中是存在自己的一些路由的，但是除了这个目录之外的是采用无框架的形式进行访问，这里我们其实审计跟攻防什么的都是一个道理，柿子挑软的捏。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Dfrm5V3o6kQ71IZvgMwxQibx7GhZ4hrrkAhPps471dxBia4tI2wCe0dOGlcBP6cSvO94Q9A0nINicRs4tZ8rEYmojbkqAyZibwOVsbbicka7M3mk/640?wx_fmt=png&from=appmsg "")  
  
上面这个漏洞图片其实是当时的一个漏洞点，是存在直接的文件上传漏洞的，这里我们可以先进行分析一下鉴权方式。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Dfrm5V3o6kRQR9k7JxPvDFJe82eByuB3hIab4k7xkQLexQ3logowiaQe5ickIsTOmCn3NuezcZzWN8XZg4ZfnF3gnRG84g8c37duPWuF7tr8Y/640?wx_fmt=png&from=appmsg "")  
  
    这里随意点击一些文件，可以看到这里包含的是auth文件，采用的是文件包含的形式来进行鉴权，同时我们进行跟入这个文件当中进行查看详细的鉴权过程。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Dfrm5V3o6kTv1s6KSsLEa8g0bNhwjCL4z8UBN6t1TMLQxJygltdiaQ4Z9MIPrFiad0fYvNAUwGx5ibx6rzldQ1gI9maZJ7DPejVAjEJ2bQeZO4/640?wx_fmt=png&from=appmsg "")  
  
    可以看到在这里就是进行相关的判断session信息，通过是否存在session信息来进行判断是否登录，这里还是一样的，柿子挑软的捏，我们寻找不调用auth文件之外的无框架访问的文件即可。  
  
（使用AI或者自己搞一个小工具速度还是很快的）  
  
三、漏洞分析  
  
    那么其实还是一样的，在这个文件当中可以看到没有进行调用鉴权文件，auth同时在下面还存在文件上传功能点。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Dfrm5V3o6kRbDibecnTZIxNQSuHic4icIcQLlEeDtSY6ehgmAlSl7mUMU3rol2pamWBdclGMjYIlKt6ccbq871naHWicgflC8IhuicT3fwuiaiboGs/640?wx_fmt=png&from=appmsg "")  
  
在这里继续跟下去，查看上传点。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Dfrm5V3o6kTC9icn0XictiajD3h9L8Jc2bhLvCliaics7QmBgJQIndOuQvibLhZoXqia23dwLK5vu9R76S9C7h0uRgyEAEn2YmqffibnwOgxnLJt76k/640?wx_fmt=png&from=appmsg "")  
  
在这里可以看到，针对上传的文件名进行过滤，其中首先进行。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Dfrm5V3o6kQrmQ0U6U1iajn4Sf3eEw0ic2HoacP6rtvQicIVVicuClLia8PibtlFAOEl1F6CfAv06DoyMa5TeibxNxbpQz1mgrp6micYukWmY76iacIA/640?wx_fmt=png&from=appmsg "")  
  
判断后缀是否为白名单当中的信息（这个源码之前存在漏洞，是直接上传的，这里的代码是打了补丁之后的样子）。  
  
可以看到白名单比较多，有非常多的白名单，其中还有一些acc等关键词。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Dfrm5V3o6kREjCry71Thu7LvEqCvD5sEtEQTEPoXuicOxGicjUbPpaozh9icxCvJIicrvVicNkdR3xYk59yNyzbZ6KvDpMPObhGwUetl1ckrPBCo/640?wx_fmt=png&from=appmsg "")  
  
这里顺便进行部署产品，这里正在部署中。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Dfrm5V3o6kS5Ls5l3y0U39egyAPhOpasqsnzov6kiao7icYuKufdZNialIbWSyLicItcJ0SXpbvU7u0eiaUAUTbB7d0dxW517icLciaGWYPBqpoP04/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Dfrm5V3o6kTIoPdVDI380UsicKCt0h3Jk1y4A5Peu1AdvCNBTHzlESUYuV4u2ciaAhdmEgNPR0qalce57o0FZ1e2NicKF2RjYkbtuzckjDHlaI/640?wx_fmt=png&from=appmsg "")  
  
这里安装好了之后可以看到默认是apache的中间件，那么兄弟们应该都知道apache的解析顺序吧，从右到左，所以我们可以上传一个1.php.acc来绕过。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Dfrm5V3o6kR6cNbwfUpPlLOsTR9ScEXz3dE8RoyYfkficU6eiaU7PYqWDoNuJjC7dn6LHwukfl6GaFiaIdCYwbQQ9kdvF2j9iabqhm4p7TFZ4Ac/640?wx_fmt=png&from=appmsg "")  
  
但是很可惜会对文件进行重命名，这里再次寻找。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Dfrm5V3o6kQLmicrwxSciawcGQfdbME0PkQyWTENbzxeKKODldFl4qrK9f1a1Mc7j4MnibH9kEUFgnu4K1L4PZpXdibRWVWz8X4dP0YhQNoq4Go/640?wx_fmt=png&from=appmsg "")  
  
最终找到了一个新的点，但是我发现之前的那个点，md5没有包含到文件名里面。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Dfrm5V3o6kStiau6Cu8XQYbwXcY2j04nBgXPr9ansiaje9U0BsAiavQj4AFPCwaF6WuOcYQqWZmVnCGqEpHm60TyW5xmMBpiaMevl2WA8ltstkc/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Dfrm5V3o6kQbxDTz7iaDxnQ4btspag0ZPIGiaqgyMgK7JibktKO8tgPTBStFNVRTORGcex1DnJWErmBE75IeVZvCZk75l0DiaaR3NQZHxzSRqpQ/640?wx_fmt=png&from=appmsg "")  
  
而且还输出了文件名称和文件。  
  
还是用他来复现。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Dfrm5V3o6kSic7xA3gObSeg6c2rbeicmhcYJ1QqCiaJb6VHx97v6k3ibmgbSUgOZrdbLBly783VdcAkaCTh3XgXVUTE2HRfvEQzh9Wuqc8ibrdcI/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Dfrm5V3o6kSYmTbPf6g7TJUf0VENibGHZ0icZocYZNMmy2YP5V3GeLzVokNgPsMD1NCR7f2GxYnSvDOIXZvttLC2CsNFTXDAZ7icb0C5rQQypU/640?wx_fmt=png&from=appmsg "")  
  
其实就是用到了白名单比较多，然后配合apache解析顺序来绕过的。  
```
```  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/Dfrm5V3o6kSKicMR42goLwh74FCSt3NVPhvLHoVKm3M6w2Ip9rjiafOyJ2DdFNiaLvd0aL7xaWPXia7UNcWHxrXOXAWME1Xpu5M052L9E5bzDPI/640?wx_fmt=png&watermark=1&wxfrom=5&wx_lazy=1&tp=webp#imgIndex=23 "")  
  
  

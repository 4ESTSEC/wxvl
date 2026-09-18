#  泛微云桥 e-Bridge sendWxMsg SQL注入漏洞  
Superhero
                    Superhero  Nday Poc   2026-09-18 01:55  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/Melo944GVOJECe5vg2C5YWgpyo1D5bCkYN4sZibCVo6EFo0N9b7Kib4I4N6j6Y10tynLOdgov9ibUmaNwW5yeoCbQ/640?wx_fmt=other&from=appmsg&wxfrom=5&wx_lazy=1&wx_co=1&randomid=8n1b48rw&tp=webp#imgIndex=0 "")  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/Melo944GVOJECe5vg2C5YWgpyo1D5bCkhic5lbbPcpxTLtLccZ04WhwDotW7g2b3zBgZeS5uvFH4dxf0tj0Rutw/640?wx_fmt=other&from=appmsg&wxfrom=5&wx_lazy=1&wx_co=1&randomid=vx7xykg3&tp=webp#imgIndex=1 "")  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/Melo944GVOJECe5vg2C5YWgpyo1D5bCk524CiapZejYicic1Hf8LPt8qR893A3IP38J3NMmskDZjyqNkShewpibEfA/640?wx_fmt=other&from=appmsg&wxfrom=5&wx_lazy=1&wx_co=1&randomid=x3261qcu&tp=webp#imgIndex=2 "")  
  
内容仅用于学习交流自查使用，由于传播、利用本公众号所提供的  
POC  
信息及  
POC对应脚本  
而造成的任何直接或者间接的后果及损失，均由使用者本人负责，公众号Nday Poc及作者不为此承担任何责任，一旦造成后果请自行承担！  
  
  
**01**  
  
**漏洞概述**  
  
  
泛微云桥 e-Bridge sendWxMsg 接口存在SQL注入漏洞，未经身份验证的远程攻击者除了可以利用 SQL 注入漏洞获取数据库中的信息（例如，管理员后台密码、站点的用户个人信息）之外，甚至在高权限的情况可向服务器中写入木马，进一步获取服务器系统权限。  
**02******  
  
**搜索引擎**  
  
  
fofa:  
```
(body="泛微云桥e-Bridge" && (body="wx.weaver.com.cn" || body="www.weaver.com.cn")) || header="EBRIDGE_JSESSIONID" || title="泛微云桥e-Bridge"
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/8o9dnzNIF1jzuf7PrPAiafYNRqWwloLOYibEspYuLByZC5v4PUHwO5orb2Q2WRGgoQ5t4XwMYwjoINsQCtASwmCDYMd9dzMdmjcCjOsIQyCick/640?wx_fmt=png&from=appmsg "")  
  
  
**03******  
  
**漏洞复现**  
  
延时5秒  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/8o9dnzNIF1hhPN7I9KIia1TjfyibeKic1ia5Pkg6epWHD0H9mC5Wen94e5GJ0oufOHqvSbykCQqsmehRk9ZNqsS55XO7ojZN5E38BFvUx1lIy6c/640?wx_fmt=png&from=appmsg "")  
  
  
**04**  
  
**自查工具**  
  
  
nuclei  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/8o9dnzNIF1jibHibozjOIFMvMzWzHUU2ZYLFlwl63gzEO2MjIeqCY7trguBCVpMTeAAiaLR9rwGwqOjxXeoTCIJcN6KlnFUWViaX6SfNc7j0VQc/640?wx_fmt=png&from=appmsg "")  
  
afrog  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/8o9dnzNIF1h1vGd2OQRlzibMS68ub0y4KmghwntMUNgYxwJbljykj8xGGyzUyS8UV1cOHGayOH4GnE3y1X68HkbgJmf1iawAoD5vZzY7TNKwM/640?wx_fmt=png&from=appmsg "")  
  
  
**05******  
  
**修复建议**  
  
  
1、关闭互联网暴露面或接口设置访问权限  
  
2、升级至安全版本  
  
  
**06******  
  
**内部圈子介绍**  
  
### 【Nday漏洞实战圈】🛠️  
  
专注公开1day/Nday漏洞复现 · 工具链适配支持  
  
✧━━━━━━━━━━━━━━━━✧  
  
🔍 **资源内容**  
  
▫️ 整合全网公开1day/Nday漏洞POC详情  
  
▫️ 适配Afrog/Nuclei检测脚本  
  
▫️ 支持内置与自定义POC目录混合扫描  
  
🔄 **更新计划**  
  
▫️ 每周新增7-10个实用POC（来源公开平台）  
  
▫️ 所有脚本经过基础测试，降低调试成本  
  
🎯 **适用场景**  
  
▫️ 企业漏洞自查 ▫️ 渗透测试 ▫️ 红蓝对抗 ▫️ 安全运维  
  
✧━━━━━━━━━━━━━━━━✧  
  
⚠️ **重要声明**  
  
▫️  
仅限合法授权测试，严禁违规使用  
  
**▫️虚拟资源服务，购买后不接受任何形式退款**  
  
▫️  
付款  
前请评估需求，慎重考虑  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/8o9dnzNIF1jBQPI2L3QtI64DhvtmgKvWrA0EDBeUXKLXsIXTDm2QMpwVlYNlUFnwnkwmB9ugbRibLxwdVx7qGnYiaLibUEP9FAK7soGVaykF4M/640?wx_fmt=png&from=appmsg&watermark=1&wxfrom=5&wx_lazy=1&tp=webp#imgIndex=8 "")  
  
  

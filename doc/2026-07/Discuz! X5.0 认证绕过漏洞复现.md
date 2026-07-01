#  Discuz! X5.0 认证绕过漏洞复现  
原创 安羽安全
                    安羽安全  安羽安全   2026-07-01 03:10  
  
**01****免责声明：**  
  
文章内容仅供日常学习使用，请勿非法测试，由于传播、利用此文所提供的信息而造成的任何直接或者间接的后果及损失，由使用者承担全部法律及连带责任，作者及发布者不承担任何法律及连带责任。如有内容争议或侵权，我们会及时删除。  
  
**02    漏洞描述**  
  
Discuz! X5.0 认证绕过漏洞  
   
漏洞  
复现 poc  
。影响产品：Discuz! X5.0  影响版本：20260320 ≤ 版本 ≤ 20260501  
  
**hunter：app.name=="Discuz!" and app.version=="X5.0"**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/h29PqzMrB7uVgBOGeo2iaJ26kkZynAt0MYkTxmhoKkEGstKribZ0YEv6tbkoibPqwGJsow2PpYHPibm35UQwbZY6nY0yJlV9wZphPaRT0H4Rl1A/640?wx_fmt=png&from=appmsg "")  
## 03    漏洞复现   
  
1、访问系统  
  
![](https://mmbiz.qpic.cn/mmbiz_png/h29PqzMrB7utFUFhoqMcQjxtIJib5bwKmibrVuDBD07icxasUfqMUZLGZVtVBNq4tDj0fr6FzIu8gttYVsTWLnO9W3gGAjto0ghWotkTRWZNBg/640?wx_fmt=png&from=appmsg "")  
  
2  
、漏洞验证 获取auth认证参数  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/h29PqzMrB7twI6domMNzSvWCsXIWxJgH7RQia50h3NhsBfhNEsXhvcDMqTmFhVvalfsGQGmNVES1npkoUVRJ5k4SA87d89whzofOMSWrBVp8/640?wx_fmt=png&from=appmsg "")  
  
使用获取的  
auth参数 放到code=后面 ，复制返回包中的链接即可下载备份的数据库文件。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/h29PqzMrB7uu0CFXTHpXJtXYMax8WZe8X61wL2PrVPQUzCMA5RjWrfDJbEB0eeM0AibcRD4NichBwxgyFbbTFckqwkr8xkia9lN9w5L545Vbw0/640?wx_fmt=png&from=appmsg "")  
  
**04    漏洞详情**  
  
**漏洞POC详情，加入下方内部圈子获取**  
  
**圈子目前福利：**  
  
整合全网公开1day/Nday漏洞POC、漏洞复现  
  
每周持续更新，已更新漏洞500+  
  
md5帮忙解密、凌风云网盘搜索 帮忙下载资源  
  
后续福利还在增加中......  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/h29PqzMrB7s3zIwW89fh2MxrvowUy9XtAWZaV1iad6c68a84Sly1ZETmv3gYUm6pFibuiatB2gM2BPILviaGgj5BbelDyYVHwcaJE3K9I2l8dia4/640?wx_fmt=png&from=appmsg "")  
  
**漏洞持续更新中，限时 39 一年**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/h29PqzMrB7uicO3xOYWNfjIW6mhjlOoJQV8eiawMnpPuLzq9EBN0xyvqVGXSjJLZEJP0hctduBAiao32AuWN4LhHh0ea038DianV1FZabCeXROA/640?wx_fmt=png&from=appmsg "")  
  
扫描上方图片 二维码即可加入纷传内部圈子  
  
![](https://mmbiz.qpic.cn/mmbiz_png/h29PqzMrB7uEkTwg3fZp16B5KulTOxVVuIoaI17qkX5zqJjM7vbOia1LAD8oDoicHO8gGdNIMWia8Ijtj3t4gVBEHiaHHws7VDv09VGIpFibuuYM/640?wx_fmt=png&from=appmsg "")  
  

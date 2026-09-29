#  记某SRC阿里云存储接管&企业SRC隐私合规漏洞  
原创 神农Sec
                        神农Sec  神农Sec   2026-09-29 01:00  
  
  课程培训  
  
  扫码咨询  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b7iaH1LtiaKWXLicr9MthUBGib1nvDibDT4r6iaK4cQvn56iako5nUwJ9MGiaXFdhNMurGdFLqbD9Rs3QxGrHTAsWKmc1w/640?wx_fmt=jpeg&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/b96CibCt70iaaJcib7FH02wTKvoHALAMw4fchVnBLMw4kTQ7B9oUy0RGfiacu34QEZgDpfia0sVmWrHcDZCV1Na5wDQ/640?wx_fmt=png&wxfrom=13&wx_lazy=1&wx_co=1&tp=wxpic "")  
  
  
#   
  
专注于SRC漏洞挖掘、红蓝对抗、渗透测试、代码审计JS逆向，CNVD和EDUSRC漏洞挖掘，以及工具分享、前沿信息分享、POC、EXP分享。不定期分享各种好玩的项目及好用的工具，欢迎关注。加内部圈子，文末有彩蛋（课程培训限时优惠）。  
#   
  
  
01  
  
0x1 记某SRC阿里云存储接管&企业SRC隐私合规漏洞  
  
## 0x1 阿里云存储接管  
  
  
这个漏洞想着还是给师傅们分享下，也就是前面介绍资产收集的过程，后面使用转子工具跑JS接口文件中，可以跑出一些敏感接口，里面有一些敏感信息，比如AK/SK，sfz、xm、手机号以及一些接口漏洞  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QWO2I6yicJTI4iaSBCs2EqTP0jIeh7MEXiacBOxxyxjlnYib7PSmiaZicHwLfshtHmiaEhst0HJW00d48sicBpYvhWwKHxrfpicrQKcLWAQ/640?wx_fmt=png&from=appmsg "")  
  
**常见云的ak/sk特征**  
  
下面我给师傅们介绍下常见的几个厂商的 Access Key  
 内容特征，然后就能够根据不同厂商 Key 的不同特征，直接能判断出这是哪家厂商的 Access Key  
 ，从而针对性进行渗透测试。其中我们云服务器常见的就是阿里云和腾讯云了，我主要给师傅们介绍下面两种Access Key的特点。  
  
**阿里云**  
  
阿里云 (Alibaba Cloud) 的 Access Key 开头标识一般是 "LTAI  
"。  
  
^LTAI[A-Za-z0-9]{12,20}$  
- **Access Key ID**  
长度为16-24个字符，由大写字母和数字组成。  
  
- **Access Key Secret**  
长度为30个字符，由大写字母、小写字母和数字组成。  
  
**腾讯云**  
  
腾讯云 (Tencent Cloud) 的 Access Key 开头标识一般是 "AKID  
"。  
```
^AKID[A-Za-z0-9]{13,20}$

```  
- SecretId长度为17个字符，由字母和数字组成。  
  
- SecretKey长度为40个字符，由字母和数字组成。  
  
可以使用行云管家进行云服务器接管操作  
  
![img](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QUHONJaibZbZRLudKgIWzJlubEYnPiazQAkDbc3PEc05679kcO0C0NOUSMc2ewAMGXlVTI964aj1CpG8eTAyokCAmflrCicFjGtNg/640?wx_fmt=png&from=appmsg "")  
  
img  
  
使用云存储桶工具登陆，接管云资产  
  
![img](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QVcXADV7OHLsjQ8z5E7kBfw1fOlE2fUwVwAqCWeMzSpCLwyUA4TYMo4SibFBcKRMvWcKwQCmHbjmZdlbJH9oiaZ2p2g6ktTfaXXA/640?wx_fmt=png&from=appmsg "")  
  
img  
  
成功云接管该公司云资产几十个G的内部公司文件  
  
![img](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QXTn1LsBW0vITC5kUwAmSx4DVUvsicWL2AbtpCUiaOyTqCiaMWboZxfHjuW9yyiaibjjK4ZQ65ew7d01yUpgBFiboSZP5A4uUKSyjHib4/640?wx_fmt=png&from=appmsg "")  
  
img  
  
然后使用CF进行云接管操作也是可以的，可以看到目前还是root权限，最高权限，至此该资产全部都拿下了  
  
![img](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QV64c4ibwLdvreuJJQ9IPicwraNZ0yexgzeCtbHGWAWPB5TzjxvV0c4MUCLkCm7wo0QlrfhXgAiciaOibpHonvibloWWWHNbT5hofsvU/640?wx_fmt=png&from=appmsg "")  
  
img  
## 0x2 前台SQL注入漏洞  
  
一个前台管理系统，通过1/1和1/0进行判断存在SQL注入  
  
然后构造payload如下：  
```
/student_list.php?cid=2/if((length(11))>1,1,0)

```  
- length(11)  
：计算 “11” 的长度（字符串 “11” 的长度为 2）。  
  
- 条件 (length(11))>1  
：即 2>1  
，结果为 **真**  
。  
  
- 因此整个表达式的计算结果为 1  
（满足条件时返回第一个值）。  
  
![img](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QVdy3Z3g5xXhzsCc80KAicaNXWd3TqLbHzSSPibicHUgYvHZj4LNaBASXsnhdNlAPibvicf5CXDBibKP4LL3JoHYW47nmTZ8uUetsriac/640?wx_fmt=png&from=appmsg "")  
  
img  
```
/student_list.php?cid=2/if((length(1))>1,1,0)

```  
- length(1)  
：计算 “1” 的长度（字符串 “1” 的长度为 1）。  
  
- 条件 (length(1))>1  
：即 1>1  
，结果为** ****假**。  
  
- 因此整个表达式的计算结果为 0（不满足条件时返回第二个值）。  
  
![img](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QWnkzqkPJZ0XPic6c8Vic2H6uvCYK63FyWvUKp2h4NQHZK2AoU1S079cqjo4ouV622fXiaN1LCaVoDdPiccgKJyuAILuSoTuicaPC8c/640?wx_fmt=png&from=appmsg "")  
  
img  
  
最后通过判断数据库长度是7  
```
/student_list.php?cid=2/if((length(schema()))>1,1,0)  //正常页面
/student_list.php?cid=2/if((length(schema()))>8,1,0)  //报错

```  
  
![img](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QV5icNFpXZqprgr91d8iakOCyh41HLV9DibuFxoicVLFibH3IibuXbC8G9bicS5rOWdxRcEJ7jicXjA0WxwbpsgUqMv7posqPBVXwj6LVA/640?wx_fmt=png&from=appmsg "")  
  
img  
  
针对SQL注入中关键字绕过，下面是waf过滤，对应的常见关键字替换：  
```
schema()  //数据库
database()

@@version  //版本
version()

current_user  //用户名
system_user()
session_user()

```  
  
![img](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QUWiaIAD0YK0tM4vx05bmXZKfzNtASNOcLzDcBxxtzmGEPiabtDb0QwI65Pc6EY6b4vEz1OTCKf6kaP1H8vn2uPjzGhvhrkWAJ5U/640?wx_fmt=png&from=appmsg "")  
  
img  
## 0x3 JWT可爆破/未验证签名导致越权  
  
这里使用天眼查网站，去寻找相关企业资产的微信小程序、微信公众号  
  
![img](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QXMqzc6BNTDlx4jGArx18sBLwkkFLItfEHYib6vNk38dOhNV3H0NgnQibmQW457cHvWKOUw6QmHgNxfDGJwtkj2pkQqsp1lMcEDc/640?wx_fmt=png&from=appmsg "")  
  
img  
  
首先通过微信搜索小程序，找到对应目标资产中的小程序系统  
  
![img](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QXdpJ8Ya8ibkIuxZC8JUcs4MxHZuI7h7g3wrbFw0h9gk7xKDPoccxMiccer6r1NGibOibrHxcraqMAEawibRD069lbz6pxUIYKiaRrgA/640?wx_fmt=png&from=appmsg "")  
  
img  
  
然后还有就是需要师傅们关注下微信公众号，很多官方的公众号关注之后，下面都有对应的微信小程序功能，或者跳转到web界面到功能点  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QWklABQayDcXicJrjwuch5Nf5pUZnficNs2rqZ1w6AicD5aRDmhCtmRMS4B1NP5Or46lUBxlmKs3UxBAibY3nRKLia2UeEib33KCfOyM/640?wx_fmt=png&from=appmsg "")  
  
这里来到我们熟悉的微信小程序功能界面，这个时候我们需要一直打开我们的bp抓取小程序的数据包（这个是一个测试小程序的一个好习惯，因为有些接口，包括敏感信息泄露，看历史的数据包很关键），然后看看数据包有没有什么提示，因为这里我的bp安装了HAE，一般重点关注带颜色的数据包  
  
![img](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QU4RZpfzyBApsZ0I2Qm9mViaomhDdJv54GsTicHM2IaAgHLMk8zCBvjiac0eWVOMysyo7j1gEkv8Rj3vmLzxoMadm1fCoaMRVDSicw/640?wx_fmt=png&from=appmsg "")  
  
img  
  
这里我们可以看到bp的历史数据包，显示了多个JSON Web Token也就是大家常说的JWT值，像一般碰到这样的JWT值，我一般都会选择JWT爆破尝试haiy选择有无设置None加密，去进行做一个渗透测试  
  
![img](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QUH7uQqmsu6b0NQ9YF2Fzxnlg2ibLibzGWibMbviaP6CwbDJCJFw9tXpk1S3SRzdQzMBARCMeSxTllvFPIXl2QmoAELicS6r1HCibOJY/640?wx_fmt=png&from=appmsg "")  
  
img  
  
这里先直接复制到https://jwt.io/ 去看看这个JWT里面的内容，然后去猜测这个paylod校验哪部分  
  
![img](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QXLZicsRkO0VPTe89hXrNiazrgl6PXlU7UtT6KKxZXUPUv4ku57kbO3Sph3icrPzaqZSvkjRBLiarwNCq6iaxsXbIicX4KqonC0CggbI/640?wx_fmt=png&from=appmsg "")  
  
img  
  
下面我来给师傅们讲解下这个payload代表什么，一些新手师傅可能没有了解过，包括后面进行数据包替换，也是要修改其中的payload值  
  
<table><thead><tr><th style="color: rgb(89, 89, 89);font-size: 15px;line-height: 1.5em;letter-spacing: 0.04em;text-align: left;font-weight: bold;background: none left top / auto no-repeat scroll padding-box border-box rgb(240, 240, 240);height: auto;border-style: solid;border-width: 1px;border-color: rgba(204, 204, 204, 0.4);border-radius: 0px;padding: 5px 10px;min-width: 85px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">字段名</span></span></section></th><th style="color: rgb(89, 89, 89);font-size: 15px;line-height: 1.5em;letter-spacing: 0.04em;text-align: left;font-weight: bold;background: none left top / auto no-repeat scroll padding-box border-box rgb(240, 240, 240);height: auto;border-style: solid;border-width: 1px;border-color: rgba(204, 204, 204, 0.4);border-radius: 0px;padding: 5px 10px;min-width: 85px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">值</span></span></section></th><th style="color: rgb(89, 89, 89);font-size: 15px;line-height: 1.5em;letter-spacing: 0.04em;text-align: left;font-weight: bold;background: none left top / auto no-repeat scroll padding-box border-box rgb(240, 240, 240);height: auto;border-style: solid;border-width: 1px;border-color: rgba(204, 204, 204, 0.4);border-radius: 0px;padding: 5px 10px;min-width: 85px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">说明</span></span></section></th></tr></thead><tbody><tr style="color: rgb(89, 89, 89);background-attachment: scroll;background-clip: border-box;background-color: rgb(255, 255, 255);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;"><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">role</span></span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">appUser</span></span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">用户角色，表明用户属于应用层普通用户（非管理员）</span></span></section></td></tr><tr style="color: rgb(89, 89, 89);background-attachment: scroll;background-clip: border-box;background-color: rgb(248, 248, 248);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;"><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">exp</span></span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">1747377338</span></span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">令牌过期时间（Unix 时间戳）。通过转换可得具体时间：2025-11-14 11:15:38 UTC</span></span></section></td></tr><tr style="color: rgb(89, 89, 89);background-attachment: scroll;background-clip: border-box;background-color: rgb(255, 255, 255);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;"><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">userId</span></span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">xxxxxxxxxxxxxxxxxx</span></span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">用于标识用户身份</span></span></section></td></tr><tr style="color: rgb(89, 89, 89);background-attachment: scroll;background-clip: border-box;background-color: rgb(248, 248, 248);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;"><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">user_key</span></span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">xxxxx-xxxx-xxxx-xxxx</span></span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">用户密钥或关联密钥（可能用于访问控制或加密）。</span></span></section></td></tr><tr style="color: rgb(89, 89, 89);background-attachment: scroll;background-clip: border-box;background-color: rgb(255, 255, 255);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;"><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">username</span></span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">1xxxxxxxxx79</span></span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">手机号，一键微信登陆的</span></span></section></td></tr></tbody></table>  
  
这里先使用自己修改的JWT脚本爆破工具，看看能不能爆破出密钥  
  
爆破发现其密钥为123456  
  
![img](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QUBeYCp6vpVZeFALgvZ64S5YsCGfCqpbq00CKrxY1HzXiaT71bK4VPiciah2lVicTG9amw9OFYD1AlT8S2HLHhSUyszyicl3ZQzIWjw/640?wx_fmt=png&from=appmsg "")  
  
img  
  
然后直接来到刚才JWT的网站，去利用该key构造JWT，可以直接进入后台，下面的勾需要勾上  
  
![img](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QWCRRHCSUGbHZaTSo2ibSYCSLpxhXI6nMMzJAZrlmTDHaaJDzZDJWszTbohTAqwibFqHfRBhL8KLNT0uhcvuQg9PDsTSxaeABIPc/640?wx_fmt=png&from=appmsg "")  
  
img  
  
因为这里我经过测试，这个网站的JWT是对user_key进行校验，所以只要在规定时间内user_key不过期，那么我们就可以拿另外一个手机号进行测试，替换bp抓取登陆口的数据包，然后放包就可以直接登陆别的账号  
  
首先这里需要修改下时间戳，拿这个网站：https://tool.lu/timestamp/  一般都是改成第二天的时间，不可以早于测试时间  
  
![img](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QX0CxEfFHK8QiboAS2hicWnKt9UNpxUluMSYCaQsGK6YHYRTic8LQPbOWPjhM8EAf5qpAicHj6KsxicFGbanXSljTsgQplK4fCho2A8/640?wx_fmt=png&from=appmsg "")  
  
img  
  
还有就是把username替换下，这里我做测试，替换我的卡二，也就是最后面说93的尾号，因为经过测试，普通用户的role 都是appuser，这里猜测管理员可能是admin  
  
![img](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QXrWmIiaRDKJBWmYNEib6H0V05wWQcXEYVFRwRLjMciay73n7lzricjodJAVMZ1KQiaRwrEacoZsMUUQIDRNLiam5Ax0Q9rxm939qDBQ/640?wx_fmt=png&from=appmsg "")  
  
img  
  
然后直接在小程序登陆口，使用bp抓包，然后劫持数据包，进行替换token值，因为这里经过测试是校验的JWT值  
  
通过不断替换JWT值，然后不断测试放包，放包，最后面可以直接不需要使用账号密码，直接登陆改账号  
  
![img](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QWGFSFaYxp5RoGBmveDmpHzdCApRGrmZWWLEibyFricWMIv4fibhMMANicbfDjfVcickYjMRXI3qkgxxic0gjoVRcicaWVgF7kiao4Kia5E/640?wx_fmt=png&from=appmsg "")  
  
img  
## 0x4 隐私合规漏洞  
### 一、SRC的隐私合规介绍  
  
写文章写到这里，突然翻到了之前的笔记，看了下隐私合规漏洞的内容，然后上网搜了下，相关资料比较少，这里就给师傅们拓展下隐私合规漏洞。  
  
像很多小程序和APP应用，都有这个隐私合规的东西  
  
![img](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QUicGqDz2JGXxs7eicEk52tYPJPrSy65V5gTzAp9OibU2fqAkFQ3pfY3HibDSd2LMBDXOyDb3QAbbOS8A7otLt4ZgBCCq7sjpRYV2g/640?wx_fmt=png&from=appmsg "")  
  
img  
  
隐私合规，就是国家出台的对互联网公司的一个法律规范，防止 app 没有做好对用户的隐私合规性做出的规定，如果发现有公司的 app 没有按照要去要做，就会对该公司进行处罚，因此很多 src 会收取隐私合规漏洞。  
  
比如：对于是否收取隐私合规漏洞，需要看他们的公告，如果没有写，那就一般不收取，如果写了隐私合规方面，并且还有奖励标准，那就收取！  
  
下面拿小米SRC的隐私合规的公告给师傅们分享下：https://sec.xiaomi.com/#/notice/detail/221  
  
![img](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QWBGcVTx7x9du5viaSXUGOsWGzhLX4kHar20qXHytGs8Zh6O7tiauDylL2WHo8n6qsFIdHwp7BxT4AK6lv496NSESTGia1tVOqN4U/640?wx_fmt=png&from=appmsg "")  
  
img  
  
![img](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QWZrPgPoicLytI0hGclCMzeYtica58rQQ8DLdjMRFZzjjkS4ERJakXxlXDtfSJulu93MYNcDwpktrdJiazIxvHQBUR25Hfnnyzv0k/640?wx_fmt=png&from=appmsg "")  
  
img  
  
就比如说下面的这几个小截屏，是不是我们平常在APP和小程序非常常见的一些弹窗提示，但是有些APP和小程序不提示这样的授权权限，而是非授权读取手机敏感个人信息，那么就违反了隐私合规，可以进行相关平台提交安全漏洞。  
  
![img](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QU7dRVBsuEqvyD8TwibjplGmwDiauQYDpn80yE5vHBSAxsohscnJicOPjBAuza4GUBqQyKibgGEEdne5kTk0ibbnTibM4FjMNRQE1sXI/640?wx_fmt=png&from=appmsg "")  
  
img  
### 二、隐私合规漏洞汇总  
1. 一个 app 用户登录需要两个条款，用户协议和隐私政策。  
  
1. 用户对于自己信息的处理，包括查看 删除 注销 投诉等以及政策发布，失效，更新日期等。  
  
1. 隐私政策的描述的情况需要符合 APP 实际情形，比如隐私政策中对于注销，投诉提供的操作方法，需要与 app 内实际操作路径相同。  
  
1. 对于业务开展目标用户为 14 周岁以下的儿童，需要有专门的儿童个人信息保护规则。  
  
1. 举例：不同意隐私政策直接退出 app 算违规 如果有提醒则不算默认勾选同意的正常 算违规隐私政策需要采用“告知-同意”的方式，以明显的方式提醒用户阅读并主动同意隐私 政策。  
  
1. 比如 APP 首次运行时，主动弹框提醒用户查看，用户点击同意后才能开始手机号个人信息和申请权限，弹窗要设置"同意"和"不同意"两个选项。  
  
1. 在注册登录环节需要再次提醒用户查看，一般会采用勾选框等形式。也要确保用户进入APP 后能随时查看隐私政策，一般要求在四次（包含）以内点击就可以阅读到。所有地方的隐私政策内容需要保持一致。  
  
1. 用户拒绝提供一些不必要的信息不可以直接退出 app(必须有二次提醒才可以退出)  
  
1. 权限索取最小原则，寄快递的地方 app 需要你的定位，如果不仅仅要了你的定位则违规。  
  
1. 拒绝定位不让用功能违规。  
  
1. 有注册就要有注销的功能，没有就是违规。且注销的条件不能多余注册的条件。  
  
1. 注销了 app 没有删除你的数据是不算的。  
  
1. 注册的时候选择国家，台湾和中国是分开的。(不一定)  
  
1. 在 app 使用中，用户拒绝了 app 权限授权，则 app 不能频繁申请权限。不频繁的申请评论应为超过 48 小时再次申请。  
  
1. app 申请适用权限，不告知目的。算违规。  
  
1. app 申请权限时，只有拒绝和始终同意，不算违规。  
  
1. 实名认证也要告知-同意的原则  
  
1. 推送功能是要求有关闭功能点的，没有就违规。  
  
1. 开屏广告，不可诱导用户点击，比如欺骗按钮。  
  
1. 用户投诉的渠道APP 内设置反馈表单，在线客服，智能客服等功能处理用户反馈。  
  
1. 涉及人工处理的，承诺处理实现不超过 15 天。  
  
1. 未成年类  
  
1. 若是网络游戏服务，必须防沉迷，设置充值上限，未成年尽可周五，周六，周日和法定节假日每日 20-21 时向用户提供 1 小时服务。  
  
1. 若是网络文学，网络动漫，网络直播，网络音频服务需要设置青少年模式，提供适合未成年用户浏览的内容。  
  
1. 隐私政策涉及敏感信息的地方未加粗。  
  
### 三、隐私漏洞审核标准相关参考  
- 《中华人民共和国个人信息保护法》 http://www.cac.gov.cn/2021-08/20/c_1631050028355286.htm  
  
- 《App违法违规收集使用个人信息行为认定方法》 http://www.cac.gov.cn/2019-12/27/c_1578986455686625.htm?from=groupmessage&isappinstalled=0  
  
- 《工业和信息化部关于开展纵深推进APP侵害用户权益专项整治行动的通知》 https://www.miit.gov.cn/jgsj/xgj/fwjd/art/2020/art_0b18f16130584615a3c585b931092da6.html  
  
- 《常见类型移动互联网应用程序必要个人信息范围规定》 http://www.cac.gov.cn/2021-03/22/c_1617990997054277.htm  
  
- 《工业和信息化部关于进一步提升移动互联网应用服务能力的通知》 https://www.miit.gov.cn/jgsj/xgj/gzdt/art/2023/art_73991cdf4bb2407c822d475250cd21e7.html  
  
![img](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QWLq5AIHOIVGNulF3SVJjlOYG9EhDjflRwmMxcbIvUU9OmWibhByjxk7Uyyz9Arv5PQ11VcKLRgxkez2C9s9fmnWyyFqdzbY19M/640?wx_fmt=png&from=appmsg "")  
  
02  
  
0x2 培训课程介绍  
  
26  
  
**SRC漏洞挖掘培训课程**  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/6cIuvSQkkicOHhYFkQLTibYAMUR9rfZ9eUrI78toIC4V2304G909O6s6CnVrAGiaYLEJM9XuUARhzNfxCtYKQfQ83wfPSlqpshSScfoYzSKzgY/640?wx_fmt=png&from=appmsg&wxfrom=5&wx_lazy=1&watermark=1&tp=wxpic#imgIndex=4 "")  
  
  
**1.课程价格目前是575（后面也会随着人数越多，涨价）🌟师傅们还可以上车补票，冲冲冲！**  
  
**2.报名成功送知识星球一个，拉内部小圈子交流群+SRC直播通知群！✨**  
  
**3.一周2节课程，直播+录播形式，课程内容大家可以看课表，目前是第一期，一次报名永久无限听课！❤️**  
  
**4.目前是第一期课程，后面比如说开了二、三期，都是不用在花钱的！**  
  
**5.上课结束后，会把视频录播+课件笔记一起打包发直播群！**  
  
**6.哔哩哔哩SRC课程公开课，链接🔗直达：**  
  
**https://space.bilibili.com/642258933**  
  
SRC课程详情🔎：  
[学了一堆理论，还是挖不到漏洞？你缺的是实战！](https://mp.weixin.qq.com/s?__biz=Mzk0Mzc1MTI2Nw==&mid=2247509869&idx=1&sn=4bd678e9f9c864300cc2426432a8c967&scene=21#wechat_redirect)  
  
  
内部小圈子知识星球详情🔎：[50 元封顶！渗透攻防 + SRC 漏洞星球限时开放！](https://mp.weixin.qq.com/s?__biz=Mzk0Mzc1MTI2Nw==&mid=2247509408&idx=1&sn=2e12452dfc2d34631af5109af28a6758&scene=21#wechat_redirect)  
  
  
欢迎关注公众号：  
神农Sec  
，报名咨询添加VX：  
routing_love  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/mcko8AHj6QVcCkxIUpaBmNic17zibGfXMWrr9z89gE0DFtbOu3QYzD5d62zsp6qwc38Pssk60mLq8VKthcMOmctVlHU716S5G4KYmrKVrEj5c/640?wx_fmt=other&from=appmsg&watermark=1&wxfrom=5&wx_lazy=1&tp=webp#imgIndex=6 "")  
  
开课快五个月时间  
，课程目前已经  
累计加入了1000+个学员  
了，课程培训招生任火热持续中，师傅们  
对于我们课程感兴趣的，想要学习技术，找工作的可以咨询我报名  
。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QVmdLBDlbl5p4Teyw5qqFOIFTIUxIxRay83I5qDXG690XI61gRj8MXTvTaibC4q2cCb1CbM4XS2FK6X4KYhPTX2ibgvA363YYwcE/640?wx_fmt=png&from=appmsg "")  
  
课程培训记录📝，每次上车在1-3小时之间，上课包括课程内部群大家  
交流氛围很好！  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/mcko8AHj6QV7iczVHowN13BzCTraG8jDUoe5hluiaZ90RUy7FjW398DictcrZhHrYpMgw4polRqvlGua6iakYdARPI3Jkiahhjvrvkviblm19U4F0/640?wx_fmt=other&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=4 "")  
  
课程上课笔记课件📒都会打包给师傅们，笔记都非常详细，很多几k价格的培训机构哪怕是课件笔记都没有的，我这里都是下课第一时间把  
录播+笔记打包发给大家！  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QWbRV4mBn8GZHrvHocPMYYcBuAM3gyIKOM0SicBWQhywMehkXInvEerRLySOPPMzEmM2GLSlOMFREx6QItqtCgCibGs2MeY6yvu0/640?wx_fmt=png&from=appmsg "")  
  
平常也都会给学员进行一些项目发布，包括后面的  
工作、护网内推等，经常上麦交流，大家互相学习，简历优化等。  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QXvjjkgJibDEUhdDjErjibiangGsN0rqb0Av59xfyxBbDrTMNdfIAhNXlx0HQKvxIVBIEGAAbYrEENzd77j65asejlD4a50Sb4U7o/640?wx_fmt=other&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=20 "")  
  
SRC漏洞挖掘课程培训已经两个星期了，期间也是创建了  
“回本小群”，希望学员回本越来越多，创建这个群主要是鼓励学员学习进步，以及不定时发小项目！  
  
最后也是希望大家都可以赚钱，找到好工作🎉  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QXxg5ZD0UshZs8zQRSETzhKibZ6UfWhZk64QtTgIevAUZxl5pwhVPiaL6DFyN4priaTh8x8Uhjh2KD6Brtkn1dOdia0kiaAicgjPV5ib0/640?wx_fmt=png&from=appmsg "")  
  
培训时间不长，感谢🙏师傅们的  
喜报  
，很开心看到师傅们给我分享自己的成果，  
希望师傅们越来越强！  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QUMAtEWv3xXZPDsGBRhESmwGRciaasCGibU8TtbP2U0YVZPBdf5tlLqpWAtQKBh5oFwgETyvicKBeW1JSsekAyJ5cbRlSdjooQkSM/640?wx_fmt=other&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=22 "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QUrS4N68nZ0EyE76Wkib7ZDrpnZWw2Q1RJQvFEdIOu5XvFGCwpz9lziabKyo9C9d5ZiamibuSXlibhXLHb7b8QJhqEIs3hXvqktkkyA/640?wx_fmt=png&from=appmsg "")  
  
  
平常也会分享项目，下面是一些  
学员项目成果  
，群里报课的学员都是不抽成的，主要是帮助学员进行  
回本  
，  
让大家都可以进步！  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QWS8lR4pmzZrczwr6YjtG48EqF9q4FlAUH78M7DXkiboqF8Q1HkeWJLzpFPOQBToO3auj8r4rU9x3fuafXUDVMEcFj5EI6U3P9w/640?wx_fmt=other&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=23 "")  
  
上课结束后，会把  
视频录播+课件笔记  
一起打包发直播群  
  
**「神农安全」**  
知识星球目前已经  
累计2500+网络安全爱好者的加入！  
  
后面也是小圈子做大起来了，师傅们也都喜欢看我文章，想着给大家教下src漏洞挖掘思路，所以自己花了很长时间做了✨  
课件和课表，都是纯自己手搓的，大家也可以看下课表的内容。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/b7iaH1LtiaKWXNmpV89Zxcm1J56eeHltthM2sjuWQFbmvWv79V058KwI0DswFF9LysewGtULj81Vp5bX9nTEK78A/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QVhliaOc71FnQLZjEUB2QiavqaRdiaaAN25Gb1HNADIy0cYvIIHC46za7Ab6sibRKvKG2tbJBxqrOGyczqWF44LQOKllnZXE6PU5iaE/640?wx_fmt=png&from=appmsg "")  
  
03  
  
0x3 课程特色  
  
课程  
主打真实，  
一线SRC漏洞挖掘师傅是如何学习和挖掘SRC漏洞的，让你真正了解SRC漏洞挖掘，助力在岗人员和大学生的能力提升，掌握新的技能树，为下一次  
跳槽涨薪做好准备。本  
课程内容覆盖企业  
SRC、众测项目挖掘、护网HVV红蓝攻防技巧、CVE、CNVD、EDUSRC等平台通杀案例技巧挖掘方法。  
  
本课程  
适合人群  
（光看不挖啥也不会）  
```
1、有计算机经验，想从0转行入行的大学生或自学者
2、想从CTF比赛/Web或SRC进阶到项目实战的选手
3、想参与项目/找工作/提高收入的转型者
4、想通过挖SRC赏金做副业的师傅们
5、挖SRC漏洞遇到瓶颈的师傅们
6、想学习AI安全自动化渗透测试漏洞挖掘的师傅
```  
  
课程价格：575 元  
  
报课成功的师傅们直接免费送内部小圈：一个知识星球+内部小圈子交流群  
```
1、课程价格真心实惠，绝不割韭菜
2、四五百的课程价格让你体会大几千的培训课程内容
3、带着大家从0到1，本人上课坚持手搓课件（实战案例+知识体系）
4、拒绝使用PPT演讲模式（无实操，很枯燥）
```  
  
直播培训教学方式  
  
课程  
一周1-2节课，课程特色涵盖直播多人上麦活跃回答，直播过程中有问题随时解决或私信我。  
拉群：一个知识星球内部小圈子交流群+课程培训直播通知群。有项目/工作/护网第一时间内推报课的师傅，  
一对一简历优化，助力在岗人员和大学生的能力提升。  
  
一次报名每期均可永久学习，并且赠送内部「神农安全」知识星球，一对一永久解答、无保留教学！  
  
欢迎关注公众号：  
神农Sec  
，报名咨询添加VX：  
routing_love  
  
课程均为线上交付，报名成功后  
不支持退款  
  
内部小圈子  
（知识星球+内部小圈子交流群+知识库）  
  
对内部小圈子感兴趣的师傅们也可以看下下面的这个  
跳转链接，里面有对小圈子的详细介绍，报名课程成功的师傅们直接免费送一个（直接点击下面直接可以跳转）。  
  
[强烈推荐一个永久的SRC挖掘、渗透攻防内部知](https://mp.weixin.qq.com/s?__biz=Mzk0Mzc1MTI2Nw==&mid=2247508882&idx=1&sn=0ca5ab133a5b589e26e25de14882b28f&scene=21#wechat_redirect)  
  
[‍](https://mp.weixin.qq.com/s?__biz=Mzk0Mzc1MTI2Nw==&mid=2247508882&idx=1&sn=0ca5ab133a5b589e26e25de14882b28f&scene=21#wechat_redirect)  
  
[识库](https://mp.weixin.qq.com/s?__biz=Mzk0Mzc1MTI2Nw==&mid=2247508882&idx=1&sn=0ca5ab133a5b589e26e25de14882b28f&scene=21#wechat_redirect)  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QVRzhiawbmNicgOFicLKeMZPtpyqtP9M0IA7gJZPerY1pI0P1Owcs0ttibWiaw87asg3qibyVF9NEVeGuxL3YqASaQhUn3pUBjicpMTPM/640?wx_fmt=png&from=appmsg "")  
  
讲师介绍  
  
id：一个想当文人的黑客  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QX6UX8mhQtia4qnfEiasbq2R3KjlwQg2ysg4ibj744R4DF0BXZQZBjHc3qNgPKkqG7msub5w6WjSmoElCibibTp6qImS3FkupITqJUk/640?wx_fmt=png&from=appmsg "")  
  
欢迎关注公众号：神农Sec，报名咨询添加VX：  
routing_love  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b7iaH1LtiaKWXLicr9MthUBGib1nvDibDT4r6iaK4cQvn56iako5nUwJ9MGiaXFdhNMurGdFLqbD9Rs3QxGrHTAsWKmc1w/640?wx_fmt=jpeg&from=appmsg "")  
  
04  
  
0x4 第一期挖洞培训课表内容  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QXdFkU8hwaxia8XQ7EyshqMb1BUOknbNI4lhtliaE0iakNZ0PRmjBUocUGbGDmEaGwuZDDP4sXkOrjicxI1exTafD6wdNUTj66wCWw/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/MVPvEL7Qg0F0PmZricIVE4aZnhtO9Ap086iau0Y0jfCXicYKq3CCX9qSib3Xlb2CWzYLOn4icaWruKmYMvqSgk1I0Aw/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1 "")  
  
**内部圈子介绍（报课赠送）**  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/MVPvEL7Qg0F0PmZricIVE4aZnhtO9Ap08Z60FsVfKEBeQVmcSg1YS1uop1o9V1uibicy1tXCD6tMvzTjeGt34qr3g/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1 "")  
  
  
  
  
**圈子专注于更新src/红蓝攻防相关：**  
  
```
1、维护更新src专项漏洞知识库，包含原理、挖掘技巧、实战案例
2、知识星球专属微信“小圈子交流群”
3、微信小群一起挖洞
4、内部团队专属EDUSRC证书站漏洞报告
5、分享src优质视频课程（企业src/EDUSRC/红蓝队攻防）
6、分享src挖掘技巧tips
7、不定期有众测、渗透测试项目（一起挣钱）
8、不定期有工作招聘内推（工作/护网内推）
9、送全国职业技能大赛环境+WP解析（比赛拿奖）
10、十个专栏会持续更新~提前续费有优惠，好用不贵很实惠
11、每日内部资料分享，内部圈子资料1000+
12、联系圈主获取：内部漏洞知识库+圈子使用手册+内部圈子交流群
13、VX：routing_love，技术交流+疑问解决
```  
  
  
**内部圈子**  
**专栏介绍**  
  
知识星球内部共享资料截屏详情如下  
  
（只要没有特殊情况，每天都保持更新）  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/b7iaH1LtiaKWWYcoLuuFqXztiaw8CzfxpMibgpeLSDuggy2U7TJWF3h7Af8JibBG0jA5fIyaYNUa2ODeG1r5DoOibAXA/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/b7iaH1LtiaKWUw2r3biacicUOicXUZHWj2FgFxYMxoc1ViciafayxiaK0Z26g1kfbVDybCO8R88lqYQvOiaFgQ8fjOJEjxA/640?wx_fmt=png&from=appmsg "")  
  
  
05  
  
0x5   
优秀学员报喜  
  
下面是最近两个月培训期间，很多  
优秀学员进行报喜，看到师傅们有收获，也是感到很开心的！  
拉回本小群，就是为了促进大家学习，在群里发学员成果，也是为了让大家学习优秀的师傅们。  
  
加油，你我皆是黑马！  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QWS8lR4pmzZrczwr6YjtG48EqF9q4FlAUH78M7DXkiboqF8Q1HkeWJLzpFPOQBToO3auj8r4rU9x3fuafXUDVMEcFj5EI6U3P9w/640?wx_fmt=other&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=23 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QWkBFHe0S1MayHGboNyYGhNR94Fic11frXxdUGBgjjIx6dnJ6lgWxw7iajkmFTiczQq5DHN1bwUchcVzatv95E5gibAMUiaZ7fHlypw/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QU6ibZutPq43zUiap7IgDmJq7kwUKJBCa2IDujYiadMJfe9fFH9DOfUEOM2TibibYRuFiahDqMnBX1MVjLw5XIdNDSuR5P3g7XibaUkBo/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QVz2wFpVfer0uAFVpLKyicMaaLkmJDdg5bWnOotuzN3S9r2FMKpEKrJy8ND7icWVzNgqyYS2J6XElVN43vGca4X6HcEqapwGcNX0/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QWoKYrxQzob221aCmicmemD3aVPbk5dvVIaEic4TNPXrnkRazOTHnIbq87Jbk0GREdlI4iaZUmVU3c6K6rBbyZzBnkicooOUVtzN1I/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QWdSycCicsfpn14HlUgEibU04lpXJ4a70L4D6oSWx5s0tLgnLnLiaRAdclxNVicYKFRD1mGn40jQ4t4ic8XZzVoOSTTCzY9xz9auzJI/640?from=appmsg&wxfrom=12&wx_fmt=other&tp=webp&usePicPrefetch=1&watermark=1 "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QVRqR4bI22pibTSVQVSibImicNHOCUGQAaUlo9lZsNJicLmcTaQKl662ulqoX54EmbCDKUD3ibibdZxqKaOJAcoyIV7cAn7tKqia8S470/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QXB1O6Fx3ia62NNWITh9vUQaEKp7epibLWeEsdobibvBvqNDoTCAvfyQFHw597O24naJAIpM4QALgfqMWWc4E1KHrxBoaGRxE4Ajc/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QXnjwIRWjJOVSuN4X4HjmEFtCVqCHZ05M77sXqzmVjibaJbLUw3ApOuz7iaH8OCCnRmTRYVtKC5NajGKVkI4pnKZsJaj0T4iaYibq0/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QWRdtEUY2aeAZh34wDle515j7UwnibFQeCibWSeDKGnIZ2YH5VGX64cYeXgPGdCwHLKdsMY07EIVliapxh10gzQ2EO3bks7bxhmVs/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QW7rozFqSBVNRDE2kbfUSB4FefOPm9LXM2B9bV4n9VPM7Kt11rfw284Ejn4AHUc1Uc1r1gZs3FF6umgPk1QejcC6zrOAYEyegY/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QV0epFsicbJrNPgGwNXcXRCDrC1sGQySI2ylkfs2Hdic6d6unjwqNiby5DfhtfT6ezabX13bNeR53pOW3BUqLaZrIvPM8Z4IsBpgI/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QXwfrgic5XLseOxkPOWkjm1yicAW2ZiaAqzxtbjPok4Yhic2Wiblic93SSGN5BtT77AFuZt6ySuRL09icqIicPuOUUbL5NbWgMCHgLictiak/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/mcko8AHj6QU7jGMRbvGyeDmHE6KjibHDNmuqO2NDaG4soIjTtg6uQoy4H5x0FntPDicjnUtibVgFMTNvNRaA9SJicNj2BIWQNz2vfRaQDfkKsibI/640?wx_fmt=jpeg "")  
  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QUyyXAWlMf8dHcspnMDucUzRBbeXWW58yMWQncvENPDmIpEKr4HlZ0cyWZSLGiakB03zRmvicfIF79LdWQ6s6VOyxl44WgvMwzf4/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QX83LJfPytAENC2dGNwhg0pFd7as7FhJZum31EJkbnicO88ZNIwXflHjpsuQV3I0BQIRkNzbVy2nkhMicice6QDO6gMkdg5RLiboiaA/640?wx_fmt=png&from=appmsg "")  
  
  
  
**神农安全公开交流群**  
  
有需要的师傅们直接扫描文章二维码加入，然后要是后面群聊二维码扫描加入不了的师傅们，直接扫描文章开头的二维码加我（备注加群）  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/mcko8AHj6QX3UwWpoejXtX2TADSwiax2gFhVyKp0VTcVckC0lISUCBO3tVU90PHIqrpc2dSEwdEaUvticZOwy9HpLqiaetDkgUTnSfrAJU9ImQ/640?wx_fmt=jpeg&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/mcko8AHj6QV9grs7NOhSTCfTpCc4xrxdnlISIReNNCKR2EOyWvhMpyIzbma8nuelSg8LicKF5yYZ7hgyODlWgMmhViaE8Ahhs7PZlnmA0VFcY/640?wx_fmt=jpeg&from=appmsg "")  
```
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/b7iaH1LtiaKWW8vxK39q53Q3oictKW3VAXz4Qht144X0wjJcOMqPwhnh3ptlbTtxDvNMF8NJA6XbDcljZBsibalsVQ/640?wx_fmt=gif "")  
  
  

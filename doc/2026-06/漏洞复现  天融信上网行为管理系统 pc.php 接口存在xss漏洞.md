#  漏洞复现 | 天融信上网行为管理系统 pc.php 接口存在xss漏洞  
 实战安全研究   2026-06-28 01:00  
  
**免责声明**  
<table><tbody><tr style="-webkit-tap-highlight-color: transparent;outline: 0px;visibility: visible;"><td valign="top" style="-webkit-tap-highlight-color: transparent;outline: 0px;word-break: break-all;hyphens: auto;visibility: visible;"><span style="color: rgb(255, 0, 0);letter-spacing: 0.544px;-webkit-tap-highlight-color: rgba(0, 0, 0, 0);outline: 0px;visibility: visible;font-size: 14px;"><span leaf="">本文仅用于技术学习和安全研究，请勿使用本文所提供的内容及相关技术从事非法活动，由于传播和利用此文所提供的内容或工具而造成任何直接或间接的损失后果，均由使用者本人承担，所产生一切不良后果与文章作者及本账号无关。如内容有争议或侵权，请私信我们！我们会立即删除并致歉。谢谢！</span></span></td></tr></tbody></table>  
1  
  
**漏洞描述**  
  
  
  
天融信上网行为管理系统 pc.php 接口存在xss漏洞，攻击者可通过构造恶意参数，在页面中注入 JavaScript 代码，当用户访问恶意链接后，可能导致Cookie 窃取、会话劫持、页面篡改、钓鱼跳转、后台权限进一步利用，若后台管理员访问恶意链接，风险将进一步扩大。  
  
2  
  
**影响版本**  
  
  
  
   
天融信上网行为管理系统  
  
3  
  
**测绘语法**  
  
  
  
fofa语法  
```
body="ActiveXObject" && body="dkey_login" && body="repeat-x"
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/yIciaKAicYtoorniad6GJxic0Goa2JFNxOxWytRRkJQTZNESakuItVyy2dZoYEIVkjy1gWib0cicdqib59NDN6D1B1OXHTqGR1qq8Oase0Qo16eHn0/640?wx_fmt=png&from=appmsg "")  
  
  
4  
  
**漏洞复现**  
  
  
  
访问地址  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/yIciaKAicYtopo5fibzWQPjfm1aYZNEA2zG3Gb2hzHePriazfcQKHqsuBlFfOFNCVuXsC6OZtic2dSG2JLWgUUj9Ge52icBgia2tYDsSZmGKHvWQIo/640?wx_fmt=png&from=appmsg "")  
  
  
5  
  
**检测POC**  
  
  
  
nuclei  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/yIciaKAicYtoqibr5x3ttKzricsY0jWbKtIUTGic3ZzHdvxOF4hkYzvnpzEeueibCf0v28earPvwWa94MQCPoTN0d7L6G9rGRCdaU0OY181NjP8iaw/640?wx_fmt=png&from=appmsg "")  
  
afrog  
  
![](https://mmbiz.qpic.cn/mmbiz_png/yIciaKAicYtorcpTcqHNrl02HA3qxeZLlYna94dNZVlSa0cdoQFHjDtakYqycNxybN0f3CgOsv27TsLicS4BvXm5FI8Ds6ZqXZcV1UhE6X4lW4/640?wx_fmt=png&from=appmsg "")  
  
  
6  
  
**漏洞修复**  
  
  
1、建议联系厂商打补丁或升级版本。  
  
2、增加Web应用防火墙防护。  
  
3、关闭互联网暴露面或接口设置访问权限。  
  
7  
  
**内部圈子**  
  
  
**现在已更新POC数量 2400+（中危以上）**  
  
🔥 **1day/Nday 漏洞实战圈上线**  
 🔥  
  
还在到处找公开漏洞 POC？  
  
这里专注整合全网公开1day/Nday漏洞POC和复现，一站式解决你的痛点！  
  
🔍   
圈子福利  
  
✅ 整合全网 1day/Nday 漏洞POC，附带复现步骤，新手也能快速上手  
  
✅ 每周更新 7-15 个POC测试脚本，经过实测验证，到手就能用  
  
✅ 完美适配 Nuclei/Afrog 扫描工具，脚本无需额外修改，即拿即用  
  
✅ 重磅福利：免费 FOFA 高级会员查询，无需账号也能高效资产测绘  
  
✅ 专属权益：提供指纹识别库，指纹库持续更新  
  
💡   
适合对象  
  
渗透测试🔹攻防演练🔹安全运维🔹企业自查  
🔹SRC漏洞挖掘  
  
⚠️   
重要提醒  
  
仅限授权范围内的合法安全测试，严禁用于未授权攻击行为！  
  
本服务为虚拟资源服务，一经购买概不退款，请按需谨慎购买！  
  
目前圈子已满200人，价格由66.9调整为69.9元（  
交个朋友啦  
），250人后调整为71.9元。  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/yIciaKAicYtooO0hSFToW8oqP3icIqkEK5L3d7bSVm5sBjr45laiciaAjqDjGGYvR2mOX6jAkDS4QL7zHoPm54WZxYdayRy2eJdT16SAQ9yV0pw8/640?wx_fmt=jpeg&from=appmsg "")  
  
  

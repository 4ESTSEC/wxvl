#  某次内部行业渗透测试&攻防演练多个系统从资产打点到RCE漏洞  
原创 神农Sec
                        神农Sec  神农Sec   2026-09-28 01:00  
  
  课程培训  
  
  扫码咨询  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b7iaH1LtiaKWXLicr9MthUBGib1nvDibDT4r6iaK4cQvn56iako5nUwJ9MGiaXFdhNMurGdFLqbD9Rs3QxGrHTAsWKmc1w/640?wx_fmt=jpeg&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/b96CibCt70iaaJcib7FH02wTKvoHALAMw4fchVnBLMw4kTQ7B9oUy0RGfiacu34QEZgDpfia0sVmWrHcDZCV1Na5wDQ/640?wx_fmt=png&wxfrom=13&wx_lazy=1&wx_co=1&tp=wxpic "")  
  
  
#   
  
专注于SRC漏洞挖掘、红蓝对抗、渗透测试、代码审计JS逆向，CNVD和EDUSRC漏洞挖掘，以及工具分享、前沿信息分享、POC、EXP分享。不定期分享各种好玩的项目及好用的工具，欢迎关注。加内部圈子，文末有彩蛋（课程培训限时优惠）。  
#   
  
  
01  
  
0x1 某次内部行业渗透测试&攻防演练多个系统从资产打点到RCE漏洞  
  
## 0x1 前言  
  
首先声明下，本次文章分享是有渗透测试授权的，且下面的漏洞都已经被修复，文章相关图片都已经被打码了，不做任何未授权、有危害的渗透测试、漏洞挖掘、攻防行为。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QWiaWuWAdBdkPJ1OJJmpbI6JIAKlw8R7hJdCzSWuADIj9pdUtXw4K82k0icK9t7MCBHeYQNZrib9nHyvAAAoEVbeyIdkibI4Z3Lt4E/640?wx_fmt=png&from=appmsg "")  
  
周期时间是一个星期，总共提交的有效漏洞报告是18个，前几天是先进行内部的渗透测试操作，后面几天就是内部模拟攻防演练行动，最后一天就是内部钓鱼演练。  
  
这次文章主要是开始给师傅们分享目标指定的资产如何进行信息收集以及收集到对应的资产，如何快速有效的进行一个打点操作，写的都是我自己的实战经验，欢迎师傅们来交流，有些地方写的不足，希望师傅们可以补充。  
  
后面给师傅们分享的是几个案例，很多漏洞报告太明显了，就没有分享，拿几个典型的给师傅们分享即可，希望师傅们都有收获！  
  
![img](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QVqx6jg2Yicib2Qa9tH1reh5jOYk0xs9Dicv8O7SibZN5TRYaoyQJql5nA9ZAZFjBEo8YmSx0bVOdrNiczhhe8fKiaIq68qVvw58Sgsw/640?wx_fmt=png&from=appmsg "")  
  
img  
## 0x2 攻防演练的简介和注意事项  
### 一、什么是攻防演练  
  
攻防演练是一种模拟真实攻击和防御的活动，旨在评估和提高组织的安全防护能力。在攻防演练中，一个团队（红队）扮演攻击者的角色，试图发起各种攻击，而另一个团队（蓝队）则扮演防御者的角色，负责检测、阻止和应对攻击。  
### 二、攻防演练的步骤  
- **规划和准备：**  
确定演练的目标、范围和规则，制定攻击方案和防御策略，并准备相应的工具和环境。  
  
- **攻击模拟：**  
红队使用各种攻击技术和工具，模拟真实攻击，如网络渗透、社会工程、恶意软件传播等，以测试组织的安全防护措施和响应能力。  
  
- **防御检测：**  
蓝队负责监测和检测红队的攻击行为，使用安全监控工具和技术，如入侵检测系统（IDS）、入侵防御系统（IPS）、日志分析等，及时发现和报告攻击。  
  
- **攻防对抗：**  
红队和蓝队之间进行攻防对抗，红队试图绕过蓝队的防御措施，而蓝队则尽力阻止和应对攻击，修复漏洞，提高安全防护能力。  
  
- **分析和总结：**  
演练结束后，对攻击和防御过程进行分析和总结，评估组织的安全弱点和改进空间，制定相应的安全改进计划。  
  
通过攻防演练，用户可以发现和修复安全漏洞，提高安全防护能力，增强对真实攻击的应对能力，同时也可以培养和训练安全团队的技能和经验。攻防演练是一种有效的安全评估和提升手段，有助于保护组织的信息资产和业务安全。  
### 三、攻防演练常见丢分项有哪些？得分项有哪些？  
#### 常见的丢分项  
1. 漏洞未修复：如果目标系统存在已知漏洞，但未及时修复，红队可以利用这些漏洞进行攻击，导致丢分。  
  
1. 弱密码和默认凭证：如果目标系统使用弱密码或者默认凭证，红队可以轻易地获取系统访问权限，导致丢分。  
  
1. 安全配置不当：如果目标系统的安全配置不当，例如开放了不必要的服务或者权限设置不正确，红队可以利用这些漏洞进行攻击，导致丢分。  
  
1. 未发现攻击行为：如果蓝队未能及时发现红队的攻击行为，或者未能有效地监测和检测到攻击，导致丢分。  
  
1. 未能及时响应和阻止攻击：如果蓝队未能及时响应红队的攻击行为，未能采取有效的防御措施，导致攻击成功或者造成严重影响，会丢分。  
  
#### 常见的得分项  
1. 漏洞修复和安全补丁：如果目标系统及时修复了已知漏洞，并安装了最新的安全补丁，可以得分。  
  
1. 强密码和凭证管理：如果目标系统使用强密码，并且凭证管理得当，可以得分。  
  
1. 安全配置和权限控制：如果目标系统的安全配置正确，并且权限控制合理，可以得分。  
  
1. 发现和报告攻击行为：如果蓝队能够及时发现红队的攻击行为，并及时报告，可以得分。  
  
1. 有效的防御和响应措施：如果蓝队能够采取有效的防御措施，及时响应和阻止攻击，可以得分。  
  
得分项和丢分项的具体评判标准可能因不同的攻防演练规则和目标而有所不同。  
  
![img](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QWVLNUqTVIS4OFsHKCDI1yU8A5OLkzTopwJ1eq2X6p1pV5EaVmXGib30O4KiaQekjox2tdvhlicyw2gOtrj5FSOjfW2WuD1xibTjaM/640?wx_fmt=png&from=appmsg "")  
  
img  
## 0x3 指定资产收集&打点思路  
### 一、信息收集/资产收集  
  
改站点的首页如下，是个公司官网，拿到这样域名，直接去信息收集一波  
  
![img](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QU1W2VRcbclXibkYaHYx1sD121xmEU5BEbuaQvpGdeV6OvOfdEn8H7X1Q1tXrFEerEnPiaIYyE9IQ9NdkMib007Igo8Xu9dXbqZR4/640?wx_fmt=png&from=appmsg "")  
  
img  
  
这里先推荐去使用爱企查和风鸟这两个公司资产收集的网站，其中爱企查很多功能是收费的，但是收集的资产多，风鸟是免费的，两个一起使用效果好点  
  
![img](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QVnOvweqkwicELibDWNJ0Z17MKicyfpVAAxiaZNMNnwhqXMt53YgOyBsB1c0kw8UUumuKAabjVHO1e4cWv8fNmxdPjxEfunmPh7pnI/640?wx_fmt=png&from=appmsg "")  
  
img  
  
凤鸟还可以搜索相关的APP资产  
  
![img](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QXhlQ5eicibN48jpiakThLgJaLwUxm9pRJhmqKJn8dLGtfF2Xianq9busSIicOKC2TFmlgjlq8a0ltja1LGJlOcHmPpV0ycsXTbTT5I/640?wx_fmt=png&from=appmsg "")  
  
img  
  
首先拿公司名称到ICP/IP地址/域名信息备案管理系统搜索备案号以及备案的域名网站以及小程序、app等  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QUiaXuH2xOsh7IpPibqgKxDTYDnZCOs1293OAQ2WO8gB7Sriax7c29beqAJS9SLv2wtcTWKYVKepIickeY35sbEufZicjzb5xecs76w/640?wx_fmt=png&from=appmsg "")  
  
然后拿到备案号去使用搜索引擎搜索，再导出域名  
  
![img](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QVPs1hkx4Srs060DSxQyibGSE8mdueVvuaAMEWXCXPlUqEEa21XxMiaicXssNCaVIyyA35yibLIk8nwciadoen5tSNYPduJVZ8JQibDg/640?wx_fmt=png&from=appmsg "")  
  
img  
  
![img](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QVHFqA46d9CZU5hdt1NHJknBCEzZtlI0Z24l4gtIWazwv1NdFHZGc5KO3OryQUSiaOKV22U1KoJ61tEiblGRZ6Iz4KrhJKIpCRc8/640?wx_fmt=png&from=appmsg "")  
  
img  
  
建议使用类型无影的相关工具搜索引擎的功能，然后一起导出，这样就可以一次性使用多个搜索引擎，且快速导出域名资产  
  
![img](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QUsRUzfzD2mOv0cuicPre72jnxNA9tBbX5vWvibnMQIXd9C99qKWibSgWMkg0Su2e5kCpMicctMglicDysVff5hPaB2pia0pzfD1EX7Y/640?wx_fmt=png&from=appmsg "")  
  
img  
  
就可以导出相关的域名了，且收集的资产要全  
  
![img](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QUZiaKFVKlDRrf01tWBDEbwTeQVOnW01J784SAZibMm52ibiaL4qtc3Q6tnWnP0ZeyXzR7YPquPnYsugXw7S9AGpMQtq3CfRjuFWBQ/640?wx_fmt=png&from=appmsg "")  
  
img  
  
最后面还可以使用ENscan这个工具去爬取网络上面的这个公司相关的资产信息，然后导出给我们  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QWspOselF1w2iat1oOd3jWyicgEbWZN3QYjNoKRjusqV4u1oWMpepaxp8aKc9uUAeCRnPGeNzcWKvQNhaxrBfQnO55T5EHo4W2vk/640?wx_fmt=png&from=appmsg "")  
  
跑出来的相关资产数据，我们就可以进行整理，然后与上面收集到的站点域名，进行汇总  
  
![img](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QVUJUFxwRMuM1MZFKYUib6mvyH936akmapN31fl2YBcG2bcutSRt1HibhKvvbicUhaia9vqt0Mt17ibZUlM29G6DaryYAf5Z9afnDFI/640?wx_fmt=png&from=appmsg "")  
  
img  
### 二、子域名/IP网段收集  
  
子域名我常用的就是灯塔ARL+oneforall+空间引擎三种方式一起收集，利用上面的方式收集主域名相关资产，放到灯塔ARL去跑  
  
![img](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QXKL5t9H1Sn4gvv2BxPxw7tRZby54OID3DrShMTH960EcAKAQPqPxY09kXaBsicRcyJvric6rDfD2jtC2iaYYnqCeqSeb7wxbq7oM/640?wx_fmt=png&from=appmsg "")  
  
img  
  
然后灯塔扫描完成，就可以使用自带的导出功能进行选择数据的导出即可  
  
![img](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QVEQZnZZYwtiaU38bE1jBV1BOApWWbndy3UoC72eQcxtuRfa6gcRt2DFRCHtT9FlpyAd7ppMr5801XkVv5FvDqIKgOHYOibjsIn8/640?wx_fmt=png&from=appmsg "")  
  
img  
  
把上面保存的主域名，放到oneforall里面去跑，收集对应的子域名即可  
  
![img](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QUPZw1lAYM1dERMjWlOhp2hjDnTUSQmf4I4APFsK1VicdDNZicI8w5AEFCV52yxAppJCiaCuLWXFSVKFy05kviaoURmwBmyUeZB3aE/640?wx_fmt=png&from=appmsg "")  
  
img  
  
再利用上面的空间引擎搜索到的子域名，然后用收集到的数据进行去重  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QVGreJH5LIJh2dtJqiccfkSX3owLrb50gU8ZzplcR2TlibCSWyqPd2EcmHexnFFI7RTTzpg7vRjibWXQ5sngjuQx6CDowXibsjRb08/640?wx_fmt=png&from=appmsg "")  
  
IP网段的话，推荐把上面工具手法收集到的IP资产，放到无影工具上面去资产整理即可得到IP网段  
  
![img](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QUdGNFEmgAEv9mIuPhicAVfJiaayG5nANCvRsaEslrFm6CbUm76lVLiaJk0CzpBaeSfnabia4eDgVKMkicHMLqmpdYicSzysCazP1m0s/640?wx_fmt=png&from=appmsg "")  
  
img  
  
比如举例下面这个IP段，就可以这样去使用FOFA搜索，找到更多的存活域名资产  
```
ip="69.197.157.0/24" && is_domain=true
ip="69.197.157.1/20"

```  
  
![img](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QUC7aAiaz1mWBGnaF9RuZyicTdYn88pkdLlaAKsgx7E2KRjM5ymwbN9nEGksF9Aoy0IjvSGJKEejCNlnWG5SvR3vAIwiaqz1Wic2UY/640?wx_fmt=png&from=appmsg "")  
  
img  
### 三、资产打点  
  
这里推荐使用无影的web指纹识别，这个功能可以把我们导入的域名、IP直接进行探活和指纹检测操作  
  
![img](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QVNbPYo5ibtfdAdmReice5LdcmcqxWr7tl0hTG4icXpL31Dn0sX3DY785kibibluBxibQVocUJGYcRUib9grDxRafUH1rxFNPlb4XeE00/640?wx_fmt=png&from=appmsg "")  
  
img  
  
这里打点很快的一个方式，就是使用灯塔扫描敏感文件泄露，这个师傅们可以先去网上搜索下修改配置文件，也就是修改里面的默认敏感文件，可以把自己收藏的大字典放上去，效果更好，可以扫描到一些敏感文件出来，具体师傅们可以参考这篇文章：  
  
https://blog.csdn.net/mashiro_hibiki/article/details/138245669，具体就是修改/app/dicts/目录下的file_top_2000.txt文件即可，下面就是我的字典文件，对应的修改下灯塔ARL的配置文件即可  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QVsr7kFlB8KTM4RHcf4btb9qxt5n4z9mjUnjibGBPcTS7lWjqDB2RibicPEnWLpWX8KsbSNHqPPX3sZKINIujEicLyzweQUpTQua28/640?wx_fmt=png&from=appmsg "")  
  
如果师傅们没有安装灯塔的话，推荐师傅们看下地图大师的B站课程，安装灯塔的操作很简单，然后再进行配置相关操作，也就是上面的CSDN的博客地址即可  
```
【【地图大师src漏洞挖掘番外篇】ARL资产灯塔自救指南】 https://www.bilibili.com/video/BV14W421R77j/?share_source=copy_web&vd_source=268f8d699ac32cf11e9bdc248399c5bd

```  
  
![img](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QWEROhwzH6gv60ZiaeuRRXNTKxSO7HGO0DmkeictWmg2w7V26TyHMKwc0tbo4cP62Y4a3mnBLD9GiabOPchX3ur4tED7TWdCv0kU4/640?wx_fmt=png&from=appmsg "")  
  
img  
  
等灯塔ARL跑完，就可以在文件泄露模块去看了  
  
![img](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QVFsoJKhpIbMWyEJr7Ork3nfvYlWLdYbicARibRkq58iaEIQNccwDlIa1Dib3PaYKR8TVSuXLvXbH1nQtAu9hB4E6NOYqiaP2Nrpplc/640?wx_fmt=png&from=appmsg "")  
  
img  
  
其次还有就是我一般喜欢在打开无影探测完的资产后，要是这个域名访问报错，或者没什么信息，我会使用Google语法来访问，有时候可以找到该站点的存活站点，从而打该站点的资产  
  
就比如你访问http://xxxxx.xxx.com这个站点，显示下面的界面报错，一般别人遇到就放弃了  
  
![img](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QUucBsG3HXNN4KhPrl9NtgCcX2RaAgv5MDU6xNFwafvpLrTlzYpbI3QGEVas4lQWBp0rIsSaicF7xrlT0B9EcTRCIMqZOz90OSw/640?wx_fmt=png&from=appmsg "")  
  
img  
  
我们可以使用Google浏览器用site:域名尝试，有时候可以找到对应站点的别的访问路径，就不会报错了  
  
![img](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QWJxsIOSHAcfVZ5FWITWN6oBiaz58nyy7sg3qBibnQXgRdEnzJ1I5O8fApBibhslwJR0D05UmiaMNl0YDbeaEBU722uibag61MBibeno/640?wx_fmt=png&from=appmsg "")  
  
img  
  
![img](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QX787IUbfA0Xhoo2T3YWkqPCjnu0plLRfLn6BNqKuwyBWWS9m3YdfXTiahFwrWib59PsiaJZElXZliaY1rxDoAaZ7kGibaJcehZ7icmQ/640?wx_fmt=png&from=appmsg "")  
  
img  
  
下面给师傅们分享我收藏的一些Google语法：  
```
注入漏洞:
site:edu.cn inurl:id|aspx|jsp|php|asp

文件上传：
site:edu.cn inurl:file|load|editor|Files

前台登录：
site:edu.cn intext:管理|后台|登陆|用户名|密码|验证码|系统|帐号|手册|admin|login|sys|managetem|password|username

site:edu.cn inurl:login|admin|manage|manager|admin_login|login_admin|system|boss|master

敏感信息搜索:
site:edu.cn ( "默认密码" OR "学号" OR "工号")

后台接口和敏感信息探测:
site:edu.cn (inurl:login OR inurl:admin OR inurl:index OR inurl:登录) OR (inurl:config | inurl:env | inurl:setting | inurl:backup | inurl:admin | inurl:php)

查找暴露的特殊文件:
site:edu.cn filetype:txt OR filetype:xls OR filetype:xlsx OR filetype:doc OR filetype:docx OR filetype:pdf

常见的敏感文件扩展:
site:edu.cn ext:log | ext:txt | ext:conf | ext:cnf | ext:ini | ext:env | ext:sh | ext:bak | ext:backup | ext:swp | ext:old | ext:~ | ext:git | ext:svn | ext:htpasswd | ext:htaccess

XSS 漏洞倾向参数:
inurl:q= | inurl:s= | inurl:search= | inurl:query= | inurl:keyword= | inurl:lang= inurl:& site:edu.cn

重定向漏洞倾向参数:
inurl:url= | inurl:return= | inurl:next= | inurl:redirect= | inurl:redir= | inurl:ret= | inurl:r2= | inurl:page= inurl:& inurl:http site:edu.cn

SQL 注入倾向参数:
inurl:id= | inurl:pid= | inurl:category= | inurl:cat= | inurl:action= | inurl:sid= | inurl:dir= inurl:& site:edu.cn

SSRF 漏洞倾向参数:
inurl:http | inurl:url= | inurl:path= | inurl:dest= | inurl:html= | inurl:data= | inurl:domain= | inurl:page= inurl:& site:edu.cn

本地文件包含（LFI）倾向参数:
inurl:include | inurl:dir | inurl:detail= | inurl:file= | inurl:folder= | inurl:inc= | inurl:locate= | inurl:doc= | inurl:conf= inurl:& site:edu.cn

远程命令执行（RCE）倾向参数:
inurl:cmd | inurl:exec= | inurl:query= | inurl:code= | inurl:do= | inurl:run= | inurl:read= | inurl:ping= inurl:& site:edu.cn

敏感参数:
inurl:email= | inurl:phone= | inurl:password= | inurl:secret= inurl:& site:edu.cn

API 文档:
inurl:apidocs | inurl:api-docs | inurl:swagger | inurl:api-explorer site:edu.cn

代码泄露:
site:pastebin.com edu.cn

云存储:
site:s3.amazonaws.com edu.cn

JFrog Artifactory:
site:jfrog.io edu.cn

Firebase:
site:firebaseio.com edu.cn

文件上传端点:
site:edu.cn "choose file"

漏洞赏金和漏洞披露程序:
"submit vulnerability report" | "powered by bugcrowd" | "powered by hackerone" site:*/security.txt "bounty"

暴露的 Apache 服务器状态:
site:*/server-status apache

WordPress:
inurl:/wp-admin/admin-ajax.php

```  
  
还有就是遇到站点打不进去，可以了解下JS相关打法，针对于自动化JS渗透测试的操作，这里我推荐师傅们使用转子这款工具，我个人觉得很强，收集JS信息非常详细，而且会进行自动分类漏洞详情，缺点就是只能在windows版本上面运行，所以要是别的操作系统的师傅们可以使用虚拟机试试。  
  
使用的话，直接执行exe文件即可，然后下面直接插入URL地址即可，我喜欢后面加一个—scan=3  
```
--cer : 过证书 / https://www.xxx.com--cer

--time=x : 超时设置 / https://www.xxx.com--time=5

--url : 自定义URL拼接 / https://www.xxx.com--url

--proxy=127.0.0.1:8080 : 过证书 / https://www.xxx.com--proxy=127.0.0.1:8080

--sleep=x : 请求的睡眠时间 / https://www.xxx.com--sleep=0.5

--scan=1-5 : 扫描深度(默认为1,最高为5) / https://www.xxx.com--scan=3

```  
  
  
输出的内容蛮多的，之前有个企业SRC泄露的AK/SK就是这个，直接云接管一百多个G的资产，且可以进行使用CF提权操作  
  
![img](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QUYXj48iaRVrEG0LccTkzY6v3LouZDBfDDMbpo7I85o7hgR5I4niaIbiakwnvgG4njZ6Xad1KiaNibqgYAtiaAxWmXiaenkjaMmE1wHMw/640?wx_fmt=png&from=appmsg "")  
  
img  
  
后面就可以按照对应的URL泄露的JS文件去找泄露和未授权即可  
  
![img](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QWfOJxcLvQhQuQNO7h3pniaicgxdLZXsdAuZYReXeaxqElVpmkuMQ6D7jmMpPwlnvVqf0h9mDLqoNvayPOp4dZgGGmT7anwZeaXU/640?wx_fmt=png&from=appmsg "")  
  
img  
## 0x4 从XSS客服弹窗获取cookie到RCE  
### 一、未授权接管admin管理员权限  
  
书接上回，开始的官网有个客服的聊天功能点，猜测存在XSS漏洞  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QUmGl0RDFRV7bOX9dWqWq1U8TS4ZtsjlywhX9c0tmO5cVAFzXYmDXyPEJEfC5qrd5faFuRLc0X0hiaNy9TTFAGhdZVZkgyfEXGs/640?wx_fmt=png&from=appmsg "")  
  
直接去里面找客服聊天，然后进行输入下库存的XSS标签payload  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QUw2z3UEXPO7WQUia9EeQO5rUjRic8xx1FlibrTRAFNRjxqweB4062paMsFeygulz7icZpFpmVm8ic9wX4KIr8TR8HekMriaicsHSqvjs/640?wx_fmt=png&from=appmsg "")  
  
发现可以成功弹窗，那么这里尝试下使用弹客服cookie的payload，看看能不能弹cookie进行未授权登陆客服管理员的账户  
  
![img](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QV9jic4dkH4wQetTxbI7ibYN8JLhKlyUDrGeNLz8dniaa6ibfKh46IAP6DLA2wibmBJe6qfXpia6p7282wV4ypJJGFJ5SibVuRkXDnuO4/640?wx_fmt=png&from=appmsg "")  
  
img  
  
这里成功获取到了客服的cookie值，然后尝试登陆口替换cookie未授权登陆后台  
  
![img](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QVbGee2vw7XyO412hxpcZ0BmVrwr9FuXdtDib8icyCTqIKRbVibic1qGyQAHItZ8Mn4VSj0Jsj9IS8MVjXfDnB4H5sum6KzNPWDnj4/640?wx_fmt=png&from=appmsg "")  
  
img  
  
现在我们替换网站的cookie，发现可以成功登陆进去  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QXuDViabfJofuPon1xwV3vqsANzGibA21sqUCNj82bsoKrTiafsJsSlEibYH9nG42JLRR980lhZnpgfyfjPhWOfauyv08tNdcMBn7Q/640?wx_fmt=png&from=appmsg "")  
  
  
  
![img](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QUtYkibD3Bg2oF5rScicuicU0KQ6TcbapkddzFkxfuPsjVyl3OXRh0TPXVohcUNNI5ibiaUzJ55MpicE53m7AG8eialPcCAZQye1XdP5g/640?wx_fmt=png&from=appmsg "")  
  
img  
  
登陆进去后发现账户上admin的管理员账户  
  
![img](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QWEAwibFmsXhpAwBfYkJL7SZw4GgL2eoiaiaoNg98LBQ4eBEJAeJiaqyGSL2xdbawDNS6uWE8FfogeiaqIRqFMibSuOK8cXRmbXlxhso/640?wx_fmt=png&from=appmsg "")  
  
img  
### 二、后台SSRF漏洞  
  
进入后台后，发现后台有个图标管理可以进行新增操作，然后我这里尝试了XSS不行，后面就尝试打一个SSRF漏洞  
  
插入一个DNSlog的域名，来验证看看是不是存在SSRF漏洞  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QVGmqoyCOV8YN1PhpmJiaicaHHDtSwYY00jUicQ4SXvoUW2wwoT1tbLXk2BiaMkUmtsicG9n0D29sxj8FlFlER1GFdfwbFibWmKu32DQ/640?wx_fmt=png&from=appmsg "")  
  
发现DNSlog有回显记录，说明这里存在SSRF漏洞  
  
![img](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QVRTC4O3hZxfGprdKiaLpuLfupibGD2CAgibORBK1VbRvDX7J1LGeHsiacJMZiaYjWjZ8KNFpWx06HspJZRce4UtBg8JyJib349ibgIuk/640?wx_fmt=png&from=appmsg "")  
  
img  
### 三、后台RCE漏洞  
  
峰回路转，在这个站点找到了一个后台登陆口，且账号密码是弱口令：admin:Admin123  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QUuOwLNjl9eheanHFMQQnWkt6ekKoTxON1XECE8I2xNoKktBv1nicSnR0vZAiambjFSMt3icUFw85qcSrExyOAibwbVJniblKzHlCVc/640?wx_fmt=png&from=appmsg "")  
  
然后来到后台，发现这个存在cmd传参，让人觉得这个存在一个cmd的命令执行漏洞  
  
![img](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QV2ibybpUeNDVou0yUOF1K1J8iaia4jiayB0jSGZvF5VxFVmxHdic88Nq76FeaqFpiaib3tImdR6A6hQcMrdv8BmtDFxpflqFuNhpImt0/640?wx_fmt=png&from=appmsg "")  
  
img  
  
然后把cmd传参的内容进行base64解密，发现是一个文件，说明是利用cmd传参命令执行读取文件  
  
![img](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QUibMnfh8TfpxxDnHl4gLQiaTcDic0rHRrmicriak2Gt0nO6Bz9MCVicUUFp0TgTd1TnXqzhwddUDNS4rB0ngXscRKjfEb6ZtwkTU5O4/640?wx_fmt=png&from=appmsg "")  
  
img  
  
我这里直接进行尝试读取/etc/passwd文件，base64编码再进行使用cmd传参  
  
![img](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QXhJEAXITakOF0QZOUVgoThmawica37Fcl35wlZ7FVgaM4QQYZWCGvb2lXgxPtU6nS1JKVAvrE1nvnQ1475LLdSGCypExPPU0Mk/640?wx_fmt=png&from=appmsg "")  
  
img  
  
成功读取/etc/passwd文件  
  
![img](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QUxx3qZnRS6EcQjYIRRAXS6mvP7FaGkc4tsFhQfMMx72dzZe8yDIPuV1eQtzLobdgwodlXRLOiaFfDYonlx78vnxIUJTlS2rFvs/640?wx_fmt=png&from=appmsg "")  
  
img  
  
Linux操作系统，执行命令获取改系统的内核版本信息  
```
../../../../../../../..//proc/version

```  
  
![img](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QWyhbjwy3nHsCavAdeBuW1yuqgkc4PNOSSCz04MsEbKibp1sMtbzicRDbv06uy6cv3j9RLUQeibpPHntHnicothgeibMskqJ62ItSAo/640?wx_fmt=png&from=appmsg "")  
  
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
  
  

#  第一个edusrc高危漏洞  
原创 陌笙
                    陌笙  陌笙不太懂安全   2026-06-30 09:24  
  
免责声明  
```
由于传播、利用本公众号所提供的信息而造成
的任何直接或者间接的后果及损失，均由使用
者本人负责，公众号陌笙不太懂安全及作者不
为此承担任何责任，一旦造成后果请自行承担！
如有侵权烦请告知，我们会立即删除并致歉，谢谢！
```  
  
前言  
  
刚挖掘edusrc的时候，因为复现过很多nday漏洞，所以基本都是找nday来打，这个是挖掘edusrc的第一个高危，没那么好的综合案例分享了，以后每天分享一点简单漏洞啥的，混混活跃度。  
  
信息收集  
  
当初收集资产用的是360quake  
  
  
语法用的这个，可以记一下，挖掘edu还是挺好用的  
  
```
isp: "中国教育网"
```  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboSfd4J0bjy3muVcibANIWMibjKianS5bibeicX6xnhJz0RRLguuAPKgoKDbGOiaGoS3A8g0ppk4ACRLQ07Dn5yZnrYLicj9ZN6GE2icBwU/640?wx_fmt=png&from=appmsg "")  
  
  
  
然后配合tomcat的指纹进行测试  
```
isp: "中国教育网" AND (favicon: "4644f2d45601037b8423d45e13194c93")
```  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboTpt3OZZLrJRAe2t1tkATuFHopzVH4BpNNS5XxL1oHklyAkniaXvHmrQLXLlBaERWXBL7UibZ50hzOnRTZw7uxvkHicqTljrE2X4g/640?wx_fmt=png&from=appmsg "")  
  
拿到资产后，试了几个，真就发现弱口令了，后续直接构造war包getshell  
  
漏洞挖掘  
  
信息收集定位到这个资产，点击看到tomcat的默认页面  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboQZJicbnDeF7ztbtfRM5EJ9LrMa8U9iaWFCgxj4iajiaCxSAs3ycleSWPyvVxgy9sgMXu1AAqXxCbl9nic6WibwhapXGiaLjYicqwHmEibw/640?wx_fmt=png&from=appmsg "")  
  
点击manageApp进行登录  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboShATOQjjrTerC7hEGvxIhufqLc3JQFLYkWJriaMsYlg82wzVt61iceIJGibM7zPnTdAJsbwGpNVZ2icQeuItGABbHwI8UlDxXvofk/640?wx_fmt=png&from=appmsg "")  
  
手工尝试几个弱口令tomcat/tomcat直接进入后台  
  
当然这里现在来看的话直接把所有资产导出批量用漏洞扫描器更高效。  
  
进入后台之后来到war包上传这里  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboTUG8LusDAOSppIHf8MqtIYODDpvNkJxufic4M0ibNO7ibTLJTLI6XuiatVlanYVH3AUWmvTflMhUWtwqUyvzpVAhXSobwB8ATozd8/640?wx_fmt=png&from=appmsg "")  
  
先不要上传任何内容，制作war包  
  
使用哥斯拉生成一个木马  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboRZvQrRZAJ27STsSJm7aicXo0CdncjI7laOicLbODYrwm1nVV91uB0MzakFTMDf9ORiaOQapehhDH2f6tHjWEk4zYEewC8JAcPGgY/640?wx_fmt=png&from=appmsg "")  
  
生成之后保存为test.jsp之后压缩成压缩包test.zip然后给压缩包重命名为test.war找到刚才那个上传点进行上传  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboTibYl9kk1mGAthJPYMSgbuVNYkVOibRr7qdMQqb1Ynqk3YL7AyPsriceKWFabpUzTnLKrpq8IcKelNvVMw1gXEMeg6ibkmladgMPs/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboSc4pwibZj5Bm4I3V3lgPcXK66ylsclPibCRrxLvBsf1coZoRwfkicehRXiaMWwgIoT54Ew5hRzxcr3DeibJ3M3Xl0naMp7OdkcS13w/640?wx_fmt=png&from=appmsg "")  
  
点击上传上传之后访问地址 http://xxxx/test/test.jsp 可以看到空白，之后使用哥斯拉连接  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboRgk56KfJwMsSPaIiasvrtMyrFMK7lLS81RwK2sHibYFccKZicCJ93ia4fialQ0rZPnEbtOiahlSJ51qibbSPQZB9dKCxcdib3ibuRNCWPo/640?wx_fmt=png&from=appmsg "")  
  
24年这块，连接成功后，直接进入  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboSFfDcDiaRSUPwUNhC6StabJag2tBn7Rd8KRYAAl5xNicUsoVic2BpwFlkKrFEcyEficrVk7GvMys8ex01x3csPRXCnFpIpWibqPxUY/640?wx_fmt=png&from=appmsg "")  
  
执行命令成功回显  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboQcrak0tRz4ibGxr32KY4U1wLPw6FFU4kqwcfP7iafSjUAIwJHsCtq18RJ64BYIWI9HpYxQR8NIsHC41f6jHctuAAqbQBBVenKag/640?wx_fmt=png&from=appmsg "")  
  
也是非常的遵纪守法  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboQogC9qgTkfykrT2aG17agNkIpujJaYq9a92ib7hjXlxEPffbHbypYwufoevefHx9bnV4RGt8Kd4Y0FfFlqwkXN4kzoc5Cmk2Iw/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboSXymdtkI6Kt8195dTx8lk5h1mQEX1sLkeL3CIra1p9Xwl0QpJD8ia5UltaBpGtNIsrbFxZFjke01Yc1NgZ3asVpjQXgMDibreyg/640?wx_fmt=png&from=appmsg "")  
  
总结  
  
现在nday可能没那么多了，但是大力出奇迹，漏洞之鱼肯定还是有的，我这里扫描器，就出了一些，过几天可以水一篇。  
  
tomcat常见漏洞可以看这个文章  
  
[Tomcat常见漏洞汇总](https://mp.weixin.qq.com/s?__biz=Mzk1NzgzMjkxOQ==&mid=2247486040&idx=1&sn=80cfa917ec2d97904770ea3ce2842ddc&token=958237576&lang=zh_CN&scene=21#wechat_redirect)  
  
  
  
**后台回复加群加入交流群**  
  
****  
**广告：********cisp pte/pts &nisp1级2级低价报考**  
  
  
**陌笙安全纷传圈子+陌笙src挖掘知识库+陌笙安全漏洞库+陌笙安全面试题库**  
**简单介绍****（**  
**加入纷传圈子**  
**送****知识库+漏洞库+面试题库****）**  
                          
  
如果觉得合适可以加入,圈子目前价格  
39.9元，价格只会根据圈子内容和圈子人数进行上调，不会下跌。。。    
  
  
**圈子福利**  
   
  
**edu漏洞挖掘1v1指导出洞**  
  
****  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboRKQWHxLsRrPqpqdiceX76d7yExQIyOqFmmJAfHQh7qzKvPc2V5z6iaa0RY6Ib8AsGvgS5MKkAk5aaHnJBaSnI10LDKQYMLcQMmg/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboR8pnPeapLBK4Jsa4ufCvFoGL66t7PKeZyA3AjNxsObjtnCibN2gzGX7NMS7Wo5sj3YYL2iboeRuQDcWqiapc8xuo5fticoBG4DsyY/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboRKIBNIQIVicRWJLbyGRmg92vPzc8375PJpcYVvfywzwqnaeBicZuEbfvuic9KRdjwkahSDic5VqrH2Mb4NkqtkADl5HLIh8gPex60/640?wx_fmt=png&from=appmsg "")  
  
  
**陌笙src挖掘知识库介绍（内容持续更新中!!!)**  
```
信息收集(主域名信息收集,子域名信息收集等&会永久提供fofa-key助力)
弱口令漏洞&未授权访问漏洞挖掘
任意文件读取&删除&下载&上传漏洞
sql注入漏洞
url重定向漏洞
csrf&ssrf漏洞挖掘
XSS&XXE漏洞挖掘等等常见漏洞
cors&目录遍历&越权漏洞挖掘
EDUSRC(证书站挖掘案例分享&edusrc挖掘技巧分享)
CNVD挖掘技巧分享&实战案例报告编写
公益漏洞挖掘（公益src挖掘漏洞分享&提供补天1权重资产）
SRC挖掘实战(针对各种常见功能总结的常见测试思路等快速提升)
经典常见Nday漏洞(常见中间件&以及各种常见框架)复现
云安全相关漏洞挖掘（云key扫盲&云存储桶&快速识别云环境&云攻防）
AI相关学习（AI基础&AI代码审计实战测试&webLLM攻击等）
APP&小程序漏洞挖掘
等各模块不在一一介绍
```  
  
信息收集  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboTu9DGyTubluhYicFynwVBKa4V06sDfEVKOyk5Q4ghZzLMDAuLb1M1oR4RJumGWrADPapFjTrOjpksKQ8q0YYCnl3ZWLof8Knzg/640?wx_fmt=png&from=appmsg "")  
  
src挖掘基础  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboR45bibbJEb28a1gS5yth3r5HyOsgPiaOUHHYriahZyIyrk0LMOsHW4VoDibyBRibTNzptGiaLWX62UwykicwvbxCJPopvklqiaxML8lS8/640?wx_fmt=png&from=appmsg "")  
  
src挖掘实战  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboTKWnTsN6CXf3djhXIlMKNRjVmJn3g5b23ur9E6Cx3O68f0hXVjCiaj8J4RYeTGBecqf1k99phG0ice2wtd5lKgR46OeqeLQfMpk/640?wx_fmt=png&from=appmsg "")  
  
  
edusrc  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboQVVlTXhibjR8UiakZBQicXRZrQ7hdoOz5G8MQrcuDBGbqJdO0kIz6R9IU4ObAeOiabT8pr6lc7jibdIkKoTjiaXNHPLAwAB3BV2UvLM/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboSCrvarBbzP4L9kS6P0LVH9JMdmcbFDKiaicHqMFgTxq3x4iatjDJQicmc7NPC14C9Fk3icFjrouSgNVaN8Byuf0C0Iq9O6D1XPvFvY/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboRALwXmgZ4mh2LW0RdicrKjBCP7P1iaF14G0Eq2v3KRnTJORpwXZlF58WEz6QicxLJpyJaA5iah5CF2rHjBz4JzOELFRaZTAKOQ2tQ/640?wx_fmt=png&from=appmsg "")  
  
经典nday复现  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboRu8Gf849iaCkSBxLL8IlzJTRs185QicEe9l5UGI1dEVKISt2IGGveZynXBW9tIUsxNsz4adSTib7rib50uSJdjNfTvVRFrbPJhzL4/640?wx_fmt=png&from=appmsg "")  
  
  
云安全&AI安全  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboQ9qiavETNjaaX162czpNCqpw3uJqVpicbI15AXzhf5x8icmHxBdTGOgRgzNPGF3Aw2gglT4Fx09JGXYibQC6U7CQKVmoH08l3meia4/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboQgcsjiaZ4S26TWowHfpBkhSeHf2pjrcDyicJuia3uqvRBauLEOicibibEMqibnBMtjopFL8No7UXNibbURvzeJ3dQHTibvGxRQGnorb4co/640?wx_fmt=png&from=appmsg "")  
  
**陌笙安全漏洞库介绍**  
```
最新漏洞查看
1day&0day分享
EDU学校相关漏洞
Web应用漏洞
CMS漏洞
OA产品漏洞
中间件漏洞
云安全漏洞
人工智能漏洞
其他漏洞
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboTFBRMa8XwYxfcZMyXicx94xSKxawPcqFia2rJKOL7fSLYXiccwHc868XxNGIQ5z7ibiaI1MNAGRrK7U6wXJTsZOCAu2I5XV1boTAL4/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboSBzdakI9XI33ReAm2dxO8vgzw3JicQmUuWCb5ayBlKR1PoQHEHFETteBnicyupwU0mXvXibfrDoyg8nSWBGoK1p2YXY3ElhcvOQ0/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboSajCclDhuRpaLic9Ld915CHU7RqSC1LCrPGfNZiavdPEVeDedDWOPBhtMLCicTp3RNd1lT0Pmfo3mx5B0hUxbQg3ic6Via90NMtZVk/640?wx_fmt=png&from=appmsg "")  
  
****  
****  
**陌笙安全面试库**  
```
渗透测试基本问题一汇总
渗透测试基本问题二汇总
渗透测试基本问题三汇总
微步护网面试题目
长亭科技面试
深信服护网面试
启明星辰渗透测试面试题目
安恒面试题目
绿盟笔试题目
360面试
奇安信护网面试
运维面试题目
运维面试题库
网安面试相关文档大全
相关面试文章推荐
等等
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboSoLqEzH0a3A4LQrvTIkGx81Sh5pf6fCoEQJhYg715vrJicSkfBuCoAmV2Kp4uOMe5jcUZutPwicibFibtJ1ZmyiaAibCg0XicWnsNcicE/640?wx_fmt=png&from=appmsg "")  
  
****  
**POC库****&&更新适配afrog&&nuclei&&dddd的POC&1day/Nday等&&**  
**dddd二开****工具[助力渗透测试&&红蓝攻防]**  
  
**工具截图**  
  
****  
**实战效果**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboSKqLXNcOPE07xOwOUCjRGuFphopPumW9RaticmNCuEUXu52GtdTTfpTUicrBj80kMcZzJsnps3abyvXIvLHEIhvMoXUApOqZCe4/640?wx_fmt=png&from=appmsg "")  
  
****  
**poc库【后续持续更新】**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboTG9Lyp44aFffUOxQKtHjToGfqFWTjswYft0VtAPINtV5MqmrTTj8GWrVb6yowvHURubPgOqdribmibWEb0Fcj3YdN4iahUwItcxE/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboRAMJIIvexOOJa5KhrsKmlsx8bkwib9SPoK72Q0OSPWR5qx67yvl8scMQ5bg8caBXZH01kM39RDnKpnWSaTicgobRmLygERGFWls/640?wx_fmt=png&from=appmsg "")  
  
  
**AI赋能-**  
**skill辅助**  
**漏洞挖掘（免责&&慎用）**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboR3Dib0RVxVhUOzS6ibC6BvkfulXQAclic0XCXMS35C4EPoqX1b2eMVj2CFiaLCelVs1szGibaHiaAq7WibRdwHUg0IwO8fjDdWxNv6eY/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboSXZZVels2NibmHgyxntlCRNIkgoqMPfUPwSM9O43OqniaZLDEJic9QRkW01gNTydFkibdI6yBRkJJ1sDUmfl7iaicoibz1QLp0J2pWE4/640?wx_fmt=png&from=appmsg "")  
  
  
**圈友ai辅助渗透**  
**实战效果**  
**，支持打假！**  
  
证书站  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboQibuxKAyHBZicB1t5yGVKyV82Teo8C2MbjKPytKziaXUcjPiao8ylHbD4vicAld8equC9alic3NksvWJ09wArXaPXZD10vjPtfoia4Vg/640?wx_fmt=png&from=appmsg "")  
  
普通站点  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboQ1Xt6gdLgd2d1mf8QURQX4YZjHA2uIw9GdTuxzSafBVOJzrQVmHJlqdhVWdVDj3OQsiaQhYOaoiabXc6EgajvBMvB6xBZwdJkIQ/640?wx_fmt=png&from=appmsg "")  
  
****  
**陌笙**  
**纷传****圈子介**  
**绍**  
```
1、src挖掘思维导图，信息收集思维导图，edusrc挖掘思维导图，以及后续的红队&面试思维导图&自己网安笔记等持续更新
2、2025-2026的edusrc实战报告包含证书站和非证书站以及2025之前的各种优质报思路分享
3、各种src报告思路分享（内部&外部）
4、分享各种src挖掘&edusrc挖掘培训资料&视频
5、不定期分享通杀、0day
6、有圈子群可以技术交流以及不定期抽取证书&免费rank
7.分享各种护网资料各家安全厂商讲解视频&精选实战面试题目
8、各种框架漏洞技巧分享
9、各种源码分享（泛微、正方系统、用友等）
10、漏洞挖掘工具&信息收集工具&内网渗透免杀等网安工具分享
11、各种ctf资料以及题目分享
12、cnvd挖掘技巧&CNVD资产&src资产分享&补天1权重资产分享&fofakey共用
13、免杀、逆向、红队攻内网防渗透等课程分享
14、漏洞库&字典以各种内容不在一一说明
15、cisp-pte/pts&nisp一级&nisp二级&edusrc证书内部价格
15、如果有漏洞挖掘问题或者工具资料需求可以找群主(尽量满足)
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboSY2pbvbP3qGAlW8O43bRvAISCxZm4UDTRsaMVbJKTsjfTMTDlq6qNBcVs4tkl4UzgqGz5ag81baU1rusKE09J9T6cMVliaibibwQ/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/MSDUaqtwboTrLRQpTicOR7bzyNiajiapVJgyMiaYlEDBVU87YXMnanOFWsCYN3cCVGsKkibzV9dMryvbFXBb4Z3472ib27RJ1Xq1HnKJIp5u49GYQ/640?wx_fmt=jpeg&from=appmsg "")  
  
**目前740多条内容，扫描下方二维码查看详情以及加入圈子，持续更新中。。**  
  
**如果觉得合适可以加入，价格不定期会根据圈子内容和圈子人数进行上调。。**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboS6aWPIgWkKicUwu8ZiamtqWhg7UcFK22okQrXnQcTZxiaXFVrl4QXmUMxrSOic69VyUlQictbauRDrKXllL810lAWMOtcOvb9taUAM/640?wx_fmt=png&from=appmsg "")  
  

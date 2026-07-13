#  00Query 发布！基于00信安API的微信公众号/小程序隐藏资产发现工具，快速扩展SRC漏洞挖掘攻击面！  
原创 地图大师挖漏洞
                    地图大师挖漏洞  地图大师的漏洞追踪指南   2026-07-13 08:17  
  
   
  
   
  
#   
  
# 1.小程序及公众号资产查找  
  
大家好，依旧是我，  
地图大师！今天给大家带来点好康的！！  
  
为什么公众号以及小程序资产越来越重要？  
  
对于**红队和 SRC 漏洞挖掘**  
来说，**公众号**  
/**小程序资产寻找的重要性越来越高，而且很多时候比传统 Web 资产更有价值**  
。原因在于很多企业已经把核心业务迁移到了微信生态，但安全建设往往没有完全跟上。  
  
文中使用的工具已开源至github在地图大师仓库找00query即可  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/DdVXYMZZ2ziapY782icu9uqzrSoQfN8kMdvibmWKUeUxRIkXffGjnL7D9VNZuUHsWSumVX0RvZibUlTVWmJkPbGRT8daltpqG6FibfmTDCs8Uw68/640?wx_fmt=png&from=appmsg "")  
  
  
过去企业的业务通常是：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/DdVXYMZZ2zhwjiaiaHibhGWZgOn8po9gxaZmL2dw85n8coXhgXRHlhEbjPX0rpElJrmPsCzE5EdrZFRrMlZnb6aCiblOAx8waNPJlm8mVZU6jibU/640?wx_fmt=png&from=appmsg "")  
  
现在很多企业变成了：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/DdVXYMZZ2zgcaaSzErGSdf2fwuoIN2Bz7BkNNPb13a4rysg9YZxicmKAOS15CyeT0qoYMnNRaP8zbrzZDhRTb8rxJvaibHJZJUbEXFSSicRp84/640?wx_fmt=png&from=appmsg "")  
  
今天就告诉大家一些寻找微信公众号和小程序的方法，并且地图大师也给大家开发了便利的工具供大家使用，一定要看到最后呦。并且地图大师专门做了个00信安的图形化查询工具供大家寻找微信小程序和公众号资产。  
### 一、方案1：通过微信小程序面板直接搜索名字（传统方案）  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/DdVXYMZZ2zg2Tq3icWDR5s55BhY1LgyzDpktfdz6gI5asrkzX8oQxj0C7aBqUhGg3AoOibRXo471wuE9CFdXLC77OmyNLRRhyGnzXyIqwmTxA/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/DdVXYMZZ2zge7amUiazYVqM0CHxj4szYaPkAYicaE9Df8wKHLRrlaYE9yn7qiavxZnoXCz1lFFtXoAAkWLzCjpxVhcuF5dERmEicNa4DoFFoCYs/640?wx_fmt=png&from=appmsg "")  
### 二、方案2：通过ICP备案查找该企业备案的小程序并使用微信公众号后台查询小程序appid  
  
（1）使用icpApi查询目标企业已备案的小程序  
  
![](https://mmbiz.qpic.cn/mmbiz_png/DdVXYMZZ2zja0fWVvjtmUlEoFR2jViaO5qjxAiawRia3HalofJCa9kT7ibZ6nGf2MS1GibuAfvU3tgGvIFaicmz2BGICBzSxU4Kaib3ibFcIYAFty5s/640?wx_fmt=png&from=appmsg "")  
  
（2）注册一个微信公众号进入微信公众号后台  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/DdVXYMZZ2zia0AicfLbIXGKibPemYEyqky4uOMY6aphVicDb7VP1ibZdicGw8eVkhxeAMPGqMqcfPhh4KcLM7bQfB9bOoGE2HFTsH5UF5u97VVEib0/640?wx_fmt=png&from=appmsg "")  
  
（3）验证身份进入该功能  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/DdVXYMZZ2ziaAnQFztMJMFZvUjHzIZjfAuD0icG0iccwBBEcaVibLjccNgBUqoJ9RAcPzw5emDHBNLKWvnDhWkd6Noq4hk3ejtbN4YtFficoezmE/640?wx_fmt=png&from=appmsg "")  
  
（4）输入备案查询到的小程序名字，点击查询，如果查不到，代表该小程序已消失，能查询到的话我们点击f12在查询一次获取小程序appid(appid相当于小程序的身份证号)  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/DdVXYMZZ2zhNGrRP3MmJ5eiaN0WtJQDbW7iboAJzTkFYvBVlQfBNwiasfwNQibSPO5t8HDgsklh9wFvvahMx7D0afhDZibvoU9BibxINGb6DCGQqI/640?wx_fmt=png&from=appmsg "")  
  
（5）拿到整个ID后微信打开公众号，**地图大师的漏洞追踪指南**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/DdVXYMZZ2ziaUYvwgE5B3poTYNphfMmia97mPzD1nRIicus1YJcNg2j5iaGDhI4o553CLxIE7ibPl1FexPFCV0AaTZKwGicfbyo6sg1tSTSEJLp0o/640?wx_fmt=png&from=appmsg "")  
  
（6）进入到下方功能找到小程序跳一跳，至于公众号跳一跳怎么用我们后面来讲，先专注于小程序跳一跳（**小程序跳转工具我看网上有很多前辈已经做了，就直接公众号里搜了一个供大家使用，大家用哪个都行，公众号跳一跳是大师自己做的一个简单的小网页**  
）  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/DdVXYMZZ2zhXyGibqTFcJsSNbKZLEJTV73zsPM84PYWrvZo9hiab3becLic1XgYCfkgIEaOnxt9KCIR688wChehIRnnCTy6efBg2wcAicStXLTU/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/DdVXYMZZ2ziaEstHon72bHu51CFZDv8Qw39OiaHcmTCg6SPYvzMJuImZDp2DbtOl55MZB3r0z5QwzsxeG3GT9e8cO9yW6FJSw2FhO0fBqnEsw/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/DdVXYMZZ2ziaroS55mkWzLLHCw2sDiaO58fbhpmoT3XAsE2cb6YxdjVqcGiaHTuuIiaibO1YFeS4mfdawxE2rxZYeycbgSf63QjET7GNQIrUicWy4/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/DdVXYMZZ2zhTMuDD14rqkBYf0gm2JqXgtdFUq8JZzSMUdnSH0oWaIrhQe6N4kHQ4dyZXko4a5rqxM5Yu0HvsoF9G8FGzlNpfEOiaxj2uduZk/640?wx_fmt=png&from=appmsg "")  
### 三、没有ICP备案的情况下，使用地图大师特制零零信安综合查询工具进行查询  
  
工具已开源在地图大师的github仓库：returnwrong/00query  
  
![](https://mmbiz.qpic.cn/mmbiz_png/DdVXYMZZ2zj1VZxU533QRssDrtxJ4ic4icEicBoZZEv06IXV6Co5CsWB1Tmib9ksJ09JFBia8FbsqzpFCQd0u7PsBXFrCsLEZZHNJeqbsG1r2Puw/640?wx_fmt=png&from=appmsg "")  
  
  
首先需要点击右上角配置好你的00信安key  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/DdVXYMZZ2zjt6SZPoA7azzZFFPpxSE967B0L4EAMUJQ9xVQysNJ3E5w16ChEN1n6r9sTEpMXuPSKL2xCYeYr7IcGDBwvdPvdeWO4a7p1EfI/640?wx_fmt=png&from=appmsg "")  
  
key获取：访问00信安官网注册后获取  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/DdVXYMZZ2zgu38g8NvuV1yRfbth4AM2MPYQZ7SSSOEOtS7Z3VKXFUn9iazBb3Ie1GiaIP8Rtjnc0Z6KticoGJZRNic7vJ8toLfOcOm13ScV9VuI/640?wx_fmt=png&from=appmsg "")  
  
（1）选择移动端应用模块，支持icp备案号已经企业名查询，语法如下，一般大师喜欢公众号和小程序一起查询  
  
icp==沪ICP备07032180号&&(type==微信小程序||type==微信公众号)  
  
company==中国银联股份有限公司&&(type==微信小程序||type==微信公众号)  
  
![](https://mmbiz.qpic.cn/mmbiz_png/DdVXYMZZ2zgz5rQkcNCwFXZQwWbiaJgRBX86LSRibNoN9eAsdW8zqPWBdf6EviaDkq1BKS14cs3kF3oOgXO2hTrckOY107TaqUdEneyqh9ne1U/640?wx_fmt=png&from=appmsg "")  
  
（2）双击具体条目可复制公众号的fakeid或者小程序的appid  
  
![](https://mmbiz.qpic.cn/mmbiz_png/DdVXYMZZ2zj3bFWmonadmfic14oCTYM2XsicHH3AgSBd5ZZs2upUicXpicnYowtCTDCUb66gO6kexYI6WKxoJzEMkQQzb8ROV0qeORCq2Rr8rrE/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/DdVXYMZZ2zg7U3PTmeXT3mquwfAHWJuxzSCeLicGQyvmtpzEKmI34y2h3GrGSZh8mk8aC27vqXegfrg4QTuKmQTV4VhegicGvScXANvbPUG7o/640?wx_fmt=png&from=appmsg "")  
### 四、公众号跳转：现在好多资产都嵌套在微信公众号里做h5网页，遇到这种我们怎么找资产呢？  
  
（1）依旧通过地图大师特制0.zone综合查询工具，获取公众号的fakeid  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/DdVXYMZZ2zgx1teWGdU8JbxO50hsICx3kRuAfWc9WzEM6EZmYIGAFxQgwEnBCja4pg5bibSPP9qIMqibcEcdrf1Mia3L3UdMmWj3eV15ybToRE/640?wx_fmt=png&from=appmsg "")  
  
（2）打开 公众号：地图大师的漏洞追踪指南  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/DdVXYMZZ2zgRvp3KOJBCMW71BlLobmvZx20ia638mBO0sD2AZBbZ9I9BwS1fdDVM5QVtiamcAVuQvlJwoe4vibuGXywCd2iccaUtYcOZ1uUnPZU/640?wx_fmt=png&from=appmsg "")  
  
（3）选择公众号跳一跳  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/DdVXYMZZ2zjTyfDf0yllRQH0hmXicPAsAxNkUFl253t93HuptxNfosDCsSrKAhJLuZv0floK5dM1SNckC03oqbSSUkaRv01VO68CzNI8fRls/640?wx_fmt=png&from=appmsg "")  
  
（4）获取到链接，不要复制到浏览器打开，直接点击打开  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/DdVXYMZZ2zhphf9CnuFc1HCu2ia7S9tw7D3wIavCTQKNz9JibBR2vQibE8icew6ucG5qAHNDaOEwHo1EtS5UoQHnRXibEANbOttJ6MufX4zbqOeM/640?wx_fmt=png&from=appmsg "")  
  
（5）复制fakeid进框里然后就可以跳转到公众号首页或者历史消息啦，你自己二选一把  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/DdVXYMZZ2zialPJFZmgeriaLmM0wTK56YWMCAyPmXLDDlLniav6MgCSgFELYNQEsgCsqdXMvdJibXUEbVvhyicg5ko6mFALaR5Qtr9uQXNlVl3lg/640?wx_fmt=png&from=appmsg "")  
  
如果公众号主页打不开，就点历史消息那个（因为有的公众号注销了）  
  
![](https://mmbiz.qpic.cn/mmbiz_png/DdVXYMZZ2zj3BR5MqQsf3LCBuLtibf06XuY9BGRHVmibrdgjBUGqusO2P7mkkAMxZGicr1IempFx95lQ1l9xTmn7XMR7ibceSPMKdoDaD0KBaEw/640?wx_fmt=png&from=appmsg "")  
  
最后感谢奶龙师傅提供api key给我做测试用,感激的心！！  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/DdVXYMZZ2ziaIYF7hictGWibOkXfNcKlMiawNaVDiarKZgRUAQpl92dbnrTgNyaXePQEl8goCPwexkHuicdJwQwribuDh7Mbic9W65vmb8w6pEDYY98/640?wx_fmt=png&from=appmsg "")  
  
  
   
  
   
  
![](https://mmbiz.qpic.cn/mmbiz_png/DdVXYMZZ2zhCSbQtiacqnUiaAsE8gMeL1M07ibSu6n7aA61u2dibmd1ZHxmL5Eu6JwhickzHmrTY9OOZdX4ibJ2Vp8YI9L1kBibYRQIVtOuhXdAkL0/640?wx_fmt=png&from=appmsg "")  
  
  

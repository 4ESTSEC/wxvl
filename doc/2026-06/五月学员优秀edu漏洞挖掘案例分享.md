#  五月学员优秀edu漏洞挖掘案例分享  
深情哥
                    深情哥  湘安无事   2026-06-28 15:57  
  
声明：  
由于传播、利用本公众号湘安无事所提供的信息而造成的任何直接或者间接的后果及损失，均由使用者本人负责，请勿利用文章内的相关技术从事非法测试，如因此产生的一切不良后果与文章作者和本公众号无关。如有侵权烦请告知，我们会立即删除并致歉。  
  
## 前言  
  
  
上个月有几位学员和成员再湘安内部平台名列前茅，给top3的学员和成员都发送了kfc奖励。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/tlibgKYKL9Esj4VSHf9tibTr9xppJHibaOUBSKdiaw9Vu8URVesf1PuicETf7adZbepgbwRugoeddpJX95nk5ibGGTl9ddFgm2zT1Ju8wAZUX7qIw/640?wx_fmt=png&from=appmsg "")  
  
  
所以我们来看看他们的优秀案例吧  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/tlibgKYKL9Evn0dquH9ib81HEAmyb7bwzOgySM8hg4cW37uGOXCH2o0DfeMgjbm5Azg3hpPnZ7V8PEc6XuTqkeLVkzpUicUJDnjH3ynjZc4TibU/640?wx_fmt=png&from=appmsg "")  
  
## 记一次未授权访问到接管大量学校管理员账号＋SQL注入的经历  
  
  
故事的开始是煮包在复现一个SQL注入案例，下面这个小程序是存在SQL注入的  
  
微信搜索***小学，找到这个服务号  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/tlibgKYKL9EvQTicXc9qpdzpxCOI3LjTIf18zBrXEDroib3EAewLsem8B4ia2aOPAhHibpB0kqFGchH7odh99bLg1MROnPcGic9eIdwfh5GF0YubY/640?wx_fmt=png&from=appmsg "")  
  
点击**小学抓包发现有四个包都存在注入，接口是不一样的  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/tlibgKYKL9Euc3yUgrdFDkNa3OfNOScK5ydFL6KCaGzrjahbCOpJE8p0fbg2G7Bg44ZUFNSgiacy01AkSwfaxGMY4OvtOR5x3XnzygmqEsibuU/640?wx_fmt=png&from=appmsg "")  
  
本来故事到这里，就应该结束了，但是煮包测试的时候，习惯看一下bp的插件，打开TsojanScan插件，可以看到存在swagger-ui api接口文档泄露漏洞  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/tlibgKYKL9EtB9W8t2J1Avos16kDw4RfQQpmAJSYibImCDvmpSVBrKdnUXBhRaj3wSoEmQtbtibxrIhU6sU2icpDgnymNgWwysEGmpbVrCRwERY/640?wx_fmt=png&from=appmsg "")  
# 未授权访问漏洞，可以看到接口文档的路径，再次进行拼接  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/tlibgKYKL9Ev1YgxicDhBEHHYVjU1uu1xtMHcH5jgfpLFHHbYmEmIys9wnxVsaWoc0fYRSgNXluoD9y9z9ib4jRaq6NPfWfUw2mOHtjEFYD83E/640?wx_fmt=png&from=appmsg "")  
  
可以看到所有的api接口路径，大量接口存在未授权访问，下面这个路径存在未授权访问，泄露了其他学校管理员的手机号  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/tlibgKYKL9EsI0XW1K28KduqdnXSnkyfibr6cvmAOu0VicTgBIkvIH6ut6qI6pCELOiauw5ib84M4Cgib0PiawrIeoxt1Ke8YBfZ6PH4V4G6m8GIm8/640?wx_fmt=png&from=appmsg "")  
  
搞到这里，煮包也是交了edusrc,准备拿个1rank美美下播的,毕竟是复现过程中出现的，也没有太高的奢望，但是理想是丰满的，现实是骨感的，漏洞不够敏感，危害不足  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/tlibgKYKL9EtDaLMgc23MKgXwVx1BibkfemHA9Lia7gJcVefgcNjr3ofVLicrFeiap1bkBt6ERprIH7DJuebuallRUMYQtHkFnUgYulQKXQf4mfM/640?wx_fmt=png&from=appmsg "")  
  
这就很难受了，难道要耻辱下播了吗？但是煮包不甘心，毕竟这个系统又有SQL注入，还有未授权访问，肯定还有漏洞，于是打开hunter，搜了一下这个网站  
  
![](https://mmbiz.qpic.cn/mmbiz_png/tlibgKYKL9EvibtnePnbLJnsn3WiaqqSceLAKrLSiapPlVCLttiabdEbNgWQVRgVy8boI5bAse3v8GmcptRwXwETZFTZWHaYFQz1I31MGnrKqK8g/640?wx_fmt=png&from=appmsg "")  
  
点进去发现需要手机号登录，还记得之前未授权访问获取的管理员手机号不，这个时候派上大用场了  
  
![](https://mmbiz.qpic.cn/mmbiz_png/tlibgKYKL9EtgrDUcoc1XvhRwue8ictzPibHSI4HHxtUzc0Xjjiaj50RUwMvEQ47CSznKBVsyFWouqic0paOxOq74jWpyXWhfxQIBLgDxgEc2Q0w/640?wx_fmt=png&from=appmsg "")  
  
直接试了一手弱口令123456  
  
![](https://mmbiz.qpic.cn/mmbiz_png/tlibgKYKL9EtoUsgCLT3Ogic6Hsn7UkMD0s3rFO2Qx4dCQibqKIiaYVibhEMdribRedLeVb6Ce2xA7eRQAsFqZQ3UmxXEVFdDI2AOcTOCFOia49KNE/640?wx_fmt=png&from=appmsg "")  
  
没想到成功接管了管理员账号，并且权限还挺高的  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/tlibgKYKL9EursZia8L2ck07IeKZd9KFZB05BZ9BoWkmw3yPzV6qTBcBV2RUdVarP1t3zCfaib5DEF9TZUfxRo4ER12a3dxbgpZPu4t84RMlOc/640?wx_fmt=png&from=appmsg "")  
  
信息管理里面存在大量敏感信息  
  
![](https://mmbiz.qpic.cn/mmbiz_png/tlibgKYKL9EukkHxzJtCzrY4qhsgd6XtibHvrJEm3Zv3PhBNb5NY304lyuCJVLrIjkT5XAINYtcxPKrXusCt4oEgh3eatNKZ4mkclibpiccEaC8/640?wx_fmt=png&from=appmsg "")  
  
其他学校的思路都是一样的，把未授权访问获取的管理员账号都尝试一下，最后也是成功接管了七个管理员账号  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/tlibgKYKL9Et6wRz1JPTnV5jUtkGuq9sSdqsZBZXtwZsiaMMTSj4Uh5Cc3CROxo4WBYCStluI7mOz3hicHIwGCMNDAVByicV1BMcIJEbTPCLlSg/640?wx_fmt=png&from=appmsg "")  
  
  
都进后台了，煮包觉得还是要测一测的，点击考勤管理里面的学生考勤  
  
![](https://mmbiz.qpic.cn/mmbiz_png/tlibgKYKL9Etkn9gC3p2k8LEn7awjwU3JxYMQiccDEltBict0K69dewIsp0ibdVYrGRcOZWEsEdZZV5gbkn0VcOGgTX1LXLOdV041ovDUsCE1lE/640?wx_fmt=png&from=appmsg "")  
  
点击新增，备注处存在XSS漏洞，输入XSSpayload：  
```
<div onmouseover="alert('XSS')">Hover over me</div>
```  
  
![](https://mmbiz.qpic.cn/mmbiz_png/tlibgKYKL9EvQMNxqG4ibbc2DNnTXlTtWoXC5oicuguPC6vKia9zfzKPaRNoNo5gBNCkZUib6k0Lic1vqKujF2BHF5HLqc0U3qUkZtFStSj8HQ8Sk/640?wx_fmt=png&from=appmsg "")  
  
点击提交，鼠标悬停，出现弹窗，存在XSS漏洞  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/tlibgKYKL9Etje1pMsFBtqbFe7jqAcveibk6dxlHaUsWknoiaFU8tfiatwPjNy60CeSnTm2g5KSUWMHXx6YK4laIFibMdrJg6j2aGlX8NqVdWH3g/640?wx_fmt=png&from=appmsg "")  
  
整个系统的功能点均存在该类型的XSS漏洞，最后也是成功拿下3个XSS漏洞  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/tlibgKYKL9Ev3njqLNgh6ibygm9icKz2lsXa2dgiaic9Tn0uXdfAT2jqwgrzgGosO2iaVPicDbAzAeAYq1cPVhRtgOy9EGTvXhicSGCpB9iaj2icsteOQ/640?wx_fmt=png&from=appmsg "")  
  
## 供应链思路拿下其他学校  
  
  
故事的开始是煮包不是在复现一个服务号的SQL注入案例嘛，然后煮包想了一下既然这个学校存在SQL注入，那接管的其他学校应该也存在SQL注入，煮包就去微信搜了一下其他的学校的服务号  
  
![](https://mmbiz.qpic.cn/mmbiz_png/tlibgKYKL9Et7dr3M6pq0z22FW31SEsDZFerTpZllFdic1G1xaTxTic9FPz5GGrRplLZiaRmZBsg1yk4bs9PYAqezLQhN3pgtibzibV6JakEvnx5U/640?wx_fmt=png&from=appmsg "")  
  
结果还真让煮包搜到了好几个学校也有类似的服务号，套路和复现的案例是一模一样的，也是智*校园处存在SQL注入  
  
![](https://mmbiz.qpic.cn/mmbiz_png/tlibgKYKL9Ev4GmBvdhonRKaUBDic4IkOvJvjVywStv6kChwKo38fX7UsRKgFxgjSyoWsiaWjcFVM6W8TaiaK6hllpfDvj33OSm0ticiaQ3ticvfiao/640?wx_fmt=png&from=appmsg "")  
  
报错注入成功注入出user，四个注入都是一模一样的，就不一一展示了  
```
125-updatexml (1, user,1)
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/tlibgKYKL9EuWOtoVLonENfq6jbvJ4GB42CZa8X8E4vMicO4H0XP4c7pWGkOvbP0Km4on8PPSZ4vrKSMwtrlgUMTskzjhCbyw9et6JAEqEYg8/640?wx_fmt=png&from=appmsg "")  
  
最后也是成功拿下六个站的SQL注入  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/tlibgKYKL9EvJ1w1tvY5APIibw820eHJMhf2D3AvPlZzVJKjV9mMIPmbALdmBPn2OibTaatkrEfiaD4dgJcMUGM5WUXfbhWW08wQhZwVos6yewQ/640?wx_fmt=png&from=appmsg "")  
  
## 成果展示  
  
  
弱口令由于是同一个系统下的网站，审核的时候全都给的是中危1rank  
  
![](https://mmbiz.qpic.cn/mmbiz_png/tlibgKYKL9Evjj4sm7F47o88Pt3AncuXDwCfmgVtj9aDibX19Giclx8JOYiclZYFEAPkk0mna5YCIJHFlR3ohxQUVZfl7PxItHVBV90LHJdu75s/640?wx_fmt=png&from=appmsg "")  
  
SQL注入由于是不同学校的服务号，所以每个都给了中危2rank  
  
![](https://mmbiz.qpic.cn/mmbiz_png/tlibgKYKL9EuDhFqptMFWTjoHEjkCYEf3FoJgJibiagia3vKYCbs5y3c1BxlWU6PPp5uLHDftpuSm6QcKFSBdCF2rg3bJz8DzHnj50MXROIe0iaM/640?wx_fmt=png&from=appmsg "")  
  
最后也是一个系统刷了二十多rank，所以说复现案例的时候一定要细心一点，说不定就能挖到更深的东西  
  
![](https://mmbiz.qpic.cn/mmbiz_png/tlibgKYKL9Es4OjSy4hmV7WqpNsEc1FANQtPgl5QV5EtLQnBZw9cJPqZ44ricUHeZaNy7Gyibl0bbz4iaZRIdV8gib5dqV8JMEHDAcY0VRefqqI4/640?wx_fmt=png&from=appmsg "")  
  
总结：故事的最后，总结一下这次挖洞的心得吧  
```
1.用bp的时候，可以多看看插件，说不定就有惊喜
2.漏洞被驳回的时候，不要灰心，想想如何进一步深入利用
3.小程序和web是有关联的，有的时候小程序挖不到，可以去web看看有没有方向
有的时候web端挖到了漏洞，也可以考虑一下
是否能够在小程序或者服务号进一步利用
4.漏洞也是相关联的，未授权获取到的管理员手机号可能不够敏感
但是弱口令的时候却能派上大用场
```  
  
往期文章  
  
[记一次edu攻防演练又拿下top2(湘安无事ai辅助版)](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247495313&idx=1&sn=c5de1dd35d54b28a9e1349ac16c58dc5&scene=21#wechat_redirect)  
  
  
[2026最新版burp破解教程+ai操作burp挖洞](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247495278&idx=1&sn=37563ab9c3aa7dbd0032d668c444579c&scene=21#wechat_redirect)  
  
  
[湘安无事首推的0基础web安全课程](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247495233&idx=1&sn=c213276f0a0e3114fadb2ee693967c5b&scene=21#wechat_redirect)  
  
  
[学员挖掘母校实战案例+4月湘安漏洞库平台优秀实战案例](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247495226&idx=1&sn=964c5d38716a725c251affa609579d9c&scene=21#wechat_redirect)  
  
  
[从逆向加密逻辑到一键明文改包：我的 Yakit Hotpatch Skill 实战](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247495182&idx=1&sn=c00c7fed753d2eb5c28c7609ce1706e6&scene=21#wechat_redirect)  
  
  
["深入探究JWT：解锁身份验证的挖洞小技巧"](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247495180&idx=1&sn=6d13c243db60ec14db5c9e8dae12b8b0&scene=21#wechat_redirect)  
  
  
[敏感信息泄露漏洞总结：深情哥提醒你aksk正在“裸奔”](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247495067&idx=1&sn=47d93faf7fa2632bdedef8da9396bf04&scene=21#wechat_redirect)  
  
  
[985–edu证书案例之有意思的报告](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247495097&idx=1&sn=dcb8c4a665b2030bf0e86d042f687ef6&scene=21#wechat_redirect)  
  
  
[五一弯道超车！深情版Edu+SRC培训限时低价，文末免费抽奖(两份kfc+五个无影激活码)](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247495149&idx=1&sn=9250a9e7ac52ec71c4a492ec77645426&scene=21#wechat_redirect)  
  
  
[记母校漏洞测试一次waf绕过经历](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247494963&idx=1&sn=a3402602ab048ef32c1bc7f36d702dde&scene=21#wechat_redirect)  
  
  
[Codex-AI 道德审查绕过进行js逆向](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247494987&idx=1&sn=7344cac0a26228596f0e267793aca865&scene=21#wechat_redirect)  
  
  
[究极无敌的srcAI-xss手法（快看过来）](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247495003&idx=1&sn=fcd19fb5f7f950a3e7a15785577cd738&scene=21#wechat_redirect)  
  
  
[记一次差点进编制的漏洞测试](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247494898&idx=1&sn=9f9c5a3527c8330f2e4312eadf0ce863&scene=21#wechat_redirect)  
  
  
[新版微信强开f12和新版本微信反编译](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247494896&idx=1&sn=ac8ebcaecd5e18d45f79c7b6d8e3b10c&scene=21#wechat_redirect)  
  
  
[湘安无事2025团队和培训总结-福利抽奖](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247494849&idx=1&sn=a7084a23faa401c4fbcb06af4650846a&scene=21#wechat_redirect)  
  
  
[985证书漏洞越权成为教授 + EDU/RCE漏洞实战解析｜湘安内部平台月榜 TOP3 案例](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247494786&idx=1&sn=9ee660a005413f34e6d89f728df59803&scene=21#wechat_redirect)  
  
  
[服务号存在注入之有意思的edu漏洞](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247494659&idx=1&sn=e357db59d806c93f3a5a36b5c312b335&scene=21#wechat_redirect)  
  
  
[湘安无事之湘潭大学冬令营总结](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247494727&idx=1&sn=1d17ba9ee9bcd5fc4cfabe12d463e4da&scene=21#wechat_redirect)  
  
  
[赏金src报告分享&&edu证书站漏洞分享](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247494759&idx=1&sn=65084c580bb13fa31f82985721147e7e&scene=21#wechat_redirect)  
  
  
[edu小灶案例之泄露上w敏感信息&有意思的证书站报告案例](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247494702&idx=1&sn=5f0d05b6bea8550eda36ed2ce8ddbf7a&scene=21#wechat_redirect)  
  
  
[学员投稿之edu漏洞的JS逆向解密导致任意密码重置](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247494634&idx=1&sn=7cc83a90badea88d7d7d25583bd0c419&scene=21#wechat_redirect)  
  
  
[最近学员小灶总结之双十二特惠](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247494604&idx=1&sn=80f030c8114ee3287036586c670289c9&scene=21#wechat_redirect)  
  
  
[卡顿页面导致三本edu证书现世之学员案例分享](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247494572&idx=1&sn=14c21f1cae87dbdbf2ca5c4b10533f5d&scene=21#wechat_redirect)  
  
  
[看完这场EDU通杀刷屏，连我自己都沉默了](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247494535&idx=1&sn=113dd5e17065def94a5dacad361176b3&scene=21#wechat_redirect)  
  
  
[edu证书站挖掘之学员分享案例](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247494524&idx=1&sn=bff39f27ea27bccb884e832603acda92&scene=21#wechat_redirect)  
  
  
[如何快速挖掘低微漏洞-项目挖掘总结版](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247494500&idx=1&sn=2da7aa0d40075b462eb27695f2da9e83&scene=21#wechat_redirect)  
  
  
[公众号接管漏洞之偷偷加](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247494345&idx=1&sn=6d9ab80cde99e95c4d1ac710e091ffdd&scene=21#wechat_redirect)  
  
[小姐姐微信](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247494345&idx=1&sn=6d9ab80cde99e95c4d1ac710e091ffdd&scene=21#wechat_redirect)  
  
  
[辅助学员审计案例-php代码审计](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247494284&idx=1&sn=2feaf2786717c2c577fe0b5230cbc58f&scene=21#wechat_redirect)  
  
  
[学员-补天800赏金报告分享](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247494284&idx=2&sn=c54ea91e0b3dc79f48f54f931df413c0&scene=21#wechat_redirect)  
  
  
[从js逆向到sql注入waf绕过到net审计-edu证书漏洞](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247494171&idx=1&sn=0d589d01dfbb068e53f9ad9c22676d7a&scene=21#wechat_redirect)  
  
  
[空白页面引起的高危src漏洞-再次绕过](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247494082&idx=1&sn=6d1cc92a049d28b5395d417e0a5203a5&scene=21#wechat_redirect)  
  
  
[空白页面引起的高危src漏洞](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247494061&idx=1&sn=bfb1406138041fcba8286811aa7ac6e1&scene=21#wechat_redirect)  
  
  
[难忘的优惠劵漏洞(深情哥破防版)](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247494035&idx=1&sn=422326cc617c33c78b3d518471e0862d&scene=21#wechat_redirect)  
  
  
[什么？又日母校？竟然还有表扬](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247493987&idx=1&sn=f8edc2f3a113591e6e4cc57fdf821c8f&scene=21#wechat_redirect)  
  
[信](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247493987&idx=1&sn=f8edc2f3a113591e6e4cc57fdf821c8f&scene=21#wechat_redirect)  
  
  
[sql server注入靶场搭建](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247493947&idx=1&sn=885ee489f74211ba26a4a097f7ba30d6&scene=21#wechat_redirect)  
  
  
[记一次学校ai沦为我的宠物之拿下e](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247493890&idx=1&sn=380e9dd566b29b713993b26f524fe0e4&scene=21#wechat_redirect)  
  
[du证书](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247493890&idx=1&sn=380e9dd566b29b713993b26f524fe0e4&scene=21#wechat_redirect)  
  
  
[疯狂星期四两本证书漏洞合集](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247493926&idx=1&sn=06ac298909e27d0e37fef90f64b205da&scene=21#wechat_redirect)  
  
  
[湘安无事之深情哥版edu+src培训](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247493855&idx=2&sn=bb025b6a32bc022319f8dc8057a6436d&scene=21#wechat_redirect)  
  
  
[记两次js逆向拿下985证书站(学员投稿)~](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247493855&idx=1&sn=0d507bc1f90b04588393698ee1c2d4b8&scene=21#wechat_redirect)  
  
  
[重生之我在教育园暴打小朋友](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247493410&idx=1&sn=17df8a525145bf062ed5d6ecc3e09539&scene=21#wechat_redirect)  
  
  
[某医院微信小程序签名机制绕过分析](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247493408&idx=1&sn=672ff76188ab6c97bc1b8d688ac7228f&scene=21#wechat_redirect)  
  
  
[记二次帮学员拿下edu证书站](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247493399&idx=1&sn=425e84fa877f789598a2e8afdf8320c6&scene=21#wechat_redirect)  
  
  
[记一次难忘的net直播审计](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247493369&idx=1&sn=e10ae1cd25ece5fe3c3ca4c2b455cb51&scene=21#wechat_redirect)  
  
  
[记一次小米-root+简易app抓包(新手)](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247493374&idx=1&sn=7dea1086256be1d5a2b1a18fa4ab3be0&scene=21#wechat_redirect)  
  
  
[记一次带学员渗透母校](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247493292&idx=1&sn=7b1881174201094a5449fe1400ddc8a6&scene=21#wechat_redirect)  
  
  
[同学，你试过交edu漏洞交两天两夜嘛](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247493292&idx=2&sn=0d706a4064111b3e131f24460b2f2c56&scene=21#wechat_redirect)  
  
  
[手把手带学员拿下浙大edu证书](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247493292&idx=4&sn=cd157b6471a1ab1e63b8dbfe0f62b42e&scene=21#wechat_redirect)  
  
  
[记一次手把手带学员拿下600赏金](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247493151&idx=1&sn=3e1a791a4d14b9ad21d2f0afe8a455a2&scene=21#wechat_redirect)  
  
  
[深情版edu+src培训讲解(3000)](https://mp.weixin.qq.com/s?__biz=MzU3Mjk2NDU2Nw==&mid=2247494535&idx=2&sn=9dfb9ceda7361c9523471a64d8f16f5b&scene=21#wechat_redirect)  
  
  
最后总结  
  
感兴趣的可以联系深情哥进群  
，有公开课会在群里面通知，包括审计和src。  
edu邀请码获取，咨询问题，hvv渠道推荐，nisp和cisp考证都可以联系深情哥  
。  
  
**内部edu+src培训，包括src挖掘，edu挖掘，小程序逆向，js逆向，app渗透，导师是挖洞过50w的奥特曼深情哥，edu上千分的带头大哥！！！联系深情哥即可。**  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/tlibgKYKL9EuCDvDrD2X9KLUPqNn5HwlRHHaCPicV7ubs6JaHMw5nicyWs0I0QIqsIficrTiaommBibnZ0S70VVOhXdpfHBmNI1bOcElshBuR4KibY/640?wx_fmt=png&from=appmsg&watermark=1&wxfrom=5&wx_lazy=1&tp=webp#imgIndex=22 "")  
  
  

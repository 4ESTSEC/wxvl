#  Pwn2Own 2026首日赛果出炉，32个0Day攻破三星S26、OpenAI Codex等产品  
 FreeBuf   2026-10-08 10:00  
  
![FreeBuf](https://mmbiz.qpic.cn/sz_mmbiz_gif/icBE3OpK1IX0QYicHRDhC9m1qhTbQKZBRl7tLKv3ld6v6s19J6NhZa8Pt3EWrQommucsERdib4KRMdSaT0ISiaz4QjdshbvZYjH4Jc4hR7e4KBk/640?wx_fmt=gif "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/icBE3OpK1IX2gfWyJvz2ldDD5e7UZTO3TcL4PLicuCAkY8qTbW31s9LLtfMic5ftJV6eiaEEASraQhLxgU8QQnze2SBrW73FS4blOMhW7rOhC5U/640?wx_fmt=png&from=appmsg "")  
  
  
据报道，在2026年Pwn2Own爱尔兰站开赛首日，安全研究人员共利用32个独立0Day漏洞完成攻破，累计获得38.85万美元奖金。参赛队伍先后攻破三星Galaxy S26、OpenAI Codex、智能家居设备及多款AI服务，但Google Pixel 10的攻击尝试在比赛时限内未能成功。  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/icBE3OpK1IX08VrzhYreq2ZExJDHxofENamUE1l50Q11OW3EYBIDtMrFEQaIWRniblciccL7MlIkicluZ6ic8ZTiaHniaHxWicwEvFIRucSmxCzOyjA/640?wx_fmt=png&from=appmsg "")  
  
  
10月6日的赛事结果显示，安全漏洞广泛存在于手机、联网设备以及AI系统开发所用的软件中。ZDI公布首日共有21个参赛项目，多起成功攻击都结合了新发现的漏洞与厂商已知但未修复的缺陷。  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX0Um6drNlk1L73oI2G23BujMyeSzX9sXiboENQz2JJP7wRFdOibpHfwH0tbGRJ5LYwywL6OAu0w7BntHiaBfib9M76eGaoGjQVvGQ4/640?wx_fmt=png&from=appmsg "")  
  
  
Part  
01  
  
手机类靶标赛果出炉  
  
  
根据ZDI官方公布的结果，共有三支队伍成功攻破三星Galaxy S26。每支队伍的攻击链都用到4个漏洞，但由于部分漏洞与此前已上报的缺陷重合，各队获得的奖金数额存在差异。  
  
  
Viettel Cyber Security的Nguyen Thanh Dat在攻击中结合1个新漏洞与3个厂商已知漏洞，拿下3.125万美元奖金。Interrupt Labs的攻击链包含1个0Day和3个撞洞漏洞，获得1.575万美元奖金。Ikotas Labs的攻击用到3个新漏洞和1个三星已知但未修复的漏洞，获得1.1万美元奖金。  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX2WDicc5NL8oUicn8eOBq5MARYP98m3Kh9u7dGfuCckP7ZzyOpOGc6MPaPpicsl39VUU4wHSB7ZHvVX4tG2J2q6uJ9J4fYib9UPZpA/640?wx_fmt=png&from=appmsg "")  
  
  
这些结果凸显了赛事规则中的一项重要区分：攻击成功不代表漏洞链中的每个漏洞都是全新的。ZDI会将不同队伍重叠发现的漏洞判定为撞洞，仍会认定攻击有效，但会相应下调奖金金额。  
  
  
Google Pixel 10的攻击结果则不同。White Noise Club的研究人员Mikhail Evdokimov、Polina Smirnova、Mate Zombor未能在规定时间内完成攻击流程。需要说明的是，这一结果并不能证明该手机不存在安全漏洞。  
  
  
Part  
02  
  
多款AI服务遭攻破  
  
覆盖开发工具、数据库等品类  
  
  
Ikotas Labs利用一个参数注入漏洞攻破OpenAI Codex，拿下4万美元奖金。ZDI在首日报告中仅公布了该漏洞的类型，未披露完整攻击步骤、受影响版本，也未分配对应的CVE编号。  
  
  
Xint的Taisic Yun结合输入验证不当缺陷与代码注入漏洞，在LiteLLM上获取反向shell，拿下4万美元奖金。反向shell可以让研究人员从目标系统建立返回的命令连接，证明攻击者已经获得超出普通应用报错层面的深层访问权限。  
  
  
Out of Bounds队伍利用4个漏洞（其中2个为已知漏洞）攻破LiteLLM，获得1.5万美元奖金。与此同时，VinSOC利用5个漏洞组成的攻击链攻破Oracle Autonomous AI Database，拿下4万美元奖金。该队伍针对Chroma的攻击尝试未能在规定时间内完成。  
  
  
Part  
03  
  
多款智能设备被攻破  
  
涉及音箱、打印机等品类  
  
  
VinSOC的研究人员Vũ Chí Thành、Huỳnh Đức Tin在攻破Philips Hue Bridge Pro的过程中，共披露7个0Day，拿下4万美元奖金。其他针对Hue设备的成功攻击大多利用已知漏洞，这也说明赛事统计中必须将被利用的漏洞总数，与独立新发现的漏洞数分开计算。  
  
  
McCaulay Hudson组合利用越界写入漏洞与格式化字符串漏洞攻破Sonos Era 300，拿下5万美元奖金。这两类漏洞分别涉及不安全的内存访问与格式化文本处理问题，不过ZDI未披露具体的攻击技术细节。  
  
  
Team Confused的Thanh Do、Summoning Team的Sina Kheirkhah分别攻破Lexmark CX532adwe打印机。Interrupt Labs还组合利用越界读写漏洞攻破Garmin Index BPM，拿下2万美元奖金。  
  
  
所有攻破演示均在赛事规则框架下开展，仅用于比赛比拼，不能证明有攻击者针对真实用户发起过相关攻击。  
  
  
参考来源：  
  
32 Unique 0-Days Exploited in Samsung S26, Pixel 10, OpenAI Codex and Other Devices in Pwn2Own 2026  
  
https://cybersecuritynews.com/32-0-days-pwn2own-2026/  
  
###   
  
### 推荐阅读  
  
  
[](https://mp.weixin.qq.com/s?__biz=MjM5NjA0NjgyMA==&mid=2651347479&idx=1&sn=1cf9c858a30d0b7af06d196002bd2913&scene=21#wechat_redirect)  
  
  
###   
  
### 电报讨论  
  
  
[]()  
  
  
  
![扫码加入AI安全交流群](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX0Py7ibxdLKXia1pMziaic5vIE9XPXG9OGaeJDa07iaG10eicuzhW59nwpF5msHiaYZvfMqCNkx2aFDiaMzm3oAf4rTaHXU5UAI1mUYgts/640?wx_fmt=png "")  
  
  
  
![下载FreeBuf知识大陆APP](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX0TIGzII2Hcmtzu7AJeZFicnqd1mXojVoawje2uLxYqwJbVgzJpmSXzVhrpOsLurRZ2lVa4vfgLBqg7uJKbrKg5F18VzZxVPicZU/640?wx_fmt=png "")  
  
  
  
  

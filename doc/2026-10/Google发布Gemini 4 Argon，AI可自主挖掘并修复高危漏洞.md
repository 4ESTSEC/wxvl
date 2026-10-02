#  Google发布Gemini 4 Argon，AI可自主挖掘并修复高危漏洞  
 FreeBuf   2026-10-02 10:00  
  
![FreeBuf](https://mmbiz.qpic.cn/mmbiz_gif/icBE3OpK1IX29NmxQvJoGUdC49QEFm5h6ZK0X6vCbquBWiaumxg4RvicvszFThLGDAKV368stOUGgCJyGiaEiaOdwKJcQCxQPiaN0GchurMn2rYX4/640?wx_fmt=gif "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/icBE3OpK1IX1BOuwQsZqibzWdGCp9K64fFEWN8FPVY4300ia43r0QcwicKRt2v9hDLEyWjT18dOuWgWY2jAibmhz1x7x0vXnfp62Z7WrGyKNyR4c/640?wx_fmt=png&from=appmsg "")  
  
  
Google发布全新前沿AI模型Gemini 4 Argon，目前正通过Fairwind项目向一批可信网络防御者开放推送。Google表示，该模型可在无人工协助的情况下定位、验证并修复关键软件漏洞，后续还将向上述防御者及内部团队提供移除网络安全护栏的版本。  
  
  
开发者、企业及普通消费者需等待后续开放，首批推送对象为付费API客户与Google AI Ultra订阅用户。Google表示，这类能力等级的模型必须采用分阶段发布策略才能保障安全，目前团队仍在根据早期测试者的反馈调整安全护栏，同时参与美国政府推出的模型预发布自愿接入流程。  
  
  
大范围发布前，Google正在加固安全防护机制，防止模型被滥用于网络攻击或化学、生物、放射性、核相关恶意活动。目前内外部红队（即通过攻击系统查找弱点的团队）已完成相关防护测试。  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX0BkOdXlnDffyxGicgY1KDOWC6hJAZbN4LfzdmE8oBAAz5L46fibfzXa4jFTYW7TP2QibO8anlOeGJzMzAw5Sicc1lkVjBOuakvwmI/640?wx_fmt=png&from=appmsg "")  
  
  
Google还部署了监控机制，全程跟踪Argon的推理过程与操作行为，必要时可直接中止运行。  
  
  
Part  
01  
  
输出上限提至百万token  
  
  
Google将Argon的单次响应输出上限从6.4万token提升至100万token。官方表示，充足的输出空间可支撑模型单次运行完成数十万token的思考与内容生成。  
  
  
云安全公司  
Wiz正通过旗下Scan for Good项目使用Argon，该项目面向关键公共基础设施免费扫描高风险暴露面并修复相关问题。Google称，Argon在一款全球医院广泛使用的医疗软件中发现了一处泄露敏感个人信息的关键漏洞，此前的前沿模型均未发现该漏洞。官方未透露这款软件的名称，也未说明漏洞是否已完成修复。  
  
  
Argon Agent已在Google全数据中心落地内存优化方案，累计释放超过300TiB内存。这些Agent还将libgav1视频解码器中的3.2万行SIMD代码替换为Rust代码——Rust是专为避免内存错误设计的编程语言。重写后的解码器视频输出效果与原版本完全一致，运行速度是此前Rust移植版本的2.7倍。  
  
  
其他Agent正在将C和C++代码重写为Rust代码，涉及Fuchsia Zircon内核的80多万行代码。目前这些重写工作仍处于审核、仿真测试与评审阶段，尚未正式上线生产环境。  
  
  
Part  
02  
  
多项基准测试排名居首  
  
  
Google公布的基准测试数据显示，Argon在三类通用能力测试中表现优异：在长软件工程任务测试集DeepSWE v1.1上得分77.9%，达到业界领先水平；在Zapier AutomationBench自动化测试中得分51.3%，排名第一；在长视频测试集LVBench上得分91.7%，同样处于业界领先位置。  
  
  
在专门测试安全漏洞修复能力的CWE-bench v1测试中，Argon得分68%，与其他模型并列第一。  
  
  
漏洞挖掘相关测试结果来自Google内部，测试覆盖20种编程语言编写的复杂代码库。  
  
  
云安全公司  
Wiz还开展了黑盒渗透测试，要求模型在无源代码的情况下对真实运行的Web系统开展探测。结果显示，Argon在攻击面梳理、漏洞发现、生成概念验证证据三个环节的表现，均优于3.8 Flash Cyber。  
  
  
Part  
03  
  
Argon公布上线定价方案  
  
  
Argon上线初期将执行优惠定价：每百万输入token收费2美元，每百万输出token收费10美元，缓存输入token的价格比普通输入token低95%。  
  
  
优惠期结束后，定价将翻倍至每百万输入token 4美元、每百万输出token 20美元。  
  
  
参考来源：  
  
Google says Gemini 4 Argon can find and patch critical software flaws  
  
https://www.helpnetsecurity.com/2026/10/01/google-gemini-4-argon/  
  
  
### 推荐阅读  
  
  
[](https://mp.weixin.qq.com/s?__biz=MjM5NjA0NjgyMA==&mid=2651347479&idx=1&sn=1cf9c858a30d0b7af06d196002bd2913&scene=21#wechat_redirect)  
  
  
###   
  
### 电报讨论  
  
  
[]()  
  
  
  
![扫码加入AI安全交流群](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX0Py7ibxdLKXia1pMziaic5vIE9XPXG9OGaeJDa07iaG10eicuzhW59nwpF5msHiaYZvfMqCNkx2aFDiaMzm3oAf4rTaHXU5UAI1mUYgts/640?wx_fmt=png "")  
  
  
  
![下载FreeBuf知识大陆APP](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX0TIGzII2Hcmtzu7AJeZFicnqd1mXojVoawje2uLxYqwJbVgzJpmSXzVhrpOsLurRZ2lVa4vfgLBqg7uJKbrKg5F18VzZxVPicZU/640?wx_fmt=png "")  
  
  
  
  

#  Anthropic 点名中国黑客+7家AI巨头：长沙学生用Claude搞出十几个0day，阿里被指薅了1.5亿次  
原创 hacking
                    hacking  Hacking黑白红   2026-09-11 07:49  
  
****  
各位网安圈的兄弟们，这几天是不是被 Anthropic（Claude 的母公司）发的那份 2026 年 9 月威胁情报报告刷屏了？  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/TVljsu2eAicIYnGmzrmZ6Cl8MZskWZehZYcnewuL8DlU0A4WBXTibEia4ICwRSVVhmdEEZd2rLR7icib7icm6ekFpY5Wvx8MLKaeMP52wO8zwjlEk/640?wx_fmt=jpeg "")  
  
  
 这份报告可以说是把咱们中国网安从业者和 AI 圈的脸，按在地上摩擦了一遍。  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/TVljsu2eAicLubFeiaIo3YhhI2bFxCon2kUicDI93BJ2Yr5iae0jktsXytuWO6E1cP8DTxdAPZUkWblXNCrnBGqUWAwlxmZI2KM8icEULz8SElbw/640?wx_fmt=jpeg "")  
  
  
  
今天咱们抛开情绪，用大白话聊聊这份报告到底爆了哪些猛料。  
  
### 🕵️‍♂️ 长沙大学生的“自动化军火库”  
###   
### 报告里给了个编号：GTG-10007。  
  
  
说的是一个讲中文的小团伙，人大概率在**湖南长沙。**  
  
  
里头有两个是某高校计算机与通信工程学院的本科生，还有一个曾在深信服实习、面过奇安信的进攻岗——注意啊，报告**没说这两家厂商参与**  
，别上来就扣帽子。  
  
  
这伙人干的事儿，听着像科幻片：  
  
- 把固件丢进去反编译  
  
- 让Claude提漏洞假设  
  
- 自动写利用代码  
  
- 在沙箱里自己验证  
  
- 24小时扫攻击面、定时扒公开情报  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/TVljsu2eAicI8fj9uU3jXESibRspomaTrPA4Iicibpzc5FgZvwGFJHzIXE9sTl8Izbn25ECG4iccLpkgtQ9sCkMEGLbxRZ5Ba9er5WDHAm7hJPEA/640?wx_fmt=jpeg "")  
  
  
报告原话：  
  
一个月搞出**十几个疑似0day**  
，盯了约50个目标，教培数据、零售生产系统、东南亚政务库全沾了边，还挂了13个定时采集智能体去捞美军方公开材料。  
>   
> 💡 网安人一眼就懂：这不是“黑客”，这是**把Claude当CI/CD用的漏洞作坊**。一个人+智能体，干过去一个红队班的活。  
>   
  
  
### 🏭 阿里、Kimi、DeepSeek 被指“工业级蒸馏”  
###   
  
除了黑客攻击，报告的另一大重头戏是**“非法模型蒸馏”**  
。  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/TVljsu2eAicIBicicbdtaX0FnhTBtQRsKOXTyBoFXRadCiaiaErTH9WBco9W14rFC0JPy5KfUoHQpWVicKlQIDFw9s9CExIp3QzicPzIVyXiaYr6dv0/640?wx_fmt=jpeg "")  
  
  
Anthropic 直接点名了阿里、月之暗面（Kimi）、DeepSeek、智谱、小米、商汤和 MiniMax 这七家中国 AI 公司。  
  
  
没有字节和豆包。  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/TVljsu2eAicJc2PjVgb18JcSdqkGHAe8CAyicZXDaPnTKdc2zXEMh0gjVE6KjyAtc5LicOG0tiaC0JPMctmFkZlQaKJ1z63EulJF7uicwVWoAENM/640?wx_fmt=jpeg "")  
  
  
- **阿里通义千问**  
：被指是 Anthropic 有史以来测到过的**最大规模蒸馏**  
。今年 5 到 7 月，通过 3500 多个假账号，交互次数超过 1.51 亿次，硬生生把 Claude 的推理链薅下来喂给了 Qwen 3.5/3.6/3.7。  
  
-   
-   
- **月之暗面 Kimi**  
：被指在后台偷偷把用户请求转发给 Claude，10 天转发了近 30 万次，还专门建了“思维链提取流水线”。  
  
-   
- **DeepSeek**  
：手法类似，14 天内交互超 1210 万次，被指专门拦截开发者流量转发给 Claude。  
  
网友总结版  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/TVljsu2eAicIAKPRCfs01h8tT9wo99vryoEn2a81aaPEYvZDyDVCrfRuz9bsqrhPCfzL7MY2oB32UtXSxByIgk5RxnguLdW1fl26ZEGYAhx8/640?wx_fmt=jpeg "")  
  
  
网友评论：  
  
“  
没有被点名的安全公司要看看思考一下了，为什么没有被点名。”  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/TVljsu2eAicKP3SFs5FjdpBwao3x1XRXibkx5D0Cg92EKGQvYu2IJ5DKF3ENNQAibicibibrz0oBUBN8T2icoiccT1k6BUIvtPqRJBYV21XJJwZdibU4/640?wx_fmt=jpeg "")  
  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/TVljsu2eAicI3jG7PP0y1RasLjYXn8Q1lPtnqDKqA0H1Mb4C2HBnXbibICqWTMXEU42nhewdB4ZVgTludibjmfOXwIcDiaK5J43iab0Z9zWT9m7E/640?wx_fmt=jpeg "")  
  
###   
### ⚠️ 咱们该怎么看这事？  
###   
  
看完报告，我只想说两点大实话：  
  
  
第一，**AI 降低了作恶的门槛**  
。以前搞网络攻击，得是顶尖高手；  
  
  
现在，几个懂点代码的学生，靠着大模型编排，就能搞出堪比国家级黑客组织的动静。  
  
  
这对咱们网安行业的防御体系提出了前所未有的挑战。  
  
  
第二，**别把“技术争议”直接等同于“实锤定罪”**  
。  
  
  
Anthropic 这份报告是单方面指控，目前还没有看到被点名公司的官方详细回应，也没有第三方独立审计机构的背书。  
  
  
特别是蒸馏这块，在 AI 圈本身就游走在“学习借鉴”和“窃取知识产权”的灰色地带，到底怎么界定，还需要更多事实说话。  
  
  
但不管怎样，大模型时代的网络攻防战，已经不是科幻电影，而是咱们每天要面对的真实战场了。  
  
  
各位同行，提高警惕吧！  
  
  
  

#  【安全圈】Cisco 确认 Unified CM 漏洞已在野外被利用  
 安全圈   2026-07-03 11:00  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/aBHpjnrGylgOvEXHviaXu1fO2nLov9bZ055v7s8F6w1DD1I0bx2h3zaOx0Mibd5CngBwwj2nTeEbupw7xpBsx27Q/640?wx_fmt=other&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1 "")  
  
  
**关键词**  
  
  
  
漏洞  
  
  
****  
Cisco 证实，其 Unified Communications Manager (Unified CM) 和 Unified Communications Manager Session Management Edition (Unified CM SME) 中最近修补的一个漏洞已在野外被利用。  
  
  
该安全缺陷被追踪为 CVE-2026-20230（CVSS 评分 8.6），被描述为对特定 HTTP 请求的验证不当，这可能允许攻击者发起 SSRF 攻击。  
  
  
成功利用该漏洞可能导致任意文件被放置到底层操作系统上，随后可用于获取 root 访问权限。  
  
  
Cisco 表示，只有启用了 WebDialer 服务的设备易受攻击。该服务默认处于禁用状态。  
  
  
6月初，Cisco 为 Unified CM 和 Unified CM SME 版本 14SU6 推出了针对该 CVE 的补丁，并宣布修复程序也将包含在预计于9月发布的版本 15SU5 中。  
  
  
Cisco 曾警告称，存在针对该漏洞的概念验证 (PoC) 代码，但表示当时并未意识到其在野外被利用。  
  
  
周三，该公司更新了其公告，警告客户该安全缺陷正在攻击中被积极利用。  
  
“Cisco 继续强烈建议客户升级到已修复的软件版本以消除此漏洞，”该公司表示。  
  
  
这一警告是在漏洞情报公司 Defused 报告看到“来自单一来源使用未经审查的 PoC”的利用行为，以及 credited 发现该漏洞的 SSD Secure Disclosure 发布技术信息和 PoC 一周后发出的。  
  
  
当时，Cisco 告诉 SecurityWeek，它并未意识到该安全弱点有任何恶意使用。  
  
  
  END    
  
  
阅读推荐  
  
  
[【安全圈】DeepSeek又崩了](https://mp.weixin.qq.com/s?__biz=MzIzMzE4NDU1OQ==&mid=2652077635&idx=1&sn=8ce36247d6600124679f36ce800fb293&scene=21#wechat_redirect)  
  
  
  
[【安全圈】Chrome 382漏洞速更！](https://mp.weixin.qq.com/s?__biz=MzIzMzE4NDU1OQ==&mid=2652077635&idx=2&sn=ff1b58c3db45fe363feb913cafcb6c3b&scene=21#wechat_redirect)  
  
  
  
[【安全圈】FortiBleed 凭证窃取活动与 Lynx 勒索软件有关联](https://mp.weixin.qq.com/s?__biz=MzIzMzE4NDU1OQ==&mid=2652077635&idx=3&sn=83fbcc4593627fac3961b43fc28d3294&scene=21#wechat_redirect)  
  
  
  
[【安全圈】Claude被曝暗检中国用户](https://mp.weixin.qq.com/s?__biz=MzIzMzE4NDU1OQ==&mid=2652077617&idx=1&sn=2a61e7a7472ab85e4f5f8dc2bab2526d&scene=21#wechat_redirect)  
  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/aBHpjnrGylgeVsVlL5y1RPJfUdozNyCEft6M27yliapIdNjlcdMaZ4UR4XxnQprGlCg8NH2Hz5Oib5aPIOiaqUicDQ/640?wx_fmt=gif "")  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/aBHpjnrGylgeVsVlL5y1RPJfUdozNyCEDQIyPYpjfp0XDaaKjeaU6YdFae1iagIvFmFb4djeiahnUy2jBnxkMbaw/640?wx_fmt=png "")  
  
**安全圈**  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/aBHpjnrGylgeVsVlL5y1RPJfUdozNyCEft6M27yliapIdNjlcdMaZ4UR4XxnQprGlCg8NH2Hz5Oib5aPIOiaqUicDQ/640?wx_fmt=gif "")  
  
  
←扫码关注我们  
  
**网罗圈内热点 专注网络安全**  
  
**实时资讯一手掌握！**  
  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/aBHpjnrGylgeVsVlL5y1RPJfUdozNyCE3vpzhuku5s1qibibQjHnY68iciaIGB4zYw1Zbl05GQ3H4hadeLdBpQ9wEA/640?wx_fmt=gif "")  
  
**好看你就分享 有用就点个赞**  
  
**支持「****安全圈」就点个三连吧！**  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/aBHpjnrGylgeVsVlL5y1RPJfUdozNyCE3vpzhuku5s1qibibQjHnY68iciaIGB4zYw1Zbl05GQ3H4hadeLdBpQ9wEA/640?wx_fmt=gif "")  
D  
  

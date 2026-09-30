#  Bitget披露3.88亿美元被盗细节，第三方安全产品0Day成入侵入口  
 FreeBuf   2026-09-30 10:00  
  
![FreeBuf](https://mmbiz.qpic.cn/mmbiz_gif/icBE3OpK1IX0icHX1onbG8Y5MwCM0FNrerFpvH42oqUaVPibNtcv6WyPBVHdBicEcLOOy3wspOv2U8VFLibBicUGca81XFqtDpZb7jmcpZwkknu2I/640?wx_fmt=gif "")  
  
![image](https://mmbiz.qpic.cn/sz_mmbiz_png/icBE3OpK1IX0mUw15Y9fqpRVpiaicFzCXAN5EuvQnXdyLribQiajaSphKc3E9o4NejoPtmLs3avESjxvscILOrxH2hndcq0wNZ7MLcOZHbAmnH50/640?wx_fmt=png "")  
  
  
加密货币交易所Bitget周一表示，攻击者从该平台盗走约3.88亿美元，初始入侵路径是其部署的一款第三方安全产品存在漏洞。  
  
  
Part  
01  
  
攻击者利用第三方产品0Day漏洞  
  
  
攻击者利用该漏洞获取了高权限内部凭证。9月24日，攻击者使用这些凭证向Bitget钱包系统发送伪造的提现指令。  
  
  
加密货币交易所通常会将绝大多数用户资金存储在离线冷钱包中，仅使用热钱包和温钱包处理提现业务。所有从热、温钱包转出的资金，在签名前都必须经过审批流程。此次被盗资金全部来自Bitget的部分热钱包和温钱包，冷钱包未受任何影响。  
  
  
Bitget上周曾披露，其钱包基础设施中的一套核心后端系统遭攻击者入侵，被用来伪造交易数据、触发审批流程，但当时并未公布攻击者的初始入侵路径。  
  
  
Bitget首席执行官Gracy Chen周一公布了此次攻击的完整细节，相关表述分别来自一场公开直播、她接受The Block的采访，以及对Cointelegraph的回应。攻击者通过该漏洞接入内部管理系统，向钱包相关后端服务注入伪造的提现指令。这些指令被系统判定为合法操作，没有触发拦截。  
  
  
Part  
02  
  
攻击者分阶段转账绕过风控机制  
  
  
9月24日UTC 18:31，攻击者首先发起两笔小额测试转账。这两笔交易金额低于Bitget的风控阈值，没有触发任何警报。约30分钟后，攻击者开始发起大额转账，Bitget钱包系统执行了这些转账操作，全程绕过了风控机制。  
  
  
U.Today报道援引Chen的表述称：“攻击者全程使用合法凭证，将恶意活动伪装成常规管理操作，同时清除了操作痕迹。”  
  
  
Bitget称，根据目前的调查结果，本次攻击未造成任何私钥泄露。Chen在周一的公开表述中没有透露该第三方安全产品的具体名称。据The Block消息，她表示本次被利用的是一个0Day漏洞，即漏洞厂商尚未发布修复补丁、就已被攻击者利用的安全缺陷。据Crypto Briefing报道，Bitget已经通知了该产品厂商，隔离了受影响系统，作废并重新签发了内部凭证，同时在漏洞修复完成前关闭了相关功能，目前尚未披露厂商是否已发布漏洞补丁。  
  
  
目前上述攻击细节均由Bitget单方面披露，安全厂商Mandiant和SlowMist正在协助调查，Bitget预计将于本周发布正式的事件报告。  
  
  
事件发生后，Bitget已经收紧了内部访问权限，新增了提现独立校验环节，同时加强了异常活动监控，还计划重新评估第三方安全产品的审核与部署流程。  
  
  
Bitget表示，本次事件未影响用户账户余额。平台专门为这类安全事件设立的保护基金将全额承担本次损失。  
  
  
比特币提现服务已于周一恢复，其他资产的提现服务将在10月2日前分阶段逐步恢复，用户无需进行任何操作。  
  
  
Part  
03  
  
攻击疑似指向朝鲜黑客组织  
  
  
Chen在接受The Block采访时表示，Bitget上周曾指出攻击来自朝鲜黑客，目前仍怀疑是“同一批人员”所为。在正式事件报告发布前，她不会透露该组织的具体名称。  
  
  
区块链分析机构TRM Labs上周表示，他们发现被盗资金的流向，与此前朝鲜相关盗窃事件中用于洗钱的钱包存在重叠。这些线索指向朝鲜攻击组织TraderTraitor，但TRM Labs尚未最终确认攻击来源。  
  
  
Bitget已经公布了接收被盗资金的主要地址，同时上线了实时追踪看板。平台已请求各交易所、稳定币发行方、跨链桥、托管机构及其他基础设施提供商监控这些地址，通过其资产追回门户上报相关线索。  
  
  
Bitget在9月25日公布的地址如下：  
  
- 以太坊及EVM兼容网络： 0x770b10b273fc44fe9197d6bf20f145c2e98463ee  
  
  
- XRP网络： rwNhefsz1UQEusxhCvHip3RANinWi4CTck  
  
  
- Zcash网络： t1WgMdtND8NF7NDUuYmq8MpMj1NTCXkMDVG  
  
  
- TRON网络： TBWNguTTgezw9dVorX441C6nDrZpRxYwKD  
  
  
TRM Labs上周建议各交易所，不仅要筛查来自标记攻击者地址的直接转账，还要筛查经过多个中间钱包流转、源自这些地址的资金，做好入金检测。  
  
  
目前被盗资金正通过跨链桥和跨链兑换服务转移，因此更有可能以间接方式流入交易所。  
  
  
参考来源：  
  
Bitget Says Attacker Exploited Third-Party Security Product Flaw to Steal $388M  
  
https://thehackernews.com/2026/09/bitget-says-attacker-exploited-third.html  
  
###   
  
### 推荐阅读  
  
  
[](https://mp.weixin.qq.com/s?__biz=MjM5NjA0NjgyMA==&mid=2651347479&idx=1&sn=1cf9c858a30d0b7af06d196002bd2913&scene=21#wechat_redirect)  
  
  
###   
  
### 电报讨论  
  
  
[]()  
  
  
  
![扫码加入AI安全交流群](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX0Py7ibxdLKXia1pMziaic5vIE9XPXG9OGaeJDa07iaG10eicuzhW59nwpF5msHiaYZvfMqCNkx2aFDiaMzm3oAf4rTaHXU5UAI1mUYgts/640?wx_fmt=png "")  
  
  
  
![下载FreeBuf知识大陆APP](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX0TIGzII2Hcmtzu7AJeZFicnqd1mXojVoawje2uLxYqwJbVgzJpmSXzVhrpOsLurRZ2lVa4vfgLBqg7uJKbrKg5F18VzZxVPicZU/640?wx_fmt=png "")  
  
  
  
  

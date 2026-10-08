#  OpenAI曝出沙箱逃逸漏洞，无需API密钥就能免费调用付费模型  
 FreeBuf   2026-10-08 10:00  
  
![FreeBuf](https://mmbiz.qpic.cn/mmbiz_gif/icBE3OpK1IX0dUibZLusYJCIpgHLRu2QlEPNCIYS6onJAvW21LQwY3zWFLon5u1kibFiatcqfC3Lscv2rXPib11MWI8mpnMERHAv7DMHYBFY3BnM/640?wx_fmt=gif "")  
  
  
![OpenAI沙箱逃逸漏洞相关截图](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX0nsOCshjJE1lvl2lM8gqwTWM7bVnMH8TalEvsb7CVSUUJaO6rGoSxNndOrEw8fWiblOM1yS0iaYeaknCx1zswGibVUX81EHJKqI0/640?wx_fmt=png "")  
  
  
安全研究员Oliver Fish表示，他发现OpenAI存在一处沙箱逃逸漏洞，无需API密钥甚至账号，就能调用OpenAI的付费AI模型。网上流传的截图显示，OpenAI针对这份题为《未认证沙箱逃逸可访问OpenAI内部Responses API》的漏洞报告，向他发放了300美元的奖励。  
  
  
这一漏洞同时突破了两层安全边界：沙箱隔离机制与API身份认证机制。根据OpenAI开发者指南的要求，开发者发起请求前必须创建API密钥；而OpenAI的Responses API主要提供模型与工具访问能力，支撑Agent工作流运行。  
  
  
如果Fish披露的情况属实，远程攻击者可以通过内部路径发送模型调用请求，绕过常规的身份校验与计费流程。  
  
  
Part  
01  
  
漏洞技术细节未公开  
  
暂未发现大规模利用迹象  
  
  
目前该漏洞的技术细节尚未公开，现有公开资料中未披露PoC代码、存在漏洞的接口地址、受影响模型列表、CVE编号、漏洞暴露时长以及补丁相关说明。  
  
  
目前也没有公开证据表明攻击者获取了客户数据，或是有人在Fish的测试之外利用过该漏洞。因此这一发现目前仅属于研究员的个人披露，不能认定为已确认的大规模数据泄露事件。  
  
  
Part  
02  
  
漏洞奖励引发研究员公开不满  
  
  
300美元的奖励金额引发了广泛争议。OpenAI表示，其在Bugcrowd平台运营的漏洞奖励计划会根据漏洞严重程度与影响范围发放奖金，低危漏洞奖励从200美元起，特别严重的漏洞最高可获2万美元奖励。本次奖励金额较低，可能意味着OpenAI评定的漏洞实际影响远低于报告标题描述的等级，但公司尚未解释这一评定依据。  
  
  
Oliver Fish于2026年10月7日在X上写道：“一个无需API密钥或账号就能免费访问OpenAI付费模型的漏洞，只值300美元。我以后再发现任何问题，都绝对不会报给他们了。”  
  
  
Part  
03  
  
多方需落实  
沙箱安全  
防护措施  
  
  
沙箱安全是AI服务领域的重点关注问题。Cyber Security News此前曾报道过两起相关事件：一起是ChatGPT沙箱漏洞导致Gmail数据泄露，另一起是OpenAI Agent可绕过沙箱限制。这些案例充分说明，企业必须将共享内部服务、薄弱的访问规则、开放的网络路径视为安全边界，全面开展测试。  
  
  
OpenAI应当尽快公开受影响的服务范围、漏洞修复时间、暴露窗口期，以及日志中是否存在未授权访问记录。  
  
  
所有API服务商应当在每一个内部请求转发节点强制进行身份校验，拦截匿名模型调用请求，设置严格的速率限制与消费额度限制，对无有效客户身份的异常请求触发告警。  
  
  
用户应当定期检查API账单与调用日志，需要注意的是，密钥轮换无法修复这类服务端存在的身份认证绕过漏洞。  
  
  
参考来源：  
  
OpenAI Sandbox Escape Flaw Allowed Free Access to Paid AI Models Without an API Key  
  
https://cybersecuritynews.com/openai-sandbox-escape-vulnerability/  
  
###   
  
### 推荐阅读  
  
  
[](https://mp.weixin.qq.com/s?__biz=MjM5NjA0NjgyMA==&mid=2651347479&idx=1&sn=1cf9c858a30d0b7af06d196002bd2913&scene=21#wechat_redirect)  
  
  
###   
  
### 电报讨论  
  
  
[]()  
  
  
  
![扫码加入AI安全交流群](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX0Py7ibxdLKXia1pMziaic5vIE9XPXG9OGaeJDa07iaG10eicuzhW59nwpF5msHiaYZvfMqCNkx2aFDiaMzm3oAf4rTaHXU5UAI1mUYgts/640?wx_fmt=png "")  
  
  
  
![下载FreeBuf知识大陆APP](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX0TIGzII2Hcmtzu7AJeZFicnqd1mXojVoawje2uLxYqwJbVgzJpmSXzVhrpOsLurRZ2lVa4vfgLBqg7uJKbrKg5F18VzZxVPicZU/640?wx_fmt=png "")  
  
  
  
  

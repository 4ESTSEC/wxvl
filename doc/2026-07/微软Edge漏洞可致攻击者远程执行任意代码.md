#  微软Edge漏洞可致攻击者远程执行任意代码  
 FreeBuf   2026-07-07 10:00  
  
![FreeBuf](https://mmbiz.qpic.cn/mmbiz_gif/icBE3OpK1IX3GueKo7bibyFgxO6WzuOaoBKngLPic7o8qRMdQlSV0IrichTicjhwNn6IJgwamyphGpnK39U7O01jZyo0FvNjauOskVZs3wsjiaBqM/640?wx_fmt=gif "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/icBE3OpK1IX3We0NzkIkCCkdQFhWwH5mVkIMRxsicQAa7oxt5NF0jWkVLmfGaxJiagzgbsa5y129pO93RpfUNFRib63zKsh7HGNb3prSUhvRMia4/640?wx_fmt=jpeg&from=appmsg "")  
  
  
微软披露了基于Chromium的Microsoft Edge浏览器中存在一个新的安全漏洞（CVE-2026-57992），远程攻击者可利用该漏洞在受影响系统上执行任意代码。该漏洞源于Use-After-Free（UAF）内存破坏问题，CVSS评分为7.5（高危）。截至发稿时，尚无可用补丁或公开的PoC利用代码。  
  
  
Part  
01  
  
漏洞技术细节  
  
CVE-2026-57992被归类为影响Microsoft Edge Chromium引擎的关键级Use-After-Free漏洞。未经授权的攻击者可通过诱骗用户访问特制网页，在网络环境下执行代码。该攻击媒介基于网络传播，需要用户交互且攻击复杂度较高。  
  
  
高攻击复杂度评级意味着成功利用该漏洞并非易事。攻击者必须在恶意页面上制作具有欺骗性或不可见的表单元素，并依赖受害者执行两次连续点击手势，从而无意间触发Edge的自动填充功能，最终激活内存破坏链。当攻击者托管一个特制网站并触发Edge渲染引擎中的UAF条件时，漏洞就会被利用。  
  
  
Part  
02  
  
攻击实施条件  
  
由于攻击者无法强制用户访问恶意内容，他们通常依赖社会工程学手段，通过钓鱼邮件、即时消息或恶意电子邮件附件引诱受害者访问攻击者控制的页面。要成功利用该漏洞，受害者必须：  
  
- 访问攻击者控制的网页  
  
- 执行两次点击手势以激活自动填充机制，从而触发use-after-free条件  
  
  
这种交互要求在一定程度上降低了实际风险，因为攻击无法被动执行或无需受害者参与即可完成。  
  
  
受影响版本  
<table><thead><tr></tr></thead></table>  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/icBE3OpK1IX0g6cAMo3icdPno2Toa0roKanbrbyvlpCWfh1r0HSAOAic844N3GEn885uhcmpD8ynjsssswAPulSAh8zk1rtppA9c8iaQGSVLFuQ/640?wx_fmt=png&from=appmsg "")  
  
  
Part  
04  
  
潜在危害及缓解措施  
  
若成功利用，CVE-2026-57992可能导致系统完全沦陷，使攻击者能够在浏览器进程上下文中执行任意代码。根据攻击者的目标，这可能成为进一步横向移动、数据外泄或有效载荷部署的入口点。  
  
  
由于目前尚无官方补丁，企业和用户应采取以下措施：  
  
- 关注微软安全响应中心(MSRC)的补丁发布信息  
  
- 教育用户避免点击可疑链接和附件  
  
- 在可行情况下启用"增强安全模式"等浏览器安全功能  
  
- 在企业环境中限制自动填充功能作为临时缓解方案  
  
  
参考来源：  
  
Microsoft Edge Vulnerability Allows Remote Attacker to Execute Arbitrary Code  
  
https://cybersecuritynews.com/microsoft-edge-flaw-code-execution/  
  
****  
**推荐阅读**  
  
[](https://mp.weixin.qq.com/s?__biz=MjM5NjA0NjgyMA==&mid=2651341548&idx=1&sn=bb9edaa490d92c0258ff47c5dd29faf4&scene=21#wechat_redirect)  
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
### 电报讨论  
  
  
[]()  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX0ctiaj5Z87Tg1RMJbr06lrE2fqlFoKFB0d4hx9AsKnZwJlVP4C7SBicZtVYotXf2IOL9UhETZBwFP2Q5D9A7vpdzWjR2M6r7abc/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX1cNPEia7j7bXCX8P8iaDo801yQlaF965NduoqX5nEfgC2mLLgM6VdzcRdkYkeGebHaia3JRK31e08ibfS1WnmYl8DtvPf83e6XW6k/640?wx_fmt=png&from=appmsg "")  
  
  
  

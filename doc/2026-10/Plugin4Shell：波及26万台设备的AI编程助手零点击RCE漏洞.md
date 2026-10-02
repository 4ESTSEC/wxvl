#  Plugin4Shell：波及26万台设备的AI编程助手零点击RCE漏洞  
原创 Dr. Clay
                    Dr. Clay  黑白之道   2026-10-02 00:50  
  
![](https://mmbiz.qpic.cn/mmbiz_png/nGzNudUIJ6MJeDKxhmRogP4hhVFtOpaovt82UXxNRI5UeO950Cshh4TFYnjDjJsZDqOB0ibbmnDUibSSj3V2Ddu67Xib8lW4wY2O9PAHsbTIr8/640?from=appmsg "")  
> **导语**  
：当AI编程助手成为开发者的标配生产力工具，其插件生态的安全门槛却迟迟未能跟上。2026年9月17日，安全研究机构AIR Security甩出了一枚深水炸弹：一枚无需用户任何点击、即可在四大主流AI编程工具中实现远程代码执行的供应链漏洞，被命名为"Plugin4Shell"。它在公开披露前已扩散至约26万个活跃AI代理——而此时四大厂商中只有两家完成了修复。  
  
## 一、漏洞概述  
  
Plugin4Shell是一种零点击（zero-click）远程代码执行（RCE）供应链漏洞，由AIR Security的安全研究人员Or Nevo、Dor Granat和Niv Hoffman联合发现，并于2026年6月向所有受影响的四大厂商负责任披露。公开披露时间为2026年9月17日。  
  
该漏洞存在于AI编程助手内置的插件商店（plugin store）机制中，攻击者可以通过上传恶意插件，在受害者毫无察觉的情况下——无需任何钓鱼、诱导或交互——直接在被控AI代理的运行环境中执行任意系统命令。  
  
![Plugin4Shell影响范围与传播路径](https://mmbiz.qpic.cn/sz_mmbiz_jpg/nGzNudUIJ6NPZmJDJ5EldDvZgPDw0du2qOBI51eGJ0jbvO3icBb97cnVQ9sMHYhAuRdiaP4cdtLDJfo1qpC3o5j3rqczOR5iaDhT7EOfqxyNv0/640?from=appmsg "Plugin4Shell影响范围与传播路径")  
## 二、受影响产品  
  
Plug in4Shell同时击穿了以下四款市占率最高的AI编程工具：  
<table><thead><tr><th style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;font-weight: bold;background-color: rgb(255, 249, 249);color: rgb(239, 112, 96);"><section><span leaf="">产品</span></section></th><th style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;font-weight: bold;background-color: rgb(255, 249, 249);color: rgb(239, 112, 96);"><section><span leaf="">厂商</span></section></th><th style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;font-weight: bold;background-color: rgb(255, 249, 249);color: rgb(239, 112, 96);"><section><span leaf="">漏洞状态</span></section></th></tr></thead><tbody><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">Claude Code</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">Anthropic</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">修复中</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">OpenAI Codex</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">OpenAI</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><strong><span leaf="">已修复</span></strong></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">GitHub Copilot</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">Microsoft</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><strong><span leaf="">已修复</span></strong></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">Google Gemini CLI</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">Google</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">修复中</span></section></td></tr></tbody></table>  
这意味着截至Disclosure日期，仍有一半的受影响产品处于未修复状态。值得注意的是，该漏洞在正式披露前就已在野外部署到了约26,000个AI代理中，安全研究人员从发现到披露的三个月窗口期内，并未观测到大规模主动 exploitation，但这组数字已足以说明插件供应链的传播速度远超传统软件。  
## 三、技术剖析  
### 攻击链解剖  
  
Plugin4Shell的攻击路径充分利用了AI编程助手对插件系统的"信任链"。标准攻击链如下：  
```
恶意插件上传 → 插件市场审核绕过 → 插件安装触发代码执行→ 受害者环境中的AI代理被控 → 远程命令注入
```  
  
零点击的核心在于：AI编程助手在安装插件时，默认会对插件包执行预加载/初始化代码，以验证插件有效性。这个预加载阶段发生在LLM介入之前，因此即使用户从未主动触发任何操作，恶意代码也会在安装流程中静默执行。  
  
这与传统的"提示注入"攻击有本质区别——提示注入需要LLM解析并执行恶意指令，而Plugin4Shell在LLM被调用之前就已经完成了代码执行，等价于直接获得了宿主机的命令执行权限。  
### 为什么危险：AI编程助手的特殊攻击面  
  
AI编程助手与普通应用不同，它通常拥有以下高权限：  
- 文件系统读写权限（项目代码）  
  
- Git操作权限（提交、推送）  
  
- 包管理权限（安装依赖）  
  
- 环境变量访问（API密钥、凭证）  
  
一旦RCE漏洞被打穿，攻击者不仅能操控开发者的IDE，还能横向渗透到代码仓库、CI/CD流水线乃至生产环境。  
## 四、漏洞披露时间线  
<table><thead><tr><th style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;font-weight: bold;background-color: rgb(255, 249, 249);color: rgb(239, 112, 96);"><section><span leaf="">时间节点</span></section></th><th style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;font-weight: bold;background-color: rgb(255, 249, 249);color: rgb(239, 112, 96);"><section><span leaf="">事件</span></section></th></tr></thead><tbody><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">2026年5月</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">AIR Security研究团队在实验室环境中演示了该攻击手法</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">2026年6月</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">向Anthropic、OpenAI、Microsoft、Google四大厂商提交漏洞报告</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">2026年9月17日</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">负责任披露，公开漏洞详情</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">披露后</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">OpenAI Codex和GitHub Copilot完成修复；Anthropic Claude Code和Google Gemini CLI仍在修复中</span></section></td></tr></tbody></table>## 五、验证与检测  
  
如果你正在使用上述AI编程工具，可以通过以下方式初步判断是否受影响：  
  
**版本检测**  
：确认当前运行的版本是否包含最新安全补丁。以Claude Code为例，9月17日之后发布的版本已包含修复。  
  
**审计活动日志**  
：检查AI编程助手的工作日志中是否存在异常的插件安装记录或陌生的网络出站连接。  
  
**网络层监控**  
：由于RCEPayload通常会尝试向外部C2服务器建立连接，关注AI代理进程的非预期出站流量是有效的检测手段。  
## 六、缓解建议  
- **立即更新**  
：将Claude Code、Codex、Copilot、 Gemini CLI全部更新至最新版本  
  
- **插件来源限制**  
：仅启用来自官方认证渠道的插件，关闭第三方插件自动安装  
  
- **网络分段**  
：AI编程助手运行环境应与生产环境严格隔离  
  
- **环境变量清点**  
：重置可能在AI编程助手中暴露过的所有密钥和凭证  
  
## 七、趋势前瞻  
  
Plugin4Shell的披露再次证明了一个正在加速的安全趋势：AI工具链正在成为下一代攻击链路的核心节点。攻击者的视线正从"攻击人类用户"转向"攻击AI Agent本体"——因为一个被控的AI代理可以不知疲倦地访问代码、密钥和基础设施。  
  
可以预见，未来围绕AI编程助手插件生态的攻击将进入高发期。传统应用的安全模型并不能直接套用，AI编程助手需要在插件隔离、权限最小化和沙箱执行上做出根本性重新设计。对安全社区而言，2026年的Plugin4Shell或许只是冰山一角。  
  
**版权声明**  
：本文由华盟网原创发布，保留所有权利。配图由华盟网授权使用。  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/nGzNudUIJ6P3ib8a5aLiaIZ6UdFLskvKClVvzkrprxl8UT8QibUbvMza9RIxQ1z7wgzcNsoXrjibJ7EHtQ3G8qrJwJrBCGB5BQ082wq9z5M4CFo/640?from=appmsg "")  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650621549&idx=1&sn=21c4b072726d2387d562109ada6b9bbb&scene=21#wechat_redirect)  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650621944&idx=1&sn=3cc6dc9876a20466d4ec3634deb80220&scene=21#wechat_redirect)  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650622065&idx=1&sn=09f8ae84c06d4331e71c277b177ae701&scene=21#wechat_redirect)  
> 👇 点击**阅读原文**  
，访问我的网站  
  
  

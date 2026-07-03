#  红队实战：本地AI如何发现其他工具都漏掉的漏洞  
原创 A译
                    A译  黑白之道   2026-07-03 01:27  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/nGzNudUIJ6PEtrnib92D2oK87RibXnQvpZCZAkK3atZrcptiahK7489EsepicU8jY03gBQqJmM9H50cKQRKIY72vFj6hjQLAHDmMSOQic6mQ150A/640?wx_fmt=png "")  
> **导语**  
：自上次尝试类似方法以来，模型智能和攻击技术都有了很大进步。关于Anthropic发布的研究外界有很多热议，但成本和隐私问题仍然是障碍。当无法保证工作完成得足够彻底时，安保工作就变成了某种像在赌博一样的事情。  
  
  
为了测试，我设计了一个小实验。我对四种不同的方法进行了基准测试，用一个我已知的漏洞来评估每种方法的有效性。  
## 一、基准漏洞：PHPIPAM 认证型本地文件包含（LFI）  
  
基准漏洞是一个经典的.php认证型本地文件包含问题。  
  
控制器名称取自用户输入，直接拼接到require_once中，没有任何过滤。  
  
如果API已启用（默认关闭）且你有有效凭据获取API令牌，就可以包含/执行任何可被Web服务器访问的、以.php结尾的文件。  
  
![PHPIPAM LFI演示](https://mmbiz.qpic.cn/sz_mmbiz_png/nGzNudUIJ6OdszLIB7VhicLTW1PrV8YrcxOy5XibLalukZJq8NP2AzKbksScUAAaQSqOgSZUxUicQq9ToicIcBkt2T2pr5063eZVB9maQPrUM38/640?wx_fmt=png "PHPIPAM LFI演示")  
  
第二次请求包含了我植入的phpinfo();文件来演示问题。  
  
⚠️ 此问题已在没有补丁的情况下公开披露，因为风险相对较低，且PHPIPAM维护者没有任何回应。该漏洞仅影响启用了API的实例（默认不启用）。默认影响也有限，似乎没有办法向Web服务器上传任意.php文件，也没有有趣的默认文件可以包含/执行。  
  
如果你正在运行PHPIPAM并希望立即采取行动，建议在此期间先禁用API。此漏洞编号为CVE-2026-12194。  
## 二、测试结果  
  
简而言之：在测试的四种方法中，最成功的一种清楚地表明：**框架/方法比模型本身更重要**  
。  
  
如果你只对这个方法感兴趣，可以直接跳到"本地AI模型+自定义框架"章节。  
<table><thead><tr><th style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;font-weight: bold;background-color: rgb(255, 249, 249);color: rgb(239, 112, 96);"><section><span leaf="">方法</span></section></th><th style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;font-weight: bold;background-color: rgb(255, 249, 249);color: rgb(239, 112, 96);"><section><span leaf="">是否发现了漏洞？</span></section></th></tr></thead><tbody><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">Semgrep</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">否</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">云端GLM 5.1 + Strix AI Agent</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">否 *</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">云端SOTA + 代码审查技能</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">有时能</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">本地AI + 自定义框架</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">是</span></section></td></tr></tbody></table>### 2.1 Semgrep  
  
仅使用semgrep scan --config auto运行的Semgrep没有识别出这个漏洞。  
  
当然，写自定义规则来检测这个特定模式是可能的。然而，这一直是传统SAST工具的核心挑战之一：你只能为你已经知道的危险模式编写规则。  
### 2.2 GLM 5.1 + Strix全自动化AI渗透测试  
  
接下来我看了Strix：一个全自动化AI渗透测试工作流。拥有超过25,000个GitHub stars，我很期待看看它能做到什么，以及是否能识别这个bug。  
  
💡 **注意**  
：其文档要求使用GPT-5.4、Claude Sonnet 4.6或Gemini 3 Pro。  
  
我不想签一张空白支票，所以选择了GLM 5.1，这样我就能先用更便宜的模型观察token消耗情况。  
  
看它运行起来真的很激动人心。它克隆了仓库、探索了代码库，甚至自己安装了应用程序来动态验证可能的发现。  
  
大约12小时后，消耗了近6000万tokens，一份report.md文件已经准备好了。  
  
![GLM 5.1 + Strix运行结果](https://mmbiz.qpic.cn/mmbiz_png/nGzNudUIJ6MZOybXKMZz7TicMvh3Dc52ykUGxkzVJMJTjscCZpx54VYJJrprs3lzTwWvULGEk6bo4o4t4wpZ1XIs6A8IUVCgrF7b4V1RN3cA/640?wx_fmt=png "GLM 5.1 + Strix运行结果")  
  
它**没有**  
发现我们的LFI。这让我想起了这条推文：  
> GPT-5 just refactored my entire codebase in one call. 25 tool invocations. 3,000+ new lines. 12 brand new files. It modularized everything. Broke up monoliths. Cleaned up spaghetti. None of it worked. But boy was it beautiful.  
  
  
使用GLM 5.1的实际花费约为30美元。如果使用Sonnet，费用大约在180到300美元之间。Opus则要再加倍。  
  
**执行摘要**  
  
对phpIPAM（IP地址管理）1.8.1版本进行的外部渗透测试发现了多个安全弱点，如果被利用，可能导致敏感数据泄露、存储型跨站脚本、服务器端请求伪造和权限提升。整体风险态势：高。  
  
主要发现包括：  
- 通过API暴露敏感用户数据（任何认证用户均可访问密码哈希、令牌、2FA密钥）  
  
- 通过API的存储型XSS（HTML转义被禁用，允许持久化脚本注入）  
  
- 保险库证书获取中的SSRF（可扫描内网和访问云元数据）  
  
- API中功能级授权缺陷（非管理员用户可以创建管理员资源）  
  
- 管理员端点上状态变更的多个CSRF漏洞  
  
- 自定义字段重排序功能中的二阶SQL注入  
  
- 使用松散比较而非计时安全比较的弱CSRF令牌验证  
  
其中一些问题按PHPIPAM的威胁模型是属于设计如此。其他的看起来像是误报/不太有趣/已经修复了。  
### 2.3 云端SOTA + 技能型代码审查  
  
接下来我尝试了另一种方法：AI技能。对于不熟悉这个概念的人来说，AI技能实际上就是Markdown文件，为Agent提供特定的指令、工作流程和专业知识。  
  
我用一个社区贡献的安全审查技能作为起点。  
  
我做的修改包括：  
- 移除了依赖审计、密钥扫描和提议补丁步骤  
  
- 将每个漏洞类别分散到各自的专用子Agent中  
  
- 在注入缺陷部分增加了关于本地文件包含（LFI）的额外指导  
  
最终，我发现结果非常不一致。扫描有时能找到漏洞，有时完全找不到。  
#### Claude Code + Opus 4.8  
  
在这个技能加持下尝试使用Claude Code的'Pro'计划，得到了一个相当糟糕的结果。  
  
![Claude Code运行结果](https://mmbiz.qpic.cn/mmbiz_png/nGzNudUIJ6M4z4vt2LeSt6FNlbAVuicP1JupHqbKcPLwzztPaHwKxI9zIzibOVDStrovWW7TibfC3DXlhrCQYhpkxK5IAj6HFbm2eNlgUbQJvs/640?wx_fmt=png "Claude Code运行结果")  
  
阅读它的思考过程，它决定不生成子Agent来处理每个漏洞类别。  
  
![Claude拒绝生成子Agent](https://mmbiz.qpic.cn/mmbiz_png/nGzNudUIJ6OX6STTXCJ6cgiaj34dwVApQN2J0S8sibMmPXt60V5gRlwUHDTzLiaH5TcZibFLMMqYj4a6W4FZ28BveJKfzsbV1MUgficLZb7UuMgA/640?wx_fmt=png "Claude拒绝生成子Agent")  
  
要求它显式生成子Agent，结果立即达到了5小时的会话限制……  
  
![Claude会话限制](https://mmbiz.qpic.cn/sz_mmbiz_png/nGzNudUIJ6Ol2f3fEUk08g33iaaU1QKv8yYwxwdAqtk2vgM3ODfqhic4gic0ez45EHIeuHDtN334dR8lTHr4lWiaRl93CcY6EeibcDHYGXV8PZeE/640?wx_fmt=png "Claude会话限制")  
#### Cursor + GPT 5.5（中配）——使用OpenRouter约5-10美元  
  
经过几次使用各种模型的尝试后，下面是使用Cursor和GPT 5.5找到问题的一次运行。  
  
![Cursor + GPT 5.5运行结果](https://mmbiz.qpic.cn/sz_mmbiz_png/nGzNudUIJ6NdWYfarwdmOGRpQFyeXq5icfwOsQPb9QkWA3gh2dBZVPQHIZicql6gn845rOrqXicIBia29lsBjTKmj9rZzHcFrFhiarEpibN4ia2kyo/640?wx_fmt=png "Cursor + GPT 5.5运行结果")  
#### 问题所在  
  
我很快意识到，在相当大的代码库中，审查工作不够彻底。是否能找到问题往往取决于Agent决定读取哪些文件，以及它选择用哪些术语进行grep。  
  
有趣的是，一旦指向正确的文件，几乎每个模型都能立即识别出漏洞。  
#### 使用技能的困难  
  
使用技能尝试这种方法，立即遭到了拒绝。看模型的推理，它经常得出结论：任务太大，无法在单次运行中完成。  
  
![Claude拒绝执行](https://mmbiz.qpic.cn/sz_mmbiz_png/nGzNudUIJ6NjTFtVEozZzQjHTwNWictmz785gVXW2ia8ejCsL52Fgd0hRbNqbGbUOMMmYdqpDeIqdZdjA1NE7WG2Tibq3drmFXT1eIribibvCbJQ/640?wx_fmt=png "Claude拒绝执行")  
  
即使我通过了拒绝，这方法的成本也可能令人望而却步。  
  
但是，如果本地模型在给定单个源代码文件+上下文的情况下就足够用了，而不是一次性审查整个代码库这个难得多得多的任务呢？  
### 2.4 本地AI模型 + 自定义框架  
  
我们的下一步方法是将单次大审查换成一个小型的本地框架。不是让一个Agent一次推理整个代码库，而是让框架引导本地模型逐个文件地遍历项目，每次给它一个文件加上所需的上下文。  
  
对于每个源文件：  
- 本地模型审查单个文件（+上下文）  
  
- 写一份结构化报告  
  
- 收集所有报告  
  
- 整合分析  
  
这种方法**每次运行都找到了基准漏洞**  
。  
  
安全漏洞报告：api/index.php  
  
**高风险：路径遍历/通过Controller参数的任意文件包含**  
  
描述：API入口点使用未经过滤的用户输入构造文件包含路径。值从、  
_POST、JSON body或XML body直接流入require_once()，没有任何路径验证，启用了目录遍历（../）来包含文件系统上任意PHP文件。  
  
对于这个代码库，我估计大约消耗了1.2亿tokens，审查了约800个源代码文件。  
#### 局限性  
- **Token密集型**  
：如果你无法本地运行模型，成本会很高。幸运的是，Project Black已经为需要密码破解的渗透测试维护了一个hashcat设备。事实证明，这套硬件也足以运行Qwen 3.6 27b，约170k上下文。  
  
- **误报多**  
：由于这种方法纯粹基于代码审查，可能会产生误报。你可以将这个阶段的输出输入更多AI工具来验证可利用性，但这会进一步增加token消耗。  
  
- **缺乏威胁模型/更广泛的应用程序上下文理解**  
：根据我对其他已知问题的测试，它似乎在识别更复杂的访问控制缺陷时遇到困难——这些问题在假设的细微差异方面更为复杂。  
  
无论如何，一次可能是侥幸。看看它能不能找到我自己都不知道的东西？  
## 三、myVesta 认证型RCE  
  
就在我完成基准测试时，我收到了一位使用myVesta的朋友的消息——myVesta是一个类似cPanel的Web服务器控制面板。  
  
![myVesta消息](https://mmbiz.qpic.cn/mmbiz_png/nGzNudUIJ6MibuEmpN4xmRTFHzaEA7yRb7A1rX9FShKQC5XltRZCvfqNvS5SPxeput0gqEHdm8Qdt5Ey5drZedAMmgUuTeSFOp5a45gOLjJ8/640?wx_fmt=png "myVesta消息")  
  
时机刚好。目标到手。8小时后，它发现了认证型RCE。  
  
需要说明的是，像cPanel这样的Web服务器控制面板用于让互联网上的普通人为共享Web服务器执行有限的管理任务。在这种软件中存在认证型RCE漏洞意味着你可以在托管服务商处注册一个账户，然后可能接管整台服务器。这个命令以Web服务器上更高权限的管理员用户身份执行，而不是你自己的用户。  
  
原来，FTP用户名删除功能中的一段旧代码，允许将Username参数直接传递到exec中。  
  
![myVesta RCE测试](https://mmbiz.qpic.cn/sz_mmbiz_png/nGzNudUIJ6Pry9YG0K6E1fwz1X38BJlox3YwdMfg8elssXK6f5z9g1K25qLgOl6ZeWS4MRicIg1HIHnHDicM6jOice5t9RgoMLaDE7zZjU9MDc/640?wx_fmt=png "myVesta RCE测试")  
  
创建示例请求。  
  
![替换用户名](https://mmbiz.qpic.cn/sz_mmbiz_png/nGzNudUIJ6MxRaSTRakf4dBbFqaic5tjnhEahnw3vIkll3AMZe9oX53VUlUSbQSnnj1icsfnpmAibF9RjL6C8Gibjicmr8U7YyEBAWhHBTiaDngrA/640?wx_fmt=png "替换用户名")  
  
替换请求中的用户名和POC。  
  
![验证结果](https://mmbiz.qpic.cn/sz_mmbiz_png/nGzNudUIJ6O2ym7Z82zXyQDF6nK8RmvJgVHibYia54vsriaupGBYYWOMxlDVlA9uc9e6n5bjUgsWcDWXWL5Uw15ibMz97AibEbzUibpbn53kqJTV4/640?wx_fmt=png "验证结果")  
  
检查服务器上创建的文件。  
  
感谢myvesta团队的快速回应！此漏洞编号为CVE-2026-12195。补丁已在此处提交。  
## 四、我们还能继续吗？  
  
事实证明，可以。目前还有更多发现正在酝酿中。我们暂时保留细节，以便受影响的项目有时间打补丁。它们会在自己的时间表里浮出水面，但还有更多要来的。  
## 五、结论  
  
随着本地AI模型不断改进，这些能力将落入更多人的手中，而不是更少。总的来说，我认为这是一件好事。  
  
在我们这边，我们正在尝试将这类技术整合到我们的工作方式中，这可能意味着为我们的客户提供的渗透测试会更快、更彻底。这里有很大的潜力，我们会继续摸索，看看本地AI在哪里能发挥它的作用。  
  
**原文出处**  
：https://projectblack.io/blog/local-ai-for-cyber-security/  
  
**版权声明**  
：本文由华盟网原创发布，保留所有权利。配图来自ProjectBlack授权使用。  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/nGzNudUIJ6Puc8HNiaVFMibMWyXuRCjam9iat0x1rCpsp1iatD7HveaKn7X0FMG8AlYEYZQbWQjAzjbYJjgdDQYTxLsn3W66vehH9V8LV2z9XnE/640?wx_fmt=jpeg "")  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650618755&idx=1&sn=48e0a85464fb20c73b6f338928a8f850&scene=21#wechat_redirect)  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650618776&idx=2&sn=a8fc65fd2a71822830022fc52d967bcd&scene=21#wechat_redirect)  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650618496&idx=1&sn=96ecdff99136258a4bf2cb156542311e&scene=21#wechat_redirect)  
> 👇 点击**阅读原文**  
，访问我的网站  
  
  

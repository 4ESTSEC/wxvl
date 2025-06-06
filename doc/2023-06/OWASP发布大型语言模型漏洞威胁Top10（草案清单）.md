#  OWASP发布大型语言模型漏洞威胁Top10（草案清单）   
 安全牛   2023-06-07 11:18  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/kuIKKC9tNkCAzibbJLHcZNNRl7icTYP2kIzmMeslWtL3qJHFAOpiblhG1b7jopEvFCfVl0h9PZUXWIt8rgf2w3iaog/640?wx_fmt=jpeg "")  
  
  
基于大型语言模型（LLM）的生成式AI技术应用已经成为当前全球企业普遍关注的热点。作为一种创新技术，企业组织在未来数字化发展中有很多机会应用ChatGPT或类似AI工具。因此，CISO们需要提前做好准备，以避免可能出现的安全隐患和隐私泄露风险。  
  
  
日前，OWASP（全球开放应用软件安全项目组织）发布了LLM应用风险草案清单，并梳理总结了最严重的10大LMM应用安全漏洞类型，包括提示注入、数据泄漏、不充分的沙箱机制和未经授权的代码执行等。OWASP研究人员表示，这份清单旨在让LLM应用的开发者、设计者、架构师和管理者，更好地了解在部署和管理LLM应用过程中可能存在的潜在风险，并提高漏洞防范认识，从而改善LLM未来应用中的安全态势。  
  
  
**01**  
  
**提示注入（LLM01:2023）**  
  
  
提示注入是指通过精心制作的提示绕过内容监管过滤，使其忽略先前的指令或执行非法的操作。这类漏洞可能导致意想不到的后果，包括数据泄露、未经授权的访问或其他安全隐患。常见的提示注入漏洞包括：通过使用特定的语言模式或token绕过过滤器或限制，利用LLM的文本分词或编码机制中的弱点，以及通过提供欺骗性上下文误导LLM执行意外操作。  
  
  
**防护建议**  
  
  
  
针对该类型漏洞的预防措施包括：  
  
- 对用户提供的提示执行严格的输入验证和净化。  
  
- 使用上下文感知过滤和输出编码来防止提示操纵。  
  
- 定期更新和微调LLM，以提高理解恶意输入和极端情况的能力。  
  
  
  
**02**  
  
**数据泄漏（LLM02:2023）**  
  
  
当LLM通过其响应意外泄露敏感信息、专有算法或其他机密资料时，就会发生数据泄漏。这可能导致未经授权访问敏感数据、侵犯个人隐私及其他安全隐患。  
  
  
常见的数据泄露漏洞包括：对LLM响应中的敏感信息过滤不完整或不恰当，记忆LLM训练过程中的敏感数据，以及因LLM算法错误而导致机密信息的意外泄露。攻击者可以通过精心设计的提示来故意探测LLM，试图提取LLM凭训练数据所记忆的敏感信息，或者合法用户无意中向LLM提出的包含机密信息的提问。  
  
  
**防护建议**  
  
  
  
针对该类型漏洞的预防措施包括：  
  
- 实施严格的输出过滤和上下文感知机制，以防止LLM泄露敏感信息。  
  
- 在LLM训练过程中使用差分隐私技术或其他数据匿名化方法，以减小过拟合或记忆的风险。  
  
- 定期审计和审查LLM的响应，以确保敏感信息不会无意中泄露。  
  
  
  
**03**  
  
**不充分的沙箱机制（LLM03:2023）**  
  
  
如果LLM在访问外部资源或敏感系统时未加适当隔离，不充分的沙箱机制就会导致潜在的漏洞、未经授权的访问或LLM违规操作。和不充分的沙箱机制相关的常见漏洞包括：LLM环境与其他关键系统的数据存储区隔离不足，不充分的限制任由LLM访问敏感资源，以及LLM执行系统级操作/与其他进程交互。  
  
  
**防护建议**  
  
  
  
针对该类型漏洞的预防措施包括：  
  
- 将LLM环境与其他关键系统和资源隔离开来。  
  
- 限制LLM对敏感资源的访问，并将访问功能限制在最低限度。  
  
- 定期审计和审查LLM的环境和访问控制，以确保保持适当的隔离。  
  
  
  
**04**  
  
**未经授权执行代码（LLM04:2023）**  
  
  
当攻击者通过自然语言提示利用LLM在底层系统上执行恶意代码、命令或操作时，就会发生未经授权的代码执行。典型的攻击类型包括：攻击者设计提示以指令LLM执行命令，该命令在底层系统上启动反向shell，从而授予攻击者未经授权的访问权限；LLM无意中被允许与系统级API进行交互，攻击者操纵该API在系统上执行未经授权的操作。  
  
  
**防护建议**  
  
  
  
针对该类型漏洞的预防措施包括：  
  
- 实施严格的输入验证和净化流程，以防止LLM处理恶意或意外的提示。  
  
- 确保适当的沙箱机制，并限制LLM的功能，以限制其与底层系统交互的能力。  
  
  
  
**05**  
  
**服务器请求伪造（LLM05:2023）**  
  
  
当攻击者利用LLM执行意外请求或访问受限制的资源（比如内部服务、API或数据存储）时，就会出现服务器请求伪造（SSRF）漏洞。常见的SSRF漏洞包括：输入验证不足，允许攻击者操纵LLM提示发起未经授权的请求，以及网络或应用安全设置中的错误配置将内部资源暴露给LLM。为了执行攻击，攻击者还可以设计提示，指令LLM向内部服务发出请求，绕过访问控制，并获得对敏感信息未经授权的访问。  
  
****  
**防护建议**  
  
  
  
针对该类型漏洞的预防措施包括：  
  
- 实施严格的输入验证和净化策略，防止通过恶意输入发起未经授权的请求。  
  
- 定期审计和审查网络/应用软件安全设置，以确保内部资源不会无意中暴露给LLM。  
  
  
  
**06**  
  
**过度依赖模型生成的内容（LLM06:2023）**  
  
  
过度依赖LLM生成的内容是指组织和用户未经验证就信任LLM生成的内容，从而导致不正确的误导信息大量传播，降低人在决策中的参与度，并弱化批判性思考。与过度依赖LLM生成的内容相关的常见问题包括：未经验证就接受LLM生成的内容，以为LLM生成的内容没有偏误或错误信息，以及在没有人参与或监督的情况下依赖LLM生成的内容用于关键决策。  
  
  
如果一家公司依赖LLM生成安全报告和分析，而LLM生成的报告含有大量的不正确数据，那么如果企业依赖这份由LLM生成的内容进行关键决策，就可能会酿成重大后果。网络安全分析师称这种现象为LLM幻觉。  
  
  
**防护建议**  
  
  
  
针对该类型漏洞的预防措施包括：  
  
- 对LLM生成的内容进行充分验证；  
  
- 严格限制使用那些未经验证的LLM生成内容；  
  
- 定期开展LLM生成内容的安全风险审计。  
  
  
  
**07**  
  
**对LLM目标和行为对齐不足（LLM07:2023）**  
  
  
当企业的LLM应用行为与预期中的应用目标不一致时，就会导致不良的应用后果或安全漏洞，这种漏洞被称为AI应用对齐不足。常见问题包括：定义不明确的目标导致LLM优先考虑了那些不良或有害的行为，不一致的奖励机制引发意想不到的模型行为，以及对LLM行为测试和验证不足。如果旨在协助系统管理任务的LLM出现未对齐漏洞，就可能会执行有害的命令或降低系统的安全防护级别。  
  
  
**防护建议**  
  
  
  
针对该类型漏洞的预防措施包括：   
  
- 在设计和开发过程中明确定义LLM的目标和预期行为。  
  
- 确保奖励机制和训练数据与预期结果相一致，不鼓励任何有害的违规行为。  
  
- 定期测试和验证LLM在众多场景、输入和上下文中的行为，以识别和解决对齐问题。  
  
  
  
  
**08**  
  
**不完善的访问控制（LLM08:2023）**  
  
  
这种漏洞是指LLM在应用中未正确实施访问控制或身份验证，允许未经授权的用户与 LLM 进行交互，从而产生可被利用的安全漏洞。常见例子包括：未对访问LLM执行严格的身份验证要求，基于角色的访问控制（RBAC）实施不充分，允许用户执行超出预期权限的操作，以及未为LLM生成的内容和操作提供适当的访问控制。  
  
  
**防护建议**  
  
  
  
针对该类型漏洞的防护措施包括：  
  
- 实施强身份验证机制，比如多因素身份验证（MFA）；  
  
- 应确保只有授权用户才能访问LLM；  
  
- 对LLM生成的内容和操作实施适当的访问控制，以防止未经授权的操作。  
  
  
  
**09**  
  
**不恰当的错误处置（LLM09:2023）**  
  
  
错误处置漏洞主要指由于LMM的错误处置或调试信息被公开暴露，从而导致了向威胁分子泄露敏感信息、系统资料或潜在攻击途径。常见的错误处置漏洞包括：通过错误消息暴露敏感信息或系统资料，泄露可能帮助攻击者识别潜在漏洞或攻击途径的调试信息，以及未能有效处理应用中的错误，从而可能导致意外行为或系统崩溃。  
  
  
**防护建议**  
  
  
  
针对该类型漏洞的预防措施包括：  
  
- 实施适当的错误处理机制，以确保错误被及时地获取、记录和处理。  
  
- 确保错误消息和调试信息中不包含敏感信息或系统资料，同时考虑使用通用的错误消息，为开发者和管理员记录详细的错误数据。  
  
  
  
**10**  
  
**训练数据中毒（LLM10:2023）**  
  
  
训练数据中毒是指攻击者操纵LLM的训练数据或微调程序，以引入漏洞、后门或偏误，从而危害模型的安全性、有效性或道德。常见的训练数据中毒问题包括：通过恶意操纵训练数据向LLM引入后门或漏洞，以及向LLM注入诱导数据，导致LLM生成有偏差或不适当的响应。  
  
  
**防护建议**  
  
  
  
针对该类型漏洞的防护措施包括：   
  
- 从可信来源获取训练数据并验证其质量，确保训练数据的完整性。  
  
- 实施可靠的数据净化和预处理技术，以消除训练数据中的潜在漏洞或欺骗内容。  
  
- 使用监测和警报机制来检测LLM中的异常行为或安全问题，这些问题可能会帮助企业及时发现训练数据中毒。  
  
  
  
  
**参考链接：**  
  
  
  
**https://www.csoonline.com/article/3698533/owasp-lists-10-most-critical-large-language-model-vulnerabilities.html**  
  
**相关阅读**  
  
[19项网络安全国家标准获批发布](http://mp.weixin.qq.com/s?__biz=MjM5Njc3NjM4MA==&mid=2651124199&idx=2&sn=3905203ca597260d53e31369dffe0e0b&chksm=bd1441348a63c8225a3b7e57311b8f152cf45f0af1b3f20f56ba503b7ad5ff09718769a5bfeb&scene=21#wechat_redirect)  
  
  
[六一节思考，如何向儿童传授网络安全知识？](http://mp.weixin.qq.com/s?__biz=MjM5Njc3NjM4MA==&mid=2651124199&idx=1&sn=dd5b9f9fec3f67e0aa1d255d41def930&chksm=bd1441348a63c82212d016020e92059f89ceb6cdfca691967401625d9f7b80fa6d55d6c87c2e&scene=21#wechat_redirect)  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/kuIKKC9tNkAZCYzVo8chRhenUiaciciar8gfTILGNk9w3aNO0K6kU5uQ4Ah35lQCs6hUWSQb7TVibMUKK0fVPBjyow/640?wx_fmt=png "")  
  
合作电话：18311333376  
  
合作微信：aqniu001  
  
投稿邮箱：editor@aqniu.com  
  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/kuIKKC9tNkAfZibz9TQ8KWj4voxxxNSGMAGiauAWicdDiaVl8fUJYtSgichibSzDUJvsic9HUfC38aPH9ia3sopypYW8ew/640?wx_fmt=gif&wxfrom=5&wx_lazy=1 "")  
  

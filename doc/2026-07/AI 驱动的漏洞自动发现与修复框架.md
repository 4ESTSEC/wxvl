#  AI 驱动的漏洞自动发现与修复框架  
原创 Red Hunter
                    Red Hunter  黑白之道   2026-07-07 00:32  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/nGzNudUIJ6PEtrnib92D2oK87RibXnQvpZCZAkK3atZrcptiahK7489EsepicU8jY03gBQqJmM9H50cKQRKIY72vFj6hjQLAHDmMSOQic6mQ150A/640?wx_fmt=png "")  
> **导语**  
：Anthropic 开源 Defending Code Reference Harness，这是一套基于 Claude 构建的自主漏洞发现与修复参考框架，将威胁建模、代码扫描、漏洞分级排序到补丁生成全部串联成自动化流水线。安全团队可直接参考或二次开发，一周内即可搭建自有漏洞扫描管道。  
  
## 项目背景  
  
自 Claude Mythos Preview 推出以来，Anthropic 与多家大型组织的安全团队展开了深度合作。在合作中积累的实践经验最终汇聚成了这套开源工具——Defending Code Reference Harness。  
  
项目的核心理念是：用 AI 替代传统规则型静态分析工具，让安全扫描像人类研究员一样理解代码逻辑，而不是死板地匹配已知漏洞模式。传统工具能发现明文密码、过期加密算法等常见问题，但对业务逻辑漏洞、访问控制缺陷这类深层风险往往无能为力。  
## 核心能力  
  
该框架由两部分组成：  
  
**Claude Code Skills**  
（交互式技能）提供四条核心指令。/threat-model  
 负责在目标代码库上构建威胁模型，划定扫描范围；/vuln-scan  
 基于威胁模型执行有边界约束的静态扫描；/triage  
 对扫描结果进行去重、验证和风险优先级排序；/patch  
 针对已确认漏洞自动生成候选修复方案。每条指令都具备独立的子代理，可精准绑定模型版本。  
  
**Harness 自主流水线**  
（harness/）将上述能力整合为一条端到端链路：侦察 → 发现 → 验证 → 报告 → 修复。流水线针对 C/C++ 代码库中的内存安全漏洞进行了专项优化，内部使用 Docker 容器配合 AddressSanitizer 执行目标代码，并强制在 gVisor 沙箱中运行以确保隔离性。  
## 工作流程  
  
项目的推荐落地路径分为四个阶段。Day 1 的目标是走通全流程闭环——用 /threat-model  
 建立威胁模型，再用 /vuln-scan  
 和 /triage  
 完成第一次扫描与分级，最后用 /patch  
 产出候选补丁。到 Day 2，可将自主流水线跑在 C/C++ 示例库上进行端到端验证。Days 3–5 根据实际业务代码库调整检测器和漏洞类别。Week 2 启动常态化自主扫描、分级与补丁生成。  
  
对于 C/C++ 内存安全漏洞场景，流水线使用 ASAN 插桩构建，在 gVisor 容器内运行目标代码，确保恶意 payload 不会逃逸到宿主机。  
  
![AI 安全漏洞自动发现与修复流水线](https://mmbiz.qpic.cn/sz_mmbiz_png/nGzNudUIJ6PrlbRWssB0nJugR8FugdeBic4APIJlvLdAbicNdOugSu9iaVeKfWzplOYcEWlbsicibzwuBJm8RGRdic0rcTNM8jWWaMqc21yVPI6fk/640?wx_fmt=png "AI 安全漏洞自动发现与修复流水线")  
## 安全注意事项  
  
项目文档明确指出，交互式技能（威胁建模、扫描、分级）仅读取和写入代码库文件，无沙箱也可安全使用，但仍要求操作者在 Claude Code 中逐条审批工具调用。自主流水线包含目标代码执行步骤，因此默认拒绝在 gVisor 沙箱外运行，除非显式覆盖该限制。  
## 下载与部署  
  
**GitHub 仓库（开源参考实现）：**  
 https://github.com/anthropics/defending-code-reference-harness  
  
**托管商业版（Claude Security）：**  
 https://claude.com/product/claude-security  
  
部署步骤：克隆仓库后在 Claude Code 中打开，执行 /quickstart  
 即可获得 30 秒导览并引导完成首次在 Canary 目标上的全流程演练。如需适配 Java、Python 等其他语言栈，使用 /customize  
 指令即可完成端口移植。  
  
**版权声明**  
：本文由华安普特原创发布，保留所有权利。配图由华安普特授权使用。  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/nGzNudUIJ6Puc8HNiaVFMibMWyXuRCjam9iat0x1rCpsp1iatD7HveaKn7X0FMG8AlYEYZQbWQjAzjbYJjgdDQYTxLsn3W66vehH9V8LV2z9XnE/640?wx_fmt=jpeg "")  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650618755&idx=1&sn=48e0a85464fb20c73b6f338928a8f850&scene=21#wechat_redirect)  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650618776&idx=2&sn=a8fc65fd2a71822830022fc52d967bcd&scene=21#wechat_redirect)  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650618496&idx=1&sn=96ecdff99136258a4bf2cb156542311e&scene=21#wechat_redirect)  
> 👇 点击**阅读原文**  
，访问我的网站  
  
  

#  curl 发布史上规模最大的 CVE 公告，修复一个存续长达 25 年的漏洞  
鹏鹏同学
                    鹏鹏同学  黑猫安全   2026-06-26 00:50  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/DYqn7TU9icq2lBxZc8Lm4Hjl104qOA3tjuXGicUjF76DP3rNBCoOxlIwaLS9YapTTibLymx3xeLpiahAE55qRHUiaXbh3qMu6xglTIkGEXiaLyWoU/640?wx_fmt=png&from=appmsg "")  
  
curl 维护者在一次更新中修复了 18 个安全漏洞，其中一个漏洞的历史长达 25 年。这并不是笔误，该漏洞自 21 世纪初就一直潜伏在代码中。curl 是一款应用极广的开源网络数据传输工具与开发库，运行在超过 300 亿台设备上。  
  
2026 年 5 月 11 日，curl 项目首席开发者表示，Anthropic 旗下 Mythos 模型只挖掘出 1 个 CVE 漏洞。此后，AISLE 团队与其他机构对该工具展开深度分析，又陆续挖出另外 18 处安全缺陷。  
  
AISLE 发布的报告中写道：“在这新增的 18 条 CVE 漏洞里，AISLE 团队独占 6 条，同时还针对 curl 与 libcurl 挖出多项有效缺陷。排名第二的 AI 安全团队仅挖到 3 条 CVE，而使用 Anthropic 与 OpenAI 模型的研究人员各自只发现了 1 条漏洞。这一成果进一步证明，AISLE 这套不绑定特定大模型的检测系统，能够以极低的成本，在各类部署环境下超越顶尖前沿模型。”  
  
本次事件最值得关注的并非漏洞数量，而是折射出 curl 项目的演变现状。显而易见的低级漏洞早已被清理干净，剩余隐患全都藏在状态处理逻辑、老旧协议分支、可复用连接这类冷门场景中，运行行为往往难以预判。  
  
AISLE 一共发现 6 条全新的 curl CVE 漏洞，涵盖 libcurl 中的内存缺陷与逻辑漏洞。其中年代最久远的漏洞编号为 CVE-2026-8932，最早出现在 2001 年 3 月发布的 curl 7.7 版本。  
  
漏洞公告原文描述：“即便客户端证书或私钥配置已经变更，libcurl 仍会继续复用旧连接，可造成身份认证绕过。”  
  
这条漏洞的关键影响在于连接复用机制。在实际场景中，一旦复用已有连接，新更换的客户端证书与密钥配置就会失效。命令行版本的 curl 不受此漏洞影响，但集成了 libcurl 库的各类应用程序会遭到波及。  
  
本次检测的亮点在于 AI 辅助分析的成效：研究人员不再只找到单个漏洞，而是批量挖出凭证处理、内存安全、主机校验等多个维度的缺陷，包含凭证混淆、二次释放、释放后重用以及主机校验不严等多种问题。  
  
如今，curl 已经不存在随手就能找出的简单漏洞。遗留问题都深埋业务逻辑之中，而非基础语法层面。这也是安全研究者反复对其开展审计的原因，同时意味着在代码审计流程里，AI 分析工具的重要性已经超过了传统扫描软件。  
  
尽管数十亿设备都在运行 curl，本次爆出的所有漏洞，目前还没有出现已被公开利用的真实攻击案例。这一现状既让人安心，也警示我们：最难发现的漏洞，往往会长期潜伏，直到某天才被攻击者盯上。  
  
以下为 AISLE 团队挖掘出的全部漏洞清单：  
<table><thead><tr><th><section><span leaf="">漏洞编号</span></section></th><th><section><span leaf="">漏洞领域</span></section></th><th><section><span leaf="">漏洞详情</span></section></th></tr></thead><tbody><tr><td><section><span leaf="">CVE-2026-8926</span></section></td><td><section><span leaf="">.netrc 凭证处理</span></section></td><td><section><span leaf="">当 URL 中指定用户名但未填写密码时，curl 可能为同一主机匹配到其他用户的密码，造成凭证混淆。</span></section></td></tr><tr><td><section><span leaf="">CVE-2026-8925</span></section></td><td><section><span leaf="">SASL 身份认证</span></section></td><td><section><span leaf="">在启用 SASL 的协议交互过程中，curl 会对同一个 GSASL 上下文执行两次清理与释放操作，引发内存二次释放漏洞。</span></section></td></tr><tr><td><section><span leaf="">CVE-2026-8932</span></section></td><td><section><span leaf="">mTLS 连接复用</span></section></td><td><section><span leaf="">客户端证书或私钥配置变更后，libcurl 仍然复用旧连接，可实现身份认证绕过。</span></section></td></tr><tr><td><section><span leaf="">CVE-2026-9080</span></section></td><td><section><span leaf="">多套接字回调生命周期</span></section></td><td><section><span leaf="">在套接字回调函数内调用 curl_easy_pause ()，会导致 libcurl 通过已释放的内部指针执行写入操作，产生释放后重用漏洞。</span></section></td></tr><tr><td><section><span leaf="">CVE-2026-9547</span></section></td><td><section><span leaf="">SSH 主机校验</span></section></td><td><section><span leaf="">使用 libssh 后端时，配置主机密钥回调的 SCP/SFTP 传输会接纳本应被拒绝的服务器密钥类型，存在主机校验不严问题。</span></section></td></tr><tr><td><section><span leaf="">CVE-2026-10536</span></section></td><td><section><span leaf="">HTTP/2 流依赖</span></section></td><td><section><span leaf="">对开启 HTTP/2 依赖项的句柄执行重置再清理操作，会让 libcurl 访问已经释放的内存状态，触发释放后重用漏洞。</span></section></td></tr></tbody></table>  
  

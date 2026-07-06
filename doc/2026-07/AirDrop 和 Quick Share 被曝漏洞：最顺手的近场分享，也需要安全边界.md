#  AirDrop 和 Quick Share 被曝漏洞：最顺手的近场分享，也需要安全边界  
原创 tcode
                    tcode  字节脉搏实验室   2026-07-06 04:31  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/nOo5YmK1PHzWjwhaTQ4hDiaJuHmwDct2by0zhZIVfSib4bU1CZIMJMicX2qnchJJBdT5IT0Hlvew5iatCTAXenWQFJelJzn6s47628iawXU3a6q4/640?wx_fmt=png&from=appmsg "")  
  
    Apple 的 AirDrop、Google 与 Samsung 的 Quick Share，解决的是一个很实用的问题：不用数据线、不加好友、不连同一个账号，就能把文件发给附近设备。也正因为它太方便，很多人会在会议、地铁、展会、学校和办公室里长期开着。  
  
    Help Net Security 在 2026 年 7 月 5 日的周度复盘中继续推荐这项研究。CISPA Helmholtz Center for Information Security 的研究人员此前发布论文，对 AirDrop 和 Quick Share 的应用层协议做了系统性研究，发现六项漏洞，覆盖 macOS、iOS、Android 与 Windows 相关实现。The Hacker News、Tom’s Guide 等媒体也在 6 月 30 日至 7 月 1 日跟进报道。  
  
    这不是“所有人马上会被偷文件”的恐慌故事，但它确实提醒我们：越是无感、自动、近场响应的功能，越需要把默认暴露面控制好。  
  
核心事实  
  
    研究显示，AirDrop 和 Quick Share 都依赖后台高权限服务来响应附近设备。攻击者只要在无线范围内，就可能与这些服务发生协议交互；在某些配置和实现下，问题可能导致服务崩溃、协议状态绕过或潜在内存安全风险。  
  
    公开信息显示，Apple 已修复其中一个 AirDrop 相关问题，但部分报告仍在协调披露中；Google 已为 Quick Share for Windows 的相关代码落地修复，公开 CVE 分配仍有待进一步披露；Samsung 相关问题也处于调查或协调流程中。  
  
影响分析  
  
    近场分享功能的安全难点，在于它把“陌生设备输入”放到了用户感知之前。  
  
    为了让体验足够顺滑，设备必须在用户点击同意之前就做一部分发现、握手、解析和状态处理。换句话说，用户还没看到弹窗，系统底层服务已经开始和陌生设备说话了。只要这部分代码处理复杂格式、状态机或并发连接，就可能产生新的攻击面。  
  
    对普通用户来说，风险主要来自公共空间和“所有人可发现”设置。对企业来说，风险还包括办公区、展会、客户现场、开放会议室和共享工位，因为攻击者不一定要接入企业网络，只要靠近目标设备，就可能触发部分协议交互。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/nOo5YmK1PHwocVxZLXggFZL2HZCicpcw8OMrxOZ2icTRx6AjuAvgJPoG2z0wfaupSNaus2XDvzSwLOaoDxDSJq2sdiaPfibS96IPZe5dRsZyGD4/640?wx_fmt=png&from=appmsg "")  
  
普通用户和企业应对建议  
  
    第一，更新系统和客户端。iPhone、Mac、Android 手机、Windows 电脑上的近场分享相关更新都应尽快安装，特别是长期不重启、不更新的笔记本。  
  
    第二，不要长期开启“所有人可发现”。日常建议设置为仅联系人、仅设备所有者、接收关闭，或只在需要传文件时临时打开。  
  
    第三，在公共场所减少自动发现。地铁、机场、会议、展会、学校和咖啡店里，不建议长时间让设备对陌生人可见。  
  
    第四，企业要纳入 MDM 管理。移动设备管理策略可以限制 AirDrop 或 Quick Share 的可用范围，特别是高敏岗位、研发终端、财务终端和涉密会议设备。  
  
    第五，关注 Windows 客户端。很多企业安全团队会盯手机系统更新，却忽略 Windows 上安装的近场分享客户端。它同样需要版本管理和补丁追踪。  
  
    第六，把“便利功能”纳入风险教育。近场分享、剪贴板同步、投屏、蓝牙配对、连续互通等功能都属于便利性很强的入口，不应被默认视为无风险。  
  
事实、推测与观点区分  
  
    事实：CISPA 研究人员披露了 AirDrop 与 Quick Share 的六项漏洞研究；公开报道称相关问题影响多个操作系统生态，涉及超过 50 亿活跃设备；部分修复已经开始，部分问题仍处于协调披露或待公开 CVE 阶段。  
  
    推测：公开研究没有证明攻击者已在现实世界中大规模利用这些问题，也不能推断所有设备都能被直接窃取文件。  
  
    观点：近场分享不应该被禁用到失去价值，但默认可见性应更保守。对普通用户来说，最实用的安全动作，就是更新设备并收紧“谁能发现我”。  
  
结语  
  
    AirDrop 和 Quick Share 的问题，提醒我们安全不是便利的反面，而是便利能长期存在的前提。最好的体验，应该是在你想分享时顺手，在你不想分享时安静。  
  
关键来源  
  
•	Help Net Security，2026-07-05，Week in review: AirDrop and Quick Share vulnerabilities included in weekly review：https://www.helpnetsecurity.com/2026/07/05/week-in-review-simplehelp-vulnerability-exploited-oracle-ebs-payments-flaw-under-attack/  
  
•	Help Net Security，2026-06-30，AirDrop and Quick Share vulnerabilities affect protocols on five billion devices as fixes begin：https://www.helpnetsecurity.com/2026/06/30/apple-airdrop-google-samsung-quick-share-vulnerabilities/  
  
•	CISPA / arXiv，2026-06-25，Protocol Prying: Systematic Vulnerability Research in the Apple AirDrop and Android Quick Share Proximity Transfer Protocols：https://arxiv.org/abs/2606.26967  
  
•	The Hacker News，2026-06-30，AirDrop and Quick Share Flaws Let Nearby Attackers Trigger Crashes and Bypass Checks：https://thehackernews.com/2026/06/airdrop-and-quick-share-flaws-let.html  
  
•	Tom’s Guide，2026-07-01，Major security holes in AirDrop and QuickShare put your phone at risk of attack：https://www.tomsguide.com/phones/major-security-holes-in-airdrop-and-quickshare-put-your-phone-at-risk-of-attack-heres-how-to-protect-yourself  
  
  

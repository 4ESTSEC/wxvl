#  Citrix 两个 NetScaler RCE 0day 已遭活跃利用  
Swati Khandelwal
                    Swati Khandelwal  代码卫士   2026-09-28 09:08  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/Az5ZsrEic9ot90z9etZLlU7OTaPOdibteeibJMMmbwc29aJlDOmUicibIRoLdcuEQjtHQ2qjVtZBt0M5eVbYoQzlHiaw/640?wx_fmt=gif "")  
    
聚焦源代码安全，网罗国内外最新资讯！  
  
编译：代码卫士  
  
**Citrix****公司于****9****月****27****日证实称，****Citrix NetScaler ADC****和****NetScaler Gateway****中存在两个可导致远程代码执行的严重漏洞且已遭在野利用（****CVE-2026-88771****和****CVE-2026-88772****，****CVSS****评分均为****9.5****）。该公司发布了针对这两个漏洞以及另外六个缺陷的修复方案。**  
  
就在该公告发布的前一天，安全公司watchTowr表示NetScaler两个 0day远程代码执行漏洞已被利用，并且此前一些管理员表示已将设备下线。Citrix并未说明这两个漏洞和watchTowr所述漏洞是否相同，但描述一致。  
  
NetScaler ADC和NetScaler Gateway位于企业网络边缘，负责处理VPN和远程访问、负载均衡以及用户身份验证。Citrix 在公告中提到的漏洞信息简述如下：  
  
- CVE-2026-88771（CVSS v4 评分：9.5）是一个不正确的输入验证漏洞，可导致未经身份验证的攻击者运行任意命令，影响所有 NetScaler ADC 和 NetScaler Gateway 部署，无需额外功能即可遭利用。  
  
- CVE-2026-88772（CVSS v4 评分：9.5）是一个内存溢出漏洞，可导致远程代码执行或拒绝服务，影响启用了 DTLS 的设备。DTLS 默认在 VPN 虚拟服务器上开启，因此除非显式关闭 DTLS，否则 NetScaler Gateway 会受到影响。  
  
  
  
该公司表示：“已观察到在未缓解的 NetScaler 部署上利用 CVE-2026-88771 和 CVE-2026-88772 的情况。”但并未说明这些漏洞被利用的范围有多广、由谁利用，或从何时开始。  
  
该公告是 Citrix 首次公开披露这些漏洞，因此两者在修复公开前就已遭到攻击。公告未列出任何应变方法，也没有失陷指标。运行 14.1-73.32 和 13.1-63.21 的设备——这些版本在 8 月修复了已被利用的身份验证绕过漏洞 CVE-2026-19490——包含在受影响范围内，需要新的更新。修复方案位于以下版本中，Citrix 敦促受影响的客户尽快安装：  
  
- NetScaler ADC 和 NetScaler Gateway 14.1-73.37 及更高版本  
  
- NetScaler ADC 和 NetScaler Gateway 13.1-64.23 及 13.1 的更高版本  
  
- NetScaler ADC 14.1-FIPS 14.1-73.37 FIPS 及 14.1-FIPS 的更高版本  
  
- NetScaler ADC 13.1-FIPS 和 13.1-NDcPP 13.1-37.279 及 13.1-FIPS 和 13.1-NDcPP 的更高版本  
  
  
  
该公告涵盖客户管理的设备，包括用于 Secure Private Access Hybrid 部署的 NetScaler 实例。Citrix 升级自有云服务和 Citrix 管理的 Adaptive Authentication。13.1 版本修复是在该分支根据 Citrix 发布计划于 9 月 15 日达到维护终止后发布的。  
  
公告未将另外六个漏洞列为已被利用：  
  
- CVE-2026-88773（CVSS v4 评分：9.3）是一个 HTTP 请求走私漏洞，存在于具有 HTTP 或 SSL 类型的负载均衡、内容交换、VPN 或身份验证虚拟服务器的设备上。  
  
- CVE-2026-88774（CVSS v4 评分：7.0）是一个策略绕过漏洞，存在于任何策略使用基于 HTTP URL 的表达式的设备上。  
  
- CVE-2026-88775（CVSS v4 评分：8.8）是一个内存溢出漏洞，可导致不可预测的行为或 DoS，存在于配置为 Gateway（SSL VPN、ICA Proxy、CVPN、RDP Proxy）或身份验证、授权和审计（AAA）虚拟服务器的设备上。  
  
- CVE-2026-88776（CVSS v4 评分：8.8）是一个内存溢出漏洞，可导致不可预测的行为或 DoS，存在于 Oracle 类型的负载均衡虚拟服务器上。  
  
- CVE-2026-88777（CVSS v4 评分：8.8）是一个内存溢出漏洞，可导致不可预测的行为或 DoS，存在于启用了非 HTTP 第 7 层协议功能（如 FTP、RTSP 或 DNS64）的负载均衡、内容交换或 CGNAT-LSN/NAT64 设置上。  
  
- CVE-2026-88778（CVSS v4 评分：8.8）是一个 TCP 初始序列号（ISN）预测漏洞，存在于具有基于 TCP 的虚拟服务器（如 HTTP、SSL 或 TCP）且 Enhanced ISN Generation 被禁用的设备上。Citrix 建议受影响的设备应用一项 TCP 配置变更，开启 Enhanced ISN Generation。  
  
  
  
9月26日，watchTowr公司发布帖子表示回应有关多个未修复 NetScaler RCE 漏洞在野被利用的传言。“虽然细节很少，但信息可信，”它写道。该公司在UTC 时间 22:19 的后续帖子称，这两个漏洞是在取证调查期间发现的，并预计 Citrix 的沟通和补丁会在 9 月 28 日那周初发布。  
  
9 月 26 日，一名管理员在 r/Citrix 上发帖称，IT 供应商的安全团队打电话建议立即关闭他们的 NetScaler，但没有提供细节。该帖中其他人表示他们的组织机构也这样做了。供应商的告警信息来自何处尚未确定。由于这些漏洞在修复公开之前就被利用，安装更新不会显示攻击者是否先进入了系统。  
  
2025 年，在一个 NetScaler 漏洞作为零日漏洞被用于攻击荷兰组织后机构，荷兰国家网络安全中心表示，仅更新并不能消除风险，因为攻击者可以保留在补丁前获得的访问权限，并告诉管理员运行其检查脚本。  
  
Citrix 关于怀疑 NetScaler 遭入侵的现有指南表示：  
  
- 首先保存证据：VPX 实例的快照、远程 syslog 服务器和 NetScaler Console 上保存的日志、技术支持包，以及数据包引擎的核心转储。  
  
- 将设备与网络隔离。  
  
- 更改所存储的每个服务账户密码和密钥，重置通过它登录的用户密码，并撤销证书和私钥。  
  
- 将管理接口保持在互联网之外。指南提到：“NetScaler Management Services 永远不应暴露于公共互联网。”  
  
  
  
荷兰国家网络安全中心的 2025 年检查脚本覆盖活动设备、核心转储和完整 NetScaler 镜像，是另一个选项，但有局限。活动设备脚本的 README 提到会查找表明遭入侵的文件，并不针对某个特定漏洞，也不保证有效。该代码最后一次更新是在 2025 年 9 月。Citrix 和 NetScaler 的母公司Cloud Software Group 以及 watchTowr 尚未就此事置评。  
  
  
代码卫士试用地址：https://sast.qianxin.com/  
  
开源卫士试用地址：https://oss.qianxin.com/  
  
  
  
  
  
  
  
  
  
**推荐阅读**  
  
[Citrix NetScaler 存在两个严重漏洞，无需凭证即可绕过认证机制](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247526935&idx=1&sn=07147e7ec3cbfb9ffe750a7853798e1e&scene=21#wechat_redirect)  
  
  
[Citrix 修复可导致文件读取和拒绝服务的多个漏洞](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247526489&idx=1&sn=70f1d92bbf820f6f31eaeaed6636eb22&scene=21#wechat_redirect)  
  
  
[Citrix：尽快修复这两个 NetScaler 漏洞](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247525554&idx=2&sn=1cd600c5708dc44ab8e2421ef606e780&scene=21#wechat_redirect)  
  
  
[Citrix 紧急修复已遭利用的 NetScaler RCE 0day漏洞](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247523897&idx=1&sn=e11d4a106337143972fe49dc8aee936b&scene=21#wechat_redirect)  
  
  
  
  
  
**原文链接**  
  
https://thehackernews.com/2026/09/warning-two-unpatched-citrix-netscaler.html  
  
  
题图：Pixa  
b  
ay Licens  
e  
  
  
**本文由奇安信编译，不代表奇安信观点。转载请注明“转自奇安信代码卫士 https://codesafe.qianxin.com”。**  
  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/oBANLWYScMSf7nNLWrJL6dkJp7RB8Kl4zxU9ibnQjuvo4VoZ5ic9Q91K3WshWzqEybcroVEOQpgYfx1uYgwJhlFQ/640?wx_fmt=jpeg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/oBANLWYScMSN5sfviaCuvYQccJZlrr64sRlvcbdWjDic9mPQ8mBBFDCKP6VibiaNE1kDVuoIOiaIVRoTjSsSftGC8gw/640?wx_fmt=jpeg "")  
  
**奇安信代码卫士 (codesafe)**  
  
国内首个专注于软件开发安全的产品线。  
  
   ![](https://mmbiz.qpic.cn/mmbiz_gif/oBANLWYScMQ5iciaeKS21icDIWSVd0M9zEhicFK0rbCJOrgpc09iaH6nvqvsIdckDfxH2K4tu9CvPJgSf7XhGHJwVyQ/640?wx_fmt=gif "")  
![]( "")  
![]( "")  
  
   
觉得不错，就点个 “  
在看  
” 或 "  
赞  
”   
  

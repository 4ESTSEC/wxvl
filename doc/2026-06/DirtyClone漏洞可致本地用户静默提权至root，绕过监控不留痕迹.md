#  DirtyClone漏洞可致本地用户静默提权至root，绕过监控不留痕迹  
 FreeBuf   2026-06-28 10:00  
  
![FreeBuf](https://mmbiz.qpic.cn/mmbiz_gif/icBE3OpK1IX2NV6fSibicic34AjkuKv6dIwfqyef6OnUcmG2XjgTJUK7T9fFqU13y6MQAibTqicEHibrubo8CvibZINergqbuC2LpelZwO1XHoHXMmA/640?wx_fmt=gif "")  
  
  
![配图](https://mmbiz.qpic.cn/sz_mmbiz_png/icBE3OpK1IX0v17jBXRmHvRnDOpGyxNvyiaorpL23ZOP33DmqfAH7nMQ2wkfibmkGQ08EibCfTRleXfFibNdmuFibS7rADFO0gc7EcQIbc1euC1Hc/640?wx_fmt=png "")  
  
  
Part  
01  
  
漏洞概述  
  
JFrog安全研究团队于6月25日发布了针对CVE-2026-43503（CVSS评分8.8）的完整漏洞利用分析报告，该漏洞被命名为DirtyClone，是DirtyFrag漏洞家族中的第四个成员。这些漏洞具有相同的根本性缺陷：内核错误地将文件支持的内存视为网络数据包数据，并在本应执行复制操作时执行了原地网络写入操作。若您的内核尚未应用5月21日发布的主线补丁，请立即更新。  
  
  
Part  
02  
  
攻击原理  
  
攻击者首先将特权二进制文件（如/usr/bin/su）加载到内存中，将这些内存页面绑定到网络数据包，然后通过其控制的环回IPsec隧道强制内核进行克隆。解密步骤会用攻击者指定的字节覆盖二进制文件的认证逻辑，当下次执行su命令时就会直接获取root权限——而磁盘上的原始文件却保持完好无损。  
  
  
JFrog在报告中指出：该漏洞的严重性在于，任何无特权的本地用户都可以通过操纵Linux页面缓存来获取root权限（LPE）。这种攻击是静默的，不会留下任何内核日志或审计痕迹，并能绕过常见的磁盘完整性监控工具。  
  
  
Part  
03  
  
利用条件与影响  
  
该漏洞利用需要CAP_NET_ADMIN权限来配置IPsec环境。在Debian和Fedora系统中，任何本地用户都可以通过默认启用的非特权用户命名空间获得该能力。  
  
  
报告进一步说明：攻击者首先创建一个新的网络命名空间：unshare -Urn，这将在命名空间内提供网络管理能力。虽然能力是命名空间隔离的，但页面缓存在主机级别是共享的，因此如果通过共享映射修改了文件支持的页面，其影响可能会传播到使用这些页面的其他进程。  
  
  
Ubuntu 24.04及更高版本通过AppArmor限制了命名空间创建，阻断了默认的利用路径，但其他所有采用默认命名空间配置的发行版均受影响。  
  
  
Part  
04  
  
漏洞家族演变  
  
DirtyFrag漏洞家族目前已发现四个成员：  
  
- 4月下旬出现的Copy Fail（CVE-2026-31431）  
  
- 5月7日披露的DirtyFrag（CVE-2026-43284和CVE-2026-43500）  
  
- 5月13日发现的Fragnesia（CVE-2026-46300），该漏洞通过skb_try_coalesce()中的标志丢弃错误绕过了DirtyFrag补丁  
  
  
报告指出：DirtyFrag是影响Linux内核网络核心栈中套接字缓冲区（skb）引用共享页面缓存内存方式的一系列内存破坏漏洞，攻击者随后通过XFRM/IPsec或RxRPC等子系统中的原地加密转换将其武器化。尽管针对不同的数据包克隆或转发路径，但DirtyFrag、Fragnesia和DirtyClone等变体都依赖于相同的技术：诱使内核将只读的文件支持页面缓存内存视为可写的网络缓冲区。  
  
  
Part  
05  
  
修  
复进展  
与临时缓解措施  
  
5月16日，DirtyFrag的原始研究者Hyunwoo Kim提交了一个涵盖剩余片段传输辅助函数的综合补丁。JFrog在5月19日独立重新发现了其中一个受影响函数，构建了可行的漏洞利用程序并进行了报告。合并后的修复补丁于5月21日发布，CVE-2026-43503于5月23日公开，Linux v7.1-rc5作为首个修复版本于5月24日发布。Ubuntu、Debian和SUSE已发布安全公告，Red Hat也在Bugzilla中创建了跟踪条目。  
  
  
若无法立即打补丁，可采用两种缓解措施降低风险：  
  
- 在Debian和Ubuntu上设置kernel.unprivileged_userns_clone=0以阻断基于命名空间的CAP_NET_ADMIN获取路径  
  
- 禁用esp4、esp6和rxrpc内核模块以移除漏洞利用所需的原地解密原语（但会破坏IPsec和AFS功能）  
  
  
DirtyFrag类漏洞可能尚未完全修复——任何在传输过程中丢弃shared-frag标志的片段传输函数都可能成为新的变体，而全面审计内核网络栈中的所有此类路径仍是一项庞大且未完成的工作。  
  
  
参考来源：  
  
DirtyClone: Fourth Linux Kernel Flaw in Six Weeks Escalates to Root  
  
https://securityaffairs.com/194338/uncategorized/dirtyclone-fourth-linux-kernel-flaw-in-six-weeks-escalates-to-root.html  
  
  
**推荐阅读**  
  
[](https://mp.weixin.qq.com/s?__biz=MjM5NjA0NjgyMA==&mid=2651340988&idx=1&sn=0937f2692c838e2a62e89dde8d9dbe41&scene=21#wechat_redirect)  
  
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
  
  
  
![扫码加入AI安全交流群](https://mmbiz.qpic.cn/sz_mmbiz_png/icBE3OpK1IX0UGcrYtIkSYDEgbDkib0yMF23VlKQibpJyRnibia1cD3no5XF7Je0Sic98ytMyvbY9LhO8tKoxBlnibsAXh8CnBTYoAxLReujuqjomI/640?wx_fmt=png "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX1cNPEia7j7bXCX8P8iaDo801yQlaF965NduoqX5nEfgC2mLLgM6VdzcRdkYkeGebHaia3JRK31e08ibfS1WnmYl8DtvPf83e6XW6k/640?wx_fmt=png&from=appmsg "")  
  
  
  
  

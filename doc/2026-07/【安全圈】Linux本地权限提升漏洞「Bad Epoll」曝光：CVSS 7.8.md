#  【安全圈】Linux本地权限提升漏洞「Bad Epoll」曝光：CVSS 7.8  
 安全圈   2026-07-05 11:00  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/aBHpjnrGylgOvEXHviaXu1fO2nLov9bZ055v7s8F6w1DD1I0bx2h3zaOx0Mibd5CngBwwj2nTeEbupw7xpBsx27Q/640?wx_fmt=other&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1 "")  
  
  
**关键词**  
  
  
  
漏洞  
  
## 一、60秒看完全部要点  
- 🎯 Linux核心 **epoll子系统**  
 被发现本地权限提升漏洞 **Bad Epoll**  
（CVE-2026-46242），CVSS评分**7.8分**  
（高危）  
  
- 🐛 根因：**UAF（释放后重用）**  
竞态条件，攻击者可将普通进程提升为root权限  
  
- 💻 影响范围：**Linux桌面系统、服务器**  
以及**Android**  
（Linux核心6.4+版本）均受影响  
  
- 📅 漏洞2023年4月引入，2026年5月正式披露CVE编号，4月底已合并修补程式至Linux核心主线  
  
- 🤖 **Anthropic AI模型Mythos**  
立功：率先发现CVE-2026-43074（另一epoll竞态漏洞），之后Jaeyoung Chung团队补报了Bad Epoll  
  
- ✅ 唯一缓解措施：**套用修补程式**  
（epoll无法禁用，无简单缓解方法）  
  
## 二、技术详解：Bad Epoll漏洞是什么？  
### 2.1 什么是 epoll？  
  
epoll是Linux核心提供的**高效I/O事件通知机制**  
（I/O Multiplexing），广泛应用于Nginx、Redis、HAProxy等需要**同时处理大量连接的网络服务器**  
。它能让一个线程同时监控大量文件描述符（FD），包括网络socket、pipe、eventfd等，而无需为每个连接单独建立线程。  
  
**关键问题：**  
因为epoll是内核级基础组件，**无法禁用**  
，所以Bad Epoll没有简单的缓解手段，唯一有效的补救措施就是套用官方补丁。  
### 2.2 漏洞根因：竞态条件 + UAF  
  
Bad Epoll漏洞的根源在于epoll实现中的**竞态条件（Race Condition）**  
引发的**UAF（Use-After-Free，释放后重用）**  
问题：  
- 攻击者精心构造的时序，使内核在释放某块内存后、重新分配给其他用途前，插入一个操作窗口  
  
- 攻击者的代码趁机访问这块**已被释放但未被重新分配的内存**  
，实现特权提升  
  
- 最终将**无特权的用户进程提升为具有root权限**  
的状态  
  
值得特别注意的是，Bad Epoll与另一CVE-2026-43074均源自**2023年4月**  
引入的同一段epoll代码——两个漏洞同时潜伏了约3年才被发现。  
### 2.3 漏洞发现过程：AI也立功了  
  
2026年2月，韩国首尔大学电脑安全实验室将Bad Epoll问题通报Linux核心安全团队，但维护者提出的补丁原型**并非正确解决方案**  
，讨论随后陷入停滞。  
  
**转折点：**  
2026年4月初，Anthropic的顶尖AI模型**Mythos**  
在代码审计中独立发现了其中一个epoll竞态条件漏洞（**CVE-2026-43074**  
），补丁随后合并至Linux核心主线。  
  
2026年4月底，首尔大学Jaeyoung Chung团队进一步通报Mythos漏掉的另一个epoll竞态条件漏洞（即Bad Epoll，CVE-2026-46242），补丁也于此时合并至Linux核心主线。  
  
**AI辅助漏洞挖掘再下一城：**  
继Anthropic Mythos在2026年4月创下批量挖掘高危漏洞的记录后，Bad Epoll案例再次证明AI在代码安全审计领域的实用价值。  
## 三、影响范围：谁在受影响名单里？  
### 3.1 Linux 发行版  
  
使用**Linux核心6.4版或更新版本**  
，且**未将修补机制回溯移植**  
（backport）的发行版均受影响。Linux核心6.1版不受影响（问题在6.4版才引入）。  
  
**已公告存在CVE-2026-46242的发行版：**  
- **Red Hat**  
- **SUSE**  
- **Debian**  
- **Ubuntu**  
- **Amazon Linux**  
### 3.2 Android 平台  
  
**Pixel 10**  
（Linux核心版本6.6及以上）：Bad Epoll的PoC程序会触发UAF，**存在被利用风险**  
。  
  
Pixel 8及其他采用**Linux核心版本6.1的设备不受影响**  
，原因是该漏洞在6.4版才引入。  
  
Jaeyoung Chung进一步说明：Google kernelCTF项目累计有约130个可利用漏洞，但仅10个能用于取得Android root权限，Bad Epoll正是其中之一。原因是其他类似Copy Fail漏洞需要搭配特定模块利用，而Android恰好不加载这些模块——这反而让Bad Epoll成为**Android上少数可用的提权路径之一**  
。  
## 四、补丁状态与处置建议  
### ✅ 补丁已入主线，处置分两步走  
  
**第一步：**  
Bad Epoll的修补程式已于**2026年4月底**  
合并至Linux核心主线（CVE-2026-46242）。使用主线内核的用户应确保更新至最新版。  
  
**第二步：**  
各Linux发行版需将主线的修补机制**回溯移植**  
至各自维护的内核分支。请检查你的发行版核心安全更新，确认是否已推送包含CVE-2026-46242补丁的版本更新。  
### 🔧 运维处置建议  
1. **立即核查：**  
运行 uname -r  
 确认内核版本；若显示6.4及以上版本，立即检查安全更新  
  
1. **关注发行版公告：**  
Red Hat、SUSE、Debian、Ubuntu、Amazon均已发布安全通告，对照检查是否已在你的版本上推送补丁  
  
1. **Android用户：**  
Pixel 10用户需等待Google推送2026年7月安全补丁更新；其他机型（6.1内核）暂不受影响  
  
1. **无补丁期间的缓解：**  
由于epoll无法禁用，暂时没有简单的workaround；核心缓解手段就是**尽快打补丁**  
  
1. **服务器场景特别注意：**  
运行Nginx、Redis、HAProxy等依赖epoll的高并发服务，务必优先修复——这些服务的root权限落入攻击者手中影响最为严重  
  
## 五、漏洞时间线  
<table><tbody><tr style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;background: rgb(248, 249, 250);"><th style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 8px;outline: 0px;overflow-wrap: break-word !important;word-break: break-all;hyphens: auto;border: 1px solid rgb(221, 221, 221);background: rgb(247, 247, 247);max-width: 100%;box-sizing: border-box !important;"><section><span leaf="">时间</span></section></th><th style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 8px;outline: 0px;overflow-wrap: break-word !important;word-break: break-all;hyphens: auto;border: 1px solid rgb(221, 221, 221);background: rgb(247, 247, 247);max-width: 100%;box-sizing: border-box !important;"><section><span leaf="">事件</span></section></th></tr><tr style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><td style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 8px;outline: 0px;overflow-wrap: break-word !important;word-break: break-all;hyphens: auto;border: 1px solid rgb(221, 221, 221);max-width: 100%;box-sizing: border-box !important;"><section><span leaf="">2023年4月</span></section></td><td style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 8px;outline: 0px;overflow-wrap: break-word !important;word-break: break-all;hyphens: auto;border: 1px solid rgb(221, 221, 221);max-width: 100%;box-sizing: border-box !important;"><section><span leaf="">问题代码被引入Linux核心epoll子系统（Bad Epoll + CVE-2026-43074同源）</span></section></td></tr><tr style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><td style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 8px;outline: 0px;overflow-wrap: break-word !important;word-break: break-all;hyphens: auto;border: 1px solid rgb(221, 221, 221);max-width: 100%;box-sizing: border-box !important;"><section><span leaf="">2026年2月</span></section></td><td style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 8px;outline: 0px;overflow-wrap: break-word !important;word-break: break-all;hyphens: auto;border: 1px solid rgb(221, 221, 221);max-width: 100%;box-sizing: border-box !important;"><section><span leaf="">首尔大学电脑安全实验室向Linux核心安全团队通报漏洞，维护者补丁原型不正确</span></section></td></tr><tr style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><td style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 8px;outline: 0px;overflow-wrap: break-word !important;word-break: break-all;hyphens: auto;border: 1px solid rgb(221, 221, 221);max-width: 100%;box-sizing: border-box !important;"><section><span leaf="">2026年4月初</span></section></td><td style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 8px;outline: 0px;overflow-wrap: break-word !important;word-break: break-all;hyphens: auto;border: 1px solid rgb(221, 221, 221);max-width: 100%;box-sizing: border-box !important;"><section><span leaf="">Anthropic AI模型Mythos发现CVE-2026-43074（另一epoll竞态漏洞），补丁入主线</span></section></td></tr><tr style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><td style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 8px;outline: 0px;overflow-wrap: break-word !important;word-break: break-all;hyphens: auto;border: 1px solid rgb(221, 221, 221);max-width: 100%;box-sizing: border-box !important;"><section><span leaf="">2026年4月底</span></section></td><td style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 8px;outline: 0px;overflow-wrap: break-word !important;word-break: break-all;hyphens: auto;border: 1px solid rgb(221, 221, 221);max-width: 100%;box-sizing: border-box !important;"><section><span leaf="">Jaeyoung Chung团队通报Bad Epoll（CVE-2026-46242），补丁合并至Linux核心主线</span></section></td></tr><tr style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><td style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 8px;outline: 0px;overflow-wrap: break-word !important;word-break: break-all;hyphens: auto;border: 1px solid rgb(221, 221, 221);max-width: 100%;box-sizing: border-box !important;"><section><span leaf="">2026年5月底</span></section></td><td style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 8px;outline: 0px;overflow-wrap: break-word !important;word-break: break-all;hyphens: auto;border: 1px solid rgb(221, 221, 221);max-width: 100%;box-sizing: border-box !important;"><section><span leaf="">CVE-2026-46242正式分配编号，CVSS 7.8</span></section></td></tr><tr style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><td style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 8px;outline: 0px;overflow-wrap: break-word !important;word-break: break-all;hyphens: auto;border: 1px solid rgb(221, 221, 221);max-width: 100%;box-sizing: border-box !important;"><section><span leaf="">2026年6月底</span></section></td><td style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 8px;outline: 0px;overflow-wrap: break-word !important;word-break: break-all;hyphens: auto;border: 1px solid rgb(221, 221, 221);max-width: 100%;box-sizing: border-box !important;"><section><span leaf="">安全社群开始广泛关注Bad Epoll</span></section></td></tr><tr style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><td style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 8px;outline: 0px;overflow-wrap: break-word !important;word-break: break-all;hyphens: auto;border: 1px solid rgb(221, 221, 221);max-width: 100%;box-sizing: border-box !important;"><section><span leaf="">2026年7月5日</span></section></td><td style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 8px;outline: 0px;overflow-wrap: break-word !important;word-break: break-all;hyphens: auto;border: 1px solid rgb(221, 221, 221);max-width: 100%;box-sizing: border-box !important;"><section><span leaf="">iThome等媒体开始广泛报道</span></section></td></tr></tbody></table>## 六、同期相关：DirtyClone 漏洞  
  
与Bad Epoll同期（2026年6月29日），Linux核心还披露了另一个高危本地权限提升漏洞**DirtyClone**  
（CVE编号待定），CVSS严重度评分高达**8.8分**  
，影响覆盖Linux服务器与桌面系统。两个高危内核漏洞在短期内连续曝光，Linux生态面临较大的补丁压力。  
  
**提醒：**  
如服务器同时存在DirtyClone和Bad Epoll两个漏洞被利用，攻击者可在数秒内完成从普通用户到root的完整权限提升，强烈建议优先测试并部署补丁。  
  
  
   END    
  
  
阅读推荐  
  
  
[【安全圈】Linux惊现"Bad Epoll"零日漏洞，服务器和安卓均中招](https://mp.weixin.qq.com/s?__biz=MzIzMzE4NDU1OQ==&mid=2652077654&idx=1&sn=a686c79c41874b66d1f9e9ea4bf7abf7&scene=21#wechat_redirect)  
  
  
  
[【安全圈】今天是deadline！微软SharePoint高危漏洞正在被疯狂利用](https://mp.weixin.qq.com/s?__biz=MzIzMzE4NDU1OQ==&mid=2652077654&idx=2&sn=1ab239e93225350d7a4fd85eaf6f1aa2&scene=21#wechat_redirect)  
  
  
  
[【安全圈】FortiBleed 凭证窃取活动与 Lynx 勒索软件有关联](https://mp.weixin.qq.com/s?__biz=MzIzMzE4NDU1OQ==&mid=2652077654&idx=3&sn=1364c56512bff2a13e2fe37aa40b0f27&scene=21#wechat_redirect)  
  
  
  
[【安全圈】Claude 解封！特朗普政府撤销对 Anthropic 的限制](https://mp.weixin.qq.com/s?__biz=MzIzMzE4NDU1OQ==&mid=2652077646&idx=1&sn=8203484451ac8bf2c5d2e86005bf8b98&scene=21#wechat_redirect)  
  
  
  
[【安全圈】Claude 解封！特朗普政府撤销对 Anthropic 的限制](https://mp.weixin.qq.com/s?__biz=MzIzMzE4NDU1OQ==&mid=2652077646&idx=1&sn=8203484451ac8bf2c5d2e86005bf8b98&scene=21#wechat_redirect)  
  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/aBHpjnrGylgeVsVlL5y1RPJfUdozNyCEft6M27yliapIdNjlcdMaZ4UR4XxnQprGlCg8NH2Hz5Oib5aPIOiaqUicDQ/640?wx_fmt=gif "")  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/aBHpjnrGylgeVsVlL5y1RPJfUdozNyCEDQIyPYpjfp0XDaaKjeaU6YdFae1iagIvFmFb4djeiahnUy2jBnxkMbaw/640?wx_fmt=png "")  
  
**安全圈**  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/aBHpjnrGylgeVsVlL5y1RPJfUdozNyCEft6M27yliapIdNjlcdMaZ4UR4XxnQprGlCg8NH2Hz5Oib5aPIOiaqUicDQ/640?wx_fmt=gif "")  
  
  
←扫码关注我们  
  
**网罗圈内热点 专注网络安全**  
  
**实时资讯一手掌握！**  
  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/aBHpjnrGylgeVsVlL5y1RPJfUdozNyCE3vpzhuku5s1qibibQjHnY68iciaIGB4zYw1Zbl05GQ3H4hadeLdBpQ9wEA/640?wx_fmt=gif "")  
  
**好看你就分享 有用就点个赞**  
  
**支持「****安全圈」就点个三连吧！**  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/aBHpjnrGylgeVsVlL5y1RPJfUdozNyCE3vpzhuku5s1qibibQjHnY68iciaIGB4zYw1Zbl05GQ3H4hadeLdBpQ9wEA/640?wx_fmt=gif "")  
  
  

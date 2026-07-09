#  GhostLock：已存在15年的内核 UAF 漏洞，影响 Linux 所有发行版  
Swati Khandelwal
                    Swati Khandelwal  代码卫士   2026-07-09 06:57  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/Az5ZsrEic9ot90z9etZLlU7OTaPOdibteeibJMMmbwc29aJlDOmUicibIRoLdcuEQjtHQ2qjVtZBt0M5eVbYoQzlHiaw/640?wx_fmt=gif "")  
    
聚焦源代码安全，网罗国内外最新资讯！  
  
**编译：代码卫士**  
  
**Nebula****安全公司的研究人员发现了一个已存在15年之久的 Linux 内核漏洞 (CVE-2026-43499)，可导致任何已登录用户获得未修复机器的完整 root 控制权限。**  
  
易受攻击代码默认存在于自2011年以来发布的每个主流发行版。利用该漏洞无需特殊权限、无需任何异常设置、无需任何网络访问权限，来自任何本地程序的普通多线程调用即可。  
  
研究人员测试发现将该漏洞转换为可运行的root 利用的可靠性达到97%，并可逃逸容器。该漏洞通过谷歌的 kernelCTF 漏洞奖励计划获得92337美元的赏金。目前尚不存在在野利用的证据，但由于研究人员已发布可运行的利用代码，因此如今任何人皆可运行，打补丁就成为优先级最高的任务。  
  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/t5z0xV2OYfWQuOrrHtYNibN87y1n4FJsklvZx0iaZvuiciap5riaDpWhAa1SZFWG2J7ZArK5g7VyRGm0KJZT9qIxMq9NocEC9U3NuRIM7FQIFIf8/640?wx_fmt=gif&from=appmsg "")  
  
**漏洞原理**  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/t5z0xV2OYfX1B1QsBAiaV4S0TQhg5x9R0Gln7x2rupWR53ibhS8icytIEVkAvc4Y2V0TvvXmTJoTboNyKhKqWXUibNibQacK6O6O4AB1hspuSv9A/640?wx_fmt=gif&from=appmsg "")  
  
  
  
Linux 内核有一套机制，用来防止紧急任务被无关紧要的任务拖住无法执行。清理是该机制的一个组成部分，负责在任务结束等待后进行收尾。正常情况下，该机制运行没有任何状况发生。但在一种罕见情形下，当一次锁操作面临死胡同不得不回退时，清理步骤会在错误的时机运行，从而抹掉错误任务的记录。  
  
该错误导致内核持有了一张“便条”，指向一块已经被丢弃并重新使用的内存。信任这个失效的指针就是整个漏洞的根源，这类问题被称为“释放后使用”。研究人员基于这一点组合多个巧妙的步骤，将这个小失误转化为完全的控制权，最后诱使内核以最高权限的“root”用户身份运行他们自己的代码。在测试机器上，整个过程大约花了五秒钟。  
  
该漏洞自2011年起便存在于Linux内核中，已于今年4月修复，各发行版目前正在陆续推送补丁（3bfdc63936dd）。它影响几乎所有Linux构建版本，CVSS评分为7.8（高危，但非“严重”），因为攻击者需要已经登录到目标机器上。该漏洞由研究人员借助公司的AI漏洞挖掘工具VEGA发现。  
  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/t5z0xV2OYfXTsr2lUEk6SOd4zTfoynouMa5dq2MMeYYG4Luia1Yt6GtMhq67twV1MywfzIkzxib7B7B5wjq6sT0hX0kiaPLOw0G4RAmkSZZJic4/640?wx_fmt=gif&from=appmsg "")  
  
**应对措施**  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/t5z0xV2OYfUL2r64GjBJibfqcVfB3Joyxs9YqVEJms432Sp0o1lz9YuiaOdYrMTjrtsPcubsCPBYnc4aVyJ5L1ghgODcJVVECv8KZhzxrYer4/640?wx_fmt=gif&from=appmsg "")  
  
  
  
用户需安装所用发行版的最新内核，而不仅仅是首个打补丁的构建版本。最初的修复引入了一个单独的系统崩溃问题（CVE-2026-53166），对该崩溃的清理工作截至7月初仍在上游进行调整，因此早期构建版本可能并未包含最终修复方案。  
  
目前尚无完整的应变措施，因为触发该漏洞的操作对于任何本地进程而言都属于常规操作。  
  
各发行版的修复进度目前参差不齐。例如，Ubuntu已为其最新版本及部分云内核打了补丁，但截至7月初，24.04、22.04和20.04 LTS仍被列为“存在漏洞”或“修复中”。用户需查阅所用发行版的安全公告，确认已修复的软件包版本，而不能假设系统会自动推送更新。两个内核构建选项RANDOMIZE_KSTACK_OFFSET和STATIC_USERMODE_HELPER，可增加利用难度，但它们仅是缓解措施，而非修复方案。用户应优先为共享和多租户机器打补丁，包括云服务器、容器和CI运行环境，因为这些地方是攻击者最有可能获得该漏洞所需的本地落脚点。  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/t5z0xV2OYfVwsLA2gxNmTeNGe9d0VujJsiawaDTS59INxIo2SOYm9zvECMJWskYPBvRUJY0pSHgZ2iazYzIxib2YbbLgYc8Sw2iaRaT5S4G60d0/640?wx_fmt=gif&from=appmsg "")  
  
**并非今年唯一的内核提权漏洞**  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/t5z0xV2OYfVQehTnzZrcSRmJNsJoXcHLjx9nTibTLS08RwjuVldgKXZXz3eBKkZibg4GBibJ9KibJicl2utu4fVNtgL4jEZR451IZ3YMRPYhwfKU/640?wx_fmt=gif&from=appmsg "")  
  
  
  
2026年的Linux提权漏洞频发，其中多个漏洞都是由自动化工具发现的。  
  
VEGA发现了GhostLock；而数天前，研究人员披露了它的“近亲”Bad Epoll（CVE-2026-46242），它同样可将普通用户提升为root。该漏洞已通过kernelCTF验证，并且与同类漏洞不同的是，它还能影响Android系统。另外，CVE-2026-43456也是其中一个Linux 内核漏洞。  
  
Bad Epoll与Anthropic的Mythos模型此前发现的一个相关漏洞位于同一代码区域。它们的共同之处在于，都是老旧且广泛使用的内核机制，多年来少有人重新审阅，直到自动化工具开始深入梳理。Futex优先级继承机制可追溯至2011年。这类漏洞并非停留在理论上：另一个2026年漏洞Copy Fail（CVE-2026-31431）已被CISA纳入已知遭利用的漏洞清单中。  
  
GhostLock也是被研究人员称为IonStack的攻击链的后半部分。该攻击链的前半部分是位于 Firefox 中的漏洞CVE-2026-10702，它可用于在浏览器内执行代码并逃逸其沙箱；GhostLock则将其一路提升至root权限。  
  
研究人员已在Android版Firefox上演示了完整的攻击链——从用户点击一次恶意链接到获得完全控制权，全程可行。这正是为什么一个“仅本地”的内核漏洞仍然重要：单独来看，它需要一个落脚点，但一旦与浏览器漏洞结合，就变成了远程入侵。  
  
  
 开源  
卫士试用地址：  
https://oss.qianxin.com/#/login  
  
 代码卫士试用地址：https://sast.qianxin.com/#/login  
  
  
  
  
  
  
  
  
  
**推荐阅读**  
  
[Linux 内核0day已修复：隐藏19+年、利用稳定性极高、影响极广](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247526554&idx=1&sn=62d794dc43dc49ccb43d31be669f3712&scene=21#wechat_redirect)  
  
  
[已存在16年的 Linux KVM 漏洞可导致从客户虚拟机逃逸至宿主机](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247526532&idx=1&sn=cadb21f6556d420bb88549f455cfa643&scene=21#wechat_redirect)  
  
  
[Bad Epoll：Linux 内核新漏洞，导致低权限用户获得 root 权限](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247526510&idx=1&sn=5d8dd588bd5df97068ed04c486fd6458&scene=21#wechat_redirect)  
  
  
[DirtyClone: Linux 内核新漏洞，通过克隆的数据包获得根权限](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247526443&idx=2&sn=c847b004b20a9d1f991dad8dcd81c7b4&scene=21#wechat_redirect)  
  
  
  
  
  
**原文链接**  
  
https://thehackernews.com/2026/07/15-year-old-ghostlock-flaw-enables-root.html  
  
  
题图：Pixa  
bay Licens  
e  
  
  
**本文由奇安信编译，不代表奇安信观点。转载请注明“转自奇安信代码卫士 https://codesafe.qianxin.com”。**  
  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/oBANLWYScMSf7nNLWrJL6dkJp7RB8Kl4zxU9ibnQjuvo4VoZ5ic9Q91K3WshWzqEybcroVEOQpgYfx1uYgwJhlFQ/640?wx_fmt=jpeg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/oBANLWYScMSN5sfviaCuvYQccJZlrr64sRlvcbdWjDic9mPQ8mBBFDCKP6VibiaNE1kDVuoIOiaIVRoTjSsSftGC8gw/640?wx_fmt=jpeg "")  
  
**奇安信代码卫士 (codesafe)**  
  
国内首个专注于软件开发安全的产品线。  
  
   ![](https://mmbiz.qpic.cn/mmbiz_gif/oBANLWYScMQ5iciaeKS21icDIWSVd0M9zEhicFK0rbCJOrgpc09iaH6nvqvsIdckDfxH2K4tu9CvPJgSf7XhGHJwVyQ/640?wx_fmt=gif "")  
![]( "")  
  
   
觉得不错，就点个 “  
在看  
” 或 "  
赞  
” 吧~  
  

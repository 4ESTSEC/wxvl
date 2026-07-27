#  NodeBB 修复由AI发现的8个漏洞，可暴露管理员访问权限和私密聊天记录  
Swati Khandelwal
                    Swati Khandelwal  代码卫士   2026-07-27 06:56  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/Az5ZsrEic9ot90z9etZLlU7OTaPOdibteeibJMMmbwc29aJlDOmUicibIRoLdcuEQjtHQ2qjVtZBt0M5eVbYoQzlHiaw/640?wx_fmt=gif "")  
    
聚焦源代码安全，网罗国内外最新资讯！  
  
**编译：代码卫士**  
  
**上周三，八个****NodeBB****漏洞及其利用代码公开。****Aikido Security****公司将这些漏洞评级为高危级别，并表示该公司的****AI****渗透测试代理在六小时的源代码审查过程中发现了这些漏洞。**  
  
NodeBB   
表示，  
4.14.0  
之前的所有版本均受影响。  
NodeBB  
已修复所有漏洞，管理员应升级至  
4.14.2  
版本。其中最简单的漏洞只需更改设置即可触发。普通论坛成员可将自己的主页设置指向管理员地址，重新加载页面后，管理后台便会为其打开。无需密码，无需利用代码。  
  
虽然  
NodeBB  
论坛自身的界面会阻止该设置，但该阻止机制仅在浏览器端运行，可被绕过。成员此后能够访问的大部分内容为只读状态，包括错误日志和管理员导出的任何用户列表，不过也可以更换网站标志。还有两个漏洞让根本没有账户的攻击者能够访问本应私密的内容。其中一个漏洞允许任何人冒充任意用户，逐一读取私信。另一个漏洞则以正确方式将私有版块的内容交予任何索取者。  
  
最广泛的漏洞存在于  
NodeBB  
构建页面的方式中。该软件先填充页面，然后进行第二轮处理以替换翻译后的文本。用户输入此时已经存在于页面中，可以夹带第二轮处理所查找的代码。这使得攻击者能够在网站几乎任何位置植入链接，包括普通论坛帖子内部，访客点击该链接时便会运行攻击者的代码。  
  
其余漏洞允许攻击者接管已有帖子、虚增帖子的投票数，以及通过联邦宇宙（  
NodeBB  
论坛可加入的互联社交网站网络）中的虚假服务器发起两种植入恶意代码的攻击。  
  
  
**实际受影响范围**  
  
  
  
这八个漏洞并不相同。其中三个漏洞无需在目标论坛拥有账户，两个需要普通成员账户，最后三个需要有人点击链接或打开页面。  
  
根据  
The Hacker News  
的统计，这八个漏洞中有五个位于  
NodeBB  
的联邦代码中，即论坛连接  
Mastodon  
及其它社交网站的部分。这决定了哪些用户面临风险。在版本  
4  
上全新安装的论坛默认开启联邦功能，因此存在全部八个漏洞。从版本  
3  
升级而来的论坛则自动关闭了联邦功能，除非管理员重新开启，否则仅适用其中三个漏洞。  
  
Aikido  
公司未发布各项漏洞的严重程度评分，  
NodeBB  
的发布说明也未进行评级。  
NodeBB  
自身的漏洞赏金标准将跨站脚本和账户接管评为高风险，将获得管理员访问权限评为严重风险。  
  
  
**自五月起分批修复**  
  
  
  
NodeBB  
以静默方式修复了其中大部分漏洞，未说明具体内容。  
The Hacker News  
将每个修复方案与  
NodeBB  
的发布历史进行了对照：四个漏洞的修复方案于五月发布，两个漏洞的修复方案于六月发布，而最大的一个漏洞修复方案  
——  
对软件处理页面文本方式的重新构建  
——  
于  
7  
月  
9  
日在  
4.14.0  
版本中发布。该重构涉及  
325  
个文件。  
  
Aikido  
的文章称这些问题于七月初修复，与该记录不符。其管理后台修复的链接指向  
2024  
年  
1  
月（审查发生两年前）的一次更改，而  
NodeBB  
自身的发布说明则指向五月的一次不同更改。双方均未解释这一差异。  
  
管理员应升级至  
7  
月  
23  
日发布的  
4.14.2  
版本。由于  
4.14.0  
版本更改了页面模板处理文本的方式，自定义主题和插件可能需要更新，因此可能会需要一定的时间。仅关闭联邦功能也并非完整解决方案，因为其中三个漏洞与之无关。  
  
这八个漏洞均无  
CVE  
追踪编号，也无人报告已有利用这些漏洞的攻击发生。另一个独立的  
NodeBB  
联邦漏洞则拥有  
CVE  
编号  
CVE-2026-58593  
，于  
7  
月  
1  
日提交。虽然该漏洞不属于  
Aikido  
的八个漏洞之一，但位于同一代码中，可导致外部服务器以任意本地账户（包括管理员账户）的名义发帖和发送消息。该漏洞需要开启联邦功能，且记录中未指明修复版本。  
  
NodeBB  
的漏洞赏金页面表示拒绝  
AI  
生成的报告，且仅对提交者自行完成的工作支付赏金。该页面涉及的是赏金支付而非漏洞修复，而这八个漏洞是直接报告给维护者并已修复的。  
  
NodeBB  
并非唯一接收此类报告的项目：自动化平台  
n8n  
于六月修复了一个由另一个  
AI  
渗透测试代理发现的登录漏洞。联合创始人  
Julian Lam  
在发布公告中的说明指出，当月持续收到有效的安全报告，  
“  
尽管几乎全部由  
AI  
发现和生成。  
”  
  
这八个漏洞背后的模式相同。  
NodeBB  
在主路径上检查了用户身份，但在到达同一位置的分支路径上跳过了检查。  
  
  
 开源  
卫士试用地址：  
https://oss.qianxin.com/  
  
 代码卫士试用地址：https://sast.qianxin.com/  
  
  
  
  
  
  
  
  
  
**推荐阅读**  
  
[NodeBB原型污染漏洞可导致账户遭接管](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247514951&idx=2&sn=5870d1d7a05482ecc3c9c55c44ee1147&scene=21#wechat_redirect)  
  
  
[开源论坛软件 NodeBB 中存在多个严重漏洞](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247509600&idx=1&sn=582898152ba45f6cbf5e73626f6196e6&scene=21#wechat_redirect)  
  
  
  
  
  
**原文链接**  
  
https://thehackernews.com/2026/07/nodebb-patches-eight-ai-found-flaws.html  
  
  
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
![]( "")  
  
   
觉得不错，就点个 “  
在看  
” 或 "  
赞  
” 吧~  
  

#  黑客攻陷OpenAI：两个漏洞让我们拿下ChatGPT控制权  
原创 Red Hunter
                    Red Hunter  黑白之道   2026-09-19 01:20  
  
![](https://mmbiz.qpic.cn/mmbiz_png/nGzNudUIJ6MNriboHGq2pWxJibkGNjJZlIDRnia3rbksl5FV0nL6uDJTN4JSGsW6GSSLTO9P100TxOjmX3phXb1iasOlovfiascGlib6mcMQjDh5o/640?from=appmsg "")  
> **导语**  
：安全研究团队Hacktron披露，他们用libheif图像解码器的堆缓冲区溢出漏洞，结合OpenAI SSO身份认证的配置缺陷，72小时内接管了多名OpenAI员工的ChatGPT与Codex账户，最终通过GitHub集成访问到OpenAI内部monorepo。为了证明确实拿到了访问权限，他们让员工的Codex在openai/openai仓库开了个PR #1186742。OpenAI为此支付了6,500美元漏洞赏金。  
  
## 一、攻击链全景  
  
整个利用链分七步走：libheif图像解码器 → Debian缺失的安全补丁 → ImageMagick调用libheif → Discourse论坛图片上传 → OpenAI社区论坛 → OpenAI SSO身份缺陷 → ChatGPT/Codex账户接管 → GitHub连接集成 → OpenAI内部仓库。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/nGzNudUIJ6NriamzEjZ5V24x0hqDv4ldb3efsgophRcgC669vnMjBJzLGAibiaBFvd2iadBib9IbN95TJPxjfcEs0qaWGD73atzibY1QqoENqKf1w/640?wx_fmt=png&from=appmsg "")  
  
过去两个月里，任何登录过OpenAI官方帮助论坛的用户或员工都可能被一锅端。因为人们会把各种服务接入Codex与ChatGPT，理论上能接触到的东西包括GitHub、Slack、邮箱，范围大得吓人。  
  
![实锤：在OpenAI内部monorepo提交的PR截图](https://mmbiz.qpic.cn/sz_mmbiz_jpg/nGzNudUIJ6MCY3PTPMpTmmdNhdnMwcJFZBeNCZKOgeFx9ekzxV2JUunXS0YESpsibIrYOyBrKXbCtOsetaEoUyGt7gbGlOIC0cx336SN0N9w/640?from=appmsg "实锤：在OpenAI内部monorepo提交的PR截图")  
## 二、libheif堆缓冲区溢出  
  
Hacktron团队几个月前开始研究前沿AI公司的安全问题，由Harsh Jaiswal带队，加上Mohan Pedhapati和Rahul Maini。后来把研究扩展成了名为「HEIF Heist」的跨产品调查，追踪libheif在Slack、Meta、GitHub Enterprise、Ruby on Rails，以及Next.js、Astro、Gatsby等Node.js框架里的踪迹。大量被广泛使用的软件都依赖这同一个图像处理库。  
  
**自托管Discourse的用户请立即重建**  
：旧版Docker镜像可能含有漏洞版本的libheif依赖，通过图片上传就能执行代码。仅做Web界面升级可能不够，必须从/var/discourse  
执行git pull  
后跑./launcher rebuild app  
。Discourse官方托管客户已修复。  
  
7月23日，团队审Discourse的图片上传管线时发现，HEIC和HEIF文件走了条不寻常的路：FastImage不支持HEIF，于是被转交给ImageMagick的magick命令做转换，底层libheif解析器直接暴露给攻击者可控的文件。  
  
他们用Opus 4.8会话审查安装的libheif包后发现，某些安全修复没被反向移植到Debian的包里，导致HEIC解码时存在堆缓冲区溢出，能拿到越界读写原语。漏洞代码前一年在上游就改过，但那次提交没标注为安全修复，也没分配CVE，所以Debian 12和13都没及时拿到反向移植。Discourse的Docker镜像基于Debian 12，安装的libheif版本是1.19.7（漏洞版本），Debian 13当时也还在用1.19.8。直到2026年8月8日，Debian才发布13的安全更新。  
## 三、Opus 5 救场  
  
7月24日，团队用Opus 4.8开发出本地版的ImageMagick/libheif利用脚本（关闭ASLR）。开了几个独立会话尝试适配Discourse默认开启ASLR的配置，没成功。  
  
当晚Anthropic发布了Claude Opus 5。新会话3小时内产出一份可在Mac ARM64环境跑的利用脚本，然后让他们移植到Discourse的x86-64加jemalloc环境。7月25日凌晨6点确认通过图片上传实现本地RCE。  
  
接着他们把Claude放进自主/goal  
循环里跑，对象是自己的Discourse Cloud实例，通过rce.ee/ctf-forum  
代理出去伪装成CTF靶机——因为Opus拒绝直接对远程实例写利用。上午10点回来检查时，Agent已经拿下Discourse Cloud的RCE，并读取/etc/hosts  
作为访问证据。拿着生成的脚本，团队成功在OpenAI的实例上也拿到了RCE。  
  
确认能无交互接管论坛活跃成员的ChatGPT/Codex账户后，他们立刻把报告发给OpenAI安全团队。然后接管了一名OpenAI员工的账户——该员工的Codex接入了OpenAI的GitHub组织。为了在不读取任何内部代码的前提下证明影响，他们让这名员工的Codex替他们在OpenAI内部monorepo里开了个PR，然后立即停止进一步测试。  
  
他们也通过HackerOne向Discourse上报，周六收报、周日回复、周一修好，响应速度值得点赞。Discourse还立即开始对ImageMagick做沙箱化处理。  
  
要强调的是，这次能提权并不是Discourse的锅——是OpenAI SSO的问题，把论坛被攻破这件事放大成了ChatGPT和Codex访问权限。任何使用OpenAI SSO的第一方或第三方服务被攻破，都会通向同样的结果。  
## 四、发现这些漏洞的成本  
  
整套攻击Agent跑下来几天，人工只花了几小时。整个「HEIF Heist」研究项目横跨Slack、Meta等目标，2个月、3名研究员、token总成本不到3,000美元。把利用适配到每家新公司通常只要一两天。  
  
模型代际能力跳跃明显：Opus 4.8开了好几个会话都没搞出能在开启ASLR条件下工作的利用。Opus 5发布后几小时内把同样的问题丢给它，就成功了。在更广的Campaign里，还看到了Opus 5到GPT-5.6 Sol之间清晰的能力跳跃——那次要在对目标系统一无所知的情况下完成利用。  
  
每个目标都从一次图片上传开始，把内存损坏转化成稳定的内存泄漏或shell，通常完全不需要知道准确的libheif版本、libc版本、部署环境。AI几乎是两眼一抹黑开干，1到2天内适配好每家公司的利用。几千张图片投递、图像处理器反复崩溃，没几家公司检测到异常——只有Shopify除外。  
  
代码执行落到沙箱或受限环境里，模型还帮着搞权限提升、横向移动、绕过已有防御。这不是完全自主的黑客行为，熟练的人工引导仍然重要，但小团队能干的工作量已经被显著放大了。  
## 五、尾声：安全的假设需要重写  
  
![xkcd 2347：现代软件依赖图](https://mmbiz.qpic.cn/mmbiz_jpg/nGzNudUIJ6OauhJ0kiadMZPdpKChxJuIPxXBU9xlghHKFQibWiaTHfBeI4KFyIbTyOEVbFfdz1bwOpdnwEKeZcSUY9wEIpiaL3HXiaZaluIBdibtw/640?from=appmsg "xkcd 2347：现代软件依赖图")  
  
软件长期以来享受着「靠复杂性带来的安全」。代码甚至漏洞都可以是公开的，但把一个Bug打磨成可靠的利用脚本，仍然需要稀缺的专业知识、大量时间、以及对目标环境的了解。已知的内存损坏漏洞运营成本很高，零日漏洞基本只留给最高价值的目标。  
  
这从来就不是真正的安全边界，但它在实践中保护了普通公司。AI正在瓦解这层保护，把稀缺的专业能力更多转化为算力。曾经需要资源充足的团队花几个月干的活，现在能压缩到几天。  
  
安全假设必须跟上攻击者能力。现实的威胁模型应当考虑当下的利用经济学，而不是依赖那些关于「谁能发起复杂攻击」的过时假设。Hacktron正在把研究扩展到前沿实验室和其他互联网关键系统。  
## 六、影响版本与修复  
  
「HEIF Heist」不绑死某个版本，针对的是多个发行系列（1.19.x、1.20.x、1.22.x、1.23.x）里的整个漏洞生态。任何没打最新上游安全补丁的部署都可能中招。  
- **更新上游**  
：通过发行版安全频道或上游发布，安装最新打了安全补丁的libheif和libde265包。截至2026年9月14日，最新上游libheif安全版本是v1.23.4，v1.23.2已被后续修复取代。  
  
- **纵深防御**  
：生产架构应在不需要时禁用不可信的HEIF/AVIF解码，或将图像处理管线隔离在加固的临时沙箱中。ImageMagick的安全策略支持限制接受的格式和资源占用。  
  
## 七、研究仓库与延伸阅读  
- **HEIF Heist 专题站**  
：https://heif-heist.com  
  
- **Hacktron 博客原文**  
：https://www.hacktron.ai/blog/hacking-openai  
  
- **Discourse 安全公告**  
：https://github.com/discourse/discourse/security/advisories/GHSA-vhm9-85gw-x335  
  
- **ImageMagick 沙箱化提交**  
：https://github.com/discourse/discourse/commit/a07188016987de1613c961277e2e928aaa7c37ec  
  
- **xkcd 2347**  
：https://xkcd.com/2347/  
  
**致谢**  
：Hacktron团队感谢Sudanshu Rajhbhar的技术协助，以及Zayne Zhang、Fabian Faessler、Robert Chen、Jessica Ruan的校对与反馈。  
  
**原文出处**  
：本文编译自Hacktron博客《Hacking OpenAI》（2026年9月13日发布），原文链接：https://www.hacktron.ai/blog/hacking-openai  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/nGzNudUIJ6MmGSjzr4icpQ5P3PGtxBdbBMzowL7CZQFzVZybvZdybsPyOO5Kay9s1vPHyXw3yOiapF4Crl2PKYtnNYGULibnzMXibp8YBEDboxw/640?from=appmsg "")  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650621549&idx=1&sn=21c4b072726d2387d562109ada6b9bbb&scene=21#wechat_redirect)  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650621944&idx=1&sn=3cc6dc9876a20466d4ec3634deb80220&scene=21#wechat_redirect)  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650622065&idx=1&sn=09f8ae84c06d4331e71c277b177ae701&scene=21#wechat_redirect)  
> 👇 点击**阅读原文**  
，访问我的网站  
  
  

#  微信出现严重漏洞，可在无交互的情况下导致微信账户被黑客控制，影响安卓和 IOS 设备！  
原创 mmc
                    mmc  AGI安全   2026-09-09 09:19  
  
# 微信出现严重漏洞，可在无交互的情况下导致微信账户被黑客控制，影响安卓和 IOS 设备！  
  
2026 年 9 月 8 日，安全公司 Calif 公开了代号 WeWorm  
 的研究：微信 VoIP 栈存在一个内存破坏漏洞，攻击者只要给目标拨一通微信通话，在还在响铃的那几秒里就能拿到对方微信账号的完全控制权  
。目标不需要接听，不需要点任何东西。拿到账号后，程序会继续给这个人的联系人拨号，在 iOS 和 Android 之间来回跳。Calif 7 月 24 日报告腾讯，腾讯 8 月 21 日发布客户端补丁，8 月 28 日在服务端对全量用户阻断。这是一次概念验证演示，没有证据表明被真实利用。  
  
被控账号即是下一个攻击者：呼叫 → 响铃阶段触发 → 接管账号 → 用通讯录继续呼叫① 攻击方 AndroidPixel 10a发起一通微信通话前提：已是对方好友无需对方任何操作响铃中② 触发点：VoIP 栈memory corruption通话管理代码内存破坏提示与接听之间的空档数秒完成③ 被控 iPhoneiPhone 17e收发消息、拨号、冒充本人拿到的是账号不等于拿到手机④ 继续传播拨打第二台 Android跨 iOS / Android 跳转好友关系即信任链链式扩散Calif 三台真机演示链路 · 每一跳都不需要被叫方做任何操作技术细节尚未公开，图为公开信息还原的攻击流程示意  
  
## 背景  
  
安全公司 Calif 的研究人员通过POC测试：给一台手机拨微信通话，在对方还在响铃、什么都没做的时候，接管了对方的微信账号。然后用这台被控的手机，再打给下一台，同样接管。三台真机，跨 iOS 和 Android，链条完整跑通。  
  
这个演示的代号叫 WeWorm  
。Calif 把它称为首个通过微信通话在 iOS 与 Android 之间传播的零点击蠕虫。  
  
**重点：这是概念验证POC，不是真实攻击。**  
没有任何证据表明有用户因此被入侵。腾讯已经修复，并称未发现补丁发布前存在在野利用。  
  
该漏洞落在一个几乎没人设防的位置（响铃阶段）；账号被接管在微信这个生态里的后果远超普通聊天软件；以及，Calif 说这个漏洞是 AI 找到的，从发现到写出第一个可用的远程代码执行，只花了大约两天。  
  
Calif 是这家安全公司的创始人 Thai Duong，曾在 Google 安全团队工作。  
  
## 基础信息  
<table><tbody><tr><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);background: rgb(249, 250, 251);color: rgb(107, 114, 128);vertical-align: top;"><section><span leaf="">研究代号</span></section></td><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);color: rgb(31, 41, 55);"><section><span leaf="">WeWorm</span></section></td></tr><tr><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);background: rgb(249, 250, 251);color: rgb(107, 114, 128);vertical-align: top;"><section><span leaf="">披露方</span></section></td><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);color: rgb(31, 41, 55);"><section><span leaf="">Calif（攻击性安全公司，创始人 Thai Duong）</span></section></td></tr><tr><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);background: rgb(249, 250, 251);color: rgb(107, 114, 128);vertical-align: top;"><section><span leaf="">漏洞位置</span></section></td><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);color: rgb(31, 41, 55);"><section><span leaf="">微信 VoIP 栈（负责通话管理的代码）中的内存破坏缺陷</span></section></td></tr><tr><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);background: rgb(249, 250, 251);color: rgb(107, 114, 128);vertical-align: top;"><section><span leaf="">触发方式</span></section></td><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);color: rgb(31, 41, 55);"><section><span leaf="">向目标发起一通微信通话，在响铃阶段完成利用，耗时数秒</span></section></td></tr><tr><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);background: rgb(249, 250, 251);color: rgb(107, 114, 128);vertical-align: top;"><section><span leaf="">交互要求</span></section></td><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);color: rgb(185, 28, 28);font-weight: 700;"><section><span leaf="">零点击，目标无需接听或触碰手机</span></section></td></tr><tr><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);background: rgb(249, 250, 251);color: rgb(107, 114, 128);vertical-align: top;"><section><span leaf="">前置条件</span></section></td><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);color: rgb(31, 41, 55);"><section><span leaf="">攻击者必须已在目标的微信好友列表中</span></section></td></tr><tr><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);background: rgb(249, 250, 251);color: rgb(107, 114, 128);vertical-align: top;"><section><span leaf="">获得权限</span></section></td><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);color: rgb(31, 41, 55);"><section><span leaf="">微信账号完全控制权（收发消息、拨打通话、以本人身份行事）</span><span leaf=""><br/></span><span style="color: rgb(107, 114, 128);"><span leaf="">不含手机本身的控制权；Calif 称与其他已报告的 Android／iOS 漏洞串联可做到设备完全接管</span></span></section></td></tr><tr><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);background: rgb(249, 250, 251);color: rgb(107, 114, 128);vertical-align: top;"><section><span leaf="">测试设备</span></section></td><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);color: rgb(31, 41, 55);"><section><span leaf="">Pixel 10a（攻击方）→ iPhone 17e → 第二台 Pixel 10a；iOS 26.6 及若干旧版 Android</span></section></td></tr><tr><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);background: rgb(249, 250, 251);color: rgb(107, 114, 128);vertical-align: top;"><section><span leaf="">测试版本</span></section></td><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);color: rgb(31, 41, 55);"><section><span leaf="">微信 Android 8.0.76、iOS 8.0.75（均比修复版低一个版本号）</span></section></td></tr><tr><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);background: rgb(249, 250, 251);color: rgb(107, 114, 128);vertical-align: top;"><section><span leaf="">修复版本</span></section></td><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);color: rgb(4, 120, 87);font-weight: 700;"><section><span leaf="">Android 8.0.77 / iOS 8.0.76（2026 年 8 月 21 日发布）</span></section></td></tr><tr><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);background: rgb(249, 250, 251);color: rgb(107, 114, 128);vertical-align: top;"><section><span leaf="">服务端阻断</span></section></td><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);color: rgb(4, 120, 87);font-weight: 700;"><section><span leaf="">2026 年 8 月 28 日，覆盖全量用户</span></section></td></tr><tr><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);background: rgb(249, 250, 251);color: rgb(107, 114, 128);vertical-align: top;"><section><span leaf="">CVE 编号</span></section></td><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);color: rgb(31, 41, 55);"><section><span leaf="">截至 9 月 8 日核查：无</span></section></td></tr><tr><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);background: rgb(249, 250, 251);color: rgb(107, 114, 128);vertical-align: top;"><section><span leaf="">影响规模参照</span></section></td><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);color: rgb(31, 41, 55);"><section><span leaf="">微信与 WeChat 合并月活 14.39 亿（腾讯 2026 Q2 财报，截至 6 月 30 日）</span></section></td></tr></tbody></table>  
  
## 漏洞关键  
  
关键在于「响铃」这个状态本身就是一段代码在运行。  
  
当有人给你打微信通话，你的手机要先把这通呼叫解析出来，才知道该弹谁的头像、该响哪个铃声。这一步发生在你看到来电提示之后、你按下接听或挂断之前。**处理这段数据的就是 VoIP 栈，漏洞就在这里。**  
  
Calif 说这是一个内存破坏（memory corruption）缺陷，利用它可以在远端设备上执行任意命令。完整技术细节还没有公开，他们打算放在之后的安全会议上讲。  
### 「那我不接不就行了」——不行  
  
这是最反直觉的一点，需要拆成两种情况说：  
- **接了：**  
照样中招。Calif 明确说，接起来的人什么声音都听不到，利用一样成功。因为攻击在你接听之前就已经完成了。  
  
- **拒接：**  
能挡住**这一次**  
。但攻击者可以稍后再打——比如在你睡着的时候。手机静音、勿扰模式，反而让你更不容易察觉。  
  
换句话说，用户侧**不存在有效的行为层面防御**  
。你没有任何操作可以稳定地避开它。这也是「零点击」这个词真正的分量所在——它把责任完全留给了厂商。  
### 唯一的门槛：他得先是你的好友  
  
主叫方必须已经在目标的微信联系人列表里。听起来是个不小的限制，但 Calif 认为这算不上障碍，理由很直接：**只要有一个联系人被拿下，微信赋予「好友」的那份额外信任，就整个倒向了攻击者。**  
  
这正是它被做成蠕虫的原因——被控账号自己就是下一轮的攻击者，它的通讯录就是下一轮的目标名单。攻击者呼叫受害者，受害者变成攻击者，再呼叫下一个受害者。链条自己会走。  
  
## 时间线  
<table><tbody><tr><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);background: rgb(249, 250, 251);color: rgb(107, 114, 128);vertical-align: top;"><section><span leaf="">7 月</span></section></td><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);color: rgb(31, 41, 55);"><section><span leaf="">Calif 的 AI 发现该漏洞</span></section></td></tr><tr><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);background: rgb(249, 250, 251);color: rgb(107, 114, 128);vertical-align: top;"><section><span leaf="">7 月 23 日</span></section></td><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);color: rgb(31, 41, 55);"><section><span leaf="">工程团队接手</span></section></td></tr><tr><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);background: rgb(249, 250, 251);color: rgb(107, 114, 128);vertical-align: top;"><section><span leaf="">7 月 24 日</span></section></td><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);color: rgb(31, 41, 55);"><section><span leaf="">向腾讯报告</span></section></td></tr><tr><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);background: rgb(249, 250, 251);color: rgb(107, 114, 128);vertical-align: top;"><section><span leaf="">7 月 25–28 日</span></section></td><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);color: rgb(31, 41, 55);"><section><span leaf="">Calif 的微信账号被封；7 月 29 日恢复</span></section></td></tr><tr><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);background: rgb(249, 250, 251);color: rgb(107, 114, 128);vertical-align: top;"><section><span leaf="">7 月 30 日</span></section></td><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);color: rgb(31, 41, 55);"><section><span leaf="">Android 远程代码执行完成</span></section></td></tr><tr><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);background: rgb(249, 250, 251);color: rgb(107, 114, 128);vertical-align: top;"><section><span leaf="">8 月 2 日</span></section></td><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);color: rgb(31, 41, 55);"><section><span leaf="">iOS 远程代码执行完成</span></section></td></tr><tr><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);background: rgb(249, 250, 251);color: rgb(107, 114, 128);vertical-align: top;"><section><span leaf="">8 月 11 日</span></section></td><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);color: rgb(185, 28, 28);font-weight: 700;"><section><span leaf="">跨平台蠕虫演示跑通</span></section></td></tr><tr><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);background: rgb(249, 250, 251);color: rgb(107, 114, 128);vertical-align: top;"><section><span leaf="">8 月 21 日</span></section></td><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);color: rgb(4, 120, 87);font-weight: 700;"><section><span leaf="">腾讯发布 Android 8.0.77 / iOS 8.0.76</span></section></td></tr><tr><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);background: rgb(249, 250, 251);color: rgb(107, 114, 128);vertical-align: top;"><section><span leaf="">8 月 26 日</span></section></td><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);color: rgb(31, 41, 55);"><section><span leaf="">腾讯表示正在评估</span></section></td></tr><tr><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);background: rgb(249, 250, 251);color: rgb(107, 114, 128);vertical-align: top;"><section><span leaf="">8 月 28 日</span></section></td><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);color: rgb(4, 120, 87);font-weight: 700;"><section><span leaf="">确认服务端阻断已对全量用户生效</span></section></td></tr><tr><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);background: rgb(249, 250, 251);color: rgb(107, 114, 128);vertical-align: top;"><section><span leaf="">9 月 3 日</span></section></td><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);color: rgb(31, 41, 55);"><section><span leaf="">Calif 向腾讯提供利用代码与完整分析</span></section></td></tr><tr><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);background: rgb(249, 250, 251);color: rgb(107, 114, 128);vertical-align: top;"><section><span leaf="">9 月 4 日</span></section></td><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);color: rgb(31, 41, 55);"><section><span leaf="">腾讯确认远程命令执行确实可行</span></section></td></tr><tr><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);background: rgb(249, 250, 251);color: rgb(107, 114, 128);vertical-align: top;"><section><span leaf="">9 月 8 日</span></section></td><td style="padding: 9px 10px;border: 1px solid rgb(229, 231, 235);color: rgb(31, 41, 55);"><section><span leaf="">公开披露，《纽约时报》同日报道</span></section></td></tr></tbody></table>  
从报告到客户端补丁 28 天，到服务端全量阻断 35 天，到公开披露 46 天。对一个影响十亿级用户、且用户侧无法自保的漏洞来说，这个节奏算是正常偏快。  
  
## 微信「账号被接管」有点严重！  
  
**这个漏洞拿到的是微信账号，不是手机。**  
攻击者读不到你的相册、通讯录 App、银行短信。Calif 补充说，如果和其他已报告的 Android／iOS 漏洞串联，在实验环境里能做到设备完全接管。  
  
话虽如此，在微信这个生态里，账号本身的分量和普通聊天软件不是一回事。微信里装着支付、公众号、小程序，很多人的工作沟通、家庭群、生活缴费、身份验证全在里面。一个能**以你的身份收发消息、拨打通话**  
的攻击者，能做的事远不止看聊天记录：  
- 对你的家人朋友发起借钱、代付这类熟人诈骗，且发出的确实是你本人的账号  
  
- 读取工作群里的内部信息，或以你的名义在群里发布内容  
  
- 拿到以微信为验证渠道的第三方服务入口  
  
- 最关键的：继续往你的通讯录里传播，而收到来电的人同样无法防御  
  
有专家表示，如果这个蠕虫被真正放出来，**数小时内可能波及数亿台设备**  
。这个数字的依据是微信 14.39 亿的月活规模，以及蠕虫沿好友关系指数扩散的特性。  
  
##  AI的作用   
  
这可能才是 WeWorm 最值得记住的部分。  
  
Calif 说，他们写了一套算法来指导 AI 在各类消息应用中识别潜在攻击面，漏洞就是 AI 用这套方法找到的。**从发现漏洞到写出第一个能在手机上执行代码的利用，大约两天；再把它做成能自我传播的蠕虫，又花了一周。**  
  
这种规模的活儿，过去需要一个更大的团队干上几个月。人类在其中负责的是「打哪里」和「怎么安全地测试」这类判断，其余的基础工作由 AI 承担。所用模型是开源模型与美国头部模型的组合，没有指名具体是哪些。  
  
时间线里各阶段之间的间隔其实并不短（7 月 23 日发现，7 月 30 日 Android 利用完成，8 月 11 日蠕虫演示）。  
### Calif 自己的结论不是「限制 AI」  
  
他们担心的是**能力平民化**  
——AI 正在把这种级别的攻击能力交到技术水平不高的人手里。但他们明确反对把「限制 AI」当作结论，理由是：漏洞本来就在那儿，AI 只是让它更早被找到；而同样的工具也让防守方受益更多。他们呼吁的是中美两国与产业界在 AI 安全上的合作，并拿 WannaCry 当反面例子——工具过早流出的后果。  
  
## 普通用户需要做什么  
  
**不用紧张，目前普通用户需要把微信升到最新版。**  
腾讯的阻断跑在服务端，8 月 28 日起对全量用户生效，理论上不升级也已经被保护。升级只是让客户端这一层也补齐。  
### 1. 确认版本号  
  
微信 → 我 → 设置 → 关于微信，查看版本号。不低于下面这两个即可：  
Android   8.0.77   (2026-08-21 发布)iOS       8.0.76   (2026-08-21 发布)### 2. 别指望自查，因为查不了  
  
Calif 没有公开任何技术细节，也**没有放出任何可供防守方使用的 IoC（失陷指标）**  
。这意味着普通用户和企业都没有办法判断自己是否曾被攻击。这不是疏忽，是他们在会议正式披露前的克制。  
### 3. 用户可以做的  
- 把「微信升级到 8.0.77／8.0.76 以上」纳入这一轮的终端合规检查  
  
- 重申一条老规矩：**涉及转账、报销、验证码的请求，一律换一个渠道二次确认**  
。这条对任何形式的账号接管都有效，不只是这次  
  
- 如果内部有基于微信的审批或通知流程，评估一下「消息来源可信」这个假设还成不成立  
  
- HarmonyOS、Windows、Mac、Linux 客户端是否受影响，双方都没说。如果你的组织在这些端上有关键流程，目前只能保持关注  
  
  
  
## 几个没有答案的问题  
  
把话说完整，这件事里有几处至今没有公开答案：  
- **受影响版本清单没有公布。**  
Calif 只说测试了 Android 8.0.76 和 iOS 8.0.75，腾讯什么都没说。用其他版本的人无从判断自己当时是否暴露。  
  
- **其他平台客户端的情况不明。**  
HarmonyOS、Windows、Mac、Linux 各有自己的发布节奏，Calif 拒绝透露是否测试过，腾讯未回应。  
  
- **没有 CVE 编号，也没有官方安全公告。**  
9 月 8 日核查时，腾讯安全响应站点上最新的公告日期还停在 2022 年 4 月。iOS 更新说明里只写了常规的问题修复。  
  
- **腾讯没有就此发布任何公开声明**  
只在回应媒体询问时确认了漏洞与修复。  
  
这些空白本身也是信息。一个影响十亿级用户、用户完全无法自保的漏洞，走完全流程之后没有 CVE、没有公告、没有受影响版本清单、普通用户好像什么也做不了。  
  
## 结论  
  
**一、这次不用慌，但下次未必。**  
漏洞已修，无在野利用，服务端全量阻断。真正的问题是：同类攻击面在各家消息应用里普遍存在。  
  
**二、零点击意味着安全意识教育在这里失效。**  
「不点陌生链接、不加陌生人」这套话术对这次攻击毫无作用——来电者是你的好友，你什么都不用点。责任只能落在厂商的代码质量和响应速度上。  
  
**三、好友关系是一条被低估的攻击路径。**  
产品设计里给「联系人」的那些额外信任（可以直接拨通、可以免验证发文件），在账号可被接管的前提下会整个反转成攻击者的便利。  
  
**四、AI 压缩的是时间，不是难度上限。**  
两天找到漏洞并写出利用，一周做成蠕虫。防守方需要接受的新现实是：从漏洞存在到有人能用它，窗口正在快速收窄。  
  
**五、披露流程该补的还是要补。**  
没有 CVE、没有公告、没有受影响版本清单，对企业安全团队来说等于无法做资产梳理和风险定级。修得快是好事，说清楚是另一回事。  
  
## 最后后续我们会继续分享移动端零点击攻击面、AI 辅助漏洞挖掘与企业应急响应的实践经验，欢迎点赞、收藏、分享、转发！  
  
  
AGI 安全 · 企业 AI 安全专项培训  
  
针对零点击攻击面、移动端账号接管、AI 辅助漏洞挖掘与应急响应  
  
我们提供完整的企业 AI 安全培训体系，助力企业建立完整的安全防护能力  
<table><tbody><tr><td data-colwidth="50%" width="50%" style="background: rgba(255, 255, 255, 0.14);border-radius: 10px;padding: 10px 8px;text-align: center;border: 1px solid rgba(153, 246, 228, 0.45);"><p style="margin: 0px;font-size: 13px;font-weight: 700;color: rgb(153, 246, 228);line-height: 1.5;"><span leaf="">大模型安全</span></p></td><td data-colwidth="50%" width="50%" style="background: rgba(255, 255, 255, 0.14);border-radius: 10px;padding: 10px 8px;text-align: center;border: 1px solid rgba(153, 246, 228, 0.45);"><p style="margin: 0px;font-size: 13px;font-weight: 700;color: rgb(153, 246, 228);line-height: 1.5;"><span leaf="">智能体安全</span></p></td></tr><tr><td data-colwidth="50%" width="50%" style="background: rgba(255, 255, 255, 0.1);border-radius: 10px;padding: 10px 8px;text-align: center;"><p style="margin: 0px;font-size: 13px;font-weight: 700;color: rgb(255, 255, 255);line-height: 1.5;"><span leaf="">AI 合规审计</span></p></td><td data-colwidth="50%" width="50%" style="background: rgba(255, 255, 255, 0.1);border-radius: 10px;padding: 10px 8px;text-align: center;"><p style="margin: 0px;font-size: 13px;font-weight: 700;color: rgb(255, 255, 255);line-height: 1.5;"><span leaf="">组件供应链安全</span></p></td></tr><tr><td data-colwidth="50%" width="50%" style="background: rgba(255, 255, 255, 0.14);border-radius: 10px;padding: 10px 8px;text-align: center;border: 1px solid rgba(153, 246, 228, 0.45);"><p style="margin: 0px;font-size: 13px;font-weight: 700;color: rgb(153, 246, 228);line-height: 1.5;"><span leaf="">数据安全</span></p></td><td data-colwidth="50%" width="50%" style="background: rgba(255, 255, 255, 0.1);border-radius: 10px;padding: 10px 8px;text-align: center;"><p style="margin: 0px;font-size: 13px;font-weight: 700;color: rgb(255, 255, 255);line-height: 1.5;"><span leaf="">AI 编程工具安全</span></p></td></tr><tr><td colspan="2" style="background: rgba(255, 255, 255, 0.1);border-radius: 10px;padding: 10px 8px;text-align: center;"><p style="margin: 0px;font-size: 13px;font-weight: 700;color: rgb(255, 255, 255);line-height: 1.5;"><span leaf="">企业安全体系搭建</span></p></td></tr></tbody></table>  
如需定制企业内部 AI 安全培训、移动端与账号安全风险评估，欢迎扫码咨询  
  
![](https://mmecoa.qpic.cn/mmecoa_png/3o01zBiam5M3dCmU6ibXpPpQjEPvl0HnWhYGv0pMRhgamSQajLpKrPCB11mR54ru892zMbIxMSrib7SJraWvoF1I9vicIWv20HoDRKj1zag6TQ0/640?wx_fmt=png&from=appmsg "")  
  
  
联系人：马老师 ｜ 微信：AICodingC ｜ 公众号：AGI安全  
  
## 参考资料  
  
https://calif.io/research/weworm  
  
  
Help Net Security：Zero-click WeChat worm could hijack accounts and spread via a single call  
  
https://securityaffairs.com/198688/hacking/wechat-worm-can-hijack-accounts-without-victims-answering-calls.html  
  
  
https://securityboulevard.com/2026/09/using-ai-calif-creates-demo-wechat-exploit-that-spreads-through-phone-calls/  
  

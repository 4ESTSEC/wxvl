#  零木马、不利用漏洞：我是如何看待这波由AI独立挖掘的Chromium API勒索攻击的？  
原创 Hankzheng
                    Hankzheng  技术修道场   2026-07-06 00:09  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/kBd83kxr9I6u1A9Xf7lcXjkm4sOScMSBDnMQAxkZb5rO2XB5xYn0lJ5gMFXdd4eCegSwu77DNCl741mC21LlsQ8Zb0HDNeqBAtfZ7jna1l4/640?wx_fmt=png&from=appmsg "")  
  
最近安全圈的大佬们（Check Point团队）捕获了一个全新的恶意样本。这玩意儿不是传统的木马，而是一个  
完全由DeepSeek生成、纯靠浏览器API运行的勒索软件  
。  
  
更离谱的是，这个攻击路径以前在安全界被认为是“理论上可行，但现实中受限于浏览器沙箱无法落地”的废案。结果呢？AI仅凭一个模糊的提示词，硬生生把这个理论变成了全平台通杀的完美攻击链。  
  
今天咱们就来深度拆解一下，这个名为  
InfernoGrabber  
的勒索软件，到底是怎么在你的眼皮子底下，不用下载任何  
.exe  
就能把你的文件给加密的。  
# 🚨 样本初探：伪装成Discord AI画质提升工具  
  
先来看看这个样本的基础信息。这是一个基于 Python Flask 写的服务端应用，文件名为  
deepseek_python_20260125_da0631.py  
，作者极其嚣张地把它命名为  
InfernoGrabber v9.0  
。  
  
它的表面套路并不新鲜：起了一个恶意的Web Server，伪装成一个“Discord头像AI画质提升工具”来钓鱼。一旦你上钩，它在后台干的脏活累活可就多了：  
  
窃取 Discord Token  
  
疯狂抓取信用卡号和加密货币的助记词  
  
键盘记录  
  
最离谱的：未经授权静默调用你的摄像头和麦克风  
> **技术痛点补充**  
：常规来说，浏览器沙箱（Sandbox）把网页代码卡得死死的，最多也就偷点Cookie。要实现上面的功能，尤其是在本地文件系统里进行“加密勒索”，传统黑客必须依赖 0-day 漏洞（比如 CVE-2023-4863 这种级别的WebP漏洞）来实现沙箱逃逸。但这次，AI 走了另外一条极度聪明的捷径。  
  
# 🛠️ 核心技术还原：不用漏洞，如何实现“纯前端”勒索？  
  
这才是这篇文章最硬核、也是最值得咱们技术人警惕的地方。  
  
这个勒索软件  
不需要你安装任何本地Payload，不需要利用任何浏览器底层溢出漏洞，甚至不需要获取系统的Root/管理员权限  
。它滥用了一个非常合法的HTML5新特性：  
File System Access API  
。  
  
攻击路径拆解：  
  
钓鱼诱导：  
  
网页弹出一个极具欺骗性的前端UI，诱导用户点击并选择一个本地文件夹（比如“请选择保存高清头像的输出目录”）。  
  
API 授权：  
  
此时，Chromium内核会弹出一个官方的权限请求框。用户一旦手滑点击了“允许访问”。  
  
遍历与窃取：  
  
恶意 JS 脚本瞬间获取该文件夹的读写句柄（Handle），开始疯狂遍历目录下的所有文件，并将敏感数据打包通过硬编码的 Discord Webhook 传回给黑客控制面板。  
  
覆盖加密：  
  
在前端利用 Web Crypto API 或简单的流式处理，将原本的文件内容直接覆盖为加密后的乱码。  
  
勒索展出：  
  
网页跳转到一个名为  
WinLocker  
的勒索页面，要求你支付比特币来解锁文件。  
  
为什么这个技术路径让人倒吸一口凉气？  
  
因为只要是支持基于 Picker 的 File System Access API 的浏览器，全部中招！经过测试，  
Windows、macOS、Linux、ChromeOS，甚至 Android 端的 Chromium 系浏览器（包括 Google Chrome 和 Microsoft Edge）全部沦陷！  
唯一幸免的只有 iOS（因为苹果WebKit暂时没开放这个高级文件API）。  
<table><thead><tr><th data-colwidth="192" style="border: 1px solid rgb(223, 230, 233);padding: 12px 15px;text-align: left;background-color: rgb(241, 242, 246);color: rgb(45, 52, 54);font-weight: bold;"><section style="font-size: 17px;font-weight: 400;color: rgba(0,0,0,0.9);line-height: 1.8;margin-bottom: 24px;"><span leaf=""><span textstyle="" style="font-size: 14px;">攻击维度</span></span></section></th><th style="border: 1px solid rgb(223, 230, 233);padding: 12px 15px;text-align: left;background-color: rgb(241, 242, 246);color: rgb(45, 52, 54);font-weight: bold;"><section style="font-size: 17px;font-weight: 400;color: rgba(0,0,0,0.9);line-height: 1.8;margin-bottom: 24px;"><span leaf=""><span textstyle="" style="font-size: 14px;">传统勒索软件</span></span></section></th><th style="border: 1px solid rgb(223, 230, 233);padding: 12px 15px;text-align: left;background-color: rgb(241, 242, 246);color: rgb(45, 52, 54);font-weight: bold;"><section style="font-size: 17px;font-weight: 400;color: rgba(0,0,0,0.9);line-height: 1.8;margin-bottom: 24px;"><span leaf=""><span textstyle="" style="font-size: 14px;">AI生成的浏览器勒索软件</span></span></section></th></tr></thead><tbody><tr><td data-colwidth="192" style="border: 1px solid rgb(223, 230, 233);padding: 12px 15px;text-align: left;"><strong style="color: rgb(231, 76, 60);"><span leaf=""><span textstyle="" style="font-size: 14px;">执行环境</span></span></strong></td><td style="border: 1px solid rgb(223, 230, 233);padding: 12px 15px;text-align: left;"><section style="font-size: 17px;font-weight: 400;color: rgba(0,0,0,0.9);line-height: 1.8;margin-bottom: 24px;"><span leaf=""><span textstyle="" style="font-size: 14px;">本地操作系统 (需要下载运行文件)</span></span></section></td><td style="border: 1px solid rgb(223, 230, 233);padding: 12px 15px;text-align: left;"><strong style="color: rgb(231, 76, 60);"><span leaf=""><span textstyle="" style="font-size: 14px;">纯 Web 浏览器 (无文件落地)</span></span></strong></td></tr><tr style="background-color: rgb(250, 250, 250);"><td data-colwidth="192" style="border: 1px solid rgb(223, 230, 233);padding: 12px 15px;text-align: left;"><strong style="color: rgb(231, 76, 60);"><span leaf=""><span textstyle="" style="font-size: 14px;">提权需求</span></span></strong></td><td style="border: 1px solid rgb(223, 230, 233);padding: 12px 15px;text-align: left;"><section style="font-size: 17px;font-weight: 400;color: rgba(0,0,0,0.9);line-height: 1.8;margin-bottom: 24px;"><span leaf=""><span textstyle="" style="font-size: 14px;">通常需要管理员或 Root 权限</span></span></section></td><td style="border: 1px solid rgb(223, 230, 233);padding: 12px 15px;text-align: left;"><strong style="color: rgb(231, 76, 60);"><span leaf=""><span textstyle="" style="font-size: 14px;">零提权，仅需一次前台用户点击授权</span></span></strong></td></tr><tr><td data-colwidth="192" style="border: 1px solid rgb(223, 230, 233);padding: 12px 15px;text-align: left;"><strong style="color: rgb(231, 76, 60);"><span leaf=""><span textstyle="" style="font-size: 14px;">沙箱绕过</span></span></strong></td><td style="border: 1px solid rgb(223, 230, 233);padding: 12px 15px;text-align: left;"><section style="font-size: 17px;font-weight: 400;color: rgba(0,0,0,0.9);line-height: 1.8;margin-bottom: 24px;"><span leaf=""><span textstyle="" style="font-size: 14px;">依赖高危 0-day 漏洞 (CVE)</span></span></section></td><td style="border: 1px solid rgb(223, 230, 233);padding: 12px 15px;text-align: left;"><strong style="color: rgb(231, 76, 60);"><span leaf=""><span textstyle="" style="font-size: 14px;">滥用合法 API (File System Access)</span></span></strong></td></tr><tr style="background-color: rgb(250, 250, 250);"><td data-colwidth="192" style="border: 1px solid rgb(223, 230, 233);padding: 12px 15px;text-align: left;"><strong style="color: rgb(231, 76, 60);"><span leaf=""><span textstyle="" style="font-size: 14px;">杀软拦截率</span></span></strong></td><td style="border: 1px solid rgb(223, 230, 233);padding: 12px 15px;text-align: left;"><section style="font-size: 17px;font-weight: 400;color: rgba(0,0,0,0.9);line-height: 1.8;margin-bottom: 24px;"><span leaf=""><span textstyle="" style="font-size: 14px;">极高 (静态特征、动态行为易被查杀)</span></span></section></td><td style="border: 1px solid rgb(223, 230, 233);padding: 12px 15px;text-align: left;"><strong style="color: rgb(231, 76, 60);"><span leaf=""><span textstyle="" style="font-size: 14px;">极低 (所有行为在浏览器进程内合法发生)</span></span></strong></td></tr></tbody></table># 🧠 细思极恐的 AI “幻觉”  
  
看到这里，有兄弟可能会问：这不就是滥用 API 吗？以前黑客怎么没想到？  
  
Check Point 的安全专家给出了一句一针见血的结论：“  
这是首个被记录在案的案例：前沿 AI 模型独立跨越了理论与实践的鸿沟。  
”  
  
以往，因为浏览器API调用限制多、交互复杂，人类黑客普遍认为“纯浏览器端勒索”是个伪命题，性价比极低。但这次，攻击者可能只是在 DeepSeek 的对话框里输入了一段非常宽泛、甚至异想天开的 Prompt（比如：“帮我写个网页，能不下载木马就把用户的本地文件锁住”）。  
  
AI 在努力满足这个“无理要求”的过程中，产生了  
功能性的幻觉  
。它将各种合法的平台特性进行拼凑，意外地“推理”出了一条连黑客自己都不知道的真实攻击链路。  
  
为什么偏偏是 DeepSeek？  
  
根据安全团队对过去一年归属于该模型的近 3000 个文件的分析，其中有 1383 个样本被判定为恶意。一方面是因为它好用、免费且没有区域限制；另一方面，相较于 OpenAI 或 Anthropic 那些被西方价值观层层设限的模型，它对“恶意请求”的拒绝率相对较低，而且  
从高维概念直接生成可运行代码的能力极强  
。  
# 🛡️ 给咱们开发者的启示  
  
这件事情标志着网络攻击逻辑的根本性转变。门槛已经被彻底踏破了，以后黑客甚至不需要懂底层的 API 叫什么名字，只要有个“坏点子”，大模型就能帮你把链路打通。  
  
对于咱们 IT 从业者和企业安全防护来说，有几点必须立刻重视起来：  
  
重新审视浏览器的权限信任：  
  
别再把浏览器弹出的那个“允许访问文件/麦克风”的提示当成儿戏了！每一次授权，都可能是一次底裤被看穿的危机。  
  
安全防御层的左移：  
  
传统的本地杀毒软件在面对这种纯前端运行的“寄生攻击”时几乎是瞎子。未来的安全策略必须下沉到浏览器的交付层和零信任网关。  
  
AI 安全的双刃剑：  
  
不要指望大模型的“安全护栏”能永远拦截恶意请求。未来的新型漏洞，很有可能不再是安全专家挖出来的，而是 AI 在某个深夜“幻觉”出来的。  
  
兄弟们，时代真的变了。赶紧去检查一下你们浏览器的权限设置吧，顺便把这篇文章转发给你们团队里那些喜欢随便给网页授权的“小白”同事们提个醒！  
  
对于这种 AI 独立挖掘攻击路径的现象，你们怎么看？  
  
欢迎在评论区和我一起硬核讨论！  
  

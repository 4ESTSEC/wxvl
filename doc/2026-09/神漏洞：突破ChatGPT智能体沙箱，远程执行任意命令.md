#  神漏洞：突破ChatGPT智能体沙箱，远程执行任意命令  
e安在线
                    e安在线  e安在线   2026-09-22 01:43  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/Hxdb7gjfn9nqibllaDsBsm15D6rTfD4RarhVUWkLdHHwh4cE5hVPZXuk5W0wGDzM41QDPM30bNf2CN0jTzqvGmSS7kOb9ibxgib8icodNJOlic6w/640?wx_fmt=jpeg&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Hxdb7gjfn9lZdtPv7CwqvYJILH5J9guwKrJT24on6B1JxudAGCiaofibYkPibXxtB5hHl6WGUzSUmPB3bPzkKAwicqLHqo0IrX5FfWuSOocy8ib4/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Hxdb7gjfn9kLuX42nP2OIBN2lQySZa0tOpkjknIj6LYOfz5O3Z2q55K1yxcicPAnlGtqPbCHfwZnqiaIAicr5N97qw4tJl37yBicict8JPna9Xiaw/640?wx_fmt=png&from=appmsg "")  
  
  
**研究员发现两个高危漏洞，可以以不同方法突破Codex（ChatGPT）的沙箱保护，进而破坏用户电脑环境，目前Codex新版本已修复漏洞；**  
  
  
**一个是冒充可信组件逃出沙箱的Heapjack攻击，当用户让Codex在最安全的只读模式下检查某个外部项目，但项目的恶意代码可以沙箱逃逸并远程执行任意命令；**  
  
  
**另一个是利用特殊指令修改沙箱外文件的Overpatch漏洞，当用户让Codex修改项目文件，实际上它可以通过特殊指令让其修改沙箱外的电脑文件。**  
  
  
  
安全研究人员发现了两种突破OpenAI Codex（已改名为ChatGPT）沙箱的方法。其中一种甚至能在Codex权限最严格的模式下，在开发者电脑上执行命令，而且整个过程无需用户批准，屏幕上也不会显示任何提示。  
  
  
Accomplish AI研究人员Oren Yomtov表示，两项漏洞均于8月12日报告给OpenAI，并在8天内得到修复。  
  
  
其中更严重的一项被研究人员称为“Heapjack”。它可以将一个看似普通的操作变成远程代码执行。用户在Codex中打开他人的代码仓库，并询问一个与代码有关的问题，代码仓库的编写者就可能获得一种能力：在用户电脑上执行不受沙箱限制的命令。  
  
  
Codex是OpenAI的编程智能体，可通过命令行工具和桌面应用使用。与其他同类智能体一样，Codex会在沙箱中执行模型发起的操作，以防不受信任的代码接触系统其他部分。上述两种攻击的共同点是，都从沙箱内部突破安全边界。  
  
  
**Heapjack攻击冒充可信组件逃逸沙箱**  
  
  
根据Yomtov发布的技术分析，Heapjack针对的是一个名为`node_repl`的组件。Codex 桌面版安装时会将该组件写入全局配置文件`~/.codex/config.toml`。  
  
  
用户无需主动启用，也没有关闭该组件的设置。由于相关配置写在共享配置文件中，普通Codex CLI用户也会继承这一工具，而且整个过程不会有任何提示。  
  
  
`node_repl`运行一个Node.js进程，其中包含两个相互独立的JavaScript执行上下文。一个是受信任上下文，运行OpenAI自己的代码；另一个是不受信任上下文，运行智能体代码。受信任上下文通过出示每次运行随机生成的令牌，来证明自身身份。  
  
  
问题在于，这两个上下文运行在同一个Node.js进程中，共享同一块内存堆。因此，这个所谓的“秘密”令牌实际上只是内存中的一个字符串，不受信任的一侧可以读取它。  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/Hxdb7gjfn9kl3uZonCmsJIl9nQw4W7WAHksibCsSmqrNDAy1kxg7AqR9FLJbsEYeG2XslgV14m2tI8SJNofLibcGxn8WnYF0FoawExSUiaib9Eg/640?wx_fmt=jpeg&from=appmsg "")  
  
图：Heapjack攻击路径  
  
  
不受信任的代码通过`v8.getHeapSnapshot()`获取内存堆快照，然后尝试找出所有符合UUID格式的字符串。如果猜错，系统返回“not authorized”（未获授权）；如果令牌正确但参数错误，系统则会返回真正的参数验证错误。攻击者由此可以确认自己找到了正确令牌。  
  
  
获得令牌后，不受信任的代码便可以通过同一通信管道发送自己的请求。该管道原本用于让受信任上下文与一个位于沙箱之外、未受沙箱限制的原生父进程通信。  
  
  
父进程验证令牌后发现其有效，便会执行请求。概念验证代码使用系统的`open`命令，在Codex进程树之外直接启动应用程序。  
  
  
攻击者借此还可以访问Unix套接字，其中一个明显的攻击目标就是Docker守护进程套接字。此外，攻击者还可以使用一个修改全局配置文件的工具。  
  
  
而这一切都可以在只读模式下完成。这是Codex最严格的沙箱模式，按照设计，智能体在该模式下根本不应写入任何内容。  
  
  
**Overpatch漏洞利用特殊指令修改沙箱外文件**  
  
  
第二个漏洞被称为Overpatch，存在于开源Codex CLI中。  
  
  
在workspace-write模式下，智能体原则上只能写入项目目录。如果尝试通过shell命令写入用户主目录，系统会拒绝操作。  
  
  
但研究人员发现，可以利用Codex自带的`apply_patch`工具绕过这一限制。  
  
  
该工具会根据补丁中指定的路径，给该路径的父目录授予写入权限。如果指定`/tmp`，工具就会获得磁盘根目录的写入权限。  
  
  
研究人员设计的攻击补丁包含两处修改。一处指定`/tmp`，本身并不执行任何有用操作，其作用只是扩大写入权限。  
  
  
另一处则通过指向用户主目录的符号链接，在`.zshrc`文件末尾追加一行代码。  
  
  
如果删除第一处修改，写入操作就会被拒绝。但如果保留这处修改，开发者下一次打开终端时，就会在未受沙箱限制的环境中执行攻击者写入的代码。  
  
  
**两个漏洞的共同问题**  
  
  
这两个漏洞具有相同的根本问题：负责实施安全限制的机制，本身却处在它应该限制的对象内部。  
  
  
`apply_patch`会根据攻击者提供的输入自行判断应该拥有哪些权限；`node_repl`则把用于区分受信任代码和不受信任代码的秘密令牌，与不受信任代码放在了同一块内存中。  
  
  
换句话说，沙箱从内部被诱导放行了原本应该拦截的操作。  
  
  
这种漏洞并非首次出现。2026年7月，Pillar Security的研究人员曾展示过类似攻击方式，涉及Cursor、Codex、Gemini CLI和Google Antigravity。在这些案例中，智能体虽然一直运行在沙箱内，却可以写入一个文件，随后由沙箱外的受信任工具执行该文件。  
  
  
Yomtov在X上发布相关技术文章后，一名评论者指出：“V8上下文可以隔离全局变量，却不能隔离内存，所以这个沙箱实际上只是在承诺不会触碰那块内存，而堆本身并没有作出这种承诺。”另一名评论者则将这种信任边界比作“一道隔断墙”。  
  
  
由于相关功能默认启用，也有人质疑，为什么不受信任的JavaScript代码一开始就能拿到一个高权限令牌。  
  
  
**该如何应对？**  
  
  
Accomplish表示，OpenAI已在Codex Desktop 26.818.21641版本中修复Heapjack，并在Codex CLI 0.149.0版本中修复Overpatch。  
  
  
用户应升级至上述版本或更高版本。Yomtov表示，OpenAI在收到报告后的8天内解决了这两项问题，并对此表示认可。  
  
  
  
  
  
  
  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Hxdb7gjfn9kmiaC463F9kJyZ55yLibexich1CN3KboSaqbo0XFiaYkYFsJkNU7uLLBmBdxQs2eia4CqG6ibDXV0hLwpraznfOvFPJGeuo9xsfSQ7U/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Hxdb7gjfn9mjfNiapico8GskoPzDQ1HHxaP2ChvFZ1UKp6yqicmicSIYYl0Bc2JXciaxmBTV4SIuEjUvboqZXxU39q1uT5lVMdwFEMFvCIqaXUOs/640?wx_fmt=png&from=appmsg "")  
  
  
声明：除发布的文章无法追溯到作者并获得授权外，我们均会注明作者和文章来源。如涉及版权问题请及时联系我们，我们会在第一时间删改，谢谢！文章来源：安全内参  
  
  
  
  
  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Hxdb7gjfn9nASibV7ZzOYxCUI83E59EXxmCn8rWeL0w5mL4ld5BQkUhSCCmMP69sy6FPia1ktfPJxXujicDPlsZGRCsMzkmoKwXVcaofEMVDC0/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Hxdb7gjfn9lhbJbfnqrqhFODUXPdScqkJXiboR0OJEXTgeFhGQG6cYf3eZvK7KIF1Q4n6SKV83AfTtUoTV9CoI6gaZmdzaqrIGYPjVuW38uA/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Hxdb7gjfn9lftc0EL7MZZJAlbYR7UBuEJOy3vEr4pgxiamjjiazicEV8K6QuibskHwmpuTSJCib3OzWwkiac9xVDKnsiceZvmgBouSShWGMhAZ9rzE/640?wx_fmt=png&from=appmsg "")  
  
  
  

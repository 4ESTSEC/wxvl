#  DDE 回调劫持：一个藏在 explorer.exe 里的进程注入 primitive  
 Ots安全   2026-09-29 05:20  
  
**威胁简报**  
  
  
**恶意软件**  
  
  
**漏洞攻击**  
  
## 一、开篇：一个被遗忘的 IPC 机制，成了注入的跳板  
  
当你以为进程注入离不开 CreateRemoteThread  
、WriteProcessMemory  
 这种“标准动作”时，Windows 上一个年头不小的进程间通信（IPC）机制——**动态数据交换（DDE）**  
——却悄悄留了一扇后门。  
  
explorer.exe  
 在后台默默托管着 DDE 服务。这套服务为每个 DDE 会话在用户态堆上分配一个结构体，并把结构体地址挂在窗口的“额外字节”里。更关键的是，这个结构体里有一个**可写的函数指针 pfnCallback**  
。一旦它被改写，再随便触发一次 DDE 事务，目标进程就会“主动”调用攻击者的代码。  
  
这不是新发现。它的公开出处可以追溯到 2019 年 odzhan（modexp）发布的 **“Breaking BaDDEr”**  
 研究，原理与近期社区讨论的 WDML_INSTANCE  
 中 PFNCALLBACK  
 可写性完全一致。本文带你把它拆透。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/zNsFJyIuL0GhVSnn8Pwb9E4jHSB8KcSmpKf3LxhUeXXj1Smn9ibhEgjkQdjzt0UbvKiaM2gg3aC9G9aUe0yEVrrQajuiaUrL1V7oDT8konRfuQ/640?wx_fmt=png&from=appmsg "")  
## 二、背景：DDE 与 DDEML 到底是什么  
  
**DDE（Dynamic Data Exchange，动态数据交换）**  
 是 Windows 早期用于应用程序之间共享数据的协议；**DDEML（DDE Management Library）**  
 则是封装这套协议的库，让开发者用更友好的 API 来收发数据。  
  
DDE 真正“出圈”是在 2017 年 10 月：微软 Office 被曝可通过 DDE 公式执行命令（安全公告 4053440）。此后 Office 默认禁用了 DDE，它不再被视为关键风险面。但 **DDEML 本身仍由系统组件（如 shell32.dll）在 explorer.exe 中提供 DDE 服务**  
，这成了少数仍存活的注入面。  
  
在 Windows 10 上，真正提供 DDE 服务的 DLL 屈指可数：shell32.dll  
、ieframe.dll  
、twain_32.dll  
。其中 shell32.dll  
 会创建三个 DDE 服务，宿主就是 explorer.exe  
。换句话说，**这套注入技术的天然目标，基本就是 explorer.exe**  
。  
## 三、核心原理：那个“可写的函数指针”  
  
DDEML 在初始化时（user32!DdeInitializeW  
）会在堆上分配一个未公开文档化的结构体——各研究里叫 CL_INSTANCE_INFO  
（modexp）或 WDML_INSTANCE  
（社区新近命名），两者指向同一类结构。我们关心的字段只有一个：**pfnCallback**  
。  
  
这个结构体的地址，被存放在 DDE 服务“母窗口”的**窗口额外字节（Extra Window Memory, EWM）**里。在 user32  
 中，该母窗口的类名是 **DDEMLMom**  
。只要拿到这个窗口句柄，就能用 GetWindowLongPtr(hwnd, GWLP_INSTANCE_INFO)  
 跨进程读回结构体地址——而窗口额外字节本来就是设计为跨进程可读的。  
  
于是整个脆弱性链条就清晰了：  
> DDEMLMom 隐藏窗口 → EWM 存结构地址（跨进程可读）→ 结构内含可写的 pfnCallback  
 → 覆盖它并触发 DDE 事务 → 代码在 explorer.exe  
 中执行。  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/zNsFJyIuL0EacTKHcVQ6adficLHhNcqbGuUw8asMAaU1lWibogcrmsv8MOs4mRLcAQRYmCE0hpKpEsNictPBxiaSrAka5LJib6UJhgKaKt0PNOSs/640?wx_fmt=png&from=appmsg "")  
## 四、注入五步详解（逻辑骨架）  
  
下面给出**已公开研究（Breaking BaDDEr）中描述的技术流程骨架**  
。此处仅以 API 序列呈现原理，不构成可运行的武器化实现；完整源码已在作者公开仓库中，本文不做搬运与拼接。  
  
**① 定位窗口**  
  
通过窗口类名 DDEMLMom  
 找到 explorer.exe  
 托管的 DDE 母窗口。  
  
**② 读取结构地址**  
  
用 GetWindowLongPtr(hwnd, GWLP_INSTANCE_INFO)  
 取回 CL_INSTANCE_INFO  
 的用户态地址；再用 GetWindowThreadProcessId  
 取得宿主进程 PID。  
  
**③ 在目标进程分配并写入 payload**  
  
以 OpenProcess  
 打开 explorer.exe  
，VirtualAllocEx  
 在其地址空间分配一块 PAGE_EXECUTE_READWRITE  
（RWX）内存，并用 WriteProcessMemory  
 写入 shellcode。  
  
**④ 劫持回调指针**  
  
再次用 WriteProcessMemory  
，仅覆盖结构体中 pfnCallback  
 字段，使其指向刚写入的 payload 地址。  
  
**⑤ 触发执行并清理**  
  
调用 DdeInitialize  
 / DdeConnectList  
 触发一次 DDE 事务——DDEML 会像往常一样回调 pfnCallback  
，于是 payload 在 explorer.exe  
 上下文里运行。执行完后，把原始 pfnCallback  
 写回、释放内存、关闭句柄，痕迹被抹平。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/zNsFJyIuL0GOJhFiczfdTDU5Jb3uRUiceUqaC99ibqgWsnxonhHyu2joRB4JY3wLHk5541PaM5WET8Nd46iciaXVnnxkDb57KIGvohTdV5r8nvw0/640?wx_fmt=png&from=appmsg "")  
  
注意第 ⑤ 步的“隐身”本质：代码是被 explorer.exe**自己调用的**  
，全程没有 CreateRemoteThread  
、QueueUserAPC  
 这类特征鲜明的注入 API，因此单纯监控“建远程线程”的防御规则会直接漏掉它。  
## 五、范围与限制：它并非无所不能  
  
必须客观地说，这项技术的实战天花板并不高：  
- **目标受限**  
：DDE 服务主要由 explorer.exe  
 托管，注入面基本锁定在它身上，无法随意挑选高权限进程（除非该进程本身提供 DDE 服务）。  
  
- **需要一定权限**  
：攻击者进程必须能 OpenProcess  
 到 explorer.exe  
 并写其内存——在严格的权限隔离与代码完整性策略下会受到制约。  
  
- **残留可见性**  
：RWX 内存分配、explorer.exe  
 执行非预期可执行内存等行为，仍会被现代 EDR 的行为监控捕获。  
  
- **历史包袱**  
：自 2017 年 Office DDE 事件后，DDE 在很多场景已被默认禁用，现实攻击面进一步收窄。  
  
所以它的研究价值不在于“多么犀利”，而在于提醒我们：**遗留的 IPC 机制里，往往藏着被忽视的可写 primitive**  
——防御侧如果只盯着“标准”注入 API，就会留出盲区。  
## 六、检测与防御  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/zNsFJyIuL0EzYPvfQticU9q29ukJulcsH2HrpWaYl9hNt37SYQTsic8GHugAaeBKC7iaPnozf9ePe4StWrP4Kb5rrAEoZ96utdJJSAG5l9uibJ0/640?wx_fmt=png&from=appmsg "")  
  
**蓝队可监控的异常行为：**  
- 跨进程写入 DDEMLMom  
 窗口额外字节；  
  
- explorer.exe  
 中出现异常的 RWX 内存分配；  
  
- 可疑的 DdeConnectList  
 / DdeInitialize  
 调用（尤其是无对应业务背景时）；  
  
- explorer.exe  
 执行非预期的可执行内存页；  
  
- CL_INSTANCE_INFO  
 内容被异常改写。  
  
**缓解措施：**  
- **最小权限原则（PoLP）**  
：收紧进程间写权限，降低低权限代码改写高信任进程结构的能力；  
  
- **代码完整性策略（WDAC / AppLocker）**  
：限制未签名/未授权代码的加载与执行；  
  
- **漏洞利用防护**  
：启用 CFG（控制流防护）、DEP，提高劫持控制流的难度；  
  
- **EDR / XDR 行为监控**  
：对跨进程写窗口字节、异常 RWX 分配等做行为级告警与阻断；  
  
- **及时打补丁**  
：Office DDE 已默认禁用，保持系统更新以收缩现实攻击面。  
  
## 七、真实性核实  
- **技术真实性**  
：核心机制（DDEMLMom  
 窗口、GWLP_INSTANCE_INFO  
 取结构地址、CL_INSTANCE_INFO/WDML_INSTANCE  
 中可写的 pfnCallback  
、用 DDE 事务触发回调）与 odzhan（modexp）2019 年公开研究 **“Windows Process Injection: Breaking BaDDEr”**  
（modexp.wordpress.com，2019-08-09）完全一致；近期安全社区（LinkedIn 等）关于 WDML_INSTANCE.PFNCALLBACK  
 可写性的讨论亦印证同一 primitive。  
  
- **源头核实**  
：本文编译自 Medium 用户 jaytiwari05  
 的同名技术博客《DDE Callback Hijacking — A Process Injection Technique》。Medium 页面为 JS 渲染，正文未能直接抓取，故以公开权威来源（Breaking BaDDEr）交叉核实技术细节，未对原文做无依据的增补。  
  
- **限制说明**  
：该注入面的现实影响受“目标基本限于 explorer.exe、需相应进程权限、EDR 可观测”等因素制约，不宜夸大其威胁等级。  
  
## 参考地址  
1. **Medium 原文**  
：https://medium.com/@jaytiwari05/dde-callback-hijacking-a-process-injection-technique-768ad16b1379  
  
1. **modexp《Windows Process Injection: Breaking BaDDEr》(2019-08-09)**  
：https://modexp.wordpress.com/2019/08/09/windows-process-injection-breaking-badder/  
  
1. **odzhan 公开 PoC 源码（GitHub）**  
：https://github.com/odzhan/injection/tree/master/dde  
  
1. **MITRE ATT&CK T1055 Process Injection**  
：https://attack.mitre.org/techniques/T1055/  
  
1. **MITRE ATT&CK T1559.002 Inter-Process Communication: DDE**  
：https://attack.mitre.org/techniques/T1559/002/  
  
1. **微软安全公告 4053440（Office DDE 命令执行，防御规避更新）**  
：https://msrc.microsoft.com/update-guide/  
  
  
  
**END**  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/zNsFJyIuL0Ezx6FsZmkla1qxUgBFsVeB6kQPhowWGTBibyfiazlkwlDBuTkoNl0uvvS0g2TOW4ibCT8uRSGrsbicrcbHvSP56rkqwiaicf8OBVQCI/640?wx_fmt=jpeg&from=appmsg "")  
  
  
公众号内容都来自国外等平台- 搜索的内容通过结合编写 -   
  
公众号 |   
AnQuan7 (Ots安全)  
  

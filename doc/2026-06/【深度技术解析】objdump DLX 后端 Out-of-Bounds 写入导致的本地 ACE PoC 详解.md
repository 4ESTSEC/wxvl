#  【深度技术解析】objdump DLX 后端 Out-of-Bounds 写入导致的本地 ACE PoC 详解  
 Ots安全   2026-06-29 01:10  
  
**威胁简报**  
  
  
**恶意软件**  
  
  
**漏洞攻击**  
  
今天我们来详细拆解 GitHub 上一个非常有意思的 PoC 项目：bikini/exploitarium 中的 objdump-dlx-calc-poc。  
  
这个 PoC 利用的是 GNU Binutils 中 objdump -g 处理 DLX ELF 对象文件时的堆/调试节越界写入漏洞，最终在特定环境下实现从崩溃到执行任意命令（这里是打开计算器）的效果。  
  
重要声明：这是一个本地解析器漏洞（ACE - Arbitrary Code Execution via crafted local file），不是网络远程漏洞。  
  
需要受害者主动运行 objdump -g（或类似带调试信息的选项）处理恶意文件。  
  
PoC 保留了 ASLR，采用相对偏移 + 多 payload 尝试策略，并非单发全 bypass。   
  
一、背景知识  
  
objdump 和 DLX 架构objdump 是 GNU Binutils 套件中的核心工具，用于显示对象文件（ELF、PE 等）的各种信息。-g 选项会尝试解析调试信息（DWARF 等）。  
  
DLX 是一种教学用的 32 位 RISC 架构（类似 MIPS 的简化版），在 Binutils 中有对应的 elf32-dlx 后端支持。虽然现实中很少用到，但正是这个冷门后端存在实现上的不完善，导致了本次漏洞。  
  
漏洞核心在于 DLX 后端处理重定位（Relocation） 时，对调试节（.debug_*）的边界检查不足，允许越界写入。   
  
二、漏洞原理简述  
1. （高层次）构造一个特制的 DLX ELF 对象文件（表面上看似合法的 ELF）。  
  
1. 当 objdump -g 解析它时，DLX 后端在处理重定位条目时，会向超出预期调试节边界的内存区域写入数据。  
  
1. PoC 精心设计这些写入操作，覆盖关键的 libc FILE 结构（如 _IO_2_1_stderr_ 相关的 vtable 或函数指针字段）。  
  
1. 通过精心选择的 32 位 big-endian 重定位加法，将 _IO_2_1_stderr_ 指针的低 32 位“转换”为 system 函数的低 32 位。  
  
1. 后续触发 I/O 操作或清理逻辑时，控制流跳转到 system("P")，而 P 是当前目录下的 helper 脚本，从而执行命令。   
  
为什么需要多个 payload？  
  
因为 ASLR（地址空间布局随机化）开启后，libc 和堆的基址是随机的。PoC 使用相对 delta（稳定偏移）+ 多组猜测的 system_delta，覆盖常见 libc 低 32 位布局。命中率取决于目标环境的 heap/libio profile。   
  
三、项目文件结构  
  
详解进入仓库目录 objdump-dlx-calc-poc：  
- payloads/：预先生成的多个 .bin 文件（实际是 ELF 格式，只是扩展名用 bin）。每个对应不同 delta 组合，还有 .notes 说明。  
  
- P：核心 helper 脚本（可执行）。它会写入 calc_hit.log 日志，并在 WSL 下调用 Windows 计算器（calc.exe）。  
  
- run_dlx_calc_poc.sh：自动化尝试脚本，循环尝试所有 payload，直到命中。  
  
- generate_objdump_dlx_calc_poc.py：payload 生成器，使用 dlx_chain_builder.py 构建重定位链。  
  
- docs/aslr-bypass-analysis.md：详细的 ASLR 绕过分析，解释为什么无法做到通用单文件 bypass。  
  
- tools/：Z3 求解器验证脚本、delta 覆盖率计算等。   
  
四、环境准备与复现步骤  
  
推荐环境：WSL（Ubuntu 24.04） + 已编译的 Binutils（支持 dlx-elf）。克隆仓库：  
  
```
git clone https://github.com/bikini/exploitarium.gitcd exploitarium/objdump-dlx-calc-pocchmod +x Pexport PATH="$PWD:$PATH"   # 关键！让系统能找到 ./P# 指定你的 objdump 路径，例如系统自带或自定义编译的MAX_TRIES=50 bash run_dlx_calc_poc.sh /usr/bin/objdump# 或者自定义构建# MAX_TRIES=50 bash run_dlx_calc_poc.sh /opt/binutils-master/binutils/objdumpcat calc_hit.log
```  
  
  
如果看到 CALC_HELPER_RAN ... 并且计算器弹出，即为成功。   
  
手动单步尝试（便于理解）：  
  
```
rm -f calc_hit.logfor p in payloads/*.bin; do  echo "Trying $p"  /path/to/objdump -g "$p" >/dev/null 2>&1 || true  if [ -s calc_hit.log ]; then    echo "HIT! $p"    cat calc_hit.log    break  fidone
```  
  
  
常见现象：即使出现 “Segmentation fault”，也不代表失败——只要 helper 没触发，就继续尝试。成功信号是日志文件更新或计算器弹出。  
  
五、重新生成 Payload  
  
如果想自定义或更新：  
  
```
rm -rf payloadspython3 generate_objdump_dlx_calc_poc.py --out-dir payloads
```  
  
  
脚本会根据不同 profile（wsl2404、gnu2461 等）生成对应 payload。   
  
六、ASLR Bypass 技术细节（进阶）  
- 核心转换：利用 DLX 支持的 32-bit big-endian relocation add，对 FILE 结构中 _IO_2_1_stderr_ 指针低 32 位进行算术操作，使其指向 system。  
  
- 覆盖点：涉及 heap buffer、section 对象偏移（如 0xbb0 / 0xbb8）、rbase 等。  
  
- 为什么不是通用 bypass？  
  
1. libc 基址低 32 位随机，需要覆盖多种 delta。  
  
1. 重定位加法的 carry 传播方向（big-endian）限制了依赖关系，无法对所有 page-aligned base 都成立。  
  
1. Z3 验证脚本确认了固定转换序列的局限性。   
  
项目作者也提到，4D4J 的另一个 repo 提供了更完整的 ASLR bypass PoC，值得参考。  
  
七、安全意义与修复建议  
  
影响：  
任  
何  
使用 objdump 解析不可信 ELF 文件的场景（CI/CD、逆向工具、自动化脚本等）都可能受影响。  
  
缓解：  
- 避免用 objdump 处理不受信任的文件。  
  
- 更新 Binutils 到最新版本（项目测试了 2.46.1，仍受影响，建议关注官方修复）。  
  
- 在沙箱或容器中运行解析工具。  
  
- 编译时加强 hardening（ASLR、stack protector 等已默认开启，但本 PoC 已适配）。  
  
这个 PoC 非常适合学习 ELF 格式、重定位机制、Binutils 内部结构以及现实中的 ASLR 部分绕过技术。  
  
参考链接：  
  
原 PoC：https://github.com/bikini/exploitarium/tree/main/objdump-dlx-calc-poc  
  
相关发现：https://github.com/4D4J/objdump-Out-Of-Bounds-write  
  
如果你在复现过程中遇到问题，或者想深入讨论 DLX 重定位的具体字节构造，欢迎留言！我们下期再见~  
  
**END**  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/zNsFJyIuL0FXqSyPb7qZOuQ5fxUlic0p6tZVZJPG2vKXav2UPicnd2xOupiby9TcKZ46dmFxSHg0icaWskKA1yuSGfZ8HC7ndnu8ZtUKDUWOGmU/640?wx_fmt=jpeg&from=appmsg "")  
  
  
公众号内容都来自国外平台-所有文章可通过点击阅读原文到达原文地址或参考地址  
  
排版 编辑 | Ots 小安   
  
采集 翻译 | Ots Ai牛马  
  
公众号 |   
AnQuan7 (Ots安全)  
  

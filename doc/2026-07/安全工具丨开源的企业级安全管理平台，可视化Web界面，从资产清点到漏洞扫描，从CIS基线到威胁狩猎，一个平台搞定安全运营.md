#  安全工具丨开源的企业级安全管理平台，可视化Web界面，从资产清点到漏洞扫描，从CIS基线到威胁狩猎，一个平台搞定安全运营  
原创 蓝星安全
                    蓝星安全  蓝星安全   2026-07-12 22:00  
  
         
**点击上方****蓝星安全****关注我**  
  
****  
  
**免责声明：本公众号分享的任何资料仅限用于安全学习，严禁用于其他用途，请严格遵守中华人民共和国法律法规，对因不遵守国家法律法规而产生的任何后果，均由个人自行承担，本公众号不承担任何责任！**  
  
****  
**获取资料，请扫码下方二维码加入知识星球**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/opKHaHXLxcmxg12rU33pwcICNRkiaof5YSUAGfWPApU7M1BfdsTTOyvREw0gKD42g4U8eefG4n0XuYETEtbxSIN2drACnNVz3UeicCcldM5AA/640?wx_fmt=png&from=appmsg "")  
## 一、简介  
  
MxCwpp是一款开源的企业级主机与容器安全管理平台。它并非将多种安全工具简单堆砌，而是从架构层面深度整合了**安全基线、资产管理、漏洞扫描、病毒查杀、EDR检测与合规审计**  
等核心能力，为安全运营团队提供统一的可视化管控视图  
。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/opKHaHXLxckicvicxlSaZjvDGgGv1LpWoeicOI4GlzNiauLU84VTcEQswv3GGzeibKicpSTtbwaENA6rQOQr12MFNqHJhZBf20ibf7ZiaIe7dRJelak/640?wx_fmt=png&from=appmsg "")  
<table><thead><tr style="box-sizing: border-box;background-color: rgb(255, 255, 255);border-top: 0.666667px solid rgba(209, 217, 224, 0.7);"></tr></thead></table>  
  
二、核心能力：一站式覆盖主机与容器全生命周期安全  
### 2.1. 资产管理与漏洞风险排查  
- **资产中心（11类资产采集）**  
：自动采集主机上的**进程、端口、用户、软件包、容器**  
等11类资产信息，并自动计算资产间的关系，形成统一的资产指纹图谱  
。这是所有安全工作的基础。  
  
- **漏洞管理（OSV.dev + CVSS v3.1）**  
：通过采集软件包的PURL（包统一资源定位符），对接**OSV.dev**  
漏洞数据库，并进行**CVSS v3.1**  
评分，帮助团队按优先级修复漏洞。同时支持**SBOM（软件物料清单）导出**  
，满足供应链安全合规要求  
。  
  
- **安全基线（CIS Benchmark）**  
：内置**9种检查器**  
和**212条CIS Benchmark（互联网安全中心基准）核心规则**  
，覆盖主机和容器的关键配置项。更关键的是，它支持**单机或批量自动修复**  
，大大减轻了运维人员的手动加固负担  
。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/opKHaHXLxcnHMKrYKPeibe5u4ib8WrhaQYTsn6ZYliacABU84A0qeBHWgSxbXqLyiaEibHAk4PN8PMsyGKicPk9OP3ssd17elYJaNm3hIUvNiasdvA/640?wx_fmt=png&from=appmsg "")  
### 2.2. 高级威胁检测与响应（EDR级能力）  
- **eBPF内核级运行时检测**  
：MxCwpp的EDR引擎基于**Tetragon**  
和**eBPF**  
技术，能够在内核态实时捕获进程、文件、网络和内存事件。这使得它能有效检测**进程镂空、memfd_exec（无文件执行）、shellcode注入、LSASS（本地安全认证子系统服务）内存转储**  
等高级攻击  
。  
  
- **内存取证与Rootkit检测**  
：专门针对高级持续性威胁（APT），它能检测**DKOM（直接内核对象操作）类Rootkit**  
，包括隐藏的PID、内核模块、网络端口以及LD_PRELOAD劫持等  
。  
  
- **AD/LDAP域控安全审计**  
：针对企业内网核心设施，它内置**7条检测规则**  
，覆盖**DCSync（目录服务复制攻击）、Kerberoasting（Kerberos票据攻击）、暴力破解、非工作时间RDP（远程桌面协议）登录、特权账户异常分配**  
等高危场景，有效捕捉横向移动的早期信号  
。  
  
- **蜜罐传感器与文件诱饵**  
：部署**SSH和HTTP蜜罐**  
，结合**文件诱饵策略**  
并配合合法备份工具白名单，精准识别勒索软件等恶意程序的早期侦察行为，实现主动防御  
。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/opKHaHXLxcl08rDjWiaC5vOpN1AicQ9BgrjR3ibxh7DRALHQNERfkoibpOsQzKEk4oHyfJSZ6Iq6j4slCcWer0v2xS2t40GbOvfsYH9HBcmwUZA/640?wx_fmt=png&from=appmsg "")  
### 2.3. 安全运营与响应闭环  
- **攻击故事线（ATT&CK杀链时间线）**  
：引擎会自动将分散的告警，基于**MITRE ATT&CK框架**  
关联成完整的攻击时间线（Kill Chain）。从初始访问到执行、持久化、提权、防御规避，安全分析师可以一目了然地看清攻击者的完整行动路径  
。  
  
- **威胁狩猎（SPL风格DSL）**  
：平台提供了**SPL（搜索处理语言）风格**  
的领域特定语言（DSL），可自动转译为ClickHouse SQL。分析师无需学习新语法，即可利用其Splunk经验，在PB级事件归档上进行交互式威胁狩猎  
。  
  
- **告警聚合与自动响应**  
：支持告警聚合、白名单和自动化响应。检测到威胁后，系统可自动执行**kill进程、网络隔离、文件隔离**  
等操作，形成从发现到处置的闭环  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/opKHaHXLxclYr5ggFxPSb9BibH7Bz9yz7wiblORjCXhiaiaG2014C0Ps6aavAgiaNM6Ow5tZRPPVnrU8GJSOhDDJ3rOc4rvpEll4HMMPBwRzSbQA/640?wx_fmt=png&from=appmsg "")  
  
三、立即获取  
  
https://github.com/matrixplusio/mxcwpp  
  

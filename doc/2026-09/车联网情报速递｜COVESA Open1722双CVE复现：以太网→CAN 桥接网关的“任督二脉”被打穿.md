#  车联网情报速递｜COVESA Open1722双CVE复现：以太网→CAN 桥接网关的“任督二脉”被打穿  
太初众测
                    太初众测  太初众测   2026-09-21 00:49  
  
COVESA Open 1722双CVE 复现：以太网→CAN 桥接网关的“任督二脉”被打穿  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ounUznLmib9icsVO1QXmfK5T4JRgZ8wNSLMf6gp3RtUNwBIdxex9owROYVlP62amz8c4MjSVIkqcmpIlDicbV3dy8V2fW83LXhODz6dg9iaqulU/640?wx_fmt=png&from=appmsg "")  
  
**一个 uint8_t，如何把车载网关的栈内存广播到CAN 总线**  
  
**CVE-2026-73522 / CVE-2026-73523 漏洞复现：**  
  
本文基于披露者 Fatullayev Asadbek 在 COVESA/Open1722 issue [#154]()  
 的官方披露、VulnCheck 公告、v0.9.2 源码逐行走读与实机复现整理。复现环境为 ARM64 虚拟机（Ubuntu 24.04 / 内核 6.8.0-88-generic，VMware Fusion，真实 SocketCAN vcan0），两个漏洞均完整复现：栈溢出写触发 AddressSanitizer 报告与披露者逐行一致，栈内存泄露在真实 CAN 总线上抓到含 stream ID 明文、argv、环境变量的帧。  
  
  
**01 漏洞介绍**  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ounUznLmib99dwbZWRWQH7VWNLFlpkdhbiaHYwgOyB3mAczDX5M5GQj5AkfkSpkfUQbrO4yf6O2fPk83mFDiamkC7f23xBGCiaotgiamiaoWuUKZY/640?wx_fmt=png&from=appmsg "")  
  
  
CVE-2026-73522 与 CVE-2026-73523 是 COVESA Open1722（IEEE 1722 AVTP 开源参考实现）examples/acf-can 示例程序 acf-can-listener 中的**一对共生漏洞**  
，由 Fatullayev Asadbek 于 2026 年 8 月 17 日通过 GitHub issue [#154]()  
 公开披露。  
  
**• CVE-2026-73522**  
（栈缓冲区溢出写，CWE-121，披露者自评 CVSS 3.1 7.5）：解析函数没有“容量”概念，第 16 条消息写穿 15 槽栈数组；  
  
**• CVE-2026-73523**  
（整数截断 → 栈内存泄露到 CAN 总线，CWE-197/CWE-200，CNA/NVD 定 CVSS 4.0 8.7）：错误码 -1 被 uint8_t 截断为 255，listener 把 15 槽数组连同旁边约 18 KB 栈内存当作 255 个 CAN 帧发到总线上。  
  
针对**车联网**  
场景：Open1722 是车载以太网（AVTP/1722）与 CAN 总线之间协议桥接的参考实现，采用它的**车载以太网****↔CAN 网关、域控制器原型、基于 Zephyr 的车载 MCU**  
中，这两个漏洞可能成为“网络可达 → 网关进程内存泄露 / 潜在代码执行”的关键环节。  
  
**一句话概括：任何人往网关的 UDP 端口发一个报文，要么把数据写进它的栈里（73522），要么让它把自己栈里的秘密一帧帧喊到 CAN 总线上（73523）。**  
  
  
<table><tbody><tr style="box-sizing: border-box;"><td data-colwidth="25.0000%" width="25.0000%" style="border-width: 1px;border-color: rgb(62, 62, 62);border-style: solid;vertical-align: middle;box-sizing: border-box;padding: 0px;"><section style="line-height: 1.5;letter-spacing: 0px;box-sizing: border-box;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">项目</span></p></section></td><td data-colwidth="75.0000%" width="75.0000%" style="border-width: 1px;border-color: rgb(62, 62, 62);border-style: solid;vertical-align: middle;box-sizing: border-box;padding: 0px;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">内容</span></p></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="25.0000%" width="25.0000%" style="border-width: 1px;border-color: rgb(62, 62, 62);border-style: solid;vertical-align: middle;box-sizing: border-box;padding: 0px;"><section style="line-height: 1.5;letter-spacing: 0px;box-sizing: border-box;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">CVE 编号</span></p></section></td><td data-colwidth="75.0000%" width="75.0000%" style="border-width: 1px;border-color: rgb(62, 62, 62);border-style: solid;vertical-align: middle;box-sizing: border-box;padding: 0px;"><section style="text-align: left;letter-spacing: 0px;box-sizing: border-box;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">CVE-2026-73522 / CVE-2026-73523</span></p></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="25.0000%" width="25.0000%" style="border-width: 1px;border-color: rgb(62, 62, 62);border-style: solid;vertical-align: middle;box-sizing: border-box;padding: 0px;"><section style="line-height: 1.5;letter-spacing: 0px;box-sizing: border-box;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">漏洞类型</span></p></section></td><td data-colwidth="75.0000%" width="75.0000%" style="border-width: 1px;border-color: rgb(62, 62, 62);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="text-align: left;box-sizing: border-box;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">栈溢出写（CWE-121）/ 整数截断→信息泄露（CWE-197、CWE-200）</span></p></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="25.0000%" width="25.0000%" style="border-width: 1px;border-color: rgb(62, 62, 62);border-style: solid;vertical-align: middle;box-sizing: border-box;padding: 0px;"><section style="line-height: 1.5;letter-spacing: 0px;box-sizing: border-box;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">影响组件</span></p></section></td><td data-colwidth="75.0000%" width="75.0000%" style="border-width: 1px;border-color: rgb(62, 62, 62);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="text-align: left;letter-spacing: 0px;box-sizing: border-box;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">COVESA Open1722 ≤ 0.9.2，examples/acf-can（acf-can-listener；Zephyr 变体 acf-can-bridge）</span></p></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="25.0000%" width="25.0000%" style="border-width: 1px;border-color: rgb(62, 62, 62);border-style: solid;vertical-align: middle;box-sizing: border-box;padding: 0px;"><section style="line-height: 1.5;letter-spacing: 0px;box-sizing: border-box;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">披露时间</span></p></section></td><td data-colwidth="75.0000%" width="75.0000%" style="border-width: 1px;border-color: rgb(62, 62, 62);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="text-align: justify;box-sizing: border-box;"><p style="white-space: normal;margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">2026-08-17（GitHub issue <a class="wx_topic_link" topic-id="mu9uy3db-ju0e60" style="color: #576B95 !important;" data-topic="1" data-recommend="">#154</a> + VulnCheck 公告）</span></p></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="25.0000%" width="25.0000%" style="border-width: 1px;border-color: rgb(62, 62, 62);border-style: solid;vertical-align: middle;box-sizing: border-box;padding: 0px;"><section style="line-height: 1.5;letter-spacing: 0px;box-sizing: border-box;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">修复</span></p></section></td><td data-colwidth="75.0000%" width="75.0000%" style="border-width: 1px;border-color: rgb(62, 62, 62);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="text-align: left;box-sizing: border-box;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">截至 2026-09-14 上游 main 未合入修复（v0.9.2..main 仅 2 个无关提交）；issue给出修复建议</span></p></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="25.0000%" width="25.0000%" style="border-width: 1px;border-color: rgb(62, 62, 62);border-style: solid;vertical-align: middle;box-sizing: border-box;padding: 0px;"><section style="line-height: 1.5;letter-spacing: 0px;box-sizing: border-box;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">危险特性</span></p></section></td><td data-colwidth="75.0000%" width="75.0000%" style="border-width: 1px;border-color: rgb(62, 62, 62);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="text-align: left;box-sizing: border-box;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">网络可达、无需认证、默认 stream ID 硬编码明文、泄露不崩溃（静默）、73522 为数据可控栈写</span></p></section></td></tr></tbody></table>  
  
**它最反常的一点：**  
  
**触发73523不需要任何“利用技巧”——不需要绕过、不需要竞态、不需要内存布局赌博，只要让解析函数返回一个 -1；一个本来表示“失败”的返回值，被 uint8_t 读成了 255 次“成功”。**  
 而 73522 的触发报文里 16 条消息**每一条都完全合法**  
，能通过全部协议校验——挡住它们的从来不是校验，而是一道根本不存在的“容量闸门”。  
  
**02 影响范围**  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ounUznLmib9iba0d88acKS7kc2Mq22RR16pRsia7DF1035fezkQOVwtBSMaq84NbXQia045C0cC6oCseXcFa6z1qTP2E41ycpygmia3pJJOXVZ0c/640?wx_fmt=png&from=appmsg "")  
  
  
以下条件**同时成立**  
即受影响：  
  
1. 使用 **Open1722 ≤ 0.9.2**  
的**examples/acf-can**  
代码——直接编译安装，**或把avtp_to_can() 的解析逻辑移植进自有网关代码**  
（参考实现的示例代码最常见的归宿就是被原样照抄）；  
  
2. **acf-can-listener**  
处于运行状态并监听 UDP（默认 17220 端口）或以太网 1722 流；  
  
3. 攻击者网络可达该监听点（默认 stream ID 0xAABBCCDDEEFF0001 为公开硬编码值，无认证、无完整性校验）。  
  
**不受影响的场景：**  
examples 目录在构建系统中标记为**EXCLUDE_FROM_ALL**  
且安装为 **OPTIONAL**  
——未显式构建安装示例、也未移植其解析逻辑的系统不直接受影响；已按 issue [#154]()  
 建议（容量参数 + int 负值检查）打补丁的系统不受影响。  
  
**自检命令：**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ounUznLmib9icicFCd4t0iaF2buqnIXOOCkQ4hzQczwkwslBNibGXSJlTcH6xVgb7tzVTfLxGsHNh8KCaogBTTXevfz5pn6bQJlviajZzsrIg3ibDs/640?wx_fmt=png&from=appmsg "")  
  
在车联网场景中，先要看清**Open1722**  
的角色——它解决的是“车内以太网骨干与经典总线共存”的桥接问题：  
  
**• COVESA 官方参考实现**  
：**COVESA**  
（车联网联盟，**GENIVI**  
后继组织）是推动**SDV**  
（软件定义汽车）的行业联盟，**Open1722**  
是其维护的 **IEEE 1722（AVTP）**  
开源实现，定位就是车载以太网协议栈的参考底座——业内做 1722 相关开发大多从它起步，示例代码被移植进产品的概率远高于普通开源项目；  
  
**• 区域架构（Zonal Architecture）下的典型用法：**  
新一代 E/E 架构用以太网骨干连接中央计算与各区域控制器，而车门、座椅、灯光等末端 **ECU**  
 仍挂在 **CAN/CAN-FD**  
上——区域控制器与中央网关因此都需要“以太网**↔CAN**  
”协议桥。acf-can 示例（用 AVTP ACF 格式把 CAN 帧隧道化）正是这类桥接的参考实现；  
  
**• AVTP 在车内已有的落地形态：****1722/AVTP**  
 最初服务于车载音视频（音响主机、功放、后排娱乐、麦克风阵列），**ACF**  
子族把它扩展到 **CAN/LIN/MOST/FlexRay**  
等控制类总线的隧道化——Open1722 因此同时出现在**座舱音频网关与车身控制桥接**  
两类研发中；  
  
**• Zephyr 变体面向车载 MCU：**  
examples 中的 Zephyr 版本（acf-can-bridge）面向资源受限的实时控制器，配套提供 NUCLEO-144、Portenta H7 等评估板工程——正是区域架构里末端桥接 ECU 的典型形态，也是充电桩、边缘桥接器等嵌入式场景的常见选型；  
  
**• 研发与产线联调环境：****OEM/Tier1 的 HIL**  
台架、总线仿真、协议联调中常用这类开源桥接件快速搭建环境，示例参数**（默认 stream ID 0xAABBCCDDEEFF0001**  
、默认 UDP 17220 端口）容易原样流入试制与测试环境。  
  
**不适用条件：****仅使用 Open1722 库函数但自行实现了解析与容量处理的项目、示例未上车（未装未移植）的量产系统，不在直接暴露面内；攻击者也必须先具备到监听点的网络可达性。**  
  
**03 修复建议**  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ounUznLmib98Victdju0frV0c4xB2jPJxj0Ey2RleUkguocssfHgfA5aUhqF7MOq3cAYQoafTJ7rRvAU8bByp2h14Jtl25rNvVruJqeZicssAc/640?wx_fmt=png&from=appmsg "")  
  
  
  
**唯一可靠手段：**  
修补解析器与调用方两侧（两个漏洞必须同时修）：  
  
**• avtp_to_can()**  
增加容量参数，写入索引 i 达到上限即停止（治 73522）；  
  
• 调用方**num_can_msgs由uint8_t 改为 int**  
，对负返回值直接跳过（治 73523）；  
  
• Zephyr 变体**（acf-can-bridge.c）**  
同步修复。  
  
截至**2026-09-14，**  
上游 main 分支尚未合入修复——使用 Open1722 任何版本做网关开发的项目都需自行打补丁（本文 4.5 节包含补丁实现与验证结果）。  
  
**补丁空窗期内可做的排查与缓解：**  
  
• 改掉硬编码默认**stream ID**  
，并对**UDP17220 / 1722**  
流做来源白名单或防火墙隔离；  
  
• 网关入口把**AVTP/UDP**  
按不可信输入处理，限制到诊断网段之外不可达；  
  
• CAN 侧监测：总线上突发大量 ID/数据无规律的帧（正常流量不会出现的 ID 分布），或网关进程日志出现 **“failed validation”**  
类拒绝信息后紧跟大量总线写，即为疑似泄露信号。  
  
**04 漏洞原理分析**  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ounUznLmib997cZRwmmdl1c5QXib0ibzKbTFDG4L826jyCib6HJYj3TxjjUwjkmZaDicCHEibw5WEDibaAVAQGMR6PlTKPUAjPTUcNFF4SEbWbo6os/640?wx_fmt=png&from=appmsg "")  
  
  
**4.1 专业术语背景介绍**  
  
<table><tbody><tr style="box-sizing: border-box;"><td data-colwidth="30.0000%" width="30.0000%" style="border-width: 1px;border-color: rgb(62, 62, 62);border-style: solid;box-sizing: border-box;padding: 0px;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">术语</span></p></td><td data-colwidth="70.0000%" width="70.0000%" style="border-width: 1px;border-color: rgb(62, 62, 62);border-style: solid;box-sizing: border-box;padding: 0px;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">解释</span></p></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="30.0000%" width="30.0000%" style="border-width: 1px;border-color: rgb(62, 62, 62);border-style: solid;box-sizing: border-box;padding: 0px;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">AVTP / IEEE</span></p></td><td data-colwidth="70.0000%" width="70.0000%" style="border-width: 1px;border-color: rgb(62, 62, 62);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="text-align: left;letter-spacing: 0.5px;box-sizing: border-box;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">车载以太网音视频桥接传输协议，acf-can 用它把 CAN</span></p></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="30.0000%" width="30.0000%" style="border-width: 1px;border-color: rgb(62, 62, 62);border-style: solid;box-sizing: border-box;padding: 0px;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">1722</span></p></td><td data-colwidth="70.0000%" width="70.0000%" style="border-width: 1px;border-color: rgb(62, 62, 62);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="text-align: left;letter-spacing: 0.5px;box-sizing: border-box;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">帧封装进以太网/UDP 传输——漏洞所在的协议层</span></p></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="30.0000%" width="30.0000%" style="border-width: 1px;border-color: rgb(62, 62, 62);border-style: solid;box-sizing: border-box;padding: 0px;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">ACF</span></p></td><td data-colwidth="70.0000%" width="70.0000%" style="border-width: 1px;border-color: rgb(62, 62, 62);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="text-align: left;letter-spacing: 0.5px;box-sizing: border-box;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">AVTP Control Format，非音视频总线消息的容器格式；一条 CF 报文可串联多条 ACF 消息——“消息计数失控”的源头</span></p></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="30.0000%" width="30.0000%" style="border-width: 1px;border-color: rgb(62, 62, 62);border-style: solid;box-sizing: border-box;padding: 0px;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">TSCF</span></p></td><td data-colwidth="70.0000%" width="70.0000%" style="border-width: 1px;border-color: rgb(62, 62, 62);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="text-align: left;letter-spacing: 0.5px;box-sizing: border-box;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">时间同步控制格式（subtype 0x05），ACF 消息的“集装箱”，头部声明载荷总长 stream_data_length——驱动拆包循环的字段</span></p></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="30.0000%" width="30.0000%" style="border-width: 1px;border-color: rgb(62, 62, 62);border-style: solid;box-sizing: border-box;padding: 0px;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">stream ID</span></p></td><td data-colwidth="70.0000%" width="70.0000%" style="border-width: 1px;border-color: rgb(62, 62, 62);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="text-align: left;letter-spacing: 0.5px;box-sizing: border-box;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">8 字节流标识，listener 只处理匹配的流；示例中硬编码为 0xAABBCCDDEEFF0001，明文在线、无认证</span></p></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="30.0000%" width="30.0000%" style="border-width: 1px;border-color: rgb(62, 62, 62);border-style: solid;box-sizing: border-box;padding: 0px;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">SocketCAN / vcan</span></p></td><td data-colwidth="70.0000%" width="70.0000%" style="border-width: 1px;border-color: rgb(62, 62, 62);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="text-align: left;box-sizing: border-box;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">Linux 的 CAN 协议族与纯软件虚拟 CAN 接口；write() 上 vcan 的帧等同于“上了总线”，candump 可实收</span></p></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="30.0000%" width="30.0000%" style="border-width: 1px;border-color: rgb(62, 62, 62);border-style: solid;box-sizing: border-box;padding: 0px;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">candump</span></p></td><td data-colwidth="70.0000%" width="70.0000%" style="border-width: 1px;border-color: rgb(62, 62, 62);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="text-align: left;box-sizing: border-box;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">can-utils 的 CAN 抓包工具，CAN 世界的 tcpdump——本文泄露证据的采集工具</span></p></section></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="30.0000%" width="30.0000%" style="border-width: 1px;border-color: rgb(62, 62, 62);border-style: solid;box-sizing: border-box;padding: 0px;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">frame_t</span></p></td><td data-colwidth="70.0000%" width="70.0000%" style="border-width: 1px;border-color: rgb(62, 62, 62);border-style: solid;box-sizing: border-box;padding: 0px;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">listener 栈上的帧 union（Linux 下 72 字节），can_frames[15] 共 1080 字节——两个漏洞共同的“战场”</span></p></td></tr><tr style="box-sizing: border-box;"><td data-colwidth="30.0000%" width="30.0000%" style="border-width: 1px;border-color: rgb(62, 62, 62);border-style: solid;box-sizing: border-box;padding: 0px;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">AddressSanitizer（ASan）</span></p></td><td data-colwidth="70.0000%" width="70.0000%" style="border-width: 1px;border-color: rgb(62, 62, 62);border-style: solid;box-sizing: border-box;padding: 0px;"><section style="letter-spacing: 0.5px;box-sizing: border-box;"><p style="margin: 0px;padding: 0px;box-sizing: border-box;"><span leaf="">编译期内存错误检测器，73522 溢出写的“验钞机”</span></p></section></td></tr></tbody></table>  
  
  
**背景关键点：**  
acf-can-listener 干的事是“拆包”——把 UDP 报文里的 ACF-CAN 消息逐条还原成 CAN 帧写上总线；**拆多少条、往哪写、出错怎么办，全部由报文字段和一个 uint8_t 决定。**  
  
****  
**4.2 漏洞原理与根因分析**  
  
**4.2.1 先看清这条链：CAN 帧如何变成 UDP 报文**  
  
**CAN 帧(16B) ─①加16B ACF头─► ACF-CAN消息×N ─②套24B TSCF头─► TSCF报文 ─③加4B UDP头─► UDP数据报**  
  
listener 拆包方向正好相反：剥UDP头(4B) → 剥TSCF头(24B) → 逐条拆ACF消息 → 还原can_frame写上总线  
  
这是 talker 真实封装后我们用 **UDP**  
 抓包实收的 72 字节（标准帧**0x123**  
 + 扩展帧 0x1ABCDEF0，各带载荷），每一层都看得见：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ounUznLmib9icaStuY07N6khDzyrcTe3kBKPCKNzicibZOpuHNPZs9EdZocJOmelMtLJwYeC1Ec73UX2icL1jGPl2rPXGvVBd7PkCRM2W1pawKoo/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ounUznLmib99AR8c8GGcypXlQicF3ckg2ausZWJFl58jW3cp04ISpNFlJefOGCFNPAzOiccPpiaWvCMFtLE9kmtYcjrgY8kaUMAeTSf58tgPEdY/640?wx_fmt=png&from=appmsg "")  
  
**三个要点：**  
CAN 载荷在报文里**完全明文、原样携带**  
；TSCF 头里的 stream_data_length 是“集装箱总重”，listener 靠它决定拆多少条；**stream_id（0xAABBCCDDEEFF0001）**  
就是默认硬编码值，抓包可见、无认证。  
  
****  
**4.2.2 拆包循环：proc_bytes < msg_length——循环几次，报文说了算**  
  
**avtp_to_can()**  
 里有两个长度变量：  
  
• msg_length（终点线）= 4（UDP 头）+ 24（TSCF 头）+ **stream_data_length**  
（报文字段，攻击者控制）；  
  
• proc_bytes（进度条）从 28 起步，每拆一条前进“本条自己声明的长度”。  
  
循环**while (proc_bytes < msg_length)**  
每转一圈往数组写一帧——写**几帧完全由报文声明的总长决定，函数对“数组只有 15 槽”一无所知。**  
代入实测：15 条消息 → 28+15×16 = 268 字节，干净；16 条 → 28+16×16 = 284 字节，第 16 帧越界（与披露者“268 干净 / 284 溢出”基准一致）。  
  
**注意一个从属缺口：**  
循环只和“声明总长”对账，从不和 recv() 实际收到的字节数对账——声明一个夸大的总长，拆包就会读到接收缓冲的未初始化残留。闸门 Avtp_Can_IsValid() 也存在职责边界：它逐条审计“单条消息内部错配”（类型、长度包含、pad 防下溢、载荷≤8B/64B），**但对“消息条数”零约束**  
——所以 16 条完全合法的消息能全部过检，73522 在这道闸的管辖范围之外。  
  
****  
**4.2.3 缺陷一（73522）：没有容量闸门的写入循环**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ounUznLmib98bINjbibbtmvEYwtA7Q2Ufa8Vn6kXZKyQgeMzg8ETWg7vauf69ib5oH9aOGiaGXUFe1aYRSicAuEd8FMjdzKenzlJBLQiaWrL9lmDk/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ounUznLmib98TwvbIL1UliavSNb2cNibYvEcxw1RiapJ3CfcC4ShdPG56uyv5tJxHpNUm6QRibmVoARg0RsMrBopjdnqMm2iaz4wBnianEjDVicGqMs/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ounUznLmib9ib1d9bkUK5zQicXfmRRRejsm8g0rTSWicnxkpuRjsdDXTLaAQI4Ir5ia4MoriargXrhx1WgyibFrAMvmnMGln02iapuD4sM3yV7TlbD4/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ounUznLmib9931tngmic7kreVzTiaKYm5c2vrfEeHs5Vtian6pTFEr5w7VOcQPuP6ia5tm6tjZS56iaOianRPwDzia002Gt1h6DwDvzricg9iaIVSvBuk/640?wx_fmt=png&from=appmsg "")  
  
**数值推导（73522）：**  
合法下标 0~14，第 16 条消息 frame = &can_frames[15] 越出数组 72 字节；随后 can_id（4B）、len（1B）、最多 64B 的载荷 memcpy 全部落在 main() 栈帧数组之外。单条越界净距离 72B；一个 1500B UDP 报文最多塞约 88 条 16B 最小消息，破坏面约 (88−15)×72 ≈ 5.3 KB。写入内容（can_id、len、载荷）由攻击者逐槽控制；CAN-FD 分支（FDF=1）单槽注入量从 8B 升到**64B**  
。披露者在 x86-64 release 构建下实测：92 条消息的数据报把 main() 栈帧改写到“打印出与输入矛盾的错误信息”。  
  
****  
**4.2.4 缺陷二（73523）：-1 如何变成 255 次“成功”**  
  
**avtp_to_can()**  
的返回值复用一个通道：成功返回条数**（0~255）**  
，失败返回 -1。而**拒绝路径有五条**  
，攻击者任选其一即可：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ounUznLmib9icdHwNnmUugYJeicfZmVr3LcYUSaksroibMy5P19bna4KBzjMCn2Tfoyh4eibwFO07gSUgMaoc28jiaX4N3n4DpsNmdFKDWdTtgpIc/640?wx_fmt=png&from=appmsg "")  
  
int 型 -1（0xFFFFFFFF）存进 uint8_t num_can_msgs 时只保留低 8 位——**变成 255**  
。**:183**  
写循环于是迭代 255 次：i=0~14 读数组（旧流量/未初始化栈），i=15~254 越过 1080B 边界继续往栈高地址读。  
  
**数值推导（73523）：**  
 源地址按 72B/槽步进、每次 write() 16B：读取范围 255×72 ≈ **18 KB**  
 栈区间，落总线 255×16 = 4080 字节。披露者 x86-64 实测约 240 帧落总线；我们 ARM64 实测 63~69 帧（其余被内核 len>8 校验与未映射页 EFAULT 拦下——但失败只丢这一帧，循环继续，**这就是它“稳定泄露而不自我毁灭”的原因）。**  
  
泄露内容不是随机噪声，而是分层的：  
  
**• 前 15 帧：**  
can_frames 数组现状——**最近真实 CAN 流量的回放；**  
  
**• 其后：**  
相邻栈局部变量、pdu[1500] 接收缓冲残留（最近收到的报文原文，我们实测抓到 AVTP stream ID 明文 AA BB CC DD EE）；  
  
**• 更深：**  
保存的寄存器/返回地址方向的内容（**栈指针，ASLR 布局信息**  
）、argv（listener.--canif）、环境变量（SHELL=、PWD=、HOME=）。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ounUznLmib98CAUtNIh7bolw012OkXK60jt1icJRjiaSDWe6EQOqVvqg5pkVqt351YwAjzsSZGTXFqoM9w8nJ0wltgFM8icJ0Alp8f5QX8qqiaE8/640?wx_fmt=png&from=appmsg "")  
  
**根因一句话： 73522 是“解析器不知道数组有多大”，73523 是“调用方把 -1 读成了 255”——两个缺陷分属解析器与调用方，修复必须双侧同修，只修其一另一个依然成立。**  
  
****  
**4.2.5 修复实现与验证（本团队按 issue #154 建议实现）**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ounUznLmib9ibugIrIhhUhrJrlicLlCR0poWI10chydSLYiclLyCLGAEoEtULbUhm9ed4Mj8UJS8SOgGmNTarXjKIF0XxGZDoJVJZYQBibdnCvDI/640?wx_fmt=png&from=appmsg "")  
  
回测（4.5 节环境）：16 条消息 → 总线恰好 15 帧并打印 capacity 拦截日志；拒绝报文 → 0 帧、进程存活。**上游 main 截至 2026-09-14 未合入修复，Zephyr 变体同模式亦未修。**  
  
****  
**4.3 白话介绍**  
  
把 listener 想象成快递分拣中心里一名**照单分拣的工人：**  
  
• 他的工作台上只有**15 个格子**  
（can_frames 数组）。每来一张“总单”（UDP 报文），单子上写着“本单共 N 件”（stream_data_length），他就逐件把包裹誊进格子；  
  
**• 第一个漏洞（73522）：**  
规则里从来没说“格子满了要停”。来一张写着 16 件的单子，前 15 件放格子，第 16 件他顺手塞进了**隔壁同事的工位**  
（相邻栈内存）——包裹里装的什么（can_id、载荷）全由寄件人决定。单子上写 88 件，他能一路塞进好几米外的工位；  
  
**• 第二个漏洞（73523）：**  
遇到“问题件”（被拒绝的报文），流程应该回执“-1 件，本次不发货”。但这名工人**只认单子上的最后一个数字**  
（uint8_t 只取低 8 位）——“-1”在他眼里是“255 件”。于是他不但把 15 个格子全发了，还把自己**办公桌抽屉里的东西**  
继续打包：快递单存根（pdu 缓冲里的 stream ID 明文）、工牌和钥匙串（argv、环境变量）、记满地址的便签（栈指针），一件一件全发上传送带（CAN 总线）——而且发完他继续正常上班，**谁也不报警。**  
  
**结果就是：**  
寄件人什么都不用破解，要么往分拣中心的邻居工位塞私货（栈写），要么让分拣中心把自己的办公秘密广播给整条传送带（栈泄露）——而后者发生时，系统日志里只有几行无关紧要的“写失败”。  
  
  
**4.4 攻击场景与触发条件**  
  
**触发前提（漏洞层面）：**  
 listener 运行且网络可达（UDP 17220 或 1722 流）；报文使用公开的默认 stream ID；73522 需要 ≥16 条合法 ACF-CAN 消息（单报文即可），73523 只需一个触发任一拒绝路径的报文。**两个漏洞均无需认证、无需用户交互、无需任何先立足点。**  
  
**典型攻击场景：**  
  
**• 车载以太网-CAN 网关原型：**  
基于 Open1722 示例搭建的网关，攻击者经诊断网段或被攻陷的车内 ECU 向其 17220 端口发报文——73523 把网关进程栈（会话数据、密钥材料、ASLR 布局）泄到 CAN 总线供全车广播，73522 提供潜在代码执行落点；  
  
**• 基于 Zephyr 的车载 MCU / 边缘桥接器：**  
Zephyr 变体同样的 15 槽无界模式位于无保护页线程栈，泄露/破坏直接落在 RTOS 关键任务内存区；  
  
**• 研发与产线测试设备：**  
工程师本机或测试台架上跑 acf-can 示例做协议联调，示例参数（默认 stream ID、默认端口）原样保留，被同网段扫描发现后即成为入口。  
  
**绕过能力：**  
73523 的泄露不产生崩溃、不产生内核告警，主機日志仅有 write() 的 EFAULT 报错（垃圾帧 len 字段超 8 被内核拒绝），常规“看进程是否存活”的健康检查完全无感；默认 stream ID 硬编码且公开，意味着“认证”形同虚设。  
  
****  
**4.5 漏洞复现**  
  
4.5.1 复现环境  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ounUznLmib99Soc9dGkqk1qHLXDVucHpEwpIicdK4ibAxDkGZgiciajNtZnDyF6IStbcrxITb8tKEIc2knTv26ZS3ApWFRSHH6afCXUeZkjI2euU/640?wx_fmt=png&from=appmsg "")  
  
**说明：**  
vcan0 为纯软件虚拟 CAN 接口，与真实 CAN 接口在 SocketCAN 层行为一致；candump 抓到的帧与物理总线等价。另在 macOS Docker（无 CAN 内核）环境用 --wrap=setup_can_socket 链接期 shim 做过平行复现，结论一致（详见工作区手册）。  
  
4.5.2 复现原理  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ounUznLmib98rIeicbH4uq7FyNkWkl28nVbIBTvLibMzibdu46o0JfXYwFVAJDj4YvNg7eaQ1TBDImUH4s3jicxXiaFoPYcblZDDeYzvjCLib5ytQU/640?wx_fmt=png&from=appmsg "")  
  
4.5.3 复现过程与结果  
  
**73522 实录（三窗口：攻击端 / 受害进程 / candump）：**  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ounUznLmib9ibUXXUh6hXgcF7f4N5atSmZtU1IDyRgcicRkvAj05VicpdN0K7s8WOPvIPvk307Y5YYPicWQLaBDIBVTOrv7XPKsnxA2KicRicjVrAQ/640?wx_fmt=png&from=appmsg "")  
  
**73522 终端复现实录（攻击端与效果分窗，CAN 总线为真 candump）**  
  
**关键输出：**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ounUznLmib98gHjEl9dFqZ9B0iawiaH0Hk7N1ZCpAGibZ4dXXr2BtYA1FtSRPuh3gR9fBIMvaeoQNYYLre231UUKAshh6eibzJzBpia28d4ZcWGhU/640?wx_fmt=png&from=appmsg "")  
  
**73523 实录（三窗口：攻击端 / 受害进程 / candump）：**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ounUznLmib9ibNK6pZyTeNjk8BspWxKzHVIsJ4hibMv7fBibE2c1AGMK8wm8OuUelHS9I9YuJDpiciacSRBQfib8Y8ibOhysQ1jaZoIq75fUZPj5NqM/640?wx_fmt=png&from=appmsg "")  
  
**73523 终端复现实录**  
（受害进程刷 EFAULT 但不崩溃；总线上标记帧与泄露帧同屏）  
  
**关键输出（candump 实收，真实 vcan0）：**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ounUznLmib9ickqVglA92AR2NN503dyX3icxXtSicb8Swdgw4gcjufED6BMhBQJTt8ZjscSbibUbtGnxibO32bpg7nIR8qVUR2u6vMcsBDo4Q2ejE/640?wx_fmt=png&from=appmsg "")  
  
  
**05 车联网相关性分析**  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ounUznLmib98kPSrk3jsbZEa945y5dNe8oJbxiaLgicwmFFCZT03cRepEXGFAIH0v5KtXTIrib78mzbdxHgfT5zsB36NOMyX84x9xgxnxunwruc/640?wx_fmt=png&from=appmsg "")  
  
  
这两个漏洞**不是直接冲击制动、转向等功能安全的漏洞，**  
但对采用“车载以太网 + CAN”混合拓扑的智能汽车具有明确的安全相关性：  
  
**• 协议桥接是 SDV 的标准组件**  
，Open1722 是 COVESA 官方参考实现——参考实现的示例代码会被原样抄进网关原型，两个缺陷会一起被继承；**Zephyr**  
 变体则直接覆盖车载 **MCU**  
场景；  
  
**• 典型攻击链：**  
攻击者经诊断网段 / 被攻陷的车内 ECU / 供应链测试设备获得对网关 17220 端口的可达性 → 73523 泄露网关进程栈（会话、密钥材料、ASLR 布局）到 CAN 总线 → 73522 的数据可控栈写作为潜在代码执行落点 → 以网关为跳板向 CAN 总线注入任意帧；  
  
**• 73523 的泄露方向值得特别注意：**  
它把“以太网侧不可信输入”转换成“CAN 总线侧的信息广播”——泄露面从单个进程扩大到**总线上所有监听者；**  
  
**•**  
在**ISO/SAE 21434**  
的语境下，这对漏洞属于网关组件的**网络安全缺陷（信息泄露 + 内存破坏）**  
，而非直接的功能安全故障；车企与 Tier1 应在 SBOM 与开源组件审计中排查 Open1722 examples 的使用与移植情况，重点核查车载以太网-CAN 网关、基于Zephyr 的桥接 ECU、研发/产线联调环境。  
  
**06 参考链接**  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ounUznLmib9ibial2fRCpraiac1jiaOCZTCtP8b9DXj0Xtbd7BAaBneokHdwcrqpbVFfTGbNBuJhvXcuiafTPvXjzy58bXHwz3frNoMQciaMafmt5c/640?wx_fmt=png&from=appmsg "")  
  
  
**• 官方披露（含复现基准与修复建议）：**  
 https://github.com/COVESA/Open1722/issues/154  
  
**• VulnCheck 公告：**  
 https://www.vulncheck.com/advisories/covesa-open1722-stack-memory-disclosure-via-acf-can-listener-c-integer-truncation  
  
**• NVD：**  
 https://nvd.nist.gov/vuln/detail/CVE-2026-73523 / https://nvd.nist.gov/vuln/detail/CVE-2026-73522  
  
**• Open1722 仓库：**  
 https://github.com/COVESA/Open1722  
  
上期回顾  
  
  
[](https://mp.weixin.qq.com/s?__biz=Mzg3MTY3NTY4MA==&mid=2247488276&idx=1&sn=6be1bb719e68376282ce08ebfc4afa6e&scene=21#wechat_redirect)  
  
  
END  
  
  
  
**扫码申请获取PoC**  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/ounUznLmib98IdbJ9NrIAZDibTaUrxmWR1oS6H7jWg945AiaQ6JsNuq23sYviavhYicD39V5DUOmlic3eS6W2qeVvlwc5IUI3nuicKqpTq7feWicYW0/640?wx_fmt=jpeg&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ounUznLmib99Yj0cXg1JttsrAgDqtqiazibG7YvCtUKIo8Mt2IiaSxJrniafdibkN5VkPtjdGNm5oMLrb4e2qCgo5Hs8awScNyIlqibDrib7ls0SAa8/640?wx_fmt=png&from=appmsg "")  
  
  
  

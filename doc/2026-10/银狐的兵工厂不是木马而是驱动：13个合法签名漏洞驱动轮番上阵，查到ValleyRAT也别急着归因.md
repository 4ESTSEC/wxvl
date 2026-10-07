#  银狐的兵工厂不是木马而是驱动：13个合法签名漏洞驱动轮番上阵，查到ValleyRAT也别急着归因  
原创 安全情报官
                    安全情报官  AI和提效工具实验记   2026-10-07 01:14  
  
# 银狐的兵工厂不是木马而是驱动：13个合法签名漏洞驱动轮番上阵，查到ValleyRAT也别急着归因  
  
SILVER FOX THREAT INTELLIGENCE DAILY  
  
## 一、今日概览  
  
1.**头条（补录）**  
：独立研究机构 Alpha Cyber Research 于 9 月 28 日发布《Silver Fox and the Signed-Driver Arsenal》汇总报告，首次系统性清点银狐 2024–2026 年间在至少 5 场战役中滥用的 **13 个以上合法签名但存在漏洞的驱动**  
（多数滥用时不在微软黑名单），并高置信度裁定「银狐运营 Go RAT」的业界流传说法**不成立**  
（Go 代码仅出现在投递层 stager 与假安装器）；报告同时确认 Check Point 数据——ValleyRAT 构建器 2025 年 3 月公开后，2024-11~2025-11 间约 6,000 个样本中 85% 集中于最后 6 个月——**ValleyRAT 检出已不能再作为银狐归因依据**  
。本报 10-03 待核实的 Cato 日本三驱动（BootRepair / EnPortv / wsftprm）经此报告确认为 2026-07 日本战役旧驱动、非新增。  
  
2.**监测面**  
：Derp C2 tracker 窗口**连续第 4 天冻结**  
在 9/28–10/4（12 C2 / Last activity 10-2，三字段与 10-04~10-06 完全一致）。按本报 10-06 预设的「真休眠（A）vs 采集盲区（B）」判别框架，连续 4 天冻结已触发 **B（采集盲区）权重上调**  
；但微步侧同步低频（事件库停于 10-03）仍与 A 兼容，维持双假设并下调 A 置信至「低-中」。  
  
3.**微步 IoC 池**  
：7,227 → **7,254（+27）**  
，连续第四日小步回补；事件库头条仍为 10-03 微软假软件下载链收录，无新事件。  
  
4.**CCTGA**  
：第六批恶意程序通告持续未发布（专项已于 9-28 转常态化治理，第五批后仅 9-23 阶段总结）。  
  
5.**二次传播预警（蓝队需知）**  
：博客园 0xfisher 10-05 长文《银狐木马新一轮攻击全链拆解》在中文社区传播升温，经核实为**火绒 7-30 TrueSight 报告 + 0xfisher 10-03 DoH 分析的合成总集篇**  
，全部 IOC（8.218.106.149:7000、156.251.16.226、oidng2.duoshit.com 等）均为已知指标、本报 7-31 与 10-04 已分别覆盖——企业侧若收到一线人员转询该文，可直接对应到既有检测规则，无需新建。  
## 二、重点事件  
### 2.1 【补录·头条】Alpha Cyber 清点银狐「签名驱动兵工厂」：13 驱动 × 5 场役，Go RAT 传言被裁定不成立  
  
•**时间**  
：2026-09-28 发布（TLP:CLEAR）  
  
•**来源**  
：Alpha Cyber Research — silver-fox-and-the-signed-driver-arsenal（https://alpha-cyber.com/silver-fox-and-the-signed-driver-arsenal）  
  
•**要点**  
：  
  
•**13+ 驱动清单**  
（本报此前零散覆盖仅 3-4 个，此为首次完整成建制清点）：     | 驱动 | 合法签名方 | CVE | 场役 | 证据强度 |     |---|---|---|---|---|     | amsdk.sys v1.0.600 | WatchDog（Zemana SDK） | 无 | 2025-05 末 Win10/11 | 一手 |     | wamsdk.sys v1.1.100 | WatchDog 补丁版 | 无 | 2025（补丁后） | 一手 |     | zam.exe | Zemana | 无 | Win7 等旧系统 | 一手 |     | **Truesight.sys v2.0.2**  
（另改名 189atohci.sys / TTruespanl.sys） | Adlice (RogueKiller) | 无；2024-12-17 入黑名单 | Silent Killers（载荷为 Gh0st RAT）/ Philips DICOM 医疗战役 2024-12~2025-01 / 2024-09 | 一手 |     | NSecKrnl64.sys | **山东安泽信息**  
（NSecsoft 产品；**并非 NVIDIA**  
——dropper 故意命名 NVIDIA.exe） | CVE-2025-68947 (CVSS 5.7) | 2025-09 / 2025-11 | 一手 |     | rwdriver.sys | 中兴（过期泄露证书） | 无 | 2025-10~11（改内核回调地址致盲 EDR） | 一手 |     | BootRepair.sys | 未具名 | 无 | 2026-07 日本 | 一手 |     | EnPortv.sys | EnCase 取证套件 | 无 | 2026-07 日本 | 一手 |     | wsftprm.sys | Topaz Antifraud (Warsaw) 银行防护 | CVE-2023-52271 | 2026-07 日本（复用） | 一手 |     | wnBios | wnBios | 无；已在 LOLDrivers | 2026-04 Telegram MSI（物理内存访问，非杀软杀手） | 单源 |     | BdApiUtil64.sys | 百度杀毒 | CVE-2024-51324 | 2025-10 | 单源（微步报告论坛转录） |     | Cndom6.sys | 北京天水科技（过期证书） | 无 | 2025-10（InfinityHook 拦截系统调用藏进程） | 单源 |     | XiaoH.sys | 上海齐思教育科技（过期证书） | 无 | 2025-10（劫持 nsiproxy.sys IRP 回调伪造连接表藏 C2） | 单源 |  
  
•**签名魔术**  
：Check Point 发现某战役中**仅修改 1 个时间戳字节即可让补丁版驱动保持微软签名有效、同时文件哈希改变**  
——哈希封禁被设计性绕开。  
  
•**ValleyRAT Driver Plugin（内核 rootkit）**  
：2025-12 对泄露 builder 的分析显示，其驱动插件可在**全新 Windows 11 + HVCI + Secure Boot 全开**  
环境下加载（利用 2015-07-29 前旧证书签名例外，非 HVCI 绕过），能力包括受保护进程增删、强制删文件、APC 注入，且**直接强删 360 / 火绒 / 腾讯 / 金山 / 卡巴斯基的内核驱动文件**  
（不只杀用户态进程）。  
  
•**归因研究现状梳理**  
：ReliaQuest（高置信归因银狐、国家级间谍与网赌资金混合）、Sekoia（中国境内、财政动机、2024 起 APT 化）、Cato（中高置信复合归因）、PwC（不表态）；四家起始时间互相打架（2022H2 / 2022 / 2023 初 / 2024 初）——报告结论：**该行为体的画像应基于驱动兵工厂而非恶意软件家族名**  
。  
  
•**附带辟谣**  
：广泛流传的「Go RAT」说法系对投递层 Go stager 的误读；Winos 是恶意软件名而非行为体名。  
  
•**影响面**  
：企业 EDR/驱动加载监控可一次性纳入 13 驱动基线；「检出 ValleyRAT = 银狐」的归因话术在企业威胁建模与上报口径中需修正；报告附带可直接落地的 SIGMA 规则（id 07eeedd3-0835-46e0-965b-121480a3d9fd  
）与 PowerShell 只读狩猎查询（见本报第五节转引）。  
### 2.2 【监测面】Derp 冻结第 4 天：双假设判别框架更新，采集盲区权重上调  
  
•**时间**  
：2026-10-07 09:05 抓取  
  
•**来源**  
：Derp ValleyRAT 7d C2 Tracker（https://www.derp.ca/win_valley_rat）  
  
•**要点**  
：窗口 / C2 总数 / Last activity 三字段连续第 4 天（10-04、10-05、10-06、10-07）完全一致——9/28–10/4、12 C2（11 IP + 2 主机名）、HK10 / SG1 / US1、Last activity 10-2、10/2 当日 +2 C2 + 3 样本。已超出本报历史记录的「回填延迟 1 天」与「窗口滑动」两种已知修正模式。  
  
•**判别框架更新**  
（承接 10-06）：  
  
•  
 假设 A（真休眠）：CCTGA 专项打击（IP:端口处置 2,588 个、域名 8,587 个）后团伙转入基础设施重建期——微步事件库同步停更与此兼容；  
  
•  
 假设 B（采集盲区）：Derp 管线自 10/2 后停止采集或图表停止渲染——连续 4 天零滑动使 B 权重**上调**  
；  
  
•  
 维持结论：**在窗口恢复滑动或 Derp 侧数据管线状态得到确认前，本 tracker 数据不作为「团伙活跃/休眠」的唯一判据**  
；存量 12 C2 的回撞检测价值不变。  
  
•**影响面**  
：依赖 Derp 做银狐活跃度周报/月报的团队需在图表上标注「10/3 起数据置信度下降」。  
### 2.3 【监测面】微步 IoC +27 至 7,254，事件库停更第 4 天  
  
•**时间**  
：2026-10-07 09:05 抓取  
  
•**来源**  
：微步在线银狐情报共享站（https://s.threatbook.cn/cybercrime/silverfox）  
  
•**要点**  
：收录 IoC 7,254（10-06 为 7,227，+27）；收录事件 1,189 个、持续狩猎 1,199 天；事件库头条仍为 10-03「微软披露假冒软件下载传播恶意安装程序」。热点 IoC 面板未见成批新域名/IP（+27 为常规回补量级，约为日常均值的 1/3）。  
  
•**影响面**  
：低——维持「银狐公开披露静默期」判断，与 Derp 低频互证但不足以区分 A/B 假设。  
### 2.4 【二次传播甄别】0xfisher 博客园合成长文升温：火绒 7-30 + 0xfisher 10-03 的拼装  
  
•**时间**  
：2026-10-05 08:00 发布，10-06~07 搜索权重上升  
  
•**来源**  
：博客园 0xfisher（https://www.cnblogs.com/32bin/p/23200570）  
  
•**要点**  
：全文以「新一轮攻击」为叙事框架，实际内容 = **火绒 7-30《层层伪装 暗藏玄机》（TrueSight.sys / 212 进程杀手名单 / vdi_ipc.dat 三层容器 / thumbs!Edge 56 指令 / C2 8.218.106.149:7000）+ 0xfisher 本人 10-03 DoH 分析（oidng2.duoshit.com 经 AliDNS/Google DoH）**  
的合成，另拼接 AtlasCross（2026-03 旧案）、RustSL、GAC 劫持、WDAC 借刀杀人等已知公开材料；文中「管控终端滥用上线 IP 156.251.16.226」即火绒报告原始 IOC。**全部 IOC 均为已知，无新样本、无新 C2**  
。  
  
•**处置建议**  
：不入库增量；但该文已成中文社区新的「银狐科普入口」（阅读量正在爬升），蓝队值守时若收到内部转发询问，按既有规则应答即可；其 Suricata 规则段质量尚可（DoH POST /dns-query 检测、AtlasCross 握手魔数 53 46 75 63 6b  
），可作规则参考但注意 reference:url  
 字段为占位符。  
  
•**影响面**  
：无新增风险面；避免一线将「新一轮攻击」误读为新战役。  
### 2.5 【旧闻回流·不计增量】今日检索面其他银狐条目甄别  
<table><thead><tr><th style="background-color:#2c3e50;color:#fff;padding:10px;text-align:left;font-weight:600;font-size:13px;border:none;"><section><span leaf="">条目</span></section></th><th style="background-color:#2c3e50;color:#fff;padding:10px;text-align:left;font-weight:600;font-size:13px;border:none;"><section><span leaf="">实际日期</span></section></th><th style="background-color:#2c3e50;color:#fff;padding:10px;text-align:left;font-weight:600;font-size:13px;border:none;"><section><span leaf="">判定</span></section></th></tr></thead><tbody><tr><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">c.eihee.com「网安局公布 5 起银狐案例」（吉林 700 万等）</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">2026-06-16 公安部通稿</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">第 N 次回流，本报 9-19 已甄别</span></section></td></tr><tr style="background-color:#f8f9fa;"><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">targetedlocalleads / izendestudioweb「日本制造商 3 驱动 BYOVD」</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">Cato 2026-07 原始报告</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">内容农场翻炒，本报 7 月已覆盖</span></section></td></tr><tr><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">essgroup.tech「Pelagos KuGou WhatsApp 马来西亚链」</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">2026-09-27/28</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">本报 10-03 已覆盖（134.122.155.135:443 链）</span></section></td></tr><tr style="background-color:#f8f9fa;"><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">secnews.in「微软克隆下载站战役」</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">2026-10-03 微软报告</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">二次传播，本报 10-03/10-04 已覆盖</span></section></td></tr><tr><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">infosecurity-magazine「Philips DICOM 医疗战役」</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">2025-03 Forescout</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">旧闻，Truesight 复用案例之一</span></section></td></tr><tr style="background-color:#f8f9fa;"><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">SC Media / informationsecurity.report「QN Wallpaper 假广告软件」</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">2025-09 Kaspersky</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">本报 9 月已覆盖</span></section></td></tr></tbody></table>## 三、技术分析  
### 3.1 Alpha Cyber 报告核心画像：为什么「驱动兵工厂」比「家族名」更适合刻画银狐  
  
该报告的方法论价值在于把银狐的**对抗核心从恶意软件层上移到了驱动层**  
：  
  
•**横向覆盖**  
：13+ 驱动横跨杀软（Zemana/WatchDog/百度）、反 rootkit（Adlice TrueSight）、取证（EnCase）、银行防护、教育/安全厂商（过期证书）五类合法签名来源——签名来源的多样性说明是**系统性选型**  
而非单点利用；  
  
•**纵深组合**  
：kill（终止进程：amsdk/TrueSight）、blind（致盲 EDR：rwdriver 改内核回调）、hide（藏进程/藏 C2：Cndom6 InfinityHook、XiaoH nsiproxy IRP 伪造）三种能力分层配备，对应不同战役需求；  
  
•**签名保活**  
：1 字节时间戳改写保持微软签名有效 + 多数驱动滥用时不在黑名单——意味着**黑名单驱动部署永远滞后一个战役周期**  
，防护重心应转向「设备名 + IOCTL 码 + 签名者」等不随哈希变化的特征。  
### 3.2 ValleyRAT Driver Plugin：针对国产 EDR 驱动文件的「拔根」战术  
  
2025-12 泄露 builder 分析（Alpha Cyber 引用）确认了与本报长期观察互补的关键细节：该内核插件不只终止用户态进程，而是**直接强制删除 360 / 火绒 / 腾讯 / 金山 / 卡巴斯基的内核态驱动文件**  
——即使安全软件用户态进程存活，失去驱动支撑后实时防护与回调监测同步失效。这解释了 2025 下半年起多起「EDR 进程在、防护已死」的受害现场特征。其在 HVCI + Secure Boot 全开环境下依赖 2015 年前旧证书例外加载成功，说明**仅开 HVCI 不构成对旧签名驱动的防线**  
。  
### 3.3 0xfisher 合成文的技术要素归属核对（供蓝队溯源）  
<table><thead><tr><th style="background-color:#2c3e50;color:#fff;padding:10px;text-align:left;font-weight:600;font-size:13px;border:none;"><section><span leaf="">合成文中的技术点</span></section></th><th style="background-color:#2c3e50;color:#fff;padding:10px;text-align:left;font-weight:600;font-size:13px;border:none;"><section><span leaf="">原始出处</span></section></th><th style="background-color:#2c3e50;color:#fff;padding:10px;text-align:left;font-weight:600;font-size:13px;border:none;"><section><span leaf="">本报覆盖日</span></section></th></tr></thead><tbody><tr><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">TrueSight.sys / IOCTL 0x22e044 / 212 进程名单</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">火绒 2026-07-30</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">2026-07-31</span></section></td></tr><tr style="background-color:#f8f9fa;"><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">thumbs!Edge / 8.218.106.149:7000 / 56 指令</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">火绒 2026-07-30</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">2026-07-31</span></section></td></tr><tr><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">156.251.16.226 管控终端上线 IP</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">火绒 2026-07-30</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">2026-08-01</span></section></td></tr><tr style="background-color:#f8f9fa;"><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">DoH（223.5.5.5/8.8.8.8）解析 oidng2.duoshit.com</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">0xfisher 2026-10-03</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">2026-10-04</span></section></td></tr><tr><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">AtlasCross / Setup Factory / EV 证书</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">Hexastrike 2026-03</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">2026-08-04</span></section></td></tr><tr style="background-color:#f8f9fa;"><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">WDAC SiPolicy 借刀杀人 / GAC 劫持 / PoolParty</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">公开汇总（微步 2025-10 月报等）</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">历史多期</span></section></td></tr></tbody></table>### 3.4 ATT&CK 映射（本期聚焦 Alpha Cyber 补录项）  
<table><thead><tr><th style="background-color:#2c3e50;color:#fff;padding:10px;text-align:left;font-weight:600;font-size:13px;border:none;"><section><span leaf="">战术</span></section></th><th style="background-color:#2c3e50;color:#fff;padding:10px;text-align:left;font-weight:600;font-size:13px;border:none;"><section><span leaf="">技术</span></section></th><th style="background-color:#2c3e50;color:#fff;padding:10px;text-align:left;font-weight:600;font-size:13px;border:none;"><section><span leaf="">说明</span></section></th></tr></thead><tbody><tr><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">防御规避 (Defense Evasion)</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">T1553.002 滥用代码签名</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">13 驱动全部持合法/过期签名；1 字节改写保签名换哈希</span></section></td></tr><tr style="background-color:#f8f9fa;"><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">防御规避</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">T1562.001 禁用安全工具</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">内核态终止 212 进程 + 强删 EDR 驱动文件</span></section></td></tr><tr><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">防御规避</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">T1562.006 清除事件日志/痕迹</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">Cndom6.sys 系统调用拦截藏进程</span></section></td></tr><tr style="background-color:#f8f9fa;"><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">权限提升 (Privilege Escalation)</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">T1068 利用漏洞签名驱动</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">CVE-2025-68947 / CVE-2023-52271 / CVE-2024-51324</span></section></td></tr><tr><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">防御规避</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">T1014 Rootkit</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">ValleyRAT Driver Plugin（受保护进程 + APC 注入）</span></section></td></tr><tr style="background-color:#f8f9fa;"><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">命令与控制</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">T1071.001 / T1573</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">thumbs!Edge 固定帧协议 + 简单编码（(b^0xfc)+0x31，承接火绒报告）</span></section></td></tr><tr><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">命令与控制</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">T1572 协议隧道</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">DoH 隧道（已知，10-04 覆盖，今日合成文再传播）</span></section></td></tr><tr style="background-color:#f8f9fa;"><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">隐藏基础设施</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">T1090 / 连接表伪造</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">XiaoH.sys 劫持 nsiproxy.sys IRP 伪造 netstat 输出</span></section></td></tr></tbody></table>## 四、IOC 清单  
### 4.1 Alpha Cyber 9-28 报告新增狩猎基线（13 驱动文件名 + 2 签名者）【新增·高优先级】  
<table><thead><tr><th style="background-color:#2c3e50;color:#fff;padding:10px;text-align:left;font-weight:600;font-size:13px;border:none;"><section><span leaf="">类型</span></section></th><th style="background-color:#2c3e50;color:#fff;padding:10px;text-align:left;font-weight:600;font-size:13px;border:none;"><section><span leaf="">指标</span></section></th><th style="background-color:#2c3e50;color:#fff;padding:10px;text-align:left;font-weight:600;font-size:13px;border:none;"><section><span leaf="">备注</span></section></th></tr></thead><tbody><tr><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">驱动文件</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">amsdk.sys (v1.0.600) / wamsdk.sys (v1.1.100)</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">WatchDog；wamsdk 注意 1 字节改写变体</span></section></td></tr><tr style="background-color:#f8f9fa;"><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">驱动文件</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">Truesight.sys / 189atohci.sys / TTruespanl.sys</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">Adlice 签名；2024-12-17 入微软黑名单</span></section></td></tr><tr><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">驱动文件</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">NSecKrnl64.sys</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">CVE-2025-68947；dropper 名 NVIDIA.exe 是伪装</span></section></td></tr><tr style="background-color:#f8f9fa;"><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">驱动文件</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">rwdriver.sys</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">中兴过期证书；改内核回调</span></section></td></tr><tr><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">驱动文件</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">BootRepair.sys / EnPortv.sys / wsftprm.sys</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">2026-07 日本战役三件套</span></section></td></tr><tr style="background-color:#f8f9fa;"><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">驱动文件</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">zam.exe（Zemana）/ wnBios / BdApiUtil64.sys / Cndom6.sys / XiaoH.sys</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">后三个为单源（微步论坛转录），入库标注置信度</span></section></td></tr><tr><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">签名者字符串</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">&#34;Shandong Anzai&#34; / &#34;Adlice&#34;</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">Sigma 规则匹配字段</span></section></td></tr><tr style="background-color:#f8f9fa;"><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">服务名</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">Termaintor / Amsdk_Service</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">Alpha Cyber PowerShell 查询附带</span></section></td></tr><tr><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">设备名</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">\.\TrueSight</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">IOCTL 0x22e044</span></section></td></tr><tr style="background-color:#f8f9fa;"><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">CVE</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">CVE-2025-68947 / CVE-2023-52271 / CVE-2024-51324</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">关联驱动见 2.1 表</span></section></td></tr></tbody></table>### 4.2 微步 IoC 池快照（10-07）  
<table><thead><tr><th style="background-color:#2c3e50;color:#fff;padding:10px;text-align:left;font-weight:600;font-size:13px;border:none;"><section><span leaf="">指标</span></section></th><th style="background-color:#2c3e50;color:#fff;padding:10px;text-align:left;font-weight:600;font-size:13px;border:none;"><section><span leaf="">数值</span></section></th><th style="background-color:#2c3e50;color:#fff;padding:10px;text-align:left;font-weight:600;font-size:13px;border:none;"><section><span leaf="">环比</span></section></th></tr></thead><tbody><tr><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">收录 IoC</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">7,254</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">+27（10-06 为 7,227）</span></section></td></tr><tr style="background-color:#f8f9fa;"><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">收录事件</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">1,189</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">持平</span></section></td></tr><tr><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">事件库头条</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">微软假软件下载链（2026-10-03）</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">停更第 4 天</span></section></td></tr></tbody></table>### 4.3 已知指标再确认（合成文传播导致查询量上升，均非新增）  
<table><thead><tr><th style="background-color:#2c3e50;color:#fff;padding:10px;text-align:left;font-weight:600;font-size:13px;border:none;"><section><span leaf="">类型</span></section></th><th style="background-color:#2c3e50;color:#fff;padding:10px;text-align:left;font-weight:600;font-size:13px;border:none;"><section><span leaf="">指标</span></section></th><th style="background-color:#2c3e50;color:#fff;padding:10px;text-align:left;font-weight:600;font-size:13px;border:none;"><section><span leaf="">首次披露</span></section></th></tr></thead><tbody><tr style="background-color:#f8f9fa;"><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">C2</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">8.218.106.149:7000（thumbs!Edge 固定帧）</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">火绒 2026-07-30</span></section></td></tr><tr><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">C2</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">156.251.16.226（管控终端上线 IP）</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">火绒 2026-07-30</span></section></td></tr><tr style="background-color:#f8f9fa;"><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">C2 域名</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">oidng2.duoshit.com（DoH 解析）</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">0xfisher 2026-10-03</span></section></td></tr><tr><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">样本 MD5</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">8c4fc902905459a53f686372a1a85526（雷电模拟器伪装）</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">0xfisher 2026-10-03</span></section></td></tr><tr style="background-color:#f8f9fa;"><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">握手魔数</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">53 46 75 63 6b 00 00 00（AtlasCross）</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">Hexastrike 2026-03</span></section></td></tr></tbody></table>### 4.4 Derp 存量 C2（窗口 9/28–10/4，冻结第 4 天）  
  
•  
 窗口 12 C2（11 IP + 2 主机名）：HK 10 / SG 1 / US 1；Last activity 2026-10-02  
  
•  
 10/2 新增 3 样本 SHA256（3e90a76f… / 5c1c6ec4… / 5fc62cfd…）仍为最近一次真实投放，加载链细节**待核实**  
（Triage 公开报告不充分，延续）  
## 五、检测与防护建议  
  
1.**【今日优先】部署 Alpha Cyber 驱动基线狩猎**  
：  
  
2.  
 SIGMA（转引，id 07eeedd3-0835-46e0-965b-121480a3d9fd  
）：ImageLoaded endswith  
 13 驱动文件名 **或**Signature contains "Shandong Anzai"/"Adlice"  
，级别 high；误报场景为这些驱动的正版软件安装（WatchDog/RogueKiller/EnCase/Warsaw/百度杀毒）；  
  
3.  
 PowerShell 只读排查（转引）：Get-CimInstance Win32_SystemDriver  
 过滤 13 基名 + Get-Service Termaintor,Amsdk_Service  
；  
  
4.  
 注意报告提醒：**文件名可随意改、漏报不等于干净**  
，建议叠加「签名者 + 版本 + 设备名 + IOCTL 码」多维匹配。  
  
5.**驱动加载纵深（旧证书例外防线）**  
：Windows 11 22H2+ 确认「易受攻击驱动程序黑名单」策略未被关闭（部分企业镜像默认关闭）；对 2015-07-29 前签名证书的驱动启用 WDAC 策略审查——ValleyRAT Driver Plugin 证明 HVCI + Secure Boot 全开不能挡旧签名驱动。  
  
6.**EDR 驱动文件完整性监控**  
：针对「强删安全软件驱动文件」战术，对 C:\Windows\System32\drivers\  
 下自有 EDR/杀软驱动文件的删除行为（尤其来自 SYSTEM 服务进程且伴随随机名 .sys 落盘）设高优告警。  
  
7.**DoH 上移检测（延续 10-04 建议，合成文扩散后查询量上升）**  
：非浏览器进程向 223.5.5.5 / 8.8.8.8 发 POST /dns-query  
（Content-Type: application/dns-message）即高危；DNS 出口强制重定向企业内部解析器。  
  
8.**邮件网关 / 终端入口（常态）**  
：拦截大体积安装包（>50MB PE）、伪装「音乐播放器/雷电模拟器/打印机驱动」类附件；计划任务审计关注 SYSTEM 权限每分钟触发 + MicrosoftEdgeUpdate 类伪装名（今日合成文再次印证）。  
  
9.**存量 C2 回撞**  
：Derp 12 个存量 C2 + 8.218.106.149:7000 + 156.251.16.226 纳入 30 天日志回溯；「随机名侧加载 DLL + 任一 C2 连接」同时出现即升级处置（火绒报告建议）。  
## 六、参考来源  
<table><thead><tr><th style="background-color:#2c3e50;color:#fff;padding:10px;text-align:left;font-weight:600;font-size:13px;border:none;"><section><span leaf="">#</span></section></th><th style="background-color:#2c3e50;color:#fff;padding:10px;text-align:left;font-weight:600;font-size:13px;border:none;"><section><span leaf="">标题</span></section></th><th style="background-color:#2c3e50;color:#fff;padding:10px;text-align:left;font-weight:600;font-size:13px;border:none;"><section><span leaf="">链接</span></section></th><th style="background-color:#2c3e50;color:#fff;padding:10px;text-align:left;font-weight:600;font-size:13px;border:none;"><section><span leaf="">日期</span></section></th></tr></thead><tbody><tr><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">1</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">Silver Fox and the Signed-Driver Arsenal（Alpha Cyber Research，今日头条补录）</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">https://alpha-cyber.com/silver-fox-and-the-signed-driver-arsenal</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">2026-09-28</span></section></td></tr><tr style="background-color:#f8f9fa;"><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">2</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">Derp ValleyRAT 7d C2 Tracker（实时抓取）</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">https://www.derp.ca/win_valley_rat</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">2026-10-07 查询</span></section></td></tr><tr><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">3</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">微步在线银狐情报共享站（实时抓取）</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">https://s.threatbook.cn/cybercrime/silverfox</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">2026-10-07 查询</span></section></td></tr><tr style="background-color:#f8f9fa;"><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">4</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">银狐木马新一轮攻击全链拆解：从 DoH 隧道到管控终端滥用（0xfisher，二次传播甄别对象）</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">https://www.cnblogs.com/32bin/p/23200570</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">2026-10-05</span></section></td></tr><tr><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">5</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">层层伪装 暗藏玄机——银狐木马借正规驱动静默接管你的电脑（火绒，合成文主要原始出处之一）</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">https://huorong.cn/document/tech/vir_report/2021</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">2026-07-30</span></section></td></tr><tr style="background-color:#f8f9fa;"><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">6</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">净网：网安局公布 5 起银狐木马案例（旧闻回流甄别）</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">https://c.eihee.com/plus/view-70700-1.html</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">原始 2026-06-16</span></section></td></tr><tr><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">7</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">Silver Fox uses adware to distribute ValleyRAT backdoor（SC Media，旧闻二次传播）</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">https://www.scmagazine.com/brief/silver-fox-uses-adware-to-distribute-valleyrat-backdoor</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">2025-09 原始</span></section></td></tr><tr style="background-color:#f8f9fa;"><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">8</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">Chinese-Backed Silver Fox Plants Backdoors in Healthcare Networks（Infosecurity，旧闻二次传播）</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">https://www.infosecurity-magazine.com/news/chinese-silver-fox-backdoors</span></section></td><td style="padding:8px 10px;border-bottom:1px solid #e8e8e8;vertical-align:top;font-size:13px;line-height:1.6;"><section><span leaf="">2025-03 原始</span></section></td></tr></tbody></table>### 脚注·历史背景（折叠说明）  
  
•**Derp 数据质量规律**  
（本报 9 月起 10 次验证）：滚动窗口最末日计数系统性虚高 7–28 倍、回填延迟约 1 天、最末两日计数不可引用；本期冻结模式与此前的回填修正模式不同，为 9/28 窗口上线以来首次全字段连续 4 天静止。  
  
•**CCTGA 专项背景**  
：2026-08-28 起五批通报 + 9-23 第五批 + 9-28 阶段总结（处置 IP:端口 2,588、域名 8,587、境内被控端 -55%）后转入常态化治理；第六批节奏未知。  
  
•**10/2 三样本**  
：SHA256 3e90a76f…/5c1c6ec4…/5fc62cfd… 为 9/29 以来唯一确认新投放，公开加载链细节不足，持续待核实。  
  
•**金蝉协作链**  
（微步 9-15 收录）：远控破门后的电诈变现路径，IM 行为异常（拉群+禁言+发二维码）为唯一可靠拦截点——处置银狐远控事件时同步延伸电诈风险告知。  
  
  
本日报由自动化脚本生成 · 仅供企业蓝队参考  
  

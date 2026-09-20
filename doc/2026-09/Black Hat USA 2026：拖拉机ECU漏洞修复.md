#  Black Hat USA 2026：拖拉机ECU漏洞修复  
原创 Max Luo
                        Max Luo  白帽子罗棋琛   2026-09-20 00:18  
  
# 一次“噪声召回”如何同时修掉拖拉机 ECU 漏洞  
  
2024 年，北美多家重型卡车 OEM 因 Bendix EC80 制动控制器错误处理拖车 Power Line Carrier（PLC）信号而发起安全召回。公开召回文件描述的是电子噪声或低信号强度可能让 ECU 设置故障、停止运行或异常工作，进而削弱 ABS、ATC、ESP 等安全功能。修复方式是重新刷写固件，移除有缺陷的 PLC 功能。  
  
Black Hat USA 2026 公开课件与配套白皮书《Tractor ECU RE》从另一个角度检查这次更新：如果触发条件不只是随机噪声，而是校验和正确、可被攻击者控制的 J1587/J2497 报文，安全召回是否也在悄悄修复网络安全漏洞？研究者提取三种 EC80 的更新前后固件，重建 S12X 地址空间，做三组 binary diff，再对被删除的 PID handler 做静态分析、台架和低速封闭场地验证。  
  
结果显示，ID9363 更新不只是修一个分支，而是从 J2497 接收路径中删除绝大多数非必要 J1587 PID 处理。被移除代码包含 buffer overflow、unbounded copy、硬编码凭据和越界写。白皮书明确限定：只讨论已被更新修复的功能，不披露未修复 0-day。  
  
本文依据公开 Slides、Whitepaper、NHTSA 与 CISA 文件整理，不以现场参会视角叙述。为避免把已修漏洞重新变成攻击手册，文中不提供无线注入波形、触发帧和可直接执行的 exploit，只保留逆向方法、修复边界和车队落地检查。  
## 1、J2497把拖车电源线变成了数据总线  
  
SAE J2497，也称 PLC4TRUCKS，把通信信号调制在拖车 12V 辅助电源线上，用于满足拖车 ABS warning lamp 等兼容需求。它继承 J1708 的数据链路 framing，并用 J1587 表达应用层参数。一个 frame 最长 21 字节，末尾包含一字节 two's-complement checksum。  
  
![拖车与牵引车总线结构](https://mmbiz.qpic.cn/sz_mmbiz_jpg/4yRoMuNP853mIw6bYiaGZKSJFNdmIouKBuaUrsRvnkibZs6tjicjuYs9TYw2xMYrwraFC4pvUVSR6OH3gLxVa3gJ66qyVwTdcXmNMYWf6y0wHQ/640?from=appmsg "")  
  
图 1：J2497 在牵引车和拖车之间复用电源线，J1939 则承担车内主要控制通信  
  
白皮书列出的必要消息包括 LAMP ON、LAMP OFF 和 Active Trailer ABS Event；其他 MID/PID 可以携带诊断、VIN、车速或厂商私有数据。EC80 的职责是 ABS、ATC 和 ESP，本应把来自拖车侧的 J2497 视为不可信网络。  
  
![J2497与J1939的差异](https://mmbiz.qpic.cn/sz_mmbiz_jpg/4yRoMuNP850yzy9WEqLnL2NMP5iayPtIHQWuZiaiagMqBrd2c3YbNOJvatdTqwh2bhs2GibG0ZbKP2bqM6l81P6tCOibV43FVN5KTY60KdUZL0MI/640?from=appmsg "")  
  
图 2：两条总线速率、帧格式和标准不同，但 ECU 可能在内部把数据桥接到同一控制逻辑  
  
这正是威胁模型的关键：功能安全设计可能把“线路噪声”视为随机 fault，网络安全则必须考虑相邻攻击者能够构造语义正确的消息。  
## 2、无线可达性让随机Fault升级为攻击输入  
  
课件引用既有研究说明，J2497 信号可以在设备相关距离内通过无线方式注入，典型量级约 15 米，特定条件可更远；另一个现实入口是已失陷的 trailer telematics unit。白皮书还引用 CVE-2022-26131 作为无线写入 J2497 的既有证据。  
  
![J2497的无线邻近攻击面](https://mmbiz.qpic.cn/sz_mmbiz_jpg/4yRoMuNP8528hCl5BYJ6smDLbWZtD3u4F91hDZibqW3ib4qq1RdnTrvFb6EjVBoYkqGibYAKSKgs0zo9h70VwenvtM0uszyRH31YVEeB1eqAJA/640?from=appmsg "")  
  
图 3：拖车制动通信不再只存在于封闭线束，邻近无线和联网拖车设备都可能成为入口  
  
从风险建模看，应把输入源分成三类：  
  
yaml  
```
j2497_input_sources:accidental:examples: [line_noise, weak_signal, wiring_fault]     intent:noneadjacent_adversary:examples: [proximal_rf_injection, diagnostic_port_access]     intent:crafted_protocol_inputcompromised_equipment:examples: [trailer_telematics, maintenance_adapter]     intent:authenticated_or_persistent_protocol_input
```  
  
  
如果 parser 对 length、state 或 authentication 的处理有缺陷，后两类攻击者能够稳定重复输入；“随机噪声很难刚好生成有效 checksum”反而证明恶意触发与随机 fault 的概率模型不同。  
## 3、召回固件从哪里来，更新前镜像怎么取  
  
ID9363 是面向车队的 Windows 更新程序。研究团队抓取它通过 J1939 发送的 UDS 流量，从 CAN capture 重建更新后的 PFLASH image；更新前固件无法通过观察到的 UDS session 上传，因此从 ECU 的 BDM 接口转储 PFLASH、DFLASH 和 Emulated EEPROM。  
  
![ID9363更新与固件提取](https://mmbiz.qpic.cn/mmbiz_jpg/4yRoMuNP8506PHC8ME7PJMLAkcAiawAUjfibcviaW3b1O590Jsz3zPR1qOwsRRlXlOAia0iaSbfYNIf0c0yBBdT4s5AuVa5CibHUVhWAekjUwFjzM/640?from=appmsg "")  
  
图 4：更新后镜像可从诊断传输重组，更新前镜像则需要 BDM 读取  
  
三组目标都使用 NXP MC9S12XEQ512，包含 CPU12X 与 XGATE，采用 PPAGE/EPAGE/RPAGE 把 16 位逻辑窗口映射到 23 位全局地址空间。研究发现读出保护未启用；只有右侧 MCU 的 PFLASH 发生代码变化，左侧 MCU 虽未改代码，但 DFLASH/EEE 配置得到更新。  
  
![EC80双MCU与S12X地址空间](https://mmbiz.qpic.cn/mmbiz_jpg/4yRoMuNP850EKWHAHnxjURBwa2KuaNibZ8RZHhQDYtwY3picLdT2Ig6exKo3ohBkZIcayeljKfOicfESaicFeicUPSssHlLQicSUksQkAQZSk0eaM/640?from=appmsg "")  
  
图 5：白皮书展示三个目标 ECU 的 PCB、双 S12X MCU，以及初步 PFLASH 差异分布  
  
逆向前先建立不可变 manifest，防止把不同 OEM、不同 Z-version 或左右 MCU 混在一起：  
  
yaml  
```
firmware_pair:ecu_part_number:REPLACE_MEoem:REPLACE_MEhardware_date_code:REPLACE_MEmcu:right-mc9s12xeq512before:source:bdm-readpflash_sha256:REPLACE_MEdflash_sha256:REPLACE_MEeee_sha256:REPLACE_MEafter:source:id9363-uds-reassemblypflash_sha256:REPLACE_MEdflash_sha256:REPLACE_MEeee_sha256:REPLACE_MEvalidation:updater_transfer_matches_after_pflash:trueaddress_ranges_and_fill_bytes_checked:true
```  
  
## 4、裸字节Diff看不出源码变化  
  
更新前后 PFLASH 有大量 byte difference，但这不等于开发者修改了大量源代码。链接顺序、函数地址变化和 padding 都会放大二进制差异。白皮书还发现更新跳过一段很可能包含 bootloader 的区域，新的 0x3F  
 Software Interrupt padding 增多，暗示函数被删除。  
  
![固件全局字节差异](https://mmbiz.qpic.cn/mmbiz_jpg/4yRoMuNP8506PHC8ME7PJMLAkcAiawAUjfibcviaW3b1O590Jsz3zPR1qOwsRRlXlOAia0iaSbfYNIf0c0yBBdT4s5AuVa5CibHUVhWAekjUwFjzM/640?from=appmsg "")  
  
图 6：大量字节变化跨越两个 Drive Block，必须先恢复函数和地址映射才能解释  
  
研究使用 IDA Pro、BinDiff/QBinDiff、自定义函数识别、jump-table 恢复和 rudimentary concolic analysis。一个很容易踩中的坑是 S12X 运行时内存映射：MMCCTL1 的 RAMHM/ROMHM 会把 0x4000—0x7FFF  
 窗口从 PFLASH 切换到 RAM。如果数据库仍按 flash 建段，会产生大量错误 data write 和伪 cross-reference。  
  
分析流水线可以写成可重建任务，而不是依赖某个分析员手工保存的 IDB：  
  
yaml  
```
reverse_pipeline:-verify_hashes-parse_s19_global_ranges-map_pflash_dflash_eee-create_ppage_epage_rpage_windows-switch_0x4000_window_to_runtime_ram-define_interrupt_vectors-recover_function_prologs-recover_jump_tables_and_indirect_calls-exclude_bootloader_from_application_diff-anchor_interrupt_handlers-run_three_pair_qbindiff-export_deleted_modified_unchanged
```  
  
  
质量门槛不是“工具跑完”，而是 disassembly error 接近零、interrupt handler 对齐、跨三个 OEM 版本得到一致类别，并对低置信匹配单独人工复核。  
## 5、补丁的主要动作是删除非必要PID处理栈  
  
白皮书对三个版本的 QBinDiff 结果显示：更新后没有发现新函数；每组删除约 104—127 个函数，修改 11—13 个，大部分函数保持不变。三组更新共有 87 个相同删除函数。  
  
![三组固件自动差分结果](https://mmbiz.qpic.cn/mmbiz_jpg/4yRoMuNP853XDwxDDRIou6muSVH0TTGLo1ByVFKawaadYgt70AianfMatibVy2HWjWKUrgB4247BYEOicAgBNmwNJNQfCbWBc8diaaX5KvVE2FQ/640?from=appmsg "")  
  
图 7：跨三个目标做差分，能够把版本噪声与共同安全变更分开  
  
手工验证后，删除内容主要包括：全部 J2497 上的 J1587 PID processing（保留 LAMP 和 active ABS event）、SCI2 UART EDGE interrupt handling，以及部分 secondary J1587 diagnostics/transport functions。接收主线程中调用 non-LAMP handler 的分支消失，handler table 和后续函数随之被链接器清理。  
  
![更新前后的接收路径](https://mmbiz.qpic.cn/mmbiz_jpg/4yRoMuNP852MB4jg9E84xpUkyyOawiahfLhkdyvicgYbZeAPlpQsWu5miaKBOVHTEcIWHqU4wnOhUxomGGkkAJib8libnSg83zZU0MVtkDDibGFws/640?from=appmsg "")  
  
图 8：修复没有逐个给危险 handler 打补丁，而是切断非必要 PID 进入控制器的路径  
  
这是非常典型的 YAGNI 安全修复：如果 tractor brake controller 根本不需要从不可信 J2497 处理这些诊断功能，最强的 validation 就是不存在对应代码路径。  
## 6、四类问题必须区分已验证与理论影响  
  
课件与白皮书给出的汇总如下：  
  
text  
```
PID 0xC2   Buffer Overflow     DoS 已验证，RCE 已验证 PID 0xED   Unbounded Copy      DoS 已验证，RCE 理论可达 PID 0xC7   Hardcoded Credential 认证绕过已验证 SCI2 EDGE  OOB Write           DoS/RCE 理论可达 
```  
  
  
![被移除代码中的漏洞](https://mmbiz.qpic.cn/mmbiz_jpg/4yRoMuNP851nENWScJqjw2bc0GGVBFdlShQux5hmYib0REEoDVbcPUu1iarYibBqgLsF68yx9QOg8mKn0aYPn0yaTHEp6mrk8kD8x4JWGYWeXI/640?from=appmsg "")  
  
图 9：只有部分影响经过台架或车辆验证，不能把全部条目都写成已实现 RCE  
  
PID 0xC2 handler 的问题是 attacker-controlled length 参与复制，覆盖相邻状态和数据；研究在台架上确认了 crash，并把受控执行流导向现有发送函数证明 RCE。PID 0xED 的 static buffer copy 缺少边界检查，可覆盖后续 pointer；理论上与另一个 PID handler 组合可形成 write primitive，但完整 RCE 没有在材料中标为已验证。  
  
PID 0xC7 则使用硬编码 password 保护 traction-control configuration。白皮书公开了具体凭据和测试 frame，但防守复盘无需再次分发这些字节；关键结论是设备级敏感状态不能用固件内共享常量认证。  
## 7、Parser安全的第一原则是Allowlist和长度不变量  
  
对 J2497 接收端，最有效的修复不是给每个历史 PID handler 加一个 if  
，而是仅允许业务所需消息进入。CISA ICSA-25-021-03 建议尽可能关闭 J2497 功能，只保留向后兼容所需的 LAMP ON；新 tractor equipment 应移除除 LAMP 外的 J2497 接收支持，并把诊断迁移到现代总线。  
  
产品仍需结合标准、车型和功能安全要求确定最小集合。例如下面的 Rust 结构把 fixed-length、frame length 和 checksum 检查放在 dispatch 之前，并默认拒绝未知 MID：  
  
rust  
```
#[derive(Debug, Clone, Copy, PartialEq, Eq)]enumAllowedMessage {     LampOn,     LampOff,     ActiveTrailerAbsEvent, }  fnchecksum_ok(frame: &[u8]) ->bool {     frame.iter().fold(0u8, |sum, b| sum.wrapping_add(*b)) == 0 }  fnparse_j2497(frame: &[u8]) ->Result<AllowedMessage, &'staticstr> {     if !(2..=21).contains(&frame.len()) {         returnErr("invalid frame length");     }     if !checksum_ok(frame) {         returnErr("invalid checksum");     }     match frame[0] {         0x0Aif frame.len() == 2 => Ok(AllowedMessage::LampOn),         0x0Bif frame.len() == 2 => Ok(AllowedMessage::LampOff),         0x57if frame.len() == 2 => Ok(AllowedMessage::ActiveTrailerAbsEvent),         _ => Err("message not required on tractor receive path"),     } } 
```  
  
  
长度值不能直接驱动 memcpy  
；parser 不能在完成校验前修改全局状态；错误 frame 不应改变 write offset；每条异常路径都必须在 watchdog 允许的时间内返回。  
  
可以用 property test 固定不变量：  
  
rust  
```
proptest! {     #[test]fnarbitrary_frames_never_escape_allowlist(frame in prop::collection::vec(any::<u8>(), 0..64)) {         ifletOk(message) = parse_j2497(&frame) {             prop_assert!(matches!(message,                 AllowedMessage::LampOn |                 AllowedMessage::LampOff |                 AllowedMessage::ActiveTrailerAbsEvent             ));             prop_assert!(frame.len() <= 21);             prop_assert!(checksum_ok(&frame));         }     } } 
```  
  
  
这是面向新实现的示意，不是 EC80 固件补丁源码，也不能替代 SAE 合规与 HIL 测试。  
## 8、台架验证要证明物理后果，但不能把道路变成实验室  
  
研究先在台架发现 crash 和 RCE，再在封闭场地、低速条件下验证 DoS 对车辆的影响。白皮书记录不同 OEM 的症状不同：部分车辆出现 speedometer、steering assist、shifting 和 ABS pulsing 丢失；另一车型主要出现 cluster fault 和 ABS 功能受影响。某些 crash 需要断开电池才能恢复。  
  
![台架中的RCE证据](https://mmbiz.qpic.cn/mmbiz_jpg/4yRoMuNP852J2bZ694muL6esxELATmkaSu2iakiaguqmd2vwBmqPQIBrhHiaiaORzlawJlq3wZxmVkkPR8kljY8DiaUQc9W4B1Dx6JlhdsqBelxQ/640?from=appmsg "")  
  
图 10：逻辑分析仪同时观察 J2497 接收与 ECU 发出的 CAN frame，用于证明控制流影响  
  
![封闭场地测试配置](https://mmbiz.qpic.cn/mmbiz_jpg/4yRoMuNP853GLFngib2qUQKdzYjS7vF3k2y9oXmX7UuzPL4bqH4H0mnmDkOsgSGFa1L7g8qrSibvVnHZs2JEic4sKMnpEEJunheBqKwNicgic25c/640?from=appmsg "")  
  
图 11：白皮书记录了诊断口、隔直电容和仪表侧证据，车辆测试在受控低速环境完成  
  
企业或实验室不要复刻公开 payload。更安全的验证计划应以 patched ECU 的负向行为为目标：非白名单 frame 被丢弃；fuzz 不导致 watchdog reset；LAMP 兼容功能不回归；总线 flood 下 ECU 进入安全降级而非锁死。  
  
yaml  
```
hil_safety_plan:target:patched-production-equivalent-ecuenvironment:vehicle_motion:prohibitedpneumatic_actuators:isolated_or_simulatedcan_gateway:instrumentedemergency_power_disconnect:reachabletests:-valid_lamp_on_off_compatibility-invalid_length_and_checksum_rejection-unknown_mid_pid_drop-rate_limit_and_watchdog_behavior-power_cycle_and_dtc_recoveryevidence:-firmware_hash-bus_capture_hash-power_trace-watchdog_and_reset_reasonforbidden:-vulnerable_firmware_on_public_road-rf_transmission_outside_shielded_lab-operational_vehicle_exploit_payload
```  
  
## 9、车队修复的难点是资产映射和召回闭环  
  
材料估算涉及约 45 万辆卡车，白皮书列出多组 NHTSA/Transport Canada 召回编号。2025 年 10 月，Bendix 又发现约 2,300 个具有相同或相似软件的 ECU 曾作为售后件销售，并追加设备召回。单看车辆出厂年份或车型，可能漏掉后装 ECU。  
  
![召回时间线与扩展](https://mmbiz.qpic.cn/mmbiz_jpg/4yRoMuNP853M0VlMTTuiaQppUbQLGZ7zdQY8OFe41iasHNtKl3sB2pTPbabbpsKWeUH7kBrD7DKrfiaQZHUkE7NibAYSJiaKVD1dnXJ2WzSgNJsk/640?from=appmsg "")  
  
图 12：初始车辆召回之后还有 OEM remedy 与 aftermarket equipment 扩展  
  
车队应以 VIN、ECU part number、software ID、维修记录和当前扫描结果做交叉确认：  
  
sql  
```
SELECT   v.vin,   v.make,   v.model_year,   e.ecu_part_number,   e.software_id,   e.last_scanned_at,   r.campaign_number,   r.remedy_software_id FROM fleet_vehicle v JOIN installed_ecu e ON e.vehicle_id = v.id JOIN recall_applicability r   ON r.ecu_part_number = e.ecu_part_number WHERE e.software_id <> r.remedy_software_id    OR e.last_scanned_at ISNULLOR e.last_scanned_at < r.last_expanded_at; 
```  
  
  
“工单已关闭”不等于“设备已刷写”。维修后读取 software ID，记录 updater/tool ID 和时间戳，再与官方 remedy version 对账；替换过 ECU 的车辆必须重新纳入查询。具体车辆是否受影响，应使用 NHTSA VIN lookup 与 OEM 公告确认，不凭文章中的型号列表决定道路安全。  
## 10、安全补丁应以安全召回的触达能力公开沟通  
  
课件的结论是 ID9363 同时是一项 security patch：它删除未经认证的 memory corruption 和 hardcoded credential 路径，攻击者若具有无线邻近或其他 J2497 access，将无法再触达这些 handler。  
  
![ID9363同时是一项安全补丁](https://mmbiz.qpic.cn/mmbiz_jpg/4yRoMuNP853czI8r51BaP1Kffp54ptOiaoTKMOxVYSd99b103hIJjk4dlfuqXtO5ibQXsvDE8D9erF852Bic8VRhOffOODf2DsIt2QYuhmlNZ0/640?from=appmsg "")  
  
图 13：补丁移除危险 handler，并把接收面收敛到最低兼容功能  
  
研究肯定厂商主动召回与更新，也指出没有单独分配 CVE 可能让用户低估安全紧迫性。安全召回通常比普通固件公告具有更强触达率，但这不意味着需要隐藏安全属性。ISO/IEC 29147 与 CISA 的方向都是：已修漏洞应以足够信息告知用户，让资产所有者能够评估风险和优先级。  
  
![J2497最小接收面的行业建议](https://mmbiz.qpic.cn/mmbiz_jpg/4yRoMuNP852oS66KKbSa57DC7c91AXhVMHLSrNcFkB6KOnnEYZmmcoTLFWVI7KKB5V5zRFrhibvibUw7vDb9RumGUfp03j8QQtn11C1HQlicC4/640?from=appmsg "")  
  
图 14：ATA TMC、CISA 与 SAE 的建议都趋向仅保留必要消息，诊断迁移到更合适的网络  
  
可以把车队与供应商门禁写成：  
  
yaml  
```
commercial_vehicle_ecu_gate:inventory:vin_to_ecu_part_number_complete:trueaftermarket_replacements_included:truesoftware_id_read_from_vehicle:trueremediation:nhtsa_and_oem_campaigns_mapped:trueremedy_firmware_hash_or_id_verified:truepost_flash_dtc_and_brake_test_passed:truearchitecture:j2497_receive_allowlist_documented:truenonessential_diagnostics_removed:truehardcoded_shared_credentials_absent:trueparser_length_properties_fuzzed:trueoperations:trailer_telematics_treated_as_untrusted:truediagnostic_adapter_access_controlled:trueanomalous_j2497_rate_and_mid_logged:truedisclosure:safety_and_security_impact_both_communicated:trueaffected_and_fixed_versions_machine_readable:truecoordinated_disclosure_contact_current:true
```  
  
  
这项研究最值得借鉴的方法，不是从召回公告猜测漏洞，而是把安全工程与逆向证据连起来：以官方 updater 重建 after image，以 BDM 保存 before image，修正微控制器地址映射，跨多个产品版本做函数级 diff，再用受控台架验证。最终得到的也不是“噪声会让 ECU 崩溃”这么简单，而是一个更普遍的结论：只要不可信总线进入 safety-critical ECU，任何超出最低业务需求的 parser 和 diagnostics 都会成为长期负债。  
  
资料：  
- Black Hat 官方 Session 页面  
  
- Black Hat USA 2026 Session  
  
- CISA ICSA-25-021-03：J2497 缓解建议  
  
- NHTSA 24V818 召回与修复说明  
  
- NHTSA 25E073 售后件扩展召回  
  
- NHTSA Recall Lookup  
  
**原始会议材料（仓库内）**  
- 演讲课件 PDF  
  
- 配套白皮书 PDF  
  
开源资料与原始议题 PDF  
  
本文对应的 Markdown 原稿、Black Hat 原始议题 PDF 与配图已整理到 GitHub，可按文章编号查找和下载。  
  
https://github.com/cybermaxluo/black-hat-usa-2026-talks  
  
也可以点击文末“阅读原文”进入仓库。欢迎 Star、提交 Issue 或参与勘误。  
  

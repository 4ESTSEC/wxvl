#  漏洞预警 | Linux kernel释放后使用漏洞  
浅安
                    浅安  浅安安全   2026-09-27 23:50  
  
**0x00 漏洞编号**  
- # CVE-2026-52924  
  
**0x01 危险等级**  
- 高危  
  
**0x02 漏洞概述**  
  
Linux kernel是美国Linux基金会开源的操作系统Linux所使用的内核。  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/NQlfTO30MhxQXbwG1WSjsS1TMvUY3aIsCJ1ic2kxclUiaYic5gV2p9dmfrcTERVZZ5bEOnWFIvWxTRHhnpSrVwYdscfiaPjyuYZZadMxgIIH8Gg/640?wx_fmt=png&from=appmsg&wxfrom=5&wx_lazy=1&tp=webp#imgIndex=0 "")  
  
**0x03 漏洞详情**  
  
**CVE-2026-52924**  
  
**漏洞类型：本地**  
权限提升  
  
**影响：**  
获取root权限  
  
**简述：**  
Linux kernel存在释放后使用漏洞，由于SCTP在处理Stale Cookie时未正确清理输出队列和无效化调度器缓存指针，当关联状态回滚至COOKIE_WAIT时，虽然旧的流表被释放，但指针stream->out_curr未被清除，导致后续调度器操作访问已释放内存，攻击者通过构造特殊SCTP包可远程触发漏洞，进而拿到root权限。  
  
**0x04 影响版本**  
- Linux Kernel   
4.15  
  
**0x05 POC状态**  
- 已公开  
  
**0x06****修复建议**  
  
**目前官方已发布漏洞修复版本，建议用户升级到安全版本****：**  
  
https://www.kernel.org/  
  
  
  

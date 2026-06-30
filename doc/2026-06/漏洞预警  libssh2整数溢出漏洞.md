#  漏洞预警 | libssh2整数溢出漏洞  
浅安
                    浅安  浅安安全   2026-06-30 00:00  
  
**0x00 漏洞编号**  
- # CVE-2026-55200  
  
**0x01 危险等级**  
- 高危  
  
**0x02 漏洞概述**  
  
libssh2是使用最广泛的SSH客户端C库之一，被大量主流工具直接或间接依赖。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/NQlfTO30Mhx62ssooHX63liaZaKd6BY4bNkzy8CmC1bQ0Bst038zjekkZAtR1D1aTVB3C3tNVpPJeyL5AP34YXcjVgsFca7aonfvyfGYJ3Ro/640?wx_fmt=png&from=appmsg "")  
  
**0x03 漏洞详情**  
  
**CVE-2026-55200**  
  
**漏洞类型：**  
整数溢出  
  
**影响：**  
执  
行任意代码  
  
**简述：**  
libssh2存在整数溢出漏洞，由于其ssh2_transport_read()函数未能正确验证传入SSH数据包的packet_length字段，攻击者可提供过大的packet_length值，触发整数溢出并导致堆越界写入，从未获取代码执行权限。  
  
**0x04 影响版本**  
- libssh2 <= 1.11.1  
  
**0x05****POC状态**  
- 已公开  
  
**0x06****修复建议**  
  
**目前官方已发布漏洞修复版本，建议用户升级到安全版本****：**  
  
https://libssh2.org/  
  
  
  

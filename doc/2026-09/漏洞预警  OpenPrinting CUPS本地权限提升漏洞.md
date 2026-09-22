#  漏洞预警 | OpenPrinting CUPS本地权限提升漏洞  
浅安
                    浅安  浅安安全   2026-09-21 23:50  
  
**0x00 漏洞编号**  
- # QVD-2026-70361  
  
**0x01 危险等级**  
- 高危  
  
**0x02 漏洞概述**  
  
OpenPrinting CUPS是一款开源的打印系统，广泛应用于Linux及其他类Unix操作系统中。  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/NQlfTO30Mhwc7ej5Mh3AMKF4Bjz3snCODsia7k5EvxfzQgafhCsicaGXfTZ9MlZObTPXSudJrkjHDn1KbuIkvpdPZID0MfNAaN2z32Tib0oxSg/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=0 "")  
  
**0x03 漏洞详情**  
  
**QVD-2026-70361**  
  
**漏洞类型：**  
本地权限提升  
  
**影响：**  
执行任意代码  
  
**简述：**  
CUPS存在本地权限提升漏洞，具备lpadmin组成员的本地攻击者首先利用以root权限运行的serial后端改写/etc/cups/cups-files.conf，再通过畸形IPP订阅请求使cupsd崩溃并由systemd自动重启，从而以攻击者控制的ServerBin目录重启服务，最终由cupsd以root身份执行被替换的cups-exec，获得交互式root shell。  
  
**0x04 影响版本**  
- CUPS  
  
**0x05****POC状态**  
- 已公开  
  
**0x06****修复建议**  
  
**目前官方已发布漏洞修复版本，建议用户升级到安全版本****：**  
  
https://github.com/OpenPrinting/cups  
  
  
  

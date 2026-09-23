#  漏洞预警 | Arcane服务端请求伪造漏洞  
浅安
                    浅安  浅安安全   2026-09-23 00:00  
  
**0x00 漏洞编号**  
- # CVE-2026-40242  
  
**0x01 危险等级**  
- 高危  
  
**0x02 漏洞概述**  
  
Arcane是一个用于管理Docker容器、镜像、网络和卷的接口。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/NQlfTO30Mhwq1NDdka49w8cfoW6w2zgnsm8Jg2Lkx3REwT0eU757rI9ozRdo6SjvN68KFSF282FXmoywVzkKFSKk0BnrJGyzlMfEQQbQGC4/640?wx_fmt=png&from=appmsg "")  
  
**0x03 漏洞详情**  
  
**CVE-2026-40242**  
  
**漏洞类型：**  
服务  
端  
请求伪造  
  
**影响：**  
获取  
敏感信息  
  
**简述：**  
Arcane的/api/templates/fetch接口  
存在服务端请求伪造漏洞  
，未授权的攻击者可构造恶意请求向非预期目标地址发起HTTP/HTTPS连接，进而导致敏感信息泄露、内部网络探测。  
  
**0x04 影响版本**  
- Arcane < 1.17.3  
  
**0x05****POC状态**  
- 已公开  
  
**0x06****修复建议**  
  
**目前官方已发布漏洞修复版本，建议用户升级到安全版本****：**  
  
https://github.com/getarcaneapp/arcane  
  
  
  

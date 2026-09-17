#  漏洞预警 | JumpServer Access Key越权泄露漏洞  
浅安
                    浅安  浅安安全   2026-09-17 00:00  
  
**0x00 漏洞编号**  
- # QVD-2026-65008  
  
**0x01 危险等级**  
- 高危  
  
**0x02 漏洞概述**  
  
Jumpserver 是一款使用 Python, Django 开发的开源跳板机系统, 为互联网企业提供了认证，授权，审计，自动化运维等功能，基于ssh协议来管理，客户端无需安装agent。  
  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/7stTqD182SXGQIIlHG6aTeP4zxVCPMbbJFicSnyc1CiaVNgicc49tj8zJiajouqJ2HKXojicFa0lEBhYEvNeLfs4CVg/640?wx_fmt=other&from=appmsg&wxfrom=5&wx_lazy=1&wx_co=1&tp=webp#imgIndex=0 "")  
  
**0x03 漏洞详情**  
  
****  
**QVD-2026-65008**  
  
**漏洞类型：**  
Access Key越权泄露  
  
**影响：权限提升**  
  
**简述：**  
JumpServer存在Access Key越权泄露漏洞，由于其在通用API列表视图中使用的过滤后端会对整个查询集条件取反，而不仅仅是客户端提供的过滤表达式，导致已应用于查询集的授权或所有权约束也被一并反转；同时序列化器选择可被客户端查询参数影响，可能暴露列表响应中通常不包含的字段。攻击者可利用该漏洞，通过附加_rel=not查询参数越权读取所有用户的AccessKey Secret和TempToken明文，进而在开启AUTH_TEMP_TOKEN的环境中直接以管理员身份登录，完成权限接管。  
  
**0x04 影响版本**  
- v3.7.0 <= JumpServer V3 < v3.10.23 LTS  
  
- v4.0.0 <= JumpServer V4 < v4.10.19 LTS  
  
**0x05****POC状态**  
- 已公开  
  
**0x06****修复建议**  
  
**目前官方已发布漏洞修复版本，建议用户升级到安全版本****：**  
  
https://www.jumpserver.org/  
  
  
  

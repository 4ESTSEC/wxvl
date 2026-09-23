#  漏洞预警 | Cisco ISE和ISE-PIC身份认证绕过漏洞  
浅安
                    浅安  浅安安全   2026-09-23 00:00  
  
**0x00 漏洞编号**  
- # CVE-2026-76460  
  
**0x01 危险等级**  
- 高危  
  
**0x02 漏洞概述**  
  
Cisco Identity Services Engine是一款身份识别与网络访问控制平台，主要用于对接入网络的用户和终端进行身份认证、授权及访问策略管理，可应用于企业网络中的终端接入控制、身份管理和安全策略实施等场景。  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/7stTqD182SU7yiaA9KHbxNq1iaCtatkAMRdMxh9GQW5qsybAMVCfgV1icAvzV2Y7tKZDV9WTF3l0dMcaKaNR0jllQ/640?wx_fmt=other&from=appmsg&wxfrom=5&wx_lazy=1&wx_co=1&tp=webp#imgIndex=0 "")  
  
**0x03 漏洞详情**  
  
**CVE-2026-76460**  
  
**漏洞类型：**  
身份认证绕过  
  
**影响：**  
执行任意命令  
  
**简述：**  
Cisco ISE和ISE-PIC存在身份认证绕过漏洞，未认证的远程攻击者只需发送一个精心构造的HTTP请求，就能绕过Web管理界面身份认证，并获得root权限的命令执行能力。  
  
**0x04 影响版本**  
- Cisco ISE  
/ISE-PIC  
 < 3.1  
  
- 3.1 <= Cisco ISE  
/ISE-PIC  
 < 3.1 Patch 12  
  
- 3.2 <= Cisco ISE  
/ISE-PIC  
 < 3.2 Patch 11  
  
- 3.3 <= Cisco ISE  
/ISE-PIC  
 < 3.3 Patch 12  
  
- 3.4 <= Cisco ISE  
/ISE-PIC  
 < 3.4 Patch 7  
  
- 3.5 <= Cisco ISE  
/ISE-PIC  
 < 3.5 Patch 4  
  
**0x05****POC状态**  
- 未公开  
  
**0x06****修复建议**  
  
**目前官方已发布漏洞修复版本，建议用户升级到安全版本****：**  
  
https://www.cisco.com/  
  
  
  

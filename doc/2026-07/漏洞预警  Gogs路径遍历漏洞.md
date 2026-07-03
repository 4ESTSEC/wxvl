#  漏洞预警 | Gogs路径遍历漏洞  
浅安
                    浅安  浅安安全   2026-07-03 00:00  
  
**0x00 漏洞编号**  
- # CVE-2026-52813  
  
**0x01 危险等级**  
- 高危  
  
**0x02 漏洞概述**  
  
Gogs是一款开源的自托管Git服务，它提供了简单易用的Web界面，帮助用户轻松管理代码仓库，支持多种认证方式和权限管理，适用于个人开发者及团队协作。  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/7stTqD182SXFFsGjialIUicXBMLa7jZKS5fyVdK7ELoiaib44y7YkRYCAELyIs3faNSeWv7eEPJIqiagDAgQnFVQRicg/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=0 "")  
  
**0x03 漏洞详情**  
  
**CVE-2026-52813**  
  
**漏洞类型：**  
路径遍历  
  
**影响：**  
执行任意命令  
  
**简述：**  
Gogs存在路径遍历漏洞，在internal/database/org.go等相关代码中，由于系统未对组织名称中的路径遍历序列进行严格校验，导致仓库可被创建到预期目录之外。攻击者可构造恶意组织名称，将嵌套Git仓库写入其他仓库的本地工作目录，篡改hooks/update等Git Hooks脚本并触发执行，从而以Gogs服务进程身份执行任意命令。  
  
**0x04 影响版本**  
- Gogs < 0.14.3  
  
**0x05****POC状态**  
- 已公开  
  
**0x06****修复建议**  
  
**目前官方已发布漏洞修复版本，建议用户升级到安全版本****：**  
  
https://gogs.io/  
  
  
  

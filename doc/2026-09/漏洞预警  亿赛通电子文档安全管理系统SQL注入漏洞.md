#  漏洞预警 | 亿赛通电子文档安全管理系统SQL注入漏洞  
浅安
                    浅安  浅安安全   2026-08-31 23:50  
  
**0x00 漏洞编号**  
- # 暂无  
  
**0x01 危险等级**  
- 高危  
  
**0x02 漏洞概述**  
  
亿赛通电子文档安全管理系统以数据资产防泄密为核心，涵盖文档安全、终端安全、防勒索、安全态势、集团管控五大板块，实现对用户电脑终端、移动办公、各类应用系统上的数据从生产、存储、流程、外发到销毁进行全生命周期保护。  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/7stTqD182SVvibFbLxuoIiaeezXhN5VdbvaBws9RFvV0bibSaExGKk1WZCbXOEibiaJLYNod1O0UTwA9xzvWBhTRxIQ/640?wx_fmt=other&wxfrom=5&wx_lazy=1&wx_co=1&randomid=psbesmp4&tp=webp#imgIndex=0 "")  
  
**0x03 漏洞详情**  
  
**漏洞类型：**  
SQL注入  
  
**影响：**  
获取敏感信息  
  
**简述：**  
亿赛通电子文档安全管理系统的  
/CDGServer3/client/notouchapprove.jsp;Servicelogin  
接口存在SQL注入漏洞，未授权的攻击者可以通过该漏洞执行SQL语句，获取数据库敏感信息。  
  
**0x04 影响版本**  
- 亿赛通电子文档安全管理系统  
  
**0x05****POC状态**  
- 已公开  
  
**0x06****修复建议**  
  
**目前官方已发布漏洞修复版本，建议用户升级到安全版本****：**  
  
https://www.esafenet.com/  
  
  
  

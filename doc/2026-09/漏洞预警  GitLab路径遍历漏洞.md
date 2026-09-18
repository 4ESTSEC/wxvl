#  漏洞预警 | GitLab路径遍历漏洞  
浅安
                    浅安  浅安安全   2026-09-17 23:50  
  
**0x00 漏洞编号**  
- CVE-202  
6-85706  
  
**0x01 危险等级**  
- 高危  
  
**0x02 漏洞概述**  
  
GitLab是一个用于仓库管理系统的开源项目，其使用Git作为代码管理工具，可通过Web界面访问公开或私人项目。  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/7stTqD182SWurkicwgzOT4LeOPBpry1N5ugc3t7jF2S3qXGNeicXtdSxC1YB5a1Gnrniar8VV7TVtDoH5D9TYSw2g/640?wx_fmt=png&from=appmsg&wxfrom=5&wx_lazy=1&tp=webp#imgIndex=0 "")  
  
**0x03 漏洞详情**  
  
**CVE-2026-85706**  
  
**漏洞类型：**  
路径遍历  
  
**影响：**  
获取敏感信息  
  
**简述：**  
GitLab存在路径遍历漏洞，由于该接口存在路径限制不当以及身份认证执行缺失问题，在特定条件下，未经身份认证的远程攻击者可利用该漏洞读取GitLab服务器上的任意文件，进而造成服务器敏感文件及相关数据泄露，对系统数据机密性造成严重影响。  
  
**0x04 影响版本**  
- 18.7 <= GitLab CE/EE < 19.1.8  
  
- 19.2 <= GitLab CE/EE < 19.2.6  
  
- 19.3 <= GitLab CE/EE < 19.3.2  
  
**0x05****POC状态**  
- **未公开**  
  
**0x06****修复建议**  
  
******目前官方已发布漏洞修复版本，建议用户升级到安全版本****：******  
  
https://about.gitlab.com/  
  
  
  

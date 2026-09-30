#  漏洞预警 | WordPress编码绕过漏洞  
浅安
                    浅安  浅安安全   2026-09-30 00:00  
  
**0x00 漏洞编号**  
- # CVE-2026-87902  
  
**0x01 危险等级**  
- 高危  
  
**0x02 漏洞概述**  
  
WordPress是一款使用PHP语言开发的开源内容管理系统，提供了丰富的主题和插件生态系统，支持博客、企业官网、电商平台等多种场景。  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/NQlfTO30MhydhWlf0jP0jzUhEwjF9E0zjT95dGhSI6VpNExsglt6bzHiaqA99LjAQDRMqQ6tVxLP4aWHm7DcoibfzLW2VkkgqsTyS9HQPrpzQ/640?wx_fmt=png&from=appmsg&wxfrom=5&wx_lazy=1&tp=webp#imgIndex=0 "")  
  
**0x03 漏洞详情**  
  
**CVE-2026-87902**  
  
**漏洞类型：**  
编码绕过  
  
**影响：**  
加载任意PHP文件  
  
**简述：**  
WordPress存在编码绕过漏洞，未经验证的攻击者配合双重URL编码即可让get_page_template()加载任意PHP文件，在register_argc_argv=On且pearcmd.php可读的环境下还能升级为RCE。  
  
**0x04 影响版本**  
- 4.7.0 <= WordPress <= 7.1.1  
  
**0x05****POC状态**  
- 已公开  
  
**0x06****修复建议**  
  
**目前官方已发布漏洞修复版本，建议用户升级到安全版本****：**  
  
https://cn.wordpress.org/  
  
  
  

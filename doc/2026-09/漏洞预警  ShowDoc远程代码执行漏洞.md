#  漏洞预警 | ShowDoc远程代码执行漏洞  
浅安
                    浅安  浅安安全   2026-09-27 23:50  
  
**0x00 漏洞编号**  
- # QVD-2026-61708  
  
**0x01 危险等级**  
- 高危  
  
**0x02 漏洞概述**  
  
ShowDoc是基于thinkPHP开发的开源文档管理系统，支持使用Markdown语法书写API文档、数据字典、在线Excel文档等功能。  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/7stTqD182SXSjhhHib4Jz0yt06S7YHPTFibqzlrDoSLEUsdqYYXMeLfgic4rmxPobTq6jB1rd60icFMicu6WdVWGhPg/640?wx_fmt=png&from=appmsg&wxfrom=5&wx_lazy=1&tp=webp#imgIndex=0 "")  
  
**0x03 漏洞详情**  
  
**QVD-2026-61708**  
  
**漏洞类型：**  
SQL注入  
  
**影响：**  
获取敏感信息  
  
**简述：**  
ShowDoc存在远程代码执行漏洞，由于其注册接口registerByVerify对username参数仅做trim()处理、缺少字符白名单校验，恶意用户名可被原样存入数据库；而默认部署将SQLite数据库文件放置在Web根目录下，当攻击者直接请求该数据库.php文件时，PHP-FPM会将其作为PHP脚本解析，执行username中注入的代码，从而实现未授权远程代码执行。  
  
**0x04 影响版本**  
- ShowDoc <= v3.9.2  
  
**0x05 POC状态**  
- 未公开  
  
**0x06****修复建议**  
  
**目前官方已发布漏洞修复版本，建议用户升级到安全版本****：**  
  
https://www.showdoc.com.cn/  
  
  
  

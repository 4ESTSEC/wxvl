#  史诗级安全军火库！百度OpenRASP漏洞测试用例库让Web安全检测能力产生质的飞跃  
棉花糖糖糖
                    棉花糖糖糖  棉花糖网络安全工具箱   2026-10-07 02:45  
  
免责声明：本文仅供技术研究使用，任何利用本文所提技术进行未授权测试的行为由使用者自行承担后果。
重点导读概述
OpenRASP TestCases 是百度安全团队开源的 RASP（运行时应用自保护）技术专项测试用例库。该项目汇集了覆盖 Java 与 PHP 两大主流语言的上百种真实漏洞场景，为安全研究人员和开发团队提供了标准化的漏洞复现与检测验证环境。
重点导读项目结构
PART 01Java 测试用例
Java 分支涵盖以下漏洞类型：

Struts 系列：S2-001、S2-007、S2-008、S2-012、S2-013、S2-015、S2-016、S2-029、S2-032
反序列化漏洞：Fastjson 全版本、Jackson-databind、CVE-2019-12384、CVE-2019-10173
SQL 注入：MySQL、Oracle、PostgreSQL、SQLite、MSSQL 多数据库支持
模板注入：FreeMarker、Velocity、Thymeleaf、SpEL、MVEL、QLExpress
命令执行：ScriptEngineManager、OGNL、EL、groovy
其他漏洞：Log4j JNDI 注入、MyBatis 注入、文件上传、XStream 反序列化、XMLDecoder、SnakeYAML

PART 02PHP 测试用例
PHP 分支覆盖以下漏洞场景：

文件操作：目录遍历、任意文件读取、任意文件写入、文件删除
命令执行：回显与非回显两种模式
WebShell：回调型、eval 型、dropper 型、文件包含型
其他漏洞：SSRF（curl/file）、SQL 注入（mysqli）、XSS、文件包含

重点导读漏洞复现脚本
项目提供独立的漏洞利用脚本，位于 tools/ 目录：

S2-001.py、S2-007.py、S2-008.py、S2-012.py、S2-013.py、S2-015.py、S2-016.py、S2-029.py、S2-032.py、S2-045.py

重点导读构建与部署
项目提供自动化构建脚本 build.sh，执行以下操作：

Java 部分：通过 Maven 编译生成 WAR 包，输出至 output/ 目录
PHP 部分：打包为 php-vulns.tar.gz 归档文件

重点导读技术架构
Java 测试用例采用 Maven 多模块结构，每个漏洞类型独立子项目。PHP 测试用例采用单目录组织，文件命名规范统一。整体架构简洁，便于扩展新的漏洞类型。
重点导读应用场景

RASP 引擎检测能力验证
Web 安全产品测评基准
安全开发测试环境搭建
渗透测试技术研究

重点导读项目地址
本文介绍的项目开源地址如下：
https://github.com/baidu-security/openrasp-testcases

本公众号非项目作者，仅做技术分享。
## 广告时间  


  
    低价考证包括但不限于CISP系列、PMP等等国内网安证书、网络安全交流群请关注公众号后点菜单栏的找棉花糖。
  
  
    糖心会员站，网络安全必备网站，包括在线内网靶场、web靶场、src靶场、应急响应靶场，以及各种网安资料、教程、方案模版、以及超级多在线工具，99元包年！详细介绍：棉花糖会员站介绍(26年4月26日版本) ：在线内网靶场、网安资料方案、在线工具全能资源站，看完介绍百分百心动！
  
  
    
  
  
    
  

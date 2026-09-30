#  炸裂！这款开源Web漏洞扫描器让安全检测效率提升百倍  
棉花糖糖糖
                    棉花糖糖糖  棉花糖网络安全工具箱   2026-09-30 02:52  
  
免责声明：本文介绍的工具仅供安全教育与合法授权测试使用，使用前请遵守当地法律法规。
W13Scan是一款基于Python3开发的开源Web漏洞扫描器，支持主动扫描与被动扫描两种模式，可运行在Windows、Linux、macOS平台。该工具采用模块化架构设计，内置丰富的漏洞检测插件，覆盖XSS、SQL注入、命令注入、路径穿越、敏感信息泄露等常见Web安全风险。
W13Scan Logo
重点导读主要特性
PART 01扫描模式

主动扫描：指定目标URL或批量导入URL列表，工具自动进行参数分析与漏洞检测
被动扫描：启动本地代理服务，拦截并分析HTTP流量，发现潜在安全问题
联动扫描：支持与crawlergo等动态爬虫工具配合，实现全链路自动化扫描

PART 02跨平台支持
Python3.6及以上运行环境即可工作，不受操作系统限制。
重点导读核心架构
PART 03模块分工



模块
职责




lib/controller
任务调度与线程管理


lib/parse
HTTP请求/响应解析


lib/proxy
异步MITM代理服务


lib/reverse
反连平台通信


scanners
漏洞检测插件库


fingprints
Web技术指纹库



PART 04插件分类
扫描插件按作用范围分为三类：

PerFile：针对单个URL及其参数进行检测
PerFolder：针对目录层级进行检测
PerServer：针对域名整体进行检测

PART 05并发处理
采用多线程并发模型，默认31线程，支持自定义线程数。任务队列统一管理，实时输出扫描进度。
重点导读检测能力
PART 06PerFile 插件



插件
检测类型




xss
反射型XSS、存储型XSS


sqli_bool
布尔型SQL注入


sqli_error
报错型SQL注入


sqli_time
时间型SQL注入


command_system
系统命令注入


command_php_code
PHP代码执行


command_asp_code
ASP代码执行


directory_traversal
路径穿越漏洞


backup_file
备份文件泄露


jsonp
JSONP信息泄露


js_sensitive_content
JavaScript敏感信息


php_real_path
PHP真实路径泄露


poc_fastjson
Fastjson反序列化


shiro
Shiro反序列化


struts2_032
Struts2 S2-032


struts2_045
Struts2 S2-045


ssti
服务端模板注入


unauth
未授权访问


webpack
Webpack源码泄露


analyze_parameter
参数分析



PART 07PerFolder 插件



插件
检测类型




backup_folder
备份目录检测


directory_browse
目录浏览漏洞


phpinfo_craw
PHPinfo文件泄露


repository_leak
仓库文件泄露



PART 08PerServer 插件



插件
检测类型




backup_domain
备份域名检测


errorpage
错误页面信息


http_smuggling
HTTP走私攻击


iis_parse
IIS解析漏洞


net_xss
.Net XSS检测


swf_files
SWF文件检测


idea
IDEA配置泄露



PART 09指纹识别
内置Web技术指纹识别能力，支持识别以下类型：

框架指纹
操作系统指纹
编程语言指纹
Web服务器指纹

重点导读反连平台
支持配置反连平台用于检测无回显漏洞：

HTTP反连
DNS反连
RMI反连

适用于检测命令注入、SQL注入时间盲注等场景。
重点导读输出格式
扫描结果支持多种输出方式：

JSON格式：完整原始数据
HTML报告：可视化报告页面

重点导读安装使用
PART 10环境要求
Python 3.6+
PART 11安装步骤
bashgit clone https://github.com/w-digital-scanner/w13scan.git
cd w13scan
pip3 install -r requirements.txt
cd W13SCAN
python3 w13scan.py -h

PART 12被动扫描模式
bashpython3 w13scan.py -s 127.0.0.1:7778 --html

PART 13主动扫描模式
bashpython3 w13scan.py -u http://target.com

批量扫描：
bashpython3 w13scan.py -f urls.txt --html

重点导读扩展集成
W13Scan提供插件开发接口，安全研究人员可基于现有架构扩展自定义检测模块。开发文档位于doc/dev.md。
插件开发要点：

继承插件基类
使用FakeReq获取请求信息
使用FakeResp获取响应信息
返回标准JSON格式结果

本公众号非项目作者，仅做技术分享。
本文介绍的项目开源地址如下：
bashhttps://github.com/w-digital-scanner/w13scan

## 广告时间  


  
    低价考证包括但不限于CISP系列、PMP等等国内网安证书、网络安全交流群请关注公众号后点菜单栏的找棉花糖。
  
  
    糖心会员站，网络安全必备网站，包括在线内网靶场、web靶场、src靶场、应急响应靶场，以及各种网安资料、教程、方案模版、以及超级多在线工具，99元包年！详细介绍：棉花糖会员站介绍(26年4月26日版本) ：在线内网靶场、网安资料方案、在线工具全能资源站，看完介绍百分百心动！
  
  
    
  
  
    
  

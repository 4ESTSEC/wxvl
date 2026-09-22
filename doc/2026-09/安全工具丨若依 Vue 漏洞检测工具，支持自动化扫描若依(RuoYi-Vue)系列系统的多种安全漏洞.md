#  安全工具丨若依 Vue 漏洞检测工具，支持自动化扫描若依(RuoYi-Vue)系列系统的多种安全漏洞  
原创 蓝星安全
                    蓝星安全  蓝星安全   2026-09-21 22:00  
  
         
**点击上方****蓝星安全****关注我**  
  
****  
  
**免责声明：本公众号分享的任何资料仅限用于安全学习，严禁用于其他用途，请严格遵守中华人民共和国法律法规，对因不遵守国家法律法规而产生的任何后果，均由个人自行承担，本公众号不承担任何责任！**  
  
****  
**获取资料，请扫码下方二维码加入知识星球**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/opKHaHXLxcmxg12rU33pwcICNRkiaof5YSUAGfWPApU7M1BfdsTTOyvREw0gKD42g4U8eefG4n0XuYETEtbxSIN2drACnNVz3UeicCcldM5AA/640?wx_fmt=png&from=appmsg "")  
## 一、简介  
  
   若依（RuoYi-Vue）作为国内使用最广泛的开源后台管理框架之一，凭借其前后端分离架构和丰富的功能模块，被大量企业和开发者用于快速搭建管理系统。然而，正因其普及度高，若依系统面临的安全风险也日益突出——Swagger文档泄露、Druid监控未授权访问、路径穿越文件读取、SQL注入等漏洞频频出现在安全通告中  
。  
  
**ruoyi-vue-scanner**  
 正是为应对这一痛点而生的专项安全检测工具。该项目由 AakiTT 开源，专为若依系列后台系统打造，支持自动化扫描多种常见安全漏洞，同时具备接口爬取和敏感信息搜集能力  
。  
  
与通用型漏洞扫描器不同，ruoyi-vue-scanner 深度贴合若依框架的架构特征，针对其特有的接口路径、参数命名和业务逻辑进行精准检测，显著降低了误报率。工具基于 Java 11 运行，界面简洁直观，只需输入目标 URL 即可开启一键扫描  
。无论是安全工程师进行授权渗透测试，还是运维人员在自查中排查隐患，都能从中获得高效的检测体验。  
> ⚠️ **免责声明**  
：本工具仅供技术学习与合法授权安全评估使用，严禁用于任何非法用途。使用前请遵守《中华人民共和国网络安全法》等相关法律法规  
。  
  
## 二、核心功能  
  
ruoyi-vue-scanner 的功能体系分为 **漏洞检测模块**  
 和 **辅助功能模块**  
 两大板块，覆盖了从漏洞发现到信息搜集的完整安全评估流程。  
### 2.1 漏洞检测模块  
  
**Swagger 文档泄露检测**  
：自动探测 /swagger-ui/index.html  
、/v2/api-docs  
、/v3/api-docs  
 等常见接口路径，快速识别是否存在 Swagger 文档未授权访问。一旦泄露，攻击者可获取完整的 API 接口清单和参数结构，为后续攻击提供精准导航  
。  
  
**Druid 监控未授权与弱口令检测**  
：Druid 是若依默认集成的数据库连接池组件，其监控页面若未做鉴权，将暴露数据库连接信息。该工具不仅检测未授权访问，还内置了常见弱口令字典进行爆破尝试（如 admin/123456、druid/druid 等），全面排查 Druid 安全风险  
。  
  
**路径穿越文件读取检测**  
：针对 /common/download/resource  
 等接口，检测是否存在路径穿越漏洞。攻击者可能利用该漏洞读取 /etc/passwd  
、windows/win.ini  
 等系统敏感文件，工具会自动验证并报告可读取的文件路径  
。  
  
**dataScope 参数 SQL 注入检测**  
：若依系统的 dataScope 参数用于数据权限控制，若未做充分过滤，可能成为 SQL 注入的入口。该工具针对这一若依特有参数进行精准的注入检测  
。  
  
**定时任务任意文件读取检测**  
：检测 /monitor/job  
 定时任务接口是否存在任意文件读取漏洞，该漏洞可被利用读取服务器上的任意文件内容  
。  
  
**任意密码修改检测**  
：检测若依系统密码重置接口是否存在逻辑漏洞，可能导致攻击者无需验证即可修改任意用户密码  
。  
  
**系统接口越权测试**  
：自动测试 /system/user  
、/system/role  
、/system/config  
 等管理接口是否存在未授权访问，帮助发现权限控制缺失问题  
。  
  
**全面检测**  
：一键执行以上所有检测项目，适合快速完成整体安全评估。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/opKHaHXLxcnY4z96UTRGUbRKT1ibd8KM9Iw9Od4f7pu0S9FF3SZdVP0Xej0tTOr8l5ftA7iajDnZa5zDxkxbmBtvSMgZotLK3LvW08ZPHIx0w/640?wx_fmt=png&from=appmsg "")  
  
### 2.2 辅助功能模块  
  
**JS 接口提取**  
：多线程爬取目标页面引用的所有 JavaScript 文件，通过正则匹配提取其中的 API 路径，支持 /prod-api  
、/dev-api  
、/monitor  
 等若依常见前缀，帮助安全人员发现隐藏的接口端点  
。  
  
**接口批量测试**  
：对收集到的接口进行批量请求测试，支持 GET、POST、POST-JSON 三种请求方式，并可自定义请求体，便于灵活验证接口行为  
。  
  
**敏感信息搜集**  
：基于已抓取的 JS 文件内容，自动检测密码明文、API 密钥、JWT 令牌、数据库连接字符串、Redis URL、云存储地址、GitHub 令牌、内网 IP、邮箱、手机号等敏感信息泄露  
。  
  
**自动提取 Basedir**  
：输入完整 URL 时，工具自动识别并填充后端 API 基础路径（如 /prod-api  
），省去手动配置的繁琐步骤  
。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/opKHaHXLxckquqHUuYRicoxibvBYzvId9HRajTMuaKjNsXTmpeSRzdfgjEayzfJZ7GI44TdojFs4upRc6tbanTjZkjo0PpDGFfqVPXDHfHiaLg/640?wx_fmt=png&from=appmsg "")  
### 2.3 技术亮点  
  
ruoyi-vue-scanner 在工程实现上也做了多项优化：采用 **10 线程池**  
并行请求，大幅提升扫描效率；支持 **HTTP 代理**  
配置，方便搭配 Burp Suite 等工具进行流量分析；内置 **SSL 忽略**  
策略，可扫描自签名 HTTPS 站点；支持自定义 **Cookie/Authorization**  
 认证头，能够测试登录后的接口安全；彩色日志输出让关键漏洞一目了然，进度条和状态栏实时展示扫描进度  
。  
  
三、立即获取  
  
https://github.com/AakiTT/ruoyi-vue-scanner  
  

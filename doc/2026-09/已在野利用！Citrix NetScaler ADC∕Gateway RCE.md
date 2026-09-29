#  已在野利用！Citrix NetScaler ADC/Gateway RCE  
原创 微步情报局
                    微步情报局  微步在线研究响应中心   2026-09-29 03:35  
  
漏洞概况  
  
  
Citrix NetScaler ADC 与 NetScaler Gateway 是 Cloud Software Group（Citrix）的应用交付与远程接入设备，分别用于负载均衡、应用代理与 VPN/AAA 接入。  
  
近日，  
Citrix   
官方发布通告，修复了 NetScaler ADC/Gateway 未认证命令注入漏洞（CVE-2026-88771）。  
在默认配置下，未认证攻击者可通过登录接口把任意文本写入设备日志，设备内置的 pitboss 崩溃监控脚本解析日志时未做校验，将日志内容拼进反引号 shell 命令执行，最终以 root 身份执行任意命令。  
（完整漏洞情报请查阅https://x.threatbook.com/v5/vul/XVE-2026-70357）  
  
该漏洞已发现在野利用，  
建议受影响用户  
尽快修复。  
  
漏洞处置优先级(VPT)  
  
  
**综合处置优先级：**  
高风险  
<table><tbody><tr><td rowspan="3" style="border: 1px solid rgb(221, 221, 221);padding: 12px;text-align: left;vertical-align: top;font-weight: bold;background-color: rgb(248, 249, 250);"><section><span leaf="">基本信息</span></section></td><td style="border: 1px solid rgb(221, 221, 221);padding: 12px;text-align: left;vertical-align: top;"><section><span leaf="">微步编号</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">XVE-2026-70357</span></section></td></tr><tr><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">CVE编号</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">CVE-2026-88771</span></section></td></tr><tr><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">漏洞类型</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">命令注入</span></section></td></tr><tr><td rowspan="5" style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;font-weight:bold;background-color:#f8f9fa;"><section><span leaf="">利用条件评估</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">利用漏洞的网络条件</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">远程</span></section></td></tr><tr><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">是否需要绕过安全机制</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">否</span></section></td></tr><tr><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">对被攻击系统的要求</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">默认配置</span></section></td></tr><tr><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">利用漏洞的权限要求</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">无须用户权限</span></section></td></tr><tr><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">是否需要受害者配合</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">否</span></section></td></tr><tr><td rowspan="2" style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;font-weight:bold;background-color:#f8f9fa;"><section><span leaf="">利用情报</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">是否有POC</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><span style="color:#d93025;font-weight:bold;"><span leaf="">是</span></span></td></tr><tr><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">已知利用行为</span></section></td><td><section><span leaf="" style="color: rgb(217, 48, 37);font-weight: bold;">已发现在野利用</span></section></td></tr></tbody></table>  
漏洞影响范围  
  
<table><tbody><tr><td style="border: 1px solid rgb(221, 221, 221);padding: 12px;text-align: left;vertical-align: top;font-weight: bold;background-color: rgb(248, 249, 250);"><section><span leaf="">产品名称</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">NetScaler ADC 与 NetScaler Gateway</span></section></td></tr><tr><td style="border: 1px solid rgb(221, 221, 221);padding: 12px;text-align: left;vertical-align: top;font-weight: bold;background-color: rgb(248, 249, 250);"><section><span leaf="">受影响版本</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">13.1&lt;=version&lt;13.1-64.23、13.1&lt;=version&lt;13.1.37.279（FIPS）、13.1&lt;=version&lt;13.1.37.279（NDcPP）、14.1&lt;=version&lt;14.1-73.37、14.1-66.68&lt;=version&lt;=14.1-73.37（FIPS）</span></section></td></tr><tr><td style="border: 1px solid rgb(221, 221, 221);padding: 12px;text-align: left;vertical-align: top;font-weight: bold;background-color: rgb(248, 249, 250);"><section><span leaf="">有无修复补丁</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">有</span></section></td></tr></tbody></table>  
漏洞复现  
  
### 1. 执行poc  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/T4OSm0sXdEMbhGxRLNDg0CVnQtAKZmQ9iaMicBycc8ETjUENtE6FGtT6qZjqcYWNcicOJibNTG0JdQ5icfJalMsS8QQSmrHjrVjpYnDBFpQhKOO0/640?wx_fmt=png&from=appmsg "")  
  
2.   
检查命令执行情况  
  
![](https://mmbiz.qpic.cn/mmbiz_png/T4OSm0sXdEPriblvJ0wicQ1bXEfjUgOaQN6hP9zFFFicfoA51iaRib6qVQPl3zrjA8DZgdhktPNm9DZGq1vUWokDNzng5qrckBZd0W6vUZ0OdicLU/640?wx_fmt=png&from=appmsg "")  
  
  
修复方案  
  
### 官方修复方案  
  
官方已发布修复方案，请访问链接下载：  
  
https://support.citrix.com/support-home/kbsearch/article?articleNumber=CTX697096  
### 临时缓解措施  
  
1、只允许受信网络访问登录页NSIP 管理面；  
  
2.   
使用防护设备拦截以下特征：如登录参数中出现 pitboss、unexpectedly died、;/反引号等组合  
  
微步产品支撑  
  
  
微步漏洞情报于  
2026-09-27  
收录该漏洞。  
  
微步下一代威胁情报平台NGTIP及X情报中心已于漏洞收录时向漏洞订阅用户推送该漏洞情报，并将持续推送后续更新；对于已经录入资产的用户，支持实时自动化排查受影响资产。  
  
微步威胁感知平台TDP已  
于  
   
2026-09-29   
支持检测，检测ID：  
S  
3100184926、S3100184927  
，模型/规则高于：  
  
20260929000000  
  
![](https://mmbiz.qpic.cn/mmbiz_png/T4OSm0sXdEOE6NcAeraFosPkbjh9doQpYq3dnEOUL73nIWGYEdYBbEXqGF8Us22xl1LicZ2o3oFWf8jhicIYHNEwLEBVEIfzdyiatu95Y6bWMQ/640?wx_fmt=png&from=appmsg "")  
  
  
微步威胁防御系统OneSIG已支持防护规则ID为：  
3100184926、3100184927  
  
  
  

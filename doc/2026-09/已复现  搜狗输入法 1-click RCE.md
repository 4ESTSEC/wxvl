#  已复现 | 搜狗输入法 1-click RCE  
原创 微步情报局
                    微步情报局  微步在线研究响应中心   2026-09-14 09:04  
  
漏洞概况  
  
  
搜狗输入法（搜狗拼音输入法）是北京搜狗科技有限公司开发的中文输入法（IME）软件，负责将用户输入的拼音、五笔、语音或手写内容转换为汉字候选，并把选中的文字送入微信、浏览器、Word 等应用。  
  
近日，微步情报局监测到互联网披露了搜狗输入法远程代码执行漏洞（CVE-2026-51990）。  
微步情报局已成功复现该漏洞  
。经分析，Windows 版搜狗输入法存在由自定义协议处理缺陷触发的远程代码执行链，远程攻击者可构造恶意 sgbiz: 链接注入启动参数，迫使内置浏览器访问恶意网页，进而执行任意代码。（完整漏洞情报请查阅https://x.threatbook.com/v5/vul/XVE-2026-62927）  
  
攻击者仅需诱导受害者点击一次恶意链接，即可执行任意代码；该攻击已被 UNC3569 用于投放 GRAYRABBIT 后门。  
建议受影响用户  
尽快修复。  
  
漏洞处置优先级(VPT)  
  
  
**综合处置优先级：**  
高风险  
<table><tbody><tr><td rowspan="3" style="border: 1px solid rgb(221, 221, 221);padding: 12px;text-align: left;vertical-align: top;font-weight: bold;background-color: rgb(248, 249, 250);"><section><span leaf="">基本信息</span></section></td><td style="border: 1px solid rgb(221, 221, 221);padding: 12px;text-align: left;vertical-align: top;"><section><span leaf="">微步编号</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">XVE-2026-62927</span></section></td></tr><tr><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">CVE编号</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">CVE-2026-51990</span></section></td></tr><tr><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">漏洞类型</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">远程代码执行</span></section></td></tr><tr><td rowspan="5" style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;font-weight:bold;background-color:#f8f9fa;"><section><span leaf="">利用条件评估</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">利用漏洞的网络条件</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">远程</span></section></td></tr><tr><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">是否需要绕过安全机制</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">否</span></section></td></tr><tr><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">对被攻击系统的要求</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">Windows 操作系统</span></section></td></tr><tr><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">利用漏洞的权限要求</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">无须用户权限</span></section></td></tr><tr><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">是否需要受害者配合</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">是</span></section></td></tr><tr><td rowspan="2" style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;font-weight:bold;background-color:#f8f9fa;"><section><span leaf="">利用情报</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">是否有POC</span></section></td><td style="font-size: 15px;font-family: -apple-system, BlinkMacFont, &#34;Segoe UI&#34;, &#34;PingFang SC&#34;, sans-serif;color: rgb(217, 48, 37);font-weight: bold;"><section><span leaf="" style="color: rgb(217, 48, 37);font-weight: bold;">是</span></section></td></tr><tr><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">已知利用行为</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">是，已被 UNC3569 用于投放 GRAYRABBIT 后门</span></section></td></tr></tbody></table>  
漏洞影响范围  
  
<table><tbody><tr><td style="border: 1px solid rgb(221, 221, 221);padding: 12px;text-align: left;vertical-align: top;font-weight: bold;background-color: rgb(248, 249, 250);"><section><span leaf="">产品名称</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">搜狗拼音输入法</span></section></td></tr><tr><td style="border: 1px solid rgb(221, 221, 221);padding: 12px;text-align: left;vertical-align: top;font-weight: bold;background-color: rgb(248, 249, 250);"><section><span leaf="">受影响版本</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">version&lt;16.3.0.3498</span></section></td></tr><tr><td style="border: 1px solid rgb(221, 221, 221);padding: 12px;text-align: left;vertical-align: top;font-weight: bold;background-color: rgb(248, 249, 250);"><section><span leaf="">有无修复补丁</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">有</span></section></td></tr></tbody></table>  
漏洞复现  
  
  
点击特殊构造的sgbiz: 链接，成功在搜狗内嵌chromium中触发v8nday漏洞，弹出计算器，实现任意代码执行。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/T4OSm0sXdEOQjVx8icsyNxswWkQNwOp12cyBqFEdOda8w0pH5BKYXBam5oFAbfJgjiaShwEYhmLunjtusHh9W2B44SCyuib5Tj9EpSzhJeQENg/640?wx_fmt=png&from=appmsg "")  
  
  
修复方案  
  
### 官方修复方案  
  
官方已发布新版本，请访问链接下载：  
  
https://shurufa.sogou.com/windows  
  
微步产品支撑  
  
  
微步漏洞情报于  
2  
026  
-09-  
14  
收录该漏洞。  
  
微步下一代威胁情报平台NGTIP及X情报中心已于漏洞收录时向漏洞订阅用户推送该漏洞  
情报，  
并将持续推送后续更新；对于已经录入资产的用户，支持实时自动化排查受影响资产。  
  
当前OneSEC已支持CVE-2026-51990检测，可关注告警规则ID：  
1994   
  
![](https://mmbiz.qpic.cn/mmbiz_png/T4OSm0sXdEMibHdNrtXW6RsNF8IGdgoqgdAMibIfenPQ7iawx2Ler9haTXIa51jHz05x9ibxkC1wPhD4RpklXu3VnvcpQ3WMWRLmjDK0ZzQOTPY/640?wx_fmt=png&from=appmsg "")  
  
  
  

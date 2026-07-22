#  新增受影响场景，Fastjson远程代码执行漏洞三次更新  
微步情报局
                    微步情报局  微步在线研究响应中心   2026-07-22 03:24  
  
漏洞概况  
  
  
Fastjson 是 Alibaba 开源的一款基于 Java 的快速 JSON 解析器/生成器，广泛用于 Java 应用的 JSON 序列化与反序列化。  
  
近日，微步情报局监测到  
互联网披露了Fastjson 远程代码执行漏洞。  
  
经分析，Fastjson 存在远程代码执行漏洞，远程攻击者通过向使用受影响版本 Fastjson 的应用发送特制的恶意 JSON 数据，无需依赖任何第三方类库，即可在目标服务器上执行任意代码。  
  
此漏洞  
无须用户权限  
，攻击者成功利用此漏洞可  
远程攻击者可在未启用安全模式的目标服务器上执行任意代码，直接威胁系统机密性、完整性与可用性，可导致服务器被完全控制。  
建议受影响用户  
尽快修复。  
  
漏洞处置优先级(VPT)  
  
  
**综合处置优先级：**  
高风险  
<table><tbody><tr><td rowspan="3" style="border: 1px solid rgb(221, 221, 221);padding: 12px;text-align: left;vertical-align: top;font-weight: bold;background-color: rgb(248, 249, 250);"><section data-nest-level="7"><span leaf="" data-nest-level="8">基本信息</span></section></td><td style="border: 1px solid rgb(221, 221, 221);padding: 12px;text-align: left;vertical-align: top;"><section data-nest-level="7"><span leaf="" data-nest-level="8">微步编号</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section data-nest-level="7"><span leaf="" data-nest-level="8">XVE-2026-39684</span></section></td></tr><tr><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section data-nest-level="7"><span leaf="" data-nest-level="8">CVE编号</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section data-nest-level="7"><span leaf="" data-nest-level="8">无</span></section></td></tr><tr><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section data-nest-level="7"><span leaf="" data-nest-level="8">漏洞类型</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section data-nest-level="7"><span leaf="" data-nest-level="8">RCE(远程代码执行)</span></section></td></tr><tr><td rowspan="5" style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;font-weight:bold;background-color:#f8f9fa;"><section data-nest-level="7"><span leaf="" data-nest-level="8">利用条件评估</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section data-nest-level="7"><span leaf="" data-nest-level="8">利用漏洞的网络条件</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section data-nest-level="7"><span leaf="" data-nest-level="8">网络可达</span></section></td></tr><tr><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section data-nest-level="7"><span leaf="" data-nest-level="8">是否需要绕过安全机制</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section data-nest-level="7"><span leaf="" data-nest-level="8">否</span></section></td></tr><tr><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section data-nest-level="7"><span leaf="" data-nest-level="8">对被攻击系统的要求</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section data-nest-level="7"><span leaf="" data-nest-level="8">未启用 Fastjson 安全模式(SafeMode)</span></section></td></tr><tr><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section data-nest-level="7"><span leaf="" data-nest-level="8">利用漏洞的权限要求</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section data-nest-level="7"><span leaf="" data-nest-level="8">无须用户权限</span></section></td></tr><tr><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section data-nest-level="7"><span leaf="" data-nest-level="8">是否需要受害者配合</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section data-nest-level="7"><span leaf="" data-nest-level="8">否</span></section></td></tr><tr><td rowspan="2" style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;font-weight:bold;background-color:#f8f9fa;"><section data-nest-level="7"><span leaf="" data-nest-level="8">利用情报</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section data-nest-level="7"><span leaf="" data-nest-level="8">POC是否公开</span></section></td><td><section data-nest-level="7"><span leaf="" data-nest-level="8"><span textstyle="" style="color: rgb(255, 0, 0);">是</span></span></section></td></tr><tr><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section data-nest-level="7"><span leaf="" data-nest-level="8">已知利用行为</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section data-nest-level="7"><span data-nest-level="8" data-pm-slice="3 3 [&#34;para&#34;,{&#34;tagName&#34;:&#34;section&#34;,&#34;attributes&#34;:{&#34;style&#34;:&#34;color:#0f172a;font-size:15px;font-family:-apple-system, BlinkMacFont, \&#34;Segoe UI\&#34;, \&#34;PingFang SC\&#34;, sans-serif;&#34;,&#34;data-nest-level&#34;:&#34;2&#34;,&#34;data-pm-slice&#34;:&#34;0 0 []&#34;},&#34;namespaceURI&#34;:&#34;http://www.w3.org/1999/xhtml&#34;},&#34;para&#34;,{&#34;tagName&#34;:&#34;p&#34;,&#34;attributes&#34;:{&#34;data-nest-level&#34;:&#34;3&#34;,&#34;style&#34;:&#34;margin: 0px 0px 14px;padding: 0px;line-height: 1.6em;&#34;},&#34;namespaceURI&#34;:&#34;http://www.w3.org/1999/xhtml&#34;}]"><font face="微软雅黑" data-nest-level="9"><span leaf="" data-nest-level="10"><span textstyle="" style="color: rgb(255, 0, 0);">微步威胁感知平台</span></span></font><font face="微软雅黑" data-nest-level="9"><span leaf="" data-nest-level="10"><span textstyle="" style="color: rgb(255, 0, 0);">TDP</span></span></font></span><span leaf="" data-nest-level="8"><span textstyle="" style="color: rgb(255, 0, 0);">已捕获在野利用行为</span></span></section></td></tr></tbody></table>  
漏洞影响范围  
  
<table><tbody><tr><td style="border: 1px solid rgb(221, 221, 221);padding: 12px;text-align: left;vertical-align: top;font-weight: bold;background-color: rgb(248, 249, 250);"><section data-nest-level="7"><span leaf="" data-nest-level="8">产品名称</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section data-nest-level="7"><span leaf="" data-nest-level="8">Fastjson</span></section></td></tr><tr><td style="border: 1px solid rgb(221, 221, 221);padding: 12px;text-align: left;vertical-align: top;font-weight: bold;background-color: rgb(248, 249, 250);"><section data-nest-level="7"><span leaf="" data-nest-level="8">官方通告影响范围</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section data-nest-level="7"><span leaf="" data-nest-level="8">1.2.68</span><span style="mso-spacerun:&#39;yes&#39;;font-family:微软雅黑;font-size:10.5000pt;" data-nest-level="8"><font face="微软雅黑" data-nest-level="9"><span leaf="" data-nest-level="10">&lt;=</span></font></span><span style="mso-spacerun:&#39;yes&#39;;font-family:微软雅黑;font-size:10.5000pt;" data-nest-level="8"><span leaf="" data-nest-level="9">version</span></span><span style="mso-spacerun:&#39;yes&#39;;font-family:微软雅黑;font-size:10.5000pt;" data-nest-level="8"><font face="微软雅黑" data-nest-level="9"><span leaf="" data-nest-level="10">&lt;</span></font></span><span style="mso-spacerun:&#39;yes&#39;;font-family:微软雅黑;font-size:10.5000pt;" data-nest-level="8"><span leaf="" data-nest-level="9">=1.2.83</span></span></section></td></tr><tr><td><p data-pm-slice="0 0 []" data-nest-level="7"><span data-nest-level="8"><font face="微软雅黑" data-nest-level="9"><span leaf="" data-nest-level="10"><span textstyle="" style="font-weight: bold;">微步验证实际影响范围</span></span></font></span></p></td><td><p data-pm-slice="0 0 []" data-nest-level="7"><span style="mso-spacerun:&#39;yes&#39;;font-family:微软雅黑;color:rgb(255,0,0);mso-ansi-font-weight:normal;font-size:10.5000pt;" data-nest-level="8"><span leaf="" data-nest-level="9">1.2.66/1.2.67</span></span><span style="mso-spacerun:&#39;yes&#39;;font-family:微软雅黑;color:rgb(255,0,0);mso-ansi-font-weight:normal;font-size:10.5000pt;" data-nest-level="8"><span leaf="" data-nest-level="9"> </span></span><span style="mso-spacerun:&#39;yes&#39;;font-family:微软雅黑;color:rgb(255,0,0);mso-ansi-font-weight:normal;font-size:10.5000pt;" data-nest-level="8"><span leaf="" data-nest-level="9">jar:file</span></span><span style="mso-spacerun:&#39;yes&#39;;font-family:微软雅黑;color:rgb(255,0,0);mso-ansi-font-weight:normal;font-size:10.5000pt;" data-nest-level="8"><font face="微软雅黑" data-nest-level="9"><span leaf="" data-nest-level="10">也能复现。而且</span></font><font face="微软雅黑" data-nest-level="9"><span leaf="" data-nest-level="10">fastjson更低版本有其他安全问题，建议</span></font></span><span style="mso-spacerun:&#39;yes&#39;;font-family:微软雅黑;color:rgb(255,0,0);mso-ansi-font-weight:normal;font-size:10.5000pt;" data-nest-level="8"><font face="微软雅黑" data-nest-level="9"><span leaf="" data-nest-level="10">漏洞</span></font></span><span style="mso-spacerun:&#39;yes&#39;;font-family:微软雅黑;color:rgb(255,0,0);mso-ansi-font-weight:normal;font-size:10.5000pt;" data-nest-level="8"><font face="微软雅黑" data-nest-level="9"><span leaf="" data-nest-level="10">管控时按照</span></font></span><span style="mso-spacerun:&#39;yes&#39;;font-family:微软雅黑;color:rgb(255,0,0);mso-ansi-font-weight:normal;font-size:10.5000pt;" data-nest-level="8"><span leaf="" data-nest-level="9">version</span></span><span style="mso-spacerun:&#39;yes&#39;;font-family:微软雅黑;color:rgb(255,0,0);mso-ansi-font-weight:normal;font-size:10.5000pt;" data-nest-level="8"><font face="微软雅黑" data-nest-level="9"><span leaf="" data-nest-level="10">&lt;</span></font></span><span style="mso-spacerun:&#39;yes&#39;;font-family:微软雅黑;color:rgb(255,0,0);mso-ansi-font-weight:normal;font-size:10.5000pt;" data-nest-level="8"><span leaf="" data-nest-level="9">=1.2.83</span></span><span style="mso-spacerun:&#39;yes&#39;;font-family:微软雅黑;color:rgb(255,0,0);mso-ansi-font-weight:normal;font-size:10.5000pt;" data-nest-level="8"><span leaf="" data-nest-level="9"> </span><font face="微软雅黑" data-nest-level="9"><span leaf="" data-nest-level="10">排查</span></font></span><span style="mso-spacerun:&#39;yes&#39;;font-family:微软雅黑;color:rgb(255,0,0);mso-ansi-font-weight:normal;font-size:10.5000pt;" data-nest-level="8"><font face="微软雅黑" data-nest-level="9"><span leaf="" data-nest-level="10">。</span></font></span></p></td></tr></tbody></table>  
漏洞复现  
  
  
jar:http 远程拉取在 Spring Boot FatJar 场景中可作为第一阶段资源获取。在 Linux/macOS 下，可进一步借助 /proc/self/fd 或 /dev/fd 转为 jar:file，形成二阶段 fd bridge RCE。  
  
  
最新验证结论为：  
  
  
1、Spring Boot 常见内嵌容器 Tomcat、Jetty、Undertow 均受影响（  
说明：这里指fd bridge 链路已验证，不等价于所有容器都支持单发直接 jar:http RCE。）  
  
2、Linux/macOS：JDK8/17/21/25 等已验证可 RCE。  
  
3、Windows：JDK8 可触发。  
Windows 缺少 /proc/self/fd 或 /dev/fd 同形态路径，高 JDK 不受影响。  
     
  
4、1.2.83_noneautotype  
不是“关闭 autoType 但仍可绕过”的普通配置状态，而是 artifact 行为发生变化，safeMode 在危险路径前直接终止，因此在当前已知利用链路下不受此漏洞影响。  
  
  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/T4OSm0sXdEN6OGx2ccQxicoHtV4lmptpv5rxyFKYIgTEClM2zbYlTZuDkX6LNictEK6vZMhgCxkyVIgQFD0cuQNKRjsiaic8YJPicstSIIcvicsxc/640?wx_fmt=png&from=appmsg "")  
###   
  
  
修复方案  
  
### 临时缓解措施  
  
1、官方暂未发布该漏洞补丁及修复版本,鉴于 fastjson 1.x 系列已停更，建议迁移至 Fastjson 2.x 版本  
  
2、可通过以下任意一种方式启用 Fastjson 的安全模式(SafeMode):  
- 代码启用： ParserConfig.getGlobalInstance().setSafeMode(true)  
  
- JVM 启动参数启用： -Dfastjson.parser.safeMode=true  
  
- 配置文件启用： fastjson.parser.safeMode=true  
  
3、使用防护类设备拦截带有如下内容的  
POST、JSON请求：  
  
·  
  
@type":"jar:file:.  
  
·  
  
@type":"jar:http:..  
  
4、可  
切换  
到  
 noneautotype 版本  
，  
Maven 坐标示例：  
  
com.alibaba:fastjson:1.2.83_noneautotype  
  
  
  
微步产品支撑  
  
  
1、微步漏洞情报于  
202  
6-0  
7  
-  
20收录该漏洞  
。  
  
2、微步下一代威胁情报平台NGTIP及X情报中心已向漏洞订阅用户推送该漏洞情报，并将持续推送后续更新；对于已经录入资产的用户，支持实时自动化排查受影响资产。  
  
  
3、微步威胁感知平台  
TDP已于  
2026  
-  
0  
7  
-  
20支持检测，检测ID：S3100181015  
，模型/规则高于：20260720000000  
   
可检出。  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/T4OSm0sXdEPSCIkLibPf0zSYFNAOz9uND2ia2sozDVW9FyfgVOUknCohC53UsMoib4VqgOIfZcQDYvOd63H3825KJic7pESk5O7D0gvoTvTFcics/640?wx_fmt=png&from=appmsg "")  
  
  
4、微步威胁防御系统OneSIG已支持防护，规则ID：3100181015。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/T4OSm0sXdEPOxxfhFH6h6mcdjyy9ksJC8ibgicQUcCHTpV3nsvGqOvlmrgr29ZknD4xdlKHKNSaHNIY6JllBLtJibXHlU2fKqrB6z0WB1aF7Vk/640?wx_fmt=png&from=appmsg "")  
   
  
  

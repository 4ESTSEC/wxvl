#  思科CUCM高危漏洞PoC公开后24小时内即遭利用  
原创 网空闲话
                    网空闲话  网空闲话plus   2026-06-25 23:14  
  
2026年6月23日，SSD Secure Disclosure研究团队详细披露了思科统一通信管理器（CUCM）中一个高危漏洞的完整技术细节与概念验证利用链。仅仅一天之内，安全厂商Defused便在6月24日监测到针对该漏洞的大规模实际攻击，且攻击流量高度复刻了公开的PoC，连Webshell的访问口令都原封未动。这一“披露即武器化”的速度再次将漏洞响应的时间窗口压缩至极限，也让全球数千万CUCM用户面临前所未有的紧迫威胁。  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/lQ1jXOMq3d2icJKAfhHWEQUibVhzpcL8NJ1FKzsbuiaCibGDBgfCgibqaKX1icaIic84CdvzmviaDC62JU06qQKTibnxZlRSUibsZyApYx2EEYN2QxLuQ/640?wx_fmt=webp&from=appmsg "")  
  
**漏洞概述：SSRF串联任意文件写入，直取Root权限**  
  
该漏洞编号CVE-2026-20230，CVSS评分8.6，但思科在其官方公告中明确要求将其视为“严重”级别。漏洞属于输入验证缺陷，可导致未经身份验证的远程攻击者进行服务器端请求伪造，并最终提升权限至root。受影响产品为启用了WebDialer服务的Cisco Unified CM和Unified CM SME部署。WebDialer默认关闭，可一旦启用，攻击路径就完全敞开。  
  
根据SSD披露的根因分析，攻击链由多个精密咬合的弱点构成。首先，CUCM的Tomcat应用配置中，/cmplatform/installClusterStatusExecute  
端点无需认证即可访问。其内部调用NodeInstallStageClient#getInstallStage  
时，会向攻击者指定的“hostname”发起HTTPS请求，形成SSRF。虽然InjectionFilter  
对目标地址实施了校验，拦截了127.0.0.1等内网地址，但另一个同样无需认证的接口/webdialer/Version.jws?wsdl  
却可直接返回服务器的真实主机名，从而使校验形同虚设。  
  
更致命的缺陷在于，代码在构造请求时未对路径进行充分过滤。攻击者可向installClusterStatusExecute  
发送包含经过URL编码的Axis服务部署描述符的hostname  
参数，并利用转义序列!--  
截断正常响应内容，将一个完整的恶意Axis服务定义写入磁盘。该定义中，日志处理器的输出路径被精心设置为穿透多层目录的../../../../../../../../../../../../common/log/taos-log-a/tomcat/webapps/platform-services/axis2-web/aaa.jsp  
，最终在可公开访问的Web目录中生成一个JSP文件。整个过程类似历史Axis 1.4漏洞（CVE-2019-0227）的变种，无需任何凭据。  
  
**利用链还原：从SSRF到Webshell的机械式拷贝**  
  
SSD的PoC展示了一条极其清晰的利用路径。第一步，攻击者访问/webdialer/Version.jws?wsdl  
获取主机名。第二步，向/cmplatform/installClusterStatusExecute?action=clusterNodeInstallStatus  
发送特制GET请求，其中hostname  
参数被替换为一长段经过URL编码的Axis部署描述符。解码后的内容为：  
  
text  
```
```  
  
  
CUCM在处理节点安装状态时，会将攻击者控制的这段恶意内容写入aaa.jsp  
。该文件实际上是一个精简的文件写入器，其JSP代码仅接收f  
和t  
参数，并将t  
的内容写入f  
指定的相对路径。  
  
第三步，攻击者访问/webdialer/services/randomR11  
（此时恶意Axis服务已注册），调用nextInt  
方法，将真正的命令执行Webshell写入同一目录下的c.jsp  
。第四步，访问/platform-services/axis2-web/c.jsp?pwd=123&i=id  
即可执行任意系统命令，权限为root。整个链条中，攻击者无需任何合法身份，且所有通信均可走HTTPS加密。  
  
**24小时内武器化：连口令“123”都懒得改**  
  
Defused的监测数据显示，6月24日起的攻击活动几乎一字不差地复现了SSD的利用代码。攻击者同样通过WebDialer的SSRF漏洞部署了名为“randomR11”的Axis恶意服务，利用日志处理器写入第一阶段的JSP文件写入器，继而释放第二阶段命令执行Shell。尤为讽刺的是，最终Webshell的密码就是PoC中使用的“123”，攻击者甚至未曾修改这一默认值。这充分表明，攻击者极大概率是直接下载公开代码即行攻击，没有任何定制化开发，侧面印证了漏洞利用的成熟度和自动化攻击工具链的完备性。  
  
Defused据此发出明确警告：任何启用了WebDialer的CUCM系统若未及时修补CVE-2026-20230，“应假定已被扫描甚至已被入侵”。就在几天前，该公司已观察到对存在漏洞的CUCM系统的扫描和标记行为，而PoC公开后攻击迅速升级为批量自动化打击。  
  
**厂商与安全界紧急联动**  
  
事实上，思科早在2026年6月3日便已发布修复版本和详细安全公告，呼吁用户立即更新。然而，从补丁发布到PoC公开相隔近三周，仍有大量组织未能完成修补。PoC公开后，  
Horizon3.ai  
迅速推出名为“快速响应测试”的免费工具，可模拟真实攻击但不造成实际损害，帮助企业验证环境中的脆弱性。该公司在社交平台X上敦促受影响企业立刻实施思科提供的缓解措施，或在不需要时直接禁用WebDialer。  
Horizon3.ai  
同时强调，统一通信管理器广泛服务于医疗、金融、政府和企业环境，属于关键基础设施，一旦失陷后果严重。  
  
此外，同一周内，思科Catalyst SD-WAN部署中的另一高危漏洞也遭攻击者利用，凸显企业网络设备正面临叠加式威胁，安全团队应对压力骤增。  
  
**深度反思：补丁窗口与攻击速度的赛跑**  
  
从6月3日补丁发布到6月23日PoC公开，再到6月24日攻击现形，整个时间线暴露了现实防御的脆弱。补丁虽然早已可用，但众多机构因业务连续性顾虑、资产管理不清或流程滞后未能及时修复。PoC公开后，攻击者几乎可以零成本将研究转化为武器，留给防守方的时间已非“周”或“天”，而是以小时计。这要求企业必须构建实时漏洞威胁情报追踪能力，并实现关键业务系统的快速修补机制，否则将永远处于被动。  
  
**【闲话简评】**  
  
此次思科CUCM漏洞的闪电式利用，再次敲响了三重警钟：第一，互联网暴露面的管理必须“零信任”，WebDialer等非必要服务应一律关闭或严控访问来源，绝不可将通信管理中枢直接裸露于公网。第二，漏洞修补的时效性不是“尽快”而是“立即”，从补丁发布到PoC公开的窗口期是生死时速，应建立7×24小时漏洞预警和紧急变更流程，优先修复可被链式利用的高危缺陷。第三，攻击者连口令都懒得改，说明工业化攻击流水线已能自动抓取新PoC并植入，传统安全检测必须升级为行为分析，监控异常JSP文件生成、陌生Axis服务注册等行为，才能在“24小时窗口”内阻断入侵。安全防护，唯有比攻击者更快。  
  
**参考文献**  
1. SSD Secure Disclosure, “Cisco Unified Communications Manager Arbitrary File Write to RCE,” June 23, 2026.  
  
1. Jai Vijayan, “Attackers Exploit Cisco CUCM Vulnerability Within 24 Hours of PoC Release,” Dark Reading  
, June 26, 2026.   
  

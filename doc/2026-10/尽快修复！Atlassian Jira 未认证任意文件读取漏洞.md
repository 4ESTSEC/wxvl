#  尽快修复！Atlassian Jira 未认证任意文件读取漏洞  
原创 微步情报局
                    微步情报局  微步在线研究响应中心   2026-10-08 02:21  
  
漏洞概况  
  
  
Atlassian Jira 是 Atlassian 公司开发的项目与事务跟踪管理软件，广泛用于需求管理、缺陷跟踪与敏捷开发协作。  
  
近日，Atlassian官方发布通告，修复了Atlassian Jira 任意文件读取漏洞（CVE-2026-21589）。  
微步情报  
局已成功复现  
该漏洞。  
该漏洞位于 Atlassian 多款产品共享的 Web 资源服务组件 atlassian-plugins-webresource 中，  
该组件使用 :: 作为 URL 中 / 的转义表示，攻击者可构造包含 ..:: 的恶意请求绕过路径安全检查，  
在无需任何身份认证的  
情况下  
读取 Tomcat 应用上下文(webroot)内的任意文件。  
在部署了 Atlassian Crowd(统一身份认证)的环境中，还可进一步读取 Crowd 明文凭据并通过 Crowd REST API 创建管理员账户，实现从匿名访问到完全接管的攻击链。  
  
除 Jira 外该漏洞还影响 Confluence、Bitbucket、Bamboo、Crowd、Crucible、Fisheye 等共八款产品。  
  
（完整漏洞情报请查阅https://x.threatbook.com/v5/vul/XVE-2026-73269）  
  
此漏洞  
无须用户权限  
，  
建议受影响用户  
尽快修复。  
  
漏洞处置优先级(VPT)  
  
  
**综合处置优先级：**  
高风险  
<table><tbody><tr><td rowspan="3" style="border: 1px solid rgb(221, 221, 221);padding: 12px;text-align: left;vertical-align: top;font-weight: bold;background-color: rgb(248, 249, 250);"><section><span leaf="">基本信息</span></section></td><td style="border: 1px solid rgb(221, 221, 221);padding: 12px;text-align: left;vertical-align: top;"><section><span leaf="">微步编号</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">XVE-2026-73269</span></section></td></tr><tr><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">CVE编号</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">CVE-2026-21589</span></section></td></tr><tr><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">漏洞类型</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">任意文件读取(路径遍历)</span></section></td></tr><tr><td rowspan="5" style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;font-weight:bold;background-color:#f8f9fa;"><section><span leaf="">利用条件评估</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">利用漏洞的网络条件</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">远程</span></section></td></tr><tr><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">是否需要绕过安全机制</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">否</span></section></td></tr><tr><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">对被攻击系统的要求</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">无特殊要求</span></section></td></tr><tr><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">利用漏洞的权限要求</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">无须用户权限</span></section></td></tr><tr><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">是否需要受害者配合</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">否</span></section></td></tr><tr><td rowspan="2" style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;font-weight:bold;background-color:#f8f9fa;"><section><span leaf="">利用情报</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">是否有POC</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><span style="color:#d93025;font-weight:bold;"><span leaf="">是</span></span></td></tr><tr><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">已知利用行为</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">暂无</span></section></td></tr></tbody></table>  
漏洞影响范围  
  
- Jira Software Data Center:  
  
version<9.12.40、version<10.3.26、version<11.3.12   
  
- Jira Service Management Data Center:  
  
version<5.12.40、version<10.3.26、version<11.3.12   
  
- Atlassian Confluence:  
  
version<9.2.26、version<10.2.19   
  
- Bitbucket Data Center:  
  
version<9.4.26、version<10.2.8、version<10.5.1   
  
- Atlassian Bamboo:  
  
version<10.2.24、version<12.1.12   
  
- Atlassian Crowd:  
  
version<6.3.7、version<7.0.3、version<7.1.7、version<7.2.4   
  
- Atlassian Crucible Data Center:  
  
version<4.9.15   
  
- Atlassian Fisheye Data Center:  
  
version<4.9.15  
  
  
  
漏洞复现  
  
  
执行 poc，在受影响的 Jira 环境实现未认证读取WEB-INF/web.xml。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/T4OSm0sXdEO07RK9gacc1CGmicfPew2dVMHf4jyibiaveK3XsH1AkbpohwaSgl5eDhsbiaxoicD96JtstJU6rlGDO2GIMvuib52Qecic6kicQD5oKj8/640?wx_fmt=png&from=appmsg "")  
  
  
修复方案  
  
### 官方修复方案  
  
官方已发布安全公告，请访问链接查看：  
  
https://confluence.atlassian.com/security/cve-2026-21589-arbitrary-file-access-vulnerability-impacts-multiple-products-1870495748.html  
  
鉴于本次漏洞影响产品较多，公告中已针对各产品分别给出对应的升级方案与临时缓解措施，请根据实际使用的产品查阅并执行相应操作。  
  
微步产品支撑  
  
  
微步漏洞情报于  
2026-10-05  
收录该漏洞。  
  
微步下一代威胁情报平台NGTIP及X情报中心已于漏洞收录时向漏洞订阅用户推送该漏洞情报，并将持续推送后续更新；对于已经录入资产的用户，支持实时自动化排查受影响资产。  
  
微步威  
胁感知  
平台TDP已于  
20261008  
支持检测，检测ID：  
S3100184977  
，  
模型/规则高于：  
20261008000000   
可检出。  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/T4OSm0sXdEMgYuiclic8P4giamenSn7mLfiaU8EQscu2EuIBfblicw3TBR2wfCCFg5z8kvkSWT7qsDlq2CUT0gibGNO1lq1r22YXVTYibynicHxdsfI/640?wx_fmt=png&from=appmsg "")  
  
  
  
微步威胁防御系统OneSIG已支持防护规则ID为：  
3100184977   
  
  

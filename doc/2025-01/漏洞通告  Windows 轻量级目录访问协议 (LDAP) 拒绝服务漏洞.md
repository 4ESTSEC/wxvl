#  漏洞通告 | Windows 轻量级目录访问协议 (LDAP) 拒绝服务漏洞   
原创 微步情报局  微步在线研究响应中心   2025-01-06 02:50  
  
![](https://mmbiz.qpic.cn/mmbiz_png/fFyp1gWjicMKNkm4Pg1Ed6nv0proxQLEKJ2CUCIficfAwKfClJ84puialc9eER0oaibMn1FDUpibeK1t1YvgZcLYl3A/640?wx_fmt=png "")  
  
  
**漏洞概况**  
  
  
  
LDAP 是一种开放的、跨平台的、行业标准的应用层协议，用于访问和维护分布式目录信息服务。  
它定义了客户端如何与目录服务器进行通信，以查询、修改和管理目录数据。  
  
  
微步情报局获取到Windows 轻量级目录访问协议 (LDAP) 拒绝服务漏洞情报CVE-2024-49113（https://x.threatbook.com/v5/vul/XVE-2024-35677）。攻击者  
通过向LDAP客户端发送特定的RPC请求，诱使LDAP客户端查找或连接到恶意的LDAP服务器  
。连接建立后恶意的LDAP服务器通过向LDAP客户端发送特定的LDAP数据包，从而导致LDAP客户端整数溢出，  
最终造成拒绝服务攻击，导致LDAP客户端所在服务器重启  
。  
  
漏洞PoC已公开，建议受影响的客户尽快修复。  
  
  
**漏洞处置优先级(VPT)**  
  
  
  
**综合处置优先级：**  
**高**  
<table><tbody style="outline: 0px;"><tr style="outline: 0px;height: 31.0667px;"><td width="110" colspan="1" rowspan="2" style="padding: 0px 7.2px;outline: 0px;word-break: break-all;hyphens: auto;border-width: 0.666667px;border-color: rgb(191, 191, 191);vertical-align: top;"><p style="outline: 0px;"><span style="outline: 0px;color: rgb(84, 84, 84);font-size: 14px;letter-spacing: 1px;"><strong style="outline: 0px;">基本信息</strong></span><o:p style="outline: 0px;"></o:p></p></td><td width="186" colspan="1" rowspan="1" style="padding: 0px 7.2px;outline: 0px;word-break: break-all;hyphens: auto;border-width: 0.666667px;border-color: rgb(191, 191, 191);vertical-align: top;"><p style="outline: 0px;"><span style="outline: 0px;color: rgb(84, 84, 84);letter-spacing: 1px;font-size: 14px;">微步编号</span><o:p style="outline: 0px;"></o:p></p></td><td width="88" colspan="1" rowspan="1" style="padding: 0px 7.2px;outline: 0px;word-break: break-all;hyphens: auto;border-width: 0.666667px;border-color: rgb(191, 191, 191);vertical-align: top;"><span style="font-size: 14px;color: rgb(84, 84, 84);">XVE-2024-35677</span></td></tr><tr style="padding-right: 7.2px;padding-left: 7.2px;outline: 0px;word-break: break-all;hyphens: auto;border-width: 0.666667px;border-color: rgb(191, 191, 191);vertical-align: top;height: 31.0667px;"><td colspan="1" rowspan="1" width="189" style="padding: 0px 7.2px;outline: 0px;word-break: break-all;hyphens: auto;border-width: 0.666667px;border-color: rgb(191, 191, 191);vertical-align: top;" height="31"><span style="outline: 0px;font-size: 14px;color: rgb(84, 84, 84);">漏洞类型</span><br style="outline: 0px;"/></td><td colspan="1" rowspan="1" width="221" style="padding: 0px 7.2px;outline: 0px;word-break: break-all;hyphens: auto;border-width: 0.666667px;border-color: rgb(191, 191, 191);vertical-align: top;" height="31"><p><span style="font-size: 14px;color: rgb(84, 84, 84);">拒绝服务漏洞</span></p></td></tr><tr style="outline: 0px;height: 31.0667px;"><td width="135" colspan="1" rowspan="5" style="padding: 0px 7.2px;outline: 0px;word-break: break-all;hyphens: auto;border-width: 0.666667px;border-color: rgb(191, 191, 191);vertical-align: top;"><p style="outline: 0px;"><strong style="outline: 0px;"><span style="outline: 0px;color: rgb(84, 84, 84);letter-spacing: 1px;font-size: 14px;">利用条件评估</span></strong><o:p style="outline: 0px;"></o:p></p></td><td width="169" colspan="1" rowspan="1" style="padding: 0px 7.2px;outline: 0px;word-break: break-all;hyphens: auto;border-width: 0.666667px;border-color: rgb(191, 191, 191);vertical-align: top;"><p style="outline: 0px;"><span style="outline: 0px;color: rgb(84, 84, 84);letter-spacing: 1px;font-size: 14px;">利用漏洞的网络条件<br style="outline: 0px;"/></span><o:p style="outline: 0px;"></o:p></p></td><td width="221" colspan="1" rowspan="1" style="padding: 0px 7.2px;outline: 0px;word-break: break-all;hyphens: auto;border-width: 0.666667px;border-color: rgb(191, 191, 191);vertical-align: top;"><span style="font-size: 14px;color: rgb(84, 84, 84);">远程</span></td></tr><tr style="outline: 0px;height: 31.0667px;"><td width="189" colspan="1" rowspan="1" style="padding: 0px 7.2px;outline: 0px;word-break: break-all;hyphens: auto;border-width: 0.666667px;border-color: rgb(191, 191, 191);vertical-align: top;"><p style="outline: 0px;"><span style="outline: 0px;color: rgb(84, 84, 84);font-size: 14px;letter-spacing: 1px;">是否需要绕过安全机制</span><o:p style="outline: 0px;"></o:p></p></td><td width="221" colspan="1" rowspan="1" style="padding: 0px 7.2px;outline: 0px;word-break: break-all;hyphens: auto;border-width: 0.666667px;border-color: rgb(191, 191, 191);vertical-align: top;"><span style="font-size: 14px;color: rgb(84, 84, 84);">不需要</span></td></tr><tr style="outline: 0px;height: 27px;"><td width="189" colspan="1" rowspan="1" style="padding: 0px 7.2px;outline: 0px;word-break: break-all;hyphens: auto;border-width: 0.666667px;border-color: rgb(191, 191, 191);vertical-align: top;" height="27"><p><span style="font-size: 14px;color: rgb(84, 84, 84);">对被攻击系统的要求</span><span style="font-size: 14px;"><br style="outline: 0px;"/></span><o:p></o:p></p></td><td width="221" colspan="1" rowspan="1" style="padding: 0px 7.2px;outline: 0px;word-break: break-all;hyphens: auto;border-width: 0.666667px;border-color: rgb(191, 191, 191);vertical-align: top;" height="27"><section style="margin-bottom: 0px;"><span style="font-size: 14px;color: rgb(84, 84, 84);">无</span></section></td></tr><tr style="outline: 0px;height: 27px;"><td width="189" colspan="1" rowspan="1" style="padding: 0px 7.2px;outline: 0px;word-break: break-all;hyphens: auto;border-width: 0.666667px;border-color: rgb(191, 191, 191);vertical-align: top;"><p style="outline: 0px;"><span style="outline: 0px;color: rgb(84, 84, 84);font-size: 14px;letter-spacing: 1px;text-wrap: wrap;">利用漏洞的权限要求</span><o:p style="outline: 0px;"></o:p></p></td><td width="221" colspan="1" rowspan="1" style="padding: 0px 7.2px;outline: 0px;word-break: break-all;hyphens: auto;border-width: 0.666667px;border-color: rgb(191, 191, 191);vertical-align: top;"><span style="font-size: 14px;color: rgb(84, 84, 84);">无需任何权限</span></td></tr><tr style="outline: 0px;height: 27px;"><td width="189" colspan="1" rowspan="1" style="padding: 0px 7.2px;outline: 0px;word-break: break-all;hyphens: auto;border-width: 0.666667px;border-color: rgb(191, 191, 191);vertical-align: top;"><p style="outline: 0px;"><span style="outline: 0px;color: rgb(84, 84, 84);letter-spacing: 1px;font-size: 14px;">是否需要受害者配合</span><o:p style="outline: 0px;"></o:p></p></td><td width="88" colspan="1" rowspan="1" style="padding: 0px 7.2px;outline: 0px;word-break: break-all;hyphens: auto;border-width: 0.666667px;border-color: rgb(191, 191, 191);vertical-align: top;"><span style="font-size: 14px;color: rgb(84, 84, 84);">不需要</span></td></tr><tr style="outline: 0px;height: 27.2px;"><td width="115" colspan="1" rowspan="2" style="padding: 0px 7.2px;outline: 0px;word-break: break-all;hyphens: auto;border-width: 0.666667px;border-color: rgb(191, 191, 191);vertical-align: top;"><p style="outline: 0px;"><strong style="outline: 0px;"><span style="outline: 0px;color: rgb(84, 84, 84);letter-spacing: 1px;font-size: 14px;">利用情报</span></strong><o:p style="outline: 0px;"></o:p></p></td><td width="169" colspan="1" rowspan="1" style="padding: 0px 7.2px;outline: 0px;word-break: break-all;hyphens: auto;border-width: 0.666667px;border-color: rgb(191, 191, 191);vertical-align: top;"><p style="outline: 0px;"><span style="outline: 0px;color: rgb(84, 84, 84);font-size: 14px;letter-spacing: 1px;">PoC是否公开</span><o:p style="outline: 0px;"></o:p></p></td><td width="88" colspan="1" rowspan="1" style="padding: 0px 7.2px;outline: 0px;word-break: break-all;hyphens: auto;border-width: 0.666667px;border-color: rgb(191, 191, 191);vertical-align: top;height: 27.2px;"><span style="outline: 0px;color: rgb(84, 84, 84);font-size: 14px;">是</span></td></tr><tr style="padding-right: 7.2px;padding-left: 7.2px;outline: 0px;word-break: break-all;hyphens: auto;border-width: 0.666667px;border-color: rgb(191, 191, 191);vertical-align: top;height: 27.2px;"><td colspan="1" rowspan="1" width="169" style="padding: 0px 7.2px;outline: 0px;word-break: break-all;hyphens: auto;border-width: 0.666667px;border-color: rgb(191, 191, 191);vertical-align: top;" height="27"><span style="outline: 0px;color: rgb(84, 84, 84);font-size: 14px;">已知利用行为<br style="outline: 0px;"/></span></td><td colspan="1" rowspan="1" width="222" height="27" style="padding: 0px 7.2px;outline: 0px;word-break: break-all;hyphens: auto;border-width: 0.666667px;border-color: rgb(191, 191, 191);vertical-align: top;height: 27px;"><span style="font-size: 14px;color: rgb(84, 84, 84);">否</span><br/></td></tr></tbody></table>#   
  
**漏洞影响范围**  
  
  
  
<table><tbody style="outline: 0px;"><tr style="outline: 0px;height: 33.2px;"><td width="119" colspan="1" rowspan="1" style="padding: 0px 7.2px;outline: 0px;word-break: break-all;hyphens: auto;border-width: 0.666667px;border-color: rgb(191, 191, 191);vertical-align: top;"><p style="outline: 0px;"><strong style="outline: 0px;"><span style="outline: 0px;color: rgb(84, 84, 84);letter-spacing: 1px;font-size: 14px;">产品名称</span></strong><o:p style="outline: 0px;"></o:p></p></td><td width="324" colspan="1" rowspan="1" style="padding: 0px 7.2px;outline: 0px;word-break: break-all;hyphens: auto;border-width: 0.666667px;border-color: rgb(191, 191, 191);vertical-align: top;"><span style="font-size: 14px;color: rgb(84, 84, 84);">Microsoft - Windows</span></td></tr><tr style="outline: 0px;height: 27px;"><td width="119" colspan="1" rowspan="1" style="padding: 0px 7.2px;outline: 0px;word-break: break-all;hyphens: auto;border-width: 0.666667px;border-color: rgb(191, 191, 191);vertical-align: top;"><p style="outline: 0px;"><span style="color: rgb(84, 84, 84);"><strong style="outline: 0px;"><span style="outline: 0px;letter-spacing: 1px;font-size: 14px;">受影响版本</span></strong></span><o:p style="outline: 0px;"></o:p></p></td><td width="324" colspan="1" rowspan="1" style="padding: 0px 7.2px;outline: 0px;word-break: break-all;hyphens: auto;border-width: 0.666667px;border-color: rgb(191, 191, 191);vertical-align: top;"><p><span style="color: rgb(84, 84, 84);font-size: 14px;letter-spacing: normal;">Windows Server 2012 R2 (Server Core installation)</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);letter-spacing: normal;">Windows Server 2012 R2</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);letter-spacing: normal;">Windows Server 2012 (Server Core installation)</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);letter-spacing: normal;">Windows Server 2012</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);letter-spacing: normal;">Windows Server 2008 R2 for x64-based Systems Service Pack 1 (Server Core installation)</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);letter-spacing: normal;">Windows Server 2008 R2 for x64-based Systems Service Pack 1</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);letter-spacing: normal;">Windows Server 2008 for x64-based Systems Service Pack 2 (Server Core installation)</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);letter-spacing: normal;">Windows Server 2008 for x64-based Systems Service Pack 2</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);letter-spacing: normal;">Windows Server 2008 for 32-bit Systems Service Pack 2 (Server Core installation)</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);letter-spacing: normal;">Windows Server 2008 for 32-bit Systems Service Pack 2</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);letter-spacing: normal;">Windows Server 2016 (Server Core installation)</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);letter-spacing: normal;">Windows Server 2016</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);letter-spacing: normal;">Windows 10 Version 1607 for x64-based Systems</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);letter-spacing: normal;">Windows 10 Version 1607 for 32-bit Systems</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);letter-spacing: normal;">Windows 10 for x64-based Systems</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);letter-spacing: normal;">Windows 10 for 32-bit Systems</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);letter-spacing: normal;">Windows Server 2025</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);letter-spacing: normal;">Windows 11 Version 24H2 for x64-based Systems</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);letter-spacing: normal;">Windows 11 Version 24H2 for ARM64-based Systems</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);letter-spacing: normal;">Windows Server 2022, 23H2 Edition (Server Core installation)</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);letter-spacing: normal;">Windows 11 Version 23H2 for x64-based Systems</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);letter-spacing: normal;">Windows 11 Version 23H2 for ARM64-based Systems</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);letter-spacing: normal;">Windows Server 2025 (Server Core installation)</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);letter-spacing: normal;">Windows 10 Version 22H2 for 32-bit Systems</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);letter-spacing: normal;">Windows 10 Version 22H2 for ARM64-based Systems</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);letter-spacing: normal;">Windows 10 Version 22H2 for x64-based Systems</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);letter-spacing: normal;">Windows 11 Version 22H2 for x64-based Systems</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);letter-spacing: normal;">Windows 11 Version 22H2 for ARM64-based Systems</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);letter-spacing: normal;">Windows 10 Version 21H2 for x64-based Systems</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);letter-spacing: normal;">Windows 10 Version 21H2 for ARM64-based Systems</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);letter-spacing: normal;">Windows 10 Version 21H2 for 32-bit Systems</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);letter-spacing: normal;">Windows Server 2022 (Server Core installation)</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);letter-spacing: normal;">Windows Server 2022</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);letter-spacing: normal;">Windows Server 2019 (Server Core installation)</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);letter-spacing: normal;">Windows Server 2019</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);letter-spacing: normal;">Windows 10 Version 1809 for x64-based Systems</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);letter-spacing: normal;">Windows 10 Version 1809 for 32-bit Systems</span></p><p><span style="font-size: 14px;color: rgb(84, 84, 84);"></span></p></td></tr><tr style="outline: 0px;height: 35.6px;"><td width="139" colspan="1" rowspan="1" style="padding: 0px 7.2px;outline: 0px;word-break: break-all;hyphens: auto;border-width: 0.666667px;border-color: rgb(191, 191, 191);vertical-align: top;"><p style="outline: 0px;"><strong style="outline: 0px;"><span style="outline: 0px;color: rgb(84, 84, 84);letter-spacing: 1px;font-size: 14px;">有无修复补丁</span></strong><o:p style="outline: 0px;"></o:p></p></td><td width="324" colspan="1" rowspan="1" style="padding: 0px 7.2px;outline: 0px;word-break: break-all;hyphens: auto;border-width: 0.666667px;border-color: rgb(191, 191, 191);vertical-align: top;"><span style="letter-spacing: 0.578px;font-size: 14px;color: rgb(84, 84, 84);">有</span></td></tr></tbody></table>  
  
**漏洞复现**  
  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/fFyp1gWjicMKHEegm8960F5FG7fN9pr9zsNVL9LqYD0iclCibF09zC09Wv32bDuu6z5KwY1pibFicl99ze2KiaU8OVTQ/640?wx_fmt=png&from=appmsg "")  
  
  
**修复方案**  
  
  
  
  
**官方修复方案：**  
  
微软官方已发布漏洞公告，请尽快前往下载更新补丁：  
  
https://msrc.microsoft.com/update-guide/vulnerability/CVE-2024-49113  
  
## 临时修复方案：  
- 使用流量设备监控可疑的DNS SRV查询，对可疑查询进行防护。  
  
- 使用终端设备监控可疑的DsrGetDcNameEx2 RPC调用，对可疑RPC调用进行防护。  
  
- 根据微软官方漏洞通告，尽快安装对应的补丁。  
  
**微步产品侧支持情况**  
  
  
  
微步威胁感知平台TDP 已支持检测，  
检测ID：S3100158089  
，模型/规则高于20250106000000可检出。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/fFyp1gWjicMKHEegm8960F5FG7fN9pr9z5uunQvIb7qLCGYJdqNJHiabap5XlORmZ6kaAyMjLJpBCSRPib19fuGNA/640?wx_fmt=png&from=appmsg "")  
  
  
  
- END -  
  
  
  //    
  
**微步漏洞情报订阅服务**  
  
  
微步提供漏洞情报订阅服务，精准、高效助力企业漏洞运营：  
- 提供高价值漏洞情报，具备及时、准确、全面和可操作性，帮助企业高效应对漏洞应急与日常运营难题；  
  
- 可实现对高威胁漏洞提前掌握，以最快的效率解决信息差问题，缩短漏洞运营MTTR；  
  
- 提供漏洞完整的技术细节，更贴近用户漏洞处置的落地；  
  
- 将漏洞与威胁事件库、APT组织和黑产团伙攻击大数据、网络空间测绘等结合，对漏洞的实际风险进行持续动态更新。  
  
  
扫码在线沟通  
  
↓  
↓↓  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Yv6ic9zgr5hQl5bZ5Mx6PTAQg6tGLiciarvXajTdDnQiacxmwJFZ0D3ictBOmuYyRk99bibwZV49wbap77LibGQHdQPtA/640?wx_fmt=png "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Yv6ic9zgr5hTIdM9koHZFkrtYe5WU5rHxSDicbiaNFjEBAs1rojKGviaJGjOGd9KwKzN4aSpnNZDA5UWpY2E0JAnNg/640?wx_fmt=png "")  
  
[点此电话咨询]()  
  
  
  
  
**X漏洞奖励计划**  
  
  
“X漏洞奖励计划”是微步X情报社区推出的一款  
针对未公开  
漏洞的奖励计划，我们鼓励白帽子提交挖掘到的0day漏洞，并给予白帽子可观的奖励。我们期望通过该计划与白帽子共同努力，提升0day防御能力，守护数字世界安全。  
  
活动详情：  
https://x.threatbook.com/v5/vulReward  
  
  
  

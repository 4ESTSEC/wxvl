#  思科 SD-WAN 严重漏洞允许远程攻击者绕过管理员身份验证  
原创 ZM
                    ZM  暗镜   2026-10-05 22:00  
  
该漏洞编号为 CVE-2026-76504，CVSS v3.1 评分为 9.8，无论Cisco Catalyst SD-WAN Manager 的配置如何，都会受到影响。  
  
2026 年 9 月 30 日，思科发布了公告cisco-sa-sdwan-webauth-xr8beuuU，指出该问题是基于会话的 API 身份验证管理中的一个缺陷。  
  
该公司已分配内部漏洞 ID CSCww79570，并将该弱点归类为 CWE-177，原因是 URL 编码处理不当。  
  
该漏洞源于对 HTTP 请求中 URI 编码字符的处理不当。攻击者可以向 Catalyst SD-WAN Manager API 发送特制的请求，绕过旨在保护特定端点的身份验证规则。  
  
如果成功，攻击者可以利用此漏洞，在无需有效凭据的情况下，通过网络访问权限以管理员身份进行身份验证。值得注意的是，此漏洞利用无需任何特权或用户交互，因此被评为严重级别。  
  
Cisco 的安全公告通过一个示例请求来说明该问题，该请求在 j_security_check 端点中包含一个编码字符，例如：  
```
POST /%6a_security_check HTTP/1.1
```  
  
在这种情况下，%6a 代表字母“j”。思科强调这只是一个例子；在易受攻击的请求中对单个字符进行编码就可能允许绕过身份验证。  
  
管理员应检查 `/var/log/nms/containers/service-proxy/serviceproxy-access.log` 文件，查找来自未知或未经授权的 IP 地址的涉及 j_security_check 的请求。  
  
他们还应该查看 `/var/log/nms/vmanage-server.log` 中与以“viptela-reserved-”开头的帐户关联的 j_security_check 活动，这可能表明尝试或成功绕过。  
  
在修复软件部署完成之前，思科建议本地部署客户尽可能避免网络暴露，并将管理系统访问权限限制在已知且可信的主机上。此外，企业还应将 SD-WAN 控制组件置于防火墙后，并明确过滤入站和出站流量。  
  
如果怀疑系统遭到入侵，思科建议使用 `request admin-tech` 命令收集管理技术包，然后再提交一份包含 CVE-2026-76504 的严重性 3 级思科 TAC 案例。  
  
  

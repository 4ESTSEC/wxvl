#  Dell System Update 工具曝严重漏洞，攻击者可借此以 root 身份执行代码  
 网安百色   2026-10-07 10:19  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/WibvcdjxgJnuoiaWDJAlbQHpQC6ybDC89obTV1okE3N9ghh5Yzc9bEppPzr84YibjXFdvnibxywucLjFib6VIiaswE4nrOuw7U0fStSE7n9GAuWuA/640?wx_fmt=png&from=appmsg "")  
  
Dell 已为 Dell System Update（DSU）中的五个漏洞发布安全更新，其中包含一个严重级别缺陷：未认证远程攻击者可借此以 root 权限执行代码。这些问题影响 2.3.0.0 之前的版本，Dell 敦促客户尽早升级。  
  
于 2026 年 10 月 1 日发布安全公告 DSA-2026-324。DSU 是一款命令行工具，可帮助管理员在运行 Linux 和 Windows 的 Dell PowerEdge 服务器上部署 BIOS、固件及软件更新，其安全性对企业服务器管理至关重要。  
#### 严重级路径遍历漏洞  
  
最严重的问题是 CVE-2026-86360，CVSS 评分为 9.6。Dell 将其定性为路径遍历漏洞，即该工具未能将文件访问限制在预期目录内。拥有远程访问权限的攻击者无需先登录受影响系统即可利用该弱点。  
  
Dell 警告称，利用此漏洞可能获得文件系统访问权限，并允许以 root 权限执行任意代码。攻击成功后，DSU 与底层操作系统均可能被完全攻陷。不过，公布的 CVSS 向量显示需要用户交互（user interaction required），这一细节十分关键：它把“无需认证即可访问”与“完全不需要用户参与的攻击”区分开来。  
  
公告并未描述所需的具体用户操作、易受攻击的文件路径或完整的攻击步骤序列。这些限制条件很重要：警告本身确立了风险的严重性，但提供的细节不足以还原出可利用的攻击过程。  
#### 另外四个安全缺陷  
  
另有两个漏洞可能让权限受限的本地用户获取更高权限。CVE-2026-86361 涉及关键资源被赋予了错误的权限，CVE-2026-86362 则源于访问控制不当。两者的 CVSS 评分均为 8.2，且都需要本地访问权限，这与上述严重的远程路径遍历漏洞不同。  
  
CVSS 评分为 7.6 的 CVE-2026-63697 涉及证书验证不当。Dell 表示，已经拥有高权限的远程攻击者可利用它实现远程执行。CVSS 评分为 7.3 的 CVE-2026-71168 是另一个路径遍历漏洞，其前提条件被描述为“低权限本地访问”，但 Dell 同时将其潜在后果表述为远程执行。  
#### 修复建议  
  
将所有五个漏洞统一升级至 DSU 2.3.0.0 或更高版本。管理员应排查低于该版本的安装实例，并通过公告中链接的 Dell 官方下载渠道获取修正版本。安装早于 2.3.0.0 的 DSU 版本不符合 Dell 声明的修复要求。  
  
此次披露紧随 Cyber Security News 报道的另一组 Dell Container Storage 严重漏洞之后，这为 Dell 环境增加了又一项更新优先级。两者属于不同产品，需要分别进行修复。在 10 月 5 日发布的报告中，Dell 并未将 DSU 的这几个漏洞标记为在野利用，但客户仍不应推迟打补丁。  
  
本公众号所载文章为本公众号原创或根据网络搜索下载编辑整理，文章版权归原作者所有，仅供读者学习、参考，禁止用于商业用途。因转载众多，无法找到真正来源，如标错来源，或对于文中所使用的图片、文字、链接中所包含的软件/资料等，如有侵权，请跟我们联系删除，谢谢！  
  
![图片](https://mmbiz.qpic.cn/mmbiz_jpg/1QIbxKfhZo5lNbibXUkeIxDGJmD2Md5vKicbNtIkdNvibicL87FjAOqGicuxcgBuRjjolLcGDOnfhMdykXibWuH6DV1g/640?wx_fmt=other&from=appmsg&wxfrom=5&wx_lazy=1&wx_co=1&randomid=p6hk1x4r&tp=webp#imgIndex=1 "")  
  

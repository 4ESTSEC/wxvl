#  WordPress插件漏洞遭大规模武器化  
 网安百色   2026-07-11 10:41  
  
![](https://mmbiz.qpic.cn/mmbiz_png/WibvcdjxgJnvk3DVlm8QribhXqH2RnKDIagdCa6UQzNwpRfq6G1SvAYyG1iceXdqiaEtrw63mzqh4fPibr1cIvFjNFGfnd3b1ZYnUialNOvZVyU0U/640?wx_fmt=png&from=appmsg "")  
  
大规模漏洞利用活动正积极武器化多个内容管理系统中的已知漏洞，其中**WordPress插件构成主要攻击面**  
。  
  
网络攻击者正扫描互联网以定位存在漏洞的站点，并**链式利用未授权文件上传、远程代码执行（RCE）、服务器端请求伪造（SSRF）及反序列化漏洞**  
，部署可获取持久化远程访问权限的webshell。  
  
包括澳大利亚中小企业在内的全球组织已遭受实际影响，威胁行为者正快速将漏洞披露转化为实际入侵。  
  
一旦建立webshell，攻击者即可远程执行命令、横向渗透至内网、部署额外恶意软件、窃取数据及收集站点用户输入的凭证。  
  
**应急响应必须默认假设系统已遭入侵**  
。澳大利亚网络安全中心（ACSC）指导明确要求：检查Web目录中的异常文件（尤其是插件文件夹内）。  
  
审查Web访问日志中针对典型webshell路径的可疑GET或POST请求；对发现webshell的服务器，应视为已遭入侵——**立即隔离、保留日志，并全面审计认证与网络活动记录**  
。  
  
攻击者已将受控站点用于篡改页面、托管钓鱼页面，或作为进一步入侵的跳板。本次攻击活动的速度与规模印证了五眼联盟网络安全机构负责人的警告。  
  
调查人员应追溯初始入侵事件、排查持久化机制，并搜索横向移动与数据外泄迹象。  
## WordPress插件漏洞  
  
若存在可信备份应优先恢复，**在服务恢复前清除已识别的恶意代码及持久化载体**  
。  
<table><thead><tr style="-webkit-font-smoothing: antialiased;"><th style="-webkit-font-smoothing: antialiased;"><span data-spm-anchor-id="5176.28103460.0.i11.39f22988u98hia" style="-webkit-font-smoothing: antialiased;"><span leaf="">软件/插件</span></span></th><th style="-webkit-font-smoothing: antialiased;"><span style="-webkit-font-smoothing: antialiased;"><span leaf="">CVE编号</span></span></th></tr></thead><tbody><tr style="-webkit-font-smoothing: antialiased;"><td style="-webkit-font-smoothing: antialiased;"><span style="-webkit-font-smoothing: antialiased;"><span leaf="">Simple File List (WordPress)</span></span></td><td style="-webkit-font-smoothing: antialiased;"><span style="-webkit-font-smoothing: antialiased;"><span leaf="">CVE-2025-34085/CVE-2020-36847</span></span></td></tr><tr style="-webkit-font-smoothing: antialiased;"><td style="-webkit-font-smoothing: antialiased;"><span style="-webkit-font-smoothing: antialiased;"><span leaf="">WavePlayer (WordPress)</span></span></td><td style="-webkit-font-smoothing: antialiased;"><span style="-webkit-font-smoothing: antialiased;"><span leaf="">CVE-2025-12057</span></span></td></tr><tr style="-webkit-font-smoothing: antialiased;"><td style="-webkit-font-smoothing: antialiased;"><span style="-webkit-font-smoothing: antialiased;"><span leaf="">BerqWP (WordPress)</span></span></td><td style="-webkit-font-smoothing: antialiased;"><span style="-webkit-font-smoothing: antialiased;"><span leaf="">CVE-2025-7443</span></span></td></tr><tr style="-webkit-font-smoothing: antialiased;"><td style="-webkit-font-smoothing: antialiased;"><span style="-webkit-font-smoothing: antialiased;"><span leaf="">WPBookit (WordPress)</span></span></td><td style="-webkit-font-smoothing: antialiased;"><span style="-webkit-font-smoothing: antialiased;"><span leaf="">CVE-2025-7852</span></span></td></tr><tr style="-webkit-font-smoothing: antialiased;"><td style="-webkit-font-smoothing: antialiased;"><span style="-webkit-font-smoothing: antialiased;"><span leaf="">Ninja Forms (WordPress)</span></span></td><td style="-webkit-font-smoothing: antialiased;"><span style="-webkit-font-smoothing: antialiased;"><span leaf="">CVE-2026-0740</span></span></td></tr><tr style="-webkit-font-smoothing: antialiased;"><td style="-webkit-font-smoothing: antialiased;"><span style="-webkit-font-smoothing: antialiased;"><span leaf="">ThemeREX Addons (WordPress)</span></span></td><td style="-webkit-font-smoothing: antialiased;"><span style="-webkit-font-smoothing: antialiased;"><span leaf="">CVE-2026-1969</span></span></td></tr><tr style="-webkit-font-smoothing: antialiased;"><td style="-webkit-font-smoothing: antialiased;"><span style="-webkit-font-smoothing: antialiased;"><span leaf="">Breeze Cache (WordPress)</span></span></td><td style="-webkit-font-smoothing: antialiased;"><span style="-webkit-font-smoothing: antialiased;"><span leaf="">CVE-2026-3844</span></span></td></tr><tr style="-webkit-font-smoothing: antialiased;"><td style="-webkit-font-smoothing: antialiased;"><span style="-webkit-font-smoothing: antialiased;"><span leaf="">pay-uz (WordPress)</span></span></td><td style="-webkit-font-smoothing: antialiased;"><span style="-webkit-font-smoothing: antialiased;"><span leaf="">CVE-2026-31843</span></span></td></tr><tr style="-webkit-font-smoothing: antialiased;"><td style="-webkit-font-smoothing: antialiased;"><span style="-webkit-font-smoothing: antialiased;"><span leaf="">ACF Extended (WordPress)</span></span></td><td style="-webkit-font-smoothing: antialiased;"><span style="-webkit-font-smoothing: antialiased;"><span leaf="">CVE-2025-13486</span></span></td></tr><tr style="-webkit-font-smoothing: antialiased;"><td style="-webkit-font-smoothing: antialiased;"><span style="-webkit-font-smoothing: antialiased;"><span leaf="">Sneeit Framework</span></span></td><td style="-webkit-font-smoothing: antialiased;"><span style="-webkit-font-smoothing: antialiased;"><span leaf="">CVE-2025-6389</span></span></td></tr><tr style="-webkit-font-smoothing: antialiased;"><td style="-webkit-font-smoothing: antialiased;"><span style="-webkit-font-smoothing: antialiased;"><span leaf="">WPvivid Backup (WordPress)</span></span></td><td style="-webkit-font-smoothing: antialiased;"><span style="-webkit-font-smoothing: antialiased;"><span leaf="">CVE-2026-1357</span></span></td></tr><tr style="-webkit-font-smoothing: antialiased;"><td style="-webkit-font-smoothing: antialiased;"><span style="-webkit-font-smoothing: antialiased;"><span leaf="">Gravity Forms (WordPress)</span></span></td><td style="-webkit-font-smoothing: antialiased;"><span style="-webkit-font-smoothing: antialiased;"><span leaf="">CVE-2025-12352</span></span></td></tr><tr style="-webkit-font-smoothing: antialiased;"><td style="-webkit-font-smoothing: antialiased;"><span style="-webkit-font-smoothing: antialiased;"><span leaf="">GutenKit/Hunk Companion (WordPress)</span></span></td><td style="-webkit-font-smoothing: antialiased;"><span style="-webkit-font-smoothing: antialiased;"><span leaf="">Likely CVE-2024-9234</span></span></td></tr><tr style="-webkit-font-smoothing: antialiased;"><td style="-webkit-font-smoothing: antialiased;"><span style="-webkit-font-smoothing: antialiased;"><span leaf="">Craft CMS</span></span></td><td style="-webkit-font-smoothing: antialiased;"><span style="-webkit-font-smoothing: antialiased;"><span leaf="">CVE-2025-32432</span></span></td></tr><tr style="-webkit-font-smoothing: antialiased;"><td style="-webkit-font-smoothing: antialiased;"><span style="-webkit-font-smoothing: antialiased;"><span leaf="">MaxSite CMS</span></span></td><td style="-webkit-font-smoothing: antialiased;"><span style="-webkit-font-smoothing: antialiased;"><span leaf="">CVE-2026-3395</span></span></td></tr><tr style="-webkit-font-smoothing: antialiased;"><td style="-webkit-font-smoothing: antialiased;"><span style="-webkit-font-smoothing: antialiased;"><span leaf="">MetInfo CMS</span></span></td><td style="-webkit-font-smoothing: antialiased;"><span style="-webkit-font-smoothing: antialiased;"><span leaf="">CVE-2026-29014</span></span></td></tr><tr style="-webkit-font-smoothing: antialiased;"><td style="-webkit-font-smoothing: antialiased;"><span style="-webkit-font-smoothing: antialiased;"><span leaf="">Joomla JCE</span></span></td><td style="-webkit-font-smoothing: antialiased;"><span style="-webkit-font-smoothing: antialiased;"><span leaf="">CVE-2026-48907</span></span></td></tr></tbody></table>  
预防性措施依然直接有效：**立即应用供应商补丁**  
（这些公开CVE均有可用修复方案），并在可管理回滚的情况下考虑自动打补丁。  
  
托管面向公众的CMS实例的组织应**优先识别受影响组件、加速补丁部署**  
，并将任何无法解释的Web活动视为潜在恶意行为。  
  
**在修复前禁用或移除存在活跃利用的插件**  
。通过以下方式强化Web服务器安全：  
- 在可行情况下将Web目录设为只读  
- 限制文件与路径访问权限  
- 监控或拦截未经批准的文件创建  
实施进程监控以检测Web服务器二进制文件生成的异常子进程，并考虑通过应用控制限制互联网暴露主机的可执行进程。  
  
最后，**隔离并限制Web服务器与内部系统间的网络通信**  
，以降低受感染站点的影响范围。  
  
本公众号所载文章为本公众号原创或根据网络搜索下载编辑整理，文章版权归原作者所有，仅供读者学习、参考，禁止用于商业用途。因转载众多，无法找到真正来源，如标错来源，或对于文中所使用的图片、文字、链接中所包含的软件/资料等，如有侵权，请跟我们联系删除，谢谢！  
  
![图片](https://mmbiz.qpic.cn/mmbiz_jpg/1QIbxKfhZo5lNbibXUkeIxDGJmD2Md5vKicbNtIkdNvibicL87FjAOqGicuxcgBuRjjolLcGDOnfhMdykXibWuH6DV1g/640?wx_fmt=other&from=appmsg&wxfrom=5&wx_lazy=1&wx_co=1&randomid=p6hk1x4r&tp=webp#imgIndex=1 "")  
  

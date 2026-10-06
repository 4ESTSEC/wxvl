#  Dell System Update 5个漏洞：最高严重度9.6，可被用于root权限代码执行  
原创 播风者
                    播风者  黑白之道   2026-10-06 01:32  
  
![](https://mmbiz.qpic.cn/mmbiz_png/nGzNudUIJ6P6OHEOELpyy87BKic1yvcpOkyLicAGDBfXHjCQQo7ibiceyUs0l4fp3zzMSSXEXfcPJ74mcbJ6LyI5HicR5WiaCZuQLZpsE2RMTpx1U/640?from=appmsg "")  
> **导语**  
：Dell于2026年10月1日紧急修复Dell System Update（DSU）命令行工具中的5个安全漏洞。其中最严重的路径遍历漏洞（CVE-2026-86360，CVSS 9.6）可被未授权远程攻击者利用，以root权限在受影响服务器上执行任意代码。CISA已要求联邦机构在规定期限内完成修补。  
  
## 一、漏洞概述  
  
Dell System Update（DSU）是Dell官方的系统更新工具，用于向PowerEdge服务器推送固件和驱动更新。10月1日，Dell集中修复了DSU中的5个安全漏洞，涵盖远程代码执行（RCE）和权限提升两类风险。  
  
**本次修复的核心漏洞：**  
<table><thead><tr><th style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;font-weight: bold;background-color: rgb(255, 249, 249);color: rgb(239, 112, 96);"><section><span leaf="">CVE编号</span></section></th><th style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;font-weight: bold;background-color: rgb(255, 249, 249);color: rgb(239, 112, 96);"><section><span leaf="">类型</span></section></th><th style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;font-weight: bold;background-color: rgb(255, 249, 249);color: rgb(239, 112, 96);"><section><span leaf="">CVSS</span></section></th><th style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;font-weight: bold;background-color: rgb(255, 249, 249);color: rgb(239, 112, 96);"><section><span leaf="">说明</span></section></th></tr></thead><tbody><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><strong><span leaf="">CVE-2026-86360</span></strong></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">路径遍历</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><strong><span leaf="">9.6</span></strong></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">未授权远程攻击者可通过路径遍历以root权限执行任意代码</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">CVE-2026-63697</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">远程代码执行</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">高</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">远程攻击者可实现代码执行</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">CVE-2026-71168</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">远程代码执行</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">高</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">远程攻击者可实现代码执行</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">CVE-2026-86361</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">权限提升</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">高</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">本地攻击者可提升权限</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">CVE-2026-86362</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">权限提升</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">高</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">本地攻击者可提升权限</span></section></td></tr></tbody></table>  
其中，CVE-2026-86360被Dell归类为"不当限制路径名到受限目录"（Improper Limitation of a Pathname to a Restricted Directory），即经典的路径遍历漏洞。  
  
![Dell DSU漏洞影响概览](https://mmbiz.qpic.cn/sz_mmbiz_png/nGzNudUIJ6Or6Y2dlpThGuX1KdHaficEoLT6Ty61Z5rOYZXNs1Gx48ZhsqKXN8axG5D4UpdOpCicb3QlXDYYtiaKjhBPNcicsnjBHFEbZg37YY4/640?from=appmsg "Dell DSU漏洞影响概览")  
## 二、风险分析  
### 2.1 最高危漏洞：CVE-2026-86360  
  
CVE-2026-86360是本次披露中最危险的漏洞，具体特征如下：  
- **攻击前提**  
：攻击者需拥有远程访问能力（未要求认证）  
  
- **危害结果**  
：文件系统访问权限，可进一步以root身份执行任意代码  
  
- **影响范围**  
：使用Dell System Update的PowerEdge服务器  
  
- **利用难度**  
：低，路径遍历为常见漏洞类型，利用成熟度高  
  
- **已知利用状态**  
：目前尚无已知的在野利用报告  
  
### 2.2 其他高危漏洞  
  
CVE-2026-63697和CVE-2026-71168两个RCE漏洞同样值得高度关注。虽然CVSS评分略低于CVE-2026-86360，但远程代码执行类漏洞一旦被利用，可直接接管服务器，危害极大。  
  
CVE-2026-86361和CVE-2026-86362属于本地权限提升漏洞，适用于已经获得服务器部分访问权限的攻击者进行横向移动。  
## 三、修复方案  
### 3.1 官方补丁  
  
Dell已发布DSU **2.3.0.0**  
版本修复上述所有漏洞。管理员应立即升级到此版本。  
  
**补丁下载地址**  
（Dell支持官网）：需前往Dell技术支持页面，根据服务器型号获取对应版本的DSU安装包。  
  
**⚠️ 注意**  
：更新DSU可能需要重启相关服务，建议在维护窗口内操作，并提前评估对业务连续性的影响。  
### 3.2 临时规避措施  
  
在无法立即安装补丁的情况下，可采取以下临时措施降低风险：  
- **限制DSU端口访问**  
：确保只有受信任的管理网段可以访问DSU相关端口  
  
- **启用Dell UEFI安全启动**  
：防止恶意驱动在系统启动阶段加载  
  
- **监控异常登录行为**  
：重点关注管理接口的异常认证尝试  
  
- **最小化暴露面**  
：如无需DSU远程管理功能，建议暂时禁用  
  
### 3.3 CISA修补令  
  
美国网络安全和基础设施安全局（CISA）已将此漏洞列入已知被利用漏洞（KEV）目录，并正式要求联邦民事执行机构（FCEB）在规定期限内完成修补。各企业客户也应参照此标准尽快处理。  
## 四、总结建议  
  
本次Dell披露的DSU漏洞整体严重程度极高，尤其是CVSS 9.6的路径遍历漏洞，在无需任何凭证的情况下即可实现root代码执行，对数据中心环境威胁显著。  
  
**行动清单**  
：  
1. 立即盘点所有运行DSU的PowerEdge服务器，确认当前版本  
  
1. 尽快升级至DSU 2.3.0.0版本  
  
1. 如暂时无法升级，先行实施网络层面访问控制  
  
1. 检查CISA KEV目录，确认是否在联邦机构修补范围内  
  
1. 关注Dell后续安全公告，防范变种利用  
  
**版权声明**  
：本文由华盟网原创发布，保留所有权利。配图由华盟网授权使用。  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/nGzNudUIJ6PQoiaZ1WWWM8ce4JegSYlA05HnWibulqVkODEm1UsksgkYahIJXIBU3LIaj2QGEgJkowju1NywEOyhwl4MwXHjib6SibqtermFzrw/640?from=appmsg "")  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650621549&idx=1&sn=21c4b072726d2387d562109ada6b9bbb&scene=21#wechat_redirect)  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650621944&idx=1&sn=3cc6dc9876a20466d4ec3634deb80220&scene=21#wechat_redirect)  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650622065&idx=1&sn=09f8ae84c06d4331e71c277b177ae701&scene=21#wechat_redirect)  
> 👇 点击**阅读原文**  
，访问我的网站  
  
  

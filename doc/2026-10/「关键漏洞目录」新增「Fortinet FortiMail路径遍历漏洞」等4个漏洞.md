#  「关键漏洞目录」新增「Fortinet FortiMail路径遍历漏洞」等4个漏洞  
摄星科技
                    摄星科技  关键漏洞目录   2026-10-08 08:26  
  
![关键漏洞目录 Logo](https://mmbiz.qpic.cn/sz_mmbiz_png/0D2ibczlp7VicfImmGiaz8Yya6za8VLz15D2Rr9cLYwz8H0tG6bylC6Pag8L438Sxiagh09E8oejyHbCEpIwGnn8ibkksMvpZFr0LaDTGxbxibcX8/640?from=appmsg "")  
  
摄星科技  
# 关键漏洞目录  
  
新增 4 个漏洞  ·  2026-10-08  
  
01  
## Fortinet FortiMail路径遍历漏洞  
  
  
**CVE-2026-104286**  
超危  
  
**公开日期**  
：2026-10-02 03:17:38  
  
CVSS 3.1：**9.8**  
  
AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H/E:P/RL:O/RC:C  
  
远程  
无需认证  
公开PoC  
CISA KEV  
关键漏洞  
CWE Top 25 (2023)  
CWE Top 25 (2024)  
  
**漏洞描述**  
  
Fortinet FortiMail包含路径遍历和NULL字节或NULL字符无效漏洞，可能允许未经身份验证的攻击者通过精心编制的HTTP或HTTPS请求在底层系统上写入任意文件。  
  
**修复建议**  
  
升级到FortiMail 8.0.1、7.6.6、7.4.8、7.2.10或更高版本，参考链接：  
  
https://fortiguard.fortinet.com/psirt/FG-IR-26-175  
  
02  
## Zammad GmbH Zammad权限提升漏洞  
  
  
**CVE-2026-102490**  
超危  
  
**公开日期**  
：2026-10-01 00:21:20  
  
CVSS 4.0：**9.4**  
  
AV:N/AC:L/AT:N/PR:N/UI:P/VC:H/VI:H/VA:H/SC:H/SI:H/SA:H/E:A/AU:Y/V:C  
  
远程  
无需认证  
公开PoC  
CISA KEV  
关键漏洞  
漏洞攻击链  
智能分析漏洞  
CWE Top 25 (2023)  
CWE Top 25 (2024)  
  
**漏洞描述**  
  
Zammad 1.5.0至7.1.0-alpha等版本存在不当的权限管理漏洞，可能导致本地zammad用户提升权限至root。此漏洞与CVE-2026-102489形成漏洞攻击链。  
  
**修复建议**  
  
Zammad 6.5及以下版本已EOL，无官方安全补丁。请升级至Zammad 7.2.0或更高版本，参考链接：  
  
https://community.zammad.org/t/take-care-local-privilege-escalation-cve-2026-102490-is-reported-as-being-actively-exploited/21297/2  
  
https://zammad.com/en/product/releases/  
  
https://github.com/zammad/zammad/tags  
  
03  
## Zammad GmbH Zammad会话劫持漏洞  
  
  
**CVE-2026-102489**  
超危  
  
**公开日期**  
：2026-10-01 00:21:19  
  
CVSS 4.0：**9.4**  
  
AV:N/AC:L/AT:N/PR:N/UI:P/VC:H/VI:H/VA:H/SC:H/SI:H/SA:H/E:A/AU:Y/V:C  
  
远程  
无需认证  
公开PoC  
CISA KEV  
关键漏洞  
漏洞攻击链  
智能分析漏洞  
  
**漏洞描述**  
  
Zammad 6.3.0至6.5.4版本存在会话劫持问题，攻击者可在获得受害者会话后以zammad用户身份执行代码；7.0.0至7.1.3版本记录存在相同问题但由于底层框架的更改而无法利用。此漏洞与CVE-2026-102490形成漏洞攻击链。  
  
**修复建议**  
  
Zammad 6.5及以下版本已EOL，无官方安全补丁。请升级至Zammad 7.2.0或更高版本，参考链接：  
  
https://community.zammad.org/t/take-care-local-privilege-escalation-cve-2026-102490-is-reported-as-being-actively-exploited/21297/2  
  
https://zammad.com/en/product/releases/  
  
04  
## Citrix NetScaler ADC和Gateway拒绝服务漏洞  
  
  
**CVE-2026-88779**  
高危  
  
**公开日期**  
：2026-10-04 10:35:35  
  
CVSS 4.0：**8.7**  
  
AV:N/AC:L/AT:N/PR:N/UI:N/VC:N/VI:N/VA:H/SC:N/SI:N/SA:N  
  
远程  
无需认证  
公开PoC  
CISA KEV  
关键漏洞  
CWE Top 25 (2023)  
CWE Top 25 (2024)  
  
**漏洞描述**  
  
Citrix NetScaler ADC 14.1-73.41之前版本、13.1-64.28之前版本、14.1-73.41 FIPS之前版本以及13.1-37.282之前版本，NetScaler Gateway 14.1-73.41之前和13.1-64.28.之前版本存在内存缓冲区范围内的操作限制不当漏洞，可能导致拒绝服务攻击。设备必须配置为SAML服务提供方（SP）或身份提供方（IdP），即存在add authentication samlAction或add authentication samlIdPProfile配置项的Gateway或AAA虚拟服务器才会受影响。  
  
**修复建议**  
  
Citrix已发布针对此漏洞的修复程序，参考链接：  
  
https://support.citrix.com/support-home/kbsearch/article?articleNumber=CTX697174  
  
https://community.citrix.com/techzone-blogs/110_security-updates/understanding-and-addressing-cve-2026-88779-in-citrix-netscaler-adc-and-citrix-netscaler-gateway/  
  
**服务和支持**  
  
“关键漏洞目录”由摄星科技维护，可通过 https://www.vulinsight.com.cn/keyFlawCatalog 查看详细内容。并可下载 CSV、JSON 格式全量漏洞数据。  
  
企业用户可通过玄猫定制化情报 SaaS 平台（xm.vulinsight.com.cn）、摄星漏洞管控平台获取服务和支持。  
  

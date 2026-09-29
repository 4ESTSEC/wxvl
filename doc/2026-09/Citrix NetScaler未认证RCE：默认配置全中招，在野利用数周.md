#  Citrix NetScaler未认证RCE：默认配置全中招，在野利用数周  
零日手记
                    零日手记  随笔漫记安全路   2026-09-29 08:44  
  
9月27日，Citrix发布紧急安全公告，NetScaler ADC和NetScaler Gateway存在两个已被在野利用的未认证RCE漏洞——CVE-2026-88771和CVE-2026-88772。其中CVE-2026-88771影响**所有默认配置**  
的部署，不需要额外功能组件。  
  
CVSS 9.8。CISA当天加入KEV目录，修复截止日期9月30日——只给3天。  
  
watchTowr在9月26日率先披露了"两个未修复的NetScaler RCE零日正在被利用"，部分管理员已将设备下线。PoC已公开。  
  
**漏洞详情**  
  
CVE-2026-88771：输入验证不当（CWE-20），未认证攻击者可执行任意命令。**默认配置即受影响，不需要额外功能。**  
  
CVE-2026-88772：同样未认证RCE，在野利用。  
  
两个漏洞都是在修复公开前就被攻击了——零日利用。Citrix没有说明被利用了多久、多广泛、攻击者是谁。  
  
**影响版本：**  
<table><thead><tr><th style="font-size: 0.75em; padding: 9px 12px; line-height: 22px; color: rgb(34, 34, 34); border: 1px solid rgb(216, 216, 216); vertical-align: top; font-weight: bold; background-color: rgb(240, 240, 240);"><section><span leaf="">产品</span></section></th><th style="font-size: 0.75em; padding: 9px 12px; line-height: 22px; color: rgb(34, 34, 34); border: 1px solid rgb(216, 216, 216); vertical-align: top; font-weight: bold; background-color: rgb(240, 240, 240);"><section><span leaf="">受影响版本</span></section></th><th style="font-size: 0.75em; padding: 9px 12px; line-height: 22px; color: rgb(34, 34, 34); border: 1px solid rgb(216, 216, 216); vertical-align: top; font-weight: bold; background-color: rgb(240, 240, 240);"><section><span leaf="">修复版本</span></section></th></tr></thead><tbody><tr><td style="font-size: 0.75em; padding: 9px 12px; line-height: 22px; color: rgb(34, 34, 34); border: 1px solid rgb(216, 216, 216); vertical-align: top;"><section><span leaf="">NetScaler ADC</span></section></td><td style="font-size: 0.75em; padding: 9px 12px; line-height: 22px; color: rgb(34, 34, 34); border: 1px solid rgb(216, 216, 216); vertical-align: top;"><section><span leaf="">14.1-73.32之前, 13.1-64.23之前</span></section></td><td style="font-size: 0.75em; padding: 9px 12px; line-height: 22px; color: rgb(34, 34, 34); border: 1px solid rgb(216, 216, 216); vertical-align: top;"><section><span leaf="">14.1-73.37, 13.1-64.23</span></section></td></tr><tr><td style="font-size: 0.75em; padding: 9px 12px; line-height: 22px; color: rgb(34, 34, 34); border: 1px solid rgb(216, 216, 216); vertical-align: top;"><section><span leaf="">NetScaler ADC FIPS</span></section></td><td style="font-size: 0.75em; padding: 9px 12px; line-height: 22px; color: rgb(34, 34, 34); border: 1px solid rgb(216, 216, 216); vertical-align: top;"><section><span leaf="">14.1-73.37之前, 13.1.37.279之前</span></section></td><td style="font-size: 0.75em; padding: 9px 12px; line-height: 22px; color: rgb(34, 34, 34); border: 1px solid rgb(216, 216, 216); vertical-align: top;"><section><span leaf="">对应修复版本</span></section></td></tr><tr><td style="font-size: 0.75em; padding: 9px 12px; line-height: 22px; color: rgb(34, 34, 34); border: 1px solid rgb(216, 216, 216); vertical-align: top;"><section><span leaf="">NetScaler Gateway</span></section></td><td style="font-size: 0.75em; padding: 9px 12px; line-height: 22px; color: rgb(34, 34, 34); border: 1px solid rgb(216, 216, 216); vertical-align: top;"><section><span leaf="">14.1-73.37之前, 13.1-64.23之前</span></section></td><td style="font-size: 0.75em; padding: 9px 12px; line-height: 22px; color: rgb(34, 34, 34); border: 1px solid rgb(216, 216, 216); vertical-align: top;"><section><span leaf="">14.1-73.37, 13.1-64.23</span></section></td></tr></tbody></table>  
注意：8月份修复认证绕过漏洞CVE-2026-19490的版本（14.1-73.32和13.1-63.21）也在受影响范围内——修了上一个漏洞还得再修这个。  
  
这次公告一共修了8个CVE（88771-88778），其中88771和88772被标记为在野利用。  
  
**watchTowr的PoC**  
  
watchTowr发布了CVE-2026-88771的技术分析和检测工具。PoC显示的攻击链路：  
```
payload: pitboss PPE unexpectedly died NSPPE;id>/var/tmp/watchTowr;# X  -> 投毒日志  -> 触发force pickup  -> 命令在NetScaler PPE进程中执行
```  
  
watchTowr说明：  
- 影响所有NetScaler ADC和Gateway部署（默认配置，无需额外功能）  
  
- 命令注入到PPE进程日志中，被force pickup机制拾取执行  
  
- 可能需要最多24小时才会被pickup（取决于配置）  
  
- force pickup技术未包含在公开PoC中  
  
PoC作者：Sina Kheirkhah（@SinSinology），watchTowr团队。  
  
**事件时间线**  
<table><thead><tr><th style="font-size: 0.75em; padding: 9px 12px; line-height: 22px; color: rgb(34, 34, 34); border: 1px solid rgb(216, 216, 216); vertical-align: top; font-weight: bold; background-color: rgb(240, 240, 240);"><section><span leaf="">时间</span></section></th><th style="font-size: 0.75em; padding: 9px 12px; line-height: 22px; color: rgb(34, 34, 34); border: 1px solid rgb(216, 216, 216); vertical-align: top; font-weight: bold; background-color: rgb(240, 240, 240);"><section><span leaf="">事件</span></section></th></tr></thead><tbody><tr><td style="font-size: 0.75em; padding: 9px 12px; line-height: 22px; color: rgb(34, 34, 34); border: 1px solid rgb(216, 216, 216); vertical-align: top;"><section><span leaf="">9月26日</span></section></td><td style="font-size: 0.75em; padding: 9px 12px; line-height: 22px; color: rgb(34, 34, 34); border: 1px solid rgb(216, 216, 216); vertical-align: top;"><section><span leaf="">watchTowr在X上披露&#34;两个未修复NetScaler RCE零日在野利用&#34;</span></section></td></tr><tr><td style="font-size: 0.75em; padding: 9px 12px; line-height: 22px; color: rgb(34, 34, 34); border: 1px solid rgb(216, 216, 216); vertical-align: top;"><section><span leaf="">9月26日</span></section></td><td style="font-size: 0.75em; padding: 9px 12px; line-height: 22px; color: rgb(34, 34, 34); border: 1px solid rgb(216, 216, 216); vertical-align: top;"><section><span leaf="">Reddit r/Citrix管理员报告IT供应商建议立即关闭NetScaler设备</span></section></td></tr><tr><td style="font-size: 0.75em; padding: 9px 12px; line-height: 22px; color: rgb(34, 34, 34); border: 1px solid rgb(216, 216, 216); vertical-align: top;"><section><span leaf="">9月27日</span></section></td><td style="font-size: 0.75em; padding: 9px 12px; line-height: 22px; color: rgb(34, 34, 34); border: 1px solid rgb(216, 216, 216); vertical-align: top;"><section><span leaf="">Citrix发布安全公告CTX697096，确认88771/88772在野利用</span></section></td></tr><tr><td style="font-size: 0.75em; padding: 9px 12px; line-height: 22px; color: rgb(34, 34, 34); border: 1px solid rgb(216, 216, 216); vertical-align: top;"><section><span leaf="">9月27日</span></section></td><td style="font-size: 0.75em; padding: 9px 12px; line-height: 22px; color: rgb(34, 34, 34); border: 1px solid rgb(216, 216, 216); vertical-align: top;"><section><span leaf="">CISA加入KEV目录，修复截止9月30日</span></section></td></tr><tr><td style="font-size: 0.75em; padding: 9px 12px; line-height: 22px; color: rgb(34, 34, 34); border: 1px solid rgb(216, 216, 216); vertical-align: top;"><section><span leaf="">9月28日</span></section></td><td style="font-size: 0.75em; padding: 9px 12px; line-height: 22px; color: rgb(34, 34, 34); border: 1px solid rgb(216, 216, 216); vertical-align: top;"><section><span leaf="">NHS Digital发布安全告警</span></section></td></tr><tr><td style="font-size: 0.75em; padding: 9px 12px; line-height: 22px; color: rgb(34, 34, 34); border: 1px solid rgb(216, 216, 216); vertical-align: top;"><section><span leaf="">9月28日</span></section></td><td style="font-size: 0.75em; padding: 9px 12px; line-height: 22px; color: rgb(34, 34, 34); border: 1px solid rgb(216, 216, 216); vertical-align: top;"><section><span leaf="">watchTowr发布PoC和技术分析</span></section></td></tr></tbody></table>  
**为什么这么紧急**  
  
NetScaler ADC和Gateway部署在企业网络边缘——处理VPN远程访问、负载均衡、用户认证。拿下NetScaler=拿下企业入口。  
  
两个关键因素让这个漏洞格外危险：  
1. **默认配置受影响**  
——不需要额外功能组件，不需要特殊配置，装了就中  
  
1. **零日利用**  
——修复公开前就被攻击了，打补丁不能告诉你是否已被入侵  
  
**打补丁不等于安全。**  
 荷兰NCSC在2025年NetScaler零日事件后明确说过：更新不能消除风险，因为攻击者可能已经获得了补丁前的访问权限，需要运行入侵检查脚本。  
  
**修复建议**  
1. **立即升级到修复版本**  
：14.1-73.37或13.1-64.23  
  
1. 如果暂时无法升级，考虑将NetScaler设备下线（部分管理员已这么做）  
  
1. **升级后必须检查是否已被入侵**  
——不能只打补丁  
  
1. 使用Citrix官方入侵检查工具或荷兰NCSC的检查脚本：   
  
1. 检查活动设备中的入侵文件  
  
1. 检查核心转储（core dump）  
  
1. 检查完整NetScaler镜像  
  
1. 如果确认被入侵：重建设备+轮换所有凭据（VPN凭据、管理员密码、证书密钥）  
  
1. 检查NetScaler日志中是否有异常PPE进程重启记录（watchTowr PoC的特征是"pitboss PPE unexpectedly died"）  
  
NetScaler设备不应长期暴露在公网。如果必须暴露，确保WAF规则覆盖已知的PoC特征。  
  
**参考链接**  
- Citrix安全公告：support.citrix.com/support-home/kbsearch/article?articleNumber=CTX697096  
  
- CISA KEV：cisa.gov/known-exploited-vulnerabilities-catalog（CVE-2026-88771，修复截止9月30日）  
  
- watchTowr PoC：github.com/watchtowrlabs/watchTowr-vs-Citrix-Netscaler-CVE-2026-88771  
  
- The Hacker News：thehackernews.com/2026/09/warning-two-unpatched-citrix-netscaler.html  
  
- NHS Digital告警：digital.nhs.uk/cyber-alerts/2026/cc-4858  
  
- NVD：nvd.nist.gov/vuln/detail/CVE-2026-88771  
  
  
  

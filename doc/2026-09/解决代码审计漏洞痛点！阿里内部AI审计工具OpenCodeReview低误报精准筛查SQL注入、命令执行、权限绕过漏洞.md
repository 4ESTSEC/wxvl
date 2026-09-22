#  解决代码审计漏洞痛点！阿里内部AI审计工具OpenCodeReview低误报精准筛查SQL注入、命令执行、权限绕过漏洞  
alibaba
                    alibaba  渗透安全HackTwo   2026-09-21 16:07  
  
0x01 工具介绍  
  
OpenCodeReview是阿里内部的AI驱动代码审计CLI工具，当前已经开源，适配网络安全检测场景，可高效完成源码安全审计工作。工具融合工程约束与AI智能分析，有效解决传统审计漏报、误报、定位偏移等问题，精准挖掘SQL注入、命令执行、权限绕过等高危漏洞。支持增量diff审查与全量代码扫描，低Token消耗、检测精度更高，可无缝接入CI/CD流程，适配企业开发合规、代码加固、风险排查等各类安全场景。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibrevicNauKAWGrFM6OIpiaxlcZXHKAnQnyIWJMtbCMlqz4BdXtwhUGwdN2vU4PWDtRibc5G5Gd7gU3IibArPI0fdndicfIrVU5QlJaFAuOmZCIhg/640?wx_fmt=png&from=appmsg "")  
  
  
  
  
  
  
  
注意：  
现在只对常读和星标的公众号才展示大图推送，建议大家把  
**渗透安全HackTwo**  
"**设为****星标⭐️**  
"  
**否****则可能就看不到了啦！**  
  
**下载地址在末尾 #渗透安全HackTwo**  
  
0x02   
功能介绍  
  
✨核心特点  
  
对安全工程师和开发团队来说，代码审计最怕两件事：  
**漏审**  
和  
**误报**  
。漏审意味着漏洞带病上线，误报意味着审计报告没人信。OpenCodeReview 的解题思路，是用"确定性规则引擎 + LLM Agent"的混合架构，把这两件事同时压住。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibrevicNauKAU257dPjT8GuCZYyhVEFkR8sberxlvtmzuU74wz5HiaGSicSEZ7LHmTQiaPYfO3aquySuq3QVibkTNoknpgq9XHJWGiaJdxuOtxQVU4/640?wx_fmt=png&from=appmsg "")  
### 1️⃣ 内置安全规则：四大高频漏洞类型开箱即审  
  
工具自带的多语言规则集，开箱即覆盖安全审计中最常见、最危险的几类问题：  
<table><thead><tr style="scrollbar-color: transparent transparent;font: inherit;vertical-align: baseline;border-width: 0px;border-style: none;border-color: inherit;border-image: none;margin: 0px;padding: 0px;display: table-row;"><th data-colwidth="183" align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: 600;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px;border-style: solid none none;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor;border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">漏洞类型</span></span></section></th><th align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: 600;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px 1px;border-style: solid none none solid;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor rgba(0, 0, 0, 0.13);border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">审计重点</span></span></section></th><th align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: 600;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px 1px;border-style: solid none none solid;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor rgba(0, 0, 0, 0.13);border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">典型场景</span></span></section></th></tr></thead><tbody><tr style="scrollbar-color: transparent transparent;font: inherit;vertical-align: baseline;border-width: 0px;border-style: none;border-color: inherit;border-image: none;margin: 0px;padding: 0px;display: table-row;"><td data-colwidth="183" align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px;border-style: solid none none;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor;border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">🔴 </span></span><strong data-v-c0e67cf3="" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: 600;font-stretch: inherit;font-size: inherit;line-height: inherit;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: baseline;border: 0px;margin: 0px;padding: 0px;"><span leaf=""><span textstyle="" style="font-size: 12px;">SQL 注入</span></span></strong></section></td><td align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px 1px;border-style: solid none none solid;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor rgba(0, 0, 0, 0.13);border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">MyBatis XML Mapper 中的 </span></span><code data-v-bfaabce6="" data-v-048cb31c="" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 16px;line-height: 26px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: baseline;border: 0px;margin: 0px 4px;padding: 2px 6px;color: rgba(0, 0, 0, 0.9);background-color: rgba(0, 0, 0, 0.03);border-radius: 8px;white-space: pre-wrap;word-break: break-word;max-width: 100%;text-shadow: none;overflow: auto;"><span leaf=""><span textstyle="" style="font-size: 12px;">${}</span></span></code><span leaf=""><span textstyle="" style="font-size: 12px;"> 拼接、动态 SQL 注入风险</span></span></section></td><td align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px 1px;border-style: solid none none solid;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor rgba(0, 0, 0, 0.13);border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">金融、电商系统的数据层安全审计重点</span></span></section></td></tr><tr style="scrollbar-color: transparent transparent;font: inherit;vertical-align: baseline;border-width: 0px;border-style: none;border-color: inherit;border-image: none;margin: 0px;padding: 0px;display: table-row;"><td data-colwidth="183" align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px;border-style: solid none none;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor;border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">🔴 </span></span><strong data-v-c0e67cf3="" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: 600;font-stretch: inherit;font-size: inherit;line-height: inherit;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: baseline;border: 0px;margin: 0px;padding: 0px;"><span leaf=""><span textstyle="" style="font-size: 12px;">XSS 跨站脚本</span></span></strong></section></td><td align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px 1px;border-style: solid none none solid;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor rgba(0, 0, 0, 0.13);border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">前端文件中的不可信输出未转义、危险 DOM 操作</span></span></section></td><td align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px 1px;border-style: solid none none solid;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor rgba(0, 0, 0, 0.13);border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">UGC（用户生成内容）场景几乎不可避免</span></span></section></td></tr><tr style="scrollbar-color: transparent transparent;font: inherit;vertical-align: baseline;border-width: 0px;border-style: none;border-color: inherit;border-image: none;margin: 0px;padding: 0px;display: table-row;"><td data-colwidth="183" align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px;border-style: solid none none;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor;border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">🔴 </span></span><strong data-v-c0e67cf3="" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: 600;font-stretch: inherit;font-size: inherit;line-height: inherit;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: baseline;border: 0px;margin: 0px;padding: 0px;"><span leaf=""><span textstyle="" style="font-size: 12px;">命令注入</span></span></strong></section></td><td align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px 1px;border-style: solid none none solid;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor rgba(0, 0, 0, 0.13);border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">拼接用户输入执行系统命令、危险 API 调用</span></span></section></td><td align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px 1px;border-style: solid none none solid;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor rgba(0, 0, 0, 0.13);border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">可直接导致服务器被接管的高危漏洞</span></span></section></td></tr><tr style="scrollbar-color: transparent transparent;font: inherit;vertical-align: baseline;border-width: 0px;border-style: none;border-color: inherit;border-image: none;margin: 0px;padding: 0px;display: table-row;"><td data-colwidth="183" align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px;border-style: solid none none;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor;border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">🟠 </span></span><strong data-v-c0e67cf3="" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: 600;font-stretch: inherit;font-size: inherit;line-height: inherit;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: baseline;border: 0px;margin: 0px;padding: 0px;"><span leaf=""><span textstyle="" style="font-size: 12px;">路径穿越</span></span></strong></section></td><td align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px 1px;border-style: solid none none solid;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor rgba(0, 0, 0, 0.13);border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">文件读取/上传路径拼接用户输入未校验</span></span></section></td><td align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px 1px;border-style: solid none none solid;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor rgba(0, 0, 0, 0.13);border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">可读取任意文件、覆盖系统配置文件</span></span></section></td></tr><tr style="scrollbar-color: transparent transparent;font: inherit;vertical-align: baseline;border-width: 0px;border-style: none;border-color: inherit;border-image: none;margin: 0px;padding: 0px;display: table-row;"><td data-colwidth="183" align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px;border-style: solid none none;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor;border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">🟠 </span></span><strong data-v-c0e67cf3="" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: 600;font-stretch: inherit;font-size: inherit;line-height: inherit;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: baseline;border: 0px;margin: 0px;padding: 0px;"><span leaf=""><span textstyle="" style="font-size: 12px;">SSRF 服务端请求伪造</span></span></strong></section></td><td align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px 1px;border-style: solid none none solid;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor rgba(0, 0, 0, 0.13);border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">发起外部请求的 URL 参数可控、未做内网地址过滤</span></span></section></td><td align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px 1px;border-style: solid none none solid;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor rgba(0, 0, 0, 0.13);border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">云环境下可探测内网、窃取元数据凭证</span></span></section></td></tr><tr style="scrollbar-color: transparent transparent;font: inherit;vertical-align: baseline;border-width: 0px;border-style: none;border-color: inherit;border-image: none;margin: 0px;padding: 0px;display: table-row;"><td data-colwidth="183" align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px;border-style: solid none none;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor;border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">🟠 </span></span><strong data-v-c0e67cf3="" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: 600;font-stretch: inherit;font-size: inherit;line-height: inherit;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: baseline;border: 0px;margin: 0px;padding: 0px;"><span leaf=""><span textstyle="" style="font-size: 12px;">不安全的反序列化</span></span></strong></section></td><td align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px 1px;border-style: solid none none solid;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor rgba(0, 0, 0, 0.13);border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">反序列化不可信数据、危险类的实例化</span></span></section></td><td align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px 1px;border-style: solid none none solid;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor rgba(0, 0, 0, 0.13);border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">Java 生态经典 RCE 入口</span></span></section></td></tr><tr style="scrollbar-color: transparent transparent;font: inherit;vertical-align: baseline;border-width: 0px;border-style: none;border-color: inherit;border-image: none;margin: 0px;padding: 0px;display: table-row;"><td data-colwidth="183" align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px;border-style: solid none none;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor;border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">🟠 </span></span><strong data-v-c0e67cf3="" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: 600;font-stretch: inherit;font-size: inherit;line-height: inherit;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: baseline;border: 0px;margin: 0px;padding: 0px;"><span leaf=""><span textstyle="" style="font-size: 12px;">硬编码密钥/凭据</span></span></strong></section></td><td align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px 1px;border-style: solid none none solid;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor rgba(0, 0, 0, 0.13);border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">源码中明文 AK/SK、密码、Token、私钥</span></span></section></td><td align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px 1px;border-style: solid none none solid;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor rgba(0, 0, 0, 0.13);border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">密钥一旦提交进仓库，等于公开</span></span></section></td></tr><tr style="scrollbar-color: transparent transparent;font: inherit;vertical-align: baseline;border-width: 0px;border-style: none;border-color: inherit;border-image: none;margin: 0px;padding: 0px;display: table-row;"><td data-colwidth="183" align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px;border-style: solid none none;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor;border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">🟠 </span></span><strong data-v-c0e67cf3="" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: 600;font-stretch: inherit;font-size: inherit;line-height: inherit;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: baseline;border: 0px;margin: 0px;padding: 0px;"><span leaf=""><span textstyle="" style="font-size: 12px;">线程安全 / 竞态条件</span></span></strong></section></td><td align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px 1px;border-style: solid none none solid;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor rgba(0, 0, 0, 0.13);border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">并发场景下共享变量无同步保护、map 并发读写</span></span></section></td><td align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px 1px;border-style: solid none none solid;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor rgba(0, 0, 0, 0.13);border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">可导致数据错乱、业务逻辑被绕过</span></span></section></td></tr><tr style="scrollbar-color: transparent transparent;font: inherit;vertical-align: baseline;border-width: 0px;border-style: none;border-color: inherit;border-image: none;margin: 0px;padding: 0px;display: table-row;"><td data-colwidth="183" align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px;border-style: solid none none;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor;border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">🟡 </span></span><strong data-v-c0e67cf3="" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: 600;font-stretch: inherit;font-size: inherit;line-height: inherit;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: baseline;border: 0px;margin: 0px;padding: 0px;"><span leaf=""><span textstyle="" style="font-size: 12px;">NPE / 资源泄漏</span></span></strong></section></td><td align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px 1px;border-style: solid none none solid;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor rgba(0, 0, 0, 0.13);border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">空指针异常、连接/文件句柄未释放</span></span></section></td><td align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px 1px;border-style: solid none none solid;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor rgba(0, 0, 0, 0.13);border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">稳定性杀手，生产事故常客</span></span></section></td></tr><tr style="scrollbar-color: transparent transparent;font: inherit;vertical-align: baseline;border-width: 0px;border-style: none;border-color: inherit;border-image: none;margin: 0px;padding: 0px;display: table-row;"><td data-colwidth="183" align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px;border-style: solid none none;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor;border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">🟡 </span></span><strong data-v-c0e67cf3="" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: 600;font-stretch: inherit;font-size: inherit;line-height: inherit;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: baseline;border: 0px;margin: 0px;padding: 0px;"><span leaf=""><span textstyle="" style="font-size: 12px;">goroutine / 协程泄漏</span></span></strong></section></td><td align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px 1px;border-style: solid none none solid;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor rgba(0, 0, 0, 0.13);border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">goroutine 无退出条件、channel 阻塞未处理</span></span></section></td><td align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px 1px;border-style: solid none none solid;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor rgba(0, 0, 0, 0.13);border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">Go 服务内存缓慢上涨、最终 OOM</span></span></section></td></tr><tr style="scrollbar-color: transparent transparent;font: inherit;vertical-align: baseline;border-width: 0px;border-style: none;border-color: inherit;border-image: none;margin: 0px;padding: 0px;display: table-row;"><td data-colwidth="183" align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px;border-style: solid none none;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor;border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">🟡 </span></span><strong data-v-c0e67cf3="" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: 600;font-stretch: inherit;font-size: inherit;line-height: inherit;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: baseline;border: 0px;margin: 0px;padding: 0px;"><span leaf=""><span textstyle="" style="font-size: 12px;">敏感信息泄漏</span></span></strong></section></td><td align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px 1px;border-style: solid none none solid;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor rgba(0, 0, 0, 0.13);border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">日志/错误信息中输出密码、身份证、手机号</span></span></section></td><td align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px 1px;border-style: solid none none solid;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor rgba(0, 0, 0, 0.13);border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">违反合规要求，易被撞库利用</span></span></section></td></tr><tr style="scrollbar-color: rgba(0, 0, 0, 0.25) rgba(0, 0, 0, 0);font: inherit;vertical-align: baseline;border-width: 0px;border-style: none;border-color: inherit;border-image: none;margin: 0px;padding: 0px;display: table-row;"><td data-colwidth="183" align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px;border-style: solid none none;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor;border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">⬜ </span></span><strong data-v-c0e67cf3="" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: 600;font-stretch: inherit;font-size: inherit;line-height: inherit;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: baseline;border: 0px;margin: 0px;padding: 0px;"><span leaf=""><span textstyle="" style="font-size: 12px;">其他漏洞类型</span></span></strong></section></td><td align="left" style="scrollbar-color: transparent transparent;font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px 1px;border-style: solid none none solid;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor rgba(0, 0, 0, 0.13);border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">越权、业务逻辑缺陷、依赖组件 CVE 等，可通过自定义规则逐步纳入</span></span></section></td><td align="left" style="scrollbar-color: rgba(0, 0, 0, 0.25) rgba(0, 0, 0, 0);font-family: inherit;font-style: inherit;font-variant: inherit;font-weight: inherit;font-stretch: inherit;font-size: 14px;line-height: 22px;font-optical-sizing: inherit;font-size-adjust: inherit;font-kerning: inherit;font-feature-settings: inherit;font-variation-settings: inherit;font-language-override: inherit;vertical-align: top;border-width: 1px 0px 0px 1px;border-style: solid none none solid;border-color: rgba(0, 0, 0, 0.13) currentcolor currentcolor rgba(0, 0, 0, 0.13);border-image: none;margin: 0px;padding: 10px 16px;text-align: left;white-space: pre-wrap;word-break: break-word;max-width: 480px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">不一一列举</span></span></section></td></tr></tbody></table>  
而且规则匹配是  
**按文件特征精细化触发**  
的：Java 文件重点审 NPE 和线程安全，XML Mapper 文件重点审 SQL 注入，前端文件重点审 XSS——模型注意力高度聚焦，从源头上减少噪声。  
### 2️⃣ 深度上下文审计：不是正则匹配，是"看懂"代码  
  
传统 SAST 工具靠正则和 AST 规则，经常被编码绕过、拼接绕过耍得团团转。OpenCodeReview 的 Agent 可以：  
- **读取完整文件内容**  
，结合上下文判断这处"拼接"到底有没有经过过滤；  
  
- **搜索整个代码库**  
，追溯数据来源（用户输入从哪来、流到哪去）；  
  
- **跨文件关联分析**  
，比如 Controller 层的参数校验缺失，会在 Service 层埋雷。  
  
官方实测案例里，一段看似无害的 Go map 读写代码，被内置线程安全规则直接标为 Critical 级竞态条件，并给出了带   
sync.RWMutex  
 的完整修复方案。  
### 3️⃣ ocr scan：没有 Git 历史的老代码也能审  
  
这是安全场景下最实用的能力之一。很多时候我们要审计的是：  
- 接手的不熟悉的遗留系统；  
  
- 外包交付的、没有提交历史的代码；  
  
- 没有 diff 可比的整个目录。  
  
ocr scan  
 直接审查  
**整个文件**  
而非 diff，无需 Git 历史，等于把 AI 变成了一位随叫随到的白盒审计助手。 配合   
--path  
   
指定目录，可以快速对一个模块做定向安全排查。  
### 4️⃣ 自定义规则：把你们团队的"安全红线"写进引擎  
  
内置规则之外，规则系统支持  
**四层优先级链**  
注入自定义安全规则（CLI 参数 > 项目配置 > 全局配置 > 内置规则），团队可以在项目根目录  
的   
.opencodereview/rule.json  
 中定义自己的审计标准，例如：  
```
{
  "rules": [
    {
      "path": "**/*mapper*.xml",
      "rule": "检查 SQL 是否存在注入风险、参数错误、标签未闭合"
    },
    {
      "path": "force-api/**/*.java",
      "rule": "所有新增方法必须对必填参数做 null 校验"
    }
  ]
}
```  
  
OWASP Top 10 里的反序列化、SSRF、路径穿越、硬编码密钥等问题，都可以通过定制规则纳入日常审计——把安全规范从"Wiki 里的文档"变成"每次提交自动执行的检查"。  
### 5️⃣ 安全场景下的两个关键设计  
- **委托模式**  
：涉密项目不能把代码发给外部 API？让私有部署的 LLM 或宿主 AI Agent 执行审查，OCR 只负责文件筛选和规则解析，  
**代码不出内网**  
；  
  
- **CI/CD 卡点**  
：接入 GitHub Actions / GitLab CI 后，每个 PR 自动审查，安全漏洞在合并前就被拦下，审查意见直接回贴为行级评论。  
  
> ⚠️ 客观说一句：它的 Recall 低于通用 Agent，Benchmark 作者也明确说明  
**不要指望它替代专业安全审计工具**  
。它的定位是"每次提交都跑得起的高精度日常防线"，让高危漏洞在写代码的阶段就暴露，而不是等季度审计才被发现。  
  
  
  
0x03 更新介绍  
```
委托模式（Delegation Mode）：不用配 LLM，把审查执行交给宿主 AI Agent，省钱省心；
MCP 扩展能力：审查 Agent 可以调用外部工具；
会话查看器与断点恢复：长审查任务不再怕中断；
多语言规则集持续增强：内置 NPE、线程安全、XSS、SQL 注入等 50+ 文件类型的专项审查规则；
```  
###   
  
0x04 使用介绍  
  
📦安装与使用指南  
```
npm install -g @alibaba-group/open-code-review
```  
  
安装后 ocr  
 命令即可全局使用。也支持安装脚本、GitHub Release 二进制、Homebrew 等方式。  
### 配置 LLM  
```
ocr config provider   # 选择内置供应商或添加自定义供应商
ocr config model      # 为当前供应商选择模型
```  
  
交互式界面会引导你完成 API Key 输入，并自动测试连通性。  
### 开始审查  
```
cd your-project
# 工作区模式 —— 审查所有变更
ocr review
# 分支范围审查
ocr review --from main --to feature-branch
# 单个提交
ocr review --commit abc123
# 全量文件扫描
ocr scan --path internal/agent
# 输出 JSON 结果（CI / AI Agent 推荐）
ocr review --format json --output result.json
```  
  
就这么简单，一次行级精度的 AI 代码审查就完成了。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ibrevicNauKAUhibCoHic39CfrZsTgeJtdtFRStUZLXjmWzsBEGoyg4XIBNp84mkEINs8jLzp7icZ31cayHpgdFiczH97iaoEn2wfc9kQSj5Y0Dc1E/640?wx_fmt=png&from=appmsg "")  
## 写在最后  
  
OpenCodeReview 给了一个很好的示范：**Agent 时代，最好的架构不是"全部交给模型"，而是把确定性工程与智能体严格分工**  
。文件筛选、分组、定位、反思交给代码；动态决策交给 Agent。  
  
  
**0x05 内部VIP星球介绍-V1.5（福利）**  
  
          
如果你想学习更多**渗透测试技术/应急溯源/免杀工具/挖洞SRC赚取漏洞赏金/红队打点等**  
欢迎加入我们**内部星球**  
可获得内部工具字典和享受内部资源和  
内部交流群，  
**每天更新1day/0day漏洞刷分上分****(2026POC更新至14222+)**  
**，**  
包含全网一些**付费扫描****工具及内部原创的Burp自动化漏****洞探测插件/漏扫工具等，AI代审工具，最新挖洞技巧、SKILL等**  
。shadon/  
Hunter  
/  
0zone  
/  
Zoomeye  
/Quake/  
Fofa高级会员/AI账号  
/CTFShow等各种账号会员共享。详情点击下方链接了解，觉得价格高的师傅后台回复"   
**星球**  
 "有优惠券名额有限先到先得  
**❗️**  
啥都有  
**❗️**  
全网资源  
最新  
最丰富  
**❗️****（🤙截止目前已有3200+多位师傅选择加入❗️早加入早享受）**  
  
****  
最新漏洞情报分享：  
https://t.zsxq.com/DSAvv  
  
****  
  
**👉****点击了解加入-->>内部VIP知识星球福利介绍V1.5版本-1day/0day漏洞库及内部资源更新**  
  
****  
  
  
结尾  
  
# 免责声明  
  
  
# 获取方法  
  
  
**公众号回复20260922获取下载、回复 加群 获取交流群**  
  
****  
  
# 最后必看-免责声明  
  
  
      
文章中的案例或工具仅面向合法授权的企业安全建设行为，如您需要测试内容的可用性，请自行搭建靶机环境，勿用于非法行为。如  
用于其他用途，由使用者承担全部法律及连带责任，与作者和本公众号无关。  
本项目所有收录的poc均为漏洞的理论判断，不存在漏洞利用过程，不会对目标发起真实攻击和漏洞利用。文中所涉及的技术、思路和工具仅供以安全为目的的学习交流使用。  
如您在使用本工具或阅读文章的过程中存在任何非法行为，您需自行承担相应后果，我们将不承担任何法律及连带责任。本工具或文章或来源于网络，若有侵权请联系作者删除，请在24小时内删除，请勿用于商业行为，自行查验是否具有后门，切勿相信软件内的广告！  
  
  
  
# 往期推荐  
  
  
**1.内部VIP知识星球福利介绍V1.5（AI自动化）**  
  
**2.CS4.8-CobaltStrike4.8汉化+插件版**  
  
**3.全新升级BurpSuite2026.4专业(稳定版)**  
  
**4. 最新xray1.9.11高级版下载Windows/Linux**  
  
**5. 最新HCL AppScan Standard**  
  
  
渗透安全HackTwo  
  
微信号：关注公众号获取  
  
后台回复星球加入：  
知识星球  
  
扫码关注 了解更多  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/RjOvISzUFq6qFFAxdkV2tgPPqL76yNTw38UJ9vr5QJQE48ff1I4Gichw7adAcHQx8ePBPmwvouAhs4ArJFVdKkw/640?wx_fmt=png "二维码")  
  
  
  
  
上一篇文章：  
[Nacos配置文件攻防总结|揭秘Nacos被低估的攻击面](https://mp.weixin.qq.com/s?__biz=Mzg3ODE2MjkxMQ==&mid=2247492839&idx=1&sn=b6f091114fbd8e8922153a996c8f4f1c&scene=21#wechat_redirect)  
  
  

#  我做了一个用自然语言挖漏洞的 AI 渗透工具：VulnClaw  
 C4安全   2026-07-27 08:44  
  
# VulnClaw：说人话，打漏洞  
  
**AI 驱动的渗透测试 CLI 工具，让安全测试像聊天一样简单**  
> GitHub: Unclecheng-li/VulnClaw | Star: 23 | Fork: 6 | License: MIT  
  
## 0x00 先说痛点  
  
做渗透测试，你是否经历过这些：  
- **信息收集**  
阶段，Nmap、Masscan、Subfinder…工具一堆，命令记不住  
  
- **漏洞发现**  
时，面对大量扫描结果，不知道先打哪个  
  
- **漏洞利用**  
阶段，POC 要自己改，EXP 要自己找  
  
- **报告编写**  
更是噩梦，截图、整理、格式化…测试1小时，写报告3小时  
  
**VulnClaw 的诞生，就是为了解决这些问题。**  
## 0x01 VulnClaw 是什么  
  
VulnClaw 是一个 **AI 驱动的渗透测试 CLI 工具**  
，基于 LLM Agent + MCP 工具链 + 渗透 Skill 编排。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/HfQeCvYjQUl0jF6N11fI4lQmWQrdZicmo0NW1P3Do7qjFtVmsWAfsH6SfUJ7IbjBKdVsI9tNkI5p4apG1RhRKLHziaRH75P0Yku6NqF5HSLQw/640?wx_fmt=png&from=appmsg "")  
  
### 核心价值  
  
![](https://mmbiz.qpic.cn/mmbiz_png/HfQeCvYjQUkduuoUBulrxD7B6YUPF6R7OspTAzRw1SKjVucVMpj4shPibn8mWVUPvjhdCo2IibFLD6xeibawicLXGZaMaOOoNcNODScvHlRy0S0/640?wx_fmt=png&from=appmsg "")  
  
**你只需要说「帮我测试这个站」，剩下的，VulnClaw 来做。**  
## 0x02 核心特性一览  
### 特性分布热力图  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/HfQeCvYjQUl9xbmVQh8GmV3ictAB2MN3fQpicqLeRFsGjfVZ2VJ30TVI4f8IXqU3AiaicBNVOqK1ia72cQALHIV1WxderBW0UwRo7bibJBcyyqW30/640?wx_fmt=png&from=appmsg "")  
### 1. 自然语言驱动  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/HfQeCvYjQUlib59O1pibuFFD9RH6KicibhDCUiaCEWNUeDOeniaMId1oBQqic6tyXu1ZWLcuH1uDnibNp7PSBk645NREVHu5HLmDiaSfxmVH3tEia1Nug/640?wx_fmt=png&from=appmsg "")  
  
不再需要记命令，你说什么，AI 就懂什么。  
### 2. 多模型支持（8 个 Provider）  
  
![](https://mmbiz.qpic.cn/mmbiz_png/HfQeCvYjQUl88CeSuxyCsmiacF3WOicUeq7n0q9Do1ibYhnXOpKVpzNxe3FEbXKRuXsnCs3lKuDUCTW9Skbibfbvk2ZrdbI4KHPjOrX8KXBfWz4/640?wx_fmt=png&from=appmsg "")  
  
一键切换，想用哪个用哪个。  
### 3. MCP 工具链（11 服务 / 23 工具）  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/HfQeCvYjQUlMXPse1O6rFb5gKT8UGA5AAqCfe4cJS9Xxeic0MfsicIocBro5brM8B6LSibgnVO17XYoqP0JwQb8r1jJsCf49H0vsXVML1myick0/640?wx_fmt=png&from=appmsg "")  
### 4. 渗透 Skill 体系  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/HfQeCvYjQUmzvaLa4b7D3pa0oY74q1AhV8YmB8ibFZTcBGLaYiaXxibfVGSnN2nrnJpb6Nhl4MwlKUoHeAexJEib87ny8WicvnNT9gnl1tmPLXg0/640?wx_fmt=png&from=appmsg "")  
  
**20 个 Skill，138 个参考文档**  
，覆盖渗透测试全流程。  
### 5. 编解码/加解密工具（29 种）  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/HfQeCvYjQUngicY2fjfVtDoHVhbwxPf4IHu1YrbiaKU8RowVbHM0yjiaDLhqXdJdZmjicIkic2vxq2WhKbx2POwXH8sacL4VS34n3FjpWlEPLLTE/640?wx_fmt=png&from=appmsg "")  
### 6. 持续性渗透测试  
- **默认配置**  
：100 轮/周期 × 10 周期 = 1000 轮  
  
- **每周期**  
自动生成报告  
  
- **跨周期**  
状态保持，越打越深入  
  
### 7. 自动化报告 & PoC 生成  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/HfQeCvYjQUncWxUxtxPoDRXOibXwRUeQDg1n9bEdbGJTyqqyhtb5H8DFjEjRRIcILYiaibSHwswTiajU6VNMibcnU9qSy4q9mnT5ib9X2l51cAp14/640?wx_fmt=png&from=appmsg "")  
## 0x03 快速上手  
### 安装  
```
# 一键安装
curl -fsSL https://raw.githubusercontent.com/Unclecheng-li/VulnClaw/main/scripts/install.sh | bash
# 或 pip 安装
pip install vulclaw
```  
### 使用方式  
```
# 方式一：REPL 交互模式（推荐）
vulnclaw
# 方式二：单命令全流程
vulnclaw run 192.168.1.100
# 方式三：持续性渗透
vulnclaw persistent 192.168.1.100
# 方式四：仅信息收集
vulnclaw recon target.com
# 方式五：漏洞扫描
vulnclaw scan target.com --ports 80,443,8080
```  
### REPL 交互示例  
```
$ vulnclaw
████████╗██╗  ██╗ █████╗ ██████╗ ███████╗
╚══██╔══╝██║  ██║██╔══██╗██╔══██╗██╔════╝
   ██║   ███████║███████║██████╔╝███████╗
   ██║   ██╔══██║██╔══██║██╔═══╝ ╚════██║
   ██║   ██║  ██║██║  ██║██║     ███████║
   ╚═╝   ╚═╝  ╚═╝╚═╝  ╚═╝╚═╝     ╚══════╝
> 说人话，打漏洞
[主机] 请输入目标: 192.168.1.100
[模式] 选择模式 (1.自动 2.交互): 1
[AI] 正在分析目标...
[AI] 开始信息收集阶段...
[MCP] 调用 http_scan 工具
[发现] 目标开放端口: 80, 443, 8080
[发现] Web服务: Apache/2.4.41
[漏洞] 检测到可疑端点: /admin/login.php
[AI] 开始漏洞利用阶段...
[利用] 尝试 SQL注入检测... [疑似] 参数 id 未过滤
[报告] 报告已生成: report_192.168.1.100_20260426.md
[PoC] PoC脚本已生成: poc_sqli_192.168.1.100.py
```  
## 0x04 架构解析  
  
![](https://mmbiz.qpic.cn/mmbiz_png/HfQeCvYjQUmGAxcpvrhI7FzlMfyE2PuOl8ibiaCzI6DZH7AP0ribXFKticUZDxaTW0K5ZVXePGcqAC8slOpxoVMzA7NGZBmC2JxqaK0fHicbhjqg/640?wx_fmt=png&from=appmsg "")  
## 0x05 使用场景  
<table><thead><tr><th style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;background: #f5f5f5;font-weight: 700;"><section><span leaf="">场景</span></section></th><th style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;background: #f5f5f5;font-weight: 700;"><section><span leaf="">适用度</span></section></th><th style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;background: #f5f5f5;font-weight: 700;"><section><span leaf="">说明</span></section></th></tr></thead><tbody><tr><td style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;"><strong style="font-weight: bolder;"><span leaf="">CTF 竞赛</span></strong></td><td style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;"><section><span leaf="">⭐⭐⭐⭐⭐</span></section></td><td style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;"><section><span leaf="">CTF Web/Crypto/Misc 专项，快速解题</span></section></td></tr><tr><td style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;"><strong style="font-weight: bolder;"><span leaf="">授权渗透测试</span></strong></td><td style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;"><section><span leaf="">⭐⭐⭐⭐⭐</span></section></td><td style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;"><section><span leaf="">自动化流程，提升效率</span></section></td></tr><tr><td style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;"><strong style="font-weight: bolder;"><span leaf="">安全教学</span></strong></td><td style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;"><section><span leaf="">⭐⭐⭐⭐</span></section></td><td style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;"><section><span leaf="">学习渗透测试思路和流程</span></section></td></tr><tr><td style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;"><strong style="font-weight: bolder;"><span leaf="">红队演练</span></strong></td><td style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;"><section><span leaf="">⭐⭐⭐⭐</span></section></td><td style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;"><section><span leaf="">持续性渗透，深度利用</span></section></td></tr></tbody></table>## 0x06 与传统工具对比  
<table><thead><tr><th style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;background: #f5f5f5;font-weight: 700;"><section><span leaf="">维度</span></section></th><th style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;background: #f5f5f5;font-weight: 700;"><section><span leaf="">VulnClaw</span></section></th><th style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;background: #f5f5f5;font-weight: 700;"><section><span leaf="">传统工具</span></section></th></tr></thead><tbody><tr><td style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;"><strong style="font-weight: bolder;"><span leaf="">学习成本</span></strong></td><td style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;"><section><span leaf="">低，说人话就行</span></section></td><td style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;"><section><span leaf="">高，需要记忆大量命令</span></section></td></tr><tr><td style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;"><strong style="font-weight: bolder;"><span leaf="">自动化程度</span></strong></td><td style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;"><section><span leaf="">高，全流程 AI 驱动</span></section></td><td style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;"><section><span leaf="">低，需要手动切换工具</span></section></td></tr><tr><td style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;"><strong style="font-weight: bolder;"><span leaf="">工具数量</span></strong></td><td style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;"><section><span leaf="">统一入口，11 MCP 服务</span></section></td><td style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;"><section><span leaf="">Nmap+Burp+SQLMap+…</span></section></td></tr><tr><td style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;"><strong style="font-weight: bolder;"><span leaf="">报告生成</span></strong></td><td style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;"><section><span leaf="">自动，Markdown + PoC</span></section></td><td style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;"><section><span leaf="">手动，耗时耗力</span></section></td></tr><tr><td style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;"><strong style="font-weight: bolder;"><span leaf="">上下文保持</span></strong></td><td style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;"><section><span leaf="">跨周期状态记忆</span></section></td><td style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;"><section><span leaf="">每次任务重头来</span></section></td></tr><tr><td style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;"><strong style="font-weight: bolder;"><span leaf="">模型支持</span></strong></td><td style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;"><section><span leaf="">8 种，灵活切换</span></section></td><td style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;"><section><span leaf="">固定工具集</span></section></td></tr><tr><td style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;"><strong style="font-weight: bolder;"><span leaf="">扩展性</span></strong></td><td style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;"><section><span leaf="">Skill + MCP，插件化</span></section></td><td style="border: 1px solid #ddd;padding: .5em .75em;text-align: left;"><section><span leaf="">依赖工具更新</span></section></td></tr></tbody></table>## 0x07 安全声明  
> ⚠️ **VulnClaw 仅用于已授权的安全测试**  
> 使用前需确保：  
> 已获得目标系统的明确书面授权测试范围已与目标所有者书面确认遵守当地法律法规  
> **未经授权进行渗透测试是违法行为。**  
  
## 0x08 Roadmap  
  
![](https://mmbiz.qpic.cn/mmbiz_png/HfQeCvYjQUliaI48ps3AP7LATXDbvapqUHbJGEKVMm3PpJXDt74hte9n90QDyCAeKhtXjZcTqdkPIeo7wX8zAEkQl8IFx0W6swDjJ5zyvBZ0/640?wx_fmt=png&from=appmsg "")  
## 0x09 总结  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/HfQeCvYjQUkljlJAME23HOysfjsvvepBuP0g4bQ53VYEeOLGtrFaKMVOQx3ErNwGziaHLSMpibFm7vNtWLQO4y4ibtpmKcNY7IrRFN7Lc48ENc/640?wx_fmt=png&from=appmsg "")  
  
**VulnClaw 不是要取代安全工程师，而是让安全工程师更高效。**  
## Links  
- **GitHub**  
: https://github.com/Unclecheng-li/VulnClaw  
  
- **文档**  
: README.md  
  
- **Issue**  
: 欢迎提 Bug 和 Feature  
  
如果你觉得这个项目有帮助，请给个 Star ⭐  
  

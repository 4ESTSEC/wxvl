#  每天学一个 Kali 工具 · Day22 nuclei：YAML 模板驱动的漏洞扫描"精确制导武器"  
原创 0day收割机
                    0day收割机  0day收割机   2026-09-22 03:01  
  
每天学一个 Kali 工具 · Day22  
# nuclei：YAML 模板驱动的漏洞扫描"精确制导武器"  
  
摘要：「每天学一个 Kali 工具」第二十二天。昨天 nikto 给 Web 服务器做了全身体检，今天升级到精确打击——nuclei：基于 YAML 模板的漏洞扫描器，社区维护数千个漏洞模板，新漏洞曝光几小时内模板就上线，官方宣称"零误报"。CVE 复现、批量资产体检、红队打点，它都是当下的顶流。文末附参数速查和实战命令，建议收藏。  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/ZrTsB3aQgWARngqicDZibbFqktjmGIjHicGwbva4FGm2ABBpxEKsEic9L5jjJOJBSquT4joDxvmYVrwbQAibEB5T2MibWTTIPZSAcH1u76FMgCN24/640?wx_fmt=jpeg "")  
## 写在前面  
  
昨天的 **nikto**  
 是"体检医生"：拿着内置的七千项清单，把服务器从头查一遍——全面，但慢，且误报不少。  
  
今天的 **nuclei**  
 换了个哲学：**不做全面体检，做精确化验。**  
  
它的核心是**模板（Template）**  
：每个漏洞一个 YAML 文件，里面写清楚"发什么请求、匹配什么特征、算不算命中"。社区几千名安全研究员持续贡献模板——**Log4Shell 曝光几小时后，nuclei 模板就上线了**  
；你手里攥着几千张"化验单"，对着目标批量跑一遍，命中的就是实锤。  
  
官方定位原话：基于模板发请求，**零误报（zero false positives）**  
，在海量主机上快速扫描。  
  
nikto 是 2001 年的老军医，nuclei 是 2020 年的基因测序仪——这就是漏洞扫描的代际差。  
## 一、nuclei 是什么？  
  
Kali 官网的定义：nuclei 是一款**基于简单 YAML DSL 的快速、可定制漏洞扫描器**  
。它提供基于模板的可配置定向扫描，具备海量可扩展性和易用性。nuclei 根据模板向目标发送请求，从而实现零误报和海量主机的快速扫描。  
<table><tbody><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;background: rgb(245, 241, 255);"><strong style="color: rgb(34, 34, 34);"><span leaf="">当前版本</span></strong></td><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;"><section><span leaf="">v3.11.1</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;background: rgb(245, 241, 255);"><strong style="color: rgb(34, 34, 34);"><span leaf="">体积</span></strong></td><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;"><section><span leaf="">136.31 MB</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;background: rgb(245, 241, 255);"><strong style="color: rgb(34, 34, 34);"><span leaf="">出品方</span></strong></td><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;"><section><span leaf="">ProjectDiscovery（httpx、subfinder、naabu 全家桶的娘家）</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;background: rgb(245, 241, 255);"><strong style="color: rgb(34, 34, 34);"><span leaf="">模板库</span></strong></td><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;"><section><span leaf="">nuclei-templates，社区维护，</span><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;color: rgb(124, 58, 237);"><span leaf="">-ut</span></code><span leaf=""> 一键更新</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;background: rgb(245, 241, 255);"><strong style="color: rgb(34, 34, 34);"><span leaf="">语言</span></strong></td><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;"><section><span leaf="">Go（单二进制，无运行时依赖）</span></section></td></tr></tbody></table>  
**支持的协议类型**  
（官网帮助原文）：  
```
dns, file, http, headless, tcp, workflow, ssl, websocket, whois, code, javascript
```  
  
从 Web 到 TCP 裸协议、从证书到 DNS、甚至无头浏览器和 JavaScript 执行——一个扫描器通吃。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/ZrTsB3aQgWBCpkW6h1Dyp9BVJ9mFHZ5TmH8LxfTBJthFbqKfIQDvob1ibasDDxpVZjv2PVpQ2aQ7WaqmYD4Qc99PFl8Dia3cdoj03ibQu1kjGw/640?wx_fmt=jpeg "")  
## 二、快速上手：官网示例精讲  
  
官网给了五条示例，正好覆盖五种典型用法：  
```
# 1. 最简扫描：全模板默认跑
nuclei -target example.com

# 2. 指定模板：只跑 HTTP 的 CVE 模板 + SSL 模板
nuclei -target example.com -t http/cves/ -t ssl

# 3. 批量目标：从文件读主机列表（每行一个）
nuclei -list hosts.txt

# 4. 结果导出 JSON
nuclei -target example.com -json-export output.json

# 5. 导出 Markdown 报告目录（按模板排序）
MARKDOWN_EXPORT_SORT_MODE=template nuclei -target example.com -markdown-export nuclei_report/
```  
  
**解读第 2 条**  
——这是 nuclei 的精髓：  
- -t http/cves/  
：只跑模板库里 http/cves/  
 目录——按年份组织的**已知 CVE 漏洞模板**  
；  
  
- -t ssl  
：加上 SSL 配置类模板（弱加密套件、过期证书等）；  
  
- 多个 -t  
 可以叠加，扫什么完全由你点名。  
  
首次运行 nuclei 会自动下载官方模板库到 ~/nuclei-templates/  
，之后记得常更新：  
```
# 更新模板库（漏洞知识库保鲜）
nuclei -update-templates
```  
## 三、模板体系：nuclei 的灵魂  
### ▍1. 一个模板长什么样？  
  
YAML 格式，核心就四段——**发什么请求、怎么匹配、什么严重级、元信息**  
：  
```
id: cve-example-001
info:
  name: 某组件未授权访问
  author: 社区贡献者
  severity: high
  tags: cve,exposure
requests:
  - method: GET
    path:
      - "{{BaseURL}}/admin/api"
    matchers:
      - type: word
        words:
          - "admin_token"
```  
  
看得懂 HTTP 请求就能写模板——这就是"YAML DSL"的平易之处。  
### ▍2. 模板的筛选维度（FILTERING）  
<table><thead><tr><th style="border: 1px solid rgb(224, 224, 224);background: rgb(124, 58, 237);color: rgb(255, 255, 255);padding: 8px 10px;text-align: left;"><section><span leaf="">维度</span></section></th><th style="border: 1px solid rgb(224, 224, 224);background: rgb(124, 58, 237);color: rgb(255, 255, 255);padding: 8px 10px;text-align: left;"><section><span leaf="">参数</span></section></th><th style="border: 1px solid rgb(224, 224, 224);background: rgb(124, 58, 237);color: rgb(255, 255, 255);padding: 8px 10px;text-align: left;"><section><span leaf="">例子</span></section></th></tr></thead><tbody><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;"><section><span leaf="">按标签</span></section></td><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;color: rgb(124, 58, 237);"><span leaf="">-tags</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-tags cve2024,xss</span></code></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;"><section><span leaf="">按严重级</span></section></td><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;color: rgb(124, 58, 237);"><span leaf="">-s</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-s critical,high</span></code></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;"><section><span leaf="">按模板 ID</span></section></td><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;color: rgb(124, 58, 237);"><span leaf="">-id</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-id CVE-2021-44228</span></code><section><span leaf="">（支持通配符）</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;"><section><span leaf="">按作者</span></section></td><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;color: rgb(124, 58, 237);"><span leaf="">-a</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-a pdteam</span></code></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;"><section><span leaf="">按协议</span></section></td><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;color: rgb(124, 58, 237);"><span leaf="">-pt</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-pt http,dns</span></code></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;"><section><span leaf="">排除</span></section></td><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;color: rgb(124, 58, 237);"><span leaf="">-etags / -es / -eid</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-etags dos</span></code><section><span leaf="">（排除 DoS 类）</span></section></td></tr></tbody></table>```
# 组合示例：只跑高危+严重级的 CVE 模板，排除 DoS
nuclei -list hosts.txt -tags cve -s high,critical -etags dos
```  
### ▍3. 三个高级玩法（官网帮助里的宝藏）  
<table><thead><tr><th style="border: 1px solid rgb(224, 224, 224);background: rgb(124, 58, 237);color: rgb(255, 255, 255);padding: 8px 10px;text-align: left;"><section><span leaf="">参数</span></section></th><th style="border: 1px solid rgb(224, 224, 224);background: rgb(124, 58, 237);color: rgb(255, 255, 255);padding: 8px 10px;text-align: left;"><section><span leaf="">玩法</span></section></th></tr></thead><tbody><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-as</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;"><strong><span leaf="">自动扫描</span></strong><section><span leaf="">：先用 wappalyzer 指纹识别目标技术栈，自动映射到对应标签的模板——不知道扫什么就交给它</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-ai &lt;提示词&gt;</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;"><strong><span leaf="">AI 生成模板</span></strong><section><span leaf="">：用自然语言描述漏洞，nuclei 直接生成并运行模板（v3 新特性）</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-w</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;"><strong><span leaf="">工作流</span></strong><section><span leaf="">：把多个模板串成条件链（先确认是 Tomcat，再跑 Tomcat 专属漏洞）</span></section></td></tr></tbody></table>  
![](https://mmbiz.qpic.cn/mmbiz_png/ZrTsB3aQgWDMDzCERmwU15mg0H9iawFzgrFHaxgY7g0HlnE3JRodjib0FmJL7bPLicic4yImq6oPZHfK2f8QCvGJuYIgHoQ6Rnq3JGZ4UIcILnc/640?wx_fmt=png&from=appmsg "")  
## 四、Interactsh：盲漏洞的"传呼机"  
  
SQL 盲注、SSRF、XXE 这类漏洞有个共同点：**响应里看不到证据**  
，得让目标"主动回连"才能实锤。  
  
nuclei 内置了 **Interactsh**  
（ProjectDiscovery 的 OAST 平台）：每个扫描分配一个专属回连域名，目标一旦发起带外请求（DNS/HTTP），nuclei 立刻收到"传呼"——盲漏洞无所遁形。  
  
眼熟吗？**这就是 Day19 Burp Collaborator 的开源同类**  
。默认用官方免费服务器（oast.pro 等），也可以 -iserver  
 自建：  
```
# 禁用 Interactsh（不想跑 OAST 类模板时）
nuclei -target example.com -no-interactsh
```  
## 五、和已学工具的联动  
  
nuclei 是 ProjectDiscovery 工具链的枢纽，和我们学过的兵器天然咬合：  
<table><thead><tr><th style="border: 1px solid rgb(224, 224, 224);background: rgb(124, 58, 237);color: rgb(255, 255, 255);padding: 8px 10px;text-align: left;"><section><span leaf="">联动</span></section></th><th style="border: 1px solid rgb(224, 224, 224);background: rgb(124, 58, 237);color: rgb(255, 255, 255);padding: 8px 10px;text-align: left;"><section><span leaf="">打法</span></section></th></tr></thead><tbody><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;background: rgb(245, 241, 255);"><strong><span leaf="">Day19 Burp Suite</span></strong></td><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-im burp</span></code><section><span leaf=""> 直接吃 Burp 导出的流量文件；或 </span><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-p http://127.0.0.1:8080</span></code><span leaf=""> 让流量全进 Burp 复盘</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;background: rgb(245, 241, 255);"><strong><span leaf="">Day21 nikto</span></strong></td><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;"><section><span leaf="">nikto 全面体检找&#34;病&#34;，nuclei 精确化验找&#34;实锤 CVE&#34;——报告互补</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;background: rgb(245, 241, 255);"><strong><span leaf="">Day09 amass / Day12 theHarvester</span></strong></td><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;"><section><span leaf="">挖出的子域名列表直接 </span><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-l subdomains.txt</span></code><span leaf=""> 批量扫</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;background: rgb(245, 241, 255);"><strong><span leaf="">Day17 SpiderFoot</span></strong></td><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;"><section><span leaf="">情报里的资产清单喂给 nuclei 做漏洞验证</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;background: rgb(245, 241, 255);"><strong><span leaf="">Day01 nmap</span></strong></td><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-preflight-portscan</span></code><section><span leaf=""> 自带预检端口扫描；也可 nmap 出端口再喂 nuclei</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;background: rgb(245, 241, 255);"><strong><span leaf="">uncover 引擎</span></strong></td><td style="border: 1px solid rgb(224, 224, 224);padding: 8px 10px;"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-uc -uq &#39;query&#39; -ue shodan</span></code><section><span leaf=""> 直接从 Shodan/FOFA/ZoomEye 拉目标来扫</span></section></td></tr></tbody></table>```
# 联动示例：Shodan 搜目标 → nuclei 直接扫
nuclei -uc -uq 'http.title:"后台登录"' -ue shodan -s critical,high
```  
## 六、参数速查表  
  
官网 nuclei -h  
 选项极多，挑高频的分类整理：  
### ▍目标（TARGET）  
<table><tbody><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-u, -target &lt;目标&gt;</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">单个/多个目标 URL 或主机</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-l, -list &lt;文件&gt;</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">目标列表文件（每行一个）</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-eh, -exclude-hosts</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">从输入中排除指定主机</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-resume &lt;文件&gt;</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">断点续扫</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-sa, -scan-all-ips</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">扫描域名解析出的所有 IP</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-im, -input-mode</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">输入格式：list / </span><strong><span leaf="">burp</span></strong><span leaf=""> / jsonl / yaml / openapi / swagger</span></section></td></tr></tbody></table>### ▍模板与过滤（TEMPLATES / FILTERING）  
<table><tbody><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-t, -templates</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">指定模板文件或目录（逗号分隔）</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-nt, -new-templates</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">只跑最新模板库新增的模板（追新漏洞）</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-as, -automatic-scan</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">指纹识别自动匹配模板</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-ai, -prompt</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">AI 生成并运行模板</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-w, -workflows</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">运行工作流（模板条件链）</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-tl / -tgl</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">列出当前过滤条件的模板 / 所有可用标签</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-tags / -etags</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">按标签跑 / 排除标签</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-s / -es</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">按严重级跑 / 排除（info/low/medium/high/critical）</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-id / -eid</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">按模板 ID 跑 / 排除（支持通配符）</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-pt / -ept</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">按协议类型跑 / 排除（dns/http/tcp/ssl/websocket…）</span></section></td></tr></tbody></table>### ▍输出（OUTPUT）  
<table><tbody><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-o, -output &lt;文件&gt;</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">结果写入文件</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-silent</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">只输出发现项（管道友好）</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-je / -jle</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">导出 JSON / JSONL</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-me, -markdown-export</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">导出 Markdown 报告目录</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-se, -sarif-export</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">导出 SARIF（接 CI/CD 安全流水线）</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-pe, -pdf-export</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">导出 PDF</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-sresp, -store-resp</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">保存全部请求/响应</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-rd, -redact</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">输出中脱敏指定字段</span></section></td></tr></tbody></table>### ▍速率与优化（RATE-LIMIT / OPTIMIZATIONS）  
<table><tbody><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-rl, -rate-limit &lt;n&gt;</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">每秒最大请求数，默认 150</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-c, -concurrency &lt;n&gt;</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">并行模板数，默认 25</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-bs, -bulk-size &lt;n&gt;</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">每模板并行主机数，默认 25</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-timeout &lt;秒&gt;</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">请求超时，默认 10</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-spm, -stop-at-first-match</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">命中即停（提速，可能破坏工作流逻辑）</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-project</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">项目模式：避免重复发相同请求</span></section></td></tr></tbody></table>### ▍高级（INTERACTSH / HEADLESS / HONEYPOT / DEBUG / UPDATE）  
<table><tbody><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-iserver &lt;url&gt;</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">自建 Interactsh 服务器</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-ni, -no-interactsh</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">禁用 OAST 回连检测</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-headless</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">启用需要无头浏览器的模板（DOM XSS 等）</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-hpd, -honeypot-detect</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><strong><span leaf="">蜜罐检测</span></strong><section><span leaf="">：按命中浓度识别蜜罐主机</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-shp, -suppress-honeypot</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">屏蔽蜜罐主机的输出</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-p, -proxy &lt;代理&gt;</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">走 HTTP/SOCKS5 代理（</span><strong><span leaf="">接 Burp</span></strong><span leaf="">）</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-debug / -hc / -stats</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">调试请求响应 / 诊断自检 / 进度统计</span></section></td></tr><tr><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;background: rgb(245, 241, 255);"><code style="font-family: Consolas, Monaco, monospace;font-size: 13px;"><span leaf="">-ut, -update-templates</span></code></td><td style="border: 1px solid rgb(224, 224, 224);padding: 7px 10px;"><section><span leaf="">更新模板库</span></section></td></tr></tbody></table>## 七、实战速查  
```
# 1. 首次使用：更新模板库
nuclei -update-templates

# 2. 官网同款：单目标全量扫描
nuclei -target example.com

# 3. 只打已知 CVE + SSL 配置
nuclei -target example.com -t http/cves/ -t ssl

# 4. 批量目标：nmap/amass 的成果直接喂进来
nuclei -l hosts.txt -s critical,high -o findings.txt

# 5. 追新漏洞：只跑模板库最新增加的模板
nuclei -l hosts.txt -nt

# 6. 全自动：指纹识别 → 自动匹配模板
nuclei -target example.com -as

# 7. 复现指定 CVE（Log4Shell 为例）
nuclei -l hosts.txt -id CVE-2021-44228

# 8. 报告全家桶：JSON + Markdown + SARIF
nuclei -target example.com -je out.json -me report_dir/ -se out.sarif

# 9. 流量全走 Burp，边扫边看
nuclei -target example.com -p http://127.0.0.1:8080

# 10. 温柔扫生产：限速 + 限时 + 排除 DoS 模板
nuclei -l hosts.txt -rl 50 -mt 1h -etags dos

# 11. 断点续扫：大目标扫一半断了接着来
nuclei -l hosts.txt -resume resume.cfg

# 12. 蜜罐识别：命中浓度异常的主机自动标记
nuclei -l hosts.txt -hpd -shp
```  
## 八、合规提醒  
  
nuclei 是真实漏洞利用级别的扫描器，红线比 nikto 更粗：  
1. **书面授权是绝对前提**  
：CVE 模板发的是**真实利用请求**  
（不只是探测），未授权使用性质等同攻击；  
  
1. **生产环境三重保护**  
：-rl  
 限速 + -mt  
 限时 + -etags dos,intrusive  
 排除破坏性模板；  
  
1. **OAST 回连注意**  
：Interactsh 模板会让目标向外部服务器发请求，敏感环境评估自建 -iserver  
 或 -ni  
 禁用；  
  
1. **蜜罐意识**  
：扫到"命中率高得离谱"的主机先想想 -hpd  
——你可能正在被反制方记录；  
  
1. **结果即证据链**  
：JSON/SARIF 报告含完整漏洞证据，按最高密级资料管理。  
  
## 今日小结  
1. **nuclei**  
 = 模板驱动的精确制导漏洞扫描器：YAML 写漏洞逻辑，社区数千模板，新 CVE 几小时内跟进，官方宣称零误报；  
  
1. 和 nikto 的代际差：nikto 内置数据库全面体检（慢+误报），nuclei 模板化验精确打击（快+实锤）——正式项目双管齐下；  
  
1. 三件套必会：-ut  
 更新模板、-t  
/-tags  
/-s  
 点名扫描、-je  
/-me  
 出报告；  
  
1. 高级玩法：-as  
 指纹自动匹配、-ai  
 自然语言生成模板、Interactsh 盲漏洞回连、-hpd  
 蜜罐检测；  
  
1. 工具链枢纽：吃 Burp 流量、吃 amass 子域名列表、接 uncover 情报引擎——前面二十一天学的成果都能喂给它。  
  
觉得有用的话，**点赞 + 在看 + 转发**  
 给一起学安全的朋友  
关注「每天学一个 Kali 工具」，明天见！  
  
**参考资料**  
  
· Kali 官方工具页：https://www.kali.org/tools/nuclei/  
  
· 项目主页：https://github.com/projectdiscovery/nuclei  
  
· 官方模板库：https://github.com/projectdiscovery/nuclei-templates  
  
· 模板编写指南：https://docs.projectdiscovery.io/tools/nuclei/  
  
  

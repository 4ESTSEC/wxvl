#  SRC-Hunter SKILL AI挖洞技能库，适配SRC众测与漏洞赏金，复刻数千条HackerOne高危漏洞实战案  
MyuriKanao
                    MyuriKanao  渗透安全HackTwo   2026-07-02 16:06  
  
0x01 工具介绍  
  
**src-hunter-skill 是面向SRC、众测及漏洞赏金场景的专属Claude Code AI挖洞工具库。项目依托数千份HackerOne、乌云公开实战案例，整合19类漏洞攻防手册、305组结构化Payload与海量WAF/EDR绕过方案。遵循信息收集、资产枚举、漏洞探测、风险验证、报告输出标准化流程，支持MCP工具联动调试，全程合规可控，帮助白帽从业者摆脱盲目测试，高效提升漏洞挖掘效率与产出质量。**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ibrevicNauKAX9SbBZODCL2n8ibk6GBW7zYJEPOqyUAVxSGwxIU7MS7bbBNInwP9ynI1ux7K8ib5aVXWL0AgpribWJibPjQBQTmLr3ZEgf8xrC3icI/640?wx_fmt=png&from=appmsg "")  
  
  
  
  
  
  
  
  
  
  
  
  
  
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
  
playbook 是主要入口。所有 playbook 都按黑盒视角编写，默认你只有 URL，没有源码。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibrevicNauKAXGV7ibcZPWiaB5ribAnzdXd3LJfbu9FxpVBrHCgGhfcl2fPe1KhgXQVzvS7x0lNQBCEeAUjrv7QNnE513vzITNlHX9bW9Rub9WUM/640?wx_fmt=png&from=appmsg "")  
  
每个 playbook 都围绕同一套问题展开：  
- 去哪里找入口  
  
- 用什么 payload 测  
  
- 观察哪些响应特征  
  
- 如何判断影响  
  
- 如何提高漏洞价值  
  
- 哪些行为不能做  
  
整体思路不是堆 payload，而是把测试动作、证据留存和报告输出串起来。  
## MCP 工具集成  
  
本 skill 集成本地 MCP 服务器作为工具层，让 Claude 在 hunt 阶段能直接调用浏览器自动化、CDP 调试、网络拦截、JS hook、AST 反混淆、Frida 内存验证、WASM 逆向、Source map 重构、Android adb 桥接、SSL pinning 绕过等能力。  
  
**当前主选**  
：  
jshookmcp  
 0.3.0（134 工具精选 / 386 全集 / 36 域），完整索引与场景映射见   
references/tools/mcp-jshook.md  
。  
  
7 个高关联 playbook（  
xss  
 /   
rce  
 /   
ssrf-cache-host  
 /   
mobile  
 /   
oauth-saml-jwt  
 /   
api-rest  
 /   
file-upload  
）末尾各有   
## 相关 MCP 工具  
 反向锚点，指明该攻击面下应该调哪些 jshook 工具、何时调。  
## 触发关键词  
  
skill 内置触发词包括：  
- bug bounty、HackerOne、SRC 挖洞、漏洞赏金、众测  
  
- WAF bypass、绕过 WAF  
  
- 如何测试某个 endpoint / API / 参数  
  
- 任意账号、任意修改、任意删除  
  
- 密码重置、找回密码  
  
- 默认凭据、Actuator、暴露的管理后台  
  
也可以显式调用：  
```
/src-hunter:src-hunter <target>   # Marketplace 插件安装
/src-hunter <target>              # Plain git standalone skill
```  
## Playbook 列表  
  
<table><thead><tr style="box-sizing: border-box;"><th style="box-sizing: border-box;"><span cid="n57" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">Playbook</span></span></span></span></th><th style="box-sizing: border-box;"><span cid="n58" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">嵌入 H1 案例数</span></span></span></span></th></tr></thead><tbody><tr style="box-sizing: border-box;"><td style="box-sizing: border-box;"><span cid="n60" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">arbitrary-x-authz（IDOR / 任意账户 / 提权）</span></span></span></span></td><td style="box-sizing: border-box;"><span cid="n61" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">465</span></span></span></span></td></tr><tr style="box-sizing: border-box;"><td style="box-sizing: border-box;"><span cid="n63" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">rce（反序列化 / SSTI / XXE / 框架）</span></span></span></span></td><td style="box-sizing: border-box;"><span cid="n64" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">385</span></span></span></span></td></tr><tr style="box-sizing: border-box;"><td style="box-sizing: border-box;"><span cid="n66" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">xss</span></span></span></span></td><td style="box-sizing: border-box;"><span cid="n67" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">335</span></span></span></span></td></tr><tr style="box-sizing: border-box;"><td style="box-sizing: border-box;"><span cid="n69" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">info-disclosure</span></span></span></span></td><td style="box-sizing: border-box;"><span cid="n70" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">319</span></span></span></span></td></tr><tr style="box-sizing: border-box;"><td style="box-sizing: border-box;"><span cid="n72" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">oauth-saml-jwt</span></span></span></span></td><td style="box-sizing: border-box;"><span cid="n73" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">240</span></span></span></span></td></tr><tr style="box-sizing: border-box;"><td style="box-sizing: border-box;"><span cid="n75" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">logic-flaws（CSRF / 点击劫持 / 支付）</span></span></span></span></td><td style="box-sizing: border-box;"><span cid="n76" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">234</span></span></span></span></td></tr><tr style="box-sizing: border-box;"><td style="box-sizing: border-box;"><span cid="n78" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">path-traversal / LFI / RFI</span></span></span></span></td><td style="box-sizing: border-box;"><span cid="n79" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">163</span></span></span></span></td></tr><tr style="box-sizing: border-box;"><td style="box-sizing: border-box;"><span cid="n81" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">sqli</span></span></span></span></td><td style="box-sizing: border-box;"><span cid="n82" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">147</span></span></span></span></td></tr><tr style="box-sizing: border-box;"><td style="box-sizing: border-box;"><span cid="n84" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">dos</span></span></span></span></td><td style="box-sizing: border-box;"><span cid="n85" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">138</span></span></span></span></td></tr><tr style="box-sizing: border-box;"><td style="box-sizing: border-box;"><span cid="n87" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">ssrf-cache-host</span></span></span></span></td><td style="box-sizing: border-box;"><span cid="n88" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">108</span></span></span></span></td></tr><tr style="box-sizing: border-box;"><td style="box-sizing: border-box;"><span cid="n90" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">unauth-access（默认凭据 / Actuator / 暴露服务）</span></span></span></span></td><td style="box-sizing: border-box;"><span cid="n91" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">46</span></span></span></span></td></tr><tr style="box-sizing: border-box;"><td style="box-sizing: border-box;"><span cid="n93" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">http-smuggling / CRLF</span></span></span></span></td><td style="box-sizing: border-box;"><span cid="n94" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">38</span></span></span></span></td></tr><tr style="box-sizing: border-box;"><td style="box-sizing: border-box;"><span cid="n96" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">api-rest / WebSocket</span></span></span></span></td><td style="box-sizing: border-box;"><span cid="n97" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">15</span></span></span></span></td></tr><tr style="box-sizing: border-box;"><td style="box-sizing: border-box;"><span cid="n99" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">file-upload</span></span></span></span></td><td style="box-sizing: border-box;"><span cid="n100" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">8</span></span></span></span></td></tr><tr style="box-sizing: border-box;"><td style="box-sizing: border-box;"><span cid="n102" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">mobile（Android / iOS）</span></span></span></span></td><td style="box-sizing: border-box;"><span cid="n103" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">8</span></span></span></span></td></tr><tr style="box-sizing: border-box;"><td style="box-sizing: border-box;"><span cid="n105" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">race-conditions</span></span></span></span></td><td style="box-sizing: border-box;"><span cid="n106" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">5</span></span></span></span></td></tr><tr style="box-sizing: border-box;"><td style="box-sizing: border-box;"><span cid="n108" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">llm-prompt-injection</span></span></span></span></td><td style="box-sizing: border-box;"><span cid="n109" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">1</span></span></span></span></td></tr><tr style="box-sizing: border-box;"><td style="box-sizing: border-box;"><span cid="n111" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">graphql</span></span></span></span></td><td style="box-sizing: border-box;"><span cid="n112" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">1</span></span></span></span></td></tr><tr style="box-sizing: border-box;"><td style="box-sizing: border-box;"><span cid="n114" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">intranet-postexp（内网 / 后渗透速查）</span></span></span></span></td><td style="box-sizing: border-box;"><span cid="n115" mdtype="table_cell" style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""><span textstyle="" style="font-size: 16px;">—</span></span></span></span></td></tr></tbody></table>## 数据来源  
- HackerOne hacktivity feed：2887 份已披露 High / Critical 报告，来源为公开数据。  
  
- WooYun 历史档案：覆盖 88,636 条案例，仅保留参数频率、案例 ID 和 bypass 模式等统计残余。  
  
- Payloader：305 条结构化 payload + 263 个 WAF / EDR 绕过步骤 + 114 条工具命令，原仓库为   
3516634930/Payloader  
。  
  
本项目只整理、翻译和重组公开资料，不包含专有数据，也不抓取需要认证的内容。  
## 红线  
  
每个 playbook 末尾都写了具体的边界，下面是抽出来的几个最常踩的点：  
- **样本控制**  
：SQLi 探测到库名 / 版本即可证明，不要 dump 数据；IDOR、Mongo / ES 拉数据 1–3 条样本就够，别全量。  
  
- **测试账号自演**  
：越权、密码重置、JWT 伪造、redirect_uri、XSS 盲打全部用自己注册的两个号互测，  
**不要碰陌生人的账号**  
——即使能。  
  
- **只读，不写**  
：拿到 RCE 只跑   
id  
 /   
whoami  
 /   
uname -a  
；Redis / Mongo 默认未授权只   
info  
 /   
ping  
 /   
db.version()  
；任意文件读看到   
root:x:  
 一行即停，不读   
/etc/shadow  
。  
  
- **不真做副作用动作**  
：不真发短信、不真扣款、不真发邮件、不真退款、不真覆盖文件、不真改公告 / 邮件模板。证明接口能调通 + 200 即停。  
  
- **DoS / 并发**  
：单次复现 ≤ 60s，串行做 5 次足够。竞态并发 50–100，绝不 1000+。短信 / 邮件不限速这种，发到自己手机 5–10 次为止。  
  
- **不留物**  
：webshell、heapdump、备份、dump 出来的源码——本地保存，报告后立即删除，不要 push 到 GitHub / 第三方网盘。  
  
- **凭据：拿到不用**  
：泄露的 AWS / Stripe / 数据库凭据，仅   
sts get-caller-identity  
 / 看 banner 验证，绝不用来扣款 / 发邮件 / 连接生产库。  
  
- **报告里所有 PII 脱敏**  
：手机号、邮箱、用户名、token、cookie 留前 2 + 后 2，必要时附 sha256 指纹证明拿到过原文。  
  
- **OOB 验证**  
：不要使用公开的公共 DNSLog 平台，使用厂商提供的 SSRF 测试平台，或自架 interactsh / 自有 DNSLog。  
  
- **没抓包就没发现**  
：所有断言都要有 HTTP 包 / 截图 / 视频，不要凭"应该"提交。  
  
具体到每类漏洞还有更细的限制（DoS 类最敏感、上传不留 webshell、读类只读 1 条样本等），看对应 playbook 的最后一节。  
###   
  
0x03 更新介绍  
```
更新更多漏洞漏洞检测SKILl分类
```  
  
  
0x04 使用介绍  
  
📦安装与使用指南  
#### Marketplace   
  
通过 Claude Code 插件市场一键安装，自动处理版本更新：  
```
# 1. 添加插件市场源
/plugin marketplace add MyuriKanao/src-hunter-skill
# 2. 安装 skill
/plugin install src-hunter@src-hunter
```  
> **优点**  
：后续可通过   
/plugin update  
 自动获取最新版本，无需手动维护。  
  
#### Git 手动安装  
  
如果你需要自定义修改 playbook 或处于离线环境，可通过 Git 手动克隆：  
```
# 安装到 Claude Code 的 skill 目录
下载：src-hunter-skill.zip
解压到：~/.claude/skills/src-hunter
```  
  
安装完成后，Claude Code 会自动识别该目录下的   
SKILL.md  
 并加载 skill。  
### 安装后验证  
  
安装完成后，在 Claude Code 中输入以下命令验证是否加载成功：  
```
/src-hunter --help
```  
  
或直接调用 skill 测试：  
```
/src-hunter https://example.com
```  
  
如果 Claude 返回了   
**intake → recon → enum → hunt → report**  
 的流程说明，即表示安装成功。  
###   
  
  
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
  
  
**公众号回复20260703获取下载、回复 加群 获取交流群**  
  
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
  
  

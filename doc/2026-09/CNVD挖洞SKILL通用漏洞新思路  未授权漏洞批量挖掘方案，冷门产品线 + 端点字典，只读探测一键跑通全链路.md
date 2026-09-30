#  CNVD挖洞SKILL通用漏洞新思路 | 未授权漏洞批量挖掘方案，冷门产品线 + 端点字典，只读探测一键跑通全链路  
sywinksvg
                    sywinksvg  渗透安全HackTwo   2026-09-29 23:53  
  
0x01 工具介绍  
  
cnvd-skill 是一套面向 CNVD 通用型未授权漏洞挖掘的开源便携工具包，主打冷门设备产品线横向批量只读验证。内置 9 个纯标准库 Python 脚本，无需额外安装依赖，覆盖 FOFA 多通道测绘、HTTP/RTSP/FTP 探测、查重抽验全链路；附带 8 组实战端点字典，采用双差分校验降低误报，内置蜜罐识别与访问限速机制。工具严格限定只读操作，固化合规扫描规则，可快速产出符合 CNVD 申报标准的案例素材，仅用于授权安全测试与正规漏洞提交。  
  
  
  
  
  
  
  
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
  
一套面向 **CNVD 通用型未授权漏洞**  
 的批量挖掘工作流工具包：  
- **Skill 本体**  
（skills/cnvd/SKILL.md  
）——完整流程 / 原则 / 判据 / 合规红线，不用 Claude Code 也能当操作手册人肉照着走  
  
- **9 个 Python 脚本**  
——纯标准库、零 pip 依赖，覆盖 **测绘 → 定级检测 → 查重抽验**  
 全链路  
  
- **8 文件 28 条端点字典**  
——来自实战验证的未授权端点，跨厂商可迁移（本包核心资产）  
  
- **脱敏示例 + 一键自检**  
——示例产出/  
 展示全部中间产物格式，校验.bat  
 双击完成环境自检与冒烟  
  
核心思路：**不做固件级深挖，做端点级的横向覆盖**  
。挑一个冷门产品线，用预置字典批量验证同类设备，命中 ≥10 台即够一个 CNVD 通用型漏洞的案例量。  
  
**运行语义（2.3）**  
：**无产出不停**  
 —— 唯一合法停机条件 = 已挖到 ≥1 个可提交级洞并落盘（命中 ≥10 + 查重无重复 + 复验通过 + 厂商资质达标 + 报告完成）。在此之前，换线 ≠ 收工：没出货就换下一条产品线，多条线重复就继续换，直到出货或用户喊停。合规红线在连续作业下执行强度不变。  
  
**厂商资质门槛（2.3）**  
：CNVD 发证硬门槛 = 通用型 + 厂商**注册资本实缴 > 5000 万人民币**  
 + 优先国内。甜点区 = **注册资本过线但知名度低的中型/小厂**  
——大厂是重复重灾区（白干），小厂不够发证资格。准备阶段须核验厂商工商信息（§1.1a），报告须含「厂商信息」段。  
  
**复现规范（2.3 · 反虚构铁律）**  
：复现步骤以最简单形式呈现——**优先 curl / 浏览器直接访问**  
；curl 无法表达时（特殊头部/POST/WebSocket）贴**真实数据包**  
。**禁止任何虚构**  
：所有请求/响应必须是实操原文，所有复现方式必须亲自跑过且能复现，没跑过的步骤不写进报告（详见 SKILL.md §1.4a）。  
## 工作流  
```
准备(10min) → 测绘(recon) → 批量验证(check) → 查重+抽验(agent) → 报告 → 下一条线
```  
<table><thead><tr style="box-sizing: border-box;background-color: rgb(255, 255, 255);border-top: 1px solid rgba(209, 217, 224, 0.7);"><th style="box-sizing: border-box;padding: 6px 13px;font-weight: 600;border: 1px solid rgb(209, 217, 224);"><section style="margin-top: 16px;margin-bottom: 16px;"><span leaf=""><span textstyle="" style="font-size: 16px;">阶段</span></span></section></th><th style="box-sizing: border-box;padding: 6px 13px;font-weight: 600;border: 1px solid rgb(209, 217, 224);"><section style="margin-top: 16px;margin-bottom: 16px;"><span leaf=""><span textstyle="" style="font-size: 16px;">工具</span></span></section></th><th style="box-sizing: border-box;padding: 6px 13px;font-weight: 600;border: 1px solid rgb(209, 217, 224);"><section style="margin-top: 16px;margin-bottom: 16px;"><span leaf=""><span textstyle="" style="font-size: 16px;">产出</span></span></section></th></tr></thead><tbody><tr style="box-sizing: border-box;background-color: rgb(255, 255, 255);border-top: 1px solid rgba(209, 217, 224, 0.7);"><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(209, 217, 224);"><section style="margin-top: 16px;margin-bottom: 16px;"><span leaf=""><span textstyle="" style="font-size: 16px;">0 测绘</span></span></section></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(209, 217, 224);"><code style="box-sizing: border-box;font-family: &#34;Monaspace Neon&#34;, ui-monospace, SFMono-Regular, &#34;SF Mono&#34;, Menlo, Consolas, &#34;Liberation Mono&#34;, monospace;font-size: 13.6px;tab-size: 4;white-space: break-spaces;background-color: rgba(129, 139, 152, 0.12);border-radius: 6px;margin: 0px;padding: 0.2em 0.4em;"><span leaf=""><span textstyle="" style="font-size: 16px;">cnvd_recon.py</span></span></code></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(209, 217, 224);"><section style="margin-top: 16px;margin-bottom: 16px;"><span leaf=""><span textstyle="" style="font-size: 16px;">目标存量 CSV（FOFA 多通道拉取）</span></span></section></td></tr><tr style="box-sizing: border-box;background-color: rgb(246, 248, 250);border-top: 1px solid rgba(209, 217, 224, 0.7);"><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(209, 217, 224);"><section style="margin-top: 16px;margin-bottom: 16px;"><span leaf=""><span textstyle="" style="font-size: 16px;">1 验证</span></span></section></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(209, 217, 224);"><code style="box-sizing: border-box;font-family: &#34;Monaspace Neon&#34;, ui-monospace, SFMono-Regular, &#34;SF Mono&#34;, Menlo, Consolas, &#34;Liberation Mono&#34;, monospace;font-size: 13.6px;tab-size: 4;white-space: break-spaces;background-color: rgba(129, 139, 152, 0.12);border-radius: 6px;margin: 0px;padding: 0.2em 0.4em;"><span leaf=""><span textstyle="" style="font-size: 16px;">cnvd_check.py</span></span></code></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(209, 217, 224);"><code style="box-sizing: border-box;font-family: &#34;Monaspace Neon&#34;, ui-monospace, SFMono-Regular, &#34;SF Mono&#34;, Menlo, Consolas, &#34;Liberation Mono&#34;, monospace;font-size: 13.6px;tab-size: 4;white-space: break-spaces;background-color: rgba(129, 139, 152, 0.12);border-radius: 6px;margin: 0px;padding: 0.2em 0.4em;"><span leaf=""><span textstyle="" style="font-size: 16px;">findings.csv</span></span></code><section style="margin-top: 16px;margin-bottom: 16px;"><span leaf=""><span textstyle="" style="font-size: 16px;">（三重防误报）</span></span></section></td></tr><tr style="box-sizing: border-box;background-color: rgb(255, 255, 255);border-top: 1px solid rgba(209, 217, 224, 0.7);"><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(209, 217, 224);"><section style="margin-top: 16px;margin-bottom: 16px;"><span leaf=""><span textstyle="" style="font-size: 16px;">1.5 查重+抽验</span></span></section></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(209, 217, 224);"><code style="box-sizing: border-box;font-family: &#34;Monaspace Neon&#34;, ui-monospace, SFMono-Regular, &#34;SF Mono&#34;, Menlo, Consolas, &#34;Liberation Mono&#34;, monospace;font-size: 13.6px;tab-size: 4;white-space: break-spaces;background-color: rgba(129, 139, 152, 0.12);border-radius: 6px;margin: 0px;padding: 0.2em 0.4em;"><span leaf=""><span textstyle="" style="font-size: 16px;">verify_*.py</span></span></code><section style="margin-top: 16px;margin-bottom: 16px;"><span leaf=""><span textstyle="" style="font-size: 16px;"> + 派发模板</span></span></section></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(209, 217, 224);"><code style="box-sizing: border-box;font-family: &#34;Monaspace Neon&#34;, ui-monospace, SFMono-Regular, &#34;SF Mono&#34;, Menlo, Consolas, &#34;Liberation Mono&#34;, monospace;font-size: 13.6px;tab-size: 4;white-space: break-spaces;background-color: rgba(129, 139, 152, 0.12);border-radius: 6px;margin: 0px;padding: 0.2em 0.4em;"><span leaf=""><span textstyle="" style="font-size: 16px;">verify_report.json</span></span></code></td></tr><tr style="box-sizing: border-box;background-color: rgb(246, 248, 250);border-top: 1px solid rgba(209, 217, 224, 0.7);"><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(209, 217, 224);"><section style="margin-top: 16px;margin-bottom: 16px;"><span leaf=""><span textstyle="" style="font-size: 16px;">2 报告</span></span></section></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(209, 217, 224);"><section style="margin-top: 16px;margin-bottom: 16px;"><span leaf=""><span textstyle="" style="font-size: 16px;">SKILL.md §1.4 流程</span></span></section></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(209, 217, 224);"><section style="margin-top: 16px;margin-bottom: 16px;"><span leaf=""><span textstyle="" style="font-size: 16px;">提交版 + 复现版报告 + 全量案例 IP</span></span></section></td></tr></tbody></table>## 特性  
- 三类协议只读探针：HTTP（GET/POST 双差分）、RTSP（严格 SDP 判据）、FTP（匿名 230 探测）  
  
- 双差分防误报：HTTP 命中后自动二次无 cookie 请求，结果不可复现即丢弃  
  
- 蜜罐排除：内置 ip-api 归属查询，云厂商段自动标 cloud_suspect 供人工复核  
  
- 严格 RTSP 判据：必须 DESCRIBE 200 且 SDP 含 v=0 + m=video/audio；OPTIONS-only 一律丢弃（实战返工教训固化）  
  
- FOFA 多通道：支持任意数量通道（官方格式 / 中转格式均可），失败自动切下一个  
  
- 断点续跑：--resume 跳过已完成目标，中断不丢进度  
  
- 合规内置：单 IP 严格串行 + 间隔 ≥1s（代码级固化），全链路只读  
  
- 零依赖：只需 Python 3.8+，不装任何第三方包  
  
0x03 更新介绍  
```
CNVD 通用型未授权漏洞广扫 Skill 便携包 | 冷门产品线 x 预置端点字典 x 批量只读验证。含 FOFA 多通道测绘、HTTP/RTSP/FTP 只读探针、双差分防误报、查重+抽验流程与合规红线。仅用于授权范围内的 CNVD 提交。
```  
###   
  
0x04 使用介绍  
  
📦安装与使用指南  
- **Python 3.8+**  
- Windows / Linux / macOS 均可（校验.bat  
 仅 Windows）  
  
**第 1 步：装 Python**  
（已有可跳过）  
  
到   
https://python.org  
 下载安装，装时勾选 "Add python.exe to PATH"。验证：命令行敲   
python --version  
 有输出即成功。  
  
**第 2 步：填自己的 FOFA key（必做）**  
  
包内   
api_keys.json  
 是  
**占位模板**  
（所有 key 都是   
REPLACE_ME  
）。编辑   
channels  
 数组配置你的查询通道：  
```
{
  "channels": [
    {
      "name": "primary",
      "enabled": true,
      "base_url": "https://你的中转站地址",
      "search_path": "/api/v1/search/all",
      "key_param": "key",
      "query_param": "qbase64",
      "key": "你的key"
    }
  ]
}
```  
- query_param  
：官方格式用   
qbase64  
（查询语句 base64 编码），中转明文格式用   
query  
  
- key_param  
：一般是   
key  
，个别站是   
api_key  
  
- 不同中转站格式不一样，对照你的站文档配置；配错会 404 或返回空  
  
- 支持任意数量通道：自动按顺序尝试，失败切下一个。加备用站复制一项改   
name  
 /   
base_url  
 即可  
  
> 不想先配 key？  
--help  
、字典（rules）、check 扫描都不需要 key，只有 recon 拉资产需要。  
  
  
**第 3 步：自检**  
  
双击   
校验.bat  
。全 ✅ 即可开工。会检查：① Python 可用 ② 各脚本能跑 ③ key 读得到 ④ FOFA 通道实测发一条小查询。  
### 最短命令  
```
cd tools\scripts
:: 1) 测绘：拉某类产品线存量（preset 可选 nvr/printer/industrial/network/access/nas/conference）
python cnvd_recon.py --preset nvr --out stage0_nvr.csv
:: 2) 整理 targets.csv（表头 ip,port 两列）后批量验证
python cnvd_check.py --targets targets.csv --rules ..\rules\rules_boa.json --geo --out findings.csv
:: 3) 命中 ≥10 → 按 SKILL.md §1.3 派 agent 查重 + 抽验
```  
> ⚠️ 输入格式别搞反：cnvd_check.py --targets 要的是 targets 格式（只有 ip,port 两列）；findings_样例-脱敏.csv 是 check 的输出格式，四列版（ip,port,rule_id,url）才是给抽验脚本的输入。  
  
  
****  
  
**0x05 内部VIP星球介绍-V1.5（福利）**  
  
          
如果你想学习更多**渗透测试技术/应急溯源/免杀工具/挖洞SRC赚取漏洞赏金/红队打点等**  
欢迎加入我们**内部星球**  
可获得内部工具字典和享受内部资源和  
内部交流群，  
**每天更新1day/0day漏洞刷分上分****(2026POC更新至18222+)**  
**，**  
包含全网一些**付费扫描****工具及内部原创的Burp自动化漏****洞探测插件/漏扫工具等，AI代审工具，挖洞技巧、挖洞SKILL等**  
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
https://t.zsxq.com/WfBxz  
  
****  
  
**👉****点击了解加入-->>内部VIP知识星球福利介绍V1.5版本-1day/0day漏洞库及内部资源更新**  
  
****  
  
  
结尾  
  
# 免责声明  
  
  
# 获取方法  
  
  
**公众号回复20260930获取下载、回复 加群 获取交流群**  
  
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
  
  

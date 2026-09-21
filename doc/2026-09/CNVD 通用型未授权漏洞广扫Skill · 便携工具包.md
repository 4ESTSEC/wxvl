#  CNVD 通用型未授权漏洞广扫Skill · 便携工具包  
sywinksvg
                    sywinksvg  夜组安全   2026-09-21 01:16  
  
免责声明  
  
由于传播、利用本公众号夜组安全所提供的信息而造成的任何直接或者间接的后果及损失，均由使用者本人负责，公众号夜组安全及作者不为此承担任何责任，一旦造成后果请自行承担！如有侵权烦请告知，我们会立即删除并致歉。谢谢！  
**所有工具安全性自测！！！VX：**  
**NightCTI**  
  
朋友们现在只对常读和星标的公众号才展示大图推送，建议大家把  
**夜组安全**  
“**设为星标**  
”，  
否则可能就看不到了啦！  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/icZ1W9s2Jp2WrOMH4AFgkSfEFMOvvFuVKmDYdQjwJ9ekMm4jiasmWhBicHJngFY1USGOZfd3Xg4k3iamUOT5DcodvA/640?wx_fmt=png&from=appmsg "")  
  
## 这是什么  
  
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
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/WibL3bOeESMJlnZ42B2MN31rJWjTdjJCKhCwGCdRPgRfReyiagMEy3hOTTdJTwDbp3nzNbR7Ujakw2sfPw3ibSJGOKfrricx2O0efRrrBicwKTV8/640?wx_fmt=png&from=appmsg "")  
  
**复现规范（2.3 · 反虚构铁律）**  
：复现步骤以最简单形式呈现——**优先 curl / 浏览器直接访问**  
；curl 无法表达时（特殊头部/POST/WebSocket）贴**真实数据包**  
。**禁止任何虚构**  
：所有请求/响应必须是实操原文，所有复现方式必须亲自跑过且能复现，没跑过的步骤不写进报告（详见 SKILL.md §1.4a）。  
## 工作流  
```
准备(10min) → 测绘(recon) → 批量验证(check) → 查重+抽验(agent) → 报告 → 下一条线
```  
  
<table><thead><tr><th style="color: rgb(66, 75, 93);font-size: 13px;line-height: 1.5em;letter-spacing: 0em;text-align: left;font-weight: bold;background: none left top / auto no-repeat scroll padding-box border-box rgb(240, 240, 240);height: auto;border-style: solid;border-width: 1px;border-color: rgba(204, 204, 204, 0.4);border-radius: 0px;padding: 5px 10px;min-width: 85px;"><section><span leaf="">阶段</span></section></th><th style="color: rgb(66, 75, 93);font-size: 13px;line-height: 1.5em;letter-spacing: 0em;text-align: left;font-weight: bold;background: none left top / auto no-repeat scroll padding-box border-box rgb(240, 240, 240);height: auto;border-style: solid;border-width: 1px;border-color: rgba(204, 204, 204, 0.4);border-radius: 0px;padding: 5px 10px;min-width: 85px;"><section><span leaf="">工具</span></section></th><th style="color: rgb(66, 75, 93);font-size: 13px;line-height: 1.5em;letter-spacing: 0em;text-align: left;font-weight: bold;background: none left top / auto no-repeat scroll padding-box border-box rgb(240, 240, 240);height: auto;border-style: solid;border-width: 1px;border-color: rgba(204, 204, 204, 0.4);border-radius: 0px;padding: 5px 10px;min-width: 85px;"><section><span leaf="">产出</span></section></th></tr></thead><tbody><tr style="color: rgb(66, 75, 93);background-attachment: scroll;background-clip: border-box;background-color: rgb(255, 255, 255);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;"><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">0 测绘</span></span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><code><span leaf=""><span textstyle="" style="font-size: 14px;">cnvd_recon.py</span></span></code></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">目标存量 CSV（FOFA 多通道拉取）</span></span></section></td></tr><tr style="color: rgb(66, 75, 93);background-attachment: scroll;background-clip: border-box;background-color: rgb(248, 248, 248);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;"><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">1 验证</span></span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><code><span leaf=""><span textstyle="" style="font-size: 14px;">cnvd_check.py</span></span></code></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><code><span leaf=""><span textstyle="" style="font-size: 14px;">findings.csv</span></span></code><section><span leaf=""><span textstyle="" style="font-size: 14px;">（三重防误报）</span></span></section></td></tr><tr style="color: rgb(66, 75, 93);background-attachment: scroll;background-clip: border-box;background-color: rgb(255, 255, 255);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;"><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">1.5 查重+抽验</span></span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><code><span leaf=""><span textstyle="" style="font-size: 14px;">verify_*.py</span></span></code><section><span leaf=""><span textstyle="" style="font-size: 14px;"> + 派发模板</span></span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><code><span leaf=""><span textstyle="" style="font-size: 14px;">verify_report.json</span></span></code></td></tr><tr style="color: rgb(66, 75, 93);background-attachment: scroll;background-clip: border-box;background-color: rgb(248, 248, 248);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;"><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">2 报告</span></span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">SKILL.md §1.4 流程</span></span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">提交版 + 复现版报告 + 全量案例 IP</span></span></section></td></tr></tbody></table>  
## 特性  
- **三类协议只读探针**  
：HTTP（GET/POST 双差分）、RTSP（严格 SDP 判据）、FTP（匿名 230 探测）  
  
- **双差分防误报**  
：HTTP 命中后自动二次无 cookie 请求，结果不可复现即丢弃  
  
- **蜜罐排除**  
：内置 ip-api 归属查询，云厂商段自动标 cloud_suspect  
 供人工复核  
  
- **严格 RTSP 判据**  
：必须 DESCRIBE 200  
 且 SDP 含 v=0  
 + m=video/audio  
；OPTIONS-only 一律丢弃（实战返工教训固化）  
  
- **FOFA 多通道**  
：支持任意数量通道（官方格式 / 中转格式均可），失败自动切下一个  
  
- **断点续跑**  
：--resume  
 跳过已完成目标，中断不丢进度  
  
- **合规内置**  
：单 IP 严格串行 + 间隔 ≥1s（代码级固化），全链路只读  
  
- **零依赖**  
：只需 Python 3.8+，不装任何第三方包  
  
## 快速开始  
### 环境要求  
- **Python 3.8+**（推荐 3.12）——唯一硬性依赖，脚本零第三方包  
  
- Windows / Linux / macOS 均可（校验.bat  
 仅 Windows）  
  
### 三步跑起来  
  
**第 1 步：装 Python**  
（已有可跳过）  
  
到 https://python.org 下载安装，装时勾选 "Add python.exe to PATH"。验证：命令行敲 python --version  
 有输出即成功。  
  
**第 2 步：填自己的 FOFA key（必做）**  
  
包内 api_keys.json  
 是**占位模板**  
（所有 key 都是 REPLACE_ME  
）。编辑 channels  
 数组配置你的查询通道：  
```
{  "channels": [    {      "name": "primary",      "enabled": true,      "base_url": "https://你的中转站地址",      "search_path": "/api/v1/search/all",      "key_param": "key",      "query_param": "qbase64",      "key": "你的key"    }  ]}
```  
- query_param  
：官方格式用 qbase64  
（查询语句 base64 编码），中转明文格式用 query  
  
- key_param  
：一般是 key  
，个别站是 api_key  
  
- 不同中转站格式不一样，对照你的站文档配置；配错会 404 或返回空  
  
- 支持任意数量通道：自动按顺序尝试，失败切下一个。加备用站复制一项改 name  
 / base_url  
 即可  
  
> 不想先配 key？--help  
、字典（rules）、check 扫描都不需要 key，只有 recon 拉资产需要。  
  
  
**第 3 步：自检**  
  
双击 校验.bat  
。全 ✅ 即可开工。会检查：① Python 可用 ② 各脚本能跑 ③ key 读得到 ④ FOFA 通道实测发一条小查询。  
### 最短命令  
```
cd tools\scripts:: 1) 测绘：拉某类产品线存量（preset 可选 nvr/printer/industrial/network/access/nas/conference）python cnvd_recon.py --preset nvr --out stage0_nvr.csv:: 2) 整理 targets.csv（表头 ip,port 两列）后批量验证python cnvd_check.py --targets targets.csv --rules ..\rules\rules_boa.json --geo --out findings.csv:: 3) 命中 ≥10 → 按 SKILL.md §1.3 派 agent 查重 + 抽验
```  
> ⚠️ **输入格式别搞反**  
：cnvd_check.py --targets  
 要的是 targets 格式（**只有 ip,port 两列**  
）；findings_样例-脱敏.csv  
 是 check 的**输出**  
格式，四列版（ip,port,rule_id,url  
）才是给抽验脚本的输入。  
  
## 合规红线（务必遵守）  
```
只做：只读 GET/OPTIONS/DESCRIBE/LIST ｜ 单 IP 间隔 ≥1s ｜ 完整操作日志 ｜ 仅用于 CNVD 提交禁止：改配置/删数据 ｜ 下载敏感文件 ｜ 暴力破解/横向 ｜ 提交前公开细节 ｜ 无授权扫政府/军事/关基
```  
- 本工具**只做未授权访问验证**  
（证明"能读到什么"），不提供任何写入 / 利用 / 持久化能力  
  
- 使用前确认你对目标资产**有测试授权**  
，或目标为公开致谢范围  
  
- 漏洞细节在平台披露前不得公开  
  
## 免责声明  
  
本工具仅供**授权范围内的安全测试**  
与 **CNVD 等平台的正规漏洞提交**  
使用。使用者应确保其行为符合《网络安全法》等相关法律法规，并自行承担因不当使用产生的一切责任。作者不对任何滥用行为负责。  
  
## 工具获取  
  
  
  
点击关注下方名片  
进入公众号  
  
回复关键字【  
260921  
】获取  
下载链接  
  
  
## 往期精彩  
  
  
往期推荐  
  
[渗透武器库、渗透工具箱 | 专为渗透测试从业者开发的一站式工具启动器](https://mp.weixin.qq.com/s?__biz=Mzk0ODM0NDIxNQ==&mid=2247497712&idx=1&sn=dffc1878ca3abee54f04de6e3d331783&scene=21#wechat_redirect)  
  
  
[AI 驱动的自动化渗透测试平台 | AI漏洞挖掘系统 - 黑板架构 / 模型分级 / 结果验证铁律](https://mp.weixin.qq.com/s?__biz=Mzk0ODM0NDIxNQ==&mid=2247497704&idx=1&sn=fd2e8ff5246b2030696dab42bc80f2ab&scene=21#wechat_redirect)  
  
  
[DSH RedTeam 模式：红队作战指挥台——一个靶标名称拉起信息收集/漏洞检测/漏洞利用/内网渗透四个角色](https://mp.weixin.qq.com/s?__biz=Mzk0ODM0NDIxNQ==&mid=2247497703&idx=1&sn=fab9a9feec8879427e2a514bea4a8c79&scene=21#wechat_redirect)  
  
  
[GitHub C2红队指挥平台 MCP接入AI，全自动渗透必备](https://mp.weixin.qq.com/s?__biz=Mzk0ODM0NDIxNQ==&mid=2247497686&idx=1&sn=4a6cda2c6b7a7cc481311f6ca6634ae0&scene=21#wechat_redirect)  
  
  
[dddd-Next 自动化资产测绘 + 漏洞扫描工具，覆盖指纹识别、弱口令、nuclei POC、Shiro 专项检测和 HTML 报告](https://mp.weixin.qq.com/s?__biz=Mzk0ODM0NDIxNQ==&mid=2247497685&idx=1&sn=c82165f4f3f86a5f1ea97e63f78c7abc&scene=21#wechat_redirect)  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/OAmMqjhMehrtxRQaYnbrvafmXHe0AwWLr2mdZxcg9wia7gVTfBbpfT6kR2xkjzsZ6bTTu5YCbytuoshPcddfsNg/640?wx_fmt=other&wxfrom=5&wx_lazy=1&wx_co=1&random=0.8399406679299557&tp=webp "")  
  

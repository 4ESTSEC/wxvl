#  AI 驱动的自动化渗透测试平台 | AI漏洞挖掘系统 - 黑板架构 / 模型分级 / 结果验证铁律  
1nceSec
                    1nceSec  夜组安全   2026-09-17 23:30  
  
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
  
## 这是什么？  
  
Trocar 是一个 AI 自主渗透测试系统。给它一个 URL，它会自动完成 JS 分析、接口发现、漏洞利用和报告生成。  
  
五阶段「外科手术」模式：**威胁建模 → 精准打击 → 深度反思 → 绕过探索 → 深度验证**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/WibL3bOeESMI5zaetic3f3KK0VQOd0nNnbZkicJgn72QwvuVzaLkJCpycng9WEP78VicQugiaw6LiaV17ibfPJ7sYB6boebLiacQHPz5Q2uWkmgmUFI/640?wx_fmt=png&from=appmsg "")  
  
## 核心特性  
- **黑板架构**  
 — 五分区持久化记忆（攻击面 / 已验证发现 / 待验证假设 / 利用原语 / 失败记录），避免重复探测  
  
- **34 个漏洞知识库**  
 — 覆盖 IDOR、SQLi、XSS、SSRF、RCE、JWT、GraphQL、WebSocket 等主流漏洞类型，特征匹配自动加载测试模块  
  
- **思考强度**  
 — 5 级可调（Low / Medium / High / XHigh / Max），控制模型推理深度和 token 消耗  
  
- **验证铁律**  
 — 所有漏洞发现必须有真实 curl 请求证据，模型自述「我认为」不算数  
  
- **多视角轮转**  
 — 6 个分析视角覆盖 4 个阶段，像一个专家团队系统化覆盖  
  
- **自动收敛**  
 — 连续 N 轮无新发现自动停止，不浪费 token  
  
- **浏览器引导**  
 — 首次启动在浏览器填写 API Key，无需手动编辑配置文件  
  
- **多模型支持**  
 — Anthropic Claude / OpenAI / DeepSeek / 通义千问 / 智谱 GLM 及任何 OpenAI 兼容 API  
  
- **实时推送**  
 — WebSocket 实时展示 AI 测试过程和工具执行结果  
  
- **安全沙箱**  
 — 内置危险命令拦截 + 文件写入路径限制  
  
- **并发会话**  
 — 同时测试多个目标  
  
- **交接文档**  
 — 一键生成会话交接文档（黑板状态+已确认漏洞+攻击面+失败记录），支持会话恢复  
  
- **Docker 部署**  
 — 一条命令启动  
  
## 多模型支持  
  
<table><thead><tr><th style="color: rgb(66, 75, 93);font-size: 13px;line-height: 1.5em;letter-spacing: 0em;text-align: left;font-weight: bold;background: none left top / auto no-repeat scroll padding-box border-box rgb(240, 240, 240);height: auto;border-style: solid;border-width: 1px;border-color: rgba(204, 204, 204, 0.4);border-radius: 0px;padding: 5px 10px;min-width: 85px;"><section><span leaf="">提供商</span></section></th><th style="color: rgb(66, 75, 93);font-size: 13px;line-height: 1.5em;letter-spacing: 0em;text-align: left;font-weight: bold;background: none left top / auto no-repeat scroll padding-box border-box rgb(240, 240, 240);height: auto;border-style: solid;border-width: 1px;border-color: rgba(204, 204, 204, 0.4);border-radius: 0px;padding: 5px 10px;min-width: 85px;"><section><span leaf="">Base URL</span></section></th><th style="color: rgb(66, 75, 93);font-size: 13px;line-height: 1.5em;letter-spacing: 0em;text-align: left;font-weight: bold;background: none left top / auto no-repeat scroll padding-box border-box rgb(240, 240, 240);height: auto;border-style: solid;border-width: 1px;border-color: rgba(204, 204, 204, 0.4);border-radius: 0px;padding: 5px 10px;min-width: 85px;"><section><span leaf="">模型</span></section></th></tr></thead><tbody><tr style="color: rgb(66, 75, 93);background-attachment: scroll;background-clip: border-box;background-color: rgb(255, 255, 255);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;"><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">Anthropic</span></span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><code><span leaf=""><span textstyle="" style="font-size: 12px;">https://api.anthropic.com</span></span></code></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><code><span leaf=""><span textstyle="" style="font-size: 12px;">claude-sonnet-5</span></span></code></td></tr><tr style="color: rgb(66, 75, 93);background-attachment: scroll;background-clip: border-box;background-color: rgb(248, 248, 248);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;"><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">DeepSeek</span></span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><code><span leaf=""><span textstyle="" style="font-size: 12px;">https://api.deepseek.com/v1</span></span></code></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><code><span leaf=""><span textstyle="" style="font-size: 12px;">deepseek-v4-pro</span></span></code></td></tr><tr style="color: rgb(66, 75, 93);background-attachment: scroll;background-clip: border-box;background-color: rgb(255, 255, 255);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;"><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">通义千问</span></span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><code><span leaf=""><span textstyle="" style="font-size: 12px;">https://dashscope.aliyuncs.com/compatible-mode/v1</span></span></code></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><code><span leaf=""><span textstyle="" style="font-size: 12px;">qwen-3.7-max</span></span></code></td></tr><tr style="color: rgb(66, 75, 93);background-attachment: scroll;background-clip: border-box;background-color: rgb(248, 248, 248);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;"><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">智谱 GLM</span></span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><code><span leaf=""><span textstyle="" style="font-size: 12px;">https://open.bigmodel.cn/api/paas/v4</span></span></code></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><code><span leaf=""><span textstyle="" style="font-size: 12px;">glm-5.3</span></span></code></td></tr><tr style="color: rgb(66, 75, 93);background-attachment: scroll;background-clip: border-box;background-color: rgb(255, 255, 255);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;"><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">Ollama</span></span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><code><span leaf=""><span textstyle="" style="font-size: 12px;">http://127.0.0.1:11434/v1</span></span></code></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><code><span leaf=""><span textstyle="" style="font-size: 12px;">llama3.1</span></span></code></td></tr><tr style="color: rgb(66, 75, 93);background-attachment: scroll;background-clip: border-box;background-color: rgb(248, 248, 248);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;"><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 12px;">OpenAI</span></span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><code><span leaf=""><span textstyle="" style="font-size: 12px;">https://api.openai.com/v1</span></span></code></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><code><span leaf=""><span textstyle="" style="font-size: 12px;">gpt-4o</span></span></code></td></tr></tbody></table>  
> 注意：非 Claude 模型的效果取决于模型自身的安全知识和推理能力。  
  
## 工作流程  
```
输入: https://target.com阶段1: 威胁建模  视角: 攻击面侦察 → curl 抓页面、探测敏感路径、收集接口  视角: JS 逆向分析 → 下载 JS、搜索硬编码密钥/凭证阶段2: 精准打击  视角: 未授权探测  → 逐个接口测试无认证访问  视角: 漏洞利用构造 → 对假设构造 PoC，用真实请求验证阶段3: 绕过探索  视角: 绕过 403   → 路径截断、参数污染、UA 欺骗...阶段4: 深度验证  视角: 深度验证   → 评估真实影响、扩大攻击面连续 N 轮无新发现自动停止
```  
## 交接文档  
  
每个测试会话结束后，可以一键生成交接文档，完整导出当前会话的测试状态：  
- 黑板五分区数据（攻击面、已验证漏洞、待验证假设、利用原语、失败记录）  
  
- 已确认漏洞列表及 PoC  
  
- AI 对话历史摘要  
  
**使用方式：**  
- 界面：点击会话卡片上的「交接文档」按钮  
  
- API：GET /api/sessions/{id}/handoff  
  
**典型场景：**  
- 测试中断后恢复：导出当前进度，新会话从断点继续  
  
- 团队协作：将测试成果交接给其他成员  
  
- 归档留存：保存完整的测试过程和发现  
  
  
  
## 工具获取  
  
  
  
点击关注下方名片  
进入公众号  
  
回复关键字【  
260918  
】获取  
下载链接  
  
  
## 往期精彩  
  
  
往期推荐  
  
[DSH RedTeam 模式：红队作战指挥台——一个靶标名称拉起信息收集/漏洞检测/漏洞利用/内网渗透四个角色](https://mp.weixin.qq.com/s?__biz=Mzk0ODM0NDIxNQ==&mid=2247497703&idx=1&sn=fab9a9feec8879427e2a514bea4a8c79&scene=21#wechat_redirect)  
  
  
[GitHub C2红队指挥平台 MCP接入AI，全自动渗透必备](https://mp.weixin.qq.com/s?__biz=Mzk0ODM0NDIxNQ==&mid=2247497686&idx=1&sn=4a6cda2c6b7a7cc481311f6ca6634ae0&scene=21#wechat_redirect)  
  
  
[dddd-Next 自动化资产测绘 + 漏洞扫描工具，覆盖指纹识别、弱口令、nuclei POC、Shiro 专项检测和 HTML 报告](https://mp.weixin.qq.com/s?__biz=Mzk0ODM0NDIxNQ==&mid=2247497685&idx=1&sn=c82165f4f3f86a5f1ea97e63f78c7abc&scene=21#wechat_redirect)  
  
  
[EduSRC 教育行业漏洞猎洞工具 —— 基于搜索引擎 Dork 的一键式侦察 Chrome 扩展](https://mp.weixin.qq.com/s?__biz=Mzk0ODM0NDIxNQ==&mid=2247497655&idx=1&sn=36b38d3d6c80efc41a4a74da0d3869ef&scene=21#wechat_redirect)  
  
  
[DeepSeek-V4.1 / V4 Flash 网络安全红队工具（无限四代）v0.3.0 纯净红队版](https://mp.weixin.qq.com/s?__biz=Mzk0ODM0NDIxNQ==&mid=2247497643&idx=1&sn=beef3f54f260361eb96dc8677d124448&scene=21#wechat_redirect)  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/OAmMqjhMehrtxRQaYnbrvafmXHe0AwWLr2mdZxcg9wia7gVTfBbpfT6kR2xkjzsZ6bTTu5YCbytuoshPcddfsNg/640?wx_fmt=other&wxfrom=5&wx_lazy=1&wx_co=1&random=0.8399406679299557&tp=webp "")  
  

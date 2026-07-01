#  ARL-Next 灯塔二开版 | 企业级自动化资产侦察与漏洞监控“灯塔”  
owl234
                    owl234  夜组安全   2026-06-30 23:30  
  
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
  
## 什么是 ARL-Next？  
  
ARL-Next 旨在为安全团队、红蓝对抗工程师及 Bug Bounty 猎人提供一套**开箱即用**  
的资产全周期监控与漏洞发现平台。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/WibL3bOeESMK6gfSt9yRjk98lJkZy5eN1iaXc6c2ib5vlEnwyXM3J28dibKCc7mkVynnOPT4fnDybaIdCEmAiaicRfLENia7wlyQoic00TnIeY3SuBY/640?wx_fmt=png&from=appmsg "")  
  
彻底告别繁杂的脚本拼接，ARL-Next 将资产发现、端口扫描、指纹识别到漏洞探测融为一体，并通过现代化的 Vue 控制台为您呈现全局安全态势。  
### 为什么要重构 ARL-Next？(与原版的区别)  
  
随着网络环境的演变和安全人员需求的升级，我们对原项目进行了现代化改造，以解决原版在当今复杂环境下的部分痛点：  
- 现代化前端栈：彻底重构前端，基于 Vue 3 + 现代 UI 框架构建，提供更流畅的交互体验和更直观的数据大屏。  
  
- 部署与运维架构解耦：引入全面的 Docker 化与 CI/CD 自动化构建流程，支持 Cloudflare Tunnel 零端口暴露部署，数据持久化（Volumes）更加安全可靠。  
  
## 核心特性  
- **🛡️ 零信任架构与全链路 HTTPS (New)**  
彻底解决浏览器安全限制与公网暴露风险。本地开发采用 mkcert  
 无缝实现 HTTPS 热重载；生产环境引入 Cloudflare Tunnel 内网穿透，服务器实现**“零入站端口”**  
暴露，完美隐身于公网扫描器与 DDoS 攻击之外，并享受免维护的长效边缘证书。  
  
- **🔥 现代化前后端分离架构**  
彻底告别臃肿。前端基于 Vue 3 + Vite 构建现代化 SPA 面板，后端依托 Python 3.8+ 与 Flask 提供纯粹的 RESTful API。这带来了毫秒级的交互体验和极佳的二次开发扩展性。  
  
- **⚡ 全自动 CI/CD 敏捷交付**  
时间应该花在挖掘逻辑上，而不是运维上。本项目已完全打通 GitHub Actions 自动化流水线。代码一经 Push，系统自动在云端构建不可变的纯净 Docker 镜像，并跨网络全自动部署到你的生产节点，实现基础设施的“丝滑热更”。  
  
- **🌍 生产级分布式调度与高并发**  
以 RabbitMQ 为消息中枢，Celery 分布式工作节点为执行引擎。你可以轻松将核心扫描 Worker 和专门的 GitHub 敏感信息监控 Worker 分散部署，实现真正的高并发多节点协同扫描。  
  
- **📸 现代 Web 无头渲染引擎 (New)**  
彻底重构了底层截图与指纹探测引擎，抛弃陈旧的 PhantomJS，全面拥抱原生 Chromium 与 Puppeteer。完美支持 React、Vue 等现代 SPA（单页面应用）的智能长截图和 Wappalyzer 深度指纹解析，内置中文字库彻底解决乱码问题，让每一次资产快照都如肉眼所见般精准。  
  
- **⚔️ 硬核武器库无缝集成**  
内置庞大且不断更新的 ARL-NPoC 武器库，并无缝对接 FOFA 等第三方资产引擎。结合资产梳理、指纹识别、端口扫描，实现从“发现资产”到“自动打出 Payload”的完整闭环。  
  
## 配置管理与数据安全  
  
本项目在生产环境严格遵循 DevSecOps 标准，杜绝任何密钥硬编码泄露风险：  
### 1. 防泄漏配置隔离机制  
- **代码库原则**  
：Git 仓库中仅保留 backend/config-docker.example.yaml  
 模板文件，不包含任何真实密码或 Token。  
  
- **本地生效原则**  
：真正控制生产环境的 .env  
（存储随机生成的数据库密码）和 config-docker.yaml  
（存储第三方 API 密钥）已经被 .gitignore  
 保护。CI/CD 拉取最新代码时**绝对不会**  
覆盖或抹除您在服务器上配置的凭据。  
  
### 2. 业务数据 0 丢失 (Volumes 持久化)  
  
每次 CI/CD 自动部署本质上是销毁旧容器并使用新镜像启动新容器。我们通过 Docker Named Volumes 实现了数据的完全解耦：  
- arl_db_test  
：持久化存储 MongoDB 中的所有扫描资产与漏洞数据。  
  
- arl_tmp_test  
：存储报表等临时文件。  
  
- arl_screenshot_test  
：存储系统自动截取的资产快照。  
  
- arl_upload_poc_test  
：存储用户自定义上传的 PoC 文件。**结论：只要不手动执行 docker volume rm，任何高频的代码自动更新都不会导致您的业务数据丢失。**  
  
## 环境隔离架构设计  
  
为了兼顾“极致的开发效率”与“铁桶般的生产安全”，项目做了严格的环境分离：  
  
<table><thead><tr><th style="color: rgb(66, 75, 93);font-size: 13px;line-height: 1.5em;letter-spacing: 0em;font-weight: bold;background: none left top / auto no-repeat scroll padding-box border-box rgb(240, 240, 240);height: auto;border-style: solid;border-width: 1px;border-color: rgba(204, 204, 204, 0.4);border-radius: 0px;padding: 5px 10px;min-width: 85px;text-align: left;"><section><span leaf="">特性</span></section></th><th style="color: rgb(66, 75, 93);font-size: 13px;line-height: 1.5em;letter-spacing: 0em;font-weight: bold;background: none left top / auto no-repeat scroll padding-box border-box rgb(240, 240, 240);height: auto;border-style: solid;border-width: 1px;border-color: rgba(204, 204, 204, 0.4);border-radius: 0px;padding: 5px 10px;min-width: 85px;text-align: left;"><section><span leaf="">本地开发环境 (</span><code><span leaf="">docker-compose.local.yml</span></code><span leaf="">)</span></section></th><th style="color: rgb(66, 75, 93);font-size: 13px;line-height: 1.5em;letter-spacing: 0em;font-weight: bold;background: none left top / auto no-repeat scroll padding-box border-box rgb(240, 240, 240);height: auto;border-style: solid;border-width: 1px;border-color: rgba(204, 204, 204, 0.4);border-radius: 0px;padding: 5px 10px;min-width: 85px;text-align: left;"><section><span leaf="">生产/CI 环境 (</span><code><span leaf="">docker-compose.test.yml</span></code><span leaf="">)</span></section></th></tr></thead><tbody><tr style="color: rgb(66, 75, 93);background-attachment: scroll;background-clip: border-box;background-color: rgb(255, 255, 255);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;"><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;text-align: left;"><strong style="color: rgb(66, 75, 93);font-weight: bold;background-attachment: scroll;background-clip: border-box;background-color: rgba(0, 0, 0, 0);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;margin-top: 0px;margin-bottom: 0px;margin-left: 0px;margin-right: 0px;padding-top: 0px;padding-bottom: 0px;padding-left: 0px;padding-right: 0px;border-top-style: none;border-bottom-style: none;border-left-style: none;border-right-style: none;border-top-width: 3px;border-bottom-width: 3px;border-left-width: 3px;border-right-width: 3px;border-top-color: rgba(0, 0, 0, 0.4);border-bottom-color: rgba(0, 0, 0, 0.4);border-left-color: rgba(0, 0, 0, 0.4);border-right-color: rgba(0, 0, 0, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><span leaf="">代码运行方式</span></strong></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;text-align: left;"><strong style="color: rgb(66, 75, 93);font-weight: bold;background-attachment: scroll;background-clip: border-box;background-color: rgba(0, 0, 0, 0);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;margin-top: 0px;margin-bottom: 0px;margin-left: 0px;margin-right: 0px;padding-top: 0px;padding-bottom: 0px;padding-left: 0px;padding-right: 0px;border-top-style: none;border-bottom-style: none;border-left-style: none;border-right-style: none;border-top-width: 3px;border-bottom-width: 3px;border-left-width: 3px;border-right-width: 3px;border-top-color: rgba(0, 0, 0, 0.4);border-bottom-color: rgba(0, 0, 0, 0.4);border-left-color: rgba(0, 0, 0, 0.4);border-right-color: rgba(0, 0, 0, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><span leaf="">挂载覆写 (Bind Mounts)</span></strong><section><span leaf="">: 宿主机的 </span><code><span leaf="">./backend</span></code><span leaf=""> 直接映射入容器。</span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;text-align: left;"><strong style="color: rgb(66, 75, 93);font-weight: bold;background-attachment: scroll;background-clip: border-box;background-color: rgba(0, 0, 0, 0);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;margin-top: 0px;margin-bottom: 0px;margin-left: 0px;margin-right: 0px;padding-top: 0px;padding-bottom: 0px;padding-left: 0px;padding-right: 0px;border-top-style: none;border-bottom-style: none;border-left-style: none;border-right-style: none;border-top-width: 3px;border-bottom-width: 3px;border-left-width: 3px;border-right-width: 3px;border-top-color: rgba(0, 0, 0, 0.4);border-bottom-color: rgba(0, 0, 0, 0.4);border-left-color: rgba(0, 0, 0, 0.4);border-right-color: rgba(0, 0, 0, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><span leaf="">不可变基础设施 (Immutable)</span></strong><section><span leaf="">: 容器 100% 运行从 GHCR 拉取的纯净只读镜像。</span></section></td></tr><tr style="color: rgb(66, 75, 93);background-attachment: scroll;background-clip: border-box;background-color: rgb(248, 248, 248);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;"><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;text-align: left;"><strong style="color: rgb(66, 75, 93);font-weight: bold;background-attachment: scroll;background-clip: border-box;background-color: rgba(0, 0, 0, 0);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;margin-top: 0px;margin-bottom: 0px;margin-left: 0px;margin-right: 0px;padding-top: 0px;padding-bottom: 0px;padding-left: 0px;padding-right: 0px;border-top-style: none;border-bottom-style: none;border-left-style: none;border-right-style: none;border-top-width: 3px;border-bottom-width: 3px;border-left-width: 3px;border-right-width: 3px;border-top-color: rgba(0, 0, 0, 0.4);border-bottom-color: rgba(0, 0, 0, 0.4);border-left-color: rgba(0, 0, 0, 0.4);border-right-color: rgba(0, 0, 0, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><span leaf="">热重载 (Hot Reload)</span></strong></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;text-align: left;"><section><span leaf="">✅ 支持。保存代码瞬间生效，方便联调。</span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;text-align: left;"><section><span leaf="">❌ 不支持。彻底杜绝“线上直接改代码”造成的幽灵 Bug。</span></section></td></tr><tr style="color: rgb(66, 75, 93);background-attachment: scroll;background-clip: border-box;background-color: rgb(255, 255, 255);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;"><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;text-align: left;"><strong style="color: rgb(66, 75, 93);font-weight: bold;background-attachment: scroll;background-clip: border-box;background-color: rgba(0, 0, 0, 0);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;margin-top: 0px;margin-bottom: 0px;margin-left: 0px;margin-right: 0px;padding-top: 0px;padding-bottom: 0px;padding-left: 0px;padding-right: 0px;border-top-style: none;border-bottom-style: none;border-left-style: none;border-right-style: none;border-top-width: 3px;border-bottom-width: 3px;border-left-width: 3px;border-right-width: 3px;border-top-color: rgba(0, 0, 0, 0.4);border-bottom-color: rgba(0, 0, 0, 0.4);border-left-color: rgba(0, 0, 0, 0.4);border-right-color: rgba(0, 0, 0, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><span leaf="">镜像构建</span></strong></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;text-align: left;"><section><span leaf="">每次启动需在本地 </span><code><span leaf="">build</span></code><span leaf="">。</span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;text-align: left;"><section><span leaf="">由 GitHub Actions 统一构建，节点仅需 </span><code><span leaf="">pull</span></code><span leaf="">。</span></section></td></tr><tr style="color: rgb(66, 75, 93);background-attachment: scroll;background-clip: border-box;background-color: rgb(248, 248, 248);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;"><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;text-align: left;"><strong style="color: rgb(66, 75, 93);font-weight: bold;background-attachment: scroll;background-clip: border-box;background-color: rgba(0, 0, 0, 0);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;margin-top: 0px;margin-bottom: 0px;margin-left: 0px;margin-right: 0px;padding-top: 0px;padding-bottom: 0px;padding-left: 0px;padding-right: 0px;border-top-style: none;border-bottom-style: none;border-left-style: none;border-right-style: none;border-top-width: 3px;border-bottom-width: 3px;border-left-width: 3px;border-right-width: 3px;border-top-color: rgba(0, 0, 0, 0.4);border-bottom-color: rgba(0, 0, 0, 0.4);border-left-color: rgba(0, 0, 0, 0.4);border-right-color: rgba(0, 0, 0, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><span leaf="">外部流量接入</span></strong></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;text-align: left;"><code><span leaf="">localhost</span></code><section><span leaf=""> 直连测试。</span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;text-align: left;"><section><span leaf="">Cloudflare Tunnel 加密隧道，服务器真正实现“零入站端口”。</span></section></td></tr></tbody></table>  
## 工具部署  
  
我们为开发者提供了两套编排方案，以平衡“开发效率”与“生产稳定”。  
### 方案 A：本地极速迭代调试流  
  
适用于二次开发、编写 PoC 与前后端联调。采用热重载机制，代码修改浏览器/接口即刻生效。  
### Windows 版  
  
**1.克隆代码**  
```
git clone https://github.com/owl234/arl-nextcd arl-next
```  
  
**2. 准备本地受信任证书 (必须)**  
为了让本地联调拥有合法的 HTTPS 绿锁并解决跨域问题，需先生成本地证书：  
```
# 1. 在项目根目录下载 mkcert (Windows 为例)Invoke-WebRequest -Uri "https://github.com/FiloSottile/mkcert/releases/download/v1.4.4/mkcert-v1.4.4-windows-amd64.exe" -OutFile "mkcert.exe"# 2. 将根证书安装到系统信任库 (如遇弹窗请点击"是").\mkcert.exe -install# 3. 创建目录并签发 localhost 证书，完成后将文件重命名为 localhost.pem 和 localhost-key.pemmkdir certscd certs..\mkcert.exe localhost 127.0.0.1
```  
  
(注意：certs/ 目录已加入 .gitignore 防止私钥泄露)  
  
**3. 启动本地环境**  
```
# 启动后端底座（挂载本地源码，暴露 5003 端口供 Vite 代理）docker-compose -f docker-compose.local.yml build backenddocker-compose -f docker-compose.local.yml up -d backend worker worker-github scheduler mongodb rabbitmq# 另起终端启动前端（原生支持 https://localhost:3000）cd frontendnpm install -g pnpmpnpm installpnpm run dev
```  
### Mac版  
  
**1.克隆代码**  
```
git clone https://github.com/owl234/arl-nextcd arl-next
```  
  
**2. 准备本地受信任证书 (必须)**  
为了让本地联调拥有合法的 HTTPS 绿锁并解决跨域问题，需先生成本地证书。在 macOS 上，推荐使用 Homebrew 来安装mkcert  
：  
```
# 1. 使用 Homebrew 安装 mkcert（如果你的 Mac 还没安装 Homebrew，需先安装）brew install mkcert# (可选) 如果你使用 Firefox 浏览器进行调试，建议额外执行 brew install nss# 2. 将根证书安装到系统信任库 (可能需要输入你的 Mac 开机密码或验证 Touch ID)mkcert -install# 3. 创建目录并签发 localhost 证书，这里直接指定输出符合项目要求的文件名mkdir certscd certsmkcert -cert-file localhost.pem -key-file localhost-key.pem localhost 127.0.0.1
```  
  
(注意：certs/ 目录已加入 .gitignore 防止私钥泄露)  
  
**3. 启动本地环境**  
  
**启动后端底座**  
（挂载本地源码，暴露 5003 端口供 Vite 代理）： 请确保你的 Mac 已经启动了 Docker Desktop。  
```
# 退回项目根目录 (如果你刚刚在 certs 目录下)cd ..# 构建并启动后端与核心依赖docker-compose -f docker-compose.local.yml build backenddocker-compose -f docker-compose.local.yml up -d backend worker worker-github scheduler mongodb rabbitmq
```  
  
**启动前端开发服务器**  
（原生支持 https://localhost:3000）： 请确保你的 Mac 已经安装了 Node.js 环境。另开启一个新的终端窗口执行：  
```
cd frontendnpm install -g pnpmpnpm installpnpm run dev
```  
  
  
## 工具获取  
  
  
  
点击关注下方名片  
进入公众号  
  
回复关键字【  
260701  
】获取  
下载链接  
  
  
## 往期精彩  
  
  
[开源企业级主机与容器安全管理平台 | 安全基线、资产管理、漏洞扫描、病毒查杀、EDR 检测与合规审计2026-06-30](https://mp.weixin.qq.com/s?__biz=Mzk0ODM0NDIxNQ==&mid=2247497162&idx=1&sn=774dcb6cc88a2d5a34bb0830a09d7ed5&scene=21#wechat_redirect)  
[一款针对Spring漏洞框架进行快速利用的图形化工具2026-06-29](https://mp.weixin.qq.com/s?__biz=Mzk0ODM0NDIxNQ==&mid=2247497150&idx=1&sn=7c61498269bf756cb7458a57f4b0c830&scene=21#wechat_redirect)  
[Cybersparker 攻击面管理利用系统2026-06-26](https://mp.weixin.qq.com/s?__biz=Mzk0ODM0NDIxNQ==&mid=2247497140&idx=1&sn=a7a4a46aa3ac30c9fa272db02d9be953&scene=21#wechat_redirect)  
[LLM SecRange · 大模型攻防渗透测试靶场2026-06-25](https://mp.weixin.qq.com/s?__biz=Mzk0ODM0NDIxNQ==&mid=2247497129&idx=1&sn=d3dd59233be71fa4c0d3ed5743399f42&scene=21#wechat_redirect)  
[AI赋能！一个面向 Windows 主机应急响应场景的轻量化图形客户端2026-06-24](https://mp.weixin.qq.com/s?__biz=Mzk0ODM0NDIxNQ==&mid=2247497117&idx=1&sn=fc03d0dca0f45a9b05098f2e7f6369b9&scene=21#wechat_redirect)  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/OAmMqjhMehrtxRQaYnbrvafmXHe0AwWLr2mdZxcg9wia7gVTfBbpfT6kR2xkjzsZ6bTTu5YCbytuoshPcddfsNg/640?wx_fmt=other&wxfrom=5&wx_lazy=1&wx_co=1&random=0.8399406679299557&tp=webp "")  
  

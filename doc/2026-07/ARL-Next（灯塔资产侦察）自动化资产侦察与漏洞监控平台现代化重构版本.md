#  ARL-Next（灯塔资产侦察）自动化资产侦察与漏洞监控平台现代化重构版本  
owl234
                    owl234  渗透测试   2026-07-06 23:00  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/z3TOtprWtZ9B5GNXNkmQP5D3TtJwuTcHXiazd2SHA7YvyojBy8Iibia4VcR2sBQGOA7FicEgbXAZ0CTv24R7EIiaIiaQ/640?wx_fmt=other&wxfrom=5&wx_lazy=1&wx_co=1&randomid=d3ftoiiz&watermark=1&tp=wxpic#imgIndex=0 "")  
  
**点击上方蓝字****关注【渗透测试】不迷路**  
  
1.          ARL-Next 是 ARL (资产侦察灯塔) 的现代化重构版本，旨在为安全团队提供  
极简、高效的自动化资产发现与漏洞监控方案。  
  
1. 项目地址：https://github.com/owl234/ARL-Next  
  
## 📸 界面预览  
  
**全局仪表盘**  
：直观呈现资产分布、任务状态与实时日志，全局安全态势一目了然。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/EZicQGyMXoOkMEKiaGX9jrpGmjcLY970jWWJP9CTznZzELWThb0u079Pa5YWngspzZkA3xl6Ru4B4gpDw4EicTDM1vVGLhEBkN7TnzcygdkBB0/640?wx_fmt=png&from=appmsg "")  
  
**企业资产查询**  
：内置 ICP 备案与天眼查查询，快速摸清企业资产边界（网站/APP/公众号等），支持一键下发自动化扫描任务。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/EZicQGyMXoOkq4b32SPzXdEkgE9weiazrlJYShyl99C9Kug8R2MN3cEWsV2qZtgVqVMhG58naZ8B2gPtSTuDqCYibIoaMHYAda7uCibXW97ztic8/640?wx_fmt=png&from=appmsg "")  
  
**任务管理**  
：精细化的任务调度与状态追踪，支持多维度过滤；新增优化的插件分类与资产分组视图，交互更直观。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/EZicQGyMXoOnapdA0U2Znz4Va8yiayK4YpFZDCAaeWKibl1EDjm8kiahfDg2vaqWEXOtpqhnNic7giarzhZmYEjibmEVjeNLfqQpGssxO6Zl0TAAOA/640?wx_fmt=png&from=appmsg "")  
  
**系统设置**  
：支持 Web 端热更新扫描字典、灵活调整任务并发，以及配置多渠道（钉钉/飞书/企微）告警推送。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/EZicQGyMXoOlZbIw6xzIKGssAiaINicUQDGsv8KictspGtLqic22VGV0ibOv1wWLHh0iaG9MyqwKKySLquqNIENofarztiabcDsGLEicsdObbdvicbrZM/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/EZicQGyMXoOmETYDMrKecDBibH5VDjNZlIQ9Mo7gP6fib5EhdaKUHuIkfqcgzS7TEn02bqjlWWJpsicT48OsEPVIROoVMGLeBQQFwCtouJC7QYY/640?wx_fmt=png&from=appmsg "")  
## 🚀 部署指南  
### 推荐部署方案：前端本地 + Docker 后端源码  
  
**适用对象**  
：二次开发者、安全研究人员。 **方案优势**  
：后端全套服务（API / Worker / 数据库 / MQ）运行在 Docker 容器中，且**通过代码卷挂载实现修改即时生效**  
。前端在本地 Vite 环境独立运行并代理请求，彻底解耦，体验丝滑。  
> **前置条件**  
：已安装 Docker Desktop 和 Node.js（附带 npm），并全局安装 pnpm：npm install -g pnpm  
  
#### 第一步：构建并启动后端开发环境  
```
# 克隆代码
git clone https://github.com/owl234/ARL-Next
cd ARL-Next

# 首次构建后端开发镜像（内置所需底层引擎，耗时约 10~20 分钟）
# 此后只要 Dockerfile.dev 不变，无需重复 build
docker-compose -f docker-compose.dev.yml build

# 一键启动全部后台服务
docker-compose -f docker-compose.dev.yml up -d
```  
  
> **说明**  
：  
> docker-compose.dev.yml 会将本地项目目录挂载入容器，修改后端 Python 代码后，服务会自动热重载。容器启动时会自动重置/注入默认管理员账号，账号密码为：admin / arlpass。容器内的服务已为您自动映射好宿主机端口（API -> 5001，Mongo -> 27018，RabbitMQ -> 5673），完全不影响本地环境。  
  
#### 第二步：确认前端 API 代理配置  
  
后端 API 默认映射到宿主机 5001  
 端口。请确认 frontend/vite.config.js  
 中的代理指向正确：  
```
// frontend/vite.config.js
proxy: {
  '/api': {
    target: 'http://127.0.0.1:5001', 
    changeOrigin: true,
  }
}
```  
  
#### 第三步：启动前端开发服务器  
```
cd frontend
# 首次安装依赖
pnpm install
# 启动 Vite 开发服务器（支持热重载）
pnpm run dev
```  
  
启动后访问控制台打印的本地地址（默认 http://localhost:5173  
，若端口占用则顺延）即可登录系统。  
> **HTTPS 证书（可选）**  
：如需开启 HTTPS 以避免浏览器的各种安全拦截（如 Web Worker 限制等），可使用 mkcert localhost  
 生成本地证书，并将 localhost.pem  
 与 localhost-key.pem  
 放置于项目根目录 certs/  
 下，Vite 开发服务器检测到后会自动读取并开启 HTTPS (https://localhost:5173  
)。  
  
#### 常用开发管理命令  
```
# 查看所有容器状态
docker-compose -f docker-compose.dev.yml ps
# 实时查看后端主服务（API、Worker、定时任务）的混合日志
docker-compose -f docker-compose.dev.yml logs -f arl-dev
# 停止开发环境（不丢失数据）
docker-compose -f docker-compose.dev.yml down
```  
#### 项目地址：https://github.com/owl234/ARL-Next  
  
👇关注公众号  
👇  
  
## ✅Aiscan-N使用反馈  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/z3TOtprWtZ9B5GNXNkmQP5D3TtJwuTcHwRkCiaHciaNxU0KAxOaCiag0m4Vnic4DYu4F1g3ZIyu6u5WcfOMryN1qfg/640?wx_fmt=jpeg&from=appmsg&wxfrom=5&wx_lazy=1&watermark=1&tp=wxpic#imgIndex=11 "")  
  
🎁获取方式(  
加入付费**星球**  
)  
  
客服支持 💬：24小时在线解答，不怕有问题！  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/z3TOtprWtZ9B5GNXNkmQP5D3TtJwuTcHuPIQvAAcdcibOy9tzrVmKOQrdZe7tKEo7A8l9IM34WIkseaKVsdhBKA/640?wx_fmt=jpeg&watermark=1&wxfrom=5&wx_lazy=1&tp=wxpic#imgIndex=12 "")  
  
💎  
**终身使用权**  
：购买即可获得星球所有工具的永久使用权，终身使用所有工具及未来升级版本。  
  
🖥️   
**多设备支持**  
：所有工具采用一机一码授权，支持多台自用电脑激活，灵活无忧。  
  
🏆  
**一次购买，终身受益！**  
享受无忧售后服务、技术支持与永久更新  
  
**星球介绍**  
  
**自研工具、二开工具、免杀工具、漏洞复现、教程等资源、漏洞挖掘分析、网络安全相关资料分享。**  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/z3TOtprWtZ9B5GNXNkmQP5D3TtJwuTcHqJ7j2Ca2NicodUoCc1ZTKsXUocGySIvricOwksJND6icqlmrgmd3VFNAw/640?wx_fmt=png&from=appmsg&watermark=1&wxfrom=5&wx_lazy=1&tp=wxpic#imgIndex=13 "")  
  

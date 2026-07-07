#  工具 | ARL灯塔 二开重构版本 自动化资产发现和漏洞监控平台  
owl234
                    owl234  渗透安全团队   2026-07-07 07:47  
  
****1.          ARL-Next 是 ARL (资产侦察灯塔) 的现代化重构版本，旨在为安全团队提供  
极简、高效的自动化与漏洞监控方案。  
  
1.   
##  界面预览  
  
**全局仪表盘**  
：直观呈现资产分布、任务状态与实时日志，全局安全态势一目了然。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/RkQWDjsKa0SOeVtw8U6BySlicP1Jb4PyGbEQxbt6vKX5LlYFRRmsvqsiarVh0SRkD5aFsw4u1gfewoA3ClMuL0VJrm56biayI6Y6BoXFumpCiaI/640?wx_fmt=png&from=appmsg "")  
  
  
**企业资产查询**  
：内置 ICP 备案与天眼查查询，快速摸清企业资产边界（网站/APP/公众号等），支持一键下发自动化扫描任务。  
  
**任务管理**  
：精细化的任务调度与状态追踪，支持多维度过滤；新增优化的插件分类与资产分组视图，交互更直观。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/RkQWDjsKa0TO1gjuNUU4WsD1fkV6BdPHCXyT0o64Y8tcVRDx0Xbv4Wzxtu4RMVvq3ENiaBqpuZxGc2LqyU3XWZABT48fqhewVNI5dJ5WicvTk/640?wx_fmt=png&from=appmsg "")  
  
  
**系统设置**  
：支持 Web 端热更新扫描字典、灵活调整任务并发，以及配置多渠道（钉钉/飞书/企微）告警推送。  
  
##  部署指南  
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
  

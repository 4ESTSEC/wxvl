#  7月1日高危CVE漏洞速报  
 探知安全   2026-07-01 02:58  
  
高危预警  
# 【安全速报】07月01日高危漏洞紧急预警  
  
2026年07月01日  |  探知安全  
  
本期重大威胁：SimpleHelp RMM满分认证绕过CVE-2026-48558 CISA KEV TaskWeaver恶意软件在野利用；Gitea act_runner容器逃逸CVSS 9.9 PoC已公开CI/CD供应链风险；Oracle EBS未认证远程接管6月27日蜜罐捕获在野攻击；Adobe ColdFusion满分输入验证漏洞未认证RCE；StoneFly SQL注入CVSS 9.3密钥窃取在野利用，请立即排查修复。  
## 漏洞详情  
<table><tbody><tr><td style="white-space: nowrap;vertical-align: middle;"><span style="color:#c0392b;font-size:12px;font-weight:bold;padding:3px 10px;border:1px solid #c0392b;border-radius:3px;"><span leaf="">严重</span></span></td><td style="vertical-align:middle;padding-left:10px;"><span style="font-size:17px;font-weight:bold;color:#7c1d1d;"><span leaf="">CVE-2026-48558</span></span></td><td style="white-space: nowrap;vertical-align: middle;text-align: right;"><span style="color:#c0392b;font-size:12px;font-weight:bold;"><span leaf="">CVSS 10.0</span></span></td></tr></tbody></table>### SimpleHelp RMM OIDC认证绕过满分漏洞——CISA KEV在野利用·TaskWeaver+Djinn Stealer双恶意软件投递  
  
SimpleHelp远程管理与监控(RMM)平台存在加密签名验证不当漏洞（CWE-347），CVSS满分10.0。当服务器配置OIDC认证时，登录提交的身份令牌被接受但未验证其加密签名，无认证攻击者可伪造令牌创建技术员账户，获得对所有受管端点的完全管理控制权。CISA于6月29日将其列入KEV目录，联邦机构修复截止日期为7月2日（BOD 26-04三天期限）。攻击者已通过被攻破的SimpleHelp实例投递TaskWeaver loader及Djinn Stealer窃密木马，MSP及其客户均面临供应链攻击风险——攻击者可通过RMM通道向所有受管端点推送恶意软件。受影响版本为SimpleHelp 5.5.15及更早版本、6.0预发布版本（启用OIDC时）。  
  
影响范围  
  
SimpleHelp 5.5.15及更早版本、6.0预发布版本（启用OIDC认证时）；所有受管MSP客户端点面临供应链风险  
  
修复建议：立即升级SimpleHelp至已修补版本；禁用不必要的OIDC认证作为临时缓解；审计技术员账户是否存在未授权添加；检查受管端点是否被部署TaskWeaver/Djinn Stealer恶意软件；轮换所有RMM凭证  
<table><tbody><tr><td style="white-space: nowrap;vertical-align: middle;"><span style="color:#c0392b;font-size:12px;font-weight:bold;padding:3px 10px;border:1px solid #c0392b;border-radius:3px;"><span leaf="">严重</span></span></td><td style="vertical-align:middle;padding-left:10px;"><span style="font-size:17px;font-weight:bold;color:#7c1d1d;"><span leaf="">CVE-2026-58053</span></span></td><td style="white-space: nowrap;vertical-align: middle;text-align: right;"><span style="color:#c0392b;font-size:12px;font-weight:bold;"><span leaf="">CVSS 9.9</span></span></td></tr></tbody></table>### Gitea act_runner Docker容器逃逸CVSS 9.9——PoC已公开·CI/CD主机完全沦陷风险  
  
Gitea act_runner CI/CD执行引擎使用Docker后端时存在不当权限管理漏洞（CWE-269），CVSS评分9.9。工作流中定义的container.options字符串被直接传递给Docker HostConfig，即使配置privileged:false也会被绕过——攻击者可注入--pid=host、--cap-add=SYS_ADMIN、--volume=/:/host等危险选项，实现容器逃逸至CI/CD宿主机。任何拥有工作流写入权限的攻击者（包括公开仓库的Pull Request提交者）均可利用，攻击复杂度极低。PoC利用代码已于6月28日在Exploitarium公开，影响所有使用Docker后端的Gitea act_runner实例。成功后攻击者可窃取CI/CD管道的构建密钥、部署密钥、源代码及云凭证。  
  
影响范围  
  
Gitea act_runner使用Docker后端的所有版本（通过act 0.262.0及之前）；自托管Gitea及Gitea.com云runner  
  
修复建议：立即升级act_runner至已修补版本；实施工作流审批关卡限制外部贡献者执行权限；轮换所有CI/CD密钥（部署密钥/API令牌/镜像仓库凭证/签名密钥）；审计runner宿主机异常进程和容器；考虑迁移至Kubernetes后端  
<table><tbody><tr><td style="white-space: nowrap;vertical-align: middle;"><span style="color:#c0392b;font-size:12px;font-weight:bold;padding:3px 10px;border:1px solid #c0392b;border-radius:3px;"><span leaf="">严重</span></span></td><td style="vertical-align:middle;padding-left:10px;"><span style="font-size:17px;font-weight:bold;color:#7c1d1d;"><span leaf="">CVE-2026-46817</span></span></td><td style="white-space: nowrap;vertical-align: middle;text-align: right;"><span style="color:#c0392b;font-size:12px;font-weight:bold;"><span leaf="">CVSS 9.8</span></span></td></tr></tbody></table>### Oracle E-Business Suite未认证远程完全接管——6月27日蜜罐确认在野攻击·补丁已发布超1月  
  
Oracle E-Business Suite (EBS) Oracle Payments组件的File Transmission功能存在严重漏洞，允许未认证攻击者通过HTTP网络访问完全控制EBS实例。Oracle将其描述为「极易利用」(easily exploitable)。安全公司Defused于6月27-28日周末在蜜罐基础设施上捕获到活跃的野外攻击尝试，确认该漏洞正被实际利用。受影响版本为EBS 12.2.3至12.2.15。EBS承载企业核心敏感数据——财务、人力资源、供应链、采购信息——一旦沦陷后果严重。Oracle于5月28日发布补丁，至今已超过一个月，大量机构尚未修复。当前尚未列入CISA KEV目录，但鉴于已确认在野利用，预计近期将被纳入。  
  
影响范围  
  
Oracle E-Business Suite 12.2.3-12.2.15（Oracle Payments组件）；全球大量金融机构、制造企业、政府部门部署  
  
修复建议：立即应用Oracle 2026年5月关键补丁更新(CPU)；审计EBS访问日志中Oracle Payments组件的未授权活动；审查EBS用户账户是否存在意外的管理添加；EBS实例严禁直接面向互联网部署，实施网络分段隔离  
<table><tbody><tr><td style="white-space: nowrap;vertical-align: middle;"><span style="color:#c0392b;font-size:12px;font-weight:bold;padding:3px 10px;border:1px solid #c0392b;border-radius:3px;"><span leaf="">严重</span></span></td><td style="vertical-align:middle;padding-left:10px;"><span style="font-size:17px;font-weight:bold;color:#7c1d1d;"><span leaf="">CVE-2026-48281</span></span></td><td style="white-space: nowrap;vertical-align: middle;text-align: right;"><span style="color:#c0392b;font-size:12px;font-weight:bold;"><span leaf="">CVSS 10.0</span></span></td></tr></tbody></table>### Adobe ColdFusion未认证输入验证漏洞致任意代码执行CVSS 10.0——全版本紧急修复  
  
Adobe ColdFusion存在严重的不当输入验证漏洞（CWE-20），CVSS评分满分10.0。受影响版本为ColdFusion 2025.9、2023.20及更早版本。未认证的攻击者可通过发送特制HTTP请求触发任意代码执行，全程无需任何身份凭据。ColdFusion长期是高级威胁行为者的重点攻击目标，历史上多次被用于部署Webshell、Cobalt Strike及勒索软件。Adobe已发布紧急安全公告APSB26-64，于6月10日提供修复补丁。全球大量政府机构、教育组织和企业仍在使用ColdFusion构建Web应用，公网暴露实例数量庞大，风险面极广。  
  
影响范围  
  
Adobe ColdFusion 2025.9及更早版本、2023.20及更早版本（全球大量政府/教育/企业Web应用服务器）  
  
修复建议：立即升级至ColdFusion最新修复版本（2025 Update 10 / 2023 Update 21或更高）；如暂无法升级，限制ColdFusion仅内网访问并部署WAF规则；审计服务器Web目录是否存在异常文件（webshell）；检查ColdFusion管理员账户是否存在未授权添加  
<table><tbody><tr><td style="white-space: nowrap;vertical-align: middle;"><span style="color:#c0392b;font-size:12px;font-weight:bold;padding:3px 10px;border:1px solid #c0392b;border-radius:3px;"><span leaf="">严重</span></span></td><td style="vertical-align:middle;padding-left:10px;"><span style="font-size:17px;font-weight:bold;color:#7c1d1d;"><span leaf="">CVE-2026-55721</span></span></td><td style="white-space: nowrap;vertical-align: middle;text-align: right;"><span style="color:#c0392b;font-size:12px;font-weight:bold;"><span leaf="">CVSS 9.3</span></span></td></tr></tbody></table>### StoneFly Storage Concentrator SQL注入CVSS 9.3——Cookie参数注入窃取会话令牌及密钥·在野利用  
  
StoneFly Storage Concentrator存储集中器存在严重的SQL注入漏洞，CVSS评分9.3。攻击者通过操纵Cookie参数注入恶意SQL语句，可提取数据库中的会话令牌、密码哈希及存储密钥等敏感凭证。该漏洞于6月30日公开披露，已确认存在活跃的野外利用活动。StoneFly Storage Concentrator广泛部署于企业数据中心和云存储环境，用于统一管理多种存储后端（SAN/NAS/对象存储），一旦被突破攻击者可横向移动至整个存储基础设施。该漏洞利用复杂度低，影响所有未修补版本。  
  
影响范围  
  
StoneFly Storage Concentrator受影响版本（企业数据中心、云存储环境广泛部署；统一管理SAN/NAS/对象存储后端）  
  
修复建议：立即升级StoneFly Storage Concentrator至最新官方修复版本；临时措施：部署WAF规则过滤恶意SQL注入请求，限制管理界面仅内网访问；审计数据库是否存在异常查询记录；轮换所有存储系统的访问凭证和API密钥  
  
紧急提醒  
  
本期最高优先级：SimpleHelp CVE-2026-48558 CVSS 10.0已被CISA KEV确认在野利用，TaskWeaver+Djinn Stealer双恶意软件通过RMM通道投递，联邦机构修复截止7月2日仅剩1天，所有MSP及其客户须立即排查修复；Gitea act_runner CVE-2026-58053 CVSS 9.9 PoC已公开，CI/CD密钥泄露风险极高，所有公开仓库须立即启用工作流审批；Oracle EBS CVE-2026-46817补丁发布已超1月却在野攻击持续，补丁滞后效应严重；Adobe ColdFusion满分RCE及StoneFly SQL注入同步需紧急处置。请安全团队立即启动应急响应流程。  
  
处置建议  
  
① SimpleHelp用户立即升级至已修补版本，禁用不必要的OIDC认证，审计技术员账户并检查受管端点恶意软件  
  
② Gitea act_runner实例立即升级并启用工作流审批关卡，轮换所有CI/CD管道密钥和凭证  
  
③ Oracle EBS用户立即应用2026年5月CPU补丁，审计Oracle Payments组件访问日志  
  
④ Adobe ColdFusion用户立即升级至最新修复版本，检查服务器Web目录是否存在webshell  
  
⑤ StoneFly Storage Concentrator用户立即升级并轮换存储系统凭证，部署WAF过滤SQL注入请求  
  
觉得有用？点击右下角**在看**  
，让更多人看到  
  
探知安全 · 每日推送最新漏洞资讯  
  

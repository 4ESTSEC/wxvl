#  【热点安全风险】7月10日 |Google发布Chrome更新，修复27个安全漏洞，包含多个常用组件中的Critical级别漏洞  
 华顺信安威胁情报中心   2026-07-09 23:30  
  
**PART.****0****1**  
  
  
风险汇总  
‍  
‍  
‍  
  
## 风险一：Chrome 150修复27个安全漏洞，企业浏览器基线需尽快统一  
  
Google发布Chrome 150桌面稳定版更新，修复27个安全漏洞，其中包含Ozone、Views等组件中的Critical级别Use-after-free问题。浏览器仍是企业访问SaaS、邮箱、代码平台、运维后台和内部门户的高频入口，一旦终端版本分散、自动更新失效或受管浏览器策略滞后，钓鱼、恶意广告、漏洞利用链和会话窃取风险都会被放大。企业应把浏览器更新视为终端补丁基线的一部分，而不是只依赖操作系统月度补丁。  
### 建议排查  
1. 盘点Windows、macOS和Linux终端上的Chrome、Chromium内核浏览器及企业受管浏览器版本。  
  
1. 确认Chrome Stable已更新到150.0.7871.114或后续版本，并检查长期离线终端和VDI镜像。  
  
1. 排查是否存在禁用自动更新、固定旧版本、浏览器扩展过宽授权或非官方Chromium衍生版本。  
  
1. 对访问邮箱、VPN、代码仓库、云控制台、财务系统和运维后台的终端优先抽样核验浏览器版本。  
  
### 加固建议  
1. 通过企业浏览器管理、MDM或终端管理平台强制推送Chrome 150.0.7871.114及后续版本。  
  
1. 对VDI、跳板机、客服坐席和研发终端建立浏览器版本合规检查，禁止长期运行旧版。  
  
1. 收敛高风险扩展、调试接口、远程调试端口和非必要第三方浏览器同步功能。  
  
1. 将浏览器版本、扩展清单和自动更新状态纳入终端安全巡检与资产台账。  
  
参考来源：  
  
https://chromereleases.googleblog.com/2026/07/stable-channel-update-for-desktop_01162222768.html  
##   
  
## 风险二：Ubiquiti披露UniFi OS多项关键漏洞，网络管理、门禁与视频系统需优先升级  
  
Ubiquiti发布安全更新，修复UniFi Connect、UniFi Talk、UniFi Access、UniFi Protect和UniFi OS Server相关多项关键漏洞，其中CVE-2026-50746评分达到10.0，可在满足网络访问条件时触发命令注入。UniFi类设备常承担网络、门禁、电话、监控和商业楼宇运维管理职责，一旦暴露到公网或管理网权限过宽，攻击者可利用其作为横向移动、代理转发或内网侦察入口。  
### 建议排查  
1. 盘点UniFi OS Server、UniFi Connect、Talk、Access、Protect及相关网关、路由、NAS和监控设备。  
  
1. 检查UniFi Connect Application是否低于3.4.20，并核对其他UniFi组件是否已安装7月安全更新。  
  
1. 排查公网暴露、弱口令、默认管理端口、历史新增管理员账号和异常API调用。  
  
1. 关注UniFi设备是否出现异常外联、配置变更、管理账号创建、固件降级或未知插件安装。  
  
### 加固建议  
1. 立即升级UniFi Connect至3.4.20或后续版本，并同步更新受影响的Talk、Access、Protect和UniFi OS组件。  
  
1. 禁止UniFi管理界面直接暴露公网，仅允许通过VPN、堡垒机或受控管理网访问。  
  
1. 对网络设备、门禁、视频和楼宇自动化系统启用独立管理网段和最小权限账号。  
  
1. 对已暴露设备执行配置备份、账号复核、日志留存和必要的固件完整性检查。  
  
参考来源：  
  
https://www.bleepingcomputer.com/news/security/ubiquiti-warns-of-new-max-severity-unifi-os-vulnerability/  
  
## 风险三：GhostApproval与Friendly Fire连续披露，AI编码代理需从“人工确认”转向真实隔离  
  
Wiz披露GhostApproval问题，指出多款AI编码助手在处理符号链接和用户确认提示时可能出现信任边界失效；另有研究展示，面向代码审计的AI代理在自动模式下可能被不可信仓库诱导执行攻击者控制的代码。对企业研发团队而言，AI Agent正在接触源码、SSH配置、云凭据、构建脚本和CI上下文，如果只依赖“用户点击同意”或“Agent自动审查”作为安全边界，恶意仓库和供应链样本可能反向控制开发环境。  
### 建议排查  
1. 盘点研发人员使用的Amazon Q Developer、Claude Code、Cursor、Windsurf、Augment、Google Antigravity、Codex及其他AI编码代理。  
  
1. 检查AI代理是否允许在不可信仓库中自动执行命令、写入工作区外文件或读取用户目录中的敏感配置。  
  
1. 排查近期开发机上的.ssh  
、shell启动文件、AI工具配置、云凭据和包管理器配置是否异常变更。  
  
1. 对AI审计、自动修复、自动测试和开源项目扫描流程检查是否存在无人值守执行权限。  
  
### 加固建议  
1. 将AI编码代理运行在隔离容器、临时虚拟机或受控沙箱内，默认禁止访问用户主目录和长期凭据。  
  
1. 对不可信仓库关闭自动批准、自动执行和跨目录写入能力，要求展示真实解析路径和目标文件。  
  
1. 将SSH Key、云凭据、Git Token、npm/PyPI Token迁移到短期凭据或专用密钥代理中。  
  
1. 对AI工具版本、插件、MCP配置和项目内指令文件纳入研发安全基线和代码审查。  
  
参考来源：  
  
https://www.wiz.io/blog/ghostapproval-a-trust-boundary-gap-in-ai-coding-assistants  
  
## 风险四：Entra passkey注册被用于语音钓鱼，Microsoft 365身份治理需复核新认证方式  
  
Okta披露，O-UNC-066/Pink相关活动正在利用“注册新passkey”的安全升级话术实施语音钓鱼，攻击者通过电话引导目标访问仿冒Microsoft Entra注册页面，并尝试在受害者账号上绑定攻击者控制的passkey。该活动已面向食品饮料、科技、医疗、汽车、建筑和航空等行业，最终目标偏向数据外泄与勒索。对企业来说，抗钓鱼认证本身也可能被社工滥用，关键在于注册流程、帮助台核验和异常认证方法变更监控。  
### 建议排查  
1. 检查Microsoft Entra中近90天新增passkey、FIDO2密钥、认证方法和安全信息变更记录。  
  
1. 排查用户是否接到自称IT或安全团队的电话，要求访问含passkey  
字样域名或重新注册认证方式。  
  
1. 关注同一账号短时间内出现密码登录、MFA挑战、认证方法新增、SharePoint/OneDrive批量访问的组合行为。  
  
1. 对高权限用户、财务、HR、客服、IT运维和外包账号优先核查认证方法绑定历史。  
  
### 加固建议  
1. 对passkey/FIDO2注册启用受控流程，限制可注册设备类型、地理位置和用户组。  
  
1. 对认证方法新增、删除和重置设置高优先级告警，并与帮助台工单做自动比对。  
  
1. 明确内部通知渠道，禁止通过来电或非工单方式要求员工临时注册新的认证方式。  
  
1. 对疑似中招账号立即吊销会话、移除未知认证方法、重置密码并回溯云盘和邮件访问。  
  
参考来源：  
  
https://www.okta.com/en-au/blog/threat-intelligence/vishing-actors-target-microsoft-entra-passkey-enrollment-/  
  
## 风险五：伪造Paysafe、Skrill与Neteller SDK进入npm和PyPI，支付研发链路需排查依赖与密钥  
  
Socket披露，npm和PyPI上出现至少17个伪造支付SDK包，冒充Paysafe、Skrill和Neteller相关开发包，目标是窃取Paysafe API Key、AWS密钥、GitHub Token、npm Token及主机元数据。这类攻击针对开发者、CI系统和支付集成环境，不一定直接攻击生产应用，但一旦污染构建链路，就可能导致支付密钥、云权限和软件发布凭据同步外泄。  
### 建议排查  
1. 在代码仓库、锁文件、制品缓存和CI日志中检索paysafe-checkout  
、paysafe-vault  
、paysafe-node  
、skrill-sdk  
、paysafe-sdk  
等可疑包名。  
  
1. 检查支付相关项目是否近期新增未知npm/PyPI依赖，尤其是版本号为1.0.0至1.0.3的包。  
  
1. 排查CI/CD环境、开发机和构建镜像中的Paysafe API Key、AWS Key、GitHub Token、npm Token访问记录。  
  
1. 关注支付集成测试环境是否出现异常外联、假成功返回、未命中真实支付接口却显示交易成功的情况。  
  
### 加固建议  
1. 对命中可疑包的环境立即隔离，删除依赖并轮换所有可能被读取的支付、云和代码仓库凭据。  
  
1. 在私有包代理、制品库和依赖防火墙中阻断相关包名及相似拼写包。  
  
1. 对支付SDK采用官方来源白名单、包签名校验和依赖变更审批。  
  
1. 将CI中的长期Token改为短期凭据，限制构建任务对生产支付密钥和发布Token的访问。  
  
参考来源：  
  
https://socket.dev/blog/npm-pypi-campaign-typosquats-popular-secure-payment-apps  
  
## 风险六：AssuranceAmerica披露近700万司机记录泄露，保险与渠道型企业需复核员工账号入口  
  
AssuranceAmerica向监管机构披露数据泄露事件，影响约699.9万人，涉及姓名、联系方式、保险保单或账户信息、车辆和驾驶员信息、理赔相关信息以及驾照号码等数据。公开资料显示，事件起点与针对员工的恶意活动有关，攻击者随后访问并复制部分数据文件。对保险、汽车、金融和依赖大量代理渠道的企业而言，这类事件再次说明员工入口、代理网络、数据文件权限和批量复制监控是个人信息保护的关键控制点。  
### 建议排查  
1. 盘点存放客户、司机、保单、理赔、车辆和证件数据的文件服务器、SaaS平台和共享目录。  
  
1. 检查员工账号是否出现异常登录、MFA疲劳、会话劫持、异常设备绑定或非工作时间批量访问。  
  
1. 回溯近90天大规模文件复制、压缩打包、外部共享、数据库导出和云盘同步行为。  
  
1. 对代理、外包、客服和理赔岗位账号做权限抽样，确认是否存在跨区域或跨业务线过度访问。  
  
### 加固建议  
1. 对高敏感数据目录启用最小权限、访问审批、下载速率限制和批量访问告警。  
  
1. 对员工入口强化抗钓鱼MFA、设备合规校验和异常会话自动吊销。  
  
1. 对保险、代理、理赔等多渠道业务建立按字段和用途分级的数据访问策略。  
  
1. 预置数据泄露响应流程，包括影响人群识别、监管通知、客户通知、取证和二次欺诈监测。  
  
参考来源：  
  
https://www.bleepingcomputer.com/news/security/assuranceamerica-data-breach-exposes-records-of-69-million-drivers/  
  
  
**PART.****02**  
  
  
总体处置建议  
‍  
‍  
‍  
  
## 总结  
  
今天企业应重点关注浏览器补丁、网络管理平台、AI研发工具、身份注册流程、开源依赖和敏感数据文件六条风险线。使用Chrome/Chromium内核浏览器、UniFi设备、AI编码代理、Microsoft 365、支付SDK和大规模客户/司机数据系统的组织，应把处置重点放在补丁闭环、管理面收敛、Agent隔离、认证方法审计、依赖清理和数据批量访问监控上。  
  
## 整体风险处置建议  
1. 优先确认Chrome/Chromium内核浏览器、UniFi组件和关键终端软件版本，形成当天补丁闭环。  
  
1. 对公网暴露的网络管理、门禁、视频、VPN和远程管理入口执行一次暴露面复核。  
  
1. 对AI编码代理、CI/CD和开源依赖引入隔离执行、最小凭据和包源白名单。  
  
1. 对Microsoft 365认证方法新增、passkey注册、MFA重置和帮助台工单建立联动告警。  
  
1. 对客户、保单、理赔、支付和身份数据目录启用批量访问、外链共享和异常导出监控。  
  
1. 对今天涉及的终端、网络设备、身份平台、开发机和数据系统保留关键日志，便于后续回溯。  
  
##   
## 合规说明  
  
以上内容基于公开信息整理，仅用于网络安全防护与管理决策参考，具体影响范围与修复方式请以厂商官方公告为准。  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/iaPgUxMqaSAhoh0qmQmVFSZR5CZH6zqTtXJAnxcsIYOGT6gdZxjJicvloaqjr7XhADDbc0IfHy7SYHoDKfQR3bwwRZoz58vxD8YkL4E1S6uKA/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/iaPgUxMqaSAjLgib6qPpqOw0kZlYBRwrY4qLM4trxNBkSLlZECbuuGKGnia3DwZZQC5lGe1z03Dqc22xCwc0UrhAHkJiaLQdKpCz24b18P4B6Ow/640?wx_fmt=jpeg&from=appmsg "")  
  
  
  

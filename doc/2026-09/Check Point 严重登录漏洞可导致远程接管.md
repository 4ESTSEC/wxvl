#  Check Point 严重登录漏洞可导致远程接管  
sec随谈
                    sec随谈  sec随谈   2026-09-17 01:08  
  
Check Point 发布紧急安全更新，修复了一个严重的登录漏洞。该漏洞允许未经身份验证的远程攻击者以 root 权限执行任意代码。管理员必须立即安装已发布的 LivePatch 热修复补丁，以保护企业网络安全。  
  
**为何此威胁至关重要**  
  
全球数以千计的组织使用 Check Point 管理服务器控制企业防火墙。因此，这些核心平台中存在的漏洞使整个网络边界面临被恶意接管的风险。一旦攻击者入侵管理服务器，便可篡改安全策略，在内部网络中横向移动并禁用现有防御措施。  
  
**攻击方式**  
  
该漏洞的 CVSS 基础评分高达 9.8。安全公告警告称："未经身份验证的登录过程中出现的栈溢出，可能允许攻击者以 root 权限远程执行任意代码。"攻击者在初始身份验证序列中发送超长输入即可触发该漏洞，导致登录服务发生栈缓冲区溢出。  
  
此外，管理员可在 SmartConsole 日志中识别潜在的攻击尝试，具体方法是搜索内容为"Administrator failed to log in: Username too long"（管理员登录失败：用户名过长）的审计消息。Check Point 确认目前不存在公开的漏洞利用代码或在野攻击案例。  
  
**受影响版本**  
  
此 Check Point 登录漏洞影响多个软件版本，包括安全管理服务器（Security Management Server）和多域安全管理服务器（Multi-Domain Security Management Server）部署，以及日志服务器（Log Server）和多域日志服务器（Multi-Domain Log Server）系统。受影响版本包括 R82.20、R82.10、R82、R81.20 及更早的不受支持版本。厂商确认 Smart-1 Cloud 实例不受影响。  
  
**补丁与缓解措施**  
  
管理员应立即部署最新的 Check Point LivePatch 软件包。已启用自动更新的系统将自动接收修复补丁。详细升级说明可在 Check Point 官方安全公告中获取。若无法立即打补丁，管理员应将受信任的 GUI 客户端限制在专用内部 IP 子网范围内，并切勿将受信任客户端类型设置为接受任意远程地址。  
  
参考链接：  
  
https://support.checkpoint.com/results/sk/sk1000155  
  

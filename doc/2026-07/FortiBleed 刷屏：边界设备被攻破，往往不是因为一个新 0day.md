#  FortiBleed 刷屏：边界设备被攻破，往往不是因为一个新 0day  
原创 tcode
                    tcode  字节脉搏实验室   2026-07-06 04:31  
  
![](https://mmbiz.qpic.cn/mmbiz_png/nOo5YmK1PHwIYG7akOSaiazpXKdYz2ntPREibL9bLXU8RxCJtiaXASzjGbIvHib1aV8TZq6s1DFPCS19ncIFM86kD7WglPWGfTM5TIvgJLicFJyA/640?wx_fmt=png&from=appmsg "")  
  
    今天最值得企业安全团队复盘的事件，不是某个新 CVE，也不是一段新 PoC，而是一类更常见、更难收拾的边界设备凭据风险。  
  
    英国媒体在 2026 年 7 月 5 日至 7 月 6 日继续报道 FortiBleed 相关影响，称英国政府、地方机构及海外部门账号可能受到牵连。与此相呼应的是，英国 NCSC 早在 6 月 18 日就发布过针对 Fortinet 防火墙和 VPN 网关被全球性 targeting 的提醒；Fortinet 也在 6 月 19 日发布分析，强调相关活动并不是一个新的 Fortinet 漏洞，而更像是攻击者复用历史凭据、暴力破解、字典攻击，以及针对缺少 MFA 和弱口令治理的设备开展 credential harvesting。  
  
    这件事的传播点很强：边界防护设备本来是“守门员”，一旦管理口、VPN 账号或配置被攻击者掌握，它就可能从防线变成入口。  
  
核心事实  
  
    Fortinet 官方公开说明称，其知悉第三方将该活动称为 FortiBleed，并认为相关攻击涉及历史凭据复用、暴力破解以及弱密码卫生等因素。Fortinet 明确表示，这不是新的 Fortinet 漏洞，也与近期新公告无关。  
  
    NCSC 的提醒则更偏向防守动作：使用 Fortinet 边界设备的组织应检查是否受影响，确认设备归属，排查异常账号、异常日志和未经授权的配置变更；如果有入侵证据，应隔离设备、保留日志与配置证据，并在恢复时重新加固。  
  
    英国媒体关于政府账号与机构受影响的报道，属于公开媒体披露信息。它强化了事件关注度，但具体受害范围、攻击者归因和已造成的实际损害，仍应以官方后续通报和受影响机构披露为准。  
  
影响分析  
  
    FortiBleed 的真正警示，是边界设备不再只是“网络设备”，而是身份系统的一部分。  
  
    很多企业会给 VPN、防火墙、堡垒机、远程办公入口配置大量高权限账号。这里面有管理员账号、运维账号、外包账号、应急账号，也可能存在早已无人记得的共享账号。攻击者不一定需要打穿一条复杂利用链，只要拿到能登录的账号，就可能以“正常访问”的形式进入网络。  
  
    更麻烦的是，边界设备往往连接目录服务、VPN 用户、日志系统和内部网段。如果攻击者能改配置、加账号、保留后门或利用已有凭据横向移动，单纯改一个密码可能已经不够。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/nOo5YmK1PHyaZgpMaU6hF1QbjyRenXPhSyZAX4uc4hLl4icSxpgeGb3rFtzQvicNQn5DFZry4XQJ1FUrVEvlEfa1XaTefgWOsEic7rGBdwbCzw/640?wx_fmt=png&from=appmsg "")  
  
企业应对建议  
  
    第一，立即盘点 Fortinet 及其他边界设备。不要只查生产主防火墙，也要查灾备、分支机构、测试环境、旧 VPN 网关和外包运维保留的设备。  
  
    第二，终止现有管理和 VPN 会话，重置管理员与 VPN 凭据。对共享账号、默认命名账号、长期未登录账号和外包账号做重点清理。  
  
    第三，强制 MFA。边界设备管理员、VPN 用户和远程运维账号都应启用多因素认证；仍依赖单密码的入口，应被视为高风险。  
  
    第四，关闭公网管理面。管理接口不应暴露在互联网；如业务暂时无法下线，应限制到可信来源、跳板机或专用管理网段。  
  
    第五，对比“已知良好配置”。检查是否有新增账号、异常策略、陌生地址、可疑路由、异常 VPN 用户、异常认证失败/成功记录。  
  
    第六，如果发现未授权配置变更或横向移动迹象，不要只改密码。应按受陷设备处理：隔离、取证、重置、重建信任，并排查与其共享凭据的其他设备。  
  
事实、推测与观点区分  
  
    事实：NCSC 已发布 Fortinet 防火墙和 VPN 网关全球 targeting 提醒；Fortinet 公开表示相关活动不是新的 Fortinet 漏洞，并给出凭据重置、MFA、升级、配置校验和关闭公网管理面的建议。  
  
    推测：英国媒体报道中的具体受害机构、攻击者国别关联和损害范围，公开信息仍有限，不应在没有官方确认前做确定性归因。  
  
    观点：企业应该把边界设备凭据当作“域控级别”的关键资产管理。一个 VPN 管理账号被盗，影响可能不止于一台设备，而是整个远程访问体系。  
  
结语  
  
    FortiBleed 最像一面镜子：它照出的不是某家厂商的单点问题，而是很多企业共同的边界设备治理债。安全设备也会成为攻击入口，账号、MFA、管理面和配置审计，才是这类事件里真正该补的课。  
  
关键来源  
  
•	The Times，2026-07-06，Hackers breach Foreign Office systems with stolen logins：https://www.thetimes.com/uk/technology-uk/article/hackers-breach-foreign-office-systems-logins-nhd0rgr3v  
  
•	UK NCSC，2026-06-18，Alert: NCSC issues advice following global targeting of Fortinet firewalls and VPN gateways：https://www.ncsc.gov.uk/news/advice-following-global-targeting-of-fortinet-firewalls-and-vpn-gateways  
  
•	Fortinet PSIRT，2026-06-19，Analysis of Reported Credential Compromise of FortiGate Devices：https://www.fortinet.com/blog/psirt-blogs/analysis-of-reported-credential-compromise-of-fortigate-devices  
  
•	Recorded Future，2026-06，FortiBleed Campaign Exposes Credentials for 73,932 FortiGate Systems：https://www.recordedfuture.com/blog/critical-fortibleed-campaign  
  

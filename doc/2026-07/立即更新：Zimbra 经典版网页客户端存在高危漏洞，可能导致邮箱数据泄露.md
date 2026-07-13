#  立即更新：Zimbra 经典版网页客户端存在高危漏洞，可能导致邮箱数据泄露  
鹏鹏同学
                    鹏鹏同学  黑猫安全   2026-07-13 01:16  
  
![](https://mmbiz.qpic.cn/mmbiz_png/DYqn7TU9icq20fUP9yg24yBD0pRd1Bz6PSShpmCXhNW1s6C3tIgMaJBx6qSpnu7jwrx1DQq55vib8EBlibicpM8qicy3zCUBxfvYxZn9uygQ6RjY/640?wx_fmt=png&from=appmsg "")  
  
Zimbra 已发布 10.1.19 版本，修复其经典版网页客户端中一处高危**存储型跨站脚本（XSS）漏洞**  
，该客户端被广泛用于访问 Zimbra 协作套件。此漏洞目前尚未分配 CVE 编号，攻击者可通过发送特制邮件进行利用：用户在经典界面打开邮件时，恶意代码就会执行。漏洞利用成功后，攻击者可获取邮箱信息、会话数据以及账户设置。  
  
安全公告中写道：“本次更新修复了经典版网页客户端的一处安全漏洞：特制邮件被打开时可运行恶意代码。一旦遭到利用，攻击者可访问邮箱信息、会话数据及账户设置。”  
  
“我们强烈建议所有用户升级至 ZCS v10.1.19，以部署最新安全补丁、错误修复及功能优化。”  
  
该漏洞由谷歌威胁分析小组（Google’s Threat Analysis Group）发现。  
  
目前暂无证据表明该漏洞已被实际攻击利用，但使用经典版网页客户端的机构应尽快完成更新。  
  
自 2026 年初以来，美国网络安全和基础设施安全局（CISA）已将以下 Zimbra 漏洞录入已知被利用漏洞（KEV）目录：  
- CVE-2025-68645（CVSS 评分 8.8）：Synacor Zimbra 协作套件（ZCS）PHP 远程文件包含漏洞  
  
- CVE-2020-7796（CVSS 评分 9.8）：Synacor Zimbra 协作套件（ZCS）服务器端请求伪造漏洞  
  
- CVE-2025-66376（CVSS 评分 7.2）：Synacor Zimbra 协作套件（ZCS）跨站脚本漏洞  
  
今年 3 月，与俄罗斯有关联的 APT 组织（大概率为 APT28，又称 UAC-0001、梦幻熊（Fancy Bear）、兵棋行动（Pawn Storm）、索法西组织（Sofacy Group）、Sednit、BlueDelta、STRONTIUM）曾利用 CVE-2025-66376 漏洞攻击乌克兰相关机构。攻击者在钓鱼邮件中嵌入 JavaScript 脚本，静默窃取账号凭证、会话令牌、二次验证（2FA）验证码、保存的密码以及近 90 天的邮箱邮件数据，并通过 DNS 和 HTTPS 通道向外窃取数据。  
  
1 月 22 日，某国家海事机构遭到攻击，攻击者盗用学生邮箱发起攻击。Seqrite Labs 将该攻击活动命名为 “幽灵邮件行动（Operation GhostMail）”。  
  
一封钓鱼邮件瞄准乌克兰关键基础设施 —— 乌克兰国家水文局，攻击者通过被盗用的学生账号伪装成合法发件人，在邮件正文 HTML 中嵌入恶意 JavaScript，利用 Zimbra 跨站脚本漏洞 CVE-2025-66376。  
  
邮件被打开后，恶意脚本会在用户会话中执行，窃取账号凭证、会话令牌、邮件内容及二次验证信息。该多级攻击载荷借助 SOAP 请求、DNS 与 HTTPS 通道窃取数据，并建立持久访问通道，使攻击者能够长期监控邮箱账户、窃取长达 90 天的邮件数据。  
  
  

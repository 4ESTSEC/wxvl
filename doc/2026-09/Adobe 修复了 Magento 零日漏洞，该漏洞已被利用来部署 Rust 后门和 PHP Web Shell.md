#  Adobe 修复了 Magento 零日漏洞，该漏洞已被利用来部署 Rust 后门和 PHP Web Shell  
Rhinoer
                    Rhinoer  犀牛安全   2026-09-19 16:00  
  
![](https://mmbiz.qpic.cn/mmbiz_png/vO1zY1O9p8KOtsBmKibHK3nI6W0ia6dEKvjzbNl0C3OE3iciaJh6tPiboYJtXGxV8HmYjtsQxc7UelibgCRqyo3RfMntFHnCQFqBGz4TqMIzKaiaJA/640?wx_fmt=png&from=appmsg "")  
  
Adobe 周一发布了安全补丁，以解决影响 Adobe Commerce 和 Magento Open Source 的最高级别漏洞，该漏洞已被公开利用。  
  
该漏洞目前被追踪为**CVE-2026-75650**（CVSS 评分：10.0），由 Sansec 命名为StyleSmuggler，Sansec 发现该漏洞将于 2026 年 9 月 4 日开始遭受零日攻击。  
  
Adobe表示： “此次更新修复了一个可能导致任意代码执行的严重漏洞”，并补充说，“我们意识到 CVE-2026-75650 已被恶意利用，目标是 Adobe Commerce 商家。”  
  
该漏洞的核心在于利用 PHP 代码注入滥用 Magento 的模板系统，生成“支付交易失败提醒”电子邮件，并在过程中触发代码执行。  
  
  
该缺陷影响以下版本——  
- Adobe Commerce  
- 2.4.9-2026年8月及之前  
- 2.4.8-2026年8月及之前  
- 2.4.7-2026年8月及之前  
- 2.4.6-2026年8月及之前  
- 2.4.5-2026年8月及之前  
- 2.4.4-2026年8月及之前  
- Adobe Commerce B2B  
- 1.5.3-2026年8月及之前  
- 1.5.2-2026年8月及之前  
- 1.4.2-2026-8 月及之前  
- 1.3.4-2026年8月及之前  
- 1.3.3-2026年8月及之前  
- Magento 开源  
- 2.4.9-2026年8月及之前  
- 2.4.8-2026年8月及之前  
- 2.4.7-2026年8月及之前  
- 2.4.6-2026年8月及之前  
补丁已作为热修复程序的一部分发布，可从以下链接下载：repo.magento[.]com/patch/VULN-39341-composer-patches.zip  
  
Adobe表示： “为了帮助解决受影响产品和版本的漏洞，您必须应用 VULN-39341 补丁（取决于您的版本）并轮换您的加密密钥。 ”  
  
就在几天前，这家荷兰电子商务安全公司披露，攻击者正在利用 CVE-2026-75650 漏洞部署一个基于 Rust 的 Linux 后门程序，该后门程序会连接到外部服务器并等待进一步指令。此外，该漏洞还被滥用，用于在易受攻击的网站上投放 PHP 投放器，该投放器会写入一个能够执行任意 PHP 代码的 Web Shell。  
  
据总部位于荷兰的 Disrex 公司称，电子商务开发平台管理的 Magento 服务器在 2026 年 9 月 4 日晚上 10:20（UTC）首次确认的 StyleSmuggler 漏洞利用事件发生 50 分钟后遭到入侵。  
  
Disrex 表示：“StyleSmuggler 将 Magento 自身的模板处理和依赖注入代码转换为未经身份验证的远程代码执行链。”  
  
Previdian的遥测数据显示，自2026年9月7日以来，其蜜罐遭到了12次攻击，攻击者分别来自中国和罗马尼亚的两个独立IP地址。不过，创始人兼首席执行官Ryan Dewhurst表示，这些攻击均未成功。  
### 更新  
  
美国网络安全和基础设施安全局 (CISA) 于 2026 年 9 月 8 日将 CVE-2026-75650添加到其已知利用漏洞 ( KEV ) 目录中，要求联邦民事行政部门 (FCEB) 机构在 2026 年 9 月 11 日之前应用修复程序。  
  
与此同时，Adobe 还发布了针对其产品中 170 多个漏洞的补丁，其中包括 CVE-2026-82004（CVSS 评分：10.0），这是一个 Campaign Classic 中的操作系统命令注入漏洞，会导致任意代码执行。  
  
Adobe 还修复了 ColdFusion 中的两个严重漏洞（CVE-2026-48273，CVSS 评分：9.9；CVE-2026-75746，CVSS 评分：9.1），这两个漏洞可能导致任意代码执行。这家网页设计软件制造商表示，目前尚未发现任何利用这些漏洞的攻击。  
  
  
信息来源：  
The Hacker News  
  

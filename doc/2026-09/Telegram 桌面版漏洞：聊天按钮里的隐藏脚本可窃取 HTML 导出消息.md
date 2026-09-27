#  Telegram 桌面版漏洞：聊天按钮里的隐藏脚本可窃取 HTML 导出消息  
 Ots安全   2026-09-25 04:51  
  
**威胁简报**  
  
  
**恶意软件**  
  
  
**漏洞攻击**  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/zNsFJyIuL0Eful1aIgqDkHINbrwoFjuF7ibibg7vA0p2VO1ibZ8Z2QNnNAcogg0Vzks0l7cCicG4aiafH2tqePYibl5e5jTib0GP33HXBf1e0NcCpQ/640?wx_fmt=webp&from=appmsg "")  
  
2026 年 9 月 12 日，安全研究团队 **ExPatch**  
 公开披露，Telegram Desktop 的 HTML 聊天导出功能存在一处**存储型跨站脚本（Stored-XSS）**漏洞。恶意 Bot 可以将 <script>  
 标签藏进消息按钮文本；当用户用旧版客户端导出聊天，并在浏览器中打开生成的 .html  
 文件时，脚本自动执行，可读取该文件中的全部消息、发送者、时间戳、群聊元数据乃至本地文件路径，并外发到攻击者服务器，也能篡改页面内容。  
  
Telegram 已在 2024 年 7 月修复该问题，但**旧版导出的 HTML 文件不会被补丁自动清理**  
，依然可能触发攻击。MITRE / NVD 于 2026 年 9 月 21 日正式分配编号 **CVE-2026-94488**  
（CWE-79，CVSS 4.0 8.3 / CVSS 3.1 8.2）。  
## 漏洞机制：一个按钮文本的转义遗漏  
  
Telegram Desktop（Windows、macOS、Linux 桌面客户端）允许用户把单个聊天或全部聊天导出为 HTML 文件，在浏览器中查看。Bot 消息可以附带 inline keyboard（内联键盘）按钮，按钮上显示的文本由 Bot 自行决定。  
  
导出代码对普通消息文本、发送者姓名等字段都做了 HTML 转义（把 <  
 等字符转成 &lt;  
，使浏览器显示为文本而不是代码），但**偏偏遗漏了 inline-button 的文本**  
。结果就是，Bot 可以把一段 <script>...</script>  
 塞进按钮标签，并用不可见 Unicode 字符填充，让按钮在 Telegram 桌面端看起来是空的或正常的；一旦导出成 HTML，这段脚本就会被原样写进页面。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/zNsFJyIuL0EdyQWCVPQZXko0IZlGOLnrLZmB5jjgjIp7WicwMH5jxcia0UdhhCmia8wcEpzEur1ibGo3mQ8RBqA9rxMh4CmnWsmyYcvQS7x7bia0/640?wx_fmt=png&from=appmsg "")  
  
图 1：修复的本质——给按钮文本补上与其他字段相同的 HTML 转义（自制示意图）  
## 攻击链路：不需要 Bot 进群  
  
这个漏洞的巧妙之处在于：恶意 Bot **不必是目标群聊的成员**  
。  
  
Telegram 转发机制允许"只含网页链接按钮"的消息在转发后保留按钮。任何群成员把这条消息转发进群，脚本就作为群历史的一部分"沉睡"下来；数月甚至数年后，当有人用受影响版本导出该聊天并用浏览器打开时，脚本才苏醒并执行。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/zNsFJyIuL0H9NfDZeYsSgVDh4xbJrCAmkEm1c9qYeD8ibxf3QDickaGHdUlibShtgL3SM4rf6Ev1scEhVAHiaECxCI3GqxL84PWJRBVjv8iboIVo/640?wx_fmt=png&from=appmsg "")  
  
图 2：概念攻击链路——按钮里的脚本在聊天中 dormant，在浏览器中激活（自制示意图）  
  
触发攻击必须同时满足三个条件：  
1. HTML 导出使用的是修复前版本（**4.15.1 - 6.9.3**  
）；  
  
1. 带脚本的消息落在被导出的聊天范围内；  
  
1. 导出文件在**启用 JavaScript 的浏览器**  
中打开。  
  
## 能读什么、能改什么  
  
脚本在浏览器中对该 HTML 页面拥有完整 DOM 访问权，可以：  
- 读取该文件中的**所有消息、发送者姓名、时间戳**  
；  
  
- 读取**聊天名称、类型、成员数**  
；  
  
- 读取**本地文件路径**  
；  
  
- 将上述信息发送到攻击者控制的服务器；  
  
- **重写页面内容**  
，例如把整个导出替换成伪造的 Telegram"验证"表单。  
  
局限也同样明显：  
- Telegram Desktop 将长导出拆分为每文件 1,000 条消息，因此**一个文件最多泄露自身内容**  
，不会自动暴露整个账号或全部聊天。  
  
- 脚本**不改 Telegram 服务器上的聊天记录**  
，也**不改磁盘上已保存的导出文件本身**  
。  
  
## 影响版本与披露时间线  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/zNsFJyIuL0Gx8TkLpgY0LfLRkZagZrEMncQwicLIBxDOyXXZkEZnv6NeKoQgC4xzFGMkvXfSLHR6ibO0gJJxianD6vxfDA7lBRvxZHpPgaKv6s/640?wx_fmt=png&from=appmsg "")  
  
图 3：漏洞暴露约 2 年 4 个月，修复后旧导出仍危险（自制示意图）  
<table><thead><tr><th data-colwidth="164" style="border: 1px solid rgb(201, 201, 201);padding: 6px 12px;background: rgb(242, 242, 242);font-weight: 600;"><section style="margin-bottom: 24px;margin-top: 24px;"><span leaf=""><span textstyle="" style="font-size: 14px;">时间</span></span></section></th><th data-colwidth="370" style="border: 1px solid rgb(201, 201, 201);padding: 6px 12px;background: rgb(242, 242, 242);font-weight: 600;"><section style="margin-bottom: 24px;margin-top: 24px;"><span leaf=""><span textstyle="" style="font-size: 14px;">事件</span></span></section></th></tr></thead><tbody><tr><td data-colwidth="164" style="border: 1px solid rgb(201, 201, 201);padding: 6px 12px;"><section style="margin-bottom: 24px;margin-top: 24px;"><span leaf=""><span textstyle="" style="font-size: 14px;">2024 年 3 月</span></span></section></td><td data-colwidth="370" style="border: 1px solid rgb(201, 201, 201);padding: 6px 12px;"><section style="margin-bottom: 24px;margin-top: 24px;"><span leaf=""><span textstyle="" style="font-size: 14px;">Telegram Desktop 4.15.1 开始包含未转义的按钮文本处理，漏洞潜伏期约 2 年 4 个月</span></span></section></td></tr><tr><td data-colwidth="164" style="border: 1px solid rgb(201, 201, 201);padding: 6px 12px;"><section style="margin-bottom: 24px;margin-top: 24px;"><span leaf=""><span textstyle="" style="font-size: 14px;">2026 年 6 月 1 日</span></span></section></td><td data-colwidth="370" style="border: 1px solid rgb(201, 201, 201);padding: 6px 12px;"><section style="margin-bottom: 24px;margin-top: 24px;"><span leaf=""><span textstyle="" style="font-size: 14px;">ExPatch 的 Denis Rostilov 与 Aleksander Rostilov 发现漏洞</span></span></section></td></tr><tr><td data-colwidth="164" style="border: 1px solid rgb(201, 201, 201);padding: 6px 12px;"><section style="margin-bottom: 24px;margin-top: 24px;"><span leaf=""><span textstyle="" style="font-size: 14px;">2026 年 6 月 3 日</span></span></section></td><td data-colwidth="370" style="border: 1px solid rgb(201, 201, 201);padding: 6px 12px;"><section style="margin-bottom: 24px;margin-top: 24px;"><span leaf=""><span textstyle="" style="font-size: 14px;">报告给 Telegram；研究人员称仅在自有账户和测试组验证</span></span></section></td></tr><tr><td data-colwidth="164" style="border: 1px solid rgb(201, 201, 201);padding: 6px 12px;"><section style="margin-bottom: 24px;margin-top: 24px;"><span leaf=""><span textstyle="" style="font-size: 14px;">2026 年 6 月 30 日</span></span></section></td><td data-colwidth="370" style="border: 1px solid rgb(201, 201, 201);padding: 6px 12px;"><section style="margin-bottom: 24px;margin-top: 24px;"><span leaf=""><span textstyle="" style="font-size: 14px;">修复提交 </span></span><code><span leaf=""><span textstyle="" style="font-size: 14px;">8457d13a</span></span></code><span leaf=""><span textstyle="" style="font-size: 14px;">，开发者 John Preston</span></span></section></td></tr><tr><td data-colwidth="164" style="border: 1px solid rgb(201, 201, 201);padding: 6px 12px;"><section style="margin-bottom: 24px;margin-top: 24px;"><span leaf=""><span textstyle="" style="font-size: 14px;">2026 年 7 月 3 日</span></span></section></td><td data-colwidth="370" style="border: 1px solid rgb(201, 201, 201);padding: 6px 12px;"><section style="margin-bottom: 24px;margin-top: 24px;"><span leaf=""><span textstyle="" style="font-size: 14px;">6.9.4 beta 发布，修复已合入该 beta 版本</span></span></section></td></tr><tr><td data-colwidth="164" style="border: 1px solid rgb(201, 201, 201);padding: 6px 12px;"><section style="margin-bottom: 24px;margin-top: 24px;"><span leaf=""><span textstyle="" style="font-size: 14px;">2026 年 7 月 14 日</span></span></section></td><td data-colwidth="370" style="border: 1px solid rgb(201, 201, 201);padding: 6px 12px;"><section style="margin-bottom: 24px;margin-top: 24px;"><span leaf=""><span textstyle="" style="font-size: 14px;">7.0.1 stable 发布，修复已合入该稳定版本</span></span></section></td></tr><tr><td data-colwidth="164" style="border: 1px solid rgb(201, 201, 201);padding: 6px 12px;"><section style="margin-bottom: 24px;margin-top: 24px;"><span leaf=""><span textstyle="" style="font-size: 14px;">2026 年 9 月 12 日</span></span></section></td><td data-colwidth="370" style="border: 1px solid rgb(201, 201, 201);padding: 6px 12px;"><section style="margin-bottom: 24px;margin-top: 24px;"><span leaf=""><span textstyle="" style="font-size: 14px;">ExPatch 公开技术 writeup</span></span></section></td></tr><tr><td data-colwidth="164" style="border: 1px solid rgb(201, 201, 201);padding: 6px 12px;"><section style="margin-bottom: 24px;margin-top: 24px;"><span leaf=""><span textstyle="" style="font-size: 14px;">2026 年 9 月 14 日</span></span></section></td><td data-colwidth="370" style="border: 1px solid rgb(201, 201, 201);padding: 6px 12px;"><section style="margin-bottom: 24px;margin-top: 24px;"><span leaf=""><span textstyle="" style="font-size: 14px;">The Hacker News 报道；当时尚无 CVE/NVD 官方分数</span></span></section></td></tr><tr><td data-colwidth="164" style="border: 1px solid rgb(201, 201, 201);padding: 6px 12px;"><section style="margin-bottom: 24px;margin-top: 24px;"><span leaf=""><span textstyle="" style="font-size: 14px;">2026 年 9 月 21 日</span></span></section></td><td data-colwidth="370" style="border: 1px solid rgb(201, 201, 201);padding: 6px 12px;"><section style="margin-bottom: 24px;margin-top: 24px;"><span leaf=""><span textstyle="" style="font-size: 14px;">MITRE / NVD 正式发布 CVE-2026-94488（CWE-79，CVSS 4.0 8.3 / 3.1 8.2）</span></span></section></td></tr></tbody></table>## 导出范围决定风险大小  
  
带毒消息是否会被写进导出文件，取决于导出方式：  
- **单聊导出**  
（从聊天菜单选择导出）：包含该聊天中所有成员的消息。只要群里出现过那条被转发的 Bot 消息，就会被导出，**风险更高**  
。  
  
- **全账号导出**  
（默认设置）：群组和频道中只包含账号所有者自己的消息；但一对一聊天和 Bot 聊天中会包含全部消息。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/zNsFJyIuL0GrOYaWMmYYwiaPcZ6WqSjORvSicE6vM4icNW7zpicRdLlBKYrqRfHxzqgKKcKvaD14X9Zk3BcpZodKpCx09UGOu70yjOsCFTgtAyY/640?wx_fmt=png&from=appmsg "")  
  
图 4：不同导出方式下，带毒消息被纳入导出的范围差异（自制示意图）  
  
这意味着，在全账号导出的群组里，除非你自己转发了那条消息，否则默认不会把别人的转发消息导出来；但在单聊或 Bot 聊天中，所有消息都会被纳入。  
## 修复是一行转义，但旧文件不会自动安全  
  
修复提交 8457d13a  
 做的改动非常直接：给按钮文本加上与消息文本、发送者姓名相同的 HTML 实体转义，把 <  
 等敏感字符转成 &lt;  
，浏览器会将其显示为普通文本而非执行代码。  
  
但这里有一个关键陷阱：**更新 Telegram Desktop 不会重写你已经导出的旧 .html 文件**  
。补丁只保护新的导出。因此，所有在 7 月 14 日之前生成的 HTML 导出都应被视为不可信，尤其是来源复杂、难以逐条核实的大群导出。  
## 披露风波：500 美元赏金与"不允许公开"  
  
Telegram 于 2026 年 7 月 1 日确认了漏洞，并向 ExPatch 提供了 **500 美元**  
漏洞赏金。研究人员拒绝了这笔奖金，要求 Telegram 将其捐给慈善机构，同时提出协调披露日期，并承诺在补丁发布前保持沉默。  
  
研究人员公开的 Telegram Support 邮件显示，Telegram 在 7 月 1 日回应称：  
> "We also have considered the possibility of a public disclosure, but we cannot approve it as disclosing even the already addressed issues could put more Telegram users at risk in the future."  
  
  
ExPatch 将此解读为"即使在修复后也不允许公开披露"。由于双方并未签署保密协议，研究人员在补丁发布后的 9 月 12 日公开了 writeup。  
  
Telegram 公开的漏洞赏金规则仅规定"在漏洞被修复前向公众或第三方披露"的漏洞不享有赏金，对修复后的发布未作说明。  
## 参考来源  
- ExPatch writeup: https://expatch.com/writeups/telegram-html-export-xss.html  
  
- The Hacker News 报道: https://thehackernews.com/2026/09/telegram-desktop-flaw-lets-hidden.html  
  
- CVE-2026-94488 (MITRE): https://www.cve.org/CVERecord?id=CVE-2026-94488  
  
- NVD: https://nvd.nist.gov/vuln/detail/CVE-2026-94488  
  
- 修复提交 8457d13a  
: https://github.com/telegramdesktop/tdesktop/commit/8457d13aa795fadf99c955d2a04f00ebc3c59df9  
  
- 引入提交 52c779bf  
: https://github.com/telegramdesktop/tdesktop/commit/52c779bffa8dde3c5c09826add2607328fae0924  
  
- Telegram Desktop v7.0.1 release: https://github.com/telegramdesktop/tdesktop/releases/tag/v7.0.1  
  
- Telegram 导出文档: https://core.telegram.org/import-export  
  
  
  
**END**  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/zNsFJyIuL0Hk0nhMfKbfkGib3ruu9gglziay5H7gbokBYibsSSffAfd0Wpn8BF6Hd6SRu5XdyHjBAZ6SCOmX9aLd3ibWrbjUdVoibA4CnvFFtkibo/640?wx_fmt=jpeg&from=appmsg "")  
  
  
公众号内容都来自国外等平台- 搜索的内容通过结合编写 -   
  
公众号 |   
AnQuan7 (Ots安全)  
  

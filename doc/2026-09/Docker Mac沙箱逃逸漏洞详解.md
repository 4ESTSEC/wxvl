#  Docker Mac沙箱逃逸漏洞详解  
Dubito
                    Dubito  云原生安全指北   2026-09-20 06:33  
  
   
  
> 注：本文翻译自 Accomplish 的文章  
《Guest to host: escaping Docker's hypervisor》  
，可点击文末“阅读原文”按钮查看英文原文。  
  
## 一、引言  
  
Docker 已修复我们报告的 Mac hypervisor 中的沙箱逃逸漏洞：容器运行三行 bash 即可获得对宿主机文件系统的完整读写权限。  
  
Docker Desktop 和 Docker Sandboxes 均受影响。仅当在设置中开启 Docker VMM 时，Docker Desktop 才会受影响。幸好我们现在发现了它，因为 Docker VMM 计划于 2026 年 10 月底成为 Docker Desktop 的默认选项。  
  
该漏洞已被分配为 **CVE-2026-77179**  
，并已在 Docker Sandboxes 0.42.0 和 Docker Desktop 4.88.0 中修复。  
## 二、漏洞  
  
当你将文件夹挂载到容器中时，Docker 的 VMM 会使用 virtio-fs，而提供该文件服务的文件服务器运行在宿主机上。客户机第一次访问某个路径时，会发送一次 lookup，服务端则返回一个 nodeid：这是服务端为自身记录而选定的编号。此后，客户机都通过 nodeid 发起请求，不再发送路径，因此服务端必须在每次请求时重新找到该文件。  
  
它有两种方式找到该文件。首先通过 macOS volfs，按 inode 查找。如果失败，则回退到首次查找该文件时保存的路径字符串。  
  
删除文件会移除 volfs 路径。保持文件打开状态，nodeid 会被保留。两者结合，服务端就只剩下那个文件路径字符串。然后客户机将父文件夹替换为符号链接。服务端读取该文件路径字符串，看到的是位于挂载文件夹内的路径，于是放行；但随后内核读取同一字符串，跟随符号链接，打开了宿主机上挂载文件夹之外的文件。  
## 三、漏洞利用  
  
客户机选择宿主机上想要读取或覆盖的文件。比如 /Users/you/.zshenv  
。  
1. 1. 创建文件夹 pv  
，并在其中创建一个与目标同名的文件 pv/.zshenv  
。  
  
1. 2. 打开 pv/.zshenv  
 并保持打开。这次 lookup 让服务端获得了一个 nodeid，而打开的 handle 会阻止服务端将其丢弃。  
  
1. 3. 删除该文件，然后删除该文件夹。volfs 路径也随之消失，因此服务端只剩下文件路径字符串。  
  
1. 4. 创建一个名为 pv  
 的符号链接，指向 /Users/you  
。  
  
1. 5. 通过第 2 步的 handle 进行读取或写入。  
  
服务端会再次解析该字符串。现在 pv  
 是符号链接，且它不是路径中的最后一段，因此内核会跟随它。请求最终落到 /Users/you/.zshenv  
。  
  
![服务端在 lookup 时保存的字符串是 /Users/you/proj/pv/.zshenv。第一次请求时 pv 是文件夹，路径仍位于沙箱内。之后的请求中，pv 是指向 /Users/you 的符号链接，因此同一字符串会解析为 /Users/you/.zshenv，即宿主机上的任意位置。](https://mmbiz.qpic.cn/sz_mmbiz_png/Kric7mM9eA5BKruvvDUiazJDVOBPEhL1peMyKnS5X8J5aBtNGyS4Rv6QGtmnxic4ibYLiaibKuhxTqr5CgK5SOAKSQRwunsKiczTTlASLrtuibqmoGQ/640?from=appmsg "null")  
  
服务端在 lookup 时保存的字符串是 /Users/you/proj/pv/.zshenv。第一次请求时 pv 是文件夹，路径仍位于沙箱内。之后的请求中，pv 是指向 /Users/you 的符号链接，因此同一字符串会解析为 /Users/you/.zshenv，即宿主机上的任意位置。  
  
在 bash 中也是如此：  
```
mkdir pv && : > pv/.canary && exec 9< pv/.canaryrm pv/.canary; rmdir pv; ln -s /Users/Shared pvecho CONFIRMED > /proc/self/fd/9
```  
  
<table><thead><tr><th style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 14px;border: 1px solid #dfdfdf;padding: 0.25em 0.5em;color: #3f3f3f;word-break: keep-all;background: rgba(0, 0, 0, 0.05);"><section><span leaf="">时间</span></section></th><th style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 14px;border: 1px solid #dfdfdf;padding: 0.25em 0.5em;color: #3f3f3f;word-break: keep-all;background: rgba(0, 0, 0, 0.05);"><section><span leaf="">事项</span></section></th></tr></thead><tbody><tr><td style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 14px;border: 1px solid #dfdfdf;padding: 0.25em 0.5em;color: #3f3f3f;word-break: keep-all;"><section><span leaf="">2026-08-12 14:46 UTC</span></section></td><td style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 14px;border: 1px solid #dfdfdf;padding: 0.25em 0.5em;color: #3f3f3f;word-break: keep-all;"><section><span leaf="">我们向 </span><span style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 14px;color: rgba(10, 142, 171, 1);"><span leaf="">security@docker.com</span></span><span leaf=""> 报告。</span></section></td></tr><tr><td style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 14px;border: 1px solid #dfdfdf;padding: 0.25em 0.5em;color: #3f3f3f;word-break: keep-all;"><section><span leaf="">2026-08-12 21:54 UTC</span></section></td><td style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 14px;border: 1px solid #dfdfdf;padding: 0.25em 0.5em;color: #3f3f3f;word-break: keep-all;"><section><span leaf="">他们回复并确认收到，距报告约七小时。</span></section></td></tr><tr><td style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 14px;border: 1px solid #dfdfdf;padding: 0.25em 0.5em;color: #3f3f3f;word-break: keep-all;"><section><span leaf="">2026-08-13 22:16 UTC</span></section></td><td style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 14px;border: 1px solid #dfdfdf;padding: 0.25em 0.5em;color: #3f3f3f;word-break: keep-all;"><section><span leaf="">Sailor 提交 </span><code style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 90%;color: rgba(255, 143, 38, 1);background: rgba(0, 0,0, .03);padding: 3px 5px;border-radius: 4px;"><span leaf="">9f348c0</span></code><span leaf="">。即修复方案。距报告约 31 小时。</span></section></td></tr><tr><td style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 14px;border: 1px solid #dfdfdf;padding: 0.25em 0.5em;color: #3f3f3f;word-break: keep-all;"><section><span leaf="">2026-08-14 14:36 UTC</span></section></td><td style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 14px;border: 1px solid #dfdfdf;padding: 0.25em 0.5em;color: #3f3f3f;word-break: keep-all;"><section><span leaf="">Docker 确认该漏洞，表示修复正在进行，并计划发布 CVE。</span></section></td></tr><tr><td style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 14px;border: 1px solid #dfdfdf;padding: 0.25em 0.5em;color: #3f3f3f;word-break: keep-all;"><section><span leaf="">2026-08-24</span></section></td><td style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 14px;border: 1px solid #dfdfdf;padding: 0.25em 0.5em;color: #3f3f3f;word-break: keep-all;"><section><span leaf="">Docker Desktop 4.88.0 发布 sailor 0.118.0。</span></section></td></tr><tr><td style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 14px;border: 1px solid #dfdfdf;padding: 0.25em 0.5em;color: #3f3f3f;word-break: keep-all;"><section><span leaf="">2026-09-07</span></section></td><td style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 14px;border: 1px solid #dfdfdf;padding: 0.25em 0.5em;color: #3f3f3f;word-break: keep-all;"><section><span leaf="">Docker Sandboxes 0.42.0 发布 CVE-2026-77179 的修复。</span></section></td></tr></tbody></table>  
  
Sailor 是 Docker hypervisor 的内部名称。  
  
Docker 安全团队反应迅速且专业。他们约七小时内回复，并在两天内确认漏洞。Docker Desktop 修复于 8 月 24 日发布，Docker Sandboxes 修复于 9 月 7 日在 0.42.0 中发布。  
## 四、检查你是否受影响  
  
如果你使用 Docker Sandboxes，运行 sbx --version  
。确保运行的是 0.42.0 或更高版本。  
  
如果你使用 Docker Desktop，需要 4.88.0 或更高版本。打开 Settings、General、Virtual Machine Manager。任何更早版本且选择了 Docker VMM 的都会受影响。  
  
   
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/Kric7mM9eA5ATqNawYEiaHG70DIiaX4jlpTEvFTGHpT9Ro6MLIP1xaKzzK5ysibrqO9ROibvuLTGaGok0jLb2VBOJe0ttkXExuBtK89Z92z4TOVk/640?wx_fmt=gif&from=appmsg "")  
  
  
  
**交流群**  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Kric7mM9eA5AFZSUHnom7E4u9gkK03LL1L4CmJib4zaTrVmZgxL7PHUOkkmicWSAwmvEE2jNxmBEoXmg3Y9DnXdoCbNu95p8hic9lNqNWXd6PgE/640?wx_fmt=png&from=appmsg "")  
  
  
  
  
**知识库**  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Kric7mM9eA5DLBLI1CLdbrGEYtic0nXBUWOzpLqdMPQ9pfX5ia1UfliaS50mBY5MmSUYia6hBGpdIvQ1BvSJrrjADp5HM6AKRmeHEfsado5YxZbM/640?wx_fmt=png&from=appmsg "")  
  
  
  
  
  

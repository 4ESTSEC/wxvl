#  WordPress 路径遍历漏洞：无需登录即可 RCE  
 撅人   2026-09-25 01:41  
  
WordPress 路径遍历漏洞：无需登录即可 RCE  
  
CVE-2026-87902 · CVSS 9.2 分（Critical）· POC 已公开  
  
1  
  
漏洞速览  
  
CVE 编号  
CVE-2026-87902  
  
漏洞类型  
路径遍历 + 本地文件包含  
  
影响产品  
WordPress Core  
  
安全等级  
Critical（严重）  
  
CVSS v4.0  
9.2  
  
披露时间  
2026-09-22  
  
受影响版本  
4.7.0 至 7.1.1  
  
修复版本  
7.1.2（及所有分支回移植版本）  
  
利用情况  
POC 已公开，已在野利用  
  
发现者  
Robert Ressl（Wordfence）  
  
2  
  
漏洞概述  
  
WordPress 是全球使用最广泛的内容管理系统，截至 2026 年全球超过 **43%**  
 的网站基于 WordPress 构建。2026 年 9 月 22 日，WordPress 安全团队披露了一个 **无需登录即可利用**  
 的路径遍历漏洞（CVE-2026-87902），CVSS v4.0 评分高达 **9.2 分（Critical）**  
。  
  
攻击者无需任何 WordPress 账号，即可在页面模板解析过程中使服务端包含 **当前主题目录以外**  
、Web 账户可读的本地 PHP 文件。如果当前主题存在名称以 **page-**  
 开头的顶层目录，且服务器上存在可利用的本地 PHP 文件，则可进一步导致 **远程代码执行（RCE）**  
 和站点完全控制。  
  
**已观察到的攻击链：**  
漏洞披露后数小时内，攻击者利用 **pearcmd.php**  
（PHP PEAR 包管理工具）实现从文件包含到命令执行的跨越，并通过 **register_argc_argv=On**  
 配置将请求参数传递给 PEAR，最终写入并执行恶意 PHP 文件。Patchstack 于 9 月 23 日观察到从侦察到实际入侵的升级，攻击流量已超过初始量的 **十倍**  
。  
  
3  
  
详细漏洞分析  
  
第一层：页面模板解析机制  
  
WordPress 根据页面内容自动选择模板文件。当用户访问一个页面时，WordPress 调用 **get_page_template()**  
 函数，从 URL 中提取 **pagename**  
 查询参数，构建候选模板文件名。例如访问   
?pagename=about  
，WordPress 会尝试加载   
page-about.php  
。  
  
问题在于：**WordPress 对 pagename 执行了双重 URL 解码**  
。Web 服务器已经对请求参数进行了一次 URL 解码，WordPress 又调用了一次 **urldecode()**  
。攻击者可以提交双重编码的 **../**  
 序列，使它在最终模板名解析时变成路径遍历符号。  
  
// 漏洞代码（WordPress < 7.1.2） $pagename = $_GET['pagename']; $pagename_decoded = urldecode(urldecode($pagename));  // 双重解码 $templates[] = "page-{$pagename_decoded}.php";  
  
第二层：路径遍历攻击原理  
  
WordPress 将构建的候选模板名传给 **locate_template()**  
 函数。在受影响版本中，该函数将候选文件路径拼接到主题目录路径下，**检查文件是否存在**  
，但没有**验证解析后的路径是否仍在主题目录内**  
。如果文件名包含 **../**  
，就可以跳出主题目录，指向服务器上的任意可读文件。  
  
但漏洞利用需要满足两个关键条件：  
  
**条件一：**  
当前父主题或子主题必须包含一个名称以 **page-**  
 开头的顶层目录，例如   
page-templates  
。WordPress 模板解析逻辑要求模板候选名必须以 **page-**  
 开头。  
  
**条件二：**  
服务器上必须存在一个可被 Web 账户读取、且在包含后能产生有用行为的 PHP 文件。  
  
第三层：远程代码执行链  
  
攻击者通过双重 URL 编码构造路径遍历请求，使 **locate_template()**  
 包含服务器上的恶意 PHP 文件。目前已知最有效的利用路径是通过 **pearcmd.php**  
：  
  
**步骤 1**  
 — 攻击者发送双重编码的请求，使 WordPress 模板解析跳转到   
/usr/local/lib/php/pearcmd.php  
  
↓  
  
**步骤 2**  
 — **pearcmd.php**  
 被包含，由于 **register_argc_argv=On**  
，请求参数被暴露为 PEAR 命令行参数  
  
↓  
  
**步骤 3**  
 — 利用 PEAR 的 **package-install**  
 功能写入攻击者控制的 PHP 代码到服务器  
  
↓  
  
**步骤 4**  
 — 通过访问写入的 PHP 文件，完成 **远程代码执行（RCE）**  
  
**受影响的主题：**  
官方安全公告特别提到了以下主题存在 **page-***  
 目录结构：  
  
**Twenty Twelve**  
、**Twenty Fourteen**  
（WordPress 官方遗留主题）、**Neve**  
、**Hestia**  
、**Sydney**  
（第三方热门主题）。  
  
**受影响的环境：**  
官方 PHP Docker 镜像、PHP 8.5 之前的默认 cPanel 配置，均暴露了可利用的 **pearcmd.php**  
 文件。  
  
4  
  
快速自查命令  
  
① 检查 WordPress 核心版本  
  
检查方法：登录 WordPress 后台 → 仪表板 → 查看版本信息  
  
或命令行：检查   
/wp-includes/version.php  
 文件中的   
$wp_version  
 变量  
  
② 检查主题是否含有   
page-*  
 目录  
  
find /path/to/wp-content/themes -maxdepth 1 -name "page-*" -type d  
  
若存在输出，说明当前主题可能满足漏洞利用条件  
  
③ 检查   
pearcmd.php  
 是否存在且可被 Web 账户读取  
  
find / -name "pearcmd.php" 2>/dev/null | xargs ls -la  
  
若该文件存在且对 Web 进程用户可读，存在被利用风险  
  
④ 检查 PHP   
register_argc_argv  
 配置  
  
php -r "echo ini_get('register_argc_argv');" && echo  
  
若输出为   
1  
 或   
On  
，存在利用条件  
  
⑤ 检查 Web 日志中的可疑请求  
  
grep -r "page-.*%252e%252e" /var/log/nginx/ /var/log/apache2/  
  
搜索包含双重编码 **../**  
 的 **pagename**  
 参数请求  
  
5  
  
修复方案  
  
WordPress 安全团队已修复该漏洞，**7.1.2**  
 为最新修复版本，且所有分支均回移植了安全补丁。请根据当前使用的 WordPress 版本，升级到对应的修复版本：  
  
受影响版本 → 修复版本  
  
7.1.0 至 7.1.1 → **7.1.2**  
  
7.0.0 至 7.0.5 → **7.0.6**  
  
6.9.0 至 6.9.8 → **6.9.9**  
  
6.8.0 至 6.8.9 → **6.8.10**  
  
6.7.0 至 6.7.8 → **6.7.9**  
  
6.6.0 至 6.6.8 → **6.6.9**  
  
6.5.0 至 6.5.11 → **6.5.12**  
  
6.4.0 至 6.4.11 → **6.4.12**  
  
6.3.0 至 6.3.11 → **6.3.12**  
  
6.2.0 至 6.2.12 → **6.2.13**  
  
6.1.0 至 6.1.13 → **6.1.14**  
  
6.0.0 至 6.0.15 → **6.0.16**  
  
5.9.0 至 5.9.17 → **5.9.18**  
  
5.8.0 至 5.8.16 → **5.8.17**  
  
5.7.0 至 5.7.18 → **5.7.19**  
  
5.6.0 至 5.6.20 → **5.6.21**  
  
5.5.0 至 5.5.21 → **5.5.22**  
  
5.4.0 至 5.4.22 → **5.4.23**  
  
5.3.0 至 5.3.24 → **5.3.25**  
  
5.2.0 至 5.2.27 → **5.2.28**  
  
5.1.0 至 5.1.25 → **5.1.26**  
  
5.0.0 至 5.0.28 → **5.0.29**  
  
4.9.0 至 4.9.32 → **4.9.33**  
  
4.8.0 至 4.8.31 → **4.8.32**  
  
4.7.0 至 4.7.36 → **4.7.37**  
  
升级操作步骤  
  
**步骤 1：**  
备份当前 WordPress 站点文件和数据库  
  
**步骤 2：**  
下载对应分支的修复版本，覆盖上传核心文件（或执行   
wp core update  
）  
  
**步骤 3：**  
访问 WordPress 后台 → 仪表板，确认版本号已更新到修复版本  
  
**步骤 4：**  
检查 Web 日志，确认无异常访问请求  
  
**步骤 5：**  
如使用自动后台更新，务必**人工验证更新是否成功**  
  
6  
  
安全提醒  
  
双重 URL 解码陷阱  
  
WordPress 对 **pagename**  
 参数执行了额外的一次 URL 解码，使双重编码的 **../**  
 在模板解析时生效。安全开发者应避免对已解码的参数再次解码，或在解码后进行路径合法性校验。  
  
路径遍历防护原则  
  
**locate_template()**  
 在解析模板路径后，未验证最终路径是否在合法主题目录内。核心防护应在路径拼接后调用 **realpath()**  
 并校验路径前缀，确保不会跳出授权目录。  
  
PEAR 与 PHP 环境安全  
  
**pearcmd.php**  
 是 PHP 的命令行工具，但可通过 Web 服务器包含。建议：**（1）禁用**  
register_argc_argv  
 配置；**（2）从 Web 可读路径移除或限制访问**  
pearcmd.php  
；**（3）升级至 PHP 8.5+**  
（官方已修复相关利用链）。  
  
主题目录结构审查  
  
自定义主题时应避免使用 **page-***  
 作为顶层目录名称。如需存放页面模板相关资源，建议使用 **templates/**  
、**page-templates/**  
 以外的命名约定，减少攻击面。  
  
日志监控与应急响应  
  
**Patchstack 观察到：**  
漏洞披露后数小时内即开始侦察，9 月 23 日升级为实际入侵。建议立即：**（1）检查 Web 日志中是否包含双重编码 ../ 的请求**  
；**（2）搜索服务器上新创建的可疑 PHP 文件**  
；**（3）审查管理员账号、已安装插件和定时任务**  
。  
  
7  
  
漏洞时间线  
  
2026-09-22  
  
WordPress 安全团队发布 **7.1.2**  
 及所有分支回移植版本，同步披露安全公告  
  
2026-09-22  
  
Wordfence Intelligence 将漏洞分配为 CVE-2026-87902，CVSS 9.2 分（Critical）  
  
2026-09-22  
  
Wordfence Premium/Care/Response 用户获得防火墙规则保护  
  
2026-09-23  
  
Patchstack 检测到从侦察到实际入侵的升级，攻击流量增长超十倍  
  
2026-09-25  
  
本文发布  
  
8  
  
参考链接  
  
WordPress Core Security Advisory GHSA-7hp8-65ch-5whp  
  
WordPress Core changeset 63792（修复提交）  
  
Wordfence: Critical Unauthenticated Path Traversal Vulnerability Patched in WordPress Core  
  
CyberWorldOps: Attackers Turn WordPress Template Flaw Into Remote Code Execution  
  
CVE-2026-87902 官方记录（CVE.org）  
  
WordPress Release Archive  
  
本文信息来源为官方安全公告及第三方安全研究，仅供安全研究参考。实际利用情况可能因环境和配置而异，请及时更新到修复版本。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/C2LH9pdiblyKiaq0ylL6VeXGicOdEyN1xahCeCJJWRKk8VEgsREoS54m3sUZgbm2b4eLvO0zibPLesDkbhAI6cnGaGibVh0CzVkpQu0hibEWyBVx4/640?wx_fmt=jpeg&from=appmsg "")  
  

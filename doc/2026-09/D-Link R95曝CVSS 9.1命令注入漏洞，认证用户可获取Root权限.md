#  D-Link R95曝CVSS 9.1命令注入漏洞，认证用户可获取Root权限  
tuto
                    tuto  杂杂咱谈   2026-09-22 05:13  
  
> 核心结论: D-Link R95(BE9500) Wi-Fi 7路由器存在一个位于DHMAPI接口的OS命令注入漏洞。攻击者在获得有效的Web管理会话后，可通过SetTimeSettings请求中的NTPServer参数触发命令执行。公开PoC已验证该漏洞能够以Root权限执行系统命令  
。  
  
## 一、漏洞发生了什么？  
  
漏洞编号为CVE-2026-93958，该漏洞影响D-Link R95/BE9500 Wi-Fi 7智能路由器。公开漏洞信息显示，问题位于设备的DHMAPI组件，具体涉及/bin/ssi后端程序对SetTimeSettings请求的处理。  
  
攻击者提交的参数在进入配置系统前没有进行充分的Shell特殊字符过滤，随后又被拼接进Shell命令执行流程。  
  
由于/bin/ssi进程本身以Root权限运行，因此最终形成  
：  
```
认证管理会话      
      ↓
DHMAPI/SetTimeSettings
      ↓     
NTPServer参数     
      ↓
   配置写入      
      ↓      
Shell命令拼接   
      ↓    
   命令注入      
      ↓     
Root权限执行
```  
  
CVE数据显示，该漏洞CVSS 3.1为9.1(Critical)，攻击向量为网络，攻击复杂度低，但需要较高权限；CVSS 4.0为9.4。  
## 二、这个漏洞真正危险在哪里？  
  
单纯看需要认证，这个漏洞似乎比未授权RCE的风险低一些。但从PoC验证结果来看，问题的关键并不只是可以执行命令，而是  
攻击者进入管理接口后，可以直接进入设备Root权限环境  
。  
  
公开研究已经验证：  
- 可以执行任意系统命令；  
  
- 执行上下文为uid=0(root)；  
  
- 可以读取设备敏感配置；  
  
- 可以进一步建立持久化机制；  
  
- 理论上可以利用路由器作为内网攻击跳板。  
  
因此，该漏洞的实际风险为：  
> Web管理权限 → 命令注入 → Root → 路由器完全控制  
  
  
而不是普通的配置修改漏洞。  
## 三、技术原理：为什么 NTPServer 可以变成命令执行？  
  
漏洞的核心在于  
用户输入数据进入了Shell命令执行链路  
。研究人员对/bin/ssi进行分析后发现，SetTimeSettings处理函数会获取参数，并将其写入内部配置，  
问题在于，这个过程没有对Shell元字符进行有效过滤  
。随后配置同步阶段会生成类似: uci set %s="%s"的Shell命令，并通过类似system()的方式执行。  
```
用户输入      
   ↓     
NTPServer      
   ↓      
配置数据库      
   ↓     
UCI配置同步      
   ↓      
Shell字符串拼接      
   ↓      
system()
```  
  
当攻击者控制的内容中包含Shell命令替换语法时，Shell会在执行原始命令之前对其进行解析。  
  
由于/bin/ssi以Root身份运行，因此最终结果就是  
：  
```
用户可控输入      
      ↓     
Shell命令替换     
      ↓     
/bin/ssi      
      ↓     
uid=0(root)  
```  
  
GitHub PoC进一步通过时间延迟和输出回传等方式验证了命令确实能够在设备端执行。  
## 四、研究人员实际上验证到了什么？  
  
根据公开PoC：  
### 1. 可以确认命令执行权限  
  
研究人员在实验环境中验证到：  
```
uid=0(root) gid=0(root)  
```  
  
说明注入命令并不是普通Web用户权限，而是在Root上下文中执行。  
### 2. 存在时间型验证方式  
  
研究人员使用延时方式验证命令是否被执行。  
  
当输入触发延时操作后，HTTP请求响应时间明显增加，公开报告记录的延迟约为10秒。  
  
这种验证方式的价值在于即使命令执行结果不会直接返回到HTTP响应中，也可以通过响应时间判断后端是否执行了注入内容。  
### 3. 存在输出回传路径  
  
PoC还验证了可以将命令执行结果写入设备Web目录，再通过HTTP请求读取。  
  
这意味着漏洞不仅可以证明命令被执行，还能够形成：  
```
命令注入      
  ↓      
Root执行      
  ↓      
结果写入Web目录     
  ↓     
HTTP读取结果
```  
  
从而形成较完整的命令执行验证链。  
## 五、漏洞利用需要什么条件？  
  
CVE-2026-93958并不是完全未授权RCE。  
  
公开PoC表明，攻击者首先需要获得有效的Web管理会话，然后才能访问相关DHMAPI功能。CVE的CVSS向量也明确标记为PR:H，即需要较高权限。  
  
攻击条件可以简化为：  
<table><thead><tr><th style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">条件</span></span></section></th><th style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">要求</span></span></section></th></tr></thead><tbody><tr><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">目标设备</span></span></section></td><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">D-Link R95 / BE9500</span></span></section></td></tr><tr><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">网络访问</span></span></section></td><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">可以访问设备管理接口</span></span></section></td></tr><tr><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">身份认证</span></span></section></td><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px; font-weight: 500">需要有效管理会话</span></span></section></td></tr><tr><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">用户交互</span></span></section></td><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">不需要</span></span></section></td></tr><tr><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">攻击复杂度</span></span></section></td><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">低</span></span></section></td></tr><tr><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">漏洞位置</span></span></section></td><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">DHMAPI</span></span></section></td></tr><tr><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">关键接口</span></span></section></td><td style="text-align: left;"><code><span leaf=""><span textstyle="" style="font-size: 16px">SetTimeSettings</span></span></code></td></tr><tr><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">关键参数</span></span></section></td><td style="text-align: left;"><code><span leaf=""><span textstyle="" style="font-size: 16px">NTPServer</span></span></code></td></tr><tr><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">最终权限</span></span></section></td><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px; font-weight: 500">Root</span></span></section></td></tr></tbody></table>  
因此，  
如果路由器管理接口本身已经暴露给不可信网络，或者管理账户凭据已经泄露，那么这个漏洞的利用条件会明显降低。  
## 六、公开PoC意味着什么？  
  
目前已经出现公开PoC仓库：  
  
HackSpeak/CVE-2026-93958  
  
仓库包含：  
- 漏洞技术分析；  
  
- DHMAPI 请求分析；  
  
- SetTimeSettings利用流程；  
- Root 权限执行验证；  
  
- 时间型验证方法；  
  
- Python PoC/EXP；  
  
- 对其他潜在命令注入点的分析。  
  
研究人员还发现同一固件中存在多个类似的注入点，包括TZLocation/DeviceName/DDNS相关字段以及SetNetworkSettings引发的二阶注入等。公开研究称这些位置也进行了Root权限执行验证。  
  
所以此次问题不一定只是单一NTPServer参数漏洞，而可能反映出该固件中存在一类系统性Shell参数处理问题。  
## 七、受影响版本  
  
目前公开CVE数据明确记录的受影响设备为：  
<table><thead><tr><th style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">产品</span></span></section></th><th style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">型号</span></span></section></th><th style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">受影响固件</span></span></section></th></tr></thead><tbody><tr><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">D-Link R95</span></span></section></td><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">BE9500</span></span></section></td><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px; font-weight: 500">BE9500_1.00.16</span></span></section></td></tr></tbody></table>  
D-Link官方产品页面目前可以确认R95/BE9500的产品及固件下载入口，但公开搜索结果中尚未发现能够明确对应CVE-2026-93958的官方修复版本说明。  
## 八、如何排查？  
  
由于该漏洞发生在路由器管理接口，企业用户可以重点关注以下几个方向。  
### ① 检查设备型号和固件  
  
首先确认网络中是否存在：  
```
D-Link R95      
BE9500
```  
  
然后记录当前固件版本，与D-Link官方发布信息进行比对。  
### ② 检查管理接口暴露情况  
  
重点确认：  
```
  WAN      
   ↓   
路由器管理接口
```  
  
是否允许来自互联网或不可信网段的访问，如果管理界面不应该暴露到公网，应立即限制来源地址。  
### ③ 检查异常管理请求  
  
关注DHMAPI相关请求，尤其是SetTimeSettings以及NTPServer参数。  
  
如果日志中能够保留请求内容，可以重点搜索异常Shell特征，例如: $(  ;等。  
  
不过，由于不同固件的日志记录能力不同，不能仅依靠Web日志确认设备是否遭到利用。  
### ④ 检查异常配置变化  
- NTP 配置异常变化；  
  
- DNS 配置变化；  
  
- 管理员账户变化；  
  
- 路由规则变化；  
  
- 异常端口监听；  
  
- 异常出站连接；  
  
- 配置文件突然发生变化。  
  
## 九、修复与缓解建议  
### 1. 优先升级官方修复固件  
  
持续关注D-Link官方R95/BE9500固件发布情况，在官方修复版本明确之前，不建议仅依赖修改NTP配置作为解决方案。  
### 2. 不要将管理接口直接暴露到互联网  
```
Internet      
   ↓      
防火墙/ACL      
   ↓     
管理网段      
   ↓     
R95管理接口
```  
  
仅允许可信管理终端访问。  
### 3. 强化管理员账户  
  
尤其需要避免：  
- 默认密码；  
  
- 弱密码；  
  
- 多台设备使用相同密码；  
  
- 管理凭据长期不更换。  
  
因为该漏洞本身需要有效管理会话，管理凭据安全会直接影响实际攻击门槛。  
### 4. 对管理网络进行隔离  
  
如果路由器承担企业网络入口功能，建议将其管理面与普通终端、访客网络进行隔离。  
  
这样即使管理设备出现漏洞，也可以降低攻击者继续向内部网络横向移动的机会。  
### 5. 已疑似入侵的设备不要只升级固件  
  
如果已经发现异常配置、未知账户、异常网络连接等迹象，应进一步进行：  
```
设备隔离      
   ↓     
配置/日志保全      
   ↓     
检查管理账户     
   ↓     
检查异常配置     
   ↓   
恢复/刷写可信固件  
   ↓
重新设置管理凭据    
   ↓     
恢复网络连接
```  
## 十、这次漏洞最值得关注的三个点  
  
第一，漏洞位置比较明确。  
```
  DHMAPI
      
→ SetTimeSettings
      
→ NTPServer
      
→ 配置同步
      
→ Shell执行  
```  
  
第二，公开PoC已经完成Root权限验证。  
  
研究人员已经验证命令可以在uid=0(root)上下文中执行。  
  
第三，可能存在同类漏洞。  
  
公开研究指出，同一固件中至少还有多个类似输入点存在相同的命令注入模式。所以，从防守角度看，不应该只修一个NTPServer参数，而应该检查整个DHMAPI输入处理链。  
## 十一、总结  
  
CVE-2026-93958本质上是一个  
认证后的远程OS命令注入漏洞  
。  
  
其危险性来自三个因素叠加：  
```
用户可控参数    
      +     
Shell命令拼接     
      +     
Root权限运行      
      ↓     
远程Root命令执行
```  
  
目前公开PoC已经能够验证Root权限命令执行，因此对于部署R95/BE9500的用户而言，需应该检查管理接口是否暴露、管理员凭据是否安全以及设备是否出现异常配置或网络行为。  
  
  
[#CVE]()  
-2026-93958 [#D]()  
-Link [#WIFI7]()  
 [#漏洞]()  
  
  

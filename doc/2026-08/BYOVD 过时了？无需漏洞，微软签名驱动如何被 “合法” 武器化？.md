#  BYOVD 过时了？无需漏洞，微软签名驱动如何被 “合法” 武器化？  
decoylab
                    decoylab  吉宙实验室   2026-08-24 05:55  
  
![](https://mmbiz.qpic.cn/mmbiz_png/VQqGVAok0qrr96jCMf2fUOCIKxgiaLjW9mbsDwXiakPep3oN7aF6ubj2v5AcxWlfnTn5efRclibScianCiaRjZ4gFBA/640?from=appmsg "")  
  
点击蓝字  
  
关注我～  
  
一起发现更多精彩哟  
  
![](https://mmbiz.qpic.cn/mmbiz_png/yD8EGuAMwkKtE35bkkAyaSqicNvicmnsHg3kbPYuhoLsickCwu559KCRMwmlYckjujRuQ2EJT3nrCDx7K8o3NzbZQ/640?from=appmsg "")  
  
  
最近，CheckPoint 发表了一篇  
可不依赖 BYOVD 技术对合法驱动进行滥用  
技术研究细节文章[1]  
，针对合法驱动，以往都是利用漏洞进行，而这一次却不是，当看到这篇报告时很感兴趣，想了解其中原理，以下是报告原文加上自己理解整理。  
  
想一想，如果一个受信任的安全组件可以被重新利用，变成攻击者控制的内核原语，会怎么样？如果一个经过签名的微软修复驱动程序可以被指示从 Ring 0 执行任意文件和注册表操作而无需利用漏洞、避免内存损坏，又会怎么样？  
  
CheckPoint 研究人员称这次发现是源于一次系统入侵事件响应调查，在调查过程中，发现终端遥测数据可疑，这些可疑的数据产生的源头是 Windows Defender，是由于一次修复行为产生，在 C:\Windows\System32\drivers 目录下出现一个随机命令的驱动程序，原始文件名 BTR.sys，驱动对应的服务名称也是随机化，注册表路径  
```
HKLM\SYSTEM\CurrentControlSet\Services\mzqnjtaq
```  
  
<table><tbody><tr><td data-colwidth="191"><section><span style="font-size: 14px;"><span style=""><span leaf="">键名</span></span></span></section></td><td data-colwidth="191"><section><span style="font-size: 14px;"><span style=""><span leaf="">类型</span></span></span></section></td><td data-colwidth="191"><section><span style="font-size: 14px;"><span style=""><span leaf="">数据</span></span></span></section></td></tr><tr><td data-colwidth="191"><section><span style="font-size: 14px;"><span style=""><span leaf="">Type</span></span></span></section></td><td data-colwidth="191"><section><span style="font-size: 14px;"><span style="caret-color: rgb(0, 122, 255);color: rgb(51, 51, 51);font-family: &#34;Open Sans&#34;, &#34;Clear Sans&#34;, &#34;Helvetica Neue&#34;, Helvetica, Arial, &#34;Segoe UI Emoji&#34;, &#34;SF Pro&#34;, sans-serif;font-style: normal;font-variant-caps: normal;font-weight: 400;letter-spacing: normal;orphans: 2;text-align: left;text-indent: 0px;text-transform: none;white-space: pre-wrap;widows: 2;word-spacing: 0px;-webkit-text-stroke-width: 0px;background-color: rgb(255, 255, 255);text-decoration: none;float: none;display: inline !important;" data-pm-slice="0 0 []"><span leaf="">REG_DWORD</span></span></span></section></td><td data-colwidth="191"><section><span style="font-size: 14px;"><span data-pm-slice="0 0 []"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;" data-mpa-action-id="mt6s2by35jz" data-pm-slice="0 0 []">[Kernel Driver]</span></span></span></section></td></tr><tr><td data-colwidth="191"><section><span style="font-size: 14px;"><span style=""><span leaf="">Start</span></span></span></section></td><td data-colwidth="191"><section><span style="font-size: 14px;"><span data-pm-slice="0 0 []"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;" data-mpa-action-id="mt6s1em38o" data-pm-slice="0 0 []">REG_DWORD</span></span></span></section></td><td data-colwidth="191"><section><span style="font-size: 14px;"><span data-pm-slice="0 0 []"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;" data-mpa-action-id="mt6s295p8w7" data-pm-slice="0 0 []">[System Start]</span></span></span></section></td></tr><tr><td data-colwidth="191"><section><span style="font-size: 14px;"><span style=""><span leaf="">ErrorControl</span></span></span></section></td><td data-colwidth="191"><section><span style="font-size: 14px;"><span data-pm-slice="0 0 []"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;" data-mpa-action-id="mt6s1if7xl0" data-pm-slice="0 0 []">REG_DWORD</span></span></span></section></td><td data-colwidth="191"><section><span style="font-size: 14px;"><span data-pm-slice="0 0 []"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;" data-mpa-action-id="mt6s263y51" data-pm-slice="0 0 []">[Ignore]</span></span></span></section></td></tr><tr><td data-colwidth="191"><section><span style="font-size: 14px;"><span style=""><span leaf="">ImagePath</span></span></span></section></td><td data-colwidth="191"><section><span style="font-size: 14px;"><span data-pm-slice="0 0 []"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;" data-mpa-action-id="mt6s0wljghq" data-pm-slice="0 0 []">REG_EXPAND_SZ</span></span></span></section></td><td data-colwidth="191"><section><span style="font-size: 14px;"><span data-mpa-action-id="mt6s23ea1w1c" data-pm-slice="0 0 []"><span md-inline="escape" data-pm-slice="0 0 []"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;">\</span></span><span md-inline="plain"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;">??\C:\Windows\System32\Drivers\mzqnjtaq.sys</span></span></span></span></section></td></tr><tr><td data-colwidth="191"><section><span style="font-size: 14px;"><span style=""><span leaf="">Group</span></span></span></section></td><td data-colwidth="191"><section><span style="font-size: 14px;"><span data-pm-slice="0 0 []"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;" data-mpa-action-id="mt6s1nh0bgz" data-pm-slice="0 0 []">REG_SZ</span></span></span></section></td><td data-colwidth="191"><section><span style="font-size: 14px;"><span data-pm-slice="0 0 []"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;" data-mpa-action-id="mt6s1ztlic5" data-pm-slice="0 0 []">Boot Bus Extender</span></span></span></section></td></tr><tr><td data-colwidth="191"><section><span style="font-size: 14px;"><span style=""><span leaf="">Args</span></span></span></section></td><td data-colwidth="191"><section><span style="font-size: 14px;"><span data-pm-slice="0 0 []"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;" data-mpa-action-id="mt6s1pu61bct" data-pm-slice="0 0 []">REG_SZ</span></span></span></section></td><td data-colwidth="191"><section data-mpa-action-id="mt6s2ilux77"><span style="font-size: 14px;"><span data-pm-slice="0 0 []"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-family: mp-quote, &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;line-height: 1.6;letter-spacing: 0.034em;font-style: normal;font-weight: normal;" data-mpa-action-id="mt6s1wn7163k" data-pm-slice="0 0 []">C:\Windows\System32\Drivers\mzqnjtaq.sys:changelist</span></span></span></section></td></tr></tbody></table>  
  
研究人员认为以下几点和恶意内核加载器行为非常相似  
- 系统重启前释放了一个驱动文件  
  
- 创建了一个加载此驱动的临时服务名  
  
- 有 RC4 加密数据  
  
- 与驱动程序文件中的 ADS 流 (:changelist) 进行交互  
  
- 执行后自动清理  
  
其中 ADS 流中包含一个加密的二进制数据，用作驱动程序的配置输入。此驱动是由微软签名的合法文件，研究人员认为这可能是被攻击者利用或者滥用，最初他们的假设是攻击者利用了此驱动程序进行了后渗透利用，但最终证明该假设是错误的，这个行为是由 Defender 的合法修复引起的。  
  
这一意外发现促使研究人员对 BTR.sys 进行了全面的逆向工程，揭示了未公开的功能、自定义协议以及出乎意料的强大内核执行模型。  
  
BTR.sys 来自于 mpengine.dll，而 mpengine.dll 来自于 mpam-fe.exe。mpam-fe.exe 是 Windows Defender 防病毒软件的安全智能和病毒定义更新文件，里面包含了最新的病毒特征码，供手动更新使用，下载链接  
```
https://go.microsoft.com/fwlink/?LinkID=121721&arch=x86
https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64
https://go.microsoft.com/fwlink/?LinkID=121721&arch=arm64
```  
  
文件大小 202MB 左右 (x86)，在资源表中存在 CAB 数据，大小占到了 99.8%  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/bCJqpmbtQZwHP2RIj6Izz4MicqE72x83E864ESRmG6b2FRBxic4nOI6b5M055ApTSqMGNp25wEs46SCPzRkK4YY20Ov0dR1qT8Sajbje6fF0Q/640?wx_fmt=png&from=appmsg "")  
  
将 CAB 数据 dump 出并尝试解压，里面包含 6 个文件  
  
![](https://mmbiz.qpic.cn/mmbiz_png/bCJqpmbtQZxGZtOlEj0Oicfwic2vZbXFXHib2mPoXADRWl8qVwO9ibGbiaBs5xPLsuLia3bmcWInJfYibgGKWKH2xA4dYM8nibFbsGzQHvH3pVxfFc8/640?wx_fmt=png&from=appmsg "")  
  
作下简单说明：  
- mpasbase.vdm - 随平台更新发布 (通常每月一次)，包含反间谍软件签名  
  
- mpasdlta.vdm - 每日发布，仅包含新增的签名，在运行时加载数据库时，这些签名会在内存中与 mpasbase.vdm 进行合并  
  
- mpavbase.vdm - 随平台更新发布 (通常每月一次)，包含反恶意软件签名  
  
- mpavdlta.vdm - 每日发布，仅包含新增的签名，在运行时加载数据库时，这些签名会在内存中与 mpavbase.vdm 进行合并  
  
- mpengine.dll - 实现了扫描器、模拟器、从 VDM 文件加载签名和签名处理等功能  
  
- MpSigStub.exe - 主体文件运行后，用于更新病毒库  
  
如果是自动更新，4 个 vdm 文件及 dll 文件会保存在以下目录中  
```
C:\ProgramData\Microsoft\Windows Defender\Definition Updates\{随机 GUID}
```  
  
最关键的文件是 mpengine.dll，关于它是如何解析 VDM 文件可单独另作一篇单独进行讨论  
  
在它的资源表中，有一个内嵌的驱动文件数据，资源名为 BOOTTIMETOOL  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/bCJqpmbtQZwGQLiaxE0AJwJS3f1mqkdKcR6CngCZv2u0Kpq5gHWVJrk8Al731fzFYJdoxiacVClqP87iapIrt3Dx4FgmCwicVrw9991kRNsDz78/640?wx_fmt=png&from=appmsg "")  
  
而这个内嵌的驱动文件便是 BTR.sys  
  
![](https://mmbiz.qpic.cn/mmbiz_png/bCJqpmbtQZzwdghuoAXZk3tM1ZiczQhoApvQVicjsmmk8RZtwbgqyKbNxYr3f2VibibHYPHbMwJqRJ1Zr7cSOGxaeCHtNvTgW5g2kKTW7vphzas/640?wx_fmt=png&from=appmsg "")  
  
从 mpengine.dll 中释放  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/bCJqpmbtQZzpPic1l3CPfprNtr9lEEictxsDbYokM1lvvMVTX8eGHliauK7gdaq6kxtcR0JVKEYwoqMiaDZLzicHCOxbCdk9ia6hDwBgzj0qt3JT0/640?wx_fmt=png&from=appmsg "")  
  
属于 "one-shot" 驱动，加载后完成预定的任务，汇报执行状态和结果，最后主动请求从系统中移除自己。  
  
该驱动不提供 IOCTL 接口，而是读取服务注册表项中 Args 值指向的 Blob 配置数据，注册表路径  
```
HKLM\SYSTEM\CurrentControlSet\Services\[随机值]\Args
```  
  
![](https://mmbiz.qpic.cn/mmbiz_png/bCJqpmbtQZxiaz1hdpibGG5ugG4HOTpc3koVMym0QGYVicMljSFf97v5aLxS1juiaxgOfXqAvJuloKdokkHJtsAgbEOMiaVC0Pcsuj5YWYoG16uE/640?wx_fmt=png&from=appmsg "")  
  
Blob 配置数据包含 RC4 加密的 ADS 流  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/bCJqpmbtQZy1MTMKRfkBMiaJwUiaxicqfNTjlvYtvicar9PolJ7HoNDVG8fc2YWt1ibHiaJ9meNwSian8XhA3YdBBQ5U6m8CULe2SylsAOQt9vMsAU/640?wx_fmt=png&from=appmsg "")  
  
并受到加密和完整性检查的双重保护，以防止篡改，RC4 加密，KEY 硬编码在内  
  
![](https://mmbiz.qpic.cn/mmbiz_png/bCJqpmbtQZxdKWIueauxBYDW6WGINoh8XibJbQnSVb5fWW1Bu1k5MpL9aajBBvPbAXdCSMwkibz9h5Fwve9BgmeRaibHPrvmDfxulE5z9J37Vc/640?wx_fmt=png&from=appmsg "")  
  
再做 CRC32 校验，它与标准 CRC32 实现稍有些许不同，在最后结果中并没有按位取反返回，所以这里的结果是标准 CRC32 校验值的再取反，即 ~CRC32  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/bCJqpmbtQZyymy3nMnGWZC5P5YTw54LadDD99U42N90mksyhgFQmlS7jJBeUAvGc3t6Mqmz7vdZibia4OTwaVzicUTzLTFYNIg49ia9uicmvibXF8/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ic71wVtGetfHDu1wydMskOKuoXicunjouOlWhIdKVhicUw0VEEUibaahW73bFBktvxRUb7w5A9GS4POh7uqoAm4I8w/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Bd7jlAWL2kJb0ad17GyZNCYMgklIXicN3a4gtzIPibVdu0k3QMYmTqXZcJbzKN4BCUdoPTPfNvEyOenQmWShIKUw/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/EeJSAMoAk9AaZBDONXq5mr1CnahhdFPRnN7Zk7c1R8mNJb1T2PCGUYiatym44rUOVbxzCHRByoeKaqz0Eh6o3ng/640 "")  
  
BLOB 配置数据结构  
  
![](https://mmbiz.qpic.cn/mmbiz_png/cZBtichFtVOicw0ecmyjicxsyvdEtIDcDaN5EoEiaejwr3qhO8FFapczeI7LbibJlGTxgjQIm6Avy8ISPuEPI6yus7Q/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/3QTIKn9jFuMib8ic1Qldejuib3qXtTqDpDTRq0ciaZRBbE4WGzg7K2FvVhaIJ4HM6gs06qqYzoiaz22FEF38R0Ozr8A/640 "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/3AJf6zpEXO8e3OMs0hVse7E8dySmiaRe5p5vExYnzKGdUV6pMv9VmicM0ib4QIUj08lGtiaQp2dXbSn2coO8mK4yBg/640 "")  
  
  
查看驱动文件的 BLOB ADS 流配置数据  
```
Get-Item .\xxxxxxxx.sys -Stream *
```  
  
![](https://mmbiz.qpic.cn/mmbiz_png/bCJqpmbtQZzI4K2QA6VVyuQRGcReBjicKtJ8mm176RzR1eusZWiaRma6h3avdyvQibRkpLOPgm7RYKBiaLAgbnOPpy0Pvw7hE0icGoCtWLPLIEjI/640?wx_fmt=png&from=appmsg "")  
  
提取 ADS 流数据并解密  
```
$data = Get-Content .\xxxxxxxx.sys -Stream [ADS Stream name] -Raw -Encoding Byte
[IO.File]::WriteAllBytes("[File Path]", $data)
```  
  
![](https://mmbiz.qpic.cn/mmbiz_png/bCJqpmbtQZyibk8QAd27sTK3MiaCHVt2CRsZ602J8H9yp6Y4hiaIKMCPPn8H56OqpyiajHJgDhjeUovgkCt7UhIbanicKaH1dr5bMIvMoFZ2epRY/640?wx_fmt=png&from=appmsg "")  
  
解密后数据  
  
![](https://mmbiz.qpic.cn/mmbiz_png/bCJqpmbtQZwDgpu3iaEUYht0YZwEY3eBsBEG9IpHtg7oGtxxWMTNOZQRFtmqBOYceibKVxPpHP8Ou5cn3lzWsDSASv5gkX300rvCLfUicDHFn0/640?wx_fmt=png&from=appmsg "")  
  
为方便查看定位，写了个模板来解析此结构  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/bCJqpmbtQZwQxmlHJUbpniaJKWzUmhb1ibFVbQAG8ViamTOd0jMB6bR4EDnNzcqHaYLsIlUGnG78qhXOjKSic2iaryBaT6UzbzTm7ls4OzS7e9ps/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ic71wVtGetfHDu1wydMskOKuoXicunjouOlWhIdKVhicUw0VEEUibaahW73bFBktvxRUb7w5A9GS4POh7uqoAm4I8w/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Bd7jlAWL2kJb0ad17GyZNCYMgklIXicN3a4gtzIPibVdu0k3QMYmTqXZcJbzKN4BCUdoPTPfNvEyOenQmWShIKUw/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/EeJSAMoAk9AaZBDONXq5mr1CnahhdFPRnN7Zk7c1R8mNJb1T2PCGUYiatym44rUOVbxzCHRByoeKaqz0Eh6o3ng/640 "")  
  
Global Header  
  
![](https://mmbiz.qpic.cn/mmbiz_png/cZBtichFtVOicw0ecmyjicxsyvdEtIDcDaN5EoEiaejwr3qhO8FFapczeI7LbibJlGTxgjQIm6Avy8ISPuEPI6yus7Q/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/3QTIKn9jFuMib8ic1Qldejuib3qXtTqDpDTRq0ciaZRBbE4WGzg7K2FvVhaIJ4HM6gs06qqYzoiaz22FEF38R0Ozr8A/640 "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/3AJf6zpEXO8e3OMs0hVse7E8dySmiaRe5p5vExYnzKGdUV6pMv9VmicM0ib4QIUj08lGtiaQp2dXbSn2coO8mK4yBg/640 "")  
  
  
开始 24 字节大小固定  
  
<table><tbody><tr><td data-colwidth="143"><section><span style="font-size: 14px;"><span leaf="">偏移</span></span></section></td><td data-colwidth="143"><section><span style="font-size: 14px;"><span leaf="">大小</span></span></section></td><td data-colwidth="143"><section><span style="font-size: 14px;"><span leaf="">字段</span></span></section></td><td data-colwidth="143"><section><span style="font-size: 14px;"><span leaf="">描述</span></span></section></td></tr><tr><td data-colwidth="143"><section><span style="font-size: 14px;"><span leaf="">0x00</span></span></section></td><td data-colwidth="143"><section><span style="font-size: 14px;"><span leaf="">4</span></span></section></td><td data-colwidth="143"><section><span style="font-size: 14px;"><span leaf="">Magic</span></span></section></td><td data-colwidth="143"><p><span style="font-size: 14px;"><span leaf="">0xFEE1DEAD (小端序)</span></span></p></td></tr><tr><td data-colwidth="143"><section><span style="font-size: 14px;"><span leaf="">0x04</span></span></section></td><td data-colwidth="143"><section><span style="font-size: 14px;"><span leaf="">4</span></span></section></td><td data-colwidth="143"><section><span style="font-size: 14px;"><span leaf="">Version</span></span></section></td><td data-colwidth="143"><p><span style="font-size: 14px;"><span leaf="">0x00000002</span></span></p></td></tr><tr><td data-colwidth="143"><section><span style="font-size: 14px;"><span leaf="">0x08</span></span></section></td><td data-colwidth="143"><section><span style="font-size: 14px;"><span leaf="">4</span></span></section></td><td data-colwidth="143"><section><span style="font-size: 14px;"><span leaf="">PayloadOffset</span></span></section></td><td data-colwidth="143"><p><span style="font-size: 14px;"><span leaf="">0x00000010</span></span></p></td></tr><tr><td data-colwidth="143"><section><span style="font-size: 14px;"><span leaf="">0x0C</span></span></section></td><td data-colwidth="143"><section><span style="font-size: 14px;"><span leaf="">4</span></span></section></td><td data-colwidth="143"><section><span style="font-size: 14px;"><span leaf="">GlobalCRC</span></span></section></td><td data-colwidth="143"><p><span style="font-size: 14px;"><span leaf="">~CRC32 校验值</span></span></p></td></tr><tr><td data-colwidth="143"><section><span style="font-size: 14px;"><span leaf="">0x10</span></span></section></td><td data-colwidth="143"><section><span style="font-size: 14px;"><span leaf="">8</span></span></section></td><td data-colwidth="143"><section><span style="font-size: 14px;"><span leaf="">TransID</span></span></section></td><td data-colwidth="143"><p data-mpa-action-id="mt6sqaz0zzz"><span style="font-size: 14px;"><span leaf="">低 4 字节为 ~CRC32(Payload)，高 4 字节为 Size(Payload)</span></span></p></td></tr></tbody></table>  
  
注：  
以上结构包括后面提到的结构都是通过逆向工程获得，微软并没有公开文档记录  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ic71wVtGetfHDu1wydMskOKuoXicunjouOlWhIdKVhicUw0VEEUibaahW73bFBktvxRUb7w5A9GS4POh7uqoAm4I8w/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Bd7jlAWL2kJb0ad17GyZNCYMgklIXicN3a4gtzIPibVdu0k3QMYmTqXZcJbzKN4BCUdoPTPfNvEyOenQmWShIKUw/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/EeJSAMoAk9AaZBDONXq5mr1CnahhdFPRnN7Zk7c1R8mNJb1T2PCGUYiatym44rUOVbxzCHRByoeKaqz0Eh6o3ng/640 "")  
  
Global Payload  
  
![](https://mmbiz.qpic.cn/mmbiz_png/cZBtichFtVOicw0ecmyjicxsyvdEtIDcDaN5EoEiaejwr3qhO8FFapczeI7LbibJlGTxgjQIm6Avy8ISPuEPI6yus7Q/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/3QTIKn9jFuMib8ic1Qldejuib3qXtTqDpDTRq0ciaZRBbE4WGzg7K2FvVhaIJ4HM6gs06qqYzoiaz22FEF38R0Ozr8A/640 "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/3AJf6zpEXO8e3OMs0hVse7E8dySmiaRe5p5vExYnzKGdUV6pMv9VmicM0ib4QIUj08lGtiaQp2dXbSn2coO8mK4yBg/640 "")  
  
  
紧跟着 Global Header 后是 Global Payload，内容以空字符结尾的 Unicode 字符串，作用为存放 Feedback File 路径  
```
\??\D:\Projects\c\BTR_CLI\x64\Release\ezkuqxry.sys:ezkuqxry.dat
```  
  
驱动程序会创建此文件并写入 Transaction 执行报告，结构基本与 BLOB 配置数据结构相同，但会将每项数据 Payload 的前 4 个字节更新为该特定操作产生的 NTSTATUS 值  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ic71wVtGetfHDu1wydMskOKuoXicunjouOlWhIdKVhicUw0VEEUibaahW73bFBktvxRUb7w5A9GS4POh7uqoAm4I8w/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Bd7jlAWL2kJb0ad17GyZNCYMgklIXicN3a4gtzIPibVdu0k3QMYmTqXZcJbzKN4BCUdoPTPfNvEyOenQmWShIKUw/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/EeJSAMoAk9AaZBDONXq5mr1CnahhdFPRnN7Zk7c1R8mNJb1T2PCGUYiatym44rUOVbxzCHRByoeKaqz0Eh6o3ng/640 "")  
  
Item Header  
  
![](https://mmbiz.qpic.cn/mmbiz_png/cZBtichFtVOicw0ecmyjicxsyvdEtIDcDaN5EoEiaejwr3qhO8FFapczeI7LbibJlGTxgjQIm6Avy8ISPuEPI6yus7Q/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/3QTIKn9jFuMib8ic1Qldejuib3qXtTqDpDTRq0ciaZRBbE4WGzg7K2FvVhaIJ4HM6gs06qqYzoiaz22FEF38R0Ozr8A/640 "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/3AJf6zpEXO8e3OMs0hVse7E8dySmiaRe5p5vExYnzKGdUV6pMv9VmicM0ib4QIUj08lGtiaQp2dXbSn2coO8mK4yBg/640 "")  
  
  
Item 结构跟随在 Global Payload 之后，里面包含了 Action 指定操作 ID，Item 中 Header 结构  
  
<table><tbody><tr><td data-colwidth="143"><section><span style="font-size: 14px;"><span leaf="">偏移</span></span></section></td><td data-colwidth="143"><section><span style="font-size: 14px;"><span leaf="">大小</span></span></section></td><td data-colwidth="143"><section><span style="font-size: 14px;"><span leaf="">字段</span></span></section></td><td data-colwidth="143"><section><span style="font-size: 14px;"><span leaf="">描述</span></span></section></td></tr><tr><td data-colwidth="143"><section><span style="font-size: 14px;"><span leaf="">0x00</span></span></section></td><td data-colwidth="143"><section><span style="font-size: 14px;"><span leaf="">4</span></span></section></td><td data-colwidth="143"><section><span style="font-size: 14px;"><span leaf="">DataSize</span></span></section></td><td data-colwidth="143"><p><span style="font-size: 14px;"><span leaf="">Data 大小，包括填充</span></span></p></td></tr><tr><td data-colwidth="143"><section><span style="font-size: 14px;"><span leaf="">0x04</span></span></section></td><td data-colwidth="143"><section><span style="font-size: 14px;"><span leaf="">4</span></span></section></td><td data-colwidth="143"><section><span style="font-size: 14px;"><span leaf="">ActionID</span></span></section></td><td data-colwidth="143"><p><span style="font-size: 14px;"><span leaf="">要执行的操作 ID (1 ~ 6)</span></span></p></td></tr><tr><td data-colwidth="143"><section><span style="font-size: 14px;"><span leaf="">0x08</span></span></section></td><td data-colwidth="143"><section><span style="font-size: 14px;"><span leaf="">4</span></span></section></td><td data-colwidth="143"><section><span style="font-size: 14px;"><span leaf="">HeaderCRC</span></span></section></td><td data-colwidth="143"><p><span style="font-size: 14px;"><span leaf="">~CRC32</span></span></p></td></tr><tr><td data-colwidth="143"><section><span style="font-size: 14px;"><span leaf="">0x0C</span></span></section></td><td data-colwidth="143"><section><span style="font-size: 14px;"><span leaf="">4</span></span></section></td><td data-colwidth="143"><section><span style="font-size: 14px;"><span leaf="">DataCRC</span></span></section></td><td data-colwidth="143"><p data-mpa-action-id="mt6swpdz1yrp"><span style="font-size: 14px;"><span leaf="">Data 的 ~CRC32</span></span></p></td></tr></tbody></table>  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ic71wVtGetfHDu1wydMskOKuoXicunjouOlWhIdKVhicUw0VEEUibaahW73bFBktvxRUb7w5A9GS4POh7uqoAm4I8w/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Bd7jlAWL2kJb0ad17GyZNCYMgklIXicN3a4gtzIPibVdu0k3QMYmTqXZcJbzKN4BCUdoPTPfNvEyOenQmWShIKUw/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/EeJSAMoAk9AaZBDONXq5mr1CnahhdFPRnN7Zk7c1R8mNJb1T2PCGUYiatym44rUOVbxzCHRByoeKaqz0Eh6o3ng/640 "")  
  
Item Data  
  
![](https://mmbiz.qpic.cn/mmbiz_png/cZBtichFtVOicw0ecmyjicxsyvdEtIDcDaN5EoEiaejwr3qhO8FFapczeI7LbibJlGTxgjQIm6Avy8ISPuEPI6yus7Q/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/3QTIKn9jFuMib8ic1Qldejuib3qXtTqDpDTRq0ciaZRBbE4WGzg7K2FvVhaIJ4HM6gs06qqYzoiaz22FEF38R0Ozr8A/640 "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/3AJf6zpEXO8e3OMs0hVse7E8dySmiaRe5p5vExYnzKGdUV6pMv9VmicM0ib4QIUj08lGtiaQp2dXbSn2coO8mK4yBg/640 "")  
  
  
Data 结构取决于 ActionID  
- 简单操作 (1 和 2)：Data 直接以路径开头  
  
- 复杂操作 (3 ~ 6)：Data 以 Flags 字段开头  
  
Data 中填充数据，驱动程序要求在 Data 的末尾附加 4 个 null 字节，这并非用于对齐，对于缺少 Flags 字段来说 (ActionID 为 1 或 2)，驱动程序会利用这部分预留空间生成 Feedback Report，将字符串数据向内移动 4 个字节到这部分填充区域，从而在缓冲区开头为 NTSTATUS 腾出空间，避免重新分配内存。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ic71wVtGetfHDu1wydMskOKuoXicunjouOlWhIdKVhicUw0VEEUibaahW73bFBktvxRUb7w5A9GS4POh7uqoAm4I8w/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Bd7jlAWL2kJb0ad17GyZNCYMgklIXicN3a4gtzIPibVdu0k3QMYmTqXZcJbzKN4BCUdoPTPfNvEyOenQmWShIKUw/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/EeJSAMoAk9AaZBDONXq5mr1CnahhdFPRnN7Zk7c1R8mNJb1T2PCGUYiatym44rUOVbxzCHRByoeKaqz0Eh6o3ng/640 "")  
  
驱动程序武器化  
  
![](https://mmbiz.qpic.cn/mmbiz_png/cZBtichFtVOicw0ecmyjicxsyvdEtIDcDaN5EoEiaejwr3qhO8FFapczeI7LbibJlGTxgjQIm6Avy8ISPuEPI6yus7Q/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/3QTIKn9jFuMib8ic1Qldejuib3qXtTqDpDTRq0ciaZRBbE4WGzg7K2FvVhaIJ4HM6gs06qqYzoiaz22FEF38R0Ozr8A/640 "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/3AJf6zpEXO8e3OMs0hVse7E8dySmiaRe5p5vExYnzKGdUV6pMv9VmicM0ib4QIUj08lGtiaQp2dXbSn2coO8mK4yBg/640 "")  
  
  
CheckPoint 已公开了 PoC[2]  
，实现了 ActionID 1 ~ 6 功能  
  
<table><tbody><tr><td data-colwidth="191"><section><span style="font-size: 14px;"><span leaf="">ActionID</span></span></section></td><td data-colwidth="191"><section><span style="font-size: 14px;"><span leaf="">说明</span></span></section></td><td data-colwidth="191"><section><span style="font-size: 14px;"><span leaf="">描述</span></span></section></td></tr><tr><td data-colwidth="191"><section><span style="font-size: 14px;"><span leaf="">1</span></span></section></td><td data-colwidth="191"><p><span style="font-size: 14px;"><span leaf="">删除文件</span></span></p></td><td data-colwidth="191"><p><span style="font-size: 14px;"><span leaf="">内核级删除，绕过独占文件锁</span></span></p></td></tr><tr><td data-colwidth="191"><section><span style="font-size: 14px;"><span leaf="">2</span></span></section></td><td data-colwidth="191"><p><span style="font-size: 14px;"><span leaf="">删除目录</span></span></p></td><td data-colwidth="191"><p><span style="font-size: 14px;"><span leaf="">删除一个空目录，且必须为空目录</span></span></p></td></tr><tr><td data-colwidth="191"><section><span style="font-size: 14px;"><span leaf="">3</span></span></section></td><td data-colwidth="191"><p><span style="font-size: 14px;"><span leaf="">移动文件</span></span></p></td><td data-colwidth="191"><p><span style="font-size: 14px;"><span leaf="">如果目标路径为空，则此操作相当于删除操作，如果不为空，则允许任意写入或移动文件，例如将恶意 DLL 文件放入 System32 文件夹中</span></span></p></td></tr><tr><td data-colwidth="191"><section><span style="font-size: 14px;"><span leaf="">4</span></span></section></td><td data-colwidth="191"><p><span style="font-size: 14px;"><span leaf="">删除注册表 Key</span></span></p></td><td data-colwidth="191"><p><span style="font-size: 14px;"><span leaf="">删除注册表项及其子项</span></span></p></td></tr><tr><td data-colwidth="191"><section><span style="font-size: 14px;"><span leaf="">5</span></span></section></td><td data-colwidth="191"><p><span style="font-size: 14px;"><span leaf="">删除注册表 Value</span></span></p></td><td data-colwidth="191"><p><span style="font-size: 14px;"><span leaf="">驱动程序通过查找 \\ 来解析字符串，从而将键与值分开，标准路径无法正常工作，需要特定格式</span></span></p></td></tr><tr><td data-colwidth="191"><section><span style="font-size: 14px;"><span leaf="">6</span></span></section></td><td data-colwidth="191"><p><span style="font-size: 14px;"><span leaf="">设置注册表 Value</span></span></p></td><td data-colwidth="191"><p data-mpa-action-id="mt6t1eyp1czw"><span style="font-size: 14px;"><span leaf="">任意写入注册表 + 创建注册表项，可用于建立持久性或禁用安全控制</span></span></p></td></tr></tbody></table>  
  
![](https://mmbiz.qpic.cn/mmbiz_png/bCJqpmbtQZz3VNL7v8qbVGXpicI4mWEhlLjDLCaX1ZtbTDnl9kGQq0gEXfNeAd12RcQMawricX6zEjpnWicJ1icamaFfUougyeqXSicTYxd2wS4M/640?wx_fmt=png&from=appmsg "")  
  
BTR.sys 的一个独特之处在于成功执行时的返回值，返回 0xC0000056 (STATUS_DELETE_PENDING)，而不是 STATUS_SUCCESS，这会向内核发出信号，立即卸载驱动程序并将驱动程序对象标记为删除，从而确保它不会持久存在于内存中。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ic71wVtGetfHDu1wydMskOKuoXicunjouOlWhIdKVhicUw0VEEUibaahW73bFBktvxRUb7w5A9GS4POh7uqoAm4I8w/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Bd7jlAWL2kJb0ad17GyZNCYMgklIXicN3a4gtzIPibVdu0k3QMYmTqXZcJbzKN4BCUdoPTPfNvEyOenQmWShIKUw/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/EeJSAMoAk9AaZBDONXq5mr1CnahhdFPRnN7Zk7c1R8mNJb1T2PCGUYiatym44rUOVbxzCHRByoeKaqz0Eh6o3ng/640 "")  
  
清理痕迹  
  
![](https://mmbiz.qpic.cn/mmbiz_png/cZBtichFtVOicw0ecmyjicxsyvdEtIDcDaN5EoEiaejwr3qhO8FFapczeI7LbibJlGTxgjQIm6Avy8ISPuEPI6yus7Q/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/3QTIKn9jFuMib8ic1Qldejuib3qXtTqDpDTRq0ciaZRBbE4WGzg7K2FvVhaIJ4HM6gs06qqYzoiaz22FEF38R0Ozr8A/640 "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/3AJf6zpEXO8e3OMs0hVse7E8dySmiaRe5p5vExYnzKGdUV6pMv9VmicM0ib4QIUj08lGtiaQp2dXbSn2coO8mK4yBg/640 "")  
  
  
如果是 Defender 正常发出，调用该驱动程序，会在 \SystemRoot\Temp 下创建 BootClean.log 日志文件，然而 CheckPoint 给出的 BTR_CLI 工具会在 Trans 列表开头注入一个 ActionID 为 1 的项，目标是 BootClean.log，这样驱动创建日志，执行用户操作，然后在卸载前删除自身的日志文件，这样就能清除痕迹。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ic71wVtGetfHDu1wydMskOKuoXicunjouOlWhIdKVhicUw0VEEUibaahW73bFBktvxRUb7w5A9GS4POh7uqoAm4I8w/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Bd7jlAWL2kJb0ad17GyZNCYMgklIXicN3a4gtzIPibVdu0k3QMYmTqXZcJbzKN4BCUdoPTPfNvEyOenQmWShIKUw/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/EeJSAMoAk9AaZBDONXq5mr1CnahhdFPRnN7Zk7c1R8mNJb1T2PCGUYiatym44rUOVbxzCHRByoeKaqz0Eh6o3ng/640 "")  
  
BTR_CLI  
  
![](https://mmbiz.qpic.cn/mmbiz_png/cZBtichFtVOicw0ecmyjicxsyvdEtIDcDaN5EoEiaejwr3qhO8FFapczeI7LbibJlGTxgjQIm6Avy8ISPuEPI6yus7Q/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/3QTIKn9jFuMib8ic1Qldejuib3qXtTqDpDTRq0ciaZRBbE4WGzg7K2FvVhaIJ4HM6gs06qqYzoiaz22FEF38R0Ozr8A/640 "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/3AJf6zpEXO8e3OMs0hVse7E8dySmiaRe5p5vExYnzKGdUV6pMv9VmicM0ib4QIUj08lGtiaQp2dXbSn2coO8mK4yBg/640 "")  
  
  
BTR_CLI 是一个功能齐全的 PoC，展示了利用 BTR.sys 的攻击用途，实现了一个完整的利用链，该利用链模拟了 MpEngine.dll 的行为，同时扩展了其功能，可用于研究和红队演练。  
  
BTR_CLI 工具在涵盖 Windows 操作系统的广泛测试环境中进行了全面评估，测试范围从 Windows 7 Build 7601 (2011 年发布) 到 Windows 8.1 和 Windows 10 22H2，直到最新的完全更新的 Windows 11 25H2 (截至 2026 年 7 月)。测试证实，该工具能够在所有版本中成功执行所有受支持的功能 (ActionID)。值得注意的是，虽然该工具包含一个嵌入式备用驱动程序，但在测试过程中从未需要用到该备用驱动程序。在所有情况下，目标特定的 BTR.sys 文件都已成功从本地 MpEngine.dll 中提取出来。此功能使该工具无需引入外部二进制文件即可运行，从而有效避免了类似 BYOVD 的情况。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ic71wVtGetfHDu1wydMskOKuoXicunjouOlWhIdKVhicUw0VEEUibaahW73bFBktvxRUb7w5A9GS4POh7uqoAm4I8w/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Bd7jlAWL2kJb0ad17GyZNCYMgklIXicN3a4gtzIPibVdu0k3QMYmTqXZcJbzKN4BCUdoPTPfNvEyOenQmWShIKUw/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/EeJSAMoAk9AaZBDONXq5mr1CnahhdFPRnN7Zk7c1R8mNJb1T2PCGUYiatym44rUOVbxzCHRByoeKaqz0Eh6o3ng/640 "")  
  
把握黄金窗口  
  
![](https://mmbiz.qpic.cn/mmbiz_png/cZBtichFtVOicw0ecmyjicxsyvdEtIDcDaN5EoEiaejwr3qhO8FFapczeI7LbibJlGTxgjQIm6Avy8ISPuEPI6yus7Q/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/3QTIKn9jFuMib8ic1Qldejuib3qXtTqDpDTRq0ciaZRBbE4WGzg7K2FvVhaIJ4HM6gs06qqYzoiaz22FEF38R0Ozr8A/640 "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/3AJf6zpEXO8e3OMs0hVse7E8dySmiaRe5p5vExYnzKGdUV6pMv9VmicM0ib4QIUj08lGtiaQp2dXbSn2coO8mK4yBg/640 "")  
  
  
BTR.sys 的运行前提表明它能够在操作系统启动过程的早期阶段执行。然而，实证测试证实了一个严格的架构限制，BTR.sys 不能作为 SERVICE_BOOT_START (Start=0) 驱动程序运行。  
  
虽然标准的 EDR 内核微型过滤器使用 Start=0 在内核初始化后立即注册回调，但 BTR.sys 由 Microsoft 设计，用于在其 DriverEntry 中直接执行文件 I/O 操作 (读取 ADS 配置和创建日志)。在启动过程的第 0 阶段，Windows 对象管理器尚未建立 SystemRoot 符号链接，存储堆栈也尚未完全初始化。因此，强制 BTR.sys 设置为 Start=0 会导致立即失败。  
  
因此，该驱动程序必须配置为 SERVICE_SYSTEM_START (Start=1) 。为了最大限度地发挥其攻击效用，它被分配到 "Boot Bus Extender" 加载顺序组。此配置将其置于第一阶段最早可用的实际执行点之一，紧随文件系统 (Ntfs.sys) 初始化以及从操作系统加载程序过渡到内核 I/O 管理器之后。值得注意的是，此配置与 MpEngine.dll 在合法的 Windows Defender 修复事件期间用于部署驱动程序的机制完全相同。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ic71wVtGetfHDu1wydMskOKuoXicunjouOlWhIdKVhicUw0VEEUibaahW73bFBktvxRUb7w5A9GS4POh7uqoAm4I8w/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Bd7jlAWL2kJb0ad17GyZNCYMgklIXicN3a4gtzIPibVdu0k3QMYmTqXZcJbzKN4BCUdoPTPfNvEyOenQmWShIKUw/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/EeJSAMoAk9AaZBDONXq5mr1CnahhdFPRnN7Zk7c1R8mNJb1T2PCGUYiatym44rUOVbxzCHRByoeKaqz0Eh6o3ng/640 "")  
  
加载顺序分析和服务组优先级  
  
![](https://mmbiz.qpic.cn/mmbiz_png/cZBtichFtVOicw0ecmyjicxsyvdEtIDcDaN5EoEiaejwr3qhO8FFapczeI7LbibJlGTxgjQIm6Avy8ISPuEPI6yus7Q/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/3QTIKn9jFuMib8ic1Qldejuib3qXtTqDpDTRq0ciaZRBbE4WGzg7K2FvVhaIJ4HM6gs06qqYzoiaz22FEF38R0Ozr8A/640 "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/3AJf6zpEXO8e3OMs0hVse7E8dySmiaRe5p5vExYnzKGdUV6pMv9VmicM0ib4QIUj08lGtiaQp2dXbSn2coO8mK4yBg/640 "")  
  
  
Windows 内核通过两次不同的扫描过程来强制执行严格的时间层次结构，即扫描 ServiceGroupOrder 注册表项。首先，操作系统加载程序在阶段 0 加载所有 Start=0 (启动) 驱动程序。 阶段 0 结束后，内核 I/O 管理器再次扫描该列表，在阶段 1 加载 Start=1 (系统) 驱动程序。正是在这一特定阶段， "Boot Bus Extender" 组提供了战略优势。虽然 Start=0 安全筛选器 (例如 WdFilter) 已经处于活动状态，但 BTR.sys 会在第一阶段开始时立即执行，从而有效地抢占其他位于较低优先级组，例如 "FSFilter 活动监视器" 中的关键安全驱动程序 (UCPD、WdNisDrv)：  
```
System Reserved
EMS
WdfLoadGroup
Boot Bus Extender             <-- BTR.sys executes here (Start=1)
... (23 Groups) ...
FSFilter Replication
FSFilter Anti-Virus           <-- WdFilter (the Group is lower, but Start=0)
FSFilter Undelete
FSFilter Activity Monitor     <-- UCPD.sys (Start=1)
... (24 Groups) ...
NDIS                          <-- Network Drivers
... (14 Groups) ...
```  
  
这种架构定位创造了一个 "黄金窗口" (文件系统可写，但高级安全服务和用户模式保护代理尚未启动的特定时间段)。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ic71wVtGetfHDu1wydMskOKuoXicunjouOlWhIdKVhicUw0VEEUibaahW73bFBktvxRUb7w5A9GS4POh7uqoAm4I8w/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Bd7jlAWL2kJb0ad17GyZNCYMgklIXicN3a4gtzIPibVdu0k3QMYmTqXZcJbzKN4BCUdoPTPfNvEyOenQmWShIKUw/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/EeJSAMoAk9AaZBDONXq5mr1CnahhdFPRnN7Zk7c1R8mNJb1T2PCGUYiatym44rUOVbxzCHRByoeKaqz0Eh6o3ng/640 "")  
  
启动日志验证  
  
![](https://mmbiz.qpic.cn/mmbiz_png/cZBtichFtVOicw0ecmyjicxsyvdEtIDcDaN5EoEiaejwr3qhO8FFapczeI7LbibJlGTxgjQIm6Avy8ISPuEPI6yus7Q/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/3QTIKn9jFuMib8ic1Qldejuib3qXtTqDpDTRq0ciaZRBbE4WGzg7K2FvVhaIJ4HM6gs06qqYzoiaz22FEF38R0Ozr8A/640 "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/3AJf6zpEXO8e3OMs0hVse7E8dySmiaRe5p5vExYnzKGdUV6pMv9VmicM0ib4QIUj08lGtiaQp2dXbSn2coO8mK4yBg/640 "")  
  
  
通过进程监视器 Process Monitor 进行启动时日志记录，为该执行时间线提供了确凿的证据。在完全更新的 Windows 11 25H2 环境下，重启周期中捕获的事件揭示了以下顺序。需要注意的是，虽然进程监视器的启动日志记录可能会引入轻微的延迟，但执行的相对顺序在架构上是确定的，并且保持一致  
  
![](https://mmbiz.qpic.cn/mmbiz_png/bCJqpmbtQZwqgClWF8pia8QCYIVIQ2u4BjhDOia2w1PATMYOLw6nlkWfNcVLh51qAFFL4YHXIku3mD6ib31pOicbs2fQFuDBSQSwyRH4GUAkja8/640?wx_fmt=png&from=appmsg "")  
  
阶段 0，内核初始化 (Start=0 Boot)，内核初始化文件系统和早期启动安全驱动程序  
- 2:45:28.3130411 AM – WdBoot.sys (Defender ELAM 启动驱动程序) 加载  
  
- 2:45:28.3130685 AM – WdFilter.sys (Defender Minifilter) 加载  
  
- 2:45:28.3130700 AM – Ntfs.sys (文件系统) 加载  
  
阶段 1，"黄金窗口期" (Start=1 System)，内核过渡到系统启动，由于其 "Boot Bus Extender" 组，BTR.sys 立即执行  
- 2:45:28.6353170 AM – mlrmqchs.sys (BTR 驱动程序) 加载，驱动程序在此处执行其有效载荷 (文件或注册表修改)  
  
- 2:45:28.6915450 AM – UCPD.sys (用户选择保护驱动程序) 加载，BTR 驱动程序抢占了 UCPD，允许在保护驱动程序加载之前修改受保护的用户选择注册表项  
  
阶段 2，用户模式初始化 (Start=2 自动，start=3 手动)，服务控制管理器 (SCM) 开始启动服务，这发生在相当晚的时候  
- 2:46:02.7308562 AM – MpDefenderCoreService.exe 加载  
  
- 2:46:02.9603201 AM – MsMpEng.exe (Defender 服务) 加载，主 AV 服务在 BTR 驱动程序完成其工作后大约 34 秒启动  
  
- 2:49:23.4912735 AM – WdNisDrv.sys (网络检查驱动程序) 加载，由平台按需触发的网络检测驱动程序在近 4 分钟后加载完成  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ic71wVtGetfHDu1wydMskOKuoXicunjouOlWhIdKVhicUw0VEEUibaahW73bFBktvxRUb7w5A9GS4POh7uqoAm4I8w/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Bd7jlAWL2kJb0ad17GyZNCYMgklIXicN3a4gtzIPibVdu0k3QMYmTqXZcJbzKN4BCUdoPTPfNvEyOenQmWShIKUw/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/EeJSAMoAk9AaZBDONXq5mr1CnahhdFPRnN7Zk7c1R8mNJb1T2PCGUYiatym44rUOVbxzCHRByoeKaqz0Eh6o3ng/640 "")  
  
绕过 EDR/AV  
  
![](https://mmbiz.qpic.cn/mmbiz_png/cZBtichFtVOicw0ecmyjicxsyvdEtIDcDaN5EoEiaejwr3qhO8FFapczeI7LbibJlGTxgjQIm6Avy8ISPuEPI6yus7Q/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/3QTIKn9jFuMib8ic1Qldejuib3qXtTqDpDTRq0ciaZRBbE4WGzg7K2FvVhaIJ4HM6gs06qqYzoiaz22FEF38R0Ozr8A/640 "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/3AJf6zpEXO8e3OMs0hVse7E8dySmiaRe5p5vExYnzKGdUV6pMv9VmicM0ib4QIUj08lGtiaQp2dXbSn2coO8mK4yBg/640 "")  
  
  
通过利用这种加载顺序的差距，BTR.sys 可以有效地绕过 Defender 和 EDR。  
  
尽管 WdFilter 已加载，但由于缺少用户模式服务 (MsMpEng.exe)，它容易受到已签名 Microsoft 内核驱动程序执行的合法操作的影响。测试证实，在启动过程中，受保护的示例二进制文件，例如 WdFilter.sys、MsMpEng.exe 和 WdNisDrv.sys 已被成功删除。由于 MsMpEng.exe 服务二进制文件在服务控制管理器尝试启动它之前就被移除，因此该安全解决方案完全无法启动，从而导致无法进行自我修复、云报告等操作。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/bCJqpmbtQZzrNRYegATTTFwHVPcxttViaSHvfooX8GzicvHa5PRQe4UDibRBWt6kRrQ5YnjppbZicYhQHib7aDO4mMHCbK8dQBPIm5zpkibVzk1QA/640?wx_fmt=png&from=appmsg "")  
  
BTR.sys 注册表篡改保护主要针对用户模式进程。BTR.sys 进程在内核模式下运行，并在运行时成功删除了关键的服务注册表项，例如   
```
HKLM\SYSTEM\CurrentControlSet\Services\WdFilter
```  
  
这会导致 WdFilter.sys 驱动程序在后续重启时无法加载  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/bCJqpmbtQZx8aCiaj0Krge046cr8vw22UDTuNW9O5Jew6jho2ZWDlNpSSjkQan5riaIMrpmQz1vJSDiamrxB7ema0AIDSpXq82pibrmcovicc6G0/640?wx_fmt=png&from=appmsg "")  
  
虽然早期启动反恶意软件 (WdBoot.sys) 会保护初始启动链，但其作用仅限于在早期初始化期间。BTR.sys 在 ELAM 之后的环境 (Start=1) 中执行，这意味着它不会受到与 ELAM 相关的启动驱动程序检查。此外，即使不存在这种架构缺陷，BTR.sys 带有有效的 Microsoft 签名可以通过签名强制验证，但这并不能保证在所有情况下都永久可信。  
  
手动设置 BTR.sys 驱动程序在下次启动时执行，可以有效绕过主动保护，因为它是在内核处于活动状态但安全套件的智能功能处于休眠状态的这段时间内运行的。此外，测试表明，在运行时也能成功绕过篡改保护。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ic71wVtGetfHDu1wydMskOKuoXicunjouOlWhIdKVhicUw0VEEUibaahW73bFBktvxRUb7w5A9GS4POh7uqoAm4I8w/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Bd7jlAWL2kJb0ad17GyZNCYMgklIXicN3a4gtzIPibVdu0k3QMYmTqXZcJbzKN4BCUdoPTPfNvEyOenQmWShIKUw/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/EeJSAMoAk9AaZBDONXq5mr1CnahhdFPRnN7Zk7c1R8mNJb1T2PCGUYiatym44rUOVbxzCHRByoeKaqz0Eh6o3ng/640 "")  
  
如何检测  
  
![](https://mmbiz.qpic.cn/mmbiz_png/cZBtichFtVOicw0ecmyjicxsyvdEtIDcDaN5EoEiaejwr3qhO8FFapczeI7LbibJlGTxgjQIm6Avy8ISPuEPI6yus7Q/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/3QTIKn9jFuMib8ic1Qldejuib3qXtTqDpDTRq0ciaZRBbE4WGzg7K2FvVhaIJ4HM6gs06qqYzoiaz22FEF38R0Ozr8A/640 "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/3AJf6zpEXO8e3OMs0hVse7E8dySmiaRe5p5vExYnzKGdUV6pMv9VmicM0ib4QIUj08lGtiaQp2dXbSn2coO8mK4yBg/640 "")  
  
  
由于 BTR.sys 是经过微软签名的合法组件，因此基于签名的阻止方法无效。BTR.sys 最显著的运行特性是其依赖 ADS 数据流进行配置。Sysmon 能够高精度捕获到这一行为  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/bCJqpmbtQZxO3ZOKHnv5K7fiaamW4ibcTcS9DIhYEg3ldrCe3nGfaTicqtkUYYAU6u56gXxbXy4R460P9J87wiaLFIWqkPHU2nUicfj9FO5ZSB3o/640?wx_fmt=png&from=appmsg "")  
  
当 BTR.sys 执行文件删除 (ActionID 为 1) 操作时，是在 Ring 0 中发生，Sysmon 记录了文件删除，事件 ID 为 23，但执行的删除操作的 Image 值为 System，而不是触发删除操作的用户模式工具，解决办法，关联 System 在 DriverLoad 加载事件 (事件 ID 为 6) 与 BTR.sys 哈希匹配的二进制文件之后立即删除任意文件  
  
![](https://mmbiz.qpic.cn/mmbiz_png/bCJqpmbtQZzCX4XYeTbMp7VCw0UsHg4gwHsSVU0nrUvm0yy0hHShCSFINfFrOdDhEHOSkiafNtsv19M0GicxJbicuZELJ2y9mJ2WkLVxGv9Aa0/640?wx_fmt=png&from=appmsg "")  
  
除了以上检测方法外，还可通过谁释放的 BTR.sys 的文件路径及父进程路径是否位于 Defender 目录内来判断是否被滥用  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ic71wVtGetfHDu1wydMskOKuoXicunjouOlWhIdKVhicUw0VEEUibaahW73bFBktvxRUb7w5A9GS4POh7uqoAm4I8w/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Bd7jlAWL2kJb0ad17GyZNCYMgklIXicN3a4gtzIPibVdu0k3QMYmTqXZcJbzKN4BCUdoPTPfNvEyOenQmWShIKUw/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/EeJSAMoAk9AaZBDONXq5mr1CnahhdFPRnN7Zk7c1R8mNJb1T2PCGUYiatym44rUOVbxzCHRByoeKaqz0Eh6o3ng/640 "")  
  
参 考  
  
![](https://mmbiz.qpic.cn/mmbiz_png/cZBtichFtVOicw0ecmyjicxsyvdEtIDcDaN5EoEiaejwr3qhO8FFapczeI7LbibJlGTxgjQIm6Avy8ISPuEPI6yus7Q/640 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/3QTIKn9jFuMib8ic1Qldejuib3qXtTqDpDTRq0ciaZRBbE4WGzg7K2FvVhaIJ4HM6gs06qqYzoiaz22FEF38R0Ozr8A/640 "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/3AJf6zpEXO8e3OMs0hVse7E8dySmiaRe5p5vExYnzKGdUV6pMv9VmicM0ib4QIUj08lGtiaQp2dXbSn2coO8mK4yBg/640 "")  
  
  
[1]   
https://research.checkpoint.com/2026/btr-reforged-weaponizing-defenders-remediation-driver-as-a-kernel-operation-primitive/  
  
[2]   
https://github.com/Dump-GUY/BTR_CLI  
  

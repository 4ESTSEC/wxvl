#  Citrix NetScaler再曝高危漏洞：SAML解析缺陷可泄露服务器内存  
原创 Red Hunter
                    Red Hunter  黑白之道   2026-07-03 01:27  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/nGzNudUIJ6PEtrnib92D2oK87RibXnQvpZCZAkK3atZrcptiahK7489EsepicU8jY03gBQqJmM9H50cKQRKIY72vFj6hjQLAHDmMSOQic6mQ150A/640?wx_fmt=png "")  
> **导语**  
：Citrix NetScaler又来了。这家企业的应用交付控制器和VPN网关产品线，几乎每隔几个月就要上一次安全头条。7月1日，Citrix官方发布安全公告，一口气修补了六个漏洞，其中最引人关注的是预认证内存越读漏洞CVE-2026-8451，CVSS评分8.8。披露不足24小时，安全研究人员已监测到野外利用迹象。  
  
## 一、漏洞概述：SAML解析器的经典错误  
  
CVE-2026-8451的本质，是一个输入验证不足导致的内存越读（Memory Overread）问题。当NetScaler ADC或NetScaler Gateway配置为SAML身份提供者（IDP）时，攻击者可以通过向/saml/login  
端点发送特制的SAML认证请求，触发内存越读，从而获取本不该暴露的进程内存数据。  
  
Citrix官方对这个漏洞的描述相当简洁："Insufficient input validation leading to memory overread"——输入验证不足导致内存越读。CVSS评分8.8，属于高危级别。  
  
受影响的产品版本：  
- NetScaler ADC和NetScaler Gateway 14.1，早于14.1-72.61  
  
- NetScaler ADC和NetScaler Gateway 13.1，早于13.1-63.18  
  
- NetScaler ADC FIPS 14.1，早于14.1-72.61 FIPS  
  
- NetScaler ADC FIPS和NDcPP 13.1，早于13.1-37.272  
  
## 二、发现过程：顺着CVE-2026-3055摸到的新坑  
  
这个漏洞的发现颇具戏剧性。watchTowr实验室的研究人员表示，他们是今年3月在尝试复现CVE-2026-3055（另一个NetScaler内存越读漏洞，CVSS高达9.3）时，意外发现了这个新问题。  
  
两次漏洞的根因相同——都是NetScaler的XML解析器在处理SAML请求时存在缺陷。具体来说，当解析器遇到被精心构造的XML属性时，特别是包含换行符或未正确终止的属性值时，解析器会持续读取超出预期边界的数据，并将这些数据包含在返回给客户端的认证Cookie中。  
  
watchTowr的研究人员在分析日志时发现了这一行为：  
```
AuthnReq start tag parsed, id=<>, acs=<▒^M▒ﾭ▒="2.0" id="11"AssertionConsumerServiceURL="22"ﾭ▒mple.com/demo1/index.php</saml:Issuer>,
```  
  
注意看，acs  
字段的值中混入了大量本不该出现的数据——这些数据来自进程的内存空间。  
  
不过，相比CVE-2026-3055动辄KB级别的数据泄露，CVE-2026-8451的泄露量要小得多。研究人员发现，当遇到NULL字符或>  
等控制字符时，越读就会停止。在实际测试中，他们通过变化请求长度，每次能稳定挤出几个字节。  
  
但正如watchTowr在报告中强调的："需要关注的是更大的图景——Citrix NetScaler中的内存管理问题显然已经是系统性的。"  
## 三、漏洞原理：XML解析器的边界陷阱  
  
要理解这个漏洞的原理，需要先了解NetScaler如何解析SAML认证请求。  
  
SAML（Security Assertion Markup Language）认证的标准流程是这样的：用户发起认证请求，应用构造一个base64编码的XML文档，发送到IDP的saml/login  
端点。XML文档中包含AuthnRequest，描述了身份提供者需要知道的一切信息——发行者、目标地址、时间戳等。  
  
问题出在这个XML解析器的属性解析逻辑上。研究人员逆向分析了相关代码，发现了一段"令人愉悦"的代码片段：  
```
cursor = <some string input>whitespaceCharList = 0x100002600;// Skip leading whitespacefor ( lookahead = (v32 + 28); ; lookahead++ ){    ch = *cursor;    if ( ch > '=' )        break;    if ( !_bittest64(&whitespaceCharList, ch) )    {        if ( ch == '=' )        {            while ( 1 )            {                ch = *lookahead;                if ( ch > 0x20 || !_bittest64(&whitespaceCharList, ch) )                    break;                ++lookahead;            }            cursor = lookahead;        }        break;    }    ++cursor;}
```  
  
这段代码负责定位XML属性的值。但问题在于：它没有明显的边界检查。如果属性值从未被正确终止，解析器完全可能读穿输入缓冲区的边界。  
  
更诡异的是解析器的终止逻辑差异。对于带引号的值，解析器会正确地在引号处终止；但对于无引号的值，解析器只在遇到NULL字节、>  
或匹配的引号时才停止。这种不一致性在某些畸形输入下会导致越界读取。  
## 四、野外利用：24小时内已有攻击者入场  
  
漏洞在7月1日公开披露后，安全公司Lupovis迅速监测到了野外利用。  
  
Lupovis在X平台上发布的一系列帖子显示，一个来自法兰克福（IP：146.70.139.154）的攻击者在6月30日——也就是官方公告前两天——就开始针对其蜜罐传感器进行攻击。攻击者在一个五小时的窗口内向传感器发送漏洞利用代码，成功时返回200 OK状态，跳过返回404的传感器。  
  
Lupovis首席执行官Xavier Bellekens表示："特别有趣的是我们看到的攻击者目标选择逻辑。攻击者首先验证目标，只在获得预期响应时才交付完整payload。这表明这是主动利用行为，而非通用扫描。"  
  
Payload与watchTowr Labs发布的漏洞利用代码匹配，攻击者正在尝试复现那个畸形的SAML请求，以获取成功的内存越读。  
  
这个速度再次印证了一个老生常谈的事实：漏洞披露后的前24-48小时是最危险的窗口。  
## 五、检测与缓解：ProjectDiscovery已发布工具  
  
好消息是，ProjectDiscovery已经发布了针对CVE-2026-8451的检测工具，可以在GitHub上找到：github.com/projectdiscovery/netscaler-cve-2026-8451  
  
这个工具可以帮助安全团队快速检测是否存在脆弱的NetScaler实例。  
  
在补丁方面，Citrix已经发布了修复版本：  
<table><thead><tr><th style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;font-weight: bold;background-color: rgb(255, 249, 249);color: rgb(239, 112, 96);"><section><span leaf="">产品</span></section></th><th style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;font-weight: bold;background-color: rgb(255, 249, 249);color: rgb(239, 112, 96);"><section><span leaf="">修复版本</span></section></th></tr></thead><tbody><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">NetScaler ADC/Gateway 14.1</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">14.1-72.61 及以后</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">NetScaler ADC/Gateway 13.1</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">13.1-63.18 及以后</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">NetScaler ADC 14.1 FIPS</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">14.1-72.61 FIPS 及以后</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">NetScaler ADC 13.1 FIPS/NDcPP</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">13.1-37.272 及以后</span></section></td></tr></tbody></table>  
对于无法立即打补丁的组织，以下缓解措施可以降低风险：  
1. **确认SAML IDP功能是否必要**  
：如果NetScaler未配置为SAML身份提供者，则不受此漏洞影响  
  
1. **限制互联网暴露**  
：通过防火墙、VPN或IP白名单限制对NetScaler认证和管理接口的访问  
  
1. **监控SAML登录请求**  
：关注畸形SAML请求、认证日志异常和nsppe进程崩溃  
  
1. **强制网络钓鱼抵抗MFA**  
：对NetScaler管理员和管理接口启用抗钓鱼多因素认证  
  
## 六、结语：Citrix NetScaler，内存安全的反面教材  
  
CVE-2026-8451是Citrix NetScaler系列内存泄露漏洞中的最新一员。从2023年的CitrixBleed（CVE-2023-4966）到今年的CVE-2026-3055，再到今天的CVE-2026-8451，这个产品线仿佛陷入了一个"内存管理脆弱"的诅咒。  
  
watchTowr的研究员Aliz Hammond说得直白："感觉我们一直在玩一个高敏感度手枪的游戏，伤及无辜。而这把枪，藏在很多人认为高度可靠的安全控制设备里。"  
  
对于安全团队而言，这意味着：一，继续盯着Citrix的漏洞公告，这个产品短期内不太可能告别漏洞舞台；二，对于互联网暴露的NetScaler实例，拥抱零信任架构——假设任何边界设备都可能被攻破，用纵深防御来对冲风险。  
  
**版权声明**  
：本文由华盟网原创发布，保留所有权利。配图由华盟网授权使用。  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/nGzNudUIJ6Puc8HNiaVFMibMWyXuRCjam9iat0x1rCpsp1iatD7HveaKn7X0FMG8AlYEYZQbWQjAzjbYJjgdDQYTxLsn3W66vehH9V8LV2z9XnE/640?wx_fmt=jpeg "")  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650618755&idx=1&sn=48e0a85464fb20c73b6f338928a8f850&scene=21#wechat_redirect)  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650618776&idx=2&sn=a8fc65fd2a71822830022fc52d967bcd&scene=21#wechat_redirect)  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650618496&idx=1&sn=96ecdff99136258a4bf2cb156542311e&scene=21#wechat_redirect)  
> 👇 点击**阅读原文**  
，访问我的网站  
  
  

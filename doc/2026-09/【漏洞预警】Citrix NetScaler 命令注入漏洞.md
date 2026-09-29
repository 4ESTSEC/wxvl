#  【漏洞预警】Citrix NetScaler 命令注入漏洞  
 数字人才创研院   2026-09-29 13:15  
  
点击上方  
蓝字  
关注我们  
  
 漏洞预警  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/5d6O0HSxyXkoJlmfe0w3ScXV6VhNKNLP8JvkZzicOziaviaSxjKtia1pzPiaT7RYjdTUm8TYGhpY365mIXEzVGucoSA/640?wx_fmt=png&from=appmsg "")  
  
01  
  
漏洞基本概述  
  
Vulnerability Overview  
  
![](https://mmbiz.qpic.cn/mmbiz_png/4kcMpxcoP324FB41iaOFjLovYzq48jiayABVZozfrfjNU4hTK3iaTAS71icN9rSUtVqAavoMiagZ3BVyyJlLgxTCiarQ/640?wx_fmt=png "")  
  
01  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/qiaou2SWwgPxZsPuGUBqvfztY349JahH5gTicMQHkTaziasQOt0sYZP0QicweflsMyu4g1lUGPOrIpDlBwCYibwNjYw/640?wx_fmt=png "")  
  
    
Citrix NetScaler ADC 与 Citrix NetScaler Gateway 是面向企业网络的应用交付控制器和远程接入网关，主要用于负载均衡、SSL 卸载、身份认证和 VPN 远程访问，广泛部署于大型政企网络边缘，作为内部应用与远程办公的入口  
。  
  
**【风险等级】**  
**极 危**  
  
**【CVE编号】**  
**CVE-2026-88771**  
  
  2026年9月27日，禾盾安全应急响应中心监测到该漏洞  
，  
经分析，  
攻击者通过发送特制的恶意HTTP/HTTPS请求至目标设备，利用输入验证缺陷绕过身份验证，直接在底层操作系统执行任意命令。  
建议受影响的用户尽快修复，  
与此同时，请做好资产自查以及预防工作，以免遭受黑客攻击。  
  
02  
  
漏洞影响范围  
  
Vulnerability Impact  
  
![](https://mmbiz.qpic.cn/mmbiz_png/4kcMpxcoP324FB41iaOFjLovYzq48jiayABVZozfrfjNU4hTK3iaTAS71icN9rSUtVqAavoMiagZ3BVyyJlLgxTCiarQ/640?wx_fmt=png "")  
  
01  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/qiaou2SWwgPxZsPuGUBqvfztY349JahH5gTicMQHkTaziasQOt0sYZP0QicweflsMyu4g1lUGPOrIpDlBwCYibwNjYw/640?wx_fmt=png "")  
  
      
Citrix NetScaler ADC：版本低于14.1-73.37、13.1-64.23及对应FIPS版本  
  
      
Citrix NetScaler Gateway：版本低于14.1-73.37、13.1-64.23及FIPS/NDcPP版本  
  
03  
  
漏洞修复方案  
  
Vulnerability Fixes  
  
![](https://mmbiz.qpic.cn/mmbiz_png/4kcMpxcoP324FB41iaOFjLovYzq48jiayABVZozfrfjNU4hTK3iaTAS71icN9rSUtVqAavoMiagZ3BVyyJlLgxTCiarQ/640?wx_fmt=png "")  
  
01  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/qiaou2SWwgPxZsPuGUBqvfztY349JahH5gTicMQHkTaziasQOt0sYZP0QicweflsMyu4g1lUGPOrIpDlBwCYibwNjYw/640?wx_fmt=png "")  
  
  目前官方暂已发布修复版本，建议  
更新系统补丁，限制网络访问，实施输入验证和异常检测等  
。  
  
    
**注：其它建议**  
  
      
1.  
紧  
急升级：根据CISA BOD 26-04指导原则，将受影响设备升级至14.1-73.37、13.1-64.23或更高版本（含FIPS版本）  
。  
  
    2.  
临时缓解  
：若无法立即升级，通过防火墙限制对管理接口及服务端口的公网访问，仅允许可信IP通信  
。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/0wuPBaVgoObicDAtjVB2COXOL4bRbbfIXUNdOJ65HQPtYy6dq5rkibVDsFk5AxyCZID4yswQdpSLmp8JPmeMjicUjm6sc2FicVkVoNWvlYWQL8g/640?wx_fmt=png&from=appmsg "")  
  
‍  
‍  
‍  
‍  
‍  
‍  
‍**下载链接：**  
```
https://support.citrix.com/support-home/kbsearch/article?articleNumber=CTX697096
```  
  
  
04  
  
漏洞参考链接  
  
Vulnerability Fixes Link  
  
![](https://mmbiz.qpic.cn/mmbiz_png/4kcMpxcoP324FB41iaOFjLovYzq48jiayABVZozfrfjNU4hTK3iaTAS71icN9rSUtVqAavoMiagZ3BVyyJlLgxTCiarQ/640?wx_fmt=png "")  
  
01  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/qiaou2SWwgPxZsPuGUBqvfztY349JahH5gTicMQHkTaziasQOt0sYZP0QicweflsMyu4g1lUGPOrIpDlBwCYibwNjYw/640?wx_fmt=png "")  
  
```
https://www.cisa.gov/known-exploited-vulnerabilities-catalog
https://support.citrix.com/support-home/kbsearch/article?articleNumber=CTX697096
https://www.cisa.gov/known-exploited-vulnerabilities-catalog?field_cve=CVE-2026-88771
```  
  
  
05  
  
漏洞时间滚轴  
  
Vulnerability Time  
  
[#发现时间]()  
#  
  
2026年09月27日  
  
[#验证时间]()  
#  
  
2026年09月28日  
  
[#通告时间]()  
#  
  
2026年09月29日  
  
HD  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibwov0bgAkBeVIILoZgYNTn6rrKD6sSkPVG8nZcwn2QWHibG6AsEyuy9n3XGMuoZIyGCEmvv1ZRnaibXbYkrIP4Hg/640?wx_fmt=png "")  
  
禾盾安全应急响应中心  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/f1Ap1TId9Sp8P5te00xOHDe8oCZN6GRlKk24gBLXpsI9gV0pyabsYgkrAhygibQL1VzhABHrUstCyoMHysrBY5g/640?wx_fmt=png "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/XCey8HnLC70G2J8ft1vNlFXiaURP3NcPfiaB4KASRaibFGRX73tiaiaKFFxVbDv0UiaGpvxGxAvdr147CIwPrVkw8Qvw/640?wx_fmt=png "")  
  
    
**以技术为驱动，以安全专家为核心，以诚信为本、以专业为先、以坚持为恒，围绕漏洞生态体系打造集漏洞监测、漏洞收集、漏洞挖掘、漏洞分析、漏洞管理、专家响应、漏洞预警、安全服务定制化于一体的漏洞安全一站式服务，帮助客户防患于未然，在降低资产风险的同时，大幅提升客户对漏洞感知、预警、分析等响应能力，为国家、政企客户、用户抢占风险预警处置先机，提升网络安全主动防护能力。**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ry30yBl2sr8jsWibpdia0ku8mG2eibhcjnIADPzCMXXlsdUXs3KzibJ8Q6q6VCw33WRPxx0xNpI8vyepKOONH1Ij2A/640?wx_fmt=png "")  
  
  
HD  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibwov0bgAkBeVIILoZgYNTn6rrKD6sSkPVG8nZcwn2QWHibG6AsEyuy9n3XGMuoZIyGCEmvv1ZRnaibXbYkrIP4Hg/640?wx_fmt=png "")  
  
获取更多最新情报  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/f1Ap1TId9Sp8P5te00xOHDe8oCZN6GRlKk24gBLXpsI9gV0pyabsYgkrAhygibQL1VzhABHrUstCyoMHysrBY5g/640?wx_fmt=png "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/XCey8HnLC70G2J8ft1vNlFXiaURP3NcPfiaB4KASRaibFGRX73tiaiaKFFxVbDv0UiaGpvxGxAvdr147CIwPrVkw8Qvw/640?wx_fmt=png "")  
  
**建议您订阅「禾盾安全-漏洞情报」服务，及时获取更多漏洞情报详情以及处置建议，让您的单位真正远离漏洞威胁。**  
  
**电话：177-128-77993**  
  
**邮箱：src@hedun.com.cn**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ry30yBl2sr8jsWibpdia0ku8mG2eibhcjnIADPzCMXXlsdUXs3KzibJ8Q6q6VCw33WRPxx0xNpI8vyepKOONH1Ij2A/640?wx_fmt=png "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/5d6O0HSxyXkoJlmfe0w3ScXV6VhNKNLPmYBiaYmk4QgObS8Iicl7vJ6Q685oE8vTNLHFfHefk58InmILibRUJDzGw/640?wx_fmt=jpeg&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/SkPgzibRbCL5X8OKYMB024f1gWFpTuBibMDe5YTnPU2uAuEBUoiaXD3OXnTBfPfGyew6VpxO5dBgFoic9TaRhHouFw/640?wx_fmt=jpeg&from=appmsg "")  
  

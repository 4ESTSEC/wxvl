#  【安全圈】攻击者利用 Issabel Framework 漏洞实现未经身份验证的 OS 命令执行  
 安全圈   2026-09-19 11:00  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/aBHpjnrGylgOvEXHviaXu1fO2nLov9bZ055v7s8F6w1DD1I0bx2h3zaOx0Mibd5CngBwwj2nTeEbupw7xpBsx27Q/640?wx_fmt=other&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1 "")  
  
  
**关键词**  
  
  
  
漏洞  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/sbq02iadgfyHddVbLDb4ewrHVough8wQGAZsovrnU2rwdqs8nZNlwgOKXl7QEVDFXFibxX87yXzRicv8qZAnaLfIzBh7MiaOEpIk5UnAFjAaialA/640?wx_fmt=png&from=appmsg "")  
  
开源统一通信 PBX 软件的 Web 框架 Issabel Framework 中的一个严重安全漏洞已遭到积极利用。  
  
  
该漏洞为 CVE-2026-89026（CVSS v3.1 评分：9.8 / CVSS v4.0 评分：9.3），可允许未经身份验证的远程攻击者利用一个硬编码的 JSON Web Token (JWT) 签名密钥执行任意操作系统 (OS) 命令。  
  
  
"Issabel Framework 在 pbxapi index.php 文件中包含一个硬编码的 HS256 JWT 签名密钥，该密钥在所有安装实例中完全相同，允许未经身份验证的远程攻击者伪造有效的 bearer 令牌，"VulnCheck 在一份警报中表示。  
  
  
"攻击者可以使用伪造的令牌调用管理器 '/ pbxapi / manager / originate' 端点并附带 System 应用参数，导致 Asterisk 以 Asterisk 用户身份执行任意 OS 命令。"  
  
  
该漏洞的补丁于 2026 年 8 月 1 日推送，通过将硬编码的 JWT 密钥（"da893kasdfam43k29akdkfaFFlsdfhj23rasdf"）替换为存储在 "/ etc / issabel.conf" 文件中的 JWT 密钥来修复该缺陷。  
  
  
据这家网络安全公司称，Shadowserver Foundation 于 2026 年 9 月 9 日首次观察到对 CVE-2026-89026 的利用。不过，目前尚无关于该漏洞在现实攻击中如何被滥用、攻击者是谁以及此类行动规模等方面的细节。  
  
  
建议 Issabel Framework 用户应用最新修复程序以获得最佳保护。  
  
  
   END    
  
  
阅读推荐  
  
  
  
  
[【安全圈】程序员炸锅！智谱 ZCode 被曝静默打包工作区与 Git 历史直传云端](https://mp.weixin.qq.com/s?__biz=MzIzMzE4NDU1OQ==&mid=2652079007&idx=1&sn=e3ef54109cd4fbca8f3ac931c56cc95c&scene=21#wechat_redirect)  
  
  
  
[【安全圈】AI 控机还杀不死！新型安卓木马曝光：卸载仍常驻 Shell](https://mp.weixin.qq.com/s?__biz=MzIzMzE4NDU1OQ==&mid=2652079007&idx=2&sn=1de45fa1e0c91b7f0ce3601436604bef&scene=21#wechat_redirect)  
  
  
  
[【安全圈】AI 智能体打穿沙箱！Docker 曝 9.4 分逃逸漏洞：穿透虚拟机读写宿主](https://mp.weixin.qq.com/s?__biz=MzIzMzE4NDU1OQ==&mid=2652079007&idx=3&sn=f573fda0930936118a001a7d0985e466&scene=21#wechat_redirect)  
  
  
  
[【安全圈】防火墙中枢被击穿！Check Point 曝 9.8 分漏洞：超长用户名直接拿 Root](https://mp.weixin.qq.com/s?__biz=MzIzMzE4NDU1OQ==&mid=2652079007&idx=4&sn=ee03a1d00069903ffa5e2b89cb3178f0&scene=21#wechat_redirect)  
  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/aBHpjnrGylgeVsVlL5y1RPJfUdozNyCEft6M27yliapIdNjlcdMaZ4UR4XxnQprGlCg8NH2Hz5Oib5aPIOiaqUicDQ/640?wx_fmt=gif "")  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/aBHpjnrGylgeVsVlL5y1RPJfUdozNyCEDQIyPYpjfp0XDaaKjeaU6YdFae1iagIvFmFb4djeiahnUy2jBnxkMbaw/640?wx_fmt=png "")  
  
**安全圈**  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/aBHpjnrGylgeVsVlL5y1RPJfUdozNyCEft6M27yliapIdNjlcdMaZ4UR4XxnQprGlCg8NH2Hz5Oib5aPIOiaqUicDQ/640?wx_fmt=gif "")  
  
  
←扫码关注我们  
  
**网罗圈内热点 专注网络安全**  
  
**实时资讯一手掌握！**  
  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/aBHpjnrGylgeVsVlL5y1RPJfUdozNyCE3vpzhuku5s1qibibQjHnY68iciaIGB4zYw1Zbl05GQ3H4hadeLdBpQ9wEA/640?wx_fmt=gif "")  
  
**好看你就分享 有用就点个赞**  
  
**支持「****安全圈」就点个三连吧！**  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/aBHpjnrGylgeVsVlL5y1RPJfUdozNyCE3vpzhuku5s1qibibQjHnY68iciaIGB4zYw1Zbl05GQ3H4hadeLdBpQ9wEA/640?wx_fmt=gif "")  
  
  

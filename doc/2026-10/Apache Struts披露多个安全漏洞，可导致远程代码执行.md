#  Apache Struts披露多个安全漏洞，可导致远程代码执行  
 FreeBuf   2026-10-08 10:00  
  
![FreeBuf](https://mmbiz.qpic.cn/sz_mmbiz_gif/icBE3OpK1IX050c3XFiafUZeK5rjXnGJLnAAjib4voghf9QJ7TgCamYibxBicMdZA1nYnXORYEBYbeMJ2KsFPvB64VF6t6Ym18Vkhl4TKhOe5GuM/640?wx_fmt=gif "")  
  
  
![Apache Struts漏洞披露相关截图](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX0CpShz36ZELuSY8sx2DwpHibFsF6icHpgLwibCSo4l9UHz24FSdJyibXk3RhGUOXeTGc6cOleKU8vxYHBsdoAJbJTvjqbmaWxou0I/640?wx_fmt=png "")  
  
  
Part  
01  
  
官方披露4个Struts漏洞  
  
  
Apache Struts 官方安全公告共披露4个安全漏洞，受影响应用可能面临远程代码执行、拒绝服务、非预期数据泄露三类风险。  
  
  
官方给出的修复方案明确：使用7.x分支的用户需升级至Struts 7.4.0及以上版本，使用6.x长期维护分支的用户需升级至Struts 6.12.0及以上版本。这些漏洞分布在框架的不同组件中，实际受影响范围取决于应用的具体配置和启用的功能。  
  
  
本次披露的漏洞中，3个为中危评级，REST插件存在的请求体无大小限制漏洞为重要评级。目前暂无证据表明这些漏洞已遭到在野主动利用。  
  
  
Part  
02  
  
四个漏洞影响条件各不相同  
  
  
第一个漏洞编号为CVE-2026-104711，存在于遗留版RESTful动作映射器中，属于OGNL注入漏洞。如果应用启用了该映射器，攻击者发送构造的特殊请求就可以注入恶意表达式，最终实现远程代码执行。  
  
  
该漏洞的影响版本包括Struts 2.0.0-2.3.37、2.5.0-2.5.33、6.0.0-6.11.0；Struts 7.0.0-7.3.0仅在关闭OGNL白名单机制时会受影响。使用默认映射器、restful2映射器或Struts REST插件的应用不受该漏洞影响，Struts 7默认配置下自带防护能力。该漏洞由LeaveSong上报。  
  
  
第二个漏洞编号为CVE-2026-104712，攻击者可以用极小的请求触发体量巨大的响应。当请求参数赋值给java.math.BigDecimal类型属性、且后续通过Struts标签库渲染时，漏洞就会被触发。  
  
  
未授权攻击者可以持续发送低流量请求，逐步消耗服务器CPU资源和出口网络带宽。当资源被占满时，服务就会出现拒绝访问的情况。如果应用使用其他数值类型，或通过JSON、REST插件生成响应，则不受该漏洞影响。  
  
  
该漏洞的影响版本包括Struts 2.5.14-2.5.33、6.0.0-6.11.0、7.0.0-7.3.0，由0xCc.Zhang上报发现。  
  
  
针对该漏洞的临时缓解方案为使用自定义BigDecimal转换器，在渲染前限制数值精度。用户可以通过classpath根目录下的struts-conversion.properties文件，注册全局生效的转换器。  
  
  
第三个漏洞编号为CVE-2026-104713，是本次披露中唯一评级为重要的漏洞，影响通过可选REST插件接收请求体的应用。存在漏洞的实现逻辑不会限制请求体大小，会直接将全部内容读入内存。该REST插件负责处理传入的XML、JSON等格式内容。  
  
  
攻击者仅需发送一个超大请求，就可以耗尽服务器堆内存。内存耗尽后，服务会直接陷入不可用状态。该漏洞影响版本覆盖Struts 2.1.8-2.3.37、2.5.0-2.5.33、6.0.0-6.11.0、7.0.0-7.3.0，由n0mi1k上报。  
  
  
修复版本默认设置了2097152字符的请求体长度上限。暂时无法升级的用户可以在反向代理或Servlet容器层面，强制限制请求体大小。  
  
  
第四个漏洞编号为CVE-2026-104714，出在处理日期、时间参数的共享本地化消息格式化器上。并发请求会产生干扰，导致某一用户的返回内容中出现其他用户的数据，或触发页面渲染错误。  
  
  
该漏洞同样由n0mi1k上报，影响6.11.0、7.3.0及以下的对应分支版本。漏洞触发不需要恶意输入，普通的并发流量就可以引发问题。  
  
  
Part  
03  
  
管理员需尽快升级  
  
  
使用受影响版本的管理员应尽快升级，同时排查四类配置：动作映射器设置、小数渲染逻辑、REST接口配置、本地化消息逻辑。  
  
  
针对格式化器并发问题，暂时无法升级的用户可以在消息插值前提前完成日期格式化，将其作为临时缓解措施。  
  
  
参考来源：  
  
Critical Apache Struts Vulnerabilities Enables Remote Code Execution Attacks  
  
https://cybersecuritynews.com/apache-struts-vulnerabilities/  
  
###   
  
### 推荐阅读  
  
  
[](https://mp.weixin.qq.com/s?__biz=MjM5NjA0NjgyMA==&mid=2651347479&idx=1&sn=1cf9c858a30d0b7af06d196002bd2913&scene=21#wechat_redirect)  
  
  
###   
  
### 电报讨论  
  
  
[]()  
  
  
  
![扫码加入AI安全交流群](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX0Py7ibxdLKXia1pMziaic5vIE9XPXG9OGaeJDa07iaG10eicuzhW59nwpF5msHiaYZvfMqCNkx2aFDiaMzm3oAf4rTaHXU5UAI1mUYgts/640?wx_fmt=png "")  
  
  
  
![下载FreeBuf知识大陆APP](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX0TIGzII2Hcmtzu7AJeZFicnqd1mXojVoawje2uLxYqwJbVgzJpmSXzVhrpOsLurRZ2lVa4vfgLBqg7uJKbrKg5F18VzZxVPicZU/640?wx_fmt=png "")  
  
  
  
  

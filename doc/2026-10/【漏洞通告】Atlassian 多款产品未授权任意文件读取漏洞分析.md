#  【漏洞通告】Atlassian 多款产品未授权任意文件读取漏洞分析  
FL_Clover
                    FL_Clover  网络安全007   2026-10-08 09:00  
  
一、漏洞事件概述  
  
    2026 年 10 月初，Atlassian 集中披露并修复了一个编号为 CVE-2026-21589 的高危安全缺陷，在 CVSS 4.0 体系下被评定为 9.3 分（严重/Critical），Atlassian 官方将症状严重级别定为最高的 Severity 1。该缺陷的本质是一处未授权的路径遍历（Path Traversal，CWE-22）导致任意文件读取问题，根因其位于 Atlassian 旗下众多产品共用的 Web 静态资源组件 atlassian-plugins-webresource 中，因此呈现出"一处失守、全线中招"的特点，波及 Confluence、Jira、Bitbucket、Bamboo、Crowd、Crucible、Fisheye 等 8 款私有化部署产品，受影响资产量级达到十万级。  
  
    攻击者  
无需任何账号或登录态，只要能够访问到目标实例的 HTTP 服务，即可构造畸形请求绕过路径校验，读取 Web 应用根目录（含受保护的 WEB-INF 目录）内已知路径的文件。一旦读取到配置文件，便可能拿到数据库连接串、密码、API Token、私钥等高价值凭据，进而发展为权限提升、账号接管或内网横向移动。在统一部署了 Crowd 身份认证的环境中，危害还会进一步放大（详见技术要点）。目前该漏洞的 PoC 与技术细节均已公开，但尚未观测到大规模在野利用，处于"窗口期紧、需尽快处置"的状态。  
  
  
二、漏洞影响范围  
  
    仅影响自托管的 Data Center / Server 版本，Atlassian Cloud 已由官方侧自动修补，用户无需自行处理。各产品需要升级到下列"安全版本"才不受影响（低于安全版本者均受影响，且已停止维护的历史版本同样存在风险，建议直接升至最新受支持版本）：  
<table><thead><tr><th><section><span leaf="">产品</span></section></th><th><section><span leaf="">受影响的最低版本起点</span></section></th><th><section><span leaf="">已修复的安全版本</span></section></th></tr></thead><tbody><tr><td><section><span leaf="">Bitbucket Data Center</span></section></td><td><section><span leaf="">4.6.0 起；10.0.0 起；10.3.0 起</span></section></td><td><section><span leaf="">9.4.26 / 10.2.8 / 10.5.1</span></section></td></tr><tr><td><section><span leaf="">Confluence Data Center</span></section></td><td><section><span leaf="">5.10.0 起；10.0.0 起</span></section></td><td><section><span leaf="">9.2.26 / 10.2.19</span></section></td></tr><tr><td><section><span leaf="">Crowd Data Center</span></section></td><td><section><span leaf="">2.11.0 起；7.0.0 起；7.1.0 起；7.2.0 起</span></section></td><td><section><span leaf="">6.3.7 / 7.0.3 / 7.1.7 / 7.2.4</span></section></td></tr><tr><td><section><span leaf="">Jira Software Data Center</span></section></td><td><section><span leaf="">7.1.0 起；10.0.0 起；11.0.0 起</span></section></td><td><section><span leaf="">9.12.40 / 10.3.26 / 11.3.12</span></section></td></tr><tr><td><section><span leaf="">Jira Service Management Data Center</span></section></td><td><section><span leaf="">3.1.0 起；10.0.0 起；11.0.0 起</span></section></td><td><section><span leaf="">5.12.40 / 10.3.26 / 11.3.12</span></section></td></tr><tr><td><section><span leaf="">Bamboo Data Center</span></section></td><td><section><span leaf="">7.0.1 起；12.0.0 起</span></section></td><td><section><span leaf="">10.2.24 / 12.1.12</span></section></td></tr><tr><td><section><span leaf="">Crucible</span></section></td><td><section><span leaf="">全版本受影响</span></section></td><td><section><span leaf="">4.9.15</span></section></td></tr><tr><td><section><span leaf="">Fisheye</span></section></td><td><section><span leaf="">全版本受影响</span></section></td><td><section><span leaf="">4.9.15</span></section></td></tr></tbody></table>  
  
利用前提：  
  
① 目标为可被网络访问的受影响版本实例；  
  
② 攻击者需预先知道目标文件的准确文件名与相对路径（该漏洞无法枚举目录列表，不能像列目录一样浏览）；  
  
③ 请求路径经多层解码后能形成有效的路径遍历序列并被路由器还原成合法资源路径。  
  
  
三、漏洞技术要点分析  
  
    核心问题在于"静态资源请求的路由处理"与"底层文件系统的真实路径解析"之间出现了校验脱节，可拆解为三个关键环节：  
1. 双重 URL 解码缺陷。Web 资源组件在处理请求时对 URL 进行了不止一次的解码。这导致攻击者用双重编码（例如 %252e%252e%252f）构造的字符串，能够通过前置的净化检查——因为检查时看到的还是"未解码"的安全形态，而真正取文件时却被还原成 ../ 这样的遍历序列，安全过滤器被直接穿透。  
  
1. ":: 反转义为 /"的处理逻辑。该组件把 :: 当作路径分隔符 / 的一种内部转义写法（用于 webresource 命名空间）。缺陷在于路由器会把请求里的 ..::..:: 这类片段反转义还原成 ../../，从而借助 :: 这种非常规分隔符绕过了 Tomcat 对 URI 的规范化检查——Tomcat 看到的是不含 ../ 的"合法"路径，放行后由 Atlassian 组件自行还原出真正的遍历路径。  
  
1. 越界文件直读。一旦遍历序列被还原，后端文件服务便会跳出静态资源的白名单目录，将 Web 根目录乃至 WEB-INF 下指定文件的完整内容原样回显给未认证客户端。由于组件本身没有目录列表能力，攻击者只能"点名读取"已知文件，但像 WEB-INF/web.xml、数据库配置、crowd.cfg.xml 这类文件名是公开可知的，足以构成稳定利用。  
  
攻击链放大点：在部署 Crowd 统一认证的环境中，攻击者可先读取到 Crowd 的明文凭据，再调用 Crowd 的 REST API 直接创建一个管理员账号，从而把"匿名读文件"升级为"完全接管平台"，形成从信息泄露到 RCE 级别的完整利用链，这也是该漏洞评分被推到 9.3 的重要原因。  
  
  
四、漏洞 EXP和自校验脚本（利用方式说明）  
  
    PoC 与 EXP 均已在 GitHub（watchTowr Labs）及多家安全厂商处公开。利用方式极简，本质是向存在 webresource 路由的下载接口发送一条携带遍历序列的 GET 请求，无需登录。构造思路如下：  
- 明文形态：  
  
    在正常的静态资源路径后拼接多段 ..::（被反转义为 ../），指向目标文件，例如：  
```
/s/xxx/.../jira.webresources:color-picker-popup/images/..::..::..::..::..::..::WEB-INF::web.xml
→ 还原后等价于 ../../../../../WEB-INF/web.xml
```  
  
- 编码形态（绕过更表层过滤）：  
  
    将 :: 用 %3a%3a、点号用 %2e、斜杠用 %252f 等做双重编码后发送：  
```
.../images/..%3a%3a..%3a%3a..%3a%3a..%3a%3a..%3a%3a..%3a%3aWEB-INF/web.xml
```  
  
  
公开 PoC（Python 脚本）会自动发起"明文 + 编码"两次探测，只要任一请求返回 HTTP 200 并回显出文件内容（如 <display-name>Atlassian JIRA Web Application</display-name>），即判定目标存在漏洞。实测在未打补丁的 Jira 实例上可成功读到 WEB-INF/web.xml 全文，验证了未授权任意文件读取。  
  
  
建议仅用于自有资产的授权自查，切勿用于未授权目标。  
  
自查脚本地址如下：  
```
作者：watchyowrlabs
https://github.com/watchtowrlabs/watchTowr-vs-Atlassian-CVE-2026-21589/blob/main/watchTowr-vs-Atlassian-CVE-2026-21589.py
```  
  
  
五、修复建议  
  
5.1 首要措施：立即升级至安全版本（强烈推荐）  
  
    最根本、最有效的修复手段。请根据实际使用的产品，尽快升级至对应的安全版本：  
<table><thead><tr><th><section><span leaf="">产品</span></section></th><th><section><span leaf="">升级目标版本</span></section></th></tr></thead><tbody><tr><td><section><span leaf="">Jira Software Data Center</span></section></td><td><section><span leaf="">≥ 9.12.40 / ≥ 10.3.26 / ≥ 11.3.12</span></section></td></tr><tr><td><section><span leaf="">Jira Service Management Data Center</span></section></td><td><section><span leaf="">≥ 5.12.40 / ≥ 10.3.26 / ≥ 11.3.12</span></section></td></tr><tr><td><section><span leaf="">Confluence Data Center</span></section></td><td><section><span leaf="">≥ 9.2.26 / ≥ 10.2.19</span></section></td></tr><tr><td><section><span leaf="">Bitbucket Data Center</span></section></td><td><section><span leaf="">≥ 9.4.26 / ≥ 10.2.8 / ≥ 10.5.1</span></section></td></tr><tr><td><section><span leaf="">Bamboo Data Center</span></section></td><td><section><span leaf="">≥ 10.2.24 / ≥ 12.1.12</span></section></td></tr><tr><td><section><span leaf="">Crowd Data Center</span></section></td><td><section><span leaf="">≥ 6.3.7 / ≥ 7.0.3 / ≥ 7.1.7 / ≥ 7.2.4</span></section></td></tr><tr><td><section><span leaf="">Crucible / Fisheye</span></section></td><td><section><span leaf="">≥ 4.9.15</span></section></td></tr></tbody></table>  
官方安全公告及下载地址：  
```
https://confluence.atlassian.com/security/cve-2026-21589-arbitrary-file-access-vulnerability-impacts-multiple-products-1870495748.html
```  
  
  
5.2 临时缓解措施（无法立即升级时）  
  
方案 A：网络隔离  
  
    将受影响的实例从公网下线，限制仅允许内网可信网络访问，直至完成补丁升级。这是最简单有效的临时手段。  
  
方案 B：部署 WAF 正则过滤规则  
  
    在 Web 应用防火墙（WAF）或反向代理层添加 URL 过滤规则，拦截包含路径遍历特征的请求。Atlassian 官方提供的核心正则匹配模式为：  
```
(?is).*(?:/|\\|::|%(?:25)*(?:2f|5c)|(?::|%(?:25)*3a){2})(?:\.|%(?:25)*2e){2}(?:/|\\|::|%(?:25)*(?:2f|5c)|(?::|%(?:25)*3a){2}|;|%(?:25)*3b|$).*
```  
  
方案 C：启用 Tomcat RewriteValve 进行请求重写  
- 在每个 Data Center 集群节点的 Tomcat 配置中：  
  
- 在 conf/server.xml 的 <Context> 元素内添加 RewriteValve 声明。  
  
- 在应用的 WEB-INF/ 目录下放置 Atlassian 官方提供的 rewrite.config 规则文件。  
  
参考链接：  
```
1.https://jira.atlassian.com/browse/BAM-26567
2.https://github.com/watchtowrlabs/watchTowr-vs-Atlassian-CVE-2026-21589
```  
  
本文技术推断部分基于公开披露信息与移动安全领域通用原理，具体漏洞细节以正式发布的研究报告为准。本文不构成任何攻击指导性内容，仅用于防御研究与安全教育,  
不含可直接武器化的完整利用代码。  
  
  
**免责声明：**  
  
   
本文章仅做网络安全技术研究使用！另利用网络安全007公众号所提供的所有信息进行违法犯罪或造成任何后果及损失，均由**使用者自身承担负责**  
，与网络安全007公众号**无任何关系**  
，也不为其负任何责任，**请各位自重！**  
公众号发表的一切文章如有侵权烦请私信联系告知，我们会立即删除并对您表达最诚挚的歉意！感谢您的理解！**让我们一起为中国网络安全事业尽一份自己的绵薄之力！**  
  
  
---推荐阅读---  
  
[攻防演习系列](https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI1NTE2NzQ3NQ==&action=getalbum&album_id=4480577090483748870#wechat_redirect)  
  
  
[渗透技术文章系列](https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI1NTE2NzQ3NQ==&action=getalbum&album_id=4483666897053253633#wechat_redirect)  
  
  
[未授权漏洞系列](https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI1NTE2NzQ3NQ==&action=getalbum&album_id=4483456618323345413#wechat_redirect)  
  
  
[HW专项系列](https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI1NTE2NzQ3NQ==&action=getalbum&album_id=4483461171911426059#wechat_redirect)  
  
  
[应急响应系列](https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI1NTE2NzQ3NQ==&action=getalbum&album_id=2735815599062548484#wechat_redirect)  
  
  
[工具推荐系列](https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI1NTE2NzQ3NQ==&action=getalbum&album_id=4483471065368592394#wechat_redirect)  
  
  
[漏洞通告系列](https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzI1NTE2NzQ3NQ==&action=getalbum&album_id=4728706414628405252#wechat_redirect)  
  
  
  
写作不易，分享快乐  
  
期待你的 **分享**  
●**点赞●在看●关注●收藏**  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/IGws6hSXN0yC4UvVZTh0GWblmLs0dtN1Sfnf88e3vkpokovgdsQAfPI16CnM3C7S6uNVNGHtnsiaFU1via2Bibo92ria29FVIMstgj6wQDg9XbI/640?wx_fmt=png&from=appmsg "")  
  
  
  

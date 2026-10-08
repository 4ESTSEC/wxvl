#  Atlassian：注意 Jira 和 Confluence 中严重的文件访问漏洞  
Bill Toulas
                    Bill Toulas  代码卫士   2026-10-08 08:06  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/Az5ZsrEic9ot90z9etZLlU7OTaPOdibteeibJMMmbwc29aJlDOmUicibIRoLdcuEQjtHQ2qjVtZBt0M5eVbYoQzlHiaw/640?wx_fmt=gif "")  
    
聚焦源代码安全，网罗国内外最新资讯！  
  
编译：代码卫士  
  
**Atlassian****公司正在提醒客户注意一个严重漏洞****CVE-2026-21589****，它可在多个自托管****Data Center****产品（包括****Confluence****、****Jira****和****Bitbucket****）中被利用，实现任意文件访问。**  
  
该漏洞可导致未认证攻击者访问受影响应用程序  
 Web   
根目录中的特定文件。不过需要知道文件的确切名称和路径才能实施利用。该公司在安全公告中提到，“该任意文件访问漏洞可导致未认证攻击者访问受影响版本中  
 Web   
应用程序根目录内的特定文件。利用漏洞需要事先知道目标文件的确切名称和路径；该漏洞不允许攻击者枚举或列出目录内容。”  
  
CVE-2026-21589   
影响以下列出的修复版本之前发布的所有产品版本：  
  
- Bitbucket Data Center  
：  
9.4.26  
、  
10.2.8  
、  
10.5.1  
  
- Confluence Data Center  
：  
9.2.26  
、  
10.2.19  
  
- Jira Service Management Data Center  
：  
5.12.40  
、  
10.3.26  
、  
11.3.12  
  
- Jira Software Data Center  
：  
9.12.40  
、  
10.3.26  
、  
11.3.12  
  
- Bamboo Data Center  
：  
10.2.24  
、  
12.1.12  
  
- Crowd Data Center  
：  
6.3.7  
、  
7.0.3  
、  
7.1.7  
、  
7.2.4  
  
- Crucible  
：  
4.9.15  
  
- Fisheye  
：  
4.9.15  
  
  
  
Atlassian   
敦促管理自托管实例的系统管理员立即应用安全更新。由于供应商已自动修复产品中的漏洞，因此云客户无需采取任何措施。如果无法立即修复，公司建议限制外部网络访问，包括需要对用户进行身份验证的面向互联网实例。临时缓解措施包括添加  
 Web   
应用防火墙（  
WAF  
）或代理规则，以阻止所有受影响产品中指定的遍历模式；为  
 Confluence  
、  
JSM  
、  
Jira  
、  
Bamboo   
和  
 Crowd   
添加  
 Tomcat RewriteValve   
规则；或为  
 Bitbucket   
添加  
 URL   
重写规则。  
Atlassian   
的公告提供了分步说明和配置细节，便于实施建议的临时缓解措施。  
  
这些变更必须覆盖每个集群节点，包括  
 Bitbucket   
镜像和镜像场节点。  
Atlassian   
公司表示，目前没有证据表明  
 CVE-2026-21589  
正遭利用，但敦促管理员检查访问日志，查找公告中描述的遍历模式。该公司还表示无法确定各个客户实例是否已被入侵，敦促使用自托管实例的客户与本地安全团队联系。  
  
  
代码卫士试用地址：https://sast.qianxin.com/  
  
开源卫士试用地址：https://oss.qianxin.com/  
  
  
  
  
  
  
  
  
  
**推荐阅读**  
  
[Atlassian Bamboo 高危RCE漏洞威胁 CI/CD 环境安全](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247525516&idx=2&sn=f2ba1657254018cbcc2be50de9f7fe9c&scene=21#wechat_redirect)  
  
  
[Atlassian 和思科修复多个高危漏洞](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247522791&idx=2&sn=841f61a29df71610844f2e021c5c9bab&scene=21#wechat_redirect)  
  
  
[Atlassian 修复Confluence 和 Crowd 中的多个严重漏洞](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247522309&idx=2&sn=75d35854eb171a70fb22bd76ed1b2cf4&scene=21#wechat_redirect)  
  
  
  
  
  
**原文链接**  
  
https://www.bleepingcomputer.com/news/security/atlassian-warns-of-critical-file-access-flaw-in-jira-confluence/  
  
  
题图：Pixa  
b  
ay Licens  
e  
  
  
**本文由奇安信编译，不代表奇安信观点。转载请注明“转自奇安信代码卫士 https://codesafe.qianxin.com”。**  
  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/oBANLWYScMSf7nNLWrJL6dkJp7RB8Kl4zxU9ibnQjuvo4VoZ5ic9Q91K3WshWzqEybcroVEOQpgYfx1uYgwJhlFQ/640?wx_fmt=jpeg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/oBANLWYScMSN5sfviaCuvYQccJZlrr64sRlvcbdWjDic9mPQ8mBBFDCKP6VibiaNE1kDVuoIOiaIVRoTjSsSftGC8gw/640?wx_fmt=jpeg "")  
  
**奇安信代码卫士 (codesafe)**  
  
国内首个专注于软件开发安全的产品线。  
  
   ![](https://mmbiz.qpic.cn/mmbiz_gif/oBANLWYScMQ5iciaeKS21icDIWSVd0M9zEhicFK0rbCJOrgpc09iaH6nvqvsIdckDfxH2K4tu9CvPJgSf7XhGHJwVyQ/640?wx_fmt=gif "")  
![]( "")  
![]( "")  
  
   
觉得不错，就点个 “  
在看  
” 或 "  
赞  
”   
  

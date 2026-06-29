#  CVSS 10.0！Joomla内容编辑器JCE漏洞正被积极利用，GitHub公开扫描工具  
原创 Red Hunter
                    Red Hunter  黑白之道   2026-06-29 00:38  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650618185&idx=1&sn=e7a4ddec94c9af10451bca515c67e228&scene=21#wechat_redirect)  
> **导语**  
：美国网络安全和基础设施安全局（CISA）将Joomla最流行内容编辑器JCE的一个满分漏洞CVE-2026-48907列入已知被利用漏洞（KEV）目录，明确确认该漏洞正在被积极攻击。攻击者无需任何认证，仅需三步HTTP请求即可在服务器上执行任意PHP代码。GitHub上已出现公开的扫描工具，安全形势严峻。  
  
## 一、漏洞概述  
  
CVE-2026-48907是Widget Factory公司开发的Joomla Content Editor（即JCE，Joomla最流行的内容编辑器，也是安装量最大的Joomla扩展之一）中的一个严重漏洞。  
  
该漏洞属于不当访问控制（Improper Access Control），允许未认证的攻击者通过JCE的资料导入功能上传并执行任意PHP代码，最终获得服务器完整控制权。由于无需认证、无需用户交互，且直接导致远程代码执行，该漏洞的CVSS v4评分达到了满分**10.0**  
，属于紧急程度最高的漏洞。  
  
漏洞影响范围涵盖JCE版本**1.0.0至2.9.99.4**  
。官方已于2026年6月3日发布2.9.99.5版本修复此问题，并在随后6月10日的2.9.99.6版本中进行了额外加固。  
  
![JCE漏洞扫描工具功能截图](https://mmbiz.qpic.cn/sz_mmbiz_jpg/nGzNudUIJ6PXVvicCPaVkGAo9whPfnUK9nutMvxjJhwDCKIcDtMjNmpvQ8hWUtA7iaAZsohG0obC0TDtcHzkR7twM05IecHfGAicnXFjjTD4rg/640?wx_fmt=jpeg "JCE漏洞扫描工具功能截图")  
## 二、CISA确认被积极利用，联邦机构被强制修复  
  
2026年6月16日，CISA将CVE-2026-48907正式列入KEV目录，并在公告中明确指出已有证据表明该漏洞正在被**主动利用**  
。  
  
联邦民政执行机构（FCEB）被要求在**2026年6月19日**  
前完成修复补丁应用，留给管理员的窗口极短。  
  
JCE官方在安全公告中特别警告：  
> "该漏洞正在被积极利用，可用的利用代码已公开，且攻击已实现自动化。即使网站没有开放公开注册功能，也并不安全。"  
  
  
官方还强调了一个关键点：**更新补丁只能关闭攻击入口，但无法清除已被入侵的网站中攻击者留下的后门程序**  
。这意味着如果站点在更新前已被攻陷，仅靠更新无法根除威胁，必须进行完整的安全排查。  
  
mySites.guru的安全研究人员Phil E. Taylor披露，攻击者正利用该漏洞导入恶意编辑器资料，借此在服务器上部署WebShell，从而建立持久化后门。  
  
![攻击流程示意图](https://mmbiz.qpic.cn/mmbiz_jpg/nGzNudUIJ6PATs0BSz6Oj1vGunZZY7Hibch29LLKpDNibZDpqafWkAEaEJ2ygdfPPujPG1LnByJNTiafUrkKiaCicsn8eReTCRn2uyAFEwvHQcrY/640?wx_fmt=jpeg "攻击流程示意图")  
## 三、技术分析：三层漏洞链如何打通RCE  
  
安全研究团队YesWeHack对该漏洞进行了完整的技术分析，揭示了这是一个由**三个独立弱点串联**  
形成的完美攻击链。移除其中任意一环，攻击即会中断。  
### 第一层：缺失的授权验证  
  
JCE的资料导入端点（/index.php?option=com_jce&task=profiles.import  
）在2.9.99.5之前的版本中，唯一的安全检查是CSRF token验证。  
  
Joomla会在每个公开页面中嵌入CSRF token（作为隐藏表单字段或JavaScript变量），因此攻击者只需抓取任意一个页面的HTML即可获得有效token。CSRF检查只能阻止跨站表单提交，无法阻止攻击者自己构造的直接请求。  
  
更关键的是，该端点没有任何Factory::getUser()  
或$user->authorise(...)  
调用，完全不检查用户身份。任何持有有效CSRF token的人都可以调用这个管理功能。  
### 第二层：无文件扩展名验证  
  
文件上传处理代码使用File::makeSafe()  
对文件名进行处理，但该方法仅去除操作系统非法的字符（空格、空字节、Shell元字符等），**完全不检查文件扩展名**  
。  
  
这意味着像nuclei-deadbeef.xml.php  
这样的文件名可以毫无阻碍地通过"安全检查"——从文件系统角度它确实是"安全"的。服务器直接接受.php  
、.php5  
、.phtml  
乃至nuclei-hash.xml.php  
这样的双后缀文件名。  
### 第三层：allow_unsafe=true关闭了最后防线  
  
这是决定性的使能因素。Joomla的File::upload()  
方法签名如下：  
```
File::upload($src, $dest, $use_streams = false, $allow_unsafe = false)
```  
  
当$allow_unsafe  
为false  
（默认值）时，Joomla会启用内置的危险扩展名黑名单（.php、.php3~.php7、.phtml、.exe等），直接拒绝或重命名匹配文件。  
  
但漏洞代码传递了true  
作为第四个参数：  
```
File::upload($source, $destination, false, true);  // 关闭了安全机制！
```  
  
这明确禁用了Joomla的最后一道内置防线，文件以原始.php  
扩展名被写入tmp/  
目录，可被Web服务器直接解析执行。  
## 四、完整攻击流程：仅需三次HTTP请求  
  
三个弱点串联，构成了一个只需三次HTTP请求的RCE攻击链：  
  
**第一步**  
：访问目标站点任意页面，从HTML或JavaScript中提取CSRF token。  
  
**第二步**  
：发送精心构造的multipart POST请求，携带有效CSRF token，POST到/index.php?option=com_jce&task=profiles.import  
，上传文件名类似nuclei-hash.xml.php  
的PHP文件。  
```
POST /index.php?option=com_jce&task=profiles.importContent-Type: multipart/form-data--boundaryContent-Disposition: form-data; name="task"profiles.import--boundaryContent-Disposition: form-data; name="<csrf_token>"1--boundaryContent-Disposition: form-data; name="profile_file"; filename="nuclei-deadbeef.xml.php"Content-Type: application/xml<?= 45*69 ?>--boundary--
```  
  
服务器返回200 OK，文件已被写入/var/www/html/tmp/nuclei-deadbeef.xml.php  
。  
  
**第三步**  
：直接通过HTTP访问上传的PHP文件，即可执行代码。访问/tmp/nuclei-deadbeef.xml.php  
，响应体返回3105  
（即45×69的计算结果），RCE确认。  
  
整个过程：**无会话Cookie、无用户名、无密码**  
。  
## 五、补丁分析：六层修复全面封堵  
  
2.9.99.5版本从六个维度封堵了漏洞：  
<table><thead><tr><th style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;font-weight: bold;background-color: rgb(255, 249, 249);color: rgb(239, 112, 96);"><section><span leaf="">修复层</span></section></th><th style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;font-weight: bold;background-color: rgb(255, 249, 249);color: rgb(239, 112, 96);"><section><span leaf="">措施</span></section></th></tr></thead><tbody><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">授权检查</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">所有管理操作增加</span><code><span leaf="">core.manage</span></code><span leaf="">权限验证，访客用户（ID=0）直接被拒绝</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">扩展名白名单</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">仅接受</span><code><span leaf="">.xml</span></code><span leaf="">扩展名，且使用</span><code><span leaf="">PATHINFO_EXTENSION</span></code><span leaf="">取最后一个后缀组件，防止双后缀绕过</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">关闭unsafe标志</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><code><span leaf="">$allow_unsafe</span></code><section><span leaf="">恢复为默认值</span><code><span leaf="">false</span></code><span leaf="">，重新启用扩展名黑名单</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">文件大小限制</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">上传文件限制512KB，阻断大体积恶意载荷</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">XXE防护</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">对PHP 7.x显式调用</span><code><span leaf="">libxml_disable_entity_loader(true)</span></code><span leaf="">，防止XML外部实体攻击</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">字段白名单</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">仅处理固定的合法XML字段名（name、rows、plugins等），忽略其他任意键</span></section></td></tr></tbody></table>## 六、GitHub公开扫描工具：防御者的双刃剑  
  
正如DarkWebInformer报道的，GitHub上已出现专门扫描CVE-2026-48907漏洞的工具（仓库：gh1mau/masta-cve-2026-48907）。该工具公开宣传的功能包括：  
- Joomla和JCE指纹识别及版本检测  
  
- 受影响JCE安装的版本检查  
  
- WAF检测  
  
- 批量目标扫描  
  
- 多线程扫描支持  
  
- 代理支持  
  
- 终端结果卡片输出  
  
- Excel报告生成  
  
- **侵入性PoC验证**  
（直接对目标执行漏洞验证）  
  
- 上传测试文件清理提醒  
  
![GitHub上的CVE-2026-48907扫描工具仓库](https://mmbiz.qpic.cn/sz_mmbiz_jpg/nGzNudUIJ6PGHZQG7e3mdAvTGkjyLGwdytqoo6ficZGbxfBzXGnTDFQu5j3iaOyzIicgDPzD0hjIibwhibCwibwvzMTG1n6cZAw5EEW3wJ5lBV0OE/640?wx_fmt=jpeg "GitHub上的CVE-2026-48907扫描工具仓库")  
  
安全社区对"侵入性PoC验证"功能存在争议——对未经授权的目标执行此类验证本身就是违法行动，批量扫描更是直接跨越了研究与攻击的边界。  
## 七、应急响应建议  
  
对于已使用受影响版本JCE的站点管理员：  
  
**立即行动**  
：  
- 升级至JCE **2.9.99.6**  
或更高版本，这是最直接的缓解措施  
  
- 使用CDN或WAF限制对/component/jce/  
路径的访问（临时措施）  
  
**深度排查**  
（已中招但已更新的站点）**：  
- 检查JCE管理后台是否存在来源不明的编辑器资料配置文件  
  
- 审计Web服务器访问日志，查找对index.php?option=com_jce&task=profiles.import  
端点的非授权请求  
  
- 检查tmp/  
目录是否存在异常PHP文件（尤其是日期较早或文件名含随机哈希的）  
  
- 查找服务器上是否存在攻击者遗留的WebShell  
  
**历史站点特殊处理**  
：JCE官方为无法立即升级的老版本站点提供了免费补丁（jce-pro older sites patch），请参阅官方公告获取。  
## 八、总结  
  
CVE-2026-48907是一个教科书级别的"三弱串联"漏洞：缺失授权给了任何人入口，扩展名无检查让.php文件畅通无阻，allow_unsafe=true  
亲手拆掉了最后一道防线。三者叠加，成就了一个满分10.0的未授权RCE。  
  
CISA已确认在野利用，公开利用代码已扩散，GitHub扫描工具已上线——留给使用JCE的Joomla站点管理员的时间不多了。立即打补丁，补丁打完后排查后门，别把补丁当成万能药。  
  
**版权声明**  
：本文由华盟网原创发布，保留所有权利。配图由华盟网授权使用。  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/nGzNudUIJ6Puc8HNiaVFMibMWyXuRCjam9iat0x1rCpsp1iatD7HveaKn7X0FMG8AlYEYZQbWQjAzjbYJjgdDQYTxLsn3W66vehH9V8LV2z9XnE/640?wx_fmt=jpeg "")  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650618755&idx=1&sn=48e0a85464fb20c73b6f338928a8f850&scene=21#wechat_redirect)  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650618776&idx=2&sn=a8fc65fd2a71822830022fc52d967bcd&scene=21#wechat_redirect)  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650618496&idx=1&sn=96ecdff99136258a4bf2cb156542311e&scene=21#wechat_redirect)  
> 👇 点击**阅读原文**  
，访问我的网站  
  
  

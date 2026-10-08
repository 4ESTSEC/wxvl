#  OpenSSH 10.6修复多项高危漏洞，可致明文恢复、文件写入与注入  
 FreeBuf   2026-10-08 10:00  
  
![FreeBuf](https://mmbiz.qpic.cn/mmbiz_gif/icBE3OpK1IX2ZvIicqrY3QkSncIs6bEpjdkc49vSZh90c3VnbDV34UGd1ExN38F9WkjDqG05ALBibWJybKqOKG5xO6DIYB27Yu2W3qdmr4bLvI/640?wx_fmt=gif "")  
  
  
![OpenSSH 10.6漏洞修复相关截图](https://mmbiz.qpic.cn/sz_mmbiz_png/icBE3OpK1IX31AWkhEC2IygZp3EXTneAFhHsic8vuCvr61p7ubGiaL9c6IEibWEOTBcnxgINEXCwZZeCx8Evibf7DvkW0ljEkdYbg9eETjJPWIus/640?wx_fmt=png "")  
  
  
2026年10月6日，OpenSSH发布10.6版本，修复了多个安全漏洞。这些漏洞在特定条件下可导致敏感信息泄露、写入非预期目录文件，或触发shell注入攻击。本次更新同时覆盖客户端与服务端工具，所有依赖SSH开展远程访问、文件传输的管理员和用户都需关注。  
  
  
官方发布公告明确，本次修复的是多个独立漏洞，各漏洞的攻击前提条件各不相同，并非一个可影响所有部署场景的通用漏洞。本次修复的核心风险点主要有三类：SSH多通道共享压缩机制、SFTP服务器返回路径校验、传入shell命令的不可信用户名处理。  
  
  
Part  
01  
  
压缩机制可导致SSH明文恢复  
  
  
研究人员Fabian Bäumer与Marcus Brinkmann在《Crossing the Streams》报告中披露了该压缩漏洞。当SSH启用压缩功能时，同一连接内的所有通道会共享同一个压缩字典。攻击者可向其中一个通道插入提前构造的文本，同时观测加密流量的长度变化。基于这些长度变化特征，攻击者就能恢复另一通道中传输的敏感内容。  
  
  
这一问题源于LZ77压缩算法的特性。该算法会将重复内容替换为指向此前数据的引用，如果攻击者插入的文本与部分敏感内容匹配，最终生成的流量长度就会泄露线索。这种攻击不会直接破解SSH加密机制，只是利用了加密前压缩阶段暴露的信息。  
  
  
研究人员在最低噪声环境下测试发现，测试所用的敏感内容从26个字母中选取、长度为8字符，最多仅需276次猜测就能恢复。该结果仅适用于特定测试条件，不能代表所有场景下的恢复效率。  
  
  
OpenSSH 10.6在ssh和sshd组件中均禁用了LZ77字典编码器。压缩功能仍可正常使用，但压缩效率会有所下降。开发人员建议用户优先使用应用层压缩，避免在可信与不可信流量之间共享SSH压缩层。  
  
  
Part  
02  
  
修复两类逻辑缺陷  
  
  
本次更新针对SFTP场景强化了服务器返回路径的校验逻辑。在修复前，恶意SFTP服务器可返回构造的路径，诱导客户端递归复制操作将文件写入目标目录之外。该漏洞由研究人员Junghoon Cho报告并提供补丁，风险存在于客户端处理服务器响应的环节，并非未授权攻击者可直接写入任意SSH服务器的问题。  
  
  
另一项修复针对SSH命令行的目标用户名参数，新增规则屏蔽参数中的美元符号与反斜杠。在部分配置场景下，来自不可信来源的用户名可能通过ProxyCommand、Match exec或相关功能进入shell执行上下文，引发命令注入。该漏洞由腾讯KeenLab SecBuddyF团队报告。  
  
  
需要注意的是，通过配置文件中User指令设置的用户名，不受本次新增字符规则的限制。OpenSSH团队提醒，字符过滤机制无法覆盖所有shell环境与部署场景，因此应用程序不应直接将不可信输入传入SSH命令行。  
  
  
CyberSecurity News此前曾报道OpenSSH 10.3版本的shell注入修复，当时的补丁主要针对用户名与ProxyJump的校验逻辑，与本次新增的防护机制相互独立。  
  
  
Part  
03  
  
更新覆盖多项其他修复  
  
  
本次更新还修复了其他多个问题，包括GSSAPI凭证处理逻辑错误、隧道访问限制缺陷、超大解压数据包处理异常、证书日期转换错误。在QNX 6、SCO OpenServer 5等部分老旧平台上，新版本禁用了与保留root权限相关的转发选项。  
  
  
管理员部署本次更新时，应结合自身业务场景核查相关变更，尤其是需要启用压缩或转发功能的环境。  
  
  
目前随着AI辅助提交的漏洞报告不断增多，OpenSSH维护团队计划提高版本发布频率。团队同时提醒，攻击者也可能独立发现同类漏洞，对于这类AI辅助提交的报告，人工审核、测试用例验证、补丁有效性校验仍然很关键。  
  
  
参考来源：  
  
Multiple OpenSSH Vulnerabilities Could Enable Plaintext Recovery, File Write and Injection Attacks  
  
https://cybersecuritynews.com/openssh-10-6-released/  
  
###   
  
### 推荐阅读  
  
  
[](https://mp.weixin.qq.com/s?__biz=MjM5NjA0NjgyMA==&mid=2651347479&idx=1&sn=1cf9c858a30d0b7af06d196002bd2913&scene=21#wechat_redirect)  
  
  
###   
  
### 电报讨论  
  
  
[]()  
  
  
  
![扫码加入AI安全交流群](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX0Py7ibxdLKXia1pMziaic5vIE9XPXG9OGaeJDa07iaG10eicuzhW59nwpF5msHiaYZvfMqCNkx2aFDiaMzm3oAf4rTaHXU5UAI1mUYgts/640?wx_fmt=png "")  
  
  
  
![下载FreeBuf知识大陆APP](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX0TIGzII2Hcmtzu7AJeZFicnqd1mXojVoawje2uLxYqwJbVgzJpmSXzVhrpOsLurRZ2lVa4vfgLBqg7uJKbrKg5F18VzZxVPicZU/640?wx_fmt=png "")  
  
  
  
  

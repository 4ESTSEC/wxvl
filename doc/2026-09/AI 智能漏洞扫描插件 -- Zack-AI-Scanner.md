#  AI 智能漏洞扫描插件 -- Zack-AI-Scanner  
ZackSecurity
                    ZackSecurity  Web安全工具库   2026-09-28 01:20  
  
===================================  
  
**免责声明**  
  
请勿利用文章内的相关技术从事非法测试，由于传播、利用此文所提供的信息而造成的任何直接或者间接的后果及损失，均由使用者本人负责，作者不为此承担任何责任。工具来自网络，  
安全性自测  
，  
大家都要把工具当做病毒对待，在虚拟机运行。  
如有侵权请联系删除。个人微信：  
ivu123ivu  
  
  
**0x01 工具介绍**  
  
Burp Suite 的 AI 智能漏洞扫描插件：右键把请求交给它，或开启「Proxy 流量自动扫描」（默认关闭） 让它自动接管带参数的代理流量 —— 由大模型判断哪些参数值得打、该用什么载荷，载荷真正发出去， 最后依据证据判断目标到底能不能被利用。结果落进任务表格，可导出 HTML / Markdown 报告。![image-20260927214343162](https://mmbiz.qpic.cn/mmbiz_png/U7LDNXUGXQsdpniaAFvxNj47Us9aTYPUAtGKz4yY47IFkicqAUR1Pr7KbTLj7j4ZMpSibd1x4joCN40cQ5DQNcgBBMCbNYlXKwvl9ABItn0qwE/640?wx_fmt=png&from=appmsg "")  
  
  
**0x02 安装与使用**  
  
安装方法：  
```
Burp → Extender → Extensions → Add → Extension type 选 Java → 选中那个 JAR。
```  
  
加载成功后，Extender 的 Output 里会打印版本横幅与版权声明，主界面多出一个 Zack-AI-Scanner 标签页。  
  
使用方法：  
```
打开「配置」页，选服务商、填 API Key，点「验证 Key」——没验证过之前「保存配置」** 按钮是灰的——然后点「保存配置」。
在 Proxy history / Repeater / Target 里右键任意请求 → Zack-AI-Scanner → 选一个漏洞类型， 或选**「AI智能扫描」。
在「任务列表」页看进度与结果。双击某行看每个载荷的「请求与响应详情」；勾选若干行可批量 删除 / 暂停 / 继续，或导出报告。同时只跑 10 个任务，其余排队并显示位次（排队中 3/12）。
```  
  
![image-20260927213645108](https://mmbiz.qpic.cn/sz_mmbiz_png/U7LDNXUGXQtdxMibQ2I9Sa47iaFKOwrIggvIfQqyVhK3AZrrVcRiax4GTCMzWQlibAiboZyfib4X2Af0hgp3A4aAoiabDemTkyRxmNfrUTalfbYoFM/640?wx_fmt=png&from=appmsg "")  
![image-20260927213749746](https://mmbiz.qpic.cn/sz_mmbiz_png/U7LDNXUGXQuia1JjKJHDXHXYeeRVgCE2sY2YnBicUJ2m8mTctJpfYIE1tdcQ22KxF3A98F19ae4U0lqjicdiaz6WSoebialGY9HIzu0qR79WbpLE/640?wx_fmt=png&from=appmsg "")  
  
  
  
网盘下载链接（一定要在虚拟机运行）：  
```
后台回复：20260928
获取下载链接，仅一天有效
```  
  
  
  
  
**·****今 日 推 荐**  
**·**  
<table><tbody><tr><td data-colwidth="287" style="word-break: break-all;"><p><span leaf=""><img class="rich_pages wxw-img" data-aistatus="1" data-imgfileid="100035332" data-s="300,640" data-src="https://mmbiz.qpic.cn/mmbiz_jpg/U7LDNXUGXQsSuz7fuz5iblWLDaYYFBoZur2qOvXLlfHSWo4NRAQPGpwbsHSztpIWbywTs3OwwdnC7B3B8lBBIjvUaslxRScFt2sMNCPAxZP0/640?wx_fmt=jpeg&amp;from=appmsg" data-type="jpeg" type="inline"/></span></p><p><span leaf=""><br/></span></p></td><td data-colwidth="287" style="word-break: break-all;"><section nodeleaf=""><img class="rich_pages wxw-img" data-aistatus="1" data-imgfileid="100035049" data-ratio="1.2469635627530364" data-s="300,640" data-src="https://mmbiz.qpic.cn/sz_mmbiz_jpg/8H1dCzib3UibsC4yYFwgTnJrN0q57DearHJhaWSE6XQllpkUviaibg5MqTYgdUQYDNt8ysfV2v6o4jsN34pmq3DAOg/640?wx_fmt=jpeg&amp;from=appmsg" data-type="jpeg" data-w="1235" style="letter-spacing: 0.578px;"/></section></td></tr></tbody></table>  
  

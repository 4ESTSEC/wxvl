#  智能自动化漏洞挖掘平台 -- AutoHunter  
Chris-biu
                    Chris-biu  网络安全者   2026-07-27 03:31  
  
===================================  
  
**免责声明**  
  
请勿利用文章内的相关技术从事非法测试，由于传播、利用此文所提供的信息而造成的任何直接或者间接的后果及损失，均由使用者本人负责，作者不为此承担任何责任。工具来自网络，安全性自测，如有侵权请联系删除。  
个人微信：ivu123ivu  
  
  
**0x01 工具介绍**  
  
  
AutoHunter 把红队自动化和 AI 决策结合起来：你给它一个目标，它像一名渗透测试工程师那样， 自主决定"用哪个工具、打哪个面、查什么漏洞"，把 Burp、Xray、nuclei 这些工具串成攻击链， 最后给你一份对齐 OWASP、诚实标注覆盖盲区、去过误报的报告。核心不实现任何具体攻击——一切皆插件。用户可以零代码(YAML)或低代码(Python)不断接入新工具， 工具库越大，agent 能力越强。  
  
  
**0x02 安装与使用**  
  
常用命令：  
```
pip install -r requirements.txt
# 内置 demo 插件 + 自带靶场，端到端跑通整条流水线
python -m autohunter scan --target http://demo-shop.local --type website --scope demo-shop.local
报告输出到 
reports/<任务ID>/report.html。想接真实工具？一条命令下载：
python scripts/fetch_tools.py         # 自动下载 Xray / nuclei
```  
  
一定要在虚拟机运行，工具下载链接：  
  
公众号后台回复：20260727  
  
链接仅一天有效，每日更新  
  
  
  
**·****今 日 推 荐**  
**·**  
<table><tbody><tr><td data-colwidth="287"><section><span leaf=""><img class="rich_pages wxw-img" data-aistatus="1" data-imgfileid="100016956" data-ratio="1.4015518913676042" data-s="300,640" data-src="https://mmbiz.qpic.cn/sz_mmbiz_jpg/PQNvx9ufMAiafVwpBgXPxhkJfYHmoiafxgzsnebYCqhE4AurfCODou7icJ5SFWA89grH350m6VQBZeoVLfwIydOCicdd0GW1FRy19GuibvJrjBSs/640?wx_fmt=jpeg&amp;from=appmsg" data-w="1031" type="inline"/></span></section></td><td data-colwidth="287"><section><span leaf=""><img class="rich_pages wxw-img" data-aistatus="1" data-imgfileid="100016948" data-ratio="1.5036585365853659" data-s="300,640" data-src="https://mmbiz.qpic.cn/mmbiz_jpg/PQNvx9ufMAg72kwqmjiccmjCVo8dPpVabnY9EauQxibgOKK4uh8fMISXibTfibicoG9Kic5GgNAKnCATdUv4eYAibHemhKicpwyvuYd5eGIjaKicSdg4/640?wx_fmt=jpeg&amp;from=appmsg" data-w="820" type="inline"/></span></section></td></tr></tbody></table>  
  

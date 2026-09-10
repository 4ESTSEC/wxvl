#  漏洞扫描智能体 -- vuln-scan-agent  
bear561
                    bear561  Web安全工具库   2026-09-10 01:12  
  
===================================  
  
**免责声明**  
  
请勿利用文章内的相关技术从事非法测试，由于传播、利用此文所提供的信息而造成的任何直接或者间接的后果及损失，均由使用者本人负责，作者不为此承担任何责任。工具来自网络，  
安全性自测  
，  
大家都要把工具当做病毒对待，在虚拟机运行。  
如有侵权请联系删除。个人微信：  
ivu123ivu  
  
  
**0x01 工具介绍**  
  
这是一个以学习 LangChain 1.x / LangGraph 1.x 生态为主线设计的学习项目。Agent 本身是真实可用的（HTTP 探测、SQLi/XSS 识别、报告生成、SQLite 持久化），但更核心的目的是练习：只给靶场根 URL 也能扫描：recon 会抓根页、爬取一层同源链接，并从 HTML 表单和链接里自动发现参数；GET 表单按 query 探测，POST 表单按 body 探测。  
```
Annotated[list, operator.add] 增量合并 reducer
Send() 从条件边做动态扇出
AsyncSqliteSaver 检查点持久化
interrupt_before 实现人机协作授权
astream 翻译为统一的 WebSocket 事件协议
```  
  
  
**0x02 安装与使用**  
```
cd backend

# 非网络全量测试（38 passed, 1 skipped, 8 deselected）
pytest -m "not network"

# 全量测试（含 httpbin.org 网络用例；外部不可达时可能环境性失败）
pytest

# 纯逻辑单测
pytest tests/unit -v

# 根地址不带 query + query/body 表单探测
pytest tests/integration/test_root_no_query.py -v

# httpbin 端到端集成测试
pytest tests/integration/test_httpbin.py -v

# WebSocket 事件流测试
pytest tests/integration/test_ws_events.py -v

# 中断 + 恢复测试
pytest tests/integration/test_interrupt_resume.py -v

# 跑单个测试
pytest tests/integration/test_ws_events.py::test_ws_resume_completes_scan -v
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/U7LDNXUGXQuFeibeCIkwJbRjjwXlpIgWPEWjXqBDAE4kLpVh2RVA36ShBK1BzOuaTNbbmGRx5TRMibibjggWVkiaWYlNnHbN4hW5AE9thLhJzNM/640?wx_fmt=png&from=appmsg "")  
  
  
  
网盘下载链接（一定要在虚拟机运行）：  
```
后台回复：20260910
获取下载链接，仅一天有效
```  
  
  
  
  
**·****今 日 推 荐**  
**·**  
<table><tbody><tr><td data-colwidth="287" style="word-break: break-all;"><p><span leaf=""><img class="rich_pages wxw-img" data-aistatus="1" data-imgfileid="100035229" data-ratio="1.4015518913676042" data-s="300,640" data-src="https://mmbiz.qpic.cn/sz_mmbiz_jpg/U7LDNXUGXQvcQ6NvGFpD17ZHlkabrBPRPo2hTSFZ70qDImj2dOpicNMUXTJ5MF9z4tGbcCd1RKhHmDGS5z1z0K5Emib9ib8tJ5iaNfggPiaSyxZQ/640?wx_fmt=jpeg&amp;from=appmsg" data-w="1031" type="inline"/></span></p><p><span leaf=""><br/></span></p></td><td data-colwidth="287" style="word-break: break-all;"><section nodeleaf=""><img class="rich_pages wxw-img" data-aistatus="1" data-imgfileid="100035049" data-ratio="1.2469635627530364" data-s="300,640" data-src="https://mmbiz.qpic.cn/sz_mmbiz_jpg/8H1dCzib3UibsC4yYFwgTnJrN0q57DearHJhaWSE6XQllpkUviaibg5MqTYgdUQYDNt8ysfV2v6o4jsN34pmq3DAOg/640?wx_fmt=jpeg&amp;from=appmsg" data-type="jpeg" data-w="1235" style="letter-spacing: 0.578px;"/></section></td></tr></tbody></table>  

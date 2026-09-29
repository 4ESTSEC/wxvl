#  自动化资产测绘 + 漏洞扫描工具 -- dddd-next（9月29日更新）  
galact-byte
                    galact-byte  Web安全工具库   2026-09-29 00:58  
  
===================================  
  
**免责声明**  
  
请勿利用文章内的相关技术从事非法测试，由于传播、利用此文所提供的信息而造成的任何直接或者间接的后果及损失，均由使用者本人负责，作者不为此承担任何责任。工具来自网络，  
安全性自测  
，  
大家都要把工具当做病毒对待，在虚拟机运行。  
如有侵权请联系删除。个人微信：  
ivu123ivu  
  
  
**0x01 工具介绍**  
  
面向授权环境的自动化资产测绘 + 漏洞扫描工具，基于原 dddd 的设计思路做现代化重写，覆盖指纹识别、弱口令、nuclei POC、Shiro 专项检测和 HTML 报告。主要功能：  
```
支持 IP、网段、域名、URL、测绘语句和目标文件，接入 FOFA / Hunter / Quake。
端口与服务识别、子域名枚举、主动及被动指纹识别、产品路径探测。
Nuclei 精准 POC、GoPoC 弱口令与协议检测、Shiro 专项检测。
TXT / JSON / HTML 报告及审计日志；HTML 支持严重度筛选、详情展开、目标地址和请求 / 响应复制。
```  
  
  
**0x02 安装与使用**  
  
常用命令：  
  
Windows PowerShell：  
```
.\dddd.exe help
.\dddd.exe update
.\dddd.exe -t 192.168.1.1
.\dddd.exe -t targets.txt -p 1-65535
```  
  
Linux：  
```
./dddd help
./dddd update
./dddd -t 192.168.1.1
./dddd -t targets.txt -p 1-65535
```  
```
# 扫描网段或网站
./dddd -t 192.168.1.0/24
./dddd -t http://example.com

# 指定端口，仅做资产探测
./dddd -t 192.168.1.1 -p 80,443,8000-8100 -no-poc

# 按 POC 名称 / ID 片段筛选，或运行全部 Nuclei 模板
./dddd -t http://example.com -poc nacos
./dddd -t http://example.com -full

# FOFA 测绘（先在环境变量或 .env 配置 FOFA_EMAIL 和 FOFA_KEY）
./dddd -fofa -t 'app="seeyon"' -limit 100
```  
  
网盘下载链接（一定要在虚拟机运行）：  
```
后台回复：20260929
获取下载链接，仅一天有效
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/U7LDNXUGXQuqq4FBST7rLEb0EPstUbDmzKicmVOHictIkKAamHKzCKgibKSLjuhCVlZFYYJGn1H5wzpudV3CSR5jibwZXBrW4xs1xicu6uXKpP7U/640?wx_fmt=png&from=appmsg "")  
  
  
  
  
**·****今 日 推 荐**  
**·**  
<table><tbody><tr><td data-colwidth="287" style="word-break: break-all;"><p><span leaf=""><img class="rich_pages wxw-img" data-aistatus="1" data-imgfileid="100035332" data-ratio="1.4015518913676042" data-s="300,640" data-src="https://mmbiz.qpic.cn/mmbiz_jpg/U7LDNXUGXQsSuz7fuz5iblWLDaYYFBoZur2qOvXLlfHSWo4NRAQPGpwbsHSztpIWbywTs3OwwdnC7B3B8lBBIjvUaslxRScFt2sMNCPAxZP0/640?wx_fmt=jpeg&amp;from=appmsg" data-w="1031" type="inline"/></span></p><p><span leaf=""><br/></span></p></td><td data-colwidth="287" style="word-break: break-all;"><section nodeleaf=""><img class="rich_pages wxw-img" data-aistatus="1" data-imgfileid="100035049" data-ratio="1.2469635627530364" data-s="300,640" data-src="https://mmbiz.qpic.cn/sz_mmbiz_jpg/8H1dCzib3UibsC4yYFwgTnJrN0q57DearHJhaWSE6XQllpkUviaibg5MqTYgdUQYDNt8ysfV2v6o4jsN34pmq3DAOg/640?wx_fmt=jpeg&amp;from=appmsg" data-type="jpeg" data-w="1235" style="letter-spacing: 0.578px;"/></section></td></tr></tbody></table>  
  
  
  
  
  
  

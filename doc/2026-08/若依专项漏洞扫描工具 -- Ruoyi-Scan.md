#  若依专项漏洞扫描工具 -- Ruoyi-Scan  
xiabai2008
                    xiabai2008  Web安全工具库   2026-08-25 01:44  
  
===================================  
  
**免责声明**  
  
请勿利用文章内的相关技术从事非法测试，由于传播、利用此文所提供的信息而造成的任何直接或者间接的后果及损失，均由使用者本人负责，作者不为此承担任何责任。工具来自网络，  
安全性自测  
，  
大家都要把工具当做病毒对待，在虚拟机运行。  
如有侵权请联系删除。个人微信：  
ivu123ivu  
  
  
**0x01 工具介绍**  
  
  
若依（RuoYi）专项漏洞扫描器 — 插件化 / 三态判定 / WAF绕过 / 利用链 / AI生成POC / nuclei模板兼容 / 组件版本检测 / Web API  
  
**0x02 安装与使用**  
  
常用命令：  
```
# 单目标漏洞扫描
python main.py -p http://target:8080/

# 批量扫描
python main.py -f targets.txt -p --report ./reports

# 手动指定 CMS（跳过指纹识别）
python main.py -p http://target:8080/ --cms ruoyi

# 综合扫描（目录扫描 + 漏洞检测 + 登录爆破）
python main.py -u http://target:8080/

# 生成全格式报告（HTML/JSON/CSV/PDF/Word/Excel）
python main.py -p http://target:8080/ --report ./reports --report-format all

# WAF 绕过（检测到 WAF 自动启用）
python main.py -p http://target:8080/ --bypass-waf auto

# 执行漏洞利用链
python main.py --chain ruoyi_sql_to_rce -u http://target:8080/
python main.py --chain list  # 列出可用链

# 组件版本检测（fastjson/SpringBoot/Shiro/Nacos/Log4j → CVE 比对）
python main.py -p http://target:8080/ --components

# 执行 nuclei 模板（nuclei-templates 生态直接复用）
python main.py -p http://target:8080/ --nuclei examples/nuclei/
python main.py --nuclei-validate examples/nuclei/  # 模板校验（不扫描）

# AI 生成插件（LLM 自验证回灌；无 Key 时降级规则模板）
python main.py --ai "检测若依任意文件读取漏洞" --category ruoyi

# 插件模板仓库（社区分发）
python main.py --plugin-export ./ruoyi-scan-templates
python main.py --plugin-manifest ./ruoyi-scan-templates  # 生成/校验 manifest（Ed25519 签名）
python main.py --plugin-update  # 从官方仓库更新插件

# Web API 服务
python main.py --serve
# 访问 http://localhost:8000/ (Web 控制台)
# 访问 http://localhost:8000/docs (OpenAPI 文档)

# 端口扫描 + 漏洞检测
python main.py -p http://target:8080/ --portscan

# 被动代理模式
python main.py --passive --passive-port 8080

# Docker 部署（见下方「Docker 部署」章节）
# docker-compose up -d
```  
  
网盘下载链接（一定要在虚拟机运行）：  
```
后台回复：20260825
获取下载链接，仅一天有效
```  
  
  
  
  
**·****今 日 推 荐**  
**·**  
<table><tbody><tr><td data-colwidth="287" style="word-break: break-all;"><p><span leaf=""><img class="rich_pages wxw-img" data-aistatus="1" data-imgfileid="100035165" data-ratio="1.4015518913676042" data-s="300,640" data-src="https://mmbiz.qpic.cn/mmbiz_jpg/U7LDNXUGXQuibiciaRzwfw5QtjwDHvtwKHBLVriaD1picuNUblTthG4Tk5T547z2glCmTFXcVNtTcMmwiavVcbtgLuy4kZKEEPG6QjHYkkEJCGtSU/640?wx_fmt=jpeg&amp;from=appmsg" data-w="1031" type="inline"/></span></p><p><span leaf=""><br/></span></p></td><td data-colwidth="287" style="word-break: break-all;"><section nodeleaf=""><img class="rich_pages wxw-img" data-aistatus="1" data-imgfileid="100035049" data-ratio="1.2469635627530364" data-s="300,640" data-src="https://mmbiz.qpic.cn/sz_mmbiz_jpg/8H1dCzib3UibsC4yYFwgTnJrN0q57DearHJhaWSE6XQllpkUviaibg5MqTYgdUQYDNt8ysfV2v6o4jsN34pmq3DAOg/640?wx_fmt=jpeg&amp;from=appmsg" data-type="jpeg" data-w="1235" style="letter-spacing: 0.578px;"/></section></td></tr></tbody></table>  
  

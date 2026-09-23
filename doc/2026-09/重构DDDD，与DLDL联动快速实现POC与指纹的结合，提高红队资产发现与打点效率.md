#  重构DDDD，与DLDL联动快速实现POC与指纹的结合，提高红队资产发现与打点效率  
迷人安全
                    迷人安全  夜组安全   2026-09-22 23:30  
  
免责声明  
  
由于传播、利用本公众号夜组安全所提供的信息而造成的任何直接或者间接的后果及损失，均由使用者本人负责，公众号夜组安全及作者不为此承担任何责任，一旦造成后果请自行承担！如有侵权烦请告知，我们会立即删除并致歉。谢谢！  
**所有工具安全性自测！！！VX：**  
**NightCTI**  
  
朋友们现在只对常读和星标的公众号才展示大图推送，建议大家把  
**夜组安全**  
“**设为星标**  
”，  
否则可能就看不到了啦！  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/icZ1W9s2Jp2WrOMH4AFgkSfEFMOvvFuVKmDYdQjwJ9ekMm4jiasmWhBicHJngFY1USGOZfd3Xg4k3iamUOT5DcodvA/640?wx_fmt=png&from=appmsg "")  
  
## 工具介绍  
  
DDDL 是一款面向**授权渗透测试**  
与红队行动的自动化资产发现与漏洞打点工具。项目重构自 dddd 工作流，与 DLDL 联动，快速实现 POC 与指纹的结合，显著提高红队资产发现与打点效率。  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/WibL3bOeESMIzKCOb71UKkcfSy8BAcCdwsX5Aok3hQJXSCQmOSrrVPjsSK85fUtOHqc6qianibjrJM6rAOicDu1uiaZvQ0I3AXhIovojOSLahvuM/640?wx_fmt=webp&from=appmsg "")  
## 功能特性  
- **自动识别输入**  
：IP、IP 段、CIDR、IP:Port、URL、域名、历史结果文件，一个 -t  
 全兼容  
  
- **子域名枚举**  
：外部引擎 dnsx  
 / ksubdomain  
 / auto  
 自动回退，支持被动 subfinder 收集  
  
- **端口扫描**  
：-p  
 指定、-portf  
 文件读取（兼容区间写法）、SYN 扫描、-np  
 排除、-nps  
 关闭  
  
- **Web 探测与指纹**  
：被动指纹 + 主动路径指纹 + 黑名单过滤，CDN/WAF 默认识别并跳过  
  
- **漏洞打点**  
：Nuclei 模板映射 + GoPoc 服务类检测，与 DLDL 联动实现 POC 与指纹自动关联  
  
- **低感知设计**  
：可逐项关闭 POC、通用 POC、主动指纹、域名绑定等模块，适应敏感环境  
  
- **资产导入**  
：Hunter / Fofa / Quake 网络空间搜索引擎查询导入，Hunter 单字段自动规范化  
  
- **附加能力**  
：Katana 未授权接口探测、网站备份文件扫描、服务爆破、Shiro Keys 检测  
  
- **结果输出**  
：文本、JSON、按需 HTML 报告与审计日志  
  
## 快速开始  
  
扫描单个 URL，仅做低影响信息收集：  
```
dddd -t http://10.10.0.176:8080/login -nps -dgp -npoc -nd -nhb
```  
  
扫描 IP 段，从文件读取端口并关闭漏洞探测：  
```
dddd -t 192.168.1.0/24 -portf ports.txt -npoc
```  
  
枚举子域名，优先 ksubdomain  
、失败自动回退 dnsx  
：  
```
dddd -t example.com -sd -sde auto
```  
  
Hunter 查询域名后缀（自动规范化为 domain.suffix="example.com"  
）：  
```
dddd -t domain.suffix=example.com -hunter
```  
  
扫描已存活 Web 的常见网站备份文件：  
```
dddd -t http://www.baidu.com -bs -npoc -nd -nps
```  
  
敏感环境建议组合：  
```
dddd -t target.txt -npoc -dgp -nps -nd -nhb -a
```  
## 更新记录  
  
**v1.1.2（2026-09-15）**  
- 新增 -portf  
/--port-file  
 端口文件输入，兼容区间写法  
  
- 修复 -nt  
 追加自定义 Nuclei 模板后扫描流程被跳过  
  
- Shiro/MS17010 错误处理完善，修复 ADB/ICMP/RDP 等多个 panic 与竞态问题  
  
- 工程规范化，清理冗余代码，提升稳定性  
  
  
  
## 工具获取  
  
  
  
点击关注下方名片  
进入公众号  
  
回复关键字【  
260923  
】获取  
下载链接  
  
  
## 往期精彩  
  
  
往期推荐  
  
[PivotHub · 链透中枢 — 多层内网渗透辅助工具 | CTF、内网渗透、攻防](https://mp.weixin.qq.com/s?__biz=Mzk0ODM0NDIxNQ==&mid=2247497740&idx=1&sn=ae0117e7a728e36eb2027265fd6730b2&scene=21#wechat_redirect)  
  
  
[CNVD 通用型未授权漏洞广扫Skill · 便携工具包](https://mp.weixin.qq.com/s?__biz=Mzk0ODM0NDIxNQ==&mid=2247497727&idx=1&sn=318420c51440662571aa1e479e7d4a79&scene=21#wechat_redirect)  
  
  
[渗透武器库、渗透工具箱 | 专为渗透测试从业者开发的一站式工具启动器](https://mp.weixin.qq.com/s?__biz=Mzk0ODM0NDIxNQ==&mid=2247497712&idx=1&sn=dffc1878ca3abee54f04de6e3d331783&scene=21#wechat_redirect)  
  
  
[AI 驱动的自动化渗透测试平台 | AI漏洞挖掘系统 - 黑板架构 / 模型分级 / 结果验证铁律](https://mp.weixin.qq.com/s?__biz=Mzk0ODM0NDIxNQ==&mid=2247497704&idx=1&sn=fd2e8ff5246b2030696dab42bc80f2ab&scene=21#wechat_redirect)  
  
  
[DSH RedTeam 模式：红队作战指挥台——一个靶标名称拉起信息收集/漏洞检测/漏洞利用/内网渗透四个角色](https://mp.weixin.qq.com/s?__biz=Mzk0ODM0NDIxNQ==&mid=2247497703&idx=1&sn=fab9a9feec8879427e2a514bea4a8c79&scene=21#wechat_redirect)  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/OAmMqjhMehrtxRQaYnbrvafmXHe0AwWLr2mdZxcg9wia7gVTfBbpfT6kR2xkjzsZ6bTTu5YCbytuoshPcddfsNg/640?wx_fmt=other&wxfrom=5&wx_lazy=1&wx_co=1&random=0.8399406679299557&tp=webp "")  
  

#  IBM Langflow OSS 高危漏洞分析：VackBot 已实现检测覆盖  
 墨云安全   2026-09-20 02:46  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/7lCiaSMMkhia4WIkRNZHTwq8jJicy27jdbWa7ED26252RGmSPRE0rmHQsgZ6ZoichVyFNlvhLelZS09a194B9dyoAQ/640?wx_fmt=other&wxfrom=5&wx_lazy=1&wx_co=1&tp=webp#imgIndex=0 "")  
  
点击↑  
**蓝字**  
  
关注  
**墨云安全**  
  
![图片](https://mmbiz.qpic.cn/mmbiz_gif/7QRTvkK2qC64Jegp0lgLDGafrdvU8MV80FwNQPMbnsrL7uZZicQcmtDkwNu5wflcYicoJ1OSoKLFpcH2wc15vJxw/640?wx_fmt=gif&wxfrom=5&wx_lazy=1&tp=webp#imgIndex=1 "")  
  
IBM Langflow OSS 存在一个高危认证代码注入远程代码执行漏洞（CVE-2026-17633）。该漏洞源于 Langflow OSS 在认证与代码执行模块中存在的逻辑缺陷，攻击者可通过串联  
/api/v1/auto_login  
和   
/api/v1/validate/code  
 接口，在未授权状态下获取超级管理员权限，并利用代码验证接口执行任意 Python 代码，从而完全接管服务器。CISA 已将该漏洞列入  
已知被利用漏洞  
目录（KEV），表明该漏洞已被攻击者实际利用  
。  
  
针对该高危漏洞，**墨云 VackBot虚拟黑客机器人已完成该漏洞检测能力的支****持**  
。VackBot   
以黑客攻  
击视角实现漏  
洞的真实  
性验证，可对企业内暴露的   
IBM Langflow OSS  
资  
产开  
展批量检测，识别是否受远程代码执行漏洞影响，输出风险判定结果与处置建议，帮助用户提前发现隐患，验证防护策略有效性，支撑安全团队快速完成资产风险排查工作。  
  
**漏洞概述**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/NcM654OfmXLww5l3A6oOPER4LuTMSAIBnVGQXfLia7vPTHicJGzZWK5dSjgQVE2DFYT2kcmvhvibUEeXXJaylFVoqQVia1MO40riaZQG0jibBZdaE/640?wx_fmt=png&from=appmsg "")  
  
**漏洞原理**  
  
双重缺陷叠加：从"默认登录"到"代码执行"  
  
Langflow OSS 的设计初衷是降低 AI 应用开发门槛，提供开箱即用的低代码体验。然而，这种便利性在安全层面付出了沉重代价——默认配置即安全的误区，使得两个看似独立的缺陷叠加后形成了致命的攻击链。  
  
缺陷一：自动登录接口"裸奔"  
  
Langflow 默认部署时，/api/v1/auto_login接口处于开启状态，且无需任何身份验证。任何访问者只需向该接口发送请求，即可获取一个拥有超级管理员权限的 Token。这意味着 Langflow 的认证体系形同虚设，攻击者无需爆破密码、无需社工钓鱼，即可直接获得最高权限。  
  
缺陷二：代码验证接口"来者不拒"  
  
Langflow 提供了  
/api/v1/validate/code  
接口，允许用户在保存自定义组件之前检查 Python 代码是否合法。该接口的设计初衷是"只验证不执行"，但实际实现中却使用了 Python 的   
exec()  
 函数来解析用户提交的代码——  
代码验证即代码执行  
，且没有任何沙箱隔离或危险函数过滤。  
  
**漏洞复现**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/NcM654OfmXKxUTGcVTnnC7M3vtxFegnSvRCgicPDytq7sUzyaY0pcOCTBlv9lPGu0qVIH3uWU0ooL0Kg6UKT8uBOVfFOQxSZOeGiaOibBEDje4/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/NcM654OfmXIQ7pM8k8XJxSES6t8P9oCibkm4vpNU3ibU9Y5SM5DPEcTFB3rcxhff0yw48cRIAYuhwH0nft3DoibtkAtHBHqZ9ib0YgYQKUwZYPA/640?wx_fmt=png&from=appmsg "")  
  
**破坏性分析**  
  
该漏洞的 CVSS 评分估算为   
9.1  
，属于  
满分级  
远程代码执行漏洞，其破坏性体现在以下几个维度  
：  
  
零门槛利用  
：  
无需认证、无需特殊网络条件、无需用户配合，两条 HTTP 请求即可完成利用，攻击门槛极低  
。  
  
服务器完全接管  
：  
以 Langflow 进程权限执行任意系统命令，可直接实现反弹 Shell、文件读写、进程操控等操作，服务器彻底沦陷  
。  
  
敏感信息全面泄露  
：  
Langflow 通常存储着 API Key、数据库凭证、向量数据库连接信息、云平台凭据等敏感配置，攻击者可通过 RCE 直接读取或篡改这些数据  
。  
  
AI 应用供应链攻击跳板  
：  
Langflow 作为 AI 应用构建的核心平台，攻击者可通过篡改 AI 工作流组件代码，植入持续性数据窃取逻辑，长期监控 AI 交互内容，甚至污染下游所有依赖该平台的 AI 应用  
。  
  
**在野利用态势**  
  
CISA 已将该漏洞纳入"已知被利用漏洞"（KEV）目录，表明该漏洞已被攻击者实际利用。自漏洞细节公开以来，攻击者已迅速将矛头指向那些未更改默认配置的部署系统  
  
**修复建议**  
  
官方已发布  
安全补丁，请及时  
更新至最新版本：  
  
Langflow OSS / IBM Langflow Desktop  
：  
升级至   
1.10.1  
 或更高版本（最新版为   
1.11.2  
）  
。  
  
升级命令  
：  
pip install --upgrade langflow>=1.10.1  
  
![图片](https://mmbiz.qpic.cn/mmbiz/cZV2hRpuAPgyGRhyoqbTupN7lM2NSVJqkaFQzA59F6kiblIQsL175lxIVZbSLrFDFicibxXiaXpXmAGkrGNSib76Ylw/640?wx_fmt=other&wxfrom=5&wx_lazy=1&wx_co=1&tp=webp#imgIndex=3 "")  
  
  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/asLg7via5ibAkf1mRkpS4IuZibZE5eeC0t8nibIZBfZEekibOEZVWyf9jHzIVvT2sTzKS1OtZzSBErxJUZXD1AwAAWw/640?wx_fmt=other&wxfrom=5&wx_lazy=1&wx_co=1&tp=webp#imgIndex=4 "")  
  
往期回顾  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/icjDF5uGXY5ibE0P0Mtzns3KNb5hsCIKPfMIRultHDbmzgJcDaibI4wNKM6ZloyGRtRovyXtVdv3SuuVOcmA8gn8A/640?wx_fmt=other&wxfrom=5&wx_lazy=1&wx_co=1&tp=webp#imgIndex=5 "")  
  
# AI红客机器人重磅更新，全面覆盖OpenClaw安全检测  
# 墨云科技AI红客机器人亮相香港世界青年科学大会  
# Black Hat Asia 2026 | 墨云AI红客团队闪耀亮相  
  
  
让网络攻防更智能  
  
![图片](https://mmbiz.qpic.cn/mmbiz_gif/CD1iaLIMEhibPv9rc3gdLj3g6fiaAcCZqIicylIMVKlbvd5ic5usJ2oia9cTgavs6BwQpEEYbfglc82kCJ0Qic3OHMEaw/640?wx_fmt=gif&wxfrom=5&wx_lazy=1&tp=webp#imgIndex=6 "")  
  
  
  
点击  
**转发**  
  
分享给小伙伴  
  
  

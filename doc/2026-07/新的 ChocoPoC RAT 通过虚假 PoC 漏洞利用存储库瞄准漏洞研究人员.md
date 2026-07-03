#  新的 ChocoPoC RAT 通过虚假 PoC 漏洞利用存储库瞄准漏洞研究人员  
原创 骨哥说事
                    骨哥说事  骨哥说事   2026-07-03 01:25  
  
#   
  
#   
  
****# 防走失：https://gugesay.com/  
  
**不想错过任何消息？设置星标****↓ ↓ ↓**  
#   
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/hZj512NN8jlbXyV4tJfwXpicwdZ2gTB6XtwoqRvbaCy3UgU1Upgn094oibelRBGyMs5GgicFKNkW1f62QPCwGwKxA/640?wx_fmt=png&from=appmsg "")  
  
## 当你下载 PoC 验证漏洞时，黑客也在"验证"你的电脑  
  
对于每一位漏洞研究员、渗透测试工程师、红队成员来说，一个新的 CVE 公布后，第一件事往往不是阅读官方公告，而是打开 GitHub 搜索：  
> **"CVE-2026-XXXX PoC"**  
  
  
大家都希望尽快拿到概念验证（PoC）代码，复现漏洞、编写扫描规则、开发检测工具，甚至完成客户的应急响应。  
  
然而，攻击者已经开始利用这种工作习惯。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/TKdPSwEibsZjM8oMsB4SiaZqAyaRBSpXfVZo1b9MvLhwIPyQz0xz19BWOy8LH8UgNLdBXScpD38sWr07P37sk5OXKBPRqsQoA3VOaYufcI7vs/640?wx_fmt=png&from=appmsg "")  
  
近日，安全媒体 **The Hacker News**  
 报道，一种名为 **ChocoPoC**  
 的新型远程控制木马（RAT）正在通过伪装成热门漏洞 PoC 的 GitHub 仓库传播，专门针对漏洞研究人员、渗透测试工程师和安全从业者。一旦运行这些 PoC，恶意程序便会窃取浏览器密码、Cookie、SSH 密钥、文件等敏感数据，并最终给予攻击者远程 Shell 控制权限。  
## 这次攻击，目标不是普通用户  
  
与传统恶意软件不同，ChocoPoC 的目标十分精准：  
- 漏洞研究人员  
  
- 白帽黑客  
  
- 红队成员  
  
- 渗透测试工程师  
  
- 漏洞扫描器开发者  
  
- Bug Bounty（漏洞赏金）研究员  
  
为什么攻击这些人？  
  
因为他们电脑里的"资产价值"远高于普通用户。  
  
例如：  
- 企业 VPN 凭据  
  
- SSH 私钥  
  
- 云平台 Access Key  
  
- 浏览器 Cookie  
  
- 客户渗透项目资料  
  
- 尚未公开的漏洞研究成果  
  
- 企业内网拓扑信息  
  
如果攻击者成功控制一位安全研究人员，往往能够进一步攻击其客户或企业环境，形成典型的供应链攻击。  
## ChocoPoC 是如何伪装自己的？  
  
本次攻击最巧妙的地方在于：  
  
**GitHub 上展示的 PoC 看起来几乎完全正常。**  
  
研究人员发现，这些仓库通常声称能够利用最新披露的高危 CVE，源码中的漏洞利用逻辑也没有明显异常。  
  
真正的问题隐藏在 Python 依赖中。  
  
PoC 会安装一些看似普通的第三方包，例如 **frint**  
 和 **skytext**  
。这些包表面上提供终端颜色输出等功能，实际上却包含经过编译和混淆的恶意代码。运行后，它们会进一步下载并加载 ChocoPoC RAT，而真正的漏洞利用代码仍然能够正常执行，因此很容易让使用者误以为一切正常。  
  
换句话说，攻击者没有把恶意代码直接写进 PoC，而是藏进了依赖链中，大大降低了人工审查发现异常的概率。  
## ChocoPoC 能做什么？  
  
根据 YesWeHack 与 Sekoia 的联合分析，ChocoPoC 并不是一个简单的信息窃取程序，而是一套功能完整的 Python RAT。  
  
一旦感染，它能够执行包括但不限于以下操作：  
  
**浏览器数据窃取**  
  
包括：  
- 保存密码  
  
- Cookie  
  
- 自动填充信息  
  
- 浏览器配置数据  
  
攻击者甚至可能利用有效 Cookie 绕过部分网站的登录验证。  
  
**收集系统环境**  
  
恶意程序还会获取：  
- Shell 历史记录  
  
- 网络配置  
  
- 系统信息  
  
- 正在运行的进程  
  
- 本地文件  
  
这些信息足以帮助攻击者快速了解受害者的工作环境。  
  
**远程控制**  
  
最危险的是，ChocoPoC 支持：  
- 执行系统命令  
  
- 运行 Python 代码  
  
- 上传下载文件  
  
- 持续控制受害主机  
  
研究人员还发现，其基础设施采用了较为隐蔽的通信方式，包括利用 Mapbox 数据集作为"Dead Drop Resolver"（死投递点）定位控制服务器，并结合 DNS over HTTPS 等技术提升隐蔽性。  
## 为什么这种攻击越来越成功？  
  
原因其实很简单——**时间压力。**  
  
每当重大漏洞披露时，安全行业都会进入"抢时间"模式：  
- 快速复现漏洞  
  
- 编写扫描规则  
  
- 更新 Nuclei 模板  
  
- 制作检测脚本  
  
- 完成客户应急响应  
  
攻击者正是利用这种心理，在 GitHub 上发布伪造的 PoC 仓库。  
  
由于研究人员通常只会快速浏览漏洞利用逻辑，很容易忽略：  
- requirements.txt  
  
- setup.py  
  
- pyproject.toml  
  
- pip 自动安装依赖  
  
真正的恶意代码也因此顺利进入开发环境。  
## 这已经不是第一次了  
  
事实上，利用假 PoC 攻击安全研究人员已经成为近年来越来越常见的攻击方式。  
  
The Hacker News 在报道中提到，此前已有多个类似案例：  
- 朝鲜 Lazarus 组织曾伪装成安全研究人员，通过恶意 Visual Studio 工程攻击漏洞分析人员  
  
- 2025 年曾出现伪造 Windows LDAP 漏洞 PoC 窃取研究人员数据  
  
- 另一轮攻击则利用假 CVE PoC 分发 WebRAT 木马，主要针对学生和初级安全测试人员  
  
YesWeHack 与 Sekoia 的调查还指出，ChocoPoC 背后的攻击活动可能最早可以追溯到 2025 年底，攻击者持续利用一次性 GitHub 账号和恶意 PyPI 包实施类似攻击。  
## 如何避免成为下一个受害者？  
  
对于经常分析漏洞的安全研究人员，可以养成以下几个习惯：  
- **不要直接在本机运行未知 PoC**  
，优先使用虚拟机、容器或隔离实验环境  
  
- **重点检查依赖文件**  
，包括 requirements.txt  
、setup.py  
、pyproject.toml  
 等，警惕陌生依赖包  
  
- **优先选择可信来源**  
，如官方安全团队、长期维护的开源项目或知名研究机构发布的 PoC  
  
- **监控异常网络行为**  
。正常 PoC 很少会主动连接多个外部服务器，如果运行过程中出现未知下载、远程连接或计划任务创建，应立即停止分析  
  
- **遵循"零信任"原则**  
。不要因为仓库名称包含热门 CVE 或拥有一定 Star 数，就默认其安全可信  
  
## 结语  
  
过去，安全研究人员最担心的是漏洞利用失败。  
  
如今，更值得担心的是：  
  
**你下载的漏洞利用代码，本身就是攻击工具。**  
  
ChocoPoC 再次提醒整个安全行业：攻击者已经开始利用漏洞披露后的"时间窗口"，专门针对那些负责分析漏洞的人发起攻击。  
  
未来，PoC 不再只是验证漏洞的工具，也可能成为供应链攻击的新入口。  
  
对于每一位安全从业者来说，一个原则值得牢记：  
> **任何来自互联网的 PoC，都应默认不可信；真正值得信任的，永远是经过完整审计后的代码。**  
  
### 参考资料  
1. New ChocoPoC RAT Targets Vulnerability Researchers via Fake PoC Exploit Repos（2026-07-02）  (The Hacker News)  
  
1. 《Don't eat the ChocoPoCs! How vulnerability researchers were repeatedly targeted by trojanised exploits》  (yeswehack.com)  
  
1. New ChocoPoC malware targets researchers via trojanized PoC exploits (bleepingcomputer.com)  
  
- END -  
  
**感谢阅读，如果觉得还不错的话，动动手指给个三连吧～**  
  

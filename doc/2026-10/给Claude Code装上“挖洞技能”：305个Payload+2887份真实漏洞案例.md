#  给Claude Code装上“挖洞技能”：305个Payload+2887份真实漏洞案例  
原创 菜狗
                    菜狗  只会看监控的实习生   2026-10-08 08:45  
  
# src-hunter：给 Claude Code 加上一套 SRC 漏洞挖掘技能库  
  
最近在 GitHub 上看到一个比较实用的 Claude Code Skill：**src-hunter**  
。  
  
这个项目主要面向 **SRC、众测、Bug Bounty 和渗透测试**  
场景，把常见的漏洞挖掘方法、真实漏洞案例、Payload 以及测试流程整理到了 Claude Code 里面。  
  
简单来说，就是让 Claude Code 按照安全测试人员平时做 SRC 的思路来推进漏洞挖掘。  
  
项目地址：  
  
https://github.com/MyuriKanao/src-hunter-skill  
## 一、src-hunter 是干什么的？  
  
这个项目的核心流程比较简单：  
  
**目标确认 → 信息收集 → 资产枚举 → 漏洞测试 → 报告整理**  
  
对应到项目里面就是：  
```
intake → recon → enum → hunt → report

```  
  
也就是说，不是让 Claude 上来就开始猜漏洞，而是先确认测试范围，再做信息收集和资产分析，最后进入漏洞测试和报告阶段。  
  
项目主要针对黑盒测试场景设计。  
  
默认情况下，你可以理解成：  
  
**我手里只有一个 URL，接下来应该怎么开始挖？**  
  
src-hunter 就是围绕这个场景来组织内容的。  
## 二、里面都有什么？  
  
项目目前整理了不少安全测试资料。  
  
包括：  
  
**19 类攻击 Playbook**  
  
**305 个结构化 Payload**  
  
**263 个 WAF / EDR 绕过变体**  
  
**2887 份 HackerOne 已披露案例**  
  
**88,636 条 WooYun 历史案例统计**  
  
**国产组件指纹和默认凭据**  
  
这些内容都被重新整理到了项目自己的目录结构里。  
## 三、19 类漏洞 Playbook  
  
项目里面比较核心的就是 Playbook。  
  
每一类漏洞基本都有对应的测试思路和案例。  
  
目前包括：  
  
**IDOR / 越权 / 任意账号 / 提权**  
  
**RCE / 反序列化 / SSTI / XXE**  
  
**XSS**  
  
**信息泄露**  
  
**OAuth / SAML / JWT**  
  
**逻辑漏洞 / CSRF / 点击劫持 / 支付**  
  
**目录穿越 / LFI / RFI**  
  
**SQL 注入**  
  
**DoS**  
  
**SSRF / Cache / Host**  
  
**未授权访问**  
  
**HTTP Smuggling / CRLF**  
  
**REST API / WebSocket**  
  
**文件上传**  
  
**Android / iOS**  
  
**竞态条件**  
  
**LLM Prompt Injection**  
  
**GraphQL**  
  
以及内网和后渗透相关内容。  
  
其中部分 Playbook 还会关联对应的 HackerOne 公开案例。  
  
这样在测试的时候，可以直接参考以前真实漏洞报告的思路。  
## 四、它不是单纯堆 Payload  
  
这一点我觉得是这个项目比较值得看的地方。  
  
很多漏洞字典的使用方式比较简单：  
  
**漏洞类型 → Payload → 打一下 → 看结果**  
  
src-hunter 的 Playbook 则会继续往后走。  
  
比如：  
  
**去哪里找入口**  
  
**应该测试什么参数**  
  
**使用什么 Payload**  
  
**需要观察什么响应**  
  
**怎么判断漏洞是否成立**  
  
**漏洞成立以后怎么判断影响**  
  
**哪些操作不能继续做**  
  
所以它更像是一套漏洞测试流程，而不是单纯的 Payload 集合。  
## 五、真实 HackerOne 案例  
  
项目里面还整理了 **2887 份 HackerOne 已披露 High / Critical 案例**  
。  
  
这些案例按照漏洞类型进行了整理。  
  
比如：  
  
IDOR  
  
RCE  
  
XSS  
  
SQLi  
  
SSRF  
  
信息泄露  
  
OAuth  
  
逻辑漏洞  
  
等等。  
  
对于做 SRC 的人来说，这种案例库还是比较有价值的。  
  
因为很多时候漏洞类型本身并不难理解，真正难的是：  
  
**这个漏洞应该从哪里找？**  
  
**别人是怎么利用的？**  
  
**什么样的影响才值得提交？**  
  
真实案例可以拿来做参考。  
## 六、WooYun 历史案例  
  
项目还整理了一部分 WooYun 的历史数据。  
  
README 中提到的规模是 **88,636 条案例统计残余**  
。  
  
这里并不是把整个历史漏洞库直接搬过来，而是保留了一些：  
  
**参数出现频率**  
  
**案例 ID**  
  
**绕过方式**  
  
等统计信息。  
  
对于做国产 Web 系统、OA、中间件测试的人来说，这类历史数据还是比较有参考价值的。  
## 七、目录结构  
  
项目的目录也比较清楚：  
```
references/
├── methodology/
├── playbooks/
├── industry/
├── dictionaries/
├── templates/
├── h1-reports/
└── payloader/

```  
  
分别对应：  
  
**methodology**  
  
测试方法、攻击优先级、绕过工具和证据规则。  
  
**playbooks**  
  
各种漏洞类型的测试手册。  
  
**industry**  
  
银行、金融、电信、ISP 等行业场景。  
  
**dictionaries**  
  
国产组件指纹和默认凭据。  
  
**templates**  
  
漏洞报告模板，目前使用 CVSS 4.0。  
  
**h1-reports**  
  
HackerOne 已披露漏洞报告。  
  
**payloader**  
  
Payload、WAF / EDR 绕过方式以及工具命令。  
## 八、还集成了 MCP  
  
src-hunter 后面的版本还加入了 MCP 工具层。  
  
目前主要使用 **jshookmcp**  
。  
  
可以给 Claude 提供：  
  
浏览器自动化  
  
CDP 调试  
  
网络拦截  
  
JS Hook  
  
AST 反混淆  
  
Frida 内存验证  
  
WASM 逆向  
  
Source Map 重构  
  
Android ADB  
  
SSL Pinning 绕过  
  
等能力。  
  
这样 Claude 在进行漏洞测试的时候，就不只是查知识库，还可以结合实际工具进行分析。  
  
项目 README 中目前给出的 jshookmcp 版本为 **0.3.0**  
，包含 134 个精选工具、386 个完整工具以及 36 个功能域。  
## 九、怎么安装？  
  
如果你使用 Claude Code，可以直接通过 Marketplace 安装：  
```
/plugin marketplace add MyuriKanao/src-hunter-skill
/plugin install src-hunter@src-hunter

```  
  
也可以直接 Clone：  
```
git clone https://github.com/MyuriKanao/src-hunter-skill.git ~/.claude/skills/src-hunter

```  
  
安装完成之后，可以直接调用：  
```
/src-hunter <target>

```  
  
Marketplace 版本则可以使用：  
```
/src-hunter:src-hunter <target>

```  
  
项目也支持通过一些关键词自动触发，比如：  
  
**Bug Bounty**  
  
**HackerOne**  
  
**SRC 挖洞**  
  
**漏洞赏金**  
  
**WAF Bypass**  
  
**任意账号**  
  
**密码重置**  
  
**默认凭据**  
  
**Actuator**  
  
等。  
## 十、一个比较重要的地方：测试边界  
  
这个项目里面专门强调了测试边界。  
  
比如 SQL 注入，证明数据库名称或者版本就可以，不需要继续 Dump 数据。  
  
IDOR、MongoDB、Elasticsearch 这类问题，只需要取少量样本证明即可。  
  
如果测试越权、密码重置、JWT 伪造等问题，使用自己注册的账号进行验证。  
  
如果拿到了 RCE，也只执行：  
```
id
whoami
uname -a

```  
  
这类低风险命令进行证明。  
  
简单来说就是：  
  
**证明漏洞存在就停，不要为了证明影响而把测试目标打穿。**  
  
对于 SRC 和 Bug Bounty 来说，这个思路还是比较重要的。  
## 十一、适合什么人？  
  
这个项目比较适合：  
  
**SRC 漏洞挖掘**  
  
**Bug Bounty**  
  
**众测**  
  
**Web 渗透测试**  
  
**红队测试**  
  
**Claude Code 用户**  
  
尤其是平时已经在使用 Claude Code 做安全测试的人，可以研究一下它的 Playbook 是怎么组织的。  
  
相比直接让 AI：  
> 帮我找一下这个网站有没有漏洞。  
  
  
这种方式，src-hunter 把整个测试过程拆得更加细。  
  
从目标确认开始，一直到漏洞验证和报告输出，都有对应的内容。  
## 十二、项目目前的状态  
  
需要注意的是，这个仓库已经在 **2026 年 7 月 20 日归档**  
，目前处于只读状态。GitHub 页面显示项目有 633 个 Star、135 个 Fork。  
  
目前最新 Release 是：  
  
**src-hunter v1.2.0**  
  
这个版本主要做了 Playbook 的渐进式拆分，把一些体积比较大的漏洞知识拆成多个子文件，减少 Claude 一次性读取大量内容造成的上下文占用。  
## 十三、最后  
  
src-hunter 这个项目有意思的地方，不只是里面放了多少 Payload。  
  
更值得看的其实是它把：  
  
**漏洞案例**  
  
**漏洞测试方法**  
  
**Payload**  
  
**工具**  
  
**证据**  
  
**报告**  
  
这些东西全部放到了一套 Claude Code Skill 里面。  
  
对于做 SRC、众测或者 Web 渗透的人来说，可以把它当成一个比较完整的安全测试知识库来研究。  
  
如果你最近也在研究 **Claude Code + 网络安全**  
，这个项目可以收藏一下。  
  
**项目地址：**  
  
https://github.com/MyuriKanao/src-hunter-skil  
  
  

#  HexStrike AI：127个工具+MCP协议，我把Kali变成了AI驱动的攻击平台，但它把自己也变成了漏洞  
原创 KLSEC
                    KLSEC  昆仑AI安全实验室   2026-09-30 16:21  
  
一个AI Agent，127个安全工具，12个自主代理，通过MCP协议直接操控Kali Linux上的Nmap、SQLMap、Nuclei和Metasploit。  
  
这是HexStrike AI在GitHub上宣称的能力。2026年9月，当我第一次把它装进Claude Desktop，看着AI自己决定先跑Nmap还是先跑Subfinder、自己根据扫描结果选择下一步工具的时候，我确实愣了几秒。  
  
但同一周，安全研究社区披露了HexStrike AI自身的三个CVE。其中一个是远程命令注入，攻击者可以**利用这个AI攻击平台，反过来攻击运行它的服务器**  
。  
## 它到底是什么：MCP服务器，不是“AI渗透工具”  
  
HexStrike AI的准确定位是：**一个Model Context Protocol（MCP）服务器**  
。它本身不“挖漏洞”，它做的事情是把Kali Linux上127个安全工具的能力，通过MCP协议暴露给任何兼容的AI Agent。  
  
它的架构分三层。最上面是AI Agent层，支持Claude Desktop、Cursor、VS Code Copilot、Roo Code、5ire等MCP客户端。中间是HexStrike MCP Server，包含智能决策引擎、12个自主Agent、可视化引擎和BOAZ载荷引擎。最下面是工具执行层，127个安全工具按类别划分：网络侦察10个、Web应用安全19个、密码与认证5个、二进制分析与逆向工程13个、取证与CTF 16个、云安全、OSINT等。  
  
其中53个工具在安装时自动部署，剩余74个需要手动安装（因为许可证、特殊依赖或平台限制）。安装完成后，AI Agent可以通过自然语言指令调用这些工具——你说“扫描这个目标”，Agent自己决定用Nmap还是Masscan，自己判断扫描参数，自己根据结果决定下一步。  
  
这个设计解决了一个真实痛点：**传统用AI做安全测试，最大的摩擦不在模型能力，在“AI不知道怎么调用工具”**  
。你写一段Prompt让AI跑Nmap，它给你一段Nmap命令，你复制粘贴到终端，跑完把结果贴回对话框。HexStrike AI把这条链路打通了——AI直接执行工具，直接读取结果，直接决定下一步。  
## 实战：从“帮我扫一下”到拿到Shell  
  
我在授权靶场环境里跑了一遍完整流程。  
  
**第一步，配置。**  
 在Kali上安装HexStrike：sudo apt install hexstrike-ai  
。安装完成后启动服务器：hexstrike_server --port 8888  
。然后在Claude Desktop里配置MCP连接，指向http://127.0.0.1:8888  
。  
  
**第二步，下达指令。**  
 我在Claude对话框里输入：“对192.168.1.0/24进行网络发现和漏洞评估，目标是找到可利用的入口。”  
  
**第三步，观察AI自主执行。**  
 Claude通过MCP连接到HexStrike，智能决策引擎开始工作。它先调用Subfinder和Amass做子域枚举，然后调用Nmap做端口扫描，根据扫描结果识别出目标主机开放了80端口和8080端口，接着自动调用WhatWeb做技术栈指纹识别，发现8080端口运行的是Spring Boot。决策引擎判断Spring Boot应用可能存在Actuator端点泄露，自动调用Nuclei扫描Actuator相关模板。发现/actuator/env  
未授权可访问，提取出环境变量中的数据库连接串。AI继续调用SQLMap对数据库进行验证，确认可连接后生成完整报告。  
  
整个过程中，我只输入了一句话。AI自己选择了工具、调整了参数、串联了攻击链。  
  
**第四步，BOAZ载荷引擎。**  
 HexStrike AI v6.0 fork版本集成了BOAZ红队载荷引擎，包含77个进程注入加载器、12种编码方案和EDR/AV绕过能力。在实际红队场景中，AI可以在拿到初始立足点后，自动生成经过BOAZ处理的载荷，用于横向移动和权限维持。BOAZ的工作流是：MSFVenom生成载荷→熵值分析→BOAZ规避层处理→企业级隐蔽二进制。  
## 最讽刺的部分：HexStrike AI自己有三个CVE  
  
2026年9月14日，HexStrike AI披露了三个CVE。这三个漏洞的讽刺之处在于：**一个用来攻击别人的平台，自己成了被攻击的目标。**  
  
**CVE-2026-90619（CVSS 7.3）——Execute端点命令注入。**  
 HexStrike的Execute端点接收code/script  
参数，直接传递给系统执行。攻击者可以构造恶意请求，在运行HexStrike的Kali服务器上执行任意命令。  
  
**CVE-2026-90620——API Command端点缺失认证。**  
 API Command端点没有认证机制，任何能访问HexStrike API端口（默认8888）的人都可以调用命令执行功能。  
  
**CVE-2026-90690——Tools端点命令注入。**  
 Tools端点的additional_args/target/username/password/scan_type/payload  
参数全部可被操纵，导致操作系统命令注入。攻击可以远程发起，且PoC已公开。  
  
**这意味着什么？**  
 如果你把HexStrike AI的API端口暴露在公网上，攻击者可以直接调用它的工具执行功能，在你的Kali服务器上为所欲为。一个安全工具本身成为攻击入口，这是2026年AI安全工具生态里最需要警惕的模式。  
## 它真正解决了什么，以及它没解决什么  
  
**它解决了：工具调用的摩擦。**  
 AI直接操作Kali工具链，不需要你在终端和对话框之间来回切换。智能决策引擎根据目标类型自动选择工具和参数，省去了“下一步该跑什么”的犹豫。  
  
**它解决了：攻击链的自动化串联。**  
 从侦察到漏洞发现到利用验证，AI自主编排工具调用顺序。你不需要手动把Nmap的结果导入Nuclei，把Nuclei的结果导入SQLMap。  
  
**它没有解决：判断力。**  
 AI决定用哪个工具，但“这个发现是不是漏洞”仍然需要人判断。HexStrike的BugBounty Agent可以自动执行侦察和漏洞狩猎，但它无法判断一个total  
字段从2变成34000意味着什么，无法理解业务逻辑漏洞。  
  
**它没有解决：自身安全。**  
 三个CVE说明了一个残酷的现实：AI安全工具的攻击面，比传统安全工具更大。因为它把工具执行能力暴露给了AI Agent，而AI Agent的输入通道（聊天框、API端点）本身就是攻击面。一个提示注入，可能让AI Agent执行非预期的工具调用。一个未认证的API端点，可能让攻击者直接操控你的Kali服务器。  
## 适合谁，不适合谁  
  
**适合：**  
 已经在用Claude Desktop或Cursor做安全测试的人。HexStrike的MCP集成让AI从“顾问”变成“操作员”。做CTF和靶场的人。CTF Solver Agent可以自动解题。做红队演练的人。BOAZ载荷引擎提供了EDR绕过能力。  
  
**不适合：**  
 把API端口暴露在公网的人。三个CVE已经说明了后果。指望“一键挖洞”的人。HexStrike加速的是工具调用，不是漏洞判断。不做授权测试的人。它的所有能力都建立在“你已经有合法授权”的前提上。  
## 一句实在话  
  
HexStrike AI是2026年AI安全工具生态里一个标志性项目。它证明了MCP协议可以把AI从“聊天机器人”变成“工具操作员”。但它的三个CVE也证明了一件事：**AI安全工具需要被当作攻击面来对待。**  
  
装HexStrike之前，先确认你的API端口没有暴露在公网。装完之后，先确认AI Agent的指令通道没有被注入。然后再去考虑怎么用它扫描别人的系统。  
  
**一个能攻击别人的平台，首先得确保自己不会被攻击。**  
  
**严正声明**  
  
HexStrike AI采用MIT许可证，安装和使用请遵循项目开源协议。CVE-2026-90619、CVE-2026-90620、CVE-2026-90690已由上游确认并发布修复，请更新至最新版本。本文所述所有测试均在授权靶场环境内进行。HexStrike AI及BOAZ载荷引擎仅可用于**你拥有或已获得明确书面授权**  
的安全测试、红队演练和CTF竞赛。未授权扫描、测试、攻击行为均属违法。AI工具本身不创造授权，授权是你的责任。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/3oR6eMARh6yozmVLFlzjN4SmK00q4cYcFSuCvK02oCdQf5yN1t28Xu1WElCHuwPiadWWcpicicicravc0vgw4UlaLghg8Q9fASROw75M82fbHKc/640?wx_fmt=png&from=appmsg "")  
  
  

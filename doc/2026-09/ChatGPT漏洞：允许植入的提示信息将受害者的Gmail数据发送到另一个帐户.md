#  ChatGPT漏洞：允许植入的提示信息将受害者的Gmail数据发送到另一个帐户  
 SecHub网络安全社区   2026-09-09 06:41  
  
****  
****  
****  
**点击蓝字 关注我们**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/8icWLyUKibZZrPdaxnm18Zscp6Xcu0OiaMwuh8LP87lPQLxMwiceAsv3TurmE7zZOulOhMELnQ2OulwFIJkbmB3bRg/640?wx_fmt=png "")  
  
  
**免责声明**  
  
本文发布的工具和脚本，仅用作测试和学习研究，禁止用于商业用途，不能保证其合法性，准确性，完整性和有效性，请根据情况自行判断。  
  
如果任何单位或个人认为该项目的脚本可能涉嫌侵犯其权利，则应及时通知并提供身份证明，所有权证明，我们将在收到认证文件后删除相关内容。  
  
文中所涉及的技术、思路及工具等相关知识仅供安全为目的的学习使用，任何人不得将其应用于非法用途及盈利等目的，间接使用文章中的任何工具、思路及技术，我方对于由此引起的法律后果概不负责。  
## 🌟简介                            
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/KZCmibwhia3VUS9m04TYuZWXzE6eqib6mqlibmiasyrB0JJIRlxYWvOowD0e1yUrGlP2Kcv4BFoKgRwFXbnzFNrKFumHd3SteC4UqzpicCWpHEXnE/640?wx_fmt=png&from=appmsg "")  
### 一、事件概述  
  
    Check Point Research 在今天发布的一份报告中表示，在 ChatGPT 对话中植入一条指令，就可能导致 ChatGPT 在像往常一样回答用户问题的同时，悄悄地为攻击者工作。  
  
    在该公司的概念验证中，这项隐藏功能会读取用户已连接的 Gmail 帐户中的数据，并通过两个帐户之间的隐藏通道将其传递给另一个 ChatGPT 帐户。用户看到的回复中对此只字未提。  
### 二、具体流程  
  
    分析报告显示同一渠道还可以复制聊天记录和对话中的文件。攻击者能够窃取多少信息取决于会话已经能够访问的内容，包括其数据、工具、其他连接的应用程序和权限。  
  
    在所有操作生效之前，对话中必须包含指令。Check Point 列出了三种方法：用户粘贴提示信息、用户打开共享的 ChatGPT 对话，或者使用自定义 GPT，将指令包含在其构建器指令中（该指令不会显示给用户）。  
  
    之后，只需一条普通消息即可启动它。Check Point 编写了指令，使得 ChatGPT 在思考模式下，可以在同一回合内运行两个工作流。  
  
    ChatGPT 回答了用户的问题。同时，它检查了一个隐藏邮箱，寻找攻击者发送的任务，并使用用户会话中的工具执行了该任务，然后将结果发送了回去。指令指示模型将这两个数据流分开，因此隐藏的任务不会出现在可见的回答中。  
  
  
    唯一表明应用已被使用的迹象是答案上方显示的“已与 Gmail 对话”的小标签。它记录了一次已经发生的阅读，并且没有给用户允许或拒绝的机会。  
  
    由于联网应用的默认工作方式，ChatGPT 不会事先询问用户。OpenAI 的文档将“重要操作”列为默认权限，这允许 ChatGPT 无需提示即可从应用读取数据。ChatGPT 仅在执行可能对 ChatGPT 外部产生实际影响、泄露敏感信息或难以撤销的操作之前才会询问用户。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/KZCmibwhia3VWDtZdIgScibPLIPvibRooY965YYYIgyicJNianrufGXbYHeJwKz6w3Pujvjzo6dDOMMzLvjPGpgLSndgRoeDDoNEm64hU3tDWWichY/640?wx_fmt=png&from=appmsg "")  
  
  
    如果用户希望每次都收到询问，可以切换到“始终询问”模式。在商业版、企业版和教育版工作区中，管理员可以选择每个应用可以执行哪些操作以及哪些用户可以使用它。商业版计划默认启用应用，企业版和教育版计划默认禁用应用。  
  
    Check Point表示已将此发现告知OpenAI，OpenAI也确认该频道背后的内部服务已离线。用户无需安装任何更新。  
  
    该通道运行于ChatGPT运行代码的容器之间。当任务需要时，ChatGPT会为每个对话创建一个通道。  
  
    OpenAI 的文档指出，ChatGPT 用于数据分析的 Python 环境无法向网络或外部 API 发出请求。Check Point 也表示，为不同对话（包括不同账户下的对话）构建的容器之间也没有直接的通信路径。  
  
    它们都可以访问同一个内部服务。ChatGPT 有时需要安装额外的 Python 或 npm 包。为了避免容器直接访问公共包仓库，每个容器都只能与内部的 JFrog Artifactory 实例通信，由该实例获取所需的包。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/KZCmibwhia3VVfSDnwr6BL8gArSqJMSaAqYKX3qqicLK88Dib9qyugia7XX5IsUXNfg89su1DLJDWJ76XqL0cgFKxW9G2n7kc7qq16icDpcaNZq0E/640?wx_fmt=png&from=appmsg "")  
  
  
    该实例允许容器将名为“属性”的命名值附加到存储的文件，并读取这些属性。容器持有的读取权限凭据也足以写入这些属性。这些属性存储在环境变量中，ChatGPT 运行的代码可以从中获取它们。代码无需窃取单独的密钥或提升权限。  
  
    这些属性并未按账户分开保存。Check Point 在一个账户下的容器中，将一个名为 chatgpt_test_ts 的属性（包含当前时间）附加到一个缓存文件中。在另一个账户下的对话中，它请求该文件的属性，却收到了相同的名称和值。  
  
    属性可以包含纯文本或 Base64 编码，任何过大的数据都可以拆分成多个文件，并在另一端重新组装。这使得软件包服务的元数据变成了原本不应该相互访问的容器之间的共享剪贴板。  
  
    Check Point 将其工作日期标注为 2026 年 6 月，且未说明该通道何时停止工作，因此该报告未显示其开放了多长时间。  
  
  
  
  
欢迎关注SecHub网络安全社区，SecHub网络安全社区目前邀请式注册，邀请码获取见公众号菜单【邀请码】  
  
**#**  
  
  
**企业简介**  
  
  
**赛克艾威 - 网络安全解决方案提供商**  
  
****  
       北京赛克艾威科技有限公司（简称：赛克艾威），成立于2016年9月，提供全面的安全解决方案和专业的技术服务，帮助客户保护数字资产和网络环境的安全。  
  
  
安全评估|渗透测试|漏洞扫描|安全巡检  
  
代码审计|钓鱼演练|应急响应|安全运维  
  
重大时刻安保|企业安全培训  
  
![](https://mmbiz.qpic.cn/mmbiz_png/8icWLyUKibZZrPdaxnm18Zscp6Xcu0OiaMwuh8LP87lPQLxMwiceAsv3TurmE7zZOulOhMELnQ2OulwFIJkbmB3bRg/640?wx_fmt=png "")  
  
  
**联系方式**  
  
电话｜16637726088  
  
官网｜https://sechub.com.cn  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/MVPvEL7Qg0FW5uwU0BZtn2lmMrLPwpibCeCVbtBFDRkbFb7n7ibhPRxg20spUo9mUIiakmRYABB88Idl81IpGuXfw/640?wx_fmt=gif "")  
  
**关注我们**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/SUZ43ICubr4mWJcUARDKYbQooQjbjbmqZTerAIXqDX9CaVxXbB7pyWwnMRklrCJias9r59PhnJAxZ4e3gYjyqVQ/640?wx_fmt=png "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/SUZ43ICubr4mWJcUARDKYbQooQjbjbmqZTerAIXqDX9CaVxXbB7pyWwnMRklrCJias9r59PhnJAxZ4e3gYjyqVQ/640?wx_fmt=png "")  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/8icWLyUKibZZrPdaxnm18Zscp6Xcu0OiaMwyhlWCYDVqK38BA5dbjKkH7icWmAew7SYRA7ao1bFibialrMvmQ9ib0TBvw/640?wx_fmt=jpeg "")  
  
  
**公众号：**  
sechub安全  
  
**哔哩号：**  
SecHub官方账号  
  
  
  

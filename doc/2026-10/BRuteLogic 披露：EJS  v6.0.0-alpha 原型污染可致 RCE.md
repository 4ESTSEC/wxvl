#  BRuteLogic 披露：EJS < v6.0.0-alpha 原型污染可致 RCE  
 Ots安全   2026-10-08 07:11  
  
**威胁简报**  
  
  
**恶意软件**  
  
  
**漏洞攻击**  
  
## 导语  
  
2026 年 10 月 7 日，知名 Web 安全研究员 **BRuteLogic（Rodolfo Assis）**  
 在 X（原 Twitter）上发布一条简短但杀伤力极强的披露：  
> Node.js RCE via EJS (<v6.0.0-alpha)  
  
Unsafe merge - Prototype Pollution leading to RCE via template rendering.  
  
  
配图是一张实验截图：左侧是一段存在 unsafeMerge  
 递归合并漏洞的 Express 服务端代码，右侧终端里同一个 /api/status  
 接口先是正常返回 <h1>Hello Guest</h1>  
，在攻击者向 /api/config  
 投递一个含 __proto__  
 键的 JSON 后，再次访问即输出 id  
 命令的结果——整个服务器被“远程命令执行”了。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/zNsFJyIuL0HaGIKwD6MZwPmjOiaE6m9BkZCPqGicbWaHuD3FdvTIhwhydYj8eiblwWxO2SuPicbChKbKcbAO5p8DnziaPntSztpFsGCEStC0lcX0/640?wx_fmt=jpeg&from=appmsg "")  
  
这不是新漏洞类型，却是一个值得警惕的**版本边界提醒**  
：研究者认为，EJS 官方直到 **v6.0.0-alpha**  
 才真正补上这个原型污染→RCE 的利用面，意味着大量仍在使用 3.x/4.x/5.x 的 Node.js 应用可能仍在风险之中。  
## 一、事件概述：一张图里有什么  
  
推文附图可拆解为三部分：  
  
**① 漏洞服务端（server.js）**  
- unsafeMerge(target, source)  
：递归合并函数，未对 __proto__  
、constructor  
、prototype  
 做过滤；  
  
- POST /api/config  
：把请求体 req.body  
 直接合并进内部配置对象；  
  
- GET /api/status  
：调用 res.render('index', { user: 'Guest' })  
 渲染 EJS 模板。  
  
**② 普通模板（views/index.ejs）**  
```
<h1>Hello <%= user %></h1>
```  
  
这个模板本身没有 SSTI 风险，但 EJS 渲染时读取的某些**选项**  
会被污染。  
  
**③ 攻击结果**  
- 攻击者先 POST 一个 JSON 到 /api/config  
；  
  
- 再正常 GET /api/status  
；  
  
- 返回页面里出现 id  
 命令输出，证明命令执行成功。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/zNsFJyIuL0EYXIbEaNWHPuOtkvgevBPR9RXSc4Mj5dEKXL9zviaZOpia3ktQicPU4jmgGZTFezib5Kicd87tYHWAAFv1BPYYYibScI9mmThOzbicRg/640?wx_fmt=png&from=appmsg "")  
## 二、核心原理：不安全的合并 + EJS 选项继承  
### 2.1 不安全的递归合并  
  
服务端的核心问题不在 EJS，而在**应用自己的合并函数**  
。简化逻辑如下：  
  
```
function unsafeMerge(target, source) {
  for (let key insource) {
    if (typeof source[key] === 'object') {
      if (!target[key]) target[key] = {};
      unsafeMerge(target[key], source[key]);
    } else {
      target[key] = source[key]; // X
    }
  }
  return target;
}
```  
  
  
当攻击者发送：  
  
```
{
  "__proto__":{
    "client":true,
    "escapeFunction":"function(){ /* 攻击者控制的代码逻辑 */ }"
  }
}
```  
  
  
target[key] = source[key]  
 中的 key  
 为 __proto__  
 时，**所有对象的默认原型**Object.prototype  
 被改写。这不是普通的对象属性赋值，而是直接污染了原型链。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/zNsFJyIuL0GdF4cIgUYkx2Sia7hiaSACcYDdcxGibyDyJ8EIVnIuJJZDQlwSvGiauJfM1RUFib1Xrvsg8DkqOxkunHqCiaYgGmPfG5ibvHhMVkzz7k/640?wx_fmt=png&from=appmsg "")  
### 2.2 EJS 如何利用被污染的选项  
  
EJS 在渲染模板时会读取多个选项，其中与本链相关的有：  
- opts.client  
：决定模板是否按客户端模式编译；  
  
- opts.escapeFunction  
：被拼接到生成的模板函数源码中，作为 HTML 转义函数。  
  
当这些选项未在 opts  
 自身上定义时，JavaScript 会沿着原型链向上查找，于是落到被污染的 Object.prototype.client  
 和 Object.prototype.escapeFunction  
。EJS 把攻击者提供的字符串直接嵌入生成的模板函数，一旦该函数被执行，就等同于在服务端运行任意 Node.js 代码。  
  
研究者为了验证 RCE，在 escapeFunction  
 里放置了一个会调用 child_process.spawnSync('id')  
 的函数；实际利用中，该位置可以替换为任何 Node.js 可执行代码。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/zNsFJyIuL0Ht8ePDPVfIz7qpnZMlQXje7K2wSaNmIiaebVTvOmric5WJQABKtUVSXWtdX9oaDlImGicGicoTJia2FhvAiaWhIfj5GsPIZWtA3rPvs/640?wx_fmt=png&from=appmsg "")  
## 三、影响范围与版本边界  
### 3.1 研究者声称的版本范围  
  
BRuteLogic 在推文中明确标注：  
> **影响范围：EJS < v6.0.0-alpha**  
  
  
也就是说，6.0.0-alpha 之前的所有版本（包括大量生产环境仍在使用的 3.x 系列）理论上都可能受影响。这个结论的前提条件是：**应用层存在把用户输入合并进配置对象，并最终触发 EJS 渲染的代码路径**  
。  
### 3.2 已核实的版本事实  
- **EJS 6.0.1**  
 已于 2026 年 5 月 26 日发布（npmx 数据）；  
  
- **EJS v6.0 引入了安全硬化**  
：官方文档说明，v6 在模板执行前会把 locals  
 浅拷贝到一个 **null-prototype 对象**  
，切断原型链回退，这正是针对原型污染利用的结构性修复；  
  
- **EJS 3.x 系列的安全补丁历史**  
- CVE-2022-29078（修复于 3.1.7）  
  
- CVE-2022-29869（修复于 3.1.8）  
  
- CVE-2023-29827（修复于 3.1.9）  
  
- CVE-2024-33883（修复于 3.1.10）  
  
这些 CVE 说明 EJS 维护者一直在逐步封堵原型污染 gadget。但 BRuteLogic 本次披露指出，client  
 / escapeFunction  
 这条路径直到 6.0.0-alpha 才被完全修复。  
## 四、总结与合规边界  
  
BRuteLogic 的这条推文把一个老生常谈的问题重新拉回到版本边界上：**即使模板引擎本身被认为是“安全使用”的，只要应用层把用户输入不加以区分地合并进配置或选项，原型污染仍然是进入 RCE 的高速通道**  
。  
  
EJS 6.0 用 null-prototype 对象切断这条路径，是一个结构性的改进。对于仍在 6.0 以下的项目，安全团队的优先级应该是：  
1. 清点资产中 EJS 的具体版本；  
  
1. 检查是否有把 req.body  
 / req.query  
 直接 spread 进模板选项的代码；  
  
1. 无法立刻升级时，先冻结原型、过滤危险键、加监控。  
  
  
  
**END**  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/zNsFJyIuL0EPjuf2SLWcelicsXicjUdfHo44OSzJpZmFbZZf8SRZe4z2RUF2EjvYr4EUPqmpaxyhn3Eb0CwIqELEFJEUCZNOFmiaIlj4B41riaM/640?wx_fmt=jpeg&from=appmsg "")  
  
  
公众号内容都来自国外等平台- 搜索的内容通过结合编写 -   
  
公众号 |   
AnQuan7 (Ots安全)  
  

#  API配置泄露—浅析api端口配置漏洞及危害  
原创 AzumiSaki
                    AzumiSaki  船山信安   2026-06-25 23:00  
  
## 什么是应用程序编程接口 (API)？  
  
应用程序编程接口 (API) 是一套规则，它让一个软件程序能够向另一个软件程序传输数据。  
  
API 让开发人员能够避免重复工作，根据 API 的要求设置请求格式来将现有功能纳入新的应用程序，而不是构建和重建现有应用程序功能。  
  
API 是一个“接口”，意味着一个事物与另一个事物交互的一种方式。举一个现实世界中的例子，ATM 有一个界面——一个屏幕和几个按钮，允许客户与他们的银行互动并请求服务，例如取出现金。同样，API 是一个软件与另一个程序交互以获得所需服务的方式。  
  
官方对于api定义的文档：  
https://www.cloudflare.com/zh-cn/learning/security/api/what-is-an-api/  
  
API配置泄露的本质是：**攻击者拿到了调用系统能力的“钥匙”和“说明书”**  
。它不一定只是一个Key的泄露，往往还包括环境地址、鉴权方式、回调地址、云服务凭证、数据库连接串、第三方服务密钥、权限范围、限流策略等信息。危害通常比“密码泄露”更隐蔽，因为API凭证常常可以被程序长期自动调用，而且不一定有明显登录痕迹。  
  
API配置泄露简单地说，CTF里面的.env环境配置泄露，.git文件泄露，备份文件泄露(例如www.zip)  
  
最常见也是最危险的莫过于前端JS的bundle包源码里面的key泄露  
## 案例1：Stripe Secret Key被打进前端JavaScript Bundle  
  
一个比较典型的脱敏案例是：某marketplace的**Stripe sk_live_... secret key**  
被嵌入到客户端JavaScript文件里，网站访问者只要打开DevTools，在Sources或Network里搜索sk_live_  
就能看到。报告称这个key已暴露约4个月，期间约8000人访问过该站点。  
  
它的链路大概是：  
```
Stripe secret key配到前端/低代码平台API Connector
        ↓
构建或运行时把key放进浏览器收到的JS payload
        ↓
所有访问者都能下载这份JS
        ↓
DevTools/Sources/Network中可搜索到sk_live_
        ↓
攻击者可直接拿key调Stripe API
```  
  
这个案例危险的地方在于：Stripe的**publishable key**  
可以在前端使用，但**secret key**  
不能。Stripe官方也明确提醒：如果把secret key放进框架会打包的变量里，它会被内联进JavaScript bundle或初始HTML，任何访问网站的人都能看到；不要把sk_live_  
或rk_live_  
放进会被打包的前端变量。  
  
危害包括：  
```
读取客户数据
发起退款
创建订阅或收费
修改 Connect 相关账户信息
访问交易记录
```  
  
所以这不是“泄露一个字符串”，而是把支付后台的服务端权限发给了每个浏览器。  
## 案例2：大规模研究发现，很多有效API凭证暴露在网页/JS中  
  
2026年一篇名为**Keys on Doormats: Exposed API Credentials on the Web**  
的研究扫描了1000万个网页，发现了**1748个有效API凭证**  
，分布在近1万个网页中，涉及云平台、支付服务、开发工具等14类服务商。研究摘要还提到，很多凭证暴露时间从1个月到数年不等，并且多数根因来自JavaScript环境。  
  
TechRadar对该研究的报道进一步提到，约**84%**  
的识别凭证出现在 JavaScript资源中，很多是由Webpack等构建工具生成的bundled files。  
  
典型链路就是：  
```
开发者把 secret 放进.env
        ↓
前端代码引用了这个变量
        ↓
Webpack/Vite/Next/React构建时把值替换成明文字符串
        ↓
dist/static/js/main.xxxxx.js对公网开放
        ↓
扫描器批量抓JS并匹配ke 模式
        ↓
发现可用API key
```  
  
所以，常见的api配置泄露可以总结为  
```
.env、.git、备份文件、前端 JS、Source Map、Swagger、Debug、日志、CI/CD、Docker、数据库连接串
```  
  
随后是  
GraphQL  
  
REST API通常是这样：  
```
GET /api/users/1
GET /api/orders/1001
POST /api/admin/refund
```  
  
攻击者需要猜路径、看JS、扫目录、看文档。  
  
GraphQL通常只有一个入口：  
```
POST /graphql
GET /graphql
```  
  
真正的“接口”藏在query/mutation里：  
```
query{
  user(id:"1"){
    id
    name
    email
    orders{
      id
      amount
    }
  }
}

```  
  
也就是说，GraphQL的攻击面不一定体现在URL路径上，而是体现在：  
```
schema
query
mutation
field
type
argument
resolver
authorization
```  
  
如果schema被暴露，攻击者不需要猜接口名，可以直接看到有哪些查询、哪些修改操作、哪些字段、哪些对象关系。  
  
**Introspection**  
  
**Introspection**  
是GraphQL的自省机制。简单说，它允许客户端向GraphQL服务询问：  
```
你有哪些类型？
你有哪些 Query？
你有哪些 Mutation？
每个字段需要什么参数？
每个字段返回什么类型？
哪些字段是废弃的？
```  
  
这是 GraphQL的正常特性，用于开发工具、文档生成、GraphiQL/Playground自动补全等。官方GraphQL安全文档也把introspection作为GraphQL API需要考虑的安全面之一；Apollo文档也明确指出，内省查询是了解schema的最快方式，生产环境通常需要限制或关闭。  
  
典型introspection返回的信息包括：  
```
Query:
  user(id:ID!):User
  users(filter:UserFilter):[User]
  order(id:ID!):Order
  adminUsers:[User]
  billingInfo(userId:ID!):BillingInfo
Mutation:
  updateUserRole(userId:ID!,role:String!):User
  refundOrder(orderId:ID!):RefundResult
  createApiToken(userId:ID!):ApiToken
```  
  
如果这些信息对未授权用户开放，就相当于把“接口说明书”给了攻击者。  
  
GraphQL Introspection本身通常不会直接泄露真实API Key。它更常见的危害是泄露**API结构和业务能力**  
。  
  
例如攻击者通过schema看到：  
```
type Query{
  me: User
  user(id:ID!):User
  users:[User]
  invoice(id:ID!):Invoice
  adminStats:AdminStats
}
type Mutation{
  updateUserRole(userId:ID!,role:Role!):User
  createRefund(orderId:ID!):Refund
  generateInviteCode(teamId:ID!):InviteCode
}
```  
  
这会暴露很多关键信息：  
```
有哪些对象：User、Invoice、Order、AdminStats
有哪些敏感字段：email、phone、balance、role
有哪些高危操作：refund、updateRole、deleteUser
哪些地方可能存在越权：user(id)、invoice(id)、teamId
哪些 mutation 可能改变状态
```  
# GraphQL 配置泄露常见入口  
  
实战里可以重点关注这些位置：  
```
/graphql
/api/graphql
/v1/graphql
/v2/graphql
/gql
/query
/graphiql
/playground
/graphql-playground
/console
/voyager
```  
  
前端JS里常见特征：  
```
/graphql
operationName
query:
mutation
__typename
ApolloClient
urql
Relay
graphql-tag
gql
```  
  
响应里常见特征：  
```
{
  "data": {}
}
```  
  
或者：  
```
{
  "errors": [
    {
      "message": 
"Cannot query field ..."
    }
  ]
}
```  
  
如果看到__typename  
，基本可以判断前端在使用GraphQL或GraphQL客户端缓存机制。  
  

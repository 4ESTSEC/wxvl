#  N/A | JeecgBoot 权限绕过与 SQL 注入组合漏洞解析  
alicy
                    alicy  信安百科   2026-09-27 01:00  
  
> **免责声明：**  
本文为公开安全资讯的技术整合与原创分析，仅供安全研究、防御加固与学习交流使用。漏洞技术细节均来源于公开可查证的安全研究资料，请勿将文中信息用于任何非授权用途。  
  
##   
## 0x00、前言  
  
JeecgBoot 是北京国炬信息技术有限公司推出的开源低代码开发平台，基于 Spring Boot 与 MyBatis-Plus 构建，内置权限管理、数据字典、在线表单、报表设计等能力，让企业用少量代码就能快速搭建信息化系统，在国内政企市场装机量可观。  
  
8 月 18 日，一条公开披露让这批资产集体暴露：平台的 Shiro 权限过滤链与字典查询接口同时存在缺陷，两者一组合，**攻击者不需要任何账号，仅凭一个 .js 后缀就能绕过登录**  
，再借字典码把恶意 SQL 送进数据库，以布尔盲注方式拖走用户口令哈希、手机号等敏感数据。  
##   
## 0x01、漏洞描述  
  
该组合漏洞由两处独立缺陷串联而成：  
  
其一，Shiro 过滤链为放行静态资源配置了 /**/*.js  
 匿名规则，而路由匹配只看路径形态、不辨业务语义，攻击者在 API 路径后附加 .js 后缀即可命中该规则，绕过 JWT 认证；  
  
其二，字典查询接口的四段式字典码（表名,显示字段,取值字段,过滤条件）中，第 4 段 filterSql 经字符串拼接进入 MyBatis ${}  
 原样渲染，关键词黑名单拦不住布尔表达式。  
  
利用条件仅需目标网络可达，无需账号。限制在于：注入依赖布尔盲注逐位推断，部分敏感函数在黑名单内，数据外传速度受响应差异制约。  
##   
## 0x02、CVE 编号  
  
经查证 cve.org，该组合漏洞本身**暂无 CVE 编号**  
<table><thead><tr><th style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;font-weight: bold;background-color: rgb(255, 249, 249);color: rgb(239, 112, 96);"><section><span leaf="">项目</span></section></th><th style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;font-weight: bold;background-color: rgb(255, 249, 249);color: rgb(239, 112, 96);"><section><span leaf="">内容</span></section></th></tr></thead><tbody><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">漏洞编号</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">暂无 CVE</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">公开时间</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">2026-08-18</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">奇安信评级</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">高危，CVSS 3.1 评分 8.6</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">威胁类型</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">权限绕过（CWE-285）+ SQL Injection（CWE-89）</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">利用可能性</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">高（技术细节已公开，利用代码未公开）</span></section></td></tr></tbody></table>##   
## 0x03、影响版本  
<table><thead><tr><th style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;font-weight: bold;background-color: rgb(255, 249, 249);color: rgb(239, 112, 96);"><section><span leaf="">受影响范围</span></section></th><th style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;font-weight: bold;background-color: rgb(255, 249, 249);color: rgb(239, 112, 96);"><section><span leaf="">说明</span></section></th></tr></thead><tbody><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">JeecgBoot ≤ 3.9.3</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">最宽口径，含 3.x 全系</span></section></td></tr></tbody></table>##   
## 0x04、漏洞详情  
  
以下代码片段与攻击链来源于网络，披露 Issue [#9830]()  
 与技术分析 Issue [#9572]()  
、[#9491]()  
，关键环节均有源码或复现记录佐证。  
### 1. 第一跳：.js 后缀绕过 JWT 认证  
  
JeecgBoot 的 Shiro 过滤链里，静态资源被整批匿名放行，这个规则 2022 年 11 月就写进了源码。从当前 master 分支的 ShiroConfig.java 可以直接看到问题所在：  
```
// ShiroConfig.java ——静态资源匿名放行（2022年11月引入）filterChainDefinitionMap.put("/**/*.js",   "anon");filterChainDefinitionMap.put("/**/*.css",  "anon");filterChainDefinitionMap.put("/**/*.html", "anon");// ……其余静态后缀同理……// 除上述规则外，一切路径强制走 JWT 认证filterChainDefinitionMap.put("/**", "jwt");
```  
  
（代码较长，可左右滑动查看完整内容）  
  
Shiro 的 AntPathMatcher 按**路径形态**  
匹配过滤链：以 .js 结尾的请求，无论前面挂着什么业务路径，都会命中 /**/*.js  
 的 anon 规则，JwtFilter 根本不会执行。攻击者只要在 /sys/dict/getDictItems/*  
 的 API 路径后附加 .js 后缀，就能以匿名身份抵达控制器。认证边界交给了"路径像不像静态文件"来判定，这是路由式权限模型的先天缺陷。  
### 2. 第二跳：四段式字典码直通 WHERE 子句  
  
JeecgBoot 的字典查询支持"表字典"模式：前端不传字典编码，而是直接传 表名,显示字段,取值字段,过滤条件  
 四段式字典码，后端据此动态拼 SQL。披露方 Issue [#9830]()  
 给出的代码定位一针见血：  
```
// SysDictServiceImpl.getFilterSql（第541行）filterSql = " where " + condition;// condition = 四段式字典码的第4段，未做任何转义<!-- SysDictMapper.xml（第187/208行）-->where ${filterSql}and  ${filterSql}
```  
  
（代码较长，可左右滑动查看完整内容）  
  
第 4 段 condition 被原样拼进 WHERE；MyBatis 的 ${}  
 是字符串替换而非参数绑定，不转义、不参数化，攻击者写什么数据库就执行什么。  
### 3. 第三跳：关键词黑名单为什么拦不住  
  
唯一的防线是 SqlInjectionUtil 里的注入关键词黑名单，Issue [#9572]()  
 摘出了它的定义——每个关键词都带着尾部空格：  
```
// SqlInjectionUtil ——名单里每个关键词都带着尾部空格private static String specialDictSqlXssStr = "exec |peformance_schema|information_schema|extractvalue|updatexml|" "geohash|gtid_subset|gtid_subtract|insert |select |delete |update |" "drop |count |chr |mid |master |truncate |char |declare |;|+|--";
```  
  
（代码较长，可左右滑动查看完整内容）  
  
匹配逻辑是简单 indexOf。带空格的关键词挡得住 union select 1,2,3  
，却挡不住任何不带空格的写法，更挡不住布尔盲注根本不需要的那些词。公开分析中已验证的绕过形态：  
<table><thead><tr><th style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;font-weight: bold;background-color: rgb(255, 249, 249);color: rgb(239, 112, 96);"><section><span leaf="">输入形态</span></section></th><th style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;font-weight: bold;background-color: rgb(255, 249, 249);color: rgb(239, 112, 96);"><section><span leaf="">结果</span></section></th><th style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;font-weight: bold;background-color: rgb(255, 249, 249);color: rgb(239, 112, 96);"><section><span leaf="">原因</span></section></th></tr></thead><tbody><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">union select 1,2,3</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">拦截</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">命中 &#34;select &#34;（带空格）</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">select(id)from(sys_user)</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">放行</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">括号形态无空格，不命中</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">substr(password,1,1)=&#39;c&#39;</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">放行</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">不在名单内</span></section></td></tr><tr><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">1=1 / and / or</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">放行</span></section></td><td style="font-size: 0.75em;padding: 9px 12px;line-height: 22px;border: 1px solid rgb(239, 112, 96);vertical-align: top;"><section><span leaf="">名单只管 SQL 关键词，不管逻辑运算符</span></section></td></tr></tbody></table>### 4. 完整攻击链：四步拖库  
  
**第一步，匿名进入。**  
对字典接口发起带 .js 后缀的请求，命中匿名放行规则，JWT 认证被跳过，请求直达控制器。  
  
**第二步，构造四段式字典码。**  
披露方验证过的请求形如 dictCode=sys_user,realname,id,1=1  
——第 4 段恒真条件让 WHERE 失效，一次请求返回全表用户数据。实测中 sys_user,phone,email,1=1  
 直接返回了所有用户的手机号与邮箱。  
  
**第三步，布尔盲注。**  
响应行数的"有/无"就是天然的判定信道。公开披露中已验证的谓词形态长这样——闭合单引号后追加条件，让查询随目标字段取值真假而变化：  
```
// 真值条件：首字符为 c → 结果集非空' and username='admin' and password like 'c%' and username like '// 假值条件：首字符为 x → 结果集为空' and username='admin' and password like 'x%' and username like '
```  
  
（代码较长，可左右滑动查看完整内容）  
  
**第四步，逐位提取。**  
逐字符枚举条件前缀（c% → ca% → cb%……），命中即得该位字符。公开复现中，默认种子数据里 admin 的 16 位 MD5 口令哈希被完整还原——从判定到出值，全程不需要任何回显报错。  
### 5. 边界与补充  
  
三点值得注意的事实边界：  
  
其一，**同族接口面很宽。**  
上游披露点名了 /sys/api/getDictItems、/sys/api/queryFilterTableDictInfo、  
  
/sys/dict/loadDictOrderByValue、/sys/api/queryTableDictByKeys 等一串接口，均共享 getFilterSql 与 ${filterSql} 注入点，修复时不能只盯 getDictItems 一处。  
  
其二，**签名防线同样失守。**  
部分接口挂了 @SignatureCheck 注解，但公开分析发现签名密钥在源码与默认配置中硬编码，且签名算法（fastjson 对 SortedMap 序列化后接 MD5）可被其他语言精确复现——对拿到源码的攻击者而言形同虚设。  
  
其三，**组合危害大于单点。**  
权限绕过单独看只是"匿名读接口"，SQL 注入单独看需要有效凭证；两者叠加才构成"无凭证拖库"。这也是奇安信按组合链评级 8.6 的原因——口令哈希、手机号泄露后，撞库、钓鱼、横向移动都会接踵而至。  
##   
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/v50ePV9iaVJyFphr3BIzuLyBs7oibGUMWyDvIM06I3qc7SA1VfJWBO2qHf1fhjNdhlGCLAZb3zSPnj19sBdrph9dypk6aib4UViasJm0xniba09Q/640?wx_fmt=png&from=appmsg "")  
## （图片来源于网络）  
##   
## 0x05、个人观察与判断  
  
**设计缺陷点评：**  
通配后缀放行静态资源，让"路径像不像文件"决定认证与否，是路由式权限的经典反模式；${} 拼接叠加关键词黑名单，等于把注入防御押在攻击者的书写习惯上，被绕过只是时间问题。  
  
**行业趋势：**  
低代码平台把表名和过滤条件放进前端可传的字典码，灵活性换来了数据库查询能力外露。框架级漏洞乘以 6 万级暴露资产，加上海量带二开的存量系统，正在成为攻防演练的固定得分点。  
  
**排查建议：**  
先测绘本单位 JeecgBoot 资产与版本；网关侧拦截对 /sys/dict/getDictItems/* 的匿名访问与带 .js 后缀的 API 请求；Shiro 链中将字典接口置于静态资源规则之前强制 JWT；filterSql 一律改参数化查询或白名单校验。  
##   
## 0x06、参考链接  
  
**1. JeecgBoot Issue #9830（源头披露，2026-08-18）**  
  
https://github.com/jeecgboot/JeecgBoot/issues/9830  
  
getFilterSql 与 SysDictMapper.xml 注入点代码定位、四段式字典码机制  
  
**2. JeecgBoot Issue #9572（黑名单绕过技术分析）**  
  
https://github.com/jeecgboot/JeecgBoot/issues/9572  
  
签名机制与 SqlInjectionUtil 黑名单的绕过细节  
  
  
> **— END —**  
> 原创整理不易，如果这篇分析对你有用  
> **点个红心 ❤️**  
，是对我们最大的支持  
  
  
  

#  dddd 1.8.3 - 优化检测流程，更智能化的指纹、漏洞检测工具  
原创 油漆工
                    油漆工  C4安全   2026-07-13 05:25  
  
POC关联与模板升级说明  
  
dddd现已更新优化版本的1.8.3版本  
  
在旧版本中，POC 自动关联主要依赖 **workflow.yaml**  
 里的固定产品名映射，容易出现以下问题：  
  
/>  
  
指纹名和 POC 产品名不完全一致时，无法自动关联  
  
  
/>  
  
指纹带有 _body、_header、_title、_favicon 等后缀时，容易漏检  
  
  
/>  
  
同一产品存在多个命名方式时，只能命中少数写法  
  
  
/>  
  
新增的 nuclei 模板即使放进目录，也不一定能被当前指纹自动带出  
  
  
典型场景：  
  
  
```
Huawei-VPN、Huawei-VPN_body、Huawei-Auth-Server 无法稳定关联华为 VPN 相关 POCJeecgBoot、Jeecg-Boot_body 只能命中少量模板，甚至漏掉高价值漏洞模板
```  
  
  
2. 本次做了哪些变更  
  
2.1 优化 POC 全量索引机制  
  
**目前POC数量已经更新到1w5条**  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/niasx7fyic9COLaXaJMu8Npkn3BTPhWd1brgE2uZPRtyTlwGkL5M9HHKUOzanT1PwV6aOHiaWnibag2EH8ECUDxCyI2tGBu0L35lHtF2kicqdnnU/640?wx_fmt=png&from=appmsg "")  
  
程序启动时会自动扫描：  
  
  
```
config/pocsconfig/pocgather
```  
  
  
并建立统一索引，索引内容包括：  
  
/>  
  
模板路径  
  
  
/>  
  
模板别名  
  
  
/>  
  
tags  
  
  
/>  
  
id  
  
  
/>  
  
name  
  
  
/>  
  
vendor  
  
  
/>  
  
product  
  
  
这意味着程序不再只依赖 workflow.yaml 的固定名字，而是可以基于产品、厂商、标签和别名自动关联更多 POC。  
  
2.2 优化指纹归一化处理  
  
本次新增了指纹清洗逻辑，重点处理：  
  
  
```
去掉 _body、_header、_title、_favicon 等装饰后缀统一同一产品的多种写法支持产品名、厂商名、组合名拆分匹配过滤过于泛化的词，减少误关联
```  
  
  
例如：  
  
  
```
Huawei-VPN_bodyHuawei-VPN_headerHuaWei-Auth-Server
```  
  
  
现在会先还原出稳定产品特征，再去索引里自动查找对应模板。  
  
2.3 增强 POC 别名能力  
  
模板除了文件名，还会从以下字段自动生成别名：  
  
  
```
文件名相对路径idnameproduct
```  
  
  
同时支持紧凑匹配，例如横线、下划线、目录写法差异，降低“模板明明存在但名字写法不同找不到”的问题。  
  
2.4 增强 nuclei 模板解析与调用逻辑  
  
现在会优先使用启动时建立的 POC 索引：  
  
/>  
  
支持按别名定位模板  
  
  
/>  
  
支持按标签批量解析模板  
  
  
/>  
  
支持直接使用索引中的模板路径  
  
  
结果是自动关联出的模板更容易真正执行，手工用 -poc 查找时也更稳。  
  
2.5 集成最新 POC 模板库  
  
已收集**最新POC**  
解压并纳入程序使用范围。  
  
新增模板目录：  
  
  
```
config/pocgather
```  
  
  
程序启动时会自动将其纳入统一索引，无需使用者手工再配置。  
  
3. 用户能感知到什么  
  
最直接的变化有四点：  
  
/>  
  
漏检明显减少，尤其是指纹命名不标准、同产品多别名、响应体/响应头类指纹场景  
  
  
/>  
  
POC 覆盖范围更大，config/pocgather 中的新模板可以真正参与自动检测  
  
  
/>  
  
厂商级、产品级匹配能力更强，华为、Jeecg 这类多命名产品效果提升明显  
  
  
/>  
  
使用 -poc 手工模糊查找时，模板命中率更高  
  
  
4. 已重点验证的场景  
  
4.1 华为 VPN 相关场景  
  
已重点验证以下指纹组合：  
  
  
```
HuaWei-VPNHuaWei-Auth-ServerHuawei-VPN_bodyHuawei-VPN_headerHuaWei-Auth-Server_bodyHuaWei user-login_header
```  
  
  
验证结果：  
  
/>  
  
已能自动关联到华为相关漏洞模板  
  
  
/>  
  
已确认可带出 CVE-2019-19411.yaml 等关键模板  
  
  
说明：  
  
像此前提到的 /unweb/passwd 这类场景，即使模板中没有完全同名写法，现在也可以通过产品和标签维度正确关联到华为相关 POC。  
  
4.2 Jeecg / JeecgBoot 相关场景  
  
已重点验证：  
  
  
```
JeecgBootJeecg-Boot_body
```  
  
  
验证结果：  
  
  
```
能自动关联多个 Jeecg 相关模板已确认命中 jeecg-boot-ssti-rce.yaml已确认命中 jeecg-boot-passwordChange-unauth.yaml已确认命中多个 CVE-2021、CVE-2023 Jeecg 相关模板
```  
  
  
工具下载地址：  
已分享在内部社区中  
  
![](https://mmbiz.qpic.cn/mmbiz_png/niasx7fyic9CPjXr4hGZaZWFNGMmKwtzfKd9wvju64XDl5SZXmYUxWQN9MDB8nS0X9BMesXNmU7NP1t5dNRxHEpJkRWgTia0BPShZd0ic7NhqD0/640?wx_fmt=png&from=appmsg "")  
  
  
  
历史版本更新  
  
dddd 1.8.2  
 版本在 1.8 系列基础上补充了 xray POC 兼容、第三方反连域名配置、JS 敏感信息扫描、相关主动探测误报修复。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/niasx7fyic9CO5ibpDm7ba8WaVrMO47uZJtmKCeXiczShTR5ZH5lRfwfwBorUuvOEyLmcgRhYxjduM4ru5nJ8U5NMFamwpsLFLhv87bXUDhSFnI/640?from=appmsg "")  
  
主要更新  
- 兼容   
xrayFiles  
 目录下的 xray HTTP POC，并在运行时转换为 nuclei 模板执行。  
  
- 支持 xray 常见语法：  
output.search  
 提取变量、  
.submatch  
、  
.bsubmatch  
、  
md5  
、  
substr  
、  
string  
、  
bytes  
、  
concat  
、  
base64  
、  
base64Decode  
、  
urlencode  
、  
gzip  
、  
replaceAll  
、  
replaceRegex  
、  
trimSpace  
、  
toLower  
、  
toUpper  
 等。  
  
- 支持 xray   
reverse.domain  
、  
reverse.url  
、  
reverse.url.host  
、  
reverse.url.path  
 的基础兼容。  
  
- 支持第三方反连域名生成：  
ceye  
、  
dnslog  
、  
custom  
。  
  
- 支持 nuclei 原生 interactsh 参数透传：  
-iserver  
、  
-itoken  
、  
-ni  
。  
  
- 修复 GeoServer 站点在主动目录探测中被误识别为 phpMyAdmin 后触发大量 phpMyAdmin POC 的问题。  
  
- 支持   
WIHscan-1.0  
 /   
katana  
 的 JavaScript 敏感信息扫描，并写入 HTML 报告。  
  
内部社区加入优惠券  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/niasx7fyic9CM6ibbnnP7rXgGwYFRYlibVVT4XXhCuXXUpn5qfC3MHudTZhYiaomgeFjopTUxkMnRs05icfPEH8DFgb4o8RMxTHtJNlUFBiaufCtSU/640?wx_fmt=png&from=appmsg "")  
  
  
  
**内部CTF课程上线，总课程30+小时，优惠折扣中！**  
  
[](https://mp.weixin.qq.com/s?__biz=MzkzMzE5OTQzMA==&mid=2247490327&idx=1&sn=1c18cf9b93edc1488dbc3d3aff30fb5a&scene=21#wechat_redirect)  
  
  
**帮会简介**  
  
《  
**安全渗透感知**  
》是FreeBuf知识大陆的重量级帮会，帮会致力于漏洞POC/EXP、红队攻防实战，是系统化从基础入门到实战漏洞挖掘的教程社区，包含团队自整的挖掘注意点和案例，还包含分享的渗透经验、SRC漏洞案例、代码审计、挖洞思路等高价值资源。  
****  
  
****  
**内容框架（持续新增中）**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/niasx7fyic9CPkwWfIk2R852HYPQVdLISsvAVAQ9NYcianL7p2JJNhrdnTMzBZdEkZVcEqHZ1Rjbmtv1TuAcYumXNgFqwiaQVzRv66VgNtZN1MU/640?wx_fmt=png&from=appmsg "")  
  
**目前已有「720+」小伙伴加入了帮会**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/niasx7fyic9CP30mibrkgxrCvaOHUU9zjGsEtibCdEVMoVdHv6Vgyt8nqp0Y1pyWr2nbYBaRljljo4RzniclDHpahUHL1fdnibwOg1F9lT8ajHlvo/640?wx_fmt=png&from=appmsg "")  
  
****  
**加入方式**  
  
目前帮会成员  
**720+**  
人，**永久会员优惠后只需**  
**69.9元****。**  
  
随着人数的增加及资源的积累，**之后永久会员将**  
**涨价至99元****。**  
  
  
有意向的师傅们可以扫码加入我们，共同进步。****  
  
**如何加入帮会？→**安卓/苹果用户**可扫码使用优惠券↓↓**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/niasx7fyic9CM6ibbnnP7rXgGwYFRYlibVVT4XXhCuXXUpn5qfC3MHudTZhYiaomgeFjopTUxkMnRs05icfPEH8DFgb4o8RMxTHtJNlUFBiaufCtSU/640?wx_fmt=png&from=appmsg "")  
  
****  
**→ PC端用户可复制此链接到浏览器↓↓**  
  
https://wiki.freebuf.com/societyDetail?society_id=184  
****  
  
****  
**已加入帮会的小伙伴****可以加帮主进帮会内部交流群**  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/niasx7fyic9CO6PWWqgo0cqpzbbnpJZVJibK9tTthUjNYjgGrGezQf0uvm3Igsn47ojGdug13HL9WzJf06JxoXGJVgvDwbZndWric17OB7GiajeQ/640?wx_fmt=jpeg&from=appmsg "")  
  
**请备注：帮会**  
  
****  
  
  

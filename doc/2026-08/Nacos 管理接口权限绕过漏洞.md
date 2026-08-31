#  Nacos 管理接口权限绕过漏洞  
云南启安
                    云南启安  云南启安科技   2026-08-31 08:56  
  
点击↑蓝字关注我们  
  
**ENTERPRISE**  
  
  
  
**PART**  
  
  
**0****1**  
  
**漏洞描述**  
  
  
Nacos 是  
阿里巴巴开源的一款动态服务发现、配置管理和服务管理平台，支持微服务架构中的服务注册与发现、动态配置推送、服务元数据管理及流量管理等功能。它兼容多种主流语言和框架（如 Spring Cloud、Dubbo、Kubernetes 等），提供统一的控制台进行可视化运维，广泛应用于云原生和分布式系统中，帮助开发者实现服务的快速接入、弹性伸缩和高可用治理。  
  
Nacos 存在管理接口权限绕过漏洞  
，该漏洞  
源于   
Nacos  
 3.0.0 至 3.2.3 版本 UserControllerV3.createUser() 方法的 @Secured 注解缺少 apiType 参数，导致鉴权作用域错配。攻击者可在未授权状态下创建管理员账户，进而完全接管服务端，窃取所有配置信息  
。  
  
**PART**  
  
  
**0****2**  
  
**影响范围**  
  
- 3.0.0 <= Nacos <= 3.2.3  
  
  
  
  
**PART**  
  
  
**03**  
  
**修复建议**  
  
目前官方已发布修复该漏洞的新版本，建议用户请尽快更新至最新版本：  
  
Nacos >= 3.2.4  
  
下载链接：  
  
https://github.com/alibaba/nacos/releases/tag/3.2.4  
  
  
**PART**  
  
  
**04**  
  
**参考链接**  
  
- https://github.com/alibaba/nacos/pull/15563  
  
- https://github.com/alibaba/nacos/releases/tag/3.2.4  
  
  
  
  
  
  
  

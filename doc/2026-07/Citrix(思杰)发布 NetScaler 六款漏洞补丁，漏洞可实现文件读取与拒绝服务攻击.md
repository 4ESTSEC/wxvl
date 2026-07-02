#  Citrix(思杰)发布 NetScaler 六款漏洞补丁，漏洞可实现文件读取与拒绝服务攻击  
鹏鹏同学
                    鹏鹏同学  黑猫安全   2026-07-02 01:07  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/DYqn7TU9icq1OflNDWXkUfmMqD6KiawWgwWrdgEtZxacf3DU2IfOxicl3XUDmpwwGemlJGR2xW5n3ObQNnA3rYTmYVv1zpPVuXd7hy1UuVc82k/640?wx_fmt=png&from=appmsg "")  
  
思杰于周二发布安全更新，修复 NetScaler 应用交付控制器（原 Citrix ADC）与 NetScaler 网关（原 Citrix Gateway）中的多项安全漏洞。攻击者可借助这些漏洞读取服务器任意文件，或是发起拒绝服务（DoS）攻击。  
  
各漏洞详情如下：  
1. CVE-2026-8451（CVSS 评分 8.8）：输入校验不足漏洞。若 NetScaler ADC 或 NetScaler 网关配置为 SAML 身份提供商（IDP），会产生内存越界读取问题。  
  
1. CVE-2026-8452（CVSS 评分 8.8）：内存溢出漏洞。设备作为网关或 AAA 虚拟服务器部署时，会引发程序异常，进而造成拒绝服务。  
  
1. CVE-2026-8655（CVSS 评分 8.8）：多类内存溢出漏洞。当 NetScaler ADC 用作 Oracle 类型负载均衡、DNS 代理或 DNS 递归解析器时，将出现程序异常并触发拒绝服务。  
  
1. CVE-2026-10816（CVSS 评分 7.7）：文件名路径外部可控漏洞。若设备开放 NSIP、集群管理 IP、SNIP 的管理访问权限，无身份验证的攻击者即可读取服务器任意文件。  
  
1. CVE-2026-10817（CVSS 评分 6.9）：输入校验不足漏洞。TCP 配置文件开启 TCP 时间戳，并绑定至负载均衡、内容交换、VPN 虚拟服务器或对应业务服务时，会出现内存越界读取。  
  
1. CVE-2026-13474（CVSS 评分 8.7）：内存生命周期结束后未释放漏洞。HTTP 配置模板启用 HTTP/2 并关联负载均衡、内容交换、VPN 虚拟服务器或业务服务后，恶意构造的 HTTP/2 请求可耗尽内存，引发拒绝服务。  
  
漏洞补丁已上线至以下版本：  
1. NetScaler ADC、NetScaler Gateway 14.1-72.61 及后续 14.1 分支版本  
  
1. NetScaler ADC、NetScaler Gateway 13.1-63.18 及后续 13.1 分支版本  
  
1. NetScaler ADC 14.1-FIPS：14.1-72.61 FIPS 及后续 14.1-FIPS 系列版本  
  
1. NetScaler ADC 13.1-FIPS / 13.1-NDcPP：13.1.37.272 及后续同分支版本  
  
针对 CVE-2026-13474 漏洞，厂商建议用户同步调整 Http2SmallWndTimeout 参数，该参数用于定义 HTTP/2 小窗口阻塞数据流的超时时长，单位为秒：  
1. 设备启用严格 HTTP 配置模板：该参数默认值 30 秒，升级补丁后防护即刻生效。  
  
1. 设备未启用严格 HTTP 配置模板：参数默认值为 0，仅升级程序无法完全修复漏洞，管理员必须手动将 Http2SmallWndTimeout 设置为 30 秒。  
  
修改该参数的命令如下：set ns httpProfile <配置模板名称> -http2SmallWndTimeout < 超时秒数 >  
  
思杰对漏洞报送人员表示致谢，分别是摩根大通 XOR 团队的 Michael Tucker、watchTowr 安全实验室的 Aliz Hammond 以及 Maxim Suhanov。目前暂无证据证明上述漏洞已在真实网络环境中被攻击者利用。  
  
watchTowr 安全实验室同步发布配套技术分析报告称，研究人员在复现今年年初披露的高危输入校验漏洞 CVE-2026-3055（CVSS 评分 9.3）过程中，于 2026 年 3 月底发现并上报了 CVE-2026-8451。  
  
该安全机构表示，此漏洞与 3 月曝光的漏洞根源一致，均源于 NetScaler 解析 SAML 认证请求的逻辑缺陷，攻击者发送畸形 SAML 请求即可触发内存越界读取。  
  
安全研究员 Aliz Hammond 称：“有一点需要着重说明，和此前可一次性泄露数千字节二进制数据的 CVE-2026-3055 不同，本次漏洞的内存越界读取在识别到空字符、大于号等控制字符时就会终止。但我们实际测试发现，通过调整请求长度，依旧能稳定从服务器读取少量内存字节。”  
  
“但更值得警惕的是整体趋势：多起同类漏洞清晰暴露出思杰 NetScaler 设备的内存管理机制稳定性较差，甚至仅因设备配置失误，就有可能发生内存数据泄露。”  
  
近些年来，思杰相关设备长期是攻击者的重点目标，过往已有多个设备漏洞被威胁团伙利用来投放勒索病毒。因此用户务必及时安装补丁，以实现完整防护。  
  
  

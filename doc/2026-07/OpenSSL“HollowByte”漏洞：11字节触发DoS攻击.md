#  OpenSSL“HollowByte”漏洞：11字节触发DoS攻击  
 网安百色   2026-07-18 10:39  
  
![](https://mmbiz.qpic.cn/mmbiz_png/WibvcdjxgJnsOG1r4LuBcQL8lhosAzhXiabCCkIvByTc4tSowLDhykibeoriaWicHiaSh3DpqJ7gSFU4jpgiaqesHkR9FkHHFAsT3zyxDGlppU4PBU/640?wx_fmt=png&from=appmsg "")  
  
OpenSSL新披露的漏洞“HollowByte”**允许远程未认证攻击者仅通过11字节的恶意载荷触发拒绝服务（DoS）条件**  
。该漏洞由Okta红队发现，其利用机制在于OpenSSL在TLS握手阶段**预分配内存的设计缺陷**  
——服务器会在任何身份验证发生前被迫预留超大内存块。  
## OpenSSL “HollowByte”漏洞  
  
所有TLS握手均以ClientHello消息开始，该消息被封装在**携带4字节头部的记录中**  
，该头部声明了消息主体的大小。在旧版OpenSSL中，库会**完全依据攻击者声明的长度预分配接收缓冲区**  
，且此操作发生在实际数据到达前。  
  
当仅11字节的特制载荷抵达时，TLS状态机会读取头部并触发以下**未经验证的内存分配链**  
：  
**读取头部 → grow_init_buf() → OPENSSL_clear_realloc() → malloc(攻击者声明的大小)**  
  
由于此阶段**无任何长度校验**  
，单个恶意数据包即可迫使malloc()根据攻击者声明分配**最高131 KB内存**  
，随后工作线程将无限期阻塞，等待永不抵达的数据。  
  
传统连接耗尽攻击（如Slowloris）依赖保持连接开放以耗尽服务器线程资源，而HollowByte进一步引入了**内存碎片化问题**  
——其根源在于glibc对已释放内存的管理机制：  
  
当攻击连接断开时，OpenSSL会释放缓冲区，但glibc**不会立即将中小规模内存归还操作系统**  
，而是保留以备复用。  
  
攻击者通过**发送声明大小随机化的连接洪流**  
，可阻止内存分配器复用这些已释放块，导致服务器**常驻内存集（RSS）持续且永久性增长**  
，即使攻击者断开连接后仍无法恢复。**唯一解决方式是终止进程本身**  
。  
  
Okta红队对未修复的OpenSSL+NGINX实例测试显示严重后果：  
  
• **1 GB内存环境**  
：服务器在累积547 MB冻结碎片内存后被OOM机制强制终止。  
  
• **16 GB内存环境**  
：攻击**锁定25%系统内存**  
，同时保持在标准连接限制阈值内——意味着常规连接数限制防御对此失效。  
  
由于OpenSSL支撑着互联网基础设施的核心层，该漏洞影响范围覆盖：  
  
• **Web服务器**  
：Apache、NGINX等  
  
• **语言运行时**  
：Node.js、Python、Ruby、PHP  
  
• **数据库**  
：MySQL、PostgreSQL等  
  
OpenSSL通过**改用增量式缓冲区增长机制**  
修复此问题，相关代码已通过pull r  
equests #30792、#30793和#30794合并。  
新逻辑**仅在实际数据抵达时扩展缓冲区**  
，而非直接信任头部声明——这意味着空声明对服务器**零成本**  
。  
  
修复已静默集成至OpenSSL v4.0.1，并**向后移植至3.6.3、3.5.7、3.4.6及3.0.21版本**  
。值得注意的是，OpenSSL将此问题归类为**加固改进而非正式CVE通告**  
，这与近期其他DoS类漏洞的处理模式一致——此类问题常以无高调CVE编号的方式修复。  
  
**因HollowByte未分配CVE编号，标准漏洞扫描可能无法识别**  
。鉴于OpenSSL在Web服务器、运行时及数据库中的广泛部署，安全团队**应无视CVE状态将其列为高优先级补丁**  
。  
## 防御建议  
  
 **立即升级至OpenSSL 4.0.1或对应向后移植版本**  
（3.6.3/3.5.7/3.4.6/3.0.21）。  
  
 **审计语言运行时中嵌入的OpenSSL版本**  
（Node.js/Python/Ruby/PHP），操作系统级补丁无法修复捆绑副本。  
  
 **监控TLS终止服务器的RSS内存趋势**  
，排查符合碎片化攻击特征的渐进式、无解释内存膨胀。  
  
本公众号所载文章为本公众号原创或根据网络搜索下载编辑整理，文章版权归原作者所有，仅供读者学习、参考，禁止用于商业用途。因转载众多，无法找到真正来源，如标错来源，或对于文中所使用的图片、文字、链接中所包含的软件/资料等，如有侵权，请跟我们联系删除，谢谢！  
  
![图片](https://mmbiz.qpic.cn/mmbiz_jpg/1QIbxKfhZo5lNbibXUkeIxDGJmD2Md5vKicbNtIkdNvibicL87FjAOqGicuxcgBuRjjolLcGDOnfhMdykXibWuH6DV1g/640?wx_fmt=other&from=appmsg&wxfrom=5&wx_lazy=1&wx_co=1&randomid=p6hk1x4r&tp=webp#imgIndex=1 "")  
  

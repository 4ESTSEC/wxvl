#  Anthropic Buffa Rust库0Day漏洞可引发拒绝服务攻击  
 FreeBuf   2026-07-02 10:00  
  
![FreeBuf](https://mmbiz.qpic.cn/sz_mmbiz_gif/icBE3OpK1IX19V10z2Txp40ib1penyM1g16J3NkEJhCTN5xJXdWWvWicdxI6xl5x6UbF2oorvBnPDJpZqL7tvStOsXexz1z0c51MicZ50ySfiak8/640?wx_fmt=gif "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/icBE3OpK1IX19ctZE75VN2Bvic6z6OU7TleRgibsvmlSwSuZpgibYiajUs1hK4NnJSGYMr7R9Zs1kqQgwzll0UM24UJrJXUQchPFlmf65nlcDtdI/640?wx_fmt=png&from=appmsg "")  
  
  
Anthropic 基于 Rust 语言的 protobuf 实现库 buffa 被发现存在 0Day 拒绝服务（DoS）漏洞，攻击者可通过控制输入触发无限制堆内存分配。  
  
  
该漏洞编号为（CVE-2026-55407）和 GHSA-f9qc-qg88-7pq5，影响 buffa 和 connectrpc 0.8.0 之前版本，CVSS 评分为 4.0/6.3（中危）。但根据实际部署架构，其影响可能升级为高危或严重级别。  
  
  
Part  
01  
  
漏洞发现过程  
  
Endor Labs 的 AI SAST 引擎在分析 buffa 代码库时，于未知字段解码器中发现了可疑数据流。在 decode_unknown_field 函数中，长度值直接从不受信任的 protobuf 线数据解析，转换为 usize 类型后用于分配 Vec，除基本类型限制外未设置明确上限。  
  
  
Part  
02  
  
漏洞技术细节  
  
虽然防护机制确保缓冲区至少包含 len 字节以防止越界读取，但未对分配本身进行约束，使得攻击者可通过提交超大长度分隔字段强制进行大规模堆分配。初步分析显示输入大小与堆使用量存在约 2 倍放大效应，这在严格输入限制下通常可控。  
  
  
但进一步追踪 WireType::StartGroup 处理分支时，发现了更危险的放大向量。该分支解码器会循环处理嵌套未知字段直至遇到匹配的 EndGroup 标签，将每个解码字段推入基于 Vec 的 UnknownFields 结构。由于线路上最廉价的嵌套字段仅需 2 字节编码，却会导致约 40 字节堆分配及增长开销，精心构造的组可将较小输入扩展为巨大的内存结构。  
  
  
Part  
03  
  
漏洞验证  
  
Endor Labs 的概念验证显示，包含数百万个最小 varint 字段的 64 MiB protobuf 负载可使堆使用量激增至约 1.4 GiB，达到输入大小的 22 倍。在内存限制为 256 MiB 的 Docker 容器中解码此类消息时，进程会因退出代码 137 被终止，证实存在内存耗尽型 DoS。  
  
  
Part  
04  
  
影响范围与修复方案  
  
该漏洞路径可通过 buffa 默认解码 API（包括 Message::decode 和 decode_from_slice）触发，这意味着任何启用 preserve_unknown_fields（默认开启）解码不受信任 protobuf 消息的服务都可能受影响。Anthropic 已在 buffa 和 connectrpc 0.8.0 版本中发布修复，实现了可配置的每消息未知字段限制，将最大分配开销控制在数十兆字节内。  
  
  
对于无法立即升级的环境，可通过设置 preserve_unknown_fields=false 重新生成代码作为缓解措施，这将关闭未知字段保留功能。此案例表明，仅依赖输入大小限制并不足够，因为组放大路径可能将看似安全的消息大小转化为致命的内存分配。  
  
  
参考来源：  
  
Anthropic's Buffa Rust Library 0-Day Vulnerability Enables DoS Attack  
  
https://cybersecuritynews.com/anthropics-buffa-rust-library-0-day-vulnerability-enables-dos-attack/  
  
  
**推荐阅读**  
  
[](https://mp.weixin.qq.com/s?__biz=MjM5NjA0NjgyMA==&mid=2651341548&idx=1&sn=bb9edaa490d92c0258ff47c5dd29faf4&scene=21#wechat_redirect)  
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
###   
  
### 电报讨论  
  
  
[]()  
  
  
  
![扫码加入AI安全交流群](https://mmbiz.qpic.cn/sz_mmbiz_png/icBE3OpK1IX0UGcrYtIkSYDEgbDkib0yMF23VlKQibpJyRnibia1cD3no5XF7Je0Sic98ytMyvbY9LhO8tKoxBlnibsAXh8CnBTYoAxLReujuqjomI/640?wx_fmt=png "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX1cNPEia7j7bXCX8P8iaDo801yQlaF965NduoqX5nEfgC2mLLgM6VdzcRdkYkeGebHaia3JRK31e08ibfS1WnmYl8DtvPf83e6XW6k/640?wx_fmt=png&from=appmsg "")  
  
  
  
  

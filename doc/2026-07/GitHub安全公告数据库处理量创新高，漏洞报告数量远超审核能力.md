#  GitHub安全公告数据库处理量创新高，漏洞报告数量远超审核能力  
 FreeBuf   2026-07-01 04:00  
  
![FreeBuf](https://mmbiz.qpic.cn/sz_mmbiz_gif/icBE3OpK1IX37qibljZVfTacqamnJ6RJWSich2Quz1WyT6eianwhJ9d0JavFeKaszMiadvY8CJZIvueCcndIfZmicDN1o0ibbmPBwuicicB8uUm4A9oU/640?wx_fmt=gif "")  
  
  
![GitHub安全公告数据库](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX2KZIJcDFu5r8Bu9d9W1lcATOPUYKcN6TYWsjIl2W3FCSiaRhfR1sJ6mQdvBWWLibvZbd7JcGhAkCbhWu2RUKmWYILbblFKPJt3E/640?wx_fmt=png "")  
  
  
2026年5月，GitHub安全公告数据库创下历史新高，发布了1560份经过审核的安全公告，达到月均处理量的五倍以上。尽管取得这一里程碑式进展，该平台仍难以应对快速增长的漏洞报告数量，反映出全球漏洞披露生态系统的重大转变。  
  
  
GitHub表示，此次激增并非暂时现象，而是持续趋势的一部分。2026年3月至5月期间，平台每月处理超过6000份公告决策，包括新发布、更新和入站审核。与此同时，所有来源的输入数据均急剧增加：私有漏洞报告从1月的每周约550份增至5月的每周超3000份，仓库公告每周提交量突破5000份。  
  
  
通过GitHub CNA（CVE编号机构）提交的CVE请求也出现大幅增长，仅5月就收到近4000份，同比增长近10倍。  
  
  
Part  
01  
  
全球漏洞披露规模持续扩大  
  
2026年全球已发布超3万份CVE，凸显漏洞发现与披露规模的持续扩大。  
  
  
这种激增直接影响公告处理时效。自4月中旬以来，GitHub持续无法达成内部发布目标，部分案例的审核时间从数天延长至数周，增加了未修复漏洞的潜在暴露窗口。  
  
  
![公告处理时间线（来源：GitHub）](https://mmbiz.qpic.cn/mmbiz_png/icBE3OpK1IX3I1CW9yTjfhFhA21fPxIKia3JOEXk2zIpozic3dbKVP8INsXauE3DH8asrAtSuLEfefqFLibkxHgjwaO9l6dicQAqfA60S2icTzIgE/640?wx_fmt=png "")  
  
  
尽管存在延迟，GitHub强调所有经审核公告仍保持人工验证流程，确保软件包映射、受影响版本及严重性分类的准确性。CVE分配率稳定在91%-94%之间，表明提交质量未显著下降。  
  
  
Part  
02  
  
系统容量面临严峻挑战  
  
核心挑战在于处理吞吐量而非系统故障。GitHub的基础架构和数据管道仍按设计运行，但当前公告的复杂度和数量已超出系统原始容量。  
  
  
不同公告所需处理强度差异显著：结构清晰、包含明确软件包名称、版本范围和修复方案的报告可在数分钟内完成审核。但越来越多提交需要深度调查，包括解决跨生态系统的软件包歧义、重建缺失版本数据、协调冲突的上游信息等。  
  
  
典型案例包括：共享库漏洞可能同时影响npm和NuGet软件包，需要跨生态系统分别验证；CVE记录与仓库提交数据不一致时，维护人员需手动核实实际影响范围。  
  
  
Part  
03  
  
多措并举提升处理能力  
  
为应对挑战，GitHub正通过以下措施扩展运营能力：  
  
- 优化分级处理系统  
  
- 扩展后端容量  
  
- 部署AI辅助研究工具  
  
这些工具在自动化重复任务的同时，保留关键验证步骤的人工监督。公司还投资改进文档和培训体系，以提升新审核员上岗效率。  
  
  
未来计划包括：  
  
- 基于实际风险信号（如漏洞利用活动和软件包使用情况）增强优先级排序  
  
- 通过强化上游报告系统集成提升源数据质量  
  
GitHub特别强调社区参与的关键性，鼓励研究者和维护者提交完整准确的漏洞数据，包括CVSS向量、CWE分类和精确的软件包标识符。高质量提交可显著缩短审核时间，提升整体生态系统效率。  
  
  
Part  
04  
  
网络安全生态正向演进  
  
此次破纪录增长折射出网络安全领域的根本性转变：  
  
- 更多组织采用负责任披露机制  
  
- 更多研究者参与漏洞识别  
  
- 更多维护者发布修复方案  
  
  
虽然带来运营压力，这也标志着软件供应链透明度和安全性提升的重要进展。  
  
  
参考来源：  
  
GitHub Advisory Database Hits Record Volume as Vulnerability Reports Surpass Review Capacity  
  
https://cybersecuritynews.com/github-advisory-database-hits-record-vulnerability/  
  
  
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
  
  
  
  

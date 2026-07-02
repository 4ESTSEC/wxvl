#  Adobe 发布补丁修复 ColdFusion 与 Campaign Classic 中 7 个 CVSS 满分 10.0 级高危漏洞  
鹏鹏同学
                    鹏鹏同学  黑猫安全   2026-07-02 01:07  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/DYqn7TU9icq3qBEyAXauV9FSbRbKuK2vyfdo1r6xlO0n6x7gJvAEdzvPmibCOVmlt69z8dBNRXic7804XnHxWwhLc3opW26f4u5TLVQKO0LGM8/640?wx_fmt=png&from=appmsg "")  
  
Adobe 发布补丁，修复 Adobe ColdFusion 与 Adobe Campaign Classic 多款最高严重等级安全漏洞。  
  
Adobe 于周二发布安全预警称，本次 ColdFusion 更新修复了高危及重要级漏洞，攻击者可利用漏洞实现远程代码执行、权限提升、任意文件读取以及绕过安全防护机制。  
  
漏洞详情如下：  
1. CVE-2026-48276、CVE-2026-48283（CVSS 评分 10.0）：危险类型文件无限制上传漏洞，可触发远程代码执行；  
  
1. CVE-2026-48277、CVE-2026-48281、CVE-2026-48316（CVSS 评分 10.0）：输入校验不当漏洞，存在远程代码执行风险；  
  
1. CVE-2026-48282（CVSS 评分 10.0）：路径遍历漏洞，可实现远程代码执行；  
  
1. CVE-2026-48313（CVSS 评分 9.3）：路径遍历漏洞，允许攻击者任意读取服务器文件；  
  
1. CVE-2026-48315（CVSS 评分 9.3）：输入校验缺陷漏洞，可实现权限提升。  
  
上述漏洞已在 ColdFusion 2023 第 21 次更新包、ColdFusion 2025 第 10 次更新包中完成修复。安全研究员 Anirudh Anand、Matan Sandori 以及 2Bsecure 安全团队向厂商上报了 CVE-2026-48283、CVE-2026-48313、CVE-2026-48307 漏洞，Adobe 对其表示致谢。  
  
除此之外，Adobe 同步推送修复程序，封堵 Adobe Campaign Classic 中的一处高危漏洞。该漏洞影响 Windows 与 Linux 平台下 ACC v7 7.4.3 build 9396 及更早版本，存在远程代码执行风险。  
  
该漏洞编号为 CVE-2026-48286（CVSS 评分 10.0），根源为授权校验错误，攻击者可在受影响设备上执行任意代码。厂商已在 ACC v7 7.4.3 build 9397 版本中完成补丁修复。  
  
Adobe 说明，CVE-2026-48286 仅影响本地私有化部署的 Adobe Campaign 环境，包含纯本地部署架构，以及混合部署模式下的本地组件；由 Adobe 云端托管的实例已自动完成升级，用户无需手动操作。  
  
Adobe 同时表示，目前暂未发现上述两类更新所修复的漏洞存在野外利用攻击。  
  
本次漏洞披露之际，Adobe 宣布：受人工智能模型大幅加速漏洞挖掘速度影响，自 2026 年 7 月 14 日起，安全公告发布频次由每月一次调整为每月两次，固定于每月第二、第四个周二对外推送。  
  
Adobe 首席安全官安查尔・古普塔表示：“我们所使用的前沿 AI 技术同样被攻击者掌握，漏洞公开披露到出现实战利用的时间窗口已从数天缩短至数小时。我们率先运用 AI 挖掘并修复漏洞，而更快向客户交付补丁是顺势而为的举措。”  
  
  

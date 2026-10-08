#  CVSS 9.8！思科核心交换机NGOAM漏洞，远程无登录即可拿下最高权限  
 看雪学苑   2026-10-08 10:00  
  
思科（Cisco）于10月7日对外发布安全公告，披露**Nexus 3000、9000系列交换机存在3个高危漏洞，CVSS评分高达9.8分。**  
攻击者无需账号登录，仅通过网络可达，就能远程执行代码获取设备root最高权限，同时可触发设备进程崩溃、整机重启，造成网络业务中断。  
  
  
本次漏洞均来自NX-OS系统中的NGOAM（新一代运维管理功能），根源是设备对入站IP数据包校验存在缺陷。当NGOAM功能启用时，攻击者向交换机IP接口发送特制数据包，即可完成漏洞利用。  
  
漏洞利用成功后，攻击者可拿到操作系统root权限，完全接管交换机；同一攻击链路还能触发拒绝服务攻击，造成进程宕机、交换机强制重载，直接瘫痪数据中心网络。  
  
  
NGOAM原本用于网络故障排查，包含VXLAN环路检测、SRv6连通性检测等运维能力，开启该功能后设备才会暴露攻击面。  
  
  
三个CVE漏洞影响条件区分  
  
不同漏洞触发需要配套不同功能开启，并非所有Nexus设备都会受影响，管理员需要核对固件版本与已启用特性：  
  
1. CVE-2026-76485：仅需开启NGOAM功能即可触发，门槛最低  
  
2. CVE-2026-76486：NGOAM开启，同时启用SRv6，或配置VXLAN EVPN虚拟网络（NVE接口存在学习到的隧道对等端点）  
  
3. CVE-2026-76501：NGOAM+SRv6同时开启；Nexus3000不支持SRv6，仅部分Nexus9000机型具备该能力  
  
  
**✅ 不受影响设备清单**  
  
Nexus 9000运行ACI模式、Nexus7000系列、MDS 9000多层交换机，不在本次漏洞影响范围内。  
  
  
管理员核查命令（NX-OS）  
```
# 查看NGOAM是否开启
show feature | include ngoam
# 查看NVE/VXLAN overlay状态
show feature | include nve
# 查看SRv6状态
show feature | include srv6
# 额外Overlay配置核验
show running-config | begin "interface nve"
show nve vni
show nve peers
```  
  
  
处置方案建议  
  
1. 无临时补丁替代方案，关闭不需要的NGOAM可直接消除攻击面  
  
全局配置模式下执行命令：no feature ngoam  
  
操作前务必评估对现有网络运维业务的影响，避免业务中断。  
  
  
2. 思科同步放出Live Protect防护规则，仅作为临时缓解手段，不可替代固件升级。  
  
  
3. 使用Cisco Software Checker工具，查询对应平台的安全修复版本，安排NX-OS系统升级。  
  
此前Nexus9000系列还爆出过另一款root权限漏洞CVE-2026-20212（Silicon One芯片机型），但两个漏洞触发条件完全不同。管理员需要**独立评估本次NGOAM漏洞风险，分开规划升级计划，不要混淆。  
  
  
思科公告说明：漏洞为内部安全测试发现，公告发布时暂未发现公开POC与在野恶意利用。但因攻击门槛极低，建议政企、IDC、云厂商等使用该系列交换机的单位，立刻开展资产排查。  
  
  
  
资讯来源：CybersecurityNews  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/Cpo2XCpI7K39KF1GuYGv84E7yfZh2fiagWklqQTMMianNPHqnhYR1Mc7NxMqyK5LfRwFPkbd5ia3mpw5ETl6tibDGf4FvxYqcaxck5eHZL2o9LA/640?wx_fmt=jpeg&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/Cpo2XCpI7K0l8JbC0y0X7vpW8s6l2qNzyy4aPp5YMnoUwN2ma5GctFubILfS80Fd1BtuXiatiaeIzmvticnEypFQz5jW7q0ITPdFZia1l0vyjNA/640?wx_fmt=gif&from=appmsg "")  
  
**球分享**  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/Cpo2XCpI7K0TFiaVxE7sHtN5ReiaZZag4EGsJk4UWMyGLEnSM3oh75F0Wy1Kqs7Cosu23apjvL1KXeHuBk5zasicGGQ82MF5Q2UXgXGpqYpzwQ/640?wx_fmt=gif&from=appmsg "")  
  
**球点赞**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/Cpo2XCpI7K1fXJqickvG8weYCq8VxIsaDtHAiafFUk4s9IuiaZIEK1n3MLulOKeic47yWsDWAG8fRl0g45bGP4XXHGab4Mxia8mEryseyEzrXYrw/640?wx_fmt=gif&from=appmsg "")  
  
**球在看**  
  

#  紧急安全预警｜CUPS曝本地提权漏洞，lpadmin低权限用户可获取Root权限  
tuto
                    tuto  杂杂咱谈   2026-09-19 02:59  
  
## 一、概述  
  
cups2root是针对LinuxCUPS的本地提权PoC。该利用链将多个CUPS配置与权限边界问题串联起来，使已经属于lpadmin用户组的本地低权限用户，最终获得Root交互式Shell。  
  
公开资料显示，该PoC由V12 Security研究人员发布，目前尚未看到对应的CVE编号或官方修复公告。PoC已在Ubuntu 26.04 LTS的AMD64和ARM64环境进行测试。  
  
核心攻击链可以概括为：  
```
lpadmin低权限用户
        ↓
CUPS Serial Backend
        ↓
篡改 cups-files.conf
        ↓
构造恶意配置
        ↓
触发 CUPS 崩溃
        ↓
systemd 自动重启 CUPS
        ↓
控制 ServerBin 目录
        ↓
替换 cups-exec
        ↓
CUPS以Root权限执行
        ↓
Root Shell
```  
## 二、漏洞利用条件  
  
攻击者需要满足以下条件：  
- 已经拥有目标 Linux 系统上的本地账户；  
  
- 账户属于 lpadmin  
 用户组；  
  
- 主组不能是 lpadmin  
、root  
 或 CUPS 配置中的 SystemGroup  
；  
  
- 系统使用   
systemd；  
  
- CUPS 本地服务处于运行状态；  
  
- 系统启用了 CUPS Serial Backend；  
  
- 存在 PoC 所依赖的标准 Ubuntu CUPS 路径。  
  
PoC不需要额外安装第三方Python依赖，运行方式为：  
```
python3 cups2root.py
```  
  
成功后会直接打开交互式Root Shell，退出Shell后PoC会尝试清理临时文件、队列以及其他攻击过程中产生的本地痕迹。  
## 三、漏洞利用原理  
### 1. 利用 Serial Backend 修改 CUPS 配置  
  
PoC首先创建一个RAW Serial Printer，并将打印设备URI指向  
：  
```
/etc/cups/cups-files.conf
```  
  
由于相关Serial Backend以Root权限运行，攻击者可以利用打印数据覆盖该配置文件。  
  
随后PoC修改多个关键CUPS路径，包括：  
```
Group
RequestRoot
ServerRoot
TempDir
ServerBin
```  
  
其中尤其重要的是：  
```
ServerBin → /etc/cups/interfaces
```  
  
这使后续CUPS执行程序的搜索路径被重定向到攻击者能够控制的位置。  
### 2. 触发 CUPS 崩溃并让 systemd 自动重启  
  
完成配置篡改后，PoC发送一个构造异常的IPP Subscription请求，使CUPS守护进程发生崩溃。  
  
由于CUPS由systemd管理，服务发生异常后会被自动重新启动。重新启动时CUPS会读取已经被修改的/etc/cups/cups-files.conf，于是攻击者之前设置的ServerBin等路径开始生效。  
### 3. 控制 ServerBin 并替换 cups-exec  
  
重启后的CUPS将/etc/cups/interfaces视为新的ServerBin目录。  
  
由于该目录的权限已经被前面的配置修改影响，攻击者可以向其中写入恶意版本的cups-exec之后通过触发CGI请求，让CUPS调用这个替换后的cups-exec。  
  
关键问题在于cups-exec此时由CUPS以Root权限执行，因此攻击者的恶意程序可以进一步创建一个  
setuid Root Shell  
，从而完成本地权限提升。  
## 四、完整攻击链  
```
本地 lpadmin 用户
        ↓
创建恶意 Serial Printer
        ↓
Serial Backend以Root权限处理打印数据
        ↓
覆盖 /etc/cups/cups-files.conf
        ↓
修改 ServerBin 等关键路径
        ↓
 发送恶意 IPP 请求
        ↓
    CUPS 崩溃
        ↓
 systemd 自动重启
        ↓
ServerBin → /etc/cups/interfaces
        ↓
写入恶意 cups-exec
        ↓ 
     触发 CGI
        ↓
cups-exec以Root执行
        ↓
创建Root权限Shell
        ↓
   获得Root权限
```  
## 五、AppArmor为何无法阻止  
  
该利用链的一个值得注意的地方是: 它并不需要直接突破AppArmor沙箱。  
  
PoC利用的是CUPS本身已经被允许执行的文件写入操作，将这些合法权限组合成了完整的提权链。  
```
AppArmor允许的CUPS操作
          ↓
     配置文件写入
          ↓
       目录控制
          ↓
     程序路径重定向
          ↓
Root进程执行攻击者文件
```  
  
因此，单纯依赖Ubuntu默认AppArmor策略并不能阻止这条利用链。公开分析也指出，cups2root 的关键操作均落在现有CUPS AppArmor策略允许的范围内。  
  
## 六、影响范围  
  
目前公开PoC主要针对  
Ubuntu 26.04 LTS  
，并支持：  
- AMD64  
  
- ARM64  
  
需要特别注意的是，该问题属于本地权限提升，并不是"未认证远程攻击者直接获得Root"。  
  
攻击者首先需要获得本地账户，并满足lpadmin组权限条件。  
  
不过，对于存在：  
```
恶意软件
Web Shell
被攻陷的普通账户
不可信本地用户
AI Agent / 自动化任务
```  
  
等场景的服务器，lpadmin → root的权限跃升可能进一步导致整台主机失陷。  
## 七、当前状态与缓解建议  
  
目前公开信息显示，cups2root尚未对应公开CVE，也没有看到针对该利用链的官方补丁公告。  
  
在官方修复发布前，可以考虑：  
1. 严格限制lpadmin  
组成员，仅允许可信管理员加入；  
  
1. 不需要打印功能的服务器可以考虑  
禁用CUPS；  
  
1. 不需要串口打印功能时，禁用Serial Backend；  
  
1. 限制CUPS服务的网络暴露范围；  
  
1. 持续关注OpenPrinting/CUPS官方安全公告；  
  
1. 对lpadmin  
组成员以及近期CUPS配置变更进行审计。  
  
另外，CUPS在2026年已经出现过多项安全问题，因此不建议仅针对cups2root单独进行防护，应结合当前CUPS版本和已公开漏洞统一进行升级与配置审计。  
### 八、总结  
  
cups2root的核心并非单一漏洞，而是一条CUPS权限边界利用链：  
> lpadmin  
 → 修改 CUPS 配置 → CUPS 重启 → 控制 ServerBin  
 → 替换 cups-exec  
 → Root Shell  
  
  
其最大风险在于，攻击者只需要具备一定的本地打印管理权限，就可能进一步突破到Root权限。  
  
  
poc:  
https://github.com/v12-security/pocs/tree/main/cups/cups2root  
  
[#Simple]()  
 [#cpus2root]()  
 [#LPE]()  
  
  

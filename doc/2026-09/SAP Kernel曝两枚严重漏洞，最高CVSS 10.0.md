#  SAP Kernel曝两枚严重漏洞，最高CVSS 10.0  
tuto
                    tuto  杂杂咱谈   2026-09-09 08:13  
  
## 一、漏洞概述  
  
SAP于  
2026年9月8日安全补丁日  
披露两枚SAP Kernel相关漏洞：  
<table><thead><tr><th style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">CVE</span></span></section></th><th style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">组件</span></span></section></th><th style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">漏洞类型</span></span></section></th><th style="text-align: right;"><section style="text-align: center;"><span leaf=""><span textstyle="" style="font-size: 16px">CVSS </span></span></section></th></tr></thead><tbody><tr><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px; font-weight: 500">CVE-2026-44756</span></span></section></td><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">SAP Kernel / EPP</span></span></section></td><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">内存安全漏洞</span></span></section></td><td style="text-align: right;"><section style="text-align: center;"><span leaf=""><span textstyle="" style="font-size: 16px; font-weight: 500">10</span></span></section></td></tr><tr><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px; font-weight: 500">CVE-2026-58240</span></span></section></td><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">SAP NetWeaver Message Server</span></span></section></td><td style="text-align: left;"><section><span leaf=""><span textstyle="" style="font-size: 16px">身份验证/组件注册校验不足</span></span></section></td><td style="text-align: right;"><section style="text-align: center;"><span leaf=""><span textstyle="" style="font-size: 16px; font-weight: 500">9.8</span></span></section></td></tr></tbody></table>  
两枚漏洞均被SAP评为严重级别，其中  
CVE-2026-44756达到满分10.0  
。  
  
不过需要特别注意：  
CVSS评分并不等于漏洞实际能够实现RCE  
。尤其是CVE-2026-44756，公开漏洞描述目前仅明确提到了异常行为和程序终止，并没有直接确认远程代码执行。  
## 二、CVE-2026-44756：EPP处理组件内存安全漏洞  
### 基本信息  
- CVE: CVE-2026-44756  
  
- SAP Note: 3747649  
  
- 组件: SAP Kernel/Extended Passport Protocol  
  
- CWE: CWE-120  
  
- CVSS: 10.0  
  
- 攻击方式: 网络远程攻击  
  
- 认证要求: 无  
  
- 用户交互: 无  
  
SAP描述称，在特定条件下，未认证攻击者可以向目标发送包含恶意EPP Header的网络请求，从而触发内存安全问题，最终导致程序出现异常行为或终止。  
  
漏洞与  
缓冲区复制过程中缺少输入长度检查  
有关。  
## 三、CVE-2026-58240：Message Server组件注册验证漏洞  
### 基本信息  
- CVE: CVE-2026-58240  
  
- SAP Note: 3759472  
  
- 组件: SAP NetWeaver Message Server  
  
- CWE: CWE-308  
  
- CVSS: 9.8  
  
- 攻击方式: 网络远程攻击  
  
- 认证要求: 无  
  
该漏洞源于Message Server在注册内部应用服务器组件时，对组件真实性的验证不足。  
  
攻击者在能够访问相关网络服务的情况下，可以尝试注册未经授权的组件，并进一步在应用环境中执行未经授权的操作。  
  
与CVE-2026-44756相比，该漏洞的公开描述对  
机密性、完整性和可用性影响  
描述得更加明确。  
## 四、为什么10.0和9.8只差0.2？  
  
两枚漏洞的CVSS 3.1评分非常接近。  
  
两者的主要区别只有一个指标：  
```
CVE-2026-44756：S:C
CVE-2026-58240：S:U
```  
  
其中：  
- S:C (Changed)：漏洞影响范围超出了最初受漏洞影响的安全权限边界；  
  
- S:U (Unchanged)：影响仍局限于原有安全权限范围。  
  
因此：  
> CVE-2026-44756：10.0  
  
CVE-2026-58240：9.8  
  
  
这并不意味着10.0漏洞一定比9.8漏洞更容易造成实际入侵。  
  
实际风险仍然应该结合  
网络暴露面、部署架构、业务用途以及攻击者所需条件  
进行判断。  
## 五、重点关注Message Server攻击面  
  
对于CVE-2026-58240，需要重点检查SAP Message Server的网络暴露情况。  
  
Message Server通常属于SAP应用环境中的内部组件，因此应重点确认：  
- 哪些服务器可以访问Message Server；  
  
- Message Server相关端口是否暴露到互联网；  
  
- 是否存在跨网段访问；  
  
- 应用服务器注册机制是否存在过于宽松的访问控制；  
  
- 防火墙是否限制了非必要来源；  
  
- 是否出现异常组件注册行为。  
  
缩小Message Server网络暴露范围不能从根本上修复漏洞，但可以降低攻击面。  
## 六、影响版本  
### CVE-2026-44756  
  
涉及多个SAP Kernel版本，包括：  
- KRNL64NUC 7.22 / 7.22EXT  
  
- KRNL64UC 7.22  
  
- KERNEL 7.22  
  
- KERNEL 7.53  
  
- KERNEL 7.54  
  
- KERNEL 7.77  
  
- KERNEL 7.89  
  
- KERNEL 7.93  
  
- KERNEL 8.04  
  
- WEBDISP 9.16  
  
- WEBDISP 9.18  
  
- WEBDISP 9.19  
  
- WEBDISP 9.20  
  
其中  
Web Dispatcher版本尤其值得关注  
，因为其可能处于互联网边缘位置。  
### CVE-2026-58240  
  
涉及：  
- KERNEL 9.16  
  
- KERNEL 9.18  
  
- KERNEL 9.19  
  
- KERNEL 9.20  
  
> 上述版本主要是受影响的产品/发行版本信息，并不等同于具体补丁级别。  
  
## 七、修复与排查建议  
  
目前公开CVE记录中尚未明确列出具体修复版本，因此企业应以  
SAP官方Security Note  
为最终依据。  
### 建议立即执行  
  
1. 排查资产  
  
确认环境中是否存在：  
- SAP Kernel  
  
- SAP NetWeaver  
  
- SAP Web Dispatcher  
  
- SAP Message Server  
  
2. 优先检查互联网暴露资产  
```
Internet
   ↓
SAP Web Dispatcher
   ↓
SAP Application Server
   ↓
SAP Message Server
```  
  
避免Message Server等内部服务直接暴露到互联网。  
  
3. 更新SAP安全补丁  
  
按照SAP官方安全公告和对应SAP Note获取适用补丁，不建议仅依据CVE数据库判断是否已经修复。  
  
4. 加强网络访问控制  
  
对Message Server及其他内部SAP组件实施：  
- IP白名单；  
  
- 网络分段；  
  
- 防火墙访问控制；  
  
- 最小化开放端口。  
  
5. 检查异常行为  
- 异常EPP请求；  
  
- SAP Web Dispatcher异常访问；  
  
- Message Server异常组件注册；  
  
- 非预期应用服务器连接；  
  
- 异常进程退出或频繁崩溃；  
  
- 来源异常的内部网络请求。  
  
## 八、漏洞利用情况  
  
目前公开信息中  
没有确认这两枚漏洞已经遭到大规模在野利用  
，且两枚漏洞均未被列入CISA KEV。  
  
此外，NVD当前记录主要处于  
Received  
状态，尚未提供独立的NVD评分，因此目前看到的10.0和9.8主要来自SAP自身的CVSS评估。  
## 九、总结  
  
此次SAP安全更新中，两枚Kernel漏洞分别获得：  
> CVE-2026-44756：CVSS 10.0  
  
CVE-2026-58240：CVSS 9.8  
  
  
其中，  
CVE-2026-44756虽然获得满分10.0，但公开描述目前主要指向异常行为和程序终止，不能仅凭CVSS 10.0认定其能够实现RCE。  
  
相比评分差异，更值得企业关注的是实际攻击面：  
- CVE-2026-44756：重点检查SAP Kernel及Web Dispatcher暴露情况  
  
- CVE-2026-58240：重点检查Message Server网络可达性和组件注册行为  
  
- 两者均应结合SAP官方安全补丁进行版本核查和修复。  
  
安全团队应优先按照"互联网暴露面 → 内部服务可达性 → 受影响Kernel版本 → 日志与异常行为"的顺序开展排查。  
  
  
[#Simple]()  
 [#CVE]()  
-2026-44756 [#CVE]()  
-2026-58240  
  

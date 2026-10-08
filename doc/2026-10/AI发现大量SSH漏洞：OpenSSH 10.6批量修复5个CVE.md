#  AI发现大量SSH漏洞：OpenSSH 10.6批量修复5个CVE  
原创 Dr. Clay
                    Dr. Clay  黑白之道   2026-10-08 01:00  
  
> **导语**  
：2026年10月6日，OpenSSH 正式发布了 10.6 版本，这是继上次重要安全更新后的又一次重大修订。本次更新修复了多个安全漏洞，其中最为严重的是来自波鸿鲁尔大学（Ruhr University Bochum）研究人员发现的"Crossing the Streams"压缩侧信道攻击漏洞，攻击者可利用该漏洞在特定条件下恢复加密会话中的明文内容。  
  
## 一、事件概述  
  
OpenSSH 是 SSH（Secure Shell）协议的开源实现，被广泛应用于服务器远程管理、文件传输等场景。据统计，全球有超过 1000 万台 SSH 服务器运行在不同版本的 OpenSSH 上。  
  
本次 OpenSSH 10.6 的发布背景较为特殊——项目团队表示，近期收到了大量由 AI 模型发现或借助 AI 辅助的安全漏洞报告。这一趋势反映了 AI 在代码审计领域的应用正在改变安全研究的格局。  
### 1.1 受影响版本  
  
以下版本均受影响：  
- OpenSSH < 10.6 所有版本  
  
建议所有用户尽快升级至 OpenSSH 10.6。  
## 二、漏洞详情  
### 2.1 Crossing the Streams（CVE-2026-106582）  
  
**严重程度**  
：低危（CVSS 3.7）  
**漏洞类型**  
：压缩侧信道攻击（Covert Channel）  
  
这是本次更新最受关注的漏洞，由波鸿鲁尔大学的 Fabian Bäumer 和 Marcus Brinkmann 两位研究人员发现并命名为"Crossing the Streams"（向 1984 年同名恐怖电影致敬）。该漏洞论文已收录至 arXiv:2609.07709，并被 ACM CCS 2026 接受发表。  
#### 攻击原理  
  
SSH 协议支持多路复用（Multiplexing）——即在单个加密连接上同时建立多个逻辑通道，分别用于交互式 shell、命令执行、端口转发、代理连接等。所有这些通道共享同一个压缩上下文（Compression Context）。  
```
┌─────────────────────────────────────────────┐│         SSH 加密连接 (Single Connection)     │├─────────────────────────────────────────────┤│  Channel 1: 交互式 Shell                     ││  Channel 2: 命令执行                         ││  Channel 3: 端口转发                        ││  Channel 4: Agent 连接                      │├─────────────────────────────────────────────┤│     共享压缩上下文 (Shared Compression)      │└─────────────────────────────────────────────┘
```  
  
当启用压缩时，攻击者如果能够：  
1. 向某一通道注入半选择（half-chosen）的明文  
  
1. 监控网络上的密文长度变化  
  
就能利用选择明文攻击（Chosen-Plaintext Attack）从另一通道中恢复敏感信息，如认证凭据、密钥材料等。  
#### 缓解措施  
  
OpenSSH 10.6 默认禁用了 LZ77 字典编码器，以阻断这一侧信道攻击路径。这一决定虽然会影响启用压缩时的性能，但出于安全考虑是必要的。  
### 2.2 命令行用户名注入（CVE-2026-106583）  
  
**严重程度**  
：低危（CVSS 2.5）  
**漏洞类型**  
：资源注入（Resource Injection）  
  
在 OpenSSH 10.6 之前的版本中，命令行用户名可以包含 $  
 或 \  
 字符，这可能导致在某些自动化场景下的 shell 注入问题。  
  
例如，当用户名设置为 $(whoami)  
 或包含反斜杠的字符串时，可能在特定调用链中触发意外的命令执行。  
  
OpenSSH 10.6 现在明确拒绝在命令行用户名中包含 $  
 和 \  
 字符。  
### 2.3 SFTP 目录遍历（CVE-2026-106552）  
  
**严重程度**  
：中危（CVSS 4.2）  
**漏洞类型**  
：相对路径遍历（Relative Path Traversal）  
  
在 OpenSSH 10.6 之前的 sftp 实现中，恶意服务器可以在递归复制操作期间触发目录遍历，导致文件被写入预期位置之外。  
  
这意味着连接到不受信任的 SFTP 服务器时，客户端可能被诱使将文件写入任意位置，潜在影响包括覆盖系统文件或写入敏感目录。  
### 2.4 GSSAPI 认证问题（CVE-2026-106553、CVE-2026-106555）  
  
**严重程度**  
：待评估  
**漏洞类型**  
：认证状态污染  
  
本次更新还修复了两个 GSSAPI（Generic Security Services API）认证相关漏洞：  
- **CVE-2026-106555**  
：GSSAPI 认证状态可能在认证尝试之间跨带（persist across authentication attempts），导致后续认证尝试受到先前失败状态的影响  
  
- **CVE-2026-106553**  
：GSSAPI 凭据可能在认证失败后仍然保留，而不是被正确清除  
  
这些漏洞可能导致认证绕过或凭据泄露风险，尤其是在多阶段认证场景中。  
## 三、验证方法  
  
用户可以通过以下命令检查当前 OpenSSH 版本：  
```
ssh -V
```  
  
输出示例（受影响版本）：  
```
OpenSSH_10.5p1, OpenSSL 3.4.0 30 Sep 2026
```  
  
升级后应显示：  
```
OpenSSH_10.6, OpenSSL 3.4.0 6 Oct 2026
```  
  
对于 Debian/Ubuntu 系统，可使用：  
```
apt-cache policy openssh-server openssh-client
```  
  
对于 RHEL/CentOS 系统：  
```
rpm -qa | grep openssh
```  
## 四、修复方案  
### 4.1 升级 OpenSSH  
  
**Debian/Ubuntu：**  
```
sudo apt update && sudo apt upgrade openssh-server
```  
  
**RHEL/CentOS/Fedora：**  
```
sudo dnf update openssh-server
```  
  
**从源码编译：**  
```
wget https://cdn.openbsd.org/pub/OpenBSD/OpenSSH/portable/openssh-10.6.tar.gztar -xzf openssh-10.6.tar.gzcd openssh-10.6./configuremakesudo make install
```  
### 4.2 配置缓解  
  
如果暂时无法升级，可考虑以下临时缓解措施：  
1. **禁用 SSH 压缩**  
：在 sshd_config  
 和 ssh_config  
 中设置 Compression no  
  
1. **限制 GSSAPI 认证**  
：在 sshd_config  
 中设置 GSSAPIAuthentication no  
（如不使用）  
  
1. **避免连接不可信服务器**  
：特别注意 SFTP 操作中的服务器来源  
  
## 五、趋势预测  
1. **AI 辅助代码审计的崛起**  
：OpenSSH 团队表示近期大量漏洞由 AI 模型发现，这标志着 AI 在安全研究中的应用已进入实用阶段，预计未来将有更多由 AI 发现的历史遗留漏洞（legacy bugs）被披露。  
  
1. **后量子密码学推进**  
：OpenSSH 10.6 已支持后量子签名算法，实验性密钥需要更换。随着量子计算的进展，这将成为未来 SSH 安全的重要方向。  
  
1. **压缩功能可能逐步淘汰**  
：鉴于"Crossing the Streams"攻击的发现，SSH 压缩机制的安全性受到质疑，预计未来版本可能进一步限制或移除压缩支持。  
  
**版权声明**  
：本文由华盟网原创发布，保留所有权利。配图由华盟网授权使用。  
  

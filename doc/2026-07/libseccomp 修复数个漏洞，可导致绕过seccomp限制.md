#  libseccomp 修复数个漏洞，可导致绕过seccomp限制  
微步情报局
                    微步情报局  微步在线研究响应中心   2026-07-02 03:36  
  
漏洞概况  
  
  
libseccomp 是 Linux 系统中广泛使用的 seccomp 过滤器编译库，为容器运行时（如Docker、containerd）、沙箱应用及安全敏感服务提供系统调用访问控制能力。  
  
近日，微步安全大模型 XGPT 发现 libseccomp 存在三个安全漏洞，分别涉及过滤器规则合并逻辑错误、整数溢出及双重释放问题：  
1. GHSA-4q85-33p6-j5g6 为过滤器规则合并缺陷，该漏洞源于 libseccomp 在构建过滤器树时，_db_tree_add() 函数存在复制粘贴错误，导致在合并同一系统调用的多条 64 位参数比较规则时，false-path动作字段未被正确更新。  
攻击者可利用此缺陷绕过本应生效的 seccomp 限制，执行被安全策略禁止的系统调用，例如可能实现容器逃逸或权限提升。  
  
1. GHSA-46fr-jh49-xvhx 与 GHSA-2hqh-5c36-grrm 两个漏洞均涉及 BPF 指令计数溢出问题：struct bpf_program.blk_cnt 字段为 16 位无符号整数，当生成的过滤器包含超过 65,535 条 BPF 指令时发生回绕，导致堆腐败及双重释放，进而导致编译过滤器崩溃（DoS）,可能在特定条件下实现代码执行。  
  
漏洞影响范围  
  
<table><tbody><tr><td style="text-align: left;border: 1px solid rgb(221, 221, 221);padding: 12px;vertical-align: top;font-weight: bold;background-color: rgb(248, 249, 250);"><section><span leaf="">产品名称</span></section></td><td style="text-align: left;border: 1px solid #ddd;padding: 12px;vertical-align: top;"><section><span leaf="">libseccomp</span></section></td></tr><tr><td style="text-align: left;border: 1px solid rgb(221, 221, 221);padding: 12px;vertical-align: top;font-weight: bold;background-color: rgb(248, 249, 250);"><section><span leaf="">受影响版本</span></section></td><td style="text-align: left;border: 1px solid #ddd;padding: 12px;vertical-align: top;"><section><span leaf="">libseccomp &lt; 2.6.1</span></section></td></tr><tr><td style="text-align: left;border: 1px solid rgb(221, 221, 221);padding: 12px;vertical-align: top;font-weight: bold;background-color: rgb(248, 249, 250);"><section><span leaf="">有无修复补丁</span></section></td><td style="text-align: left;border: 1px solid #ddd;padding: 12px;vertical-align: top;"><section><span leaf="">有</span></section></td></tr></tbody></table>  
修复方案  
  
### 官方修复方案  
  
官方已发布修复版本，请升级至 libseccomp v2.6.1 或更高版本。  
  
https://github.com/seccomp/libseccomp/releases/tag/v2.6.1  
### 临时缓解措施  
  
1. 审查现有 seccomp 策略，识别并重写存在冲突的 SCMP_CMP_LT 或 SCMP_CMP_LE 规则  
1. 不要在特权进程中编译不受信任的 seccomp 配置文件  
  
1. 对用户提供的 seccomp 配置设置规则数量上限（建议不超过 10,000 条）  
  
微步产品支撑  
  
  
微步漏洞情报于  
2026年7月2日  
收录该漏洞。  
  
微步下一代威胁情报平台NGTIP及X情报社区已于漏洞收录时向漏洞订阅用户推送该漏洞情报，并将持续推送后续更新；对于已经录入资产的用户，支持实时自动化排查受影响资产。  
  
  

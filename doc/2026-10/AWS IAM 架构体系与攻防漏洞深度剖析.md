#  AWS IAM 架构体系与攻防漏洞深度剖析  
原创 极客零零七
                    极客零零七  极客零零七   2026-10-06 21:30  
  
在现代云原生架构中，网络边界（Perimeter）早已逐步弱化，**身份（Identity）成为了新的安全边界**  
。AWS Identity and Access Management（IAM）作为整个 AWS 生态的核心控制平面，负责管理数以亿计的 API 请求鉴权。  
  
然而，复杂的策略判定逻辑与动态的凭证流转机制，也使其成为了攻防对抗中最激烈的战场。本文将分为两大部分深入剖析：**第一部分解构 IAM 的核心架构与鉴权评估引擎**  
；**第二部分聚焦 IAM 的典型安全风险、权限提升链及真实安全事件技术复盘**  
。  
#### 一、AWS IAM 架构体系与底层决策引擎  
  
IAM 并非简单的“访问控制列表”，而是一个分布式、高度一致且兼顾多层级策略叠加的鉴权体系。  
  
![AWS_IAM-20261006161053.png](https://mmbiz.qpic.cn/sz_mmbiz_png/L7VicJKsiaibFDHF6AicaPlUWsSjErR2pF5UHbkVygWXjMCwCwyUplTgVJ7ClSCtOH8hcoEialRvnfzaLnLibOQibjbEESt6hU7PClcleadUWLfuHI/640?from=appmsg "")  
##### 1. 核心实体模型与凭证生命周期  
  
**Principals（主体）**  
- Root User： 账户最高权威，拥有不可被策略限制的特权（初SCP外）  
  
- IAM User：具备长期静态凭证（Password，AccessKey ID/Secret Access Key）  
  
- IAM Role：无静态密码或者密钥。主体通过调用AWS STS（security Token Service）的AssumeRole、AssumeRoleWithWebIdentity等接口获取具有有效期的临时安全凭证（包含AccessKeyId、SecretAccessKey和SessionToken)。  
  
**Trust Policy（心热策略/AssumeRolesPolicyDocument）**  
  
每一个Role都必须绑定的资源策略，用于声明“谁可以扮演角色‘‘（支持IAM User、其他AWS Account、AWS Service如ec2.amazonaws.com或者外部OIDC IdP）。  
##### 2. 策略类型与六维求交逻辑  
  
在企业多账号体系中，一个请求的最终放行收到多层策略的联合制约。根据AWS官方评估逻辑，有效权限是多层边界的交集（Intersection）  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/L7VicJKsiaibFBuOB1DTlM2ia9oUlCAfAlEVicEZQBcwTFicfLo5OB43ftX9alduFNzt59fd0teUauRXYphJ2q4yqeqbC7LlUhOP5b0RTr0iaJKiaw4/640?wx_fmt=png&from=appmsg "")  
- Service Control Policies (SCPs)  
：组织级（AWS Organizations）护栏，定义成员账户的最大权限范围，不会直接赋予权限，但能实施全局 Deny  
 或界定可用 API 白名单。  
  
- **Permissions Boundary（权限边界）**  
：附加在 IAM User/Role 上的高级特性，用于安全地委托权限创建权限（Delegated Admin），限制被委托人所能赋予的最大权限。  
  
- **Session Policy**  
：在调用 STS 生成临时凭证时动态传入的内联/托管策略，用于缩小临时会话的实际权限。    
  
- **Identity-Based vs. Resource-Based Policies**  
：  
  
- 同账户内：两者任意一个显式 Allow  
 且无 Deny  
 即可放行。      
  
- 跨账户访问：**必须双向允许**  
。被访问资源的 Resource-Based Policy 必须显式授权外部 Principal，且外部 Principal 的 Identity-Based Policy 也必须显式允许调用该资源操作。  
  
![AWS_IAM-20261006161000.png](https://mmbiz.qpic.cn/mmbiz_png/L7VicJKsiaibFDVDbcuHQqD2laDsSTy5GfzwfB06EWd4icpxWenZDiapxDZBIlNdYXiarR2o45YSeGFh43YYgXWuDKoibrUZDa37JrQqRcyFtzSNCo/640?from=appmsg "")  
##### 3. Policy JSON 语法与条件键的高级求值机制  
  
常见的策略配置示例：  
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "EnforceMFAAndSourceRestriction",
      "Effect": "Deny",
      "Action": "s3:*",
      "Resource": "arn:aws:s3:::sensitive-data/*",
      "Condition": {
        "BoolIfExists": {
          "aws:MultiFactorAuthPresent": "false"
        },
        "NotIpAddress": {
          "aws:SourceIp": ["198.51.100.0/24"]
        }
      }
    }
  ]
}
```  
  
**Condition Operator 机制**  
：包含单值操作符（StringEquals  
）、多值集合操作符（ForAnyValue  
, ForAllValues  
）以及存在性修饰符（IfExists  
）。     
  
**上下文键（Context Keys）**  
：  
- aws:PrincipalArn  
 / aws:userId  
：请求方唯一身份标识。      
  
- aws:PrincipalTag/${TagKey}  
：基于属性的访问控制（ABAC）的核心，实现基于组织标签（如 Department=SecOps  
）的动态解耦鉴权。  
  
### 二、IAM 攻击面、横向提权与真实安全事件复盘  
  
IAM 的灵活性为攻击者提供了丰富的利用面。云上渗透往往遵循：**初始凭证获取  IAM 枚举  权限提升（Privilege Escalation）  跨角色/跨账户横向移动（Lateral Movement）  持久化**  
 的链路。  
##### 1.  经典权限提升（PrivEsc）向量与实战原理解析  
  
在IAM 策略配置不当时，低权限主体可通过特定API组合破坏安全边界提升为超级管理员：  
  
**向量A：Policy篡改与版本切换**  
- **涉及权限**  
：iam:CreatePolicyVersion  
 或 iam:SetDefaultPolicyVersion  
  
- **利用机制**  
：IAM 托管策略最多保留5个版本。如果低权限用户拥有 iam:CreatePolicyVersion  
 权限，且目标策略是附加在当前用户（或更高权限实体）身上的，攻击者可以直接创建一个具有 Effect: Allow, Action: *, Resource: *  
 的新版本并设为默认（--set-as-default  
），实现瞬间提权。  
  
**向量B：EC2/Lambda实例角色穿透（PassRole滥用）**  
- **涉及权限**  
：iam:PassRole  
 + ec2:RunInstances  
 / lambda:CreateFunction  
  
- **利用机制**  
：iam:PassRole  
 经常被宽泛配置为 Resource: *  
。攻击者可调用 ec2:RunInstances  
，将高特权 Instance Profile（如管理员角色）附加到新建的 EC2 实例中；或者创建一段包含窃取凭证 payload 的 Lambda 函数并附加高权限角色，随后触发执行，直接捕获高权限 STS 凭证。  
  
**向量C：登陆配置文件中重制（Login Profile Hijacking）**  
- **涉及权限**  
：iam:CreateLoginProfile  
 或 iam:UpdateLoginProfile  
  
- **利用机制**  
：部分系统仅为服务账号分配了 CLI Access Key，未开启控制台登录。如果拥有该权限的攻击者发现管理员账号未设置控制台密码，可调用 CreateLoginProfile  
 强行为其添加登录口令并关闭 MFA，进而直接登入 AWS Console。  
  
##### 2. 深度安全事件复盘与技术归因  
  
**案例 1：Capital One 数据泄露事件（SSRF 与 EC2 元数据服务窃密）**  
  
**事件背景**  
：2019 年，攻击者利用 Capital One 部署在 AWS 上的开源 WAF（ModSecurity）存在的 SSRF 漏洞，窃取了超过 1 亿用户的敏感数据。  
  
![AWS_IAM-20261006161077.png](https://mmbiz.qpic.cn/mmbiz_png/L7VicJKsiaibFAUTEjKIV0VtqibrfDwrkiacj3MbuTcno3aeg9Y2xibeyykdRqNr4D5LeN4ZGTBib9S2Hic8BL16RzNViacYMembAzCeGNWP9kRKFGjc/640?from=appmsg "")  
  
**架构层面的 IAM 缺陷**  
：  
- **过宽的 Role 权限**  
：该 EC2 实例绑定的角色被赋予了遍历、读取全部 S3 存储桶的广泛权限，严重违反最小权限原则。     
  
- **缺少网络/环境上下文限制**  
：STS 凭证未限制源 IP（缺乏 aws:SourceIP  
 或 VPC 终端节点条件限制），导致在外部机器上依然可用。  
  
**官方防御演进**  
：AWS 随后推出了 **IMDSv2**  
（基于面向会话的 PUT  
 请求获取 Token，利用 X-aws-ec2-metadata-token-ttl-seconds  
 和防 SSRF 的 HTTP Header 拦截大部分简单反向代理攻击）。  
  
**案例 2：混乱代理人问题（The Confused Deputy Problem）与 AssumeRole 劫持**  
  
当第三方 SaaS 服务需要代管企业 AWS 资源时，通常要求企业在自己账户内创建一个 IAM Role，并在 Trust Policy 中允许该 SaaS 的 AWS 账号进行 sts:AssumeRole  
。  
  
漏洞机制：SaaS 服务拥有通用 Worker 节点，替多个租户执行维护任务。若 Trust Policy 仅声明：  
```
{
  "Effect": "Allow",
  "Principal": { "AWS": "arn:aws:iam::123456789012:root" }, // SaaS 提供商账号
  "Action": "sts:AssumeRole"
}
```  
  
恶意租户 B 在 SaaS 平台上注册时，故意将待操作的 Role ARN 填入受害者租户A的Role ARN。SaaS 服务在没有上下文隔离的情况下发起 AssumeRole  
，其自身作为合法的“代理人”，代表攻击者成功获取了受害者A的控制权。  
  
技术缓解方案：在 Trust Policy 的 Condition 中强制校验唯一的、不可伪造的外部标识符：  
```
"Condition": {
  "StringEquals": {
    "sts:ExternalId": "UNIQUE-TENANT-SECRET-UUID-FOR-ACCOUNT-A"
  }
}
```  
  
**案例 3：CI/CD 供应链与 OIDC 跨租户通配符滥用**  
  
现代架构抛弃了长期存在 GitHub Secrets 里的 Access Key，转而采用 GitHub Actions 与 AWS IAM 的 **OIDC Federation**  
。  
  
漏洞机制：管理员在配置 IAM Role Trust Policy 时，为了方便使用了松散的通配符  
```
"Condition": {
  "StringLike": {
    "token.actions.githubusercontent.com:sub": "repo:my-org/*:*"
  }
}
```  
  
或者甚至  
```
"Condition": {
  "StringLike": {
    "token.actions.githubusercontent.com:aud": "sts.amazonaws.com"
  }
}
```  
  
只要条件中没有严格绑定具体的 repo:<org>/<repo>:ref:refs/heads/<branch>  
，任何攻击者在自己的 GitHub 个人仓库创建工作流，只要 aud 符合条件，均可直接向 AWS STS 发起认证并成功扮演企业的特权部署角色，实现对生产环境的反向渗透。  
#### 三、 现代 IAM 防御工程演进  
  
抵御基于IAM的高阶攻击，需要从传统的静态规则配置转向动态工程化治理：  
1. 全面启用IMDSv2并强制禁用IMDSv1：在所有的EC2 模板中通过原数据选项强制HttpTokens:required， 并在SCP层面拦截非IMDSv2实例启动。  
  
1. 彻底消灭静态长期凭证：  
  
1. 针对开发者与运维：统一收敛到AWS IAM Identity Center（SSO），结合WebAttachn/FIDO2硬件令牌  
  
1. 针对工作负载：本地服务器使用IAM Roles Anywhere（X.509私钥解密交换STS Token），CI/CD严格收口OIDC Subject细粒度校验。  
  
1. 基于CI/CD的权限左移与自动化治理：  
  
1. 采用IAM Access Analyzer 与开源工具（如Policy Sentry、CloudSplaining）对CloudFormation/Terraform模板进行静态AS T分析，CI阶段拦截潜在提权向量（如 iam:PassRole资源未受限）  
  
1. 依据C loudTrail实际调用日志生成最小权限基线（Least Privilege Profiling），自动收敛超配策略。  
  
 关注「极客零零七」，每周实战攻防干货。  
> 回复「提权」获取 Windows + Linux 提权速查表 · 回复「AD 攻击」获取 AD 域攻击手册  
  
  
  
  
往期推荐  
  
[OWASP 大模型与生成式 AI 应用安全 Top 10（2026版）](https://mp.weixin.qq.com/s?__biz=Mzk2NDgwNjA2NA==&mid=2247486645&idx=1&sn=ff369708c98d6c76f39e5e9a7fcd3097&scene=21#wechat_redirect)  
  
  
[超越传统钩子：现代红队如何利用进程参数投毒绕过 EDR 检测](https://mp.weixin.qq.com/s?__biz=Mzk2NDgwNjA2NA==&mid=2247486640&idx=1&sn=d58e1260cbe74ab907a56e7519a9d842&scene=21#wechat_redirect)  
  
  
[从NTLM Relay 到 Kerberos 认证反射：2026 最新对抗思路](https://mp.weixin.qq.com/s?__biz=Mzk2NDgwNjA2NA==&mid=2247486625&idx=1&sn=64c3329e5aaedff26bd9405586f8d424&scene=21#wechat_redirect)  
  
  
[构建自主 AI 智能体的安全边界：OpenAI Codex 在 Windows 端的沙盒架构演进](https://mp.weixin.qq.com/s?__biz=Mzk2NDgwNjA2NA==&mid=2247486594&idx=1&sn=edc8ebd2ddb046966e425e34ad051f3b&scene=21#wechat_redirect)  
  
  
[深度｜漏洞管理的 9 次失效——从 CVE 到 KEV，27 年我们一直在追，但永远没追上](https://mp.weixin.qq.com/s?__biz=Mzk2NDgwNjA2NA==&mid=2247486562&idx=1&sn=aa4905d5ce2bfc9dd525ac34bc64cedf&scene=21#wechat_redirect)  
  
  
[GRE 隧道：在公网上凿一条"私家地道"，工程师必须吃透的 7 个核心要点](https://mp.weixin.qq.com/s?__biz=Mzk2NDgwNjA2NA==&mid=2247486520&idx=1&sn=fb75641ebe37e88e2782e8bd97b3154e&scene=21#wechat_redirect)  
  
  
[赶不走的幽灵：AD域持久化技术与检测对抗](https://mp.weixin.qq.com/s?__biz=Mzk2NDgwNjA2NA==&mid=2247486414&idx=1&sn=759cc0285355dbc59666ac5410c36a13&scene=21#wechat_redirect)  
  
  
[被遗忘的攻击面：AD CS证书服务攻击完全指南（ESC1-ESC11）](https://mp.weixin.qq.com/s?__biz=Mzk2NDgwNjA2NA==&mid=2247486406&idx=1&sn=b8c59cafc975e0da117ed987fcdca266&scene=21#wechat_redirect)  
  
  
  

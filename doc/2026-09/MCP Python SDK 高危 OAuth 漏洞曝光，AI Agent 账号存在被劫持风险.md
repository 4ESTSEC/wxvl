#  MCP Python SDK 高危 OAuth 漏洞曝光，AI Agent 账号存在被劫持风险  
 看雪学苑   2026-09-30 09:59  
  
近日，  
MCP Python SDK 官方版本被曝出严重的 OAuth 认证缺陷。  
攻击者如果能诱导 AI Agent 或其客户端连接到恶意 MCP 服务器，就有可能窃取 OAuth 认证材料，并最终接管用户或代理的 AI Agent 账号。  
  
  
受影响版本包括 MCP Python SDK 1.9.1–1.29.1（1.x 分支）和 2.0.0–2.1.1（2.x 分支）。  
建议相关开发者尽快升级到1.30.0或2.2.0。  
  
  
  
  
**MCP 协议生态中的 OAuth 信任问题**  
  
  
MCP 是一种开放协议，主要用于让 AI 助手和智能代理连接外部工具、API 与数据源。在实际应用中，AI Agent 常常需要通过 OAuth 完成身份认证，才能调用邮箱、日历、企业系统等受保护资源。  
  
  
问题在于，部分基于 MCP Python SDK 实现的客户端，在处理 OAuth 服务发现时过度信任了 MCP 服务器返回的数据。这使得攻击者有机会通过恶意服务器篡改 OAuth 配置，将认证流程中的敏感信息导向攻击者控制的基础设施。  
  
  
  
  
**看似正常登录，实则凭据已被窃取**  
  
  
本次漏洞的核心风险，在于 SDK 对 MCP 服务器下发的 OAuth 配置缺少严格校验。  
  
  
1. 恶意 MCP 服务器诱导客户端连接  
  
  
攻击者可以通过多种方式让受害者连接到恶意 MCP 服务器，例如：  
  
- 恶意 MCP 注册表  
  
- 仿冒包名或相似域名  
  
- 提示注入  
  
- 网络劫持  
  
- 内网或路由层面的访问篡改  
  
  
一旦 AI Agent 客户端连接到恶意服务器，攻击者即可开始干预 OAuth 流程。  
  
  
  
2. 触发旧版 OAuth 发现逻辑  
  
  
恶意服务器会向客户端返回异常的授权服务器发现信息，迫使 SDK 进入旧版兼容路径。在该路径下，SDK 会直接接受由 MCP 服务器提供的 OAuth 配置。  
  
  
问题在于，SDK 当时并未充分校验这些配置中的 `issuer`（发行方）是否与预期身份提供商一致。  
  
  
  
3. 攻击者实施“钓鱼式 OAuth 劫持”  
  
  
这里的危险之处在于，攻击过程可能并不明显。  
  
  
恶意服务器可以让用户在浏览器中看到真实的 Google、Okta 或 Microsoft Entra ID 登录页面，从而降低用户警觉。但与此同时，它却把后续的令牌交换地址指向攻击者控制的服务器。  
  
  
也就是说，用户以为自己只是在正常登录第三方服务，实际上 OAuth 流程已被暗中劫持。  
  
  
  
  
**为什么连 PKCE 也没能挡住攻击？**  
  
  
根据披露信息，受影响 SDK 在旧版兼容路径中存在两个关键问题：  
  
  
问题一：未严格校验 OAuth issuer  
  
  
OAuth 配置本应来自可信身份提供商，但在漏洞场景下，SDK 信任了 MCP 服务器直接下发的元数据。攻击者因此可以冒用真实授权服务器的配置，却把令牌请求导向恶意端点。  
  
  
  
问题二：客户端凭据与 PKCE 保护被绕过  
  
  
攻击成功后，恶意服务器可能获取到：  
  
- 授权码（Authorization Code）  
  
- 客户端密钥（Client Secret）  
  
- PKCE Code Verifier  
  
  
其中，PKCE 的设计目的之一，正是防止攻击者在仅窃取授权码的情况下完成令牌交换。但在本次攻击中，恶意服务器同时拿到了授权码和 PKCE 校验信息，因此可以直接向真实身份提供商完成交换，骗取有效访问令牌。  
  
 PKCE 能提升授权码流程安全性，但前提是令牌交换环节没有被恶意端点接管。如果客户端本身把敏感信息发给攻击者控制的服务器，PKCE 也难以完全保护流程。  
  
  
  
  
**受影响范围：多个 OAuth 提供者均存在风险**  
  
  
本次事件影响范围并不单一，多个 OAuth 提供者都可能被波及：  
  
- OAuthClientProvider  
  
- ClientCredentialsOAuthProvider  
  
- PrivateKeyJWTOAuthProvider  
  
- 旧版部署中的 RFC7523OAuthClientProvider  
  
  
其中，交互式 OAuth 提供者风险等级为6.5，因为仍需用户参与登录；而机器对机器类提供者风险等级达到7.5，因为无需用户交互，攻击可能更隐蔽。  
  
  
  
  
**哪些场景更危险？**  
  
  
AI Agent 环境面临的风险尤其值得关注。  
  
  
如果模型能够自主选择 MCP 服务器，而系统又缺少严格的服务器校验和权限边界，那么攻击者就可能通过恶意 Registry、仿冒插件、提示注入或网络欺骗，把 AI Agent 引导到恶意服务上。一旦成功，攻击者可能长期持有有效令牌，进而访问用户或企业授权过的资源。  
  
  
  
  
**官方修复建议：立即升级并补齐 issuer 校验**  
  
  
目前，官方已发布修复版本。开发者应尽快完成以下处理：  
  
  
1. 升级 SDK  
  
  
- 1.x 分支：升级到1.30.0  
  
- 2.x 分支：升级到2.2.0  
  
  
修复版本已加强对授权服务器 `issuer` 的校验，并会拒绝来自预期之外提供商的元数据。  
  
  
  
2. 显式配置 expected issuer  
  
  
对于使用 ClientCredentialsOAuthProvider 或 PrivateKeyJWTOAuthProvider 的系统，除升级外，还应通过 issuer= 参数明确配置预期的身份提供商。  
  
  
  
3. 清理历史凭据并评估泄露风险  
  
  
如果应用在修复前已经连接过不可信的 MCP 服务器，建议立即采取以下措施：  
  
- 清除旧的 OAuth 注册信息  
  
- 轮换客户端密钥  
  
- 撤销可能暴露的令牌  
  
- 审计相关访问日志和异常行为  
  
  
本次事件再次说明，AI Agent 生态的安全风险并不只来自模型本身，也来自工具调用、插件接入和身份认证链路中的信任边界。  
  
  
一个看似正常的第三方登录页面，并不能证明背后的 OAuth 流程是安全的。真正可靠的认证体系，必须建立在严格的 issuer 校验、端点认证、权限最小化和异常行为检测之上。  
  
  
对于企业而言，尤其需要对 AI Agent 的工具接入策略进行梳理：哪些服务器允许连接、哪些 OAuth 提供者值得信任、哪些操作涉及高权限数据，都应被纳入统一的安全治理范围。  
  
  
资讯来源： Cybersecurity News / Cycode 相关技术披露与安全公告  
  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/Cpo2XCpI7K2InXRsnpOjFsJ1FmIxPRT3OibicWYibSfziak2DqcVs3CGbJlcQiaUwbYywlCrWE7L3TIX5NLbYVIbDogmGPzIpsxbQxS6tEPmxG4E/640?wx_fmt=jpeg&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/Cpo2XCpI7K39KkVvoF39X7An8GiazeicNUpOJuRnnlMlTRhibKZzrciaT9mMGY8NtjzjZh1FYR2vtqQQLuy2svCbJE6Q8Yc7NYAxgMHEib3ft484/640?wx_fmt=gif&from=appmsg "")  
  
**球分享**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/Cpo2XCpI7K1iamQbvO3IAbicNjk92LWADV2z6sSyyibGwTibn6wLcichlg47DVIQYyLof3AUeuhu2Ka48lQZicQtDggCsjePsRdMRfGsVS91libZ2o/640?wx_fmt=gif&from=appmsg "")  
  
**球点赞**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/Cpo2XCpI7K2LsPXTcUskoiazypqGNFU5aia0fgOsqXd1sGOmg6qsPSNLXtrLvGB4lfXR6jtIkforZMdI4Kb9FQkgywro2mMMCL48CXZibwMribE/640?wx_fmt=gif&from=appmsg "")  
  
**球在看**  
  

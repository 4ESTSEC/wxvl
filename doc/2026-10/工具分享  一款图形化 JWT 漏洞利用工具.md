#  工具分享 | 一款图形化 JWT 漏洞利用工具  
zjeweler
                    zjeweler  篝火信安   2026-10-08 02:00  
  
![](https://mmbiz.qpic.cn/mmbiz_png/prEia0ibIXVVsUlibPtqpjXrPggiaJOwEcY1XiaiazmuFwk2Lh2T96OSN5l1ev3WEgiaDFfxVSKwBTsdUXqRPmAb0Dic3wPbrqaqBYn5ftmPkoTeJY8/640?wx_fmt=png&from=appmsg "")  
  
  
简介  
  
一款图形化 JWT 漏洞利用工具，覆盖常见 JWT 漏洞验证向量。解码 / 编码 / 签名校验 / 漏洞一键生成 / 字典爆破 / Payload 导出，一站式完成。  
  
JWT是什么？可点击下发链接了解。  
  
[科普时间 | JWT（JSON Web Token）到底是什么？](https://mp.weixin.qq.com/s?__biz=MzIyNzc3OTMzNw==&mid=2247486555&idx=1&sn=cc1648edb22e88254d3a19b5cca21b35&scene=21#wechat_redirect)  
  
  
  
功能概览  
- 解码 / 编码 / 签名校验：自动拆分 Header / Payload / Signature, 美化 JSON 输出。  
  
- 多算法：HS256 / HS384 / HS512 / RS256 / RS384 / RS512 / ES256 / ES384 / ES512 / none。  
  
- 多密钥编码：UTF-8 / Base64 / Hex / MD5 / 16-MD5 / ALL (ALL 一次性试 5 种).  
  
- 漏洞验证 (一键生成可粘贴的 Token)：  
  
重要: 所有漏洞按钮生成的 Token 都以右侧 Header / Payload 编辑器当前内容为准。  
  
工作流: 粘贴 Token → 点 => 解码 → 在右侧修改 (如把 sub 改成 admin) → 点漏洞按钮。  
  
编辑器为空时才回退解析输入框里的原 Token。你改什么就生成什么。  
  
自签名攻击自动续期：jwk / jku / x5c / x5t / kid / Psychic / RS→HS 这类"用攻击者自己密钥重签"的向量，若原 exp 已过期会自动续期为 1 小时后 (备注列会注明)，否则服务端会因 Signature has expired 拒掉。纯篡改类 (NoVerify / alg=none / Claim)不动 exp, 方便你手动控制。  
  
自动攻击组 (改完右边的信息后一键利用)：  
```
CVE-2015-9235：alg=none绕过 (none / None / NoNe / NONE 四种)
未验证签名攻击：(NoVerify)
只改 Payload (无密钥): 仅替换中间段, 签名保持原样
alg=none + 自定义 Payload + 空签名： 一键组合 (原样使用编辑器内容, 不注入默认字段)
CVE-2018-0114：JWKS 公钥注入 (实时生成 RSA 密钥对, 保留原 header)
CVE-2020-28042：空签名
CVE-2022-21449：Psychic Signatures (ES256/384/512 全 0 签名)
kid 注入: SQL 注入 / 路径遍历 (`/etc/passwd`、`/dev/null`、`C:\Windows\win.ini`) / 命令注入
Claim 滥用: 长期 / 已过期 / 未来 nbf / 删除 exp|nbf
Header 非标准字段提示: 检测 KID / JKU / JWK 等潜在注入点
一键全检测: 一次性跑所有免输入生成器, 适合 Intruder 批量重放
```  
  
手动攻击组 (需输入 / 密钥):  
```
RS->HS 混淆 (需公钥): Secret 框支持直接粘贴 JWK JSON 自动转 PEM; 也支持 PEM 文本 / 文件路径.
共模攻击 (需 2 个 token): RSA 共模攻击, 从同一私钥签发的两个 RS256 token 恢复公钥并自动填入  
```  
  
Secret, 再点 RS->HS 完成攻击. - jku 注入 (需托管 URL): 输入自己的公网服务器地址，一键生成payload + 自动产出要托管的命令 (自动复制到剪贴板)，将命令放到自己的服务器运行一下，直接使用工具中生成的payload就可以了。  
  
字典离线爆破：  
```
多线程 (基于 ThreadPoolExecutor)
支持超大字典流式处理
内置弱口令字典 + 可加载自定义字典
找到密钥后自动填入 Secret 框
```  
  
Fuzz 文件支持：  
```
可生成 Intruder 风格的 Payload 文件
每个攻击向量都打上 CVE / 注释
```  
  
PEM 支持：直接拖入公钥/私钥文件, 在 RS256 校验与 RS→HS 混淆中自动识别。  
  
项目结构  
```
PyJWTInspector/
├── PyJWTInspector.pyw   # PyQt6 GUI 主程序
├── jwt_codec.py         # JWT 编解码与签名校验核心库
├── cracker.py           # 字典爆破模块
├── vulns.py             # 漏洞 Payload 生成器 (全部以右侧编辑器内容为准)
├── vulns_optimized.py   # 共模攻击 gmpy2 加速版本
├── export_burp_payloads.py  # 命令行工具：导出 Burp Intruder Payload
├── password/            # 字典文件目录 (内置 1 份, 可自行添加)
│   └── password-大.txt
├── requirements.txt     # Python 依赖
└── README.md
```  
  
  
工具使用  
  
下载直接双击 PyJWTInspector.exe 启动。也可以下载源码，运行PyJWTInspector.pyw。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/prEia0ibIXVVsaSQl4D9MibVmyibauAsFOR3cOzZ68I9SERPqZvDx0gevvcpgXEsBxAGTib9LvRjQ5qtdAoTutmbUjNzwX7RM4xMHn5jTQ7dPfrM/640?wx_fmt=png&from=appmsg "")  
  
运行之后查看工具页面，左上角为数据编码的token值的位置，右边显示解码结果，可以点击校验进行签名的验证。  
  
中间是自动化检测漏洞和手动攻击模块。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/prEia0ibIXVVujl8C9JeJBebx9205kG68tfFMqZ3NfsDn83DYDxfiaExNCRWCZdephS2PgibB7uib89iaysYB70icicAgrCkg5SttApnKxmW1uZanAE/640?wx_fmt=png&from=appmsg "")  
  
最下面是对加密的数据进行密钥的暴力暴力猜解，  
相同目录下放password文件夹，会默认加载这个文件夹里面的字典（直接下拉选择）。  
相同目录下放password文件夹，会默认加载这个文件夹里面的字典（直接下拉选择）。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/prEia0ibIXVVsO8cwfnczouQTiaL96LtkaLefwDBxWvc2wahTPD98B9DtmO1UfX3aegnlNmKichweISuUWP7l8sTRn0WXicT28erHNMcT4tTpVGQ/640?wx_fmt=png&from=appmsg "")  
  
  
## 免责声明  
  
本工具仅供   
授权渗透测试 / CTF / 学习研究 使用, 请勿用于非法用途。  
  
使用本工具即代表您已阅读并同意：  
- 仅在您拥有明确授权的系统上使用此工具  
  
- 对任何未授权使用导致的后果承担全部责任  
  
- 工具作者不对任何滥用行为负责  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/CQf7uHzmVb3icxXWABkpMvXDJ1aDF6RgkCFLMvzDgLEx7jjY4A1n7yTEc2AZmg5CFFoeHJLb3AiblNHRLVFBqlfw/640?wx_fmt=gif&from=appmsg "")  
  
```
```  
  
  

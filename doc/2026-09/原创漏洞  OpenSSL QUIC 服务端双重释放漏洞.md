#  原创漏洞 | OpenSSL QUIC 服务端双重释放漏洞  
微步情报局
                    微步情报局  微步在线研究响应中心   2026-09-21 09:05  
  
漏洞概况  
  
  
OpenSSL 是开源密码学工具包与 TLS 库，较新版本提供 QUIC 协议实现，供应用作为 QUIC 服务端处理入站连接。  
  
近日，OpenSSL 官方发布通告，  
修复了OpenSSL QUIC 服务端 INITIAL处理 双重释放漏洞（CVE-2026-18798）。  
该漏  
洞由  
微步XGP  
T  
发现  
并  
报送   
OpenSSL   
官方。  
  
OpenSSL QUI  
C 服务端  
在处理入站 INITIAL 包时存在双重释放漏洞，未认证攻击者向受影响服务发送畸  
形 IN  
ITIAL 包即可触发堆损坏，  
导致 QUIC 服务进程退出（拒绝服务）；满足特定利用条件时，可造成远程代码执行。  
建议受影响用户  
尽快修复。  
  
```
/* port_default_packet_handler — 修复前 */
qrx = ossl_qrx_new(...);
if (!ossl_qrx_validate_initial_packet(qrx, ...))
    goto undesirable;
port_bind_channel(..., qrx, &new_ch);   /* 原始连接 ID 过短则 enrol_odcid 失败，bind 在此释放一次 */
if (new_ch == NULL)
    goto undesirable;
if (qrx != NULL)
    qrx = NULL;   /* 仅成功时放弃释放 */
undesirable:
    ossl_qrx_free(qrx);   /* 失败时指针仍在 —— 第二次释放 */
```  
  
QUIC 服务端默认包处理函数   
`port_default_packet_handler`  
 在校验入站   
`INITIAL`  
 时创建记录层接收对象（QRX）。校验通过后，handler 把这块QRX交给 `port_bind_channel`。通  
道创建失败  
时  
，bind 会释放该 QRX，而 handler 会在错误处理中再次释放同一对象，同一块 QRX 被释放两次，构成双重释放，造成堆损坏。  
  
（完整漏洞情报请查阅https://x.threatbook.com/v5/vul/XVE-2026-54525）  
  
漏洞处置优先级(VPT)  
  
  
**综合处置优先级：**  
中风险  
<table><tbody><tr><td rowspan="3" style="border: 1px solid rgb(221, 221, 221);padding: 12px;text-align: left;vertical-align: top;font-weight: bold;background-color: rgb(248, 249, 250);"><section><span leaf="">基本信息</span></section></td><td data-colwidth="192" style="border: 1px solid rgb(221, 221, 221);padding: 12px;text-align: left;vertical-align: top;"><section><span leaf="">微步编号</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">XVE-2026-54525</span></section></td></tr><tr><td data-colwidth="192" style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">CVE编号</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">CVE-2026-18798</span></section></td></tr><tr><td data-colwidth="192" style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">漏洞类型</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">缓冲区错误</span></section></td></tr><tr><td rowspan="5" style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;font-weight:bold;background-color:#f8f9fa;"><section><span leaf="">利用条件评估</span></section></td><td data-colwidth="192" style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">利用漏洞的网络条件</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">远程</span></section></td></tr><tr><td data-colwidth="192" style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">是否需要绕过安全机制</span></section></td><td><section><span leaf="">否</span></section></td></tr><tr><td data-colwidth="192" style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">对被攻击系统的要求</span></section></td><td><p data-pm-slice="0 0 []"><span style="color: #000000;"><span leaf="">启用 QUIC 服务端监听</span></span></p></td></tr><tr><td data-colwidth="192" style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">利用漏洞的权限要求</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">无须用户权限</span></section></td></tr><tr><td data-colwidth="192" style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">是否需要受害者配合</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">否</span></section></td></tr><tr><td rowspan="2" style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;font-weight:bold;background-color:#f8f9fa;"><section><span leaf="">利用情报</span></section></td><td data-colwidth="192" style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">是否有POC</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">是</span></section></td></tr><tr><td data-colwidth="192" style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">已知利用行为</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">暂无</span></section></td></tr></tbody></table>  
漏洞影响范围  
  
<table><tbody><tr><td style="border: 1px solid rgb(221, 221, 221);padding: 12px;text-align: left;vertical-align: top;font-weight: bold;background-color: rgb(248, 249, 250);"><section><span leaf="">产品名称</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">Openssl</span></section></td></tr><tr><td style="border: 1px solid rgb(221, 221, 221);padding: 12px;text-align: left;vertical-align: top;font-weight: bold;background-color: rgb(248, 249, 250);"><section><span leaf="">受影响版本</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">4.0.0&lt;=version&lt;4.0.2、3.6.0&lt;=version&lt;3.6.4、3.5.0&lt;=version&lt;3.5.8</span></section></td></tr><tr><td style="border: 1px solid rgb(221, 221, 221);padding: 12px;text-align: left;vertical-align: top;font-weight: bold;background-color: rgb(248, 249, 250);"><section><span leaf="">有无修复补丁</span></section></td><td style="border:1px solid #ddd;padding:12px;text-align:left;vertical-align:top;"><section><span leaf="">有</span></section></td></tr></tbody></table>  
修复方案  
  
### 官方修复方案  
  
官方  
漏洞公告  
：  
  
https://openssl-library.org/news/secadv/20260825.txt  
  
官方已发布新  
版本修复，请访问链接下载：  
  
https://github.com/openssl/openssl/releases  
### 临时缓解措施  
  
1、通过防火墙、安全组等访问控制策略，阻断受影响服务的 QUIC UDP 端口；需对外提供服务时，仅允许可信来源访问。策略应覆盖 IPv4、IPv6 及源站直连入口。  
  
2、如业务无需使用 QUIC，建议暂时禁用相关监听。对于提供 HTTP/3 的服务，可在确认兼容性后，使用 HTTP/2 或 HTTP/1.1 承载业务。  
  
微步产品支撑  
  
  
微步漏洞情报于  
2026-08-25  
收录该漏洞。  
  
微步下一代威胁情报平台NGTIP及X情报中心已于漏洞收录时向漏洞订阅用户推送该漏洞情报，并将持续推送后续更新；对于已经录入资产的用户，支持实时自动化排查受影响资产。  
  

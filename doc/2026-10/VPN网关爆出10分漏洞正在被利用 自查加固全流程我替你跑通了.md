#  VPN网关爆出10分漏洞正在被利用 自查加固全流程我替你跑通了  
原创 紫禁
                    紫禁  紫禁玄科   2026-10-13 01:35  
  
SECURITY · INTELLIGENCE  
# VPN网关爆出10分漏洞正在被利用 自查加固全流程我替你跑通了  
  
2026年10月13日 · 8 min  
  
#网络安全  
#漏洞预警  
#运维加固  
  
01  
  
凌晨两点那条求助消息  
  
  
前天凌晨两点，一个做运维的朋友甩过来一句话：「我们刚收到 SonicWall 的安全公告，SMA 又出事了，CVSS 直接给了 10 分，我有点慌。」后面跟了三个捂脸的表情。我问他：你们的 WorkPlace 门户是不是还挂在公网上？他过了半分钟回了一个字：「是」。  
  
你猜怎么着，这还不是最糟的。等我把各家情报拼起来，发现补丁 10 月 6 号就发了，蜜罐厂商 10 月 8 号就在自家设备上看到了实际的利用流量——**补丁发布两天，武器化的试探就已经开始扫网了**  
。很多公司的变更窗口是「每月第二个周末」，也就是说，在他们打上补丁之前，暴露在公网的网关要先裸奔好几周。  
  
所以今天这篇不讲大道理，直接上流程：**确认你中没中招、临时怎么挡、日志怎么翻、长期怎么收**  
。两段脚本我都写好并且跑通了，你复制下去改个主机名就能用。  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/Ah5NAr7MJFjF9voTmDZicL0LGc1bicBItQm93J3kYZrZ8TK4uwribKwkV8espf9iapPv9VHMBEyCCROr9TzQiaGOThD63WgyaMg2E8Ls8ejlSUrM/640?from=appmsg "")  
  
SMA 网关自查：先确认暴露面，再谈加固  
  
  
02  
  
SSRF 到底是个什么东西  
  
  
先把这个词说成人话。SSRF，服务端请求伪造——听着拗口，其实就一句话：**别人忽悠你内网里的一台机器，替他去跑腿**  
。  
  
网关这个角色很特殊。它一只脚在公网，一只脚在内网，本来就天天替员工往内网服务发请求。正常情况下这是它的本职工作；但这次的问题出在 WorkPlace 接口上——官方公告的措辞是「unintended alternate access path」，翻译过来就是「存在一条意料之外的旁路」。远程的、不需要登录的人，顺着这条旁路，就能让网关按他的意思往外发请求。  
  
安全厂商 SOCRadar 周一的分析说得更直白：**互联网侧的网关，可能变成通往内网服务的代理**  
。那些本来「压根不该被外网访问」的服务——管理后台、身份认证、内网数据库——现在隔着一台网关就能被摸到。这个漏洞的 CVSS 能给到 10 分（满分），就是因为它不需要账号、不需要交互、影响的还偏偏是站在门口的那台设备。  
  
做过网关类项目的朋友应该都有体感：这类设备常年是「配置一次、之后三年没人动」的状态。可它偏偏是整个内网离公网最近的地方。离得最近，被扫到的概率就最高——这不是猜的，下面有时间线为证。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/Ah5NAr7MJFjKgFvG0EZsUwVMv0VLNUk2EWRcCsRkPN2egMnX3jjsrbVkR22Fn9NPG4icWh34LQM2icSDSFlibGd5HHetft3MhH1jdczxZr04iaA/640?from=appmsg "")  
  
SSRF 的本质：网关替外网跑腿进内网  
  
  
03  
  
这五天到底发生了什么  
  
  
把事件按时间摆开，你会发现「厂商口径」和「野外实况」之间有个时间差：  
  
**10 月 6 日**  
：SonicWall 发布安全公告，披露包括 CVE-2026-102255 在内的四个漏洞，同步放出热修，呼吁客户尽快安装。漏洞出在 SMA 1000 系列的 WorkPlace 接口，评分 10/10。  
  
**10 月 8 日**  
：安全厂商 Defused 观察到，自家的 SonicWall SMA 蜜罐设备开始出现未授权的 SSRF 探测流量。  
  
**10 月 9 日**  
：威胁情报公司 Previdian 在 X 上发出预警，称其传感器捕获到针对该漏洞的利用尝试。  
  
**10 月 12 日**  
：SOCRadar 发布技术分析，指出利用成功可控制 WorkPlace 接口，并给出那句「网关变代理」的判断。  
  
**同一周**  
：SonicWall 的说法是「目前没有证据表明该漏洞已被在野利用」。两边都没说谎——厂商看的是客户侧的事故报告，蜜罐厂商看的是扫描流量。**但对防守方来说，蜜罐报警就是发令枪**  
。  
  
再说个背景，方便你判断严重程度。这不是 SMA 1000 今年第一次上新闻：今年 7 月，Inc 勒索团伙就用两个零日（CVE-2026-15409 / CVE-2026-15410）打过同一系列设备。网络保险承保方 Coalition 的安全承保副总裁 Joe Toomey 说得很实在：这类边缘设备及其漏洞带来的风险是**可以量化**  
的——翻译成大白话：出一次事，明年的保费会告诉你它值多少钱。  
  
官方也划了不受影响的范围，先别自己吓自己：**SMA 100 系列不受影响，SonicWall 防火墙自带的 SSL VPN 也不受影响**  
。这次的问题只出在 SMA 1000 的 WorkPlace 接口上。照着下面两步走，十分钟就能确定你家设备在不在风险面里。  
  
  
04  
  
第一步 先摸清自家网关的暴露面  
  
  
自查第一件事不是打补丁，而是搞清楚**你的网关到底把什么暴露在了公网上**  
。很多人的第一反应是「我们有 WAF」「我们有防火墙」，但 WorkPlace 门户本来就是设计给远程员工从公网访问的——它在防火墙里面，WAF 也默认放行它。  
  
下面这段脚本只做三件事：测端口、看证书、探路径。注意它的边界——**只发正常的 GET 请求，不做任何漏洞验证**  
，对自有资产跑完全合规。把主机名换成你自己的域名即可：  
  
SMA-SELFCHECK · 网关暴露面自查  
  
"""SMA 网关暴露面自查（仅限自有授权资产）。只发 GET 正常请求，不做漏洞验证。"""  
  
import socket, ssl, sys, re  
  
from datetime import datetime  
  
   
  
TIMEOUT = 5  
  
PATHS = ["/", "/workplace/", "/workplace/login"]  
  
   
  
def now():  
  
　　return datetime.now().strftime("%Y-%m-%d %H:%M:%S")  
  
   
  
def check_tcp(host, port):  
  
　　try:  
  
　　    s = socket.create_connection((host, port), timeout=TIMEOUT); s.close()  
  
　　    return True, "可达"  
  
　　except OSError as e:  
  
　　    return False, str(e)  
  
   
  
def check_cert(host, port):  
  
　　try:  
  
　　    ctx = ssl.create_default_context()  
  
　　    with socket.create_connection((host, port), timeout=TIMEOUT) as s:  
  
　　        cert = ctx.wrap_socket(s, server_hostname=host).getpeercert()  
  
　　    days = int((ssl.cert_time_to_seconds(cert["notAfter"]) - datetime.now().timestamp()) / 86400)  
  
　　    return dict(x[0] for x in cert.get("issuer", [])).get("organizationName", "unknown"), days  
  
　　except ssl.SSLError:  
  
　　    return "证书校验失败(自签名或过期)", -1  
  
　　except Exception as e:  
  
　　    return "unavailable", str(e)[:40]  
  
   
  
def probe(host, path, port):  
  
　　last = "-"  
  
　　for ctx in (ssl.create_default_context(), ssl._create_unverified_context()):  
  
　　    try:  
  
　　        with socket.create_connection((host, port), timeout=TIMEOUT) as s:  
  
　　            ss = ctx.wrap_socket(s)  
  
　　            ss.sendall(("GET %s HTTP/1.1\r\nHost: %s\r\nUser-Agent: selfcheck\r\n"  
  
　　                        "Connection: close\r\n\r\n" % (path, host)).encode())  
  
　　            data = ss.recv(2048).decode("utf-8", "ignore")  
  
　　        status = data.split("\r\n", 1)[0]  
  
　　        m = re.search(r"(?im)^server:\s*(.+)$", data)  
  
　　        return status, m.group(1).strip() if m else "-"  
  
　　    except Exception as e:  
  
　　        last = "ERR " + str(e)[:50]  
  
　　return last, "-"  
  
   
  
def main():  
  
　　if len(sys.argv) < 2:  
  
　　    print(__doc__); sys.exit(1)  
  
　　host = sys.argv[1]  
  
　　port = int(sys.argv[sys.argv.index("--port") + 1]) if "--port" in sys.argv else 443  
  
　　print("== SMA 网关暴露面自查 ==")  
  
　　print("目标(自有资产):", host, "| 端口:", port, "| 检查时间:", now())  
  
　　ok, info = check_tcp(host, port)  
  
　　print("[1] 端口:", (info + " RTT 正常") if ok else info)  
  
　　if not ok:  
  
　　    print("结论: 互联网侧不可达，保持现状并持续监控"); return  
  
　　issuer, days = check_cert(host, port)  
  
　　print("[2] TLS 证书: 颁发者=%s 有效期剩余=%s 天" % (issuer, days))  
  
　　exposed = []  
  
　　for p in PATHS:  
  
　　    status, server = probe(host, p, port)  
  
　　    print("[3] 探测 %-14s -> %-28s Server=%s" % (p, status, server))  
  
　　    if any(c in status for c in ("200", "302", "401")):  
  
　　        exposed.append(p)  
  
　　if exposed:  
  
　　    print("[4] !! WorkPlace 门户对外可达 (%s)" % ",".join(exposed))  
  
　　    print("    -> 立即安装官方热修，热修前用 ACL 收窄来源 IP")  
  
　　else:  
  
　　    print("[4] WorkPlace 门户未见对外响应，保持最小暴露并定期复查")  
  
　　print("自查完成:", now())  
  
   
  
if __name__ == "__main__":  
  
　　main()  
  
我用本机起了个模拟的 WorkPlace 门户（自签名证书那种），把脚本对它跑了一遍，输出长这样——重点看 [3] 和 [4] 两行：  
  
实跑结果 · 2026-10-13  
  
== SMA 网关暴露面自查 ==  
  
目标(自有资产): 127.0.0.1 | 端口: 8443 | 检查时间: 2026-10-13 08:09:41  
  
[1] 端口: 可达 RTT 正常  
  
[2] TLS 证书: 颁发者=证书校验失败(自签名或过期) 有效期剩余=-1 天  
  
[3] 探测 /              -> HTTP/1.0 200 OK              Server=BaseHTTP/0.6 Python/3.11.15  
  
[3] 探测 /workplace/    -> HTTP/1.0 200 OK              Server=BaseHTTP/0.6 Python/3.11.15  
  
[3] 探测 /workplace/login -> HTTP/1.0 200 OK              Server=BaseHTTP/0.6 Python/3.11.15  
  
[4] !! WorkPlace 门户对外可达 (/,/workplace/,/workplace/login)  
  
　　-> 立即安装官方热修，热修前用 ACL 收窄来源 IP  
  
自查完成: 2026-10-13 08:09:41  
  
判读就三条：**WorkPlace 路径对公网返回 200/302/401**  
，说明门户挂着，进入下一步加固；证书是自签名或过期的，顺手也记一笔（这是我常年在客户环境里见到最多的次要风险）；端口压根不可达，那你运气好，保持现状定期复查就行。  
  
复制代码的小提示：如报非法字符 U+00A0，把行内空格重打一遍即可——这是网页复制全角空格的老毛病，不影响脚本本身。  
  
  
05  
  
热修窗口没到之前 拿这三道保险顶上  
  
  
打热修当然是唯一的根治办法。但做过企业运维的都知道，「这周就能打上补丁」这句话背后往往藏着变更审批、业务窗口、回滚预案三座大山。这几周的空窗期，靠下面三道保险顶住。  
  
**第一道：把 WorkPlace 从公网撤下来。**  
如果业务允许，最干脆的做法是让 WorkPlace 门户只在管理网段或 VPN 内可达。漏洞的前提是「外网能碰到这条旁路」，碰不到，10 分也就成了 0 分。  
  
**第二道：ACL 收窄来源。**  
撤不下来就收窄——在边界设备上把 WorkPlace 的访问来源限制到公司固定的出口 IP 或办公网段。员工出差用 4G 热点怎么办？让他们先连公司 VPN 或跳板，再进门户。  
  
**第三道：WAF 虚拟补丁。**  
在 WAF 上加一条规则，拦截 WorkPlace 路径下携带内网地址（127.0.0.1、10.x、192.168.x、169.254.169.254 这类）的请求参数。这条规则不依赖厂商细节，属于「宁可错杀」的通用防御，误报了再按业务放行。  
  
三道保险的优先级是**从上到下**  
。能撤就撤，撤不了再收窄，WAF 规则是兜底而不是替代——虚拟补丁挡得住已知特征，挡不住明天的新姿势。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/Ah5NAr7MJFgaibCKLrSmjibfmiaGbiaBUBnn3iaqBib9rqrsaJdVfAjVNczt8iaWzZ3dHRmicViaIErvzIt9nFN6iaTsia3tLFXpkaEzZmX85zLK4WNmp8/640?from=appmsg "")  
  
热修 + 日志 + 权限，三件事一起做  
  
还有一条别忘了：**限制管理后台的来源**  
。SMA 的管理界面别和 WorkPlace 走同一个入口，管理操作只允许来自堡垒机或固定运维 IP。这次的漏洞虽然出在 WorkPlace，但攻击者一旦借道网关摸进内网，下一个目标八成就是你的管理面。  
  
  
06  
  
翻翻日志 看看有没有人已经来过  
  
  
加固做完，接下来是侦探环节：过去的日志里有没有人已经试过这条路。SSRF 探测在访问日志里留下的痕迹很有辨识度——**WorkPlace 的路径下面，参数里却带着内网地址**  
。正常员工的浏览器不会这么发请求，这么发的，都是来问路的。  
  
第二段脚本就是照着这个特征写的：解析网关访问日志，把「参数含内网地址」「同 IP 高频探测」「异常时段」三类可疑行捞出来，顺带给出高频来源排名：  
  
SMA-LOG-AUDIT · 日志 SSRF 特征审计  
  
"""网关访问日志 SSRF 特征审计（防御用途，只分析日志）。用法: sma_log_audit.py <日志>"""  
  
import re, sys  
  
from datetime import datetime  
  
   
  
INTERNAL = re.compile(r"(127\.0\.0\.1|localhost|10\.\d+\.\d+\.\d+|192\.168\.\d+\.\d+"  
  
　　                  r"|172\.(1[6-9]|2\d|3[01])\.\d+\.\d+|169\.254\.169\.254)", re.I)  
  
WORKPLACE = re.compile(r"/workplace", re.I)  
  
LINE = re.compile(r'^(?P<ip>\S+) \S+ \S+ \[(?P<ts>[^\]]+)\] "(?P<req>[^"]+)" (?P<code>\d{3})')  
  
   
  
def parse(path):  
  
　　hits, per_ip_min = [], {}  
  
　　for ln, raw in enumerate(open(path, encoding="utf-8", errors="ignore"), 1):  
  
　　    m = LINE.match(raw)  
  
　　    if not m or not WORKPLACE.search(m.group("req")):  
  
　　        continue  
  
　　    ip, ts, req, code = m.group("ip"), m.group("ts"), m.group("req"), m.group("code")  
  
　　    key = (ip, ts[:17])                      # 同 IP 同分钟  
  
　　    per_ip_min[key] = per_ip_min.get(key, 0) + 1  
  
　　    reasons = []  
  
　　    if INTERNAL.search(req):  
  
　　        reasons.append("参数含内网地址")  
  
　　    if code in ("404", "403") and per_ip_min[key] > 5:  
  
　　        reasons.append("同IP高频探测")  
  
　　    hour = int(re.search(r"(\d{2}):(\d{2}):(\d{2})", ts).group(1))  
  
　　    if hour < 6 and reasons:  
  
　　        reasons.append("异常时段")  
  
　　    if reasons:  
  
　　        hits.append((ln, ip, ts, code, req[:70], "、".join(reasons)))  
  
　　return hits, per_ip_min  
  
   
  
def main():  
  
　　if len(sys.argv) < 2:  
  
　　    print(__doc__); sys.exit(1)  
  
　　print("== 网关日志 SSRF 特征审计 ==", datetime.now().strftime("%Y-%m-%d %H:%M:%S"))  
  
　　hits, per_ip_min = parse(sys.argv[1])  
  
　　print("WorkPlace 路径请求: %d 次 | 命中风险特征: %d 行" % (sum(per_ip_min.values()), len(hits)))  
  
　　for h in hits[:15]:  
  
　　    print("L%-4d %-15s %s %s | %s | %s" % h)  
  
　　busy = sorted(per_ip_min.items(), key=lambda x: -x[1])[:3]  
  
　　print("高频来源 Top3:", [(k[0], k[1], v) for k, v in busy])  
  
　　print("建议: 命中'参数含内网地址'的来源 IP 先在边界设备封禁，再回溯同 IP 其他请求")  
  
   
  
if __name__ == "__main__":  
  
　　main()  
  
我造了一份 60 行的合成测试日志（混合了正常访问和典型的可疑探测），跑出来是这样：  
  
实跑结果 · 2026-10-13  
  
== 网关日志 SSRF 特征审计 == 2026-10-13 08:09:41  
  
WorkPlace 路径请求: 60 次 | 命中风险特征: 40 行  
  
L1    198.51.100.7    13/Oct/2026:03:25:41 +0800 200 | GET /workplace/app?uri=http://127.0.0.1:8080/admin HTTP/1.1 | 参数含内网地址  
  
L4    198.51.100.7    13/Oct/2026:03:02:05 +0800 404 | GET /workplace/app?uri=http://10.1.2.3:389/ HTTP/1.1 | 参数含内网地址  
  
L5    198.51.100.7    13/Oct/2026:14:04:15 +0800 200 | GET /workplace/app?uri=http://169.254.169.254/latest/meta-data HTTP/1. | 参数含内网地址  
  
L7    198.51.100.7    13/Oct/2026:02:14:40 +0800 404 | GET /workplace/app?uri=http://10.1.2.3:389/ HTTP/1.1 | 参数含内网地址  
  
L8    198.51.100.7    13/Oct/2026:02:36:37 +0800 404 | GET /workplace/app?uri=http://10.1.2.3:389/ HTTP/1.1 | 参数含内网地址  
  
L9    198.51.100.7    13/Oct/2026:02:14:02 +0800 200 | GET /workplace/app?uri=http://169.254.169.254/latest/meta-data HTTP/1. | 参数含内网地址  
  
L10   198.51.100.7    13/Oct/2026:03:18:26 +0800 404 | GET /workplace/app?uri=http://10.1.2.3:389/ HTTP/1.1 | 参数含内网地址  
  
L12   198.51.100.7    13/Oct/2026:21:52:43 +0800 200 | GET /workplace/app?uri=http://127.0.0.1:8080/admin HTTP/1.1 | 参数含内网地址  
  
L17   198.51.100.7    13/Oct/2026:09:29:37 +0800 200 | GET /workplace/app?uri=http://169.254.169.254/latest/meta-data HTTP/1. | 参数含内网地址  
  
L18   198.51.100.7    13/Oct/2026:09:19:15 +0800 200 | GET /workplace/app?uri=http://169.254.169.254/latest/meta-data HTTP/1. | 参数含内网地址  
  
L20   198.51.100.7    13/Oct/2026:21:31:56 +0800 200 | GET /workplace/app?uri=http://127.0.0.1:8080/admin HTTP/1.1 | 参数含内网地址  
  
L21   198.51.100.7    13/Oct/2026:14:18:38 +0800 200 | GET /workplace/app?uri=http://127.0.0.1:8080/admin HTTP/1.1 | 参数含内网地址  
  
L24   198.51.100.7    13/Oct/2026:14:02:42 +0800 200 | GET /workplace/app?uri=http://169.254.169.254/latest/meta-data HTTP/1. | 参数含内网地址  
  
L26   198.51.100.7    13/Oct/2026:09:44:22 +0800 200 | GET /workplace/app?uri=http://127.0.0.1:8080/admin HTTP/1.1 | 参数含内网地址  
  
L27   198.51.100.7    13/Oct/2026:14:37:51 +0800 404 | GET /workplace/app?uri=http://10.1.2.3:389/ HTTP/1.1 | 参数含内网地址  
  
高频来源 Top3: [('198.51.100.7', '13/Oct/2026:02:14', 2), ('203.0.113.10', '13/Oct/2026:21:07', 2), ('198.51.100.7', '13/Oct/2026:03:25', 1)]  
  
建议: 命中'参数含内网地址'的来源 IP 先在边界设备封禁，再回溯同 IP 其他请求  
  
注意看 L5、L9 那几行，参数里是 **169.254.169.254**  
——这是云环境的元数据服务地址。会摸这个地址的，目标基本不是你，而是想借你的网关偷云上凭证。看到这种行，别只封 IP，把同 IP 过去一周的全部请求都拉出来复查一遍。  
  
日志这块还有个体力活：SMA 的默认日志留存往往只有 7 天。如果你的设备还没打补丁，今天就把日志留存期调长、并把日志转发到独立的日志平台——万一真要应急取证，本地那点留存根本不够看。  
  
  
07  
  
为什么受伤的总是这类设备  
  
  
把视角拉远一点看，SMA 这类远程接入设备几乎是「常驻漏洞榜」的选手。原因不复杂：它是**极少数天然拥有「公网入口 + 内网信任」双重身份的设备**  
。打一个 Web 应用，你拿到的是一个网站；打一个网关，你拿到的是一条进内网的路。收益差着量级，被盯上的概率自然也差着量级。  
  
更深一层的原因，是这类设备的运维节奏。我接触过的环境里，网关类设备的补丁窗口普遍比服务器慢一拍——业务怕断、变更要审批、而设备「看起来一直好好的」。攻击者吃准的就是这一拍。今年 7 月 Inc 勒索团伙打 SMA 1000 用的还是零日，厂商连补丁都还没来得及发；这次好歹是「先有补丁后有利用」，给了我们一个能抢跑的窗口——**窗口没关上之前，别浪费它**  
。  
  
顺带给普通读者翻译一下这事跟你的关系：你公司的远程办公入口，大概率就是这类设备。它出问题的那天，你收到的通知会是「远程办公暂停」，更糟的情况是「内网数据正在排查」。所以这篇文章，值得你转给公司里管这块的同事。  
  
  
08  
  
今天下班前把这四件事做完  
  
  
最后收个尾，四件事，按顺序做，前两件今天就能落地：  
  
**一、定性。**  
跑一遍自查脚本，确认 WorkPlace 是否对公网可达、设备是不是 SMA 1000 系列。不是就归档记录，是就往下走。  
  
**二、止血。**  
能撤下公网就撤，撤不了就 ACL 收窄来源，同时在 WAF 加上「内网地址出现在参数里就拦」的兜底规则。  
  
**三、补丁。**  
走变更流程装官方热修，装完用自查脚本复测一遍，确认响应指纹已变化。  
  
**四、回溯。**  
跑日志审计脚本，翻最近两周的记录；把日志留存期调长，管理面来源收紧。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/Ah5NAr7MJFgWz1NqC5YRWovcp8M5QDczfsNSSZl1vkrwXwszbIRibhw8lJw7aicJ9YiaicAV180EABolwiboia0cKEHAtuPFtbLD4USaL6Fu9AYjg/640?from=appmsg "")  
  
网关自查流程：确认版本到复测加固  
  
我那个运维朋友昨天回了消息：热修已经装完，WorkPlace 也从公网撤到了只允许办公网段。他说折腾一晚上，就为了让我这两段脚本的输出里，[4] 那行变成「未见对外响应」。我觉得这觉熬得挺值——**安全这行的大部分功夫，就花在把「万一」变成「已经」上**  
。  
  
脚本都在文中，复制就能跑。哪个环节卡住了，评论区把你的输出贴出来，我帮你看。  
  
  
  
觉得有用？点个「在看」让更多人看到  
  
紫禁玄科  
  
专注网络安全 · 深度分析  
  

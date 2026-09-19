#  JumpServer越权漏洞：URL里加个参数，管理员Access Key直接裸奔  
原创 承影
                    承影  兰花豆说网络安全   2026-09-19 00:38  
  
国产开源堡垒机JumpServer曝出高危越权漏洞（QVD-2026-65008），任意普通登录用户在API请求附加_rel=not参数，即可读取所有用户的Access Key明文，进而以管理员身份接管堡垒机。  
  
  
晚上十点，运维小张正准备下班，钉钉群里弹出一条安全通告：JumpServer Access Key越权泄露漏洞，高危，尽快升级。  
  
  
他心里咯噔一下。公司几百台服务器的运维入口全走JumpServer，开发人员、外包、实习生，有账号的少说几十号人。要是这些人里随便哪个动点歪心思，在请求里加一个参数，就能把管理员的Access Key明文读出来——那等于整个机房的大门钥匙被人复印了一把。  
  
  
小张默默打开电脑，开始查版本。  
  
## 一、漏洞原理：一个查询参数撬开的大门  
  
这次漏洞的根源，是JumpServer的SQL查询过滤存在缺陷。  
  
  
正常逻辑下，普通用户调用用户列表接口，只能看到自己权限范围内的信息。但攻击者发现，只要在API请求里附加一个 _rel=not 的查询参数，就能绕过权限过滤，越权读取所有用户的Access Key Secret和TempToken明文。  
  
  
更致命的是，如果环境开启了AUTH_TEMP_TOKEN，攻击者拿着偷来的临时令牌，可以直接以管理员身份登录堡垒机。到这一步，事情就从"信息泄露"升级成"全面沦陷"——堡垒机纳管的所有服务器、所有运维会话、所有审计记录，全部对攻击者敞开。  
  
  
堡垒机是干什么的？是企业运维体系的总闸门。所有对生产服务器的访问都从这过，权限分级、会话录像、操作审计全指着它。总闸门自己漏了，后面的门再结实也没用。这就像小区的保安室钥匙被配了把万能钥匙，每家每户的门锁都没动，但整个小区已经不安全了。  
  
## 二、影响范围和修复版本  
  
奇安信给这个漏洞的评级是高危，漏洞编号QVD-2026-65008，GitHub安全公告编号GHSA-6rp5-ff2m-qfrm。利用门槛极低——不需要什么高深技术，一个普通的登录账号加一个查询参数就够了。漏洞由Smart Oasis的GitHub用户@sajjadhaqi在8月提交，JumpServer官方9月9日发布修复公告。  
  
  
受影响的版本范围：  
  
V3版本：大于等于v3.7.0，小于v3.10.23 LTS；  
  
V4版本：大于等于v4.0.0，小于v4.10.19 LTS。  
  
  
安全版本是v3.10.23 LTS和v4.10.19 LTS及以上。社区版和企业版都在影响范围内。JumpServer在国内的装机量不用多说，互联网、金融、政企、制造业，用它的单位一抓一大把，这个影响面相当可观。  
  
## 三、修复方案：升级优先，临时有招  
  
正路当然是升级。把JumpServer升到v3.10.23 LTS或v4.10.19 LTS以上，一了百了。  
  
  
如果一时半会儿升不了（比如生产环境有变更窗口限制），官方给了临时缓解方案：在Nginx层禁用这个过滤参数。  
  
  
HTTPS启用的环境，  
  
改/opt/jumpserver/config/nginx/lb_http_server.conf；  
  
  
没启用HTTPS的，改jms_web容器里的  
  
/etc/nginx/conf.d/http_server.conf，在location块里加一段规则，匹配到 _rel= 参数（包括URL编码形式）直接返回400。  
  
改完提交容器变更、重启jms_web容器生效。  
  
  
修完怎么验证？官方给了三条curl命令：带 _rel=not 参数的请求期望返回400，带URL编码形式 %5Frel=not 的期望返回400，不带参数的正常请求期望返回401或403。三条都符合预期，说明缓解生效。  
  
  
注意，临时方案只是堵住了这一个利用路径，治标不治本，窗口期过后还是要排期升级。  
  
## 四、升完级别急着收工，还有几件事  
  
第一，轮换凭证。  
既然漏洞存在期间Access Key可能已经泄露，光升级不算完，管理员和关键用户的Access Key、TempToken全部重新生成一遍，历史泄露的密钥作废，这步省了等于白修。  
  
  
第二，翻日志。  
往前查API访问日志，重点看带 _rel=not 或 %5Frel=not 参数的请求记录，看看漏洞公开之前有没有可疑调用。有普通账号频繁调这种接口的，顺着账号往下查。  
  
  
第三，盘一盘账号权限。  
这次漏洞的利用前提是"任意已登录用户"，你系统里那些离职没销的、外包项目结束没回收的、实习生共用账号，都是潜在入口。借着这次机会做一次账号清理，最小权限该收紧的收紧。  
  
  
第四，想想纵深。  
堡垒机这种单点价值极高的系统，除了等厂商补丁，网络层的访问控制、异常API调用的监测告警，平时就得配上。这次是一个参数的问题，下次可能是另一个参数。  
  
  
这几年国产开源组件的漏洞响应速度肉眼可见地在提升，从8月收报告到9月发修复公告、给出临时方案和验证命令，JumpServer这次的处理算得上周到。但组件靠谱不等于用的人可以躺平——开源软件用的人越多，漏洞公开后被批量扫描利用的速度就越快。  
  
  
通告发出来那一刻起，攻防的倒计时就开始了。你的堡垒机，今晚查了吗？  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/buQYO9JqQcqHibMSbuUlH1q4N0BqiaJT4A81RZWbGMibEU57wlC7W6ryRDOF3vnn8UEfjlTdxeyEovsOzIibw7B11Xp7353IVMKgugHfLB4YWic4/640?from=appmsg "")  
  
END  
  
推荐阅读  
  
[网信办最新通报的10个案例，每一个都像在说你公司](https://mp.weixin.qq.com/s?__biz=MzI3NzM5NDA0NA==&mid=2247493912&idx=1&sn=d69ea89513c346d56af1ac05c7454e68&scene=21#wechat_redirect)  
  
  
2026-09-18  
[](https://mp.weixin.qq.com/s?__biz=MzI3NzM5NDA0NA==&mid=2247493912&idx=1&sn=d69ea89513c346d56af1ac05c7454e68&scene=21#wechat_redirect)  
  
  
[湖南7家大模型运营单位出现安全围栏漏洞](https://mp.weixin.qq.com/s?__biz=MzI3NzM5NDA0NA==&mid=2247493907&idx=1&sn=da9d419af5e83b4ee65e4e05f8264bcd&scene=21#wechat_redirect)  
  
  
2026-09-17  
[](https://mp.weixin.qq.com/s?__biz=MzI3NzM5NDA0NA==&mid=2247493907&idx=1&sn=da9d419af5e83b4ee65e4e05f8264bcd&scene=21#wechat_redirect)  
  
  
[网络安全的春天，真的来了](https://mp.weixin.qq.com/s?__biz=MzI3NzM5NDA0NA==&mid=2247493902&idx=1&sn=5b5bc0912422d61454d0cb50b8be93d0&scene=21#wechat_redirect)  
  
  
2026-09-13  
[](https://mp.weixin.qq.com/s?__biz=MzI3NzM5NDA0NA==&mid=2247493902&idx=1&sn=5b5bc0912422d61454d0cb50b8be93d0&scene=21#wechat_redirect)  
  
  
[微信史诗级零点击漏洞：一通语音电话，账号就被隔空接管](https://mp.weixin.qq.com/s?__biz=MzI3NzM5NDA0NA==&mid=2247493895&idx=1&sn=4669be4aaa362b64af3764102226d2cb&scene=21#wechat_redirect)  
  
  
2026-09-11  
[](https://mp.weixin.qq.com/s?__biz=MzI3NzM5NDA0NA==&mid=2247493895&idx=1&sn=4669be4aaa362b64af3764102226d2cb&scene=21#wechat_redirect)  
  
  
[参编邀请 | 全国首部“AI生成内容合规”标准，欢迎加入编制组！](https://mp.weixin.qq.com/s?__biz=MzI3NzM5NDA0NA==&mid=2247493890&idx=1&sn=010dae1fdb791cf9eb2db0679ab498a7&scene=21#wechat_redirect)  
  
  
2026-09-10  
[](https://mp.weixin.qq.com/s?__biz=MzI3NzM5NDA0NA==&mid=2247493890&idx=1&sn=010dae1fdb791cf9eb2db0679ab498a7&scene=21#wechat_redirect)  
  
  
  

#  漏洞预警： 普华PowerPMS两漏洞组合 无需账号拿下服务器  
宝十八
                    宝十八  网络安全老宋   2026-08-09 04:00  
  
**导语：**  
 你好，我是网络安全老宋。  
安全攻防干货准时送达！  
  
<table><tbody><tr><td style="vertical-align: middle;font-size: 20px;color: rgb(26, 23, 20);"><section><span leaf="">网络安全</span><span style="color: rgb(200, 64, 26);"><span leaf="">老宋</span></span></section></td><td style="text-align: right;vertical-align: middle;"><span style="font-family: monospace;font-size: 12px;color: rgb(138, 132, 128);border: 1px solid rgb(232, 227, 216);padding: 3px 10px;border-radius: 20px;"><span leaf="">漏洞预警 · K0019</span></span></td></tr></tbody></table>  
  
// 网防技-202608-K0019 · 8月6日发布  
# 甲方运维注意：普华PowerPMS两漏洞组合无需账号拿下服务器  
  
一个能绕过登录，一个能写WebShell——两个老漏洞叠一起，等于把钥匙和撬棍一起递给黑客。  
  
鉴权绕过  
文件上传  
服务器失陷  
  
🔑 一句话精华  
鉴权绕过加文件上传，等于把服务器钥匙和写入权限一起交给黑客。  
  
8月6日，发布安全风险预警，而且是拉着中国海洋石油集团一起发的。普华PowerPMS管理系统被扒出两处高危漏洞：一个能绕过登录直接调接口，一个能往服务器写恶意的  
 .aspx  
   
文件。  
  
单看其中一个都不算新鲜，可两个叠在一起，攻击者从头到尾不用一个账号密码，就能摸进服务器、种下后门、把项目库翻个底朝天。  
  
你的单位跑PowerPMS吗？  
  
PowerPMS不是小众玩具。普华科技自己的客户清单里，中国石油、中国海油、中国石化、国家管网、中国建筑、中国电建这些能源工程央企全在列，浦东机场、各大地铁、中广核也都在用。它管的是项目计划、执行监控、业务反馈——一家工程企业的核心商业秘密，几乎都在这套系统里。换句话说，中招的不只是技术事故，很可能是商业机密外泄。  
  
## 01两处漏洞组合，才是要命的地方  
  
  
鉴权绕过那一个，出在  
 /Plan/BatchHandleFeedBackRecord  
   
接口。正常情况你得先登录，系统才让你查项目计划。这个接口偏偏不做校验，攻击者发个请求就能直接把项目数据读出来、改掉。文件上传那一个，出在  
 /PowerPlat/.../snapshot.aspx  
，对上传文件的类型、内容、保存路径几乎不检查，还放行得很松。  
  
一条完整攻击链 · 全程无需账号密码  
<table><tbody><tr style="border-bottom-width: 1px;border-bottom-style: dashed;border-bottom-color: rgb(216, 208, 196);"><td style="padding: 10px 0px;"><span style="display: inline-block;width: 30px;height: 30px;border-radius: 8px;background-color: rgb(200, 64, 26);color: rgb(255, 255, 255);font-family: monospace;font-size: 13px;font-weight: 700;text-align: center;line-height: 30px;margin-right: 10px;vertical-align: middle;"><span leaf="">01</span></span><b style="color: rgb(26, 23, 20);"><span leaf="">鉴权绕过</span></b><section><span leaf="">     直接调接口，绕过登录拿访问权</span></section></td></tr><tr style="border-bottom-width: 1px;border-bottom-style: dashed;border-bottom-color: rgb(216, 208, 196);"><td style="padding: 10px 0px;"><span style="display: inline-block;width: 30px;height: 30px;border-radius: 8px;background-color: rgb(200, 112, 80);color: rgb(255, 255, 255);font-family: monospace;font-size: 13px;font-weight: 700;text-align: center;line-height: 30px;margin-right: 10px;vertical-align: middle;"><span leaf="">02</span></span><b style="color: rgb(26, 23, 20);"><span leaf="">文件上传</span></b><section><span leaf="">     借快照接口把恶意脚本写进服务器</span></section></td></tr><tr style="border-bottom-width: 1px;border-bottom-style: dashed;border-bottom-color: rgb(216, 208, 196);"><td style="padding: 10px 0px;"><span style="display: inline-block;width: 30px;height: 30px;border-radius: 8px;background-color: rgb(200, 160, 96);color: rgb(255, 255, 255);font-family: monospace;font-size: 13px;font-weight: 700;text-align: center;line-height: 30px;margin-right: 10px;vertical-align: middle;"><span leaf="">03</span></span><b style="color: rgb(26, 23, 20);"><span leaf="">WebShell落地</span></b><section><span leaf="">     写入可执行的.aspx目录</span></section></td></tr><tr><td style="padding: 10px 0px;"><span style="display: inline-block;width: 30px;height: 30px;border-radius: 8px;background-color: rgb(30, 28, 24);color: rgb(255, 255, 255);font-family: monospace;font-size: 13px;font-weight: 700;text-align: center;line-height: 30px;margin-right: 10px;vertical-align: middle;"><span leaf="">04</span></span><b style="color: rgb(200, 64, 26);"><span leaf="">控制服务器</span></b><section><span leaf="">     翻项目库、种后门、横向移动</span></section></td></tr></tbody></table>  
  
这套打法一点都不新，同行早就在别家的系统上跑通过。致远OA那个经典的漏洞链，就是先用未授权接口骗到一个有效的JSESSIONID当登录凭证，再借文件上传把压缩包解压到Web目录落地WebShell，几分钟拿下管理员权限，红队在对抗演练里就是这么干的。PowerPMS这次的两条，本质是一模一样的组合拳——你只盯着"文件上传"一个点去修没用，攻击者是从你根本没设防的鉴权入口进来的。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/yJLbez93flicCicKYxJ4WDS4gSRDIDEI6MEuhq0njjrgXtHWOYx1ZhhaemN1PDMoHBNwQDCOLyBtnvJxbNwB8XNKIiaMhyA4sGSsia5Thtbjm6g/640?wx_fmt=png&from=appmsg "")  
  
## 02更能命的是能偷走什么  
  
  
项目计划、业务反馈这些字段，落到能源工程央企手里，就是投资规模、供应商名录、投标报价、施工节点这类核心数据。一旦被拖库，竞争对手拿着报价去投标，项目方连反应时间都没有。而且这事已经不只关系"系统安不安全"——只要发生敏感数据泄露，企业就撞上了《数据安全法》第27条、第29条：在等保基础上履行数据安全义务、发现漏洞立即补救并报告。  
  
⚠️ 合规红线  
已有企业因"系统存在高危漏洞且整改不力"被网信办按《数据安全法》第45条处罚：轻则警告加  
 5万元  
，造成严重数据泄露的可达  
 50万–200万元  
。中招之后不是补个洞就完事，可能还要面对监管。  
  
## 03先确认中没中  
  
<table><tbody><tr><td data-colwidth="76" style="vertical-align: top;padding: 16px 14px 16px 0px;"><span style="display: inline-block;width: 40px;height: 40px;border-radius: 10px;background-color: rgb(137, 109, 67);color: rgb(255, 255, 255);font-family: monospace;font-size: 13px;font-weight: 600;text-align: center;line-height: 40px;"><span leaf="">R1</span></span></td><td style="vertical-align: top;padding: 16px 0px;border-bottom-width: 1px;border-bottom-style: dashed;border-bottom-color: rgb(219, 210, 196);"><b style="display: block;font-family: &#34;Songti SC&#34;, serif;font-size: 17px;color: rgb(26, 23, 20);margin-bottom: 4px;"><span leaf="">查是否部署</span></b><span style="font-size: 14.5px;color: rgb(94, 93, 93);line-height: 1.7;"><span leaf="">这次影响&#34;通用版本&#34;，覆盖面比你想的大，别凭印象说&#34;我们没用&#34;。</span></span></td></tr><tr><td data-colwidth="76" style="vertical-align: top;padding: 16px 14px 16px 0px;"><span style="display: inline-block;width: 40px;height: 40px;border-radius: 10px;background-color: rgb(137, 109, 67);color: rgb(255, 255, 255);font-family: monospace;font-size: 13px;font-weight: 600;text-align: center;line-height: 40px;"><span leaf="">R2</span></span></td><td style="vertical-align: top;padding: 16px 0px;border-bottom-width: 1px;border-bottom-style: dashed;border-bottom-color: rgb(219, 210, 196);"><b style="display: block;font-family: &#34;Songti SC&#34;, serif;font-size: 17px;color: rgb(26, 23, 20);margin-bottom: 4px;"><span leaf="">翻日志找绕过</span></b><span style="font-size: 14.5px;color: rgb(94, 93, 93);line-height: 1.7;"><span leaf="">在IIS/WAF/反代日志里找</span><span leaf=""> </span><code style="font-family: monospace;font-size: 0.85em;background-color: rgb(242, 239, 232);padding: 1px 5px;border-radius: 4px;color: rgb(200, 64, 26);"><span leaf="">GET /Plan/BatchHandleFeedBackRecord</span></code><span leaf="">，挑返回200但无Cookie/Authorization的，按源IP与时间聚合，重点看短时间查一堆项目、非业务时段从公网过来的。</span></span></td></tr><tr><td data-colwidth="76" style="vertical-align: top;padding: 16px 14px 16px 0px;"><span style="display: inline-block;width: 40px;height: 40px;border-radius: 10px;background-color: rgb(137, 109, 67);color: rgb(255, 255, 255);font-family: monospace;font-size: 13px;font-weight: 600;text-align: center;line-height: 40px;"><span leaf="">R3</span></span></td><td style="vertical-align: top;padding: 16px 0px;"><b style="display: block;font-family: &#34;Songti SC&#34;, serif;font-size: 17px;color: rgb(26, 23, 20);margin-bottom: 4px;"><span leaf="">查上传接口</span></b><span style="font-size: 14.5px;color: rgb(94, 93, 93);line-height: 1.7;"><span leaf="">检索</span><span leaf=""> </span><code style="font-family: monospace;font-size: 0.85em;background-color: rgb(242, 239, 232);padding: 1px 5px;border-radius: 4px;color: rgb(200, 64, 26);"><span leaf="">POST .../snapshot.aspx</span></code><span leaf="">，看参数有没有 cmd、powershell、盘符路径、目录跳转，或 .aspx/.ashx 等可执行扩展名，再对服务器新文件创建时间。</span></span></td></tr></tbody></table>  
## 04没补丁前先止血  
  
  
在WAF上给这两个路径加规则，把没带合法会话的访问和可疑上传请求拦在门外，这是最快的临时补丁。上传目录把脚本执行权限关掉，就算有人传上来也跑不起来。临时让那个打印快照接口对非业务时段、非内网来源返回403，等业务不依赖时干脆注释掉路由。等官方补丁一发布，立刻升级，再把鉴权和上传校验补上。  
  
   
   
   
⏺ Nginx · 上传目录禁执行  
```
```  
  
  
// 老宋说  
技术本质：  
这不是两个漏洞，是一类设计缺陷的两次出现——把"能访问"和"能写入"两道门同时虚掩，攻击者自然长驱直入。  
行业观察：  
这类业务系统天天在跑，却长期不在等保和漏洞扫描的重点清单上，等出事才想起补，代价往往是商业机密加罚款一起挨。  
对读者的建议：  
今天就去翻日志确认那两个接口有没有被扫过，有WAF的先把虚拟补丁上了，别等监管先找上门。  
  
  
防御，不是在演练期间发现攻击，而是在演练开始前就把攻击面收敛到最小。  
  
end  
  
  
  
不想错过文章内容？读完请点一下**“在看**  
**”**  
，加个**“****关注”**  
，您的支持  
是我创作的动力  
  
期待您的一键三连支持（点赞、在看、分享~）  
  

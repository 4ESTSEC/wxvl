#  【漏洞威胁】DeepSeek Harness（DSH）未授权远程代码执行漏洞  
 信息安全大事件   2026-08-25 03:58  
  
奇安信威胁情报中心披露，DeepSeek Harness（DSH）未授权远程代码执行漏洞。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/08AVoZVARY3npsk3O4AcqEvWZa5Pzu1J2Bdibh1AfUgXxY6icgaZDeoVgmLJJq7BrraLotCHg7UmXDNiczABp1UM0Oeia8rV7w3sdzDGEZAQz6g/640?wx_fmt=png&from=appmsg "")  
  
漏洞编号QVD202657410，漏洞评级为极危，CVSS 3.0评分高达9.8，目前该漏洞POC已经公开。  
  
受影响版本为DeepSeekHarness 0.1.1rc.2。漏洞根源为平台对HTTP Host请求头校验存在缺陷。  
  
攻击者可伪造Host请求头绕过/api信任围栏，调用内部受限RPC方法，注册虚假的大模型提供者，驱动Agent工具执行任意系统命令。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/08AVoZVARY1ubuiaTCCFuxY5zclSiaIOnKv7VHwiaUQgKFBeAZyuCfYb6v5CQ2lXEEK4oiblCMOLx5icEfv7IVzfW4Gq8dAvam3ynHdoKSlqJrUk/640?wx_fmt=png&from=appmsg "")  
  
该攻击链路不需要有效的API Key，攻击成功后，攻击者可在目标服务器上以dsh服务进程权限执行命令，窃取敏感数据、植入后门、进行横向移动。  
  
漏洞利用需要满足相关条件：目标将DeepSeek Harness管理API暴露于公网，系统未对Host请求头做严格校验，同时攻击者拥有可被目标实例访问的外部服务。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/08AVoZVARY3rhG5l5hbQ0ha0oBAKCicJe3Gib9QWwjGJ6v4aOq3OLza9OftwVLfZP514iaNsGvXBIFV0M0vDmPdtjzwIaxDjcQC7aQdHeoUFbg/640?wx_fmt=png&from=appmsg "")  
  
截至目前，暂未观测到该漏洞的在野利用痕迹，也没有证据表明相关攻击和已知威胁团伙存在关联。  
  
受影响对象为所有把DeepSeek Harness管理API暴露公网、且未配置Host头访问控制的部署实例。  
  
安全机构给出多项处置建议，首要措施是将管理API端口从公网隔离，仅允许可信内网IP访问；同时可在反向代理层配置严格Host头校验规则。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/08AVoZVARY2ru581QZzib6ianDWkr8FIiaDZxibOTOxV9WrbbxxwY1GDh4K3Mqcf40XUQz1F465IV40KuZKvY5KYN7BWxPTGZKgUo0k57fodfJM/640?wx_fmt=png&from=appmsg "")  
  
此外建议运维人员持续跟进DeepSeek官方安全公告，及时升级至修复版本。  
  
同时技术文档提示，/api信任围栏需要增设独立于Host请求头的认证机制，对llm.discoverModels接口做好访问范围限制，阻断潜在SSRF风险。  
  
  
给所有正在上大模型  
 / Agent 平台的单位三点启示  
  
这次事件，与其说是一个产品的  
 Bug，不如说是给所有正在把AI 能力"接口化、服务化"的单位敲了一记警钟：  
- 暴露面就是攻击面。管理后台、运维  
 API 一旦"裸奔"到公网，就等于在大门上贴了张"欢迎光临"。  
  
- 信任不能只绑在  
 HTTP 头上。任何把访问信任寄托于 Host 头、IP 等易伪造因素上的设计，都需要补一道独立认证。  
  
- AI 平台成了新靶心。Agent 能调用工具、能执行命令，一旦被接管，杀伤力远超传统 Web 应用——这正是API 安全、漏洞管理、零信任变得前所未有的重要。  
  
国骏信息安全深耕政企网络安全服务多年，围绕上面几个问题，可以提  
供以下服务  
<table><tbody><tr><td data-colwidth="209" style="border-color:#000000;"><section><span leaf="">你担心的风险</span></section></td><td data-colwidth="287" style="border-color:#000000;"><section><span leaf="">我们能提供的对应服务</span></section></td></tr><tr><td data-colwidth="209" width="260" valign="center" style="border-color:#000000;"><p><span leaf=""><span textstyle="" style="font-size: 15px;">&#34;</span></span><font face="宋体"><span leaf=""><span textstyle="" style="font-size: 15px;">我到底有多少端口 </span></span></font><font face="Calibri"><span leaf=""><span textstyle="" style="font-size: 15px;">/ API </span></span></font><font face="宋体"><span leaf=""><span textstyle="" style="font-size: 15px;">暴露在外？</span></span></font><font face="Calibri"><span leaf=""><span textstyle="" style="font-size: 15px;">&#34;</span></span></font></p></td><td data-colwidth="287" style="border-color:#000000;"><section><span leaf=""><span textstyle="" style="font-size: 15px;">资产暴露面梳理服务（先把家底摸清楚）</span></span></section></td></tr><tr><td data-colwidth="209" style="border-color:#000000;"><section><span leaf=""><span textstyle="" style="font-size: 15px;">&#34;管理面怎么隔离、Host 头怎么校验？&#34;</span></span></section></td><td data-colwidth="287" width="293" valign="center" style="border-color:#000000;"><p><font face="宋体"><span leaf=""><span textstyle="" style="font-size: 15px;">安全加固服务</span></span></font><span leaf=""><span textstyle="" style="font-size: 15px;"> + </span></span><font face="宋体"><span leaf=""><span textstyle="" style="font-size: 15px;">零信任 </span></span></font><font face="Calibri"><span leaf=""><span textstyle="" style="font-size: 15px;">/ </span></span></font><font face="宋体"><span leaf=""><span textstyle="" style="font-size: 15px;">访问控制方案</span></span></font></p></td></tr><tr><td data-colwidth="209" width="260" valign="center" style="border-color:#000000;"><p><span leaf=""><span textstyle="" style="font-size: 15px;">&#34;</span></span><font face="宋体"><span leaf=""><span textstyle="" style="font-size: 15px;">升级跟不跟得上、接口漏不漏？</span></span></font><font face="Calibri"><span leaf=""><span textstyle="" style="font-size: 15px;">&#34;</span></span></font></p></td><td data-colwidth="287" style="border-color:#000000;"><section><span leaf=""><span textstyle="" style="font-size: 15px;">漏洞检测与 API 安全、漏扫与加固</span></span></section></td></tr><tr><td data-colwidth="209" width="260" valign="center" style="border-color:#000000;"><p><span leaf=""><span textstyle="" style="font-size: 15px;">&#34;</span></span><font face="宋体"><span leaf=""><span textstyle="" style="font-size: 15px;">万一真被打穿了怎么办？</span></span></font><font face="Calibri"><span leaf=""><span textstyle="" style="font-size: 15px;">&#34;</span></span></font></p></td><td data-colwidth="287" style="border-color:#000000;"><section><span leaf=""><span textstyle="" style="font-size: 15px;">应急响应 + 攻防演练 + 网络安全托管 MSS</span></span></section></td></tr></tbody></table>  
建议每一个把管理  
 API、AI 平台部署在公网边缘的单位，先做一次客观的风险自查。  
  
关注公众号并回复  
"暴露面体检"，我们会安排安全顾问与你对接。 早一步排查，少一分被动。  
  
  
  
如有需要，欢迎联系江苏国骏安全专家  
  
联系电话：400-6776-989/13338963885  
  
  
  
  
欢迎关注，了解更多内容  
  
  
  

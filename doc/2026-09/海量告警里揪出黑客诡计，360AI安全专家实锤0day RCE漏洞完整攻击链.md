#  海量告警里揪出黑客诡计，360AI安全专家实锤0day RCE漏洞完整攻击链  
 360数字安全   2026-09-30 09:30  
  
****  
  
  
日常的攻防日志研判，就是从海量日志中寻找异常。  
  
  
每天的攻击探测、注入尝试、异常访问从防护系统奔涌而来，绝大多数是扫描器制造的噪声与重复告警。  
  
  
但近期，360 AI安全专家注意到一组请求，不像正常业务，更像有人在试探性“敲门”。  
  
  
循着这组请求，360 AI安全专家拆开层层编码的载荷，还原出完整攻击意图，**最终确认某Web应用存在“高危0day RCE漏洞”。攻击者一旦利用，就能像拿到“遥控器”一样操控服务器。**  
  
  
作为尚未公开、暂无官方补丁的0day漏洞，该风险杀伤力极强：黑客可直接执行任意系统指令，接管业务服务器，窃取核心数据，并以此为跳板对内网开展横向渗透。  
  
  
由于没有公开修复方案，如果未能及时发现处置，一旦漏洞被扩散利用，将对大量同类业务系统造成大面积的安全威胁。  
  
  
从一声“敲门”到漏洞复现，这中间究竟发生了什么？  
  
  
  
**噪声里的“敲门声”**  
  
  
  
  
这组请求起初并不显眼，混迹于扫描器制造的噪声中，与寻常告警几无二致。若靠人工逐条翻找，几乎不可能在第一时间察觉。  
  
  
**但360 AI安全专家没有停留在关键词匹配层面，而是借助AI的语义理解能力，结合上下文判断攻击意图。**  
同一条请求，出现在扫描器批量试探中，与出现在定向攻击探测阶段，含义截然不同。  
  
  
正是这种语义级研判，让异常浮出水面：其参数并非正常业务数据，而是一段经过多级编码的探测载荷，特征极为隐蔽。  
  
  
**它由此从噪声中被精准剥离，升级为高置信可疑事件。研判的重心，也从人力堆叠的逐条翻找，转向“AI全量初筛、专家重点复核”的分工协作。**  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/zfoRGB81MxNJ2ZLzyNZVOhjzFic0h1Hh4mXRHxz5D4YumTlE1E06icO5NGibTiaCokyjlT4bccgtO2L6HN9F2fFzpnH8GwibuEdUpSQcnWaux4AI/640?wx_fmt=png&from=appmsg "")  
  
本次AI智能处置漏洞事件的成果总览  
  
  
问题随之而来：这只是一次普通扫描，还是有人已经盯上了这里？  
  
  
**从攻击链到漏洞本体**  
  
  
  
  
**锁定可疑请求后，AI随即展开顺藤摸瓜式的关联分析：把同一攻击源在不同时间、不同接口上的动作串成完整时间线，将碎片化的散点告警重新组织为一条完整的攻击链路。**  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/zfoRGB81MxPP7jpcL4ZCDWkrvx3jLktwW7ickaOhn62tvszOekrIuIzvfudVH8wUwUyHEia9EWVk0EYw9riaOOQr7IDibotVyUFlQ4DAw8NhouA/640?wx_fmt=png&from=appmsg "")  
  
AI通过“理解”日志中的攻击，进行漏洞的智能分析  
  
  
分析显示，攻击者先以低频试探摸清目标端口与组件版本，确认攻击面后再进入漏洞利用阶段。整条链路环环相扣、节奏分明。这显然是一起有预谋的针对性攻击，而非一次路过式的扫描。  
  
  
此时，一个更关键的问题摆在面前：攻击者到底是如何完成入侵的？  
  
  
顺着攻击链往回追，答案藏在载荷里。AI逐层解码，还原出多级编码掩盖下的真实指令；再拆开利用链的每一步构造，对照目标系统的组件版本与接口特征逐一求证。  
  
  
线索最终指向同一个结论：某接口未对外部传入参数做充分过滤，攻击者可以借此将系统命令拼接注入，并在服务器上执行。  
  
  
**这就是最高危的0day远程命令执行（RCE）漏洞。一个本该只响应业务请求的接口，就这样被攻击者改造成了可以远程发号施令的入口。**  
  
  
过去，从攻击行为反推漏洞成因，需要人类专家耗费大量时间排查；而这一次，AI沿着攻击链自动完成了绝大部分工作。  
  
  
**一锤定音之后**  
  
  
  
  
结论要经得起检验，就必须复现。  
  
  
验证在隔离沙箱中展开。**多个智能体协同推进：有的梳理攻击面，有的推演利用路径，有的搭建环境、构造请求。然而首次触发，服务器没有给出预期回应。**  
  
  
AI没有停在失败上。它回头检查参数，修正利用路径，再次发起验证。这一次，服务器进程按预期执行了注入的命令，靶机桌面上弹出了计算器。进程树完整记录下这条链路：Web服务进程处理恶意请求后，依次派生出命令解释进程与计算器进程。漏洞真实性，就此一锤定音。  
  
  
复现成功后，AI自动整理出标准化漏洞报告，涵盖描述、成因、复现步骤、危害评级与修复建议，提交审核，最终提报与处置仍由安全专家把关。  
攻击特征随后沉淀为新的监测拦截规则，反哺防护系统，让一次发现转化为持续的防护能力。  
  
  
这并非一次孤立的灵光乍现。  
  
  
从日志中一声不起眼的“敲门”，到一份带着完整证据链的漏洞报告，全程由AI深度参与乃至自主完成。这背后，是一套“监测—发现—研判—复现—处置—复盘”的全生命周期体系在持续运转。  
  
  
多智能体各司其职又彼此交叉验证，既保证了专业深度，也控制了误报与误判；所有验证都在严格隔离的沙箱中进行，全程留痕、可审计、可回放；每一次任务的中间产物与最终结论，又自动归档为结构化知识，反哺模型持续进化，形成“越用越强”的正向循环。  
  
  
**更重要的是，这套体系并非孤立运行，而是与360现有的漏洞管理与应急响应流程深度融合。AI把发现与研判的结论直接对接处置环节，成为人类安全专家的可靠助手与能力放大器。从早期辅助分析，到如今端到端完成漏洞的发现、验证与提报，AI在安全运营中的角色，正从“辅助”走向“自主”。**  
  
  
攻击在加速，漏洞从暴露到被利用的窗口越来越短。面对这样的对手，安全运营需要更快的发现、更准的研判、更完整的验证。360 AI安全专家的实践，正是安全运营走向智能化的一个缩影。  
  
  
这，或许就是安全运营的下一个常态。  
  
往期推荐  
  
<table><tbody><tr><td data-colwidth="100.0000%" width="100.0000%" style="border-width: 0px;border-color: rgb(62, 62, 62) rgb(62, 62, 62) rgb(255, 255, 255);border-style: none;padding: 0px 0px 10px;"><section style="min-height: 40px;margin: 0px 0%;"><section style="width: 100%;margin: 0px auto -10px;"><table><tbody><tr><td rowspan="2" data-colwidth="30.0000%" width="30.0000%" style="border-color: rgb(62, 62, 62);border-style: none;background-repeat: no-repeat;background-attachment: scroll;vertical-align: bottom;background-image: url(&#34;https://mmbiz.qpic.cn/sz_mmbiz_png/zfoRGB81MxO8VSPIqY0jhhibzq9yMdoShdflfY2O2sNOicmpAogEMnp8LcWCiaHobakM1lANW0HFSk8HickRrcZHiaXgtCHt0icsbo5wW8aRaJ0kc/640?wx_fmt=png&amp;from=appmsg&#34;);padding: 0px;background-position: 50% 50% !important;background-size: cover !important;"><section style="margin: 0px 0% 4px;"><section style="text-align: right;padding: 0px 4px;color: rgb(255, 255, 255);font-size: 32px;line-height: 1;"><p><strong><span leaf="">01</span></strong></p></section></section></td><td data-colwidth="70.0000%" width="70.0000%" style="border-color: rgb(62, 62, 62);border-style: none;padding: 0px 10px;background-color: rgb(249, 249, 249);"><section style="margin: 10px 0% 0px;"><section style="color: rgb(140, 140, 140);"><p style="white-space: normal;"><span style="color: rgb(202, 29, 24);"><span leaf="">● </span></span><span style="color: rgb(58, 66, 94);"><span leaf=""><a class="normal_text_link mp_article_text_link" target="_blank" style="" href="https://mp.weixin.qq.com/s?__biz=MzA4MTg0MDQ4Nw==&amp;mid=2247586499&amp;idx=1&amp;sn=9f0f70f075ec089ad3aa4c08aae9e67f&amp;scene=21#wechat_redirect" textvalue="“AI安全报告”首位推荐！360构建智能体全域防护体系" data-itemshowtype="0" linktype="text" data-linktype="2">“AI安全报告”首位推荐！360构建智能体全域防护体系</a></span></span></p></section></section></td></tr><tr><td data-colwidth="70.0000%" width="70.0000%" style="border-color: rgb(62, 62, 62);border-style: none;padding: 0px 10px;background-color: rgb(249, 249, 249);"><section style="margin: 10px 0%;"><section style="line-height: 1;color: rgb(140, 140, 140);"><p style="text-align: right;white-space: normal;"><span style="font-size: 14px;color: rgb(208, 208, 208);"><span leaf="">► 点击阅读</span></span></p></section></section></td></tr></tbody></table></section></section></td></tr><tr><td data-colwidth="100.0000%" width="100.0000%" style="border-width: 0px;border-color: rgb(62, 62, 62) rgb(62, 62, 62) rgb(255, 255, 255);border-style: none;padding: 0px 0px 10px;"><section style="min-height: 40px;margin: 0px 0%;"><section style="width: 100%;margin: 0px auto -10px;"><table><tbody><tr><td rowspan="2" data-colwidth="30.0000%" width="30.0000%" style="border-color: rgb(62, 62, 62);border-style: none;background-repeat: no-repeat;background-attachment: scroll;vertical-align: bottom;background-image: url(&#34;https://mmbiz.qpic.cn/mmbiz_jpg/zfoRGB81MxNwD4qeIU9M1CKn8q46lDyVHlR8cfyibmNbOy4R9rc9ouAiapj8rZS6kO7FfoXKoEDeJ5dBJJQXzFaiarbyUAoapts3TogZrC8HLY/640?wx_fmt=jpeg&amp;from=appmsg&#34;);padding: 0px;background-position: 50% 50% !important;background-size: cover !important;"><section style="margin: 0px 0% 4px;"><section style="text-align: right;padding: 0px 4px;color: rgb(255, 255, 255);font-size: 32px;line-height: 1;"><p><strong><span leaf="">02</span></strong></p></section></section></td><td data-colwidth="70.0000%" width="70.0000%" style="border-color: rgb(62, 62, 62);border-style: none;padding: 0px 10px;background-color: rgb(249, 249, 249);"><section style="margin: 10px 0% 0px;"><section style="color: rgb(71, 193, 168);"><p style="white-space: normal;"><span style="color: rgb(202, 29, 24);"><span leaf="">● <a class="normal_text_link mp_article_text_link" target="_blank" style="" href="https://mp.weixin.qq.com/s?__biz=MzA4MTg0MDQ4Nw==&amp;mid=2247586322&amp;idx=1&amp;sn=c68961f482c2dc603287457d88414ec8&amp;scene=21#wechat_redirect" textvalue="ISC.AI 2026 周鸿祎演讲全文：打造中国版“Mythos”，应对网络安全新挑战" data-itemshowtype="0" linktype="text" data-linktype="2">ISC.AI 2026 周鸿祎演讲全文：打造中国版“Mythos”，应对网络安全新挑战</a></span></span></p></section></section></td></tr><tr><td data-colwidth="70.0000%" width="70.0000%" style="border-color: rgb(62, 62, 62);border-style: none;padding: 0px 10px;background-color: rgb(249, 249, 249);"><section style="margin: 10px 0%;"><section style="line-height: 1;color: rgb(140, 140, 140);"><p style="text-align: right;white-space: normal;"><span style="font-size: 14px;color: rgb(208, 208, 208);"><span leaf="">► 点击阅读</span></span></p></section></section></td></tr></tbody></table></section></section></td></tr><tr><td data-colwidth="100.0000%" width="100.0000%" style="border-width: 0px;border-color: rgb(62, 62, 62) rgb(62, 62, 62) rgb(255, 255, 255);border-style: none;padding: 0px 0px 10px;"><section style="min-height: 40px;margin: 0px 0%;"><section style="width: 100%;margin: 0px auto -10px;"><table><tbody><tr><td rowspan="2" data-colwidth="30.0000%" width="30.0000%" style="border-color: rgb(62, 62, 62);border-style: none;background-repeat: no-repeat;background-attachment: scroll;vertical-align: bottom;background-image: url(&#34;https://mmbiz.qpic.cn/mmbiz_png/zfoRGB81MxNZBvcaF2AKsib5ibTsxJBOt0T6VtDnyOSJETyrqXtLn1GTW2nP4Hq3j8fsnyx5Sn1iaiaVqtpdW1uUicUDicibb9oK58bmy7Ru60da8A/640?wx_fmt=png&amp;from=appmsg&#34;);padding: 0px;background-position: 50% 50% !important;background-size: cover !important;"><section style="margin: 0px 0% 4px;"><section style="text-align: right;padding: 0px 4px;color: rgb(255, 255, 255);font-size: 32px;line-height: 1;"><p><strong><span leaf="">03</span></strong></p></section></section></td><td data-colwidth="70.0000%" width="70.0000%" style="border-color: rgb(62, 62, 62);border-style: none;padding: 0px 10px;background-color: rgb(249, 249, 249);"><section style="margin: 10px 0% 0px;"><section style="color: rgb(71, 193, 168);"><p style="white-space: normal;"><span style="color: rgb(202, 29, 24);"><span leaf="">● <a class="normal_text_link mp_article_text_link" target="_blank" style="" href="https://mp.weixin.qq.com/s?__biz=MzA4MTg0MDQ4Nw==&amp;mid=2247586269&amp;idx=1&amp;sn=69bbe8ecf24060ed8b241b827f1ed95f&amp;scene=21#wechat_redirect" textvalue="三项荣誉！360漏洞挖掘智能体登顶华为终端安全贡献榜" data-itemshowtype="0" linktype="text" data-linktype="2">三项荣誉！360漏洞挖掘智能体登顶华为终端安全贡献榜</a></span></span></p></section></section></td></tr><tr><td data-colwidth="70.0000%" width="70.0000%" style="border-color: rgb(62, 62, 62);border-style: none;padding: 0px 10px;background-color: rgb(249, 249, 249);"><section style="margin: 10px 0%;"><section style="line-height: 1;color: rgb(140, 140, 140);"><p style="text-align: right;white-space: normal;"><span style="font-size: 14px;color: rgb(208, 208, 208);"><span leaf="">► 点击阅读</span></span></p></section></section></td></tr></tbody></table></section></section></td></tr><tr><td data-colwidth="100.0000%" width="100.0000%" style="border-width: 0px;border-color: rgb(62, 62, 62) rgb(62, 62, 62) rgb(255, 255, 255);border-style: none;padding: 0px 0px 10px;"><section style="min-height: 40px;margin: 0px 0%;"><section style="width: 100%;margin: 0px auto -10px;"><table><tbody><tr><td rowspan="2" data-colwidth="30.0000%" width="30.0000%" style="border-color: rgb(62, 62, 62);border-style: none;background-repeat: no-repeat;background-attachment: scroll;vertical-align: bottom;background-image: url(&#34;https://mmbiz.qpic.cn/sz_mmbiz_jpg/zfoRGB81MxNuX78k2T7smkDrd5EV92r0MDAMXkCrszIaG1akSRmCOZibpncTHAn2a1zic37lf4vcbvLP1mxHw7XJCvEITckEFl5PVQ1Q5ic2HE/640?wx_fmt=jpeg&amp;from=appmsg&#34;);padding: 0px;background-position: 50% 50% !important;background-size: cover !important;"><section style="margin: 0px 0% 4px;"><section style="text-align: right;padding: 0px 4px;color: rgb(255, 255, 255);font-size: 32px;line-height: 1;"><p><strong><span leaf="">04</span></strong></p></section></section></td><td data-colwidth="70.0000%" width="70.0000%" style="border-color: rgb(62, 62, 62);border-style: none;padding: 0px 10px;background-color: rgb(249, 249, 249);"><section style="margin: 10px 0% 0px;"><section style="color: rgb(140, 140, 140);"><p style="white-space: normal;"><span style="color: rgb(202, 29, 24);"><span leaf="">● <a class="normal_text_link mp_article_text_link" target="_blank" style="" href="https://mp.weixin.qq.com/s?__biz=MzA4MTg0MDQ4Nw==&amp;mid=2247586248&amp;idx=1&amp;sn=08eae8d96a27a1c6e3b6b358160b9db1&amp;scene=21#wechat_redirect" textvalue="360获国内首个人工智能安全能力认证 智能体安全进入“持证时代”" data-itemshowtype="0" linktype="text" data-linktype="2">360获国内首个人工智能安全能力认证 智能体安全进入“持证时代”</a></span></span></p></section></section></td></tr><tr><td data-colwidth="70.0000%" width="70.0000%" style="border-color: rgb(62, 62, 62);border-style: none;padding: 0px 10px;background-color: rgb(249, 249, 249);"><section style="margin: 10px 0%;"><section style="line-height: 1;color: rgb(140, 140, 140);"><p style="text-align: right;white-space: normal;"><span style="font-size: 14px;color: rgb(208, 208, 208);"><span leaf="">► 点击阅读</span></span></p></section></section></td></tr></tbody></table></section></section></td></tr></tbody></table>  
  
  

#  硬刚FBI！黑客组织ShinyHunters 用0day入侵FBI，改FBI网站是条狗。  
原创 NightTeam
                    NightTeam  夜组OSINT   2026-09-23 00:59  
  
2026 年 9 月 22 日，网络犯罪组织 ShinyHunters 公开宣称已入侵美国联邦调查局（FBI）相关系统，并掌握“几乎所有”现职特工、前雇员及求职者的敏感信息。该组织同时向 FBI 局长 Kash Patel 与网络部门助理局长 Brett Leatherman 发出通牒，要求一周内更正或删除今年 5 月发布的一份针对该组织的公开警告。  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/es3jUghv8rib9RnvZ7oaCDDzTOztmCYbcSiaNmSv1kthhauzzBhZn84SYZUib1UHiamtGQDibLeWTBrX79mqZVcJR28CsTEUlNia3jTFKRsiahI7IY/640?wx_fmt=webp&from=appmsg "")  
  
FBI 尚未确认系统被攻破或数据被盗，仅表示“已知悉有关 FBIjobs.gov 未授权活动的说法，正在调查”。  
## 黑客声称拿到了什么  
  
多家媒体收到的说法高度一致：ShinyHunters 称已拿下 FBI 刑事司法（CJ）、人力资源（HR）、名为 Medlink 的服务等系统，并从 AWS GovCloud 环境中导出约 2–3TB 数据。  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/es3jUghv8ricGIqz44UTK5AibIibeic9XEjE1vudATnImjWjV0icLUxCk0XpXZU8MRhxX69WltXxtqsfWJJtJDEgj7VuyuMPiaUfAHHtN9HtTwPUw/640?wx_fmt=webp&from=appmsg "")  
  
该组织向其提供一份约 5000 条记录的样本，内容包括姓名、住址、电话、出生日期，部分还含配偶信息。404 Media 用开源情报工具核对部分电话号码，发现与同名人士及司法部相关人员匹配。Nextgov/FCW 亦称对样本中部分姓名进行检索后，确认对应人员确在 FBI 任职，岗位涵盖特工、情报分析员、律师、见习生等。两家媒体都强调：全量数据并未得到独立核验。  
  
该组织还提供了两条“样本记录”，其中一条据称与此前调查 BreachForums 的特工有关，另一条据称与局长 Patel 有关。该媒体未公布其中个人信息，也未独立验证真伪。  
  
Politico 援引两名知情人士称，调查人员认为这批说法“可信”，并视其为一次严重的反情报失败——哪怕只有少数人员身份信息外泄，也可能被用于骚扰、恐吓现职人员及其家属，并引起外国情报机构与暴力犯罪集团的兴趣。其中一人评价：“情况很糟。”  
## 招聘网站被“扣押”  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/es3jUghv8ricJjjaNlunM1nVxoOQsHVqwulib5zfjfM4KhkmkjZPh0aUKnSAApMScicfosZKYUl6BLIkRAHVI7GuGEIxcLI9FUTXYQpaWp2Yqw/640?wx_fmt=webp&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/es3jUghv8r8uBD1IVnIiadz9PP6V0Kbb0MXn99FhHkicv9V3qMXu6A00ibia4r2ibsXkQvoUrSTPL3OIolAvmibs56QjGorBZmme3ajfd3xaaDyY0/640?wx_fmt=webp&from=appmsg "")  
  
9 月 22 日，FBI 招聘站点 apply.fbijobs.gov 一度出现仿照执法机关扣押告示风格的页面，配有 ShinyHunters 的 Umbreon 宝可梦标志，并写着“THIS SITE HAS BEEN SEIZED BY SHINYHUNTERS. rooting your systems since '19 ;)”。页面还声称“所有 FBI 数据均已失守，包括现任、前任雇员及全部申请人的 PII/PHI”，并以戏仿特朗普 Truth Social 口吻结尾：“Thank you for your attention to this matter.” 随后该站改为“计划维护中/暂时不可用”。ShinyHunters 称 FBI 发现入侵后“几乎同时拔掉所有插头”。维护公告本身并未说明是否与安全事件有关。  
## 入口：据称是 PeopleSoft 零日  
  
周一夜间利用 Oracle PeopleSoft 中一个此前未知、可远程代码执行的漏洞进入，再横向移动到 FBI 管理的 AWS GovCloud。他们还称正把同一漏洞用于其他机构，包括财富 500 强企业，并试图擦除服务器上的痕迹，以免漏洞被识别。BleepingComputer 已向 Oracle 与 Google Cloud 旗下 Mandiant 求证，尚无公开回应。相关说法均未获独立验证。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/es3jUghv8ribnrmy9KSuiarhpbX7XQxBlFIW2fic6I8gIFgIzlUmn0EnLQlQ5skkyO4icia3zZJBG6FL95jIstibvTTVsBWxfVmQOLnBeSrddxxOY/640?wx_fmt=webp&from=appmsg "")  
  
ShinyHunters 今年 6 月曾利用当时未知的 PeopleSoft 漏洞打击教育机构；该洞后来被 Oracle 修补。因此本次既可能是新零日，也可能是旧洞未打补丁，调查仍在进行。Suzu Labs CTO Dan Calderone 提醒安全圈应重点关注“PeopleSoft 可被广泛利用”这一主张；他同时对“非经济动机”表示怀疑：数 TB 的 FBI 人事数据很难“只放在架子上”，对外国情报机构价值极高，而“黑过 FBI”本身也会抬高该组织今后的勒索可信度——若零日属实，漏洞本身可能比数据更值钱。  
## 通牒针对的是五月那份 FLASH/PSA  
  
FBI 今年第二季度 FLASH 报告对其作出“重大虚假指控”，令其“深受冒犯”，因此采取“强势姿态”以确保回应被听见。他们要求一周内更正或删除 2026 年 5 月 15 日 IC3 公告中的三点：一是行为者常夸大对敏感信息的掌握以逼迫付款；二是常以威胁短信、电话骚扰受害者及其家人，个别情况下实施“诈警”（swatting）；三是可能谎称持有令人难堪的照片或视频。ShinyHunters 逐条否认，并强调“从未对受害者家属发威胁、从未 swatting、不是性勒索者、不属于 The Com”。信中写明“这不是赎金、胁迫或勒索”“非经济动机”，并以“Make the right decision, don't be the next headline”收尾。被问及若 FBI 拒改报告是否会公开数据时，该组织对 BleepingComputer 只答“No comment”；被问是否担心美国政府加大抓捕力度，答复是“I don't care.”  
  
那份 5 月公告发布于一次针对教育机构在线学习管理系统的攻击之后。FBI 新近公布的网络战略也强调：即便行为人远在美国执法能力之外，仍要更快拆解黑客基础设施并实施抓捕。此次事件恰好落在这条更强硬路线的背景上。  
## 这并非该组织第一次与 Oracle 漏洞纠缠  
  
：2025 年 Clop 针对 Oracle E-Business Suite 的数据窃取行动中，自称为 “Scattered Lapsus$ Hunters” 的组合泄露过 PoC，后经 Oracle 确认与攻击所用漏洞相符。ShinyHunters 事后声称该利用原本属于自己，被 Clop 擅自拿走。上周该组织还攻陷并涂改 Clop 泄露站，声称拿走服务器数据与 Tor 私钥，并把 Clop 列入己方泄露站以示报复。换句话说，这次对 FBI 的“声誉战”，与它近期对同行、对厂商的高调对抗是同一条线索。  
  
该组织今年的其他动作：泄露麦迪逊广场花园顾客数据、被指参与欧盟委员会云基础设施入侵，以及 Anthropic 本月威胁情报报告中对其数据窃取行动的描述。FBI 网络部前副助理局长 Cynthia Kaiser 认为，这种针对执法机构的“报复式”攻击对勒索团伙而言并不典型，反映出该组织的不可预测与不成熟；但她也指出，网络犯罪分子长期盯着执法部门，目的就是摸清调查并锁定背后的人。  
## 目前能确定的，和还不能确定的  
  
**可以交叉印证的：**  
组织已公开对 FBI 高层喊话；招聘站出现过涂改/扣押页后进入维护；多家媒体看到含住址、电话、亲属信息的数千条样本，部分条目与公开信息及司法部人员吻合；FBI 承认正在调查与 FBIjobs.gov 相关的未授权活动说法。  
  
**尚未被官方或第三方坐实的：**  
入侵路径是否确为 PeopleSoft 新零日、横向进入 GovCloud 的细节、2–3TB 的体量、所谓“几乎全部雇员与申请人”的覆盖范围，以及样本之外数据的完整性与来源。Oracle、AWS 尚未就报道作出公开说明。  
  
若数据为真，风险并不止于隐私。人事与家属信息可被用来识别、定位、施压一线人员；对外国情报机构，这是理解美国主要联邦执法与情报机构如何运转的窗口；对同一犯罪生态中的其他团伙，这是报复调查者的弹药。404 Media 指出，同一生态里的黑客过去就曾用通话记录等数据追踪、恐吓调查他们的 FBI 人员。Kaiser 与 Calderone 的判断指向同一点：即便组织口口声声“不是勒索”，数据一旦流出，后续用途并不由声明决定。  
  
一周通牒是否会被理会、数据会否被公开或转手，目前都没有答案。对其他仍在运行 PeopleSoft、尤其是未及时打补丁的机构而言，真正需要立刻处理的，可能不是这场舆论对峙本身，而是漏洞主张是否成立、补丁与日志审计是否已经跟上。  
  
参考：https://www.nextgov.com/cybersecurity/2026/09/shinyhunters-claims-fbi-data-theft-demands-bureau-retract-cyber-warning/416144/  
  
https://www.politico.com/news/2026/09/22/shinyhunters-fbi-cyber-hack-01088494  
## 威胁情报全球监控系统  
  
以上威胁事件由全球威胁情报系统（dark.libaisec.com）实时监测发现，订阅会员即可查看威胁情报详情及原文件。  
  
![dark.libaisec.com](https://mmbiz.qpic.cn/sz_mmbiz_jpg/es3jUghv8ribkxaa9vN3kfAicicqVElCUaIPYh3ibFONMf3GQia70PlbWBWVmhAl4fHiac1ncYU5yeqcUhicaRKYCt5LT0arAKQfttGolX5tCqiaFWw/640?wx_fmt=webp&from=appmsg "")  
  
dark.libaisec.com  
  

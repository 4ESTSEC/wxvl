#  【已复现】漏洞通告 | CUPS 本地权限提升漏洞  
安全实验室
                    安全实验室  中成信息   2026-09-20 06:43  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/iboUMajImW6QEqrJF4JnQOGkr3oahUUZeljHItNu0Jlv4XmRBM5vibEPSfsM0o02hF9lNSTnOItNvzqETpOOVbiag/640?wx_fmt=png&from=appmsg "")  
  
**1**  
  
  
**漏洞描述**  
  
CUPS本地权限提升漏洞是CUPS打印系统中的一系列配置与权限边界缺陷串联形成的高危漏洞，允许低权限用户通过lpadmin组获取root权限。该漏洞利用链涉及串口后端以root运行改写配置文件、触发服务崩溃重启等步骤，最终实现交互式root shell。  
  
  
2  
  
  
**影响范围**  
  
Ubuntu 26.04 LTS(CUPS 2.4.16-1ubuntu1.3、CUPS-Filters 2.0.1-0ubuntu4.1)，其他满足“systemd+本地CUPS服务+特权serial后端+标准Ubuntu CUPS路径”条件的Ubuntu/Debian发行版可能同样受影响。  
  
  
  
3  
  
  
**漏洞详情**  
<table><tbody><tr><td colspan="4" data-colwidth="143,144,143,143"><section style="text-align: center;" data-nest-level="6"><span data-pm-slice="0 0 []" data-nest-level="7"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-family: &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;font-size: 13px;font-style: normal;font-variant-ligatures: normal;font-variant-caps: normal;font-weight: 700;letter-spacing: 0.544px;orphans: 2;text-align: center;text-indent: 0px;text-transform: none;widows: 2;word-spacing: 0px;-webkit-text-stroke-width: 0px;background-color: rgb(255, 255, 255);text-decoration-thickness: initial;text-decoration-style: initial;text-decoration-color: initial;float: none;display: inline !important;" data-nest-level="8">漏洞详情</span></span></section></td></tr><tr><td data-colwidth="143"><section style="text-align: center;" data-nest-level="6"><span data-pm-slice="0 0 []" data-nest-level="7"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-family: &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;font-size: 13px;font-style: normal;font-variant-ligatures: normal;font-variant-caps: normal;font-weight: 700;letter-spacing: 0.544px;orphans: 2;text-align: center;text-indent: 0px;text-transform: none;widows: 2;word-spacing: 0px;-webkit-text-stroke-width: 0px;background-color: rgb(255, 255, 255);text-decoration-thickness: initial;text-decoration-style: initial;text-decoration-color: initial;float: none;display: inline !important;" data-nest-level="8">漏洞名称</span></span></section></td><td colspan="3" data-colwidth="144,143,143"><section style="text-align: center;" data-nest-level="6"><span leaf="" style="text-align: center;background-color: rgb(255, 255, 255);color: rgba(0, 0, 0, 0.9);font-size: 13px;font-family: &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;letter-spacing: 0.544px;font-style: normal;font-weight: 400;" data-nest-level="7">CUPS 本地权限提升漏洞</span></section></td></tr><tr><td data-colwidth="143"><section style="text-align: center;" data-nest-level="6"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-family: &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;font-size: 13px;font-style: normal;font-variant-ligatures: normal;font-variant-caps: normal;font-weight: 700;letter-spacing: 0.544px;orphans: 2;text-align: center;text-indent: 0px;text-transform: none;widows: 2;word-spacing: 0px;-webkit-text-stroke-width: 0px;background-color: rgb(255, 255, 255);text-decoration-thickness: initial;text-decoration-style: initial;text-decoration-color: initial;float: none;display: inline !important;" data-nest-level="7">评级</span></section></td><td data-colwidth="144"><section style="text-align: center;" data-nest-level="6"><span leaf="" style="color: rgb(255, 41, 65);font-family: &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;font-size: 13px;font-style: normal;font-variant-ligatures: normal;font-variant-caps: normal;font-weight: 700;letter-spacing: 0.544px;orphans: 2;text-indent: 0px;text-transform: none;widows: 2;word-spacing: 0px;-webkit-text-stroke-width: 0px;background-color: rgb(255, 255, 255);text-decoration-thickness: initial;text-decoration-style: initial;text-decoration-color: initial;float: none;display: inline !important;" data-nest-level="7"><span textstyle="" style="color: rgb(255, 41, 65);">高危</span></span></section></td><td data-colwidth="143"><section style="text-align: center;" data-nest-level="6"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-family: &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;font-size: 13px;font-style: normal;font-variant-ligatures: normal;font-variant-caps: normal;font-weight: 700;letter-spacing: 0.544px;orphans: 2;text-align: center;text-indent: 0px;text-transform: none;widows: 2;word-spacing: 0px;-webkit-text-stroke-width: 0px;background-color: rgb(255, 255, 255);text-decoration-thickness: initial;text-decoration-style: initial;text-decoration-color: initial;float: none;display: inline !important;" data-nest-level="7">CVSS 3.1分数</span></section></td><td data-colwidth="143"><section style="text-align: center;" data-nest-level="6"><span leaf="" style="color: rgb(255, 41, 65);font-family: &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;font-size: 13px;font-style: normal;font-variant-ligatures: normal;font-variant-caps: normal;font-weight: 700;letter-spacing: 0.544px;orphans: 2;text-indent: 0px;text-transform: none;widows: 2;word-spacing: 0px;-webkit-text-stroke-width: 0px;background-color: rgb(255, 255, 255);text-decoration-thickness: initial;text-decoration-style: initial;text-decoration-color: initial;float: none;display: inline !important;" data-nest-level="7"><span textstyle="" style="color: rgb(255, 41, 65);">7.8</span></span></section></td></tr><tr><td data-colwidth="143"><section style="text-align: center;" data-nest-level="6"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-family: &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;font-size: 13px;font-style: normal;font-variant-ligatures: normal;font-variant-caps: normal;font-weight: 700;letter-spacing: 0.544px;orphans: 2;text-align: center;text-indent: 0px;text-transform: none;widows: 2;word-spacing: 0px;-webkit-text-stroke-width: 0px;background-color: rgb(255, 255, 255);text-decoration-thickness: initial;text-decoration-style: initial;text-decoration-color: initial;float: none;display: inline !important;" data-nest-level="7">威胁类型</span></section></td><td data-colwidth="144"><section style="text-align: center;" data-nest-level="6"><span leaf="" style="background-color: rgb(255, 255, 255);color: rgb(255, 41, 65);font-size: 13px;font-family: &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;letter-spacing: 0.544px;font-style: normal;font-weight: 700;" data-nest-level="7">权限提升</span></section></td><td data-colwidth="143"><section style="text-align: center;" data-nest-level="6"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-family: &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;font-size: 13px;font-style: normal;font-variant-ligatures: normal;font-variant-caps: normal;font-weight: 700;letter-spacing: 0.544px;orphans: 2;text-align: center;text-indent: 0px;text-transform: none;widows: 2;word-spacing: 0px;-webkit-text-stroke-width: 0px;background-color: rgb(255, 255, 255);text-decoration-thickness: initial;text-decoration-style: initial;text-decoration-color: initial;float: none;display: inline !important;" data-nest-level="7">利用情况</span></section></td><td data-colwidth="143"><section style="text-align: center;" data-nest-level="6"><span data-pm-slice="0 0 []" data-nest-level="7"><span leaf="" style="color: rgb(255, 41, 65);font-family: &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;font-size: 13px;font-style: normal;font-variant-ligatures: normal;font-variant-caps: normal;font-weight: 700;letter-spacing: 0.544px;orphans: 2;text-indent: 0px;text-transform: none;widows: 2;word-spacing: 0px;-webkit-text-stroke-width: 0px;background-color: rgb(255, 255, 255);text-decoration-thickness: initial;text-decoration-style: initial;text-decoration-color: initial;float: none;display: inline !important;" data-nest-level="8"><span textstyle="" style="color: rgb(255, 41, 65);">更可能被利用</span></span></span></section></td></tr><tr><td data-colwidth="143"><section style="text-align: center;" data-nest-level="6"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-family: &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;font-size: 13px;font-style: normal;font-variant-ligatures: normal;font-variant-caps: normal;font-weight: 700;letter-spacing: 0.544px;orphans: 2;text-align: center;text-indent: 0px;text-transform: none;widows: 2;word-spacing: 0px;-webkit-text-stroke-width: 0px;background-color: rgb(255, 255, 255);text-decoration-thickness: initial;text-decoration-style: initial;text-decoration-color: initial;float: none;display: inline !important;" data-nest-level="7">公开状态</span></section></td><td data-colwidth="144"><section style="text-align: center;" data-nest-level="6"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-family: &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;font-size: 13px;font-style: normal;font-variant-ligatures: normal;font-variant-caps: normal;font-weight: 400;letter-spacing: 0.544px;orphans: 2;text-indent: 0px;text-transform: none;widows: 2;word-spacing: 0px;-webkit-text-stroke-width: 0px;background-color: rgb(255, 255, 255);text-decoration-thickness: initial;text-decoration-style: initial;text-decoration-color: initial;float: none;display: inline !important;" data-nest-level="7"><span textstyle="" style="font-weight: bold;">POC、EXP已公开</span></span></section></td><td data-colwidth="143"><section style="text-align: center;" data-nest-level="6"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-family: &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;font-size: 13px;font-style: normal;font-variant-ligatures: normal;font-variant-caps: normal;font-weight: 700;letter-spacing: 0.544px;orphans: 2;text-align: center;text-indent: 0px;text-transform: none;widows: 2;word-spacing: 0px;-webkit-text-stroke-width: 0px;background-color: rgb(255, 255, 255);text-decoration-thickness: initial;text-decoration-style: initial;text-decoration-color: initial;float: none;display: inline !important;" data-nest-level="7">在野利用</span></section></td><td data-colwidth="143"><section style="text-align: center;" data-nest-level="6"><span leaf="" style="color: rgba(0, 0, 0, 0.9);font-family: &#34;PingFang SC&#34;, system-ui, -apple-system, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;font-size: 13px;font-style: normal;font-variant-ligatures: normal;font-variant-caps: normal;font-weight: 400;letter-spacing: 0.544px;orphans: 2;text-indent: 0px;text-transform: none;widows: 2;word-spacing: 0px;-webkit-text-stroke-width: 0px;background-color: rgb(255, 255, 255);text-decoration-thickness: initial;text-decoration-style: initial;text-decoration-color: initial;float: none;display: inline !important;" data-nest-level="7"><span textstyle="" style="font-weight: bold;">未发现</span></span></section></td></tr><tr><td colspan="4" data-colwidth="143,144,143,143"><section data-nest-level="6"><span leaf="" data-nest-level="7"><span textstyle="" style="font-size: 13px;font-weight: bold;">危害描述</span></span><span style="letter-spacing: 0.034em;background-color: transparent;"><span leaf="">：</span><span leaf=""><span textstyle="" style="font-size: 13px;font-weight: normal;">攻击者可完全绕过身份认证边界，将lpadmin组权限提升为root，获取交互式root shell，进而完全控制主机、读取/篡改任意数据、安装持久化后门或横向移动。</span></span></span></section></td></tr><tr><td colspan="4" data-colwidth="143,144,143,143"><section><span leaf="" style=""><span textstyle="" style="font-size: 13px;font-weight: bold;">参考链接: </span></span></section><section><span leaf=""><span textstyle="" style="font-size: 13px;font-weight: normal;">https://github.com/v12-security/pocs/tree/main/cups/cups2root</span></span></section></td></tr></tbody></table>  
  
4  
  
  
**漏洞复现**  
  
中成信息安全实验  
室已复现CUPS 本地权限提升漏洞  
，验证如下。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/IOnTlXyEl25pcIGyt59NmfbAxEyOemrUCyS0oRSKBtdCPPXJ9X3VE87MLd1Q0libNkpR8Kv2PQNbEy4oJpJy2G2LnEeMRv123icm0goX4cjUg/640?wx_fmt=png&from=appmsg "")  
  
  
5  
  
  
**修复建议**  
  
目前上游尚未发布官方补丁，建议采取以下临时缓解措施：  
  
1.收紧lpadmin组权限  
  
审计并清理lpadmin组成员，仅保留可信管理员。使用命令 getent group lpadmin 查看当前组成员，移除不必要的用户。  
  
2.处置特权serial后端  
  
若主机不需要串口打印功能，可卸载cups-filters对应的serial后端或移除后端文件。若需保留，可通过AppArmor等强制访问控制工具，限制cupsd对 /etc/cups 和 /etc/cups/interfaces 目录的写入权限。  
  
3.审计打印队列  
  
使用 lpstat -v 或检查 /etc/cups/printers.conf 文件，排查设备URI指向 serial:/ 且路径非真实串口设备（如指向 /etc/cups/...）的打印队列，发现后立即删除。  
  
4.限制CUPS网络暴露  
  
若非必要，关闭CUPS的网络端口（默认631端口），避免外部网络直接访问CUPS服务。  
  
5.持续关注官方公告  
  
定期查看OpenPrinting官方安全公告页面(https://github.com/openprinting/cups/security/advisories)，一旦补丁发布，及时更新系统。  
  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/iboUMajImW6SWlEyv47fvIgYlBYvBPY4SUlNQ5ia1qWP6CdmAnkTDWcgGM21xo6kqGqMicl0NPpncTGJZWwzicoO5A/640?wx_fmt=png&from=appmsg "")  
  
  
  
  
  
关于我们  
  
  
漳州中成信息科技有限公司是一家专注于网络安全实战防护的创新型服务提供商。我们深刻理解网络安全的核心在于攻防对抗的持续较量，并以此独特视角为基石，致力于为客户构建动态、主动、智能化的纵深防御体系。区别于传统的被动防御，我们坚信“未知攻，焉知防”。公司汇聚了顶尖的渗透测试专家（红队）、应急处置精英（蓝队）及经验丰富的安全服务工程师，形成了一支具备完整攻防对抗能力的专业团队。我们的渗透测试团队模拟真实攻击者的思维与手段，深入挖掘系统、应用及网络中的深层次漏洞与风险点；应急处置团队则能在安全事件发生时快速响应、精准定位、有效遏制损失并溯源根因；安服工程师团队则致力于将攻防对抗中获得的宝贵经验转化为常态化的安全策略、加固措施与运营流程。  
  
  
  
  
  
  
**点击名片**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/iboUMajImW6SWlEyv47fvIgYlBYvBPY4Siau2HicdH2XxjSEtMnzvqz4cTYibemFyA3TvGH4ZLYABel0MzmHoL8wJQ/640?wx_fmt=png&from=appmsg "")  
  
**关注我们**  
  
  
  
**扫描官网二维码**  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/iboUMajImW6SWlEyv47fvIgYlBYvBPY4Siau2HicdH2XxjSEtMnzvqz4cTYibemFyA3TvGH4ZLYABel0MzmHoL8wJQ/640?wx_fmt=png&from=appmsg "")  
  
**了解更多**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/IOnTlXyEl26GI78T6YncCwKHUKyaGaPfNrv9UJ9HO2UzCY5bafOpicHYkAQ0GM2nN2ib7D75utBpNud4pfcYSb2zojicstr6bVn2jOrIw6ick5s/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/iboUMajImW6SWlEyv47fvIgYlBYvBPY4SXmokj8yGgrQAoBPcFlOgWdWUcj8e5rUKUQVVTQ0ibsppahzAstALX6w/640?wx_fmt=gif&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/iboUMajImW6SWlEyv47fvIgYlBYvBPY4ShmKJlD9Q30YqOaiamGgmfOA3libRTCd5cNA1qM7z8RUsAr56ibrAocibiag/640?wx_fmt=gif&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/iboUMajImW6SWlEyv47fvIgYlBYvBPY4SsibXafic39wibiaEqD6KgYYCSR6Fn5PgAclH1kkky6SglBKoSOTDo4A8wA/640?wx_fmt=gif&from=appmsg "")  
  
  

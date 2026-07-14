#  [AI]Claude + Skills 自动漏洞挖掘  
Yhsec
                    Yhsec  C4安全   2026-07-14 02:10  
  
  CTF课程培训  
  
  扫码咨询  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/niasx7fyic9CMs6pyt2xl3Ng0QWByv0oD67COatsbL7SbcLetqC6mr3bFalmibIID1ricPHNl6xHHrqjmb0vLc7Sm0ficuiaroLKTJrNDibIPJwoBk/640?wx_fmt=jpeg "")  
  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/b96CibCt70iaaJcib7FH02wTKvoHALAMw4fchVnBLMw4kTQ7B9oUy0RGfiacu34QEZgDpfia0sVmWrHcDZCV1Na5wDQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1&tp=wxpic#imgIndex=1 "")  
  
  
#   
  
专注于漏洞挖掘、系统化从基础入门到实战漏洞挖掘，包含团队自整的挖掘注意点和案例、渗透经验、SRC漏洞案例、代码审计、挖洞思路等高价值资源。不定期分享各种好玩的项目及好用的工具，欢迎关注。加内部圈子，  
文末有优惠券。  
#   
  
文章作者：NPE~  
  
文章来源：  
https://blog.csdn.net/weixin_45565886/article/details/162495525  
  
前置环境  
  
Windows：  
  
  
```
# 安装node.js官方网址：https://nodejs.org/zh-cn# 安装Claude Code，可以安装cli也可以直接安装桌面端桌面端：https://code.claude.com/docs/zh-CN/desktopCli端：https://code.claude.com/docs/zh-CN/quickstart
```  
  
  
相关Skills：  
  
因为目标是渗透，所以必不可少需要代码审计以及渗透相关的Skills，来帮助模型学会某方面能力。  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/niasx7fyic9CM0TfGx5gvD0dkOQDpIjkl6iamBWpMhaHIoo9KulKDicjenRsYP258uyaNUuecjml9aF6g0ATcomeyBJYZE0SJia1mupeaCciae4GI/640?wx_fmt=png&from=appmsg "")  
  
相关网站：**https://skillsclaude.com/**  
  
  
```
# 安装安全审计技能npx hermes-skills add security-sentinel# 安装身份认证设计技能npx hermes-skills add auth-architect# 安装安全工程技能（我这里是全局安装会放在~/.agents/skills目录下）npx skills add alirezarezvani/claude-skills --skill senior-security# 其他各类渗透Skills在上面的skill平台中查找安装
```  
  
  
验证  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/niasx7fyic9CMzZu5UvVMeXaev6gia7JQBTdnicoufjjxBQIUDQNliaYW7Y4lgu6fIxxdURvZm5z0qZibry0U6Vc3L6bh4welDq9MCroEFTmmL9T4/640?wx_fmt=png&from=appmsg "")  
  
  
补天实战  
  
官方平台：https://www.butian.net/  
  
首先注册为白帽子，方便后续参加SRC提交漏洞。  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/niasx7fyic9CO1atwLJyYKiaclkicAZ0wQraSNLnJMibSfiaXyxQZtav7fs6MWqibE2DAZUOGReVY6SYF9DJCaa89ylNgo5sBWsWC9KZzIuOpIQyB0/640?wx_fmt=png&from=appmsg "")  
  
在补天官网项目大厅找到对应目标企业，渗透其下可测资产即可。  
  
地址：https://www.butian.net/Reward/plan/2  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/niasx7fyic9COfssto9mdUXBIb5GFUASVCD5G7icb9dsh0jNd67RaUL9icuviatThfeGBqOE91nF0mAH5Z3MTmib9tekBxcluVKVtZekQKDRQUPmg/640?wx_fmt=png&from=appmsg "")  
  
**找到对应资产后，直接让AI帮我们渗透，最后出报告之后我们来进行验证。**  
  
需要注册AI渗透过程中，所有操作需在授权范围内进行，并遵守《网络安全法》等相关法律法规。所以需要给AI强制规范。  
  
这里挑选了某个目标网站进行渗透  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/niasx7fyic9COvoK4zDNy3kYMkGIq1xFg28cAibZK7xWPtX5p55usUIrOg4vegdOhEllbk9vzRe902xdy70oy03ibvyCQUmG4nKNwoIGt4tBIJ4/640?wx_fmt=png&from=appmsg "")  
  
  
最终效果：直接扫描出某个网站认证泄露。  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/niasx7fyic9COzZCvU5TZem4I61MJDNoalq5oKSYUjf7mO0QR8PjN96zfhwFVGPia2p8Z1ia0TpflaSM8laEBKO1PBG5klr2r4vvhzurW8HgzuY/640?wx_fmt=png&from=appmsg "")  
  
  
一般来说补天里不是头部的互联网厂商，所以给的金额不会特别高。如果想要挖头部厂商漏洞的，可以去对应企业官方漏洞平台  
  
进阶方向  
  
MCP 接入 Burp：让 Claude Code 通过 MCP 协议直接读取 Burp 历史请求，自动定位污点参数  
  
多 Agent 协作：用 Agent 工具开多个子 Agent，一个跑 recon、一个跑审计、一个写报告，结果汇总  
  
持久化经验库：每次挖洞完用 /remember 把新的漏洞模式存到 memory，下次自动复用  
  
CI 化：把审计流程写成 GitHub Action，每天定时扫一遍自建监控的目标  
  
  
  
**内部CTF课程上线，总课程30+小时，优惠折扣中！**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/niasx7fyic9COmTqWiaO3MWicicQJbYDnl4VtJ8A6fkm0tBKFYBxbeKj9d35HJcpgSf7moVawMYwluFS6omJiaTIxPOSM9Fx6qLLZTXhU6sydlZ4A/640?wx_fmt=png&from=appmsg "")  
  
****  
**帮会简介**  
  
《  
**安全渗透感知**  
》  
是FreeBuf知识大陆的重量级帮会，帮会致力于漏洞POC/EXP、红队攻防实战，是系统化从基础入门到实战漏洞挖掘的教程社区，包含团队自整的挖掘注意点和案例，还包含分享的渗透经验、SRC漏洞案例、代码审计、挖洞思路等高价值资源。  
  
  
**内容框架（持续新增中）**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/niasx7fyic9COd1ITgnGXHdVfC79DficTDDlYBibvNAC2VSwy3LDNBdxsgqbx8lUH5uUwjicLYYf1Ee2a8bmKlC8NnvYDtzfmfia7PoC6ytYX05u8/640?wx_fmt=png&from=appmsg "")  
  
****  
**目前已有「700+」小伙伴加入了帮会**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/niasx7fyic9CN9CVSeMYUIpW50058icjreeNHRMK7jEabMtshNf15j1IHvicDotNG4ZnfrQcwHDroAooj6kMIonH8FWgcu9bUjN7n6aEXyQHrFo/640?wx_fmt=png&from=appmsg "")  
  
****  
**加入方式**  
  
目前帮会成员  
**700+**  
人，  
**永久会员优惠后只需**  
**69.9元**  
**。**  
  
随着人数的增加及资源的积累，  
**之后永久会员将**  
**涨价至99元**  
**。**  
  
****  
有意向的师傅们可以扫码加入我们，共同进步。  
  
**如何加入帮会？→**  
安卓/苹果用户  
**可扫码使用优惠券↓↓**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/niasx7fyic9CM6ibbnnP7rXgGwYFRYlibVVT4XXhCuXXUpn5qfC3MHudTZhYiaomgeFjopTUxkMnRs05icfPEH8DFgb4o8RMxTHtJNlUFBiaufCtSU/640?wx_fmt=png&from=appmsg "")  
  
****  
**→ PC端用户可复制此链接到浏览器↓↓**  
  
https://wiki.freebuf.com/societyDetail?society_id=184  
  
****  
  
  

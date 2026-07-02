#  DDDD_Viewer - dddd漏洞扫描工具可视化辅助工具  
原创 Youki
                    Youki  C4安全   2026-07-02 01:41  
  
  CTF课程培训  
  
  扫码咨询  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/niasx7fyic9CMs6pyt2xl3Ng0QWByv0oD67COatsbL7SbcLetqC6mr3bFalmibIID1ricPHNl6xHHrqjmb0vLc7Sm0ficuiaroLKTJrNDibIPJwoBk/640?wx_fmt=jpeg "")  
  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/b96CibCt70iaaJcib7FH02wTKvoHALAMw4fchVnBLMw4kTQ7B9oUy0RGfiacu34QEZgDpfia0sVmWrHcDZCV1Na5wDQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1&tp=wxpic#imgIndex=1 "")  
  
  
#   
  
专注于漏洞挖掘、系统化从基础入门到实战漏洞挖掘，包含团队自整的挖掘注意点和案例、渗透经验、SRC漏洞案例、代码审计、挖洞思路等高价值资源。不定期分享各种好玩的项目及好用的工具，欢迎关注。加内部圈子，  
文末有优惠券。  
  
  
dddd_viewer 使用说明  
  
在开始前，先介绍下改版的dddd信息收集+漏洞扫描工具新版本的**dddd 1.8.3版本**  
，新增两个使用参数：  
  
-fpoc  
  
指纹识别完成后，对所有已识别目标执行全量 PoC 扫描，不再只按指纹 workflow  
 映射选择 PoC。  
  
  
```
dddd.exe -t https://example.com -fpoc
```  
  
  
-fps  
  
端口扫描时，即使单个 IP 的开放端口数量超过 -pc  
 阈值，也不再丢弃该 IP，而是保留全量端口结果。  
  
  
```
dddd.exe -t 192.168.1.1/24 -fps
```  
  
  
历史功能可以参考这篇文章：  
  
[dddd 1.8.2 发布-xray POC 兼容、CEYE/DNSLog反连回查与漏洞检测增强](https://mp.weixin.qq.com/s?__biz=MzkzMzE5OTQzMA==&mid=2247490432&idx=1&sn=c75252c0bd99adc63fbd2de94c8cc8ea&scene=21#wechat_redirect)  
  
  
1. 功能说明  
  
dddd_viewer  
 是 dddd  
 的本地只读可视化面板，用来读取当前目录中的扫描结果并展示：  
  
1.  
  
HTML 报告列表  
  
  
2.  
  
漏洞概览和等级统计  
  
  
3.  
  
目标信息聚合  
  
  
4.  
  
最近扫描日志  
  
  
5.  
  
原始 HTML 报告预览  
  
  
**注意：它不能下发扫描任务。**  
  
**工具已分享在内部知识圈中：**  
  
****  
2. 支持的输入文件  
  
dddd_viewer  
 默认读取启动目录中的以下文件：  
  
1.  
  
*.html  
dddd  
 生成的 HTML 报告  
  
  
2.  
  
log.txt  
  
  扫描日志文件  
  
  
如果你希望查看别的目录，可以通过 -root  
 指定，**使用时只需要和dddd放在同一目录下即可**  
。  
  
3. 启动方式  
  
Windows  
  
  
```
.\dddd_viewer.exe
```  
  
  
Linux / macOS  
  
  
```
chmod +x ./dddd_viewer./dddd_viewer
```  
  
  
4. 常用参数  
  
-addr  
  
指定监听地址，默认：  
  
  
```
127.0.0.1:18081
```  
  
  
示例：  
  
  
```
.\dddd_viewer.exe -addr 127.0.0.1:19090
```  
  
  
-root  
  
指定报告目录，默认读取当前目录：  
  
  
```
.\dddd_viewer.exe -root .\reports
```  
  
  
Linux / macOS 示例：  
  
  
```
./dddd_viewer -root ./reports -addr 127.0.0.1:19090
```  
  
  
5. 登录方式  
  
程序启动后会在终端输出：  
  
1.  
  
访问地址  
  
  
2.  
  
固定用户名 admin  
  
  
3.  
  
本次启动生成的**随机密码**  
  
  
示例输出：  
  
  
```
[viewer] local dashboard: http://127.0.0.1:18081[viewer] username: admin[viewer] password: xxxxxxxxxx
```  
  
  
说明：  
  
/>  
  
密码每次启动都会重新生成  
  
  
/>  
  
重启后旧密码失效  
  
  
/>  
  
当前版本只支持单用户本地登录  
  
  
6. 推荐目录结构  
  
  
```
./├── log.txt├── 1778146150.html├── 1780560051.html└── ...
```  
  
  
然后在这个目录下启动：  
  
  
```
.\dddd_viewer.exe
```  
  
  
或者先进入目录再启动：  
  
  
```
cd .\reports..\dddd_viewer.exe
```  
  
  
7. 页面内容说明  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/niasx7fyic9CPicn5Nr2MCU5WbNzOqWrebKO4PP2yE7f8RTiclo2LoPlibm3GLsdbvz4GpXANibrxJlo6ydv3Z8bPqF5XzXzoVjBYZiakZn7S3hJZg/640?wx_fmt=png&from=appmsg "")  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/niasx7fyic9COlbeeiauXHibmtTNDDhNzSWnj65EibCM6z1aXcklexap5j66icsG3RltElk1QWoKh58K3MKIIX9YQgm5qX13afyQSIzA3ib8NMNCWc/640?wx_fmt=png&from=appmsg "")  
  
扫描概览  
  
显示：  
  
/>  
  
报告数量  
  
  
/>  
  
漏洞总数  
  
  
/>  
  
目标数量  
  
  
/>  
  
目标信息条目数量  
  
  
/>  
  
最近日志数量  
  
  
结果报告  
  
按本地 HTML 报告文件更新时间倒序展示，可点击切换预览。  
  
目标信息  
  
从日志和报告中聚合目标、指纹、标题等信息。  
  
原始报告预览  
  
内嵌显示原始 dddd  
 HTML 报告，便于直接查看详细内容。  
  
扫描进度日志  
  
默认展示最近 120 条日志记录。  
  
8. 常见问题  
  
访问不了页面  
  
检查：  
  
/>  
  
程序是否正常启动  
  
  
/>  
  
端口是否被占用  
  
  
/>  
  
是否使用了正确地址，例如 http://127.0.0.1:18081  
  
  
如果端口冲突，可以换端口：  
  
  
```
.\dddd_viewer.exe -addr 127.0.0.1:19090
```  
  
  
页面没有数据  
  
检查：  
  
/>  
  
当前目录或 -root  
 指定目录下是否存在 *.html  
  
  
/>  
  
是否存在 log.txt  
  
  
/>  
  
HTML 是否为 dddd  
 生成的报告格式  
  
  
登录后刷新失效  
  
当前版本使用内存会话：  
  
/>  
  
程序重启后需要重新登录  
  
  
/>  
  
关闭程序后旧会话失效  
  
  
  
  
**内部CTF课程上线，总课程30+小时，优惠折扣中！**  
  
[](https://mp.weixin.qq.com/s?__biz=MzkzMzE5OTQzMA==&mid=2247490440&idx=1&sn=bc2281a5e016a3d3d662b0d926924e6d&scene=21#wechat_redirect)  
  
****  
**帮会简介**  
  
《  
**安全渗透感知**  
》  
是FreeBuf知识大陆的重量级帮会，帮会致力于漏洞POC/EXP、红队攻防实战，是系统化从基础入门到实战漏洞挖掘的教程社区，包含团队自整的挖掘注意点和案例，还包含分享的渗透经验、SRC漏洞案例、代码审计、挖洞思路等高价值资源。  
  
  
**内容框架（持续新增中）**  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/niasx7fyic9COd1ITgnGXHdVfC79DficTDDlYBibvNAC2VSwy3LDNBdxsgqbx8lUH5uUwjicLYYf1Ee2a8bmKlC8NnvYDtzfmfia7PoC6ytYX05u8/640?wx_fmt=png&from=appmsg "")  
  
****  
**目前已有「700+」小伙伴加入了帮会**  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/niasx7fyic9CN9CVSeMYUIpW50058icjreeNHRMK7jEabMtshNf15j1IHvicDotNG4ZnfrQcwHDroAooj6kMIonH8FWgcu9bUjN7n6aEXyQHrFo/640?wx_fmt=png&from=appmsg "")  
  
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
  
  
  
  

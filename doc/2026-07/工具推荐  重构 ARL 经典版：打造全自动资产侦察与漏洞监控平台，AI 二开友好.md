#  工具推荐 | 重构 ARL 经典版：打造全自动资产侦察与漏洞监控平台，AI 二开友好  
owl234
                    owl234  星落安全团队   2026-07-09 16:00  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/spc4mP9cfo75FXwfFhKxbGU93Z4H0tgt4O9libYH9mKfZdHgvke0CeibvXDtNcdaqamRk3dEEcRQiaWbGiacZ2waVw/640?wx_fmt=other&wxfrom=5&wx_lazy=1&wx_co=1&tp=webp#imgIndex=0 "")  
  
点击上方  
蓝字  
关注我们  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/WN0ZdfFXY80dA2Z4y8cq7zy2dicHmWOIib5sIn8xAxRIzJibo2fwVZ3aicVBM8RnAqRPH5Libr4f02Zs5YnMLBcREnA/640?wx_fmt=other&wxfrom=5&wx_lazy=1&wx_co=1&tp=webp#imgIndex=1 "")  
  
  
现在只对常读和星标的公众号才展示大图推送，建议大家能把  
**星落安全团队**  
“  
**设为星标**  
”，  
否则可能就看不到了啦  
！  
  
【  
声明  
】本文所涉及的技术、思路和工具仅用于安全测试和防御研究，切勿将其用于非法入侵或攻击他人系统以及盈利等目的，一切后果由操作者自行承担！！！  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/g1pTgnczBiaG9I5Mick3dljr6poMNMBjYlIBzYbjYyjR9jbnVicv1r3xibaPUBx3uuedHrUIUpBNtdS3VribSictibCFPIQ0ks8CxFJxfcULOGezrE/640?wx_fmt=other&from=appmsg&wxfrom=5&wx_lazy=1&wx_co=1&watermark=1&tp=webp#imgIndex=2 "")  
  
**工具介绍**  
  
**ARL-Next**  
 是 ARL (资产侦察灯塔) 的现代化重构版本，提供**极简、高效的自动化资产发现与漏洞监控方案**  
。其核心亮点包括：  
- **引擎代际更替**  
：淘汰 PhantomJS，平滑升级为 Chromium + Puppeteer 动态爬虫与最新的 Nuclei 漏洞扫描引擎。  
  
- **多维资产闭环**  
：打通“边界 ➔ 拓扑 ➔ 指纹 ➔ 漏洞”闭环，集成 ICP 与天眼查，支持拉取企业旗下 APP、小程序、公众号及微博等多元资产。  
  
- **部署与极速二开**  
：生产脚本支持环境自检与内核网络调优；开发环境支持代码卷挂载与 Vite 极速热更新，天然适配 AI 辅助开发。  
  
📸 界面预览  
- **全局仪表盘**  
：实时展示系统资源消耗、任务执行队列、多维资产统计与后台运行日志流。  
  
![仪表盘](https://mmbiz.qpic.cn/mmbiz_png/g1pTgnczBiaGicvtp31MdickFZmJbnAyeG9HBcWCMKvvibicd9gIkwGsEdfw2CjYibZwYd5wCqCrf0X4tgOVwv2d6HegpLqwoyg3oteurrxEicqnjM/640?wx_fmt=png&from=appmsg "")  
  
  
- **企业资产查询**  
：支持 ICP 备案与天眼查关联检索，一键同步资产（网站/小程序/APP/公众号/微博）下发扫描任务。  
  
![ICP备案查询](https://mmbiz.qpic.cn/mmbiz_png/g1pTgnczBiaGJvC6xKHYvhiaeUGJUOdOqjRzKmurZkV1CtphRe017ZibOt0tFFGGvAial1lAAz9mkK3YRZxficd5Z4Hg35icaKZ1w5Iy1N5pNNFPU/640?wx_fmt=png&from=appmsg "")  
  
  
- **任务管理**  
：支持扫描任务全生命周期追踪、POC 插件按需自由组合与多维资产拓扑过滤。  
  
![任务新建](https://mmbiz.qpic.cn/mmbiz_png/g1pTgnczBiaFkibwhicYjHLHiag2TlHic7kprCjsJuadCEjtnNMia5teH3dEJCx5QBKNZ0UFPAwbSXyCO0mwZQeI3iakc50c5LvQ5XqC8JmSYiaEWb0/640?wx_fmt=png&from=appmsg "")  
  
  
  
![任务管理](https://mmbiz.qpic.cn/sz_mmbiz_png/g1pTgnczBiaFSfowvmVkJTXIT98Zj8RHROk8GD1oSvYgXbTicUIxMszDqibiabcIOejdJVNeuTyc3cDOXRPeQNCx4YYXlIm52tW9M2qdhCEBKFQ/640?wx_fmt=png&from=appmsg "")  
  
  
- **系统设置**  
：集成 Fofa/天眼查 API 热配置、字典云端热更新、扫描并发调度微调，以及五大告警通道（钉钉/飞书/企微/邮件/Webhook）一键测试。  
  
![系统设置](https://mmbiz.qpic.cn/mmbiz_png/g1pTgnczBiaECgWTag8fC6iaicicrREKTlxosyXicSUeyQhY5PjDDaXia0U0pwRU1B7tViaGWPOhu2I42AEibezZlApD3ImvDO2rvIoQApicPZQIR4Wo/640?wx_fmt=png&from=appmsg "")  
  
  
**相关地址**  
  
**关注微信公众号后台回复“入群”，即可进入星落安全交流群！**  
  
关注微信公众号后台回复“  
20260710  
**”，即可获取项目下载地址！**  
  
****  
  
****  
**圈子介绍**  
  
博主介绍  
：  
  
  
目前工作在某安全公司攻防实验室，一线攻击队选手。自2022-2024年总计参加过30+次省/市级攻防演练，擅长工具开发、免杀、代码审计、信息收集、内网渗透等安全技术。  
  
  
目前已经更新的免杀内容：  
- 部分免杀项目源代码  
  
- 星落安全内部免杀工具箱1.5  
  
- GoCobaltStrike星落专版2.5.1  
  
- 一键击溃windows defender  
  
- 一键击溃火绒进程  
  
-    
CobaltStrike免杀加载器  
  
- 数据库直连工具免杀版  
  
- aspx文件自动上线cobaltbrike  
  
- jsp文件  
自动上线cobaltbrike  
  
- 哥斯拉免杀工具   
XlByPassGodzilla  
  
- 冰蝎免杀工具 XlByPassBehinder  
  
- 冰蝎星落专版 xlbehinder  
  
- 正向代理工具 xleoreg  
  
- 反向代理工具xlfrc  
  
- 内网扫描工具 xlscan  
  
- Todesk/向日葵密码读取工具  
  
- 导出lsass内存工具 xlrls  
  
- 绕过WAF免杀工具 ByPassWAF  
  
- 等等...  
  
  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/DWntM1sE7icZvkNdicBYEs6uicWp0yXACpt25KZIiciaY7ceKVwuzibYLSoup8ib3Aghm4KviaLyknWsYwTHv3euItxyCQ/640?wx_fmt=other&wxfrom=5&wx_lazy=1&wx_co=1&tp=webp#imgIndex=9 "")  
  
  
目前星球已满1100人，价格由208元  
调整为  
218元(  
交个朋友啦  
)，1200名以后涨价至268元。  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/g1pTgnczBiaGJibr1IxLYYcuxAGcjzcIibmSWlU4xOHiaTgsV3SMlZc7Yn7KE1JeCzkar0a6eWM6V2aH9aMTCJm4PtXD54butkwlicRTFdUFMmI4/640?wx_fmt=png&from=appmsg&watermark=1&wxfrom=5&wx_lazy=1&tp=webp#imgIndex=13 "")  
  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/MuoJjD4x9x3siaaGcOb598S56dSGAkNBwpF7IKjfj1vFmfagbF6iaiceKY4RGibdwBzJyeLS59NlowRF39EPwSCbeQ/640?wx_fmt=other&wxfrom=5&wx_lazy=1&wx_co=1&tp=webp#imgIndex=11 "")  
  
     
往期推荐  
     
  
  
1.[加量不加价 | 星落免杀第二期，助你打造专属免杀武器库](https://mp.weixin.qq.com/s?__biz=MzkwNjczOTQwOA==&mid=2247495969&idx=1&sn=d3379e8f69c2cefb6d0564299e13d579&scene=21#wechat_redirect)  
  
  
  
2.[【干货】你不得不学习的内网渗透手法](https://mp.weixin.qq.com/s?__biz=MzkwNjczOTQwOA==&mid=2247489483&idx=1&sn=0cbeb449e56db1ae48abfb924ffd0b43&scene=21#wechat_redirect)  
  
  
  
3.[新增全新Web UI版本，操作与管理全面升级 | GoCobalt Strike 2.0正式发布！](https://mp.weixin.qq.com/s?__biz=MzkwNjczOTQwOA==&mid=2247497899&idx=1&sn=018f02ef4064930cbcb40d6b0495e136&scene=21#wechat_redirect)  
  
  
  
4.[【免杀】原来SQL注入也可以绕过杀软执行shellcode上线CoblatStrike](http://mp.weixin.qq.com/s?__biz=MzkwNjczOTQwOA==&mid=2247489950&idx=1&sn=a54e05e31a2970950ad47800606c80ff&chksm=c0e2b221f7953b37b5d7b1a8e259a440c1ee7127d535b2c24a5c6c2f2e773ac2a4df43a55696&scene=21&token=458856676&lang=zh_CN#wechat_redirect)  
  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/DWntM1sE7icZvkNdicBYEs6uicWp0yXACpt25KZIiciaY7ceKVwuzibYLSoup8ib3Aghm4KviaLyknWsYwTHv3euItxyCQ/640?wx_fmt=other&wxfrom=5&wx_lazy=1&wx_co=1&tp=webp#imgIndex=12 "")  
  
  

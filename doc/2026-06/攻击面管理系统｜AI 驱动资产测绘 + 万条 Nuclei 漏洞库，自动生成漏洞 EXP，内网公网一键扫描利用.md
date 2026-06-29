#  攻击面管理系统｜AI 驱动资产测绘 + 万条 Nuclei 漏洞库，自动生成漏洞 EXP，内网公网一键扫描利用  
killmonday
                    killmonday  渗透安全HackTwo   2026-06-29 16:02  
  
0x01 工具介绍  
  
**Cybersparker 是开源 AI 驱动攻击面管理与漏洞利用平台，基于 Django、React、Celery 构建。支持公网内网双模式资产测绘，接入 Fofa、Hunter 等测绘接口，兼容 fscanx 数据一键入库。内置一万余条 Nuclei 漏洞模板，搭配自研解析引擎，可批量漏洞验证，集成 DNSlog 处理无回显场景。平台搭载多模态 AI，自动解析漏洞资料生成标准化 PoC，提供在线指纹、插件调试功能，Docker 容器一键部署，异步多任务架构支撑大规模扫描，适配安全实战全流程需求。**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ibrevicNauKAWYdnP8rVcnNBOtE5ftzN0iarsOkCF7ww3MjBmT2YnsPG3YwQKqfKZniaJV7wnAtbo4XFJlzwtOMZqUBMBsibz8SNmVRHSDaN0ibib8/640?wx_fmt=png&from=appmsg "")  
  
  
  
  
  
  
  
  
  
  
注意：  
现在只对常读和星标的公众号才展示大图推送，建议大家把  
**渗透安全HackTwo**  
"**设为****星标⭐️**  
"  
**否****则可能就看不到了啦！**  
  
**下载地址在末尾 #渗透安全HackTwo**  
  
0x02   
功能介绍  
  
✨核心能力  
  
### 批量漏洞任务  
  
顾名思义，该任务专门扫漏洞，可以自己选择多个PoC对输入目标进行漏洞验证。插件留空 = 全量PoC扫描，适合已授权的漏洞扫描场景。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ibrevicNauKAUkBV9T0Axp48A3vICIQkc5nLE0VzDE2aonw8SKsMMCxurTepzX4NiaSfViaEt0JDicNtPWX5XC4ZZa698hakZ8bTERKlxrBkJ1V8/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ibrevicNauKAVTrTnDHGgKSL6hayNkOzibgYrxlYMibvicHMun2PSwpDuIvSFfUvhyZMw0ZrBbibB1rpDjAHHrFhz3BibBB2oXFQNnnFrtOicvZppN4/640?wx_fmt=png&from=appmsg "")  
  
PoC选择的方式除了下拉菜单直接选择，还可以根据nuclei的危害等级、标签来筛选：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ibrevicNauKAXiaZ2gogFhxKM7P3tFcc25VQc6ZDBbDsqucUPPR7KHSDktHUqXO1Jtj57pYpibSuXVHZGHXh8UOicSu61fMyo0zpF22TJw9y7SWo/640?wx_fmt=png&from=appmsg "")  
  
点击任务右侧“结果”按钮，可查看该任务的扫描结果，例如：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibrevicNauKAWFPb9GP47dIqArhzt5OdyicScuHmD6o7nqlGy5e2asQRWfHiaTV5AY1boEPUl0ZKTVx1bttGmicxjxOsyVgkjtzI6FgDibiak1poDo/640?wx_fmt=png&from=appmsg "")  
  
点击“验证”，可实时验证该漏洞现在是否依然存在。漏洞存在时，入库的验证结果由PoC插件自身决定，用户也可以自己修改。  
  
批量漏洞任务的结果也会同步“自动扫描任务”测绘的资产，可在检索页面搜索到，存在漏洞的资产会标记红色：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ibrevicNauKAWjnNt6xLtwRDAib5z2Z2N0qO08AtetRpia1D2XSZkJW8gnkbcM9PNweLX53DbRdHVW59VTDAW1dcFEz1BHcMDFTcwvopA9u5fK8/640?wx_fmt=png&from=appmsg "")  
### 自动扫描任务  
  
该类型的任务做两件事情：  
**测绘**  
和  
**漏扫**  
，但它可以在测绘识别到产品后，自动调用产品绑定的PoC插件，因此叫做自动扫描任务。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibrevicNauKAVNNbbPc8iaIiaZtNoUdtSFVAf20QiacJQFALibV6mykFfTEsbjvDiaiaz59Rqme47SSzO5D9sh1FxhvPb0PJBzKzIKChYxKQv09fmuM/640?wx_fmt=png&from=appmsg "")  
  
点击“结果”可以跳转到该任务自己的资产检索页面，仅仅展示该任务扫描到的资产：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibrevicNauKAWMHmMLXrUxoxJSAzFveDHFicXib5l8Mt7dbSlMhj9HybibOx95GL9U7Jt0rVZpwibicqenA6yGiaWiaBI9ThD17AbCtsa6oasXyD51rk/640?wx_fmt=png&from=appmsg "")  
### AI生成PoC  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibrevicNauKAWAmEckO29N2X44lhFqlaawGuzsyIa4EAqosgxOSyQVibWvLjwYYesWntQ8qubfe4UkiaZ4T2HrfibJUXDZRlYm3FXbmsFND5YD5o/640?wx_fmt=png&from=appmsg "")  
  
该任务支持3种生成方式，“URL”、“上传文件”、“直接输入文本”：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibrevicNauKAWlaXojjRnuEn7pg4ZtNOrRnd7k9Qibnh0nxgCOKfvMN7NZ9FGeS1qRamsdPdXCxtOe5yrQh3trIVibVzupicaNm1iaicqQdu9eo6IM/640?wx_fmt=png&from=appmsg "")  
  
爬取处理完成后，点击“执行”可进入生成控制页面，这里只需要操作3步（其他框框是系统自带的，当然你也可以修改，改后鼠标离开编辑框就会自动保存入库，改了系统提示词以后不会影响其他任务，如果需要全局修改系统默认提示词，需要到代码里改）：  
  
![image-20260624153310984](https://mmbiz.qpic.cn/mmbiz_png/ibrevicNauKAWNDAIyrQnHicpyuxPuC81Fr7L5pqg6NhnjmKxhEgaC1NJNmbcNBfgE6E3YGXoTqHzM859nljdYS5CbuDk6b9bQ8f3mQu4xWosE/640?wx_fmt=png&from=appmsg "")  
### fscanx导入结果查看  
  
fscanx的导入要在“自动扫描任务”中导入，导入时选择输入源为“fscanx输出文件”，扫描区域根据实际情况选择“公网”或者“xxx内网”。这个“xxx内网”需要在“扫描区域”页面添加，之所以这么设计，是因为内网测绘和公网不同，公网里的ip是唯一可以标识一个资产的，比如8.8.8.8，而内网里的192.168.1.1却可以在多个不同目标的内网同时存在，因此需要用区域这个概念来区分不同内网。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibrevicNauKAWDZvwQibhPcSC14ia0dI0OIOphUD2g65yEnLaBQnPM8N330vOicmtyKL1yJF4FUTJzzBVR0xUH2FLOiaZo2kAILDj7NAeuUUnjv6Y/640?wx_fmt=png&from=appmsg "")  
  
导入后，点击“结果”就能进入资产检索页面。另一方面，如果是内网测绘，还会有一些特殊的内网信息，如扫描出的弱口令、OS版本、MS17-010漏洞信息、ftp/smb文件清单等，这些信息需要在左侧导航栏的“fscanx导入”页面查看，这些数据不在测绘的资产检索页面展示，所以单独在这里展示：  
  
![image-20260624153618882](https://mmbiz.qpic.cn/sz_mmbiz_png/ibrevicNauKAVsn43Y4YEVX4kNYibRYD6iaiadDlg7hqnAOUNCSpc3INGEbVrStmgWuXQt2iasYwBuEic4mGXbV9GW7EWZ4ic80xQ5m6waRicP22Cia08/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ibrevicNauKAWsB86yWwLRQHChZPnZU3O4g3yEWsnzpmA3BzeNo1p6TpUuhX11TSdoQBzXNtxMoO4cq6WBxzdZMabGqHOBIPNQn6icBIftdnbs/640?wx_fmt=png&from=appmsg "")  
### 目录扫描  
  
比较简单，可以设置字典组和组内字典，输入源是历史的自动扫描任务结果，直接勾选任务，或者用检索语句来筛选你想要的资产作为目标。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ibrevicNauKAWWjsZtvJvoOgWRqr5Oy4xsFaUC8TVlbibbIUC5UqDFaF2CJHfgXupN4mc7SwsNL5ShgB4iajDIulfUR68fVzSKY6oOEELKu8f0Y/640?wx_fmt=png&from=appmsg "")  
  
可以根据长度、状态码等排序，可以检索。另外目录扫描过程识别到的产品会同步更新到自动扫描任务识别的资产，可以在检索平台搜索到。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibrevicNauKAUEsia2ibibbRkMibaMwnFK3rvNOe6lBicAtcVTTE3mhPFS6eKezdtsvxhmBI9TY0NxqKaW5WX2JJiaKzNhIrlwIdBwfBBA1vKfMCAms/640?wx_fmt=png&from=appmsg "")  
### 漏洞结果管理  
  
此处可以查看所有任务汇总的漏洞结果，可以导出csv，可以检索、过滤找到关心的高价值目标，如搜索 gov  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ibrevicNauKAU4SIQbicicuEvc9UzoMmbnzjKTJgvZEqLkvkuFkKTMH6W9wg5t8UbDy8uRKxvnCpXQn1gxpnPGILpz8XylpRJjCdqXqr23omINY/640?wx_fmt=png&from=appmsg "")  
### 指纹调试  
  
指纹编写规则，可参考该页面的提示和现有指纹内容。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ibrevicNauKAUnHgTmaiaCiatX1ktgrfYUW7SO8h8lk44mxHLnNXvdbHSNzNHMbXVjlVdfTxKVjNBIvH9FQlWUng6EKTPX3hmZ2IicgTXBypGP8c/640?wx_fmt=png&from=appmsg "")  
### PoC插件调试  
  
记得先配个ceye dnslog，系统已经hook相关nuclei变量，无回显的nuclei插件将自动使用该dnslog。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibrevicNauKAVCqIA7ewq8QkvywEpnoZ47QfBPF8lRfsDTwbvWTRM4JOY0WBh7G9ghM9ANztTCON30fJCTuKKMpeQzR3GgCRpMiaYwex51zWO8/640?wx_fmt=png&from=appmsg "")  
  
随便挑选一个java反序列化+dnslog验证的poc，测试本地docker靶场验证成功：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibrevicNauKAVpVtapaUd2svokJjmWicFrthhREpHaXicU5K6yaWttIhQ2HvQFQw34YlGUZFASBj5ichIXllHFiaH0paDXkSnqhpJaOxvJkObZPxo/640?wx_fmt=png&from=appmsg "")  
  
注意Nuclei YAML 引擎目前系统只支持   
http/requests  
 和   
tcp/network  
 两类顶层协议块，比较少用到的code协议（仅1.2k个）需要在本机运行第三方代码来验证漏洞，相对危险不可控，不打算支持。  
### AI模型配置  
  
系统支持思考类型的模型（纯文字处理）、识图类型的模型（用来处理图片->结构化文字）。思考类型的模型当前只支持OpenAI协议接口。大部分模型都提供了OpenAI协议接口，例如deepseek，在此我们也期待它的识图能力上线到API（目前仅在网页版）：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ibrevicNauKAWwejWabxZr5KyhtRpphmVSmv1ENT0C73O4sTq2A2OkTmnFT7T7xia9yCQBo3Hb36Dhp0zhWIxyjq8b8g3HqEf6Uiciare8Hic9Vn4/640?wx_fmt=png&from=appmsg "")  
  
识图模型目前仅支持阿里云百炼的api和sdk，所以必须使用qwen系列识图模型，比较便宜好用的是qwen3-vl-flash，百炼有免费额度，我至今没有用完。必须配置的api地址是   
https://dashscope.aliyuncs.com/compatible-mode/v1  
 ：  
### 测绘引擎配置  
  
只有fofa需要邮箱，其他的不需要。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ibrevicNauKAVVn5libXm1pTMljleviayDdtZbicFMxLKsQY4TAA55JnnOg2fHpngFRnYvOzU4qRjH1VOLI9FMOXWY8KSK0WrkicXapeNzJuP8aEs/640?wx_fmt=png&from=appmsg "")  
##   
  
0x03 更新介绍  
```
扫描区域api地址错误问题修复，由于更新了前端代码需要重新build静态文件，所以重新上传docker镜像，后续有空再优化一下docker部署方案，避免重复构建。替换时，需要拉取最新的项目仓库代码（git pull），然后docker load导入镜像，这会自动更新已导入的历史镜像。
```  
  
  
0x04 使用介绍  
  
📦安装与使用指南  
  
docker一键部署：  
- 如果你有网络环境问题，不想自己构建，你可以下载我打包好的docker镜像，导入后直接 docker compose up 就可以运行成功，查看本项目release，或者直接下载   
docker_cybersparker_images.tar  
。  
  
然后执行  
docker load -i docker_cybersparker_images.tar   
 来导入镜像。  
  
- 如果你要自己构建镜像，那么修改docker-compose.yml里的所有代理配置为你自己的代理：  
  
```
      args:
        - HTTP_PROXY=http://192.168.137.120:7890
        - HTTPS_PROXY=http://192.168.137.120:7890
```  
  
 如果你的网络环境不需要代理，去掉args整体，然后把   
build:  
改为  
build: -  
，如果你不会改就让AI改。然后构建：  
```
# 在项目目录下执行
docker compose up 
```  
- 启动后，默认web端口为28600。访问   
http://ip:28600  
  
- 默认登录账号为admin/admin。若线上部署，请立刻修改密码。  
  
****  
  
**0x05 内部VIP星球介绍-V1.5（福利）**  
  
          
如果你想学习更多**渗透测试技术/应急溯源/免杀工具/挖洞SRC赚取漏洞赏金/红队打点等**  
欢迎加入我们**内部星球**  
可获得内部工具字典和享受内部资源和  
内部交流群，  
**每天更新1day/0day漏洞刷分上分****(2026POC更新至8922+)**  
**，**  
包含全网一些**付费扫描****工具及内部原创的Burp自动化漏****洞探测插件/漏扫工具等，AI代审工具，最新挖洞技巧等**  
。shadon/  
Hunter  
/  
0zone  
/  
Zoomeye  
/Quake/  
Fofa高级会员/AI账号  
/CTFShow等各种账号会员共享。详情点击下方链接了解，觉得价格高的师傅后台回复"   
**星球**  
 "有优惠券名额有限先到先得  
**❗️**  
啥都有  
**❗️**  
全网资源  
最新  
最丰富  
**❗️****（🤙截止目前已有2800+多位师傅选择加入❗️早加入早享受）**  
  
****  
最新漏洞情报分享：  
https://t.zsxq.com/DSAvv  
  
****  
  
**👉****点击了解加入-->>内部VIP知识星球福利介绍V1.5版本-1day/0day漏洞库及内部资源更新**  
  
****  
  
  
结尾  
  
# 免责声明  
  
  
# 获取方法  
  
  
**公众号回复20260630获取下载、回复 加群 获取交流群**  
  
****  
  
# 最后必看-免责声明  
  
  
      
文章中的案例或工具仅面向合法授权的企业安全建设行为，如您需要测试内容的可用性，请自行搭建靶机环境，勿用于非法行为。如  
用于其他用途，由使用者承担全部法律及连带责任，与作者和本公众号无关。  
本项目所有收录的poc均为漏洞的理论判断，不存在漏洞利用过程，不会对目标发起真实攻击和漏洞利用。文中所涉及的技术、思路和工具仅供以安全为目的的学习交流使用。  
如您在使用本工具或阅读文章的过程中存在任何非法行为，您需自行承担相应后果，我们将不承担任何法律及连带责任。本工具或文章或来源于网络，若有侵权请联系作者删除，请在24小时内删除，请勿用于商业行为，自行查验是否具有后门，切勿相信软件内的广告！  
  
  
  
# 往期推荐  
  
  
**1.内部VIP知识星球福利介绍V1.5（AI自动化）**  
  
**2.CS4.8-CobaltStrike4.8汉化+插件版**  
  
**3.全新升级BurpSuite2026.4专业(稳定版)**  
  
**4. 最新xray1.9.11高级版下载Windows/Linux**  
  
**5. 最新HCL AppScan Standard**  
  
  
渗透安全HackTwo  
  
微信号：关注公众号获取  
  
后台回复星球加入：  
知识星球  
  
扫码关注 了解更多  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/RjOvISzUFq6qFFAxdkV2tgPPqL76yNTw38UJ9vr5QJQE48ff1I4Gichw7adAcHQx8ePBPmwvouAhs4ArJFVdKkw/640?wx_fmt=png "二维码")  
  
  
  
上一篇文章：  
[Nacos配置文件攻防思路总结|揭秘Nacos被低估的攻击面](https://mp.weixin.qq.com/s?__biz=Mzg3ODE2MjkxMQ==&mid=2247492839&idx=1&sn=b6f091114fbd8e8922153a996c8f4f1c&scene=21#wechat_redirect)  
  
  

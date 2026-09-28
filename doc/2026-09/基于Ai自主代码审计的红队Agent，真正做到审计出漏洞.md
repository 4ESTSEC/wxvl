#  基于Ai自主代码审计的红队Agent，真正做到审计出漏洞  
youki
                    youki  C4安全   2026-09-28 10:41  
  
CODE  
  
重要声明：本文仅用于技术讨论与学习，利用此文所提供的信息而造成的任何直接或者间接的后果及损失，均由使用者本人负责，文章作者及本公众号团队不为此承担任何责任。  
  
QUOTE  
  
Provena --   
一套基于Pi Agent驱动的自主红队AI系统，真正做到无人值守自动完成各种渗透任务！  
  
工具开源地址  
  
CODE  
  
https://github.com/youki992/Provena  
  
∞  
  
THE END  
### 功能  
  
  
运行环境要求，下面是Win的运行要求  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/niasx7fyic9CNIRkFpxxjrFYHHO5AibzhM7N6eKpJdqRdowNMfEXlzXFSs36aZuuVT5SFlxRKCpmibGVIC1j4KBxrAiabiagRjHAicGJQNZdb0I3yQ/640?wx_fmt=png&from=appmsg "")  
  
编辑 config.yaml，只改这几行  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/niasx7fyic9CO60v2txB9kGH0oN9128u2GKaAzOTicLhXhgfpXibRrs3nwK9GwqNkyOlpnFiaAuL9PJSYSliaTBJnAzhiczekMSSIajQCQicLxTuTf0/640?wx_fmt=png&from=appmsg "")  
  
内置工具如下  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/niasx7fyic9CPweVAbDxV29ZliarNvfQSovlnXHdtezOjUXG4kv5lZAaic8xQUBPhiaTJZ5sucQNLHOOucATqZIz8wZJmv6axRqlDDYozm8h6CZg/640?wx_fmt=png&from=appmsg "")  
  
常用命令  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/niasx7fyic9CMtsRW83lxM1kvS9qdyv8DicI6hIWLicT6NNYpgjPC9r45nAvSu3v2SFZYDnyqBKOdzGJ4UPcuGGdmIFvbkL7ZjSOVOiatDo9u4DA/640?wx_fmt=png&from=appmsg "")  
  
run命令的常用参数  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/niasx7fyic9COyxFjlmPEuluC8GW7cYGAODicAibq1ibAKyMGLnibSqw0kx7PibyocRAnhCWTHJSVgjYRezx7PlfaTEHpPZ3Z6SaCIEic5VAq6dyniaM/640?wx_fmt=png&from=appmsg "")  
  
chat命令的常用参数  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/niasx7fyic9COOtPsuzjkd0pqCIEqY6bo3Uqe0p9gbxxcsLDvOV0xRib5eItzNJ7ia9Jn4DwCqtQuE9r6ChYCgVwphHCtl8nTk1vDrKLiaHds9icI/640?wx_fmt=png&from=appmsg "")  
  
chat命令使用，通过cli对话式交互内容  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/niasx7fyic9CM4LsXia7c2dlA6s8BypRnxniaCY4nF51JyT0LPROKWN3aReoh6anwrrK9OGDuRIIiaiaIOWu8b9y8aRNnAz7ZTtUzo5Z1icLc2SZnM/640?wx_fmt=png&from=appmsg "")  
  
对话内容测试，询问内置的工具  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/niasx7fyic9CMy5biaNH4d51o7T5h3plXxOjibrTJeLZicTzJtqOvOMXXLIebvDpRubhDiaDyYo8RP67N5MdWTpib0ibeXnucvUPtoWN3PMyK04u2ks/640?wx_fmt=png&from=appmsg "")  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/niasx7fyic9CMQ4jM60LBTMLz8GnRKKNqJVoE3oI9VsKYX4nhbuE8lAcwTH9hjibUqheEINoFp3h4mJUmGhf1JGEVDiaxvdpiaOWEVsk2Seia2Bdc/640?wx_fmt=png&from=appmsg "")  
  
工具架构  
  
CODE  
  
        ┌──────────────────────┐  
  
        │   模型（Pi harness）  │   只做决策：下一步干什么  
  
        └──────────┬───────────┘  
  
                   │  工具调用  
  
        ┌──────────▼───────────┐  
  
        │      Provena 内核     │   执行 · 采集证据 · 记账 · 出报告  
  
        │  ① 工具执行器 + RBAC  │  
  
        │  ② FGS 图（只追加）    │   ← 唯一的"记忆"  
  
        │  ③ 人机协同（HITL）    │  
  
        │  ④ 报告（md/json/sarif）│  
  
        └──────────────────────┘  
  
模型主要是负责"下一步执行什么"，而Provena 则负责决策"能不能做"和"记录下步骤"。  
  
在设计上面，我是不指望模型可靠的，所以就把可靠性做在架构上：决策过程外置成图、结论必须依靠证据生成、缺乏证据就不算完成目标。  
  
CODE  
  
                    ┌──────────┐  
  
                    │  origin  │  起点  
  
                    └────┬─────┘  
  
                         │ motivates  
  
                    ┌────▼─────┐  
  
                    │   goal   │  本次目标  
  
                    └────┬─────┘  
  
             ┌───────────┼────────────┐  
  
        ┌────▼────┐ ┌────▼────┐  ┌────▼─────┐  
  
        │  step   │ │  step   │  │ sub_goal │ ← 运行时临时拆的子目标  
  
        └────┬────┘ └────┬────┘  └────┬─────┘  
  
             │           │            │  
  
        ┌────▼────┐ ┌────▼─────┐      │  
  
        │  fact   │ │ finding  │◄─────┘  
  
        │ 客观观测 │ │ 待确认线索 │  
  
        └─────────┘ └──────────┘  
  
  节点状态：pending → active → confirmed / completed / blocked / abandoned  
  
  另有 intent / hint 两类节点  
  
以的Java代码审计为例，从jar文件开始审计  
  
CODE  
  
 .jar  
  
    │  jar-list          列条目（不解压）  
  
    ▼  
  
  条目列表  
  
    │  jar-extract       抽目标 .class / 资源文件  
  
    ├──────────────────────────────┐  
  
    ▼                              ▼  
  
  cfr 反编译                    直接 read/grep  
  
  → 可读 Java 源码              （web.xml、.properties、  
  
    │                            mybatis 映射）  
  
    ▼  
  
  joern-parse --language JAVASRC  
  
    │  
  
    ▼  
  
  CPG（代码属性图）  
  
    │  joern-scan（官方查询库已内置，离线可用）  
  
    ▼  
  
  完整 source→sink 路径  →  置信度可升到 dataflow_reachable  
  
内测版本地址  
  
CODE  
  
https://wiki.freebuf.com/societyDetail/articleDetail?society_id=184&article_id=230351  
  
白帽集市链接  
  
如果你没有加入内部社区，也可单独付费购买工具  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/niasx7fyic9CPTibia9g3Q7TNHbm3rgnNjL3ic4icmeNMKeeDiaoFPAB8eGiaTQVOiaP3TAT6cHORYfHXfC5zKoQYEdnOx071NicJYMdLiaT0cEXmr7Tw0/640?wx_fmt=jpeg&from=appmsg "")  
  
历史审计能力和成果参考  
  
[用友U8C nDay手到擒来，Provena-Audit自动审计出洞](https://mp.weixin.qq.com/s?__biz=MzkzMzE5OTQzMA==&mid=2247491031&idx=1&sn=a9575091925efcb630f0b0e7d5c3a4c7&scene=21#wechat_redirect)  
  
  
[用友U8C-CodeSynServlet全版本未授权任意文件下载漏洞分析](https://mp.weixin.qq.com/s?__biz=MzkzMzE5OTQzMA==&mid=2247491044&idx=1&sn=24da2fcacf1950182685eefb769ca41f&scene=21#wechat_redirect)  
  
  
END  
  
我是墨格，专注于让文章更清晰地抵达读者。  
  
如果你觉得今天这篇有收获，欢迎**点赞、在看、转发**  
三连，我们下篇见。  
  
  
  

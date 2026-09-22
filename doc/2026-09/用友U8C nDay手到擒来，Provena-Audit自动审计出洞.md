#  用友U8C nDay手到擒来，Provena-Audit自动审计出洞  
原创 chobits02
                    chobits02  C4安全   2026-09-22 13:31  
  
前情提要  
  
关于Provena-Audit，Provena是我在自己开源的Provena证据驱动的测试Agent上面开发的专门代码审计的Agent，主要的底层Agent还是Pi Agent，基于FGS图驱动完成任务  
  
目前Provena-Audit仅在内部社区中分享，功能还在完善中，将在内部分享使用  
  
  
Provena-Audit功能介绍  
  
内置的代码审计工具：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/niasx7fyic9COxyoZg7FqvjATbdYm7wSSqrKsGYAJHeicMlzwRwWVObV5ZrTXuRhu072zJdaLh5vRdHhg7SyCGk88crP09F3f8vShRQohhgQXI/640?from=appmsg "")  
![]( "")  
  
支持代码审计的源码如下：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/niasx7fyic9CM74eNurFRIvtk6KTCsfhlJmibkUGsdb25O1TfjwicbtRBZOGicmHXEibG74xwBibbgSGNTFl6ZBiaUfVg2KiaPNSuEU4OevhQpN0x4Pg/640?from=appmsg "")  
![]( "")  
  
这里演示一下Java代码的审计能力，接的Ai是  
DeepSeek v4 flash  
  
Java代码审计主要依靠的工具是cfr和Joern，cfr常见的反编译jar的工具，然后Joern是专注于代码安全分析的开源工具，核心理念是将源代码抽象为代码属性图（Code Property Graph, CPG），将语法、语义、控制流、数据流等信息统一建模，从而支持复杂的漏洞分析和跨上下文推理  
  
  
演示案例  
  
这里就拿9月8号用友官网的补丁公告消息，其中有一个用友U8C的漏洞，只有标题，没有说明漏洞位置  
  
![](https://mmbiz.qpic.cn/mmbiz_png/niasx7fyic9CNE5LNm5iaPoNhkvxC0Eia88hLBlKrVYVMoI9TR5JEcBFIJ2tianHiaV7t1TdWJiaEgIkIDSWfToicYTvvYFCAqILhNF3Lh4TODEyw8M/640?from=appmsg "")  
![]( "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/niasx7fyic9CNyvDBjLicLUMI5d2nDtKclOs2QuicyxxrR4od9VZvZwzYnszIgFCicyYIiciaAb0MVsYdcvjvK3n9YDaPbEB4aYrX2AwggXJILpnac/640?from=appmsg "")  
![]( "")  
  
但是只要一个servlet的名字也够了，经常审计用友的师傅应该很  
轻车熟路了  
  
启动Provena-Audit的chat模式，开始对话安排任务：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/niasx7fyic9CPbz9ibTAAjynmu6SNwBayfqZ5ibuJFdHJvianEt4gFSCQ9QQtGvticqiaQ7AjNOpFCGPgs0sVInwh6ianPwCbaicWQV9Yj1StkeKjkia8/640?from=appmsg "")  
![]( "")  
  
Provena-Audit开始审计，思路流是直接打印的，你能看到他思考的每一步，基于FGS限制目标和步骤  
  
![](https://mmbiz.qpic.cn/mmbiz_png/niasx7fyic9CPkmACvwjmQ4p1rLYShGR4QNumOAPLlBcNzTKMib5eUmkeRYXsLtWztWEe7ESmNhlWicqmunBKDctKianLfsDzV6TN9wWagrqDDOA/640?from=appmsg "")  
![]( "")  
  
然后就是开始搜索CodeSyncServlet在哪个源码文件里面  
  
![](https://mmbiz.qpic.cn/mmbiz_png/niasx7fyic9COP4ECKNfa8BjDTxqDobx0ZYVt2ApnLA3HeKhFCe5Ba1Wz75Mmp2SAUK4iaq6hK42fVLsyFxibMdYeZIRb2hJvKPHCWjXn5Aa4ias/640?from=appmsg "")  
![]( "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/niasx7fyic9CPx1lYGCtnU0hNDtzcBATnXUzIAqHjAddJicNrx7Qm2IIND8BUzXYAZV7SzenzNA30Cb7IcCY1zPQFAZnUhmt2H8shdLRYvY3Nc/640?from=appmsg "")  
![]( "")  
  
最后他开始解压jar文件，搜索CodeSync相关的代码  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/niasx7fyic9CPYYtG04qtoOXwCiaaAVImwSPLwhKnZl2bg67iagRA6B4iaQZEBLKBcvoZhosYENh2xBOo4Bd5pnSjG8BC3btHwsgJEcx5p95az7U/640?from=appmsg "")  
![]( "")  
  
找到codesync了，开始狠狠审计  
  
![](https://mmbiz.qpic.cn/mmbiz_png/niasx7fyic9CP5U5MKCR0oyIwrib4WesBYQXNoTFxuRuN3KbaMLrtbbBO6eGY3K7qyst3Ya1w8Y6rQVGeNcUwWFc6IJAN2q1icPvqpcO2FbiavPo/640?from=appmsg "")  
![]( "")  
  
开始分析构造利用链  
  
![](https://mmbiz.qpic.cn/mmbiz_png/niasx7fyic9CP92pPibCmnL0oWqoYZWCx0alJRMK269wiczypftpZmia0datBU9iaDguruzcNw9pvaBEibTnUicB2Q4Z86hvKCYKa4y5QrwMwJ8LP9c/640?from=appmsg "")  
![]( "")  
  
分析完毕提交事实fact  
  
![](https://mmbiz.qpic.cn/mmbiz_png/niasx7fyic9CPUM5RDq2oJsiaVibJVH2f2NIX703w3o5z5DJmWZZhMaUDJxQg0icaoycqOVib8kAGU0wN4fR2qo6RiaPHaQQwf4ibwfknYaqQ5c3bRM/640?from=appmsg "")  
![]( "")  
  
给出利用分析  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/niasx7fyic9CPZqdqgWsj2taKa5pxicP5sz8EK5kQ7QMZ3ialZlnQyHKunzbFslal0Q5OwOlu8QJzGd7xagDibicKTnamtaDjnbJdgExhDdicibj2E4/640?from=appmsg "")  
![]( "")  
  
利用poc的构造思路如下：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/niasx7fyic9CPXMjOQGj3ZRFQjk8cGaxbABkPLZBZLz7aP0WCRSAwsyqo1FsiamtM0xyoHicxdd82tVBy50hJZRfCOia4Gr24AkDsibkw99dCuQ8I/640?from=appmsg "")  
![]( "")  
  
后续该漏洞的利用POC也会分享在内部社区当中  
  
写在最后  
  
整个过程由Agent自己规划，不内置任何skill来限制或者给他参考方案  
  
​  
  
  
内部知识圈目前已有「  
770+  
」师傅加入了内部社区  
  
​  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/niasx7fyic9CMUqIyXa1Zr5tWiagc2uGKbNKKSxJ8LmFBFia1jrlxEMZuWLqg0hWCZrBTlfvW4IcBKibWokIjv04xjrJS8CJGd4icKJVUC5piazwgg/640?wx_fmt=png&from=appmsg "")  
  
内容框架  
（  
持续新增中  
）  
  
内部社区致力于漏洞POC/EXP、红队攻防实战，是系统化从基础入门到实战漏洞挖掘的教程社区，包含团队自整的挖掘注意点和案例，还包含分享的渗透经验、SRC漏洞案例、代码审计、挖洞思路等高价值资源。  
  
  
→ PC端用户可复制此链接到浏览器加入↓↓  
  
https://wiki.freebuf.com/societyDetail?society_id=184  
  
​  
  

#  IOT漏洞复现----RWCTF 6th - Let’s party in the house   
a2ure  看雪学苑   2024-07-26 18:03  
  
##   
##   
  
##   
  
```
```  
  
  
  
最近通过在复现RWCTF的一些题目，本次讲一下很多文章都讲过的RWCTF 6th - Let’s party in the house这个题目，在复现过程中，由于发现很多时候一些参考文章并没有详细的分析如何产生整个漏洞的，这里会对于整个复现过程中所有的思考路径来进行阐述。  
  
  
  
```
```  
  
  
  
由于是官方的题目，因此固件以及仿真脚本都已经  
提供（https://github.com/chaitin/Real-World-CTF-6th-Challenges）  
，只需要点击下载解压就可以获得完整的题目环境，但是在复现仿真成功之后发现，由于有许多报错以及输出的信息，会发现仿真成功也没办法进行命令执行，因此这里利用他自带的telnet进行映射，最终实现获得shell的效果，需要修改一下rcS文件的内容如下。  
  
  
```
```  
  
  
```
```  
  
##   
##   
  
```
```  
  
  
  
上述两个步骤其他文章中均有提到，之后就是对于漏洞分析过程，这里引用z1r0师傅说的，发现这个  
设备（https://www.synology.com/en-global/security/advisory/Synology_SA_23_15）  
在Pwn2Own 2023上被利用，并且  
TeamT5（https://teamt5.org/en/posts/teamt5-pwn2own-contest-experience-sharing-and-vulnerability-demonstration/）  
公开了一些细节，在/lib/libjansson.so.4.7.0中进行json代码解析的时候发生了溢出漏洞，经过对比发现比赛版本正好存在此漏洞。  
  
  
因为libjansson是一个开源的项目，直接在github上面搜索我们就可以找到对应的源码(需要注意的是，这里源码和题目中lib并不是完全相同，题目的libjansson.so.4.7.0再次基础上进行了修改因此造成了漏洞，所以我们看这个源码只是方便逆向)，通过漏洞描述以及开源代码中的内容，我们确定代码的漏洞点就触发在parse_object函数中，ida在形成伪代码过程中并没有识别出parse_object，因此我们可以通过字符串“string or '}' expected”最终定位到这个函数的位置，最终形成漏洞的点就在sscanf这里，他对于overflow1和2并没有检查长度。  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/1UG7KPNHN8GHxf07w1PYL5PTBo9YHekzqIGH4ibNFvbSW3aFib08OkIKp2ic8aQibbEeusu6t1uOsCg2OdRyUbZZyA/640?wx_fmt=png&from=appmsg "")  
  
  
之后就是根据源码找到对应parse_object的实现，方便逆向，这里我们可以利用grep -r "parse_object"进行搜索，可以找到实现的代码就在src目录下的Load.c中，因此为了方便之后查看我们需要在ida中不成lex_t结构体的定义，直接找到他的定义，之后再ida中通过加入结构体的定义，最终可以方便我们阅读代码。  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/1UG7KPNHN8GHxf07w1PYL5PTBo9YHekzHhPttyiaVF8rISJo40fk4G3v28GyoXdtxlIFpukaPAIcXzDqK7ibvWWQ/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/1UG7KPNHN8GHxf07w1PYL5PTBo9YHekzreo0EoMD8lDCqx1WVZM68B8NUrTUnzC7X4G82PAtfOial5eJsfI1Qqw/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/1UG7KPNHN8GHxf07w1PYL5PTBo9YHekzVmVOVQuESicWLFN6ePiceuicdE2Ax9AGu9ltKiaTRXlXfaGfEtL53UibHYQ/640?wx_fmt=png&from=appmsg "")  
  
  
综上目前已经知道了对于漏洞触发的位置，接下来就是要探究如何根据这个漏洞进行利用，首先就是需要关注的，因为我们需要对于overflow1进行溢出，但是如果想要溢出到返回地址，就需要覆盖key,json等值，这样在之后的释放时会出现程序终止，因此这样行不通。  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/1UG7KPNHN8GHxf07w1PYL5PTBo9YHekzPPxBjU1dL1ED21F1ia3jTCV5bDC1GsA9S6uhB1tVaO1uBicexFoGgk8w/640?wx_fmt=png&from=appmsg "")  
  
  
转而我们来关注lex_scan这个函数，对于上面我们获得的结构体信息可以知道，lex_t结构体的第一个成员时stream_t的结构体类型，而stream_t的第一个成员变量又是一个get_func的函数指针，因此，我们可以再load.c源码中找一下，哪里调用了stream->get这个函数，这里可以看到stream_get会调用这个函数。  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/1UG7KPNHN8GHxf07w1PYL5PTBo9YHekzEicjupGHV0RXcPMFTEQo23GJKGAibxRGnGru1wMzmGZtOfZmsTeUfXkg/640?wx_fmt=png&from=appmsg "")  
  
  
之后进而再stream_get查一下，再lex_get和lex_get_save下都有调用。  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/1UG7KPNHN8GHxf07w1PYL5PTBo9YHekzUZFSRDGaJNaoaZpdicJuYR2Yz19Cd1KxD8SVeERXeb3onb7u7acYoNg/640?wx_fmt=png&from=appmsg "")  
  
  
可以发现再lex_scan_string这里有调用lex_get_save函数，到这里就很清晰了，因为再lex_scan中有对于lex_scan_string和lex_scan_number的调用，在漏洞发现出我们也可以发现在执行完scanf之后就可以进入到lex_scan中，因此我们只需要覆盖lex_scan结构体中第一个stream结构体的Get指针，这样我们就可以挟持程序执行流。  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/1UG7KPNHN8GHxf07w1PYL5PTBo9YHekzKZSjQZdPyiahbXD6ZIwcgichvYxG17rReZmZ2baqr9lkFDLCacc05OicQ/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/1UG7KPNHN8GHxf07w1PYL5PTBo9YHekz9dL5ica7FNtm0ss14pjSJT0g7mL9HfCIKKiaZSNZcq1n9Dld9uKBC8Ag/640?wx_fmt=png&from=appmsg "")  
  
  
此外根据源码我们也可以知道parse_object会被parse_value调用，parse_value会被parse_json调用，parse_json又被json_loads调用， 因此我们继续使用grep -r "json_loads"进行查找可以发现，下面这些二进制函数都会调用这个json_loads函数。  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/1UG7KPNHN8GHxf07w1PYL5PTBo9YHekze83Tg9QSYT8o13b7wO6noyy076CPeMQhPlibLLIHhUibuvicrNWXkrscA/640?wx_fmt=png&from=appmsg "")  
  
  
在webd中首先查看handle_main函数，这里面的发现处理函数sub_35CEC来处理对于syno-api的请求。  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/1UG7KPNHN8GHxf07w1PYL5PTBo9YHekz7SxxYpicl1Oh3cyE7qSw8nYP4TEzscElc6tIRKkibw0s7ibq8RjD35dyQ/640?wx_fmt=png&from=appmsg "")  
  
  
之后这里关注一下synocam_param.cgi。  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/1UG7KPNHN8GHxf07w1PYL5PTBo9YHekzib8Cib99kjMBk6fzMJiaQ6tBT4MpUYnB26bvXF6SUPeA1uTvSa7kwfrHg/640?wx_fmt=png&from=appmsg "")  
  
  
在synocam_param.cgi中，的handle_post中，发现传入的数据会进入final_json_load，而final_json_load里会调用json_loads。  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/1UG7KPNHN8GHxf07w1PYL5PTBo9YHekzSlQWprCia7ogtN6icOfAC0ZCEaFCiazUsXiaWB4tGDicibVaYoF19x4Va8RQ/640?wx_fmt=png&from=appmsg "")  
  
  
自此我们就可以知道如果想要触发漏洞，只需要在输入的json格式中，实现对于key的溢出，最终溢出到Lex_t结构体的stream_t的get指针，我们就可以实现对于命令执行的实现。此外需要注意的是，对于命令执行，我们可以利用环境变量来执行，在发送的http请求中加入一个自定义的请求头A，这样在环境变量中就会出现HTTP_A的值为我们传入的值，这样最终执行命令只需要传入$HTTP_A就可以执行命令。  
  
  
  
```
```  
  
  
  
复现过程因为会调用到子进程，因此根据z1r0师傅的步骤，在debug的时候输入一下命令最终就可以实现debug到synocam_param.cgi中，需要注意的是在第一个continue之后需要发送exp脚本，这样才可以执行下去。  
  
  
```
```  
  
  
  
之后我们就需要确定一下具体的偏移，首先根据gdb按照上面的步骤进行debug，可以看到最终执行到bl 0x76fb45f8时，r0指向的地址为0x7efff1f8。  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/1UG7KPNHN8GHxf07w1PYL5PTBo9YHekzsvNanC6mN20zVoV6lXCOa0MTNhPdKMic1dpGjxUwVrgj2GNLo44sFUA/640?wx_fmt=png&from=appmsg "")  
  
  
并且获取stack的地址为0x7efff140，并且overflow1的地址偏移为0x14因此相见可以得到具体的偏移为0xa8，因为我们需要在a8的地方写上gadget，这里为了方便关闭了alsr。  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/1UG7KPNHN8GHxf07w1PYL5PTBo9YHekzefvQqiacnrx5heScibKNpgibMzzZ5tiawkpQjy2oclZTmRkjAs3zdUGicBA/640?wx_fmt=png&from=appmsg "")  
  
  
gadget我们选择0x14d5c这个gadget，这里利用popen来进行命令执行，debug到这个位置我们可以看见r0正好指向的时0x7efff1f0也就是get指针前的位置，因此我们在这里加入$HTTP_A环境变量来实现命令执行。  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/1UG7KPNHN8GHxf07w1PYL5PTBo9YHekz2ribOJaZuuRwBx6eVicjFS0h5tUKzHdDDZpFhYw4zwT4tyhdRf7yxOIg/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/1UG7KPNHN8GHxf07w1PYL5PTBo9YHekzohbiay0CXibjFjZqGpeXbLyCLjbYicRnpfumwdwow6eciaDOT74Sa1YRHg/640?wx_fmt=png&from=appmsg "")  
  
  
最终执行成功之后我们就会发现/www/index.html就换成flag中的内容了（需要注意题目中没有flag文件自己创建一个）。  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/1UG7KPNHN8GHxf07w1PYL5PTBo9YHekzibiaY4vF6g23YydRY5Xoq0Sw2qb81yrwObJTbOE81aMY9TQVpb4yySpg/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/1UG7KPNHN8GHxf07w1PYL5PTBo9YHekzj5ehUicRXZcpHmJw4WFUycG1v6Y5Uia7YbiaibV2KMFhOn5xwrPFrE9VPQ/640?wx_fmt=png&from=appmsg "")  
##   
##   
  
```
```  
  
  
  
自此就完成了整个漏洞的复现以及原理分析的步骤，通过这个题目，可以锻炼我们阅读代码，逆向以及一些技巧性的东西，更好的掌握如何面对真实世界漏洞的原理解析整个过程，之后也会加强对于这种类型题目的学习，在做题中发现有些版本的gdbserver会有报错，因此提供一下后面发现不报错版本的gdbserver文件。  
  
  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/1UG7KPNHN8GHxf07w1PYL5PTBo9YHekzNp93nVONWOUglFp0I0yYCZ8DiaLdfMTRJibaMDjzPLpHlsAOIMlKWs2Q/640?wx_fmt=png&from=appmsg "")  
  
  
**看雪ID：a2ure**  
  
https://bbs.kanxue.com/user-home-991890.htm  
  
*本文为看雪论坛优秀文章，由 a2ure 原创，转载请注明来自看雪社区  
  
  
[](http://mp.weixin.qq.com/s?__biz=MjM5NTc2MDYxMw==&mid=2458563721&idx=1&sn=e2cc8158623ee93a36640990531d4b1a&chksm=b18d840386fa0d15cf8cb0fbf51100ab9e1c54ed453f329470eb54243f1db836d56a8fae7809&scene=21#wechat_redirect)  
  
  
**# 往期推荐**  
  
1、[Alt-Tab Terminator注册算法逆向](http://mp.weixin.qq.com/s?__biz=MjM5NTc2MDYxMw==&mid=2458563181&idx=1&sn=1dd42dbb95362d9678431b94473ab1f4&chksm=b18d82e786fa0bf1681d92f0d13b064eeae9530e5b2e86f78fd7f4d2662bfaffe5e99993d08f&scene=21#wechat_redirect)  
  
  
2、[恶意木马历险记](http://mp.weixin.qq.com/s?__biz=MjM5NTc2MDYxMw==&mid=2458562562&idx=2&sn=1d66bb3141b820c717f86d349660e9ec&chksm=b18d808886fa099efc353521af0839c9bf5fbd4cae2985cf3a9a6ea28fa617f8300c0ab115a7&scene=21#wechat_redirect)  
  
  
3、[VMP源码分析：反调试与绕过方法](http://mp.weixin.qq.com/s?__biz=MjM5NTc2MDYxMw==&mid=2458562488&idx=2&sn=fe5bd1498948137775db5f454bd5a6a2&chksm=b18d9f3286fa162491072b9cd141784c1a60b2b00fd8203f865c51ef753e3f45573a78810949&scene=21#wechat_redirect)  
  
  
4、[Chrome V8 issue 1486342浅析](http://mp.weixin.qq.com/s?__biz=MjM5NTc2MDYxMw==&mid=2458562487&idx=2&sn=b2d6ad2776d37f416933e1439f244430&chksm=b18d9f3d86fa162b5edfd1c8e616c9ea5460cf21afc5d41cfd8122fbc73830c61f125c8a4960&scene=21#wechat_redirect)  
  
  
5、[Cython逆向-语言特性分析](http://mp.weixin.qq.com/s?__biz=MjM5NTc2MDYxMw==&mid=2458562186&idx=1&sn=bd95ad7951c93578ed5f0cbb1971332c&chksm=b18d9e0086fa1716382e78c54135a0a296fa215688b9a741576d41897729625284287e598faf&scene=21#wechat_redirect)  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/Uia4617poZXP96fGaMPXib13V1bJ52yHq9ycD9Zv3WhiaRb2rKV6wghrNa4VyFR2wibBVNfZt3M5IuUiauQGHvxhQrA/640?wx_fmt=other&wxfrom=5&wx_lazy=1&wx_co=1&tp=webp "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/1UG7KPNHN8EIHBaXGq9BqBH3zkpkY5JictMw5qOWdKJhoy2h6vDbHicpbGhAvHHesmGaRdVuyCo7GQiaoW9R6iaYBw/640?wx_fmt=gif&from=appmsg "")  
  
**球分享**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/1UG7KPNHN8EIHBaXGq9BqBH3zkpkY5JictMw5qOWdKJhoy2h6vDbHicpbGhAvHHesmGaRdVuyCo7GQiaoW9R6iaYBw/640?wx_fmt=gif&from=appmsg "")  
  
**球点赞**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/1UG7KPNHN8EIHBaXGq9BqBH3zkpkY5JictMw5qOWdKJhoy2h6vDbHicpbGhAvHHesmGaRdVuyCo7GQiaoW9R6iaYBw/640?wx_fmt=gif&from=appmsg "")  
  
**球在看**  
  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/1UG7KPNHN8EIHBaXGq9BqBH3zkpkY5JicEiaq58E76UwjDCQR0oCmO9AUnStS42W5ZgDfVoSj6seRBIBLu6U7tNw/640?wx_fmt=gif&from=appmsg "")  
  
点击阅读原文查看更多  
  

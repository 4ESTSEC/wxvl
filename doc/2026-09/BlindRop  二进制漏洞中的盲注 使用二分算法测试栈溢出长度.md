#  BlindRop | 二进制漏洞中的盲注 使用二分算法测试栈溢出长度  
zkaq-君叹
                    zkaq-君叹  掌控安全EDU   2026-09-20 04:13  
  
扫码领资料  
  
获网安教程  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/BwqHlJ29vcrpvQG1VKMy1AQ1oVvUSeZYhLRYCeiaa3KSFkibg5xRjLlkwfIe7loMVfGuINInDQTVa4BibicW0iaTsKw/640?wx_fmt=other&from=appmsg&wxfrom=5&wx_lazy=1&wx_co=1&tp=webp#imgIndex=0 "")  
  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/b96CibCt70iaaJcib7FH02wTKvoHALAMw4fchVnBLMw4kTQ7B9oUy0RGfiacu34QEZgDpfia0sVmWrHcDZCV1Na5wDQ/640?wx_fmt=other&wxfrom=5&wx_lazy=1&wx_co=1&tp=webp#imgIndex=1 "")  
  
  
# 本文由掌控安全学院 -  君叹 投稿  
  
**来****Track安全社区投稿~**  
  
**千元稿费！还有保底奖励~（  https://bbs.zkaq.cn   ）**  
  
## Blind ROP  
#### 基本介绍  
  
BROP(Blind ROP) 于 2014 年由 Standford 的 Andrea Bittau 提出，其相关研究成果发表在 Oakland 2014，其论文题目是 Hacking Blind。  
  
BROP 是没有对应应用程序的源代码或者二进制文件下，对程序进行攻击，劫持程序的执行流。  
  
攻击条件  
```
源程序必须存在栈溢出漏洞，以便于攻击者可以控制程序流程。服务器端的进程在崩溃之后会重新启动，并且重新启动的进程的地址与先前的地址一样（这也就是说即使程序有 ASLR 保护，但是其只是在程序最初启动的时候有效果）。目前 nginx, MySQL, Apache, OpenSSH 等服务器应用都是符合这种特性的。
```  
  
以上来源于  
ctf-wiki  
  
通常，我们在测试栈溢出漏洞的时候，我们需要知道缓冲区长度，也就是缓冲区到栈上返回地址的距离。  
#### 测试  
  
用ctfshow上的一道例题进行演示  
  
如下图，我们输入 abcd ，四个字节  
  
程序返回 No passwd,See you!  
  
通过回显可以判断程序正常运行了  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ianpxKPnLHoLzFm3yYr5rpkwHZwr9dVY740uibIXwjIrXwbGdeg0aayFc0G9Bcol6uwj1ic9oZYAV6ibkxPCs3eaYDUCscEQYdzUmLeOz7NcvhM/640?wx_fmt=png&from=appmsg "")  
  
  
这时候再输入一个很长的数据，例如100个a  
  
我们可以看到，程序输出了 timeout  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ianpxKPnLHoL9KkFCpyTLUeTdnTZv57uUA2mUXhZN6ibBSsBVRLYmic5fWArduC1RnHWxU0qvV2iciba5gKeLyjxC7jPYqZC37ic3ic44wNic6CRxCs/640?wx_fmt=png&from=appmsg "")  
  
  
由此判断这里程序发生了错误  
  
以此猜测，程序发生了栈溢出漏洞  
### 栈溢出  
  
这里也浅浅的介绍一下栈溢出  
  
有C语言代码如下  
```
#include <stdio.h>int main() {    char buf[30];    read(0, buf, 0x30);    return 0;}
```  
  
程序的返回地址(即这个函数执行完了之后，要去执行哪个函数)是布在栈上的  
  
buf也是布置在栈上的  
  
上面的c语言代码中使用 read() 函数从标准输入中读取 0x30(48)个字节存储到buf变量，但是分配给buf的空间只有 30 个字节，还有 18 个字节（如果我们输入了的话）  
  
会被存储到buf后面的空间里，倘若 返回地址 的位置，刚好在 buf 后面，我们就能控制返回地址，从而控制程序的执行流程  
  
举个例子， main 函数的返回地址是 exit,也就是结束进程的函数，倘若我们把exit修改为 system(‘/bin/sh’), 就获得了目标机器执行这个程序用户的shell。  
  
题外话就说到这里，接下来开始文章的主题  
### 一般测试  
  
一般情况下，在猜测目标程序存在栈溢出漏洞后，我们会写一个这样的脚本  
  
去测试栈长度  
  
学过算法的朋友应该能看的出来，下面这个程序的算法复杂度是O(n)  
  
即程序有多少数据，就要运行多少次循环  
```
# -*- coding: utf-8 -*-# @Time     : 2023/12/27 23:49# @Author   : 君叹# @File     : cs2.pyfrom pwn import *buf_lenth = 1while True:    try:        io = remote("pwn.challenge.ctf.show", None)        log.info(f"test: {buf_lenth}")        payload = b'a' * buf_lenth        io.sendafter("Welcome to CTFshow-PWN ! Do you know who is daniu?\n", payload)        if io.recv().startswith(b"No passwd,See you!"):            buf_lenth += 1        else:            log.success(f"buf length: {buf_lenth}")            io.close()            break        io.close()    except:        pass
```  
  
像是本题中，缓冲区到返回地址的距离是72  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ianpxKPnLHoLYBL63ecREwRdMT4gfFNr8icgoiaqqzqNmicjXx8u5C7buCXmnQbSt6wzLsKXBj01mr8QlXbFNDX0ic975CYHdqdFia8aHcemZIjt4/640?wx_fmt=png&from=appmsg "")  
  
  
（为什么不是73，因为程序发送了73个a程序报错，说明第73个a覆盖了原本返回地址的第一个字节，导致程序报错）  
  
跑72次，不管是测试还是什么，需要的时间久，有时候服务器响应慢，要的时间就更久了  
### 二分算法实现缓冲区长度测试函数  
  
这里二分算法的主要逻辑分为两个部分  
  
1 确定范围  
  
2 得到数字  
  
确定范围，我们可以从 1 开始，每次乘以2  
  
代码依次发送如下payload  
  
b’a’  1b’a’   
 2  
  
b’a’  4b’a’   
 8  
  
b’a’  16……b’a’   
 128  
  
到了128，确定目标数的范围是 64-128  
  
然后开始使用常规的二分算法进行查找  
  
如果程序返回 No passwd 就说明程序正常运行了，小于等于目标值，右移左指针  
  
没有返回，说明没有正常运行，大于目标值，左移右指针  
```
# -*- coding: utf-8 -*-# @Time     : 2023/12/13 21:49# @Author   : 君叹# @File     : getLength.pyfrom pwn import *# 获取栈溢出长度def dichotomy(fun):    num = 1    jici = 0    while fun(num):        jici += 1        num *= 2 # 确定范围    min = num / 2    max = num    c = (max + min) // 2    # print(max,min)    # print("c -> ",c)    while min <= max:        jici += 1        mid = (min + max) // 2        if max - min == 1:            log.success(f"共进行了 {jici} 次链接\n栈长度为: {mid}")            return min        if fun(mid): # 返回true，成立，那就是没到位            min = mid        else:            max = mid    log.success(f"共进行了 {jici} 次链接\n栈长度为: {mid}")    return middef getStackLength(addr, port):    # 使用二分法快速寻找到 ebp-buf 的值    def is_True(num):        try:            io = remote(addr, port)            io.sendafter("Welcome to CTFshow-PWN ! Do you know who is daniu?\n", 'a' * int(num))            data = io.recv()            io.close()            if not data.startswith(b"No passwd"):                return False            return True        except EOFError:            io.close()            return False    return dichotomy(is_True)if __name__ == '__main__':    addr = None    port = None    len = getStackLength(addr, port)    print(len)
```  
  
运行结果  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ianpxKPnLHoIFSokgRGEwuwJVet0pCwx1tuXgmt7NZo7dA3QLJq02pANoXTzh93u0Njw5Q5Xqo6whjD80EtWqHaswTNSAEbNv88bHGxqWv98/640?wx_fmt=png&from=appmsg "")  
  
  
针对本题  
  
共计14次链接，只用了原本不到20%的时间  
  
当缓冲区空间越大，这个增幅也会越明显  
  
  
  
申明：本公众号所分享内容仅用于网络安全技术讨论，切勿用于违法途径，  
  
所有渗透都需获取授权，违者后果自行承担，与本号及作者无关，请谨记守法.  
  
![图片](https://mmbiz.qpic.cn/mmbiz_gif/BwqHlJ29vcqJvF3Qicdr3GR5xnNYic4wHWaCD3pqD9SSJ3YMhuahjm3anU6mlEJaepA8qOwm3C4GVIETQZT6uHGQ/640?wx_fmt=gif&wxfrom=5&wx_lazy=1&tp=webp#imgIndex=34 "")  
  
**没看够~？欢迎关注！**  
  
  
**分享本文到朋友圈，可以凭截图找老师领取**  
  
上千**教程+工具+交流群+靶场账号**  
哦  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/BwqHlJ29vcrpvQG1VKMy1AQ1oVvUSeZYhLRYCeiaa3KSFkibg5xRjLlkwfIe7loMVfGuINInDQTVa4BibicW0iaTsKw/640?wx_fmt=other&from=appmsg&wxfrom=5&wx_lazy=1&wx_co=1&tp=webp#imgIndex=35 "")  
  
******分享后扫码加我！**  
  
**回顾往期内容**  
  
[网络安全人员必考的几本证书！](http://mp.weixin.qq.com/s?__biz=MzUyODkwNDIyMg==&mid=2247520349&idx=1&sn=41b1bcd357e4178ba478e164ae531626&chksm=fa6be92ccd1c603af2d9100348600db5ed5a2284e82fd2b370e00b1138731b3cac5f83a3a542&scene=21#wechat_redirect)  
  
  
[文库｜内网神器cs4.0使用说明书](http://mp.weixin.qq.com/s?__biz=MzUyODkwNDIyMg==&mid=2247519540&idx=1&sn=e8246a12895a32b4fc2909a0874faac2&chksm=fa6bf445cd1c7d53a207200289fe15a8518cd1eb0cc18535222ea01ac51c3e22706f63f20251&scene=21#wechat_redirect)  
  
  
[重生HW之感谢客服小姐姐带我进入内网遨游](https://mp.weixin.qq.com/s?__biz=MzUyODkwNDIyMg==&mid=2247549901&idx=1&sn=f7c9c17858ce86edf5679149cce9ae9a&scene=21#wechat_redirect)  
  
  
[手把手教你CNVD漏洞挖掘 + 资产收集](https://mp.weixin.qq.com/s?__biz=MzUyODkwNDIyMg==&mid=2247542576&idx=1&sn=d9f419d7a632390d52591ec0a5f4ba01&token=74838194&lang=zh_CN&scene=21#wechat_redirect)  
  
  
[【精选】SRC快速入门+上分小秘籍+实战指南](http://mp.weixin.qq.com/s?__biz=MzUyODkwNDIyMg==&mid=2247512593&idx=1&sn=24c8e51745added4f81aa1e337fc8a1a&chksm=fa6bcb60cd1c4276d9d21ebaa7cb4c0c8c562e54fe8742c87e62343c00a1283c9eb3ea1c67dc&scene=21#wechat_redirect)  
  
##     代理池工具撰写 | 只有无尽的跳转，没有封禁的IP！  
  
![图片](https://mmbiz.qpic.cn/mmbiz_gif/BwqHlJ29vcqJvF3Qicdr3GR5xnNYic4wHWaCD3pqD9SSJ3YMhuahjm3anU6mlEJaepA8qOwm3C4GVIETQZT6uHGQ/640?wx_fmt=gif&wxfrom=5&wx_lazy=1&tp=webp#imgIndex=36 "")  
  
点赞+在看支持一下吧~感谢看官老爷~   
  
你的点赞是我更新的动力  
  
  
  

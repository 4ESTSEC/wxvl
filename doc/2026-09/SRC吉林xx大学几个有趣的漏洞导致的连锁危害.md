#  SRC吉林xx大学几个有趣的漏洞导致的连锁危害  
zkaq-zbs
                    zkaq-zbs  掌控安全EDU   2026-09-23 04:15  
  
扫码领资料  
  
获网安教程  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/BwqHlJ29vcrpvQG1VKMy1AQ1oVvUSeZYhLRYCeiaa3KSFkibg5xRjLlkwfIe7loMVfGuINInDQTVa4BibicW0iaTsKw/640?wx_fmt=other&from=appmsg&wxfrom=5&wx_lazy=1&wx_co=1&tp=webp#imgIndex=0 "")  
  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/b96CibCt70iaaJcib7FH02wTKvoHALAMw4fchVnBLMw4kTQ7B9oUy0RGfiacu34QEZgDpfia0sVmWrHcDZCV1Na5wDQ/640?wx_fmt=other&wxfrom=5&wx_lazy=1&wx_co=1&tp=webp#imgIndex=1 "")  
  
  
# 本文由掌控安全学院 -  zbs 投稿  
  
**来****Track安全社区投稿~**  
  
**千元稿费！还有保底奖励~（  https://bbs.zkaq.cn   ）**  
  
#### 前引：总的来说就是信息泄露+越权+爆破登录+xss这四个漏洞导致的连锁反应，因为他有默认密码123456，所以最终可以重置全校学生的密码，并都植入xss，危害十足。  
  
信息收集：和嘉名童鞋一起测试的，都是他收集的QAQ 主要通过百度贴吧搜集到了默认密码和学号信息，实在是牛皮  
## 漏洞复现：漏洞复现：  
#### 漏洞一：某接口没有对权限进行限制导致信息泄露  
  
登录后访问/stu/m/member/list  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ianpxKPnLHoLN6EAbUUaIUsRDqIHkvXuaydaZmdXiaYBJntVb7DDYJgnJicf1HHd3WsXPVicp9icP5uJvFuzmq9yEemZ3u0ODaAy6icw1KW0kJyo4/640?wx_fmt=png&from=appmsg "")  
  
抓取数据包：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ianpxKPnLHoKUEr1OR29d21p3H8Izwiaayx0YwDOkrGslwoj2QlgCQ3MRMoVV9JUgP1KEC2l7uLlS2ibgo5easwm0HLTFia1vFiaRMibLjAiaKZgW8/640?wx_fmt=png&from=appmsg "")  
  
可以看到其他同班同学的个人资料，有学号、电话、邮箱，并且因为默认密码为123456，所以获取其他人的学号后可以登录很多的账号；  
  
重要的是，该接口泄露了userid！这在接下来的漏洞二中有着很严重的危害：  
  
**第一个接口泄露了同班同学的userid，第二个接口可以通过userid重置密码。第三个漏洞没防爆破，有默认密码可以爆破学号登录很多账号，导致可以重置学校大部分用户的密码**  
#### 漏洞二：任意用户密码重置123456  
  
危害：只需要登录任一账号获取学生权限，便可以将任意用户的密码重置为默认密码123456：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ianpxKPnLHoKcf4QRSt8aOQN03X0E0SsM9l2yuedJLicwopsTiaT3f8xUAbxEjHZogRsgP6OAbmN67S7ibh0JMjqROYkHVtOTZHQT24K6VCUY1M/640?wx_fmt=png&from=appmsg "")  
  
POC：  
  
POST /stu/m/member/resetPassword HTTP/1.1  
  
Host: xxxx  
  
Cookie: 【登录任意账号的cookie即可】  
  
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:109.0) Gecko/20100101 Firefox/116.0  
  
Accept: application/json, text/javascript, /; q=0.01  
  
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2  
  
Accept-Encoding: gzip, deflate  
  
Content-Type: application/x-www-form-urlencoded; charset=UTF-8  
  
X-Requested-With: XMLHttpRequest  
  
Content-Length: 14  
  
Origin: xxxxx  
  
Referer: xxxxx  
  
Sec-Fetch-Dest: empty  
  
Sec-Fetch-Mode: cors  
  
Sec-Fetch-Site: same-origin  
  
Te: trailers  
  
Connection: close  
  
userId=4841276  
  
**漏洞复现**  
：先访问/stu/m/member/resetPassword后抓包  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ianpxKPnLHoLyVkpl4Kjl63cQKicfqOcU7fYszZ7sz4lr4FEiaouxjf2U2OJejSUSDQWeKIMy8Le2j0XFH5rIKlPe1BRcfWDibZ3TLfyp1PuADU/640?wx_fmt=png&from=appmsg "")  
  
改为post包，加一个参数userId=4841276【userId在漏洞一未授权访问里可以看到，我重置了我测试的账号】  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ianpxKPnLHoIw4buEuiaXic4vlqRgULUDR0Ok9DfvUU9LH8ep9bFL1BdJPkMm6jSAy307ywiayVfccJib5Ll8grpsAweq9w8y8ZkYjnXbV5bHW5g/640?wx_fmt=png&from=appmsg "")  
  
重置成功，恢复为默认密码123456  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ianpxKPnLHoJL0orUKWBUY9nVS8MbRo48VsNe3H6WJQGEbWWQNHjECcJJpPUAWzNHQadtZgyC3jPibJrc5rYF27ytxNDy2FEiaib3CeWc6jouuA/640?wx_fmt=png&from=appmsg "")  
  
userid在漏洞一的未授权访问里获取：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ianpxKPnLHoJiar3tlyKS0ee2UX3LXcf4VB7enKun9DO7YiaqzFzSgmLptic7fRNVzJokfD22GrME060mnoXK2XGpIyOcRNeK4QyibZoJnQ2iahib8/640?wx_fmt=png&from=appmsg "")  
  
**比如我们想登录这位07116224的同学账号，未授权访问获取userId=4841566**  
  
登录随意一个账号后，构造如下数据包发送  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ianpxKPnLHoKEPqB8YicBNK280zHGjcM5QLNPa4KjV8GLWAx77Bt3DJ2qfZDkkjmwTF3NdkJJWCj1gc4ys7UeSJeW2DYZmIRL4j2rUmq1N99A/640?wx_fmt=png&from=appmsg "")  
  
登陆账号为xxxx+学号，默认密码123456  
  
=  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ianpxKPnLHoKAC1dIlWjX1Z3FrLDfK12tzRkNCJkTlHx0aic6zbEV3UOx8G1jeceiavHC24pR3Fe3R0FWP61iadC5F0B4fic1YFmth2ttfSvJazo/640?wx_fmt=png&from=appmsg "")  
  
  
登陆成功  
#### 漏洞三：登录处无验证码，因为默认密码为123456，所以可爆破用户名漏洞  
  
利用登陆账号为xxxx+学号，默认密码123456，爆破学号  
  
抓取登录数据包：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ianpxKPnLHoL3H7ClpUauKV5ic6kw9MO3eObb9Imic5QMr2Prj5jc088uo6DA9D9Z2TmqWNWekBgZ5XJFABNRWUnk7fmicOEHkZHs4Ep6iayjTmk/640?wx_fmt=png&from=appmsg "")  
  
我这里就爆破后四位数字：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ianpxKPnLHoKIGNOoWM9icUcibEiaX7PwFexnpob1lXWpIq3zodWF09ssz8vG8vjoLasRysTicnVokQxk1JaHcGbg24QS0a4ia2ejibJ4AZQGvmzwM/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/ianpxKPnLHoKiaaHkUfdmMDQBV3NwWeQEPqG5rnsnLT2Aia3vaGPIRiaAK3W42LcOSZMQxVdlghEQn0CTM88peX5dyIibCm6fMYk3oNXmmU4Yd7I/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ianpxKPnLHoLwAWibRRrcicHNDr7qHV4ZSUzGAof093QdrYHfoSIib3hVShp2EcegtKHHc20nibBQ5Fd04YuSnPDicf5xQO5NTesbqHtjnK2cBLS0/640?wx_fmt=png&from=appmsg "")  
  
  
随便测四位数都登陆成功了几百个账号，如果从八位数学号都爆破，估计涉及 成千上万账号；  
  
而且结合漏洞一和漏洞二，可以重置全校的账号为默认密码123456，你说危害大不大  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ianpxKPnLHoIg57XuW6hia2xacL7KpibUcqzt7IRasz86iahCqLJmia1M1d1nDEnNxIbecyNicYINVX3INJCqSDrib7ibECpDKOg0ZicvsWOnsuFzct8/640?wx_fmt=png&from=appmsg "")  
  
登陆账号涉及支付功能  
#### 漏洞四：存储型xss  
  
个人资料上传照片，抓取数据包：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ianpxKPnLHoKCdt3iaQNsSOyE5H3tL4sX7mgTvb38hyHO3RbicYqxfPOianPiav0we9CMSicy2YdmdZpcWPCSfibPCuhico21hMc5o2v8vn3z9xUeqw/640?wx_fmt=png&from=appmsg "")  
  
图片路径更新这条数据包—可以更换图片路径参数  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/ianpxKPnLHoKIgaBibASOE9RhT1ujmJpw6Ga1swHwK7Rsn0UHKA5pN2Xjibxhn7GWgpUmySE5nykicr0gaARaAzfEpTO66wFkZibxzygO3ubcvKE/640?wx_fmt=png&from=appmsg "")  
  
**漏洞1-3可登录学校百分之八九十的账户，漏洞4可导致同学账户被上存储型xss…**  
  
  
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
  
  

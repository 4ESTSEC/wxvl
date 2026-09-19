#  众测复盘：从 APP 一条普通图片请求，挖掘外围服务器文件上传漏洞  
w4nk3r
                    w4nk3r  神农Sec   2026-09-19 01:00  
  
  课程培训  
  
  扫码咨询  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b7iaH1LtiaKWXLicr9MthUBGib1nvDibDT4r6iaK4cQvn56iako5nUwJ9MGiaXFdhNMurGdFLqbD9Rs3QxGrHTAsWKmc1w/640?wx_fmt=jpeg&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/b96CibCt70iaaJcib7FH02wTKvoHALAMw4fchVnBLMw4kTQ7B9oUy0RGfiacu34QEZgDpfia0sVmWrHcDZCV1Na5wDQ/640?wx_fmt=png&wxfrom=13&wx_lazy=1&wx_co=1&tp=wxpic "")  
  
  
#   
  
专注于SRC漏洞挖掘、红蓝对抗、渗透测试、代码审计JS逆向，CNVD和EDUSRC漏洞挖掘，以及工具分享、前沿信息分享、POC、EXP分享。不定期分享各种好玩的项目及好用的工具，欢迎关注。加内部圈子，文末有彩蛋（课程培训限时优惠）。  
#   
  
文章作者：  
w4nk3r  
  
文章来源：  
https://xz.aliyun.com/news/92800  
  
01  
  
0x1 众测复盘：从 APP 一条普通图片请求，挖掘外围服务器文件上传漏洞  
  
## 0x00 前言  
  
这次众测一开始其实并不顺利。  
  
按照正常的测试流程，我先从目标暴露在公网的 Web 资产入手，对主要业务系统进行了信息收集和常规漏洞探测。  
  
但这一轮下来，几个主要 Web 应用的防护都比较完善，并没有发现特别有价值的突破口。  
  
眼看着时间一点点过去，我开始考虑换一个方向。  
  
**Web 面没有明显突破口，并不意味着目标没有问题，也可能只是当前看到的攻击面还不够完整。**  
  
既然如此，不如换一个观察角度  
  
于是，我把测试重点转向了目标的 APP  
  
而这次真正让我找到突破口的，并不是什么复杂的漏洞利用，而是一条看起来非常普通的图片资源请求  
## 0x01 从 Web 面转向 APP  
  
在实际众测过程中，我比较习惯把目标理解成“一组业务资产”，而不是单独的一个域名。  
  
一个目标除了主站 Web 应用之外，往往还可能存在：  
- APP  
  
- 小程序  
  
- H5  
  
- API  
  
- 图片/文件服务  
  
- 对象存储  
  
- 后台管理系统  
  
- 第三方业务系统  
  
- 历史遗留服务  
  
这些系统虽然表面上属于同一个业务体系，但背后的服务器、域名、开发团队，甚至安全策略，都可能完全不同。  
  
因此，当 Web 主站没有明显突破口时，与其继续盯着同一个入口反复测试，不如换一个观察角度。  
  
这也是我这次转向 APP 流量分析的原因。  
## 0x02 APP 流量分析  
  
这次测试使用的是华为鸿蒙手机。  
  
由于设备无法 Root，我没有继续折腾模拟器，而是直接使用 Yakit 作为中间代理，通过手机 Wi-Fi 代理将 APP 的 HTTP/HTTPS 流量转发到测试机。  
  
Yakit 监听地址设置为：  
```
0.0.0.0

```  
  
随后在手机 Wi-Fi 中配置对应的代理地址和端口。  
  
配置完成后，打开 APP 并进行正常操作，就可以在 Yakit 的 MITM 模块中看到 APP 发出的请求。  
  
代理抓包本身属于比较基础的操作，这里不再展开  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QXdwLuUl5wIFD5BuXLyAewv9j6hJPlk4m6iaD9fJ8vD94ycTHc0TV1DAsiaWKQbuWXelqgN1FeIJCaibfvAI3klprpD6hIGT5c6CU/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QXgxAYm8JEsr5KgPxYBGGoMLqImWDPsiboNQDSeql7e22ESan9foIgia6xoqwic7OCdNIG3zYMlovdCiatAxA0uqM7icDUOpx2J6Uk4/640?wx_fmt=png&from=appmsg "")  
  
  
真正值得关注的，是大量正常请求中突然出现的一条异常请求  
## 0x03 一条“不太正常”的图片请求  
  
在浏览 APP 正常功能时，我看到了一批图片、接口等请求。  
  
大部分请求都很普通，基本符合：  
  
业务域名 → API → JSON / 图片 / 其他资源  
  
但其中一个图片资源请求引起了我的注意。  
  
**APP 在请求图片资源时，并没有使用业务域名，而是直接请求了一个裸 IP 地址**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QXnmoUD4KQdUncBNvzerd7m4yznqBicPBJNexEoj5Bboh1PnjicE9DAcWycgA7FhJRWmen8D4pZtxKEiau1u0hzvLeBfRRlnapAFU/640?wx_fmt=png&from=appmsg "")  
  
当时我的第一反应并不是：“这里肯定有漏洞”，  
  
而是：**“这个 IP 到底是谁？”**  
  
它可能只是正常的图片服务器，也可能是 CDN 节点。  
  
但还有一种可能：  
  
**这是一个没有出现在前期 Web 资产视野中的独立服务器**  
  
如果是后一种情况，那么这条看似普通的图片请求，就不再只是一张图片，而更像是 APP 无意中暴露出的一个新攻击面  
  
这里还有一个值得注意的地方：**这个 IP 并不是通过扫描器枚举得到的，而是 APP 在正常业务流程中主动请求到的通信地址**  
  
这意味着，接下来与其继续围绕主域名做测试，不如顺着这个 IP 看看它到底承担着什么业务  
## 0x04 从一个 IP 继续往下追  
  
拿到这个 IP 后，我先进行了基础验证  
  
这里我并没有直接把它当成漏洞，而是先确认它到底是什么角色。  
  
如果它只是普通 CDN 节点，那么继续深入测试的价值有限；  
  
如果它对应的是一台独立业务服务器，那么就意味着 APP 暴露出了一个新的业务入口。  
  
实际访问后发现，这个地址并不是简单返回图片资源，而是可以直接访问，并且背后运行着一个独立的 Web 服务  
  
也就是说:APP->图片请求->裸 IP->独立 Web 服务->新的业务入口  
  
到这里，测试思路发生了一次变化  
  
这个 IP 本身未必意味着漏洞  
  
但**IP 背后的服务，可能意味着新的攻击面**  
  
对于众测来说，这种发现往往比单纯拿到一个 IP 更有价值  
## 0x05 意外发现文件上传功能  
  
继续观察这个 Web 服务后，我发现其中存在文件上传功能  
  
到这里，我开始重点关注它的上传逻辑  
  
首先确认正常文件能否上传，然后进一步观察服务端对于文件类型、文件名以及后缀的处理方式  
  
测试过程中发现：  
  
**服务端主要依赖文件名后缀进行限制，存在校验不足的问题，可以通过修改文件名后缀上传自定义后缀文件**  
  
原本预期的业务逻辑应该更接近：  
  
用户上传文件->服务端校验文件类型->严格限制允许的文件类型->保存文件  
  
但实际观察到的处理逻辑存在明显的限制缺失。  
  
因此，这里不能简单地把它理解成一个普通的“文件上传接口”。  
  
真正值得关注的是：  
  
**攻击者能够影响最终上传文件的类型。**  
  
当然，文件上传漏洞最终能够造成多大的影响，还需要结合上传目录、文件访问方式、服务器解析机制以及权限控制等因素进行判断。  
## 0x06 文件上传漏洞验证  
  
发现上传功能后，我没有停留在“这里存在一个上传点”这一层，而是继续对其实际处理逻辑进行了验证  
  
对于文件上传类问题来说，上传接口本身并不能直接等同于高危漏洞。真正需要确认的是：**攻击者能否控制最终写入服务器的文件，以及这个文件落地后能够产生什么影响**  
  
因此，我按照实际请求链路继续进行了测试  
  
首先测试正常文件上传，确认服务端能够接收并处理上传请求；随后进一步观察服务端对上传文件名称、后缀以及文件类型的处理方式  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QVLv0CEFuY9qIl2o4f13Avh3uN36tea9TzqPx3LAsgNVRhGK7lPUyP32wmsTQUycPvTQ9rOL7qVtmoR0rVR7cr0Y8y03yESNRc/640?wx_fmt=png&from=appmsg "")  
  
测试过程中发现，服务端对上传文件的限制并不严格，上传文件的后缀存在可控空间。通过调整上传文件的文件名及相关参数，我成功绕过了原有的文件类型限制，使服务端接受了原本不应被允许上传的文件  
  
到这里，问题已经不再是简单的“存在一个上传接口”，而是**攻击者已经能够影响服务器最终接收并保存的文件类型**  
。  
  
随后我进一步确认了文件的实际落地情况，并验证上传后的文件能够被正常访问。  
  
也就是说，这条攻击链已经实际走通：上传接口->上传限制校验->绕过文件类型限制->文件成功上传->文件实际落地->上传文件可被访问  
  
至此，文件上传漏洞已经完成了实际验证。  
  
这里需要特别说明的是，我没有继续尝试利用该文件上传点进一步获取服务器权限或扩大影响。  
  
原因并不是漏洞无法继续利用，而是在后续核实过程中发现，当前服务器虽然与目标业务存在关联，但并不属于本次众测约定的核心业务资产范围。  
  
因此，在确认漏洞真实存在并保留必要验证证据后，我停止了进一步利用，并将该问题及资产归属情况一并反馈给了甲方，甲方给出的说法与我想的一致，并且给出口头嘉奖  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QW5NUO20FG6kricUPSjMrRicde8Q0icpjEicy2CPVWGL7CMlGw84CbbaY8uZq9EichJcQatMs2TTXwgN20fq1YFdJsicARzEpWLc72ZI/640?wx_fmt=png&from=appmsg "")  
  
这次测试给我的一个比较深的体会是：  
  
**漏洞验证和漏洞利用，是两个不同的阶段；能够继续利用，不代表就应该继续利用。**  
  
在真实的安全测试中，技术上“能不能打进去”和授权范围内“应不应该继续打”，是两件完全不同的事情  
## 0x07 一个有意思的问题：我好像“打偏了”  
  
到这里，其实已经可以确认：  
  
**漏洞本身是真实存在的**  
  
后续进行资产归属核验时，却发现了一个比较有意思的情况。  
  
这个上传点所在的服务器，属于目标单位的外围附属资产，但**不在本次众测约定的核心业务资产范围之内**  
。  
  
也就是说，从技术路径来看，我确实找到了漏洞；  
  
但从众测规则和资产边界来看，却相当于：  
  
**“漏洞是真的，但打偏了。”**  
  
这个结果反而让我觉得很有代表性。  
  
因为在实际众测中，我们经常会把注意力集中在：  
```
主域名
核心业务
核心 API
后台系统

```  
  
但一个真实单位的互联网暴露面，往往远比这些复杂  
  
可能存在:核心业务,APP,API,图片服务,文件服务，历史系统，独立服务器，第三方/外围系统  
  
这些系统虽然可能服务于同一个业务体系，但安全建设水平并不一定一致。  
  
因此：  
  
**发现漏洞、确认资产归属、判断业务影响，其实是三个不同的问题。**  
  
这次测试也让我更加意识到，漏洞验证不能只关注技术层面的“能不能打”，还需要确认：  
- 资产到底属于谁；  
  
- 是否属于授权范围；  
  
- 是否属于核心业务；  
  
- 漏洞实际影响是什么。  
  
确认该资产不在本次众测约定的核心范围后，我没有继续对该服务器进行进一步利用，而是保留必要的验证证据并结束了后续测试  
## 0x08 为什么 APP 流量值得关注？  
  
回头看整个过程，真正让我觉得有价值的，其实不是最后那个上传点。  
  
而是：  
  
**如果我没有去分析 APP 流量，这个服务器很可能根本不会进入我的测试视野。**  
  
传统 Web 资产发现的思路通常是：  
```
主域名
 ↓
子域名
 ↓
IP
 ↓
端口
 ↓
Web 服务
 ↓
漏洞

```  
  
而 APP 给了我另一条路径：  
```
APP
 ↓
API / 图片 / 文件请求
 ↓
通信地址
 ↓
新的 IP / 域名
 ↓
独立 Web 服务
 ↓
新的功能
 ↓
漏洞

```  
  
这两条路径最大的区别在于：**第二条路径是从业务行为反推出基础设施**  
  
它不一定每次都能发现漏洞，但有机会发现传统资产收集过程中没有进入主要测试视野的业务入口  
  
所以对于 APP 流量，我现在更关注的并不是“能不能抓到包”，而是：  
  
**APP 到底在和哪些服务器说话？**  
## 0x09 从一次异常请求重新理解攻击面  
  
这次的情况还有一个值得注意的地方。  
  
如果单纯把：“APP 请求资源时使用了裸 IP”  
  
单独拎出来，并直接定性为信息泄露漏洞，其实并不严谨。  
  
因为：  
  
**一个 IP 地址本身，并不一定构成安全漏洞。**  
  
真正有价值的是后续形成的完整链条：  
```
APP 暴露通信信息
        ↓
发现新的网络入口
        ↓
确认入口存在独立 Web 服务
        ↓
发现文件上传功能
        ↓
发现上传限制存在缺陷
        ↓
确认真实安全风险
        ↓
进行资产归属核验

```  
  
所以，这里真正值得记录的并不是：“APP 泄露了 IP。”  
  
而是：  
  
**一个看似普通的客户端通信信息，最终成为了扩大攻击面的入口。**  
  
这也是我认为这次测试最有价值的地方  
## 0x10 如果重新来一次，我还会怎么做？  
  
如果让我重新测试一次类似目标，我大概还是会遵循几个简单的原则。  
### 1. 先看业务入口，再看客户端  
  
先完成基础 Web 资产收集。  
  
如果主 Web 面没有明显突破口，再转向：  
```
APP
小程序
H5
客户端接口

```  
  
不要把 APP 只当成一个业务客户端，也可以把它当成一个观察目标基础设施的入口  
### 2. 先关注异常，再判断漏洞  
  
对于客户端流量，我会特别注意：  
- IP 地址  
  
- 异常域名  
  
- 非主域名请求  
  
- 图片/文件服务器  
  
- 对象存储  
  
- 特殊接口  
  
- 不常见的通信地址  
  
因为真正值得继续追踪的东西，往往并不会直接告诉你：“这里存在一个漏洞。”  
  
它可能只是表现得：**“这里和其他地方不太一样。”**  
### 3. 先确认资产归属，再判断影响  
  
发现新的 IP 或域名后，不要马上进入漏洞利用。  
  
需要先回答：  
  
它属于谁？  
  
为什么 APP 会访问它？  
  
对应什么业务？  
  
是否属于目标？  
  
是否属于授权范围？  
  
只有把这些问题搞清楚，后续的漏洞验证才有意义  
### 4. 先证明问题真实存在，再考虑进一步利用  
  
对于漏洞验证，我更倾向于：发现->复现->确认->评估影响  
  
而不是为了让漏洞看起来更“高危”，强行把利用链往后延伸  
  
尤其是在真实众测环境中，**测试边界本身也是测试的一部分**  
  
02  
  
0x2 培训课程介绍  
  
26  
  
**SRC漏洞挖掘培训课程**  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/6cIuvSQkkicOHhYFkQLTibYAMUR9rfZ9eUrI78toIC4V2304G909O6s6CnVrAGiaYLEJM9XuUARhzNfxCtYKQfQ83wfPSlqpshSScfoYzSKzgY/640?wx_fmt=png&from=appmsg&wxfrom=5&wx_lazy=1&watermark=1&tp=wxpic#imgIndex=4 "")  
  
  
**1.课程价格目前是575（后面也会随着人数越多，涨价）🌟师傅们还可以上车补票，冲冲冲！**  
  
**2.报名成功送知识星球一个，拉内部小圈子交流群+SRC直播通知群！✨**  
  
**3.一周2节课程，直播+录播形式，课程内容大家可以看课表，目前是第一期，一次报名永久无限听课！❤️**  
  
**4.目前是第一期课程，后面比如说开了二、三期，都是不用在花钱的！**  
  
**5.上课结束后，会把视频录播+课件笔记一起打包发直播群！**  
  
**6.哔哩哔哩SRC课程公开课，链接🔗直达：**  
  
**https://space.bilibili.com/642258933**  
  
SRC课程详情🔎：  
[学了一堆理论，还是挖不到漏洞？你缺的是实战！](https://mp.weixin.qq.com/s?__biz=Mzk0Mzc1MTI2Nw==&mid=2247509869&idx=1&sn=4bd678e9f9c864300cc2426432a8c967&scene=21#wechat_redirect)  
  
  
内部小圈子知识星球详情🔎：[50 元封顶！渗透攻防 + SRC 漏洞星球限时开放！](https://mp.weixin.qq.com/s?__biz=Mzk0Mzc1MTI2Nw==&mid=2247509408&idx=1&sn=2e12452dfc2d34631af5109af28a6758&scene=21#wechat_redirect)  
  
  
欢迎关注公众号：  
神农Sec  
，报名咨询添加VX：  
routing_love  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/mcko8AHj6QVcCkxIUpaBmNic17zibGfXMWrr9z89gE0DFtbOu3QYzD5d62zsp6qwc38Pssk60mLq8VKthcMOmctVlHU716S5G4KYmrKVrEj5c/640?wx_fmt=other&from=appmsg&watermark=1&wxfrom=5&wx_lazy=1&tp=webp#imgIndex=6 "")  
  
开课快五个月时间  
，课程目前已经  
累计加入了1000+个学员  
了，课程培训招生任火热持续中，师傅们  
对于我们课程感兴趣的，想要学习技术，找工作的可以咨询我报名  
。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QVmdLBDlbl5p4Teyw5qqFOIFTIUxIxRay83I5qDXG690XI61gRj8MXTvTaibC4q2cCb1CbM4XS2FK6X4KYhPTX2ibgvA363YYwcE/640?wx_fmt=png&from=appmsg "")  
  
课程培训记录📝，每次上车在1-3小时之间，上课包括课程内部群大家  
交流氛围很好！  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/mcko8AHj6QV7iczVHowN13BzCTraG8jDUoe5hluiaZ90RUy7FjW398DictcrZhHrYpMgw4polRqvlGua6iakYdARPI3Jkiahhjvrvkviblm19U4F0/640?wx_fmt=other&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=4 "")  
  
课程上课笔记课件📒都会打包给师傅们，笔记都非常详细，很多几k价格的培训机构哪怕是课件笔记都没有的，我这里都是下课第一时间把  
录播+笔记打包发给大家！  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QWbRV4mBn8GZHrvHocPMYYcBuAM3gyIKOM0SicBWQhywMehkXInvEerRLySOPPMzEmM2GLSlOMFREx6QItqtCgCibGs2MeY6yvu0/640?wx_fmt=png&from=appmsg "")  
  
平常也都会给学员进行一些项目发布，包括后面的  
工作、护网内推等，经常上麦交流，大家互相学习，简历优化等。  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QXvjjkgJibDEUhdDjErjibiangGsN0rqb0Av59xfyxBbDrTMNdfIAhNXlx0HQKvxIVBIEGAAbYrEENzd77j65asejlD4a50Sb4U7o/640?wx_fmt=other&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=20 "")  
  
SRC漏洞挖掘课程培训已经两个星期了，期间也是创建了  
“回本小群”，希望学员回本越来越多，创建这个群主要是鼓励学员学习进步，以及不定时发小项目！  
  
最后也是希望大家都可以赚钱，找到好工作🎉  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QXub8yvlURuxKpiclvOJ83GJ6Is7StibGAxD7DHtMHD7yT6BGRxxETEza4qJejZkRVxdYUIicoFWdaPVWXReicG6N2ne806ajRd8Ls/640?wx_fmt=png&from=appmsg "")  
  
培训时间不长，感谢🙏师傅们的  
喜报  
，很开心看到师傅们给我分享自己的成果，  
希望师傅们越来越强！  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QUMAtEWv3xXZPDsGBRhESmwGRciaasCGibU8TtbP2U0YVZPBdf5tlLqpWAtQKBh5oFwgETyvicKBeW1JSsekAyJ5cbRlSdjooQkSM/640?wx_fmt=other&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=22 "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QUrS4N68nZ0EyE76Wkib7ZDrpnZWw2Q1RJQvFEdIOu5XvFGCwpz9lziabKyo9C9d5ZiamibuSXlibhXLHb7b8QJhqEIs3hXvqktkkyA/640?wx_fmt=png&from=appmsg "")  
  
  
平常也会分享项目，下面是一些  
学员项目成果  
，群里报课的学员都是不抽成的，主要是帮助学员进行  
回本  
，  
让大家都可以进步！  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QWS8lR4pmzZrczwr6YjtG48EqF9q4FlAUH78M7DXkiboqF8Q1HkeWJLzpFPOQBToO3auj8r4rU9x3fuafXUDVMEcFj5EI6U3P9w/640?wx_fmt=other&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=23 "")  
  
上课结束后，会把  
视频录播+课件笔记  
一起打包发直播群  
  
**「神农安全」**  
知识星球目前已经  
累计2500+网络安全爱好者的加入！  
  
后面也是小圈子做大起来了，师傅们也都喜欢看我文章，想着给大家教下src漏洞挖掘思路，所以自己花了很长时间做了✨  
课件和课表，都是纯自己手搓的，大家也可以看下课表的内容。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/b7iaH1LtiaKWXNmpV89Zxcm1J56eeHltthM2sjuWQFbmvWv79V058KwI0DswFF9LysewGtULj81Vp5bX9nTEK78A/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QVhliaOc71FnQLZjEUB2QiavqaRdiaaAN25Gb1HNADIy0cYvIIHC46za7Ab6sibRKvKG2tbJBxqrOGyczqWF44LQOKllnZXE6PU5iaE/640?wx_fmt=png&from=appmsg "")  
  
03  
  
0x3 课程特色  
  
课程  
主打真实，  
一线SRC漏洞挖掘师傅是如何学习和挖掘SRC漏洞的，让你真正了解SRC漏洞挖掘，助力在岗人员和大学生的能力提升，掌握新的技能树，为下一次  
跳槽涨薪做好准备。本  
课程内容覆盖企业  
SRC、众测项目挖掘、护网HVV红蓝攻防技巧、CVE、CNVD、EDUSRC等平台通杀案例技巧挖掘方法。  
  
本课程  
适合人群  
（光看不挖啥也不会）  
```
1、有计算机经验，想从0转行入行的大学生或自学者
2、想从CTF比赛/Web或SRC进阶到项目实战的选手
3、想参与项目/找工作/提高收入的转型者
4、想通过挖SRC赏金做副业的师傅们
5、挖SRC漏洞遇到瓶颈的师傅们
6、想学习AI安全自动化渗透测试漏洞挖掘的师傅
```  
  
课程价格：575 元  
  
报课成功的师傅们直接免费送内部小圈：一个知识星球+内部小圈子交流群  
```
1、课程价格真心实惠，绝不割韭菜
2、四五百的课程价格让你体会大几千的培训课程内容
3、带着大家从0到1，本人上课坚持手搓课件（实战案例+知识体系）
4、拒绝使用PPT演讲模式（无实操，很枯燥）
```  
  
直播培训教学方式  
  
课程  
一周1-2节课，课程特色涵盖直播多人上麦活跃回答，直播过程中有问题随时解决或私信我。  
拉群：一个知识星球内部小圈子交流群+课程培训直播通知群。有项目/工作/护网第一时间内推报课的师傅，  
一对一简历优化，助力在岗人员和大学生的能力提升。  
  
一次报名每期均可永久学习，并且赠送内部「神农安全」知识星球，一对一永久解答、无保留教学！  
  
欢迎关注公众号：  
神农Sec  
，报名咨询添加VX：  
routing_love  
  
课程均为线上交付，报名成功后  
不支持退款  
  
内部小圈子  
（知识星球+内部小圈子交流群+知识库）  
  
对内部小圈子感兴趣的师傅们也可以看下下面的这个  
跳转链接，里面有对小圈子的详细介绍，报名课程成功的师傅们直接免费送一个（直接点击下面直接可以跳转）。  
  
[强烈推荐一个永久的SRC挖掘、渗透攻防内部知](https://mp.weixin.qq.com/s?__biz=Mzk0Mzc1MTI2Nw==&mid=2247508882&idx=1&sn=0ca5ab133a5b589e26e25de14882b28f&scene=21#wechat_redirect)  
  
[‍](https://mp.weixin.qq.com/s?__biz=Mzk0Mzc1MTI2Nw==&mid=2247508882&idx=1&sn=0ca5ab133a5b589e26e25de14882b28f&scene=21#wechat_redirect)  
  
[识库](https://mp.weixin.qq.com/s?__biz=Mzk0Mzc1MTI2Nw==&mid=2247508882&idx=1&sn=0ca5ab133a5b589e26e25de14882b28f&scene=21#wechat_redirect)  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QVRzhiawbmNicgOFicLKeMZPtpyqtP9M0IA7gJZPerY1pI0P1Owcs0ttibWiaw87asg3qibyVF9NEVeGuxL3YqASaQhUn3pUBjicpMTPM/640?wx_fmt=png&from=appmsg "")  
  
讲师介绍  
  
id：一个想当文人的黑客  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QX6UX8mhQtia4qnfEiasbq2R3KjlwQg2ysg4ibj744R4DF0BXZQZBjHc3qNgPKkqG7msub5w6WjSmoElCibibTp6qImS3FkupITqJUk/640?wx_fmt=png&from=appmsg "")  
  
欢迎关注公众号：神农Sec，报名咨询添加VX：  
routing_love  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/b7iaH1LtiaKWXLicr9MthUBGib1nvDibDT4r6iaK4cQvn56iako5nUwJ9MGiaXFdhNMurGdFLqbD9Rs3QxGrHTAsWKmc1w/640?wx_fmt=jpeg&from=appmsg "")  
  
04  
  
0x4 第一期挖洞培训课表内容  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QXdFkU8hwaxia8XQ7EyshqMb1BUOknbNI4lhtliaE0iakNZ0PRmjBUocUGbGDmEaGwuZDDP4sXkOrjicxI1exTafD6wdNUTj66wCWw/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/MVPvEL7Qg0F0PmZricIVE4aZnhtO9Ap086iau0Y0jfCXicYKq3CCX9qSib3Xlb2CWzYLOn4icaWruKmYMvqSgk1I0Aw/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1 "")  
  
**内部圈子介绍（报课赠送）**  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/MVPvEL7Qg0F0PmZricIVE4aZnhtO9Ap08Z60FsVfKEBeQVmcSg1YS1uop1o9V1uibicy1tXCD6tMvzTjeGt34qr3g/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1 "")  
  
  
  
  
**圈子专注于更新src/红蓝攻防相关：**  
  
```
1、维护更新src专项漏洞知识库，包含原理、挖掘技巧、实战案例
2、知识星球专属微信“小圈子交流群”
3、微信小群一起挖洞
4、内部团队专属EDUSRC证书站漏洞报告
5、分享src优质视频课程（企业src/EDUSRC/红蓝队攻防）
6、分享src挖掘技巧tips
7、不定期有众测、渗透测试项目（一起挣钱）
8、不定期有工作招聘内推（工作/护网内推）
9、送全国职业技能大赛环境+WP解析（比赛拿奖）
10、十个专栏会持续更新~提前续费有优惠，好用不贵很实惠
11、每日内部资料分享，内部圈子资料1000+
12、联系圈主获取：内部漏洞知识库+圈子使用手册+内部圈子交流群
13、VX：routing_love，技术交流+疑问解决
```  
  
  
**内部圈子**  
**专栏介绍**  
  
知识星球内部共享资料截屏详情如下  
  
（只要没有特殊情况，每天都保持更新）  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/b7iaH1LtiaKWWYcoLuuFqXztiaw8CzfxpMibgpeLSDuggy2U7TJWF3h7Af8JibBG0jA5fIyaYNUa2ODeG1r5DoOibAXA/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/b7iaH1LtiaKWUw2r3biacicUOicXUZHWj2FgFxYMxoc1ViciafayxiaK0Z26g1kfbVDybCO8R88lqYQvOiaFgQ8fjOJEjxA/640?wx_fmt=png&from=appmsg "")  
  
  
05  
  
0x5   
优秀学员报喜  
  
下面是最近两个月培训期间，很多  
优秀学员进行报喜，看到师傅们有收获，也是感到很开心的！  
拉回本小群，就是为了促进大家学习，在群里发学员成果，也是为了让大家学习优秀的师傅们。  
  
加油，你我皆是黑马！  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QWS8lR4pmzZrczwr6YjtG48EqF9q4FlAUH78M7DXkiboqF8Q1HkeWJLzpFPOQBToO3auj8r4rU9x3fuafXUDVMEcFj5EI6U3P9w/640?wx_fmt=other&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=23 "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QWkBFHe0S1MayHGboNyYGhNR94Fic11frXxdUGBgjjIx6dnJ6lgWxw7iajkmFTiczQq5DHN1bwUchcVzatv95E5gibAMUiaZ7fHlypw/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QU6ibZutPq43zUiap7IgDmJq7kwUKJBCa2IDujYiadMJfe9fFH9DOfUEOM2TibibYRuFiahDqMnBX1MVjLw5XIdNDSuR5P3g7XibaUkBo/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QVz2wFpVfer0uAFVpLKyicMaaLkmJDdg5bWnOotuzN3S9r2FMKpEKrJy8ND7icWVzNgqyYS2J6XElVN43vGca4X6HcEqapwGcNX0/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QWoKYrxQzob221aCmicmemD3aVPbk5dvVIaEic4TNPXrnkRazOTHnIbq87Jbk0GREdlI4iaZUmVU3c6K6rBbyZzBnkicooOUVtzN1I/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QWdSycCicsfpn14HlUgEibU04lpXJ4a70L4D6oSWx5s0tLgnLnLiaRAdclxNVicYKFRD1mGn40jQ4t4ic8XZzVoOSTTCzY9xz9auzJI/640?from=appmsg&wxfrom=12&wx_fmt=other&tp=webp&usePicPrefetch=1&watermark=1 "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QVRqR4bI22pibTSVQVSibImicNHOCUGQAaUlo9lZsNJicLmcTaQKl662ulqoX54EmbCDKUD3ibibdZxqKaOJAcoyIV7cAn7tKqia8S470/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QXB1O6Fx3ia62NNWITh9vUQaEKp7epibLWeEsdobibvBvqNDoTCAvfyQFHw597O24naJAIpM4QALgfqMWWc4E1KHrxBoaGRxE4Ajc/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QXnjwIRWjJOVSuN4X4HjmEFtCVqCHZ05M77sXqzmVjibaJbLUw3ApOuz7iaH8OCCnRmTRYVtKC5NajGKVkI4pnKZsJaj0T4iaYibq0/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QWRdtEUY2aeAZh34wDle515j7UwnibFQeCibWSeDKGnIZ2YH5VGX64cYeXgPGdCwHLKdsMY07EIVliapxh10gzQ2EO3bks7bxhmVs/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QW7rozFqSBVNRDE2kbfUSB4FefOPm9LXM2B9bV4n9VPM7Kt11rfw284Ejn4AHUc1Uc1r1gZs3FF6umgPk1QejcC6zrOAYEyegY/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QV0epFsicbJrNPgGwNXcXRCDrC1sGQySI2ylkfs2Hdic6d6unjwqNiby5DfhtfT6ezabX13bNeR53pOW3BUqLaZrIvPM8Z4IsBpgI/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/mcko8AHj6QXwfrgic5XLseOxkPOWkjm1yicAW2ZiaAqzxtbjPok4Yhic2Wiblic93SSGN5BtT77AFuZt6ySuRL09icqIicPuOUUbL5NbWgMCHgLictiak/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/mcko8AHj6QU7jGMRbvGyeDmHE6KjibHDNmuqO2NDaG4soIjTtg6uQoy4H5x0FntPDicjnUtibVgFMTNvNRaA9SJicNj2BIWQNz2vfRaQDfkKsibI/640?wx_fmt=jpeg "")  
  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QUyyXAWlMf8dHcspnMDucUzRBbeXWW58yMWQncvENPDmIpEKr4HlZ0cyWZSLGiakB03zRmvicfIF79LdWQ6s6VOyxl44WgvMwzf4/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QX83LJfPytAENC2dGNwhg0pFd7as7FhJZum31EJkbnicO88ZNIwXflHjpsuQV3I0BQIRkNzbVy2nkhMicice6QDO6gMkdg5RLiboiaA/640?wx_fmt=png&from=appmsg "")  
  
  
  
**神农安全公开交流群**  
  
有需要的师傅们直接扫描文章二维码加入，然后要是后面群聊二维码扫描加入不了的师傅们，直接扫描文章开头的二维码加我（备注加群）  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/mcko8AHj6QVOEGwHSvWmZdtYpN4SBDudos5e9trbzcia1KBOnDpEpmQicd3wGulBjXWRGqMMZbAf7jogvwv0sbVoPJB8iaf1ib5GYtJ0zvXVNL8/640?wx_fmt=jpeg&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/mcko8AHj6QV9grs7NOhSTCfTpCc4xrxdnlISIReNNCKR2EOyWvhMpyIzbma8nuelSg8LicKF5yYZ7hgyODlWgMmhViaE8Ahhs7PZlnmA0VFcY/640?wx_fmt=jpeg&from=appmsg "")  
```
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/b7iaH1LtiaKWW8vxK39q53Q3oictKW3VAXz4Qht144X0wjJcOMqPwhnh3ptlbTtxDvNMF8NJA6XbDcljZBsibalsVQ/640?wx_fmt=gif "")  
  
  

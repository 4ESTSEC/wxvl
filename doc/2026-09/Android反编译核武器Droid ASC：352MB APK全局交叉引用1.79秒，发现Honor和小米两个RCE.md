#  Android反编译核武器Droid ASC：352MB APK全局交叉引用1.79秒，发现Honor和小米两个RCE  
原创 Red Hunter
                    Red Hunter  黑白之道   2026-09-14 00:30  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/nGzNudUIJ6MCd7HH4ia0YAgZeeIQdRibgyWrOKxYeZDIxhDH1HMPDIW5cTriaGCC8pYnicvZkTPrYwxFBIpz79k1aHnpV6FGW29GImvw8slvmMM/640?from=appmsg "")  
> **导语**  
：Android逆向圈出了个狠角色。MG193_7开发的Droid ASC（Android Super-fast Compiler/decompiler）入选BlackHat EU Arsenal，号称全面替代Jadx MCP——352MB商业APK全局交叉引用只要1.79秒，目标类反编译177毫秒，内存峰值141MB。更狠的是，作者这周用Agent+ASC组合拳在Honor和小米产品里挖出两个RCE。  
  
## 一、为什么传统反编译器慢得像蜗牛  
  
做Android逆向的兄弟都熟悉这个流程：拿到APK，先丢进Jadx/JEB/Bytecode Viewer，等上几分钟到几十分钟，让工具把整个APK完全解压、构建完整的调用图、生成全局交叉索引。等索引建完，你才能愉快地搜索字符串、类、方法、字段引用。  
  
作者一句话点破了这种工程哲学的荒谬："编译产物本身就是高度结构化的数据，现代反编译器却浪费时间在已结构化数据上重建庞大的代码关系数据库。"  
  
说白了，传统工具是"先全量索引建仓库，查询时再查仓库"。问题是——一个352MB的APK你真的要为了一次查询吃下几个GB内存、跑上几十分钟吗？  
## 二、Droid ASC的核心思路  
  
MG193_7的反向思路极其暴力：把APK本身当成只读数据库，查询时按需穿透，不做任何预处理。  
  
核心架构四大杀招：  
  
**1. Deflate比特流里直接探针**  
——放弃完全解压，在压缩比特流里直接构建稠密Huffman查找表，只取核心元数据，连无关的数据块都不碰。这一招就把传统工具最大的内存黑洞堵死了。  
  
**2. 武器化R8编译器优化**  
——R8编译器为了减小体积，会做确定性常量重定位和指令去重，结果是字节码在物理布局上高度集中。ASC直接利用这个特性做跨DEX闪电搜索，把编译器的优化成果变成自己的搜索加速器。  
  
**3. O(1)指令定位原语**  
——传统工具要建立方法偏移映射表才能定位到具体方法。ASC搞了个常数时间定位算法，原始字节码偏移直接O(1)映射回方法，跳过所有重映射步骤。  
  
**4. 内存中动态重建最小DEX**  
——命中目标后，只提取相关字节码和依赖项，在内存里实时拼出一个最小且自洽的DEX，然后反编译。不碰APK里的其他无关类。  
  
![Droid ASC零预处理流水线](https://mmbiz.qpic.cn/sz_mmbiz_png/nGzNudUIJ6PyvZeg6iaN9fn695huInqnRj3N0pEKkVZbP21KqjzMmPawhaVNoGG79CL6ZiaKiaIghXY18Zvicvc4ap6y9l6oV5312MgljZOCCTw/640?from=appmsg "Droid ASC零预处理流水线")  
## 三、性能数据：352MB APK的硬核对决  
  
作者拿一个352MB的商业APK做实测，把Jadx/JEB/Bytecode Viewer/APKtool这些主流工具拉出来做基准测试。  
  
核心数据：  
- **全局交叉引用搜索**  
：Droid ASC 1.79秒，对手们普遍几十秒到几分钟  
  
- **目标类反编译**  
：Droid ASC 177毫秒  
  
- **内存占用峰值**  
：Droid ASC 141MB  
  
传统工具动辄吃掉几个GB内存、跑上半小时的活，ASC用百兆级内存、两秒内完成。这个数字对做大规模批量挖洞的红队研究员来说，是质变。  
## 四、配套命令：CLI简洁到让人想哭  
  
ASC只暴露两个子命令：  
  
**getclass**  
——定位目标类，提取DEX到内存，再反编译。python main.py getclass app.apk com.poc.Main -o Main.java  
  
**findrefs**  
——跨所有DEX搜索字符串/类型/方法/字段引用。python main.py findrefs app.apk string token --fuzzy-class  
  
再加个--gui  
参数直接出GUI界面。这种CLI风格让我想起NetSPI的MicroBurst——少废话，多办事。  
## 五、实战战果：Honor和小米的两个RCE  
  
这才是让我这个红队真正坐直身子的部分。  
  
MG193_7自称这周用Agent+ASC组合，在Honor和小米产品中各挖出一个RCE。考虑到ASC的并行能力（作者说同时分析10+ APK），这种Agent驱动的批量挖洞范式让单兵作战能力直接拉到团队级别。  
  
10个APK同时扔进去分析，全局交叉引用秒级返回，Agent拿到结果做模式识别和漏洞假设，反编译目标类确认漏洞点。一套流水线下来，传统人工逆向可能要一周的工作量，Agent+ASC可能一晚上搞定。  
  
国产手机厂商这两年在安全响应上下了功夫，但这种"反编译核武器+AI Agent"的攻击节奏，对任何甲方安全团队都是新挑战。  
## 六、如何获取和部署  
  
仓库地址：**https://github.com/MG1937/ASC**  
  
目前还没发布二进制release，直接git clone源码运行：  
```
git clone https://github.com/MG1937/ASC.gitcd ASCpython main.py app.apk --gui
```  
  
依赖Python 3.10+，详细安装步骤看仓库README。  
  
BlackHat EU Arsenal演讲议程：**https://blackhat.com/europe/arsenal/schedule/index.html#droid-asc-r8-compiler-optimization-as-a-decompiler-primitive-54834**  
## 七、红队视角总结  
  
Droid ASC的出现，本质上是把"反编译"这个老问题用数据库查询的思路重新解了一遍。当工具本身的性能瓶颈被消除，真正的瓶颈就变成了研究员的思路和Agent的智能化程度。  
  
Honor和小米的两个RCE是警告信号。Android App供应链的安全研究节奏，正在从"几个人手工挖"变成"AI Agent批量扫"。攻击侧的成本曲线被进一步压低，防守侧的响应速度必须跟上。  
  
这条管道，迟早会有更多团队复制。  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/nGzNudUIJ6PN1aSyo2sp6MhXBBl31DqqK1Sgg1laicKicvCN4KR257TPUZ8SiaPVRvpJvyfkAUnG01WQTfDq0Im6jl4KojkN09ZEdnqc80icra8/640?from=appmsg "")  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650621144&idx=1&sn=895132b6dea5c5055ac21126293661f9&scene=21#wechat_redirect)  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650621255&idx=4&sn=75d0f413e300d99d4e5cc631714c96ae&scene=21#wechat_redirect)  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650621242&idx=1&sn=c7504153dd6aa285da53fc1a4a907f82&scene=21#wechat_redirect)  
> 👇 点击**阅读原文**  
，访问我的网站  
  
  

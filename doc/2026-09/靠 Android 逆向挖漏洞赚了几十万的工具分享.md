#  靠 Android 逆向挖漏洞赚了几十万的工具分享  
原创 网络安全透视镜
                    网络安全透视镜  网络安全透视镜   2026-09-11 00:30  
  
BLACK HAT · ARSENAL  
  
2026.09  
  
拆包慢点而已，反正不影响挖洞  
  
352MB 的 APK  
   
搜完全局  
  
只要 141MB 内存  
  
  
零预处理 · 无状态 · 毫秒级按需反编译  
  
Droid ASC · Black Hat Europe 2026  
  
REVERSE  
ANDROID  
  
📦 4 Parts + Conclusion  
  
👉 滑动  
  
PART 01  
  
它有多快  
  
BENCHMARK  
  
   
PART 02  
  
零索引思路  
  
ZERO INDEX  
  
   
PART 03  
  
五分钟上手  
  
TUTORIAL  
  
   
PART 04  
  
值多少钱  
  
PENTEST  
  
   
PART ///  
  
写在最后  
  
SUMMARY  
  
一句话理解 Droid ASC  
  
把 APK 当数据库查，而不是当工程来索引  
  
做移动 SRC 的人心里都有一本账：  
一个洞的奖金从几千到几万，但真正吃掉预算的是时间  
——拖一个几百 MB 的包进 jadx，风扇狂转、内存飙到十几 GB，进度条爬几十分钟，运气不好直接   
OOM 崩掉  
。同样的一个晚上，别人已经交了三个洞，你还在等索引建完。  
  
拆包慢从来不只是「体验问题」：众测和 SRC 是按洞结算的，同一批资产你比别人晚半天拿到接口清单，能测的面就少一半，能交的洞就少好几个——  
工具效率是可以直接换算成收入的  
。所以今天分享的这套 Android 逆向工具流，从老牌的 jadx 到刚在 Black Hat Europe 2026 Arsenal 亮相的 Droid ASC，核心只解决一件事：  
把「拆包到出结果」压到秒级  
。  
  
先说结论：同一个 352MB 的商业包，jadx 全局搜索跑到   
49% 就 OOM 出局  
，而 **Droid ASC**  
 只用 1.79 秒出结果，内存只吃 141MB。下面按「有多快 → 凭什么这么快 → 怎么用 → 在渗透测试里值多少钱」拆开讲。  
  
01  
  
PART  
  
  
它有多快  
  
BENCHMARK · 官方跑分实测  
  
官方在四个真实商业 APK 上做了对比测试（10 线程，对照组是最常用的 jadx），数据相当暴力：  
  
<table><thead><tr><th style="background: rgb(5, 150, 105);color: rgb(255, 255, 255);font-weight: 700;padding: 8px 12px;text-align: left;"><section><span leaf="" data-page-node-id="pLYQ86cEVWNg5d8sX6CZSY" data-dm-ref="132">测试 APK（大小）</span></section></th><th style="background: rgb(5, 150, 105);color: rgb(255, 255, 255);font-weight: 700;padding: 8px 12px;text-align: left;"><section><span leaf="" data-page-node-id="k35xnc93xuMAq3sUGyBE66" data-dm-ref="134">全局字符串搜索</span></section></th><th style="background: rgb(5, 150, 105);color: rgb(255, 255, 255);font-weight: 700;padding: 8px 12px;text-align: left;"><section><span leaf="" data-page-node-id="6Ohn0LtIsbvEEA7AlszUZ2" data-dm-ref="136">反编译单个类</span></section></th><th style="background: rgb(5, 150, 105);color: rgb(255, 255, 255);font-weight: 700;padding: 8px 12px;text-align: left;"><section><span leaf="" data-page-node-id="3zVhnGiYOBCGgGJe76mVFf" data-dm-ref="138">内存占用</span></section></th></tr></thead><tbody><tr><td style="padding: 8px 12px;border-bottom: 1px solid rgb(229, 231, 235);color: rgb(55, 65, 81);"><section><span leaf="" data-page-node-id="YO8Qv3hlATWfIhe77RnS7v" data-dm-ref="142">TelegramX（59MB）</span></section></td><td style="padding: 8px 12px;border-bottom: 1px solid rgb(229, 231, 235);color: rgb(55, 65, 81);"><section><span leaf="" data-page-node-id="ycZ0vgKuTaJ3AB8HNMtJnF" data-dm-ref="144">493ms vs 20s</span></section></td><td style="padding: 8px 12px;border-bottom: 1px solid rgb(229, 231, 235);color: rgb(55, 65, 81);"><section><span leaf="" data-page-node-id="yGk53DP63e0FIn16vlOh6L" data-dm-ref="146">168ms vs 6s</span></section></td><td style="padding: 8px 12px;border-bottom: 1px solid rgb(229, 231, 235);color: rgb(55, 65, 81);"><section><span leaf="" data-page-node-id="DVjVCnPHZlkB7NfUcBFVpn" data-dm-ref="148">36MB vs 1,016MB</span></section></td></tr><tr><td style="padding: 8px 12px;border-bottom: 1px solid rgb(229, 231, 235);color: rgb(55, 65, 81);background: rgb(249, 250, 251);"><section><span leaf="" data-page-node-id="UgJH7MpdRAjslkcRj5JmQo" data-dm-ref="151">WhatsApp（130MB）</span></section></td><td style="padding: 8px 12px;border-bottom: 1px solid rgb(229, 231, 235);color: rgb(55, 65, 81);background: rgb(249, 250, 251);"><section><span leaf="" data-page-node-id="VNe93dcWXkoAqbohZipsIV" data-dm-ref="153">620ms vs 32s</span></section></td><td style="padding: 8px 12px;border-bottom: 1px solid rgb(229, 231, 235);color: rgb(55, 65, 81);background: rgb(249, 250, 251);"><section><span leaf="" data-page-node-id="yb6PHivTK6lODFhcyCf5B4" data-dm-ref="155">160ms vs 15s</span></section></td><td style="padding: 8px 12px;border-bottom: 1px solid rgb(229, 231, 235);color: rgb(55, 65, 81);background: rgb(249, 250, 251);"><section><span leaf="" data-page-node-id="Cp1tjJ8Yq5KtBSFFfCzjH8" data-dm-ref="157">36MB vs 2.1GB</span></section></td></tr><tr><td style="padding: 8px 12px;border-bottom: 1px solid rgb(229, 231, 235);color: rgb(55, 65, 81);"><section><span leaf="" data-page-node-id="ovtl5xV9t7O6PE2wsAewZ8" data-dm-ref="160">Grab（228MB）</span></section></td><td style="padding: 8px 12px;border-bottom: 1px solid rgb(229, 231, 235);color: rgb(55, 65, 81);"><section><span leaf="" data-page-node-id="R48ebyyMdJxbLMmZb8eO4k" data-dm-ref="162">1.01s vs 2m14s</span></section></td><td style="padding: 8px 12px;border-bottom: 1px solid rgb(229, 231, 235);color: rgb(55, 65, 81);"><section><span leaf="" data-page-node-id="JURE34QJx8bH9IQGBKWGLB" data-dm-ref="164">177ms vs 1m37s</span></section></td><td style="padding: 8px 12px;border-bottom: 1px solid rgb(229, 231, 235);color: rgb(55, 65, 81);"><section><span leaf="" data-page-node-id="OkVNmrlb3H0K0VpRnlYRtT" data-dm-ref="166">56MB vs 7.1GB</span></section></td></tr><tr><td style="padding: 8px 12px;border-bottom: 1px solid rgb(229, 231, 235);color: rgb(55, 65, 81);background: rgb(249, 250, 251);"><section><span leaf="" data-page-node-id="ATg2GkBUCrXneQqQR9wkir" data-dm-ref="169">抖音（352MB）</span></section></td><td style="padding: 8px 12px;border-bottom: 1px solid rgb(229, 231, 235);color: rgb(55, 65, 81);background: rgb(249, 250, 251);"><section><span leaf="" data-page-node-id="sOmg9diBZZucLLa9niVlRm" data-dm-ref="171">1.79s vs 8m02s（搜到 49% OOM）</span></section></td><td style="padding: 8px 12px;border-bottom: 1px solid rgb(229, 231, 235);color: rgb(55, 65, 81);background: rgb(249, 250, 251);"><section><span leaf="" data-page-node-id="Vtsmp2xYtia7UCESen4EBo" data-dm-ref="173">415ms vs 1m32s</span></section></td><td style="padding: 8px 12px;border-bottom: 1px solid rgb(229, 231, 235);color: rgb(55, 65, 81);background: rgb(249, 250, 251);"><section><span leaf="" data-page-node-id="zhkBWCniodwdsmFwpPKOPQ" data-dm-ref="175">141MB vs 13.2GB</span></section></td></tr></tbody></table>  
  
注意抖音那一行：jadx 全局搜索跑到   
49% 时直接 OOM 出局  
，ASC 只用 1.79 秒就把结果吐完了。换算成倍数：  
搜索最高快 269 倍  
、反编译最高快 222 倍、内存最高省 125 倍。磁盘缓存方面，ASC 全程 0MB，jadx 则要写 119～322MB 的索引缓存。  
  
8 分 02 秒 vs 1.79 秒  
  
同一个 352MB 的包，同一个全局搜索  
  
![](https://mmbiz.qpic.cn/mmbiz_png/m3tfzlbEQPr0n3uksyiaBrT5wANdd1ic0dJkDot43q2XJrmlXDSSVacOkS4IzyL9BaCD4fKG5qQCHrJh6VnNNxnQ3icsVm8VVIWXwaZDgnWvLE/640?wx_fmt=png&from=appmsg "")  
  
— ASC 与 jadx 官方基准对比（图源：GitHub MG1937/ASC）  
  
02  
  
PART  
  
  
零索引的聪明劲  
  
ZERO INDEX · 核心思路  
  
传统反编译工具的时间都花在哪？  
全量解包、建全局索引和交叉引用  
。作者的观点很直接：编译产物本来就是高度结构化的数据，现代反编译器却在上面重新造一个臃肿的关系数据库，这件事本身就违背常识。Droid ASC 的四个关键技术：  
  
1  
在   
Deflate 码流内直接探测  
——不完整解压 APK，构建稠密 Huffman 查找表，只抽取需要的核心元数据块，无关数据碰都不碰。  
  
2  
把   
R8 编译器优化「武器化」  
——确定性常量重定位与指令去重，让代码在物理布局上高度集中，ASC 利用这一编译器行为实现跨 DEX 的闪电搜索。  
  
3  
用   
O(1) 指令定位原语  
——把字节码 offset 以常数时间映射回所属方法，不建任何映射表。  
  
4  
命中后  
在内存中重组最小 DEX  
——只抽取目标类及其依赖的字节码，动态拼出一个自洽的最小 DEX，立刻反编译。  
  
整个过程  
无状态、零预处理、零磁盘缓存  
，用完即走，不在机器上留任何痕迹。  
  
03  
  
PART  
  
  
五分钟上手  
  
TUTORIAL · 使用方式  
  
STEP 01#### 环境准备  
  
  
项目是纯 Python 项目，clone 下来就能跑。从源码看：反编译后端用   
androguard  
，Dalvik 字符串编码依赖   
mutf8  
，GUI 基于 Python 自带的 Tkinter，没有其他重量级依赖。  
  
.  
.  
.  
bash  
  
git clone https://github.com/MG1937/ASC.git  
  
cd ASC  
  
pip install androguard mutf8  
  
STEP 02#### 图形界面：一条命令  
  
  
CMD  
   
python main.py app.apk --gui  
  
GUI 里可以按包名浏览类树，对 string / type / method / field 四类目标做全局搜索，点开即看反编译源码，还支持  
标识符重命名  
、主题切换和 Manifest 查看——相当于一个「秒开版 jadx-gui」。  
  
STEP 03#### 命令行提取单个类  
  
  
.  
.  
.  
bash  
  
python main.py getclass app.apk Lcom/poc/Main; -o Main.java  
  
python main.py getclass app.apk com.poc.Main --threads 16  
  
类名支持   
com.poc.Main  
 这样的常见写法，工具会自动转成 Dalvik 描述符。  
  
STEP 04#### 全局引用搜索 findrefs  
  
  
.  
.  
.  
bash  
  
python main.py findrefs app.apk string token -o string_refs.txt  
  
python main.py findrefs app.apk type com.poc.Main  
  
python main.py findrefs app.apk method onCreate --class com.poc.Main  
  
python main.py findrefs app.apk method notify --class openclaw --fuzzy-class -o method_refs.txt  
  
python main.py findrefs app.apk field changeQuickRedirect -o field_refs.txt  
  
string 和 type 是模糊匹配；method / field 可以不指定类名做全局搜，也可以用   
--class  
 配合   
--fuzzy-class  
 做模糊类名限定；  
-o  
 把结果落盘，  
--threads  
 控并发，  
--debug  
 输出各阶段耗时。  
  
  
  
  
  
✦ 实战组合拳  
  
string 搜 token、Authorization、AKID、内网 IP 段；type 搜 Lokhttp3、Lretrofit2 定位网络层；field 搜 changeQuickRedirect 看有没有热修 / 插件化痕迹。  
  
04  
  
PART  
  
  
在渗透测试里值多少钱  
  
PENTEST · 变现价值  
  
对做渗透测试、红队和移动 SRC 的人来说，Droid ASC 改变的是「拆包到出结果」这段最磨人的等待；而在  
按洞结算  
的场子里，这段等待就是真金白银。具体有五个好处：  
  
1  
大包敏感信息筛查从分钟级到毫秒级——渗透测试第一步就是拆包找硬编码密钥、API 地址、加密盐，352MB 的包原来要等 8 分钟，现在  
2 秒内出全量引用  
，批量筛资产时这个差距会被放大成小时级；多出来的时间，就是多几个能测的面、多几个能交的洞。  
  
2  
低配机器也能啃硬骨头——  
141MB 内存  
对比 jadx 的 13.2GB，云主机、旧笔记本、容器里都能跑，不会再被 OOM 劝退，也不用为了跑一次拆包去加机器。  
  
3  
对 R8 混淆产物天然友好——现在的主流 APK 基本都被 R8 处理过，ASC 直接利用 R8 优化后的物理布局做检索，  
混淆越「规整」搜得越快  
，不用再靠猜类名碰运气。  
  
4  
快速绘制 API 攻击面——用 type / method 定位网络层与业务接口类，配合 getclass 秒出目标类源码，  
接口清单的产出速度直接决定测试节奏  
，同样一个窗口期能覆盖的资产数量翻倍。  
  
5  
无痕分析、用完即走——零预处理、零磁盘缓存、无状态，对涉密样本的  
应急响应和取证场景  
特别干净，交付完不留一堆索引文件。  
  
                       老规矩：以上玩法请用在**授权测试与合法研究**  
场景。  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/apNprpz3YS4ibIBPcmgJMLNXWIaCPcW54mVicYJkaOO1JQicEDBGCBM1P7IiaiablZ9tEUrP27FyvB9CZWl5SiaqhicDw/640?wx_fmt=png "")  
  
  
///  
  
LAST  
  
  
写在最后  
  
SUMMARY · 结语  
  
Droid ASC 的价值不只是一个「更快的 jadx」，而是一个新的工作范式：  
反编译器不必是臃肿的索引工具，可以是按需查询的引擎  
。省下来的每一分钟，都是可以再多测一个接口、多交一个洞的时间。  
  
项目地址  
  
https://github.com/MG1937/ASC  
  
Black Hat Europe 2026 Arsenal  
  
blackhat.com/europe/arsenal/schedule（搜 Droid ASC）  
  
AI中转站：  
https://dg8.site 国产模型0.4倍率(4折)  
  
既然看到这里了，如果觉得有用，随手点个赞、在看、转发三连吧。  
  
点赞  
  
  
在看  
  
  
转发  
  
THANKS FOR READING  
  

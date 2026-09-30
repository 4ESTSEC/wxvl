#  苹果修复或已用于定向攻击的 CoreGraphics 漏洞  
Ravie Lakshmanan
                    Ravie Lakshmanan  代码卫士   2026-09-30 00:46  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/Az5ZsrEic9ot90z9etZLlU7OTaPOdibteeibJMMmbwc29aJlDOmUicibIRoLdcuEQjtHQ2qjVtZBt0M5eVbYoQzlHiaw/640?wx_fmt=gif "")  
    
聚焦源代码安全，网罗国内外最新资讯！  
  
编译：代码卫士  
  
**苹果公司发布安全更新，修复旧版****iOS****、****iPadOS****和****macOS****中的漏洞****CVE-2026-86950****，并表示该漏洞可能已被用于定向攻击活动中。**  
  
该漏洞是影响  
 CoreGraphics   
组件的越界写入漏洞，在处理恶意构造的文件时可能导致任意代码执行。苹果表示已通过改进边界检查修复该漏洞，并提到由  
Meta   
产品安全团队发现并报送。  
  
苹果补充表示：“苹果了解到有报告称该问题可能已用于极其复杂的攻击活动中，该攻击针对使用  
 iOS 27   
之前版本的特定个体。  
”  
不过该公司并没有详细说明有多少个人成为目标、这些尝试中是否有成功案例，或者该漏洞的首次利用发生在何时。  
  
该漏洞已在以下设备和操作系统版本中修复：  
  
- iOS 26.7.1   
和  
 iPadOS 26.7.1——iPhone 11   
及后续机型、  
iPad Pro 12.9   
英寸第  
 3   
代及后续机型、  
iPad Pro 11   
英寸第  
 1   
代及后续机型、  
iPad Air   
第  
 3   
代及后续机型、  
iPad   
第  
 8   
代及后续机型，以及  
 iPad mini   
第  
 5   
代及后续机型；  
  
- macOS Tahoe 26.7.1——  
运行  
 macOS Tahoe   
的  
 Mac  
；  
  
- macOS Sequoia 15.8.1——  
运行  
 macOS Sequoia   
的  
 Mac  
。  
  
  
  
今年  
 2   
月早些时候，苹果公司修复了位于  
 dyld   
中的一个内存损坏漏洞（  
CVE-2026-20700  
，  
CVSS   
评分：  
7.8  
），并表示该漏洞已被用于复杂的网络攻击活动中。  
  
  
代码卫士试用地址：https://sast.qianxin.com/  
  
开源卫士试用地址：https://oss.qianxin.com/  
  
  
  
  
  
  
  
  
  
**推荐阅读**  
  
[塔塔电子数据泄露，苹果和特斯拉机密供应链信息遭暴露](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247526473&idx=1&sn=40ae1df01b82f1747285dd3b6f1b6b68&scene=21#wechat_redirect)  
  
  
[苹果紧急修复可导致 FBI 恢复已删除 Signal 消息的漏洞](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247525856&idx=1&sn=b1bcd2ca134b03705eca8fb363272731&scene=21#wechat_redirect)  
  
  
[苹果新 0day 漏洞已用于“极其复杂的”攻击](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247525108&idx=1&sn=2b500bf8aa918225bd717208dcda3326&scene=21#wechat_redirect)  
  
  
[苹果紧急修复两个已遭利用的 0day 漏洞](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247524661&idx=1&sn=a7c928af6fec96c822d33060a6e3cd66&scene=21#wechat_redirect)  
  
  
[苹果零点击 RCE 漏洞最高赏金翻倍，可达500多万美元](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247524150&idx=1&sn=d8a315269ae56b881c337f89557f8304&scene=21#wechat_redirect)  
  
  
  
  
  
**原文链接**  
  
https://thehackernews.com/2026/09/apple-patches-coregraphics-flaw.html  
  
  
题图：Pixa  
b  
ay Licens  
e  
  
  
**本文由奇安信编译，不代表奇安信观点。转载请注明“转自奇安信代码卫士 https://codesafe.qianxin.com”。**  
  
  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/oBANLWYScMSf7nNLWrJL6dkJp7RB8Kl4zxU9ibnQjuvo4VoZ5ic9Q91K3WshWzqEybcroVEOQpgYfx1uYgwJhlFQ/640?wx_fmt=jpeg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/oBANLWYScMSN5sfviaCuvYQccJZlrr64sRlvcbdWjDic9mPQ8mBBFDCKP6VibiaNE1kDVuoIOiaIVRoTjSsSftGC8gw/640?wx_fmt=jpeg "")  
  
**奇安信代码卫士 (codesafe)**  
  
国内首个专注于软件开发安全的产品线。  
  
   ![](https://mmbiz.qpic.cn/mmbiz_gif/oBANLWYScMQ5iciaeKS21icDIWSVd0M9zEhicFK0rbCJOrgpc09iaH6nvqvsIdckDfxH2K4tu9CvPJgSf7XhGHJwVyQ/640?wx_fmt=gif "")  
![]( "")  
![]( "")  
  
   
觉得不错，就点个 “  
在看  
” 或 "  
赞  
”   
  

#  一文讲透新人如何上手AI漏洞挖掘  
原创 0x八月
                    0x八月  0x八月   2026-07-06 12:53  
  
⚠️  
  
    请勿利用文章内的相关技术从事  
**非法渗透测试**  
，由于传播、利用此文所提供的信息而造成的任何直接或者间接的后果及损失，均由使用者本人负责，作者不为此承担任何责任。  
**工具和内容均来自网络，仅做学习和记录使用，安全性自测，如有侵权请联系删除，**推荐的Skills/MCP/工具，建议大家先进行检测分析或者在沙箱/虚拟机等使用确保无误后，在进行日常工作或者本机使用，避免后门或者投毒受到影响。  
  
⚠️注意：现在只对常读和星标的公众号才展示大图推送，建议大家把"  
**0x八月**  
"设为星标⭐️"否则可能就看不到了啦  
,  
点击下方卡片关注我哦！  
  
**💡项目地址在文章底部哦！**  
  
  
>   内容来自洺熙的佬的 新人AI挖洞实战指南_纯净版.PPTX文件，仅供学习使用，由听风笔记Skills提炼而来  
  
  
# 一文讲透新人如何上手AI漏洞挖掘  
## 封面  
  
![](https://mmbiz.qpic.cn/mmbiz_png/L9cic5ql9ODykxAR0JgUZRS1vGBsox54PDLLBlia3iaNDUqicFFjtayA5tPeeeRDro3EcKsHtLibP5kE8kSTzJELkPFE5NBkmiaC2z6ibxyo1kWMbk/640?wx_fmt=png&from=appmsg "")  
## 一、为什么学AI挖洞  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/L9cic5ql9ODxI9zQmIjrRMOLXd9PGwkH9ZicA4KkSeFwxs8PUeV0hbskxm4AAukxOKJvfrvKMfQ6ibGu8lFZjvuicx2vKcibPz5uiaYHFneQcNeaE/640?wx_fmt=png&from=appmsg "")  
## 二、Claude Code 入门  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/L9cic5ql9ODykiaiaX4b9Bkax7HAstFqDsFglaWqMDBQsr56TZQVE6pc9uAPCfp7oGg5Wnujicxp8ZJosibFldaaFibicXSSvrJDEA7TJJ8kefRaeM/640?wx_fmt=png&from=appmsg "")  
## 三、CLAUDE.md 项目记忆卡  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/L9cic5ql9ODwUQVo2qBM6Pzkh9IdfNWrYGOqricdiazOY7LD60HDLLVDTu1cbcvyVVbyEqBGLVgwcqHbmiatlMsyppvB317ZhtOfA5Coficia7LOQ/640?wx_fmt=png&from=appmsg "")  
## 四、MCP 与 Playwright 工具链  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/L9cic5ql9ODzVlMInWnoBIqOTaBUdicVAMvbXgUBxibslCsPxAwbq9CD5IUH84AtUpR9N3KRTw3OrtwIuu40zduqmo5bSiaxFqjV4Ht0uG7xC3E/640?wx_fmt=png&from=appmsg "")  
## 五、Skill 编写指南  
  
![](https://mmbiz.qpic.cn/mmbiz_png/L9cic5ql9ODzrxXDD8YeE2AUmupluKePaOGqcPicgpRfgma6AN14MIS4ZUcyS53zicHlWwXxLbz04XopZN0pOSdBwKazHHUQK2NBXPaibFibngBg/640?wx_fmt=png&from=appmsg "")  
## 六、实战工作流：从线索到报告  
  
![](https://mmbiz.qpic.cn/mmbiz_png/L9cic5ql9ODy9yjibz151bS1zRuBjjyZ6FS2qUlbearMUJYIJ90DLqTz6qydwaRfe28PhIsXFZbL4oh00XSkjliaXjsicoRr5bsG4HLoBb106kk/640?wx_fmt=png&from=appmsg "")  
## 七、防幻觉与验证  
  
![](https://mmbiz.qpic.cn/mmbiz_png/L9cic5ql9ODzEHA6fblV6Hap1Yul07Z99iahEjBp6tEgFUkvC5B5x2vjIKZBWuNQMB3h04yAPafZG726ibRLNGsnLkTDvBO0icuSgjRbicuSJs4E/640?wx_fmt=png&from=appmsg "")  
## 八、AI能做什么 vs 不能做什么  
  
![](https://mmbiz.qpic.cn/mmbiz_png/L9cic5ql9ODxOLk1iablbYNicNh02jkEiaD3TTbqSFRpAZtKau3PUm64JIfAJo7omPdFSibAzN4fkBC19w51ibnXice6WQ9HFYWAo7FUqpApX7uhsE/640?wx_fmt=png&from=appmsg "")  
## 九、四种人机协作模式  
  
![](https://mmbiz.qpic.cn/mmbiz_png/L9cic5ql9ODznyjqBdZHZwPvWeusuItRBmtGZ7DNLeux6gImb6gG43LnZwYcBCq7grN0YbevzJ6rvhfW8fUmL63GY9UJhOia1WcPefLyo279I/640?wx_fmt=png&from=appmsg "")  
## 十、GET STARTED  
  
![](https://mmbiz.qpic.cn/mmbiz_png/L9cic5ql9ODxjFGZMbE9xFAzZkKIjA8bCsteQDguY82zTUdhJVzOQcC2URyJTUTLmSXCAXwKRJnxWJcso6kicibANJHibuMbtzVZK1hR1pFxM84/640?wx_fmt=png&from=appmsg "")  
## 完整长图  
  
![](https://mmbiz.qpic.cn/mmbiz_png/L9cic5ql9ODzK1nNwokufkrxhhHibsf93icIZj9JjtSBYkqme9rVHyIxDMAjn3jByXBuHFvskJBzwUUyhhr33DV9AEVaicNxdQDPCdb48iakUUBE/640?wx_fmt=png&from=appmsg "")  
## 💻 技术交流与学习  
  
  
  
      
如果师傅们想要第一时间获取到  
**最新的威胁情报**  
，可以添加下面我创建的  
**钉钉漏洞威胁情报群**  
，便于师傅们可以及时获取最新的  
**IOC**  
。  
  
    如果师傅们想要获取网络安全相关知识内容，可以添加下面我创建的  
**网络安全全栈知识库**  
，便于师傅们的学习和使用：  
  
覆盖渗透、安服、运营、代码审计、内网、移动、应急、工控、AI/LLM、数据、业务、情报、黑灰产、SRC、溯源、钓鱼、区块链等  方向，  
**内容还在持续整理中......**  
。  
  
⚠️  
打广告的勿进，会直接踢掉！！！  
  
<table><thead><tr style="border-width: 0px;border-style: none;border-color: currentcolor;background-color: transparent;"><th style="padding: 12px 16px;border-width: 2px;border-color: rgb(26, 26, 26);background-color: rgb(255, 217, 61);text-transform: uppercase;letter-spacing: 0.02em;font-size: 14px;min-width: 85px;text-align: center;"><span style="font-size: 16px;color: rgb(0, 0, 0);"><span leaf="">网络安全全栈知识库</span></span></th><th style="padding: 12px 16px;border-width: 2px;border-color: rgb(26, 26, 26);background-color: rgb(255, 217, 61);text-transform: uppercase;letter-spacing: 0.02em;font-size: 14px;min-width: 85px;text-align: center;"><span style="font-size: 16px;color: rgb(0, 0, 0);"><span leaf="">钉钉漏洞威胁情报群</span></span></th></tr></thead><tbody><tr style="border-width: 0px;border-style: none;border-color: currentcolor;background-color: transparent;"><td style="padding: 12px 16px;font-size: 15px;border-width: 2px;border-color: rgb(26, 26, 26);color: rgb(45, 45, 45);font-weight: 600;min-width: 85px;text-align: center;"><section nodeleaf=""><img data-src="https://mmbiz.qpic.cn/mmbiz_png/L9cic5ql9ODzDcbialtDJB2iauRibULjWbzQk2oeHEyuNcGjibhWw6SpJia0RYGY3D7UhMASYr1QPAicb1LaSL1XlDrVowaibjeB41IKYSBHE8z9sN8/640?wx_fmt=png&amp;from=appmsg" class="rich_pages wxw-img" data-ratio="0.7916666666666666" data-type="png" data-w="1080" style="margin: 32px auto;padding: 8px;font-size: 14px;font-weight: 700;letter-spacing: 0.28px;text-align: left;text-transform: uppercase;white-space: normal;display: block;border-radius: 4px;border-width: 4px;border-style: solid;border-color: rgb(26, 26, 26);box-shadow: rgb(26, 26, 26) 8px 8px 0px;background: none rgb(255, 255, 255);transform: rotate(-1deg);transition: transform 0.3s;width: 100%;height: auto;" data-backw="85.33333999999999" data-backh="68.33333999999999" data-imgfileid="100003894" data-aistatus="1"/></section></td><td style="padding: 12px 16px;font-size: 15px;border-width: 2px;border-color: rgb(26, 26, 26);color: rgb(45, 45, 45);font-weight: 600;min-width: 85px;text-align: center;"><section nodeleaf=""><img data-src="https://mmbiz.qpic.cn/sz_mmbiz_png/L9cic5ql9ODwMu4dL9ZhibwZKibzwdD01Btq6ia2183uH0ibzaGibkr1aribDe1jicrtW0px8pd6Rz1kT7QpTtzfdmicibiaFZHSqI40srWZhLQ9HpR1JY/640?wx_fmt=png&amp;from=appmsg" class="rich_pages wxw-img" data-ratio="0.625" data-type="png" data-w="1080" style="margin: 32px auto;padding: 8px;font-size: 14px;font-weight: 700;letter-spacing: 0.28px;text-align: left;text-transform: uppercase;white-space: normal;display: block;border-radius: 4px;border-width: 4px;border-style: solid;border-color: rgb(26, 26, 26);box-shadow: rgb(26, 26, 26) 8px 8px 0px;background: none rgb(255, 255, 255);transform: rotate(-1deg);transition: transform 0.3s;width: 100%;height: auto;" data-backw="204.33334" data-backh="128.33334" data-imgfileid="100003895" data-aistatus="1"/></section></td></tr></tbody></table>  
  
### 推荐阅读  
  
  
✦ ✦ ✦  
  
<table><thead><tr style="border-width: 0px;border-style: initial;border-color: initial;background-color: transparent;"><th data-colwidth="439.3333333333333" width="439.3333333333333" style="padding: 12px 16px;border-width: 2px;border-color: rgb(26, 26, 26);background: rgb(255, 217, 61);text-align: left;text-transform: uppercase;letter-spacing: 0.02em;font-size: 14px;min-width: 85px;word-break: break-all;"><section style="text-align: center;"><span leaf=""><a class="normal_text_link mp_article_text_link" target="_blank" style="color: rgb(255, 107, 107);border-bottom: 3px solid rgb(255, 217, 61);background: linear-gradient(transparent 70%, rgba(255, 217, 61, 0.3) 0px);transition: 0.2s;" href="https://mp.weixin.qq.com/s?__biz=MzE5ODgwNzgzMA==&amp;mid=2247485592&amp;idx=1&amp;sn=818004a6d625c4c4112ce73b83433854&amp;scene=21#wechat_redirect" textvalue="" linktype="text" data-linktype="2">渗透测试人员必备武器库：子域名爆破、漏洞扫描、内网渗透、工控安全工具全收录</a></span></section></th></tr></thead><tbody><tr style="border-width: 0px;border-style: initial;border-color: initial;background-color: transparent;"><td data-colwidth="459.3333333333333" width="459.3333333333333" style="padding: 12px 16px;border-width: 2px;border-color: rgb(26, 26, 26);font-size: 15px;color: rgb(45, 45, 45);font-weight: 600;min-width: 85px;"><section><span leaf=""><a class="normal_text_link mp_article_text_link" target="_blank" style="color: rgb(255, 107, 107);border-bottom: 3px solid rgb(255, 217, 61);background: linear-gradient(transparent 70%, rgba(255, 217, 61, 0.3) 0px);transition: 0.2s;" href="https://mp.weixin.qq.com/s?__biz=MzE5ODgwNzgzMA==&amp;mid=2247485309&amp;idx=1&amp;sn=292afbe37fb95c64f33470f915b0c54e&amp;scene=21#wechat_redirect" textvalue="" linktype="text" data-linktype="2">AI驱动的自动化红队编排框架(AutoRedTeam-Orchestrator)跨平台支持，集成 130+ 安全工具与 2000+ Payload</a></span></section></td></tr><tr style="border-width: 0px;border-style: initial;border-color: initial;background-color: rgb(255, 249, 196);"><td data-colwidth="459.3333333333333" width="459.3333333333333" style="padding: 12px 16px;border-width: 2px;border-color: rgb(26, 26, 26);font-size: 15px;color: rgb(45, 45, 45);font-weight: 600;min-width: 85px;"><section><span leaf=""><a class="normal_text_link mp_article_text_link" target="_blank" style="color: rgb(255, 107, 107);border-bottom: 3px solid rgb(255, 217, 61);background: linear-gradient(transparent 70%, rgba(255, 217, 61, 0.3) 0px);transition: 0.2s;" href="https://mp.weixin.qq.com/s?__biz=MzE5ODgwNzgzMA==&amp;mid=2247486181&amp;idx=1&amp;sn=3ace47da643c72cec0d615aeccb955ac&amp;scene=21#wechat_redirect" textvalue="" linktype="text" data-linktype="2">JS逆向必备：这款插件能Bypass Debugger、Hook CryptoJS、抓取路由</a></span></section></td></tr><tr style="border-width: 0px;border-style: initial;border-color: initial;background-color: transparent;"><td data-colwidth="439.3333333333333" width="439.3333333333333" style="padding: 12px 16px;border-width: 2px;border-color: rgb(26, 26, 26);font-size: 15px;color: rgb(45, 45, 45);font-weight: 600;min-width: 85px;"><section><span leaf=""><a class="normal_text_link mp_article_text_link" target="_blank" style="color: rgb(255, 107, 107);border-bottom: 3px solid rgb(255, 217, 61);background: linear-gradient(transparent 70%, rgba(255, 217, 61, 0.3) 0px);transition: 0.2s;" href="https://mp.weixin.qq.com/s?__biz=MzE5ODgwNzgzMA==&amp;mid=2247485488&amp;idx=1&amp;sn=a37acb031febe69db608de53ddee5732&amp;scene=21#wechat_redirect" textvalue="" linktype="text" data-linktype="2">上传代码即审计：AI 驱动的自动化漏洞挖掘与 POC 验证平台</a></span></section></td></tr><tr style="border-width: 0px;border-style: initial;border-color: initial;background-color: rgb(255, 249, 196);"><td data-colwidth="439.3333333333333" width="439.3333333333333" style="padding: 12px 16px;border-width: 2px;border-color: rgb(26, 26, 26);font-size: 15px;color: rgb(45, 45, 45);font-weight: 600;min-width: 85px;"><section><span leaf=""><a class="normal_text_link mp_article_text_link" target="_blank" style="color: rgb(255, 107, 107);border-bottom: 3px solid rgb(255, 217, 61);background: linear-gradient(transparent 70%, rgba(255, 217, 61, 0.3) 0px);transition: 0.2s;" href="https://mp.weixin.qq.com/s?__biz=MzE5ODgwNzgzMA==&amp;mid=2247485208&amp;idx=1&amp;sn=b5181181c1e0800124e3e099706ef2ef&amp;scene=21#wechat_redirect" textvalue="" linktype="text" data-linktype="2">AI 原生安全测试平台(CyberStrikeAI)</a></span></section></td></tr><tr style="border-width: 0px;border-style: initial;border-color: initial;background-color: transparent;"><td data-colwidth="459.3333333333333" width="459.3333333333333" style="padding: 12px 16px;border-width: 2px;border-color: rgb(26, 26, 26);font-size: 15px;color: rgb(45, 45, 45);font-weight: 600;min-width: 85px;"><section><span leaf=""><a class="normal_text_link mp_article_text_link" target="_blank" style="color: rgb(255, 107, 107);border-bottom: 3px solid rgb(255, 217, 61);background: linear-gradient(transparent 70%, rgba(255, 217, 61, 0.3) 0px);transition: 0.2s;" href="https://mp.weixin.qq.com/s?__biz=MzE5ODgwNzgzMA==&amp;mid=2247485805&amp;idx=1&amp;sn=8f374a239135f6a753d5cce887f8318b&amp;scene=21#wechat_redirect" textvalue="" linktype="text" data-linktype="2">多Agent智能协作+40+工具调用：基于大模型的端到端自动化漏洞挖掘与验证系统</a></span></section></td></tr><tr style="border-width: 0px;border-style: initial;border-color: initial;background-color: rgb(255, 249, 196);"><td data-colwidth="459.3333333333333" width="459.3333333333333" style="padding: 12px 16px;border-width: 2px;border-color: rgb(26, 26, 26);font-size: 15px;color: rgb(45, 45, 45);font-weight: 600;min-width: 85px;"><section><span leaf=""><a class="normal_text_link mp_article_text_link" target="_blank" style="color: rgb(255, 107, 107);border-bottom: 3px solid rgb(255, 217, 61);background: linear-gradient(transparent 70%, rgba(255, 217, 61, 0.3) 0px);transition: 0.2s;" href="https://mp.weixin.qq.com/s?__biz=MzE5ODgwNzgzMA==&amp;mid=2247485314&amp;idx=1&amp;sn=56082cd314311ffc15cc0bcf03a395e2&amp;scene=21#wechat_redirect" textvalue="" linktype="text" data-linktype="2">基于DeepSeek的代码审计工具 (Ai-SAST-tool.xjar)</a></span></section></td></tr><tr style="border-width: 0px;border-style: initial;border-color: initial;background-color: transparent;"><td data-colwidth="459.3333333333333" width="459.3333333333333" style="padding: 12px 16px;border-width: 2px;border-color: rgb(26, 26, 26);font-size: 15px;color: rgb(45, 45, 45);font-weight: 600;min-width: 85px;"><section><span leaf=""><a class="normal_text_link mp_article_text_link" target="_blank" style="color: rgb(255, 107, 107);border-bottom: 3px solid rgb(255, 217, 61);background: linear-gradient(transparent 70%, rgba(255, 217, 61, 0.3) 0px);transition: 0.2s;" href="https://mp.weixin.qq.com/s?__biz=MzE5ODgwNzgzMA==&amp;mid=2247485127&amp;idx=1&amp;sn=b5eb3fdc1cc23976011e2bca396c1bc7&amp;scene=21#wechat_redirect" textvalue="" linktype="text" data-linktype="2">基于AI的自主渗透测试平台 </a></span></section></td></tr></tbody></table>  
  
✦ ✦ ✦  
  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/AXRefkPRWsEZqurn2l5WTaTjyicrUtIJnAqueibZX8s1IJDIlA8UJmu3uWsZUxqahoolciaqq65A30ia93jCyEwTLA/640?wx_fmt=gif&from=appmsg "")  
  
**点分享**  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/AXRefkPRWsEZqurn2l5WTaTjyicrUtIJniaq4LXsS43znk18DicsT6LtgMylx4w69DNNhsia1nyw4qEtEFnADmSLPg/640?wx_fmt=gif&from=appmsg "")  
  
**点收藏**  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/AXRefkPRWsEZqurn2l5WTaTjyicrUtIJnev2xbu5ega5oFianDp0DBuVwibRZ8Ro1BGp4oxv0JOhDibNQzlSsku9ng/640?wx_fmt=gif&from=appmsg "")  
  
**点在看**  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/AXRefkPRWsEZqurn2l5WTaTjyicrUtIJnwVncsEYvPhsCdoMYkI6PAHJQq4tEiaK3fcm3HGLialEMuMwKnnwwSibyA/640?wx_fmt=gif&from=appmsg "")  
  
**点点赞**  
  

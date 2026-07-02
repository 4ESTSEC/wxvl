#  AI赋能挖掘&&某edusrc小程序任意用户登录漏洞  
 黑白之道   2026-07-02 00:43  
  
免责声明  
```
由于传播、利用本公众号所提供的信息而造成
的任何直接或者间接的后果及损失，均由使用
者本人负责，公众号陌笙不太懂安全及作者不
为此承担任何责任，一旦造成后果请自行承担！
如有侵权烦请告知，我们会立即删除并致歉，谢谢！
```  
  
前言  
  
圈友的点，拿来学习一手，挖掘过程遇到了很多问题，通过交流也学习到了，很多之前不太了解的内容，交流是个好东西。  
  
信息收集  
  
直接搜索学校的名字，会看到很多学校相关的小程序，我们来到红色箭头这个小程序这里，这个就是本次的挖掘目标  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboTibh60gwvqqG65kic00sHvUIsRw8UvzFJQ56vhD5psomjmqmbZWgzkKeFP73GoJ6rpyn8LS6FwWU4lAVZ5WLnib0532sRqbKHWibw/640?wx_fmt=png&from=appmsg "")  
  
edusrc挖掘的话，小程序信息收集，直接丢学校名字在搜一搜里面，  
  
搜到公众号打公众号，有小程序打小程序就行，非常适合，天天感觉  
  
自己找不到边缘资产的师傅，直接跳过信息收集，挖就完了。  
  
漏洞测试  
  
点击小程序进行  
  
查看，发现有很多功能，但是都是需要登录的  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboQ29FS8SibgTkzSXIstwF21FFK7yH0icoZpdlMbxXzzia5oVu8aXwI9KxnNQxz4L55KvpR8WneWgPdKyM2WVHJJO7UeGCP19aFR3U/640?wx_fmt=png&from=appmsg "")  
  
我们点击登录  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboTyrztJAtlccteRY3YGy9gSiaibuNmu8vQhYib2aZE1wxd9WMus8C0NO76WKSI2fh6HxfZicpcGNZypFLdnwoSmwrlYIicUtG6CAnYs/640?wx_fmt=png&from=appmsg "")  
  
在点击获取手机号登录，看到了这  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboS8hPtozoBYPI9Za4sGjoAsOKyra7Ss9OXRzupM0YZf7aQPX7yUQMMC8DjznSTibicz0Qj6Hic3fVsDglkq7YyOlic3jWtnVPhsicFM/640?wx_fmt=png&from=appmsg "")  
  
一般看到这个，经常打小程序的师傅  
  
点击允许拦截  
burp  
数据包，可以看到存在这三个参数  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboRUuJkPjvxoVFuKyyM2mQHnTicr2SFTrgxIhrjGremzMgX9ZnPrBKFU8oQuuMYBV7qTwtyjaLCdniarfbI4s0JAVNMvVNMVfMNwo/640?wx_fmt=png&from=appmsg "")  
## 微信小程序 session_key 泄露导致任意用户登录漏洞分析  
### 1. 漏洞概述  
  
该漏洞属于  
**身份认证绕过**  
类型。当微信小程序的   
session_key  
、  
encryptedData  
、  
iv  
 三个参数同时被攻击者获取时，攻击者可通过解密、篡改、重加密的方式，伪造任意用户的授权数据，从而实现  
**无需密码的任意用户登录**  
。  
  
1.1 漏洞标识  
  
<table><thead><tr style="box-sizing: border-box;break-inside: avoid;break-after: auto;border: 1px solid rgb(223, 226, 229);margin: 0px;padding: 0px;"><th style="box-sizing: border-box;padding: 6px 13px;font-weight: bold;border-width: 1px 1px 0px;border-top-style: solid;border-right-style: solid;border-left-style: solid;border-top-color: rgb(223, 226, 229);border-right-color: rgb(223, 226, 229);border-left-color: rgb(223, 226, 229);border-image: initial;border-bottom-style: initial;border-bottom-color: initial;margin: 0px;"><span cid="n24" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 229.225px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">项目</span></span></span></th><th style="box-sizing: border-box;padding: 6px 13px;font-weight: bold;border-width: 1px 1px 0px;border-top-style: solid;border-right-style: solid;border-left-style: solid;border-top-color: rgb(223, 226, 229);border-right-color: rgb(223, 226, 229);border-left-color: rgb(223, 226, 229);border-image: initial;border-bottom-style: initial;border-bottom-color: initial;margin: 0px;"><span cid="n25" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 679.775px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">内容</span></span></span></th></tr></thead><tbody><tr style="box-sizing: border-box;break-inside: avoid;break-after: auto;border: 1px solid rgb(223, 226, 229);margin: 0px;padding: 0px;"><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n27" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 229.225px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">漏洞类型</span></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n28" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 679.775px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">身份认证绕过 / 会话劫持</span></span></span></td></tr><tr style="box-sizing: border-box;break-inside: avoid;break-after: auto;border: 1px solid rgb(223, 226, 229);margin: 0px;padding: 0px;background-color: rgb(248, 248, 248);"><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n30" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 229.225px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">威胁等级</span></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n31" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 679.775px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">高危</span></span></span></td></tr><tr style="box-sizing: border-box;break-inside: avoid;break-after: auto;border: 1px solid rgb(223, 226, 229);margin: 0px;padding: 0px;"><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n33" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 229.225px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">利用条件</span></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n34" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 679.775px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">三个参数同时泄露</span></span></span></td></tr><tr style="box-sizing: border-box;break-inside: avoid;break-after: auto;border: 1px solid rgb(223, 226, 229);margin: 0px;padding: 0px;background-color: rgb(248, 248, 248);"><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n36" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 229.225px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">影响范围</span></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n37" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 679.775px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">存在参数泄露的小程序及其用户</span></span></span></td></tr></tbody></table>## 2. 核心概念解释  
  
<table><thead><tr style="box-sizing: border-box;break-inside: avoid;break-after: auto;border: 1px solid rgb(223, 226, 229);margin: 0px;padding: 0px;"><th style="box-sizing: border-box;padding: 6px 13px;font-weight: bold;border-width: 1px 1px 0px;border-top-style: solid;border-right-style: solid;border-left-style: solid;border-top-color: rgb(223, 226, 229);border-right-color: rgb(223, 226, 229);border-left-color: rgb(223, 226, 229);border-image: initial;border-bottom-style: initial;border-bottom-color: initial;margin: 0px;"><span cid="n42" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 161.762px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">参数</span></span></span></th><th style="box-sizing: border-box;padding: 6px 13px;font-weight: bold;border-width: 1px 1px 0px;border-top-style: solid;border-right-style: solid;border-left-style: solid;border-top-color: rgb(223, 226, 229);border-right-color: rgb(223, 226, 229);border-left-color: rgb(223, 226, 229);border-image: initial;border-bottom-style: initial;border-bottom-color: initial;margin: 0px;"><span cid="n43" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 422.25px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">含义</span></span></span></th><th style="box-sizing: border-box;padding: 6px 13px;font-weight: bold;border-width: 1px 1px 0px;border-top-style: solid;border-right-style: solid;border-left-style: solid;border-top-color: rgb(223, 226, 229);border-right-color: rgb(223, 226, 229);border-left-color: rgb(223, 226, 229);border-image: initial;border-bottom-style: initial;border-bottom-color: initial;margin: 0px;"><span cid="n44" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 297.987px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">正常存储位置</span></span></span></th></tr></thead><tbody><tr style="box-sizing: border-box;break-inside: avoid;break-after: auto;border: 1px solid rgb(223, 226, 229);margin: 0px;padding: 0px;"><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n46" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 161.762px;min-height: 10px;"><span md-inline="code" spellcheck="false" style="box-sizing: border-box;"><code style="box-sizing: border-box;font-family: var(--monospace);text-align: left;vertical-align: initial;border: 1px solid rgb(231, 234, 237);background-color: rgb(243, 244, 244);border-radius: 3px;padding: 0px 2px;font-size: 0.9em;"><span leaf="">encryptedData</span></code></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n47" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 422.25px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">微信返回的用户信息密文（如手机号、昵称）</span></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n48" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 297.987px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">客户端临时持有，提交后端解密</span></span></span></td></tr><tr style="box-sizing: border-box;break-inside: avoid;break-after: auto;border: 1px solid rgb(223, 226, 229);margin: 0px;padding: 0px;background-color: rgb(248, 248, 248);"><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n50" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 161.762px;min-height: 10px;"><span md-inline="code" spellcheck="false" style="box-sizing: border-box;"><code style="box-sizing: border-box;font-family: var(--monospace);text-align: left;vertical-align: initial;border: 1px solid rgb(231, 234, 237);background-color: rgb(243, 244, 244);border-radius: 3px;padding: 0px 2px;font-size: 0.9em;"><span leaf="">iv</span></code></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n51" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 422.25px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">加密算法所需的初始向量</span></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n52" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 297.987px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">随 </span></span><span md-inline="code" spellcheck="false" style="box-sizing: border-box;"><code style="box-sizing: border-box;font-family: var(--monospace);text-align: left;vertical-align: initial;border: 1px solid rgb(231, 234, 237);background-color: rgb(243, 244, 244);border-radius: 3px;padding: 0px 2px;font-size: 0.9em;"><span leaf="">encryptedData</span></code></span><span md-inline="plain" style="box-sizing: border-box;"><span leaf=""> 一同提交</span></span></span></td></tr><tr style="box-sizing: border-box;break-inside: avoid;break-after: auto;border: 1px solid rgb(223, 226, 229);margin: 0px;padding: 0px;"><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n54" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 161.762px;min-height: 10px;"><span md-inline="code" spellcheck="false" style="box-sizing: border-box;"><code style="box-sizing: border-box;font-family: var(--monospace);text-align: left;vertical-align: initial;border: 1px solid rgb(231, 234, 237);background-color: rgb(243, 244, 244);border-radius: 3px;padding: 0px 2px;font-size: 0.9em;"><span leaf="">session_key</span></code></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n55" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 422.25px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">会话密钥，用于解密 </span></span><span md-inline="code" spellcheck="false" style="box-sizing: border-box;"><code style="box-sizing: border-box;font-family: var(--monospace);text-align: left;vertical-align: initial;border: 1px solid rgb(231, 234, 237);background-color: rgb(243, 244, 244);border-radius: 3px;padding: 0px 2px;font-size: 0.9em;"><span leaf="">encryptedData</span></code></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n56" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 297.987px;min-height: 10px;"><span md-inline="strong" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">仅限后端存储，严禁返回客户端</span></span></strong></span></span></td></tr><tr style="box-sizing: border-box;break-inside: avoid;break-after: auto;border: 1px solid rgb(223, 226, 229);margin: 0px;padding: 0px;background-color: rgb(248, 248, 248);"><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n58" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 161.762px;min-height: 10px;"><span md-inline="code" spellcheck="false" style="box-sizing: border-box;"><code style="box-sizing: border-box;font-family: var(--monospace);text-align: left;vertical-align: initial;border: 1px solid rgb(231, 234, 237);background-color: rgb(243, 244, 244);border-radius: 3px;padding: 0px 2px;font-size: 0.9em;"><span leaf="">openId</span></code></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n59" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 422.25px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">用户在特定小程序中的唯一标识</span></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;"><span cid="n60" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 297.987px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">后端存储</span></span></span></td></tr></tbody></table>## 3. 攻击原理  
### 3.1 正常登录流程（标准设计）  
```
```  
  
这里我们直接使用工具加泄露的iv和key对进行encrypdata解密，发现不太行  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboTZOEVOR3PE3P5FkGmhcNAmnqV0WZu7rwJ3CTTT4B9DamrQiaHUUrc8xrh0ggAvAs2ypOalCkYZB1ovsgkj7NYmK81Rl426NJibU/640?wx_fmt=png&from=appmsg "")  
  
对key和iv进行解url码  
  
https://www.toolhelper.cn/EncodeDecode/Url  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboTIiayxvc6IWOrh3djH14Z8TqyOcg4yMmWXicRyO5F7iaDna3YwY1HYFbiazOnTaUrq4BeovCWv40ggaZ2azOx3sheYa5qJI8xu23A/640?wx_fmt=png&from=appmsg "")  
  
发现再次解密显示这个，这里就到我的知识盲区了，这个时候  
  
圈友告诉我这这两个值长度不对，是通过加密或者其他方式进行后期处理的  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboQOKmgxUkAibSVKTKQloBy0T5xljibiaia3qzXEL7xI2CIvpypPibq4RNCJz2M5Wic3nscicOJxAdNzI7mpyWh2e2xfmatrORhrofbtia8/640?wx_fmt=png&from=appmsg "")  
## 知识点  
  
根据微信小程序官方文档及 AES-CBC 加密标准，  
session_key  
 与   
iv  
 的长度规范如下：  
  
<table><thead><tr style="box-sizing: border-box;break-inside: avoid;break-after: auto;border: 1px solid rgb(223, 226, 229);margin: 0px;padding: 0px;"><th style="box-sizing: border-box;padding: 6px 13px;font-weight: bold;border-width: 1px 1px 0px;border-top-style: solid;border-right-style: solid;border-left-style: solid;border-top-color: rgb(223, 226, 229);border-right-color: rgb(223, 226, 229);border-left-color: rgb(223, 226, 229);border-image: initial;border-bottom-style: initial;border-bottom-color: initial;margin: 0px;text-align: left;"><span cid="n76" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 135.587px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">参数</span></span></span></th><th style="box-sizing: border-box;padding: 6px 13px;font-weight: bold;border-width: 1px 1px 0px;border-top-style: solid;border-right-style: solid;border-left-style: solid;border-top-color: rgb(223, 226, 229);border-right-color: rgb(223, 226, 229);border-left-color: rgb(223, 226, 229);border-image: initial;border-bottom-style: initial;border-bottom-color: initial;margin: 0px;text-align: left;"><span cid="n77" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 88.175px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">编码格式</span></span></span></th><th style="box-sizing: border-box;padding: 6px 13px;font-weight: bold;border-width: 1px 1px 0px;border-top-style: solid;border-right-style: solid;border-left-style: solid;border-top-color: rgb(223, 226, 229);border-right-color: rgb(223, 226, 229);border-left-color: rgb(223, 226, 229);border-image: initial;border-bottom-style: initial;border-bottom-color: initial;margin: 0px;text-align: left;"><span cid="n78" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 219.925px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">编码后长度（Base64）</span></span></span></th><th style="box-sizing: border-box;padding: 6px 13px;font-weight: bold;border-width: 1px 1px 0px;border-top-style: solid;border-right-style: solid;border-left-style: solid;border-top-color: rgb(223, 226, 229);border-right-color: rgb(223, 226, 229);border-left-color: rgb(223, 226, 229);border-image: initial;border-bottom-style: initial;border-bottom-color: initial;margin: 0px;text-align: left;"><span cid="n79" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 209.688px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">解码后长度（二进制）</span></span></span></th><th style="box-sizing: border-box;padding: 6px 13px;font-weight: bold;border-width: 1px 1px 0px;border-top-style: solid;border-right-style: solid;border-left-style: solid;border-top-color: rgb(223, 226, 229);border-right-color: rgb(223, 226, 229);border-left-color: rgb(223, 226, 229);border-image: initial;border-bottom-style: initial;border-bottom-color: initial;margin: 0px;text-align: left;"><span cid="n80" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 174.625px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">算法要求</span></span></span></th></tr></thead><tbody><tr style="box-sizing: border-box;break-inside: avoid;break-after: auto;border: 1px solid rgb(223, 226, 229);margin: 0px;padding: 0px;"><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;text-align: left;"><span cid="n82" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 135.587px;min-height: 10px;"><span md-inline="code" spellcheck="false" style="box-sizing: border-box;"><code style="box-sizing: border-box;font-family: var(--monospace);text-align: left;vertical-align: initial;border: 1px solid rgb(231, 234, 237);background-color: rgb(243, 244, 244);border-radius: 3px;padding: 0px 2px;font-size: 0.9em;"><span leaf="">session_key</span></code></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;text-align: left;"><span cid="n83" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 88.175px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">Base64</span></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;text-align: left;"><span cid="n84" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 219.925px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">24 字符</span></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;text-align: left;"><span cid="n85" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 209.688px;min-height: 10px;"><span md-inline="strong" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">16 字节（128位）</span></span></strong></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;text-align: left;"><span cid="n86" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 174.625px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">AES-128-CBC 密钥</span></span></span></td></tr><tr style="box-sizing: border-box;break-inside: avoid;break-after: auto;border: 1px solid rgb(223, 226, 229);margin: 0px;padding: 0px;background-color: rgb(248, 248, 248);"><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;text-align: left;"><span cid="n88" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 135.587px;min-height: 10px;"><span md-inline="code" spellcheck="false" style="box-sizing: border-box;"><code style="box-sizing: border-box;font-family: var(--monospace);text-align: left;vertical-align: initial;border: 1px solid rgb(231, 234, 237);background-color: rgb(243, 244, 244);border-radius: 3px;padding: 0px 2px;font-size: 0.9em;"><span leaf="">iv</span></code></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;text-align: left;"><span cid="n89" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 88.175px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">Base64</span></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;text-align: left;"><span cid="n90" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 219.925px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">24 字符</span></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;text-align: left;"><span cid="n91" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 209.688px;min-height: 10px;"><span md-inline="strong" style="box-sizing: border-box;"><strong style="box-sizing: border-box;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">16 字节（128位）</span></span></strong></span></span></td><td style="box-sizing: border-box;padding: 6px 13px;border: 1px solid rgb(223, 226, 229);margin: 0px;min-width: 32px;text-align: left;"><span cid="n92" mdtype="table_cell" style="box-sizing: border-box;display: inline-block;min-width: 1ch;width: 174.625px;min-height: 10px;"><span md-inline="plain" style="box-sizing: border-box;"><span leaf="">AES-CBC 块大小</span></span></span></td></tr></tbody></table>### 2. 技术说明  
#### 2.1 session_key  
  
session_key  
 是微信服务器返回的会话密钥，用于解密   
encryptedData  
。其技术规格如下：  
- **算法**  
：AES-128-CBC  
  
- **密钥长度**  
：128 位（16 字节）  
  
- **传输编码**  
：Base64  
  
- **编码后长度**  
：固定为 24 个字符  
  
> **官方定义**  
：微信官方文档明确指出   
aeskey = Base64_Decode(session_key)  
 的结果为   
**16 字节**  
 的二进制数据。任何非此长度的实现均不符合规范，将导致解密失败。  
  
#### 2.2 iv  
  
iv  
（Initialization Vector，初始向量）是 AES-CBC 模式下的必需参数，用于增加密文的随机性。其技术规格如下：  
- **算法**  
：AES-CBC  
  
- **块大小**  
：128 位（16 字节）  
  
- **传输编码**  
：Base64  
  
- **编码后长度**  
：固定为 24 个字符  
  
> **官方定义**  
：  
iv  
 的解码结果必须为   
**16 字节**  
，否则解密接口将返回   
IV not 16 bytes long  
 错误  
  
  
这里显然不符合  
  
我们直接对小程序进行反编译  
  
这里我使用无影  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboTV8AslFxImq0aZpg7Kiba6VedC93tsRyicbhXm2qibpeJ0BziaWb8DuFpPa7ib2J6wVlHCgJRSnmKpvsPA2LvDTDrO754W0gDhU1Rs/640?wx_fmt=png&from=appmsg "")  
  
反编译后，来到源码的文件夹，打开claude，让他来分析  
  
（之前听过一节某大师傅挖掘银行众测的课有个漏洞就是这个，但是他是通过手写脚本手动分析逻辑进行加解密的，ai出来之后迅速拉进了我们和大佬打差距）  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboTiaFVFnWGPiaibRfT7JavSqwYqecialYutdjuwD0jCX1DZTXdxsgDKcq9XY1BiaLGibmQCFTBQ1ufq8qFCf5WD3LfSwsQp3NDtcflibs/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboTJ97aSnDND3CBWGYcfvPXbQBhkW96icarSEzoiaNyKeWNDw9LMVsdSogWVWLjWLo61ibDoeMGSFSUqlHUlg74e88UQ30gJtncaibc/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboSQtzDEHgIA7f01YTRmjkeG7boMhiarsKibpd7QcmFGGXYxImQZ2UK9rHdDkMABgIM6N32xeHUsGtUQDuqmonibLAPlwyQYmicg3As/640?wx_fmt=png&from=appmsg "")  
  
这里可以看到，这两个值确实是经过二次处理的，逻辑已经很清楚了  
  
这个时候我们就已经可以给他手机号让他重新加密然后任意用户登录了  
  
但是需要考虑考虑扩大危害  
  
反编译之后这里泄露了两个手机号  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboQwHFN1ojAdeQAjib27VFic5aWicoFa0R69jkAnSMwFw5x0icHbBFZfhSzkSHTWkACGeLCTVLqBTwmNfOP35VN1OicztYpDfn2v4MAc/640?wx_fmt=png&from=appmsg "")  
  
对这两个手机号进行登录尝试，结果真有一个是有管理权限的  
  
让ai对这个手机号逆向加密回去  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboQ5uEE105lkfgRrtBIaia5ZyNqnL8QaeWPgXmVFib6uRb9CMiciaBQhDvbHENQhy8kWgrtYiadWAknlQ3Eibg9Enzia4GGwFc5KanCC40/640?wx_fmt=png&from=appmsg "")  
  
我们直接复制加密后的内容来到登录处进行替换  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboQJPGDBGSpZHfrMejyicCXBLibI2VYRiakwbibHhBCh5f3pib8dRjVIMHGhEtRuIdnWicU5JsIeJfOyLvvpTytMPB6T834icdZtAcpxicY/640?wx_fmt=png&from=appmsg "")  
  
然后放包，直接进入小管理员的后台  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboR9PvicCCM8Dm5iby5HovMRTmyq8ot6pyLW8WEawRMKdMgJlJKgfuajBTc85lzUh50fk3UDI3gica0FN1WTZwKDz8XMc7b6fY5SwM/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboT3Cr9kwgI4gjwLfbgXmiahzM7DUFfGReibClAVvP4yznUnIa6wr8F4uAIiaBek7RywibtUYXrzbUDPspB6BxfX0nXJDp9Z8xJNL4Q/640?wx_fmt=png&from=appmsg "")  
  
这里继续扩大危害  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboTXia9zQ321wo0HA6zSRx4sH5LaHqXUUk5FNZzPhNBQGnCudXjUIg48qiaCWJ8FnQhlD6yicPB97FwT6T8oNVibqXbA1x8QTyKgvzo/640?wx_fmt=png&from=appmsg "")  
  
点击公寓管理  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboRgKjZOchfuka5I3vKgOIAw4kPBuiaIxvfLNicWpMpqV2ZibIEzAnVkmicSJ9WtHAw9q3GpE8MrERcaeTeVMIIWFDnQhrWXABDVciaI/640?wx_fmt=png&from=appmsg "")  
  
点击学生搜索抓包  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboTsuJHEJQI0lFOQQMYvE5Vt65xkIs28eqk3O9JQGlQOEBl8eUOzyN7rF8f4sARVHdyZUH1vuCqllq8wmWRS26zxp8YFYC5uEmY/640?wx_fmt=png&from=appmsg "")  
  
可以看到手机号  
  
这里我们可以通过控制这两个参数  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboSmkZwMeu4UyxfcvibCbEEtrIVA23qBKL0eyWEAUhmD0d2qYLR6h3KNzZHpiciccRibgHDU9NI3gBBYdbJxhDbDdENMlYeeULXW550/640?wx_fmt=png&from=appmsg "")  
  
对学校3w条学生的手机号进行查看，从而造成3w用户的账号接管  
  
继续查看  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboRePhicArfu9VmpR6vwbs5rhQtsZ2966hq3BN3qPgswDGic4ZDmLSyq2zCyNmOlP5ialhH6rKTPlJ4LatPt8AVmSTeOH0pP4hibzY4/640?wx_fmt=png&from=appmsg "")  
  
点击，具体信息进行抓包，可以看到更详细的信息  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboRA9Keax6mhWcWOGJUS6b7x5SOJpsIowWzVIm8rAbSX6YgCvaQicYicnkiaCIzuZSgTrVE9x1NwbqiaicaUee1XTdVpOgJI04TGuSOs/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboQ742S7H2m1ibdKREb2kKPjEL9NTZOqJDqYfVJuZJjlSnXRQK4dtds7RgP2VTqFF5ia2O76txpf9liaeYnYGicr4quer7icwmjiat3Ac/640?wx_fmt=png&from=appmsg "")  
  
这里依然看不到敏感信息，但是这是一种很常见的情况，后端返回很多信息，但是前端渲染一部分  
  
我们抓包查看  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboTB6Qg7gayveDjWkOUnZHubAyUTNeJSEU3bdrqVZV6ZaIwm5JcLYPSCRic5gYlZ4Kzfc74zrryK4pq3aBOfouDkq8Z6mQZbJjZU/640?wx_fmt=png&from=appmsg "")  
  
直接泄露了sfz，也就是说，通过这个能获取，全校3w多条sfz信息，危害加一。  
  
  
**后台回复加群加入交流群**  
  
****  
**广告：********cisp pte/pts &nisp1级2级低价报考**  
  
  
**陌笙安全纷传圈子+陌笙src挖掘知识库+陌笙安全漏洞库+陌笙安全面试题库**  
**简单介绍****（**  
**加入纷传圈子**  
**送****知识库+漏洞库+面试题库****）**  
                          
  
如果觉得合适可以加入,圈子目前价格  
39.9元，价格只会根据圈子内容和圈子人数进行上调，不会下跌。。。    
  
  
**圈子福利**  
   
  
**edu漏洞挖掘1v1指导出洞**  
  
****  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboRKQWHxLsRrPqpqdiceX76d7yExQIyOqFmmJAfHQh7qzKvPc2V5z6iaa0RY6Ib8AsGvgS5MKkAk5aaHnJBaSnI10LDKQYMLcQMmg/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboR8pnPeapLBK4Jsa4ufCvFoGL66t7PKeZyA3AjNxsObjtnCibN2gzGX7NMS7Wo5sj3YYL2iboeRuQDcWqiapc8xuo5fticoBG4DsyY/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboRKIBNIQIVicRWJLbyGRmg92vPzc8375PJpcYVvfywzwqnaeBicZuEbfvuic9KRdjwkahSDic5VqrH2Mb4NkqtkADl5HLIh8gPex60/640?wx_fmt=png&from=appmsg "")  
  
  
**陌笙src挖掘知识库介绍（内容持续更新中!!!)**  
```
信息收集(主域名信息收集,子域名信息收集等&会永久提供fofa-key助力)
弱口令漏洞&未授权访问漏洞挖掘
任意文件读取&删除&下载&上传漏洞
sql注入漏洞
url重定向漏洞
csrf&ssrf漏洞挖掘
XSS&XXE漏洞挖掘等等常见漏洞
cors&目录遍历&越权漏洞挖掘
EDUSRC(证书站挖掘案例分享&edusrc挖掘技巧分享)
CNVD挖掘技巧分享&实战案例报告编写
公益漏洞挖掘（公益src挖掘漏洞分享&提供补天1权重资产）
SRC挖掘实战(针对各种常见功能总结的常见测试思路等快速提升)
经典常见Nday漏洞(常见中间件&以及各种常见框架)复现
云安全相关漏洞挖掘（云key扫盲&云存储桶&快速识别云环境&云攻防）
AI相关学习（AI基础&AI代码审计实战测试&webLLM攻击等）
APP&小程序漏洞挖掘
等各模块不在一一介绍
```  
  
信息收集  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboTu9DGyTubluhYicFynwVBKa4V06sDfEVKOyk5Q4ghZzLMDAuLb1M1oR4RJumGWrADPapFjTrOjpksKQ8q0YYCnl3ZWLof8Knzg/640?wx_fmt=png&from=appmsg "")  
  
src挖掘基础  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboR45bibbJEb28a1gS5yth3r5HyOsgPiaOUHHYriahZyIyrk0LMOsHW4VoDibyBRibTNzptGiaLWX62UwykicwvbxCJPopvklqiaxML8lS8/640?wx_fmt=png&from=appmsg "")  
  
src挖掘实战  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboTKWnTsN6CXf3djhXIlMKNRjVmJn3g5b23ur9E6Cx3O68f0hXVjCiaj8J4RYeTGBecqf1k99phG0ice2wtd5lKgR46OeqeLQfMpk/640?wx_fmt=png&from=appmsg "")  
  
  
edusrc  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboQVVlTXhibjR8UiakZBQicXRZrQ7hdoOz5G8MQrcuDBGbqJdO0kIz6R9IU4ObAeOiabT8pr6lc7jibdIkKoTjiaXNHPLAwAB3BV2UvLM/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboSCrvarBbzP4L9kS6P0LVH9JMdmcbFDKiaicHqMFgTxq3x4iatjDJQicmc7NPC14C9Fk3icFjrouSgNVaN8Byuf0C0Iq9O6D1XPvFvY/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboRALwXmgZ4mh2LW0RdicrKjBCP7P1iaF14G0Eq2v3KRnTJORpwXZlF58WEz6QicxLJpyJaA5iah5CF2rHjBz4JzOELFRaZTAKOQ2tQ/640?wx_fmt=png&from=appmsg "")  
  
经典nday复现  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboRu8Gf849iaCkSBxLL8IlzJTRs185QicEe9l5UGI1dEVKISt2IGGveZynXBW9tIUsxNsz4adSTib7rib50uSJdjNfTvVRFrbPJhzL4/640?wx_fmt=png&from=appmsg "")  
  
  
云安全&AI安全  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboQ9qiavETNjaaX162czpNCqpw3uJqVpicbI15AXzhf5x8icmHxBdTGOgRgzNPGF3Aw2gglT4Fx09JGXYibQC6U7CQKVmoH08l3meia4/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboQgcsjiaZ4S26TWowHfpBkhSeHf2pjrcDyicJuia3uqvRBauLEOicibibEMqibnBMtjopFL8No7UXNibbURvzeJ3dQHTibvGxRQGnorb4co/640?wx_fmt=png&from=appmsg "")  
  
**陌笙安全漏洞库介绍**  
```
最新漏洞查看
1day&0day分享
EDU学校相关漏洞
Web应用漏洞
CMS漏洞
OA产品漏洞
中间件漏洞
云安全漏洞
人工智能漏洞
其他漏洞
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboTFBRMa8XwYxfcZMyXicx94xSKxawPcqFia2rJKOL7fSLYXiccwHc868XxNGIQ5z7ibiaI1MNAGRrK7U6wXJTsZOCAu2I5XV1boTAL4/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboSBzdakI9XI33ReAm2dxO8vgzw3JicQmUuWCb5ayBlKR1PoQHEHFETteBnicyupwU0mXvXibfrDoyg8nSWBGoK1p2YXY3ElhcvOQ0/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboSajCclDhuRpaLic9Ld915CHU7RqSC1LCrPGfNZiavdPEVeDedDWOPBhtMLCicTp3RNd1lT0Pmfo3mx5B0hUxbQg3ic6Via90NMtZVk/640?wx_fmt=png&from=appmsg "")  
  
****  
****  
**陌笙安全面试库**  
```
渗透测试基本问题一汇总
渗透测试基本问题二汇总
渗透测试基本问题三汇总
微步护网面试题目
长亭科技面试
深信服护网面试
启明星辰渗透测试面试题目
安恒面试题目
绿盟笔试题目
360面试
奇安信护网面试
运维面试题目
运维面试题库
网安面试相关文档大全
相关面试文章推荐
等等
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboSoLqEzH0a3A4LQrvTIkGx81Sh5pf6fCoEQJhYg715vrJicSkfBuCoAmV2Kp4uOMe5jcUZutPwicibFibtJ1ZmyiaAibCg0XicWnsNcicE/640?wx_fmt=png&from=appmsg "")  
  
****  
**POC库****&&更新适配afrog&&nuclei&&dddd的POC&1day/Nday等&&**  
**dddd二开****工具[助力渗透测试&&红蓝攻防]**  
  
**工具截图**  
  
****  
**实战效果**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboSKqLXNcOPE07xOwOUCjRGuFphopPumW9RaticmNCuEUXu52GtdTTfpTUicrBj80kMcZzJsnps3abyvXIvLHEIhvMoXUApOqZCe4/640?wx_fmt=png&from=appmsg "")  
  
****  
**poc库【后续持续更新】**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboTG9Lyp44aFffUOxQKtHjToGfqFWTjswYft0VtAPINtV5MqmrTTj8GWrVb6yowvHURubPgOqdribmibWEb0Fcj3YdN4iahUwItcxE/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboRAMJIIvexOOJa5KhrsKmlsx8bkwib9SPoK72Q0OSPWR5qx67yvl8scMQ5bg8caBXZH01kM39RDnKpnWSaTicgobRmLygERGFWls/640?wx_fmt=png&from=appmsg "")  
  
  
**AI赋能-**  
**skill辅助**  
**漏洞挖掘（免责&&慎用）**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboR3Dib0RVxVhUOzS6ibC6BvkfulXQAclic0XCXMS35C4EPoqX1b2eMVj2CFiaLCelVs1szGibaHiaAq7WibRdwHUg0IwO8fjDdWxNv6eY/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboSXZZVels2NibmHgyxntlCRNIkgoqMPfUPwSM9O43OqniaZLDEJic9QRkW01gNTydFkibdI6yBRkJJ1sDUmfl7iaicoibz1QLp0J2pWE4/640?wx_fmt=png&from=appmsg "")  
  
  
**圈友ai辅助渗透**  
**实战效果**  
**，支持打假！**  
  
证书站  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboQibuxKAyHBZicB1t5yGVKyV82Teo8C2MbjKPytKziaXUcjPiao8ylHbD4vicAld8equC9alic3NksvWJ09wArXaPXZD10vjPtfoia4Vg/640?wx_fmt=png&from=appmsg "")  
  
普通站点  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboQ1Xt6gdLgd2d1mf8QURQX4YZjHA2uIw9GdTuxzSafBVOJzrQVmHJlqdhVWdVDj3OQsiaQhYOaoiabXc6EgajvBMvB6xBZwdJkIQ/640?wx_fmt=png&from=appmsg "")  
  
****  
**陌笙**  
**纷传****圈子介**  
**绍**  
```
1、src挖掘思维导图，信息收集思维导图，edusrc挖掘思维导图，以及后续的红队&面试思维导图&自己网安笔记等持续更新
2、2025-2026的edusrc实战报告包含证书站和非证书站以及2025之前的各种优质报思路分享
3、各种src报告思路分享（内部&外部）
4、分享各种src挖掘&edusrc挖掘培训资料&视频
5、不定期分享通杀、0day
6、有圈子群可以技术交流以及不定期抽取证书&免费rank
7.分享各种护网资料各家安全厂商讲解视频&精选实战面试题目
8、各种框架漏洞技巧分享
9、各种源码分享（泛微、正方系统、用友等）
10、漏洞挖掘工具&信息收集工具&内网渗透免杀等网安工具分享
11、各种ctf资料以及题目分享
12、cnvd挖掘技巧&CNVD资产&src资产分享&补天1权重资产分享&fofakey共用
13、免杀、逆向、红队攻内网防渗透等课程分享
14、漏洞库&字典以各种内容不在一一说明
15、cisp-pte/pts&nisp一级&nisp二级&edusrc证书内部价格
15、如果有漏洞挖掘问题或者工具资料需求可以找群主(尽量满足)
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboSY2pbvbP3qGAlW8O43bRvAISCxZm4UDTRsaMVbJKTsjfTMTDlq6qNBcVs4tkl4UzgqGz5ag81baU1rusKE09J9T6cMVliaibibwQ/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/MSDUaqtwboTrLRQpTicOR7bzyNiajiapVJgyMiaYlEDBVU87YXMnanOFWsCYN3cCVGsKkibzV9dMryvbFXBb4Z3472ib27RJ1Xq1HnKJIp5u49GYQ/640?wx_fmt=jpeg&from=appmsg "")  
  
**目前740多条内容，扫描下方二维码查看详情以及加入圈子，持续更新中。。**  
  
**如果觉得合适可以加入，价格不定期会根据圈子内容和圈子人数进行上调。。**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboS6aWPIgWkKicUwu8ZiamtqWhg7UcFK22okQrXnQcTZxiaXFVrl4QXmUMxrSOic69VyUlQictbauRDrKXllL810lAWMOtcOvb9taUAM/640?wx_fmt=png&from=appmsg "")  
  

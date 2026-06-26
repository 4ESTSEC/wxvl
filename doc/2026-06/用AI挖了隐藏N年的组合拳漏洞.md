#  用AI挖了隐藏N年的组合拳漏洞  
 迪哥讲事   2026-06-26 03:00  
  
## 前言  
  
这个漏洞是在护网中发现的，感觉很有学习价值，于是复盘了一下，花费一天时间，根据漏洞细节，在本地一比一复刻漏洞环境，以供学习。  
  
这篇内容较多，先放目录，细细品味  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/16lHuWzRRdtlCQGXxgQbXRFJrzxhKcI1BYfB91iaad5lQCDUF3wHfQCe1vDYbJ1jXuK8kBtl6YmiayyprkGytRhicSsLpxb9wNNRw1tKQsaTyo/640?wx_fmt=png&from=appmsg "")  
  
启动靶场  
  
![](https://mmbiz.qpic.cn/mmbiz_png/16lHuWzRRdvWzuJjnEFqnFqSe6RYKMgyxMkP4XRr5kibIbvePmXRict3pqdNBmzO6lmR8ndQcQSPnygZxpicJUUm2mowvGzXjht29rtUx3icgQM/640?wx_fmt=png&from=appmsg "")  
  
## 一、初步测试  
  
弱口令登录尝试  
  
首先访问目标系统登录页面，尝试使用常见弱口令登录。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/16lHuWzRRdt2v8kLNZOWcJItzHEibfELZGYf9qzE9elZdO8hqkUUZ7ng79LrrLVKIjjiadootI1OREw68sOx0HF7EzxTs8ibdYMplrez01YUnE/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/16lHuWzRRdvWXAgJy4kD7QJPUJ2Melj5pR9gVq8NPvRD1TfmpT4vSZIa9QrkIHNkPaLHlcV22vibvF09MD98Lp3mXXGicXiaP3PAfnmdyYyPdo/640?wx_fmt=png "")  
  
验证码功能测试  
  
随后测试验证码登录功能，观察相关数据包。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/16lHuWzRRdsjTG5LAhvMotTeztSPZcDFVZkAROP6P3mNA8sibAJtz5k0n73jxbWBRKXhyZibibQJYpJtM1iceib96FNhdyJhvrLm6FPDnaRb6WS4/640?wx_fmt=png "")  
## 二、信息收集与配置泄露  
  
发现 OAuth 2.0 参数  
  
在登录请求数据包中，注意到存在 grant_type 参数，结合系统注释信息，判断目标使用了 OAuth 2.0 认证框架。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/16lHuWzRRdsqXfSudstN6ENEXQDd0tZg98ObT8ibP5yZTuicMq2AzZ86SLEwq0MZH3ETmICiaQ3UEq4X4NerNiaWbryDs45ia6e5fdXhx173ks9Q/640?wx_fmt=png "")  
  
搜索客户端凭证  
  
OAuth 2.0 的客户端凭证模式需要 client_id 和 client_secret。  
  
  
 首先在前端源代码中直接搜索，未发现相关凭证。  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/16lHuWzRRdvDRAxIiariaJtsWDx1NffJtBcO1NkPZyg98OsTNwZFvKicTTIes2omlTATTHhPjo943r5icE5a5yxib8eE5szKS8hEff1GbYGXmhHg/640?wx_fmt=png "")  
  
随后使用浏览器开发者工具的“搜索”功能检索所有加载的 JS 文件，仍然未找到。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/16lHuWzRRduHE6XBCuMIqcS1kFI0oxZ6ExM3iatDgdTlict4W0XzAdYzjlKib971gofAtoAblaKXddA3fq6FY64TD9fkouyS06uzcgaC1wjXx4/640?wx_fmt=png "")  
  
最终遍历全部 JavaScript 文件，发现 api.js 中包含了大量接口定义。  
  
在该文件所在目录的索引文件中，找到了一段被注释掉的 JavaScript 代码。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/16lHuWzRRdshEnH4fwgY9icVwZiapib0XypxWTGAYV14jic50k8GEfpP8EG9iaZnOEZmUEwa1c66eX3jfmD3Qc2grOs75N2XKULP5Gy5ics9YFUlE/640?wx_fmt=png "")  
  
访问该被注释的 JS 文件后，成功获取到 OAuth 2.0 的配置信息（包括 client_id 和 client_secret）  
  
![](https://mmbiz.qpic.cn/mmbiz_png/16lHuWzRRdsXCpAZf1VnUwNc1eZc0UbJHoczqG0tXGSZ4pGOyTl5eibYBQkA8jxBahRic4PyJXdUTwTrHOYZU5pZEkxWBpMcANkjMTx49TniaU/640?wx_fmt=png "")  
## 三、凭证有效性验证  
  
参考 loginController.js 中的请求参数，构造如下请求（使用已泄露的客户端凭证）：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/16lHuWzRRdvQVub3K0yFfjNZCfSTQicKZbicjBn2s5fjqUonm5ia3ibfJuyZfGYsvQHw2A6dianSkniaYj0U9IPwFhjeARtPwzljiba0FKljiblHwPE/640?wx_fmt=png "")  
  
client_credentials是 OAuth 2.0 框架下一种授权模式（Grant Type）的参数，称为客户端凭证模式。它的核心用途是为服务器与服务器之间的通信（即“机器对机器”，M2M）提供一个安全、无需用户参与的身份认证解决方案。  
  
这一步是为了验证泄露的 oauth 信息是否有效。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/16lHuWzRRdsuv033ac3pYPMmTUXwScgZdZiag4K6OGQUecUA6Q0bI1uDmUvYt8ibj4wlr1P2skEMHUAcib7rkGbLdAnVmoF1fTUcUCVrhJMVe0/640?wx_fmt=png "")  
  
此时可以获取到token，访问接口，请求头加入token  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/16lHuWzRRdtnu2o0JChZo18icW9p9n4UtIUq46JkQYxSDgqjKgbGyxpdespn6kGlbYIWrWN0fh00IRIWDM7fiaWqT7kUuCfRvGteI2IdA72HQ/640?wx_fmt=png "")  
  
显示失败，没有权限。  
  
如果不用这种模式，直接用password，则会返回失败  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/16lHuWzRRdu8JlJCxk1KSRroJMdAeRtVZiaYkHgwNGl8790HXqcZWiaSKveMYbJakh3qKSXbicZvPuRk7Dic3I7ibRTybribKn8euW2LmkEHia7sgQ/640?wx_fmt=png "")  
  
因为password模式需要用户的参数，我们没有系统内的用户，所以这种模式就不能使用。  
  
于是返回重新查看api.js 接口，我们发现是有一个注册接口，那么我们可能通过注册一个用户，然后给他授权password模式，就可以获取有权限的token了。21 行注册接口  
  
![](https://mmbiz.qpic.cn/mmbiz_png/16lHuWzRRduHE6XBCuMIqcS1kFI0oxZ6ExM3iatDgdTlict4W0XzAdYzjlKib971gofAtoAblaKXddA3fq6FY64TD9fkouyS06uzcgaC1wjXx4/640?wx_fmt=png "")  
## 四、注册用户并尝试提权  
  
靶场环境我做了参数提示，实际环境没有具体的提示，但在登录抓包能看到username和password参数了  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/16lHuWzRRdtcJ8p6ibjFSiarLKYT9tpJLJqVF4ebicjtjr1t7n81KGy5SpsLZ9Rn8sibCWbUqZNU4VwQpOPTnLVt4PK188E1LOvLfUIxCQxeB3c/640?wx_fmt=png "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/16lHuWzRRdsMoXNHJvGfvhkJthPaelYiaVznuOTSwia7x8Udiac0sf9B3O3XXZJiavHN7cHcicX2ZO7GLGPvw3yS1yQOYzJxziapb9laclVHnRk9M/640?wx_fmt=png "")  
  
  
   
  
  
直接注册是普通账号，可以看到右边 roleType=GUEST  
  
使用oauth password模式对注册的账号授权权限  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/16lHuWzRRdvVE6lqXWlkGBybFiaghjMSIibAqhyb50MKvpUSCP1aPLnCwLT1CQqG0OGvmrP9ytWTnYpILr5rlORJTsoQ8rlicAniaVZSIAgOSts/640?wx_fmt=png "")  
  
然后访问用户个人接口,id用注册成功返回的id，可以查看到信息, token用上面返回的  
  
![](https://mmbiz.qpic.cn/mmbiz_png/16lHuWzRRdvPRtq9zQQ1TkVZSPiabsic25DHPq2j8YBOicdDMicSjpQ5kWia2bYZibZzdibj8POcNN5icRgTNDHzT4J6YVtyB8NPNcbZLFNDazSmNFw/640?wx_fmt=png "")  
  
此时去访问所有用户的接口  
  
![](https://mmbiz.qpic.cn/mmbiz_png/16lHuWzRRduO3EaVkxic8qVSoBy3I0FfnTgFP1oVk7Esm0ypPAJTCTAd6eD1pQ2Ricl0WI0ib6ricSjDvbQpibvFCnSQziaWthhQYS8wWggCOGVSI/640?wx_fmt=png "")  
  
显示权限低。说明注册的用户也分普通权限和管理员权限，此时回到前端js页面，寻找如何注册管理员用户  
## 五、寻找管理员角色注册方法  
  
代码在main.js里，划分了不同权限对应的id  
  
![](https://mmbiz.qpic.cn/mmbiz_png/16lHuWzRRdvDqHib4996jCnALmA984yCwbibpibHcQRx9VhAoSMgVevzvYBcNnZ0fibBwib9WJKhA4Fic13U8wzWzBv6VCKd6kibeMGQqDfGQicH3e8/640?wx_fmt=png "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/16lHuWzRRdvEdcItZ93F6AOPuerYjibht9Lep1CRIiaOJIdjiczCcNlH2IUgtE7iaSJnu7okmcSU1AicTb9ajSVhj3PxhgTKEVBstYlFGibQs1nFI/640?wx_fmt=png "")  
  
所有注册的时候需要传入ownerId  
  
此时重新注册管理员账号  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/16lHuWzRRdvspXqkJGuapcj60S3fl0WiaEZ5gpVW9DYesGYt7O6j89sfUEvy87CyjsIZnlDDUVVt5ULibLXWTDJEtic6JUxkT5hcXBxjJGG5V0/640?wx_fmt=png "")  
  
然后再重新赋予oauth权限  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/16lHuWzRRdtoKAO2QLV6HqSxb1eZgbp0XYicSaQBwsP9n7V4WADn8LWC1uhEMgkVIqVtwMrRM08rcRicucBbZhNgGibhwyQO7KHddibMkSFibicGQ/640?wx_fmt=png "")  
  
访问刚才403的接口  
  
![](https://mmbiz.qpic.cn/mmbiz_png/16lHuWzRRdtUMt0NuvqbZdkibWN3booroiahhm6jA2zDB7MDEMaaEP6kLqWdf2kCaIscv6QgwNd8YBpfwEMuqdS5ZoxGOdzahiaibRYdDDEYHwo/640?wx_fmt=png "")  
  
  
   
  
## 六、漏洞总结  
  
漏洞点：  
  
前端 JS 文件中残留了被注释的 OAuth 客户端凭证（client_id 和 client_secret），可被直接获取。  
  
注册接口允许通过指定 ownerId 参数创建高权限（管理员）账号。  
  
系统依赖前端参数控制权限，后端未做严格校验。  
  
利用路径：  
  
泄露凭证 → 注册普通账号 → 获取低权限 Token → 发现权限不足 → 分析前端找到管理员 ownerId → 注册管理员账号 → 获取高权限 Token → 访问所有受限接口。  
  
  
   
  
## 七、AI 自动化渗透效果对比  
  
近来AI发展迅猛，各种Skill和平台层出不穷。我周末体验了一些，总体感觉确实比较费token，但各有各的优势。还是那句话，AI肯定会越来越好，但我希望它始终是辅助我们工作的工具，帮我们拓展思路和新的攻击面，而不是在未来取代我们。  
  
  
   
  
  
有点感性了，兄弟们，废话不多说，我也来搭建一下，针对上面这个漏洞，看看AI是如何攻击的。  
  
  
   
  
  
采用三种方式，看看哪一种可以做到全流程覆盖  
### 方案1：只使用ai agent 自主渗透  
  
  
   
  
  
这里我使用   
glm 5.1 模型  
  
对这个靶场进行渗透测试，然后出具渗透报告 http://192.168.3.5:8090/ 不需要端口扫描，直接进行 web 测试  
  
  
   
  
  
截图只包括关键部分，  
  
![](https://mmbiz.qpic.cn/mmbiz_png/16lHuWzRRduJtPVWhD0jf3ViaSRkmIzHbmOPNDYtibGvUacdNvYfclVUuT1hgoXlVV8JicHR13JbpqaVhTyELI1qZHvnUib0wW3CZqSuuw7F8Vw/640?wx_fmt=png "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/16lHuWzRRdvcicKrVhfMicae2NAw35bwALGoU0OrbUjsFcGnax3icsvglCmykw0J1nwgGeudwTviaqdemOApxs3JoHtsLQVPEhqV2aNXmY0MNeA/640?wx_fmt=png "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/16lHuWzRRdvIgtRRr3a9M4RCn776WJttJBEFib1aFMBFpcVk5qicfAd7ra0YiaSibiccakFs8Rib4jV9LWdPh7wibYoFw9HQgxlbMoCAh706SNhqqc/640?wx_fmt=png "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/16lHuWzRRdvs43UicGDp0DYsv8ONgpyjPewzVdvJqrtx2ud5xVG51qicAKdZYowiavnl5ogzT1paxW8cNED14jVIOo8aiaOhqoqOBLXfNQKHYkU/640?wx_fmt=png "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/16lHuWzRRdvNFFH6rveO9eyBInnE3a9mhhQYR3kF1icHqeUicibNsEZVtA5jwgsRWbsGZ4ChcYicO77bh4ibIdQX0MOOR6P8KrSFWNQm6SWa6rmU/640?wx_fmt=png "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/16lHuWzRRdtIVZEzYGDV5gxyBic2Uul2lhwcsufnmNFvyGKDicYGQnt35k0ia1g2fIPEeicEuVhfRdwSADFvR9gwuDJPAz4cIjwsg2xAKVT2EMY/640?wx_fmt=png "")  
  
这里开始就陷入了死循环了，一直在找提升权限的接口或方法。  
  
最终没找到，结束了这次渗透  
  
目前的流程是  
  
查看前端内容，读取js中的接口，发现隐藏接口，找到了oauth的配置，然后无法利用，又去看接口，发现了注册接口，注册了用户添加了oauth配置，可以返回有效token然后获取系统接口数据，发现大部分没权限  
  
  
   
  
  
给了四次提示，最后直接告诉他对应的参数，才完整复现。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/16lHuWzRRduLbsiaNr6wjhn9X0DJQKCha3bicV2JhDGL6BGQEu9ibN76NDGKvzDrKSkveR4eUK6cIhjuKNbjLrFMEoZic9VKIce5UiaialP8IraPw/640?wx_fmt=png "")  
  
  
   
  
### 方案2：Glm 5.1 + chrome-devtools-mcp 渗透  
###   
  
  
   
  
  
首先需要安装node，然后安装chrome-devtools-mcp  
  
npm install chrome-devtools-mcp  
  
然后点击右上角的mcp按钮，配置这几行代码就可以了，再开启允许按钮就可以了  
  
  
   
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/16lHuWzRRdt4B5Yt3Xcc8HjiapDqaUbo4vxPx8NXjEWyz1XhobnREjsmZUZJz8xd3qGnXW394kqGgA5rdJk7KAlAN3jTspjibl4zugDYzaL1g/640?wx_fmt=png "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/16lHuWzRRdtRvkibCA74XLg0UFbvicwEhfl7WzNJfRWaJXe80Stog5bwdYJasfb70Yzic3COw1vgrx1la8sFSq6VAk6RIm7CyOxdj3CiapKw82U/640?wx_fmt=png "")  
  
  
   
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/16lHuWzRRdstqsg7o2hPr4M874EeVFxrbCibQLRAG3ZDf0kiaxQfNC6nG4T39ofYGHSIsTicv200hjicZLqYUrtvNF1szMPib0oiaWxtLmHVcicaco/640?wx_fmt=png "")  
  
  
   
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/16lHuWzRRdsFcAk9Dib1MaEIqFKTEE23TwHANPT3XHuk8aggibJKUfGntMepOusFIkk75qcFOZX8T6VU2VVnUq8TbCjibqEfgbpibPJ0xHMdtMc/640?wx_fmt=png "")  
  
  
   
  
  
同样提示四次，才完整复现  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/16lHuWzRRdsGXbXg4dxDnhXTC9MGJTOjQcv8daM5s0X8psJcI255lylAVLiaBU7ibQz6f2qqRiaibMf2goPvcecichGFgyZrZnOPyhK9L3lMiaHSY/640?wx_fmt=png "")  
### 方案2.1：DeepSeek v4 pro + chrome-devtools-mcp 渗透  
###   
  
突发奇想，最近 DeepSeek v4 pro 模型挺厉害的，于是我充了 10 块钱测试一下，看看它的效果如何，结果大跌眼镜！！！  
  
  
   
  
  
Claude code + DeepSeek v4 pro + chrome_mcp  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/16lHuWzRRdtPMwbiaOiazia7Hia2yH7bX45EXGCHI8ftyT7FXHpSO4QdCtM8U1QlqlgL6HNbG45nEgJ0jrzWaq4xIGx1gSxXmvqQOFLHnuoXicEE/640?wx_fmt=png "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/16lHuWzRRdupfd4rsYYAeaxGcmuhbTUvEoWzlsy2ibYibSEKl9jyXic7Ztv18YSun53libBu4ksEndibWZ7tO9dOpsOx2d70GbadCPu7hn1WEVaY/640?wx_fmt=png "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/16lHuWzRRdsWrfns01RFFkQz6slUqGGiccZSib2I6y99E7gJuheLxzwosmdMxt4KTeNvgYC8oAUJUz9uN9NmEUsKPe9XlicibKk6PkNwVc7zc54/640?wx_fmt=png "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/16lHuWzRRdsIHI7tOStArNnJaQx1bLrY3xiaFOtxzWxzNibicr89qahnqf3FGiaMX3h0Nzt9pGaGwkDiad67GUWRUX7Apd64NgwhPNSr31oQbiaSs/640?wx_fmt=png "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/16lHuWzRRdtAsDf2EclAuJpzsbmURQVZV2ibmqiaLkicMzjwQ5FibCFUFyeWHqjb5fcSDJQXicKJPyoXoveO5iaMoXhSmOwcyT4kclBrH3zVaUiaYA/640?wx_fmt=png "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/16lHuWzRRdvMlmMiclBjUEyWoobgPicvT3W5nric2lm9lQBKS73IY6ekQtuqZH4NZRuBBXLOjuaZwBfKOLNOFLmM1Xk6o5vqRNicSKqpSmzx4xw/640?wx_fmt=png "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/16lHuWzRRdvz8xtxmBiazZmlaxyia6DvsPPwPfMlP8iaeXaGfN6Kr10LAozZ5hGcNE39hKESYjwekSX2WwMzh66mo8ZAPoS1yTxWoTvHktpRCo/640?wx_fmt=png "")  
### 方案3：ai agent + skill 渗透  
###   
  
Skill 如下  
  
![](https://mmbiz.qpic.cn/mmbiz_png/16lHuWzRRdvTER3QibIicRtbMlGaic1Z6CSPlCTsPESx5JBC87djaOiaQeAYia3ic3EeZS3frAOJUDz5OtvTicDI0Z9SSqkiaIzOnS2p63aZV0Lq1so/640?wx_fmt=png "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/16lHuWzRRdvQL5BZ5cNoCZ24tGxzhIaia3niaQUheUMjZjjwnTOdRbyiadLicg2icq6VpeqKH5Eka3V9R0IRIheywH4Zpmc7zTB73CUPxnWUBhp8/640?wx_fmt=png "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/16lHuWzRRdtXS9oCHB5FwvJ35icSC2TiafBnFLkGUZTVEkNYDsJn4MfubLRkUynhvIYVZdb4MErt8L6gOOzw1mOdjOtC9E9prq2qU2S15YEls/640?wx_fmt=png "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/16lHuWzRRdtibFicve6olcqmDo08552fRP23c3kw3GSiciby2mEicTZIPwZkYjwCciaibAS6EqX6TYAR2cggB8hwQLQ1cEszqK00H9FQNHwfibCVQiaQ/640?wx_fmt=png "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/16lHuWzRRdvhx0NxpV2O36SN0jCqg1BoYgTHyxMjdoGoyLDReGAJiaaA0OABviaF5KpNlQSlZgtcYnoQLBTsEwCFBuhhHvKJv5Y8Tald3Rq2Y/640?wx_fmt=png "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/16lHuWzRRdtNHepNsZxGVXtJYcC9rT2DgmVcxlVLjtWrUmbibM6W2ic0venBEAExmicBq5GeSHc8ZABBWNkLiaTiaexG4Ndu5JxsS4IeadiciaMV60/640?wx_fmt=png "")  
  
  
   
  
  
最终漏洞  
  
![](https://mmbiz.qpic.cn/mmbiz_png/16lHuWzRRdvbcQvYCbpWKMwf42qO9Picc1CzgAbIKx8XldANte1N6Anibr3S4ibEiavVpprIzRIiafSUMLxcHnRYgzCXzvrRcDdfsBNWjpXoGMNU/640?wx_fmt=png "")  
  
  
   
  
### 总结  
<table><tbody><tr><td data-colwidth="189" width="189" valign="middle" style="border-width: 1pt;border-style: solid;border-color: windowtext;padding: 0pt 5.4pt;"><p style="text-align:center;"><span style="font-family:Calibri;mso-ascii-font-family:Calibri;mso-fareast-font-family:宋体;font-variant:normal;text-transform:none;"><span leaf="">AI 自动化渗透</span></span></p></td><td data-colwidth="189" width="189" valign="middle" style="border-width: 1pt;border-style: solid;border-color: windowtext;padding: 0pt 5.4pt;"><p style="text-align:center;"><span style="font-family:宋体;mso-ascii-font-family:Calibri;font-variant:normal;text-transform:none;"><span leaf="">漏洞细节</span></span></p></td><td data-colwidth="189" width="189" valign="middle" style="border-width: 1pt;border-style: solid;border-color: windowtext;padding: 0pt 5.4pt;"><p style="text-align:center;"><span style="font-family:宋体;mso-ascii-font-family:Calibri;font-variant:normal;text-transform:none;"><span leaf="">效果</span></span></p></td></tr><tr><td data-colwidth="189" width="189" valign="middle" style="border-width: 1pt;border-style: solid;border-color: windowtext;padding: 0pt 5.4pt;"><p style="text-align:center;"><span style="font-family:宋体;mso-ascii-font-family:Calibri;font-variant:normal;text-transform:none;"><span leaf="">方案 1：AI自主渗透</span></span></p></td><td data-colwidth="189" width="189" valign="middle" style="border-width: 1pt;border-style: solid;border-color: windowtext;padding: 0pt 5.4pt;"><p style="text-align:center;"><span style="font-family:Calibri;mso-ascii-font-family:Calibri;mso-fareast-font-family:宋体;font-variant:normal;text-transform:none;"><span leaf="">70%</span></span></p></td><td data-colwidth="189" width="189" valign="middle" style="border-width: 1pt;border-style: solid;border-color: windowtext;padding: 0pt 5.4pt;"><p style="text-align:center;"><span style="font-family:宋体;mso-ascii-font-family:Calibri;font-variant:normal;text-transform:none;"><span leaf="">中等偏上</span></span></p></td></tr><tr><td data-colwidth="189" width="189" valign="middle" style="border-width: 1pt;border-style: solid;border-color: windowtext;padding: 0pt 5.4pt;"><p style="text-align:center;"><span style="font-family:宋体;mso-ascii-font-family:Calibri;font-variant:normal;text-transform:none;"><span leaf="">方案 2： Glm 5.1 + chrome_mcp</span></span></p></td><td data-colwidth="189" width="189" valign="middle" style="border-width: 1pt;border-style: solid;border-color: windowtext;padding: 0pt 5.4pt;"><p style="text-align:center;"><span style="font-family:Calibri;mso-ascii-font-family:Calibri;mso-fareast-font-family:宋体;font-variant:normal;text-transform:none;"><span leaf="">70%</span></span></p></td><td data-colwidth="189" width="189" valign="middle" style="border-width: 1pt;border-style: solid;border-color: windowtext;padding: 0pt 5.4pt;"><p style="text-align:center;"><span style="font-family:宋体;mso-ascii-font-family:Calibri;font-variant:normal;text-transform:none;"><span leaf="">中等偏上</span></span></p></td></tr><tr><td data-colwidth="189" width="189" valign="middle" style="border-width: 1pt;border-style: solid;border-color: windowtext;padding: 0pt 5.4pt;"><p style="text-align:center;"><span style="font-family:宋体;mso-ascii-font-family:Calibri;font-variant:normal;text-transform:none;"><span leaf="">方案 2.1：DeepSeek +chrom_mcp</span></span></p></td><td data-colwidth="189" width="189" valign="middle" style="border-width: 1pt;border-style: solid;border-color: windowtext;padding: 0pt 5.4pt;"><p style="text-align:center;"><span style="font-family:Calibri;mso-ascii-font-family:Calibri;mso-fareast-font-family:宋体;font-variant:normal;text-transform:none;"><span leaf="">80%</span></span></p></td><td data-colwidth="189" width="189" valign="middle" style="border-width: 1pt;border-style: solid;border-color: windowtext;padding: 0pt 5.4pt;"><p style="text-align:center;"><span style="font-family:宋体;mso-ascii-font-family:Calibri;font-variant:normal;text-transform:none;"><span leaf="">屌爆了</span></span></p></td></tr><tr><td data-colwidth="189" width="189" valign="middle" style="border-width: 1pt;border-style: solid;border-color: windowtext;padding: 0pt 5.4pt;"><p style="text-align:center;"><span style="font-family:宋体;mso-ascii-font-family:Calibri;font-variant:normal;text-transform:none;"><span leaf="">方案 3: AI + skill </span></span></p></td><td data-colwidth="189" width="189" valign="middle" style="border-width: 1pt;border-style: solid;border-color: windowtext;padding: 0pt 5.4pt;"><p style="text-align:center;"><span style="font-family:Calibri;mso-ascii-font-family:Calibri;mso-fareast-font-family:宋体;font-variant:normal;text-transform:none;"><span leaf="">10%</span></span></p></td><td data-colwidth="189" width="189" valign="middle" style="border-width: 1pt;border-style: solid;border-color: windowtext;padding: 0pt 5.4pt;"><p style="text-align:center;"><span style="font-family:宋体;mso-ascii-font-family:Calibri;font-variant:normal;text-transform:none;"><span leaf="">拉完了</span></span></p></td></tr></tbody></table>  
  
   
  
  
我靠，我是真没想到，GLM 5.1 写的靶场，被 DeepSeek v4 pro 发现了 bug，并利用 bug 完美提权。Glm 自己竟然没发现后端代码有问题。DeepSeek v4 果然名不虚传。 修改 bug 后再次测试，就不行了，也是给了四次提示才能成功。  
  
本次实验得到的结果：  
  
ai 在多层逻辑漏洞还是存在不足。  
  
国外模型暂未测试。  
  
如果你是一个长期主义者，欢迎加入我的知识星球，本星球日日更新,包含号主大量一线实战,全网独一无二，微信识别二维码付费即可加入，如不满意，72 小时内可在 App 内无条件自助退款  
  
![](https://mmbiz.qpic.cn/mmbiz_png/YmmVSe19Qj5EMr3X76qdKBrhIIkBlVVyuiaiasseFZ9LqtibyKFk7gXvgTU2C2yEwKLaaqfX0DL3eoH6gTcNLJvDQ/640?wx_fmt=png&from=appmsg "")  
  
往期回顾  
#   
# 如何利用ai辅助挖漏洞  
#   
# 如何在移动端抓包-下  
#   
# 如何绕过签名校验  
#   
  
[一款bp神器](http://mp.weixin.qq.com/s?__biz=MzIzMTIzNTM0MA==&mid=2247495880&idx=1&sn=65d42fbff5e198509e55072674ac5283&chksm=e8a5faabdfd273bd55df8f7db3d644d3102d7382020234741e37ca29e963eace13dd17fcabdd&scene=21#wechat_redirect)  
  
  
[挖掘有回显ssrf的隐藏payload](https://mp.weixin.qq.com/s?__biz=MzIzMTIzNTM0MA==&mid=2247496898&idx=1&sn=b6088e20a8b4fc9fbd887b900d8c5247&scene=21#wechat_redirect)  
  
  
[ssrf绕过新思路](http://mp.weixin.qq.com/s?__biz=MzIzMTIzNTM0MA==&mid=2247495841&idx=1&sn=bbf477afa30391b8072d23469645d026&chksm=e8a5fac2dfd273d42344f18c7c6f0f7a158cca94041c4c4db330c3adf2d1f77f062dcaf6c5e0&scene=21#wechat_redirect)  
  
  
[一个辅助测试ssrf的工具](http://mp.weixin.qq.com/s?__biz=MzIzMTIzNTM0MA==&mid=2247496380&idx=1&sn=78c0c4c67821f5ecbe4f3947b567eeec&chksm=e8a5f8dfdfd271c935aeb4444ea7e928c55cb4c823c51f1067f267699d71a1aad086cf203b99&scene=21#wechat_redirect)  
  
  
[dom-xss精选文章](http://mp.weixin.qq.com/s?__biz=MzIzMTIzNTM0MA==&mid=2247488819&idx=1&sn=5141f88f3e70b9c97e63a4b68689bf6e&chksm=e8a61f50dfd1964692f93412f122087ac160b743b4532ee0c1e42a83039de62825ebbd066a1e&scene=21#wechat_redirect)  
  
  
[年度精选文章](http://mp.weixin.qq.com/s?__biz=MzIzMTIzNTM0MA==&mid=2247487187&idx=1&sn=622438ee6492e4c639ebd8500384ab2f&chksm=e8a604b0dfd18da6c459b4705abd520cc2259a607dd9306915d845c1965224cc117207fc6236&scene=21#wechat_redirect)  
  
  
[Nuclei权威指南-如何躺赚](http://mp.weixin.qq.com/s?__biz=MzIzMTIzNTM0MA==&mid=2247487122&idx=1&sn=32459310408d126aa43240673b8b0846&chksm=e8a604f1dfd18de737769dd512ad4063a3da328117b8a98c4ca9bc5b48af4dcfa397c667f4e3&scene=21#wechat_redirect)  
  
  
[漏洞赏金猎人系列-如何测试设置功能IV](http://mp.weixin.qq.com/s?__biz=MzIzMTIzNTM0MA==&mid=2247486973&idx=1&sn=6ec419db11ff93d30aa2fbc04d8dbab6&chksm=e8a6079edfd18e88f6236e237837ee0d1101489d52f2abb28532162e2937ec4612f1be52a88f&scene=21#wechat_redirect)  
  
  
[漏洞赏金猎人系列-如何测试注册功能以及相关Tips](http://mp.weixin.qq.com/s?__biz=MzIzMTIzNTM0MA==&mid=2247486764&idx=1&sn=9f78d4c937675d76fb94de20effdeb78&chksm=e8a6074fdfd18e59126990bc3fcae300cdac492b374ad3962926092aa0074c3ee0945a31aa8a&scene=21#wechat_redirect)  
  
[‍](http://mp.weixin.qq.com/s?__biz=MzIzMTIzNTM0MA==&mid=2247486764&idx=1&sn=9f78d4c937675d76fb94de20effdeb78&chksm=e8a6074fdfd18e59126990bc3fcae300cdac492b374ad3962926092aa0074c3ee0945a31aa8a&scene=21#wechat_redirect)  
  
  
  
  
  

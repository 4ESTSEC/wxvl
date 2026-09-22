#  简单利用NCC免费提供的模型进行渗透测试漏洞挖掘  
原创 陌笙
                    陌笙  陌笙不太懂安全   2026-09-21 09:39  
  
免责声明  
```
由于传播、利用本公众号所提供的信息而造成
的任何直接或者间接的后果及损失，均由使用
者本人负责，公众号陌笙不太懂安全及作者不
为此承担任何责任，一旦造成后果请自行承担！
如有侵权烦请告知，我们会立即删除并致歉，谢谢！
```  
  
前言  
  
众所周知，NCC平台，每天登录就会给50积分，积分可以用来使用cyber-model-1模型，本着不嫖白不嫖的想法，必须白嫖使用一下。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboSUz3I6XDGBchpfwC66MZV9JAMbxrz3ovX00yTbJvEuS7lbKXMfOZqQ616w80OrtozC9NA7qCrslg2zJ6PccvLIbSNsEM0hWiag/640?wx_fmt=png&from=appmsg "")  
  
  
积分领取的话，直接访问这个注册登录就可以自动领取，这个是我的邀请链接，使用我的链接注册，双方都可以获得一些，模型积分。  
```
https://nccsec.cn/login?invite=RaK2xdTj8gE
```  
  
拿到积分之后来到ccSwitch里面配置就可以  
  
  
环境配置  
  
  
具体配置的话  
  
  
拿到积分之后先新建令牌  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboQxN7A16yWMvLbcJuCDbUvia6dVlxCkmkP5ibFnMxJrxnpPhv3ZkEht0Riagk5NdfdZ5ZXfBUXYLu4kj8IcSXgnuVCrKdJXCVmjfU/640?wx_fmt=png&from=appmsg "")  
  
  
新建令牌之后，把key复制下来  
```
sk-xxxxx
```  
  
然后打开ccSwitch，像我这样操作  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboSTr7S6IxTaHzyDhNE70ibqARSBc5abdSJFicsL65JNibAYPkHVf3xI5zLkzQNnjE5LLjOB5skW8ic0vuFvMr0OcpaJzw1ujGIUM8U/640?wx_fmt=png&from=appmsg "")  
  
  
先添加供应商，这是我的配置  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboSw5OM2gicPeIZKxPFqIopmr5VEqibGhoN3kXgibCDbx3kLzywA6ZEz59MRPFAECNX6pO2dMtsib5QV2eOPQtibw9S2rIGic8tSzZYmw/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboRwlibHMlpSFNgoBKKWjbS9gGk6d8zXeBPRKibbpjc2NjZWuGrTHibWtgjQVjdf1H6uqs2rmqibiaI5dP8s3lLhQsYic8xWt0dFWZu2U/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboTWGFnr6KTmrfTjXZlCvdZlpQZicribAOWn7wDp6hKTfTxGOiabPIhLwz1aOQ7sj6ZOfCyKZL3ankEicDicIadIdWafib2KknbnRy4DU/640?wx_fmt=png&from=appmsg "")  
  
  
如果嫌麻烦可以直接使用我的配置,把key记得替换成自己的就可以  
```
{
  "env": {
    "ANTHROPIC_BASE_URL": "https://ai.nccsec.cn/v1",
    "ANTHROPIC_DEFAULT_SONNET_MODEL_NAME": "cyber-model-1",
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "cyber-model-1[1M]",
    "ANTHROPIC_DEFAULT_OPUS_MODEL_NAME": "cyber-model-1",
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "cyber-model-1[1M]",
    "ANTHROPIC_DEFAULT_FABLE_MODEL": "cyber-model-1[1M]",
    "ANTHROPIC_DEFAULT_FABLE_MODEL_NAME": "cyber-model-1",
    "ANTHROPIC_DEFAULT_HAIKU_MODEL_NAME": "cyber-model-1",
    "ANTHROPIC_DEFAULT_HAIKU_MODEL": "cyber-model-1",
    "CLAUDE_CODE_SUBAGENT_MODEL": "cyber-model-1[1M]",
    "ANTHROPIC_MODEL": "cyber-model-1[1M]",
    "ANTHROPIC_AUTH_TOKEN": "sk-riqro你自己的keyzxcwi"
  },
  "skipDangerousModePermissionPrompt": true,
  "includeCoAuthoredBy": false,
  "reasoning_effort": "high"
}
```  
  
然后开启路由，启动模型  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboTx0s3EUEptLm6anyHj8sVibjuZEtPEsUUavnaWyeXxRP4HlGYz8uTfiaRkT0dGvAgt3JADCfIEEBjh7mebB61EOXFnNW7ea0f08/640?wx_fmt=png&from=appmsg "")  
  
  
然后打开claude,输入你好，看看实际情况  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboQ6plibPKCR5YT10kibxBhxbzDNU96sdWRS435HukCEMo5nI2HADTvHkDupqm9XQXgM4o4vHgwD6NEJlJWDWkNKuWiaGIZlBsbGlI/640?wx_fmt=png&from=appmsg "")  
  
  
像我这样就是ok了，因为本来就是做安全的模型，没啥安全限制  
  
  
我们搞百度测试一下，会让你选择授权方式，同意之后就能直接开干了。  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboRgowpeHO2gpsmw1K8SWP7TBxGNz5icpsYtZHuC9d1etR5icAZz7jxuphC6TicI7vD3FCRMqE6RRvnQCGSDS94o6Ap9KTNnWEZ0PQ/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboTaNJ2LzxayIoC7k15Kw9r4Op7rsk5VTLOXRnxqF5rzUpFeYngfC2icOcTyiaClMeTEp11RCiagRQdVibfiaY5Q4jGElW1bEc9Pm63I/640?wx_fmt=png&from=appmsg "")  
  
  
漏洞挖掘  
  
  
因为模型能力可能不是很强，比不上什么grok,gpt啥的，所以可以直接使用我们的skill,指导挖掘一手  
  
  
挖掘技巧，别人交过的公司，ncc一定是收的  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboSZy4EicM1a4Qakv2gUJBQ7EOZSWDuCB1y0RAWZQe2ymrUH0kIM9MUibKmvILO3ycQn4481lovwEvZicteYMrMPNWA9NKOtoTyJDM/640?wx_fmt=png&from=appmsg "")  
  
  
  
所以我们直接随便找一个，比如这个，然后检索一下  
  
莱爱德心理健康体检系统  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboTp8vLja9PUGnMIyu4YeocGmLCZs9xBwSmDk93lcKtibDLBwQfoVOYI5VlJH4lDLCbQhKH4LyISE5a25Je1ySPGatp0aq6xiao8c/640?wx_fmt=png&from=appmsg "")  
  
  
  
可以看到只存在一个文件上传漏洞，但是一个系统可能只存在这一个漏洞吗？？  
  
  
我们直接fofa检索，我这 只是举个例子，师傅们可以多平台检索  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboRdg4J9SzTqpOCCtJiaibROozUtUXre2Rnvw0UOMJNqRATOvpYdk6XUUWhYFkQicuNxsoiaBlfVBYkxfxxcjnfIWKCuoTh8nCaqia2s/640?wx_fmt=png&from=appmsg "")  
  
  
然后我们就能拿到资产  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboQct282ELYVibr5FYyJvybv0sTsLIEJPR6jW144lkVZ1kIN6kOwERErBKicNrhiavQG6DKNQEX2M41FVoY1MUfmMpfIfwud6VQVicI/640?wx_fmt=png&from=appmsg "")  
  
  
拿到url之后，直接使用ai测试  
```
帮我使用当前目录下面的skill，对目标http://xxx,进行渗透测试，详细分析js，挖掘其中的漏洞,重点挖掘sql注入，和信息泄露漏洞
```  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboSoI9Z6wVLlBPQ8zGzKJbFSsxNthuygs5vb6sxRCwbthCx7IjtdLdqxyaZBal26iazm3avb8xh2Jz5Ir1gAsibtXo3kicHUsnkiclE/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboSkQNEeVkD6mA5KFlOArZ5OoYY6l5Zzlia79h6aic78d87Aiatib0w6gib4J5icmXGLVNdt8sypyFEItibdO3FZjvqic1cKP0ZAzZuzVXA/640?wx_fmt=png&from=appmsg "")  
  
  
然后他就会猛猛帮你干了  
  
  
干完之后，提交报告，然后等着拿钱就行  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboSVcBhBl6qY0hBXuibMx4DPtKTEJdibAbBDapBNFzJYY3esD63zUbxux6XCtHsQTI4SK6ZyKHab0YUPG1ALibJq89hNe1bo5icpInA/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboSJhHTNRXiabJ1qSBVpKsrZHd6ASXlcCUS1GDXWrvYwtD2ExNzRIzicf32p3XdejUPo7RWUtNhmT57n1RnS6Aogw9QDkIFM0qgG0/640?wx_fmt=png&from=appmsg "")  
  
  
  
利用获得的安全币就可以换钱花了，美滋滋，全程免费美滋滋！！  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboTCozwWWqLIfJIVkWJxia6WBLOCnkibEo9VgxIZvgjqwevjiaSxiaXY8dA7s6R7kFN9bUpn1gpm5RlhdCDrFU264bn9VzvT4PN8LdU/640?wx_fmt=png&from=appmsg "")  
  
  
  
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
  
**skill+grok辅助挖掘某企业sr**  
**c****实战效果，能出但是重复多，agent独立挖掘也可以，见仁见智，看个人习惯，好的模型是最重要的。**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboQwyn779TTwY7vZkePQCL8k3K8dYxNdyzfgADL4dJcNUvpmodLeDVCZ6xDC4RJXEBmO2tcWqgUNdTVicKTW0jpdsg7X6xwP5gIQ/640?wx_fmt=png&from=appmsg "")  
  
****  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboQnIqagDL2A4BUIXrib9YVmATWuaIDqETqYd9ToHib52mDyoMyqc6Wzh733FRnbsDsGgey7B8s8jr72UtkPY6ich58niaPJqoItKcE/640?wx_fmt=png&from=appmsg "")  
  
****  
**企业src边缘&核心资产实战效果&&有重复但是证明好模型+AI确实够用**  
  
**（图片仅供参考，我出不等于你出，见识到ai神力即可，多去用AI!!!）**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboT1YNI9U6r6NMO4UMUBROoWeC4XQC4Dge94ODZ7tXY6tbxqb3IJoghve0u1SfkygE5UJ5HTdUBvLZKrn5ps13F71piax5dlHnOE/640?wx_fmt=png&from=appmsg "")  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboTGjdxKy4nllaLXIznTvRqicichITccuB8psYFRYakw6ViauCk6iccziahfPw4fnrqhyCp7Zkq7lRI0DOicyrZlNyicYibTVibfGFZVaG0c/640?wx_fmt=png&from=appmsg "")  
  
****  
**不是P图,单洞1.2w记录**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboQQpQdR2Ttwqxibyr75Is0kBG2N2tLYQIaau7SS278oyQ4RDpNScviaMt4wtlfgDCibE05WgoMhE5kZUrP8ciaYIdnxA594wsmoAAs/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboSRn2EsfFkA5mG6dcn7JLQMroc2dy3EQb3ueY2Cspd0WYgicXEnSF68UD43nNd4plkxmkTpEOh2kkQMEWZZIjE0ibA8r1q4IfiaxI/640?wx_fmt=png&from=appmsg "")  
  
****  
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
  
![](https://mmbiz.qpic.cn/mmbiz_png/MSDUaqtwboRbZ1HYm7R7YEiaxRVQibGWyricx9l7HpGjS4ZfWRdlft8iacwkpzYyZfmYEkWdJgYRORPkNFR6dADR5MyE524tWX6cAwN8MmrCZu0/640?wx_fmt=png&from=appmsg "")  
  
  
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
  
  
**圈友skill+ai辅助渗透**  
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
  
**目前800多条内容，扫描下方二维码查看详情以及加入圈子，持续更新中。。**  
  
**如果觉得合适可以加入，价格不定期会根据圈子内容和圈子人数进行上调。。**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/MSDUaqtwboS6aWPIgWkKicUwu8ZiamtqWhg7UcFK22okQrXnQcTZxiaXFVrl4QXmUMxrSOic69VyUlQictbauRDrKXllL810lAWMOtcOvb9taUAM/640?wx_fmt=png&from=appmsg "")  
  
  

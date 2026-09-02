#  Langflow 严重漏洞被用于窃取 OpenAI 和 AWS 密钥  
Bill Toulas
                    Bill Toulas  代码卫士   2026-09-02 12:09  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/Az5ZsrEic9ot90z9etZLlU7OTaPOdibteeibJMMmbwc29aJlDOmUicibIRoLdcuEQjtHQ2qjVtZBt0M5eVbYoQzlHiaw/640?wx_fmt=gif "")  
    
聚焦源代码安全，网罗国内外最新资讯！  
  
编译：代码卫士  
  
**威胁情报公司****VulnCheck****公司称，****Langflow****自定义组件编辑器的代码验证器中的一个未认证远程代码执行漏洞（****CVE-2026-0768****）正被用于窃取凭证、令牌和密钥。****Langflow****是一个用于构建****AI****应用程序的开源框架。**  
  
VulnCheck   
公司在其位于英国的蜜罐中检测到了这一活动，上周末至少有  
 50   
次利用尝试针对这些蜜罐发起，攻击流量主要来自俄罗斯。  
VulnCheck   
的首席安全研究员  
 Caitlin Condon   
表示，该活动有所加剧，截至今日，观察到的攻击总数已增至  
 360   
次。  
  
据  
 Condon   
称，攻击者进行侦察并查询环境变量，以窃取  
 Langflow   
实例的管理员凭证或超级用户认证密钥、  
AWS   
密钥以及  
 OpenAI API   
密钥。  
Condon   
解释说：  
“  
攻击者的请求会查询环境变量（  
LANGFLOW_SUPERUSER  
、  
OPENAI_API*  
、  
AWS_ACCESS*  
、  
AWS_SECRET*  
），读取  
 /root/.cache/langflow/secret_key  
，并检查  
 .ssh   
访问权限和  
 .bash_history   
大小。  
”  
  
Langflow   
是一个基于  
 Python   
的开源低代码平台，用于构建  
 AI   
应用程序、智能体、聊天机器人和检索增强生成  
 (RAG)   
系统。它允许用户通过连接语言模型、提示词、数据库、  
API   
和其它工具的组件，在图形界面中创建工作流。  
  
CVE-2026-0768   
漏洞于  
 1   
月份披露，影响  
 Langflow 1.4.2   
及更早版本。该漏洞允许未经身份验证的攻击者以  
 root   
权限执行任意代码。漏洞描述中写道：“该特定缺陷存在于对提供给  
 validate   
端点的代码参数的处理中。问题源于在使用用户提供的字符串执行  
 Python   
代码之前，缺乏对其进行适当的验证。  
”      
  
趋势科技  
 ZDI   
指出，该漏洞源于在使用用户提供的字符串执行  
 Python   
代码之前，缺乏对其的适当验证。  
Condon   
表示，目前尚无公开的概念验漏洞利用代码。  
  
CVE-2026-0768   
并非今年被利用的第一个  
 Langflow   
漏洞。  
3   
月份，攻击者在披露后约一天内就利用了严重的代码注入漏洞  
 CVE-2026-33017  
，执行  
 Python   
脚本并窃取  
 .ENV   
和数据库文件。  
  
随后，攻击者又利用  
 CVE-2026-5027   
向易受攻击的服务器写入任意文件，并利用  
 CVE-2026-55255   
访问其他用户的  
 AI   
工作流、窃取敏感数据以及投放第二阶段植入。攻击者还利用  
 CVE-2026-0770   
以  
 root   
权限执行命令，并试图部署恶意软件以及提取云凭证、环境变量和容器元数据。最近，  
CISA   
警告称，在多个概念验证漏洞利用代码公开发布后，  
CVE-2026-9198   
已遭活跃利用。  
  
建议  
 Langflow   
用户升级到最新可用版本  
 1.11.6  
，该版本修复了该热门工具中的所有已知缺陷。  
  
  
代码卫士试用地址：https://sast.qianxin.com/  
  
开源卫士试用地址：https://oss.qianxin.com/  
  
  
  
  
  
  
  
  
  
**推荐阅读**  
  
[Langflow 严重漏洞可导致未认证远程代码执行后果](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247526389&idx=2&sn=a692a0a18f59def9c6fa164c8ac2ccec&scene=21#wechat_redirect)  
  
  
[Langflow 高危漏洞被用于未认证RCE攻击](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247526269&idx=2&sn=0d7a31b3a12799330b0f67e3123dcd7c&scene=21#wechat_redirect)  
  
  
[严重的Langflow RCE 漏洞被用于攻击AI app 服务器](https://mp.weixin.qq.com/s?__biz=MzI2NTg4OTc5Nw==&mid=2247522938&idx=1&sn=d6e3777945383ca1a0f8df487903c8e5&scene=21#wechat_redirect)  
  
  
  
  
  
**原文链接**  
  
https://www.bleepingcomputer.com/news/security/critical-langflow-flaw-exploited-to-steal-openai-and-aws-keys  
  
  
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
” 吧~  
  

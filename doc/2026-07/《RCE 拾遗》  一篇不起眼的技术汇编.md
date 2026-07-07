#  《RCE 拾遗》 | 一篇不起眼的技术汇编  
陈默
                    陈默  默识信安   2026-07-07 03:20  
  
## 写在前面  
  
过去一段时间，我把 RCE 相关的常见攻击面、利用技巧和一些实战中反复会碰到的问题做了系统整理，最终形成了《RCE 拾遗》。  
  
全文约 180 页，涉及命令注入、代码注入与模板注入、反序列化、文件上传与文件操作、协议与扩展利用链、云原生与供应链场景、权限维持与痕迹处理等多个子领域。  
  
AI 那么方便，为什么还要整理？  
  
基于 AI 的渗透工具越来越多，智能化程度越来越高。当 Agent 卡住或判断出错时，需要有人能及时接管并做出决策。  
  
更重要的是，AI 正在成为新的攻击面，前不久已经爆出 Anthropic 在 Claude Code 中植入间谍软件。每一次使用 AI 时，是否考虑过数据被窃取、隐私被泄露、行为被监控？  
## 内容概览  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/SbE9QN9lhPDu8sJQ9tzaJqSf0uKXfHfaG55UOJ50sz1AnmG1NawcGgPdoQDO3MwcxPkX56RlPGXSxfgEkoMBTB50eRSuoV2rNgl8Nl3gUibE/640?wx_fmt=jpeg&from=appmsg "")  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/SbE9QN9lhPAibEZjkGm4icDK00sXIvwQDfpPrRxVNSHDvvRP45q2B7lxXe7NCIkMcd7QLVFQtTp910sWu8ibSNCZAghCAibpcAY8VS49GRJVwia0/640?wx_fmt=jpeg&from=appmsg "")  
![](https://mmbiz.qpic.cn/mmbiz_jpg/SbE9QN9lhPDwes0OcHLqibtdh4roJVicv8iagGm6cKNpok3QROcH0IRfoZPCZwaT8YQeAD36AYPjnctU6ib28qMtCzgicF97ibU2SffH97M86ery8/640?wx_fmt=jpeg&from=appmsg "")  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/SbE9QN9lhPCFFS78ZBYWoHFjJc0iaricvjUgzs9XJhibFWtibeKiakL35JtaibllZNoRaojBPTIGSaSqFP5IoAW20pcGM6WI7icyFgTpJMquIFL17A/640?wx_fmt=jpeg&from=appmsg "")  
![](https://mmbiz.qpic.cn/mmbiz_jpg/SbE9QN9lhPA2RELWq6SU8uxLv1ca1pQJWuXtvrglEOkLLhJac3CvLRvANLoYZlnDgFYTxDNfwny4kDcd9X7dcMibMYtHcQJQokIXwiakbJGWs/640?wx_fmt=jpeg&from=appmsg "")  
![](https://mmbiz.qpic.cn/mmbiz_jpg/SbE9QN9lhPChzoJGB7rtYHdA1iclJh7Fvsu9kk72oLwIKp14r8icYdry2AC8w8wic5OJbQuJ3zCibh56RrIgnibzKLuvP0OlBsjgibzj60Hg7Siapc/640?wx_fmt=jpeg&from=appmsg "")  
![](https://mmbiz.qpic.cn/mmbiz_jpg/SbE9QN9lhPBu4ib07ic3H1MicdgaMJvnv8QsAcmqY1DFu8DK4Ib5Q5dwRibZiaKiaUundiaIYYgcoxBFbBiaHpgNhueibstjN15gpxyGqxMibzfC4DFU8/640?wx_fmt=jpeg&from=appmsg "")  
  
本文在编写过程中借助了 AI 工具进行部分内容的初步编写，作者对关键技术点逐一验证、实测和校对。但疏漏在所难免，如发现错误或有更好的思路，欢迎通过公众号交流。  
## 提示  
  
**「本文内容仅供合法授权的渗透测试与教育场景使用。任何未授权的渗透测试均属违法。」**  
## 获取方式  
```
https://wwbco.lanzouu.com/i2HKj3uw4qmd
```  
  
  
  

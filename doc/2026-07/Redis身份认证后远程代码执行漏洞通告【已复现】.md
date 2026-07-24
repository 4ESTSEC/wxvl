#  Redis身份认证后远程代码执行漏洞通告【已复现】  
新华三盾山实验室
                    新华三盾山实验室  新华三主动安全   2026-07-24 03:27  
  
![](https://mmecoa.qpic.cn/sz_mmecoa_png/kJFdfNJcFJD9teZvfeRICMsEOpzlgxlfyvXYjKS4JK2cRu54fOhjuMCARwy5UrtJBXzZmpKbprvAgE0dXBdNuUJQ7LQMLV4ndoTgaibXdXyQ/640?from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/yQjsZ8zq83vaCLpjmibq0tfSX4HJvOlKMU5W6JRKAKUCyIDknBF6ibvicZR8wjKzUicKZftuShzQso5qqU9KjGDiaOPI8ibDTNOROSy17egXiaN7Q4/640?from=appmsg "")  
  
  
01  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/yWSKK1skPzibT36tVa3SX5ILYicuiaEV46R4FkmNhwSics3Pobe6rCc0ha4PM4hLgcq1Qmp7icx7ZPy2OAAnz2zGibQoOVGfO2dMjtJ8ibj60seFx8/640?from=appmsg "")  
  
漏洞综述  
  
  
1.1漏洞背景  
  
Redis 是一款开源的内存数据存储系统，支持字符串、哈希、列表、集合及流数据等多种数据结构，广泛应用于缓存、消息队列、会话存储和实时数据处理等场景。近日，新华三盾山实验室监测到安全研究人员公开了一个 Redis 身份认证后远程代码执行漏洞的完整利用代码，当前该漏洞暂无 CVE 编号。攻击者成功利用该漏洞后，可在 Redis 服务进程权限下执行任意命令，进而控制服务器。  
  
1.2 漏洞详情  
  
该漏洞源于 Redis 流数据类型在恢复消费组状态时的内存管理缺陷。消费组用于协调多个消息处理客户端分配任务，Redis 会为尚未确认的消息维护待处理记录。攻击者通过身份认证并取得 RESTORE、XGROUP 等命令权限后，可导入特制数据，使两个客户端错误地引用同一记录。删除客户端时，该记录会被重复释放，攻击者可进一步破坏进程内存并执行任意代码。  
  
1.3 漏洞复现  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/SLl77fibWWLZqdq37puPstnd52TPBcWZfsWKFe04fpzENicGvdkHuFmvfPb9FhGCbOL6FebWmS3e89uiahmQ2Nb01J8l9HwiaKCnpVaFeSG44uo/640?wx_fmt=png&from=appmsg "")  
  
  
02  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/yWSKK1skPzibT36tVa3SX5ILYicuiaEV46R4FkmNhwSics3Pobe6rCc0ha4PM4hLgcq1Qmp7icx7ZPy2OAAnz2zGibQoOVGfO2dMjtJ8ibj60seFx8/640?from=appmsg "")  
  
影响范围  
  
  
Redis 6.2.22、7.4.9、8.6.4、8.8.0  
  
  
03  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/yWSKK1skPzibT36tVa3SX5ILYicuiaEV46R4FkmNhwSics3Pobe6rCc0ha4PM4hLgcq1Qmp7icx7ZPy2OAAnz2zGibQoOVGfO2dMjtJ8ibj60seFx8/640?from=appmsg "")  
  
严重等级  
  
  
<table><tbody><tr style="mso-yfti-irow:0;mso-yfti-firstrow:yes;"><td data-colwidth="170" width="170" valign="top" style="border: 1pt solid windowtext;padding: 0cm 5.4pt;"><p style="text-align:center;text-indent:0cm;mso-char-indent-count:
  0;"><span style="mso-bookmark:_Toc412817997;"><span style="font-family:宋体;mso-ascii-font-family:Arial;mso-hansi-font-family:Arial;"><span leaf="" mpa-font-style="mryaatnf1b8a" style="font-size: 15px;font-family: &#34;mp-quote&#34;, -apple-system-font, BlinkMacSystemFont, &#34;Helvetica Neue&#34;, &#34;PingFang SC&#34;, &#34;Hiragino Sans GB&#34;, &#34;Microsoft YaHei UI&#34;, &#34;Microsoft YaHei&#34;, Arial, sans-serif;" data-mpa-action-id="mryaatnr1ex7" data-pm-slice="0 0 []">威胁等级</span></span></span></p></td><td data-colwidth="180" width="180" valign="top" style="border-width: 1pt 1pt 1pt medium;border-style: solid solid solid none;border-color: windowtext windowtext windowtext currentcolor;border-image: none;padding: 0cm 5.4pt;"><p style="text-align:center;text-indent:0cm;mso-char-indent-count:
  0;"><span style="mso-bookmark:_Toc412817997;"><span style="font-family:宋体;mso-ascii-font-family:Arial;mso-hansi-font-family:Arial;color:red;"><span leaf="" mpa-font-style="mrya9mxyfr3" style="font-size: 15px;" data-mpa-action-id="mrya9my518ae" data-pm-slice="0 0 []">高危</span></span></span></p></td></tr><tr style="mso-yfti-irow:1;"><td data-colwidth="170" width="170" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor windowtext windowtext;border-image: none;padding: 0cm 5.4pt;"><p style="text-align:center;text-indent:0cm;mso-char-indent-count:
  0;"><span style="mso-bookmark:_Toc412817997;"><span style="font-family:宋体;mso-ascii-font-family:Arial;mso-hansi-font-family:Arial;"><span leaf="" mpa-font-style="mrya9qst1frd" style="font-size: 15px;" data-mpa-action-id="mrya9qt11hed" data-pm-slice="0 0 []">影响程度</span></span></span></p></td><td data-colwidth="180" width="180" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor windowtext windowtext currentcolor;padding: 0cm 5.4pt;"><p style="text-align:center;text-indent:0cm;mso-char-indent-count:
  0;"><span style="mso-bookmark:_Toc412817997;"><span style="font-family:宋体;mso-ascii-font-family:Arial;mso-hansi-font-family:Arial;color:red;"><span leaf="" mpa-font-style="mrya9kf8do4" style="font-size: 15px;" data-mpa-action-id="mrya9kfgi59" data-pm-slice="0 0 []">广泛</span></span></span></p></td></tr><tr style="mso-yfti-irow:2;"><td data-colwidth="170" width="170" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor windowtext windowtext;border-image: none;padding: 0cm 5.4pt;"><p style="text-align:center;text-indent:0cm;mso-char-indent-count:
  0;"><span style="mso-bookmark:_Toc412817997;"><span style="font-family:宋体;mso-ascii-font-family:Arial;mso-hansi-font-family:Arial;"><span leaf="" mpa-font-style="mrya9ucl1b1o" style="font-size: 15px;" data-mpa-action-id="mrya9ucsnd9" data-pm-slice="0 0 []">利用价值</span></span></span></p></td><td data-colwidth="180" width="180" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor windowtext windowtext currentcolor;padding: 0cm 5.4pt;"><p style="text-align:center;text-indent:0cm;mso-char-indent-count:
  0;"><span style="mso-bookmark:_Toc412817997;"><span style="font-family:宋体;mso-ascii-font-family:Arial;mso-hansi-font-family:Arial;color:red;"><span leaf="" mpa-font-style="mryaa5l653p" style="font-size: 15px;" data-mpa-action-id="mryaa5lgrud" data-pm-slice="0 0 []">高</span></span></span></p></td></tr><tr style="mso-yfti-irow:3;"><td data-colwidth="170" width="170" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor windowtext windowtext;border-image: none;padding: 0cm 5.4pt;"><p style="text-align:center;text-indent:0cm;mso-char-indent-count:
  0;"><span style="mso-bookmark:_Toc412817997;"><span style="font-family:宋体;mso-ascii-font-family:Arial;mso-hansi-font-family:Arial;"><span leaf="" mpa-font-style="mrya9yeema7" style="font-size: 15px;" data-mpa-action-id="mrya9yeo1m31" data-pm-slice="0 0 []">利用难度</span></span></span></p></td><td data-colwidth="180" width="180" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor windowtext windowtext currentcolor;padding: 0cm 5.4pt;"><p style="text-align:center;text-indent:0cm;mso-char-indent-count:
  0;"><span style="mso-bookmark:_Toc412817997;"><span style="font-family:宋体;mso-ascii-font-family:Arial;mso-hansi-font-family:Arial;color:red;"><span leaf="" mpa-font-style="mryaa24nav8" style="font-size: 15px;" data-mpa-action-id="mryaa24x4bp" data-pm-slice="0 0 []">中</span></span></span></p></td></tr><tr style="mso-yfti-irow:4;mso-yfti-lastrow:yes;"><td data-colwidth="170" width="170" valign="top" style="border-width: medium 1pt 1pt;border-style: none solid solid;border-color: currentcolor windowtext windowtext;border-image: none;padding: 0cm 5.4pt;"><p style="text-align:center;text-indent:0cm;mso-char-indent-count:
  0;"><span style="mso-bookmark:_Toc412817997;"><span style="font-family:宋体;mso-ascii-font-family:Arial;mso-hansi-font-family:Arial;"><span leaf="" mpa-font-style="mryaafkhxmg" style="font-size: 15px;" data-mpa-action-id="mryaafkr1phr" data-pm-slice="0 0 []">漏洞评分</span></span></span></p></td><td data-colwidth="180" width="180" valign="top" style="border-width: medium 1pt 1pt medium;border-style: none solid solid none;border-color: currentcolor windowtext windowtext currentcolor;padding: 0cm 5.4pt;"><p style="text-align:center;text-indent:0cm;mso-char-indent-count:
  0;"><span style="mso-bookmark:_Toc412817997;"><span style="font-family:宋体;mso-ascii-font-family:Arial;mso-hansi-font-family:Arial;color:red;"><span leaf="" mpa-font-style="mryaaiohmvf" style="font-size: 15px;" data-mpa-action-id="mryaaioq18nh" data-pm-slice="0 0 []">暂无</span></span></span></p></td></tr></tbody></table>  
  
  
04  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/yWSKK1skPzibT36tVa3SX5ILYicuiaEV46R4FkmNhwSics3Pobe6rCc0ha4PM4hLgcq1Qmp7icx7ZPy2OAAnz2zGibQoOVGfO2dMjtJ8ibj60seFx8/640?from=appmsg "")  
  
处置方法  
  
  
4.1 官方补丁  
  
https://github.com/redis/redis/releases  
  
4.2缓解措施  
1. 新华三安全设备防护方案 新华三IPS规则库将在1.0.415版本支持对该漏洞的识别，新华三全系安全产品可通过升级IPS特征库识别该漏洞的攻击流量，并进行主动拦截。  
  
1. 新华三态势感知解决方案 新华三态势感知已支持该漏洞的检测，通过信息搜集整合、数据关联分析等综合研判手段，发现网络中遭受该漏洞攻击及失陷的资产。  
  
1. 新华三云安全能力中心解决方案  
  
新华三云安全能力中心知识库已更新该漏洞信息，可查询对应漏洞产生原理、升级补丁、修复措施等。  
  
  
05  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/yWSKK1skPzibT36tVa3SX5ILYicuiaEV46R4FkmNhwSics3Pobe6rCc0ha4PM4hLgcq1Qmp7icx7ZPy2OAAnz2zGibQoOVGfO2dMjtJ8ibj60seFx8/640?from=appmsg "")  
  
参考链接  
  
  
https://github.com/berabuddies/redis-poc  
  
https://github.com/redis/redis  
  
  
  
  
  
  

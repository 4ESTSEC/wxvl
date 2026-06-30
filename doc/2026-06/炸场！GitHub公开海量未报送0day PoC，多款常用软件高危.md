#  炸场！GitHub公开海量未报送0day PoC，多款常用软件高危  
原创 hacking
                    hacking  Hacking黑白红   2026-06-30 05:44  
  
近日GitHub匿名账号bikini上线仓库 exploitarium ，短时间热度暴涨，收获2.3k Star、534次Fork。  
  
可谓是匿名仓库爆火，大量零日漏洞利用代码开源，  
引发全网网安圈震动。  
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/TVljsu2eAicIRCicGEVZEcVxuIibzxHsAetOEA4h9WELsXeQ1P4TccO5CatKzsr0r9vlBPqlp5UGIJD5AmJYIMYDyUicyVsyKrJ7tlcmeib6WZTU/640?wx_fmt=jpeg "")  
  
  
项目地址：https://github.com/bikini/exploitarium  
  
  
仓库内批量上传全套可直接利用的漏洞PoC，关键风险点在于：  
  
  
这批漏洞均未提前同步厂商，属于未修复0day零日漏洞，攻击者拿到代码即可发起渗透攻击，厂商暂无对应补丁。  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/TVljsu2eAicJk1y2ltXgKu5c72oia3HRiaUOJKfszC690Af3epLwboZwP6VL6Z9GBfprrlhDSHkqOq0nWY1tyliaVtvcAibsCHg4ctyTTTTHnWlI/640?wx_fmt=jpeg "")  
  
 覆盖品类十分全面，包含桌面工具、服务器组件、Web框架、容器、远程控制软件、浏览器等。  
  
  
波及大众高频使用产品：  
  
7-Zip、Docker、Firefox、RustDesk、PHP 8.5.7、Nmap、FFmpeg、OpenVPN等，企业与个人终端均大面积暴露风险。   
仓库作者初衷，暗藏巨大安全隐患 仓库自述内容显示，作者并未走正规漏洞报送流程，称公开PoC是为吸引新人进入安全研究领域，还提醒用户不要恶意滥用。  
  
但PoC完整可执行，门槛极低，黑产、黑客无需专业漏洞挖掘能力，复制代码就能实施远程代码执行、权限绕过、本地提权、文件逃逸、数据窃取等攻击。以往稀缺的0day攻击武器，如今全网公开下载，大幅降低网络攻击成本。   
涉及典型高危漏洞一览 仓库收录数十套完整利用链，典型风险包括： 1. Docker容器拷贝路径逃逸、AnyDesk打印机COM身份伪造；  
  
2. Firefox浏览器私密链接数据窃取、Flowise API网关远程代码执行；  
  
3. RustDesk会话权限绕过、7ZIP rar5权限提升链漏洞；  
  
4. PHP、libssh2、Nmap、ImageMagick等开源组件内存破坏漏洞。   
企业&个人紧急防护建议 1. 资产梳理：自查服务器、办公终端是否部署上述受影响软件，暂时限制公网暴露管理端口；  
  
2. 访问管控：远程控制、容器服务仅内网访问，开启IP白名单与双因素认证；  
  
3. 流量监测：部署IPS、沙箱，重点监控异常代码执行、文件外发行为；  
  
4. 持续跟踪：同步厂商安全公告，漏洞补丁发布后第一时间升级；  
  
5. 红线提醒：禁止运维、开发人员下载、传播该仓库PoC，避免合规与安全风险。 零日漏洞无补丁窗口期风险极高，本次批量公开事件，给政企、互联网企业敲响警钟，需立刻开展内部资产自查，缩小攻击面。  
  
  
作者：hacking。前北漂程序员，现在做安全。文章数据来自网络，大模型优化，侵权删。  
  

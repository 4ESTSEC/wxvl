#  UFlowBot：多漏洞传播的新型UDP僵尸网络  
威胁情报团队
                    威胁情报团队  中国电信安全   2026-09-30 02:33  
  
![图片](https://mmbiz.qpic.cn/mmbiz_gif/Dh3fqSPAOWekCSIf3ffuFuiaBPl4BSArBsDhFEMSOTbeIfb7mdz4D0mDExZesv4PPicUdsOTxfRUx8QntAMTmTBA/640?wx_fmt=gif&wxfrom=5&wx_lazy=1&tp=webp#imgIndex=0 "")  
  
一、概述  
  
  
  
近期，中国电信安全公司威胁情报中心发现一起新型僵尸网络攻击活动，攻击者以互联网暴露的IoT及网络设备为主要攻击面，持续开展大范围漏洞利用，攻击目标覆盖路由器、工业网关、摄像头等多类设备。从漏洞使用情况来看，本次攻击活动所使用的漏洞覆盖多个披露年份，其中近年来披露的漏洞占比较高，反映出攻击者具备较强的漏洞整合与利用能力，并呈现出明显的规模化利用特征。  
  
对通过漏洞传播获取的二进制样本进行分析发现，该样本具备DDoS攻击功能模块，表明其可利用受控设备发起网络攻击，具有明显的僵尸网络特征。此外，样本还具备端口转发、代理等网络通信能力，可将受控设备进一步作为流量转发节点使用。在通信协议方面，样本采用UDP协议进行通信，其通信机制区别于传统由受控主机主动连接固定C2的模式，而是通过监听指定端口被动接收C2控制数据，实现对受控设备的远程控制。综合其DDoS攻击能力、网络转发能力及独特的控制通信机制，将该样本归类为一种新型僵尸网络，并命名为UFlowBot。  
  
二、漏洞传播  
  
  
  
自9月12日起，威胁情报中心持续监测到IP地址77.239.124[.]131针对多个蜜罐探针节点发起漏洞利用攻击，涉及80余个已经公开披露的漏洞，相关漏洞的披露年份覆盖2011年至2026年，其中2022年至2026年披露的漏洞数量占比约为63.4%，这表明攻击者并未局限于利用传统历史漏洞，而是能够快速跟进近年来披露的新漏洞并将其应用于攻击活动，体现出该僵尸网络作者较强的漏洞跟踪、整合与利用能力。  
  
从漏洞类型来看，攻击目标覆盖范围广泛，既包括Apache、PHP等Web组件及应用相关漏洞，也包括路由器、工业网关、摄像头等IoT及网络设备固件中的高危漏洞。相关漏洞信息如下表所示。  
  
![](https://mmecoa.qpic.cn/sz_mmecoa_png/RiaMmbYzV5MgZLEGfibkQtHfJKNUHmibcIDyIUcHCEXe0LXsGFibjW1mQ9nn8wS0Q6mGQ5ImjjWxMxSlQoqCXyAHtfHLgbuCKjk2wF1MiaHTRQC4/640?wx_fmt=png&from=appmsg "")  
  
用户可通过电信安全情报平台（https://ti.ctct.cn）进行查询，获取漏洞描述详情、风险等级及对应的修复或缓解措施，帮助企业和个人及时发现并修复漏洞，降低安全风险，提升整体防护能力。  
  
![](https://mmecoa.qpic.cn/mmecoa_png/RiaMmbYzV5Miab791fV4nV5nkKwyfEjolr6iaJOiaibJZ9xfKkGKicE398GiavZUh9sn7HkdRU2kiaCmgISyo364j9d8FbTXk9EX2lhNZf1kickSNYus/640?wx_fmt=png&from=appmsg "")  
  
三、样本分析  
  
  
  
**3. 1Shell脚本分析**  
  
  
在漏洞利用成功以后，目标设备会从主机120.193.219[.]210下载初始入侵Shell脚本。脚本执行后首先遍历/proc/mounts，识别挂载点位于 /proc/[PID]且文件系统类型异常的挂载项，并对相关挂载进行卸载，同时强制终止对应进程；随后进一步遍历系统进程，通过检查 /proc/[PID]/exe，终止可执行文件位于/tmp/目录下的进程。攻击者通过上述两种方式清理设备中可能存在的其他恶意程序，以此实现对系统资源的独占。  
  
![](https://mmecoa.qpic.cn/mmecoa_png/RiaMmbYzV5MgRJgRJsYY4zC8a2ruTHU9PUXNRicJiaoWkAAR1ibg8GgOxGhMZxZiapVBGLtrWASVR9MdMiaByUxgH7Te59Bk0qpWuxHI3iaTE5hmvU/640?wx_fmt=png&from=appmsg "")  
  
随后，脚本遍历预置的多架构二进制文件列表，根据不同架构名称从主机120.193.219[.]210下载对应的恶意程序，覆盖ARM、MIPS、x86等多种处理器架构及其不同版本。为提高在不同设备环境下的兼容性，脚本依次尝试使用wget、busybox wget和curl三种方式下载文件。  
  
![](https://mmecoa.qpic.cn/sz_mmecoa_png/RiaMmbYzV5MgIQOs97TDvJdA3vjHUiaxRp7V8oQDNnMVuk9bYzbNnIFafKeMUG61w9OpCPdcT3vGYj1WepWpCn4tP2DFm0yHCYaOOWxjP3xxA/640?wx_fmt=png&from=appmsg "")  
  
  
**3. 2二进制样本分析**  
  
本文选取x86架构的恶意样本进行分析，样本基本信息如下表所示。  
  
![](https://mmecoa.qpic.cn/mmecoa_png/RiaMmbYzV5Mh0on8OzLVrRqfdajsiboibMwka3GeGDjpd4O9N8B8u2DejLABLkDt8g7up7ufMia54KUqscbzMcoB7GF1qgiaXuvGRLRVqgFYmkwc/640?wx_fmt=png&from=appmsg "")  
  
恶意样本在部分代码实现上参考了Mirai家族。在字符串存储方面，样本借鉴了Mirai的字符串存储方式，但不同于Mirai常见的XOR字符串加密方式，该样本采用ChaCha20加密算法对字符串进行加密。此外，样本通过绑定127.0.0.1的指定端口实现单例运行，避免同一设备上重复启动多个实例。同时，样本通过对watchdog进行篡改，防止其重启设备。  
  
![](https://mmecoa.qpic.cn/mmecoa_png/RiaMmbYzV5MhD26evG5azKcHmZJibAsTmuZFnIJHw7tcB0yHCdEKIt7hw3MW8qAibLyYGZjeKQRrmLpkJ0YQt7yLCZmuYXpbHzRIBJupJYYX5Y/640?wx_fmt=png&from=appmsg "")  
  
恶意样本会依次检查/usr/bin/wget、/bin/wget、/usr/local/bin/wget、/sbin/wget和/usr/sbin/wget等路径，判断受害主机是否存在wget程序。若存在，样本会将原有的wget程序重命名为wget.r，随后将自身复制到原wget所在目录，并命名为wget，以替换系统原有的wget程序。此外，样本还会生成wget.p文件，并将被修改后的wget.r路径写入其中。  
  
完成替换后，当用户或系统进程执行wget命令时，运行的将是恶意样本。恶意样本运行后读取wget.p中记录的wget.r路径，调用原始wget程序完成正常的下载操作，从而实现对wget的命令劫持。  
  
![](https://mmecoa.qpic.cn/sz_mmecoa_png/RiaMmbYzV5MhcB9BENzGKeDrTPciafsib3KL9HAH5SqkiabibsiaN8nW2WJPs98dO0uWpBqvYBMnqcYzUe6LGwvvPicmuUJmiaic1TTiaicd4IPSdQPnGc/640?wx_fmt=png&from=appmsg "")  
  
为实现持久化驻留，恶意样本会将自身复制并伪装为.cling，分别写入/root/.cling和/usr/local/bin/.cling。随后根据目标系统是否存在相应的启动配置文件，向/etc/inittab、/etc/init.d/rcS和/etc/rc.d/rc.boot中追加恶意程序启动命令，使恶意程序能够在系统启动过程中自动执行，从而通过多种启动机制实现持久化驻留。  
  
![](https://mmecoa.qpic.cn/mmecoa_png/RiaMmbYzV5Mia36n4TMZXl0lQcGBlpibABdetpOJ7NmJDoia6M4tagvlbBvXbIJGFGuakektlWT5oWT2Hn5GSHCCjYLzR6CLMz3iaFHdBBkz15O4/640?wx_fmt=png&from=appmsg "")  
  
恶意样本通过mount –bind命令将/tmp挂载至自身对应的/proc/[PID]目录，并将/proc/1/stat、/proc/1/status、/proc/1/cmdline和/proc/1/statm等进程信息复制到/tmp下对应文件中。由于/proc/[PID]被映射至/tmp，其他进程访问该恶意样本的/proc/[PID]路径时，获取到的将是伪造的进程信息，从而隐藏自身真实的进程状态和运行信息，增加恶意进程发现与分析的难度。  
  
![](https://mmecoa.qpic.cn/sz_mmecoa_png/RiaMmbYzV5MjNjb3SOR6n5C35Dsn6hblBoVe7QnCK9KjJWVdrqR2DCnCWeJweOda1FrcjEd2ksjc1hicicoicHXiasqC5WSvQPvhPNLicy6HLs1O8/640?wx_fmt=png&from=appmsg "")  
  
本次发现的僵尸网络采用UDP协议进行通信。恶意样本内置13个STUN服务IP地址。样本启动后会在受害主机上创建UDP Socket，并绑定0.0.0.0，由操作系统动态分配本地UDP端口。随后，样本通过该端口向内置的STUN服务地址发送STUN Binding Request（绑定请求）报文，并根据服务端返回的响应获取当前UDP通信对应的公网映射地址及端口信息。  
  
![](https://mmecoa.qpic.cn/sz_mmecoa_png/RiaMmbYzV5MgPz8ASQWkmk0YMlUMmxkKQbbHLPI4LcbuicKBswdzxw8LTRu8ice2GyWXrHZsLcTH1HwHrcAvzb6Otg5qkneHfM6XCq0uzXb0ac/640?wx_fmt=png&from=appmsg "")  
  
随后，样本创建子进程，周期性依次向内置的13个STUN服务地址发送UDP数据包，以维持相关NAT映射状态，避免因UDP长时间无通信导致映射超时回收，为后续基于该公网地址和端口的UDP通信提供条件。  
  
每轮发送完成后，样本统计发送失败的节点数量：当失败数量不超过7个时，认为本轮通信正常，并在5秒后进入下一轮发送；当失败数量超过7个时，则将本轮标记为异常，并在10秒后进行重试。若连续异常超过9轮，样本将关闭相关UDP Socket并终止通信进程。  
  
![](https://mmecoa.qpic.cn/sz_mmecoa_png/RiaMmbYzV5MgibcSfusc0Ep39AyTVQpsiczLjQXppBBuCqTS2qVSXt1rC0ucdzUKJjEd6QD9Cvv2fDLwp7V5PnsyhKX32hUAWyw5fjDJskJsmk/640?wx_fmt=png&from=appmsg "")  
  
在建立UDP通信后，恶意样本通过poll机制同时轮询13个Socket，并设置5秒超时时间。当任一Socket出现可读事件时，poll返回并进入后续数据处理流程。  
  
恶意样本内置8个控制指令，C2 返回的数据长度固定为20字节，样本根据接收到的不同控制指令执行相应功能，具体指令如下表所示。  
  
![](https://mmecoa.qpic.cn/mmecoa_png/RiaMmbYzV5MjbEywvliaVpnYxrddK68YOaTusyOTOVc4vHUDT1qyMO5qhibwdPLNsxO38q5kIwqMeruric6ksQSXibzCIzQDNv7tzfRzCodvjWlk/640?wx_fmt=png&from=appmsg "")  
  
四、总结  
  
  
  
样本通过周期性发送UDP数据包维持NAT映射，使攻击者能够基于受控设备的公网映射地址和端口发送控制指令，降低因NAT映射超时导致数据包无法到达的影响。此外，由于样本监听本地所有网络接口（0.0.0.0），对于直接暴露在公网的受感染设备，攻击者也可使用其他控制主机直接向这些设备发送控制指令。  
  
建议企业和用户提高安全防护意识，加强对互联网暴露设备及异常网络通信行为的监测，可通过中国电信威胁情报平台开展恶意IP、域名等IOC核查，并结合网络流量对异常UDP通信、高危外联行为进行持续监测，必要时根据确认的IOC及时采取访问控制和封禁措施，以降低设备被入侵及进一步利用的风险。  
同时，鉴于该僵尸网络具备利用多种安全漏洞进行传播的能力，相关设备用户应及时做好漏洞修补和版本更新，并避免将存在已知漏洞的设备直接暴露于公网。  
  
IOC  
  
  
  
IP  
:  
  
120.193.219[.]210  
  
77.239.124[.]131  
  
MD5  
:  
  
64add6ed3952276f653598a162cea991  
  
3439006ec878b07e6537900daa35c6a0  
  
4662b41663023b09838245acd154c2ac  
  
b7902ac85096347a56ea31b2cb1d352d  
  
ccf26c290b422f04f4ebb63750f800bf  
  
79ddbccecd6810186ffc894fb789ac76  
  
8394fa2a355de451349e317af85f2e23  
  
f6bf4bccf74221e8c4e8685c3a9d8fe0  
  
184e412077bdcaab347390fdc8fd37ce  
  
4a257f5ba2f0bd858fbdc931f90b40c3  
  
08d7fe9e6c1859dcff2ae877a03b49a9  
  
ab61b9316c65ea378c6ce5aa747c1254  
  
de1e9d632708cd3b7505ca644ef3d11a  
  
f05773585e01d1e47f00d2afa9a31db6  
  
1ec1edd74bbdeca90e04820da1d0431f  
  
87ad54105e11863abd6a9d27997d86e6  
  
f9395bafc8f19491fb433012a6fadc31  
  
a59b5e5d8c91db9318749654f44aa415  
  
ae7ad09d48d23ff670c9e6ffe079ab4c  
  
6db399ad41e68fbc53d0e29160819669  
  
9b4e02f614e0d24dc32a8f6bb3da4616  
  
a0b474141f8c82642ff6331378fb7489  
  
d4e91afb92dca36926eb0a443c7272e2  
  
442dfd99772b9c033821d0b0e526e93d  
  
965009fc48df25faa4c2862fdfa13ea8  
  
供稿：威胁情报团队  
  
排版：马子豪  
  
编辑：  
陈师慧  
  
校对：李雪  
  
执行主编：田金英  
  
主编：冯晓冬  
  
**推荐阅读**  
  
  
[](https://mp.weixin.qq.com/s?__biz=MzkxNDY0MjMxNQ==&mid=2247539548&idx=1&sn=69ede56b45ac25685de3e91385a4e28a&scene=21#wechat_redirect)  
  
[智能“悬赏令”：天翼安全威胁情报守卫企业安全！](https://mp.weixin.qq.com/s?__biz=MzkxNDY0MjMxNQ==&mid=2247539548&idx=1&sn=69ede56b45ac25685de3e91385a4e28a&scene=21#wechat_redirect)  
  
  
  
[](https://mp.weixin.qq.com/s?__biz=MzkxNDY0MjMxNQ==&mid=2247541066&idx=1&sn=144918a66b9363ab20ff8ae277301d8c&scene=21#wechat_redirect)  
  
**AI中转站，正在悄悄骗你**  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/z7xPqlc0GbBZdrnibkon0HxogO9iazwQy5cqsw6fRdkPujrZZCuVnk7ywgyYA7yTfRIIkEXpdpDfQlkFMENO9P0Q/640?wx_fmt=gif&from=appmsg "")  
  
  
  
  
   
  

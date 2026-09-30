#  漏洞预警 | Notepad++栈缓冲区溢出漏洞  
浅安
                    浅安  浅安安全   2026-09-30 00:00  
  
**0x00 漏洞编号**  
- CVE-202  
6-85279  
  
**0x01 危险等级**  
- 高危  
  
**0x02 漏洞概述**  
  
Notepad++是一款免费的开源文本编辑器，支持多种编程语言的语法高亮和自动完成。  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/7stTqD182SXge4Micx23dicocpZ55snE9DzMHRpHCGHFYKVPeV4na3jXDrpe3h2w79ia5C688fIDKUHmUl0EP8vTQ/640?wx_fmt=png&from=appmsg&wxfrom=5&wx_lazy=1&tp=webp#imgIndex=0 "")  
  
**0x03 漏洞详情**  
  
**CVE-2026-85279**  
  
**漏洞类型：**  
栈缓冲区溢出  
  
**影响：**  
执行任意代码  
  
**简述：**  
Notepad++的PowerEditor/src/MISC/PluginsManager/PluginsManager.cpp中的PluginsManager::loadPluginFromPath存在栈缓冲区溢出  
漏洞  
，因为插件提供的GetLexerCount()结果控制了一个循环，该循环写入containers[30]时未强制执行NB_MAX_EXTERNAL_LANG。恶意或被攻陷的插件如果报告超过30个词法分析器，就可以越界写入栈数组并破坏控制数据，从而可能在其进程上下文中执行任意代码。  
  
**0x04 影响版本**  
- Notepad++ <   
8.9.8  
  
**0x05****POC状态**  
- 已公开  
  
**0x06****修复建议**  
  
******目前官方已发布漏洞修复版本，建议用户升级到安全版本****：******  
  
https://notepad-plus-plus.org/  
  
  
  

#  QQ飞车桌面版曝"一键式"远程代码执行风险?  
tuto
                    tuto  杂杂咱谈   2026-09-18 03:46  
  
据安全研究人员描述，QQ飞车桌面版疑似存在远程代码执行攻击链，攻击者可通过公共/游戏聊天中的系统消息触发漏洞，结合输入过滤绕过、PV规范化缺陷以及不受限制的Lua执行入口，最终实现任意系统命令执行。  
  
攻击链大致涉及：  
```
攻击者
  │
  ▼
构造恶意 System Message
  │
  ▼
公共/游戏聊天传播
  │
  ▼
Sanitization Bypass
输入清理机制绕过
  │
  ▼
Unsafe PV Canonicalization
不安全的PV规范化
  │
  ▼
Lua Execution Sink
进入非预期Lua执行入口
  │
  ▼
Unrestricted Lua Execution
不受充分限制的Lua执行
  │
  ▼
Arbitrary Command Execution
任意系统命令执行
  │
  ▼
QQ飞车桌面客户端被控制
```  
  
  
  
  
  
[#Simple]()  
 [#QQ飞车]()  
 [#RCE]()  
  
  

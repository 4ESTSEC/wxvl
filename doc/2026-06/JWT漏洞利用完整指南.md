#  JWT漏洞利用完整指南  
原创 Red Hunter
                    Red Hunter  黑白之道   2026-06-26 00:35  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650618185&idx=1&sn=e7a4ddec94c9af10451bca515c67e228&scene=21#wechat_redirect)  
> **导语**  
：在JSON Web Token（JWT）成为现代应用开发主流之前，Web应用主要使用服务端会话，这带来了水平扩展性问题。JWT通过将认证数据从服务器移到令牌本身解决了这个问题。然而，当最佳实践被忽视、安全规范被忽略时，JWT漏洞就会应运而生，对受影响组织及其客户构成高风险安全威胁。  
  
## 一、JWT简介与应用场景  
  
JSON Web Token通常用于处理Web服务中的认证会话，如Web应用、API和SPA，但也存在其他常见用例：  
- 刷新令牌以获取新的会话令牌  
  
- 密码重置和邮箱确认令牌  
  
- 共享和邀请链接  
  
- 其他提供对数据对象（如文档）临时访问的密钥  
  
当JWT的实现存在缺陷时，目标应用可能容易受到多种JWT攻击。以下我们深入探讨JWT的构造方式，然后分析这些攻击的具体手法。  
## 二、JSON Web Token结构解析  
  
![JWT结构解析](https://mmbiz.qpic.cn/sz_mmbiz_png/nGzNudUIJ6Mib2N5DFXFnII897iafVdcLnwEpzflz6ejzj3ehJQ0kKY5QichXiaWXq9d05S9eKRnj8mgQaN58zLnDW8upCMthYl891PoM8VvdTk/640?wx_fmt=png "JWT结构解析")  
  
JSON Web Token由三部分组成：Header（头部）、Payload（载荷）和Signature（签名）。  
### 2.1 Header  
  
Header包含元数据，作为JWT库的指令，帮助验证、解密和签名令牌。元数据可能包括所使用的算法、令牌类型、任何标识符（如kid）及其他相关元数据。  
  
![JWT Header详解](https://mmbiz.qpic.cn/mmbiz_png/nGzNudUIJ6NibavjjHMicV9e96pFA19YISGAjUmNdB37NviceyYNH7HU76brPya6Cewna0k8nAOMM7VKK03icTN80vn1qM22c5E28xPa6eA5Y4k/640?wx_fmt=png "JWT Header详解")  
### 2.2 Payload  
  
Payload包含实际的JWT声明和应用需要的数据。最佳实践是不包含任何敏感数据（如账单信息、邮箱或其他PII），除非已加密（即JWE）。  
### 2.3 Signature  
  
签名用于验证令牌的完整性。这意味着数据无法在没有签名密钥的情况下被篡改。  
## 三、None算法攻击  
  
如前所述，JWT Header包含所有帮助解析JSON Web Token的元数据。在某些情况下，开发人员启用了None算法支持，允许他们发行和使用未签名的令牌，通常用于测试目的或支持无符号令牌（如非敏感共享链接）。  
  
然而，如果在生产环境中处理不当，任何人都能篡改JWT，包括修改任何声明以提升应用内权限或利用注入攻击。  
  
考虑以下在会计平台中使用JWT的示例：  
  
![JWT会话处理示例](https://mmbiz.qpic.cn/mmbiz_png/nGzNudUIJ6OVsah3xmJUNbaUTwwDzHVibt4onLdUnY4fwQvVtL5ic7abQmUjAL57nB5b36cElAgVqA9RPrIicp4fT7sWaPYWpdicNPDiaVduAvkI/640?wx_fmt=png "JWT会话处理示例")  
  
任何用户都可以：  
- 对Header和Payload进行Base64解码  
  
- 将Header中的alg属性设置为none  
  
- 篡改声明，在本例中我们希望将role属性改为owner，并将organizationId改为受害者的组织ID  
  
- 最后完全省略签名，但保留尾部的点  
  
![None算法JWT攻击](https://mmbiz.qpic.cn/sz_mmbiz_png/nGzNudUIJ6OgibRc43g3nlZw7HrYsb5mjUScBtLa9elXRVB5QyF434oibFMiaG0NP5jiaL0El066mtpXk4PAeicOu5alMdhQW41TRjD2YhBRHZP4/640?wx_fmt=png "None算法JWT攻击")  
  
这最终允许我们冒充任何组织所有者。  
## 四、缺失签名验证攻击  
  
与之前的错误配置类似，在某些情况下，开发人员会在没有提供签名时完全跳过签名验证。这通常是为了方便测试应用的认证部分，或支持无符号令牌（如书签或应用配置预设的共享链接）。  
  
再次强调，当执行错误的解析时，攻击者可以篡改声明，可能冒充其他用户提升应用内权限，或者在服务器错误处理用户输入的情况下利用注入攻击。  
  
要测试这种情况，我们需要完全移除签名，篡改JWT声明，只向服务器发送Header和Payload。  
  
![缺失签名验证JWT攻击](https://mmbiz.qpic.cn/sz_mmbiz_png/nGzNudUIJ6MaA8E6DNyCWPK4vLRKbKPELYrL4GpxJ7LibFUEuQaTntK2GZ8IxibicxeiaMGP5NTlyXibesAZJrcPm1eENlJTia1DxcGBnJ58UJx5k/640?wx_fmt=png "缺失签名验证JWT攻击")  
  
由于这种JWT漏洞源于错误的令牌解析，你可能需要尝试'alg' Header属性并传递意外的值为'none'。  
## 五、算法/密钥混淆攻击  
  
如果之前将算法设置为'none'的所有尝试都失败了，强烈建议进行额外测试，因为可能支持其他算法，这可能导致JWT密钥混淆攻击。  
  
JWT算法（或密钥）混淆攻击源于开发人员错误处理JWT的算法，启用了除默认算法以外的其他算法支持。这允许攻击者指定任何其他算法用于令牌签名，例如HS256（通常使用公钥作为密钥进行签名）。  
  
在实践中，这通常涉及将JWT Header中的alg属性修改为HS256，篡改Payload，并用服务器的公钥对令牌进行签名。你需要找到服务器支持的确切公钥，这通常可以在服务器上找到。  
## 六、解析缺陷（CVE-2018-0114）  
  
在其他场景中，你会注意到JWT解析库存在缺陷，允许包含你自己的密钥对。这似乎不太可能，但CVE-2018-0114是一个明确的例子，源于类型混淆，攻击者可以简单地在令牌中包含任意密钥对来伪造令牌。  
  
node-jose库允许任何未认证的攻击者只要在Header中指定jwk属性就可以签名令牌。jwk属性具有几个嵌套属性，指示解析库验证签名：  
  
使用以下Header制作JSON Web Token实际上会指示解析库使用攻击者的密钥对来验证其签名，最终允许任何人伪造其内容：  
```
{  "alg":"RS256","typ":"JWT","kid":"example-key-id","jwk":{    "kty":"RSA",    "kid":"example-key-id",    "use":"sig",    "n":"uAPuSn1MG6nFYKjiIcfke-nyMfsZM_Wrea7wlv1l553UrUM8P9VjZ0kTKYX3iyWLDXgyokLsZtqicE5q3c71cQ",    "e":"AQAB"}}
```  
  
你需要生成自己的密钥对并将其转换为'n'和'e'属性需要的正确格式。  
## 七、kid属性注入攻击  
  
除了JWT库中的错误配置外，还有其他疏忽，如JWT Header属性中的注入攻击和使用弱密钥。让我们看几个可以 exploit JWT漏洞的其他例子。  
  
如前所见，当Header参数被错误处理时可能会出现错误配置。在某些情况下，你可能会注意到JWT Header部分引用了密钥ID（kid）。此属性表示密钥文件的位置，本质上指示解析库从服务器文件系统获取签名密钥的位置。  
  
某些目标使用一组密钥，因此将使用kid参数来识别用于签名的密钥对。在这种情况下，我们可以根据参数的处理方式测试可能的路径遍历、SSRF和注入攻击。  
### 7.1 通过JWT kid属性利用路径遍历  
  
考虑以下易受攻击的代码示例：  
  
![通过JWT kid注入进行路径遍历](https://mmbiz.qpic.cn/sz_mmbiz_png/nGzNudUIJ6NCZtPaQN9p05icgiaaGopibrcDWGvqiaG2o8B3ftHUL6ROIiarQxJMUyZSia4a6qEicklPwiaUa8ELZHwdzlgEGiaQtCDWzUgic51qXg4fI/640?wx_fmt=png "通过JWT kid注入进行路径遍历")  
  
我们可以看到，在第15行，应用读取kid参数并将其附加到文件获取函数中，没有进一步验证。在代码示例中，它指向一个不可猜测的签名密钥。然而，由于我们可以有效地遍历路径，我们可以使应用从另一个目录获取签名密钥，只要它位于文件系统上。我们的目标是找到一个返回可猜测密钥组合的文件，这样我们就可以使用相同的密钥在本地复制JWT签名过程。  
```
{  "alg": "RS256",  "kid": "../../../dev/null"}
```  
### 7.2 通过JWT kid属性利用SQL注入  
  
与前面的情况类似，如果目标在从数据库加载签名密钥时考虑kid属性，我们也可以实际测试SQL甚至NoSQL注入。  
  
![通过JWT kid注入进行SQL注入](https://mmbiz.qpic.cn/sz_mmbiz_png/nGzNudUIJ6NMseBAQC59J07aq59NYYDx6icjuSib8ZbgZiaDa5LhrZgunrqMvJ5uLWd7iaoNnCSibyKHDHXE9zutdE66vjicmGGWvib5hNf8yOicGpA/640?wx_fmt=png "通过JWT kid注入进行SQL注入")  
  
在第11行，你可以看到kid属性被连接到一个未准备的SQL语句中然后才进行评估。在这种情况下，简单地跳出上下文并注入可预测的有效载荷字符串将用我们控制的字符串值替换初始签名密钥。  
```
{  "alg": "RS256",  "kid": "example-key' OR UNION SELECT 'intigriti'; --"}
```  
## 八、弱密钥暴力破解  
  
使用常见或弱密钥也会促进令牌伪造。使用John The Ripper或JWT_tool等工具，我们可以轻松地暴力破解用于签名和验证令牌的弱密钥。请注意，这只有在令牌使用密钥签名时才可能。RSA签名的JWS和JWE通常需要完整的密钥对，包括私钥，这使得猜测攻击几乎不可能。  
```
$ john --wordlist=/path/to/wordlist.txt jwt.txt
```  
  
密钥有时会被意外推送到公共代码仓库、JavaScript文件和其他配置文件中。这些可能包括用于签名和验证JSON Web Token的JWT密钥。  
  
**提示！**  
  
在客户端找到JSON Web Token密钥是JWT在客户端签名和验证的有力指示。确保你始终验证密钥的实际保密性。  
## 九、总结  
  
JSON Web Token漏洞可能源于各种错误配置、不遵循最佳实践和解析缺陷。本文涵盖了几种在开发人员在整个JSON Web Token实现过程中不加注意时可能发生的处理缺陷。  
  
现在你已经学到了关于exploit JWT漏洞的新知识……是时候将你的技能付诸实践了！你可以从易受攻击的实验室和CTF开始练习，或者浏览Intigriti上的70多个公共漏洞赏金项目，谁知道呢，也许你下次提交就能获得漏洞赏金！  
  
**原文出处**  
：https://www.intigriti.com/researchers/blog/hacking-tools/exploiting-jwt-vulnerabilities  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/nGzNudUIJ6Puc8HNiaVFMibMWyXuRCjam9iat0x1rCpsp1iatD7HveaKn7X0FMG8AlYEYZQbWQjAzjbYJjgdDQYTxLsn3W66vehH9V8LV2z9XnE/640?wx_fmt=jpeg "")  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650618755&idx=1&sn=48e0a85464fb20c73b6f338928a8f850&scene=21#wechat_redirect)  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650618776&idx=2&sn=a8fc65fd2a71822830022fc52d967bcd&scene=21#wechat_redirect)  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650618496&idx=1&sn=96ecdff99136258a4bf2cb156542311e&scene=21#wechat_redirect)  
> 👇 点击**阅读原文**  
，访问我的网站  
  
  

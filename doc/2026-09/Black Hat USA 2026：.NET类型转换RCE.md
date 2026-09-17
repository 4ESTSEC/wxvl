#  Black Hat USA 2026：.NET类型转换RCE  
原创 Max Luo
                        Max Luo  白帽子罗棋琛   2026-09-17 00:16  
  
# 字符串如何变成 RCE：.NET 类型转换层的隐形攻击面  
> Black Hat USA 2026 议题笔记：Transformers: Dark Side of the Type - Weaponizing the Conversion Layer  
  
  
很多 .NET 安全检查已经会盯住 BinaryFormatter.Deserialize()  
、Json.NET TypeNameHandling  
 和 XAML 反序列化，却很少有人把 TypeDescriptor.GetConverter()  
、反射调用 Parse()  
 或 new T(string)  
 当成同一等级的边界。问题在于，应用不一定需要序列化器才能完成“外部数据选择类型，再执行这个类型的代码”。一个类型名和一个普通字符串就够了。  
  
Oleksandr Mirosh 在公开课件与白皮书中把这类问题命名为 **Insecure String Transformation**  
。它不是给反序列化换一个新名字，而是在序列化器之外单独标出一块长期漏审的攻击面：转换层接收字符串，动态解析目标类型，随后通过 TypeConverter、静态工厂、构造器、属性访问器或自定义逻辑创建对象。如果目标类型缺少严格约束，普通配置值就可能成为网络访问、文件操作、嵌套解析乃至代码执行的入口。  
  
![议题课件封面](https://mmbiz.qpic.cn/sz_mmbiz_jpg/4yRoMuNP850L1hPqmIk5dgWnWe4raYxqFI7lPMYbTH2aIjNODVCM0n3w6P1nWZLzWucJ2icMfce42j5s8WRtooVFEhbfJhtF4e1qOC8icIEpo/640?from=appmsg "")  
  
图 1：研究关注的不是某一个危险 API，而是“字符串—类型解析—对象构造”这一整类转换边界  
  
本文基于公开文档和课件整理，不复现 SharePoint 的完整利用载荷，也不提供可直接触发进程启动、XAML 加载或嵌套反序列化的 gadget 参数。重点放在源码识别、可达性判断、修复顺序、回归测试和 SOC 检测。  
## 1、真正的边界不是序列化器，而是“谁能决定目标类型”  
  
序列化器之所以危险，不是因为输入长得像 JSON、XML 或二进制，而是它通常同时做了两件事：确定对象类型，再用输入数据实例化或填充对象。过去十年的防守措施大多围绕序列化 API 建立：禁用类型元数据、配置 Binder、淘汰危险序列化器、给 Deserialize()  
 建静态规则。  
  
但大量框架会用更轻的表示传递简单对象。例如颜色可以写成 #FF0000  
，坐标写成 3,5  
，时间写成 ISO 8601 字符串。只要接收方已经知道目标类型，就不需要完整序列化协议。  
  
![简单类型以类型和字符串传播](https://mmbiz.qpic.cn/sz_mmbiz_jpg/4yRoMuNP853tw8yaFCQGcj6dc69DuCWPcT1Ae3dCqCWKSujTplROveO724AXFu3zgYFHjMs2n67Y4l9BibWLMp44glzOicOUM5PQ1kOyEEgicM/640?from=appmsg "")  
  
图 2：简单对象可以只携带字符串；当目标类型也来自外部时，转换层便重新拥有了序列化器的关键能力  
  
风险判断不能停留在“这里没有 Deserialize，所以不是反序列化”。更有效的问题是：  
  
text  
```
外部输入是否控制 value？           |           +-- 外部输入是否直接或间接控制 targetType？                          |                          +-- 程序是否根据 targetType 执行构造或填充逻辑？                                         |                                         +-- 类型集合是否在解析前被精确限制？ 
```  
  
  
固定类型的解析通常不是本议题所说的问题。例如 DateTime.TryParse(userValue, ...)  
 的类型在编译期已确定；风险主要来自日期格式、时区和资源消耗，而不是攻击者选择任意 .NET 类型。相反，Type.GetType(userType)  
 后再转换 userValue  
，已经跨过了另一条安全边界。  
  
![转换层定义](https://mmbiz.qpic.cn/mmbiz_jpg/4yRoMuNP8520gIu2NCxicLxRYIVJEkaCsYlc1sabf88zgEuaNXrIHtUPrDibnv6uMYNTALdiaibDaZ4Xe1g1gQibq984T4FPpjtT9d8NU1kvNllg/640?from=appmsg "")  
  
图 3：转换层没有序列化器参与，却同样完成了“选择类型”和“填充实例”两步  
  
这一区分也决定了审计范围：不要把所有 Parse  
 都报成高危，否则规则很快会被噪声淹没；应优先寻找“可控类型解析”与“对象构造”在同一路径相遇的位置。  
## 2、四个条件同时成立，转换器才成为安全漏洞  
  
课件给出的判定标准很实用。一条转换链要成为不安全字符串转换器，需要同时满足四项：  
1. 接受攻击者可控字符串；  
  
1. 转换过程中解析目标类型，类型可来自字符串本身、相邻元数据或另一个参数；  
  
1. 对解析出的类型进行实例化或属性填充；  
  
1. 没有充分限制可以解析的类型。  
  
![不安全字符串转换器的四个条件](https://mmbiz.qpic.cn/mmbiz_jpg/4yRoMuNP851ESDQk4E6xMT4DoVSaTHnVxqI2icqBaphMvTzTzbKmzC2Npa1jsiaibc9aiaItPSqqQ3XVHJeGEW8Ww2u10iaChEbjYaAkcxTMebic0/640?from=appmsg "")  
  
图 4：前三项构成动态转换器，第四项把通用能力变成攻击面  
  
用代码表达，这类实现通常有相似骨架：  
  
csharp  
```
// 仅用于代码审计示例：不要在生产代码中使用外部输入解析类型。publicstaticobjectConvertUnsafe(string typeName, stringvalue) {     Type target = Type.GetType(typeName, throwOnError: true)!;     TypeConverter converter = TypeDescriptor.GetConverter(target);     return converter.ConvertFromInvariantString(value)!; } 
```  
  
  
单看 ConvertFromInvariantString()  
，审计者可能把它当成普通字符串解析；真正改变风险等级的是上游 typeName  
 的来源。相同骨架还可能把类型放在工作流 XML、ASPX 属性、消息头、数据库配置、插件清单或反射元数据中，而把 value 放在另一个字段。污点分析必须跨字段关联，不能只检查一个参数。  
  
![相同攻击骨架的不同入口](https://mmbiz.qpic.cn/mmbiz_jpg/4yRoMuNP850ictJSkd1AZ6CVYmoUMkHZqIOr7tYEX6yqxMicLeUBHq7WI7FCXwSGqgu4PVq9D9g1Uer2sic3V6zlAgtgReuSwAUDByPAS9eQXU/640?from=appmsg "")  
  
图 5：入口格式可以不同，核心始终是外部输入影响类型和字符串，再进入通用对象构造逻辑  
  
修复前应给每条路径建立证据表，避免仅凭 API 名称判断：  
  
yaml  
```
transformer_review:entrypoint:WorkflowArgumentBinder.Bindtrust_boundary:tenant-authenticated-usertype_source:workflow_xml.argument.typevalue_source:workflow_xml.argument.valueresolver:System.Type.GetTypeconstruction:TypeConverter.ConvertFromStringvalidation_before_resolution:noneload_context:web_processreachable_assemblies:inventory-requireddisposition:critical-review
```  
  
  
这个表的价值在于把“可能危险”变成可验证的工程问题。只有 value 可控、类型固定时，优先处理该类型自己的解析缺陷；类型可控但对象未实例化时，要继续查解析器是否有副作用；二者都可控且无精确白名单时，才进入最高优先级。  
## 3、五类转换原语，要按数据流组合审计  
  
课件归纳了五类常见原语：TypeConverter、反射调用静态 Parse/TryParse  
、字符串构造器、无参构造后访问属性，以及自定义转换逻辑。它们的危险性不由 API 单独决定，而由“类型是否外部可控”和“目标程序集里有什么可达行为”共同决定。  
### 3.1 TypeConverter  
  
典型实现先解析 Type  
，再查询该类型声明的转换器：  
  
csharp  
```
Type target = ResolveType(request.TypeName);       // source TypeConverter c = TypeDescriptor.GetConverter(target); object result = c.ConvertFromString(     context: null,     culture: CultureInfo.InvariantCulture,     text: request.Value);                           // sink
```  
  
  
![TypeConverter 转换汇点](https://mmbiz.qpic.cn/sz_mmbiz_jpg/4yRoMuNP8516OHBgXXEiaF7Q4FRlxnSaaicP8vezYGR9aQQjC43ich7kThdaBVoeEwicSQiaZzx30FEMGMJtJe6lTzgQAlwVBNSIWfh1Rk0KhKKk/640?from=appmsg "")  
  
图 6：TypeConverter 本身是扩展机制；当目标类型由请求决定时，扩展点便跨越了信任边界  
  
转换器不一定只解析数字或颜色。它可以执行目标类型作者设计的任意转换代码，包括解释路径或 URI、访问资源、加载其他格式。安全审计不能以方法名里有 Converter  
 就推断其无副作用。  
### 3.2 反射 Parse/TryParse  
  
另一种通用框架会约定“目标类型只要有静态 Parse 就可绑定”：  
  
csharp  
```
MethodInfo? parse = target.GetMethod(     "Parse",     BindingFlags.Public | BindingFlags.Static,     binder: null,     types: new[] { typeof(string) },     modifiers: null);  object? result = parse?.Invoke(null, newobject[] { request.Value }); 
```  
  
  
![反射调用静态 Parse](https://mmbiz.qpic.cn/sz_mmbiz_jpg/4yRoMuNP850bQiczjjSZlDmp7GEOO3UO4QOHrVc7DbwHRTI4wQk2n58lUg4y2I4884BicCHRLj98w3ttlgwdGdDIRqAwd1W9E8ystwS2ESd34/640?from=appmsg "")  
  
图 7：Parse 通常被当成纯函数约定，但反射选择目标类型后，调用的是目标程序集提供的代码  
  
审计规则若只匹配字面量 SomeType.Parse  
，会漏掉 GetMethod("Parse")  
、接口封装、缓存的 MethodInfo 和委托调用。应从类型解析点跟踪到 Invoke  
，同时检查方法名和签名约束。  
### 3.3 new T(string) 与 Activator  
  
插件框架和配置系统常用“单字符串构造器”作为统一协议：  
  
csharp  
```
ConstructorInfo? ctor = target.GetConstructor(new[] { typeof(string) }); object? instance = ctor?.Invoke(newobject[] { request.Value });  // 等价的高风险形态object? instance2 = Activator.CreateInstance(target, request.Value); 
```  
  
  
![字符串构造器作为转换入口](https://mmbiz.qpic.cn/sz_mmbiz_jpg/4yRoMuNP850mO8b4HEffddBdc2DabensUoPU2IoN05CU5ic6sVPg05pqqqc5DkxQ4A9icLYibtpNiaX6PX2yh3BYh9riaibicZQ7zN480SeD5VwJibA/640?from=appmsg "")  
  
图 8：构造器并不承诺只保存字符串；文件、网络、反射等行为都可能发生在对象初始化期间  
### 3.4 无参构造器加访问器  
  
有的 Binder 先创建对象，再根据外部字段名反射设置属性。此时危险逻辑可能藏在 setter，也可能在后续 getter、验证器或生命周期回调中：  
  
csharp  
```
object instance = Activator.CreateInstance(target)!; foreach (PropertyInput input in request.Properties) {     PropertyInfo p = target.GetProperty(input.Name)!;     p.SetValue(instance, ConvertScalar(p.PropertyType, input.Value)); } 
```  
  
  
![属性访问器也可能执行转换逻辑](https://mmbiz.qpic.cn/sz_mmbiz_jpg/4yRoMuNP8506WogQt76OlTPPtmE5gcEK0mXkgpF3ITqoCorgk7uOz32owxMhVkV2q3PPTMkkpn1eUvS3QnlktFeezRlWwboRKoibqiaibWzxgU/640?from=appmsg "")  
  
图 9：只审计构造函数会漏掉 setter、getter 和对象完成绑定后的隐式调用  
### 3.5 自定义转换器  
  
最后一类没有稳定 API 名称，可能叫 BindValue  
、Materialize  
、BuildParameter  
 或 ObjectFromString  
。识别它依靠数据流而非关键字：输入中存在类型标识和字符串值；代码解析类型；随后执行反射、组件模型或注册表中的工厂委托。  
  
这也是为什么单纯扩大危险 API 黑名单收益有限。更稳妥的审计模型是“source—type resolution—construction sink—side effect”，并把四个节点保存在结果中供人工复核。  
## 4、Gadget 是否可用，取决于进程真正能加载什么  
  
发现动态转换器不等于已经证明可利用。要判断影响，需要回答目标进程能解析、加载和实例化哪些类型。  
  
.NET Framework 时代的 Global Assembly Cache（GAC）向进程提供大量机器级程序集，通用转换器面对的类型库存很大。现代 .NET 取消了同样形态的 GAC，依赖通常由应用发布内容、shared framework 和 .deps.json  
 决定；攻击面往往更窄，但绝不是零。若服务引用 Microsoft.WindowsDesktop.App  
 或打包了高权限组件，可达类型仍可能包含带网络、文件、标记语言或反射副作用的实现。  
  
![不同 .NET 运行时的类型可达性](https://mmbiz.qpic.cn/sz_mmbiz_jpg/4yRoMuNP8529BvNxOvlWeU7OaN7I0Bo3QY6pg3OSMlNLoP7fShb5VibzjOxOE2fujx8AwIIjMwRNzSH1SuYSvnK99LbsdM3rFPTsd63fic41w/640?from=appmsg "")  
  
图 10：.NET Framework 侧重机器级 GAC，现代 .NET 侧重 shared framework 和应用依赖；二者都要按实际进程建清单  
  
![转换原语与运行时类型库存](https://mmbiz.qpic.cn/mmbiz_jpg/4yRoMuNP8529FJ699Hf1e3Zm2Mw7m0sMzSMwynUGnztuyQO1ZRmYMIJtgRbzSic70J0LpftC3iafJBgq6w9cvv84TPAX2O1tuKwM9DrnhdODA/640?from=appmsg "")  
  
图 11：同一转换器在不同部署中可能有完全不同的可利用性，类型库存是进程属性，不只是源码属性  
  
工程上可以从构建产物生成“可加载程序集—公开类型—转换入口”清单，而不是拿开发机环境代替生产环境：  
  
bash  
```
# 在隔离的构建/分析环境运行；输入应是待发布目录，不是生产服务器。 dotnet list ./src/WebApp/WebApp.csproj package --include-transitive jq -r '.targets[][] | keys[]?' ./publish/WebApp.deps.json | sort -u find ./publish -maxdepth 1 -type f -name '*.dll' -print | sort
```  
  
  
随后用受控分析器枚举下列特征：  
- 类型或基类是否注册 TypeConverterAttribute  
；  
  
- 是否存在公开 Parse(string)  
、TryParse  
 或 ctor(string)  
；  
  
- setter/getter 是否触达文件、网络、程序集加载、反射或二级解析；  
  
- 转换器是否接受路径、URI、标记语言或资源引用；  
  
- 目标类型是否在 Web 进程、定时任务或高权限服务的实际 Load Context 中可达。  
  
不要把“未找到公开 gadget”当成修复。类型库存会随 NuGet 更新、插件安装和 Windows 组件变化而漂移。只修剪依赖而不切断可控类型，最多降低当前可利用性，无法消除漏洞类别。  
## 5、SharePoint 案例说明：正确修复是解析前精确白名单  
  
白皮书复盘的第一条链是 CVE-2020-1460。工作流转换逻辑从 XML/参数中获得类型和值，随后获取目标类型的 TypeConverter 并进行字符串转换。课件将其归纳为：低权限用户可触达、默认配置可形成 RCE，问题不依赖传统 Deserialize()  
。  
  
![CVE-2020-1460 的转换链](https://mmbiz.qpic.cn/sz_mmbiz_jpg/4yRoMuNP852Vr0kzaL5zZ3YOm108PbDFwZoQwtVIy5kvEakNANBAxswojibPDl5bYP9k1Y6dYQae5cjFSAyBruyCZr2JGAXk1e9TyKk4j6vo/640?from=appmsg "")  
  
图 12：类型和值从工作流数据进入通用转换器，缺失的是类型解析之前的允许集合  
  
这次修复值得保留：在转换前检查目标类型，只允许业务确实需要的少量标量与 SharePoint 字段类型。关键不是“拦住已知危险类”，而是默认拒绝未知类。  
  
csharp  
```
privatestaticreadonly IReadOnlyDictionary<string, Func<string, object>> Parsers =     new Dictionary<string, Func<string, object>>(StringComparer.Ordinal)     {         ["System.String"] = static s => s,         ["System.Boolean"] = static s => bool.Parse(s),         ["System.Int32"] = static s => int.Parse(s, CultureInfo.InvariantCulture),         ["System.Double"] = static s => double.Parse(s, CultureInfo.InvariantCulture),         ["System.DateTime"] = static s => DateTime.Parse(             s, CultureInfo.InvariantCulture, DateTimeStyles.RoundtripKind)     };  publicstaticobjectConvertApproved(string externalType, stringvalue) {     if (!Parsers.TryGetValue(externalType, out Func<string, object>? parser))         thrownew SecurityException("Unsupported conversion type");      return parser(value); } 
```  
  
  
这个实现有意不调用 Type.GetType()  
：如果业务只需要五种标量，最小设计就是五个显式解析器。不要先解析任意类型，再拿 FullName  
 对白名单；某些解析与装载路径本身就可能触发程序集解析事件或自定义加载逻辑。  
  
课件还讨论了 2026 年出现的 CVE-2026-26106、CVE-2026-40357、CVE-2026-47294 和 CVE-2026-48560，并对其中一条 ASPX 属性转换路径展开分析。公开材料指出，相关代码会先尝试 TypeConverter，再回退到反射 Parse；外围命名空间限制又可能被复杂类型表达绕开。本文不展开注册指令、泛型嵌套与 XAML 的具体组合，以免把防守文章变成可执行利用说明。  
  
![六年后再次出现同类问题](https://mmbiz.qpic.cn/sz_mmbiz_jpg/4yRoMuNP851oeNvGa4FUZiav0AEJjvnswvXmLkI5HsTl60TsaUMEvgePlKwd4cQXDibRKenqV3P5jjn5QYImrvJyGPmDdnLBkIvWiaWh8HO2hY/640?from=appmsg "")  
  
图 13：研究材料把四个 2026 年 SharePoint CVE 归入同一转换层问题族，说明局部补丁没有替代系统性盘点  
  
![ASPX 属性转换中的两类汇点](https://mmbiz.qpic.cn/mmbiz_jpg/4yRoMuNP850sY4AbarVxicfJ20A1icfWlOXgVGWKU7Ej8V2ibdV5K8cZxcjxcibMap8cZenNsxYOwZrJW0KakXianBiat7ZllkQhjOuT0VjibCtP4U/640?from=appmsg "")  
  
图 14：同一转换函数先走 TypeConverter、再尝试静态 Parse；两条分支都必须共享同一类型策略  
  
微软安全响应中心为上述 CVE 提供独立安全更新条目。由于公告主要描述产品影响、严重性和补丁信息，具体内部调用链应以研究课件与白皮书为依据，而不能反推成微软公告的原文结论。生产处置上也应以 MSRC 对对应 SharePoint 版本列出的安全更新为准。  
  
这组案例的教训不是“SharePoint 又有一个特例”，而是同一产品内可能存在多个独立转换入口：工作流、页面标记解析、配置绑定和内部工具类各有一套类型策略。修复一个调用点后，如果没有搜索全仓库的类型解析颈部，漏洞会在另一个入口再次出现。  
## 6、代码审计先找类型解析颈部，再追五类汇点  
  
最高收益的起点不是搜索所有 Parse  
，而是建立类型解析清单：Type.GetType  
、Assembly.GetType  
、ITypeResolutionService.GetType  
、BuildManager.GetType  
、Activator.CreateInstance(string, string)  
，以及项目自定义的 ResolveType/LoadType/CreateByName  
 包装器。  
  
下面的 Semgrep 规则适合做第一轮筛查。它故意只报“外部字符串解析类型后进入 TypeConverter”的紧邻形态，避免宣称覆盖全部数据流：  
  
yaml  
```
rules:-id:dotnet-dynamic-typeconverterlanguages: [csharp]     severity:WARNINGmessage:动态解析的类型进入TypeConverter；确认类型名是否越过信任边界patterns:-pattern-inside:|           $T = System.Type.GetType($NAME, ...);           ... -pattern-either:-pattern:System.ComponentModel.TypeDescriptor.GetConverter($T)-pattern:System.ComponentModel.TypeDescriptor.GetConverter((System.Type)$T)metadata:category:securityconfidence:MEDIUMcwe:"custom-review-required"
```  
  
  
第二条规则寻找动态类型进入 Activator 的形态：  
  
yaml  
```
rules:-id:dotnet-external-type-to-activatorlanguages: [csharp]     severity:ERRORmessage:检查外部可控类型是否在实例化前经过精确允许列表mode:taintpattern-sources:-pattern-either:-pattern:$REQ.Query[$K]-pattern:$REQ.Form[$K]-pattern:$REQ.Headers[$K]pattern-propagators:-pattern:$T=System.Type.GetType($IN,...)from:$INto:$Tpattern-sinks:-pattern-either:-pattern:System.Activator.CreateInstance($T,...)-pattern:$CTOR.Invoke(...)
```  
  
  
实际仓库还要补充项目自己的请求对象、消息消费者、XML 读取器与配置抽象。对每个结果至少回溯以下证据：  
1. 类型名的最初来源和可控权限；  
  
1. 是否在 Type.GetType  
 之前执行精确匹配；  
  
1. 比较是否使用 Ordinal  
，是否允许前后缀、命名空间、程序集名或泛型表达；  
  
1. 转换分支是否共用同一策略，还是 TypeConverter 被拦住后又回退到 Parse；  
  
1. 生产发布目录中有哪些实际可达类型；  
  
1. 转换结果之后是否触发 getter、验证、渲染或任务执行。  
  
![研究给出的三条 Hunt 路径](https://mmbiz.qpic.cn/sz_mmbiz_jpg/4yRoMuNP850m6UQFrnSibwA8miayoghdJASWFdLibOSHwZZdicdYMwb3O9MXjON1SnibTwMNicJBjAbPtV1vcOkM7XkmbWKtNRKJmpnchmzcyvibQY/640?from=appmsg "")  
  
图 15：源码狩猎可分为类型解析颈部、转换汇点和可达 gadget 行为三条线，最终按数据流汇合  
  
对大型 .NET 仓库，建议再用 CodeQL/Roslyn 做跨方法数据流。查询模型不必从“命中即漏洞”开始，而应输出路径：外部输入 → 类型解析 → 验证器 → 构造汇点。人工复核验证器的位置和语义后，再定严重性。  
## 7、黑盒测试应证明类型可控，不要直接投递危险 Gadget  
  
没有源码时，公开材料建议关注几个输入形态：本应是标量的位置出现完整类型名、程序集限定名、复杂泛型语法、标记语言片段、UNC 路径或 URL。它们是定位动态转换器的线索，不是看到一次就能确认 RCE。  
  
![黑盒数据狩猎与分级](https://mmbiz.qpic.cn/mmbiz_jpg/4yRoMuNP853cI3YGtlnxlKjLr2rrKMWNniaNC5ic8uvfOwUhXeAqOYBsMhWO1Nvgso2CcDEchlJJwnibK8gdk3QibsJ7uwqcYRhmQ1JtdvXHFc0/640?from=appmsg "")  
  
图 16：黑盒阶段先识别类型元数据和异常值形态，再通过无害探针确认服务端是否进行了动态解析  
  
授权测试应使用无副作用的自定义 Canary 类型，放在隔离测试版本中：  
  
csharp  
```
[TypeConverter(typeof(AuditCanaryConverter))] publicsealedrecordAuditCanary(string Value);  publicsealedclassAuditCanaryConverter : TypeConverter {     publicoverrideboolCanConvertFrom(ITypeDescriptorContext? c, Type sourceType) =>         sourceType == typeof(string);      publicoverrideobjectConvertFrom(         ITypeDescriptorContext? c, CultureInfo? culture, objectvalue)     {         SecurityAudit.Emit("dynamic_type_conversion_reached", new         {             Canary = nameof(AuditCanary),             ValueLength = ((string)value).Length         });         returnnew AuditCanary((string)value);     } } 
```  
  
  
测试请求只使用测试程序集里的 AuditCanary  
，断言审计事件是否出现；不要用系统自带的文件、网络、XAML 或进程相关类型“证明影响”。Canary 既能验证类型是否受控，也不会把测试变成远程代码执行。  
  
黑盒结果可以按证据分级：  
  
json  
```
{"finding":"dynamic-string-transformer","evidence":{"type_name_accepted":true,"value_accepted":true,"canary_converter_reached":true,"allowlist_observed":false,"production_gadget_tested":false},"confidence":"high","impact":"requires-production-reachability-analysis","safe_test":true}
```  
  
  
如果无法部署 Canary，则用错误差异、日志和调用链遥测判断：未知类型、存在但不允许的类型、允许类型的非法值，应产生不同且可审计的内部分类，但外部统一返回 400，避免通过详细异常帮助枚举服务器程序集。  
## 8、SOC 检测要把异常类型和值与服务器副作用关联  
  
运行时检测不能只写一条“请求里出现 System.  
”的 WAF 正则。合法 .NET API、诊断页面和开发工具可能频繁携带类型名。更可信的告警需要把入口异常与后续副作用关联：  
- 请求参数、工作流 XML 或页面属性中出现程序集限定类型名；  
  
- 标量字段出现泛型符号、标记语言、URI、UNC 或本地绝对路径；  
  
- 应用随后产生异常 DNS/HTTP/SMB 出站；  
  
- Web 工作进程打开配置外文件或加载非常用程序集；  
  
- 日志出现 TypeLoadException  
、TypeConverter  
、TargetInvocationException  
、Parse  
 反射失败；  
  
- 同一来源短时间轮询多个命名空间、程序集版本或类型名。  
  
下面是一条偏运营化的 Sigma 关联前置规则，用于识别 Web 进程异常加载程序集；字段名需按实际 EDR 映射：  
  
yaml  
```
title:Web进程加载非基线.NET程序集status:experimentallogsource:category:image_loadproduct:windowsdetection:web_process:Image|endswith:-'\\w3wp.exe'-'\\dotnet.exe'managed_image:ImageLoaded|endswith:'.dll'filter_approved:ImageLoaded|startswith:-'C:\\Windows\\Microsoft.NET\\'-'C:\\Program Files\\ApprovedApp\\'condition:web_processandmanaged_imageandnotfilter_approvedfalsepositives:-已批准部署、插件更新或补丁安装level:medium
```  
  
  
单条程序集加载事件不足以定案。SIEM 应在 5—15 分钟内关联反向代理请求、ASP.NET 诊断、DNS、网络连接、文件事件和子进程：  
  
yaml  
```
correlation:insecure_string_transformer_suspectedwindow:10mgroup_by: [host, process_guid] require:-any:-request_contains_type_metadata-dotnet_dynamic_type_error-any:-nonbaseline_assembly_load-web_process_outbound_network-web_process_unexpected_file_access-web_process_child_processseverity:child_process:criticaloutbound_or_file:highmetadata_only:lowresponse:-preserve_request_and_trace_id-capture_process_tree_and_loaded_modules-isolate_only_after_business_impact_check
```  
  
  
注意保护日志中的请求正文。工作流和页面参数可能包含凭据、个人数据或业务文档，检测平台应做字段级脱敏、访问控制和短期留存。  
## 9、修复顺序：取消类型选择优先于维护 Gadget 黑名单  
  
课件给出的最佳修复很直接：让外部输入失去选择类型的能力。接口若处理日期，就调用固定的日期解析器；若支持有限的业务字段，就把外部枚举映射到内部工厂委托。只有插件平台确实需要动态类型时，才维护精确允许集合，并在任何类型解析之前检查稳定的业务标识。  
  
![最优修复是移除类型选择](https://mmbiz.qpic.cn/sz_mmbiz_jpg/4yRoMuNP853ql0Wo7KHicOGuOXIGJegX8q5Xicfw8zpd18LltZNdOc9Lb6HwvkeO9Iictkmj158b9eCttiaBCpGp4J5Ps0F8gEIh650nWQMm7v0/640?from=appmsg "")  
  
图 17：一旦目标类型由程序固定，攻击链中最关键的自由度就被删除  
  
![必须动态时使用精确白名单](https://mmbiz.qpic.cn/mmbiz_jpg/4yRoMuNP851dQmX4Z21Nic971sicVbZsGX2YEs7Bs1dXKLTSySWHEYP0KtYPpbYRUIYExHK4sVliaurxJXibSvhu3hRc6YkSom8D9aiaapeKT4Bw/640?from=appmsg "")  
  
图 18：白名单要在类型解析前生效，且描述业务能力，而不是列举当前已知危险类型  
  
推荐把协议从 CLR 类型名改成版本化的业务枚举：  
  
csharp  
```
publicenum FieldKind {     Text,     Boolean,     Integer,     Timestamp }  publicstaticboolTryConvert(FieldKind kind, string raw, outobject? value) {     value = null;     switch (kind)     {         case FieldKind.Text:             value = raw;             return raw.Length <= 4096;         case FieldKind.Boolean:             if (!bool.TryParse(raw, outbool flag))                 returnfalse;             value = flag;             returntrue;         case FieldKind.Integer:             if (int.TryParse(raw, NumberStyles.Integer,                     CultureInfo.InvariantCulture, outint number))             {                 value = number;                 returntrue;             }             returnfalse;         case FieldKind.Timestamp:             if (DateTimeOffset.TryParseExact(raw, "O", CultureInfo.InvariantCulture,                     DateTimeStyles.None, out DateTimeOffset timestamp))             {                 value = timestamp;                 returntrue;             }             returnfalse;         default:             returnfalse;     } } 
```  
  
  
需要特别拒绝的“伪修复”包括：  
- 禁止若干已知 gadget 类型；依赖更新后会出现新类型；  
  
- 只允许 System.*  
 或厂商命名空间；命名空间不是安全能力边界；  
  
- 使用 StartsWith  
、正则或去除特殊字符判断类型名；复杂类型表达和程序集解析容易制造旁路；  
  
- 解析 Type  
 后才检查；已经把不可信名称交给解析基础设施；  
  
- 只保护 TypeConverter 分支，却保留 Parse、构造器或属性绑定回退；  
  
- 通过删除当前 gadget 宣布漏洞已修；转换器仍会被将来依赖重新武器化。  
  
## 10、上线前用负向测试证明“类型选择权已经消失”  
  
修复验证不应只测合法输入仍能工作。要为每个转换入口建立负向矩阵，覆盖未知业务类型、CLR 全名、程序集限定名、泛型表达、超长字符串、路径/URI、大小写与 Unicode 混淆、旧版本协议和所有回退分支。  
  
csharp  
```
[Theory] [InlineData("System.String", "hello")] [InlineData("System.DateTime", "2026-08-10T00:00:00Z")] [InlineData("Vendor.Product.LegacyType, Vendor.Product", "x")] [InlineData("Unknown", "x")] publicvoidExternalClrTypeNamesAreNeverResolved(string typeToken, stringvalue) {     var resolver = new RecordingTypeResolver();     var binder = new SafeFieldBinder(resolver);      BindResult result = binder.Bind(typeToken, value);      Assert.False(result.Success);     Assert.Empty(resolver.RequestedTypeNames); } 
```  
  
  
再给允许的业务枚举做边界测试，确认不经过通用类型系统：  
  
csharp  
```
[Fact] publicvoidApprovedIntegerUsesPinnedParser() {     var resolver = new RecordingTypeResolver();     var binder = new SafeFieldBinder(resolver);      BindResult result = binder.Bind("integer", "42");      Assert.True(result.Success);     Assert.Equal(42, result.Value);     Assert.Empty(resolver.RequestedTypeNames); } 
```  
  
  
发布门禁还应比较依赖清单：如果 Web 进程新增带 TypeConverter、字符串构造器或敏感副作用的程序集，触发安全复核；这不是为了维护永久 gadget 黑名单，而是防止修复过渡期的风险被依赖漂移放大。  
  
![代码审计与修复分级清单](https://mmbiz.qpic.cn/sz_mmbiz_jpg/4yRoMuNP852wBXk16Ec57pY9rOO5K9dt7oX0bCbBNicsSsX3L6jqqHAibMbzctibyosX2ySYdicxJWWfRHCvs4fCJJbqaOgibK6WLTEfbqVLXgxk/640?from=appmsg "")  
  
图 19：可控类型、可控值、验证位置、可加载程序集和可达行为共同决定优先级  
  
最终验收标准可以压缩为四项：协议不再接受 CLR 类型名；所有入口使用同一业务类型注册表；任何未知类型都在解析前拒绝；生产遥测能够识别被拒绝的类型探测及其后续异常副作用。  
  
![研究结论](https://mmbiz.qpic.cn/mmbiz_jpg/4yRoMuNP8520a5D3EhTa8mvR20gKw8L5uu8pmKjNeCfS33MVmVgzLcrKictSTBbzzAVKjwDF0kvCkH1oHbRUqCVfym7hEQvp1icWyTP8Lo4Eo/640?from=appmsg "")  
  
图 20：转换层不是序列化器，却能形成等价的“选择类型并执行构造代码”能力  
  
这场研究最有价值的地方，是把一个散落在框架工具函数中的问题还原成可操作的安全模型。安全团队不需要把每个 Parse()  
 都升级为事故，也不应等到找到可执行 gadget 才承认边界存在。先证明攻击者能否选择类型，再确认对象如何被构造、进程能加载什么，最后把类型选择权从协议中移除。  
  
只要外部输入仍能把任意 CLR 类型交给通用转换器，当前没有 gadget 只是部署快照，不是安全属性。真正稳定的修复，是让业务协议表达“整数、时间、文本”等有限能力，而不是暴露运行时类型系统。  
### 资料来源  
- Black Hat 官方 Session 页面  
  
- Black Hat USA 2026 Session 页面  
  
- Microsoft MSRC：CVE-2020-1460  
  
- Microsoft MSRC：CVE-2026-26106  
  
- Microsoft MSRC：CVE-2026-40357  
  
- Microsoft MSRC：CVE-2026-47294  
  
- Microsoft MSRC：CVE-2026-48560  
  
**原始会议材料（仓库内）**  
- 演讲课件 PDF  
  
- 配套白皮书 PDF  
  
开源资料与原始议题 PDF  
  
本文对应的 Markdown 原稿、Black Hat 原始议题 PDF 与配图已整理到 GitHub，可按文章编号查找和下载。  
  
https://github.com/cybermaxluo/black-hat-usa-2026-talks  
  
也可以点击文末“阅读原文”进入仓库。欢迎 Star、提交 Issue 或参与勘误。  
  

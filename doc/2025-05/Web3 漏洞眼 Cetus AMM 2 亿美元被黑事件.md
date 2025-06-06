#  Web3 漏洞眼: Cetus AMM 2 亿美元被黑事件   
原创 一个不正经的黑客  一个不正经的黑客   2025-05-26 00:30  
  
   
  
  
  
> 字数 2672，阅读大约需 14 分钟  
  
# Web3 漏洞眼: Cetus AMM 2 亿美元被黑事件  
## 前言  
  
这算是本公众号第一次发表关于Web3区块链漏洞技术的文章，未来会持续输出越来越多Web3的知识。  
  
之前看好sui在投资SUI和链上赚币赚了一点小钱，反而在cetus上亏欠了，所以cetus令我印象深刻，这次cetus AMM 被黑事件也第一时间吸引了我的兴趣。  
  
下面让我们一起进入漏洞分析，揭开一下这个价值几个亿的漏洞庐山真面目。  
## 正文  
  
2025年5月22日，部署于 Sui 网络上的 Cetus AMM 遭遇了一场毁灭性的黑客攻击，造成超过 2 亿美元的损失。  
  
这起事件成为近年最严重的 DeFi 安全漏洞之一，起因是一处“溢出”保护机制中的微妙但致命的缺陷。  
  
本文将深入剖析此次攻击的技术细节，并回顾该问题是如何被引入、修复、又重新引入的全过程。  
### 执行摘要  
  
攻击者利用了 Cetus AMM 中一个流动性计算函数的漏洞，该漏洞会在计算过程中截断最重要的高位（MSB，Most Significant Bits）。该函数在用户开启 LP（流动性提供者）仓位时会被调用。  
  
用户在开启此类仓位时，可以通过指定一个“liquidity”（流动性）参数来决定投入比例（即希望获得多少池中份额），并提供相应数量的代币。  
  
攻击者通过将该 liquidity 参数设置为极高的数值，诱发了中间计算过程中的溢出。  
  
然而由于溢出检测逻辑存在缺陷（实际是高位截断问题），这一错误未被发现。  
  
结果就是：攻击者只需投入 1 单位代币，就能伪造出巨量的流动性头寸，并借此从多个流动性池中提取出总计数亿美元的资产。  
  
**注意**  
：  
  
技术上讲，此问题并不是真正意义上的“溢出（overflow）”，而是“MSB（最高有效位）截断”。但为了简化叙述，本文仍将其称为“溢出”。  
### 攻击过程  
  
此次攻击按部就班，精心策划，以下是一笔典型攻击交易的简化流程：  
1. 1.   
**发起闪电交换（Flash Swap Initiation）**  
：攻击者通过闪电交换借入了 1,000 万 haSUI，设定了最大滑点容忍范围。  
1. 2.   
**创建仓位（Position Creation）**  
：在 ticks 范围 [300000, 300200] 内创建了一个新的流动性仓位 —— 这是一个极窄的价格区间，位于价格曲线的上边界。  
1. 3.   
**添加流动性（Liquidity Addition）**  
：攻击者仅投入 1 单位的代币 A，却“成功”获得了一个巨量流动性数值：**10,365,647,984,364,446,732,462,244,378,333,008**  
这一行为之所以能通过，是因为系统未能检测到位运算中的高位截断漏洞。  
1. 4.   
**移除流动性（Liquidity Removal）**  
：随后通过多笔交易立即将流动性移除，实质上抽干了流动性池中的资产。  
1. 5.   
**偿还闪电贷（Flash Loan Repayment）**  
：攻击者归还了借入的 haSUI，并最终获利约 **570 万 SUI**  
。  
### 技术深度解析：“溢出”漏洞  
  
此次漏洞的根源位于 clmm_math.move 模块中的 get_delta_a 函数。该函数负责根据给定的流动性值计算所需的代币 A 数量，其函数签名如下：  
```
public fun get_delta_a(    sqrt_price_0: u128,    sqrt_price_1: u128,    liquidity: u128,    round_up: bool): u64 {    let sqrt_price_diff = sqrt_price_1 - sqrt_price_0;        let (numberator, overflowing) = math_u256::checked_shlw(        // Dedaub: result doesn't fit in 192 bits        full_math_u128::full_mul(liquidity, sqrt_price_diff)    );    // Dedaub: checked_shlw "overflows" result, since it << 64    assert!(overflowing);        let denominator = full_math_u128::full_mul(sqrt_price_0, sqrt_price_1);    let quotient = math_u256::div_round(numberator, denominator, round_up);    (quotient as u64)}
```  
#### 函数逻辑：  
1. 1.   
**计算平方根价格差**  
：let sqrt_price_diff = sqrt_price_1 - sqrt_price_0;  
1. 2.   
**尝试进行高精度乘法并左移 64 位**  
：```
let (numberator, overflowing) = math_u256::checked_shlw(    full_math_u128::full_mul(liquidity, sqrt_price_diff));
```  
  
1. *  
full_mul 用于计算 liquidity * sqrt_price_diff，结果理论上为 256 位。  
1. *  
checked_shlw 将该结果左移 64 位，但 **并未妥善处理超出 192 位上限的部分**  
，导致数值被截断  
1. *  
这种未检测的“高位截断”（MSB truncation）就是导致错误行为的关键。  
1. 3.   
**计算分母并求商，结果被强制截断为 u64**  
使用真实攻击交易中的参数：  
- *  
**liquidity**  
：10,365,647,984,364,446,732,462,244,378,333,008（约等于 2¹¹³）  
- *  
**sqrt_price_0**  
：60,257,519,765,924,248,467,716,150（对应 tick 300000）  
- *  
**sqrt_price_1**  
：60,863,087,478,126,617,965,993,239（对应 tick 300200）  
- *  
**sqrt_price_diff**  
：605,567,712,202,369,498,277,089（约等于 2⁷⁹）  
关键计算过程如下：  
```
numerator = checked_shlw(liquidity * sqrt_price_diff)          = checked_shlw(~2^113 * ~2^79)          = checked_shlw(2^192 + ε)          // checked_shlw shifts a 256-bit register by 64          = ((2^192 + ε) * 2^64) mod 2^256          = ε
```  
  
这个乘法计算的结果超出了 192 位的表示范围，而后续在 checked_shlw 中将其左移 64 位时，相当于将这个 256 位数整体左移一个 64 位字（word），但最终被这次乘法运算产生了一个超过 192 位的结果。  
  
当该值在 checked_shlw 中被左移 64 位（即“按一个 64 位字进行的受检左移”）时，它超出了 256 位整数的表示范围，但本应检测此类溢出的检查机制却未能奏效。  
  
但等等，**受检（checked）操作不就是为了防止这种问题的吗？**  
#### 有缺陷的溢出检查  
  
关键的漏洞存在于 checked_shlw 函数中：  
```
public fun checked_shlw(n: u256): (u256, bool) {    let mask = 0xffffffffffffffff << 192;  // 这是错误的！    if (n > mask) {        (0, true)    } else {        ((n << 64), false) // 溢出的确切发生点    }}
```  
  
表达式 0xffffffffffffffff << 192 并没有产生预期的结果。  
  
开发者的本意很可能是想检查 n >= (1 << 192)，但实际生成的掩码值无法达到这个目的。  
  
其结果是，大多数大于 2^192 的值都能悄无声息地通过这项检查，而随后进行的左移 64 位操作会在 Move 中造成**静默溢出**  
（Move 对移位操作不会触发运行时错误）。  
#### 整数处理注意事项  
  
在 Move 中，整数操作的安全机制旨在防止溢出（overflow）和下溢（underflow），这些问题可能导致意外行为或安全漏洞。具体而言：  
- *  
加法（+）和乘法（*）：如果结果超出了整数类型的表示范围，程序将中止执行（abort）。在这种情况下，**中止意味着程序会立即终止运行**  
。  
- *  
减法（-）：如果结果小于零，也会导致程序中止。  
- *  
除法（/）：如果除数为零，程序将中止。  
- *  
**左移（<<）是一个例外**  
：即使左移操作导致溢出（被移出的位数超出整数类型的容量），程序也**不会中止**  
。这意味着最终可能会产生错误的结果或不可预测的行为。  
在大多数具有受检算术（checked arithmetic）机制的编程语言中，位移操作在截断结果时**不会触发错误**  
，这是正常的。大多数智能合约审计人员对此也有认知。  
#### 利用造成的影响  
  
由于溢出问题，**分子（numerator）被回绕为一个非常小的值**  
。  
  
当该值与分母相除时，结果接近于 0。这意味着函数返回的结果是：**只需 1 个单位的 token A，就能铸造出一个巨量的流动性头寸**  
。  
  
用数学的方式来表述：  
- *  
**预期行为：**  
 需要非常大量的 token 才能对应如此巨大的流动性  
- *  
**实际行为（由于溢出）：**  
 只需要 1 个 token  
值得注意的是：  
  
**本次攻击中所用的数值并非随机选择，而是经过精确计算**  
。攻击者利用了合约中现有的函数（尤其是 get_liquidity_from_a）来推导出这些精准的输入值，从而实现漏洞利用。  
#### 审计追踪：类似问题曾被发现  
  
Ottersec 的审计在较早的代码版本（2023 年初），专门为 Aptos 设计的版本中，发现了一个令人毛骨悚然地相似的溢出漏洞：  
> [CITE]  
> “在对 numberator 值执行 u256::shlw 操作之前未进行验证。因此，非零字节可能会被移除，导致数值计算错误。”  
  
  
他们建议将 u256::shlw 替换为 u256::checked_shlw，并添加溢出检测逻辑，这一改动解决了该问题。  
  
需要注意的是，当时该版本的代码实现了自定义的 256 位无符号整数运算库，因为 Aptos 当时并不原生支持这类运算。  
  
Move 2 / Aptos CLI 约在 2024 年初主网上线（v1.10 版本），正式引入了原生支持。  
  
不幸的是，在团队几个月后将代码移植到 Sui 时（Sui 始终原生支持 256 位整数），在 checked_shlw 中引入了一个 bug。  
  
Ottersec 和 MoveBit 对该版本的 AMM 合约进行的审计**未能发现这个问题**  
。  
  
Zellic 于 2025 年 4 月进行的后续审计也**未发现除信息级别之外的问题**  
。  
  
可能的原因包括：用于数值计算的库代码未被纳入审计范围，此外，由于 Sui 原生支持 256 位操作，这类问题很可能被忽视了。  
#### 开发者的经验教训（Lessons for Developers  
1. 1.   
**理解你所使用语言的整数语义**  
1. *  
明确哪些操作会导致程序中止（abort），哪些会静默溢出（silent overflow）  
1. *  
对位移操作（bit shift）给予特别关注  
1. *  
用真实溢出场景测试你的溢出检测机制  
1. 2.   
**数学严谨性是不可妥协的**  
1. *  
DeFi 协议设计必须能够处理极端数值  
1. *  
需要清晰理解每个数学运算的边界（bounds）  
1. *  
建议使用形式化方法（formal methods）验证关键计算（我们的团队可以提供协助）  
1. 3.   
**对边界情况进行全面测试**  
1. *  
最大值不仅是理论问题，更是潜在攻击点  
1. *  
结合多个边界条件进行联合测试  
1. 4.   
**审计修复，而不仅仅是代码变更**  
1. *  
对关键修复进行独立验证和复审  
1. 5.   
**领域专业知识至关重要**  
1. *  
AMM 数学涉及复杂的不变量（invariants）  
1. *  
需与理解 DeFi 边界情况的审计方合作  
## 攻击事件结论  
  
Cetus 攻击事件为我们敲响警钟：DeFi 安全固然困难，但并非不可实现。  
  
一次有缺陷的溢出检查，结合闪电贷的可组合性和集中流动性机制，导致了超过 2 亿美元的盗窃。  
  
对于在基于 Move 的链（如 Sui 和 Aptos）上开发的工程师来说，此事件强调了以下几点的重要性：  
  
理解语言的整数语义、严格测试边界情况，以及与既懂平台又懂 DeFi 领域的审计团队合作。  
  
thanks :  
  
https://dedaub.com/blog/the-cetus-amm-200m-hack-how-a-flawed-overflow-check-led-to-catastrophic-loss/  
  
  
  
   
  
  

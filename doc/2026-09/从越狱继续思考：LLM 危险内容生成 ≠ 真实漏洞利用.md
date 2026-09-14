#  从越狱继续思考：LLM 危险内容生成 ≠ 真实漏洞利用  
原创 盐心糖意
                    盐心糖意  SecureNexusLab   2026-09-14 01:26  
  
LLM 安全研究里，有一个很容易被默认成立的等式：  
  
**「越狱成功 = 模型攻击能力被释放。」**  
  
这个等式很直观，也非常方便。只要模型原本拒绝回答，经过某种 jailbreak 之后开始输出危险内容，就可以计算一次攻击成功；再批量测试几百、几千条 prompt，就得到一个漂亮的 ASR（Attack Success Rate）。  
  
但如果把链路继续往后拉，这个等式很快就会出现问题。  
  
模型没有拒绝，不等于它真的理解了攻击任务；能够生成看起来专业的利用代码，也不等于代码可以运行；某个漏洞可以被打通，更不意味着模型能够稳定完成一条真实攻击链。  
  
更准确地说，从“识别风险”到“形成现实攻击能力”，中间至少隔着三个相对独立的环节：  
  
**「模型是否识别出风险 → 是否触发安全控制 → 是否形成可执行的攻击能力 → 是否能够稳定完成任务。」**  
  
其中任何一环都可能失效，而且后一层的成功率通常不能由前一层直接推出。  
  
这也是本文真正想讨论的问题：**「我们今天常用的越狱指标，到底测到了什么？」**  
## 一、模型可能识别出了危险，但仍然没有拒绝  
  
先从一个很有代表性的现象说起：prefill jailbreak。  
  
方法并不复杂。在 assistant 回复的开头预填一句类似：  
> ❝  
> Sure, here is ...  
> ❞  
  
  
模型原本可能会拒绝的请求，就有机会顺着这个前缀继续生成。  
  
真正值得关注的并不是这种攻击技巧本身，而是它暴露出来的控制结构。  
  
Qi 等人在 2025 年的研究中将这种现象概括为 **「shallow safety alignment」**  
：很多安全控制高度集中在响应刚开始生成的几个 token 上。  
  
这意味着，模型“是否识别出了有害请求”和“最终有没有拒绝”可能并不是一回事。  
### 一个很关键的对照实验  
  
Transformer 使用因果注意力机制。  
  
当 prompt 已经完成编码之后，再在 assistant 一侧加入 prefill，并不会回过头修改此前已经计算完成的 prompt 表征。  
  
于是就形成了一个很有意思的对照：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/PkfClzhSYicx5KeJovorsSwkbBRq4zXpdwXktgP3ejTxQ4LouMMVawQ8pfCHopvMxyVpic20YJlarPvuqXGeQtGghNTuTny8sSt7ZBsKaq0BA/640?wx_fmt=png&from=appmsg "")  
  
输入没有变化，但输出行为发生了明显翻转。  
  
至少在这组实验条件下，一个比较稳妥的结论是：  
  
**「prefill 改变的主要不是模型对输入的表征，而是响应生成阶段的控制行为。」**  
  
论文中四个开源模型的数据也很直观：  
<table><thead><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;"><section><span leaf="">模型</span></section></th><th style="border: 1px solid #ccc;padding: 5px 10px;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;text-align: right;"><section><span leaf="">普通请求拒绝率</span></section></th><th style="border: 1px solid #ccc;padding: 5px 10px;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;text-align: right;"><section><span leaf="">Prefill 后拒绝率</span></section></th><th style="border: 1px solid #ccc;padding: 5px 10px;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;text-align: right;"><section><span leaf="">危害探针 AUC（AdvBench）</span></section></th><th style="border: 1px solid #ccc;padding: 5px 10px;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;text-align: right;"><section><span leaf="">已遵从请求的危害分数</span></section></th></tr></thead><tbody><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;"><section><span leaf="">Qwen2.5-1.5B</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;font-size: 14px;color: #595959;text-align: right;"><section><span leaf="">1.00</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;font-size: 14px;color: #595959;text-align: right;"><section><span leaf="">0.03</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;font-size: 14px;color: #595959;text-align: right;"><section><span leaf="">0.996</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;font-size: 14px;color: #595959;text-align: right;"><section><span leaf="">0.975</span></section></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: #F8F8F8;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;"><section><span leaf="">Qwen2.5-3B</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;font-size: 14px;color: #595959;text-align: right;"><section><span leaf="">0.98</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;font-size: 14px;color: #595959;text-align: right;"><section><span leaf="">0.05</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;font-size: 14px;color: #595959;text-align: right;"><section><span leaf="">1.000</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;font-size: 14px;color: #595959;text-align: right;"><section><span leaf="">0.981</span></section></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;"><section><span leaf="">SmolLM2-1.7B</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;font-size: 14px;color: #595959;text-align: right;"><section><span leaf="">0.63</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;font-size: 14px;color: #595959;text-align: right;"><section><span leaf="">0.00</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;font-size: 14px;color: #595959;text-align: right;"><section><span leaf="">0.990</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;font-size: 14px;color: #595959;text-align: right;"><section><span leaf="">0.958</span></section></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: #F8F8F8;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;"><section><span leaf="">Phi-3-mini-3.8B</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;font-size: 14px;color: #595959;text-align: right;"><section><span leaf="">1.00</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;font-size: 14px;color: #595959;text-align: right;"><section><span leaf="">0.00</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;font-size: 14px;color: #595959;text-align: right;"><section><span leaf="">0.998</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;font-size: 14px;color: #595959;text-align: right;"><section><span leaf="">0.907</span></section></td></tr></tbody></table>  
数据来源：Breaking Refusal in the First Half  
。  
  
换句话说，在这些样本上，模型内部仍然保留了非常明显的危害相关表征，但这种表征并没有稳定转化为最终的拒答行为。  
  
这比一句“模型被越狱了”更值得研究。  
### “模型知道这是危险的”其实是一种过度简化  
  
这里很容易出现第一个认知陷阱。  
  
看到危害探针 AUC 接近 1，人们很自然会说：  
  
**「模型明明知道这是有害请求，却故意回答了。」**  
  
这种说法传播起来很方便，但技术上并不严谨。  
  
实验真正能够证明的是：  
> ❝  
> 模型某一层、某个 token 的隐藏状态中，存在能够被线性分类器有效解码的危害相关信息。  
> ❞  
  
  
这和人类意义上的“知道”“理解”“故意”不是同一个概念。  
  
类似地，高 AUC 也不能直接外推为模型具有完善的通用危害识别能力。  
  
原实验中的探针在 AdvBench 上表现非常好，但换到包含大量“表面危险、实际无害”请求的 XSTest 后，AUC 会明显下降到约 0.79。  
  
这说明它至少受到数据分布影响。  
  
因此，更合适的表述不是：  
> ❝  
> 模型已经完美识别出了危害。  
> ❞  
  
  
而是：  
> ❝  
> **「在特定数据分布下，模型表征中已经形成了较强的危害可分信息。」**  
> ❞  
  
  
两句话看似只差一点，认识论上的含义却完全不同。  
### 真正值得警惕的是：控制可能集中得太靠前  
  
论文还有一个细节很重要。  
  
当研究者在响应早期注入危害方向时，模型行为容易发生变化；但把相同操作推迟到生成后半段，效果迅速下降。  
  
换句话说，至少在这些模型里，安全控制存在明显的时间窗口。  
  
它更像是在回答刚开始的时候完成一次“是否应该拒绝”的判断，而不是在整个生成过程中持续执行安全约束。  
  
这也解释了为什么 prefill 如此有效。  
  
当前大量 SFT 安全数据，本身就是这样的格式：  
> ❝  
> 有害请求 → “抱歉，我不能帮助……”  
> ❞  
  
  
模型很容易学到一种局部模式：**「只要回答开头进入拒绝轨道，安全任务就完成了。」**  
  
于是安全能力可能并没有真正深入到完整生成过程，而是被压缩成了一个很短的响应前缀控制问题。  
  
需要强调的是，这仍然更接近一种机制解释，而不是已经被严格证明的唯一因果机制。  
  
但它至少提供了一个比“模型没识别出来”更有解释力的方向：  
  
**「识别可能还在，失效的是识别之后的控制。」**  
## 二、能够生成危险内容，离真正利用漏洞还很远  
  
如果说第一层讨论的是“识别”和“控制”的区别，那么下一层更加现实：  
  
**「模型愿意回答之后，答案到底能不能用？」**  
  
这一步在很多 jailbreak 研究里反而被跳过去了。  
  
通常的逻辑是：  
> ❝  
> 绕过拒绝 → 输出攻击内容 → 攻击能力提升  
> ❞  
  
  
问题在于，中间其实缺了几个非常重的环节：  
  
**「技术内容是否正确？代码能否运行？是否适配目标环境？最终能不能完成漏洞利用？」**  
  
这些问题远比 ASR 难测。  
### ASR 的优势，恰恰也是它最大的局限  
  
拒答率为什么会成为主流指标，并不难理解。  
  
它便宜、稳定，而且容易自动化。  
  
给模型批量发送 prompt，再通过关键词、分类器或者 LLM Judge 判断“拒绝 / 未拒绝”，几万条样本很快就能跑完。  
  
但验证一个漏洞利用是否真正成立完全是另一回事。  
  
需要准备对应版本的软件、依赖、编译环境、容器或者虚拟机，还要控制网络、权限和沙箱边界。生成的代码如果失败，还要判断到底是模型写错了、环境不同，还是漏洞条件没有满足。  
  
因此，ASR 实际测量的是一种**「策略层面的行为变化」**  
。  
  
它当然有价值。  
  
但问题出在，当它被继续解释成“攻击能力”时，指标和结论之间就跨了一层。  
### ExploitBench 展示出来的，是一条很长的能力阶梯  
  
2026 年 5 月发布的 ExploitBench 提供了一个很好的观察视角。  
  
它没有简单地问：  
> ❝  
> 模型有没有成功利用漏洞？  
> ❞  
  
  
而是把漏洞利用拆成 16 个可以验证的递进等级。  
  
为了便于理解，可以把它压缩成下面几步：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/PkfClzhSYicwe7efsgGdneE5Y5lmnHwY6fG8OJpQMCUGO0O6owicusdhR9gFM3A6fy6lgkibAvkvNRgDsm0mUvmRx47Wte8aribh3hBH20PSYHs/640?wx_fmt=png&from=appmsg "")  
  
这几个阶段看起来只是连续的几个箭头，但实际难度并不是线性增加的。  
  
找到漏洞点和写出 crash PoC，可能已经可以证明模型“理解了问题”。  
  
但从 crash 继续走到稳定内存读写、控制流劫持，再到绕过 ASLR、沙箱等保护机制，是完全不同等级的工程能力。  
  
ExploitBench 在 41 个 V8 漏洞上的实验也反映出了这种断层：  
  
不少模型能够找到漏洞，甚至构造出触发异常的代码；但一旦进入真正的利用阶段，成功样本迅速减少。即使是能力较强的闭源前沿模型，也只在部分案例上达到任意代码执行等级。  
  
这里最值得注意的不是某个模型到底成功了多少个。  
  
而是一个更普遍的事实：  
  
**「“会说”到“会做”之间，存在显著衰减。」**  
### 我自己的小规模测试也能看到类似现象  
  
我曾经在一个小型 Web 靶场里做过一轮非正式测试。  
  
样本只有 10 个公开 CVE，因此不适合把具体比例外推到更大的模型能力判断中，但现象本身比较稳定：  
  
GPT-4o 生成语法上基本正确的 PoC 并不困难，比例大约能达到 40%；真正放到标准靶机环境里执行，能够完成利用并获得预期权限的，不到 15%。  
  
大量失败并不是发生在“完全不会写”。  
  
而是发生在后面的细节：  
  
版本不匹配、参数不正确、路径判断错误、环境假设不成立，以及失败以后不会继续调试。  
  
这些能力很难通过单轮文本评测体现出来。  
  
但对于真实漏洞利用来说，它们恰恰决定了最后能不能成功。  
## 三、没有安全对齐的 base model，不等于攻击能力更强  
  
这里还有一个很常见的误会：  
> ❝  
> Base model 没有经过安全对齐，所以是不是最危险、攻击能力也最强？  
> ❞  
  
  
这两个概念其实不能直接画等号。  
  
Base model 的确可能更少拒绝。  
  
但“不拒绝”只能说明控制约束更弱，不能说明它输出的内容更加正确。  
  
换一种说法：  
  
**「拒绝率和任务能力是两个变量。」**  
  
一个模型可以能力一般、几乎什么都回答；另一个模型可以能力很强，但多数危险任务都会拒绝。  
  
如果后者的控制层被绕过，实际风险反而可能更高。  
  
因此，更值得关注的并不是“哪个模型最少拒绝”，而是：  
> ❝  
> **「当安全控制被绕过以后，底层保留下来的任务能力到底有多强？」**  
> ❞  
  
  
这个问题和传统 jailbreak 排行榜关注的东西并不完全一样。  
  
它测量的不是安全策略有多容易被绕过，而是绕过之后究竟释放了多少能力。  
## 四、即使漏洞打通一次，也不等于形成稳定攻击能力  
  
再往前走一步，问题还没有结束。  
  
假设模型真的完成了一次漏洞利用。  
  
它是否就已经具备现实意义上的攻击能力？  
  
答案仍然不能直接给“是”。  
  
因为真实攻击很少由一次调用完成。  
### 一条攻击链，本质上是连续决策过程  
  
真实环境通常每一步都依赖上一阶段的结果，同时会引入新的环境状态。  
  
端口扫描结果和预期不同，要调整下一步；利用失败，要重新判断版本；权限不够，要寻找新的入口；工具返回一段异常日志，还要理解发生了什么。  
  
所以真正困难的并不是“一次生成正确答案”。  
  
而是：  
  
**「模型能否持续观察环境、修正判断，并在几十次甚至上百次交互中保持任务状态。」**  
  
这和单轮能力是两件事。  
### 多轮工具调用，可能才是 Agent 攻击能力真正的瓶颈  
  
在 Agent 场景下，很多失败甚至和漏洞知识本身无关。  
  
常见问题包括：参数传错、工作目录判断错误、没有正确读取命令返回值、工具失败后重复执行同一操作、已经获得关键线索，却在后续步骤中丢失状态。  
  
我自己的小样本测试中也出现过这种现象：连续进行约 5 轮工具调用之后，整体任务成功率已经出现超过 40% 的下降。  
  
这个数字本身不能视为正式 benchmark 结论，但失败模式非常值得关注。  
  
因为它说明：  
  
**「Agent 的真实攻击能力，并不是“LLM 能力 × 工具”这么简单。」**  
  
中间还多了一层非常重要的系统能力：状态维护、工具选择、结果解释和错误恢复。  
## 五、还有一个更麻烦的问题：单步安全，很难推出整条任务安全  
  
2026 年 5 月的 A New Framework for Cybersecurity Refusals in AI Agents  
 还指出了另一个值得重视的问题。  
  
对于 Agent 来说，一个完整攻击任务可以被拆成很多看起来并不危险的步骤：  
> ❝  
> 扫描端口 查询软件版本 生成测试 payload 分析错误日志 调整请求参数  
> ❞  
  
  
孤立来看，这些动作都可能存在合法用途。  
  
但把它们放在同一条任务轨迹中，含义可能完全不同。  
  
论文测试的 8 个前沿模型中，有 6 个在网络攻击场景下表现出接近零的拒绝率。  
  
这个结果并不能简单解释成“模型完全没有安全意识”。  
  
更可能暴露的是另一类问题：  
  
**「当前安全判断大量发生在局部请求层，而攻击意图存在于任务轨迹层。」**  
  
模型看到的是一步一步的操作。  
  
真正的攻击目标，则隐藏在几十步交互形成的上下文里。  
  
这实际上把 LLM 安全从“内容审核问题”推向了“序列决策安全问题”。  
  
也是 Agent 时代比 Chatbot 时代更难处理的一部分。  
## 六、于是，越狱评估真正应该测什么？  
  
如果把前面的几层重新放到一起，可以得到一条更完整的能力链：  
> ❝  
> **「风险识别 → 安全控制 → 危险内容生成 → 技术正确 → 可执行 → 漏洞利用 → 多轮任务完成」**  
> ❞  
  
  
传统 ASR 通常只覆盖最前面的两三个节点。  
  
但真实风险主要发生在后面。  
  
这并不意味着 ASR 没有意义。  
  
它仍然适合测量模型的策略鲁棒性，例如一种新的 jailbreak 方法究竟有多容易突破现有拒答机制。  
  
真正需要避免的是把它继续解释成：  
> ❝  
> ASR 上升了 30%，所以模型的攻击能力也上升了 30%。  
> ❞  
  
  
两者之间没有这样的简单对应关系。  
  
一个更合理的评估框架，可以至少分成三层：  
<table><thead><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;"><section><span leaf="">层级</span></section></th><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;"><section><span leaf="">关注问题</span></section></th><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;"><section><span leaf="">典型指标</span></section></th><th style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-weight: bold;background-color: #f0f0f0;font-size: 14px;color: #595959;"><section><span leaf="">验证方式</span></section></th></tr></thead><tbody><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;"><section><span leaf="">策略层</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;"><section><span leaf="">能不能绕过控制</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;"><section><span leaf="">拒答绕过率</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;"><section><span leaf="">文本分类、Judge</span></section></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: #F8F8F8;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;"><section><span leaf="">内容层</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;"><section><span leaf="">输出是否技术有效</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;"><section><span leaf="">技术正确率、代码可执行率</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;"><section><span leaf="">静态检查、编译、单元测试</span></section></td></tr><tr style="border: 0;border-top: 1px solid #ccc;background-color: white;"><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;"><section><span leaf="">能力层</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;"><section><span leaf="">能不能完成真实任务</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;"><section><span leaf="">漏洞复现率、端到端任务完成率</span></section></td><td style="border: 1px solid #ccc;padding: 5px 10px;text-align: left;font-size: 14px;color: #595959;"><section><span leaf="">沙箱、靶场、Agent 环境</span></section></td></tr></tbody></table>  
三层并不是互相替代，而是回答不同问题。  
  
如果研究的是 jailbreak，策略层仍然重要。  
  
如果讨论的是现实安全风险，那么至少还需要向后验证一层甚至两层。  
  
否则，我们很容易把模型“愿不愿意说”误认为模型“到底能不能做到”。  
## 七、从这个角度看，安全防御的重点也会发生变化  
  
如果风险只发生在拒答这一层，解决方案自然是继续训练模型：  
  
让模型识别更多危险 prompt，增加拒答数据，提高 jailbreak 鲁棒性。  
  
但前面的分析说明，这种方案只能覆盖其中一部分问题。  
  
因为即使模型级控制存在缺口，攻击最终仍然要经过真实执行环境。  
  
这意味着，Agent 安全还有一个很重要、但经常被低估的方向：  
  
**「把安全控制继续向执行层下沉。」**  
  
例如对工具调用设置能力边界，对高风险动作进行二次授权，对网络访问、文件操作、命令执行和凭据使用实施独立策略，在执行环境中加入审计、隔离和回滚机制。  
  
这些措施并不能替代模型对齐。  
  
但它们解决的是另一个问题：  
> ❝  
> 当模型判断错误、控制失效，甚至已经输出危险操作时，系统是否仍然有机会阻止风险真正落地？  
> ❞  
  
  
从工程角度看，这往往比试图让模型永远“不犯错”更加现实。  
## 结语：不要把第一道门，当成整套安全系统  
  
回头再看最开始那个等式：  
> ❝  
> 越狱成功 = 攻击能力释放  
> ❞  
  
  
问题并不是它完全错误。  
  
而是它省略了太多中间变量。  
  
模型内部是否存在危害表征，是一个问题；  
  
这些表征能否稳定触发拒绝，是另一个问题；  
  
绕过拒绝以后，生成内容是否正确，还需要单独验证；  
  
代码正确以后，能否在真实环境完成漏洞利用，又是一道门槛；  
  
即使某一次利用成功，模型能否持续完成一个开放环境中的多阶段任务，依然没有答案。  
  
因此，比“这个模型还能不能被 jailbreak”更值得继续追问的，其实是：  
  
**「jailbreak 之后，真实、可验证的能力到底增加了多少？」**  
  
这个问题没有 ASR 那么容易测。  
  
需要靶场，需要沙箱，需要执行环境，也需要更细粒度的能力分层。  
  
但它距离真实安全风险更近。  
  
LLM 安全从来不是一道闸门。  
  
它更像一条由识别、控制、生成、执行和系统约束共同组成的防线。前一层失效，不意味着后一层一定失守；同样，前一层表现很好，也不意味着整个系统已经安全。  
  
如果只盯着最外面那道门，我们很容易高估一些风险，也可能低估另一些真正危险的能力。  
  
欢迎大家加入微信技术交流群，各位师傅可以添加小助手微信拉你进群：  
  
![图片](https://mmbiz.qpic.cn/mmbiz_jpg/PkfClzhSYiczeGuYDS022pxXvnmLkXt4YG6JPg5gApBVJFN7ZINOlbBJ8ZI0VVN5U8abvoRTtXK0kRNWAJJ5Dic3lnSrM9xibQ4ia8oVRhMaW9E/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&watermark=1&tp=wxpic#imgIndex=4 "")  
  
相关阅读推荐：  
  
[UniFi 设备漏洞细节曝光：一条请求如何绕过认证并获得 root](https://mp.weixin.qq.com/s?__biz=MzU2MDE2MjU1Mw==&mid=2247489500&idx=1&sn=0f50125fadfc001fd31c4d897abcc6da&scene=21#wechat_redirect)  
  
  
[中转站安全研究 从灰产链路到攻防实践](https://mp.weixin.qq.com/s?__biz=MzU2MDE2MjU1Mw==&mid=2247489443&idx=1&sn=cdae3c038f791c5adf10626de7072865&scene=21#wechat_redirect)  
  
  
  

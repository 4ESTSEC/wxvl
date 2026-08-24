#  TSE 2024 | LIVABLE：破解软件漏洞类型识别中的“长尾”难题  
AIForSecurity
                    AIForSecurity  AI安全这点事   2026-08-24 05:31  
  
最近在看漏洞检测相关论文，对这个方向疑问挺多的，论文中只给原始样本数目，比如bigvul中18万多样本，但是这篇论文源码特征使用joern提取代码属性图后确预处理了，源代码中特征生成也只保留节点数<400的，还有无法解析的。简直很难让人复现。欢迎这个方向的朋友讨论一下。  
> **论文题目**  
：LIVABLE: Exploring Long-Tailed Classification of Software Vulnerability Types**发表期刊**  
：IEEE Transactions on Software Engineering (TSE 2024, 软件工程顶级期刊)**论文作者**  
：Xin-Cheng Wen, Cuiyun Gao*, Feng Luo, Haoyu Wang, Ge Li, Qing Liao**作者单位**  
：哈尔滨工业大学（深圳）、鹏城实验室、华中科技大学、北京大学  
  
## 📌 论文概览与研究背景  
  
在软件安全领域，深度学习（DL）和图神经网络（GNN）已广泛应用于**漏洞检测**  
（即判断某段代码是否存在漏洞）。然而，仅告诉开发人员“这段代码有漏洞”是不够的。  
  
软件漏洞种类繁多，不同类型的漏洞其修复优先级和严重程度（CVSS 评分）截然不同。如果能精准预测**漏洞的具体类型**  
（如 CWE-119 缓冲区溢出、CWE-22 路径遍历等），将极大地方便开发者定位根因并优先修复高危漏洞。  
  
然而，研究人员在分析 National Vulnerability Database (NVD) 近十年的数据后发现，真实的软件漏洞类型分布呈现极端的**长尾分布（Long-Tailed Distribution）**  
：  
1. **头部类（Head Classes）**  
：极少数常见的漏洞类型占了绝大多数样本（如 CWE-119, CWE-20 等）。  
  
1. **尾部类（Tail Classes）**  
：绝大多数漏洞类型只有极少数样本（如 CWE-507, CWE-776 等）。  
  
### ⚠️ 关键挑战  
1. **尾部漏洞极具威胁**  
：虽然尾部漏洞样本少，但其严重性极高。统计显示尾部漏洞的平均 CVSS 得分为 7.01，某些极端尾部漏洞（如 CWE-507 木马程序）CVSS 得分高达 9.8。  
  
1. **GNN 的过平滑问题（Over-smoothing）**  
：代码结构图（结合了 AST、CFG、DFG 等）通常较深。传统 GNN 增加层数会导致节点表示趋同（过平滑），而层数太少又无法捕捉长距离依赖。  
  
1. **严重的数据不平衡**  
：标准模型倾向于预测头部类，直接忽略样本极少的尾部类，导致尾部预测准确率极低。  
  
为了解决上述难题，本文提出了首个专门面向长尾软件漏洞类型分类的自适应框架——**LIVABLE**  
（**L**  
ong-tailed software **V**  
ulner**AB**  
i**L**  
e type classification）。  
## 💡 核心创新点  
1. **首创性探索**  
：首次针对函数级软件漏洞类型的长尾分类问题展开深入研究。  
  
1. **差异化传播 GNN（Differentiated Propagation-based GNN）**  
：通过在每一步传播中显示融入初始节点特征，有效缓解了多层 GNN 的过平滑问题，大幅提升代码结构图的表示能力。  
  
1. **双通路协同增强**  
：结合基于序列的 Bi-LSTM 语义抽取与基于图的结构抽取，形成 Multi-View（多视图）代码表示。  
  
1. **自适应重加权机制（Adaptive Re-weighting Module）**  
：提出一种随着训练 Epoch 动态调整的损失函数，前期聚焦尾部难样本，后期兼顾头部平滑，实现头尾平衡学习。  
  
## ⚙️ LIVABLE 详细方法  
  
LIVABLE 整体架构主要由两大核心模块组成：**漏洞表示学习模块**  
与**自适应重加权模块**  
。![](https://mmbiz.qpic.cn/mmbiz_png/fuWvNJnVyic5PicibibhPjvia5OK76HcZzUO5hbo89GWcekTjbY4dPrIpHicZdW9LFicCaynkznvoYZjSNeefF41X6UIGcA7B31JYF8yGib8MaqmHn4/640?wx_fmt=png&from=appmsg "")  
  
### 1. 漏洞表示学习模块 (Vulnerability Representation Learning Module)  
#### (1) 差异化传播 GNN  
  
对于输入的代码结构图   
G  
(  
V  
,  
E  
)  
，首先使用 Word2Vec 将每个节点   
v  
 映射为 128 维向量，并通过 GRU 进行初始特征变换：  
  
  
![公式](https://mmbiz.qpic.cn/mmbiz_svg/Q3auHgzwzM7fAqJTKNvZygfIjOxztuXTKmHqqlgOJK4E3CvgvlvWkGF5CGBJTqibkgYszKYyWaO50O3eyEvic6hSMmliaEsNNpdBqmVStsiaR1yzcG9bjXMHmQ/640?wx_fmt=svg&from=appmsg "")  
  
- **X**  
：初始节点表示矩阵。  
  
- **F**  
：GRU 特征变换层。  
  
为了在深度传播中保留初始节点的差异性并消除过平滑，第   
l  
 层的节点表示   
![公式](https://mmbiz.qpic.cn/mmbiz_svg/Q3auHgzwzM7jMASdviboopkBOsBsTibKxgklicfD9wKRgicTW78f5uvwHOGVccBS9J1l0iaIEWE0xSO5ottvpibCC7rmeE7mgF9GKFUXuUAD5rlE4w6tpnT7OnqQ/640?wx_fmt=svg&from=appmsg "")  
  
 定义为：  
  
  
![公式](https://mmbiz.qpic.cn/mmbiz_svg/Q3auHgzwzM7R87TIGmOBdC1IjQExiaWCWqiaFuBSqW9P2AL59dib4PllmxsF6Maklu9x73HIeB1SxsbtaSRU3mjowmz5hVtqlWps8XQKb1sEQzSsFp96BP9fw/640?wx_fmt=svg&from=appmsg "")  
  
- ****  
：带自环的邻接矩阵（Adjacency Matrix）。  
  
- ****  
：  
![公式](https://mmbiz.qpic.cn/mmbiz_svg/Q3auHgzwzM7ewg0o7FcMpehib0muoVXEyseRMZpxNFiaH5cj4SpmiaRD81ZhKico7M8biaNSNpc04UYOhI7qZH89zuxqGEibia91gicoerV8lcxgcTAfrAVrnhgELA/640?wx_fmt=svg&from=appmsg "")  
  
 的度矩阵（Degree Matrix）。  
  
- **α**  
：传送超参数（Teleport Hyperparameter），用于控制初始特征在传播过程中的保留比例。  
  
最终通过混合池化（Hybrid Pooling）提取图特征   
![公式](https://mmbiz.qpic.cn/mmbiz_svg/Q3auHgzwzM6gzxrn01NWKSSXMj3FJWFZ618xRDOcM2few1UZ5ZtqZ479jQPKibKibS0HsFqTm70dV4TiaR68TwaCnb2kA95jB5penhu8ySLYSWUZTJ7DklJWg/640?wx_fmt=svg&from=appmsg "")  
  
：  
  
  
![公式](https://mmbiz.qpic.cn/mmbiz_svg/Q3auHgzwzM7icic8GJCD5pfLw2fFok2TIw5kHSqPqbGVQCicVBqutPGKibmUP6X9pUC7IWALrciaKpcmkEKVBYeY0dNYYic2IWnnXiaNXfrUmvcvGw7dzh5RRBkEw/640?wx_fmt=svg&from=appmsg "")  
  
- **AvgPool(⋅) / MaxPool(⋅)**  
：平均池化与最大池化，分别捕捉局部与全局图结构信息。  
  
- **C(⋅)**  
：多层感知机（MLP）。  
  
#### (2) 序列到序列语义提取  
  
针对代码 Token 序列   
![公式](https://mmbiz.qpic.cn/mmbiz_svg/Q3auHgzwzM4ZzguKoiakdVGEz9lxicUgBZwZFKjtrZtKNhrBjXXVR6VPJUVQm7xMx2icZJYHfhsg5F95l4XsCYujavqXgKrexyTkAnHUyVypic4F2TkweGZGnw/640?wx_fmt=svg&from=appmsg "")  
  
，使用双向 LSTM（Bi-LSTM）提取上下文语义特征：  
  
  
![公式](https://mmbiz.qpic.cn/mmbiz_svg/Q3auHgzwzM6aicav7lZggpOn6JWGRllwAa99RVSnPUM5BibjjLsKy6Ut20vvDoplkRnX7gKsxI3pn4l5b667uq0TiaHS4YMxgwAnPjbNxrP6dGZOabGCJbSRg/640?wx_fmt=svg&from=appmsg "")  
  
![公式](https://mmbiz.qpic.cn/mmbiz_svg/Q3auHgzwzM4Ebsb1yibtfRg2MpAknz6bpBqh5Yr6sVMSib94O4OMJu9WgNzUuUvjwCkrdsvjpdCHwMk3SbxCFYW6fwXOpHqdibEYFguQUxo1EPj0xKj8Alq2g/640?wx_fmt=svg&from=appmsg "")  
  
  
最终的漏洞融合表示为：  
  
  
![公式](https://mmbiz.qpic.cn/mmbiz_svg/Q3auHgzwzM69miaWseCuPFCGK2kRKria8laelib7VPqBtTGWW78oCbCrbmj3VHVgibxAQR3IO9kRlSYZdRZklwrBjngnyOXqd1dYyzszNLYkZ87ZRXZfiaM8lcQ/640?wx_fmt=svg&from=appmsg "")  
  
### 2. 自适应重加权模块 (Adaptive Re-weighting Module)  
  
针对长尾分布，设计了包含“尾部聚焦分支”和“头部聚焦分支”的全新训练损失函数   
L  
：  
  
  
![公式](https://mmbiz.qpic.cn/mmbiz_svg/Q3auHgzwzM6mHiasiaec5IATTfqNsIS6pet8SibQIvibsoMGYLt2pggDXdW3HhXACzlP5HsibKjTKTJic8pklQXYuqp3NwnvzcibYTsV4O87lOOAcSqwKUic2LDdLw/640?wx_fmt=svg&from=appmsg "")  
  
#### (1) 损失函数组成  
- **（Focal Loss，尾部聚焦）**  
：  
  
![公式](https://mmbiz.qpic.cn/mmbiz_svg/Q3auHgzwzM7icuhQx96hMevDUAZxW5iaR0d5Uic5HE9BtgK8NsAnBG8dib6CwiaT2O7I3RTXiaEG6icjrZEMGV8Tc0b1hkVZxa7TIFWHI7wkz3OfyicZQqLzQibfdFQ/640?wx_fmt=svg&from=appmsg "")  
  
  
通过调制因子   
![公式](https://mmbiz.qpic.cn/mmbiz_svg/Q3auHgzwzM7Xwiblaes95yW5Yiaiba2mVwN3YibgRZnwS9I0tMX9QpO2PScmGatu6lTBMzGGltKRsNcA3JLZsuZqeH2gvuhlNRuVqkMzGjwia3gT7cd4rC7yTug/640?wx_fmt=svg&from=appmsg "")  
  
 降低易分类样本权重，强迫模型关注难分类的尾部样本。  
- **（Label Smoothing Cross-Entropy，头部聚焦）**  
：  
  
![公式](https://mmbiz.qpic.cn/mmbiz_svg/Q3auHgzwzM5YrG9fvQLk7XfPufq33yR7vKuDRLiaIs5da8N0YCicwfOL1zJ4b3MPHicSuhkaWxra3DhtQzfrEM43vaXPanBCVWU2Jvliaegic61hD0cYdDqh2tw/640?wx_fmt=svg&from=appmsg "")  
  
  
通过平滑参数   
ϵ  
 与均匀分布   
![公式](https://mmbiz.qpic.cn/mmbiz_svg/Q3auHgzwzM47RyIaWMlJNkGEoqROChI640RBWPPft1VBXvmvDM4Q2Xfzt8fDprJIkfwCeUOo5s8DB4tFiazLJCAMHLJtFrjibG0P3VYJwUfGKJsiaiclr0arlg/640?wx_fmt=svg&from=appmsg "")  
  
，防止模型对头部类产生过拟合与过度自信。  
#### (2) 动态迁移权重 T  
  
权重   
T  
 随着训练 Epoch 动态更新：  
  
  
![公式](https://mmbiz.qpic.cn/mmbiz_svg/Q3auHgzwzM6qGBvs0dq5ovnXBj1IDFyoT0fIlic9R0eWJH6FauAXw3srShmRL5EIwrlEDnYTUK8nuwxE7qTRmuWgia7uaojPts7oskmE2quIXnSeyGPd36zA/640?wx_fmt=svg&from=appmsg "")  
  
- ****  
：当前训练轮数。  
  
- ****  
：总训练轮数。  
  
> **设计直觉**  
：在训练初期（  
![公式](https://mmbiz.qpic.cn/mmbiz_svg/Q3auHgzwzM7MyHxQZFIz4Gw7STqYyMzSFrDhAvBMufHicWU7d3K7S14VeedrnIEibYbjv1S8IibLk18kywrL2YpXLjvfYlZiarVOQ0dXRJTduKXBn9hszbnq9Q/640?wx_fmt=svg&from=appmsg "")  
  
 较小，  
T  
→  
1  
），模型重点学习较难提取的尾部类特征；随着训练深入（  
T  
 逐渐减小），重点逐步平滑过渡到头部类，避免双分支冲突。  
  
## 📊 实验评估与结果分析  
  
实验在服务器 NVIDIA GeForce RTX 3090 上运行，漏洞检测训练 100 轮，类型分类训练 50 轮。  
### 1. RQ1：漏洞类型长尾分类表现  
  
在从 Fan et al. 提取的 10,667 个漏洞函数（包含 91 种 CWE 类型及 None 类型）上的对比结果如下：  
  
  
<table><thead><tr style="border-top: 1px solid #ccc;border: none;background-color: transparent;"><th style="border-top: none;border-right: none;border-left: none;border-image: initial;padding: 6px 8px;font-size: 15px;line-height: 1.4;text-align: center;border-bottom: 2px solid rgb(4, 120, 87);background: transparent;color: rgb(19, 64, 52);font-weight: 700;letter-spacing: 0.3px;min-width: 100px;white-space: nowrap;"><section><span leaf="">方法</span></section></th><th style="border-top: none;border-right: none;border-left: none;border-image: initial;padding: 6px 8px;font-size: 15px;line-height: 1.4;text-align: center;border-bottom: 2px solid rgb(4, 120, 87);background: transparent;color: rgb(19, 64, 52);font-weight: 700;letter-spacing: 0.3px;min-width: 100px;white-space: nowrap;"><section><span leaf="">Head Acc (%)</span></section></th><th style="border-top: none;border-right: none;border-left: none;border-image: initial;padding: 6px 8px;font-size: 15px;line-height: 1.4;text-align: center;border-bottom: 2px solid rgb(4, 120, 87);background: transparent;color: rgb(19, 64, 52);font-weight: 700;letter-spacing: 0.3px;min-width: 100px;white-space: nowrap;"><section><span leaf="">Medium Acc (%)</span></section></th><th style="border-top: none;border-right: none;border-left: none;border-image: initial;padding: 6px 8px;font-size: 15px;line-height: 1.4;text-align: center;border-bottom: 2px solid rgb(4, 120, 87);background: transparent;color: rgb(19, 64, 52);font-weight: 700;letter-spacing: 0.3px;min-width: 100px;white-space: nowrap;"><section><span leaf="">Tail Acc (%)</span></section></th><th style="border-top: none;border-right: none;border-left: none;border-image: initial;padding: 6px 8px;font-size: 15px;line-height: 1.4;text-align: center;border-bottom: 2px solid rgb(4, 120, 87);background: transparent;color: rgb(19, 64, 52);font-weight: 700;letter-spacing: 0.3px;min-width: 100px;white-space: nowrap;"><section><span leaf="">Overall Acc (%)</span></section></th></tr></thead><tbody><tr style="border-top: 1px solid #ccc;border: none;background-color: transparent;"><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><section><span leaf="">Devign</span></section></td><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><section><span leaf="">38.26</span></section></td><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><section><span leaf="">7.35</span></section></td><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><section><span leaf="">23.08</span></section></td><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><section><span leaf="">34.99</span></section></td></tr><tr style="border-top: 1px solid #ccc;border: none;background-color: transparent;"><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><section><span leaf="">Reveal</span></section></td><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><section><span leaf="">39.39</span></section></td><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><section><span leaf="">42.65</span></section></td><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><section><span leaf="">46.15</span></section></td><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><section><span leaf="">39.83</span></section></td></tr><tr style="border-top: 1px solid #ccc;border: none;background-color: transparent;"><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><strong style="font-weight: 700;color: #12241c;letter-spacing: 0.2px;"><span leaf="">LIVABLE (Ours)</span></strong></td><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><strong style="font-weight: 700;color: #12241c;letter-spacing: 0.2px;"><span leaf="">64.79</span></strong></td><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><strong style="font-weight: 700;color: #12241c;letter-spacing: 0.2px;"><span leaf="">58.82</span></strong></td><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><strong style="font-weight: 700;color: #12241c;letter-spacing: 0.2px;"><span leaf="">53.85</span></strong></td><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><strong style="font-weight: 700;color: #12241c;letter-spacing: 0.2px;"><span leaf="">64.01</span></strong></td></tr></tbody></table>  
  
- **整体提升**  
：LIVABLE 的准确率达到 **64.01%**  
，相比 SOTA 方法 Reveal 提升了 **24.18%**  
。  
  
- **尾部突破**  
：在 Head、Medium、Tail 类上分别取得了 **25.40%**  
、**16.17%**  
 和 **7.70%**  
 的性能提升。  
  
- **综合指标**  
：在 Precision、Recall、Macro F1、Weighted F1 以及 MCC 显著优于所有 Baseline。  
  
### 2. RQ2：漏洞检测任务通用性验证  
  
在三个经典漏洞检测数据集（FFMPeg+Qemu、Reveal、Fan et al.）上，仅评估 LIVABLE 的漏洞表示学习（VRL）模块：  
  
  
<table><thead><tr style="border-top: 1px solid #ccc;border: none;background-color: transparent;"><th style="border-top: none;border-right: none;border-left: none;border-image: initial;padding: 6px 8px;font-size: 15px;line-height: 1.4;text-align: center;border-bottom: 2px solid rgb(4, 120, 87);background: transparent;color: rgb(19, 64, 52);font-weight: 700;letter-spacing: 0.3px;min-width: 100px;white-space: nowrap;"><section><span leaf="">数据集</span></section></th><th style="border-top: none;border-right: none;border-left: none;border-image: initial;padding: 6px 8px;font-size: 15px;line-height: 1.4;text-align: center;border-bottom: 2px solid rgb(4, 120, 87);background: transparent;color: rgb(19, 64, 52);font-weight: 700;letter-spacing: 0.3px;min-width: 100px;white-space: nowrap;"><section><span leaf="">指标</span></section></th><th style="border-top: none;border-right: none;border-left: none;border-image: initial;padding: 6px 8px;font-size: 15px;line-height: 1.4;text-align: center;border-bottom: 2px solid rgb(4, 120, 87);background: transparent;color: rgb(19, 64, 52);font-weight: 700;letter-spacing: 0.3px;min-width: 100px;white-space: nowrap;"><section><span leaf="">SOTA Baseline (Reveal)</span></section></th><th style="border-top: none;border-right: none;border-left: none;border-image: initial;padding: 6px 8px;font-size: 15px;line-height: 1.4;text-align: center;border-bottom: 2px solid rgb(4, 120, 87);background: transparent;color: rgb(19, 64, 52);font-weight: 700;letter-spacing: 0.3px;min-width: 100px;white-space: nowrap;"><section><span leaf="">VRL Module (Ours)</span></section></th><th style="border-top: none;border-right: none;border-left: none;border-image: initial;padding: 6px 8px;font-size: 15px;line-height: 1.4;text-align: center;border-bottom: 2px solid rgb(4, 120, 87);background: transparent;color: rgb(19, 64, 52);font-weight: 700;letter-spacing: 0.3px;min-width: 100px;white-space: nowrap;"><section><span leaf="">相对提升</span></section></th></tr></thead><tbody><tr style="border-top: 1px solid #ccc;border: none;background-color: transparent;"><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><strong style="font-weight: 700;color: #12241c;letter-spacing: 0.2px;"><span leaf="">FFMPeg+Qemu</span></strong></td><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><section><span leaf="">Accuracy / F1</span></section></td><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><section><span leaf="">61.07% / 62.19%</span></section></td><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><strong style="font-weight: 700;color: #12241c;letter-spacing: 0.2px;"><span leaf="">64.84%</span></strong><section><span leaf=""> / </span><strong style="font-weight: 700;color: #12241c;letter-spacing: 0.2px;"><span leaf="">67.39%</span></strong></section></td><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><section><span leaf="">+3.77% / +8.36%</span></section></td></tr><tr style="border-top: 1px solid #ccc;border: none;background-color: transparent;"><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><strong style="font-weight: 700;color: #12241c;letter-spacing: 0.2px;"><span leaf="">Reveal</span></strong></td><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><section><span leaf="">Accuracy / F1</span></section></td><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><section><span leaf="">81.77% / 41.62%</span></section></td><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><strong style="font-weight: 700;color: #12241c;letter-spacing: 0.2px;"><span leaf="">93.53%</span></strong><section><span leaf=""> / </span><strong style="font-weight: 700;color: #12241c;letter-spacing: 0.2px;"><span leaf="">51.50%</span></strong></section></td><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><section><span leaf="">+6.04% / +23.74%</span></section></td></tr><tr style="border-top: 1px solid #ccc;border: none;background-color: transparent;"><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><strong style="font-weight: 700;color: #12241c;letter-spacing: 0.2px;"><span leaf="">Fan et al.</span></strong></td><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><section><span leaf="">Accuracy / F1</span></section></td><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><section><span leaf="">92.78% / 20.98%</span></section></td><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><strong style="font-weight: 700;color: #12241c;letter-spacing: 0.2px;"><span leaf="">95.05%</span></strong><section><span leaf=""> / </span><strong style="font-weight: 700;color: #12241c;letter-spacing: 0.2px;"><span leaf="">38.60%</span></strong></section></td><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><section><span leaf="">+2.27% / +68.78%</span></section></td></tr></tbody></table>  
  
  
结果表明，即便不加重加权模块，LIVABLE 提出的表示学习模块在传统二进制漏洞检测任务上也全面超越现有的图神经网络方法。  
### 3. RQ3：消融实验 (Ablation Study)  
  
  
<table><thead><tr style="border-top: 1px solid #ccc;border: none;background-color: transparent;"><th style="border-top: none;border-right: none;border-left: none;border-image: initial;padding: 6px 8px;font-size: 15px;line-height: 1.4;text-align: center;border-bottom: 2px solid rgb(4, 120, 87);background: transparent;color: rgb(19, 64, 52);font-weight: 700;letter-spacing: 0.3px;min-width: 100px;white-space: nowrap;"><section><span leaf="">变体模式</span></section></th><th style="border-top: none;border-right: none;border-left: none;border-image: initial;padding: 6px 8px;font-size: 15px;line-height: 1.4;text-align: center;border-bottom: 2px solid rgb(4, 120, 87);background: transparent;color: rgb(19, 64, 52);font-weight: 700;letter-spacing: 0.3px;min-width: 100px;white-space: nowrap;"><section><span leaf="">Accuracy (%)</span></section></th><th style="border-top: none;border-right: none;border-left: none;border-image: initial;padding: 6px 8px;font-size: 15px;line-height: 1.4;text-align: center;border-bottom: 2px solid rgb(4, 120, 87);background: transparent;color: rgb(19, 64, 52);font-weight: 700;letter-spacing: 0.3px;min-width: 100px;white-space: nowrap;"><section><span leaf="">贡献分析</span></section></th></tr></thead><tbody><tr style="border-top: 1px solid #ccc;border: none;background-color: transparent;"><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><strong style="font-weight: 700;color: #12241c;letter-spacing: 0.2px;"><span leaf="">LIVABLE (完整模型)</span></strong></td><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><strong style="font-weight: 700;color: #12241c;letter-spacing: 0.2px;"><span leaf="">64.01</span></strong></td><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><section><span leaf="">-</span></section></td></tr><tr style="border-top: 1px solid #ccc;border: none;background-color: transparent;"><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><section><span leaf="">w/o 序列表示 (仅 GNN)</span></section></td><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><section><span leaf="">56.59</span></section></td><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><section><span leaf="">序列语义模块贡献了 </span><strong style="font-weight: 700;color: #12241c;letter-spacing: 0.2px;"><span leaf="">14.65%</span></strong><span leaf=""> 的提升</span></section></td></tr><tr style="border-top: 1px solid #ccc;border: none;background-color: transparent;"><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><section><span leaf="">w/o 图表示 (仅 Bi-LSTM)</span></section></td><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><section><span leaf="">59.32</span></section></td><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><section><span leaf="">差异化 GNN 模块贡献了 </span><strong style="font-weight: 700;color: #12241c;letter-spacing: 0.2px;"><span leaf="">5.41%</span></strong><span leaf=""> 的提升</span></section></td></tr><tr style="border-top: 1px solid #ccc;border: none;background-color: transparent;"><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><section><span leaf="">w/o 自适应重加权 (仅 CE Loss)</span></section></td><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><section><span leaf="">59.32</span></section></td><td style="border-top: none;border-right: none;border-left: none;border-image: initial;border-bottom: 1px solid rgb(228, 233, 230);padding: 6px 8px;font-size: 15px;color: rgb(36, 42, 38);line-height: 1.4;text-align: center;min-width: 100px;white-space: nowrap;"><section><span leaf="">自适应重加权模块贡献了 </span><strong style="font-weight: 700;color: #12241c;letter-spacing: 0.2px;"><span leaf="">4.69%</span></strong><span leaf=""> 的提升</span></section></td></tr></tbody></table>  
  
### 4. RQ4：超参数敏感性  
- **GNN 层数**  
：传统 GNN 通常在 2 层时达到最佳，层数增加会导致过平滑。而 LIVABLE 在 **16 层**  
 时取得最佳准确率（64.01%），验证了差异化传播算法有效克服了过平滑问题。  
  
- **隐藏层维度**  
：Sequence 模块隐藏层大小设为 **512**  
 时性能达到峰值。  
  
## 📝 总结与展望  
  
LIVABLE 针对软件漏洞类型预测中的**长尾分布**  
与 GNN **过平滑**  
两大痛点，给出了极具创新性的解决方案：  
1. **网络层面**  
：利用传送机制改进 GNN 传播逻辑，结合 Bi-LSTM 构建了高鉴别力的多视图代码表示；  
  
1. **损失函数层面**  
：提出了随训练进程自适应演化的双分支损失函数，兼顾尾部难样本与头部平滑。  
  
**未来方向**  
：作者团队计划将漏洞类型识别进一步与漏洞自动修复（Vulnerability Repair）相结合，并研究跨语言（如 Java、Python）及更大粒度（项目级/文件级）的长尾漏洞分析技术。  
  
  

---
description: Casper FFG：以实现权益证明为目标的共识协议
---

# 延伸阅读

## 前言

2017年，Vitalik Buterin 与 Virgil Griffith 共同发表了 [**Casper the Friendly Finality Gadget（Casper FFG）**](https://arxiv.org/abs/1710.09437)。Casper FFG 是受 PBFT 启发并经过改良的共识协议，它虽然被设计得很**简洁（Simple）**，但其对安全性的证明却不**简单（Easy）**。

笔者将于本文解析 Casper FFG 的原理，读者可以一窥权益证明共识所尝试解决的问题及其设计理念。此外，Casper FFG 是以太坊 2.0 的共识机制，理解其运作也能帮助研究员与开发者进一步理解以太坊 2.0 的设计。

最后要特别感谢以太坊研究员 [Chih-Cheng Liang](https://medium.com/u/5c031577a87d?source=post_page-----95705e9304d6----------------------)（梁智程）提供重要素材并与笔者共同大量讨论及给予回馈，没有他的协助便不会有这篇文章的诞生。

## Casper FFG 是怎么开始的？

以太坊对**权益证明（Proof-of-Stake, PoS）**的研究最早可追溯至 2014 年的[这篇文章](https://blog.ethereum.org/2014/01/15/slasher-a-punitive-proof-of-stake-algorithm/)。从此之后，以太坊研究员们便一直朝「实现基于 PoS 的共识协议」此一目标前进。PoS 共识的设计是一个跨领域且相当复杂的问题，其包含计算机科学/经济学/密码学等方面。以太坊拥有区块链生态系中最跨领域的团队，对 PoS 的研究可以说是相当透彻。

笔者于日前翻译了一篇关于 Casper FFG 发展脉络的重要文献：

[Casper FFG 与 Casper CBC 的瑜亮情结](https://medium.com/taipei-ethereum-meetup/history-and-state-of-ethereums-casper-research-85e8fba26002?source=post_page-----95705e9304d6----------------------)

Casper FFG 受到 PBFT 的启发，并可以被视为改良后的 PBFT ——它继承了 PBFT 的重要设计，同时添加新的机制与简化若干规则。若读者对 PBFT 感到陌生，可以参考笔者日前针对 PBFT 的解析文：

[若想搞懂区块链就不能忽视的经典：PBFT](https://medium.com/taipei-ethereum-meetup/intro-to-pbft-31187f255e68?source=post_page-----95705e9304d6----------------------)

简而言之，PBFT 是一个具有二轮投票机制的共识协议，且具有下列特性：

* **许可制的（Permissioned）**：只有被「允许」的节点能参与共识。
* **基于领袖的（Leader-based）**：只由主导节点负责「提案」（Propose），其他节点只负责投票，因此需要视域变换（View Change）机制来节制不诚实的主导节点。
* **基于通讯的（Communication-based）**：使用决定性的（Deterministic）多数决来形成共识，而不是非决定性的（Non-Deterministic）算力解谜赛局。
* **安全性重于活跃性的（Safety-over-Liveness）**：无论网络是否延迟，协议都能保证共识的安全性（即不分叉），这赋予协议**即时敲定（Instant Finality）**的特性。

其中 PBFT 所具备的即时敲定性，或许是其受到 Vitalik 青睐的主要原因。Vitalik 在熟读 PBFT 后也特[撰文总结](https://medium.com/@VitalikButerin/minimal-slashing-conditions-20f0b500fc6c)，并于其中提出日后演变成 Casper FFG 的重要想法。

## Casper FFG 的前身

#### 砍押金的 4 条规则

PBFT 虽然具有即时敲定性，但并不具有抵抗共谋的能力，因此需要一个**惩罚机制**来遏止作恶的行为，**只要节点做出逾越规则的行为，便必须承受经济损失——透过经济学法则来调节节点的行为正是 PoS 的设计理念**。任何支付押金的节点，都可以加入网络参与共识，无需任何人的许可，因此基于 PoS 模型的共识都是**非许可制的（Permissionless）。**

在这里要澄清一下「许可」这件事。我们会说他「非许可制」，是因为任何验证节点可以加入和退出。但如果在他加入的时候，链要维持一个验证节点清单，从这个角度看又有点是「许可制」。从 PBFT 的角度看，投票的验证节点也必须从许可的清单中挑选。

那么下一个问题是：**哪些行为该被惩罚**？Vitalik 仔细推敲 PBFT 后发现，PBFT 只需 **4 条规则**（PBFT 中的**断言**）便能确保共识运作良好：

[最少的砍押金条件](https://medium.com/@VitalikButerin/minimal-slashing-conditions-20f0b500fc6c?source=post_page-----95705e9304d6----------------------)

Vitalik 在这篇文章中总结了这 4 条规则，并把它们称为 PBFT 的「**最少的砍押金条件**」（Minimal Slashing Conditions），任何违反此 4 条规则的行为都要被取走押金。这 4 条规则如下：

1. 提交（commit\_req）：收到 2/3 节点的预备讯息后才能提交。
2. 2.预备（prepare\_req）：每个预备讯息只能指向某个也具有 2/3 节点预备讯息的高度（Epoch），且这些预备讯息也必须都指向同一个高度。
3. 3.预备提交一致性（prepare\_commit\_consistency）:任何新的预备讯息只能指向最后一个已提交的或其他比其更新的高度。
4. 不重复预备（no\_double\_prepare）：不能在同一个高度送出两次预备。

这 4 条规则可以进一步简化为 2 条：

```text
某验证节点 v 必不可发出两个相异的投票：
<ν, s1, t1, h(s1), h(t1)>及
<ν, s2, t2, h(s2), h(t2)>, 
且使下列任一条件成立：
1. h(t1) = h(t2) 
验证节点必不可对某高度发出两个相异投票。
2. h(s1) < h(s2) < h(t2) < h(t1) 
验证节点必不可投出高度围绕/被围绕于另一投票高度的投票。
```

这 2 条规则便是 Casper FFG 的最少砍押金条件。

## Casper FFG 如何运作？

![Casper FFG: &#x68C0;&#x67E5;&#x70B9;&#x6811;](https://upyun-assets.ethfans.org/uploads/photo/image/57339d15164b4d0f8e816739fcfab978.png)

**Casper FFG 是一个将出块机制（Block Proposing Mechanism）抽象化的覆盖链（Overlay），只负责形成共识。**出块机制由底层链实现，而来自底层链的出块（Block Proposal）称为**检查点（Checkpoints）**。检查点组成**检查点树（Checkpoint Tree）**，例如：把高度为 0、50、100、150 的区块哈希值取出，形成一棵新的树，如上图所示。最底部的检查点则称为**根检查点（Root）。**

每个节点都必须对检查点送出**投票（Vote）**，投票的内容是由两个不同高度的检查点组成的**连结（Link）**，连结的起点高度较低，称为**源头（Source）**；连结的终点高度较高，称为**目标（Target）**。节点会将投票广播到网络中，并同时收集来自其他节点的投票。其中若投票给某连结 L 的节点押金总和超过全部押金的 2/3，则称 L 为**绝对多数连结（Supermajority Link）**，以 s → t 表示。例如上图中，b1 / b2 / b3 之间都形成了绝对多数连结，分别以 b1 → b2、b2 → b3 表示。

由根检查点开始，若两个检查点之间形成绝对多数连结，则该连结的目标进入**「已证成」（ Justified）**状态；而在连结建立当下已处于「已证成」状态的源头，则进入**「已敲定」（Finalized）**状态；根检查点则预设为「已证成」及「已敲定」状态。由此可知，每个检查点在经过两次投票后，会先**「证成」（Justify）**而后**「敲定」（Finalize）**，几乎等同于 PBFT 的「预备」与「提交」。例如在上图右边的分支中，r / b1 / b2 皆为「已敲定」状态，只有 b3 为「已证成」状态。

那么验证节点该对哪些检查点建立连结？每个节点都必须遵循**分叉选择规则（Fork Choice Rule）**来选择下一个要连接的检查点，**Casper FFG 的规则是：选择最高的「已证成」状态的检查点**。

![Casper FFG: &#x9A8C;&#x8BC1;&#x8282;&#x70B9;&#x96C6;&#x5408;&#x7684;&#x5927;&#x5E45;&#x53D8;&#x5316;&#x5F15;&#x8D77;&#x7684;&#x5206;&#x53C9;](https://upyun-assets.ethfans.org/uploads/photo/image/f3643ccf075f423db436891bbc187e8b.png)

由于 Casper FFG 能让任何存入押金的节点成为验证节点，因此**验证节点集合（Validator Set）**会动态地随着时间变化。节点从退出网络至取出押金需要等待一段期间，该等待期间称为**提领延迟（Withdrawal Delay）**。每个检查点 C 都有其对应的**朝代数（Dynasty）**，其定义为：从根检查点开始至 C 为止的**已敲定检查点数量**，例如上图中，b3 的朝代数为 3。每一代检查点都对应两种验证节点集合：**前端（Front）验证节点集合**（包含于此代加入的节点）以及**后端（Rear）验证节点集合**（包含于此代退出的节点）。理论上每代检查点的前端/后端集合会高度重复，但难保节点共谋造成前端/后端集合的大幅变化，若此情形发生，则出错时可能会砍不到坏节点的押金（因为坏节点已退出）导致安全性受到威胁。例如上图中，验证节点 A 可以退出，代表对 C' 分叉（绿色）来说 A 退出了，可是对 C 分叉（紫色）来说， A 却从来没退出过。因此 A 有办法继续投旧链 C，但新链 C' 砍不到 A 的押金（因为已退出）。

为了让每代检查点在出错时都能确实**归责**，因此需要**缝合机制（Stitching Mechanism）**将检查点的前端/后端集合「缝」起来，确保每个错误都必定能归责（出错的可能是前端集合或者后端集合）。

综合以上，Casper FFG 几乎针对 PBFT 的所有方面都做出改进：

* **经济上的制约**：PBFT 是许可制的，它仰赖原本就存在信任基础的组织共同运行协议；Casper FFG 则是非许可制的，它引入最少砍押金条件，利用经济损失的风险来制约节点的行为，节点之间不需要任何信任基础也能共同运行协议，实现真正的去中心化。
* **抽象的出块机制**：PBFT 仰赖诚实的主导节点产生区块并需要视域变换机制节制拜占庭节点；Casper FFG 无需理会底层的出块机制，只需负责形成共识。出块抽象的好处是：底层链的出块频率不必与覆盖链的共识频率一致，如此可以增加效率并降低网络的负担。例如：每 100 个底层区块只产生 1 个检查点。
* **流水线化的投票**：PBFT 具有 &lt; Prepare &gt;、&lt; Commit &gt;、&lt; View-change &gt; 等数种投票讯息；Casper FFG 仅有 &lt; Vote &gt; 一种，且投票的内容并不是单一的区块/请求，而是**两个**形成连结的检查点，这使 Casper FFG 能够在不牺牲太多表达力的前提下变得简洁许多。这些形成链式结构的检查点，会于两个不同高度分别经历两轮投票，由于每一轮投票都会**敲定**源头与**证成**目标，因此共识能如流水线（Pipeline）般不断推进。相似的设计理念也出现于 Hot-Stuff，有趣的是，该论文作者 Dahlia Malkhi 还[撰文](https://dahliamalkhi.wordpress.com/2018/03/13/casper-in-the-lens-of-bft/)比较 Hot-Stuff 与Casper FFG，其相似程度可见一斑。
* **强健的抗攻击性**：PBFT 不具备对**远程攻击（Long-range Attack）**以及**灾难性崩溃（Catastrophic Crash）**的抗性；Casper FFG 则具有特别的机制来防御这两种攻击：针对远程攻击，节点必须定期同步区块及禁止回朔（Revert）已敲定的区块；针对灾难性崩溃，Casper FFG 则引入**「离线溢金」（Inactivity Leak）**机制来应对。关于这两种攻击的说明，笔者将于日后另撰文论述。

由于 Casper FFG 相当简洁，以太坊研究员一度实现了合约版本的 Casper FFG：

[ethereum/casper](https://github.com/ethereum/casper/blob/master/casper/contracts/simple_casper.v.py?source=post_page-----95705e9304d6----------------------)

然而，这个合约版的 Casper FFG 后来被[弃用](https://notes.ethereum.org/@djrtwo/rJDrKoBOQ?type=view)了！在合约版中原本假设投票能够被并行处理，但在计算投票报酬有很多中间状态，不同投票处理的先后顺序将会影响最后得到的状态，这代表并行化将无法达成共识。而要修正这个问题则必须要在合约与客户端做大量修改，失去了「逻辑用合约实现，避免修改客户端」的精神。因此，为了能够更好地整合 Casper FFG 与其他优化提案（例如分片），全新的以太坊 2.0 [磅礴登场](https://notes.ethereum.org/@vbuterin/rkhCgQteN?type=view)了。

## 以太坊2.0 中的 Casper FFG

![&#x4EE5;&#x592A;&#x574A; 2.0: &#x5206;&#x7247;](https://upyun-assets.ethfans.org/uploads/photo/image/6465cbb3c7bf43c4b7ee7b1854b3fab9.jpeg)

以太坊 2.0 是一个基于 EVM 并整合 Casper FFG 与众多优化提案（以分片为主）的分布式帐本。以太坊 2.0 除了想实现 PoS，还试图将每秒交易数（TPS）扩展到 10000 笔的量级，使区块链成为如网际网络一般的基础设施（Infrastructure），并且让任何存入 32 个以太币的押金的节点都能成为验证节点。

**分片（Sharding）**即是为了增加可扩展性（Scalability）的重要设计，也是以太坊 2.0 最重要的目标。**分片就是分工合作**，我们可以用一个简单的例子来说明分片的概念（实际上的解释要比这复杂得多）： 2 人写 2 题作业，2 人各写不同的 1 题再合起来一定比 2 人都各写完 2 题来得更有效率。

目前的以太坊只有 1 条区块链，所有节点必须各自处理所有交易（如同 2 人各自写完 2 题作业）；在以太坊 2.0 中，网络会分成 1024个**片（Shard）**，每片分别运行 1 条**分片链（Shard Chain）**，它们将各自处理一部分的交易后再将结果交由 1 条**信标链（Beacon Chain）**统整（如同 2 人各做不同的 1 题再合起来）。因此，以太坊 2.0 预计会有 1 条信标链以及 1024 条分片链。

值得注意的是：**片是一个抽象层，并不特指某一群节点**。为了更了解这个概念，笔者扩充一下上文的例子：假设写作业有**找答案**及**抄答案**两个步骤，那么 A / B 2人写 2 题作业，由**读速快**的 A 找第 1 题答案，**读速慢**的 B 找第 2 题答案；由**手速快**的 B 抄第 1 题答案，**手速慢**的 A 抄第 2 题答案。如此，A / B 便可以依照读/写的快/慢来分别负责不同题目的不同步骤。

同样地，在以太坊 2.0 中，除了有 1024 个片，还会有 1024 个**持续委员会（Persistent Committee）**与 1024 个**交联委员会（Crosslink Committee）：**

* 每个片都会对应 1 个持续委员会与 1 个交联委员会，如同上例中每个题目可以依照读/写的步骤来对应不同的个体。
* 使用**链上随机数（On-chain Random Number）**决定各委员会的分派，如同上例中依照读/写的快/慢来分派题目（关于链上随机数的实现细节留待笔者日后详述）。
* 持续委员会负责维护分片链与产生**分片区块（Shard Block）**、交联委员会负责维护信标链与产生**信标区块（Beacon Block）**，如同上例中读速快的负责找答案、手速快的负责抄答案。各区块的出块节点（Block Proposer）也交由链上随机数决定。

**换句话说，每个验证节点都需维护 1 条唯一的信标链及 1 条所属片的分片链，也都会隶属于与该分片对应之 1 个交联委员会与 1 个持续委员会。**

Casper FFG 是运行于以太坊 2.0 之上的覆盖链，这个覆盖链同样由检查点构成，各检查点之间的跨度称为**时期（Epoch）**，1 个时期（Epoch）切成 64 个**时段（Slot ）**，每个时段对应 16 个片（16 = 1024 ÷ 64），因此每片在每时期中都有对应的时段，并只能在轮到自己时才广播其对检查点的投票，且每分片只能 1 个时段中投出 1 票——也就是说，各分片需要先对投票内容形成共识，不过各片内部形成共识的方法仍尚未定论，近期最新的提案是使用[聚合签名](https://github.com/ConsenSys/handel)。另外，Casper FFG 在以太坊 2.0 中的分叉选择规则是**最新消息驱动GHOST（Latest-Message Driven GHOST, LMD GHOST）**。

理论上，Casper FFG 于每个检查点的投票应该要与底层出块机制的投票分开；实际上，以太坊 2.0 的底层投票内容会同时包含顶层投票内容（检查点的连结），如同顶层投票搭了底层投票的便车（Piggyback），借此优化效能。如此在每个时期结束时，每个片都会收到所有其他片在该时期的投票，Casper FFG 活跃性得以维持。

## 结语

Casper FFG 是一个实现权益证明的大胆尝试，它在以太坊 2.0 的表现值得期待。然而以太坊 2.0 还有许多难题留待解决，例如轻节点（Light Client）/ 链上随机数生成器（On-chain Random Number Generator）/ 跨片交易（Cross-shard Transaction）等等。与此同时，许多以太坊 2.0 的竞争者也提出新的共识协议与分片技术，例如 RapidChain / Harmony / Chainspace 等等。

Casper FFG 以及以太坊 2.0 是经过众多研究员/开发者不断激荡与迭代的重要结晶，但一直以来都缺乏提供系统性论述的中文材料，希望此文可以帮助中文世界的研究员/开发者快速理解 Casper FFG 与以太坊 2.0 的精要。

\*\*\*\*

本文由 [Juin Chiu](https://medium.com/@juinc) 首发于 [Taipei Ethereum Meetup 的 Medium 站](https://medium.com/taipei-ethereum-meetup/intro-to-casper-ffg-and-eth-2-0-95705e9304d6)。


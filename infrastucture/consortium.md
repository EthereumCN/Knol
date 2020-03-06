# 联盟链

### 选择代码库

当前有8种以太坊协议的实现：

* Go [http://github.com/ethereum/go-ethereum](http://github.com/ethereum/go-ethereum) 
* C++ [http://github.com/ethereum/cpp-ethereum](http://github.com/ethereum/cpp-ethereum) 
* Python [http://github.com/ethereum/pyethereum](http://github.com/ethereum/pyethereum) 
* Javascript [http://github.com/ethereum/ethereumjs-lib](http://github.com/ethereum/ethereumjs-lib) 
* Java [https://github.com/ethereum/ethereumj](https://github.com/ethereum/ethereumj) 
* Haskell [https://github.com/jamshidh/ethereumH](https://github.com/jamshidh/ethereumH) 
* Rust \(Parity\) [https://github.com/ethcore/parity](https://github.com/ethcore/parity) 
* Ruby [https://github.com/janx/ruby-ethereum](https://github.com/janx/ruby-ethereum)

这些实现的不同之处在于：

* 许可证（GPL、LGPL、MIT、Apache）
* 性能 
* 特性完备度 
* 模块度 
* 修改难易程度 
* 集成其他系统难易程度

对于企业用例来说，性能或许更重要。目前已开展了以太坊客户端基准测试相关工作，请参阅：[https://github.com/ethereum/wiki/wiki/Benchmarks](https://github.com/ethereum/wiki/wiki/Benchmarks)

但是，在完备的客户端（C ++，Go，Haskell，Java，Parity）上建立一套全面的基准测试仍然是一项非常有意义的任务。关于许可证这个方面，Go客户端采用LGPL许可证，C ++客户端采用GPL许可证，目前正在改为采用Apache许可证，但仍未实现，Java客户端采用MIT许可证，而Parity客户端则采用GPL许可证。

### 配置文件

以太坊节点必须支持的配置文件遵循一个非正式标准，这个标准规定了网络参数。实行这个标准旨在使节点能够轻松连接到测试网与私有网，长期作用是确定具有不同属性的替代网络，包括共识算法、P2P网络协议、初始状态和协议规则。具体标准描述如下：

[https://knol.ethereum.cn/infrastucture/chain-spec-format](https://knol.ethereum.cn/infrastucture/chain-spec-format)

[https://github.com/ethereum/wiki/wiki/Ethereum-Chain-Spec-Format](https://github.com/ethereum/wiki/wiki/Ethereum-Chain-Spec-Format)

企业版以太坊客户端应遵循该标准的超集，以使以正确配置启动的节点充当以太坊公链节点，但这些节点具有更多适用于企业用途的API和其他功能。而以不同的设置启动的节点将通过拜占庭容错（PBFT）参与到给定的联盟链网络，或者使用不同虚拟机的测试网，或者比公链更早集成新扩容性功能的网络等等。

### 共识机制

目前，以太坊客户端支持工作量证明共识（PoW）机制。开发者有意通过使用权益证明（Casper）以及专用于私有链的共识算法模块化共识算法。初始目标或许是采用PBFT和DPoS，DPoS本质上是轮询调度共识算法（round robin consensus algorithm）。第一步是明确规定共识算法“类（class）”的“接口（interface）”。

接口大概如下：

* `submitTransaction(tx)`：向参与节点提交交易。该节点将尝试将其包含在区块中，或对其进行投票，或用共识算法执行等效操作。
* `sendMessage(msg, node_id)`：发送消息到另一个节点
* `broadcastMessage(msg)`：广播消息
* `getMostRecent(conf_level)`：获得满足规定确认数的最新区块。在PoW或DPoS中，conf\_level表示确认数量，因此它将返回链的后n个区块；PBFT中的确认是二进制的（即最终确认是即时的），因此它总是返回最近确认的区块。

注意：公链共识也需要一种激励模型，以奖励表现良好的共识参与者及惩罚表现不佳的参与者。我们通过采用两个选项来对此进行抽象化：

* `finalize`函数，在处理完一个区块中的所有交易后会自动调用该函数，该函数会更改状态，并且使该区块充当输入值。
* `initialize`函数，在处理区块中的每个交易之前会自动调用该函数，该函数会更改状态，并且使该区块充当输入值。

注意：`finalize`或`Initialize`或许只调用某些以区块头作为数据的标准合约；从长远来看，以太坊公链计划转向这种方法。在联盟链中，无需使用这些函数来进行激励。但是，这些函数可能有利于其他目的，如自动运行已设定操作。因此，由于这些函数的效用与共识激励机制相关性不大，因此不应将它们视为共识接口的一部分，而应将它们视为交易处理规则。

在私有链中，目前有几种最合适的共识算法：

* Proof of authority（权威证明）：实质上就是一个拥有特定私钥的客户端生成所有区块 
* PBFT（或其他传统的拜占庭容错共识算法） 
* DPOS（或其他基于链的有限验证者共识算法） 
* Casper具备固定验证者集的以太坊权益证明算法

PBFT和DPOS纯粹用作共识算法，因此token持有人不具备对代表进行投票的权利。这两种共识算法各有优缺点。 

尤其是以下五点：

* 假定部分同步网络模型中至少67％的节点在线且诚实，PBFT就可生成针对各种属性如安全性、活动性等的学术上可靠正式证明。  
* PBFT具有即时最终确定性。一旦确认交易一次，就不可能进行还原。DPoS共识机制在一定程度上像PoW一样，但实际上，DPoS情况稍差一些，因为攻击者可以估算到他们是否能够提前恢复短程分叉，因此“de-facto finality”这一概念是有可能发生的，不过需要几分钟时间。 
* DPoS可以支持无限个节点，而PBFT在大约超过30个节点以上时效率很低，因为每个节点每次都要向其他节点发送消息和从其他节点接收消息。之所以DPoS支持无限个节点，是因为每次都只由一个随机选择的验证者生成一个区块。 
* 从概念上来说，DPoS更易于理解和实施，能够直观地了解其汇聚的原因。  
* 在同步网络模型中，DPOS可以处理的拜占庭故障多达49％，高于PBFT的33％。此外，如果DPOS包含离线节点而不攻击网络，那么它甚至可以处理更多故障。

Casper被设计成一种任何人都可以通过存款来成为验证者的加密经济学算法。但是，只需指定固定的验证者集，即可将其重新用于许可目的。在主要以太坊客户端中实现Casper之后，这种方法值得探索。 

 在某些情况下可能需要权威证明，如需要特定的安全属性。以太坊技术更多地用于提供确定性、提供可验证执行和审计属性。因此，有必要实现这三种功能，给每个用户留有选择的权利。

### 抽象化

在以太坊协议开发历程中，抽象化概念最主要的基本哲学之一，它指的是协议本身应尽可能简单，并且应尽可能通过合约代码实现，而不是通过严格的协议规则来实现。进行抽象化的目标包括：

* 账号安全（参见[https://github.com/ethereum/EIPs/issues/86）](https://github.com/ethereum/EIPs/issues/86）)
* 交易状态转换功能（即处理交易的规则） 
* 以太币（即将以太币变成ERC-20代币） 
* 日志/活动

### P2P网络

以太坊当前的P2P网络属于Kademlia架构，详细信息请点击下方链接： 

* [https://github.com/ethereum/devp2p/blob/master/rlpx.md](https://github.com/ethereum/devp2p/blob/master/rlpx.md)
* [https://github.com/ethereum/go-ethereum/wiki/Peer-to-Peer](https://github.com/ethereum/go-ethereum/wiki/Peer-to-Peer)

私有链或有意使用相同的网络代码，但会在配置文件中设置不同的网络ID，或使用其他类型的网络代码。最有可能的替代方案是一种每个节点都直接连接到其他节点的设计方案。在大约少于20个节点的网络中，这种方案非常具有可行性，并且可能是最佳方案。另外，还可以选择在网络甚至更低级别网络上进行连接。比如，所有节点都应位于同一子网中吗？如果它们在现实中距离较近，那么光纤连接是否是最佳选择？许多像这样的细节基本上不包括在EES代码库内，但是代码库应确保在许多常见的预期配置下都能正常工作。

### 隐私保护协议

相关可用方法的详细说明，请参阅[https://blog.ethereum.org/2016/01/15/privacy-on-the-blockchain/。](https://blog.ethereum.org/2016/01/15/privacy-on-the-blockchain/。)

### APIs

当前，以太坊代码库支持一组基本的API，用于查询区块链记录、合约状态、日志和发送交易等。进行较复杂查询（如特定账户的特定令牌余额及基于区块链订单要价的查询）的主要方式就是“虚拟交易”。本质上指的是客户端可以假装在本地执行交易，这将调用合约功能，并返回相应的值。然后，API或许将同步返回该值。 

或许非常有用的一类特殊API是能够对区块链进行SQL查询和类似查询的API。在这个方向上有一些现有的项目，请参见：

* 
[https://www.reddit.com/r/ethereum/comments/4gcsn2/introducing\_etherquery\_perform\_sql\_queries/](https://www.reddit.com/r/ethereum/comments/4gcsn2/introducing_etherquery_perform_sql_queries/) [https://github.com/jamshidh/ethereum-data-sql](https://github.com/jamshidh/ethereum-data-sql)


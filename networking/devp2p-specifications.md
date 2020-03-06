---
description: 以太坊点对点网络协议
---

# devp2p

devp2p的Github Repo包含以太坊所使用的点对点网络协议规范。

* [以太坊节点记录](https://github.com/ethereum/devp2p/blob/master/enr.md) \(Ethereum Node Records\)，节点元数据格式
* [节点发现协议 v4](https://github.com/ethereum/devp2p/blob/master/discv4.md)
* [节点发现协议 v5](https://github.com/ethereum/devp2p/blob/master/discv5/discv5.md)（规范处于草稿阶段）
* [RLPx传输协议](https://github.com/ethereum/devp2p/blob/master/rlpx.md)（第五版）以及基于RLPx的功能：
  * [以太坊线路协议](https://github.com/ethereum/devp2p/blob/master/caps/eth.md)（64版）
  * [以太坊轻客户端子协议](https://github.com/ethereum/devp2p/blob/master/caps/les.md)（第三版）
  * [Parity轻协议](https://github.com/ethereum/devp2p/blob/master/caps/pip.md)（第一版）

## 目标

devp2p是构建以太坊点对点网络的网络协议集合。此处“以太坊网络”'的含义十分广泛，即devp2p并非仅适用于特定的区块链，而是服务于以太坊相关的任何应用。

devp2p的目标是在多种编程环境中实现由正交组件构成的集合系统。该系统不仅能够发现整个网络中的其他参与者，还能保证参与者之间的互相沟通。

在仅在给出规范的情况下，devp2p中的网络协议也应该易于从头实现，并且即使是在消费者级网络连接存在限制的情况下，也必须能够运行。团队通常在设计协议时采取以“规范为本”的方式，但是规范必须随附有效的原型或是能够在合理时间内实现。

## devp2p vs. libp2p

[libp2p](https://libp2p.io/)与devp2p项目几乎同时启动，旨在以模块化组件构成点对点网络，而libp2p就是这一系列组件的集合。有一个问题经常被问及：devp2p和libp2p之间是什么关系？

其实很难去比较这两个项目，因为它们有着不同的范围和目标。devp2p是一个集成化的系统定义，其目标是尽力满足以太坊的需求（也可能十分适用于其他应用程序），而libp2p是由编程库构成的集合，不服务于单个特定应用。 

也就是说，这两个项目在本质上高度相似，随着libp2p越来越成熟，devp2p会逐渐采用其中的部分内容。

## 实现

devp2p is part of most Ethereum clients. Implementations include:

大多数以太坊客户端都采用了devp2p，包括：

* C\#: Nethermind [https://github.com/NethermindEth/nethermind](https://github.com/NethermindEth/nethermind)
* C++: Aleth [https://github.com/ethereum/aleth](https://github.com/ethereum/aleth)
* C: Breadwallet [https://github.com/breadwallet/breadwallet-core](https://github.com/breadwallet/breadwallet-core)
* Elixir: Exthereum [https://github.com/exthereum/ex\_wire](https://github.com/exthereum/ex_wire)
* Go: go-ethereum/geth [https://github.com/ethereum/go-ethereum](https://github.com/ethereum/go-ethereum)
* Java: Tuweni RLPx library [https://github.com/apache/incubator-tuweni/tree/master/rlpx](https://github.com/apache/incubator-tuweni/tree/master/rlpx)
* Java: Besu [https://github.com/hyperledger/besu](https://github.com/hyperledger/besu)
* JavaScript: EthereumJS [https://github.com/ethereumjs/ethereumjs-devp2p](https://github.com/ethereumjs/ethereumjs-devp2p)
* Kotlin: Tuweni Discovery library [https://github.com/apache/incubator-tuweni/tree/master/devp2p](https://github.com/apache/incubator-tuweni/tree/master/devp2p)
* Nim: Nimbus nim-eth [https://github.com/status-im/nim-eth](https://github.com/status-im/nim-eth)
* Python: Trinity [https://github.com/ethereum/trinity](https://github.com/ethereum/trinity)
* Ruby: Ciri [https://github.com/ciri-ethereum/ciri](https://github.com/ciri-ethereum/ciri)
* Ruby: ruby-devp2p [https://github.com/cryptape/ruby-devp2p](https://github.com/cryptape/ruby-devp2p)
* Rust: Parity Ethereum [https://github.com/paritytech/parity-ethereum](https://github.com/paritytech/parity-ethereum)


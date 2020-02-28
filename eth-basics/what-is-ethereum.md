# 以太坊通识

## 以太坊简介

以太坊，译自 Etheruem，是 Vitalik 借鉴比特币及社区后来所提出的一些想法所创造的一个通用区块链项目。名字来源与 Ether \(以太\) 和后缀 ruem \(希腊语义是“石油”\)，国内的早期社区成员将其翻译成“以太坊”。不同于比特币的货币定义，以太坊释放了区块链前所未有的潜力，通过一些列的重新设计，包括虚拟机\(EVM, Etheruem Virtual Machine\)，账户系统，在其上可以运行智能合约。智能合约的概念是由Nick Szabo（尼克·萨博）于1997年提出的，即自动执行的合约形式。

以太坊是去中心化的、可以运行可转移计算和数据的自洽经济系统。

必须指出的是，以太坊生态发展速度极快，核心开发和 DApps（去中心化应用）不断涌现，在这里可能无法囊括所有关于以太坊发展的实际情况，但我们会及时更新相关发展。

## 关于以太坊

> 这篇文章从技术方面简要介绍了以太坊。
>
> [https://medium.com/@djrtwo/ethereum-in-a-sentence-ba2db11c6bec](https://medium.com/@djrtwo/ethereum-in-a-sentence-ba2db11c6bec)

[以太坊](https://www.ethereum.org/)是一个[去中心化](https://medium.com/@VitalikButerin/the-meaning-of-decentralization-a0c92b76a274)的区块链平台，用于构建去中心化应用程序（DApp），以太币（Ether）是该平台所使用的加密货币。以太坊几乎可以用于任何类型的交易或协议（换句话说，具有经济或治理属性的任何类型的活动），其成本比传统的替代方案要低，例如银行卡支付，PayPal 和投票，并且全程采用去中心化、去信任（无需中介）、安全有效并且抗审查的形式。

以太坊有许多不同的诠释。

要了解以太坊，最简单的方法就是知道它能用来做些什么。就像我们不必为了使用汽车的功能而全面了解汽车的工作原理，了解以太坊已有的和潜在的用途，不失为一个好的开端。以太坊的功能包含但不限于[这些用例](https://github.com/ethereum/wiki/wiki/Decentralized-apps-%28dapps%29)。

以下是更为宏观的介绍，最后的以太坊黄皮书是关于以太坊虚拟机的技术介绍和规范。

* [”一代智能合约和去中心化应用平台“——以太坊白皮书](https://github.com/ethereum/wiki/wiki/White-Paper)
* [一个开源的”可编程区块链“——Ethdocs](http://ethdoc.cn/)
* ”一种安全的去中心化的通用交易账本“以及通用的”具有共享状态的交易化单例状态机“。也被诠释为”加密法律“，”以太坊在整体上可以看作一个基于交易的状态机：起始于一个创世状态，然后随着交易的执行状态逐步改变达到一个最终状态。该最终状态将被看作以太坊世界的权威”版本“。状态中包含的信息有：账户余额、信誉度、现实世界相关数据等。简而言之，能包含电脑可以表现的任何信息。因此交易成为了两个状态之间的有效桥梁，”有效性"十分重要，因为无效状态改变远多于有效状态改变。例如，无效状态改变可能是减少账户余额，但同时没有在另一个账户上增加同等的额度。而有效的状态改变是通过交易发生的。“——[以太坊黄皮书](https://knol.ethereum.cn/eth-basics/evm-basics/yellowpaper)

#### 常见的基本术语到底是什么意思？

**去中心化**技术使用[点对点计算机网络](https://en.wikipedia.org/wiki/Peer-to-peer)（如下图所示），不受中央权力机构（例如政府或服务器管理者）的约束，因此可以更好地服务于公共决策的制定。**区块链**在结构上意味着“区块”+“链”，通过在先前确认有效的区块之后验证并添加新的交易区块来维护数字资产，从而形成“链”式结构。随着时间的推移，随着越来越多点对点网络中的节点对区块进行验证，要攻击已经添加到链中的区块也会越来越难。

区块链技术被称为**Web 3.0**。万维网（这要追溯到Web 1.0时代）由发布内容的网站和被动阅读、查看内容的用户群体组成。 而后，Web 2.0实现了用户交互，例如论坛（用户可以进行点赞和评论）、回应按钮（以Facebook为例：点赞👍/喜欢❤️/哈哈😆/哇😲/悲伤😢/愤怒😠）、分享（二次发布）。这些交互行为对宿主网站不产生直接的经济影响，同样用户也无法共享网站所创造的价值。 因此Web 3.0开始被定义为：从[客户端-服务器网络](https://en.wikipedia.org/wiki/Client%E2%80%93server_model)（向用户提供服务的中心化服务器）转向点对点网络和区块链，并且[将中心化的权力分配给参与网络的个人主体](https://github.com/DemocracyEarth/paper/blob/master/README.mediawiki)。

![&#x70B9;&#x5BF9;&#x70B9;&#x7F51;&#x7EDC;](https://camo.githubusercontent.com/a83d6ca09a1c9e8b181d23d78838fceb6aa42a0b/68747470733a2f2f73757374657267792e66696c65732e776f726470726573732e636f6d2f323031372f30352f32303070782d7032702d6e6574776f726b2d7376672e706e67)

![&#x57FA;&#x4E8E;&#x4E2D;&#x5FC3;&#x5316;&#x670D;&#x52A1;&#x5668;&#x7684;&#x7F51;&#x7EDC;](https://camo.githubusercontent.com/b214a512ed66682bd305f5a1bff12d85c10c0471/68747470733a2f2f73757374657267792e66696c65732e776f726470726573732e636f6d2f323031372f30352f32303070782d7365727665722d62617365642d6e6574776f726b2d7376672e706e67)

**加密货币**指一种使用加密代码以保护交易的数字货币，该加密代码能够通过硬件计算能力（称为“挖矿”或proof-of-work“工作量证明”）或其他能耗较低的方式（例如proof-of-stake“权益证明”）来解决。 

像[ZK SNARKs](https://crypto.stackexchange.com/questions/19884/what-are-snarks)这样的零知识证明也可以用于增强加密货币交易的私密性🤐（私密性与安全性有所不同），从而无需在以太坊企业联盟\([EEA](https://entethalliance.org/)\)等须授权\( permissioned\)的私有网络上运行应用程序。

## [以太坊历史](https://knol.ethereum.cn/extended-resources/short-history-of-ethereum)

## 使用以太坊

以太坊的平台使其比单纯的加密货币更有效用。通过以太坊，用户可以创建任何去中心化应用程序（即DApp，使用点对点网络而不是前文所提到的中心化客户端-服务器网络💻 ️），理论上可编程的以太坊可以用于任何经济或治理活动。

#### [DApp参考列表](https://github.com/ethereum/wiki/wiki/Decentralized-apps-%28dapps%29)

#### [加密货币市场总览（以太坊）](https://cryptolization.com/ethereum)

## 以太坊的意义

以太坊为全球金融系统打开了一扇大门，用户只需要通过互联网就能够以去信任化的方式访问应用程序、产品和服务。每一个人都可以与以太坊网络进行交互，亲自参与到这种数字经济形式中，而不需要第三方，还能抗审查。通过以太坊平台上的治理应用程序和系统，甚至有可能消除国家之间由于边界而产生的障碍，创建一个更加开放、包容和公平的人类社会。

## 延伸阅读

* [ethereum.org上的以太坊学习资料](https://www.ethereum.org/learn/)
* [以太坊社区贡献的以太坊百科](https://eth.wiki/en/ethereum-introduction)
* [EthHub](https://docs.ethhub.io/ethereum-basics/what-is-ethereum/)
* [Blockgeek](https://blockgeeks.com/guides/ethereum/)
* [以太坊白皮书（英文版）](https://github.com/ethereum/wiki/wiki/White-Paper)[（中文版）](https://knol.ethereum.cn/eth-basics/whitepaper)
* [精通以太坊（英文版）](https://github.com/ethereumbook/ethereumbook)[（中文版）](https://github.com/inoutcode/ethereum_book)


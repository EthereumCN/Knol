# 以太坊通识

## 以太坊简介

以太坊，翻译自 Etheruem，是 Vitalik 借鉴比特币及社区后来所提出的一些想法所创造的一个通用区块链项目。名字来源与 Ether \(以太\) 和后缀 ruem \(希腊语义是“石油”\)，国内的早期社区成员将其翻译成“以太坊”。不同于比特币的货币定义，以太坊释放了区块链前所未有的潜力，通过一些列的重新设计，包括虚拟机\(EVM, Etheruem Virtual Machine\)，账户系统，在其上可以运行智能合约。智能合约的概念由Nick Szabo（尼克 萨博）于1997年提出的一个自动执行的合约形式。

以太坊是去中心化的、可以运行可转移计算和数据的自洽经济系统。

必须指出的是，在以太坊世界里，发展速度极快，核心开发和 DApps（去中心化应用）不断涌现，我们这里可能无法囊括所有关于以太坊发展的实际情况，我们会及时更新相关发展。

## 关于区块链

网上有很多关于区块链的教学视频，可以看看这个 [TED talk](https://www.youtube.com/watch?v=RplnSVTzvnU)。

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

### 以太坊历史



## Uses

The platform part of Ethereum makes it much more useful than just a cryptocurrency. With it, you can create any decentralized application \(known as a dapp, which works over a peer-to- peer network rather than a centralized client-server network 💻🕸️\), so the functionality is only limited by what programs could potentially do and not do, and by consequence, what programmers develop, 👨‍💻 but it can theoretically be used for any economic or governance activity.

### List of dapps

For a list of dapps, visit [here](https://github.com/ethereum/wiki/wiki/Decentralized-apps-%28dapps%29).

### Market analysis

Assessing the actual usage of blockchains is a more reliable indicator than the market cap. [http://blocktivity.info/](http://blocktivity.info/) provides data to do that.

As of the 9th of January 2018, [the market capitalisation of Ethereum is $118.5 billion USD](https://cryptolization.com/ethereum) \(refer to the link for the latest figure\), and [it has been in circulation since 30 July 2015](https://github.com/ethereum/wiki/wiki/Releases), with the [first transaction](https://www.reddit.com/r/ethereum/comments/6qildp/what_is_the_first_ever_ethereum_transaction/) after the [genesis block](https://etherscan.io/block/0) using Ethereum on [7 August 2015](https://etherscan.io/block/46147). Compare this with the next largest and the current largest cryptocurrency, [Bitcoin, with a market cap of $253.0 billion USD](https://cryptolization.com/ethereum), where [it has been in circulation since January 2009](http://www.newyorker.com/reporting/2011/10/10/111010fa_fact_davis). Technically, Ethereum has had a much faster growth rate, while more importantly for long term investment \(I do not encourage speculation or buying with capital that you can't afford to lose, without due diligence as that only causes volatility as has been seen\) the fundamentals are much better than Bitcoin. While it is true that Bitcoin has more of a market and currency, e.g. in terms of more entities that will accept it as a form of payment, the creator of this wiki expects that time will change that \(indeed the [market cap of Ethereum recently surpassed half that of Bitcoin, around May 2017](https://seekingalpha.com/article/4077679-ethereum-blasts-20-billion-market-cap-half-bitcoin)\). Also, [the number of transactions of Ethereum surpassed that of several cryptocurrencies combined on 22 Nov 2017](https://www.reddit.com/r/ethereum/comments/7est9k/ethereum_is_now_processing_more_transactions_a/). However, note [this retort](https://www.reddit.com/r/ethereum/comments/7est9k/ethereum_is_now_processing_more_transactions_a/dq7a31u/).

## Issues

There also several issues with Ethereum, such as not being scalable enough, not being fully decentralized, energy consumption with mining and quantum computing attacks. With its [large storage database](https://www.reddit.com/r/ethtrader/comments/7axn5g/ethereum_blockchain_sizewe_have_a_problem/) \(I have to provide a [Reddit link](https://www.reddit.com/r/ethtrader/comments/7axn5g/ethereum_blockchain_sizewe_have_a_problem/) as a source as the [original link](https://etherscan.io/chart/chaindatasizefull) doesn't have the graph any more, while [Wayback doesn't render it either](https://web.archive.org/web/20171211015955/https://etherscan.io/chart/chaindatasizefull)\), mining and architecture requiring to run a full node to mine or validate transactions, it is not decentralized enough. More \(outdated but still applicable\) info on that is e.g. [here](https://ethereum.stackexchange.com/questions/143/what-are-the-ethereum-disk-space-needs#826), as well as [here](https://github.com/ethereum/go-ethereum#full-node-on-the-main-ethereum-network).

### Scalability

Ethereum will need to scale to process far more transactions per second \(to become a "[world computer](https://www.youtube.com/watch?v=j23HnORQXvs)"\) than Visa, Mastercard and American Express combined \(which process on the order of [tens of thousands of transactions per second](https://usa.visa.com/run-your-business/small-business-tools/retail.html) \[in the link, CTRL+F 24,000\]\), while Ethereum 1.0, the current version as of December 30 2017, processed [a record of 1103523 transactions on Friday, December 22, 2017, or 12.77 transactions per second](https://web.archive.org/web/20171230005127/https://etherscan.io/chart/tx).

Note that [Ripple claims that it's Consensus Ledger can process a thousand transactions per second](https://ripple.com/dev-blog/ripple-consensus-ledger-can-sustain-1000-transactions-per-second/), while it could process more with payment channels. "Although payment channels achieve practically infinite scalability by decoupling payment from settlement, they do so without incurring the risk typically associated with delayed settlement." Further note that Ripple achieves this by trading off on decentralization, through a [distributed network of validators or distributed servers](https://ripple.com/build/xrp-ledger-consensus-process/), while it has been described as a [federation protocol](https://wiki.ripple.com/Federation_protocol).

There are even more scalable blockchains that use a delegated proof of stake \(DPOS\) consensus protocol, such as Bitshares and Steem. [Bitshares can apparently process 100,000 TPS](https://bitshares.org/technology/industrial-performance-and-scalability/).

More generally, in order to have faster payments or higher transaction throughput, you need to reduce the number of validators \(miners are a kind of validator that perform energy intensive computational work, finding a random nonce or sequence number in a large set of numbers\) in the consensus protocol, or reduce the other \(i.e. for faster payments you can reduce transaction throughput or reduce validators, while for higher transaction throughput you can reduce validators or have payments take longer to finalize\). This is [a trade-off triangle](https://twitter.com/VladZamfir/status/932319930363494400). You could potentially have one blockchain with [heterogeneous sharding](https://twitter.com/VladZamfir/status/932320997021171712), with different shards with a different degree of balance between these properties. Ethereum is working on [sharding](https://ethresear.ch/t/sharding-phase-1-spec/1407/26).

If you increase scalability in an instant via some blockchain or shard, while keeping latency constant \(or reducing it\) you need to reduce decentralization, which reduces the number of points of attack needed to compromise the whole network, i.e. reducing decentralization reduces security.

### Volatility

Ethereum and all cryptocurrencies \(except for USDT and DAI\) have a lot of [volatility](https://twitter.com/JamesCRay01/status/959595937856303104), [particularly](https://twitter.com/JamesCRay01/status/953506845015994368) during news events such as banning [individual companies that don't register with the local securities authority but are selling a security](https://twitter.com/leashless/status/941501389687214081), bans on all ICOs or cryptocurrencies, bugs \(see below\) or geopolitical tensions such as between the [North Korea and the US and South Korea](https://twitter.com/JamesCRay01/status/938926302525980673), with missile launch tests from the former and large-scale military drills from the latter two. Non-stable cryptos tend to drop when there is news that governments ban or may ban ICOs or trading of cryptocurrencies, e.g. with [China](https://t.co/E0FDsBzk01), [Brazil](https://t.co/361eAPRSBU), [South Korea](https://t.co/lVe8VIafIO), Europe and the US. On the other hand, other countries like Saudi Arabia and [Australia](https://t.co/zFza3AV6Hm) have been very supportive of cryptocurrencies. Non-stable cryptos tend to rise when there is news of geopolitical tensions, or just politicians making bad decisions. NSCs have dropped in price when there are issues with exchanges, such as [being](https://www.wired.com/2014/03/bitcoin-exchange/) [hacked](https://bravenewcoin.com/news/fourth-largest-bitcoin-exchange-bithumb-hacked-for-billions-of-won/), [flash crashes](https://twitter.com/JamesCRay01/status/939014321006305281), or just [closing down](https://t.co/WOIQPCbBzr).

The biggest risk with cryptocurrencies is not so much from actors within the ecosystem such as mining/validator attacks, **scams** \(note that there is no substitute for due diligence, but you can use scam checkers such as this site [here](https://fried.com/crypto-scam-checker/) and exchange issues\), but from regulators doing blanket bans on ICOs or more broadly, cryptocurrency trading. But this isn't reasonable \(certainly not as a permanent ban\) as they are useful for governments as well. Governments may also want to regulate or ban cryptocurrencies as they perceive them as a threat to devaluing their fiat currencies \(but this can be offset through regulation, taxation, etc.\), as well as a potential tool for tax evasion \(but this can be regulated e.g. with KYC\). There is also the major risk that people buy into it without really understanding it and/or with non-risk capital, and then sell it off when bad news events occur.

Note also that with stable cryptocurrencies, the price can't rise anywhere nearly fast as cryptocurrencies, as well as not being able to drop anywhere nearly as fast. So stable cryptocurrencies are more suitable as a store of value, but may not rise in value as much in the very long-term \(e.g. over a year\).

More news:

* [China to stamp out cryptocurrency trading completely with ban on foreign platforms](http://www.scmp.com/business/banking-finance/article/2132009/china-stamp-out-cryptocurrency-trading-completely-ban)
* [More Crypto Bans: Lloyds Shuts Down Credit Card Purchases](https://cryptocurrencynews.com/business/banking/%5Blloyds-credit-card-purchases-crypto-bans/)

### Energy consumption and centralization with Proof of work vs proof of stake

The mining process to crack cryptographic code \(specifically to discover the nonce, a very large number, for each block by trial and error\) requires a lot of computation power. Nevertheless, I'm guessing that the computation power should be less when you consider the **energy consumption** of incumbent financial systems. \(Think of extracting and processing resources to make coins and notes, minting and printing, energy consumption of banks and tiers of related energy consumption in the life cycle of fiat money.\) Still, developers of some cryptocurrencies such as Ethereum are transitioning to \(as is the case for Ethereum\), or already using, a different way of maintaining and creating blocks, known as proof of stake. For more information, you can see this Proof of Stake Wikipedia article [here](https://en.wikipedia.org/wiki/Proof-of-stake) \(although note the header warning about the article potentially not being verifiable or neutral due to relying heavily on sources too closely associated to the subject\). The tricky part is in getting proof methods to work better than proof of work, as outlined [here](https://en.wikipedia.org/wiki/Proof-of-stake) in the criticism section of the PoS Wiki. For more information about PoS, see Casper [The Friendly Finality Gadget](https://github.com/ethereum/research/tree/master/papers/casper-basics) for PoS validation with Proof-of-Work \(PoW\), [Casper The Friendly Ghost: CBC](https://github.com/ethereum/research/blob/master/papers/CasperTFG/CasperTFG.pdf) for full Proof-of-Stake \(PoS\) and a [testnet](https://hackmd.io/s/Hk6UiFU7z).

### Quantum computing attacks

If quantum computing becomes more performant Ethereum's cryptographic signature scheme, Elliptic Curve Digital Signature Algorithm \(ECDSA\), would be insecure. However, there are solutions for this that will be implemented soon in the [Constantinople release](https://github.com/ethereum/wiki/wiki/Releases) with [EIP 86: Abstraction of transaction origin and signature, which has "**Custom cryptography**: users can upgrade to ed25519 signatures, Lamport hash ladder signatures or whatever other scheme they want on their own terms; they do not need to stick with ECDSA." Lamport signatures could be used in a quantum resistant algorithm](https://github.com/ethereum/EIPs/pull/208). More info on that is e.g. [here](https://ethereum.stackexchange.com/a/13577/95840)\), [here](https://blog.ethereum.org/2015/12/24/understanding-serenity-part-i-abstraction/), and [here](https://github.com/ethereum/wiki/wiki/Ethereum-Development-Tutorial).

### No technological artefacts can be a panacea

For the continual improvement of humanity, there needs to be balance in life between things that benefit us materially and things that benefit us on higher levels, particularly spiritually. There is a risk that technology can make some people better off, and others worse off. So there needs to be consideration for how technology can be implemented to maximise [utility](https://en.wikipedia.org/wiki/Utilitarianism). One consideration of that is [here](https://medium.com/@RhysLindmark/co-evolving-the-phase-shift-to-cryptocapitalism-by-founding-the-ethereum-commons-co-op-f4771e5f0c83), which looks at using Ethereum to establish a global commons \(including infrastructure, healthcare, education and universal basic income\) with voluntary contributions or a tax on transactions.

### Bugs

Ethereum has had expensive bugs, such as those listed [here](https://github.com/ethereum/wiki/wiki/Major-issues-resulting-in-lost-or-stuck-funds) and [here](https://github.com/ethereum/wiki/wiki/Bugs).

## How do you buy and sell Ether, the currency of Ethereum?

Refer to [here](https://github.com/ethereum/wiki/wiki/Getting-Ether).

## Development

Are you interested in learning to develop smart contracts with Ethereum, and develop a really useful dapp and become a millionaire?

Check out the [Ethereum website](https://www.ethereum.org/)! Then, you can [read the Solidity docs](https://solidity.readthedocs.io/en/develop/).

If you want to help contribute to core development, there is also:

### Technical introduction and Ethereum Virtual Machine \(EVM\) intro

* the [Yellow Paper](https://github.com/ethereum/yellowpaper);
* [Beige Paper](https://github.com/chronaeon/beigepaper); and
* [Py-EVM](https://github.com/ethereum/py-evm).

### Core development

* [client development](https://github.com/ethereum/wiki/wiki/Clients);
* if you're interested in testing, see the documentation [here](https://ethereum-tests.readthedocs.io/en/latest/), as well as [the Github tests repo](https://github.com/ethereum/tests), [a Gist here \(it is outdated\)](https://gist.github.com/Souptacular/fd197b1fac7c6d2660b0bef27a33ed40#lll-and-evm-stack-resources), and [Gitter here](https://gitter.im/ethereum/tests);
* documentation and specification review, e.g. \(and in particular\) of [EIPs](https://github.com/ethereum/EIPs);
* [many other repositories](https://github.com/ethereum).

For R&D, refer to [here](https://github.com/ethereum/wiki/wiki/R&D).

### Programming languages

* Learn Python first, e.g. with [Learn Python the Hard Way](https://www.learnpythonthehardway.org/) \(I learnt using this, but it's rather condescending, so I'd recommend [Pydocs](https://docs.python.org/3/) instead\). There's others, e.g. [Codecademy](https://www.codecademy.com/learn/learn-python), and [Coursera](https://www.coursera.org/courses?languages=en&query=learn+python), etc. Knowing Python is useful for research and demonstration, with demonstration e.g. for [client development](https://github.com/ethereum/wiki/wiki/Clients), e.g. for [Py-EVM](https://github.com/ethereum/py-evm) which is being used as an Ethereum client, to implement statelessness and sharding, as well as [vyper](https://github.com/ethereum/Vyper), an experimental, secure, smart contract programming language;
* [LLL](https://media.consensys.net/an-introduction-to-lll-for-ethereum-smart-contract-development-e26e38ea6c23) \(also see [here](https://github.com/ethereum/solidity/tree/develop/liblll) and [here](https://github.com/ethereum/solidity/tree/develop/lllc)\);
* [JULIA](https://solidity.readthedocs.io/en/develop/julia.html), an intermediate language for different Ethereum virtual machines;

## Concluding remarks

Ether certainly seems like a good investment, and a good alternative to using fiat currencies, as well as an enabler for otherwise uneconomical business, due to lower transaction costs. It's more decentralized nature than central banks has advantages for trade from a local to global scale. With governance applications and systems on top Ethereum, it is even possible to do away with the hindering borders surmounted by nation-states. By doing away with these borders, society can be more open, inclusive and equitable.

However, all technology can only help mankind and the world to a certain extent. What is more important is for each and every person to become increasingly blissful. Each person must go within and enter a stillness of body and mind, which is when that bliss starts to manifest, and practice balanced living. Practicing certain techniques such as those given by Self-Realization Fellowship, such as daily Kriya yoga meditation, developing unconditional love that starts in the heart, keeping the mind at the point between the eyebrows, and moral living, helps each person manifest that bliss within, and from there, express that bliss outwardly at all times.

## Further reading

* [Another introduction](https://github.com/jamesray1/Ethereum-introduction/wiki/Ethereum-introduction)
* [MyEtherWallet knowledge base \(good for issues with wallets\)](https://myetherwallet.github.io/knowledge-base/)
* [An introduction \(Frontier first release, outdated\)](https://ethereum.gitbooks.io/frontier-guide/content/ethereum.html)
* [Here's another introduction, made in November 2017](https://medium.com/@Ethereum_AI/ethereum-introduction-what-exactly-is-it-why-care-how-to-invest-9a627ab04408)
* [Ethereum community on Gitter](https://gitter.im/ethereum)
* [Ethereum Wikipedia article](https://en.wikipedia.org/wiki/Ethereum)
* [Ethereum and the hodlers that love them](https://www.reddit.com/r/ethtrader/comments/6jyn9y/ethereum_the_hodlors_that_love_them/)

This article was originally created here in May 2017, and has been regularly updated since then: [https://sustergy.wordpress.com/2017/05/18/why-buy-ether-and-how/](https://sustergy.wordpress.com/2017/05/18/why-buy-ether-and-how/). Feel free to send a donation to the initial author at jamesray.eth, or make edits to it yourself, or fork it!

More resources: [https://blockbyblock.io/resources](https://blockbyblock.io/resources)


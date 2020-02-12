---
description: Casper Proof of Stake (权益证明) 研究汇编
---

# Casper PoS

## 什么是 Proof of Stake \(权益证明\)？

**权益证明（PoS）是一种用于公共区块链的共识算法，该算法取决于验证者在网络中的经济权益。**在基于工作量证明（PoW）的公共区块链（例如目前的比特币和以太坊）中，通过奖励解决密码难题的参与者，以验证交易并创建新区块（即挖矿）。而在基于 PoS 的公共区块链（以太坊即将发布的 Casper）中，一组验证者轮流对下一个区块进行提议和投票，每个验证者的投票权重取决于其押金（即权益）的多少。 PoS 的显着优势包括**提高安全性、降低中心化风险和节省能耗**。

通常，权益证明算法是这样运行的：区块链持续追踪一个验证者集合，任何持有区块链基础加密货币（以太坊为 ETH）的人都可以通过**发送一种特殊的交易将其  ETH 锁定为押金**，以成为验证者。然后，所有的验证者都可以通过参与共识算法完成新区块的创建并确认新区块的产生。

目前有许多种共识算法，对参与共识算法验证者的奖励机制也多种多样，因此，存在不同类别的权益证明机制。从算法角度来看，PoS主要有**两种**类型：基于链的 PoS 和基于BFT（拜占庭容错）的PoS。

在**基于链的 PoS** 中，该算法在每个时隙（每10秒的时间段可能作为一个时隙）中伪随机地选择一个验证者，并向该验证者分配创建单个区块的权限，并且该区块必须指向某个先前的区块（按照最长链规则，通​​常是最长链末尾的区块），因此随着时间的推移，大多数区块会按照顺序组成一条不断延伸的链。

在**基于BFT（拜占庭容错）的 PoS** 中，验证者被**随机分配**提议区块的权利，但是要就区块有效性达成一致需要通过多轮回合制过程，即每个验证者在每一回合中针对某个特定区块投票，从而就区块的规范性达成共识。在该过程结束时，所有（诚实的和在线的）验证者都将商定是否有任何给定区块被纳入链中，其结果是永久有效的。需要注意的是，区块仍然可以链接在一起，关键区别在于，对某个区块达成的共识将保留在区块内，而不取决于其后链的长度或大小。

## PoS vs. PoW

* No need to consume large quantities of electricity in order to secure a blockchain. \(It's estimated that both Bitcoin and Ethereum burn over $1 million worth of electricity and hardware costs per day as part of their consensus mechanism.\)
* Because of the lack of high electricity consumption requirements there is not as much need to issue as many new coins in order to motivate participants to keep participating in the network. It may theoretically even be possible to have negative net issuance, where a portion of transaction fees is "burned" thus decreasing the supply over time.
* Proof of Stake opens the door to a wider array of techniques that use game-theoretic mechanism design in order to more effectively discourage centralized cartels from forming and, if they do form, from acting in ways that are harmful to the network \(such as selfish mining in Proof of Work\).
* Reduced centralization risks, as economies of scale are much less of an issue. $10 million of coins will get you exactly 10 times higher returns than $1 million of coins, without any additional disproportionate gains because at the higher level you can afford better mass-production equipment, which is an advantage for Proof of Work.
* Ability to use economic penalties to make various forms of 51% attacks vastly more expensive to carry out than Proof of Work. To paraphrase Vlad Zamfir, "it's as though your ASIC farm burned down if you participated in a 51% attack".

#### 51％攻击

There are four basic types of 51% attack:

* Finality reversion: validators that already finalized block A then finalize some competing block A', thereby breaking the blockchain's finality guarantee.
* Invalid chain finalization: validators finalize an invalid \(or unavailable\) block.
* Liveness denial: validators stop finalizing blocks.
* Censorship: validators block some or all transactions or blocks from entering the chain.

In the first case, users can socially coordinate out-of-band to agree which finalized block came first, and favor that block. The second case can be solved with fraud proofs and data availability proofs. The third case can be solved by a modification to PoS algorithms that gradually reduces \("leaks"\) non-participating nodes' weights in the validator set if they do not participate in consensus; the Casper FFG paper includes a description of this.

The fourth is most difficult. The fourth can be recovered from via a "minority soft fork", where a minority of honest validators agree the majority is censoring them, and stop building on their chain. Instead, they continue their own chain, and eventually the "leak" mechanism described above ensures that this honest minority becomes a 2/3 supermajority on the new chain. At that point, the market is expected to favor the chain controlled by honest nodes over the chain controlled by dishonest nodes.

## Staking常见问题

#### Why would I want to stake my ETH? <a id="why-would-i-want-to-stake-my-eth"></a>

For staking your ETH and attesting to correct blocks, you will be rewarded with additional ETH through a network wide interest rate as well as receive a portion of network transaction fees. Details can be found [here](https://docs.ethhub.io/ethereum-roadmap/ethereum-2.0/eth-2.0-economics).

#### What are the minimum requirements to stake? <a id="what-are-the-minimum-requirements-to-stake"></a>

* A minimum of 32 ETH per validator
* Computer with sufficient hardware specs
* Internet connection

#### What software do I need to run to stake? <a id="what-software-do-i-need-to-run-to-stake"></a>

There are two main types of software to be aware of when considering staking on Ethereum:

* Beacon nodes: This is the hub for your validators.
* Stores canonical state, handles peers and incoming sync, propagates blocks and attestations.
* Has a gRPC server that clients can connect to and provides a public API.
* Validator clients: Talks to your beacon node and signs blocks. You can have multiple of these at 32 ETH each.
* Stores important secrets such as RANDAO reveal, proof of custody for shared data, and BLS private key.
* Can swap underlying beacon nodes efficiently.
* Tracks shared state execution data and data blobs that the validator has signed.

This means that there are three possible combinations of software to run:

1. Beacon node only
2. Beacon node + validator client
3. Beacon node + multiple validator clients

#### What are the hardware requirements to run this software? <a id="what-are-the-hardware-requirements-to-run-this-software"></a>

Still TBD. Ideally we can get minimum requirements for all three setups mentioned above.

#### What happens if I lose my internet connection while staking? <a id="what-happens-if-i-lose-my-internet-connection-while-staking"></a>

The key to being a validator is to ensure that you are consistently available to vote for blocks which in turn secures the network. Therefore, there is a slight penalty if your validator client goes offline at any point, in order to encourage validator availability. There are two scenarios where this can happen:

1. If blocks are finalizing and you're offline, you can lose x% of your deposit over a year where x=current\_interest
2. For example, if the current interest rate is 5%, you would lose 0.0137% of your deposit every day, but gain that for every day you're online.
3. If blocks aren't finalizing \(&gt;33% of validators are offline\) and you're offline, you can lose 60% in 18 days.

If at any point your deposit drops below 16 ETH you will be removed from the validator set entirely.

#### How long is my Ether locked up if I stake? <a id="how-long-is-my-ether-locked-up-if-i-stake"></a>

There is a withdraw queue that you are placed into when wanting to withdraw ETH from your validator. If there is no queue, then the minimum withdraw time is 18 hours and adjusts dynamically depending on how many people are withdrawing at that time.

## 延伸阅读

[PoS F](https://github.com/ethereum/wiki/wiki/Proof-of-Stake-FAQ)[AQs](https://github.com/ethereum/wiki/wiki/Proof-of-Stake-FAQ).


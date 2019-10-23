# 分片研究概况

分片技术目前仍处于研究阶段，下面的内容可能不时进行增删、更新。

参考来源：[https://notes.ethereum.org/@serenity/H1PGqDhpm?type=view](https://notes.ethereum.org/@serenity/H1PGqDhpm?type=view)

## 分片基本信息

* \*\*\*\*[**分片路线图**](https://github.com/ethereum/wiki/wiki/Sharding-roadmap)\*\*\*\*
* \*\*\*\*[**设计理念**](https://notes.ethereum.org/9l707paQQEeI-GPzVK02lA)\*\*\*\*
* \*\*\*\*[**分片思维导图**](https://www.mindomo.com/zh/mindmap/sharding-d7cf8b6dee714d01a77388cb5d9d2a01)\*\*\*\*
* \*\*\*\*[**Beacon chain Casper FFG 简要规范**](https://ethresear.ch/t/beacon-chain-casper-ffg-rpj-mini-spec/2760)\*\*\*\*
* \*\*\*\*[**Beacon chain 详细规范**](https://github.com/ethereum/eth2.0-specs/tree/master/specs/core)\*\*\*\*
* \*\*\*\*[**Reddit 讨论板块**](https://ethresear.ch/c/sharding)\*\*\*\*
* \*\*\*\*[**分片常见问题解答**](https://github.com/ethereum/wiki/wiki/Sharding-FAQ)\*\*\*\*

## Proof of Stake 算法理论

* \*\*\*\*[**PoS 常见问题解答**](https://github.com/ethereum/wiki/wiki/Proof-of-Stake-FAQ)\*\*\*\*
* \*\*\*\*[**Casper FFG 论文**](https://arxiv.org/abs/1710.09437)\*\*\*\*
* \*\*\*\*[**基于认证委员会的完全PoS链**](https://ethresear.ch/t/attestation-committee-based-full-pos-chains/2259)\*\*\*\*

## Casper FFG/GHOST/信标链模拟

* \*\*\*\*[**主文件夹**](https://github.com/ethereum/research/tree/master/clock_disparity)\*\*\*\*
  * Node: `lmd_node.py`
  * Test script: `lmd_test.py`

## Casper CBC

* \*\*\*\*[**CBC Casper 教程**](https://vitalik.ca/general/2018/12/05/cbc_casper.html)\*\*\*\*
* \*\*\*\*[**简单解释 CBC Casper**](https://medium.com/@aditya.asgaonkar/casper-cbc-simplified-2370922f9aa6)\*\*\*\*
* \*\*\*\*[**信标链友好的 CBC Casper**](https://ethresear.ch/t/beacon-chain-friendly-cbc-casper/4710/2)\*\*\*\*
* \*\*\*\*[**Bitwise LMD GHOST**](https://ethresear.ch/t/bitwise-lmd-ghost/4749/5)\*\*\*\*
* \*\*\*\*[**LMD GHOST** ](https://ethresear.ch/t/comparing-lmd-ghost-implementations/4945/3)

#### 验证者集轮替 <a id="Validator-set-rotation"></a>

* \*\*\*\*[**限速进入/退出而非立即撤出**](https://ethresear.ch/t/rate-limiting-entry-exits-not-withdrawals/4942/)\*\*\*\*

#### 轻客户端 <a id="Light-clients"></a>

* \*\*\*\*[**信标链轻客户端同步**](https://notes.ethereum.org/Irbhsn63R0W6o-r0K9mBOA)\*\*\*\*
* \*\*\*\*[**Casper FFG 轻客户端同步**](https://github.com/ethereum/eth2.0-specs/blob/dev/specs/light_client/sync_protocol.md)\*\*\*\*

#### 签名集合 <a id="Signature-aggregation"></a>

* \*\*\*\*[**BLS 和 STARK 集合签名**](https://ethresear.ch/t/pragmatic-signature-aggregation-with-bls/2105)\*\*\*\*

#### 无状态区块验证 <a id="Stateless-block-verification"></a>

* \*\*\*\*[**什么是无状态客户端**](%20https://ethresear.ch/t/the-stateless-client-concept/172)\*\*\*\*
* **分批处理和多状态根的效率提升**[ **\[1\]**](%20https://ethresear.ch/t/detailed-analysis-of-stateless-client-witness-size-and-gains-from-batching-and-multi-state-roots/862/9)\*\*\*\*[ **\[2\]**](https://ethresear.ch/t/multi-tries-vs-partial-statelessness/391)\*\*\*\*
* **累加器** [**\[1\]**](https://ethresear.ch/t/history-state-and-asynchronous-accumulators-in-the-stateless-model/287) ****[**\[2\]**](%20https://ethresear.ch/t/batching-and-cyclic-partitioning-of-logs/536) ****[**\[3\]**](https://ethresear.ch/t/double-batched-merkle-log-accumulator/571)
* \*\*\*\*[**状态最小化执行**](https://ethresear.ch/t/state-minimised-executions/748)\*\*\*\*
* \*\*\*\*[**面向状态最小合约的加密经济累加器**](%20https://ethresear.ch/t/a-cryptoeconomic-accumulator-for-state-minimised-contracts/385)\*\*\*\*

#### 跨分片交流 <a id="Cross-shard-communication"></a>

* \*\*\*\*[**区块合并以及同步跨分片**](https://ethresear.ch/t/merge-blocks-and-synchronous-cross-shard-state-execution/1240)
* **跨分片锁定** [**\[1\]**](https://ethresear.ch/t/cross-shard-locking-scheme-1/1269)\*\*\*\*[ **\[2\]**](https://ethresear.ch/t/cross-shard-locking-resolving-deadlock/1275)\*\*\*\*[ **\[3\]**](https://ethresear.ch/t/sharded-byzantine-atomic-commit/1285) ****
* \*\*\*\*[**跨分片拉取操作 \(Yanking\)**](https://ethresear.ch/t/cross-shard-contract-yanking/1450)
* \*\*\*\*[**简单的同步跨分片交易协议**](https://ethresear.ch/t/simple-synchronous-cross-shard-transaction-protocol/3097)\*\*\*\*
* \*\*\*\*[**跨分片收据及休眠/唤醒反双花机制**](%20https://ethresear.ch/t/cross-shard-receipt-and-hibernation-waking-anti-double-spending/4748)\*\*\*\*
* **如何在基础层建立快速跨分片通信**[ **\[1\]**](https://ethresear.ch/t/a-layer-2-computing-model-using-optimistic-state-roots/4481) ****[**\[2\]**](https://ethresear.ch/t/fast-cross-shard-transfers-via-optimistic-receipt-roots/5337) 

#### 存储费用/租金 <a id="Storage-maintenace-fees--Rent"></a>

* \*\*\*\*[**计算租金的简单原则**](https://ethresear.ch/t/a-simple-and-principled-way-to-compute-rent-fees/1455)\*\*\*\*
* \*\*\*\*[**通过休眠/唤醒机制提升用户体验**](https://ethresear.ch/t/improving-the-ux-of-rent-with-a-sleeping-waking-mechanism/1480)\*\*\*\*
* \*\*\*\*[**Actor 和资产模型**](%20https://ethresear.ch/t/ethereum-2-0-data-model-actors-and-assets/4117)\*\*\*\*
* \*\*\*\*[**常见合约种类及其存储租金**](https://ethresear.ch/t/common-classes-of-contracts-and-how-they-would-handle-ongoing-storage-maintenance-fees-rent/4441)

#### 托管证明 P**roofs of custody** <a id="Proofs-of-custody"></a>

* \*\*\*\*[**可用性陷阱**](https://ethresear.ch/t/proposer-withholding-and-collation-availability-traps/1294)\*\*\*\*
* **基于哈希算法的托管证明** [**\[1\]**](https://ethresear.ch/t/extending-skin-in-the-game-of-notarization-with-proofs-of-custody/1639) ****[**\[2\]**](%20https://ethresear.ch/t/bitwise-xor-custody-scheme/5139)\*\*\*\*
* \*\*\*\*[**易于聚合的1-bit托管bond** ](https://ethresear.ch/t/1-bit-aggregation-friendly-custody-bonds/2236)\*\*\*\*
* \*\*\*\*[**目前的机制**](https://github.com/ethereum/eth2.0-specs/blob/dev/specs/core/1_custody-game.md)\*\*\*\*

#### 数据可用性证明 <a id="Data-availability-proofs"></a>

* \*\*\*\*[**欺诈证明和数据可用性证明**](%20https://arxiv.org/abs/1809.09044)\*\*\*\*

#### 随机性 <a id="Randomness"></a>

* \*\*\*\*[**30% 分片攻击**](https://ethresear.ch/t/30-sharding-attack/1340)\*\*\*\*
* \*\*\*\*[**不可操作的熵**](%20https://ethresear.ch/t/an-impractical-idea-for-unmanipulable-entropy/1355)\*\*\*\*
* \*\*\*\*[**RNG \(随机数生成器\) 可利用性分析 \(RANDAO\)**](https://ethresear.ch/t/rng-exploitability-analysis-assuming-pure-randao-based-main-chain/1825)\*\*\*\*
* \*\*\*\*[**RANDAO 可利用性分析之二**](https://ethresear.ch/t/randao-beacon-exploitability-analysis-round-2/1980)\*\*\*\*
* \*\*\*\*[**使用低影响函数 \(Iddo Bentov\)**](%20https://arxiv.org/pdf/1406.5694.pdf)\*\*\*\*
* \*\*\*\*[**Shuffle\(打乱验证者\) 方法：Swap-or-not**](https://github.com/ethereum/eth2.0-specs/issues/563)\*\*\*\*

#### 时间戳 <a id="Timestamps"></a>

* \*\*\*\*[**时间戳的激励性问题**](https://ethresear.ch/t/highlighting-a-problem-stability-of-the-equilibrium-of-minimum-timestamp-enforcement/2257)\*\*\*\*
* \*\*\*\*[**根据网络调整的时间戳**](https://ethresear.ch/t/network-adjusted-timestamps/4187)\*\*\*\*

#### 数据结构 <a id="Data-structures"></a>

* \*\*\*\*[**优化稀疏默克尔树**](https://ethresear.ch/t/optimizing-sparse-merkle-trees/3751)\*\*\*\*
* \*\*\*\*[**稀疏默克尔树的执行**](https://github.com/ethereum/research/tree/master/trie_research/bintrie2)\*\*\*\*
* \*\*\*\*[**双层默克尔记录累加器**](https://ethresear.ch/t/double-batched-merkle-log-accumulator/571)\*\*\*\*
* \*\*\*\*[**高效默克尔证明和广义 SSZ 轻客户端证明**](https://github.com/ethereum/eth2.0-specs/blob/dev/specs/light_client/merkle_proofs.md)\*\*\*\*

#### 杂项 <a id="Miscellaneous"></a>

* \*\*\*\*[**分片的缺陷/可能面临的作弊和攻击问题**](http://notes.ethereum.org/MwNgJgpgHFbAtAQwKwQvALNA7PW2p4AzAYwCYAjARgrGxIE4jgg=)\*\*\*\*
* \*\*\*\*[**分片数据遗忘可能性**](https://ethresear.ch/t/sharding-and-data-forgetfulness/61)\*\*\*\*
* [**如何提高安全性以抵御参与者腐败**](https://ethresear.ch/t/shard-security-in-the-bribing-model/1366)\*\*\*\*
* \*\*\*\*[**更优的默克尔树**](%20https://ethresear.ch/t/data-availability-proof-friendly-state-tree-transitions/1453/6)\*\*\*\*

\*\*\*\*

{% embed url="https://notes.ethereum.org/@serenity/H1PGqDhpm?type=view" %}




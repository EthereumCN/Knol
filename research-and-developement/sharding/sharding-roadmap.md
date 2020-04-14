# 路线图

* [以太坊 2.0](https://github.com/ethereum/wiki/wiki/Sharding-roadmap#ethereum-20)
  * 阶段 0：实行PoS的信标链（无分片）
  * 阶段 1：实施基础分片（无 EVM）
  * 阶段 2：EVM 状态转换函数
  * 阶段 3：轻客户端状态协议
  * 阶段 4：跨分片交易
  * 阶段 5：与主链安全性紧密耦合
  * 超二次分片或指数分片
* [以太坊 3.0](https://github.com/ethereum/wiki/wiki/Sharding-roadmap#ethereum-30)

## 以太坊 2.0

### **阶段 0**

实行PoS的信标链（无分片）

* 在 PoS 信标链中使用 Casper FFG 以确保最终确定性
* 验证者可在区块提议中通过 RANDAO 创建 RNG（随机数生成器）
* 验证者根据 RNG 的输出值组织成提议者和验证委员会
* 验证者为完成记录的分片创建交叉链接
* [阶段 0 信标链详细规范](https://github.com/ethereum/eth2.0-specs/blob/master/specs/core/0_beacon-chain.md)

### 阶段 1

实施基础分片（无 EVM）

* Blobs（二进制大型对象）在无交易的分片中进行整理
* 提议者提交 blobs
* 公证人
* 更多细节请参看[阶段1规范](https://github.com/ethereum/eth2.0-specs/blob/master/specs/core/1_shard-data-chains.md)以及[执行](https://github.com/ethereum/wiki/wiki/Sharding-introduction-R&D-compendium#implementations)

### 阶段 2

EVM 状态转换函数

* 仅支持全节点
* 仅支持异步跨合约调用
* 账户抽象
* [eWASM](https://github.com/ewasm/design)；eWASM 团队目前正在进行以太坊2.0集成
* 待确认：归档累加器[ \[1\]](https://ethresear.ch/t/history-state-and-asynchronous-accumulators-in-the-stateless-model/287) [\[2\]](https://ethresear.ch/t/batching-and-cyclic-partitioning-of-logs/536) [\[3\]](https://ethresear.ch/t/double-batched-merkle-log-accumulator/571)
* 存储租金 [\[1\]](https://ethresear.ch/t/a-simple-and-principled-way-to-compute-rent-fees/1455) [\[2\]](https://ethresear.ch/search?q=storage%20rent)；[带宽费用](https://ethresear.ch/t/incentivizing-a-robust-p2p-network-relay-layer/1438)；以及其他在改善用户界面的同时将被成本内部化的机制设计

### 阶段 3

轻客户端状态协议

* 执行器
* [状态最小化客户端](https://ethresear.ch/t/state-minimised-executions/748)。[无状态客户端](https://ethresear.ch/t/the-stateless-client-concept/172)不是理想的选择，相比将所有存储都转移到二级市场，让用户可以选择在区块链上支付存储租金还是在二级市场上支付更妥当。

### 阶段 4

跨分片交易 [\[1\]](https://notes.ethereum.org/@serenity/H1PGqDhpm?type=view) [\[2\]](https://ethresear.ch/search?q=cross-shard)

* 内部同步区：[包含架构的思维导图](https://www.mindomo.com/zh/mindmap/sharding-d7cf8b6dee714d01a77388cb5d9d2a01)
* [更多跨分片交易讨论](https://ethresear.ch/t/synchronous-cross-shard-transactions-with-consolidated-concurrency-control-and-consensus-or-how-i-rediscovered-chain-fibers/2318/5)

### 阶段 5

与主链安全性紧密耦合 [\[1\]](https://notes.ethereum.org/@serenity/rkDgPLqRm?type=view) [\[2\]](https://ethresear.ch/search?q=tight%20coupling)

* 数据可用性证明：[数据可用性和纠删编码](https://github.com/ethereum/research/wiki/A-note-on-data-availability-and-erasure-coding)；[分片与数据](https://ethresear.ch/t/sharding-and-data-forgetfulness/61)
* [分片内部无分叉](https://ethresear.ch/search?q=internally%20fork-free)
* 管理者分片：对于最新的规范较为困难，因为其使用的是信标链而非合约

### 阶段 6

超二次分片或指数分片

* 分片递归地包含分片，同上，对于最新规范来说这可能很困难
* [负载平衡](https://en.wikipedia.org/wiki/Load_balancing_%28computing%29)

更多最新关于分片的讨论参见：[https://ethresear.ch/c/sharding](https://ethresear.ch/c/sharding).

## 以太坊 3.0

* 整合 [Casper CBC](https://github.com/ethereum/wiki/wiki/Casper-Proof-of-Stake-compendium)
* 零知识证明 \(zk-STARKS\)：视频介绍 [\[1\]](https://www.youtube.com/watch?v=VUN35BC11Qw&t=2s) [\[2\]](https://www.youtube.com/watch?v=9VuZvdxFZQo&t=7s)[ \[3\]](https://www.youtube.com/watch?v=9VuZvdxFZQo&t=7s)；[论文](https://eprint.iacr.org/2018/046)
* [异构分片](https://ethresear.ch/t/heterogeneous-sharding/1979)


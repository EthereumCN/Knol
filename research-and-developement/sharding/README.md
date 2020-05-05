---
description: Sharding + Casper / shasper 是以太坊2.0计划中的扩容性解决方案，研发工作正在进行当中，目前阶段0的信标链规范已经完成冻结。
---

# Sharding 分片

目前以太坊1.0每秒只能处理7-15笔交易，Sharding 的目的是将所有网络计算资源划分为分片，这样一来节点（一台计算机设备连接到网络作为点对点节点）就不需要必须处理（包括下载、计算、存储、读取）区块链历史中的每笔交易，来进行新的交易（写入和上传）或参与维护和使用以太坊。在分片机制中，一个节点可以根据选择只参与一个或多个分片中。多个分片由不同的可靠参与者子集独立运行，又称为安全检查者（包括公证人、提议者、矿工和验证者）。其主要目标是对可扩展性进行大规模改进，分片在路线图的第6阶段可能呈指数级增长，尽管 Vitalik 曾在 ethresear.ch 上的讨论中质疑指数分片是否还能成立。二次分片只包含距离主链最多1个深度的分片，也就是说，分片内不存在分片，也没有管理子分片的管理者分片，而指数分片使得分片递归地包含在其他分片中。‌

每个分片（最新规范中设置为64个分片）将具有比当前现有的以太坊主链更大的容量。最新规范使用 RANDAO 信标链将分片与 Casper FFG PoS 结合在一起。

获取更多的分片相关信息，请继续阅读：

* [已经冻结的信标链规范](https://github.com/ethereum/eth2.0-specs/blob/dev/specs/core/0_beacon-chain.md)
* [分片常见问题解答](https://github.com/ethereum/wiki/wiki/Sharding-FAQ)
* [分片路线图](https://github.com/ethereum/wiki/wiki/Sharding-roadmap)
* [关于分片的总结性概括](https://github.com/ethereum/wiki/wiki/Sharding-roadmap)
* [分片在Reddit上的最新讨论](https://ethresear.ch/c/sharding)
* [以太坊研发者对分片的介绍](https://docs.google.com/presentation/d/1mdmmgQlRFUvznq1jdmRwkwEyQB0YON5yAg4ArxtanE4/edit#slide=id.p4)
* [ethresear.ch上的分片研究汇编](https://github.com/ethereum/wiki/wiki/Wiki:-ethresear.ch-Sharding-Compendium)
* [分片研究汇编](https://notes.ethereum.org/@serenity/H1PGqDhpm?type=view)




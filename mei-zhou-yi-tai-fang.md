---
description: 2019.10.13
---

# 每周以太坊

### Eth2 <a id="Eth2"></a>

* Eth2 的战术转变：[更少的分片数量（64？）但有更快的跨分片交互](https://notes.ethereum.org/@vbuterin/HkiULaluS)，单片吞吐量提升 8 倍
* [Lighthouse 客户端升级](https://lighthouse.sigmaprime.io/update-16.html) —— 仍在开发组网工具
* [Devcon5 上讲解 phase0 的幻灯片](https://docs.google.com/presentation/d/1MZ-E6TVwomt4rqz-P2Bd_X3DFUW9fWDQkxUP_QJhkyw/edit?pli=1#slide=id.g621e2d5823_0_205)
* 上周 Vitalik 决定把一些（如果你经常读本周报）你可能已经知道的观念写下来，不过显然 Coindesk 的记者并不知道：
  * [eth1 到 eth2 的迁移计划](https://ethresear.ch/t/the-eth1-eth2-transition/6265)
  * [形式化 eth2 处理无效分片区块确定性的方法](https://ethresear.ch/t/formalizing-and-improving-eth2s-approach-toward-finalization-of-invalid-shard-blocks/6263)
  * [分片不会打破可组合性](https://ethresear.ch/t/cross-shard-defi-composability/6268)
  * [部分分片的应用设计模式](https://ethresear.ch/t/partially-sharding-single-threaded-apps-a-design-pattern/6287)
  * [用分片链来保存合约](https://ethresear.ch/t/on-beacon-chain-saved-contracts/6295)。虽然昂贵，但可以做到
  * [为什么说 eth1 和 eth2 的双向链桥很难](https://ethresear.ch/t/two-way-bridges-between-eth1-and-eth2/6286)，但因为可以实现，也算一个选择

### Layer-2 <a id="Layer-2"></a>

* Plasma Group 和 Uniswap [在 Devcon 上展示了一个 Optimistic Rollup 的 demo](https://decrypt.co/10030/plasma-group-and-uniswap-release-new-ethereum-scaling-solution-at-devcon)，TPS 可上百
* Celer 放出了其[状态通道的技术详述](https://www.celer.network/docs/celercore/)
* [跨 zk-Rollup 转账协议](https://ethresear.ch/t/trustless-and-secure-cross-zk-rollup-transfer-protocol/6255/2)

### 开发者材料 <a id="&#x5F00;&#x53D1;&#x8005;&#x6750;&#x6599;"></a>

* Metamask：[web3 插件](https://medium.com/metamask/introducing-the-next-evolution-of-the-web3-wallet-4abdf801a4ee)。加载在 ENS、IPFS 或者 Swarm 上的经过验证的脚本
* [ZoKrates v0.5](https://github.com/Zokrates/ZoKrates/releases/tag/0.5.0)
* [OpenZKP](https://blog.0xproject.com/introducing-openzkp-1dea6b22dceb)：0x 的 STARK 开源 Rust 实现
* [使用 MythX 来检查自定义的合理性属性](https://medium.com/consensys-diligence/checking-custom-correctness-properties-of-smart-contracts-using-mythx-25cbac5d7852)
* Kovan 测试网上[成功激活 Istanbul 硬分叉](https://twitter.com/kovantestnet/status/1183326400784158721)
* [Mutant 测试工具](https://medium.com/swlh/introduction-into-mutation-testing-d6512dc702b0)
* [将 Eth 的数据实时加载到 BigQuery 和 Pub/sub 上](https://medium.com/google-cloud/live-ethereum-and-bitcoin-data-in-google-bigquery-and-pub-sub-765b71cd57b5)
* [如何查询 ENS 和 0x 事件](https://medium.com/@medvedev1088/query-ens-and-0x-events-with-sql-in-google-bigquery-4d197206e644)
* [Remix 桌面 IDE](https://medium.com/remix-ide/remix-desktop-8c1e9e946ee1)

### 生态 <a id="&#x751F;&#x6001;"></a>

* [Devcon 的全部演讲视频](https://slideslive.com/ethereum)（配合 [议程表](https://devcon.org/agenda) 使用更佳）
* Unstoppable Domains [在以太坊上开发 .crypto 域名](https://medium.com/unstoppabledomain/crypto-ac3eec150768)
* [EthDNS](https://medium.com/the-ethereum-name-service/ethdns-9d56298fa38a)：ENS+IPFS
* [在 ARM 上部署以太坊节点](https://www.reddit.com/r/ethereum/comments/dehcq9/ethereum_on_arm_nanopct4_rockpro64_and_raspberry/)。给 NanoPC-T4，Rockpro64 和树莓派准备的新镜像。除了用 Geth/Parity/Trinity 运行一个全节点，还可以加上 Status.im、Raiden、IPFS、Swarm 和 Vipnode。或者用 Prysmatic 搭建一个 eth2 测试网
* 多抵押 DAI 计划在 [11 月 18 号上线](https://blog.makerdao.com/breaking-launch-date-of-multi-collateral-dai-announced-at-devcon-5/)
* [Compound 对多抵押 DAI 的计划](https://medium.com/compound-finance/support-for-multi-collateral-dai-c8691d0ef794)。用户有责任迁移自己的 DAI
* SetProtocol 的[再平衡面板](https://medium.com/set-protocol/introducing-the-rebalancing-dashboard-9130e31435d9)，现在你也可以参与再平衡过程了
* Alethion 的 devcon 前一周 [DeFi/dex 数据可视化](https://twitter.com/AlethioEthstats/status/1181055545987366912)
* Brave 已有 [800 万月活用户](https://twitter.com/BrendanEich/status/1181370032032321536)



**原文链接:** [https://weekinethereumnews.com/](https://weekinethereumnews.com/)\*\*\*\*

**作者:** Evan Van Ness  
**翻译:** 阿剑

**译文转载自**[**Ethfans**](https://ethfans.org)**，ECN二次查校**

  



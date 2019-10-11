# 每周以太坊

### Eth1 <a id="Eth1"></a>

* Nethermind [v1.1.0](https://github.com/NethermindEth/nethermind/releases/tag/1.1.0)：数据库仅约 50 GB
* Geth [v1.9.6](https://www.reddit.com/r/ethereum/comments/dcon81/geth_v196_elasa_light_client_fixes_and_huge_disk/) 轻客户端提升了同步速度，并将硬盘读写需求降低了 10 倍
* Eth 测试工具 [v7.0.0-beta.1](https://github.com/ethereum/tests/releases/tag/v7.0.0-beta.1) 包含了伊斯坦布尔升级
* 最新的[全体核心开发者会议](https://www.youtube.com/watch?v=rPD2EpDDI-0)。Pooja 的[备忘录](https://github.com/ethereum/pm/blob/eca93a683edd7f8384af117d85fb7c23c7e8091b/All%20Core%20Devs%20Meetings/Meeting%2072.md)。如果你好奇 Ropsten 测试网的伊斯坦布尔混乱的原因，很简单，有人在大力挖矿，但没有支持伊斯坦布尔分叉
* [提议 CALLWITHSCHEDULE 操作码](https://medium.com/@earlz/a-simple-ethereum-proposal-e19efd9c3f72)来解决合约的可重入漏洞
* [伊斯坦布尔分叉提高了数据存储的 Gas 用量](https://medium.com/@agusx1211/evm-istambul-storage-pricing-5befaac32403)。[Reddit 上还有有趣的讨论](https://www.reddit.com/r/ethereum/comments/dbxij1/evm_istambul_storage_pricing_or_how_to_hack_the/)

### Eth2 <a id="Eth2"></a>

* [What’s New in Eth2](https://notes.ethereum.org/@ChihChengLiang/Sk8Zs--CQ/Sk8Zs--CQ?type=book)，包含了 Ben 对 Phase 1 和 Phase 2 都会在 2020 年上线的乐观想法
* Ryuya 提议 [FMD GHOST](https://ethresear.ch/t/saving-strategy-and-fmd-ghost/6226)
* 委员会随机选举的[安全算术](https://ethresear.ch/t/security-level-of-random-sampling-with-sharding/6230?u=benjaminion)
* [Eth2 的开发者体验](https://thebitcoinpodcast.com/hio-panels-1/)（一个来自 Nimbus、Lighthouse、Prysmatic、EF、ConsenSys 工程师的播客）
* [使用这个命令行运行 5 种客户端测试网](https://github.com/status-im/nim-beacon-chain/tree/interop/multinet)
* [Prysmatic](https://medium.com/prysmatic-labs/ethereum-2-0-development-update-36-prysmatic-labs-3ea492024c4d) 客户端升级、重新启动测试网，大量的 BLS 升级

### Layer-2 <a id="Layer-2"></a>

* Counterfactual 和 Magmo [合并了他们的广义状态通道代码库](https://medium.com/statechannels/state-channels-developer-update-0-counterfactual-and-magmo-are-joining-forces-4acfabf3fc98)，并代之以 State Channels 的新名字
* [Hodor](https://medium.com/matter-labs/meet-hodor-matter-labsstark-prover-implementation-c759a6ef4c98)，Matter Lab 的开放式 STARK 证明器实现
* [Matic 测试版](https://blog.matic.network/matic-beta-mainnet-is-here/)包括了 Plasma predicates

### 开发者材料 <a id="&#x5F00;&#x53D1;&#x8005;&#x6750;&#x6599;"></a>

* Solidity [0.5.12](https://github.com/ethereum/solidity/releases/tag/v0.5.12) 支持 SMT solver 的循环，还有 Yul 优化实现
* 使用 Buidler EVM 的[栈层跟踪来完成调试](https://medium.com/nomic-labs-blog/better-solidity-debugging-stack-traces-are-finally-here-dd80a56f92bb)
* [Remix 的 ZoKrates 插件](https://medium.com/@edi.sinovcic/zokrates-zksnarks-on-ethereum-made-easy-8022300f8ba6)
* 3Box 的 [IdentityWallet SDK](https://medium.com/3box/introducing-identitywallet-sdk-4750d6afa519)
* [Burner Wallet 2](https://medium.com/@dmihal/introducing-the-burner-wallet-2-542604dc8d28)：模块化了开发 burner 钱包的组件
* [如何避免给你的 Gas Station Network app 中的恶意用户发放补贴](https://forum.openzeppelin.com/t/advanced-gsn-gsnbouncersignature-sol/1414)
* [Mexa](https://medium.com/biconomy/mexa-magical-experiences-anytime-anywhere-7a9c0c364221)：元交易 SDK
* [Hookpad](https://medium.com/hookpad/hookpad-beta-is-here-6e8a63713219)：监听 EVM 链，并且过滤出你需要的事件
* Brownie python 框架：[通过操作码跟踪来评估 Solidity 的代码覆盖率](https://medium.com/coinmonks/brownie-evaluating-solidity-code-coverage-via-opcode-tracing-a7cf5a92d28c)

### 生态 <a id="&#x751F;&#x6001;"></a>

* ENS 短域名拍卖[遇上了一些 OpenSea 的 bug](https://medium.com/opensea/how-were-resolving-the-issues-with-the-ens-short-name-auctions-93c78158de48)，不过一些被盗的域名已经通过奖金计划还回来了。因为拍卖现已暂停，一些域名的拍卖会延期。ENS 也为域名加入了[文字记录](https://medium.com/the-ethereum-name-service/new-text-records-now-available-for-ens-names-in-manager-a0ebb9cda73a)
* 基准测试表明 [GPU 矿工可以一边在 Livepeer 上转码](https://medium.com/livepeer-blog/livepeer-gpu-miner-update-663a3e19de56)、一边挖矿还保持同等算力，甚至降低能耗
* 以太坊上的 Mimblewimble：[使用 zk-SNARKs 隐秘地发送 ERC20 代币](https://ethresear.ch/t/ethereum-9-send-erc20-privately-using-mimblewimble-and-zk-snarks/6217)
* [一个找寻 Uniswap 最佳流动性池的工具](https://pools.fyi/#/)
* UMA 的论文 [BitDEX](https://twitter.com/UMAprotocol/status/1179045704918011906)：一个去中心化的 Bitmex



**原文链接:** [https://weekinethereumnews.com/](https://weekinethereumnews.com/)\*\*\*\*

**作者:** Evan Van Ness  
**翻译:** 阿剑

**译文转载自**[**Ethfans**](https://ethfans.org)**，ECN二次查校**

  



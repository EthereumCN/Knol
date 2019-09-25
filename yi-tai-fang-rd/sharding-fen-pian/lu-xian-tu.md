# 路线图

The roadmap is an active area of research. The outline below is only intended to provide flavour, with more details in specifications \(which have been released for phases 0 and 1 as of 14 Dec 2018, as linked to below\). It is intended for this document to be maintained with ongoing R&D.

**Contents**

* [Ethereum 2.0](https://github.com/ethereum/wiki/wiki/Sharding-roadmap#ethereum-20)
  * [**Phase 0**: PoS beacon chain without shards](https://github.com/ethereum/wiki/wiki/Sharding-roadmap#strongphase-0strong-pos-beacon-chain-without-shards)
  * [**Phase 1**: Basic sharding without EVM](https://github.com/ethereum/wiki/wiki/Sharding-roadmap#strongphase-1strong-basic-sharding-without-evm)
  * [**Phase 2**: EVM state transition function](https://github.com/ethereum/wiki/wiki/Sharding-roadmap#strongphase-2strong-evm-state-transition-function)
  * [**Phase 3**: Light client state protocol](https://github.com/ethereum/wiki/wiki/Sharding-roadmap#strongphase-3strong-light-client-state-protocol)
  * [**Phase 4**: Cross-shard transactions: see here and more.](https://github.com/ethereum/wiki/wiki/Sharding-roadmap#strongphase-4strong-cross-shard-transactions-see-here-and-more)
  * [**Phase 5**: Tight coupling with main chain security: here and more.](https://github.com/ethereum/wiki/wiki/Sharding-roadmap#strongphase-5strong-tight-coupling-with-main-chain-security-here-and-more)
  * [**Phase 6**: Super-quadratic or exponential sharding](https://github.com/ethereum/wiki/wiki/Sharding-roadmap#strongphase-6strong-super-quadratic-or-exponential-sharding)
* [Ethereum 3.0](https://github.com/ethereum/wiki/wiki/Sharding-roadmap#ethereum-30)

### Ethereum 2.0

#### **Phase 0**: PoS beacon chain without shards

* PoS beacon chain using Casper FFG for finality
* Validators create an RNG via RANDAO in block proposals
* Validators organize into proposers and attestation committees from the output of the RNG
* Validators create crosslinks for stubbed shards
* See the [phase 0 spec](https://github.com/ethereum/eth2.0-specs/blob/master/specs/core/0_beacon-chain.md) FMI.

#### **Phase 1**: Basic sharding without EVM

* Blobs \(Binary Large Objects\) are collated in shards without transactions \(which require execution\)
* Proposers submit blobs
* Notaries
* For more details, see the [phase 1 spec](https://github.com/ethereum/eth2.0-specs/blob/master/specs/core/1_shard-data-chains.md) and [implementations](https://github.com/ethereum/wiki/wiki/Sharding-introduction-R&D-compendium#implementations).

#### **Phase 2**: EVM state transition function

* Full nodes only
* Asynchronous cross-contract calls only
* [Account abstraction](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-101.md)
* [eWASM](https://github.com/ewasm/design); the eWASM team is working on Ethereum 2.0 integration after being developed \(although not integrated in production yet?\) with 1.0 clients.
* TBC: archive accumulators: [History, state, and asynchronous accumulators in the stateless model](https://ethresear.ch/t/history-state-and-asynchronous-accumulators-in-the-stateless-model/287) and [Batching and cyclic partitioning of logs](https://ethresear.ch/t/batching-and-cyclic-partitioning-of-logs/536) and [Double-batched Merkle log accumulator](https://ethresear.ch/t/double-batched-merkle-log-accumulator/571)
* Storage rent: [here](https://ethresear.ch/t/a-simple-and-principled-way-to-compute-rent-fees/1455) and [here](https://ethresear.ch/search?q=storage%20rent); [bandwidth fees](https://ethresear.ch/t/incentivizing-a-robust-p2p-network-relay-layer/1438); and other mechanism design to internalize costs while improving UI.

#### **Phase 3**: Light client state protocol

* Executors
* [State-minimized clients](https://ethresear.ch/t/state-minimised-executions/748). [Stateless clients](https://ethresear.ch/t/the-stateless-client-concept/172) are not ideal as we don't want to offload all storage into secondary markets, rather we can give people a choice to pay storage rent on the blockchain or pay for it in secondary markets.

#### **Phase 4**: Cross-shard transactions: see [here](http://notes.ethereum.org/s/BJc_eGVFM#cross-shard-communication) and [more](https://ethresear.ch/search?q=cross-shard).

* Internally-synchronous zones: [mind map including architectures](https://www.mindomo.com/zh/mindmap/sharding-d7cf8b6dee714d01a77388cb5d9d2a01)
* See also [here](https://ethresear.ch/t/synchronous-cross-shard-transactions-with-consolidated-concurrency-control-and-consensus-or-how-i-rediscovered-chain-fibers/2318/5)

#### **Phase 5**: Tight coupling with main chain security: [here](https://hackmd.io/s/HJ_BbgCFz#%E2%9F%A0-1600---1645--Ethereum-20-End-game) and [more](https://ethresear.ch/search?q=tight%20coupling).

* Data availability proofs: [A note on data availability and erasure coding](https://github.com/ethereum/research/wiki/A-note-on-data-availability-and-erasure-coding), [Sharding and data forgetfulness](https://ethresear.ch/t/sharding-and-data-forgetfulness/61)
* [Internally fork-free sharding](https://ethresear.ch/search?q=internally%20fork-free)
* Manager shard: may be difficult with the latest spec as it uses a beacon chain rather than a contract.

#### **Phase 6**: Super-quadratic or exponential sharding

* Recursively, shards within shards within shards... Again, this may be difficult with the latest spec as it uses a beacon chain rather than a contract.
* Load balancing: [Wikipedia](https://en.wikipedia.org/wiki/Load_balancing_%28computing%29), [search results](https://duckduckgo.com/?q=load+balancing&t=canonical&ia=web). Related: [History, state, and asynchronous accumulators in the stateless model](https://ethresear.ch/t/history-state-and-asynchronous-accumulators-in-the-stateless-model/287), [State minimized implementation on current evm](https://ethresear.ch/t/state-minimized-implementation-on-current-evm/1255)

And a lot more: [https://ethresear.ch/c/sharding](https://ethresear.ch/c/sharding).

### Ethereum 3.0

* Casper CBC integration. See [here](https://github.com/ethereum/wiki/wiki/Casper-Proof-of-Stake-compendium) FMI.
* zk-STARKs, e.g. via [StarkWare](https://www.starkware.co/); videos [here](https://www.youtube.com/watch?v=VUN35BC11Qw&t=2s), [here](https://www.youtube.com/watch?v=9VuZvdxFZQo&t=7s) and [here](https://www.youtube.com/watch?v=9VuZvdxFZQo&t=7s), as well as a paper [here](https://eprint.iacr.org/2018/046) \(the abstract is a more succinct intro than the videos\), while the full paper is more detailed; and
* [heterogeneous sharding](https://ethresear.ch/t/heterogeneous-sharding/1979)
* Outdated: [Are there any ideas that’s potentially more useful than implementing sharding?](https://ethresear.ch/t/are-there-any-ideas-thats-potentially-more-useful-than-implementing-sharding/334/3).

For more information see [Sharding introduction R&D compendium](https://github.com/ethereum/wiki/wiki/Sharding-introduction-R&D-compendium) and [Sharding FAQs](https://github.com/ethereum/wiki/wiki/Sharding-FAQs).


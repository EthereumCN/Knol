# 以太坊理论研究



## Ethereum Sharding Research Compendium <a id="Ethereum-Sharding-Research-Compendium"></a>

This is an ongoing curated list, entries will be added or removed to reflect the articles that have the most relevance to the current state of research.

Basic information and specs:

* **Sharding FAQ**: [https://github.com/ethereum/wiki/wiki/Sharding-FAQ](https://github.com/ethereum/wiki/wiki/Sharding-FAQ)
* **Beacon chain Casper FFG mini-spec**: [https://ethresear.ch/t/beacon-chain-casper-ffg-rpj-mini-spec/2760](https://ethresear.ch/t/beacon-chain-casper-ffg-rpj-mini-spec/2760)
* **Beacon chain full spec**: [https://github.com/ethereum/eth2.0-specs/tree/master/specs/core](https://github.com/ethereum/eth2.0-specs/tree/master/specs/core)
* **Sharding mindmap**: [https://www.mindomo.com/zh/mindmap/sharding-d7cf8b6dee714d01a77388cb5d9d2a01](https://www.mindomo.com/zh/mindmap/sharding-d7cf8b6dee714d01a77388cb5d9d2a01)
* **Design rationale**: [https://notes.ethereum.org/9l707paQQEeI-GPzVK02lA](https://notes.ethereum.org/9l707paQQEeI-GPzVK02lA)

#### Proof of stake theory <a id="Proof-of-stake-theory"></a>

* **Proof of stake FAQ**: [https://github.com/ethereum/wiki/wiki/Proof-of-Stake-FAQ](https://github.com/ethereum/wiki/wiki/Proof-of-Stake-FAQ)
* **Casper FFG paper**: [https://arxiv.org/abs/1710.09437](https://arxiv.org/abs/1710.09437)
* **Attestation committee-based full PoS chains**: [https://ethresear.ch/t/attestation-committee-based-full-pos-chains/2259](https://ethresear.ch/t/attestation-committee-based-full-pos-chains/2259)

#### Casper FFG/GHOST/beacon chain simulations <a id="Casper-FFGGHOSTbeacon-chain-simulations"></a>

* **Main folder**: [https://github.com/ethereum/research/tree/master/clock\_disparity](https://github.com/ethereum/research/tree/master/clock_disparity)
  * Node: `lmd_node.py`
  * Test script: `lmd_test.py`

#### Casper CBC <a id="Casper-CBC"></a>

* **A CBC Casper tutorial**: [https://vitalik.ca/general/2018/12/05/cbc\_casper.html](https://vitalik.ca/general/2018/12/05/cbc_casper.html)
* **Casper CBC, Simplified!**: [https://medium.com/@aditya.asgaonkar/casper-cbc-simplified-2370922f9aa6](https://medium.com/@aditya.asgaonkar/casper-cbc-simplified-2370922f9aa6)
* **Beacon chain-friendly CBC Casper**: [https://ethresear.ch/t/beacon-chain-friendly-cbc-casper/4710/2](https://ethresear.ch/t/beacon-chain-friendly-cbc-casper/4710/2)
* **Bitwise LMD GHOST**: [https://ethresear.ch/t/bitwise-lmd-ghost/4749/5](https://ethresear.ch/t/bitwise-lmd-ghost/4749/5)
* **LMD GHOST implementations**: [https://ethresear.ch/t/comparing-lmd-ghost-implementations/4945/3](https://ethresear.ch/t/comparing-lmd-ghost-implementations/4945/3)

#### Validator set rotation <a id="Validator-set-rotation"></a>

* **Rate-limiting entry/exits instead of withdrawals**: [https://ethresear.ch/t/rate-limiting-entry-exits-not-withdrawals/4942/](https://ethresear.ch/t/rate-limiting-entry-exits-not-withdrawals/4942/)

#### Light clients <a id="Light-clients"></a>

* **Beacon chain light client syncing**: [https://notes.ethereum.org/Irbhsn63R0W6o-r0K9mBOA](https://notes.ethereum.org/Irbhsn63R0W6o-r0K9mBOA)
* **Casper FFG light client syncing**: [https://github.com/ethereum/eth2.0-specs/blob/dev/specs/light\_client/sync\_protocol.md](https://github.com/ethereum/eth2.0-specs/blob/dev/specs/light_client/sync_protocol.md)

#### Signature aggregation <a id="Signature-aggregation"></a>

* **BLS and STARK aggregate signatures**: [https://ethresear.ch/t/pragmatic-signature-aggregation-with-bls/2105](https://ethresear.ch/t/pragmatic-signature-aggregation-with-bls/2105)

#### Stateless block verification <a id="Stateless-block-verification"></a>

* **The stateless client concept**: [https://ethresear.ch/t/the-stateless-client-concept/172](https://ethresear.ch/t/the-stateless-client-concept/172)
* **Efficiency gains from batching and multi-state roots**: [https://ethresear.ch/t/detailed-analysis-of-stateless-client-witness-size-and-gains-from-batching-and-multi-state-roots/862/9](https://ethresear.ch/t/detailed-analysis-of-stateless-client-witness-size-and-gains-from-batching-and-multi-state-roots/862/9) and [https://ethresear.ch/t/multi-tries-vs-partial-statelessness/391](https://ethresear.ch/t/multi-tries-vs-partial-statelessness/391)
* **Accumulators**: [https://ethresear.ch/t/history-state-and-asynchronous-accumulators-in-the-stateless-model/287](https://ethresear.ch/t/history-state-and-asynchronous-accumulators-in-the-stateless-model/287) and [https://ethresear.ch/t/batching-and-cyclic-partitioning-of-logs/536](https://ethresear.ch/t/batching-and-cyclic-partitioning-of-logs/536) and [https://ethresear.ch/t/double-batched-merkle-log-accumulator/571](https://ethresear.ch/t/double-batched-merkle-log-accumulator/571)
* **State-minimized executions**: [https://ethresear.ch/t/state-minimised-executions/748](https://ethresear.ch/t/state-minimised-executions/748)
* **A cryptoeconomic accumulator for state-minimized contracts**: [https://ethresear.ch/t/a-cryptoeconomic-accumulator-for-state-minimised-contracts/385](https://ethresear.ch/t/a-cryptoeconomic-accumulator-for-state-minimised-contracts/385)

#### Cross-shard communication <a id="Cross-shard-communication"></a>

* **Merge blocks anc synchronous cross-shard state execution**: [https://ethresear.ch/t/merge-blocks-and-synchronous-cross-shard-state-execution/1240](https://ethresear.ch/t/merge-blocks-and-synchronous-cross-shard-state-execution/1240)
* **Cross-shard locking**: [https://ethresear.ch/t/cross-shard-locking-scheme-1/1269](https://ethresear.ch/t/cross-shard-locking-scheme-1/1269) and [https://ethresear.ch/t/cross-shard-locking-resolving-deadlock/1275](https://ethresear.ch/t/cross-shard-locking-resolving-deadlock/1275) and [https://ethresear.ch/t/sharded-byzantine-atomic-commit/1285](https://ethresear.ch/t/sharded-byzantine-atomic-commit/1285)
* **Cross-shard yanking**: [https://ethresear.ch/t/cross-shard-contract-yanking/1450](https://ethresear.ch/t/cross-shard-contract-yanking/1450)
* **A simple synchronous cross-shard transaction protocol**: [https://ethresear.ch/t/simple-synchronous-cross-shard-transaction-protocol/3097](https://ethresear.ch/t/simple-synchronous-cross-shard-transaction-protocol/3097)
* **Cross-shard receipt and hibernation/waking anti-double-spending**: [https://ethresear.ch/t/cross-shard-receipt-and-hibernation-waking-anti-double-spending/4748](https://ethresear.ch/t/cross-shard-receipt-and-hibernation-waking-anti-double-spending/4748)
* **Fast cross shard on top of slow cross shard via optimistic conditional state objects**: [https://ethresear.ch/t/a-layer-2-computing-model-using-optimistic-state-roots/4481](https://ethresear.ch/t/a-layer-2-computing-model-using-optimistic-state-roots/4481) and [https://ethresear.ch/t/fast-cross-shard-transfers-via-optimistic-receipt-roots/5337](https://ethresear.ch/t/fast-cross-shard-transfers-via-optimistic-receipt-roots/5337)

#### Storage maintenace fees / Rent <a id="Storage-maintenace-fees--Rent"></a>

* **A simple and principled way to compute rent fees**: [https://ethresear.ch/t/a-simple-and-principled-way-to-compute-rent-fees/1455](https://ethresear.ch/t/a-simple-and-principled-way-to-compute-rent-fees/1455)
* **Improving UX via a sleep/wake mechanism**: [https://ethresear.ch/t/improving-the-ux-of-rent-with-a-sleeping-waking-mechanism/1480](https://ethresear.ch/t/improving-the-ux-of-rent-with-a-sleeping-waking-mechanism/1480)
* **Actor/asset model**: [https://ethresear.ch/t/ethereum-2-0-data-model-actors-and-assets/4117](https://ethresear.ch/t/ethereum-2-0-data-model-actors-and-assets/4117)
* **Common classes of contracts and how they would handle ongoing storage maintenance fees**: [https://ethresear.ch/t/common-classes-of-contracts-and-how-they-would-handle-ongoing-storage-maintenance-fees-rent/4441](https://ethresear.ch/t/common-classes-of-contracts-and-how-they-would-handle-ongoing-storage-maintenance-fees-rent/4441)

#### Proofs of custody <a id="Proofs-of-custody"></a>

* **Availability traps**: [https://ethresear.ch/t/proposer-withholding-and-collation-availability-traps/1294](https://ethresear.ch/t/proposer-withholding-and-collation-availability-traps/1294)
* **Hash-based proofs of custody**: [https://ethresear.ch/t/extending-skin-in-the-game-of-notarization-with-proofs-of-custody/1639](https://ethresear.ch/t/extending-skin-in-the-game-of-notarization-with-proofs-of-custody/1639) and [https://ethresear.ch/t/bitwise-xor-custody-scheme/5139](https://ethresear.ch/t/bitwise-xor-custody-scheme/5139)
* **1-bit aggregation-friendly custody bonds**: [https://ethresear.ch/t/1-bit-aggregation-friendly-custody-bonds/2236](https://ethresear.ch/t/1-bit-aggregation-friendly-custody-bonds/2236)
* **Current scheme**: [https://github.com/ethereum/eth2.0-specs/blob/dev/specs/core/1\_custody-game.md](https://github.com/ethereum/eth2.0-specs/blob/dev/specs/core/1_custody-game.md)

#### Data availability proofs <a id="Data-availability-proofs"></a>

* **Fraud proofs and data availability proofs via erasure coding**: [https://arxiv.org/abs/1809.09044](https://arxiv.org/abs/1809.09044)

#### Randomness <a id="Randomness"></a>

* **30% sharding attack**: [https://ethresear.ch/t/30-sharding-attack/1340](https://ethresear.ch/t/30-sharding-attack/1340)
* **An \(impractical\) idea for unmanipulable entropy**: [https://ethresear.ch/t/an-impractical-idea-for-unmanipulable-entropy/1355](https://ethresear.ch/t/an-impractical-idea-for-unmanipulable-entropy/1355)
* **RNG exploitability analysis \(RANDAO\)**: [https://ethresear.ch/t/rng-exploitability-analysis-assuming-pure-randao-based-main-chain/1825](https://ethresear.ch/t/rng-exploitability-analysis-assuming-pure-randao-based-main-chain/1825)
* **RANDAO exploitability analysis, round 2**: [https://ethresear.ch/t/randao-beacon-exploitability-analysis-round-2/1980](https://ethresear.ch/t/randao-beacon-exploitability-analysis-round-2/1980)
* **Low-influence functions \(Iddo Bentov\)**: [https://arxiv.org/pdf/1406.5694.pdf](https://arxiv.org/pdf/1406.5694.pdf)
* **Swap-or-not shuffle**: [https://github.com/ethereum/eth2.0-specs/issues/563](https://github.com/ethereum/eth2.0-specs/issues/563)

#### Timestamps <a id="Timestamps"></a>

* **Incentive worries around timestamps**: [https://ethresear.ch/t/highlighting-a-problem-stability-of-the-equilibrium-of-minimum-timestamp-enforcement/2257](https://ethresear.ch/t/highlighting-a-problem-stability-of-the-equilibrium-of-minimum-timestamp-enforcement/2257)
* **Network-adjusted timestamps**: [https://ethresear.ch/t/network-adjusted-timestamps/4187](https://ethresear.ch/t/network-adjusted-timestamps/4187)

#### Data structures <a id="Data-structures"></a>

* **Optimizing sparse Merkle trees**: [https://ethresear.ch/t/optimizing-sparse-merkle-trees/3751](https://ethresear.ch/t/optimizing-sparse-merkle-trees/3751)
* **Implementation of sparse Merkle trees**: [https://github.com/ethereum/research/tree/master/trie\_research/bintrie2](https://github.com/ethereum/research/tree/master/trie_research/bintrie2)
* **Double-batched Merkle log accumulator**: [https://ethresear.ch/t/double-batched-merkle-log-accumulator/571](https://ethresear.ch/t/double-batched-merkle-log-accumulator/571)
* * **Efficient Merkle proofs and generalized SSZ light client proofs**: [https://github.com/ethereum/eth2.0-specs/blob/dev/specs/light\_client/merkle\_proofs.md](https://github.com/ethereum/eth2.0-specs/blob/dev/specs/light_client/merkle_proofs.md)

#### Miscellaneous <a id="Miscellaneous"></a>

* **Cheats, weaknesses and attacks**: [http://notes.ethereum.org/MwNgJgpgHFbAtAQwKwQvALNA7PW2p4AzAYwCYAjARgrGxIE4jgg=](http://notes.ethereum.org/MwNgJgpgHFbAtAQwKwQvALNA7PW2p4AzAYwCYAjARgrGxIE4jgg=)
* **Data forgetfulness**: [https://ethresear.ch/t/sharding-and-data-forgetfulness/61](https://ethresear.ch/t/sharding-and-data-forgetfulness/61)
* **Security in the bribing model**: [https://ethresear.ch/t/shard-security-in-the-bribing-model/1366](https://ethresear.ch/t/shard-security-in-the-bribing-model/1366)
* **Better Merkle trees**: [https://ethresear.ch/t/data-availability-proof-friendly-state-tree-transitions/1453/6](https://ethresear.ch/t/data-availability-proof-friendly-state-tree-transitions/1453/6)


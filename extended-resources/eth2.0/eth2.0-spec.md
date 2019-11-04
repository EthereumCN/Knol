# ETH2.0 Specifications



## Ethereum 2.0 Specifications

[![Join the chat at https://discord.gg/hpFs23p](https://camo.githubusercontent.com/993d543cf7600197ff3e3348c0ea3982f8228f4d/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f636861742d6f6e253230646973636f72642d626c75652e737667)](https://discord.gg/hpFs23p) [![Join the chat at https://gitter.im/ethereum/sharding](https://camo.githubusercontent.com/f7223c89f7ff6dfc2aa782a9e2f79efef5ef775d/68747470733a2f2f6261646765732e6769747465722e696d2f657468657265756d2f7368617264696e672e737667)](https://gitter.im/ethereum/sharding?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

To learn more about sharding and Ethereum 2.0 \(Serenity\), see the [sharding FAQ](https://github.com/ethereum/wiki/wiki/Sharding-FAQ) and the [research compendium](https://notes.ethereum.org/s/H1PGqDhpm).

This repository hosts the current Eth 2.0 specifications. Discussions about design rationale and proposed changes can be brought up and discussed as issues. Solidified, agreed-upon changes to the spec can be made through pull requests.

### Specs

Core specifications for Eth 2.0 client validation can be found in [specs/core](https://github.com/ethereum/eth2.0-specs/blob/dev/specs/core). These are divided into phases. Each subsequent phase depends upon the prior. The current phases specified are:

#### Phase 0

* [The Beacon Chain](https://github.com/ethereum/eth2.0-specs/blob/dev/specs/core/0_beacon-chain.md)
* [Fork Choice](https://github.com/ethereum/eth2.0-specs/blob/dev/specs/core/0_fork-choice.md)
* [Deposit Contract](https://github.com/ethereum/eth2.0-specs/blob/dev/specs/core/0_deposit-contract.md)
* [Honest Validator](https://github.com/ethereum/eth2.0-specs/blob/dev/specs/validator/0_beacon-chain-validator.md)

#### Phase 1

* [Custody Game](https://github.com/ethereum/eth2.0-specs/blob/dev/specs/core/1_custody-game.md)
* [Shard Data Chains](https://github.com/ethereum/eth2.0-specs/blob/dev/specs/core/1_shard-data-chains.md)
* [Misc beacon chain updates](https://github.com/ethereum/eth2.0-specs/blob/dev/specs/core/1_beacon-chain-misc.md)

#### Phase 2

Phase 2 is still actively in R&D and does not yet have any formal specifications.

See the [Eth 2.0 Phase 2 Wiki](https://hackmd.io/UzysWse1Th240HELswKqVA?view) for current progress, discussions, and definitions regarding this work.

#### Accompanying documents can be found in [specs](https://github.com/ethereum/eth2.0-specs/blob/dev/specs) and include:

* [SimpleSerialize \(SSZ\) spec](https://github.com/ethereum/eth2.0-specs/blob/dev/specs/simple-serialize.md)
* [BLS signature verification](https://github.com/ethereum/eth2.0-specs/blob/dev/specs/bls_signature.md)
* [General test format](https://github.com/ethereum/eth2.0-specs/blob/dev/specs/test_formats/README.md)
* [Merkle proof formats](https://github.com/ethereum/eth2.0-specs/blob/dev/specs/light_client/merkle_proofs.md)
* [Light client syncing protocol](https://github.com/ethereum/eth2.0-specs/blob/dev/specs/light_client/sync_protocol.md)

### Additional specifications for client implementers

Additional specifications and standards outside of requisite client functionality can be found in the following repos:

* [Eth2.0 APIs](https://github.com/ethereum/eth2.0-apis)
* [Eth2.0 Metrics](https://github.com/ethereum/eth2.0-metrics/)
* [Interop Standards in Eth2.0-pm](https://github.com/ethereum/eth2.0-pm/tree/master/interop)

### Design goals

The following are the broad design goals for Ethereum 2.0:

* to minimize complexity, even at the cost of some losses in efficiency
* to remain live through major network partitions and when very large portions of nodes go offline
* to select all components such that they are either quantum secure or can be easily swapped out for quantum secure counterparts when available
* to utilize crypto and design techniques that allow for a large participation of validators in total and per unit time
* to allow for a typical consumer laptop with `O(C)` resources to process/validate `O(1)` shards \(including any system level validation such as the beacon chain\)

### Useful external resources

* [Design Rationale](https://notes.ethereum.org/s/rkhCgQteN#)
* [Phase 0 Onboarding Document](https://notes.ethereum.org/s/Bkn3zpwxB)

### For spec contributors

Documentation on the different components used during spec writing can be found here:

* [YAML Test Generators](https://github.com/ethereum/eth2.0-specs/blob/dev/test_generators/README.md)
* [Executable Python Spec, with Py-tests](https://github.com/ethereum/eth2.0-specs/blob/dev/test_libs/pyspec/README.md)


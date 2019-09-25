# Casper PoS



## Casper Proof of Stake compendium

Rachit Phetrahsa edited this page on 17 Apr · [34 revisions](https://github.com/ethereum/wiki/wiki/Casper-Proof-of-Stake-compendium/_history)

**Contents**

* [Casper FFG](https://github.com/ethereum/wiki/wiki/Casper-Proof-of-Stake-compendium#casper-ffg)
* [Casper CBC](https://github.com/ethereum/wiki/wiki/Casper-Proof-of-Stake-compendium#casper-cbc)
  * [Further reading](https://github.com/ethereum/wiki/wiki/Casper-Proof-of-Stake-compendium#further-reading)
* [Applications/Infrastructure using Casper](https://github.com/ethereum/wiki/wiki/Casper-Proof-of-Stake-compendium#applicationsinfrastructure-using-casper)

For an introduction to PoS, see [PoS FAQs](https://github.com/ethereum/wiki/wiki/Proof-of-Stake-FAQs).

### Casper FFG

[Casper the Friendly Finality Gadget \(FFG\)](https://github.com/ethereum/research/tree/master/papers/casper-basics), AKA for short as Casper FFG, for PoS validation with Proof-of-Work \(PoW\). Resources include:

* sharding + casper \(= [shasper](https://notes.ethereum.org/SCIg8AH5SA-O4C1G1LYZHQ)\) spec
* [EIP 1011](https://eips.ethereum.org/EIPS/eip-1011) \(deprecated\)
* [implementation doc](https://github.com/ethereum/casper/blob/master/IMPLEMENTATION.md); and
* a [testnet](https://hackmd.io/s/Hk6UiFU7z)
* [repo](https://github.com/ethereum/casper)

### Casper CBC

[Casper the Friendly GHOST: Correct by Construction \(CBC\)](https://github.com/ethereum/research/blob/master/papers/CasperTFG/CasperTFG.pdf), AKA Casper CBC; for full Proof-of-Stake \(PoS\). [GHOST](https://eprint.iacr.org/2013/881) stands for Greediest Heaviest Observed Sub-Tree, and is a blockchain fork-choice rule protocol. PoS will be essential for the sustainability of Ethereum, drastically reducing its energy consumption while increasing scalability and performance. Resources include:

* See this [repo](https://github.com/ethereum/cbc-casper).
* [wiki](https://github.com/ethereum/cbc-casper/wiki)

#### Further reading

* [a comparison of FFG and CBC](https://ethereum.stackexchange.com/a/31814/9584)
* the [https://ethresear.ch](https://ethresear.ch/) Casper category, e.g.:
  * [CBC-Casper in the face of the new PoS+Sharding plans on Ethereum](https://ethresear.ch/t/cbc-casper-in-the-face-of-the-new-pos-sharding-plans-on-ethereum/2444/7), includes a comparison of FFG and CBC with a link to a [draft formal proof of FFG](https://ethresear.ch/t/epoch-less-casper-ffg-liveness-safety-argument/2702)

### Applications/Infrastructure using Casper

* [Rocket Pool](https://github.com/rocket-pool/rocketpool): allows pooling, in alpha as of August 2018. "Unlike traditional centralised proof-of-work \(PoW\) pools, Rocket Pool utilises the power of smart contracts to create a self-regulating decentralised network of smart nodes that allows users with any amount of ether to earn interest on their deposits and help secure the Ethereum network at the same time."



[https://notes.ethereum.org/@serenity/H1PGqDhpm?type=view](https://notes.ethereum.org/@serenity/H1PGqDhpm?type=view)


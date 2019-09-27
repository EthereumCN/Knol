# Sharding 分片

### 

Ethereum 1.0 can only process 7-15 transactions per second, the goal of sharding is to partition all network computational resources into shards, so that a node \(a single computer as a peer connected to the network\) doesn't have to process \(download, compute, store, read\) every transaction in the history of the blockchain, in order to make a new transaction \(write and upload\) or otherwise participate in securing and using Ethereum; rather a node can just participate in a single shard, or more if it so chooses. Multiple shards are handled separately by different subsets of securing participants, aka securitors \(which include notaries, proposers, miners and validators\)[\[1\]](https://eprint.iacr.org/2017/406.pdf). The primary goal is a massive scalability improvement, potentially exponential in phase 6 of the [roadmap](https://github.com/ethereum/wiki/wiki/Sharding-roadmap), although Vitalik questioned whether exponential sharding will even be tenable in an ethresear.ch thread. Quadratic sharding involves having shards at a depth of at most 1 from the main chain, that is, there are no shards within a shard, or a manager shard managing sub-shards; whereas exponential sharding has shards within shards, recursively.

Each one of the shards \(currently set to 1024 in the [latest spec](https://github.com/ethereum/eth2.0-specs)\) will have as high a capacity \(and likely more with phase 1\) than the current existing Ethereum chain. The latest spec combines sharding with Casper FFG PoS \(see e.g. the [PoS FAQ](https://github.com/ethereum/wiki/wiki/Proof-of-Stake-FAQs) and [compendium](https://github.com/ethereum/wiki/wiki/Casper-Proof-of-Stake-compendium) for details\) using a RANDAO beacon chain. Previously a contract was planned to exist on the main chain, however that has been scrapped, in order to make sharding implementation easier, as explained e.g. [here](https://threadreaderapp.com/thread/1029900695925706753.html) in a Tweet storm by Vitalik.



For information on sharding, refer to \(sorted roughly from the most recent/important information to less recent\):

* [https://github.com/ethereum/eth2.0-specs/blob/master/specs/beacon-chain.md](https://github.com/ethereum/eth2.0-specs/blob/master/specs/beacon-chain.md)
* [Sharding FAQs](https://github.com/ethereum/wiki/wiki/Sharding-FAQs)
* sharding [roadmap](https://github.com/ethereum/wiki/wiki/Sharding-roadmap)
* a summary [here](https://twitter.com/sinahab/status/992755776765792256);
* [ethresear.ch sharding category](https://ethresear.ch/c/sharding) \(watch new posts\);
* [sharding protocol research tracker](https://github.com/Drops-of-Diamond/diamond_drops/issues/13)
* [Sharding Fork Choice rule PoC and more info, 2018 May 1](https://twitter.com/VitalikButerin/status/991021062811930624). Also see [here](https://github.com/Drops-of-Diamond/diamond_drops/issues/13) for an issue tracking protocol specification updates.
* [Sharding introduction](https://docs.google.com/presentation/d/1mdmmgQlRFUvznq1jdmRwkwEyQB0YON5yAg4ArxtanE4/edit?usp=sharing)
  * networking diagram on slides 82–87
* [Sharding workshop notes](https://hackmd.io/s/HJ_BbgCFz#%E2%9F%A0-General-Introduction)
* [Ethereum sharding workshop blog post](https://medium.com/@icebearhww/ethereum-sharding-workshop-in-taipei-a44c0db8b8d9)
* [HackMD note: Ethereum Research Sharding Compendium](http://notes.ethereum.org/s/BJc_eGVFM)
* [Wiki: ethresear.ch Sharding Compendium](https://github.com/ethereum/wiki/wiki/Wiki:-ethresear.ch-Sharding-Compendium)

### 

### 

## Ethereum Sharding Research Compendium <a id="Ethereum-Sharding-Research-Compendium"></a>

### [https://notes.ethereum.org/@serenity/H1PGqDhpm?type=view](https://notes.ethereum.org/@serenity/H1PGqDhpm?type=view)

### Grant program

[https://blog.ethereum.org/2018/01/02/ethereum-scalability-research-development-subsidy-programs/](https://blog.ethereum.org/2018/01/02/ethereum-scalability-research-development-subsidy-programs/)

See also [https://github.com/ethereum/wiki/wiki/Grants-and-funding-sources](https://github.com/ethereum/wiki/wiki/Grants-and-funding-sources).




# 随机性



#### Randomness

Context: full PoS with Casper CBC and sharding will require a verifiable randomness function that is secure and decentralized, in order to randomly select and assign validators, shuffle notaries, and select collation/block proposers. Such a VRF is an ongoing topic of research, and is yet to be specified and implemented.

* [Sharding Fork Choice rule PoC with RANDAO and more info, 2018 May 1](https://twitter.com/VitalikButerin/status/991021062811930624)
* [Vitalik's blog post on Randomness with RANDAO preferred](https://vitalik.ca/files/randomness.html)
* [RANDAO++](https://twitter.com/VitalikButerin/status/734735362493427713) plus [an implementation of it with RNGesus](https://github.com/zweicoder/RNGesus)
* A random beacon [as used by Dfinity](https://github.com/ethereum/wiki/wiki/Alternative-blockchains,-randomness,-economics,-and-other-research-topics) although note that BLS is prone to 51% attacks while RANDAO++ isn't.
* [James' tweet thread analysing Dfinity's BLS randomness beacon in comparison to RANDAO](https://twitter.com/JamesCRay01/status/984289250400075777).
* ethresear.ch post: [PoSW random beacon](https://ethresear.ch/t/posw-random-beacon/1814). [Here](https://gitter.im/ethereum/sharding?at=5adf53096d7e07082b2bdf44) is a critique by Vitalik on Gitter.
* the blockhash, generated via PoW, e.g. [Ethash](https://ethereum.github.io/yellowpaper/paper.pdf#appendix.J). You could use the blockhash as a preimage to hash functions e.g. in RANDAO.
* [https://ethresear.ch/t/rng-exploitability-analysis-assuming-pure-randao-based-main-chain/1825](https://ethresear.ch/t/rng-exploitability-analysis-assuming-pure-randao-based-main-chain/1825)
* [RANDAO exploitability analysis results](https://gitter.im/ethereum/sharding?at=5af228fc40f24c43046242f9)


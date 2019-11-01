# ETH2.0路线图

## 迈向宁静Serenity

![](../../.gitbook/assets/image%20%282%29.png)

### _What is Serenity, what are the plans for Ethereum 2.0, and when will it happen?_

[Ethereum’s history](https://media.consensys.net/a-short-history-of-ethereum-a8fdc5b4362c) has been one of consistent improvements and upgrades to the core protocol. After February’s [Constantinople upgrade](https://media.consensys.net/the-constantinople-hard-fork-what-you-need-to-know-d438a91dec3f) and the upcoming Istanbul hard fork, the Ethereum community is approaching Serenity, the eventual and final iteration in Ethereum’s evolution. Serenity — explained in detail in [Vitalik’s 2018 Devcon speech](https://www.youtube.com/watch?v=Km9BaxRm1wA&t=1272s) — will take place in multiple stages, each estimated a year apart from each other. Ethereum 2.0 — as Serenity is also known — is being guided by [five design principles](https://media.consensys.net/exploring-the-ethereum-2-0-design-goals-fd2d901b4c01): _Simplicity, Resilience, Longevity, Security, Decentralization_. The gradual approach to Serenity is meant to ensure all principles are developed and upheld, further positioning Ethereum as a market leader in blockchain-based solutions.

## **But First — Istanbul** <a id="49ad"></a>

Before Serenity, Istanbul is currently the last planned hard fork after the Constantinople upgrade in February of this year. Istanbul is estimated for October 2019, and there are [currently 11 EIPs proposed](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-1679.md) for inclusion in the fork, including EIP 1057 \[ProgPoW\].

The issue of ProgPoW has been heavily discussed in the Ethereum community for a while. The EIP proposes switching the protocol’s mining algorithm to ProgPoW, an algorithm that reduces the advantage ASICs have over GPUs in mining efficiency. ASICs \(Application-Specific Integrated Circuits\) and GPUs \(Graphics Processing Units\) are both pieces of hardware used to mine crypto. ASICs are highly-specialized pieces of hardware that can typically mine crypto more efficiently and thus yield a greater profit. They are, however, coin-specific, meaning a Bitcoin ASIC is only applicable to the Bitcoin blockchain and an Ethereum ASIC is only applicable to the Ethereum blockchain. Though effective, ASICs are costly and harder to come by, potentially leading to centralization risks if the mining pool becomes limited to those who are able to get their hands on ASICs \(so the argument goes\). GPUs, by contrast, are general purpose computation tools, and can be used for complex calculations for a number of computational use cases. In contrast to ASICs, GPUs can be used to mine any coin and are generally widely available. They do not, however, carry the same specialized computing power as ASICs, and therefore are typically less efficient and less profitable than ASICs. If approved, EIP 1057 would implement the ProgPoW algorithm, which is an ASIC-resistant algorithm, effectively removing the efficiency ASICs have over GPUs and rendering them equally effective at mining Ether, and consequently ensuring the decentralization of the network \(again, so the argument goes\). The Ethereum core devs appear to be in general support of ProgPoW, but have launched third-party audits of the algorithm before making a final decision.An overview of the Serenity roadmap.

## **Phase 0: The Beacon Chain \| 2019** <a id="eca9"></a>

Expected in 2019, the first phase of Serenity will see the rollout of the [Beacon Chain](https://media.consensys.net/state-of-ethereum-protocol-2-the-beacon-chain-c6b6a9a69129). The Beacon Chain is a [Proof of Stake](https://media.consensys.net/the-state-of-scaling-ethereum-b4d095dbafae) blockchain and will mark the execution of the long-planned switched from proof of work to proof of stake consensus mechanism. The Beacon Chain will be stood up and will run alongside the original Ethereum PoW chain, ensuring there is no break in the continuity of the chains. In its earliest form, the Beacon Chain has three primary responsibilities:

### Managing the proof of stake mechanism <a id="dedf"></a>

Proof of Stake is the consensus mechanism in which the network stakes ETH \(as opposed to expending energy to mine\) in order to continue finalizing blocks into existence.

### Processing Crosslinks <a id="237f"></a>

Crosslinks are the main way that the Beachon Chain can determine and secure the state of shard chains. Shard chains will be released in Phase 1, so this update is in preparation for Phase 1.

### Direct consensus and finality <a id="b8bc"></a>

The Beacon chain provides finality through PoS and \(what was formerly known as\) Casper FFG. PoS dictates that 2/3 of validators must stake ETH on the next block, meaning the financial incentive is much riskier for potential malicious actors.

## **Phase 1: Shard Chains \| 2020** <a id="ed46"></a>

Shard chains are a core feature to future scalability on the Ethereum network. As an overall concept, sharding splits the data processing responsibility of a database \(decentralized or otherwise\) among many nodes, allowing parallel transaction, storing, and processing of information. This is in opposition to the existing Ethereum mainchain, which requires each full node to process & validate each transaction.

Serenity Phase 1 will address finality and consensus on shard chains. The shard chains in Serenity’s Phase 1 will be more of a “test run” for shard chains than the release of an immediately-scalable solution. The Beacon Chain will monitor the execution of these shard chains. A validator will stake 32 ETH and be randomly assigned to serve as a validator on a specific shard chain \(the randomness ensures the assignment of validators to shard chains is not predictable, which would lead to a chance of manipulation\). According to the Ethereum 2.0 specs, the Beacon Chain will support 1024 shard chains, each of which will be validated by a collection of 128 nodes.

## **Phase 2: eWASM \| 2020 or 2021** <a id="a1df"></a>

In Phase 2, the functionality of Ethereum 2.0 comes together. With the introduction of a new Virtual Machine — Ethereum-flavored Web Assembly \(eWASM\) — shard chains evolve from fairly rudimentary data markers to fully-functional transactional chains, capable of scaling the Ethereum network.

In order for a blockchain ecosystem to operate, nodes must execute transactions and smart contracts in a _virtual machine_. Ethereum 1.0’s virtual machine was called the Ethereum Virtual Machine \(EVM\). With the switch to Ethereum 2.0 and the Beacon Chain, the network’s virtual machine will be upgraded to eWASM, a virtual machine based off Web Assembly, which is defined by the World Wide Web Consortium \(W3C\) as an open-source standard. Because WASM supports a number of coding languages, eWASM could allow smart contracts written in any language to be executed on Ethereum, as opposed to just ones written in Solidity in today’s EVM.

## “Ethereum 1.x” <a id="8090"></a>

It is important to note that during Serenity Phase 0, 1, and 2, the original PoW Ethereum chain will not go away. It will continue to be maintained alongside the Beacon Chain, with the miners on the original PoW chain still being rewarded in ETH through traditional forms of mining. Gradually, as the ecosystem transitions over to the Beacon Chain, the PoW chain may be phased out if the Difficulty Bomb renders it computationally obsolete \[“may” because some advocate for its permanent continuation\]. As the Beacon Chain is being tested and proven, improvements will still be made on the original Ethereum 1.0 chain. This series of upgrades and hard forks is referred to as “Ethereum 1.x” and will ensure the current Ethereum mainchain undergoes continued upgrades to meet ecosystem demand and adoption as the Beacon Chain scales.

The team behind Ethereum 1.x is still in the early phases of establishing a roadmap, but they have determined three overarching goals for Ethereum 1.x upgrades:

1. Mainnet scalability boost by increasing the tx/s throughput \(achieved with client optimizations that will enable raising the block gas limit substantially\)
2. Ensure that operating a full node will be sustainable by reducing and capping the disk space requirements with “state fees”
3. Improved developer experience with VM upgrades including eWASM and a [different transaction fee model](https://en.ethereum.wiki/eth1#fee-market-change) that would stabilize overall transaction fees.
4. Working on the [finality gadget](https://en.ethereum.wiki/eth1#finality-gadget) to link Ethereum 1.0 and 2.0 by using the Beacon Chain to finalize Ethereum 1.x blocks.

More information about Ethereum 1.x and the team behind its continued improvements and upgrades can be found [here](https://docs.ethhub.io/ethereum-roadmap/ethereum-1.x/) and [here](https://en.ethereum.wiki/eth1).

## **Phase 3: Continued Improvement \| 2022** <a id="01dd"></a>

Beyond Phase 2, the timeline for Ethereum begins getting less specific. One this is certain — developers will continue working on pressing matters and improving the protocol to meet the growing demands of blockchain technology. Among the continued improvements being discussed: light client state protocol, coupling with mainchain security, and super-quadratic or exponential sharding. And, somewhere beyond, “Ethereum 3.0,” the next phase in Ethereum’s consistent evolution.


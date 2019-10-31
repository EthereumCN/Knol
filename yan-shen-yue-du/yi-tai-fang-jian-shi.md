# 以太坊简史

## A Short History of Ethereum

### _An overview of the upgrades and hard forks of Ethereum’s past, with an eye towards what lies ahead._

[![ConsenSys](https://miro.medium.com/fit/c/48/48/1*HWsBYAT2n833Op1P3T_Osw.png)](https://media.consensys.net/@ConsenSys?source=post_page-----a8fdc5b4362c----------------------)[ConsenSys](https://media.consensys.net/@ConsenSys?source=post_page-----a8fdc5b4362c----------------------)Follow[May 14](https://media.consensys.net/a-short-history-of-ethereum-a8fdc5b4362c?source=post_page-----a8fdc5b4362c----------------------) · 9 min read![](https://miro.medium.com/max/30/1*8LYqOKwJRQ4fvBWLavPP3A.jpeg?q=20)![](https://miro.medium.com/max/1668/1*8LYqOKwJRQ4fvBWLavPP3A.jpeg)

From a bird’s eye view, blockchain technology has not been around for a long time. Though the foundational concepts \(cryptography, decentralization, peer-to-peer networking & transaction\) have been studied for decades, not until Bitcoin’s release in 2008 can all those components be confidently seen as having come together to create a functional product. Ethereum in particular has been around in usable, public format only since 2015. Though the dates and details of its projected evolution have changed, Ethereum has stuck with its plan to consistently upgrade the protocol to ensure improved usability, security, functionality, and decentralization.

With the recent [Constantinople](https://media.consensys.net/the-constantinople-hard-fork-what-you-need-to-know-d438a91dec3f) upgrade in February, Ethereum is on the cusp of Serenity \(also known as Ethereum 2.0\), to be reached through a series of hard forks and upgrade phases, including “Ethereum 1.x.” To understand where we are going, however, we must look back at and understand from where we came. This timeline looks at the history of Ethereum’s significant \(un\)planned hard forks and upgrades in preparation of its next phase of evolution.

## **Olympic \| May 9, 2015** <a id="894c"></a>

The Ethereum blockchain sprung into public existence in July 2015. The immediate step before that, however, was Olympic — the ninth and final proof of concept open testnet, available to developers to explore what the Ethereum blockchain would look like once released. [Vitalik announced](https://blog.ethereum.org/2015/05/09/olympic-frontier-pre-release/) a total reward of 25,000 ETH to developers who spent their time stress-testing the network. The request was clear: try to overload the network and do “crazy things with the state” to provide insight into how the protocol would handle high traffic. Developers were given four categories to test: Transaction Activity, Virtual Machine Usage, Mining Prowess, and General Punishment.

## **Frontier \| July 30, 2015** <a id="5248"></a>

After a couple months of stress testing, the Ethereum network was ready for the official public mainnet launch. On July 20, Ethereum’s genesis block was mined into existence and the community began to grow. A few months before the Frontier launch, [Vinay Gupta published a note](https://ethereum.github.io/blog/2015/03/03/ethereum-launch-process/) about Ethereum’s launch process. Amidst paragraphs of excitement are warnings to potential users. Frontier, he claimed, was Ethereum “in its barest form” and developers should take caution. Just days before the Frontier launch, [Stephen Taul echoed](https://ethereum.github.io/blog/2015/07/22/frontier-is-coming-what-to-expect-and-how-to-prepare/) Gupta’s warning to developers: “Like their counterparts during the American Frontier, these settlers will be presented with vast opportunities, but will also face many dangers.”

The Frontier protocol contained a series of crucial characteristics:

* **Block Reward:** When miners successfully mines a block into existence on the Ethereum blockchain, they receive a reward in ETH. Frontier was launched with block reward of 5 ETH per block.
* [**Gas:**](https://media.consensys.net/a-guide-to-gas-12b40d03605d) ****During the first few days of Frontier’s existence, the gas limit per block was hardcoded at 5000 gas. Basically, this meant nothing could happen on the network. This was purposefully implemented to give a few days of buffer time to allow miners to start their operations on Ethereum and to allow early adopters to install their clients. After a few days, the gas limit was automatically removed and the network became capable of handling transactions and smart contracts as it was meant to.
* **Canary Contracts:** Canary contracts were included into Frontier to notify users that a particular chain was bad or vulnerable. Canary contracts were given either a 0 or a 1. Contracts that had an issue were given a 1 and clients were notified so they would not mine off that broken chain. Essentially, the canary contracts capabilities gave the core Ethereum dev group the ability to stop an operation or transaction on the network should something begin to go wrong. Canary contracts were a heavily centralized but necessary protection mechanism early in Ethereum’s existence.
* **Usability:** All developer actions were done with command lines; there was no Graphical User Interface in existence. The network was usable, but the UI was rough and its capabilities were largely limited to people with existing knowledge and experience with Ethereum.

## **Homestead \| March 14, 2016** <a id="a14b"></a>

The Homestead upgrade was the first planned hard fork of the Ethereum network and was implemented on May 14th, 2016 with block number 1,150,000. Overall, the Homestead upgrade included three major improvements to Ethereum. First, it removed the canary contract functionality, removing that point of centralization on the network. Second, it introduced new codes in Solidity, the programming language used on Ethereum. Last, it introduced the Mist wallet, which allowed users to hold/transact ETH and write/deploy smart contracts.

The Homestead upgrade was one of the earliest implementations of Ethereum Improvement Proposals. EIPs are recommendations made to the community that then, if approved, are included in network upgrades. The Homestead upgrade included three EIPs:

### **EIP-2: Main Homestead upgrades** <a id="3074"></a>

_EIP 2.1:_ Increased the cost for creating smart contracts via a transaction from 21,000 gas to 53,000 gas. The cost to create a contract via another contract — the preferred method — had cost more than creating it via a transaction. By increasing the gas cost to create contracts via transactions, EIP 2.1 incentivized users to return to creating contracts via other contracts.

_EIP 2.2:_ “All transaction signatures whose s-value is greater than secp256k1n/2 considered invalid. The ECDSA recover precompiled contract remained unchanged and kept accepting high s-values; this is useful e.g. if a contract recovers old Bitcoin signatures.” \[[source](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-2.md)\]

_EIP 2.3:_ Dictated that if a contract did not have enough gas to complete the operation, the contract would “fail” rather than create an empty contract. This changed the possible outputs of a transaction from \[success\] \[fail\] or \[empty\] to just \[success\] or \[fail\].

_EIP 2.4:_ Eliminated an incentive that allowed users to create blocks with slightly higher difficulty — i.e. blocks that would be more likely to be mined. This upgrade stabilized block times between 10–20 seconds and restored the network to its overall target time of ~15 seconds per block.

### **EIP-7** <a id="24b0"></a>

“Adds a new opcode, DELEGATECALL at 0xf4, which is similar in idea to CALLCODE, except that it propagates the sender and value from the parent scope to the child scope, ie. the call created has the same sender and value as the original call.” \[[source](https://github.com/ethereum/EIPs/issues/23)\]

### **EIP-8: Future Upgrades** <a id="cbe4"></a>

EIP-8 was an Improvement Proposal with an eye towards future, planned network upgrades. The improvement ensured all client software on Ethereum could accommodate future network protocol updates.![](https://miro.medium.com/max/30/1*bFE84TMMIf-z_zG3NQ8GyQ.png?q=20)![](https://miro.medium.com/max/500/1*bFE84TMMIf-z_zG3NQ8GyQ.png)

## **DAO Fork \| July 20, 2016** <a id="d38c"></a>

In the history of planned Ethereum upgrades and hard forks, the unplanned DAO incident deserves inclusion. In 2016, a [Decentralized Autonomous Organization called The DAO](https://media.consensys.net/the-dao-heist-faq-ef95a7f310f0) raised $150 million USD in a token sale for funding. In June, the DAO was hacked and $50 million worth of ETH was stolen by an unknown hacker. The Ethereum community at large decided to hard fork the chain in order to restore the funds to their original wallets and patch the vulnerability. The hard fork, however, was contentious, and some in the Ethereum community continued to mine and transact on the _original_ chain. The _original_ chain — with the stolen ether not returned — became [Ethereum Classic, which has become weaker and subject to exploitation over time](https://cryptobriefing.com/ethereum-51-percent-attack-myth/). The majority of the community and the core developers continued working off the forked chain — with the stolen ETH returned to their original owners — which is what we now know as the Ethereum blockchain.

## **Metropolis: Byzantium \| October 16, 2017** <a id="2102"></a>

The next stage of Ethereum’s roadmap was known as Metropolis, and it would take place in two phases: Byzantium and Constantinople. Byzantium went live in 2017 at block 4,370,000 and included nine EIPs, including:

### EIP 100 <a id="140d"></a>

Adjusted the formula to assess the difficulty of a block to take into account uncle blocks. The new formula provided stability to the issuance rate, ensuring it could not be forced upward by manipulating uncle blocks.

### EIP 658 <a id="ebe8"></a>

For blocks following the Byzantium upgrade, transaction receipts included a status field to indicate success \(represented by 1\) or failure \(represented by 0\).

### EIP 649 <a id="77cc"></a>

The Difficulty Bomb is a mechanism that, if activated, would increase the energy required \(i.e. the ‘difficulty\) to mine a new block until it becomes impossible and no new blocks can be mined. At this point, the Ethereum network would become ‘frozen.’ The Difficulty Bomb was originally included in the network in September 2015. Its purpose is to support the eventual transition away from Proof of Work towards Proof of Stake. When PoS is implemented, miners could theoretically choose to support the old PoW chain, thus causing a split in the community and the creation of two separate chains — one maintained by stakers and one maintained by miners. The solution for this not to happen is to implement the Difficulty Bomb, which would eventually phase out the efficacy of mining and allow for the complete transition of the network over to PoS without the threat of a contentious hard fork. Delay of the ice age / difficulty bomb by 1 year, and reduction of block reward from 5 ETH to 3 ETH

An overview of the remaining Byzantium EIPs \(140, 196, 197, 198, 211, 214\) [found here.](https://github.com/ethereum/wiki/wiki/Byzantium-Hard-Fork-changes)

## **Metropolis: Constantinople \| February 28, 2019** <a id="3fe6"></a>

The second part of the Metropolis upgrade, [named Constantinople,](https://media.consensys.net/the-constantinople-hard-fork-what-you-need-to-know-d438a91dec3f) was scheduled to go live at block 7,080,000 — estimated in mid-January 2019. On January 15th, an independent security auditing firm named ChainSecurity published a report that indicated one of the five main system upgrades could provide attackers with the opportunity to steal funds. In response to the report, core Ethereum developers and the extended community voted to delay the upgrade until the security loophole could be resolved. Later that month, the core developers announced the upgrade would take place at block 7,280,000. Block 7,280,000 arrived on February 28th and the Constantinople hard fork upgrade went live. Today’s Ethereum network is in the Constantinople phase.

### **EIP 145: Bitwise Shifting Instructions** <a id="71b1"></a>

Added Bitwise shifting instructions to the Ethereum Virtual Machine \(EVM\). The instructions allow for bits of binary information to move to the left and to the right. This improvement means the execution of shifts in smart contracts will be 10x cheaper.

### **EIP 1052: Smart Contract Verification** <a id="22c5"></a>

Allowed for smart contracts to verify one another by pulling just the hash of the other smart contract. Before Constantinople, smart contracts would have to pull the entire code of another in order to verify, which took time and energy to perform.

### **EIP 1014: CREATE2** <a id="9bdf"></a>

Improved the enablement of [state channels](https://media.consensys.net/the-state-of-scaling-ethereum-b4d095dbafae), an Ethereum scaling solution based on off-chain transactions.

### **EIP 1283: SSTORE** <a id="d4b4"></a>

Reduced the gas cost for the SSTORE operation. This reduction enables multiple updates to occur within a transaction more cheaply.

### **EIP 1234: Block Rewards & Difficulty Bomb Delay** <a id="3424"></a>

Comprised of two components: Block Reward Reduction and Difficulty Bomb Delay.

[_Block Reward Reduction_](https://media.consensys.net/the-thirdening-what-you-need-to-know-df96599ad857)  
__Rewards for miners were reduced from 3 ETH per block to 2 ETH per block. This reduction is known as the “[Thirdening](https://media.consensys.net/the-thirdening-what-you-need-to-know-df96599ad857).”

_Difficulty Bomb Delay_  
EIP 1234 delays the implementation of the Difficulty Bomb for another twelve months, at which point it will be voted upon again.

## **Looking Ahead: Istanbul & Serenity** <a id="e568"></a>

Looking ahead, Serenity is the eventual destination for the Ethereum blockchain, but not before the Istanbul hard fork and “Ethereum 1.x.” The Istanbul hard fork will be defined largely by the decision around ProgPoW. Serenity will be defined by the complete switch from Proof of Work to Proof of Stake, but will include other important upgrades. Notably, the introduction of the Beacon Chain, Sharding, and the switch from the Ethereum Virtual Machine \(EVM\) to Ethereum-flavored Web Assembly \(eWASM\). All of Serenity’s upgrades will be shipped in phases, and during that time, Ethereum 1.x will continue to be improved upon to ensure the continuation of the original PoW chain. Keep an eye out for the next article on future hard forks and Serenity.



我们向Consensys致敬，他们给我们提供了很好的文章作为来源，[A Short History of Ethereum](https://media.consensys.net/a-short-history-of-ethereum-a8fdc5b4362c)。


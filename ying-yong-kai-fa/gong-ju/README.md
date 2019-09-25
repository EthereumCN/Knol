# 基础工具

## The Ultimate Guide to Ethereum Developer Tools

November 18th 2018[ TWEET THIS](https://twitter.com/intent/tweet?text=The%20Ultimate%20Guide%20to%20Ethereum%20Developer%20Tools&url=https%3A%2F%2Fhackernoon.com%2Fthe-ultimate-guide-to-ethereum-developer-tools-7feb53f9d953&hashtags=ethereum,ethdevelopertools,ethereumdevelopertools)![](https://hackernoon.com/hn-images/1*f1KcuSsD63ilcpNZqz-b7Q.jpeg)Photo by [shotbycerqueira](https://unsplash.com/photos/0o_GEzyargo?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

#### Introduction

If you are a software engineer looking to get started with developing on the Ethereum blockchain, it can be overwhelming at the outset to understand all the tools and technologies available to use today.

From decentralized browsers and wallets to terms like Truffle, Ganache, Infura, Parity and Geth this can all be very confusing and act as an impediment to your nascent blockchain learning journey.

So I have decided to compile a guide which lists out the leading software tools and packages and give you a brief overview of the function of each.

Hopefully this will help give you a better picture of the Ethereum ecosystem and how all the pieces fit together while ultimately providing you with a jump start on your accelerated learning journey with this fascinating new technology!

**Solidity \(Programming Language\)**

[https://solidity.readthedocs.io/en/develop/](https://solidity.readthedocs.io/en/develop/)![Related image](https://hackernoon.com/hn-images/1*_VZIGjpHOHaoAmNcApUQgA.jpeg)

Solidity is the most popular programming language used to write smart contracts to run on the Ethereum blockchain. It is a high level language which when compiled gets converted to EVM \(Ethereum Virtual Machine\) byte code. This is very similar to the Java ecosystem where there are languages such as Scala, Groovy, Clojure, JRuby which on compilation all generate byte code which run in the JVM \(Java Virtual Machine\).

**Solc \(Solidity Compiler\)**

[https://github.com/ethereum/solc-js](https://github.com/ethereum/solc-js)![](https://hackernoon.com/hn-images/1*HNAOhmNuaOY766JIwlIoRQ.jpeg)

Solidity code may be designed to look like javascript, [but you still have to compile it](https://i.imgflip.com/1k3ffi.jpg). Solc is your translator from the smart contract language solidity to Ethereum’s byte-code and can even be run on the command line for quick-and-dirty tasks and rapid development validation.

**Remix \(Browser-based IDE\)**

[http://remix.ethereum.org/](http://remix.ethereum.org/)

This is great for debugging, and gives you an option to step through the contract directly in your browser which is extremely helpful. You should use remix until you get your dApp to compile with no bugs. There are 3 options with remix for interacting with a blockchain instance via the browser

1. You can use the Javascript Virtual Machine \(JVM\),
2. An injected web3 instance \(e.g. the chrome extension Metamask\),
3. connecting to a web3 provider \(like[ Infura](https://infura.io/)\).

The JVM is the way to go when you are beginning. It is much more forgiving, it ignores gas limits, gives you an unlimited amount of ether to play with between 5 accounts, and it is much faster to debug.![](https://hackernoon.com/hn-images/0*6cwK6wJwbnpDBsEG.png)Screenshot of remix in the browser

**Mist \(Decentralized Browser\)**

[https://github.com/ethereum/mist/releases](https://github.com/ethereum/mist/releases)

This is a browser for Ðapp \(decentralized web app\). Like the equivalent of Chrome or Firefox, but for Dapps. It’s still not fully secure and so you shouldn’t use it with untrusted dapps yet.![](https://hackernoon.com/hn-images/1*VBWEfx0kNABNt7iSGCyGgQ.png)

**Ethereum wallet \(A Specific Version of Mist\)**

[https://wallet.ethereum.org/](https://wallet.ethereum.org/)

**This is a version of Mist** but only opens **one single dapp -** the Ethereum Wallet. **Mist and Ethereum Wallet are just UI fronts** so we still need a core that will connect us to an Ethereum blockchain \(It could be the real Ethereum blockchain, or a test one\). You can use Mist to create wallets, store Ether, send transactions, deploy contracts and more. You can use the native application to play around on the blockchain or testnet. Super useful for quick transactions.![](https://hackernoon.com/hn-images/1*vFWVfB9AFpV-OfYjRdB_ZQ.png)

**Meta Mask \(Browser Plugin\)**

[https://metamask.io/](https://metamask.io/)![](https://hackernoon.com/hn-images/1*eSJrrAeM6TpYt0r_Gopqow.png)

If you’re building a Ðapp you actually want people to use then **MetaMask** support is a must-have. You can think of MetaMask as a bridge that allows you to visit the distributed web of tomorrow in your browser today by using an extension \(without running a full Ethereum node\).![](https://i.ytimg.com/vi/6Gf_kRE4MJU/hqdefault.jpg)

It ****is available in Chrome, Firefox, Opera and Brave that lets users securely manage their Ethereum accounts and private keys, and use these accounts to interact with websites that are using Web3.js \(see more on this below\).

Once you install it then your browser can now interact with any website that communicates with the Ethereum blockchain! **Note**: Metamask uses Infura’s servers under the hood as a web3 provider but it also gives the user the option to choose their own provider.

**Web3.js**

[https://github.com/ethereum/web3.js/](https://github.com/ethereum/web3.js/)![](https://hackernoon.com/hn-images/1*Vpog52LPyWoNk0zFP_NSlg.png)

**What is Web3.js?**

Web3.js is an interface you use to interact with blockchain. It is a javascript library which can be used to interact with an Ethereum node from your web based DApp. Remember each node on the network contains a copy of the blockchain. When you want to call a function on a smart contract, you need to query one of these nodes and tell it the address of the smart contract and the function you want to call.

Ethereum nodes only speak a language called _**JSON-RPC**_, which isn’t very human-readable. Luckily, Web3.js hides these nasty queries and gives you a more familiar javascript interface for example like this:

```text
MyTeam.methods.createRandomPerson("Vitalik Nakamoto").send({ from: "0xb60e8dd61c5d32be8058bb8eb970870f07233155", gas: "3000000" })
```

**First get started by including Web3 minified javascript in your project:** You can add Web3.js to your project using e.g `npm install web3.` Or you can simply download the minified `.js` file from [github](https://github.com/ethereum/web3.js/blob/1.0/dist/web3.min.js) and include it in your project:

`<script language="javascript" type="text/javascript" src="web3.min.js"></script>`

**Next get it it talking to the Blockchain using a Web3 Provider :**

> Setting a Web3 Provider in Web3.js tells our code **which node** we should be talking to handle our reads and writes.It’s kind of like setting the URL of the remote web server for your API calls in a traditional web app.

You could host your own Ethereum node as a provider. However, there’s a 3rd-party service that makes your life easier so you don’t need to maintain your own Ethereum node — _**Infura**_.

**Infura \(Infrastructure-as-a-Service\)**

[https://infura.io/](https://infura.io/)![Image result for infura](https://hackernoon.com/hn-images/1*cQ0o7-SWSTqtvF3NwE8gGw.png)Scalable Blockchain Infrastructure

**An IaaS product \(from Consensys\) offering developers a suite of tools to connect their apps to the Ethereum network and other decentralized platforms**. [Metamask](http://metamask.io/),[CryptoKitties](http://cryptokitties.co/), [UJO](http://ujomusic.com/), [uPort](http://uport.me/) — all utilize Infura’s APIs to connect their applications to the Ethereum network. It provides the infrastructure required to handle short-term spikes and longer-term scaling solutions. It includes an easy to use API and developer tools to provide secure, reliable, and scalable access to Ethereum and IPFS.

You can set up Web3 to use Infura as your web3 provider as simply as this:

```text
var web3 = new Web3(new Web3.providers.WebsocketProvider("wss://mainnet.infura.io/ws"));
```

**Geth \(Golang-Ethereum\)**

[https://github.com/ethereum/go-ethereum](https://github.com/ethereum/go-ethereum)![](https://hackernoon.com/hn-images/1*Y6cxrtfcRPfsD8zkwggD7w.png)

Geth is the official client software provided by the [Ethereum Foundation](http://ethereum.org/). It is written in the Go programming language. **Is the core application that will connect you to a blockchain.** It can also start a new local or test blockchain, create a contract, mine ether, and much more. It is the **main Ethereum command line client**.

It is the entry point into the Ethereum network \(main-, test- or private net\), capable of running as a **full node** \(default\) archive node \(retaining all historical state\) or a **light node** \(retrieving data live\).

It can be used by other processes as a **gateway into the Ethereum network via JSON RPC endpoints** exposed on top of **HTTP, WebSocket and/or IPC transports.** Geth can do anything Mist can do plus some important functionality like serving as an RPC endpoint to connect to the blockchain over http. It has 3 main components worth understanding:

**A. Geth Client Daemon —** It connects to other clients \(nodes\) in the network and downloads a copy of the blockchain. It will constantly communicate with other nodes to keep it’s copy of the blockchain up to date. It also has the ability to mine blocks and add transactions to the blockchain, validate the transactions in the block and also execute the transactions.

**B. Geth Console —** This is a command line tool which lets you connect to your running node and perform various actions like create and manage accounts, query the blockchain, sign and submit transactions to the blockchain and so on.

**C. Mist Browser —** As we have seen earlier this is a desktop application used to communicate with your node. Anything you can do using the geth console can be accomplished through this Graphical User Interface.

**Truffle Framework**

[https://github.com/ConsenSys/truffle](https://github.com/ConsenSys/truffle)![](https://hackernoon.com/hn-images/1*w7AnqwhWpufLUWtoMhRkfA.png)

Just like you have frameworks for web application development such as Ruby on Rails, Python/Django etc, Truffle is the most popular framework used to develop dapps. It abstracts away lot of the complexities of compiling and deploying your contract on the blockchain.

Truffle is the gold standard for providing the building blocks to quickly create, compile, deploy, and test blockchain apps. It is a **development environment, testing framework and asset pipeline** for blockchains using the Ethereum Virtual Machine \(EVM\)**.** In other words, it helps you develop smart contracts, publish them, and test them, among other things.

With Truffle, you get:

* Built-in smart contract compilation, linking and deployment.
* Automated contract testing for rapid development.
* Extensible deployment framework.
* Deploying to any number of public & private networks.
* Package management with EthPM & NPM.
* Interactive console for direct contract communication.
* Configurable build pipeline.

**Truffle has a built-in personal blockchain that can be used for testing.**

This blockchain is local to your system and does not interact with the main Ethereum network. You can create this blockchain and interact with it using the command **truffle develop.** This will spawn a development blockchain locally on port `9545.`

**Ganache \(TestRPC\) — A Personal Test Blockchain**

[https://truffleframework.com/ganache](https://truffleframework.com/ganache)![](https://hackernoon.com/hn-images/1*A4klHw03eZ5BAqN1whxeCA.png)

Test your code in a Test Blockchain — Since writing to the blockchain costs Ether, it’s a good idea to test out your smart contracts in a test blockchain spending test Ether.

While Truffle Develop is an all-in-one personal blockchain and console, you can also use [Ganache](https://truffleframework.com/ganache), a desktop application, to launch your personal blockchain. Ganache can be a more easy-to-understand tool for those new to Ethereum and the blockchain, as it displays much more information up-front. The only extra step required is editing the Truffle configuration file to point to the Ganache instance.![](https://hackernoon.com/hn-images/0*nZbbKj4jd8WE-djT.png)

Ganache is available for Windows, Mac, and Linux. **It was called TestRPC before but it was renamed upon integration** **into the Truffle Suite.**

**Parity \(Client written in Rust\)**

[https://github.com/paritytech/parity](https://github.com/paritytech/parity)![](https://hackernoon.com/hn-images/1*Ic5j6kR7sMisQ9L-bcMD1A.jpeg)

**Parity is an ethereum client written in the low level language Rust.**

It is an unofficial client and is maintained by a company called [Parity Inc](https://parity.io/). Formed by Dr. Gavin Wood, the former CTO of Ethereum, this client is a fast, lightweight way to run an Ethereum node. Parity is a big upgrade from Geth. It was written from the ground-up with an emphasis on efficiency. All important logic is 100% unit-tested, all public APIs documented, all the code reviewed by multiple peers.

**Open Zeppelin \(Writing Safe Smart Contracts\)**

[https://github.com/OpenZeppelin/zeppelin-solidity](https://github.com/OpenZeppelin/zeppelin-solidity)![](https://hackernoon.com/hn-images/1*pSH151JoQ-hPRghMCA18EA.png)

When you’re writing a smart contract that holds _other people’s money_ you want to make sure it’s secure. Zeppelin is library for writing secure contracts. Especially easy when you’re already working with truffle.

**IPFS \(Inter Planetary File System\)**

[https://ipfs.io/](https://ipfs.io/)

IPFS is a decentralized storage system. It is not related to Ethereum directly but can be integrated with Ethereum. You can read about the differences and similarities between Swarm and IPFS here: [https://github.com/ethersphere/go-ethereum/wiki/IPFS-&-SWARM](https://github.com/ethersphere/go-ethereum/wiki/IPFS-&-SWARM).

**TLDR:** IPFS is much further along in code maturity, scaling, adoption, community engagement and interaction with a dedicated developer community.![](https://hackernoon.com/hn-images/0*hxM4ArofCIcGRuuH.png)

#### The Bottom Line

I hope you enjoyed this introduction to the best development tools available today for creating DApps and interacting with the Ethereum Blockchain.

In the next post we will be setting up the tooling and environments necessary and **begin our journey of coding in Ethereum using Solidity** as you continue on your path from being a Web Developer **to becoming a Web 3.0 Blockchain Engineer**.

In the meantime you can head over to [**www.optimizme.com**](http://optimizme.com/) ****and sign up for the beta platform we are building to help harness the power of accelerated learning to upskill for the new economy.

{% embed url="https://hackernoon.com/the-ultimate-guide-to-ethereum-developer-tools-7feb53f9d953" %}








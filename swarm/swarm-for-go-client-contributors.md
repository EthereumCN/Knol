# 面向Go语言客户端

## 6. Swarm for Go-Client Contributors

All you need to know to contribute to the Swarm Go-Client implementation.

Source code is located at [https://github.com/ethersphere/swarm/](https://github.com/ethersphere/swarm/).

### 6.1. Introduction

Swarm nodes can also connect with one \(or several\) Ethereum blockchains for domain name resolution and one ethereum blockchain for bandwidth and storage compensation.

Nodes running the same network id are supposed to connect to the same blockchain for payments. A Swarm network is identified by its network id which is an arbitrary integer.

Swarm supports encryption. Upload of unencrypted sensitive and private data is highly discouraged as **there is no way to undo an upload**. Users should refrain from uploading illegal, controversial or unethical content.

Always use encryption for sensitive content. For encrypted content, uploaded data is ‘protected’, i.e. only those that know the reference to the root chunk \(the Swarm hash of the file\) as well as the decryption key can access the content. Since publishing this reference \(on ENS or with Feeds\) requires an extra step, users are mildly protected against careless publishing as long as they use encryption. Even though there is no guarantees for removal, unaccessed content that is not explicitly insured will eventually disappear from the Swarm, as nodes will be incentivised to garbage collect it in case of storage capacity limits.

Swarm is a [Persistent Data Structure](https://en.wikipedia.org/wiki/Persistent_data_structure), therefore there is no notion of delete/remove action in Swarm. This is because content is disseminated to Swarm nodes who are incentivised to serve it.

Important

It is not possible to **delete or remove** content uploaded to Swarm. **Always encrypt** sensitive content using the integrated Swarm encryption.

### 6.2. Reporting a bug and contributing

Issues are tracked on github and github only. Swarm related issues and PRs have labels prefixed with _swarm_:

* [https://github.com/ethersphere/swarm/issues](https://github.com/ethersphere/swarm/issues)
* [Good first issues](https://github.com/ethersphere/swarm/issues?utf8=%E2%9C%93&q=is%3Aopen+is%3Aissue+label%3A%22good+first+issue%22)

Please include the commit and branch when reporting an issue.

Pull requests should by default commit on the master branch.

Prospective contributors please read the Contributing section from our readme: [https://github.com/ethersphere/swarm\#contributing](https://github.com/ethersphere/swarm#contributing).

### 6.3. High level component description

In this chapter we introduce the internal software architecture of the go swarm code. It is only a high level description of the most important components. The code is documented in finer detail as comments in the codebase itself.[![High level component architecture](https://swarm-guide.readthedocs.io/en/latest/_images/high-level-components.svg)](https://swarm-guide.readthedocs.io/en/latest/_images/high-level-components.svg)

#### 6.3.1. Interfaces to Swarm

There are currently three entry points for communicating with a swarm node, and there is a command line interface._Wire Protocol_The p2p entry point - this is the way the peers talk to each other. Protocol structure adheres to the devp2p standard and the transport is being done RLPx over TCP._HTTP Proxy_The user entry point to Swarm. User operations over dapps and CLI that interact with Swarm are proxied through the HTTP interface. The API exposes methods to interact with content on Swarm._RPC_Another user interface mainly used for development purposes. The user facing side of this is to be deprecated._CLI_The CLI is a wrapper for the HTTP interface allowing users easy access to basic up-download functionality, content management, and it also implements some administrative tasks.

#### 6.3.2. Structural components and key processes

_Chunker_When a file is submitted to the system, the input data stream is then transformed into chunks, encrypted, then hashed and stored. This results in a single root chunk reference of the data._Syncing process_Syncing is the process that deals with changes in the network when nodes join and leave, and when new content is uploaded. Push and pull syncing work together to get chunks to where they are supposed to be stored \(to the local neighbourhood where they belong\)._Push Sync_A process initiated by the uploader of content to make sure that the chunks get to the areas in the network from which they can be retrieved. Combining push-sync with tags allows users to track the status of their uploads. Push syncing is initiated upon upload of new content._Pull Sync_Pull syncing is initiated by all participating nodes in order to fill up their local storage allocation in order to keep redundancy by replicating the local storage of their neighbouring peers. Pull syncing caters the need for chunk propagation towards the nearest neighbourhood. This process is responsible for maintaining a minimal redundancy level for the stored chunks.

#### 6.3.3. Storage module

_LocalStore_Provides persistent storage on each node. Provides indexes, iterators and metric storage to other components._NetStore_Extends local storage with network fetching. The net store is exposed internally between the APIs in order to transparently resolve any chunk dependencies that might be needed to be satisfied from the network in order to accomodate different operations on content._Kademlia_Kademlia in the sense of Swarm has two different meanings. Firstly, Kademlia is the type of the network topology that Swarm builds upon. Secondly, within the Swarm codebase the component which manages the connections to peers over the devp2p network in order to form the Kademlia topology. Peers exchange the neccessary information about each other through a discovery protocol \(which does not build upon the devp2p discovery protocol\)._Feeds_Swarm Feeds allows a user to build an update feed about a particular topic without resorting to ENS on each update. The update scheme is built on swarm chunks with chunk keys following a predictable, versionable pattern. A Feed is defined as the series of updates of a specific user about a particular topic.

#### 6.3.4. Communication layer

_PSS_A messaging subsystem which builds upon the Kademlia topology to provide application level messaging \(eg. chat dapps\) and is also used for Push-sync.

### 6.4. Simulation Framework

Find everything you need to run Swarm network simulations for testing and debugging.


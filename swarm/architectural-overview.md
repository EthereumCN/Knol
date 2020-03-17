---
description: 'https://swarm-guide.readthedocs.io/en/latest/architecture.html'
---

# 架构概述

## 2.1. 前言

Swarm定义了三个关键概念：

**Chunk**：大小有限 \(最大4K\) 的数据块，Swarm中存储和检索的基本单位。网络层只识别chunk，没有文件概念。

**Reference**：文件的唯一标识符，允许客户端检索和访问内容。对于未加密内容，文件reference是数据的加密哈希，并作为其内容地址。该哈希长度为32字节，序列化为64个十六进制字节。如果是加密文件，则包含两个等长的部分：前32个字节是内容地址，后32个字节是解密密钥（共64字节），序列化为128个十六进制字节。

**Manifest**：描述文件集合的数据结构。Manifest指定路径和相应的内容哈希，以允许基于URL的内容检索。[BZZ URL方案](https://swarm-guide.readthedocs.io/en/latest/features/bzz.html#bzz-url-schemes)假定域名中的引用内容是manifest，并呈现路径与请求路径相匹配的内容条目。Manifest也可以映射到文件系统目录树 \(directory tree\)，该目录树允上传和下载目录。最后，manifest也可以被视作是索引，因此它可以用于实现简单的键值存储或数据库索引。这提供了虚拟主机功能 \(virtual hosting\)，可以存储整个目录、web3网站或原始数据结构，类似于web2.0，使集中托管成为可能。

![](../.gitbook/assets/dapp-page.svg)

Swarm定义了文件的特定标识符 \(identifier \)。Reference的标识符部分用作内容的检索地址。该地址必须是：

* 无冲突（两个不同的数据块不会映射到同一个标识符）
* 确定的（同样的内容始终对应同样的标识符）
* 均匀分布

Swarm中标识符的选择根据[Swarm Hash](https://swarm-guide.readthedocs.io/en/latest/architecture.html#swarm-hash)中所描述的分层Swarm哈希。以上地址属性允许我们将哈希视作能够找到对应内容的地址。由于哈希要满足“无冲突”特点，所以会被绑定到一个特定版本的内容。因此在很大程度上，哈希寻址是不可篡改的，甚至不能代表变化的内容：“内容更改会引起哈希变动”。

然而网络用户习惯于使用多元资源，查找域名时看到最新版本的“网站”。以太坊域名服务 \(ENS\) 和订阅服务 \(Feed\) 使多元资源成为可能。 ENS是以太坊区块链上的智能合约，域名所有者可以对其域名注册内容。通过使用ENS进行域名解析，URL方案可基于助记符提供内容检索，与万维网DNS相似，不同的是没有服务器。 订阅服务 \(Feed\) 则是一种链下解决方案，用于提供资源更新，与ENS相比更便宜，更新更快，但是愿意付费的第三方可以将更新合并到ENS上。

就像Swarm中的内容使用32字节的哈希值寻址一样，网络中的每个Swarm节点也都与32字节的哈希地址相关联。所有Swarm节点都有自己的基址 \(base address\)，该地址由以太坊帐户公钥的哈希 \(Keccak 256bit SHA3\) 派生而来：

| 注意 |
| :--- |
| Swarm节点地址 = sha3 \(以太坊账户公钥\)，即swarm节点基址。这些节点地址在相同的数据地址空间中进行定位。 |

当在Swarm中上传资源时，内容会被分割为数据块 \(chunks\)。通过由内容派生出的确定地址（chunk哈希），每个chunk都可以被访问。数据块的references被打包到一个chunk中，具有相应的哈希。通过这种方式，内容被映射到默克尔树中。这种分层的Swarm哈希结构允许对一条内容中的chunks进行默克尔证明，从而能够对（大）文件进行完整的随机访问（例如允许在流视频中安全跳过或在数据库文件中查找密钥） 。

Swarm实现了分布式原图像存档 \(distributed preimage archive\)，这实际上是一种特定类型内容寻址的分布式哈希表，其中距离某个chunk地址最近的节点不仅提供有关内容的信息，还对数据进行托管。

两者的可行性取决于以下假设：任何节点（上传者/请求者）都可以“访问”任何其他节点（存储者）。这个假设可以通过特殊的网络拓扑结构 \(kademlia\) 来实现，该拓扑结构可以保证在网络规模中存在对数的最大数量转发跃点。

| 注意 |
| :--- |
| Swarm中不存在删除/移除功能。数据一旦上传，则无法撤回。 |

节点缓存在检索时传递的内容，从而形成自动缩放的弹性云：流行（经常访问）的内容被复制到整个网络中，从而减少检索延迟。缓存还可以最大程度地利用资源，因为节点将传输的数据存储到专用空间。如果达到容量上限，垃圾回收过程将清除访问最少的chunk。结果就是，访问最少的内容最终被删除。存储保险（尚未实施）将为用户提供安全保障，以防止重要内容被清除。

## 2.2. 叠加网络

#### 2.2.1. 对数距离

两个等长字节序列x和y的距离度量 MSB\(x,y\)MSB\(x,y\) 是xXORy（按位异或）的二进制整数转换的值。二进制类型转换为大端字节序：最高有效位在先 \(= MSB\)。

邻近度 \(x,y\) 是MSB距离的离散对数缩放，定义为距离的以2为底的对数的整数部分的倒数。通过对xXORy \(最远为0，最近255，自身为256\) 的 \(MSB\) 二进制表示形式中的共用前导零进行计数来计算。

![&#x8DDD;&#x79BB;&#x548C;&#x90BB;&#x8FD1;&#x5EA6;](https://swarm-guide.readthedocs.io/en/latest/_images/distance.svg)

按相对于固定点x的邻近度顺序将空间中的点（长度为nn的字节序列）分类为bin。每个项目与x的距离最多为前一个bin中项目的x的一半。给定均匀分布项目的样本（任意序列上的哈希函数），邻近度范围映射到基数为负指数范围的一系列子集

它还具有以下特性：属于同一个bin的任何两个地址之间的距离最多为与x的距离的一半。

如果我们将bin中项目的随机样本视为网络中节点的互连，则相对邻近度可以用作图的遍历的本地决策基础，目标是在两点之间找到一条路线。由于在每个跃点上，有限距离减半，只要每个相关的bin都不为空，就可以保证从一个节点到另一个节点所需的跃点数具有恒定的最大限制。

![Swarm&#x4E2D;&#x7684;Kademlia&#x62D3;&#x6251;](https://swarm-guide.readthedocs.io/en/latest/_images/topology.svg)

#### 2.2.2. Kademlia拓扑

Swarm使用以太坊devp2p rlpx套件作为底层网络的传输层。这种不常见的变体允许半稳定的节点连接（通过TCP）以及经验证和加密的同步数据流。

我们说一个节点具有kademlia连接性，如果（1）对于每个邻近度，直到（但不包括）某个最大值d（饱和深度），该节点至少与一个节点相连；并且（2）连接了与其自身邻近度大于或等于d的所有节点。

如果已连接子图 \(subgraph\) 的每个点都具有kademlia连接性，那么我们说该子图具有kademlia拓扑。在具有kademlia拓扑的图中（1）存在任何两点之间的路径，（2）只能通过每个跃点上的本地判定找到，并且（3）保证终止深度不超过目标深度加一。

给定一组在空间中均匀分布的点（例如应用于Swarm数据的哈希函数结果），邻近度bins映射到基数为负指数范围的一系列子集，即PO bin 0有任意随机样本的一半点数，PO bin 1有四分之一，PO bin 2有八分之一，依此类推。N节点网络中饱和深度的期望值为log2\(N\)。最后一个bin可以合并所有比深度更深的bin，称为最邻近bin。

Swarm节点由Swarm基本帐户的以太坊地址的哈希标识。这用作它们的覆盖地址，根据这些地址计算邻近度顺序bins。连接到节点的节点定义了另一个实时kademlia表，其中图形边缘表示devp2p rlpx连接。

![](../.gitbook/assets/kademlia.svg)

如果集合中的每个节点都有一个已连接节点的饱和kademlia表，则节点“实时连接”图具有kademlia拓扑。Kademlia图的属性可用于在节点之间通过覆盖寻址路由消息。在转递kademlia网络中，如果存在从发送节点到目标节点的路径（可通过该路径中继消息），那么该消息可路由 \(routable\)。在具有kademlia拓扑的成熟子网中，每条消息都是可路由的。很大一部分节点不稳定在线，将一部分已连接的节点保留在其PO bins中，即使相关节点掉线，每个节点在任何时间点转发消息的几率也增加了。

#### 2.2.3. Bootstrapping及discovery

假定加入去中心化网络的节点缺乏经验，即通过单个已知节点潜在连接。由于这个原因，bootstrapping将需要包括一个discovery组件，借助于该组件，节点之间可以相互交换信息。

协议如下：最初，每个节点的饱和深度为零。节点会随着饱和深度的变化不断通知其连接的节点。当某个节点建立了新连接，如果它和相应节点的接近程度不低于对方告知的饱和深度（即如果它们足够接近），则它可以将此新连接通知给每个节点。通知始终发送给与该新连接共享PO bin的每个节点，包含完整的覆盖地址和底层地址信息。不希望中继消息也不打算建立kademlia的轻节点会被排除。

当节点收到新节点地址的通知时，会将其存储在已知节点的kademlia表中。在收听传入的连接时，还会主动尝试连接到节点以实现饱和：尝试连接到N个最近节点的PO边界内（最近邻点深度）的每个已知节点，并且（2）尝试通过将正常节点填充到bin中以达到最近邻点深度。为了最有效地满足（1），节点会在任何时间尝试连接到最需要的节点。低（远）bins比高（近）bins更重要，因为其容量更大。将一个新节点添加到非空bin，比起将一个节点加入一个空bin更为重要，因为能够更快使kademlia达到饱和。因此，该协议使用自下而上、深度优先的策略来选择要连接的节点。已尝试但无法建立连接的节点将使用指数退避重试（即在每次尝试后时间间隔翻倍）。经过一定次数的尝试后，若仍然失败将不再考虑这些节点。

连接足够数量的节点后，bin达到饱和，bin饱和深度增加。如果节点当前的饱和深度发生变化，节点则将继续向其他节点同胞。随着其饱和深度的增加，节点收到的新节点的通知会越来越少（因为它们已经知道邻近节点）。一旦节点找到了所有最近节点并且素有bins都达到饱和，那么就不会再有新节点通知。因此如果某个节点（在一段时间内）未收到新节点，则该节点可以得出饱和的kademlia状态。该节点不需要知道网络中的节点数量。实际上，在节点停止接收新节点地址之后某时，可以根据深度有效估计网络大小（深度n表示$$2^n$$个节点）。

这样的网络可以轻松应用于转发式消息传递系统。 Swarm的PSS就是基于此。 Swarm还使用此网络来实现其存储解决方案。

## 2.3. 分布式原像存档

分布式哈希表 \(Distributed hash tables, DHT\) 利用覆盖网络来实现分布在节点上的键值存储。基本思想是将键空间映射到覆盖地址空间，并使用地址在键附近的节点查找有关信息。用于去中心化内容寻址存储的DHT通常会将内容指纹与可以提供该内容的节点 \(seeders\) 相关联。然而，我们可以直接使用相同的结构：不是将内容位置信息存储在最近地址（指纹）节点上，而是直接对内容本身进行存储。我们称这种结构为分布式原像存档（Distributed Preimage Archives, DPA）。

![Swarm&#x4E2D;&#x7684;DPA&#x53CA;&#x6570;&#x636E;&#x5757;](https://swarm-guide.readthedocs.io/en/latest/_images/dpa-chunking.svg)

DPA会考虑哪些节点存储什么内容，这也意味着一些限制：（1）节点之间的内容负载平衡是必需的，这一点可以通过将内容分割成同等大小的数据块 \(chunking\) 来实现； （2）必须有一个过程使数据块到达应该存储（同步）的位置； （3）由于节点在存储内容上没有发言权，因此应采用推诿 \(plausible deniability\) 进行度量。

该设计中的数据块检索的执行方式是：通过将检索请求从请求节点中继到存储节点，然后将检索到的数据块从存储节点传递回请求节点。

由于Swarm实现了DPA（超过4096字节的数据块），因此将检索请求中继到目标数据块地址等同于将请求传递到存储节点。转发kademlia能够将此类检索请求路由到数据块地址的附近。为了实现传递，我们只需要假设每个节点在转发检索请求时记住请求者。一旦请求到达存储节点，就可以开始传递内容，包括将数据块中继回请求者。

在这种情况下，如果检索请求可路由到最接近数据块地址的存储设备，并且从存储设备路由回请求者节点，则可以为节点检索数据块。检索成功与否取决于（1）查找此类路由策略的可用性以及（2）最近节点（同步）数据块的可用性。请求-传递往返的延迟取决于跃点数量和沿途每个节点的带宽质量。上传后可用性的延迟取决于同步协议的效率。

#### 2.3.1. 反复检索

如果最近节点是唯一的存储节点且退出，则无法检索内容。要应对这种情况，可以通过使一组邻近节点保留最近的每个数据块的副本。如果数据块可检索，并且在任何math：n-1负责节点离线后仍将保留，则将其称作“可反复检索度为math：n的数据块” \(redundantly retrievable of degree math:n\)。如果转发请求失败，可以重试或启动并发检索请求。如果存储节点发生故障，那么这两个后备选项不可用。因此反复检索非常重要。

完全相连的邻近节点区域被视为责任区 \(area of responsibility\)。如果数据块进入节点的责任范围内，则存储节点负责（存储）该数据块。然后，让我们假设：（1）沿稳定节点中继请求的转发策略，以及（2）一种存储策略，即邻近节点区域中（最少R个节点）的每个节点都要存储责任区内的所有数块。只要这些假设成立，即使R-1个存储节点同时离线也可以检索到每个数据块。至于第二点，我们仍需要假设邻近节点区域中的每个节点都能存储每个数据块。

关于反复检索的更多措施（例如[纠删编码](https://en.wikipedia.org/wiki/Erasure_code)）将在未来进行实现。

#### 2.3.2. Caching and purging Storage

Node synchronisation is the protocol that makes sure content ends up where it is queried. Since the Swarm has an address-key based retrieval protocol, content will be twice as likely be requested from a node that is one bit \(one proximity bin\) closer to the content’s address. What a node stores is determined by the access count of chunks: if we reach the capacity limit for storage the oldest unaccessed chunks are removed. On the one hand, this is backed by an incentive system rewarding serving chunks. This directly translates to a motivation, that a content needs to be served with frequency X in order to make storing it profitable. On the one hand , frequency of access directly translates to storage count. On the other hand, it provides a way to combine proximity and popularity to dictate what is stored.

Based on distance alone \(all else being equal, assuming random popularity of chunks\), a node could be expected to store chunks up to a certain proximity radius. However, it is always possible to look for further content that is popular enough to make it worth storing. Given the power law of popularity rank and the uniform distribution of chunks in address space, one can be sure that any node can expand their storage with content where popularity of a stored chunk makes up for their distance.

Given absolute limits on popularity, there might be an actual upper limit on a storage capacity for a single base address that maximises profitablity. In order to efficiently utilise excess capacity, several nodes should be run in parallel.

This storage protocol is designed to result in an autoscaling elastic cloud where a growth in popularity automatically scales. An order of magnitude increase in popularity will result in an order of magnitude more nodes actually caching the chunk resulting in fewer hops to route the chunk, ie., a lower latency retrieval.

#### 2.3.3. Synchronisation

Smart synchronisation is a protocol of distribution which makes sure that these transfers happen. Apart from access count which nodes use to determine which content to delete if capacity limit is reached, chunks also store their first entry index. This is an arbitrary monotonically increasing index, and nodes publish their current top index, so virtually they serve as timestamps of creation. This index helps keeping track what content to synchronise with a peer.

When two nodes connect and they engage in synchronisation, the upstream node offers all the chunks it stores locally in a datastream per proximity order bin. To receive chunks closer to a downstream than to the upstream, downstream peer subscribes to the data stream of the PO bin it belongs to in the upstream node’s kademlia table. If the peer connection is within nearest neighbour depth the downstream node subscribes to all PO streams that constitute the most proximate bin.

Nodes keep track of when they stored a chunk locally for the first time \(for instance by indexing them by an ever incrementing storage count\). The downstream peer is said to have completed _history syncing_ if it has \(acknowledged\) all the chunks of the upstream peer up from the beginning until the time the session started \(up to the storage count that was the highest at the time the session started\). Some node is said to have completed _session syncing_ with its upstream peer if it has \(acknowledged\) all the chunks of the upstream peer up since the session started.

In order to reduce network traffic resulting from receiving chunks from multiple sources, all store requests can go via a confirmation roundtrip. For each peer connection in both directions, the source peer sends an _offeredHashes_ message containing a batch of hashes offered to push to the recipient. Recipient responds with a _wantedHashes_.

![Syncing chunks in the Swarm network](https://swarm-guide.readthedocs.io/en/latest/_images/syncing-high-level.svg)

## 2.4. Data layer

There are 4 different layers of data units relevant to Swarm:

* _message_: p2p RLPx network layer. Messages are relevant for the devp2p wire protocols
* _chunk_: fixed size data unit of storage in the distributed preimage archive
* _file_: the smallest unit that is associated with a mime-type and not guaranteed to have integrity unless it is complete. This is the smallest unit semantic to the user, basically a file on a filesystem.
* _collection_: a mapping of paths to files is represented by the _swarm manifest_. This layer has a mapping to file system directory tree. Given trivial routing conventions, a url can be mapped to files in a standardised way, allowing manifests to mimic site maps/routing tables. As a result, Swarm is able to act as a webserver, a virtual cloud hosting service.

The actual storage layer of Swarm consists of two main components, the _localstore_ and the _netstore_. The local store consists of an in-memory fast cache \(_memory store_\) and a persistent disk storage \(_dbstore_\). The NetStore is extending local store to a distributed storage of Swarm and implements the _distributed preimage archive \(DPA\)_.

![High level storage layer in Swarm](https://swarm-guide.readthedocs.io/en/latest/_images/storage-layer.svg)

#### 2.4.1. Files

The _FileStore_ is the local interface for storage and retrieval of files. When a file is handed to the FileStore for storage, it chunks the document into a merkle hashtree and hands back its root key to the caller. This key can later be used to retrieve the document in question in part or whole.

The component that chunks the files into the merkle tree is called the _chunker_. Our chunker implements the _bzzhash_ algorithm which is parallellized tree hash based on an arbitrary _chunk hash_. When the chunker is handed an I/O reader \(be it a file or webcam stream\), it chops the data stream into fixed sized chunks. The chunks are hashed using an arbitrary chunk hash \(in our case the BMT hash, see below\). If encryption is used the chunk is encrypted before hashing. The references to consecutive data chunks are concatenated and packaged into a so called _intermediate chunk_, which in turn is encrypted and hashed and packaged into the next level of intermediate chunks. For unencrypted content and 32-byte chunkhash, the 4K chunk size enables 128 branches in the resulting Swarm hash tree. If we use encryption, the reference is 64-bytes, allowing for 64 branches in the Swarm hash tree. This recursive process of constructing the Swarm hash tree will result in a single root chunk, the chunk hash of this root chunk is the Swarm hash of the file. The reference to the document is the Swarm hash itself if the upload is unencrypted, and the Swarm hash concatenated with the decryption key of the rootchunk if the upload is encrypted.

When the FileStore is handed a reference for file retrieval, it calls the Chunker which hands back a seekable document reader to the caller. This is a _lazy reader_ in the sense that it retrieves parts of the underlying document only as they are being read \(with some buffering similar to a video player in a browser\). Given the reference, the FileStore takes the Swarm hash and using the NetStore retrieves the root chunk of the document. After decrypting it if needed, references to chunks on the next level are processed. Since data offsets can easily be mapped to a path of intermediate chunks, random access to a document is efficient and supported on the lowest level. The HTTP API offers range queries and can turn them to offset and span for the lower level API to provide integrity protected random access to files.

Swarm exposes the FileStore API via the [bzz-raw](https://swarm-guide.readthedocs.io/en/latest/features/bzz.html#bzz-raw) URL scheme directly on the HTTP local proxy server \(see [BZZ URL schemes](https://swarm-guide.readthedocs.io/en/latest/features/bzz.html#bzz-url-schemes) and API Reference\). This API allows file upload via POST request as well as file download with GET request. Since on this level the files have no mime-type associated, in order to properly display or serve to an application, the `content_type` query parameter can be added to the url. This will set the proper content type in the HTTP response.

#### 2.4.2. Manifests

The Swarm _manifest_ is a structure that defines a mapping between arbitrary paths and files to handle collections. It also contains metadata associated with the collection and its objects \(files\). Most importantly a manifest entry specifies the media mime type of files so that browsers know how to handle them. You can think of a manifest as \(1\) routing table, \(2\) an index or \(3\) a directory tree, which make it possible for Swarm to implement \(1\) web sites, \(2\) databases and \(3\) filesystem directories. Manifests provide the main mechanism to allow URL based addressing in Swarm. The domain part of the URL maps onto a manifest in which the path part of the URL is looked up to arrive at a file entry to serve.

Manifests are currently respresented as a compacted trie \([http://en.wikipedia.org/wiki/Trie](http://en.wikipedia.org/wiki/Trie)\) , with individual trie nodes serialised as json. The json structure has an array of _manifest entries_ minimally with a path and a reference \(Swarm hash address\). The path part is used for matching the URL path, the reference may point to an embedded manifest if the path is a common prefix of more than one path in the collection. When you retrieve a file by url, Swarm resolves the domain to a reference to a root manifest, which is recursively traversed to find the matching path.

The high level API to the manifests provides functionality to upload and download individual documents as files, collections \(manifests\) as directories. It also provides an interface to add documents to a collection on a path, delete a document from a collection. Note that deletion here only means that a new manifest is created in which the path in question is missing. There is no other notion of deletion in the Swarm. Swarm exposes the manifest API via the bzz URL scheme, see [BZZ URL schemes](https://swarm-guide.readthedocs.io/en/latest/features/bzz.html#bzz-url-schemes).

These HTTP proxy API is described in detail in the API Reference section.

Note

In POC4, json manifests will be replaced by a serialisation scheme that enables compact path proofs, essentially asserting that a file is part of a collection that can be verified by any third party or smart contract.

## 2.5. Components

In what follows we describe the components in more detail.

#### 2.5.1. Swarm Hash

Swarm Hash \(a.k.a. bzzhash\) is a [Merkle tree](http://en.wikipedia.org/wiki/Merkle_tree) hash designed for the purpose of efficient storage and retrieval in content-addressed storage, both local and networked. While it is used in Swarm, there is nothing Swarm-specific in it and the authors recommend it as a drop-in substitute of sequential-iterative hash functions \(like SHA3\) whenever one is used for referencing integrity-sensitive content, as it constitutes an improvement in terms of performance and usability without compromising security.

In particular, it can take advantage of parallelisation for faster calculation and verification, can be used to verify the integrity of partial content without having to transmit all of it \(and thereby allowing random access to files\). Proofs of security to the underlying hash function carry over to Swarm Hash.

Swarm Hash is constructed using any chunk hash function with a generalization of Merkle’s tree hash scheme. The basic unit of hashing is a _chunk_, that can be either a _data chunk_ containing a section of the content to be hashed or an _intermediate chunk_ containing hashes of its children, which can be of either variety.![A Swarm chunk consists of 4096 bytes of the file or a sequence of 128 subtree hashes](https://swarm-guide.readthedocs.io/en/latest/_images/chunk.png)

Hashes of data chunks are defined as the hashes of the concatenation of the 64-bit length \(in LSB-first order\) of the content and the content itself. Because of the inclusion of the length, it is resistant to [length extension attacks](http://en.wikipedia.org/wiki/Length_extension_attack), even if the underlying chunk hash function is not.

Intermediate chunks are composed of the hashes of the concatenation of the 64-bit length \(in LSB-first order\) of the content subsumed under this chunk followed by the references to its children \(reference is either a chunk hash or chunk hash plus decryption key for encrypted content\).

To distinguish between the two, one should compare the length of the chunk to the 64-bit number with which every chunk begins. If the chunk is exactly 8 bytes longer than this number, it is a data chunk. If it is shorter than that, it is an intermediate chunk. Otherwise, it is not a valid Swarm Hash chunk.

For the chunk hash we use a hashing algorithm based on a binary merkle tree over the 32-byte segments of the chunk data using a base hash function. Our choice for this base hash is the ethereum-wide used Keccak 256 SHA3 hash. For integrity protection the 8 byte span metadata is hashed together with the root of the BMT resulting in the BMT hash. BMT hash is ideal for compact solidity-friendly inclusion proofs.

#### 2.5.2. Chunker

_Chunker_ is the interface to a component that is responsible for disassembling and assembling larger data. More precisely _Splitter_ disassembles, while _Joiner_ reassembles documents.

Our Splitter implementation is the _pyramid_ chunker that does not need the size of the file, thus is able to process live capture streams. When _splitting_ a document, the freshly created chunks are pushed to the DPA via the NetStore and calculates the Swarm hash tree to return the _root hash_ of the document that can be used as a reference when retrieving the file.

When _joining_ a document, the chunker needs the Swarm root hash and returns a _lazy reader_. While joining, for chunks not found locally, network protocol requests are initiated to retrieve chunks from other nodes. If chunks are retrieved \(i.e. retrieved from memory cache, disk-persisted db or via cloud based Swarm delivery from other peers in the DPA\), the chunker then puts these together on demand as and where the content is being read.


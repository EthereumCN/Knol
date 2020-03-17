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

两者的可行性取决于以下假设：任何节点（上传者/请求者）都可以“访问”任何其他节点（存储者）。这个假设可以通过特殊的网络拓扑结构 \(kademlia\) 来实现，该拓扑结构可以保证在网络规模中存在对数的最多转发跳数。

| 注意 |
| :--- |
| Swarm中不存在删除/移除功能。数据一旦上传，则无法撤回。 |

节点缓存在检索时传递的内容，从而形成自动缩放的弹性云：流行（经常访问）的内容被复制到整个网络中，从而减少检索延迟。缓存还可以最大程度地利用资源，因为节点将传输的数据存储到专用空间。如果达到容量上限，垃圾回收过程将清除访问最少的chunk。结果就是，访问最少的内容最终被删除。存储保险（尚未实施）将为用户提供安全保障，以防止重要内容被清除。

## 2.2. Overlay network

#### 2.2.1. Logarithmic distance

The distance metric MSB\(x,y\)MSB\(x,y\) of two equal length byte sequences xx an yy is the value of the binary integer cast of xXORyxXORy \(bitwise xor\). The binary cast is big endian: most significant bit first \(=MSB\).

Proximity\(x,y\)Proximity\(x,y\) is a discrete logarithmic scaling of the MSB distance. It is defined as the reverse rank of the integer part of the base 2 logarithm of the distance. It is calculated by counting the number of common leading zeros in the \(MSB\) binary representation of xXORyxXORy \(0 farthest, 255 closest, 256 self\).

![Distance and Proximity](https://swarm-guide.readthedocs.io/en/latest/_images/distance.svg)

Taking the _proximity order_ relative to a fix point xx classifies the points in the space \(byte sequences of length nn\) into bins. Items in each are at most half as distant from xx as items in the previous bin. Given a sample of uniformly distributed items \(a hash function over arbitrary sequence\) the proximity scale maps onto series of subsets with cardinalities on a negative exponential scale.

It also has the property that any two addresses belonging to the same bin are at most half as distant from each other as they are from xx.

If we think of a random sample of items in the bins as connections in a network of interconnected nodes, then relative proximity can serve as the basis for local decisions for graph traversal where the task is to _find a route_ between two points. Since on every hop, the finite distance halves, as long as each relevant bin is non-empty, there is a guaranteed constant maximum limit on the number of hops needed to reach one node from the other.

![Kademlia topology in Swarm](https://swarm-guide.readthedocs.io/en/latest/_images/topology.svg)

#### 2.2.2. Kademlia topology

Swarm uses the ethereum devp2p rlpx suite as the transport layer of the underlay network. This uncommon variant allows semi-stable peer connections \(over TCP\), with authenticated, encrypted, synchronous data streams.

We say that a node has _kademlia connectivity_ if \(1\) it is connected to at least one node for each proximity order up to \(but excluding\) some maximum value dd \(called the _saturation depth_\) and \(2\) it is connected to all nodes whose proximity order relative to the node is greater or equal to dd.

If each point of a connected subgraph has kademlia connectivity, then we say the subgraph has _kademlia topology_. In a graph with kademlia topology, \(1\) a path between any two points exists, \(2\) it can be found using only local decisions on each hop and \(3\) is guaranteed to terminate in no more steps than the depth of the destination plus one.

Given a set of points uniformly distributed in the space \(e.g., the results of a hash function applied to Swarm data\) the proximity bins map onto a series of subsets with cardinalities on a negative exponential scale, i.e., PO bin 0 has half of the points of any random sample, PO bin 1 has one fourth, PO bin 2 one eighth, etc. The expected value of saturation depth in the network of NN nodes is log2\(N\)log2\(N\). The last bin can just merge all bins deeper than the depth and is called the _most proximate bin_.

Nodes in the Swarm network are identified by the hash of the ethereum address of the Swarm base account. This serves as their overlay address, the proximity order bins are calculated based on these addresses. Peers connected to a node define another, live kademlia table, where the graph edges represent devp2p rlpx connections.

![Kademlia table for a sample node in Swarm](https://swarm-guide.readthedocs.io/en/latest/_images/kademlia.svg)

If each node in a set has a saturated kademlia table of connected peers, then the nodes “live connection” graph has kademlia topology. The properties of a kademlia graph can be used for routing messages between nodes in a network using overlay addressing. In a _forwarding kademlia_ network, a message is said to be _routable_ if there exists a path from sender node to destination node through which the message could be relayed. In a mature subnetwork with kademlia topology every message is routable. A large proportion of nodes are not stably online; keeping several connected peers in their PO bins, each node can increase the chances that it can forward messages at any point in time, even if a relevant peer drops.

#### 2.2.3. Bootstrapping and discovery

Nodes joining a decentralised network are supposed to be naive, i.e., potentially connect via a single known peer. For this reason, the bootstrapping process will need to include a discovery component with the help of which nodes exchange information about each other.

The protocol is as follows: Initially, each node has zero as their saturation depth. Nodes keep advertising to their connected peers info about their saturation depth as it changes. If a node establishes a new connection, it notifies each of its peers about this new connection if their proximity order relative to the respective peer is not lower than the peer’s advertised saturation depth \(i.e., if they are sufficiently close by\). The notification is always sent to each peer that shares a PO bin with the new connection. These notification about connected peers contain full overlay and underlay address information. Light nodes that do not wish to relay messages and do not aspire to build up a healthy kademlia are discounted.

As a node is being notified of new peer addresses, it stores them in a kademlia table of known peers. While it listens to incoming connections, it also proactively attempts to connect to nodes in order to achieve saturation: it tries to connect to each known node that is within the PO boundary of N _nearest neighbours_ called _nearest neighbour depth_ and \(2\) it tries to fill each bin up to the nearest neighbour depth with healthy peers. To satisfy \(1\) most efficiently, it attempts to connect to the peer that is most needed at any point in time. Low \(far\) bins are more important to fill than high \(near\) ones since they handle more volume. Filling an empty bin with one peer is more important than adding a new peer to a non-empty bin, since it leads to a saturated kademlia earlier. Therefore the protocol uses a bottom-up, depth-first strategy to choose a peer to connect to. Nodes that are tried but failed to get connected are retried with an exponential backoff \(i.e., after a time interval that doubles after each attempt\). After a certain number of attempts such nodes are no longer considered.

After a sufficient number of nodes are connected, a bin becomes saturated, and the bin saturation depth can increase. Nodes keep advertising their current saturation depth to their peers if it changes. As their saturation depth increases, nodes will get notified of fewer and fewer new peers \(since they already know their neighbourhood\). Once the node finds all their nearest neighbours and has saturated all the bins, no new peers are expected. For this reason, a node can conclude a saturated kademlia state if it receives no new peers \(for some time\). The node does not need to know the number of nodes in the network. In fact, some time after the node stops receiving new peer addresses, the node can effectively estimate the size of the network from the depth \(depth nn implies 2n2n nodes\)

Such a network can readily be used for a forwarding-style messaging system. Swarm’s PSS is based on this. Swarm also uses this network to implement its storage solution.

## 2.3. Distributed preimage archive

_Distributed hash tables_ \(DHTs\) utilise an overlay network to implement a key-value store distributed over the nodes. The basic idea is that the keyspace is mapped onto the overlay address space, and information about an element in the container is to be found with nodes whose address is in the proximity of the key. DHTs for decentralised content addressed storage typically associate content fingerprints with a list of nodes \(seeders\) who can serve that content. However, the same structure can be used directly: it is not information about the location of content that is stored at the node closest to the address \(fingerprint\), but the content itself. We call this structure _distributed preimage archive_ \(DPA\).

![The DPA and chunking in Swarm](https://swarm-guide.readthedocs.io/en/latest/_images/dpa-chunking.svg)

A DPA is opinionated about which nodes store what content and this implies a few more restrictions: \(1\) load balancing of content among nodes is required and is accomplished by splitting content into equal sized chunks \(_chunking_\); \(2\) there has to be a process whereby chunks get to where they are supposed to be stored \(_syncing_\); and \(3\) since nodes do not have a say in what they store, measures of _plausible deniability_ should be employed.

Chunk retrieval in this design is carried out by relaying retrieve requests from a requestor node to a storer node and passing the retrieved chunk from the storer back to the requestor.

Since Swarm implements a DPA \(over chunks of 4096 bytes\), relaying a retrieve request to the chunk address as destination is equivalent to passing the request towards the storer node. Forwarding kademlia is able to route such retrieve requests to the neighbourhood of the chunk address. For the delivery to happen we just need to assume that each node when it forwards a retrieve request, remembers the requestors. Once the request reaches the storer node, delivery of the content can be initiated and consists in relaying the chunk data back to the requestor\(s\).

In this context, a chunk is retrievable for a node if the retrieve request is routable to the storer closest to the chunk address and the delivery is routable from the storer back to the requestor node. The success of retrievals depends on \(1\) the availability of strategies for finding such routes and \(2\) the availability of chunks with the closest nodes \(syncing\). The latency of request–delivery roundtrips hinges on the number of hops and the bandwidth quality of each node along the way. The delay in availability after upload depends on the efficiency of the syncing protocol.

#### 2.3.1. Redundancy

If the closest node is the only storer and drops out, there is no way to retrieve the content. This basic scenario is handled by having a set of nearest neighbours holding replicas of each chunk that is closest to any of them. A chunk is said to be _redundantly retrievable_ of degree math:n if it is retrievable and would remain so after any math:n-1 responsible nodes leave the network. In the case of request forwarding failures, one can retry, or start concurrent retrieve requests. Such fallback options are not available if the storer nodes go down. Therefore redundancy is of major importance.

The area of the fully connected neighbourhood defines an _area of responsibility_. A storer node is responsible for \(storing\) a chunk if the chunk falls within the node’s area of responsibility. Let us assume, then, \(1\) a forwarding strategy that relays requests along stable nodes and \(2\) a storage strategy that each node in the nearest neighbourhood \(of mimimum R peers\) stores all chunks within the area of responsibility. As long as these assumptions hold, each chunk is retrievable even if R−1R−1 storer nodes drop offline simultaneously. As for \(2\), we still need to assume that every node in the nearest neighbour set can store each chunk.

Further measures of redundancy, e.g. [Erasure coding](https://en.wikipedia.org/wiki/Erasure_code), will be implemented in the future.

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


# 架构概述

## Architectural Overview

### 2.1. Preface

Swarm defines 3 crucial notions:_chunk_Chunks are pieces of data of limited size \(max 4K\), the basic unit of storage and retrieval in the Swarm. The network layer only knows about chunks and has no notion of file or collection._reference_A reference is a unique identifier of a file that allows clients to retrieve and access the content. For unencrypted content the file reference is the cryptographic hash of the data and serves as its content address. This hash reference is a 32 byte hash, which is serialised with 64 hex bytes. In case of an encrypted file the reference has two equal-length components: the first 32 bytes are the content address of the encrypted asset, while the second 32 bytes are the decryption key, altogether 64 bytes, serialised as 128 hex bytes._manifest_A manifest is a data structure describing file collections; they specify paths and corresponding content hashes allowing for URL based content retrieval. The [BZZ URL schemes](https://swarm-guide.readthedocs.io/en/latest/features/bzz.html#bzz-url-schemes) assumes that the content referenced in the domain is a manifest and renders the content entry whose path matches the one in the request path. Manifests can also be mapped to a filesystem directory tree, which allows for uploading and downloading directories. Finally, manifests can also be considered indexes, so it can be used to implement a simple key-value store, or alternatively, a database index. This offers the functionality of _virtual hosting_, storing entire directories, web3 websites or primitive data structures; analogous to web2.0, with centralized hosting taken out of the equation.[![Example of how Swarm could serve a web page](https://swarm-guide.readthedocs.io/en/latest/_images/dapp-page.svg)](https://swarm-guide.readthedocs.io/en/latest/_images/dapp-page.svg)

In this guide, content is understood very broadly in a technical sense denoting any blob of data. Swarm defines a specific identifier for a file. This identifier part of the reference serves as the retrieval address for the content. This address needs to be

* collision free \(two different blobs of data will never map to the same identifier\)
* deterministic \(same content will always receive the same identifier\)
* uniformly distributed

The choice of identifier in Swarm is the hierarchical Swarm hash described in [Swarm Hash](https://swarm-guide.readthedocs.io/en/latest/architecture.html#swarm-hash). The properties above allow us to view hashes as addresses at which content is expected to be found. Since hashes can be assumed to be collision free, they are bound to one specific version of a content. Hash addressing is therefore immutable in the strong sense that you cannot even express mutable content: “changing the content changes the hash”.

Users of the web, however, are accustomed to mutable resources, looking up domains and expect to see the most up to date version of the ‘site’. Mutable resources are made possible by the ethereum name service \(ENS\) and Feeds. The ENS is a smart contract on the ethereum blockchain which enables domain owners to register a content reference to their domain. Using ENS for domain name resolution, the url scheme provides content retrieval based on mnemonic \(or branded\) names, much like the DNS of the world wide web, but without servers. Feeds is an off-chain solution for communicating updates to a resource, it offers cheaper and faster updates than ENS, yet the updates can be consolidated on ENS by any third party willing to pay for the transaction.

Just as content in Swarm is addressed with a 32-byte hash, so is every Swarm node in the network associated with a 32-byte hash address. All Swarm nodes have their own _base address_ which is derived as the \(Keccak 256bit SHA3\) hash of the public key of an ethereum account:

Note

Swarm node address = sha3\(ethereum account public key\) - the so called _swarm base account_ of the node. These node addresses define a location in the same address space as the data.

When content is uploaded to Swarm it is chopped up into pieces called chunks. Each chunk is accessed at the address deterministically derived from its content \(using the chunk hash\). The references of data chunks are themselves packaged into a chunk which in turn has its own hash. In this way the content gets mapped into a merkle tree. This hierarchical Swarm hash construct allows for merkle proofs for chunks within a piece of content, thus providing Swarm with integrity protected random access into \(large\) files \(allowing for instance skipping safely in a streaming video or looking up a key in a database file\).

Swarm implements a _distributed preimage archive_, which is essentially a specific type of content addressed distributed hash table, where the node\(s\) closest to the address of a chunk do not only serve information about the content but actually host the data.

The viability of both hinges on the assumption that any node \(uploader/requester\) can ‘reach’ any other node \(storer\). This assumption is guaranteed with a special _network topology_ \(called _kademlia_\), which guarantees the existence as well a maximum number of forwarding hops logarithmic in network size.

Note

There is no such thing as delete/remove in Swarm. Once data is uploaded there is no way to revoke it.

Nodes cache content that they pass on at retrieval, resulting in an auto scaling elastic cloud: popular \(oft-accessed\) content is replicated throughout the network decreasing its retrieval latency. Caching also results in a _maximum resource utilisation_ in as much as nodes will fill their dedicated storage space with data passing through them. If capacity is reached, least accessed chunks are purged by a garbage collection process. As a consequence, unpopular content will end up getting deleted. Storage insurance \(yet to be implemented\) will offer users a secure guarantee to protect important content from being purged.

### 2.2. Overlay network

#### 2.2.1. Logarithmic distance

The distance metric MSB\(x,y\)MSB\(x,y\) of two equal length byte sequences xx an yy is the value of the binary integer cast of xXORyxXORy \(bitwise xor\). The binary cast is big endian: most significant bit first \(=MSB\).

Proximity\(x,y\)Proximity\(x,y\) is a discrete logarithmic scaling of the MSB distance. It is defined as the reverse rank of the integer part of the base 2 logarithm of the distance. It is calculated by counting the number of common leading zeros in the \(MSB\) binary representation of xXORyxXORy \(0 farthest, 255 closest, 256 self\).![Distance and Proximity](https://swarm-guide.readthedocs.io/en/latest/_images/distance.svg)

Taking the _proximity order_ relative to a fix point xx classifies the points in the space \(byte sequences of length nn\) into bins. Items in each are at most half as distant from xx as items in the previous bin. Given a sample of uniformly distributed items \(a hash function over arbitrary sequence\) the proximity scale maps onto series of subsets with cardinalities on a negative exponential scale.

It also has the property that any two addresses belonging to the same bin are at most half as distant from each other as they are from xx.

If we think of a random sample of items in the bins as connections in a network of interconnected nodes, then relative proximity can serve as the basis for local decisions for graph traversal where the task is to _find a route_ between two points. Since on every hop, the finite distance halves, as long as each relevant bin is non-empty, there is a guaranteed constant maximum limit on the number of hops needed to reach one node from the other.![Kademlia topology in Swarm](https://swarm-guide.readthedocs.io/en/latest/_images/topology.svg)

#### 2.2.2. Kademlia topology

Swarm uses the ethereum devp2p rlpx suite as the transport layer of the underlay network. This uncommon variant allows semi-stable peer connections \(over TCP\), with authenticated, encrypted, synchronous data streams.

We say that a node has _kademlia connectivity_ if \(1\) it is connected to at least one node for each proximity order up to \(but excluding\) some maximum value dd \(called the _saturation depth_\) and \(2\) it is connected to all nodes whose proximity order relative to the node is greater or equal to dd.

If each point of a connected subgraph has kademlia connectivity, then we say the subgraph has _kademlia topology_. In a graph with kademlia topology, \(1\) a path between any two points exists, \(2\) it can be found using only local decisions on each hop and \(3\) is guaranteed to terminate in no more steps than the depth of the destination plus one.

Given a set of points uniformly distributed in the space \(e.g., the results of a hash function applied to Swarm data\) the proximity bins map onto a series of subsets with cardinalities on a negative exponential scale, i.e., PO bin 0 has half of the points of any random sample, PO bin 1 has one fourth, PO bin 2 one eighth, etc. The expected value of saturation depth in the network of NN nodes is log2\(N\)log2\(N\). The last bin can just merge all bins deeper than the depth and is called the _most proximate bin_.

Nodes in the Swarm network are identified by the hash of the ethereum address of the Swarm base account. This serves as their overlay address, the proximity order bins are calculated based on these addresses. Peers connected to a node define another, live kademlia table, where the graph edges represent devp2p rlpx connections.[![Kademlia table for a sample node in Swarm](https://swarm-guide.readthedocs.io/en/latest/_images/kademlia.svg)](https://swarm-guide.readthedocs.io/en/latest/_images/kademlia.svg)

If each node in a set has a saturated kademlia table of connected peers, then the nodes “live connection” graph has kademlia topology. The properties of a kademlia graph can be used for routing messages between nodes in a network using overlay addressing. In a _forwarding kademlia_ network, a message is said to be _routable_ if there exists a path from sender node to destination node through which the message could be relayed. In a mature subnetwork with kademlia topology every message is routable. A large proportion of nodes are not stably online; keeping several connected peers in their PO bins, each node can increase the chances that it can forward messages at any point in time, even if a relevant peer drops.

#### 2.2.3. Bootstrapping and discovery

Nodes joining a decentralised network are supposed to be naive, i.e., potentially connect via a single known peer. For this reason, the bootstrapping process will need to include a discovery component with the help of which nodes exchange information about each other.

The protocol is as follows: Initially, each node has zero as their saturation depth. Nodes keep advertising to their connected peers info about their saturation depth as it changes. If a node establishes a new connection, it notifies each of its peers about this new connection if their proximity order relative to the respective peer is not lower than the peer’s advertised saturation depth \(i.e., if they are sufficiently close by\). The notification is always sent to each peer that shares a PO bin with the new connection. These notification about connected peers contain full overlay and underlay address information. Light nodes that do not wish to relay messages and do not aspire to build up a healthy kademlia are discounted.

As a node is being notified of new peer addresses, it stores them in a kademlia table of known peers. While it listens to incoming connections, it also proactively attempts to connect to nodes in order to achieve saturation: it tries to connect to each known node that is within the PO boundary of N _nearest neighbours_ called _nearest neighbour depth_ and \(2\) it tries to fill each bin up to the nearest neighbour depth with healthy peers. To satisfy \(1\) most efficiently, it attempts to connect to the peer that is most needed at any point in time. Low \(far\) bins are more important to fill than high \(near\) ones since they handle more volume. Filling an empty bin with one peer is more important than adding a new peer to a non-empty bin, since it leads to a saturated kademlia earlier. Therefore the protocol uses a bottom-up, depth-first strategy to choose a peer to connect to. Nodes that are tried but failed to get connected are retried with an exponential backoff \(i.e., after a time interval that doubles after each attempt\). After a certain number of attempts such nodes are no longer considered.

After a sufficient number of nodes are connected, a bin becomes saturated, and the bin saturation depth can increase. Nodes keep advertising their current saturation depth to their peers if it changes. As their saturation depth increases, nodes will get notified of fewer and fewer new peers \(since they already know their neighbourhood\). Once the node finds all their nearest neighbours and has saturated all the bins, no new peers are expected. For this reason, a node can conclude a saturated kademlia state if it receives no new peers \(for some time\). The node does not need to know the number of nodes in the network. In fact, some time after the node stops receiving new peer addresses, the node can effectively estimate the size of the network from the depth \(depth nn implies 2n2n nodes\)

Such a network can readily be used for a forwarding-style messaging system. Swarm’s PSS is based on this. Swarm also uses this network to implement its storage solution.

### 2.3. Distributed preimage archive

_Distributed hash tables_ \(DHTs\) utilise an overlay network to implement a key-value store distributed over the nodes. The basic idea is that the keyspace is mapped onto the overlay address space, and information about an element in the container is to be found with nodes whose address is in the proximity of the key. DHTs for decentralised content addressed storage typically associate content fingerprints with a list of nodes \(seeders\) who can serve that content. However, the same structure can be used directly: it is not information about the location of content that is stored at the node closest to the address \(fingerprint\), but the content itself. We call this structure _distributed preimage archive_ \(DPA\).[![The DPA and chunking in Swarm](https://swarm-guide.readthedocs.io/en/latest/_images/dpa-chunking.svg)](https://swarm-guide.readthedocs.io/en/latest/_images/dpa-chunking.svg)

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

In order to reduce network traffic resulting from receiving chunks from multiple sources, all store requests can go via a confirmation roundtrip. For each peer connection in both directions, the source peer sends an _offeredHashes_ message containing a batch of hashes offered to push to the recipient. Recipient responds with a _wantedHashes_.[![Syncing chunks in the Swarm network](https://swarm-guide.readthedocs.io/en/latest/_images/syncing-high-level.svg)](https://swarm-guide.readthedocs.io/en/latest/_images/syncing-high-level.svg)

### 2.4. Data layer

There are 4 different layers of data units relevant to Swarm:

* _message_: p2p RLPx network layer. Messages are relevant for the devp2p wire protocols
* _chunk_: fixed size data unit of storage in the distributed preimage archive
* _file_: the smallest unit that is associated with a mime-type and not guaranteed to have integrity unless it is complete. This is the smallest unit semantic to the user, basically a file on a filesystem.
* _collection_: a mapping of paths to files is represented by the _swarm manifest_. This layer has a mapping to file system directory tree. Given trivial routing conventions, a url can be mapped to files in a standardised way, allowing manifests to mimic site maps/routing tables. As a result, Swarm is able to act as a webserver, a virtual cloud hosting service.

The actual storage layer of Swarm consists of two main components, the _localstore_ and the _netstore_. The local store consists of an in-memory fast cache \(_memory store_\) and a persistent disk storage \(_dbstore_\). The NetStore is extending local store to a distributed storage of Swarm and implements the _distributed preimage archive \(DPA\)_.[![High level storage layer in Swarm](https://swarm-guide.readthedocs.io/en/latest/_images/storage-layer.svg)](https://swarm-guide.readthedocs.io/en/latest/_images/storage-layer.svg)

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

### 2.5. Components

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


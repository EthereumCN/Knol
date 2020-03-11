---
description: Node Discovery Protocol v4
---

# 节点发现协议 v4

[本规范](https://github.com/ethereum/devp2p/blob/master/discv4.md)定义了节点发现协议v4版本，一种类似于Kademlia的DHT，用于存储以太坊节点相关信息。之所以选择Kademlia结构是因为其在组织节点分布式索引以及产生短径拓扑方面十分有效。 

当前协议版本为第4版。过往协议版本请参阅本文档末尾。

## 节点身份

每个节点都有一个加密身份，即secp256k1椭圆曲线上的密钥。节点的公钥用作其标识符或“节点ID”。

两个节点密钥之间的“距离”是按位异或 \(bitwise exclusive or\) 公钥的哈希值，以数字表示。

```text
distance(n₁, n₂) = keccak256(n₁) XOR keccak256(n₂)
```

## 节点记录

发现协议的参与者应维护包含最新信息的[节点记录](https://github.com/ethereum/devp2p/blob/master/enr.md)（ENR）。所有记录都必须使用“ v4”身份方案。其他节点可以随时通过发送[ENRRequest](https://github.com/ethereum/devp2p/blob/master/discv4.md#enrrequest-packet-0x05)数据包来请求本地记录。

要解析任何节点公钥的当前记录，请使用[FindNode](https://github.com/ethereum/devp2p/blob/master/discv4.md#findnode-packet-0x03)数据包执行Kademlia查找。找到该节点后，向其发送ENRRequest并从响应中返回记录。

## Kademlia Table

发现协议中的节点保留附近其他节点的相关信息。邻近节点存储在“ k-buckets”组成的路由表中。对于每个`0 ≤ i < 256`，每个节点对处于自身`2i`和`2i+1`之间距离的节点都保留一个k-bucket。

节点发现协议使用`k = 16`，即每个k-bucket最多包含16个节点条目。这些条目按最近看到的时间进行排序，时间最远的节点在头部，最近看到的节点在尾部。

当遇到新节点N₁时，可以将其插入相应的bucket中。如果bucket中少于`k`个条目，则可以直接将N₁添加为第一个条目。如果bucket中已经包含`k`个条目，则需要通过发送[Ping](https://github.com/ethereum/devp2p/blob/master/discv4.md#ping-packet-0x01)数据包来重新验证其中时间距离最远的节点N₂。如果没有收到来自N₂的回复，则将其视作失效，可以进行移除，然后将N₁加到bucket的前部。

## 端点验证

为了预防流量放大 \(traffic amplification\) 攻击，实现必须验证查询的发送者是否参与了发现协议。如果数据包的发送者在12小时内发送了具有匹配ping哈希的有效[Pong](https://github.com/ethereum/devp2p/blob/master/discv4.md#pong-packet-0x02)响应，则认为该数据包的发送者已经过验证。

## 递归查找

A 'lookup' locates the `k` closest nodes to a node ID.

一次查找 \(lookup\) 会找到`k`个距离目标节点最近的节点。

节点查找发起后先选取`α`个距离目标节点最近的已知节点，随后向这些节点发送[FindNode](https://github.com/ethereum/devp2p/blob/master/discv4.md#findnode-packet-0x03)包。`α` 是一个系统通用并行参数，通常可设为3。发起者继续向先前查询到的节点发送FindNode，如此不断进行递归。对获知的`k`个离目标节点最近的节点，选取`α`个尚未查询过的节点向其发送[FindNode](https://github.com/ethereum/devp2p/blob/master/discv4.md#findnode-packet-0x03)。无法快速响应的节点将不例如考虑，除非他们做出响应。

如果一轮FindNode查询失败，即没有返回任何一个比最近节点更近的节点，那么将会继续向`k`个最近节点中未被查询过的节点发送FindNode。当查询了所有`k`个最近节点，并获得其响应，查找过程终止。

## 线路协议

节点发现消息都以UDP报文形式发送，数据包最大为1280字节。

```text
packet = packet-header || packet-data
```

数据包头部：

```text
packet-header = hash || signature || packet-type
hash = keccak256(signature || packet-type || packet-data)
signature = sign(packet-type || packet-data)
```

当在同一UDP端口上运行多个协议时，`hash`的作用是使数据包格式可识别。除此之外并无其他目的。

每个包都由节点的公钥进行签名，`signature`是一个编码长度为65字节的数组，作为签名值串联`r`, `s`以及'recovery id' `v`。

`packet-type`为单字节，作用是定义消息类型。有效数据包类型如下。数据包头部之后的数据特定于该数据包，用RLP进行编码。实现应忽略`packet-data`列表中的其他任何元素及其后面的任何额外数据。

#### Ping Packet \(0x01\)

```text
packet-data = [version, from, to, expiration, enr-seq ...]
version = 4
from = [sender-ip, sender-udp-port, sender-tcp-port]
to = [recipient-ip, recipient-udp-port, 0]
```

`expiration`字段是UNIX时间戳，如果数据包内的时间戳过期可能会导致无法处理。

`enr-seq`字段是发送者的当前ENR序列号。该字段为可选项。

收到ping数据包后，接收节点应回复[Pong](https://github.com/ethereum/devp2p/blob/master/discv4.md#pong-packet-0x02)数据包，还可以考虑将发送节点添加到本地表中。实现应忽略版本中的不匹配情况。

如果在12小时内未与发送方进行任何通信，则除了pong之外还应发送ping以进行端点验证。

#### Pong Packet \(0x02\)

```text
packet-data = [to, ping-hash, expiration, enr-seq, ...]
```

Pong是ping的响应。

`ping-hash`须与相应ping包的`hash`一致。实现时应该忽略未经请求的不含ping包`hash`的pong包。

`enr-seq`字段是发送者的当前ENR序列号。该字段为可选项。

#### FindNode Packet \(0x03\)

```text
packet-data = [target, expiration, ...]
```

FindNode包用于请求距离`target`节点近的节点。`target`节点ID是一个65字节的secp256k1椭圆曲线公钥。当接收到FindNode，接收方应当回复在本地节点列表中距离请求目标节点最近的16个节点。

为了抵抗流量放大攻击，只有经过端点验证的FindNode发送者才能被邻近节点回复。

#### Neighbors Packet \(0x04\)

```text
packet-data = [nodes, expiration, ...]
nodes = [[ip, udp-port, tcp-port, node-id], ...]
```

Neighbors是[FindNode](https://github.com/ethereum/devp2p/blob/master/discv4.md#findnode-packet-0x03)的响应。

#### ENRRequest Packet \(0x05\)

```text
packet-data = [expiration]
```

当接收到此类数据包时，节点应使用包含其[节点记录](https://github.com/ethereum/devp2p/blob/master/enr.md)的当前版本ENRResponse数据包进行回复。

为了防止放大攻击，ENRRequest的发送者应该已经回复了ping数据包 \(如同FindNode\)。针对`expiration`字段（UNIX时间戳），如果指向了过去的时间，则不应发送任何回复。

#### ENRResponse Packet \(0x06\)

```text
packet-data = [request-hash, ENR]
```

本数据包是ENRRequest的响应。

* `request-hash`是被回复的整个ENRRequest包的哈希
* `ENR`是节点记录

数据包的接收者应验证节点记录是否已由公钥签名（与响应数据包的签名相同）。

## 过往协议版本

#### 当前版本的已知问题

所有数据包中的`expiration`字段数据都应能预防数据包重放。作为绝对时间戳，要求节点的时钟是准确的，因此才能进行正确验证。自协议2016年发布以来，我们收到了无数由节点时钟不正确引起的连接性问题报告。

端点验证的准确性还存在问题，因为FindNode的发送者无法确定接收者是否见过最近的足够的pong。 Geth的处理方式如下：如果在最近12小时内未与接收方进行任何通信，则要通过发送ping来启动该过程。等待对方的ping，进行回复，然后发送FindNode。

#### EIP-868 \(2019/10\)

[EIP-868](https://eips.ethereum.org/EIPS/eip-868)加入了[ENRRequest](https://github.com/ethereum/devp2p/blob/master/discv4.md#enrrequest-packet-0x05)和[ENRResponse](https://github.com/ethereum/devp2p/blob/master/discv4.md#enrresponse-packet-0x06)数据包，并且对[Ping](https://github.com/ethereum/devp2p/blob/master/discv4.md#ping-packet-0x01)和[Pong](https://github.com/ethereum/devp2p/blob/master/discv4.md#pong-packet-0x02)进行了优化以包含本地ENR序列号。

#### EIP-8 \(2017/12\)

[EIP-8](https://eips.ethereum.org/EIPS/eip-8)要求实现必须忽略Ping版本中的不匹配以及`packet-data`中的任何其他列表元素。


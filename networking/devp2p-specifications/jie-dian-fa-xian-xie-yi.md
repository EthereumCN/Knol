---
description: Node Discovery Protocol v4
---

# 节点发现协议

本规范定义了节点发现协议v4版本，一种类似于Kademlia的DHT，用于存储以太坊节点相关信息。之所以选择Kademlia结构是因为其在组织节点分布式索引以及产生短径拓扑方面十分有效。 

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

## Wire Protocol

Node discovery messages are sent as UDP datagrams. The maximum size of any packet is 1280 bytes.

```text
packet = packet-header || packet-data
```

Every packet starts with a header:

```text
packet-header = hash || signature || packet-type
hash = keccak256(signature || packet-type || packet-data)
signature = sign(packet-type || packet-data)
```

The `hash` exists to make the packet format recognizable when running multiple protocols on the same UDP port. It serves no other purpose.

Every packet is signed by the node's identity key. The `signature` is encoded as a byte array of length 65 as the concatenation of the signature values `r`, `s` and the 'recovery id' `v`.

The `packet-type` is a single byte defining the type of message. Valid packet types are listed below. Data after the header is specific to the packet type and is encoded as an RLP list. Implementations should ignore any additional elements in the `packet-data` list as well as any extra data after the list.

#### Ping Packet \(0x01\)

```text
packet-data = [version, from, to, expiration, enr-seq ...]
version = 4
from = [sender-ip, sender-udp-port, sender-tcp-port]
to = [recipient-ip, recipient-udp-port, 0]
```

The `expiration` field is an absolute UNIX time stamp. Packets containing a time stamp that lies in the past are expired may not be processed.

The `enr-seq` field is the current ENR sequence number of the sender. This field is optional.

When a ping packet is received, the recipient should reply with a [Pong]() packet. It may also consider the sender for addition into the local table. Implementations should ignore any mismatches in version.

If no communication with the sender has occurred within the last 12h, a ping should be sent in addition to pong in order to receive an endpoint proof.

#### Pong Packet \(0x02\)

```text
packet-data = [to, ping-hash, expiration, enr-seq, ...]
```

Pong is the reply to ping.

`ping-hash` should be equal to `hash` of the corresponding ping packet. Implementations should ignore unsolicited pong packets that do not contain the hash of the most recent ping packet.

The `enr-seq` field is the current ENR sequence number of the sender. This field is optional.

#### FindNode Packet \(0x03\)

```text
packet-data = [target, expiration, ...]
```

A FindNode packet requests information about nodes close to `target`. The `target` is a 65-byte secp256k1 public key. When FindNode is received, the recipient should reply with [Neighbors]() packets containing the closest 16 nodes to target found in its local table.

To guard against traffic amplification attacks, Neighbors replies should only be sent if the sender of FindNode has been verified by the endpoint proof procedure.

#### Neighbors Packet \(0x04\)

```text
packet-data = [nodes, expiration, ...]
nodes = [[ip, udp-port, tcp-port, node-id], ...]
```

Neighbors is the reply to [FindNode]().

#### ENRRequest Packet \(0x05\)

```text
packet-data = [expiration]
```

When a packet of this type is received, the node should reply with an ENRResponse packet containing the current version of its [node record](https://github.com/ethereum/devp2p/blob/master/enr.md).

To guard against amplification attacks, the sender of ENRRequest should have replied to a ping packet recently \(just like for FindNode\). The `expiration` field, a UNIX timestamp, should be handled as for all other existing packets i.e. no reply should be sent if it refers to a time in the past.

#### ENRResponse Packet \(0x06\)

```text
packet-data = [request-hash, ENR]
```

This packet is the response to ENRRequest.

* `request-hash` is the hash of the entire ENRRequest packet being replied to.
* `ENR` is the node record.

The recipient of the packet should verify that the node record is signed by the public key which signed the response packet.

## Change Log

#### Known Issues in the Current Version

The `expiration` field present in all packets is supposed to prevent packet replay. Since it is an absolute time stamp, the node's clock must be accurate to verify it correctly. Since the protocol's launch in 2016 we have received countless reports about connectivity issues related to the user's clock being wrong.

The endpoint proof is imprecise because the sender of FindNode can never be sure whether the recipient has seen a recent enough pong. Geth handles it as follows: If no communication with the recipient has occurred within the last 12h, initiate the procedure by sending a ping. Wait for a ping from the other side, reply to it and then send FindNode.

#### EIP-868 \(October 2019\)

[EIP-868](https://eips.ethereum.org/EIPS/eip-868) adds the [ENRRequest]() and [ENRResponse]() packets. It also modifies [Ping]() and [Pong]() to include the local ENR sequence number.

#### EIP-8 \(December 2017\)

[EIP-8](https://eips.ethereum.org/EIPS/eip-8) mandated that implementations ignore mismatches in Ping version and any additional list elements in `packet-data`.


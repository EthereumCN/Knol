---
description: Ethereum Wire Protocol
---

# 以太坊线路协议

'eth'是[RLPx](https://github.com/ethereum/devp2p/blob/master/rlpx.md)传输协议，用以促进节点之间沟通以太坊区块链信息。当前协议版本为eth / 64。查阅过往协议版本，请参阅文档末尾。

## 基本操作

建立连接后，必须首先发送[状态](https://github.com/ethereum/devp2p/blob/master/caps/eth.md#status-0x00)消息 \(status message\)。接收到节点的状态消息后，以太坊会话将被激活，继而可以发送其他任何类型的消息。

在状态消息发送之后，所有的已知事务都理应被囊括在一条及以上[事务](https://github.com/ethereum/devp2p/blob/master/caps/eth.md#transactions-0x02)消息 \(transcations messages\)中发送。

[事务](https://github.com/ethereum/devp2p/blob/master/caps/eth.md#transactions-0x02)消息应定期发送，因为节点需要传播新的事务。节点绝不能将事务发送回对等节点，因为先前已发送过该事务，或是该事务原本就是从该节点发出的。

一旦区块信息的基本有效性已经被确认（例如经过工作量证明检查之后），区块通常会被重新广播给所有已连接的节点。传播则是通过发送[NewBlock](https://github.com/ethereum/devp2p/blob/master/caps/eth.md#newblock-0x07)和[NewBlockHashes](https://github.com/ethereum/devp2p/blob/master/caps/eth.md#newblockhashes-0x01)消息。 [NewBlock](https://github.com/ethereum/devp2p/blob/master/caps/eth.md#newblock-0x07)消息包含完整的区块信息，并发送给一小部分已连接的节点（通常是节点总数的平方根）。[NewBlockHashes](https://github.com/ethereum/devp2p/blob/master/caps/eth.md#newblockhashes-0x01)消息则会发送给其他全部节点，仅包含新区块的哈希值。如果这些节点在某段时间内未能接收到新区块哈希，他们可以请求接收完整的区块信息。

### 区块链同步

假如两个节点进行连接并且发送了其[状态](https://github.com/ethereum/devp2p/blob/master/caps/eth.md#status-0x00)消息 \(status message\)，状态消息包含区块总难度和总难度最高的区块的哈希。

总难度最低的客户端通过[GetBlockHeaders](https://github.com/ethereum/devp2p/blob/master/caps/eth.md#getblockheaders-0x03)消息继续下载区块头，继而验证接收到的区块头中的工作量证明值，然后继续通过[GetBlockBodies](https://github.com/ethereum/devp2p/blob/master/caps/eth.md#getblockbodies-0x05)消息获取区块主体部分。接收到的区块都通过以太坊虚拟机 \(EVM\) 执行并重建状态树 \(state tree\)。

请注意，区块头、区块主体的下载和区块执行可能同时进行。

### 状态同步

#### 即"fast sync" 快速同步

协议版本eth/63及更高也允许同步事务执行结果（即“状态”）。比起重新执行所有事物，这种同步方式速度更快，但可能会牺牲一定程度的安全性。

状态同步通常通过下载区块头链来进行，并验证其工作量证明值。在上述“区块链同步”部分中，客户端请求获取区块主体，但并不执行区块事务。取而代之的是，客户端选择一个区块链前部的区块，通过[GetNodeData](https://github.com/ethereum/devp2p/blob/master/caps/eth.md#getnodedata-0x0d)请求根节点及其各个子节点，逐步下载默克尔树节点和合约代码，直到同步完整个状态树。

## 协议内消息

#### Status \(0x00\)

`[protocolVersion: P, networkId: P, td: P, bestHash: B_32, genesisHash: B_32, forkID]`

通知节点其当前状态。状态消息应在建立连接之后随即发送，先于任何其他eth协议消息。

* `protocolVersion`: 当前协议版本
* `networkId`: 区块链的标识符号，为整数，参考以下表格
* `td`: 最佳链的总难度，存在于区块头中，整数
* `bestHash`: 难度最高区块的哈希值
* `genesisHash`: 创世区块的哈希值
* `number`:  区块链中最新区块的编号
* `forkID`: EIP-2124提出的分叉标识符，编码为`[forkHash, forkNext]`

下表列出了常见的Network ID及其对应网络。其他未列出的ID，即客户端不应要求使用任何特定的网络ID。请注意，网络ID可能与用于防止交易重播的EIP-155链ID对应或不对应。

| ID | chain |
| :--- | :--- |
| 1 | 以太坊主网 |
| 3 | Ropsten测试网 \(PoW\) |
| 4 | Rinkeby测试网 \(PoA\) |
| 5 | Görli测试网 \(PoA\) |
| 42 | Kovan测试网 \(PoA\) |

更多Network IDs请查阅：[https://chainid.network](https://chainid.network/)

#### NewBlockHashes \(0x01\)

`[[hash_0: B_32, number_0: P], [hash_1: B_32, number_1: P], ...]`

表明一个或多个出现在网络上的新区块信息。为了作用最大化，节点应告知对等节点所有他们可能没有意识到的区块。如果有充分理由假设发送方节点知道新区块的存在（因为该节点本身已经通过NewBlockHashes广播了区块哈希），再重复包含区块哈希是不合理行为，并且可能损害发送方节点的信誉。如果发送方节点发送了其之后在[GetBlockHeaders](https://github.com/ethereum/devp2p/blob/master/caps/eth.md#getblockheaders-0x03)消息中拒绝执行的区块哈希，也是不合理行为，并可能损害发送节点的信誉。

#### Transactions \(0x02\)

`[[nonce: P, receivingAddress: B_20, value: P, ...], ...]`

说明节点应确保事务已经被包含在其事务队列中。列表中的事项遵循以太坊主规范中所描述的事务格式。事务消息必须至少包含一个（新）事务，不建议发送空白消息，可能导致连接中断。

节点不得在同一会话中将同一事务反复发送给对等节点，并且不得将事务转发给发送方节点。在实际操作中，这通常是通过每一个节点的步隆过滤器或保留已发送/已接收的事务哈希集来实现的。

#### GetBlockHeaders \(0x03\)

`[block: {P, B_32}, maxHeaders: P, skip: P, reverse: P in {0, 1}]`

要求节点回复[BlockHeaders](https://github.com/ethereum/devp2p/blob/master/caps/eth.md#blockheaders-0x04)消息。回复中必须包含多个区块头，当`reverse`为`0`时，其数目递增，当`reverse`为`1`时，其数目递减；从规范链中的区块`block`（以数字或哈希表示）开始，最多包含`maxHeaders`项。

#### BlockHeaders \(0x04\)

`[blockHeader_0, blockHeader_1, ...]`

回复[GetBlockHeaders](https://github.com/ethereum/devp2p/blob/master/caps/eth.md#getblockheaders-0x03)。列表中的事项（message ID之后）遵循以太坊主规范中所描述的格式，先前在GetBlockHeaders消息中要求提供。如果未发现所请求的区块头，则此字段可以合理地不包含区块头。单条消息中能够请求的区块头数量可能受执行限制。

#### GetBlockBodies \(0x05\)

`[hash_0: B_32, hash_1: B_32, ...]`

要求节点回复[BlockBodies](https://github.com/ethereum/devp2p/blob/master/caps/eth.md#blockbodies-0x06)消息。使用哈希表示我们需要的区块集合。单条消息中能够请求的区块数量可能受执行限制。

#### BlockBodies \(0x06\)

`[[transactions_0, uncles_0] , ...]`

回复[GetBlockBodies](https://github.com/ethereum/devp2p/blob/master/caps/eth.md#getblockbodies-0x05)。列表中的事项是区块的一部分（除去区块头部分），遵循以太坊主规范中所描述的格式，先前是在GetBlockBodies消息中要求提供。如果最后一个GetBlockBodies查询中没有可用区块，则该字段可以为空。

#### NewBlock \(0x07\)

`[[blockHeader, transactionList, uncleList], totalDifficulty]`

表明节点应该知道的单个区块。列表中的项目组合（message ID之后）遵循以太坊主规范中所描述的区块格式。

* `totalDifficulty`表示区块总难度，也即区块分数 \(score\).

#### GetNodeData \(0x0d\)

`[hash_0: B_32, hash_1: B_32, ...]`

要求节点回复[NodeData](https://github.com/ethereum/devp2p/blob/master/caps/eth.md#nodedata-0x0e)消息，包含与所请求哈希匹配的状态树节点或合约代码。

#### NodeData \(0x0e\)

`[value_0: B, value_1: B, ...]`

提供一组状态树节点或合约代码blobs，要与先前从[GetNodeData](https://github.com/ethereum/devp2p/blob/master/caps/eth.md#getnodedata-0x0d)请求的哈希相一致。如果条件受限，可以不需要包含所有内容。如果节点不知道任何之前请求的哈希，则此消息可能为空。单条消息中能够请求的数量可能受执行限制。

#### GetReceipts \(0x0f\)

`[blockHash_0: B_32, blockHash_1: B_32, ...]`

要求节点回复[Receipt](https://github.com/ethereum/devp2p/blob/master/caps/eth.md#receipts-0x10)消息，包含给定区块哈希值的回执。单条消息中能够请求的回执数量可能受执行限制。

#### Receipts \(0x10\)

`[[receipt_0, receipt_1], ...]`

提供一组回执，与先前[GetReceipts](https://github.com/ethereum/devp2p/blob/master/caps/eth.md#getreceipts-0x0f)消息中的区块哈希相一致。

## 旧版本协议

#### eth/64 \([EIP-2364](https://eips.ethereum.org/EIPS/eip-2364), 2019年11月\)

版本64更改了状态消息以包含EIP-2124提出的ForkID。因此节点可以在无需同步区块链的情况下确定区块链执行规则的相互兼容性。

#### eth/63 \(2016\)

Version 63 added the [GetNodeData](https://github.com/ethereum/devp2p/blob/master/caps/eth.md#getnodedata-0x0d), [NodeData](https://github.com/ethereum/devp2p/blob/master/caps/eth.md#nodedata-0x0e), [GetReceipts](https://github.com/ethereum/devp2p/blob/master/caps/eth.md#getreceipts-0x0f) and [Receipts](https://github.com/ethereum/devp2p/blob/master/caps/eth.md#receipts-0x10) messages which allow synchronizing transaction execution results.

版本63添加了[GetNodeData](https://github.com/ethereum/devp2p/blob/master/caps/eth.md#getnodedata-0x0d), [NodeData](https://github.com/ethereum/devp2p/blob/master/caps/eth.md#nodedata-0x0e), [GetReceipts](https://github.com/ethereum/devp2p/blob/master/caps/eth.md#getreceipts-0x0f) 和 [Receipts](https://github.com/ethereum/devp2p/blob/master/caps/eth.md#receipts-0x10)消息，从而允许同步事务执行结果。

#### eth/62 \(2015\)

在版本62中，[NewBlockHashes](https://github.com/ethereum/devp2p/blob/master/caps/eth.md#newblockhashes-0x01)消息可以在已广播的哈希值旁边添加区块编号。状态消息中的区块编号被移除。GetBlockHashes（0x03）、BlockHashes（0x04）、GetBlocks（0x05）和Blocks（0x06）消息被获取区块头和区块体的消息替换。 BlockHashesFromNumber（0x08）消息被移除。

重新分配/被移除消息的先前编码为：

* GetBlockHashes \(0x03\): `[hash: B_32, maxBlocks: P]`
* BlockHashes \(0x04\): `[hash_0: B_32, hash_1: B_32, ...]`
* GetBlocks \(0x05\): `[hash_0: B_32, hash_1: B_32, ...]`
* Blocks \(0x06\): `[[blockHeader, transactionList, uncleList], ...]`
* BlockHashesFromNumber \(0x08\): `[number: P, maxBlocks: P]`

#### eth/61 \(2015\)

版本61添加了BlockHashesFromNumber（0x08）消息，可用于升序请求区块。并且将最新的区块编号添加到状态消息中。

#### eth/60及以下

以太坊PoC开发阶段使用60及以下版本。

* `0x00` for PoC-1
* `0x01` for PoC-2
* `0x07` for PoC-3
* `0x09` for PoC-4
* `0x17` for PoC-5
* `0x1c` for PoC-6


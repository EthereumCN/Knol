# Patricia Tree

**Contents**

* [Modified Merkle Patricia Trie Specification \(also Merkle Patricia Tree\)](patricia-tree.md#modified-merkle-patricia-trie-specification-also-merkle-patricia-tree)
  * [Preamble: Basic Radix Tries](patricia-tree.md#preamble-basic-radix-tries)
* [Main specification: Merkle Patricia Trie](patricia-tree.md#main-specification-merkle-patricia-trie)
  * [Optimization](patricia-tree.md#optimization)
  * [Specification: Compact encoding of hex sequence with optional terminator](patricia-tree.md#specification-compact-encoding-of-hex-sequence-with-optional-terminator)
  * [Example Trie](patricia-tree.md#example-trie)
  * [Tries in Ethereum](patricia-tree.md#tries-in-ethereum)
    * [State Trie](patricia-tree.md#state-trie)
    * [Storage Trie](patricia-tree.md#storage-trie)
    * [Transactions Trie](patricia-tree.md#transactions-trie)
    * [Receipts Trie](patricia-tree.md#receipts-trie)
  * [Other Resources](patricia-tree.md#other-resources)

## Modified Merkle Patricia Trie Specification \(also Merkle Patricia Tree\)

Merkle Patricia tries provide a cryptographically authenticated data structure that can be used to store all \(key, value\) bindings, although for the scope of this paper we are restricting keys and values to strings \(to remove this restriction, just use any serialization format for other data types\). They are fully deterministic, meaning that a Patricia trie with the same \(key,value\) bindings is guaranteed to be exactly the same down to the last byte and therefore have the same root hash, provide the holy grail of O\(log\(n\)\) efficiency for inserts, lookups and deletes, and are much easier to understand and code than more complex comparison-based alternatives like red-black tries.

Merkle Patricia Tree（梅克尔帕特里夏树）提供了一种基于加密学的，自校验防篡改的数据结构，用来存储键值对关系。后文中将简称为MPT。尽管在本规范范围内，我们限定键值的类型只能是字符串（但仍对所有的类型适用，因为只需提供一个简单的序列化和反序化机制，将要存储的类型与字符串进行转换即可）。

MPT是确定的。确定性是指同样内容的键值，将被保证找到同样的结果，有同样的根哈希。关于效率方面，对树的插入，查找，删除的时间复杂度控制在O\(log\(n\)\)。相较于红黑树来说，MPT更好理解和编码实现。

### 前言：基数树（Radix Tree）

在一个标准的基数树里，要存储的数据，按下述所示：

```text
[i0, i1 ... in, value]
```

Where `i0 ... in` represent the symbols of the alphabet \(often binary or hex\), `value` is the terminal value at the node, and the values in the `i0 ... in` slots are either `NULL` or pointers to \(in our case, hashes of\) other nodes. This forms a basic \(key, value\) store; for example, if you are interested in the value that is currently mapped to `dog` in the trie, you would first convert `dog` into letters of the alphabet \(giving `64 6f 67`\), and then descend down the trie following that path until at the end of the path you read the value. That is, you would first look up the root hash in a flat key/value DB to find the root node of the trie \(which is basically an array of keys to other nodes\), use the value at index `6` as a key \(and look it up in the flat key/value DB\) to get the node one level down, then pick index `4` of that to lookup the next value, then pick index `6` of that, and so on, until, once you followed the path: `root -> 6 -> 4 -> 6 -> 15 -> 6 -> 7`, you look up the value of the node that you have and return the result.

其中的`i0`到`iN`一般表示二进制或十六进制的格式的字母符号。`value`表示的是树节点中存储的最终值。每一个`i0`到`iN`槽位的值，要么是`NULL`，要么是指向另一个节点的指针（在当前这个场景中，存储的是其它节点的哈希值）。这样我们就实现了一个简单的键值对存储。举个例子来说，如果你想在这个基数树中，找到键`dog`所对应的值。首先需要将`dog`转换为比如ascii码值（十六进制表示是`64 6f 67`）。然后按字母序形成一个逐层向下的树。沿着字母组成的路径，在树的底部叶节点上，即找到`dog`对应的值。具体来说，首先找到存储这个键值对数据的根节点，找到下一层的节点`6`，然后再往下一层，找到节点`4`，然后一层一层往下找，直到完成了路径 root -&gt; 6 -&gt; 4 -&gt; 6 -&gt; f -&gt; 6 -&gt; 7。这样你将最终找到值的对应节点。

Note there is a difference between looking something up in the "trie" vs the underlying flat key/value "DB". They both define key/values arrangements, but the underlying DB can do a traditional 1 step lookup of a key, while looking up a key in the trie requires multiple underlying DB lookups to get to the final value as described above. To eliminate ambiguity, let's refer to the latter as a `path`.

注意：在“树”与"平面键值对数据库”中查找内容的方式存在差异。它们都规定了键值对的排列，但是数据库可以对键进行传统的1步查找，然而，如上所述，在树中查找键需要进行多次数据库查找才能获得最终值。为了消除歧义，我们将后者称为`path`。

The update and delete operations for radix tries are simple, and can be defined roughly as follows:

基数树的更新和删除操作比较简单，可以按照以下代码操作：

```text
def update(node_hash, path, value):
    if path == '':
        curnode = db.get(node_hash) if node else [ NULL ] * 17
        newnode = curnode.copy()
        newnode[-1] = value
    else:
        curnode = db.get(node_hash) if node else [ NULL ] * 17
        newnode = curnode.copy()
        newindex = update(curnode[path[0]], path[1:], value)
        newnode[path[0]] = newindex
    db.put(hash(newnode), newnode)
    return hash(newnode)

def delete(node_hash, path):
    if node_hash is NULL:
        return NULL
    else:
        curnode = db.get(node_hash)
        newnode = curnode.copy()
        if path == '':
            newnode[-1] = NULL
        else: 
            newindex = delete(curnode[path[0]],path[1:])
            newnode[path[0]] = newindex

        if len(filter(x -> x is not NULL, newnode)) == 0:
            return NULL
        else:
            db.put(hash(newnode), newnode)
            return hash(newnode)
```

The "Merkle" part of the radix trie arises in the fact that a deterministic cryptographic hash of a node is used as the pointer to the node \(for every lookup in the key/value DB `key == sha3(rlp(value))`, rather than some 32-bit or 64-bit memory location as might happen in a more traditional trie implemented in C. This provides a form of cryptographic authentication to the data structure; if the root hash of a given trie is publicly known, then anyone can provide a proof that the trie has a given value at a specific path by providing the nodes going up each step of the way. It is impossible for an attacker to provide a proof of a \(path, value\) pair that does not exist since the root hash is ultimately based on all hashes below it, so any modification would change the root hash.

基数树的节点关系，一般是使用比如C语言的32位或64位的内存地址指针来串联起来的。但在以太坊中为了实现数据的防篡改及校验，我们引入了Merkle Tree，使用节点的哈希值来建立节点关系。这样，如果一个给定的前缀的根哈希值是已知的，那么任何人都可以根据这个前缀来检查。对于一个攻击者，不可能能证明一个不存在键值对存在，因为根哈希最终依赖所有的下面的哈希值，所以任何的修改都会导致根哈希值的改变。

While traversing a path 1 nibble at a time as described above, most nodes contain a 17-element array. 1 index for each possible value held by the next hex character \(nibble\) in the path, and 1 to hold the final target value in the case that the path has been fully traversed. These 17-element array nodes are called `branch` nodes.

如上所述，在一次遍历路径1时，大多数节点都包含17个元素的数组。路径中下一个十六进制字符（半字节）所保存的每个可能值的索引为1，在路径已被完全遍历的情况下，索引为最终目标值的索引为1。这些17个元素的数组节点称为分支节点。

## Main specification: Merkle Patricia Trie

However, radix tries have one major limitation: they are inefficient. If you want to store just one \(path,value\) binding where the path is \(in the case of the ethereum state trie\), 64 characters long \(number of nibbles in `bytes32`\), you will need over a kilobyte of extra space to store one level per character, and each lookup or delete will take the full 64 steps. The Patricia trie introduced here solves this issue.

然而，基数树的主要缺点是：效率低下。如果只想存储一个path-value键值对，但在以太坊状态树中，该键长度有64个字符之长（以`bytes32`类型出现时的半字节数），则需要超过一千字节的额外空间来存储每个字符的那个层级，而且每次查找或删除都要执行全部64个步骤。此处介绍的Patricia trie解决了这一问题。

### Optimization 优化

Merkle Patricia tries solve the inefficiency issue by adding some extra complexity to the data structure. A node in a Merkle Patricia trie is one of the following:

MPT在解决低效的问题时，向当前的数据结构添加了一些复杂度。MPT的节点类型定义如下：

1. `NULL`（空字符串）
2. `branch` 17个元素的节点 `[ v0 ... v15, vt ]`
3. `leaf` 2个元素的节点 `[ encodedPath, value ]`
4. `extension` 2个元素的节点 `[ encodedPath, key ]`

With 64 character paths it is inevitable that after traversing the first few layers of the trie, you will reach a node where no divergent path exists for at least part of the way down. It would be naive to require such a node to have empty values in every index \(one for each of the 16 hex characters\) besides the target index \(next nibble in the path\). Instead we shortcut the descent by setting up an `extension` node of the form `[ encodedPath, key ]`, where `encodedPath` contains the "partial path" to skip ahead \(using compact encoding described below\), and the `key` is for the next db lookup.

In the case of a `leaf` node, which can be determined by a flag in the first nibble of `encodedPath`, the situation above occurs and also the "partial path" to skip ahead completes the full remainder of a path. In this case `value` is the target value itself.

The optimization above however introduces some ambiguity.

When traversing paths in nibbles, we may end up with an odd number of nibbles to traverse, but because all data is stored in `bytes` format, it is not possible to differentiate between, for instance, the nibble `1`, and the nibbles `01` \(both must be stored as `<01>`\). To specify odd length, the partial path is prefixed with a flag.

### Specification: Compact encoding of hex sequence with optional terminator

The flagging of both _odd vs. even remaining partial path length_ and _leaf vs. extension node_ as described above reside in the first nibble of the partial path of any 2-item node. They result in the following:

```text
hex char    bits    |    node type partial     path length
----------------------------------------------------------
   0        0000    |       extension              even        
   1        0001    |       extension              odd         
   2        0010    |   terminating (leaf)         even        
   3        0011    |   terminating (leaf)         odd         
```

For even remaining path length \(`0` or `2`\), another `0` "padding" nibble will always follow.

```text
def compact_encode(hexarray):
    term = 1 if hexarray[-1] == 16 else 0 
    if term: hexarray = hexarray[:-1]
    oddlen = len(hexarray) % 2
    flags = 2 * term + oddlen
    if oddlen:
        hexarray = [flags] + hexarray
    else:
        hexarray = [flags] + [0] + hexarray
    // hexarray now has an even length whose first nibble is the flags.
    o = ''
    for i in range(0,len(hexarray),2):
        o += chr(16 * hexarray[i] + hexarray[i+1])
    return o
```

Examples:

> \[ 1, 2, 3, 4, 5, ...\] '11 23 45' \[ 0, 1, 2, 3, 4, 5, ...\] '00 01 23 45' \[ 0, f, 1, c, b, 8, 10\] '20 0f 1c b8' \[ f, 1, c, b, 8, 10\] '3f 1c b8'

Here is the extended code for getting a node in the Merkle Patricia trie:

```text
def get_helper(node,path):
    if path == []: return node
    if node = '': return ''
    curnode = rlp.decode(node if len(node) < 32 else db.get(node))
    if len(curnode) == 2:
        (k2, v2) = curnode
        k2 = compact_decode(k2)
        if k2 == path[:len(k2)]:
            return get_helper(v2, path[len(k2):])
        else:
            return ''
    elif len(curnode) == 17:
        return get_helper(curnode[path[0]],path[1:])

def get(node,path):
    path2 = []
    for i in range(len(path)):
        path2.push(int(ord(path[i]) / 16))
        path2.push(ord(path[i]) % 16)
    path2.push(16)
    return get_helper(node,path2)
```

### Example Trie

Suppose we want a trie containing four path/value pairs `('do', 'verb')`, `('dog', 'puppy')`, `('doge', 'coin')`, `('horse', 'stallion')`.

First, we convert both paths and values to `bytes`. Below, actual byte representations for _paths_ are denoted by `<>`, although _values_ are still shown as strings, denoted by `''`, for easier comprehension \(they, too, would actually be `bytes`\):

```text
<64 6f> : 'verb'
<64 6f 67> : 'puppy'
<64 6f 67 65> : 'coin'
<68 6f 72 73 65> : 'stallion'
```

Now, we build such a trie with the following key/value pairs in the underlying DB:

```text
rootHash: [ <16>, hashA ]
hashA:    [ <>, <>, <>, <>, hashB, <>, <>, <>, hashC, <>, <>, <>, <>, <>, <>, <>, <> ]
hashC:    [ <20 6f 72 73 65>, 'stallion' ]
hashB:    [ <00 6f>, hashD ]
hashD:    [ <>, <>, <>, <>, <>, <>, hashE, <>, <>, <>, <>, <>, <>, <>, <>, <>, 'verb' ]
hashE:    [ <17>, hashF ]
hashF:    [ <>, <>, <>, <>, <>, <>, hashG, <>, <>, <>, <>, <>, <>, <>, <>, <>, 'puppy' ]
hashG:    [ <35>, 'coin' ]
```

Where for example rootHash = `<59 91 bb 8c 65 14 14 8a 29 db 67 6a 14 ac 50 6c d2 cd 57 75 ac e6 3c 30 a4 fe 45 77 15 e9 ac 84>`.

When one node is referenced inside another node, what is included is `H(rlp.encode(x))`, where `H(x) = sha3(x) if len(x) >= 32 else x` and `rlp.encode` is the [RLP](https://github.com/ethereum/wiki/wiki/RLP) encoding function.

Note that when updating a trie, one needs to store the key/value pair `(sha3(x), x)` in a persistent lookup table _if_ the newly-created node has length &gt;= 32. However, if the node is shorter than that, one does not need to store anything, since the function f\(x\) = x is reversible.

### Tries in Ethereum

All of the merkle tries in Ethereum use a Merkle Patricia Trie.

From a block header there are 3 roots from 3 of these tries.

1. stateRoot
2. transactionsRoot
3. receiptsRoot

#### State Trie

There is one global state trie, and it updates over time. In it, a `path` is always: `sha3(ethereumAddress)` and a `value` is always: `rlp(ethereumAccount)`. More specifically an ethereum `account` is a 4 item array of `[nonce,balance,storageRoot,codeHash]`. At this point it's worth noting that this `storageRoot` is the root of another patricia trie:

#### Storage Trie

Storage trie is where _all_ contract data lives. There is a separate storage trie for each account. To calculate a 'path' in this trie first understand how solidity organizes a [variable's position](https://github.com/ethereum/wiki/wiki/JSON-RPC#eth_getstorageat). To get the `path` there is one extra hashing \(the link does not describe this\). For instance the `path` for the zeroith variable is `sha3(<0000000000000000000000000000000000000000000000000000000000000000>)` which is always `290decd9548b62a8d60345a988386fc84ba6bc95484008f6362f93160ef3e563`. The value at the leaf is the rlp encoding of the storage value `1234` \(`0x04d2`\), which is `<82 04 d2>`. For the mapping example, the `path` is `sha3(<6661e9d6d8b923d5bbaab1b96e1dd51ff6ea2a93520fdc9eb75d059238b8c5e9>)`

#### Transactions Trie

There is a separate transactions trie for every block. A `path` here is: `rlp(transactionIndex)`. `transactionIndex` is its index within the block it's mined. The ordering is mostly decided by a miner so this data is unknown until mined. After a block is mined, the transaction trie never updates.

#### Receipts Trie

Every block has its own Receipts trie. A `path` here is: `rlp(transactionIndex)`. `transactionIndex` is its index within the block it's mined. Never updates.

### Other Resources

[Yellow paper Appendix D: Modified Merkle Patricia Tree](https://ethereum.github.io/yellowpaper/paper.pdf#appendix.D)


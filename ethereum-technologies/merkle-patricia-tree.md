---
description: 默克尔帕特里夏树
---

# Merkle Patricia Tree

* [改良后的Merkle Patricia Trie规范](https://github.com/ethereum/wiki/wiki/Patricia-Tree#modified-merkle-patricia-trie-specification-also-merkle-patricia-tree)
  * [前言：基数树（Radix Tree）](https://github.com/ethereum/wiki/wiki/Patricia-Tree#preamble-basic-radix-tries)
* [主要规范: Merkle Patricia Trie](https://github.com/ethereum/wiki/wiki/Patricia-Tree#main-specification-merkle-patricia-trie)
  * [优化](https://github.com/ethereum/wiki/wiki/Patricia-Tree#optimization)
  * [规范：使用可选终止符对十六进制序列进行Compact编码](https://github.com/ethereum/wiki/wiki/Patricia-Tree#specification-compact-encoding-of-hex-sequence-with-optional-terminator)
  * [创建树](https://github.com/ethereum/wiki/wiki/Patricia-Tree#example-trie)
  * [以太坊树类别](https://github.com/ethereum/wiki/wiki/Patricia-Tree#tries-in-ethereum)
    * [状态树](https://github.com/ethereum/wiki/wiki/Patricia-Tree#state-trie)
    * [存储树](https://github.com/ethereum/wiki/wiki/Patricia-Tree#storage-trie)
    * [交易树](https://github.com/ethereum/wiki/wiki/Patricia-Tree#transactions-trie)
    * [收据树](https://github.com/ethereum/wiki/wiki/Patricia-Tree#receipts-trie)
  * [更多资源](https://github.com/ethereum/wiki/wiki/Patricia-Tree#other-resources)

## 改良后的Merkle Patricia Trie规范

Merkle Patricia Tree（也称为Merkle Patricia Trie，梅克尔帕特里夏树）提供了一种基于加密学的，自校验防篡改的数据结构，用来存储键值对关系。后文中将简称为MPT。尽管在本规范范围内，我们限定键值的类型只能是字符串（但仍对所有的类型适用，因为只需提供一个简单的序列化和反序化机制，将要存储的类型与字符串进行转换即可）。

MPT是确定的。确定性是指同样内容的键值，将被保证找到同样的结果，有同样的根哈希。关于效率方面，对树的插入，查找，删除的时间复杂度控制在O\(log\(n\)\)。相较于红黑树来说，MPT更好理解和编码实现。

### 前言：基数树（Radix Tree）

在一个标准的基数树里，节点如下：

```text
[i0, i1 ... in, value]
```

其中的`i0`到`iN`一般表示二进制或十六进制的格式的字母符号。`value`表示的是树节点中存储的最终值。每一个`i0`到`iN`槽位的值，要么是`NULL`，要么是指向另一个节点的指针（在当前这个场景中，存储的是其它节点的哈希值）。这样我们就实现了一个简单的键值对存储。举个例子来说，如果你想在这个基数树中，找到键`dog`所对应的值。首先需要将`dog`转换为比如ascii码值（十六进制表示是`64 6f 67`）。然后按字母序形成一个逐层向下的树。沿着字母组成的路径，在树的底部叶节点上，即找到`dog`对应的值。具体来说，首先找到存储这个键值对数据的根节点，找到下一层的节点`6`，然后再往下一层，找到节点`4`，然后一层一层往下找，直到完成了路径 root -&gt; 6 -&gt; 4 -&gt; 6 -&gt; f -&gt; 6 -&gt; 7。这样你将最终找到值的对应节点。

注意：在“树”与"平面键值对数据库”中查找内容的方式存在差异。它们都规定了键值对的排列，但是数据库可以对键进行传统的1步查找，然而，如上所述，在树中查找键需要进行多次数据库查找才能获得最终值。为了消除歧义，我们将后者称为`path`。

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

基数树的节点关系，一般是使用比如C语言的32位或64位的内存地址指针来串联起来的。但在以太坊中为了实现数据的防篡改及校验，我们引入了Merkle Tree，使用节点的哈希值来建立节点关系。这样，如果一个给定的前缀的根哈希值是已知的，那么任何人都可以根据这个前缀来检查。对于一个攻击者，不可能能证明一个不存在键值对存在，因为根哈希最终依赖所有的下面的哈希值，所以任何的修改都会导致根哈希值的改变。

如上所述，在一次遍历路径1时，大多数节点都包含17个元素的数组。路径中下一个十六进制字符（半字节）所保存的每个可能值的索引为1，在路径已被完全遍历的情况下，索引为最终目标值的索引为1。这些17个元素的数组节点称为分支节点。

## 主要规范: Merkle Patricia Trie

然而，基数树的主要缺点是：效率低下。如果只想存储一个path-value键值对，但在以太坊状态树中，该键长度有64个字符之长（以`bytes32`类型出现时的半字节数），则需要超过一千字节的额外空间来存储每个字符的那个层级，而且每次查找或删除都要执行全部64个步骤。此处介绍的Patricia trie解决了这一问题。

### 优化

MPT在解决低效的问题时，向当前的数据结构添加了一些复杂度。MPT的节点类型定义如下：

1. `NULL`（空字符串）
2. `branch` 17个元素的节点 `[ v0 ... v15, vt ]`
3. `leaf` 2个元素的节点 `[ encodedPath, value ]`
4. `extension` 2个元素的节点 `[ encodedPath, key ]`

由于path有64个字符长，所以遍历树的前第几层时，必定会发现有的节点下面并没有分叉路径。这时，我们通过插入`extension`节点来建立捷径，其形式为`[ encodedPath, key ]`，`encodedPath` 包含“部分要跳过的路径”（使用下面将要提到的compact编码），`key`用于下次DB查找。

In the case of a `leaf` node, which can be determined by a flag in the first nibble of `encodedPath`, the situation above occurs and also the "partial path" to skip ahead completes the full remainder of a path. In this case `value` is the target value itself.

然而，以上提到的优化也造成了一些歧义。

当遍历半字节路径，最终遍历的半字节数可能是奇数，但由于所有数据都是以`bytes`的形式存储，半字节`1`和`01`都必须存储为`<01>，`所以会出现无法区分的情况。为了明确奇数长度，部分路径要加前缀flag值。

### 规范：使用可选终止符对十六进制序列进行Compact编码

The flagging of both _odd vs. even remaining partial path length_ and _leaf vs. extension node_ as described above reside in the first nibble of the partial path of any 2-item node. They result in the following:

```text
hex char    bits    |    node type partial     path length
----------------------------------------------------------
   0        0000    |       extension              even        
   1        0001    |       extension              odd         
   2        0010    |   terminating (leaf)         even        
   3        0011    |   terminating (leaf)         odd         
```

对于偶数剩余路径长度（`0`或`2`）来说，要在其后补充`0`。

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

例子：

> \[ 1, 2, 3, 4, 5, ...\] '11 23 45' \[ 0, 1, 2, 3, 4, 5, ...\] '00 01 23 45' \[ 0, f, 1, c, b, 8, 10\] '20 0f 1c b8' \[ f, 1, c, b, 8, 10\] '3f 1c b8'

下面列出了获取节点的详细代码：

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

### 创建树

假设我们要创建一棵包括四个键值对的树：`('do', 'verb')`, `('dog', 'puppy')`, `('doge', 'coin')`, `('horse', 'stallion')`。

首先，把所有键值对转换成`bytes`。然后，键的字节形式用`<>`表示，值仍然以字符串的形式出现，用`''`表示。事实上，这两种表示方式都是`bytes`。

```text
<64 6f> : 'verb'
<64 6f 67> : 'puppy'
<64 6f 67 65> : 'coin'
<68 6f 72 73 65> : 'stallion'
```

现在，我们通过输入以下键值对来创建树。

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

当根哈希=`<59 91 bb 8c 65 14 14 8a 29 db 67 6a 14 ac 50 6c d2 cd 57 75 ac e6 3c 30 a4 fe 45 77 15 e9 ac 84>`

`H(rlp.encode(x))`中一个节点是另一个节点的参考量，这其中牵涉到两种RLP编码函数`H(x) = sha3(x) if len(x) >= 32 else x`和`rlp.encode`。

注意：如何在新节点长度大于等于32的情况下更新一棵树，需要在持久化查找表中存储键值对`(sha3(x), x)`。然而，如果节点长小于32，就不需要存储任何数据，因为函数f\(x\) = x是可逆的。

### 以太坊树类别

以太坊使用Merkle Patricia Trie数据结构。

一个区块头有来自3棵树的3个根哈希。

1. 状态树根哈希
2. 交易树根哈希
3. 收据树根哈希

#### State Trie 状态树

状态数存储以太坊全局数据，包含一个键值映射，不断更新，其中`path`是`sha3(ethereumAddress)`，`value`是`rlp(ethereumAccount)`。更具体来说，一个以太坊`account`内容是4个元素的数组：`[nonce,balance,storageRoot,codeHash]`。其中要留意的是，`storageRoot` 是另一棵树的根节点。

#### Storage Trie 存储树

存储树存储了所有合约数据。每个账户都有一棵独立的存储树。想计算树的“路径”，首先要理解solidity如何组织[变量位置](https://github.com/ethereum/wiki/wiki/JSON-RPC#eth_getstorageat)。想要计算出`path`，就要进行哈希。例如，计算zeroith variable的`path`采用函数`sha3(<0000000000000000000000000000000000000000000000000000000000000000>)`，结果是`290decd9548b62a8d60345a988386fc84ba6bc95484008f6362f93160ef3e563`。树节点的值是存储值`1234` \(`0x04d2`\)的RLP编码，即`<82 04 d2>`。

#### Transactions Trie 交易树

每个区块都有一棵独立的交易树。在交易树包含的键值对中，交易树的`path`是`rlp(transactionIndex)`，其中`transactionIndex`是所在区块交易的编号。区块中交易的顺序主要由“矿工”决定，因此在这个区块被挖出前这些数据都是未知的。区块被挖出后，交易树的数据将不再更新。

#### Receipts Trie 收据树

每个区块都有一棵属于自己的收据树。在收据树包含的键值对中，收据树的`path`是`rlp(transactionIndex)`。其中`transactionIndex`是所在区块交易的编号。收据树不会更新数据。

### 更多资源

[Yellow paper Appendix D: Modified Merkle Patricia Tree](https://ethereum.github.io/yellowpaper/paper.pdf#appendix.D)

部分内容转载自：[https://me.tryblockchain.org/Ethereum-MerklePatriciaTree.html](https://me.tryblockchain.org/Ethereum-MerklePatriciaTree.html)


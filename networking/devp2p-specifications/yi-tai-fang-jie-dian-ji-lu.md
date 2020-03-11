# 以太坊节点记录

[本规范](https://github.com/ethereum/devp2p/blob/master/enr.md)定义了以太坊节点记录（Ethereum Node Records），这是一种用于p2p连接信息的开放格式。节点记录通常包含节点的网络端点（即节点的IP地址和端口）以及节点的目标信息，以供其他节点决定是否连接该节点。

## 记录结构

节点记录的组成部分如下：

* `signature`: 针对所记录内容的加密签名
* `seq`: 序列号，一个64位的无符号整数。记录一旦发生更改，节点应增加数字并重新发布记录。
* 记录的其余部分由任意键/值对 \(key/value pairs\) 组成

记录的签名通过身份方案 \(identity scheme\) 生成和验证。身份方案还负责导出DHT中的节点地址。 

键/值对必须按键排序，并且必须唯一，即任何键只能出现一次。技术上来说，键可以是任何字节序列，但是ASCII文本是首选。下表中的键名已经过预定义。

| Key | Value |
| :--- | :--- |
| `id` | 身份制度，例如“v4” |
| `secp256k1` | 压缩后的secp256k1公钥，33字节 |
| `ip` | IPv4地址，4字节  |
| `tcp` | TCP端口，big endian整数 |
| `udp` | UDP端口，big endian整数 |
| `ip6` | IPv6地址，16字节 |
| `tcp6` | IPv6专用的TCP端口，big endian整数 |
| `udp6` | IPv6专用的UDP端口，big endian整数 |

除`id`以外，所有键都是非强制项，包括IP地址和端口。只要其签名有效，即使没有端点信息，记录仍然有效。如果未提供`tcp6` / `udp6`端口，则`tcp` / `udp`端口适用于这两种IP地址。应当避免在`tcp`, `tcp6` 或 `udp`, `udp6`中声明相同的端口号，但不会使记录无效。

### RLP 编码

节点记录的规范编码是`[signature, seq, k, v, ...]`的RLP列表。节点记录的最大编码大小为300字节，实现应拒绝超过此大小的记录。

记录的签名和编码范式如下：

```text
content   = [seq, k, v, ...]
signature = sign(content)
record    = [signature, seq, k, v, ...]
```

### 文本编码

节点记录的文本形式是以base64编码，表现为RLP形式，以`enr:`为前缀。实现应使用[URL-safe base64 alphabet](https://tools.ietf.org/html/rfc4648#section-5)，并省略填充字符。

### "v4"身份方案

本规范定义了一个单一身份方案，并将其用作默认方案，直到其他EIPs进一步定义新的方案。 “ v4”方案与节点发现协议v4所使用的密码系统向后兼容。

* 要使用此方案对记录`content`进行签名，需要对`content`应用keccak256哈希函数，然后为该哈希创建签名。所得的64字节签名被编码为`r`和`s`签名值的串联（省略了恢复ID`v`）。
* 要对记录进行验证，请检查签名是否由记录的“secp256k1”键/值对中的公钥生成。 
* 要导出节点地址，请使用未压缩公钥的keccak256哈希。

## 基本原理

该范式的目的是以两种方式来满足未来的需求：

* 添加新的键/值对：这始终是可行的，且并不需要实现共识 \(implementation consensus\)。既有客户端将接受任何键/值对，无论是否可以解释其内容。
* 添加身份方案：这些方案需要实现共识 \(implementation consensus\)，否则网络将不接受签名。要引入一种新的身份方案，需要提出并实施一个新的EIP。在大多数客户端接收之后，即可投入使用。

记录的大小之所以受限，是因为记录经常被转发，并且可能被包含在大小受限制的协议中（例如DNS）。当使用“ v4”方案签名时，包含IPv4地址的记录大约占用120个字节，从而为其他元数据保留足够的空间。

另一个问题在于是否需要这么多与IP地址和端口相关联的预定义密钥。之所以出现这种需求，是因为住宅和移动网络设置通常将IPv4放在NAT之后，而IPv6会直接路由到同一主机。阐明两种地址类型可确保仅支持IPv4的节点也能访问。

## 测试矢量

以下是一个节点记录实例，包含IPv4地址`127.0.0.1` 以及UDP端口`30303`. 节点ID是`a448f24c6d18e575453db13171562b71999873db5b286df957af199ec94617f7`.

```text
enr:-IS4QHCYrYZbAKWCBRlAy5zzaDZXJBGkcnh4MHcBFZntXNFrdvJjX04jRzjzCBOonrkTfj499SZuOh8R33Ls8RRcy5wBgmlkgnY0gmlwhH8AAAGJc2VjcDI1NmsxoQPKY0yuDUmstAHYpMa2_oxVtw0RW_QAdpzBQA8yWM0xOIN1ZHCCdl8
```

该条记录通过"v4"身份方案进行签名，使用数字`1`为序列数，私钥如下：

```text
b71c71a67e1177ad4e901695e1b4b9ee17ae16c6668d313eac2f96dbcda3f291
```

该条记录的RLP结构如下：

```text
[
  7098ad865b00a582051940cb9cf36836572411a47278783077011599ed5cd16b76f2635f4e234738f30813a89eb9137e3e3df5266e3a1f11df72ecf1145ccb9c,
  01,
  "id",
  "v4",
  "ip",
  7f000001,
  "secp256k1",
  03ca634cae0d49acb401d8a4c6b6fe8c55b70d115bf400769cc1400f3258cd3138,
  "udp",
  765f,
]
```


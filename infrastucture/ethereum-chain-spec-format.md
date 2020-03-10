# 以太坊区块链规范格式

* [格式](https://github.com/ethereum/wiki/wiki/Ethereum-Chain-Spec-Format#format)
  * [子格式: engine](https://github.com/ethereum/wiki/wiki/Ethereum-Chain-Spec-Format#subformat-engine)
  * [子格式: params](https://github.com/ethereum/wiki/wiki/Ethereum-Chain-Spec-Format#subformat-params)
  * [子格式: genesis](https://github.com/ethereum/wiki/wiki/Ethereum-Chain-Spec-Format#subformat-genesis)
  * [子格式: accounts](https://github.com/ethereum/wiki/wiki/Ethereum-Chain-Spec-Format#subformat-accounts)
* [例子](https://github.com/ethereum/wiki/wiki/Ethereum-Chain-Spec-Format#example)

以下是任何类似以太坊的区块链所用的格式。此格式源自`genesis.json`格式，但它还包含用于更改和配置共识算法、确定基础设施信息、确定boot nodes、确定任何内建合约及其成本的参数。

## Format

此格式是JSON格式，顶层对象带有六个键：

* `name`: 表示区块链链名称的字符串值如“Frontier/Homestead”、“Morden”和“Olympic”。
* `forkName`: 如果两条不同的链的创世区块相同，则可以再此填入字符串值指定一个子标识符。
* `engine`: 表示共识引擎的枚举值（enum value）如“ Ethash”和“ Null”。
* `params`: 确定共识引擎各种属性的对象，允许进行配置。
* `genesis`: 确定创世区块头的对象。
* `nodes`: 字符串组，每个字符串都是enode格式的节点地址。
* `accounts`: 用来设定创世区块帐号，这包括预设内建合约和预设余额。

### 子格式: engine

有两种有效引擎：`Ethash` 和`Null`。

* `Null`: 非运行引擎
* `Ethash`: 以太坊所用的sealing引擎
  * `params`: 引擎特定参数
    * `minimumDifficulty`: 区块最小估算难度。
    * `gasLimitBoundDivisor`: 规定黄皮书里的相应数值。
    * `difficultyBoundDivisor`: 规定黄皮书里的相应数值。
    * `durationLimit`: 难度增加的边界点数。
    * `blockReward`: 区块奖励。
    * `registrar`: 区块链上注册表合约地址上以`0x`为前缀的40位半字节数据。

### 子格式: params

不同的共识引擎可能会在`params`中允许使用不同的键，但是它们也存在一些共同点：

* `accountStartNonce`: 所有新创建帐户应具有的随机数。
* `frontierCompatibilityModeLimit`: Frontier兼容模式完成到Homestead模式开始期间产生的区块数量。
* `maximumExtraDataSize`: 区块头extra\_data字段的最大字节数。
* `minGasLimit`: 一个区块的规定最小gas值。
* `networkID`: 网络上该链的索引。

注意：所有数值均是以`0x`为前缀的十六进制编码字符串。

### 子格式: genesis

genesis中的键指定了链的创世区块中的相应字段，它们均是以`0x`为前缀的十六进制编码字符串。

* `seal`
* `difficulty`
* `author`
* `timestamp`
* `parentHash`
* `extraData`
* `gasLimit`

### 子格式: accounts

`accounts`从地址（不带`0x`前缀的40个半字节字符串）映射到对象，每个对象都有许多键

* `balance`: 账户创建时，以wei为单位的账户余额
* `nonce`: 账户创建时，帐户的随机数。
* `code`: 账户创建时，前缀为`0x`的十六进制账户代码。
* `storage`: 账户创建时，映射十六进制编码的数值。
* `builtin`: 替代`code`，用于指定帐户代码是本地实现的。值是具有其他字段的对象：
  * "name": 充当字符串来执行的内置代码名称，如“`identity`”，“`ecrecover`”。
  * "pricing": 确定调用此合约的成本的枚举。
    * "linear": 确定调用此合约的线性成本。值是具有两个字段的对象：`base`是以Wei为单位的基本成本，此成本始终被支付；`word`表示输入一个单词的大概费用。

## 例子

以下是Morden ECS JSON文档：

```javascript
{
    "name": "Morden",
    "engine": {
        "Ethash": {
            "params": {
                "tieBreakingGas": false,
                "gasLimitBoundDivisor": "0x0400",
                "minimumDifficulty": "0x020000",
                "difficultyBoundDivisor": "0x0800",
                "durationLimit": "0x0d",
                "blockReward": "0x4563918244F40000",
                "registrar" : "0xc6d9d2cd449a754c494264e1809c50e34d64562b"
            }
        }
    },
    "params": {
        "accountStartNonce": "0x0100000",
        "frontierCompatibilityModeLimit": "0x789b0",
        "maximumExtraDataSize": "0x20",
        "tieBreakingGas": false,
        "minGasLimit": "0x1388",
        "networkID" : "0x2"
    },
    "genesis": {
        "seal": {
            "ethereum": {
                "nonce": "0x0000000000000042",
                "mixHash": "0x0000000000000000000000000000000000000000000000000000000000000000"
            }
        },
        "difficulty": "0x20000",
        "author": "0x0000000000000000000000000000000000000000",
        "timestamp": "0x00",
        "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
        "extraData": "0x",
        "gasLimit": "0x2fefd8"
    },
    "nodes": [
        "enode://b1217cbaa440e35ed471157123fe468e19e8b5ad5bedb4b1fdbcbdab6fb2f5ed3e95dd9c24a22a79fdb2352204cea207df27d92bfd21bfd41545e8b16f637499@104.44.138.37:30303"
    ],
    "accounts": {
        "0000000000000000000000000000000000000001": { "balance": "1", "nonce": "1048576", "builtin": { "name": "ecrecover", "pricing": { "linear": { "base": 3000, "word": 0 } } } },
        "0000000000000000000000000000000000000002": { "balance": "1", "nonce": "1048576", "builtin": { "name": "sha256", "pricing": { "linear": { "base": 60, "word": 12 } } } },
        "0000000000000000000000000000000000000003": { "balance": "1", "nonce": "1048576", "builtin": { "name": "ripemd160", "pricing": { "linear": { "base": 600, "word": 120 } } } },
        "0000000000000000000000000000000000000004": { "balance": "1", "nonce": "1048576", "builtin": { "name": "identity", "pricing": { "linear": { "base": 15, "word": 3 } } } },
        "102e61f5d8f9bc71d0ad4a084df4e65e05ce0e1c": { "balance": "1606938044258990275541962092341162602522202993782792835301376", "nonce": "1048576" }
    }
}
```

Note: the builtin accounts enable usage of Solidity language. Running without them included in the chain definition file may result in unexpected behavior.

注意：内置accounts启用Solidity语言。不通过包含在链定义文件中的内置accounts来运行程序，可能会导致无法产生预期效果。


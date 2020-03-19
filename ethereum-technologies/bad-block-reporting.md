# 坏块广播

* [BADBLOCK对象格式](https://github.com/ethereum/wiki/wiki/Bad-Block-Reporting#badblock-object-format)
  * [类型](https://github.com/ethereum/wiki/wiki/Bad-Block-Reporting#types)
  * [修饰符类型](https://github.com/ethereum/wiki/wiki/Bad-Block-Reporting#type-modifiers)
  * [BADBLOCKS 对象](https://github.com/ethereum/wiki/wiki/Bad-Block-Reporting#badblocks-object)
  * [泛型](https://github.com/ethereum/wiki/wiki/Bad-Block-Reporting#generic)
  * [区块型](https://github.com/ethereum/wiki/wiki/Bad-Block-Reporting#block-specific)
  * [区块头型](https://github.com/ethereum/wiki/wiki/Bad-Block-Reporting#block-header-specific)
  * [交易型](https://github.com/ethereum/wiki/wiki/Bad-Block-Reporting#transaction-specific)
  * [叔块型](https://github.com/ethereum/wiki/wiki/Bad-Block-Reporting#uncle-specific)

发送JSONRPC请求到`https://badblocks.ethdev.com`

用下述`BADBLOCK`对象调用`eth_badBlock(BADBLOCK)`：

## BADBLOCK对象格式

注意：所有十六进制字母都要小写。

### 类型

* `DATA`: 自由格式的字节数组，为十六进制字符串，无前缀0x。
* `DATA_20`: 与`DATA`类似，长度通常为40
* `HEX`:十六进制大端字符串，适用于VM堆栈、内存、存储项，无前缀0x，无零占位。
* `INT`: 简单JS整型数据。
* `BIGINT`: 十进制整型字符串，适用于潜在的bigints。
* `TAG_ERROR`: 稍后再介绍。
* `TAG_INST`: 表示EVM指令助记符的字符串，字母要大写，例如`"PUSH4"`或`"STOP"`。
* `VMTRACE`: 稍后再介绍。

### 修饰符类型

* `SOMETIMES`：在某些情况下会省略该字段。
* `NONSTANDARD`：省略该字段也无关紧要。

### BADBLOCKS对象

```javascript
{
    "block": DATA
    "errortype": TAG_ERROR
    "hints": { (all items OPTIONAL)
        "receipts": [ DATA, ... ], OPTIONAL
        "vmtrace": VMTRACE, OPTIONAL
    }, OPTIONAL
}
```

此处，`receipts`是表示收据的RLP编码数组。

`TAG_ERROR`是包含以下其中一项错误的字符串（以下错误大致按检测难度分类）：

### 泛型

* `RLPError`: 可分为以下4类：

  * `BadRLP`: 通常情况下都无效的RLP，如0x8100
  * `BadCast`: 给定的RLP类型不正确，如会被解析为整型的0x00。
  * `OversizeRLP`: 原本有效的RLP片段末尾多余的字节，如0x8000
  * `UndersizeRLP`: 原本有效RLP片段末尾缺少的字节，如0x81。

### 区块型

* `InvalidBlock`：可分为以下7类：
  * `InvalidBlockFormat`：区块格式错误（!=数组，!= 3项＆c。）。
  * `TooManyUncles`: 多于两个叔块
  * `InvalidTransactionsRoot`: 交易根与区块中给出的交易不适配。
  * `InvalidUnclesHash`: 叔块哈希值与区块中给出的叔块哈希值不同。
  * `InvalidGasUsed`:  所消耗的gas不等于上一个收据所显示消耗的gas（如果没有交易，则为上一个区块）。
  * `InvalidStateRoot`: 状态根与计算出的状态根不同（即奖励应用不正确）。
  * `InvalidReceiptsRoot`: 收据根与计算得出的收据根不同（即交易应用导致日志、gas消耗或状态根不同）。应该提示“receipts” 和 “vmtrace”。

### 区块头型

* `InvalidHeader`: 可以分为以下九类：
  * `InvalidBlockHeaderItemCount`: 区块头item计数错误
  * `TooMuchGasUsed`: 区块头状态gas消耗比gas限值多
  * `ExtraDataTooBig`: 区块头额外数据比限值多。
  * `InvalidDifficulty`: 区块难度与上一个区块的难度、时间戳不匹配。
  * `InvalidGasLimit`: gas限值不在上一个区块的gas限值范围内。
  * `InvalidBlockNonce`: 随机数不能生成满足给定难度的工作量证明。
  * `InvalidNumber`: 区块号不等于上个区块号+1。
  * `InvalidTimestamp`: 时间戳早于父块的时间戳
  * `InvalidLogBloom`: LogBloom不等于所有收据的LogBlooms的bitwise-OR。

### 交易型

* `InvalidTransaction`: 可分为以下6类：
  * `OutOfGasIntrinsic`: gas少于交易所需量。
  * `BlockGasLimitReached`: 此区块内的交易使用过量gas。
  * `InvalidSignature`: 交易签名无效。
  * `OutOfGasBase`: gas少于此次交易所需量。
  * `NotEnoughCash`: 发送者的账户余额不足。
  * `InvalidNonce`: 交易随机数错误。

### 叔块型

* `InvalidUncle`: 可分为以下3类：
  * `UncleInChain`: 叔块已被包含在当前区块链中（要么作为直接父块，要么作为其中一个已被包含的叔块）。
  * `UncleTooOld`: 当前叔块比第6代叔块生成时间还早。
  * `UncleIsBrother`: 当前叔块比第1代叔块生成时间还迟。

`VMTRACE`指：

```javascript
[
    {
        "stack": [ HEX, ... ]
        "memory": HEX, SOMETIMES
        "sha3memory": DATA_32, SOMETIMES
        "storage": { HEX: HEX }, SOMETIMES
        "gas": BIGINT
        "pc": BIGINT
        "inst": INT
        "depth": INT, OPTIONAL
        "steps": INT
        "address": DATA_20, SOMETIMES
        "memexpand": BIGINT, NONSTANDARD
        "gascost": BIGINT, NONSTANDARD
        "instname": STRING, NONSTANDARD
    },
    ...
]
```

* `stack`: 执行之前的堆栈。
* `memory`: 执行前的内存。如果先前的操作与内存无关（MLOAD / MSTORE / MSTORE8 / SHA3 / CALL / CALLCODE / CREATE / CALLDATACOPY / CODECOPY / EXTCODECOPY），也不是在CALL / CREATE或内存大于等于1024字节情况下的首次操作，那么将此对象省略。
* `sha3memory`: 执行之前内存的Keccak哈希值。如果先前的操作与内存无关（MLOAD / MSTORE / MSTORE8 / SHA3 / CALL / CALLCODE / CREATE / CALLDATACOPY / CODECOPY / EXTCODECOPY），也不是在CALL / CREATE或内存小于1024字节情况下的首次操作，那么将此对象省略。
* `storage`: 执行前受SSTORE控制的存储内容（RE-READ THAT!）。当先前的操作与存储无关（SLOAD / SSTORE），并且不是CALL / CREATE情况下的首次操作时，将省略此命令。
* `gas`: 在实行此指令前可用的gas量。
* `pc`: 执行之前的程序计数器。
* `inst`: 要执行的指令操作码索引，如STOP为0。
* `depth`: CALL / CREATE堆栈在当前背景下的深度。自从上次操作后再没有更改，并且不是CALL / CREATE背景下的首次操作，则将省略此对象。
* `steps`: 在执行当前指令之前，在当前的CALL / CREATE背景下已进行的步骤数。
* `address`: 由`MYADDRESS`操作码返回的帐户地址。自从上次操作后再没有更改，并且不是CALL / CREATE背景下的首次操作，则将省略此对象。
* `memexpand`: 扩展内存的大小。扩展内存为零时，省略此对象。
* `gascost`: 执行该指令所需的gas总量。从技术上来说，此对象指的是最高总gas成本，CALL / CREATE可能能够返回gas）。


# 最新版本

## [py-evm 0.3.0-alpha.8 \(2019-11-05\)](https://github.com/ethereum/py-evm/releases)

### 版本新增

* 根据EIP 225，部分实现了Clique共识，尚未涵盖作为签名者并创建区块的操作模式。但是可以通过EIP-225中定义的规则来同步链（例如Görli）。 \([＃1855](https://github.com/ethereum/py-evm/issues/1855)\)
* 按照[EIP 1679](https://eips.ethereum.org/EIPS/eip-1679)，在主网中将伊斯坦布尔\(Istanbul\)区块高度设置为9069000，在Görli中设置为1561651。\([\#1858](https://github.com/ethereum/py-evm/issues/1858)\)
* 可配置extra\_data字段的最大长度验证。\([＃1864](https://github.com/ethereum/py-evm/issues/1864)\)

### 版本修正

* 修复安装期间由于插件依赖性出现的版本冲突。 \([＃1860](https://github.com/ethereum/py-evm/issues/1860)\)
* 修复了将0用作seal\_check\_random\_sample\_rate的值时Py-EVM的崩溃问题。在之前版本中，这会导致DivideByZero错误，而现在会被视为未执行确认检查。\([＃1862](https://github.com/ethereum/py-evm/issues/1862)\)
* 通过包含受影响哈希的十六进制值来提高错误消息的可用性。\([\#1863](https://github.com/ethereum/py-evm/issues/1863)\)
* Gas估算错误修正：本版本运行估算迭代时，如果交易包含自毁操作，则存储值可以正确重置为原始值。在之前版本中，如果交易包含自毁操作，估算迭代会产生未定义结果。\([\#1865](https://github.com/ethereum/py-evm/issues/1865)\)

### 性能提升

* 使用新的blake2b-py库将Blake2 F压缩函数的速度提高了560倍。\([\#1836](https://github.com/ethereum/py-evm/issues/1836)\)

### 针对贡献者的内部修改

* 将上游测试固件更新到[v7.0.0 beta.1](https://github.com/ethereum/tests/releases/tag/v7.0.0-beta.1)，并解决了在嵌套调用框架有错误的情况下应收集哪些帐户以进行状态Trie清算（根据[EIP-161](https://eips.ethereum.org/EIPS/eip-161)）所产生两个分歧。\([\#1858](https://github.com/ethereum/py-evm/issues/1858)\)


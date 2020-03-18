---
description: v0.6.5版本
---

# Solidity

Solidity合约是采用一种特殊注释格式编写而成的包含函数、返回变量等的文档。这种特殊格式就是Ethereum Natural Language Specification Format，简称NatSpec。

建议开发者将Solidity合约的所有公共接口（即ABI接口）采用NatSpec格式进行注释。

NatSpec注释格式，为智能合约开发者所使用，Solidity编译器也可以读懂。下面详细展示了Solidity编译器的输出内容，其中Solidity将注释输出为计算机可读格式。

### 文档范例

按照doxygen注释格式往文档输入每个`class`，`interface`和`function`。

* 就Solidity而言，可以使用`///`或`/`_`* ...`_ `/`表示单行或多行注释。

以下示例展示了使用所有可用标签的合约和函数。

#### 注意

当前，NatSpec不适用于公共状态变量（请参见[`solidity#3418`](https://github.com/ethereum/solidity/issues/3418)），即使声明为适用于公共变量，也会因此对ABI产生不良影响。

Solidity编译器仅在标签公开时才对其进行编译。

```text
pragma solidity >=0.5.0 <0.7.0;

/// @title A simulator for trees
/// @author Larry A. Gardner
/// @notice You can use this contract for only the most basic simulation
/// @dev All function calls are currently implemented without side effects
contract Tree {
    /// @author Mary A. Botanist
    /// @notice Calculate tree age in years, rounded up, for live trees
    /// @dev The Alexandr N. Tetearing algorithm could increase precision
    /// @param rings The number of rings from dendrochronological sample
    /// @return age in years, rounded up for partial years
    function age(uint256 rings) external pure returns (uint256) {
        return rings + 1;
    }
}
```

### 文档输出

当由编译器编译时，诸如上述示例中的文档将生成两个不同的JSON文件。一个文档是在执行某种函数时起通知终端用户之用，另一个文档供开发者使用。

如果以上合约需要另存为`ex1.sol`，则可以使用以下命令生成文档：

```text
solc --userdoc --devdoc ex1.sol
```

以下是文档输出内容。

#### 终端用户文档

上面的文档将生成JSON用户文件：

```text
{
  "methods" :
  {
    "age(uint256)" :
    {
      "notice" : "Calculate tree age in years, rounded up, for live trees"
    }
  },
  "notice" : "You can use this contract for only the most basic simulation"
}
```

注意：除了函数名称以外，找到method的关键还在于合约ABI中定义的函数规范签名（function’s canonical signature）。

#### 开发者文档

除用户文档外，还应生成JSON开发者文件，内容如下所示：

```text
{
  "author" : "Larry A. Gardner",
  "details" : "All function calls are currently implemented without side effects",
  "methods" :
  {
    "age(uint256)" :
    {
      "author" : "Mary A. Botanist",
      "details" : "The Alexandr N. Tetearing algorithm could increase precision",
      "params" :
      {
        "rings" : "The number of rings from dendrochronological sample"
      },
      "return" : "age in years, rounded up for partial years"
    }
  },
  "title" : "A simulator for trees"
}
```


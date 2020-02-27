# 相关文档

## 黄皮书

以太坊白皮书概要性地介绍了以太坊，而以太坊黄皮书则通过大量的定义和公式详细地描述了以太坊的技术实现。

{% file src="../.gitbook/assets/yellow-paper.pdf" caption="ETHEREUM: A SECURE DECENTRALISED GENERALISED TRANSACTION LEDGER" %}

{% file src="../.gitbook/assets/ethereum\_yellow\_paper\_cn.pdf" caption="以太坊黄皮书中文版" %}

## “浅”黄皮书

黄皮书的简化重写版，增强可读性，使更多用户都能理解以太坊的技术规范。

{% file src="../.gitbook/assets/beigepaper.pdf" caption="以太坊浅黄皮书：以太坊技术规范" %}

## 果冻纸皮书

Jello Paper尝试使用[KEVM项目](https://github.com/kframework/evm-semantics)（K框架虚拟机）来定义EVM语义。与黄皮书不同，Jello Paper是可执行的语义，并且可以提供完整的EVM解释器，可用于测试合约、分析gas使用量，验证合约以及[KEVM技术报告](https://www.ideals.illinois.edu/handle/2142/97207)中指定的其他广泛用途。

Jello Paper描述的KEVM语义针对EVM的第一种机器可执行的、数学形式的、人类可读的完整语义。 KEVM能够通过整套EVM [VMTests](https://github.com/ethereum/tests/tree/develop/VMTests)和[GeneralStateTests](https://github.com/ethereum/tests/tree/develop/GeneralStateTests)测试，也可用于智能合约的形式验证和调试等。 Jello Paper（此文档）是根据[KEVM语义中的K定义](https://github.com/kframework/evm-semantics)自动生成的。

{% embed url="https://jellopaper.org/" %}




# 客户端地址交换协议

* [拟议设计](https://github.com/ethereum/wiki/wiki/Inter-exchange-Client-Address-Protocol-%28ICAP%29#proposed-design) - [直接编码](https://github.com/ethereum/wiki/wiki/Inter-exchange-Client-Address-Protocol-%28ICAP%29#direct) - [基础编码](https://github.com/ethereum/wiki/wiki/Inter-exchange-Client-Address-Protocol-%28ICAP%29#basic) - [间接编码](https://github.com/ethereum/wiki/wiki/Inter-exchange-Client-Address-Protocol-%28ICAP%29#indirect) 
  * [注意](https://github.com/ethereum/wiki/wiki/Inter-exchange-Client-Address-Protocol-%28ICAP%29#notes) 
  * [其他格式](https://github.com/ethereum/wiki/wiki/Inter-exchange-Client-Address-Protocol-%28ICAP%29#other-forms)
    * [URI ](https://github.com/ethereum/wiki/wiki/Inter-exchange-Client-Address-Protocol-%28ICAP%29#uri)
    * [QR Code](https://github.com/ethereum/wiki/wiki/Inter-exchange-Client-Address-Protocol-%28ICAP%29#qr-code) 
* [交易语义](https://github.com/ethereum/wiki/wiki/Inter-exchange-Client-Address-Protocol-%28ICAP%29#transaction-semantics)
  * [直接转账](https://github.com/ethereum/wiki/wiki/Inter-exchange-Client-Address-Protocol-%28ICAP%29#direct-1)
  * [间接ETH资产：简单转账](https://github.com/ethereum/wiki/wiki/Inter-exchange-Client-Address-Protocol-%28ICAP%29#indirect-eth-asset-simple-transfers)
  * [间接XET资产：机构转账](https://github.com/ethereum/wiki/wiki/Inter-exchange-Client-Address-Protocol-%28ICAP%29#indirect-xet-asset-institution-transfers)

由于客户帐户识别存款的方式存在问题，因此在第三方帐户（尤其是交易所的帐户）之间进行资金转帐给用户带来了相当大的负担，并且容易出错。现有银行业通过使用称为IBAN的通用号码解决了这个问题。该号码将银行和客户帐户合并，同时实行一种错误校验机制，从实际上消除小错误，为用户提供了极大的便利。不幸的是，这是一种受到严格监管的中心化服务，只有大型完备机构才能使用。ICAP协议，可以视为其去中心化版本，适用于任何在以太坊系统上存有资金的机构。

#### IBAN

要全面了解IBAN系统，请参阅维基百科里的IBAN介绍文章。 IBAN号码最多包含34个不区分大小写的字母数字字符。它包含三种信息：

* 国别代码，是以下内容的顶级标识符，遵循ISO 3166-1 alpha-2标准；
* 错误校验码，采用mod-97-10校验和协议（ISO / IEC 7064：2003）；
* 基本银行账户号码（BBAN），是银行、分行和客户帐户的标识符。不同国家，其组成不同。

就英国而言，BBAN由以下要素组成：

* 银行标识符（Institution identifier），包含4个字母，如`MIDL`代表汇丰银行。
* 排序码（Sort-code，银行的分行标识符），一个6位十进制数，如`402702`代表汇丰银行的兰开斯特分行。
* 帐户号码（Account number，分行的客户标识符），一个8位十进制数。

## 拟议设计

以太坊引入新的IBAN国别代码XE，前缀X表示延申意义，E表示Ethereum的首字母E，如非法定货币XRP，XCP。

有三种 BBAN 编码：直接编码，基本编码和间接编码。

#### 直接编码

直接编码为30个字符，并且包含一个字段：

* 帐户标识符（Account identifier），30个字母数字字符（&lt;156位），是一个大端序base-36整数，表示155位以太坊地址的最低有效位。因此，这些以太坊地址通常以零字节开头。

例如，XE7338O073KYGTWWZN0F2WZ0R8PX5ZPPZS的对应地址为`0x00c5496aee77c1ba1f0854206a26dda82a81d6d8`.

#### 基本编码

\*\* 与直接编码相同，只是长度为31个字符（因此与IBAN不兼容），但包含相同的单个字段：

* 帐户标识符（Account identifier），31个字母数字字符（&lt;161位），是一个大端序的base-36整数，代表一个160位以太坊地址。

#### 间接编码

间接编码为16个字符，并且包含三个字段：

* 资产标识符（Asset identifier），3个字母数字字符（&lt;16位）;
* 机构标识符（Institution identifier），4个字母数字字符（&lt;21位）;
* 机构客户标识符（Institution client identifier），9个字母数字字符（&lt;47位）;

还包括四个首字符，因此最终的客户帐户地址长度为20个字符，格式为：

```text
XE81ETHXREGGAVOFYORK
```

可划分为以下部分：

* `XE` 表示以太坊国别代码
* `81` 表示校验和
* `ETH` 表示客户账户中的资产标识符。在本示例中，“ETH”是唯一有效的资产标识符，因为以太坊的基础注册表合约仅支持此类资产。
* `XREG` 表示机构代码。在本示例中，表示以太坊的基础注册表合约。
* `GAVOFYORK` 表示机构客户标识符。在本示例中，表示直接付款方式，且不向与以太坊基础注册表合约中的名称“ GAVOFYORK”关联的任何首要地址添加数据；

### 注意

以`x`开头的机构代码仅供系统使用。

### 其他格式

#### 统一资源标识符（URI）

在20个字符的字母数字标识符前添加URI 方案名称（URI scheme name）`iban`和一个冒号`：`，就可以构成通用URI。上述示例的URI格式为：

```text
iban:XE81ETHXREGGAVOFYORK
```

#### 二维码

可以使用二维码编码标准直接用URI生成二维码。

例如，上述示例的URI格式`iban:XE81ETHXREGGAVOFYORK`具有相应的二维码。

![QR code for iban:XE81ETHXREGGAVOFYORK](https://camo.githubusercontent.com/17390df3301d4aa0a594bbcc425ea02dc0486f36/687474703a2f2f6f70656e736563726563792e636f6d2f71722d58453831455448585245474741564f46594f524b2e676966)

## 交易语义

进行间接资产转移采用的三种路由协议是特定的，所有这些协议都特定于以太坊域名（`XE`国别代码）。一种协议是用于将货币直接转账到已有地址，另一种适用于具有系统地址的客户端，这些地址通过客户端ID注册表查找系统查找而得，以资产类别`ETH`表示，而最后一种是用于转账到与指定客户端关联的中间方，以资产类别 `XET`表示（后两种协议是间接交易）。

### 直接转账

如果IBAN号码为34个字符，则其为直接地址；资产将直接转移到该地址，当用base-36编码该地址，将得出准确的IBAN代码数据段，即最后30个字符。

### 间接ETH资产：简单转账

只要代码以`XE`**`ETH`**开头（其中\*\*是有效的校验和），那么我们可以将所需交易定义为调用由机构代码表示的注册表合约的存款地址。若不是以`x`开头，交易则被定义为与以太坊标准名称：

\[institution code\] / \[client identifier\]

以太坊标准名称只是以太坊标准接口文档中指定的常规分层查找机制。

我们将注册表合约定义为符合以太坊标准接口文档中指定的注册表接口的合约。

### 间接XET资产：机构转账

只要代码以`XE ** XET`开头，那么我们可以导出必须通过查找以太坊`iban`注册表合约进行的交易。对于特定机构来说，该合约有两个特定值：存款调用签名哈希值和机构的以太坊地址。

目前，仅确定了一种存款调用，即：

```text
function deposit(uint64 clientAccount)
```

其签名哈希值为 `0x13765838`。转移资产交易应采用上述指定的`deposit` 方法来作为对机构以太坊地址的eth负载调用，客户端账户应通过机构客户端的字母数字标识符的大端base-36整数来确定，数值使用字符 `0`到 `9`，然后将'A'（或'a'）等值为10，将'B'（或'b'）等值为11，依此类推。




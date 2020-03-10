---
description: 递归长度前缀编码
---

# RLP 编码

**Contents**

* [定义](https://github.com/ethereum/wiki/wiki/RLP#definition)
* [例子](https://github.com/ethereum/wiki/wiki/RLP#examples)
* [RLP解码](https://github.com/ethereum/wiki/wiki/RLP#rlp-decoding)
* [代码实现](https://github.com/ethereum/wiki/wiki/RLP#implementations)

RLP \(递归长度前缀\)提供了一种适用于任意二进制数据数组的编码，RLP已经成为以太坊中对对象进行序列化的主要编码方式。 RLP的唯一目标就是解决结构体的编码问题；对原子数据类型（比如，字符串，整数型，浮点型）的编码则交给更高层的协议；以太坊中要求数字必须是大端字节序的、没有零占位的存储格式（也就是说，一个整数0和一个空数组是等同的）。具有零占位的反序列化正整数必须视为无效。字符串长度的数字以及内容中的数字也必须采用这种方式进行编码。更多信息请查阅以太坊黄皮书的附录B。

对于在 RLP 格式中对一个字典数据的编码问题，有两种建议的方式，一种是通过二维数组表达键值对，比如`[[k1,v1],[k2,v2]...]`，并且对键进行字典序排序；另一种方式是通过以太坊文档中提到的高级[基数树](https://github.com/ethereum/wiki/wiki/Patricia-Tree)编码来实现。

总之，如果JSON仅适用于字符串和数组，则RLP类似于JSON的二进制编码。

#### 定义

RLP 编码函数接受一个item。定义如下：

* 将一个字符串作为一个item（比如，一个 byte 数组）
* 一组item列表（list）作为一个item

例如，一个空字符串可以是一个item，一个字符串"cat"也可以是一个item，一个含有多个字符串的列表也行，复杂的数据结构也行，比如这样的`["cat",["puppy","cow"],"horse",[[]],"pig",[""],"sheep"]`。注意在本文后续内容中，说"字符串"的意思其实就相当于"一个确定长度的二进制字节信息数据"；而不要假设或考虑关于字符的编码问题。

RLP 编码定义如下：

* 对于 `[0x00, 0x7f]` 范围内的单个字节, RLP 编码内容就是字节内容本身。
* 否则，如果是一个 0-55 字节长的字符串，则RLP编码由一个特别的数值 **0x80** 加上字符串长度，再加上字符串二进制内容。这样，第一个字节的表达范围为 `[0x80, 0xb7]`.
* 如果字符串长度超过 55 个字节，RLP 编码由定值 **0xb7** 加上字符串长度所占用的字节数，加上字符串长度的编码，加上字符串二进制内容组成。比如，一个长度为 1024 的字符串，将被编码为`\xb9\x04\x00` 后面再加上字符串内容。第一字节的表达范围是`[0xb8, 0xbf]`。
* 如果列表的内容（它的所有项的组合长度）是0-55个字节长，它的RLP编码由**0xC0**加上所有的项的RLP编码串联起来的长度得到的单个字节，后跟所有的项的RLP编码的串联组成。 第一字节的范围因此是`[0xc0, 0xf7]`
* 如果列表的内容超过55字节，它的RLP编码由 **0xf7** 加上所有的项的RLP编码串联起来的长度的长度得到的单个字节，后跟所有的项的RLP编码串联起来的长度，再后跟所有的项的RLP编码的串联组成。 第一字节的范围因此是`[0xf8, 0xff]` 。

用Python代码表达以上逻辑:

```text
def rlp_encode(input):
    if isinstance(input,str):
        if len(input) == 1 and chr(input) < 128: return input
        else: return encode_length(len(input),128) + input
    elif isinstance(input,list):
        output = ''
        for item in input: output += rlp_encode(item)
        return encode_length(len(output),192) + output

def encode_length(L,offset):
    if L < 56:
         return chr(L + offset)
    elif L < 256**8:
         BL = to_binary(L)
         return chr(len(BL) + offset + 55) + BL
    else:
         raise Exception("input too long")

def to_binary(x):
    return '' if x == 0 else to_binary(int(x / 256)) + chr(x % 256)
```

#### 例子

字符串 "dog" = \[ 0x83, 'd', 'o', 'g' \]

列表 \[ "cat", "dog" \] = `[ 0xc8, 0x83, 'c', 'a', 't', 0x83, 'd', 'o', 'g' ]`

空字符串 \('null'\) = `[ 0x80 ]`

空列表 = `[ 0xc0 ]`

数字0 = `[ 0x80 ]`

数字15 \('\x0f'\) = `[ 0x0f ]`

数字 1024 \('\x04\x00'\) = `[ 0x82, 0x04, 0x00 ]`

[集合论表达](https://en.wikipedia.org/wiki/Set-theoretic_definition_of_natural_numbers)， `[ [], [[]], [ [], [[]] ] ] = [ 0xc7, 0xc0, 0xc1, 0xc0, 0xc3, 0xc0, 0xc1, 0xc0 ]`

字符串 "Lorem ipsum dolor sit amet, consectetur adipisicing elit" = `[ 0xb8, 0x38, 'L', 'o', 'r', 'e', 'm', ' ', ... , 'e', 'l', 'i', 't' ]`



根据RLP编码的规则和过程，RLP解码的输入数据应被视为二进制数组，过程如下：

1. 根据输入数据的首字节（即前缀），解码数据类型、实际数据的长度和偏移量；
2. 根据数据类型和其偏移量，对数据进行相应的解码；
3. 继续解码剩余的输入数据

其中，解码数据类型和偏移量的规则如下：

1. 如果首字节（即前缀）的值在\[0x00，0x7f\]范围之内，那么该数据为字符串，且该字符串就是首字节本身，；
2. 如果首字节的值在\[0x80，0xb7\]范围之内，并且数据长度等于首字节减去0x80，那么该数据为字符串，且位于首字节之后。
3. 如果首字节的值在\[0xb8，0xbf\]范围之内，那么该数据为字符串，且该字符串的字节长度等于首字节减去0xb7，字符串长度位于首字节之后，字符串位于字符串长度之后。
4. 如果首字节的值在\[0xc0，0xf7\]范围之内，那么该数据为列表，这时则需要对列表里的各项item进行RLP递归解码。列表总长度等于首字节减去0xc0，列表各项item递归解码位于首字节之后。
5. 如果首字节的值在\[0xf8，0xff\]范围之内，那么该数据为列表，列表长度等于首字节减去0xf7，列表总长度位于首字节之后，并且列表各项item递归解码位于列表总长度之后。

代码如下：

```text
def rlp_decode(input):
    if len(input) == 0:
        return
    output = ''
    (offset, dataLen, type) = decode_length(input)
    if type is str:
        output = instantiate_str(substr(input, offset, dataLen))
    elif type is list:
        output = instantiate_list(substr(input, offset, dataLen))
    output = output + rlp_decode(substr(input, offset + dataLen))
    return output

def decode_length(input):
    length = len(input)
    if length == 0:
        raise Exception("input is null")
    prefix = ord(input[0])
    if prefix <= 0x7f:
        return (0, 1, str)
    elif prefix <= 0xb7 and length > prefix - 0x80:
        strLen = prefix - 0x80
        if strLen == 1 and ord(input[1]) <= 0x7f:
            raise Exception("single byte below 128 must be encoded as itself")
        return (1, strLen, str)
    elif prefix <= 0xbf and length > prefix - 0xb7 and length > prefix - 0xb7 + to_integer(substr(input, 1, prefix - 0xb7)):
        lenOfStrLen = prefix - 0xb7
        if input[1] == 0:
            raise Exception("multi-byte length must have no leading zero");
        strLen = to_integer(substr(input, 1, lenOfStrLen))
        if strLen < 56:
            raise Exception("length below 56 must be encoded in one byte");
        return (1 + lenOfStrLen, strLen, str)
    elif prefix <= 0xf7 and length > prefix - 0xc0:
        listLen = prefix - 0xc0;
        return (1, listLen, list)
    elif prefix <= 0xff and length > prefix - 0xf7 and length > prefix - 0xf7 + to_integer(substr(input, 1, prefix - 0xf7)):
        lenOfListLen = prefix - 0xf7
        if input[1] == 0:
            raise Exception("multi-byte length must have no leading zero");
        listLen = to_integer(substr(input, 1, lenOfListLen))
        if listLen < 56:
            raise Exception("length below 56 must be encoded in one byte");
        return (1 + lenOfListLen, listLen, list)
    else:
        raise Exception("input don't conform RLP encoding form")

def to_integer(b):
    length = len(b)
    if length == 0:
        raise Exception("input is null")
    elif length == 1:
        return ord(b[0])
    else:
        return ord(substr(b, -1)) + to_integer(substr(b, 0, -1)) * 256
```

注意：`decode_length`函数会拒绝“非最佳”长度的无效编码，即（1）仅字节小于128的单例字符串，其应使用短长度（即一个字节）为1进行编码，而不是用字符串本身（2）字符串和列表长度较长（即多字节）的，且具有零占位（本来不应该存在），或长度小于56（本来应使用短长度进行编码）。

#### 代码实现

* Go: [go-ethereum](https://github.com/ethereum/go-ethereum/tree/master/rlp)
* Java: [web3j](https://github.com/web3j/web3j/blob/master/rlp/src/main/java/org/web3j/rlp/RlpDecoder.java), [ethereumj](https://github.com/ethereumj/ethereumj/blob/master/ethereumj-core/src/main/java/org/ethereum/util/RLP.java), [headlong](https://github.com/esaulpaugh/headlong/tree/master/src/main/java/com/esaulpaugh/headlong/rlp)
* Javascript: [rlp](https://github.com/ethereumjs/rlp)
* Kotlin: [KEthereum/rlp](https://github.com/walleth/kethereum/tree/master/rlp)
* Objective-C: [RLP-ObjC](https://github.com/wjmelements/rlp-objc)
* Python: [pyrlp](https://github.com/ethereum/pyrlp)
* Swift: [RLPSwift](https://github.com/bitfwdcommunity/RLPSwift), [RLP](https://github.com/uport-project/swift-rlp)
* PHP: [rlp](https://github.com/web3p/rlp)

部分内容引用自：[https://github.com/ethereum/wiki/wiki/%5B%E4%B8%AD%E6%96%87%5D-RLP](https://github.com/ethereum/wiki/wiki/%5B%E4%B8%AD%E6%96%87%5D-RLP)


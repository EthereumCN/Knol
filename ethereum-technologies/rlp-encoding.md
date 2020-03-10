---
description: 递归长度前缀编码
---

# RLP 编码

**Contents**

* [Definition](https://github.com/ethereum/wiki/wiki/RLP#definition)
* [Examples](https://github.com/ethereum/wiki/wiki/RLP#examples)
* [RLP decoding](https://github.com/ethereum/wiki/wiki/RLP#rlp-decoding)
* [Implementations](https://github.com/ethereum/wiki/wiki/RLP#implementations)

The purpose of RLP \(Recursive Length Prefix\) is to encode arbitrarily nested arrays of binary data, and RLP is the main encoding method used to serialize objects in Ethereum. The only purpose of RLP is to encode structure; encoding specific data types \(eg. strings, floats\) is left up to higher-order protocols; but positive RLP integers must be represented in big endian binary form with no leading zeroes \(thus making the integer value zero be equivalent to the empty byte array\). Deserialised positive integers with leading zeroes must be treated as invalid. The integer representation of string length must also be encoded this way, as well as integers in the payload. Additional information can be found in the Ethereum yellow paper Appendix B.

If one wishes to use RLP to encode a dictionary, the two suggested canonical forms are to either use `[[k1,v1],[k2,v2]...]` with keys in lexicographic order or to use the higher-level [Patricia Tree](https://github.com/ethereum/wiki/wiki/Patricia-Tree) encoding as Ethereum does.

In summary, RLP is like a binary encoding of JSON, if JSON were restricted only to strings and arrays.

#### Definition

The RLP encoding function takes in an item. An item is defined as follows：

* A string \(ie. byte array\) is an item
* A list of items is an item

For example, an empty string is an item, as is the string containing the word "cat", a list containing any number of strings, as well as more complex data structures like `["cat",["puppy","cow"],"horse",[[]],"pig",[""],"sheep"]`. Note that in the context of the rest of this article, "string" will be used as a synonym for "a certain number of bytes of binary data"; no special encodings are used and no knowledge about the content of the strings is implied.

RLP encoding is defined as follows:

* For a single byte whose value is in the `[0x00, 0x7f]` range, that byte is its own RLP encoding.
* Otherwise, if a string is 0-55 bytes long, the RLP encoding consists of a single byte with value **0x80** plus the length of the string followed by the string. The range of the first byte is thus `[0x80, 0xb7]`.
* If a string is more than 55 bytes long, the RLP encoding consists of a single byte with value **0xb7** plus the length in bytes of the length of the string in binary form, followed by the length of the string, followed by the string. For example, a length-1024 string would be encoded as `\xb9\x04\x00` followed by the string. The range of the first byte is thus `[0xb8, 0xbf]`.
* If the total payload of a list \(i.e. the combined length of all its items being RLP encoded\) is 0-55 bytes long, the RLP encoding consists of a single byte with value **0xc0** plus the length of the list followed by the concatenation of the RLP encodings of the items. The range of the first byte is thus `[0xc0, 0xf7]`.
* If the total payload of a list is more than 55 bytes long, the RLP encoding consists of a single byte with value **0xf7** plus the length in bytes of the length of the payload in binary form, followed by the length of the payload, followed by the concatenation of the RLP encodings of the items. The range of the first byte is thus `[0xf8, 0xff]`.

In code, this is:

```text
def rlp_encode(input):
    if isinstance(input,str):
        if len(input) == 1 and ord(input) < 0x80: return input
        else: return encode_length(len(input), 0x80) + input
    elif isinstance(input,list):
        output = ''
        for item in input: output += rlp_encode(item)
        return encode_length(len(output), 0xc0) + output

def encode_length(L,offset):
    if L < 56:
         return chr(L + offset)
    elif L < 256**8:
         BL = to_binary(L)
         return chr(len(BL) + offset + 55) + BL
    else:
         raise Exception("input too long")

def to_binary(x):
    if x == 0:
        return ''
    else: 
        return to_binary(int(x / 256)) + chr(x % 256)
```

#### Examples

The string "dog" = \[ 0x83, 'd', 'o', 'g' \]

The list \[ "cat", "dog" \] = `[ 0xc8, 0x83, 'c', 'a', 't', 0x83, 'd', 'o', 'g' ]`

The empty string \('null'\) = `[ 0x80 ]`

The empty list = `[ 0xc0 ]`

The integer 0 = `[ 0x80 ]`

The encoded integer 0 \('\x00'\) = `[ 0x00 ]`

The encoded integer 15 \('\x0f'\) = `[ 0x0f ]`

The encoded integer 1024 \('\x04\x00'\) = `[ 0x82, 0x04, 0x00 ]`

The [set theoretical representation](http://en.wikipedia.org/wiki/Set-theoretic_definition_of_natural_numbers) of three, `[ [], [[]], [ [], [[]] ] ] = [ 0xc7, 0xc0, 0xc1, 0xc0, 0xc3, 0xc0, 0xc1, 0xc0 ]`

The string "Lorem ipsum dolor sit amet, consectetur adipisicing elit" = `[ 0xb8, 0x38, 'L', 'o', 'r', 'e', 'm', ' ', ... , 'e', 'l', 'i', 't' ]`

#### RLP decoding

According to rules and process of RLP encoding, the input of RLP decode shall be regarded as array of binary data, the process is as follows:

1. According to the first byte\(i.e. prefix\) of input data, and decoding the data type, the length of the actual data and offset;
2. According to type and offset of data, decode data correspondingly;
3. Continue to decode the rest of the input;

Among them, the rules of decoding data types and offset is as follows:

1. the data is a string if the range of the first byte\(i.e. prefix\) is \[0x00, 0x7f\], and the string is the first byte itself exactly;
2. the data is a string if the range of the first byte is \[0x80, 0xb7\], and the string whose length is equal to the first byte minus 0x80 follows the first byte;
3. the data is a string if the range of the first byte is \[0xb8, 0xbf\], and the length of the string whose length in bytes is equal to the first byte minus 0xb7 follows the first byte, and the string follows the length of the string;
4. the data is a list if the range of the first byte is \[0xc0, 0xf7\], and the concatenation of the RLP encodings of all items of the list which the total payload is equal to the first byte minus 0xc0 follows the first byte;
5. the data is a list if the range of the first byte is \[0xf8, 0xff\], and the total payload of the list whose length is equal to the first byte minus 0xf7 follows the first byte, and the concatenation of the RLP encodings of all items of the list follows the total payload of the list;

In code, this is:

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

Note that the `decode_length` function rejects invalid encodings that have "non-optimal" lengths, namely \(1\) singleton strings whose only byte is below 128 that are encoded with a short \(i.e. one-byte\) length of 1 instead of as the strings themselves and \(2\) strings and lists with long \(i.e. multi-byte\) lengths with leading zeros \(which must be absent\) or below 56 \(which should be encoded using short lengths\).

#### Implementations

* Go: [go-ethereum](https://github.com/ethereum/go-ethereum/tree/master/rlp)
* Java: [web3j](https://github.com/web3j/web3j/blob/master/rlp/src/main/java/org/web3j/rlp/RlpDecoder.java), [ethereumj](https://github.com/ethereumj/ethereumj/blob/master/ethereumj-core/src/main/java/org/ethereum/util/RLP.java), [headlong](https://github.com/esaulpaugh/headlong/tree/master/src/main/java/com/esaulpaugh/headlong/rlp)
* Javascript: [rlp](https://github.com/ethereumjs/rlp)
* Kotlin: [KEthereum/rlp](https://github.com/walleth/kethereum/tree/master/rlp)
* Objective-C: [RLP-ObjC](https://github.com/wjmelements/rlp-objc)
* Python: [pyrlp](https://github.com/ethereum/pyrlp)
* Swift: [RLPSwift](https://github.com/bitfwdcommunity/RLPSwift), [RLP](https://github.com/uport-project/swift-rlp)
* PHP: [rlp](https://github.com/web3p/rlp)

\| [Deutsch](https://github.com/ethereum/wiki/wiki/%5BGerman%5D-Ethereum-TOC) \| [English](https://github.com/ethereum/wiki/wiki) \| [Español](https://github.com/ethereum/wiki/wiki/%5BSpanish%5D-Ethereum-TOC) \| [Français](https://github.com/ethereum/wiki/wiki/%5BFrench%5D-Ethereum-TOC) \| [한국어](https://github.com/ethereum/wiki/wiki/%5BKorean%5D-White-Paper) \| [Italiano](https://github.com/ethereum/wiki/wiki/%5BItalian%5D-Ethereum-TOC) \| [Portuguese](https://github.com/ethereum/wiki/wiki/%5BPortuguese%5D-White-Paper/) \| [Română](https://github.com/ethereum/wiki/wiki/%5BRomanian%5D-Cuprins) \| [العربية](https://github.com/ethereum/wiki/wiki/%D8%A7%D9%84%D8%B9%D8%B1%D8%A8%D9%8A%D8%A9) \| [فارسی](https://github.com/ethereum/wiki/wiki/%5BPersian%5D-Ethereum-TOC) \| [中文\(繁体\)](https://github.com/ethereum/wiki/wiki/%5BChinese%5D-Ethereum-TOC)

\| [中文\(简体\)](https://github.com/ethereum/wiki/wiki/%5BSimplified-Chinese%5D-Ethereum-TOC) \| [日本語](https://github.com/ethereum/wiki/wiki/%5BJapanese%5D-Ethereum-TOC)

####  Pages 231

## Basics

* [Home](https://github.com/ethereum/wiki/wiki/)
* [Ethereum Whitepaper](https://github.com/ethereum/wiki/wiki/White-Paper)
* [Ethereum Introduction](https://github.com/ethereum/wiki/wiki/Ethereum-introduction)
* [Uses: DAOs and dapps](https://github.com/ethereum/wiki/wiki/Decentralized-apps-%28dapps%29)
* [Getting Ether](https://github.com/ethereum/wiki/wiki/Getting-Ether)
* [Releases](https://github.com/ethereum/wiki/wiki/Releases)
* [FAQs](https://github.com/ethereum/wiki/wiki/FAQs)
* [Design Rationale](https://github.com/ethereum/wiki/wiki/Design-Rationale)
* EVM intro: [Ethereum Yellow Paper](https://ethereum.github.io/yellowpaper/paper.pdf), [Beige Paper](https://github.com/chronaeon/beigepaper) and [Py-EVM](https://github.com/ethereum/py-evm).
* [Wiki for \(old\) website](https://github.com/ethereum/ethereum.org/wiki) \(still a good introduction\)
* [Glossary](https://github.com/ethereum/wiki/wiki/Glossary)

## [R&D](https://github.com/ethereum/wiki/wiki/R&D)

* [Sharding introduction & R&D Compendium](https://github.com/ethereum/wiki/wiki/Sharding-introduction-R&D-compendium), [FAQs](https://github.com/ethereum/wiki/wiki/Sharding-FAQs) & [roadmap](https://github.com/ethereum/wiki/wiki/Sharding-roadmap)
* [Casper Proof-of-Stake compendium](https://github.com/ethereum/wiki/wiki/Casper-Proof-of-Stake-compendium) and [FAQs](https://github.com/ethereum/wiki/wiki/Proof-of-Stake-FAQs).
* [Alternative blockchains, randomness, economics, and other research topics](https://github.com/ethereum/wiki/wiki/Alternative-blockchains,-randomness,-economics,-and-other-research-topics)
* [Hard Problems of Cryptocurrency](https://github.com/ethereum/wiki/wiki/Problems)
* [Governance](https://github.com/ethereum/wiki/wiki/Governance-compendium)

## [Ethereum Virtual Machine \(EVM\)](https://github.com/ethereum/wiki/wiki/Ethereum-Virtual-Machine-%28EVM%29-Awesome-List)

## [Ethereum clients, tools, wallets, dapp browsers and other projects](https://github.com/ethereum/wiki/wiki/Clients,-tools,-dapp-browsers,-wallets-and-other-projects)

## [ÐApp Development](https://github.com/ethereum/wiki/wiki/%C3%90App-Development)

## Infrastructure

* [Chain Spec Format](https://github.com/ethereum/wiki/wiki/Ethereum-Chain-Spec-Format)
* [Inter-exchange Client Address Protocol](https://github.com/ethereum/wiki/wiki/ICAP:-Inter-exchange-Client-Address-Protocol)
* [URL Hint Protocol](https://github.com/ethereum/wiki/wiki/URL-Hint-Protocol)
* [NatSpec Determination](https://github.com/ethereum/wiki/wiki/NatSpec-Determination)
* [Network Status](https://github.com/ethereum/wiki/wiki/Network-Status)
* [Raspberry Pi](https://github.com/ethereum/wiki/wiki/Raspberry-Pi-instructions)
* [Mining](https://github.com/ethereum/wiki/wiki/Mining)
* [Licensing](https://github.com/ethereum/wiki/wiki/Licensing)
* [Consortium Chain Development](https://github.com/ethereum/wiki/wiki/Consortium-Chain-Development)

## Networking

* [Ethereum Wire Protocol](https://github.com/ethereum/devp2p/blob/master/caps/eth.md)
* [libp2p](https://libp2p.io/)
* [devp2p Specifications](https://github.com/ethereum/devp2p)
* [devp2p Whitepaper \(old\)](https://github.com/ethereum/wiki/wiki/libp2p-Whitepaper)

## Ethereum Technologies

* [RLP Encoding](https://github.com/ethereum/wiki/wiki/RLP)
* [Patricia Tree](https://github.com/ethereum/wiki/wiki/Patricia-Tree)
* [Web3 Secret Storage](https://github.com/ethereum/wiki/wiki/Web3-Secret-Storage-Definition)
* [Light client protocol](https://github.com/ethereum/wiki/wiki/Light-client-protocol)
* [Subtleties](https://github.com/ethereum/wiki/wiki/Subtleties)
* [Solidity Documentation](https://solidity.readthedocs.io/en/latest/)
* [NatSpec Format](https://github.com/ethereum/wiki/wiki/Ethereum-Natural-Specification-Format)
* [Contract ABI](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI)
* [Bad Block Reporting](http://github.com/ethereum/wiki/wiki/Bad-Block-Reporting)
* [Bad Chain Canary](http://github.com/ethereum/wiki/wiki/Bad-Chain-Canary)

## Ethash/Dashimoto

* [Ethash](https://github.com/ethereum/wiki/wiki/Ethash)
* [Ethash Yellow Paper appendix](https://ethereum.github.io/yellowpaper/paper.pdf#appendix.J)
* [Ethash C API](https://github.com/ethereum/wiki/wiki/Ethash-C-API)
* [Ethash DAG](https://github.com/ethereum/wiki/wiki/Ethash-DAG)

## [Whisper](https://github.com/ethereum/wiki/wiki/Whisper-pages)

* [Whisper Proposal](https://github.com/ethereum/wiki/wiki/Whisper)
* [Whisper Overview](https://github.com/ethereum/wiki/wiki/Whisper-Overview)
* [PoC-1 Wire protocol](https://github.com/ethereum/wiki/wiki/Whisper-Wire-Protocol)
* [PoC-2 Wire protocol](https://github.com/ethereum/wiki/wiki/Whisper-PoC-2-Wire-Protocol)
* [PoC-2 Whitepaper](https://github.com/ethereum/wiki/wiki/Whisper-PoC-2-Protocol-Spec)

0x927c0E368722206312D243417dA9797424b56434

**Clone this wiki locally**

* © 2020 GitHub, Inc.
* [Terms](https://github.com/site/terms)
* [Privacy](https://github.com/site/privacy)
* [Security](https://github.com/security)
* [Status](https://githubstatus.com/)
* [Help](https://help.github.com/)
* [Contact GitHub](https://github.com/contact)
* [Pricing](https://github.com/pricing)
* [API](https://developer.github.com/)
* [Training](https://training.github.com/)
* [Blog](https://github.blog/)
* [About](https://github.com/about)


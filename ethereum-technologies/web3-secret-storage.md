# Web3私密存储

* [定义](https://github.com/ethereum/wiki/wiki/Web3-Secret-Storage-Definition#definition)
* [测试向量](https://github.com/ethereum/wiki/wiki/Web3-Secret-Storage-Definition#test-vectors)
  * [PBKDF2-SHA-256](https://github.com/ethereum/wiki/wiki/Web3-Secret-Storage-Definition#pbkdf2-sha-256)
  * [Scrypt](https://github.com/ethereum/wiki/wiki/Web3-Secret-Storage-Definition#scrypt)
* [版本1的修改](https://github.com/ethereum/wiki/wiki/Web3-Secret-Storage-Definition#alterations-from-version-1)
* [版本2的修改](https://github.com/ethereum/wiki/wiki/Web3-Secret-Storage-Definition#alterations-from-version-2)

要想自己的app在以太坊上运作，可以使用web3.js库提供的web3对象。web3对象通过后台调用RPC与本地节点通信。web3.js可以连接任何开放RPC层的以太坊节点。

web3.js包括eth对象，即具体用于以太坊区块链交互的web3.eth，也包括shh对象，即用于Whisper交互的web3.shh。以后我们将引入更多适用于其他web3协议的对象。代码示例如下所示：

```text
var fs = require('fs');
var recognizer = require('ethereum-keyfile-recognizer');

fs.readFile('keyfile.json', (err, data) => {
var json = JSON.parse(data);
var result = recognizer(json);**# **
/** result
  *               [ 'web3', 3 ]   web3 (v3) keyfile
  *  [ 'ethersale', undefined ]   Ethersale keyfile
  *                        null     invalid keyfile
 */
 }));
```



本文档介绍了Web3私密存储版本3的定义。

## 定义

文件里编码和解码内容大部分与版本1相同，只是加密算法不再是AES-128-CBC（目前最低要求是AES-128-CTR）。 除了添加`mac`以外，大多数含义/算法都与版本1相似。`mac`是衍生密钥最左数起第二个16字节与`ciphertext`串联后经过 SHA3 \(keccak-256\)哈希得出的结果。

在类似Unix系统中，密钥文件直接存储在`~/.web3/keystore`，在Windows系统中，则直接存储在`~/AppData/Web3/keystore`。可随意命名，但惯例是取为`<uuid>.json`，`<uuid>`是密钥的128位UUID，它是密钥地址的隐私保护代理（proxy）。

所有此类文件均具有关联的密码。要导出给定的.json文件密钥，首先要导出文件的加密密钥，这要通过获取文件的密码并将这个密码代入到`kdf`密钥所描述的密钥导出函数来获得。`kdfparams`键中描述了KDF函数的KDF相关静态和动态参数。

PBKDF2必须得到所有最低符合性实现的支持，表示为：

* `kdf`: `pbkdf2`

PBKDF2算法中的`kdfparams`包括：

* `prf`：必须是`hmac-sha256`，将来可能会改变
* `c`：迭代次数
* `salt`：传递给PBKDF的salt值
* `dklen`：导出密钥的长度，必须&gt;=32

导出文件的密钥后，应使用导出MAC对其进行验证。MAC由对一个字节数组进行SHA3（keccak-256）哈希而得，该字节数组由导出密钥的最左第二个16字节与`ciphertext`的内容串联而成，即：

```text
KECCAK(DK[16..31] ++ <ciphertext>)
```

（此处的`++`指把两者串联起来）

得出的值应与`mac`密钥进行比对， 如果不同，则应请求一个备用密码（或取消该操作）。

在验证了文件的密钥之后，可以使用由密钥指定的对称加密算法对密文（文件中的密文密钥）进行解密，并通过密文密钥对其进行参数化。 如果导出密钥的长度与算法的密钥长度不匹配，则应使用导出密钥最右边的字节的填充零作为算法的密钥。

所有最低符合度实现必须支持AES-128-CTR 算法，表示为：

* `cipher`: `aes-128-ctr`

作为`cipherparams`键的密钥，该cipher采用以下参数：

* `iv`: cipher的128位初始化向量。

该cipher的密钥是导出密钥的最左16字节，即`DK[0..15]`

在本质上，密钥的创建应逆向实施这些指令。 确保`uuid`，`salt`和`iv`是随机的。

除了表示版本“硬”标识符的`version`字段外，实现还可能会使用`minorversion`来跟踪格式的不间断小更改。

## 测试向量

细节部分：

* 地址: `008aeeda4d805471df9b2a5b0f38a0c3bcba786b`
* ICAP: `XE542A5PZHH8PYIZUBEJEO0MFWRAPPIL67`
* UUID: `3198bc9c-6672-5ab3-d9954942343ae5b6`
* 密码: `testpassword`
* 密钥: `7a28b5ba57c53603b0b07b56bba752f7784bf506fa95edc395f5cf6c7514fe9d`

### PBKDF2-SHA-256

使用AES-128-CTR 和 PBKDF2-SHA-256算法的测试向量及`~/.web3/keystore/3198bc9c-6672-5ab3-d9954942343ae5b6.json`的文件内容如下:

```javascript
{
    "crypto" : {
        "cipher" : "aes-128-ctr",
        "cipherparams" : {
            "iv" : "6087dab2f9fdbbfaddc31a909735c1e6"
        },
        "ciphertext" : "5318b4d5bcd28de64ee5559e671353e16f075ecae9f99c7a79a38af5f869aa46",
        "kdf" : "pbkdf2",
        "kdfparams" : {
            "c" : 262144,
            "dklen" : 32,
            "prf" : "hmac-sha256",
            "salt" : "ae3cd4e7013836a3df6bd7241b12db061dbe2c6785853cce422d148a624ce0bd"
        },
        "mac" : "517ead924a9d0dc3124507e3393d175ce3ff7c1e96529c6c555ce9e51205e9b2"
    },
    "id" : "3198bc9c-6672-5ab3-d995-4942343ae5b6",
    "version" : 3
}
```

Intermediates:

* Derived Key: `f06d69cdc7da0faffb1008270bca38f5e31891a3a773950e6d0fea48a7188551`
* MAC Body: `e31891a3a773950e6d0fea48a71885515318b4d5bcd28de64ee5559e671353e16f075ecae9f99c7a79a38af5f869aa46`
* MAC `517ead924a9d0dc3124507e3393d175ce3ff7c1e96529c6c555ce9e51205e9b2`
* Cipher key: `f06d69cdc7da0faffb1008270bca38f5`

### Scrypt

使用AES-128-CTR 和 Scrypt算法的测试向量：

```javascript
{
    "crypto" : {
        "cipher" : "aes-128-ctr",
        "cipherparams" : {
            "iv" : "83dbcc02d8ccb40e466191a123791e0e"
        },
        "ciphertext" : "d172bf743a674da9cdad04534d56926ef8358534d458fffccd4e6ad2fbde479c",
        "kdf" : "scrypt",
        "kdfparams" : {
            "dklen" : 32,
            "n" : 262144,
            "p" : 8,
            "r" : 1,
            "salt" : "ab0c7876052600dd703518d6fc3fe8984592145b591fc8fb5c6d43190334ba19"
        },
        "mac" : "2103ac29920d71da29f15d75b4a16dbe95cfd7ff8faea1056c33131d846e3097"
    },
    "id" : "3198bc9c-6672-5ab3-d995-4942343ae5b6",
    "version" : 3
}
```

Intermediates:

* Derived key: `fac192ceb5fd772906bea3e118a69e8bbb5cc24229e20d8766fd298291bba6bd`
* MAC Body `bb5cc24229e20d8766fd298291bba6bdd172bf743a674da9cdad04534d56926ef8358534d458fffccd4e6ad2fbde479c`
* MAC: `2103ac29920d71da29f15d75b4a16dbe95cfd7ff8faea1056c33131d846e3097`
* Cipher key: `fac192ceb5fd772906bea3e118a69e8b`

## 版本1的修改

此版本修改了[版本1](https://github.com/ethereum/homestead-guide/blob/master/old-docs-for-reference/go-ethereum-wiki.rst/Passphrase-protected-key-store-spec.rst)的一些不合理之处。 简而言之：

* 大小写不合理且不一致（`scrypt`小写，`Kdf`混合大小写，`MAC`大写）。
* `Address`不必要，会损害隐私。
* `Salt`本质上是密钥导出函数的参数，应该与函数相关联，一般来说，不应与加密相关联。
* `SaltLen`不必要，因为能从`Salt`看出。
* 虽然给出了密钥导出函数，但是很难确定加密算法。
* `Version`本质上是一串数字，但它也是一串字符串（字符串可以进行结构化版本控制，但可以认为，几乎不更改的配置文件格式不能进行结构化版本控制）。
* KDF和cipher是同级概念，但是两者的组织方式不同。
* MAC是只能通过计算与空格无关的数据来得出。

以下文件中对格式进行了更改，该文件在功能上等同于先前链接页面上给出的示例：

```javascript
{
    "crypto": {
        "cipher": "aes-128-cbc",
        "ciphertext": "07533e172414bfa50e99dba4a0ce603f654ebfa1ff46277c3e0c577fdc87f6bb4e4fe16c5a94ce6ce14cfa069821ef9b",
        "cipherparams": {
            "iv": "16d67ba0ce5a339ff2f07951253e6ba8"
        },
        "kdf": "scrypt",
        "kdfparams": {
            "dklen": 32,
            "n": 262144,
            "p": 1,
            "r": 8,
            "salt": "06870e5e6a24e183a5c807bd1c43afd86d573f7db303ff4853d135cd0fd3fe91"
        },
        "mac": "8ccded24da2e99a11d48cda146f9cc8213eb423e2ea0d8427f41c3be414424dd",
        "version": 1
    },
    "id": "0498f19a-59db-4d54-ac95-33901b4f1870",
    "version": 2
}
```

## 版本2的修改

版本2是早期的C ++实现，存在许多漏洞。 所有要点保持不变。


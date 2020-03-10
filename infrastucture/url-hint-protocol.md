# URL Hint  Protocol

* [URL 组合](https://github.com/ethereum/wiki/wiki/URL-Hint-Protocol#url-composition) 
  * [例子](https://github.com/ethereum/wiki/wiki/URL-Hint-Protocol#example-1)
* [内容](https://github.com/ethereum/wiki/wiki/URL-Hint-Protocol#content)

在地址0x42处存在一个根`Registry`合约，也存在一个 `Registry`实作的合约介面`register`。

`register`中的条目通过长度为32个字节的字符串进行分类，当查找就会发现其中有几个值类型字段，包括`content`和 `register`。

`content`是输入此字符串到Mist / AZ时加载的内容哈希值（content hash）。 

`register`是子注册表合约（subregistry contract）的地址，其中一些合约实现register。可对这些地址进行递归查找。

#### 例子

`eth://gavofyork`执行在地址0x42的主注册表查询条目`gavofyork`的命令。此条目的字段`content`是一份压缩文件或清单的SHA3哈希值，用于显示内容（更多详尽信息请看内容板块部分）。

### 

### URL组合

所有URL均以`eth：//`开头，并且有许多组件，每个组件之间都用一个/分隔。字段register用于进行递归查找。

因此，`gavofyork/tools/site/contact`包括四个组件，按顺序为`gavofyork`、`tools`、 `site`、 `contact`。

位于最左斜杠`/`之前的所有`.`，表示以相反顺序被指定的组件。

因此，上述地址的非规范形式为：

* tools.gavofyork/site/contact 
* site.tools.gavofyork/contact 
* contact.site.tools.gavofyork

注意：任何位于最左`/`之后的句号都与其他字母数字字符一样处理。

#### 例子

`eth：// gavofyork / site`执行在地址0x42的主注册表查询条目`gavofyork`的命令。该条目的register字段（`register`实现合约地址）用于查找条目`site`。字段`content`用于显示内容。

### 

### 内容

通常情况下，将使用Swarm来依据内容哈希值确定内容。

对于eth1.0来说，这是不可能的，因此我们将退回到使用HTTP分发。为此，我们将添加一个或多个URL Hint 协议，这些协议将能够发出URL提示，以允许下载指定的内容哈希值。在dapp-bin中可以找到这些合同。

下载的内容哈希值应以多种方式处理，还要进行哈希，以解锁真正的内容。可能的方式包括base 64编码，十六进制编码和原始编码，以及任何所需的内容裁剪，如HTML页面应删除body标签之前的所有内容）。

由dapp / content上载程序负责保持URL Hint条目的更新。

URL Hint合约的地址将是临时指定的，因此用户可以在浏览器中输入其他地址。


# 网络状态监测

以太坊（中心化）网络状态监测器（也称为“eth-netstats”）是基于网络的应用程序，用于通过一组节点监测测试网与主网的运行状况。

### Listing

要列节点，必须安装客户端信息中继器，即节点模块。此处给出的指令适用于Ubuntu操作系统（Mac OS X也适用相同的指令，但可能不必使用sudo指令）。不同平台的指令有所不同（请确保已安装nodejs-legacy，否则某些模块可能会失效）。

复制git repo，然后安装pm2：

`git clone` [`https://github.com/ethereum/eth-net-intelligence-api`](https://github.com/ethereum/eth-net-intelligence-api) ``

`cd eth-net-intelligence-api` 

`npm install` 

`sudo npm install -g pm2`

然后在里面编辑`app.json` 文件，为节点进行配置：

* 更改`LISTENING_PORT`右方的值（默认值：30303）
* 更改`INSTANCE_NAME`右方的值为任意节点命名
* 若有意愿分享合约细节，则更改 `CONTACT_DETAILS`右方的值
* 更改`RPC_PORT`右方的值为节点的RPC端口（默认情况下C++和go语言均为8545）
* 改`WS_SECRET`右方的值为secret值（要从[skype官方频道](http://tinyurl.com/ofndjbo)获取）

以太坊（eth或geth）必须在启用rpc的情况下运行。

`geth --rpc`

geth客户端的rpc默认端口（如未指定）为8545。

最后使用以下命令运行该过程：

`pm2 start app.json`

有几个命令可用：

* `pm2 list`命令可显示过程状态 
* `pm2 logs` 命令可显示日志  
* `gracefulReload node-app`命令  
* `pm2 stop node-app`命令可停止运行应用 
* `pm2 kill`命令可关闭daemon程序

#### 更新

必须执行以下操作才能进行更新：

* `git pull`命令可更新最新版本 
* `sudo npm update`命令可更新依赖（dependencies） 
* `pm2 gracefulReload node-app`命令可重装客户端

### 

### 在全新Ubuntu系统上自动安装

获取并运行壳（shell）后，将安装一切所需程序：最新以太坊版本所需的开发分支CLI（可选择eth或geth客户端版本）、node.js、npm和pm2。

#### 配置

配置修改`app.json`的应用程序。请注意，必须修改备份文件app.json，以允许设置环境变量在更新时不被重写）。

#### 运行

使用pm2运行以下代码：

`pm2 start app.json` 

`node app.js`

以太坊（eth或geth客户端）必须在启用rpc的情况下运行。

geth --rpc






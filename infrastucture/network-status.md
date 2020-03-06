# 网络状态监测

以太坊（中心化）网络状态监测器（也称为“eth-netstats”）是基于网络的应用程序，用于通过一组节点监测测试网与主网的运行状况。

### Listing

要列节点，必须安装客户端信息中继器，即节点模块。此处给出的指令适用于Ubuntu操作系统（Mac OS X也适用相同的指令，但可能不必使用sudo指令）。不同平台的指令有所不同（请确保已安装nodejs-legacy，否则某些模块可能会失效）。

复制git repo，然后安装pm2：

`git clone` [`https://github.com/ethereum/eth-net-intelligence-api`](https://github.com/ethereum/eth-net-intelligence-api) ``

`cd eth-net-intelligence-api` 

`npm install` 

`sudo npm install -g pm2`


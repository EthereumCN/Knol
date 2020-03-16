# 面向节点

## 5. Swarm for Node-Operators

This section is about how to run your swarm node, or deploy a seperate private or public swarm network.

### 5.1. Installation and Updates

Swarm runs on all major platforms \(Linux, macOS, Windows, Raspberry Pi, Android, iOS\).

Swarm was written in golang and requires the go-ethereum client **geth** to run.

Note

The swarm package has not been extensively tested on platforms other than Linux and macOS.

### 5.2. Installing Swarm on Ubuntu via PPA

The simplest way to install Swarm on **Ubuntu distributions** is via the built in launchpad PPAs \(Personal Package Archives\). We provide a single PPA repository that contains our stable releases for Ubuntu versions trusty, xenial, bionic and cosmic.

To enable our launchpad repository please run:

```text
$ sudo apt-get install software-properties-common
$ sudo add-apt-repository -y ppa:ethereum/ethereum
```

After that you can install the stable version of Swarm:

```text
$ sudo apt-get update
$ sudo apt-get install ethereum-swarm
```

### 5.3. Setting up Swarm in Docker

You can run Swarm in a Docker container. The official Swarm Docker image including documentation on how to run it can be found on [Github](https://github.com/ethersphere/swarm#Docker) or pulled from [Docker](https://hub.docker.com/r/ethersphere/swarm/).

You can run it with optional arguments, e.g.:

```text
$ docker run -it ethersphere/swarm --debug --verbosity 4
```

In order to up/download, you need to expose the HTTP api port \(here: to localhost:8501\) and set the HTTP address:

```text
$ docker run -p 8501:8500/tcp -it ethersphere/swarm  --httpaddr=0.0.0.0 --debug --verbosity 4
```

In this example, you can use `swarm --bzzapi http://localhost:8501 up testfile.md` to upload `testfile.md` to swarm using the Docker node, and you can get it back e.g. with `curl http://localhost:8501/bzz:/<hash>`.

Note that if you want to use a pprof HTTP server, you need to expose the ports and set the address \(with `--pprofaddr=0.0.0.0`\) too.

In order to attach a Geth Javascript console, you need to mount a data directory from a volume:

```text
$ docker run -p 8501:8500/tcp -v /tmp/hostdata:/data -it --name swarm1 ethersphere/swarm --httpaddr=0.0.0.0 --datadir /data --debug --verbosity 4
```

Then, you can attach the console with:

```text
$ docker exec -it swarm1 geth attach /data/bzzd.ipc
```

You can also open a terminal session inside the container:

```text
$ docker exec -it swarm1 /bin/sh
```

### 5.4. Installing Swarm from source

The Swarm source code for can be found on [https://github.com/ethersphere/swarm](https://github.com/ethersphere/swarm)

#### 5.4.1. Prerequisites: Go and Git

Building the Swarm binary requires the following packages:

* go: [https://golang.org](https://golang.org/)
* git: [http://git.org](http://git.org/)

Grab the relevant prerequisites and build from source.Ubuntu / DebianArchlinuxGeneric LinuxmacOSWindows

```text
$ sudo apt install git

$ sudo add-apt-repository ppa:gophers/archive
$ sudo apt-get update
$ sudo apt-get install golang-1.11-go

// Note that golang-1.11-go puts binaries in /usr/lib/go-1.11/bin. If you want them on your PATH, you need to make that change yourself.

$ export PATH=/usr/lib/go-1.11/bin:$PATH
```

#### 5.4.2. Configuring the Go environment

You should then prepare your Go environment.LinuxmacOS

```text
$ mkdir $HOME/go
$ echo 'export GOPATH=$HOME/go' >> ~/.bashrc
$ echo 'export PATH=$GOPATH/bin:$PATH' >> ~/.bashrc
$ source ~/.bashrc
```

#### 5.4.3. Download and install Geth

Once all prerequisites are met, download and install Geth from [https://github.com/ethereum/go-ethereum](https://github.com/ethereum/go-ethereum)

#### 5.4.4. Compiling and installing Swarm

Once all prerequisites are met, and you have `geth` on your system, clone the Swarm git repo and build from source:

```text
$ git clone https://github.com/ethersphere/swarm
$ cd swarm
$ make swarm
```

Alternatively you could also use the Go tooling and download and compile Swarm from master via:

```text
$ go get -d github.com/ethersphere/swarm
$ go install github.com/ethersphere/swarm/cmd/swarm
```

You can now run `swarm` to start your Swarm node. Let’s check if the installation of `swarm` was successful:

```text
swarm version
```

If your `PATH` is not set and the `swarm` command cannot be found, try:

> ```text
> $ $GOPATH/bin/swarm version
> ```

This should return some relevant information. For example:

```text
Swarm
Version: 0.3
Network Id: 0
Go Version: go1.10.1
OS: linux
GOPATH=/home/user/go
GOROOT=/usr/local/go
```

#### 5.4.5. Updating your client

To update your client simply download the newest source code and recompile.

### 5.5. Running the Swarm Go-Client

To start a basic Swarm node you must have both `geth` and `swarm` installed on your machine. You can find the relevant instructions in the [Installation and Updates](https://swarm-guide.readthedocs.io/en/latest/installation.html) section. `geth` is the go-ethereum client, you can read up on it in the [Ethereum Homestead documentation](http://ethdocs.org/en/latest/ethereum-clients/go-ethereum/index.html).

To start Swarm you need an Ethereum account. You can create a new account in `geth` by running the following command:

```text
$ geth account new
```

You will be prompted for a password:

```text
Your new account is locked with a password. Please give a password. Do not forget this password.
Passphrase:
Repeat passphrase:
```

Once you have specified the password, the output will be the Ethereum address representing that account. For example:

```text
Address: {2f1cd699b0bf461dcfbf0098ad8f5587b038f0f1}
```

Using this account, connect to Swarm with

```text
$ swarm --bzzaccount <your-account-here>
# in our example
$ swarm --bzzaccount 2f1cd699b0bf461dcfbf0098ad8f5587b038f0f1
```

\(You should replace `2f1cd699b0bf461dcfbf0098ad8f5587b038f0f1` with your account address key\).

Important

**Remember your password.** There is no _forgot my password_ option for `swarm` and `geth`.

#### 5.5.1. Verifying that your local Swarm node is running

When running, `swarm` is accessible through an HTTP API on port 8500. Confirm that it is up and running by pointing your browser to [http://localhost:8500](http://localhost:8500/) \(You should see a Swarm search box.\)

### 5.6. Interacting with Swarm

The easiest way to access Swarm through the command line, or through the [Geth JavaScript Console](http://ethdocs.org/en/latest/account-management.html) by attaching the console to a running swarm node. `$BZZKEY$` refers to your account address key.LinuxmacOSWindows

```text
$ swarm --bzzaccount $BZZKEY
```

And, in a new terminal window:

```text
$ geth attach $HOME/.ethereum/bzzd.ipc
```

Swarm is fully compatible with Geth Console commands. For example, you can list your peers using `admin.peers`, add a peer using `admin.addPeer`, and so on.

You can use Swarm with CLI flags and environment variables. See a full list in the [Configuration](https://swarm-guide.readthedocs.io/en/latest/configuration.html) .

### 5.7. How do I enable ENS name resolution?

The [Ethereum Name Service](http://ens.readthedocs.io/en/latest/introduction.html) \(ENS\) is the Ethereum equivalent of DNS in the classic web. It is based on a suite of smart contracts running on the _Ethereum mainnet_.

In order to use **ENS** to resolve names to swarm content hashes, `swarm` has to connect to a `geth` instance that is connected to the _Ethereum mainnet_. This is done using the `--ens-api` flag.

First you must start your geth node and establish connection with Ethereum main network with the following command:

```text
$ geth
```

for a full geth node, or

```text
$ geth --syncmode=light
```

for light client mode.

Note

**Syncing might take a while.** When you use the light mode, you don’t have to sync the node before it can be used to answer ENS queries. However, please note that light mode is still an experimental feature.

After the connection is established, open another terminal window and connect to Swarm:LinuxmacOSWindows

```text
$ swarm --ens-api $HOME/.ethereum/geth.ipc \
--bzzaccount $BZZKEY
```

Verify that this was successful by pointing your browser to [http://localhost:8500/bzz:/theswarm.eth/](http://localhost:8500/bzz:/theswarm.eth/)

#### 5.7.1. Using Swarm together with the testnet ENS

It is also possible to use the Ropsten ENS test registrar for name resolution instead of the Ethereum main .eth ENS on mainnet.

Run a geth node connected to the Ropsten testnet

```text
$ geth --testnet
```

Then launch the `swarm`; connecting it to the geth node \(`--ens-api`\).LinuxmacOSWindows

```text
$ swarm --ens-api $HOME/.ethereum/geth/testnet/geth.ipc \
--bzzaccount $BZZKEY
```

Swarm will automatically use the ENS deployed on Ropsten.

For other ethereum blockchains and other deployments of the ENS contracts, you can specify the contract addresses manually. For example the following command:

```text
$ swarm --ens-api eth:<contract 1>@/home/user/.ethereum/geth.ipc \
         --ens-api test:<contract 2>@ws:<address 1> \
         --ens-api <contract 3>@ws:<address 2>
```

Will use the `geth.ipc` to resolve `.eth` names using the contract at address `<contract 1>` and it will use `ws:<address 1>` to resolve `.test` names using the contract at address `<contract 2>`. For all other names it will use the ENS contract at address `<contract 3>` on `ws:<address 2>`.

#### 5.7.2. Using an external ENS source

Important

Take care when using external sources of information. By doing so you are trusting someone else to be truthful. Using an external ENS source may make you vulnerable to man-in-the-middle attacks. It is only recommended for test and development environments.

Maintaining a fully synced Ethereum node comes with certain hardware and bandwidth constraints, and can be tricky to achieve. Also, light client mode, where syncing is not necessary, is still experimental.

An alternative solution for development purposes is to connect to an external node that you trust, and that offers the necessary functionality through HTTP.

If the external node is running on IP 12.34.56.78 port 8545, the command would be:

```text
$ swarm --ens-api http://12.34.45.78:8545
```

You can also use `https`. But keep in mind that Swarm _does not validate the certificate_.

### 5.8. Alternative modes

Below are examples on ways to run `swarm` beyond just the default network. You can instruct Swarm using the geth command line interface or use the geth javascript console.

#### 5.8.1. Swarm in singleton mode \(no peers\)

If you **don’t** want your swarm node to connect to any existing networks, you can provide it with a custom network identifier using `--bzznetworkid` with a random large number.LinuxmacOSWindows

```text
$ swarm --bzzaccount $BZZKEY \
--datadir $HOME/.ethereum \
--ens-api $HOME/.ethereum/geth.ipc \
--bzznetworkid <random number between 15 and 256>
```

#### 5.8.2. Adding enodes manually

By default, Swarm will automatically seek out peers in the network.

Additionally you can manually start off the connection process by adding one or more peers using the `admin.addPeer` console command.LinuxmacOSWindows

```text
$ geth --exec='admin.addPeer("ENODE")' attach $HOME/.ethereum/bzzd.ipc
```

\(You can also do this in the Geth Console, as seen in Section [3.2](https://swarm-guide.readthedocs.io/en/latest/node_operator.html#id2).\)

Note

When you stop a node, all peer connections will be saved. When you start again, the node will try to reconnect to those peers automatically.

Where ENODE is the enode record of a swarm node. Such a record looks like the following:

```text
enode://01f7728a1ba53fc263bcfbc2acacc07f08358657070e17536b2845d98d1741ec2af00718c79827dfdbecf5cfcd77965824421508cc9095f378eb2b2156eb79fa@1.2.3.4:30399
```

The enode of your swarm node can be accessed using `geth` connected to `bzzd.ipc`LinuxmacOSWindows

```text
$ geth --exec "admin.nodeInfo.enode" attach $HOME/.ethereum/bzzd.ipc
```

Note

Note how `geth` is used for two different purposes here: You use it to run an Ethereum Mainnet node for ENS lookups. But you also use it to “attach” to the Swarm node to send commands to it.

#### 5.8.3. Connecting to the public Swarm cluster

By default Swarm connects to the public Swarm testnet operated by the Ethereum Foundation and other contributors.

The nodes the team maintains function as a free-to-use public access gateway to Swarm, so that users can experiment with Swarm without the need to run a local node. To download data through the gateway use the `https://swarm-gateways.net/bzz:/<address>/` URL.

#### 5.8.4. Metrics reporting

Swarm uses the go-metrics library for metrics collection. You can set your node to collect metrics and push them to an influxdb database \(called metrics by default\) with the default settings. Tracing is also supported. An example of a default configuration is given below:

```text
$ swarm --bzzaccount <bzzkey> \
--debug \
--metrics \
--metrics.influxdb.export \
--metrics.influxdb.endpoint "http://localhost:8086" \
--metrics.influxdb.username "user" \
--metrics.influxdb.password "pass" \
--metrics.influxdb.database "metrics" \
--metrics.influxdb.host.tag "localhost" \
--verbosity 4 \
--tracing \
--tracing.endpoint=jaeger:6831 \
--tracing.svc myswarm
```

### 5.9. Go-Client Command line options Configuration

The `swarm` executable supports the following configuration options:

* Configuration file
* Environment variables
* Command line

Options provided via command line override options from the environment variables, which will override options in the config file. If an option is not explicitly provided, a default will be chosen.

In order to keep the set of flags and variables manageable, only a subset of all available configuration options are available via command line and environment variables. Some are only available through a TOML configuration file.

Note

Swarm reuses code from ethereum, specifically some p2p networking protocol and other common parts. To this end, it accepts a number of environment variables which are actually from the `geth` environment. Refer to the geth documentation for reference on these flags.

This is the list of flags inherited from `geth`:

```text
--identity
--bootnodes
--datadir
--keystore
--port
--nodiscover
--v5disc
--netrestrict
--nodekey
--nodekeyhex
--maxpeers
--nat
--ipcdisable
--ipcpath
--password
```

### 5.10. Config File

Note

`swarm` can be executed with the `dumpconfig` command, which prints a default configuration to STDOUT, and thus can be redirected to a file as a template for the config file.

A TOML configuration file is organized in sections. The below list of available configuration options is organized according to these sections. The sections correspond to Go modules, so need to be respected in order for file configuration to work properly. See [https://github.com/naoina/toml](https://github.com/naoina/toml) for the TOML parser and encoder library for Golang, and [https://github.com/toml-lang/toml](https://github.com/toml-lang/toml) for further information on TOML.

To run Swarm with a config file, use:

```text
$ swarm --config /path/to/config/file.toml
```

### 5.11. General configuration parameters

| Config file | Command-line flag | Environment variable | Default value | Description |
| :--- | :--- | :--- | :--- | :--- |
| n/a | –config | n/a | n/a | Path to config file in TOML format |
| n/a | –bzzapi | n/a | [http://127.0.0.1:8500](http://127.0.0.1:8500/) | Swarm HTTP endpoint |
| BootNodes | –bootnodes | SWARM\_BOOTNODES |  | Boot nodes |
| BzzAccount | –bzzaccount | SWARM\_ACCOUNT |  | Swarm account key |
| BzzKey | n/a | n/a | n/a | Swarm node base address \(hash\(PublicKey\)hash\(PublicKey\)\)hash\(PublicKey\)hash\(PublicKey\)\). This is used to decide storage based on radius and routing by kademlia. |
| Contract | –chequebook | SWARM\_CHEQUEBOOK\_ADDR | 0x0 | Swap chequebook contract address |
| Cors | –corsdomain | SWARM\_CORS |  | Domain on which to send Access-Control-Allow-Origin header \(multiple domains can be supplied separated by a ‘,’\) |
| n/a | –debug | n/a | n/a | Prepends log messages with call-site location \(file and line number\) |
| n/a | –defaultpath | n/a | n/a | path to file served for empty url path \(none\) |
| n/a | –delivery-skip-check | SWARM\_DELIVERY\_SKIP\_CHECK | false | Skip chunk delivery check \(default false\) |
| EnsApi | –ens-api | SWARM\_ENS\_API | &lt;$GETH\_DATADIR&gt;/geth.ipc | Ethereum Name Service API address |
| EnsRoot | –ens-addr | SWARM\_ENS\_ADDR | ens.TestNetAddress | Ethereum Name Service contract address |
| ListenAddr | –httpaddr | SWARM\_LISTEN\_ADDR | 127.0.0.1 | Swarm listen address |
| n/a | –manifest value | n/a | true | Automatic manifest upload \(default true\) |
| n/a | –mime value | n/a | n/a | Force mime type on upload |
| NetworkId | –bzznetworkid | SWARM\_NETWORK\_ID | 3 | Network ID |
| Path | –datadir | GETH\_DATADIR | &lt;$GETH\_DATADIR&gt;/swarm | Path to the geth configuration directory |
| Port | –bzzport | SWARM\_PORT | 8500 | Port to run the http proxy server |
| PublicKey | n/a | n/a | n/a | Public key of swarm base account |
| n/a | –recursive | n/a | false | Upload directories recursively \(default false\) |
| n/a | –stdin |  | n/a | Reads data to be uploaded from stdin |
| n/a | –store.path value | SWARM\_STORE\_PATH | &lt;$GETH\_ENV\_DIR&gt;/swarm/bzz-&lt;$BZZ\_KEY&gt;/chunks | Path to leveldb chunk DB |
| n/a | –store.size value | SWARM\_STORE\_CAPACITY | 5000000 | Number of chunks \(5M is roughly 20-25GB\) \(default 5000000\)\] |
| n/a | –store.cache.size value | SWARM\_STORE\_CACHE\_CAPACITY | 5000 | Number of recent chunks cached in memory \(default 5000\) |
| n/a | –sync-update-delay value | SWARM\_ENV\_SYNC\_UPDATE\_DELAY |  | Duration for sync subscriptions update after no new peers are added \(default 15s\) |
| BackendURL | –backend-url | SWARM\_BACKEND\_URL |  | URL of the Ethereum API provider to use to settle SWAP payments |
| SwapEnabled | –swap | SWARM\_SWAP\_ENABLE | false | Enable SWAP |
| SyncDisabled | –nosync | SWARM\_ENV\_SYNC\_DISABLE | false | Disable Swarm node synchronization |
| n/a | –verbosity value | n/a | 3 | Logging verbosity: 0=silent, 1=error, 2=warn, 3=info, 4=debug, 5=detail |
| n/a | –ws | n/a | false | Enable the WS-RPC server |
| n/a | –wsaddr value | n/a | localhost | WS-RPC server listening interface |
| n/a | –wsport value | n/a | 8546 | WS-RPC server listening port |
| n/a | –wsapi value | n/a | n/a | API’s offered over the WS-RPC interface |
| n/a | –wsorigins value | n/a | n/a | Origins from which to accept websockets requests |
| n/a | n/a | SWARM\_AUTO\_DEFAULTPATH | false | Toggle automatic manifest default path on recursive uploads \(looks for index.html\) |


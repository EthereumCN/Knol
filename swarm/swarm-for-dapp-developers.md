# Swarm for DApp-Developers

## 4. Swarm for DApp-Developers

This section is written for developers who want to use Swarm in their own applications.

Swarm offers a **local HTTP proxy** API that dapps or command line tools can use to interact with Swarm. Some modules like [messaging](https://swarm-guide.readthedocs.io/en/latest/dapp_developer/PSS) are only available through RPC-JSON API. The foundation servers on the testnet are offering public gateways, which serve to easily demonstrate functionality and allow free access so that people can try Swarm without even running their own node.

Swarm is a collection of nodes of the devp2p network each of which run the BZZ URL schemes on the same network id.

### 4.1. Public gateways

Swarm offers an HTTP based API that DApps can use to interact with Swarm. The Ethereum Foundation is hosting a public gateway, which allows free access so that people can try Swarm without running their own node. The Swarm public gateway can be found at [https://swarm-gateways.net](https://swarm-gateways.net/) and is always running the latest Swarm release.

Important

Swarm public gateways are temporary and users should not rely on their existence for production services.

### 4.2. Uploading and downloading

#### 4.2.1. Introduction

Note

This guide assumes you’ve installed the Swarm client and have a running node that listens by default on port 8500. See [Getting Started](https://swarm-guide.readthedocs.io/en/latest/dapp_developer/gettingstarted.html) for details.

Arguably, uploading and downloading content is the raison d’être of Swarm. Uploading content consists of “uploading” content to your local Swarm node, followed by your local Swarm node “syncing” the resulting chunks of data with its peers in the network. Meanwhile, downloading content consists of your local Swarm node querying its peers in the network for the relevant chunks of data and then reassembling the content locally.

Uploading and downloading data can be done through the `swarm` command line interface \(CLI\) on the terminal or via the HTTP interface on [http://localhost:8500](http://localhost:8500/).

#### 4.2.2. Using HTTP

Swarm offers an HTTP API. Thus, a simple way to upload and download files to/from Swarm is through this API. We can use the `curl` [tool](https://curl.haxx.se/docs/httpscripting.html) to exemplify how to interact with this API.

Note

Files can be uploaded in a single HTTP request, where the body is either a single file to store, a tar stream \(application/x-tar\) or a multipart form \(multipart/form-data\).

To upload a single file to your node, run this:

```text
$ curl -H "Content-Type: text/plain" --data "some-data" http://localhost:8500/bzz:/
```

Once the file is uploaded, you will receive a hex string which will look similar to this:

```text
027e57bcbae76c4b6a1c5ce589be41232498f1af86e1b1a2fc2bdffd740e9b39
```

This is the Swarm hash of the address string of your content inside Swarm. It is the same hash that would have been returned by using the :ref:`swarm up <swarmup>` command.

To download a file from Swarm, you just need the file’s Swarm hash. Once you have it, the process is simple. Run:

```text
$ curl http://localhost:8500/bzz:/027e57bcbae76c4b6a1c5ce589be41232498f1af86e1b1a2fc2bdffd740e9b39/
```

The result should be your file:

```text
some-data
```

And that’s it.

Note

If you omit the trailing slash from the url then the request will result in a HTTP redirect. The semantically correct way to access the root path of a Swarm manifest is using the trailing slash.

**4.2.2.1. Tar stream upload**

Tar is a traditional unix/linux file format for packing a directory structure into a single file. Swarm provides a convenient way of using this format to make it possible to perform recursive uploads using the HTTP API.

```text
# create two directories with a file in each$ mkdir dir1 dir2$ echo "some-data" > dir1/file.txt$ echo "some-data" > dir2/file.txt# create a tar archive containing the two directories (this will tar everything in the working directory)tar cf files.tar .# upload the tar archive to Swarm to create a manifest$ curl -H "Content-Type: application/x-tar" --data-binary @files.tar http://localhost:8500/bzz:/> 1e0e21894d731271e50ea2cecf60801fdc8d0b23ae33b9e808e5789346e3355e
```

You can then download the files using:

```text
$ curl http://localhost:8500/bzz:/1e0e21894d731271e50ea2cecf60801fdc8d0b23ae33b9e808e5789346e3355e/dir1/file.txt> some-data$ curl http://localhost:8500/bzz:/1e0e21894d731271e50ea2cecf60801fdc8d0b23ae33b9e808e5789346e3355e/dir2/file.txt> some-data
```

GET requests work the same as before with the added ability to download multiple files by setting Accept: application/x-tar:

```text
$ curl -s -H "Accept: application/x-tar" http://localhost:8500/bzz:/ccef599d1a13bed9989e424011aed2c023fce25917864cd7de38a761567410b8/ | tar t> dir1/file.txt  dir2/file.txt
```

**4.2.2.2. Multipart form upload**

```text
$ curl -F 'dir1/file.txt=some-data;type=text/plain' -F 'dir2/file.txt=some-data;type=text/plain' http://localhost:8500/bzz:/> 9557bc9bb38d60368f5f07aae289337fcc23b4a03b12bb40a0e3e0689f76c177$ curl http://localhost:8500/bzz:/9557bc9bb38d60368f5f07aae289337fcc23b4a03b12bb40a0e3e0689f76c177/dir1/file.txt> some-data$ curl http://localhost:8500/bzz:/9557bc9bb38d60368f5f07aae289337fcc23b4a03b12bb40a0e3e0689f76c177/dir2/file.txt> some-data
```

**4.2.2.3. Add files to an existing manifest using multipart form**

```text
$ curl -F 'dir3/file.txt=some-other-data;type=text/plain' http://localhost:8500/bzz:/9557bc9bb38d60368f5f07aae289337fcc23b4a03b12bb40a0e3e0689f76c177> ccef599d1a13bed9989e424011aed2c023fce25917864cd7de38a761567410b8$ curl http://localhost:8500/bzz:/ccef599d1a13bed9989e424011aed2c023fce25917864cd7de38a761567410b8/dir1/file.txt> some-data$ curl http://localhost:8500/bzz:/ccef599d1a13bed9989e424011aed2c023fce25917864cd7de38a761567410b8/dir3/file.txt> some-other-data
```

**4.2.2.4. Upload files using a simple HTML form**

```text
<form method="POST" action="/bzz:/" enctype="multipart/form-data">  <input type="file" name="dir1/file.txt">  <input type="file" name="dir2/file.txt">  <input type="submit" value="upload"></form>
```

**4.2.2.5. Listing files**

Note

The `jq` command mentioned below is a separate application that can be used to pretty-print the json data retrieved from the `curl` request

A GET request with `bzz-list` URL scheme returns a list of files contained under the path, grouped into common prefixes which represent directories:

```text
$ curl -s http://localhost:8500/bzz-list:/ccef599d1a13bed9989e424011aed2c023fce25917864cd7de38a761567410b8/ | jq .> {   "common_prefixes": [     "dir1/",     "dir2/",     "dir3/"   ] }
```

```text
$ curl -s http://localhost:8500/bzz-list:/ccef599d1a13bed9989e424011aed2c023fce25917864cd7de38a761567410b8/dir1/ | jq .> {  "entries": [    {      "path": "dir1/file.txt",      "contentType": "text/plain",      "size": 9,      "mod_time": "2017-03-12T15:19:55.112597383Z",      "hash": "94f78a45c7897957809544aa6d68aa7ad35df695713895953b885aca274bd955"    }  ]}
```

Setting `Accept: text/html` returns the list as a browsable HTML document.

#### 4.2.3. Using the CLI - Command Line Interface

**4.2.3.1. Uploading a file to your local Swarm node**

Note

Once a file is uploaded to your local Swarm node, your node will sync the chunks of data with other nodes on the network. Thus, the file will eventually be available on the network even when your original node goes offline.

The basic command for uploading to your local node is `swarm up FILE`. For example, let’s create a file called example.md and issue the following command to upload the file example.md file to your local Swarm node.

```text
$ echo "this is an example" > example.md$ swarm up example.md> d1f25a870a7bb7e5d526a7623338e4e9b8399e76df8b634020d11d969594f24a
```

The hash returned is the hash of a [swarm manifest](https://swarm-guide.readthedocs.io/en/latest/features/manifests.html#swarm-manifest). This manifest is a JSON file that contains the `example.md` file as its only entry. Both the primary content and the manifest are uploaded by default.

After uploading, you can access this example.md file from Swarm by pointing your browser to:

```text
$ http://localhost:8500/bzz:/d1f25a870a7bb7e5d526a7623338e4e9b8399e76df8b634020d11d969594f24a/
```

The manifest makes sure you could retrieve the file with the correct MIME type.

You can encrypt your file using the `--encrypt` flag. See the [Encryption](https://swarm-guide.readthedocs.io/en/latest/features/encryption.html#encryption) section for details.

**4.2.3.2. Suppressing automatic manifest creation**

You may wish to prevent a manifest from being created alongside with your content and only upload the raw content. You might want to include it in a custom index, or handle it as a data-blob known and used only by a certain application that knows its MIME type. For this you can set `--manifest=false`:

```text
$ swarm --manifest=false up FILE> 7149075b7f485411e5cc7bb2d9b7c86b3f9f80fb16a3ba84f5dc6654ac3f8ceb
```

This option suppresses automatic manifest upload. It uploads the content as-is. However, if you wish to retrieve this file, the browser can not be told unambiguously what that file represents. In the context, the hash `7149075b7f485411e5cc7bb2d9b7c86b3f9f80fb16a3ba84f5dc6654ac3f8ceb` does not refer to a manifest. Therefore, any attempt to retrieve it using the `bzz:/` scheme will result in a `404 Not Found` error. In order to access this file, you would have to use the [bzz-raw](https://swarm-guide.readthedocs.io/en/latest/features/bzz.html#bzz-raw) scheme.

**4.2.3.3. Downloading a single file**

To download single files, use the `swarm down` command. Single files can be downloaded in the following different manners. The following examples assume `<hash>` resolves into a single-file manifest:

```text
$ swarm down bzz:/<hash>            #downloads the file at <hash> to the current working directory$ swarm down bzz:/<hash> file.tmp   #downloads the file at <hash> as ``file.tmp`` in the current working dir$ swarm down bzz:/<hash> dir1/      #downloads the file at <hash> to ``dir1/``
```

You can also specify a custom proxy with –bzzapi:

```text
$ swarm --bzzapi http://localhost:8500 down bzz:/<hash>            #downloads the file at <hash> to the current working directory using the localhost node
```

Downloading a single file from a multi-entry manifest can be done with \(`<hash>` resolves into a multi-entry manifest\):

```text
$ swarm down bzz:/<hash>/index.html            #downloads index.html to the current working directory$ swarm down bzz:/<hash>/index.html file.tmp   #downloads index.html as file.tmp in the current working directory$ swarm down bzz:/<hash>/index.html dir1/      #downloads index.html to dir1/
```

..If you try to download from a multi-entry manifest without specifying the file, you will get a got too many matches for this path error. You will need to specify a –recursive flag \(see below\).

**4.2.3.4. Uploading to a remote Swarm node**

You can upload to a remote Swarm node using the `--bzzapi` flag. For example, you can use one of the public gateways as a proxy, in which case you can upload to Swarm without even running a node.

```text
$ swarm --bzzapi https://swarm-gateways.net up example.md
```

Note

This gateway currently only accepts uploads of limited size. In future, the ability to upload to this gateways is likely to disappear entirely.

**4.2.3.5. Uploading a directory**

Uploading directories is achieved with the `--recursive` flag.

```text
$ swarm --recursive up /path/to/directory> ab90f84c912915c2a300a94ec5bef6fc0747d1fbaf86d769b3eed1c836733a30
```

The returned hash refers to a root manifest referencing all the files in the directory.

**4.2.3.6. Directory with default entry**

It is possible to declare a default entry in a manifest. In the example above, if `index.html` is declared as the default, then a request for a resource with an empty path will show the contents of the file `/index.html`

```text
$ swarm --defaultpath /path/to/directory/index.html --recursive up /path/to/directory> ef6fc0747d1fbaf86d769b3eed1c836733a30ab90f84c912915c2a300a94ec5b
```

You can now access index.html at

```text
$ http://localhost:8500/bzz:/ef6fc0747d1fbaf86d769b3eed1c836733a30ab90f84c912915c2a300a94ec5b/
```

and also at

```text
$ http://localhost:8500/bzz:/ef6fc0747d1fbaf86d769b3eed1c836733a30ab90f84c912915c2a300a94ec5b/index.html
```

This is especially useful when the hash \(in this case `ef6fc0747d1fbaf86d769b3eed1c836733a30ab90f84c912915c2a300a94ec5b`\) is given a registered name like `mysite.eth` in the [Ethereum Name Service](https://swarm-guide.readthedocs.io/en/latest/dapp_developer/ens.html). In this case the lookup would be even simpler:

```text
http://localhost:8500/bzz:/mysite.eth/
```

Note

You can toggle automatic default entry detection with the `SWARM_AUTO_DEFAULTPATH` environment variable. You can do so by a simple `$ export SWARM_AUTO_DEFAULTPATH=true`. This will tell Swarm to automatically look for `<uploaded directory>/index.html` file and set it as the default manifest entry \(in the case it exists\).

**4.2.3.7. Downloading a directory**

To download a directory, use the `swarm down --recursive` command. Directories can be downloaded in the following different manners. The following examples assume &lt;hash&gt; resolves into a multi-entry manifest:

```text
$ swarm down --recursive bzz:/<hash>            #downloads the directory at <hash> to the current working directory$ swarm down --recursive bzz:/<hash> dir1/      #downloads the file at <hash> to dir1/
```

Similarly as with a single file, you can also specify a custom proxy with `--bzzapi`:

```text
$ swarm --bzzapi http://localhost:8500 down --recursive bzz:/<hash> #note the flag ordering
```

Important

Watch out for the order of arguments in directory upload/download: it’s `swarm --recursive up` and `swarm down --recursive`.

**4.2.3.8. Adding entries to a manifest**

The command for modifying manifests is `swarm manifest`.

To add an entry to a manifest, use the command:

```text
$ swarm manifest add <manifest-hash> <path> <hash> [content-type]
```

To remove an entry from a manifest, use the command:

```text
$ swarm manifest remove <manifest-hash> <path>
```

To modify the hash of an entry in a manifest, use the command:

```text
$ swarm manifest update <manifest-hash> <path> <new-hash>
```

**4.2.3.9. Reference table**

| **upload** | `swarm up <file>` |
| :--- | :--- |
| ~ dir | `swarm --recursive up <dir>` |
| ~ dir w/ default entry \(here: index.html\) | `swarm --defaultpath <dir>/index.html --recursive up <dir>` |
| ~ w/o manifest | `swarm --manifest=false up` |
| ~ to remote node | `swarm --bzzapi https://swarm-gateways.net up` |
| ~ with encryption | `swarm up --encrypt` |
| **download** | `swarm down bzz:/<hash>` |
| ~ dir | `swarm down --recursive bzz:/<hash>` |
| ~ as file | `swarm down bzz:/<hash> file.tmp` |
| ~ into dir | `swarm down bzz:/<hash> dir/` |
| ~ w/ custom proxy | `swarm down --bzzapi http://<proxy address> down bzz:/<hash>` |
| **manifest** |  |
| add ~ | `swarm manifest add <manifest-hash> <path> <hash> [content-type]` |
| remove ~ | `swarm manifest remove <manifest-hash> <path>` |
| update ~ | `swarm manifest update <manifest-hash> <path> <new-hash>` |

**4.2.3.10. Up- and downloading in the CLI: example usage**

Up/downloadingUp/down as isManipulate manifests

Let’s create a dummy file and upload it to Swarm:

```text
$ echo "this is a test" > myfile.md$ swarm up myfile.md> <reference hash>
```

We can download it using the `bzz:/` scheme and give it a name.

```text
$ swarm down bzz:/<reference hash> iwantmyfileback.md$ cat iwantmyfileback.md> this is a test
```

We can also `curl` it using the HTTP API.

```text
$ curl http://localhost:8500/bzz:/<reference hash>/> this is a test
```

We can use the `bzz-raw` scheme to see the manifest of the upload.

```text
$ curl http://localhost:8500/bzz-raw:/<reference hash>/
```

This returns the manifest:

```text
{  "entries": [    {      "hash": "<file hash>",      "path": "myfile.md",      "contentType": "text/markdown; charset=utf-8",      "mode": 420,      "size": 15,      "mod_time": "<timestamp>"    }  ]}
```

### 4.3. Example Dapps

* [https://swarm-gateways.net/bzz://swarmapps.eth](https://swarm-gateways.net/bzz://swarmapps.eth)
* source code: [https://github.com/ethersphere/swarm-dapps](https://github.com/ethersphere/swarm-dapps)

### 4.4. Working with content

In this chapter, we demonstrate features of Swarm related to storage and retrieval. First we discuss how to solve mutability of resources in a content addressed system using the Ethereum Name Service on the blockchain, then using Feeds in Swarm. Then we briefly discuss how to protect your data by restricting access using encryption. We also discuss in detail how files can be organised into collections using manifests and how this allows virtual hosting of websites. Another form of interaction with Swarm, namely mounting a Swarm manifest as a local directory using FUSE. We conclude by summarizing the various URL schemes that provide simple HTTP endpoints for clients to interact with Swarm.

#### 4.4.1. Ethereum Name System \(ENS\)

Nick Johnson on the Ethereum Name System

Note

In order to resolve ENS names, your Swarm node has to be connected to an Ethereum blockchain \(mainnet, or testnet\). See [Getting Started](https://swarm-guide.readthedocs.io/en/latest/dapp_developer/gettingstarted.html#connect-ens) for instructions. This section explains how you can register your content to your ENS name.

[ENS](http://ens.readthedocs.io/en/latest/introduction.html) is the system that Swarm uses to permit content to be referred to by a human-readable name, such as “theswarm.eth”. It operates analogously to the DNS system, translating human-readable names into machine identifiers - in this case, the Swarm hash of the content you’re referring to. By registering a name and setting it to resolve to the content hash of the root manifest of your site, users can access your site via a URL such as `bzz://theswarm.eth/`.

Note

Currently The bzz scheme is not supported in major browsers such as Chrome, Firefox or Safari. If you want to access the bzz scheme through these browsers, currently you have to either use an HTTP gateway, such as [https://swarm-gateways.net/bzz:/theswarm.eth/](https://swarm-gateways.net/bzz:/theswarm.eth/) or use a browser which supports the bzz scheme, such as Mist &lt;[https://github.com/ethereum/mist](https://github.com/ethereum/mist)&gt;.

Suppose we upload a directory to Swarm containing \(among other things\) the file `example.pdf`.

```text
$ swarm --recursive up /path/to/dir>2477cc8584cc61091b5cc084cdcdb45bf3c6210c263b0143f030cf7d750e894d
```

If we register the root hash as the `content` for `theswarm.eth`, then we can access the pdf at

```text
bzz://theswarm.eth/example.pdf
```

if we are using a Swarm-enabled browser, or at

```text
http://localhost:8500/bzz:/theswarm.eth/example.pdf
```

via a local gateway. We will get served the same content as with:

```text
http://localhost:8500/bzz:/2477cc8584cc61091b5cc084cdcdb45bf3c6210c263b0143f030cf7d750e894d/example.pdf
```

Please refer to the [official ENS documentation](http://ens.readthedocs.io/en/latest/introduction.html) for the full details on how to register content hashes to ENS.

In short, the steps you must take are:

1. Register an ENS name.
2. Associate a resolver with that name.
3. Register the Swarm hash with the resolver as the `content`.

We recommend using [https://manager.ens.domains/](https://manager.ens.domains/). This will make it easy for you to:

* Associate the default resolver with your name
* Register a Swarm hash.

Note

When you register a Swarm hash with [https://manager.ens.domains/](https://manager.ens.domains/) you MUST prefix the hash with 0x. For example 0x2477cc8584cc61091b5cc084cdcdb45bf3c6210c263b0143f030cf7d750e894d

#### 4.4.2. Feeds

Note

Feeds, previously known as _Mutable Resource Updates_, is an experimental feature, available since Swarm POC3. It is under active development, so expect things to change.

Since Swarm hashes are content addressed, changes to data will constantly result in changing hashes. Swarm Feeds provide a way to easily overcome this problem and provide a single, persistent, identifier to follow sequential data.

The usual way of keeping the same pointer to changing data is using the Ethereum Name Service \(ENS\). However, since ENS is an on-chain feature, it might not be suitable for each use case since:

1. Every update to an ENS resolver will cost gas to execute
2. It is not be possible to change the data faster than the rate that new blocks are mined
3. ENS resolution requires your node to be synced to the blockchain

Swarm Feeds provide a way to have a persistent identifier for changing data without having to use ENS. It is named Feeds for its similarity with a news feed.

If you are using _Feeds_ in conjunction with an ENS resolver contract, only one initial transaction to register the “Feed manifest address” will be necessary. This key will resolve to the latest version of the Feed \(updating the Feed will not change the key\).

You can think of a Feed as a user’s Twitter account, where he/she posts updates about a particular Topic. In fact, the Feed object is simply defined as:

```text
type Feed struct {  Topic Topic  User  common.Address}
```

That is, a specific user posting updates about a specific Topic.

Users can post to any topic. If you know the user’s address and agree on a particular Topic, you can then effectively “follow” that user’s Feed.

Important

How you build the Topic is entirely up to your application. You could calculate a hash of something and use that, the recommendation is that it should be easy to derive out of information that is accesible to other users.

For convenience, `feed.NewTopic()` provides a way to “merge” a byte array with a string in order to build a Feed Topic out of both. This is used at the API level to create the illusion of subtopics. This way of building topics allows using a random byte array \(for example the hash of a photo\) and merge it with a human-readable string such as “comments” in order to create a Topic that could represent the comments about that particular photo. This way, when you see a picture in a website you could immediately build a Topic out of it and see if some user posted comments about that photo.

Feeds are not created, only updated. If a particular Feed \(user, topic combination\) has never posted to, trying to fetch updates will yield nothing.

**4.4.2.1. Feed Manifests**

A Feed Manifest is simply a JSON object that contains the `Topic` and `User` of a particular Feed \(i.e., a serialized `Feed` object\). Uploading this JSON object to Swarm in the regular way will return the immutable hash of this object. We can then store this immutable hash in an ENS Resolver so that we can have a ENS domain that “follows” the Feed described in the manifest.

**4.4.2.2. Feeds API**

There are 3 different ways of interacting with _Feeds_ : HTTP API, CLI and Golang API.

**4.4.2.3. HTTP API**

**4.4.2.4. Posting to a Feed**

Since Feed updates need to be signed, and an update has some correlation with a previous update, it is necessary to retrieve first the Feed’s current status. Thus, the first step to post an update will be to retrieve this current status in a ready-to-sign template:

1. Get Feed template

`GET /bzz-feed:/?topic=<TOPIC>&user=<USER>&meta=1`

`GET /bzz-feed:/<MANIFEST OR ENS NAME>/?meta=1`Where:

* `user`: Ethereum address of the user who publishes the Feed
* `topic`: Feed topic, encoded as a hex string. Topic is an arbitrary 32-byte string \(64 hex chars\)

Note

* If `topic` is omitted, it is assumed to be zero, 0x000…
* if `name=<name>` \(optional\) is provided, a subtopic is composed with that name
* A common use is to omit topic and just use `name`, allowing for human-readable topics

You will receive a JSON like the below:

```text
{  "feed": {    "topic": "0x6a61766900000000000000000000000000000000000000000000000000000000",    "user": "0xdfa2db618eacbfe84e94a71dda2492240993c45b"  },  "epoch": {    "level": 16,    "time": 1534237239  }  "protocolVersion" : 0,}
```

1. Post the update

Extract the fields out of the JSON and build a query string as below:

`POST /bzz-feed:/?topic=<TOPIC>&user=<USER>&level=<LEVEL>&time=<TIME>&signature=<SIGNATURE>`Where:

* `topic`: Feed topic, as specified above
* `user`: your Ethereum address
* `level`: Suggested frequency level retrieved in the JSON above
* `time`: Suggested timestamp retrieved in the JSON above
* `protocolVersion`: Feeds protocol version. Currently `0`
* `signature`: Signature, hex encoded. See below on how to calclulate the signature
* Request posted data: binary stream with the update data

**4.4.2.5. Reading a Feed**

To retrieve a Feed’s last update:

`GET /bzz-feed:/?topic=<TOPIC>&user=<USER>`

`GET /bzz-feed:/<MANIFEST OR ENS NAME>`

Note

* Again, if `topic` is omitted, it is assumed to be zero, 0x000…
* If `name=<name>` is provided, a subtopic is composed with that name
* A common use is to omit `topic` and just use `name`, allowing for human-readable topics, for example: `GET /bzz-feed:/?name=profile-picture&user=<USER>`

To get a previous update:

Add an addtional `time` parameter. The last update before that `time` \(unix time\) will be looked up.

`GET /bzz-feed:/?topic=<TOPIC>&user=<USER>&time=<T>`

`GET /bzz-feed:/<MANIFEST OR ENS NAME>?time=<T>`

**4.4.2.6. Creating a Feed Manifest**

To create a `Feed manifest` using the HTTP API:

`POST /bzz-feed:/?topic=<TOPIC>&user=<USER>&manifest=1.` With an empty body.

This will create a manifest referencing the provided Feed.

Note

This API call will be deprecated in the near future.

#### 4.4.3. Go API

**4.4.3.1. Query object**

The `Query` object allows you to build a query to browse a particular `Feed`.

The default `Query`, obtained with `feed.NewQueryLatest()` will build a `Query` that retrieves the latest update of the given `Feed`.

You can also use `feed.NewQuery()` instead, if you want to build a `Query` to look up an update before a certain date.

Advanced usage of `Query` includes hinting the lookup algorithm for faster lookups. The default hint `lookup.NoClue` will have your node track Feeds you query frequently and handle hints automatically.

**4.4.3.2. Request object**

The `Request` object makes it easy to construct and sign a request to Swarm to update a particular Feed. It contains methods to sign and add data. We can manually build the `Request` object, or fetch a valid “template” to use for the update.

A `Request` can also be serialized to JSON in case you need your application to delegate signatures, such as having a browser sign a Feed update request.

**4.4.3.3. Posting to a Feed**

1. Retrieve a `Request` object or build one from scratch. To retrieve a ready-to-sign one:

```text
func (c *Client) GetFeedRequest(query *feed.Query, manifestAddressOrDomain string) (*feed.Request, error)
```

1. Use `Request.SetData()` and `Request.Sign()` to load the payload data into the request and sign it
2. Call `UpdateFeed()` with the filled `Request`:

```text
func (c *Client) UpdateFeed(request *feed.Request, createManifest bool) (io.ReadCloser, error)
```

**4.4.3.4. Reading a Feed**

To retrieve a Feed update, use client.QueryFeed\(\). `QueryFeed` returns a byte stream with the raw content of the Feed update.

```text
func (c *Client) QueryFeed(query *feed.Query, manifestAddressOrDomain string) (io.ReadCloser, error)
```

`manifestAddressOrDomain` is the address you obtained in `CreateFeedWithManifest` or an `ENS` domain whose Resolver points to that address. `query` is a Query object, as defined above.

You only need to provide either `manifestAddressOrDomain` or `Query` to `QueryFeed()`. Set to `""` or `nil` respectively.

**4.4.3.5. Creating a Feed Manifest**

Swarm client \(package swarm/api/client\) has the following method:

```text
func (c *Client) CreateFeedWithManifest(request *feed.Request) (string, error)
```

`CreateFeedWithManifest` uses the `request` parameter to set and create a `Feed manifest`.

Returns the resulting `Feed manifest address` that you can set in an ENS Resolver \(setContent\) or reference future updates using `Client.UpdateFeed()`

**4.4.3.6. Example Go code**

```text
// Build a `Feed` object to track a particular user's updatesf := new(feed.Feed)f.User = signer.Address()f.Topic, _ = feed.NewTopic("weather",nil)// Build a `Query` to retrieve a current Request for this feedquery := feeds.NewQueryLatest(&f, lookup.NoClue)// Retrieve a ready-to-sign request using our query// (queries can be reused)request, err := client.GetFeedRequest(query, "")if err != nil {    utils.Fatalf("Error retrieving feed status: %s", err.Error())}// set the new datarequest.SetData([]byte("Weather looks bright and sunny today, we should merge this PR and go out enjoy"))// sign updateif err = request.Sign(signer); err != nil {    utils.Fatalf("Error signing feed update: %s", err.Error())}// post updateerr = client.UpdateFeed(request)if err != nil {    utils.Fatalf("Error updating feed: %s", err.Error())}
```

**4.4.3.7. Command-Line**

The CLI API allows us to go through how Feeds work using practical examples. You can look up CL usage by typing `swarm feed` into your CLI.

In the CLI examples, we will create and update feeds using the bzzapi on a running local Swarm node that listens by default on port 8500.

**4.4.3.8. Creating a Feed Manifest**

The Swarm CLI allows creating Feed Manifests directly from the console.

`swarm feed create` is defined as a command to create and publish a `Feed manifest`.The feed topic can be built in the following ways:

* use `--topic` to set the topic to an arbitrary binary hex string.
* use `--name` to set the topic to a human-readable name.For example, `--name` could be set to “profile-picture”, meaning this feed allows to get this user’s current profile picture.
* use both `--topic` and `--name` to create named subtopics.For example, –topic could be set to an Ethereum contract address and `--name` could be set to “comments”, meaning this feed tracks a discussion about that contract.

The `--user` flag allows to have this manifest refer to a user other than yourself. If not specified, it will then default to your local account \(`--bzzaccount`\).

If you don’t specify a name or a topic, the topic will be set to `0 hex` and name will be set to your username.

```text
$ swarm --bzzapi http://localhost:8500 feed create --name test
```

creates a feed named “test”. This is equivalent to the HTTP API way of

```text
$ swarm --bzzapi http://localhost:8500 feed create --topic 0x74657374
```

since `test string == 0x74657374 hex`. Name and topic are interchangeable, as long as you don’t specify both.

`feed create` will return the **feed manifest**.

You can also use `curl` in the HTTP API, but, here, you have to explicitly define the user \(which, in this case, is your account\) and the manifest.

```text
$ curl -XPOST -d 'name=test&user=<your account>&manifest=1' http://localhost:8500/bzz-Feed:/
```

is equivalent to

```text
$ curl -XPOST -d 'topic=0x74657374&user=<your account>&manifest=1' http://localhost:8500/bzz-Feed:/
```

**4.4.3.9. Posting to a Feed**

To update a Feed with the CLI, use `feed update`. The **update** argument has to be in `hex`. If you want to update your _test_ feed with the update _hello_, you can refer to it by name:

```text
$ swarm --bzzapi http://localhost:8500 feed update --name test 0x68656c6c6f203
```

You can also refer to it by topic,

```text
$ swarm --bzzapi http://localhost:8500 feed update --topic 0x74657374 0x68656c6c6f203
```

or manifest.

```text
$ swarm --bzzapi http://localhost:8500 feed update --manifest <manifest hash> 0x68656c6c6f203
```

**4.4.3.10. Reading Feed status**

You can read the feed object using `feed info`. Again, you can use the feed name, the topic, or the manifest hash. Below, we use the name.

```text
$ swarm --bzzapi http://localhost:8500 feed info --name test
```

**4.4.3.11. Reading Feed Updates**

Although the Swarm CLI doesn’t have the functionality to retrieve feed updates, we can use `curl` and the HTTP api to retrieve them. Again, you can use the feed name, topic, or manifest hash. To return the update `hello` for your `test` feed, do this:

```text
$ curl 'http://localhost:8500/bzz-feed:/?user=<your address>&name=test'
```

**4.4.3.12. Computing Feed Signatures**

1. computing the digest:

The digest is computed concatenating the following:

* 1-byte protocol version \(currently 0\)
* 7-bytes padding, set to 0
* 32-bytes topic
* 20-bytes user address
* 7-bytes time, little endian
* 1-byte level
* payload data \(variable length\)

1. Take the SHA3 hash of the above digest
2. Compute the ECDSA signature of the hash
3. Convert to hex string and put in the `signature` field above

#### 4.4.4. JavaScript example

```text
var web3 = require("web3");if (module !== undefined) {  module.exports = {    digest: feedUpdateDigest  }}var topicLength = 32;var userLength = 20;var timeLength = 7;var levelLength = 1;var headerLength = 8;var updateMinLength = topicLength + userLength + timeLength + levelLength + headerLength;function feedUpdateDigest(request /*request*/, data /*UInt8Array*/) {  var topicBytes = undefined;    var userBytes = undefined;    var protocolVersion = 0;    protocolVersion = request.protocolVersion  try {    topicBytes = web3.utils.hexToBytes(request.feed.topic);  } catch(err) {    console.error("topicBytes: " + err);    return undefined;  }  try {    userBytes = web3.utils.hexToBytes(request.feed.user);  } catch(err) {    console.error("topicBytes: " + err);    return undefined;  }  var buf = new ArrayBuffer(updateMinLength + data.length);  var view = new DataView(buf);    var cursor = 0;    view.setUint8(cursor, protocolVersion) // first byte is protocol version.    cursor+=headerLength; // leave the next 7 bytes (padding) set to zero  topicBytes.forEach(function(v) {    view.setUint8(cursor, v);    cursor++;  });  userBytes.forEach(function(v) {    view.setUint8(cursor, v);    cursor++;  });  // time is little-endian  view.setUint32(cursor, request.epoch.time, true);  cursor += 7;  view.setUint8(cursor, request.epoch.level);  cursor++;  data.forEach(function(v) {    view.setUint8(cursor, v);    cursor++;    });    console.log(web3.utils.bytesToHex(new Uint8Array(buf)))  return web3.utils.sha3(web3.utils.bytesToHex(new Uint8Array(buf)));}// data payloaddata = new Uint8Array([5,154,15,165,62])// request template, obtained calling http://localhost:8500/bzz-feed:/?user=<0xUSER>&topic=<0xTOPIC>&meta=1request = {"feed":{"topic":"0x1234123412341234123412341234123412341234123412341234123412341234","user":"0xabcdefabcdefabcdefabcdefabcdefabcdefabcd"},"epoch":{"time":1538650124,"level":25},"protocolVersion":0}// obtain digestdigest = feedUpdateDigest(request, data)console.log(digest)
```

#### 4.4.5. Manifests

In general manifests declare a list of strings associated with Swarm hashes. A manifest matches to exactly one hash, and it consists of a list of entries declaring the content which can be retrieved through that hash. This is demonstrated by the following example:

Let’s create a directory containing the two orange papers and an html index file listing the two pdf documents.

```text
$ ls -1 orange-papers/index.htmlsmash.pdfsw^3.pdf$ cat orange-papers/index.html<!DOCTYPE html><html lang="en">  <head>    <meta charset="utf-8">  </head>  <body>    <ul>      <li>        <a href="./sw^3.pdf">Viktor Trón, Aron Fischer, Dániel Nagy A and Zsolt Felföldi, Nick Johnson: swap, swear and swindle: incentive system for swarm.</a>  May 2016      </li>      <li>        <a href="./smash.pdf">Viktor Trón, Aron Fischer, Nick Johnson: smash-proof: auditable storage for swarm secured by masked audit secret hash.</a> May 2016      </li>    </ul>  </body></html>
```

We now use the `swarm up` command to upload the directory to Swarm to create a mini virtual site.

Note

In this example we are using the public gateway through the bzz-api option in order to upload. The examples below assume a node running on localhost to access content. Make sure to run a local node to reproduce these examples.

```text
$ swarm --recursive --defaultpath orange-papers/index.html --bzzapi http://swarm-gateways.net/ up orange-papers/ 2> up.log> 2477cc8584cc61091b5cc084cdcdb45bf3c6210c263b0143f030cf7d750e894d
```

The returned hash is the hash of the manifest for the uploaded content \(the orange-papers directory\):

We now can get the manifest itself directly \(instead of the files they refer to\) by using the bzz-raw protocol `bzz-raw`:

```text
$ wget -O- "http://localhost:8500/bzz-raw:/2477cc8584cc61091b5cc084cdcdb45bf3c6210c263b0143f030cf7d750e894d"> {  "entries": [    {      "hash": "4b3a73e43ae5481960a5296a08aaae9cf466c9d5427e1eaa3b15f600373a048d",      "contentType": "text/html; charset=utf-8"    },    {      "hash": "4b3a73e43ae5481960a5296a08aaae9cf466c9d5427e1eaa3b15f600373a048d",      "contentType": "text/html; charset=utf-8",      "path": "index.html"    },    {      "hash": "69b0a42a93825ac0407a8b0f47ccdd7655c569e80e92f3e9c63c28645df3e039",      "contentType": "application/pdf",      "path": "smash.pdf"    },    {      "hash": "6a18222637cafb4ce692fa11df886a03e6d5e63432c53cbf7846970aa3e6fdf5",      "contentType": "application/pdf",      "path": "sw^3.pdf"    }  ]}
```

Note

macOS users can install wget via homebrew \(or use curl\).

Manifests contain content\_type information for the hashes they reference. In other contexts, where content\_type is not supplied or, when you suspect the information is wrong, it is possible to specify the content\_type manually in the search query. For example, the manifest itself should be text/plain:

```text
http://localhost:8500/bzz-raw:/2477cc8584cc61091b5cc084cdcdb45bf3c6210c263b0143f030cf7d750e894d?content_type="text/plain"
```

Now you can also check that the manifest hash matches the content \(in fact, Swarm does this for you\):

```text
$ wget -O- http://localhost:8500/bzz-raw:/2477cc8584cc61091b5cc084cdcdb45bf3c6210c263b0143f030cf7d750e894d?content_type="text/plain" > manifest.json$ swarm hash manifest.json> 2477cc8584cc61091b5cc084cdcdb45bf3c6210c263b0143f030cf7d750e894d
```

**4.4.5.1. Path Matching**

A useful feature of manifests is that we can match paths with URLs. In some sense this makes the manifest a routing table and so the manifest acts as if it was a host.

More concretely, continuing in our example, when we request:

```text
GET http://localhost:8500/bzz:/2477cc8584cc61091b5cc084cdcdb45bf3c6210c263b0143f030cf7d750e894d/sw^3.pdf
```

Swarm first retrieves the document matching the manifest above. The url path `sw^3` is then matched against the entries. In this case a perfect match is found and the document at 6a182226… is served as a pdf.

As you can see the manifest contains 4 entries, although our directory contained only 3. The extra entry is there because of the `--defaultpath orange-papers/index.html` option to `swarm up`, which associates the empty path with the file you give as its argument. This makes it possible to have a default page served when the url path is empty. This feature essentially implements the most common webserver rewrite rules used to set the landing page of a site served when the url only contains the domain. So when you request

```text
GET http://localhost:8500/bzz:/2477cc8584cc61091b5cc084cdcdb45bf3c6210c263b0143f030cf7d750e894d/
```

you get served the index page \(with content type `text/html`\) at `4b3a73e43ae5481960a5296a08aaae9cf466c9d5427e1eaa3b15f600373a048d`.

**4.4.5.2. Paths and directories**

Swarm manifests don’t “break” like a file system. In a file system, the directory matches at the path separator \(/ in linux\) at the end of a directory name:

```text
-- dirname/----subdir1/------subdir1file.ext------subdir2file.ext----subdir2/------subdir2file.ext
```

In Swarm, path matching does not happen on a given path separator, but **on common prefixes**. Let’s look at an example: The current manifest for the `theswarm.eth` homepage is as follows:

```text
wget -O- "http://swarm-gateways.net/bzz-raw:/theswarm.eth/ > manifest.json> {"entries":[{"hash":"ee55bc6844189299a44e4c06a4b7fbb6d66c90004159c67e6c6d010663233e26","path":"LICENSE","mode":420,"size":1211,"mod_time":"2018-06-12T15:36:29Z"},            {"hash":"57fc80622275037baf4a620548ba82b284845b8862844c3f56825ae160051446","path":"README.md","mode":420,"size":96,"mod_time":"2018-06-12T15:36:29Z"},            {"hash":"8919df964703ccc81de5aba1b688ff1a8439b4460440a64940a11e1345e453b5","path":"Swarm_files/","contentType":"application/bzz-manifest+json","mod_time":"0001-01-01T00:00:00Z"},            {"hash":"acce5ad5180764f1fb6ae832b624f1efa6c1de9b4c77b2e6ec39f627eb2fe82c","path":"css/","contentType":"application/bzz-manifest+json","mod_time":"0001-01-01T00:00:00Z"},            {"hash":"0a000783e31fcf0d1b01ac7d7dae0449cf09ea41731c16dc6cd15d167030a542","path":"ethersphere/orange-papers/","contentType":"application/bzz-manifest+json","mod_time":"0001-01-01T00:00:00Z"},            {"hash":"b17868f9e5a3bf94f955780e161c07b8cd95cfd0203d2d731146746f56256e56","path":"f","contentType":"application/bzz-manifest+json","mod_time":"0001-01-01T00:00:00Z"},            {"hash":"977055b5f06a05a8827fb42fe6d8ec97e5d7fc5a86488814a8ce89a6a10994c3","path":"i","contentType":"application/bzz-manifest+json","mod_time":"0001-01-01T00:00:00Z"},            {"hash":"48d9624942e927d660720109b32a17f8e0400d5096c6d988429b15099e199288","path":"js/","contentType":"application/bzz-manifest+json","mod_time":"0001-01-01T00:00:00Z"},            {"hash":"294830cee1d3e63341e4b34e5ec00707e891c9e71f619bc60c6a89d1a93a8f81","path":"talks/","contentType":"application/bzz-manifest+json","mod_time":"0001-01-01T00:00:00Z"},            {"hash":"12e1beb28d86ed828f9c38f064402e4fac9ca7b56dab9cf59103268a62a2b35f","contentType":"text/html; charset=utf-8","mode":420,"size":31371,"mod_time":"2018-06-12T15:36:29Z"}  ]}
```

Note the `path` for entry `b17868...`: It is `f`. This means, there are more than one entries for this manifest which start with an f, and all those entries will be retrieved by requesting the hash `b17868...` and through that arrive at the matching manifest entry:

```text
$ wget -O- http://localhost:8500/bzz-raw:/b17868f9e5a3bf94f955780e161c07b8cd95cfd0203d2d731146746f56256e56/{"entries":[{"hash":"25e7859eeb7366849f3a57bb100ff9b3582caa2021f0f55fb8fce9533b6aa810","path":"avicon.ico","mode":493,"size":32038,"mod_time":"2018-06-12T15:36:29Z"},            {"hash":"97cfd23f9e36ca07b02e92dc70de379a49be654c7ed20b3b6b793516c62a1a03","path":"onts/glyphicons-halflings-regular.","contentType":"application/bzz-manifest+json","mod_time":"0001-01-01T00:00:00Z"} ]}
```

So we can see that the `f` entry in the root hash resolves to a manifest containing `avicon.ico` and `onts/glyphicons-halflings-regular`. The latter is interesting in itself: its `content_type` is `application/bzz-manifest+json`, so it points to another manifest. Its `path` also does contain a path separator, but that does not result in a new manifest after the path separator like a directory \(e.g. at `onts/`\). The reason is that on the file system on the hard disk, the `fonts` directory only contains _one_ directory named `glyphicons-halflings-regular`, thus creating a new manifest for just `onts/` would result in an unnecessary lookup. This general approach has been chosen to limit unnecessary lookups that would only slow down retrieval, and manifest “forks” happen in order to have the logarythmic bandwidth needed to retrieve a file in a directory with thousands of files.

When requesting `wget -O- "http://swarm-gateways.net/bzz-raw:/theswarm.eth/favicon.ico`, Swarm will first retrieve the manifest at the root hash, match on the first `f` in the entry list, resolve the hash for that entry and finally resolve the hash for the `favicon.ico` file.

For the `theswarm.eth` page, the same applies to the `i` entry in the root hash manifest. If we look up that hash, we’ll find entries for `mages/` \(a further manifest\), and `ndex.html`, whose hash resolves to the main `index.html` for the web page.

Paths like `css/` or `js/` get their own manifests, just like common directories, because they contain several files.

Note

If a request is issued which Swarm can not resolve unambiguosly, a `300 "Multiplce Choices"` HTTP status will be returned. In the example above, this would apply for a request for `http://swarm-gateways.net/bzz:/theswarm.eth/i`, as it could match both `images/` as well as `index.html`

#### 4.4.6. Encryption

Introduced in POC 0.3, symmetric encryption is now readily available to be used with the `swarm up` upload command. The encryption mechanism is meant to protect your information and make the chunked data unreadable to any handling Swarm node.

Swarm uses [Counter mode encryption](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Counter_%28CTR%29) to encrypt and decrypt content. When you upload content to Swarm, the uploaded data is split into 4 KB chunks. These chunks will all be encoded with a separate randomly generated encryption key. The encryption happens on your local Swarm node, unencrypted data is not shared with other nodes. The reference of a single chunk \(and the whole content\) will be the concatenation of the hash of encoded data and the decryption key. This means the reference will be longer than the standard unencrypted Swarm reference \(64 bytes instead of 32 bytes\).

When your node syncs the encrypted chunks of your content with other nodes, it does not share the full references \(or the decryption keys in any way\) with the other nodes. This means that other nodes will not be able to access your original data, moreover they will not be able to detect whether the synchronized chunks are encrypted or not.

When your data is retrieved it will only get decrypted on your local Swarm node. During the whole retrieval process the chunks traverse the network in their encrypted form, and none of the participating peers are able to decrypt them. They are only decrypted and assembled on the Swarm node you use for the download.

More info about how we handle encryption at Swarm can be found [here](https://github.com/ethersphere/swarm/wiki/Symmetric-Encryption-for-Swarm-Content).

Note

Swarm currently supports both encrypted and unencrypted `swarm up` commands through usage of the `--encrypt` flag. This might change in the future as we will refine and make Swarm a safer network.

Important

The encryption feature is non-deterministic \(due to a random key generated on every upload request\) and users of the API should not rely on the result being idempotent; thus uploading the same content twice to Swarm with encryption enabled will not result in the same reference.

Example usage:

First, we create a simple test file.

```text
$ echo "testfile" > mytest.txt
```

We upload the test file **without** encryption,

```text
$ swarm up mytest.txt> <file reference>
```

and **with** encryption.

```text
$ swarm up --encrypt mytest.txt> <encrypted reference>
```

Note that the reference of the encrypted upload is **longer** than that of the unencrypted upload. Note also that, because of the random encryption key, repeating the encrypted upload results in a different reference:

```text
$ swarm up --encrypt mytest.txt<another encrypted reference>
```

#### 4.4.7. Access Control

Swarm supports restricting access to content through several access control strategies:

* Password protection - where a number of undisclosed parties can access content using a shared secret `(pass, act)`
* Selective access using [Elliptic Curve](https://en.wikipedia.org/wiki/Elliptic-curve_cryptography) key-pairs:

  > * For an undisclosed party - where only one grantee can access the content `(pk)`
  > * For a number of undisclosed parties - where every grantee can access the content `(act)`

**Creating** access control for content is currently supported only through CLI usage.

**Accessing** restricted content is available through CLI and HTTP. When accessing content which is restricted by a password [HTTP Basic access authentication](https://en.wikipedia.org/wiki/Basic_access_authentication) can be used out-of-the-box.

Important

When accessing content which is restricted to certain EC keys - the node which exposes the HTTP proxy that is queried must be started with the granted private key as its `bzzaccount` CLI parameter.

**4.4.7.1. Password protection**

The simplest type of credential is a passphrase. In typical use cases, the passphrase is distributed by off-band means, with adequate security measures. Any user that knows the passphrase can access the content.

When using password protection, a given content reference \(e.g.: a given Swarm manifest address or, alternatively, a Mutable Resource address\) is encrypted using [scrypt](https://en.wikipedia.org/wiki/Scrypt) with a given passphrase and a random salt. The encrypted reference and the salt are then embedded into an unencrypted manifest which can be freely distributed but only accessed by undisclosed parties that posses knowledge of the passphrase.

Password protection can also be used for selective access when using the `act` strategy - similarly to granting access to a certain EC key access can be also given to a party identified by a password. In fact, one could also create an `act` manifest that solely grants access to grantees through passwords, without the need to know their public keys.

Example usage:

Important

Restricting access to content on Swarm is a 2-step process - you first upload your content, then wrap the reference with an access control manifest. **We recommend that you always upload your content with encryption enabled**. In the following examples we will refer the uploaded content hash as `reference hash`

First, we create a simple test file. We upload it to Swarm \(with encryption\).

```text
$ echo "testfile" > mytest.txt$ swarm up --encrypt mytest.txt> <reference hash>
```

Then, for the sake of this example, we create a file with our password in it.

```text
$ echo "mypassword" > mypassword.txt
```

This password will protect the access-controlled content that we upload. We can refer to this password using the –password flag. The password file should contain the password in plaintext.

The `swarm access` command sets a new password using the `new pass` argument. It expects you to input the password file and the uploaded Swarm content hash you’d like to limit access to.

```text
$ swarm access new pass --password mypassword.txt <reference hash>> <reference of access controlled manifest>
```

The returned hash is the hash of the access controlled manifest.

When requesting this hash through the HTTP gateway you should receive an `HTTP Unauthorized 401` error:

```text
$ curl http://localhost:8500/bzz:/<reference of access controlled manifest>/> Code: 401> Message: cant decrypt - forbidden> Timestamp: XXX
```

You can retrieve the content in three ways:

1. The same request should make an authentication dialog pop-up in the browser. You could then input the password needed and the content should correctly appear. \(Leave the username empty.\)
2. Requesting the same hash with HTTP basic authentication would return the content too. `curl` needs you to input a username as well as a password, but the former can be an arbitrary string \(here, it’s `x`\).

```text
$ curl http://x:mypassword@localhost:8500/bzz:/<reference of access controlled manifest>/
```

1. You can also use `swarm down` with the `--password` flag.

```text
$ swarm  --password mypassword.txt down bzz:/<reference of access controlled manifest>/ mytest2.txt$ cat mytest2.txt> testfile
```

**4.4.7.2. Selective access using EC keys**

A more sophisticated type of credential is an [Elliptic Curve](https://en.wikipedia.org/wiki/Elliptic-curve_cryptography) private key, identical to those used throughout Ethereum for accessing accounts.

In order to obtain the content reference, an [Elliptic-curve Diffie–Hellman](https://en.wikipedia.org/wiki/Elliptic-curve_Diffie%E2%80%93Hellman) \(ECDH\) key agreement needs to be performed between a provided EC public key \(that of the content publisher\) and the authorized key, after which the undisclosed authorized party can decrypt the reference to the access controlled content.

Whether using access control to disclose content to a single party \(by using the `pk` strategy\) or to multiple parties \(using the `act` strategy\), a third unauthorized party cannot find out the identity of the authorized parties. The third party can, however, know the number of undisclosed grantees to the content. This, however, can be mitigated by adding bogus grantee keys while using the `act` strategy in cases where masking the number of grantees is necessary. This is not the case when using the `pk` strategy, as it as by definition an agreement between two parties and only two parties \(the publisher and the grantee\).

Important

Accessing content which is access controlled is enabled only when using a local Swarm node \(e.g. running on localhost\) in order to keep your data, passwords and encryption keys safe. This is enforced through an in-code guard.

Danger

**NEVER \(EVER!\) use an external gateway to upload or download access controlled content as you will be putting your privacy at risk! You have been fairly warned!**

**Protecting content with Elliptic curve keys \(single grantee\):**

The `pk` strategy requires a `bzzaccount` to encrypt with. The most comfortable option in this case would be the same `bzzaccount` you normally start your Swarm node with - this will allow you to access your content seamlessly through that node at any given point in time.

Grantee public keys are expected to be in an _secp256 compressed_ form - 66 characters long string \(an example would be `02e6f8d5e28faaa899744972bb847b6eb805a160494690c9ee7197ae9f619181db`\). Comments and other characters are not allowed.

```text
$ swarm --bzzaccount <your account> access new pk --grant-key <your public key> <reference hash>> <reference of access controlled manifest>
```

The returned hash `4b964a75ab19db960c274058695ca4ae21b8e19f03ddf1be482ba3ad3c5b9f9b` is the hash of the access controlled manifest.

The only way to fetch the access controlled content in this case would be to request the hash through one of the nodes that were granted access and/or posses the granted private key \(and that the requesting node has been started with the appropriate `bzzaccount` that is associated with the relevant key\) - either the local node that was used to upload the content or the node which was granted access through its public key.

**Protecting content with Elliptic curve keys and passwords \(multiple grantees\):**

The `act` strategy also requires a `bzzaccount` to encrypt with. The most comfortable option in this case would be the same `bzzaccount` you normally start your Swarm node with - this will allow you to access your content seamlessly through that node at any given point in time

Note

the `act` strategy expects a grantee public-key list and/or a list of permitted passwords to be communicated to the CLI. This is done using the `--grant-keys` flag and/or the `--password` flag. Grantee public keys are expected to be in an _secp256 compressed_ form - 66 characters long string \(e.g. `02e6f8d5e28faaa899744972bb847b6eb805a160494690c9ee7197ae9f619181db`\). Each grantee should appear in a separate line. Passwords are also expected to be line-separated. Comments and other characters are not allowed.

```text
swarm --bzzaccount 2f1cd699b0bf461dcfbf0098ad8f5587b038f0f1 access new act --grant-keys /path/to/public-keys/file --password /path/to/passwords/file  <reference hash>4b964a75ab19db960c274058695ca4ae21b8e19f03ddf1be482ba3ad3c5b9f9b
```

The returned hash `4b964a75ab19db960c274058695ca4ae21b8e19f03ddf1be482ba3ad3c5b9f9b` is the hash of the access controlled manifest.

As with the `pk` strategy - the only way to fetch the access controlled content in this case would be to request the hash through one of the nodes that were granted access and/or posses the granted private key \(and that the requesting node has been started with the appropriate `bzzaccount` that is associated with the relevant key\) - either the local node that was used to upload the content or one of the nodes which were granted access through their public keys.

**4.4.7.3. HTTP usage**

Accessing restricted content on Swarm through the HTTP API is, as mentioned, limited to your local node due to security considerations. Whenever requesting a restricted resource without the proper credentials via the HTTP proxy, the Swarm node will respond with an `HTTP 401 Unauthorized` response code.

_When accessing password protected content:_

When accessing a resource protected by a passphrase without the appropriate credentials the browser will receive an `HTTP 401 Unauthorized` response and will show a pop-up dialog asking for a username and password. For the sake of decrypting the content - only the password input in the dialog matters and the username field can be left blank.

The credentials for accessing content protected by a password can be provided in the initial request in the form of: `http://x:<password>@localhost:8500/bzz:/<hash or ens name>` \(`curl` needs you to input a username as well as a password, but the former can be an arbitrary string \(here, it’s `x`\).\)

Important

Access controlled content should be accessed through the `bzz://` protocol

_When accessing EC key protected content:_

When accessing a resource protected by EC keys, the node that requests the content will try to decrypt the restricted content reference using its **own** EC key which is associated with the current bzz account that the node was started with \(see the `--bzzaccount` flag\). If the node’s key is granted access - the content will be decrypted and displayed, otherwise - an `HTTP 401 Unauthorized` error will be returned by the node.

**4.4.7.4. Access control in the CLI: example usage**

PasswordsElliptic curve keys

First, we create a simple test file. We upload it to Swarm using encryption.

```text
$ echo "testfile" > mytest.txt$ swarm up  --encrypt mytest.txt> <reference hash>
```

Then, we define a password file and use it to create an access-controlled manifest.

```text
$ echo "mypassword" > mypassword.txt$ swarm access new pass --password mypassword.txt <reference hash>> <reference of access controlled manifest>
```

We can create a passwords file with one password per line in plaintext \(`password1` is probably not a very good password\).

```text
$ for i in {1..3}; do echo -e password$i; done > mypasswords.txt$ cat mypasswords.txt> password1> password2> password3
```

Then, we point to this list while wrapping our manifest.

```text
$ swarm access new act --password mypasswords.txt <reference hash>> <reference of access controlled manifest>
```

We can access the returned manifest using any of the passwords in the password list.

```text
$ echo password1 > password1.txt$ swarm --password1.txt down bzz:/<reference of access controlled manifest>
```

We can also curl it.

```text
$ curl http://:password1@localhost:8500/bzz:/<reference of access controlled manifest>/
```

#### 4.4.8. FUSE

Another way of interacting with Swarm is by mounting it as a local filesystem using [FUSE](https://en.wikipedia.org/wiki/Filesystem_in_Userspace) \(Filesystem in Userspace\). There are three IPC API’s which help in doing this.

Note

FUSE needs to be installed on your Operating System for these commands to work. Windows is not supported by FUSE, so these command will work only in Linux, Mac OS and FreeBSD. For installation instruction for your OS, see “Installing FUSE” section below.

**4.4.8.1. Installing FUSE**

1. Linux \(Ubuntu\)

```text
$ sudo apt-get install fuse$ sudo modprobe fuse$ sudo chown <username>:<groupname> /etc/fuse.conf$ sudo chown <username>:<groupname> /dev/fuse
```

1. Mac OS

   Either install the latest package from [https://osxfuse.github.io/](https://osxfuse.github.io/) or use brew as below

```text
$ brew update$ brew install caskroom/cask/brew-cask$ brew cask install osxfuse
```

**4.4.8.2. CLI Usage**

The Swarm CLI now integrates commands to make FUSE usage easier and streamlined.

Note

When using FUSE from the CLI, we assume you are running a local Swarm node on your machine. The FUSE commands attach to the running node through bzzd.ipc

**4.4.8.3. Mount**

One use case to mount a Swarm hash via FUSE is a file sharing feature accessible via your local file system. Files uploaded to Swarm are then transparently accessible via your local file system, just as if they were stored locally.

To mount a Swarm resource, first upload some content to Swarm using the `swarm up <resource>` command. You can also upload a complete folder using `swarm --recursive up <directory>`. Once you get the returned manifest hash, use it to mount the manifest to a mount point \(the mount point should exist on your hard drive\):

```text
$ swarm fs mount <manifest-hash> <mount-point>
```

For example:

```text
$ swarm fs mount <manifest-hash> /home/user/swarmmount
```

Your running Swarm node terminal output should show something similar to the following in case the command returned successfuly:

```text
Attempting to mount /path/to/mount/pointServing 6e4642148d0a1ea60e36931513f3ed6daf3deb5e499dcf256fa629fbc22cf247 at /path/to/mount/pointNow serving swarm FUSE FS                manifest=6e4642148d0a1ea60e36931513f3ed6daf3deb5e499dcf256fa629fbc22cf247 mountpoint=/path/to/mount/point
```

You may get a “Fatal: had an error calling the RPC endpoint while mounting: context deadline exceeded” error if it takes too long to retrieve the content.

In your OS, via terminal or file browser, you now should be able to access the contents of the Swarm hash at `/path/to/mount/point`, i.e. `ls /home/user/swarmmount`

**4.4.8.4. Access**

Through your terminal or file browser, you can interact with your new mount as if it was a local directory. Thus you can add, remove, edit, create files and directories just as on a local directory. Every such action will interact with Swarm, taking effect on the Swarm distributed storage. Every such action also will result **in a new hash** for your mounted directory. If you would unmount and remount the same directory with the previous hash, your changes would seem to have been lost \(effectively you are just mounting the previous version\). While you change the current mount, this happens under the hood and your mount remains up-to-date.

**4.4.8.5. Unmount**

To unmount a `swarmfs` mount, either use the List Mounts command below, or use a known mount point:

```text
$ swarm fs unmount <mount-point>> 41e422e6daf2f4b32cd59dc6a296cce2f8cce1de9f7c7172e9d0fc4c68a3987a
```

The returned hash is the latest manifest version that was mounted. You can use this hash to remount the latest version with the most recent changes.

**4.4.8.6. List Mounts**

To see all existing swarmfs mount points, use the List Mounts command:

```text
$ swarm fs list
```

Example Output:

```text
Found 1 swarmfs mount(s):0:        Mount point: /path/to/mount/point        Latest Manifest: 6e4642148d0a1ea60e36931513f3ed6daf3deb5e499dcf256fa629fbc22cf247        Start Manifest: 6e4642148d0a1ea60e36931513f3ed6daf3deb5e499dcf256fa629fbc22cf247
```

#### 4.4.9. Pinning Content

When content is uploaded in Swarm, it gets chunked and scattered over the network for storage. In the original Swarm design, the nodes that store the chunks gets incentivised for by the SWAP, SWEAR and SWINDLE protocols. For now, the incentivisation protocols are yet to be implemented. As a result of that, nodes does not have use preference in storing content. The default model used now is First Come First Serve \(FIFO\). The effect of the above model is that the uploaded contents will disappear after few days depending upon the overall network storage capacity. This is a undesirable property of the network today. To overcome this issue until the incentivisation layer is fully operational, pinning contents is implemented now. Aanyone can now pin a content \(Swarm collection or a RAW file\) on a Swarm node. i.e. a copy of the pinned content will be stored locally permenantly. Even if the content in the network disappears, it can be accessed from the pinned server always.

Note

Pinned content will be available only from the Swarm node where it is pinned.

**4.4.9.1. Pinning Content**

Content can be pinned in two different ways. One is during the content upload and the other is thereafter.

1. Pinning during upload

   Add a header “x-swarm-pin” and set it to “true” when uploading content. This will upload the file and then pin it too. This method can be used for Tar, Multipart and RAW file uploads too.

```text
curl -H "Content-Type: application/x-tar" -H "x-swarm-pin: true"  --data-binary @files.tar http://localhost:8500/bzz:/
```

1. Pinning after upload

   If an already uploaded content needs to be pinned, the following HTTP API should be used.

```text
# to pin a Swarm collectionPOST /bzz-pin:/<MANIFEST OR ENS NAME># to pin a RAW file in SwarmPOST /bzz-pin:/<SWARM RAW FILE HASH>/?raw=true
```

Note

When pinning a already uploaded file, make sure that the entire file content is available locally by issuing a download once.

**4.4.9.2. Unpinning Content**

An already pinned file can be unpinned at anytime. Once the collection is unpinned, the contents will follow the FIFO rule and may be garbage collected in future.

```text
DELETE /bzz-pin:/<MANIFEST OR ENS NAME OR SWARM RAW FILE HASH>
```

**4.4.9.3. Listing Pinning Info**

Pinned contents and their information can be viewed at any point using this API. Information includes The pinned hash, wether the pinned content is a collection or RAW file, the pinned content size in bytes and the no of time the content is pinned.

```text
GET /bzz-pin:/[ { "Address"    : "0x94f78a45c7897957809544aa6d68aa7ad35df695713895953b885aca274bd955",   "IsRaw"      : "false",   "FileSize"   : "12046",   "PinCounter" : "2", }, { "Address"    : "0xccef599d1a13bed9989e424011aed2c023fce25917864cd7de38a761567410b8",   "IsRaw"      : "true",   "FileSize"   : "146",   "PinCounter" : "5", },]
```

Note

The information will be returned in json format shown above

#### 4.4.10. BZZ URL schemes

Swarm offers 6 distinct URL schemes:

**4.4.10.1. bzz**

The bzz scheme assumes that the domain part of the url points to a manifest. When retrieving the asset addressed by the URL, the manifest entries are matched against the URL path. The entry with the longest matching path is retrieved and served with the content type specified in the corresponding manifest entry.

Example:

```text
GET http://localhost:8500/bzz:/2477cc8584cc61091b5cc084cdcdb45bf3c6210c263b0143f030cf7d750e894d/readme.md
```

returns a readme.md file if the manifest at the given hash address contains such an entry.

```text
$ lsreadme.md$ swarm --recursive up .c4c81dbce3835846e47a83df549e4cad399c6a81cbf83234274b87d49f5f9020$ curl http://localhost:8500/bzz-raw:/c4c81dbce3835846e47a83df549e4cad399c6a81cbf83234274b87d49f5f9020/readme.md## Hello Swarm!Swarm is awesome%
```

If the manifest does not contain an file at `readme.md` itself, but it does contain multiple entries to which the URL could be resolved, e.g. in the example above, the manifest has entries for `readme.md.1` and `readme.md.2`, the API returns an HTTP response “300 Multiple Choices”, indicating that the request could not be unambiguously resolved. A list of available entries is returned via HTTP or JSON.

```text
$ lsreadme.md.1 readme.md.2$ swarm --recursive up .679bde3ccb6fb911db96a0ea1586c04899c6c0cc6d3426e9ee361137b270a463$ curl -H "Accept:application/json" http://localhost:8500/bzz:/679bde3ccb6fb911db96a0ea1586c04899c6c0cc6d3426e9ee361137b270a463/readme.md{"Msg":"\u003ca href='/bzz:/679bde3ccb6fb911db96a0ea1586c04899c6c0cc6d3426e9ee361137b270a463/readme.md.1'\u003ereadme.md.1\u003c/a\u003e\u003cbr/\u003e\u003ca href='/bzz:/679bde3ccb6fb911db96a0ea1586c04899c6c0cc6d3426e9ee361137b270a463/readme.md.2'\u003ereadme.md.2\u003c/a\u003e\u003cbr/\u003e","Code":300,"Timestamp":"Fri, 15 Jun 2018 14:48:42 CEST","Details":""}$ curl -H "Accept:application/json" http://localhost:8500/bzz:/679bde3ccb6fb911db96a0ea1586c04899c6c0cc6d3426e9ee361137b270a463/readme.md | jq{    "Msg": "<a href='/bzz:/679bde3ccb6fb911db96a0ea1586c04899c6c0cc6d3426e9ee361137b270a463/readme.md.1'>readme.md.1</a><br/><a href='/bzz:/679bde3ccb6fb911db96a0ea1586c04899c6c0cc6d3426e9ee361137b270a463/readme.md.2'>readme.md.2</a><br/>",    "Code": 300,    "Timestamp": "Fri, 15 Jun 2018 14:49:02 CEST",    "Details": ""}
```

`bzz` scheme also accepts POST requests to upload content and create manifest for them in one go:

```text
$ curl -H "Content-Type: text/plain" --data-binary "some-data" http://localhost:8500/bzz:/635d13a547d3252839e9e68ac6446b58ae974f4f59648fe063b07c248494c7b2%$ curl http://localhost:8500/bzz:/635d13a547d3252839e9e68ac6446b58ae974f4f59648fe063b07c248494c7b2/some-data%$ curl -H "Accept:application/json" http://localhost:8500/bzz-raw:/635d13a547d3252839e9e68ac6446b58ae974f4f59648fe063b07c248494c7b2/ | jq .{    "entries": [        {            "hash": "379f234c04ed1a18722e4c76b5029ff6e21867186c4dfc101be4f1dd9a879d98",            "contentType": "text/plain",            "mode": 420,            "size": 9,            "mod_time": "2018-06-15T15:46:28.835066044+02:00"        }    ]}
```

**4.4.10.2. bzz-raw**

```text
GET http://localhost:8500/bzz-raw:/2477cc8584cc61091b5cc084cdcdb45bf3c6210c263b0143f030cf7d750e894d
```

When responding to GET requests with the bzz-raw scheme, Swarm does not assume that the hash resolves to a manifest. Instead it just serves the asset referenced by the hash directly. So if the hash actually resolves to a manifest, it returns the raw manifest content itself.

E.g. continuing the example in the `bzz` section above with `readme.md.1` and `readme.md.2` in the manifest:

```text
$ curl http://localhost:8500/bzz-raw:/679bde3ccb6fb911db96a0ea1586c04899c6c0cc6d3426e9ee361137b270a463/ | jq{    "entries": [        {        "hash": "efc6d4a7d7f0846973a321d1702c0c478a20f72519516ef230b63baa3da18c22",        "path": "readme.md.",        "contentType": "application/bzz-manifest+json",        "mod_time": "0001-01-01T00:00:00Z"        }    ]    }$ curl http://localhost:8500/bzz-raw:/efc6d4a7d7f0846973a321d1702c0c478a20f72519516ef230b63baa3da18c22/ | jq{    "entries": [        {            "hash": "d0675100bc4580a0ad890b5d6f06310c0705d4ab1e796cfa1a8c597840f9793f",            "path": "1",            "mode": 420,            "size": 33,            "mod_time": "2018-06-15T14:21:32+02:00"        },        {            "hash": "f97cf36ac0dd7178c098f3661cd0402fcc711ff62b67df9893d29f1db35adac6",            "path": "2",            "mode": 420,            "size": 35,            "mod_time": "2018-06-15T14:42:06+02:00"        }    ]    }
```

The `content_type` query parameter can be supplied to specify the MIME type you are requesting, otherwise content is served as an octet-stream per default. For instance if you have a pdf document \(not the manifest wrapping it\) at hash `6a182226...` then the following url will properly serve it.

```text
GET http://localhost:8500/bzz-raw:/6a18222637cafb4ce692fa11df886a03e6d5e63432c53cbf7846970aa3e6fdf5?content_type=application/pdf
```

`bzz-raw` also supports POST requests to upload content to Swarm, the response is the hash of the uploaded content:

```text
$ curl --data-binary "some-data" http://localhost:8500/bzz-raw:/379f234c04ed1a18722e4c76b5029ff6e21867186c4dfc101be4f1dd9a879d98%$ curl http://localhost:8500/bzz-raw:/379f234c04ed1a18722e4c76b5029ff6e21867186c4dfc101be4f1dd9a879d98/some-data%
```

**4.4.10.3. bzz-list**

```text
GET http://localhost:8500/bzz-list:/2477cc8584cc61091b5cc084cdcdb45bf3c6210c263b0143f030cf7d750e894d/path
```

Returns a list of all files contained in &lt;manifest&gt; under &lt;path&gt; grouped into common prefixes using `/` as a delimiter. If no path is supplied, all files in manifest are returned. The response is a JSON-encoded object with `common_prefixes` string field and `entries` list field.

```text
$ curl http://localhost:8500/bzz-list:/679bde3ccb6fb911db96a0ea1586c04899c6c0cc6d3426e9ee361137b270a463/ | jq{    "entries": [        {            "hash": "d0675100bc4580a0ad890b5d6f06310c0705d4ab1e796cfa1a8c597840f9793f",            "path": "readme.md.1",            "mode": 420,            "size": 33,            "mod_time": "2018-06-15T14:21:32+02:00"        },        {            "hash": "f97cf36ac0dd7178c098f3661cd0402fcc711ff62b67df9893d29f1db35adac6",            "path": "readme.md.2",            "mode": 420,            "size": 35,            "mod_time": "2018-06-15T14:42:06+02:00"        }    ]    }
```

**4.4.10.4. bzz-hash**

```text
GET http://localhost:8500/bzz-hash:/theswarm.eth/
```

Swarm accepts GET requests for bzz-hash url scheme and responds with the hash value of the raw content, the same content returned by requests with bzz-raw scheme. Hash of the manifest is also the hash stored in ENS so bzz-hash can be used for ENS domain resolution.

Response content type is _text/plain_.

```text
$ curl http://localhost:8500/bzz-hash:/theswarm.eth/7a90587bfc04ac4c64aeb1a96bc84f053d3d84cefc79012c9a07dd5230dc1fa4%
```

**4.4.10.5. bzz-immutable**

```text
GET http://localhost:8500/bzz-immutable:/2477cc8584cc61091b5cc084cdcdb45bf3c6210c263b0143f030cf7d750e894d
```

The same as the generic scheme but there is no ENS domain resolution, the domain part of the path needs to be a valid hash. This is also a read-only scheme but explicit in its integrity protection. A particular bzz-immutable url will always necessarily address the exact same fixed immutable content.

```text
$ curl http://localhost:8500/bzz-immutable:/679bde3ccb6fb911db96a0ea1586c04899c6c0cc6d3426e9ee361137b270a463/readme.md.1## Hello Swarm!Swarm is awesome%$ curl -H "Accept:application/json" http://localhost:8500/bzz-immutable:/theswarm.eth/ | jq .{    "Msg": "cannot resolve theswarm.eth: immutable address not a content hash: \"theswarm.eth\"",    "Code": 404,    "Timestamp": "Fri, 15 Jun 2018 13:22:27 UTC",    "Details": ""}
```

### 4.5. Swarm Messaging for DAPP-Developers

_pss_ \(Postal Service over Swarm\) is a messaging protocol over Swarm with strong privacy features. The pss API is exposed through a JSON RPC interface described in the [API Reference](https://swarm-guide.readthedocs.io/en/latest/dapp_developer/apireference.rst#PSS), here we explain the basic concepts and features.

With `pss` you can send messages to any node in the Swarm network. The messages are routed in the same manner as retrieve requests for chunks. Instead of chunk hash reference, `pss` messages specify a destination in the overlay address space independently of the message payload. This destination can describe a _specific node_ if it is a complete overlay address or a _neighbourhood_ if it is partially specified one. Up to the destination, the message is relayed through devp2p peer connections using _forwarding kademlia_ \(passing messages via semi-permanent peer-to-peer TCP connections between relaying nodes using kademlia routing\). Within the destination neighbourhood the message is broadcast using gossip.

Since `pss` messages are encrypted, ultimately _the recipient is whoever can decrypt the message_. Encryption can be done using asymmetric or symmetric encryption methods.

The message payload is dispatched to _message handlers_ by the recipient nodes and dispatched to subscribers via the API.

Important

`pss` does not guarantee message ordering \([Best-effort delivery](https://en.wikipedia.org/wiki/Best-effort_delivery)\) nor message delivery \(e.g. messages to offline nodes will not be cached and replayed\) at the moment.

Thanks to end-to-end encryption, pss caters for private communication.

Due to forwarding kademlia, `pss` offers sender anonymity.

Using partial addressing, `pss` offers a sliding scale of recipient anonymity: the larger the destination neighbourhood \(the smaller prefix you reveal of the intended recipient overlay address\), the more difficult it is to identify the real recipient. On the other hand, since dark routing is inefficient, there is a trade-off between anonymity on the one hand and message delivery latency and bandwidth \(and therefore cost\) on the other. This choice is left to the application.

Forward secrecy is provided if you use the Handshakes module.

#### 4.5.1. PSS Usage

**4.5.1.1. Registering a recipient**

Intended recipients first need to be registered with the node. This registration includes the following data:

1. `Encryption key` - can be a ECDSA public key for asymmetric encryption or a 32 byte symmetric key.
2. `Topic` - an arbitrary 4 byte word.
3. `Address`- destination \(fully or partially specified Swarm overlay address\) to use for deterministic routing.

   The registration returns a key id which is used to refer to the stored key in subsequent operations.

After you associate an encryption key with an address they will be checked against any message that comes through \(when sending or receiving\) given it matches the topic and the destination of the message.

**4.5.1.2. Sending a message**

There are a few prerequisites for sending a message over `pss`:

1. `Encryption key id` - id of the stored recipient’s encryption key.
2. `Topic` - an arbitrary 4 byte word \(with the exception of `0x0000` to be reserved for `raw` messages\).
3. `Message payload` - the message data as an arbitrary byte sequence.

Note

The Address that is coupled with the encryption key is used for routing the message. This does _not_ need to be a full address; the network will route the message to the best of its ability with the information that is available. If _no_ address is given \(zero-length byte slice\), routing is effectively deactivated, and the message is passed to all peers by all peers.

Upon sending the message it is encrypted and passed on from peer to peer. Any node along the route that can successfully decrypt the message is regarded as a recipient. If the destination is a neighbourhood, the message is passed around so ultimately it reaches the intended recipient which also forwards the message to their peers, recipients will continue to pass on the message to their peers, to make it harder for anyone spying on the traffic to tell where the message “ended up.”

After you associate an encryption key with a destination they will be checked against any message that comes through \(when sending or receiving\) given it matches the topic and the address in the message.

Important

When using the internal encryption methods, you MUST associate keys \(whether symmetric or asymmetric\) with an address space AND a topic before you will be able to send anything.

**4.5.1.3. Sending a raw message**

It is also possible to send a message without using the builtin encryption. In this case no recipient registration is made, but the message is sent directly, with the following input data:

1. `Message payload` - the message data as an arbitrary byte sequence.
2. `Address`- the Swarm overlay address to use for the routing.

**4.5.1.4. Receiving messages**

You can subscribe to incoming messages using a topic. Since subscription needs push notifications, the supported RPC transport interfaces are websockets and IPC.

Important

`pss` does not guarantee message ordering \([Best-effort delivery](https://en.wikipedia.org/wiki/Best-effort_delivery)\) nor message delivery \(e.g. messages to offline nodes will not be cached and replayed\) at the moment.

**4.5.1.5. Protocols**

A framework is also in place for making `devp2p` protocols available using `pss` connections. This feature is only available using the internal golang API, read more in the GoDocs or the codes.

### 4.6. API reference

#### 4.6.1. HTTP

| Name | Method | Descriptors |  |
| :--- | :--- | :--- | :--- |
| bzz | GET | Purpose | retrieve document at domain/some/path allowing domain to resolve via the [Ethereum Name System \(ENS\)](https://swarm-guide.readthedocs.io/en/latest/features/ens.html#ethereum-name-service) |
|  |  | Locator | bzz:/&lt;domain\_part&gt;/&lt;resource\_path&gt; |
|  |  | Locator Parts | domain part: mandatory - ENS name or a valid Swarm hash. path part: optional - a case insensitive path to match for in the manifest |
|  |  | HTTP Codes | 200; 300; 404; 500 |
|  |  | Responds with | The content stored at the resolved ENS entry \(or the matched path\) with the appropriate Content-Type as stored in the manifest |
|  |  | Example |  |
|  | POST | Purpose | post an application/x-tar or multipart/form-data \(or any other Content-Type for that matter\); create an appropriate manifest and retrieve the associated hash. if an existing manifest address is given with the according path to update - a copy of the manifest will be created with the updated entry and the hash of the new manifest will be returned |
|  |  | Locator | bzz:/&lt;manifest\_hash?&gt;/&lt;resource\_path?&gt;/&lt;encrypt?&gt; |
|  |  | Locator Parts | manifest hash - optional - an existing manifest address to update a resource included in the manifest. resource path - optional - which resource to update in the manifest. encrypt - optional flag to enable encryption |
|  |  | HTTP Codes | 200 |
|  |  | Responds with | a hash of a newly created manifest |
|  |  | Example |  |
|  | DELETE | Purpose | delete a resource from a manifest by unlinking it from the existing manifest |
|  |  | Locator | bzz:/&lt;domain&gt;/&lt;path&gt; |
|  |  | Locator Parts | domain part - mandatory - a valid ENS hash or a valid Swarm manifest hash. path part - mandatory - a path to the resource to be removed from the manifest |
|  |  | HTTP Codes | 200; 404; 500 |
|  |  | Responds with | the hash of the new manifest which does not have component `path` |
|  |  | Example |  |
| bzz-immutable | GET | Purpose | The same as the generic scheme but there is no ENS domain resolution. the domain part of the path needs to be a valid hash. This is also a read-only scheme but explicit in its integrity protection. A particular bzz-immutable url will always necessarily address the exact same fixed immutable content. |
|  |  | Locator | bzz-immutable:/&lt;hash&gt; |
|  |  | Locator Parts | hash part - a valid Swarm hash that points to a manifest |
|  |  | HTTP Codes | 200; 404; 500 |
|  |  | Responds with | the resolved content at the specified address with a valid content-type as stored in the manifest |
|  |  | Example | use bzz-hash to resolve the ens name into a hash then use it with immutable |
| bzz-raw | GET | Purpose | When responding to GET requests with the bzz-raw scheme swarm does not assume a manifest but just serves the asset addressed by the url directly. The `content_type` query parameter can be supplied to specify the mime type you are requesting otherwise content is served as an octet stream per default. |
|  |  | Locator | bzz-raw:/&lt;content\_hash&gt;?content\_type=&lt;mime&gt; |
|  |  | Locator Parts | content hash - mandatory - a valid Swarm content hash. content type - optional - the mime type to serve the content as. defaults to application/octet-stream |
|  |  | HTTP Codes | 200; 404; 500 |
|  |  | Responds with |  |
|  |  | Example | a pdf document \(not the manifest wrapping it\) resides at hash 6a182226… then the following url will properly serve it: GET [http://localhost:8500/bzz-raw:/6a18222637cafb4ce692fa11df886a03e6d5e63432c53cbf7846970aa3e6fdf5?content\_type=application/pdf](http://localhost:8500/bzz-raw:/6a18222637cafb4ce692fa11df886a03e6d5e63432c53cbf7846970aa3e6fdf5?content_type=application/pdf) |
|  | POST | Purpose |  |
|  |  | Locator |  |
|  |  | Locator Parts |  |
|  |  | HTTP Codes | 200; 404; 500 |
|  |  | Responds with |  |
|  |  | Example |  |
| bzz-list | GET | Purpose | Returns a list of all files contained in &lt;manifest&gt; under &lt;path&gt; grouped into common prefixes using `/` as a delimiter. If path is `/` - all files in manifest are returned. The response is a JSON-encoded object with `common_prefixes` string field and `entries` list field. |
|  |  | Locator | bzz-list:/&lt;domain&gt;/&lt;path&gt; |
|  |  | Locator Parts | domain part - mandatory - a valid ENS entry that points to a valid manifest hash or a valid manifest hash. path part - optional - path to look for inside the manifest |
|  |  | HTTP Codes | 200; 404; 500 |
|  |  | Responds with |  |
|  |  | Example |  |
| bzz-hash | GET | Purpose | responds with the hash value of the raw content - the same content returned by requests with bzz-raw scheme. Hash of the manifest is also the hash stored in ENS so bzz-hash can be used for ENS domain resolution |
|  |  | Locator | bzz-hash:/&lt;domain&gt; |
|  |  | Locator Parts | domain part - mandatory. a valid ENS name |
|  |  | HTTP Codes | 200; 404; 500 |
|  |  | Responds with | text/plain |
|  |  | Example |  |
| bzz-feed | GET | Purpose | Retrieve a Feed update |
|  |  | Locator | bzz-feed:/&lt;hash&gt;?user=&lt;user&gt;&topic=&lt;topic&gt;&name=&lt;name&gt;&time=&lt;t&gt; |
|  |  | Locator Parts | hash - optional - manifest hash of the Feed, otherwise user param required. user - optional - Ethereum address of the user who publishes the Feed. topic - optional - Feed topic, encoded as a hex string, default: 0. name - optional - subtopic that is combined with the topic, default: “”. time - optional - The last update before that time \(unix time\) will be looked up, default: most recent update. meta - optional - Just return the Feed metadata, default 0 \(false\). |
|  |  | HTTP Codes | 200; 400; 404; 500 |
|  |  | Responds with | The content stored in the requested Feed update |
|  |  | Example |  |
|  | GET | Purpose | Get Feed metadata, used to help publishing updates |
|  |  | Locator | bzz-feed:/&lt;hash&gt;?user=&lt;user&gt;&topic=&lt;topic&gt;&name=&lt;name&gt;&meta=1 |
|  |  | Locator Parts | hash - optional - manifest hash of the Feed, otherwise user param required. user - optional - Ethereum address of the account that owns the Feed. topic - optional - Feed topic, encoded as a hex string, default: 0. name - optional - subtopic that is combined with the topic, default: “”. meta - required - Return the Feed metadata instead of the Feed content. |
|  |  | HTTP Codes | 200; 400; 500 |
|  |  | Responds with | application/json |
|  |  | Example |  |
|  | POST | Purpose | Post an update to a Feed |
|  |  | Locator | bzz-feed:/&lt;hash&gt;?user=&lt;user&gt;&topic=&lt;topic&gt;&level=&lt;level&gt;&time=&lt;time&gt;&protocolVersion=&lt;ver&gt;&signature=&lt;sig&gt; |
|  |  | Locator Parts | hash - optional - manifest hash of the Feed, otherwise user param required. user - optional - Ethereum address of the account that owns the Feed. topic - optional - Feed topic, encoded as a hex string, default: 0. level - optional - suggested frequency level \(retreived above\). time - optional - suggested timestamp \(retrieved above\). protocolVersion - optional - Feed protocol version, default: current version. signature - required - Feed signature hex encoded |
|  |  | HTTP Codes | 200; 400; 500 |
|  |  | Responds with |  |
|  |  | Example |  |

#### 4.6.2. JavaScript

Swarm currently supports a Javascript API through a few packages:

**4.6.2.1. erebos**

[erebos](https://erebos.js.org/) is available through [NPM](https://www.npmjs.com/package/@erebos/swarm) by issuing the following command:

```text
npm install @erebos/swarm-browser # browser onlynpm install @erebos/swarm-node # node onlynpm install @erebos/swarm # universal
```

Note

Full documentation is available on the [documentation website](https://erebos.js.org/).

**4.6.2.2. swarm-js**

[swarm-js](https://github.com/MaiaVictor/swarm-js) is available through [NPM](https://www.npmjs.com/package/swarm-js) by issuing the following command:

```text
npm install swarm-js
```

Note

Full documentation is available on the [GitHub](https://github.com/MaiaVictor/swarm-js) page.

**4.6.2.3. swarmgw**

[swarmgw](https://github.com/axic/swarmgw) is available through [NPM](https://www.npmjs.com/package/swarmgw) by issuing the following command:

```text
npm install swarmgw
```

When installed globally, it can also be used directly from the CLI:

```text
npm install -g swarmgw
```

Note

Full documentation is available on the [GitHub](https://github.com/axic/swarmgw) page.

#### 4.6.3. RPC

Swarm exposes an IPC API under the `bzz` namespace.

**4.6.3.1. FUSE**

`swarmfs.mount(HASH|domain, mountpoint))`mounts Swarm contents represented by a Swarm hash or a ens domain name to the specified local directory. The local directory has to be writable and should be empty. Once this command is succesfull, you should see the contents in the local directory. The HASH is mounted in a rw mode, which means any change insie the directory will be automatically reflected in Swarm. Ex: if you copy a file from somewhere else in to mountpoint, it is equvivalent of using a `swarm up <file>` command.`swarmfs.unmount(mountpoint)`This command unmounts the HASH\|domain mounted in the specified mountpoint. If the device is busy, unmounting fails. In that case make sure you exit the process that is using the directory and try unmounting again.`swarmfs.listmounts()`For every active mount, this command display three things. The mountpoint, start HASH supplied and the latest HASH. Since the HASH is mounted in rw mode, when ever there is a change to the file system \(adding file, removing file etc\), a new HASH is computed. This hash is called the latest HASH.

**4.6.3.2. PSS**

`pss` methods are by default exposed via IPC. If websockets are activated on the node, they will also be available there _if_ the `pss` module is explicitly specified.

All parameters are hex-encoded bytes or strings unless otherwise noted.`pss.getPublicKey()`Retrieves the public key of the node, in hex format`pss.baseAddr()`Retrieves the Swarm overlay address of the node, in hex format`pss.stringToTopic(name)`Creates a deterministic 4 byte topic value from an input name, returned in hex format`pss.setPeerPublicKey(publickey, topic, address)`Register a peer’s public key. This is done once for every topic that will be used with the peer. Address can be anything from 0 to 32 bytes inclusive of the peer’s swarm address. The method has no return value.`pss.sendAsym(publickey, topic, message)`Encrypts the message using the provided public key, and signs it using the node’s private key. It then wraps it in an envelope containing the topic, and sends it to the network. The method has no return value.`pss.setSymmetricKey(symkey, topic, address, bool decryption)`Register a symmetric key shared with a peer. This is done once for every topic that will be used with the peer. Address can be anything from 0 to 32 bytes inclusive of the peer’s Swarm overlay address. If the fourth parameter is false, the key will not be added to the list of symmetric keys used for decryption attempts. The method returns an id used to reference the symmetric key in consecutive calls.`pss.sendSym(symkeyid, topic, message)`Encrypts the message using the provided symmetric key, wraps it in an envelope containing the topic, and sends it to the network. The method has no return value.`pss.GetSymmetricAddressHint(topic, symkeyid)`Return the Swarm address associated with the peer registered with the given symmetric key and topic combination. If a match is found it returns the address data in hex format.`pss.GetAsymmetricAddressHint(topic, publickey)`Return the Swarm address associated with the peer registered with the given asymmetric key and topic combination. If a match is found it returns the address data in hex format.[Next ](https://swarm-guide.readthedocs.io/en/latest/node_operator.html)[ Previous](https://swarm-guide.readthedocs.io/en/latest/swarm_specification.html)  



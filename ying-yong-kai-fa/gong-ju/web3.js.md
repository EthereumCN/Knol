# Web3.js

What is **Web3.js?**

Web3.js is an interface you use to interact with blockchain. It is a javascript library which can be used to interact with an Ethereum node from your web based DApp. Remember each node on the network contains a copy of the blockchain. When you want to call a function on a smart contract, you need to query one of these nodes and tell it the address of the smart contract and the function you want to call.

Ethereum nodes only speak a language called _**JSON-RPC**_, which isn’t very human-readable. Luckily, Web3.js hides these nasty queries and gives you a more familiar javascript interface for example like this:

```text
MyTeam.methods.createRandomPerson("Vitalik Nakamoto").send({ from: "0xb60e8dd61c5d32be8058bb8eb970870f07233155", gas: "3000000" })
```

**First get started by including Web3 minified javascript in your project:** You can add Web3.js to your project using e.g `npm install web3.` Or you can simply download the minified `.js` file from [github](https://github.com/ethereum/web3.js/blob/1.0/dist/web3.min.js) and include it in your project:

`<script language="javascript" type="text/javascript" src="web3.min.js"></script>`

**Next get it it talking to the Blockchain using a Web3 Provider :**

> Setting a Web3 Provider in Web3.js tells our code **which node** we should be talking to handle our reads and writes.It’s kind of like setting the URL of the remote web server for your API calls in a traditional web app.

You could host your own Ethereum node as a provider. However, there’s a 3rd-party service that makes your life easier so you don’t need to maintain your own Ethereum node — _**Infura**_.


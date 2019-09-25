# Inter-exchange Client Address Protocol

## Inter exchange Client Address Protocol \(ICAP\)

Milo Chen edited this page on 13 Apr · [4 revisions](https://github.com/ethereum/wiki/wiki/Inter-exchange-Client-Address-Protocol-%28ICAP%29/_history)

**Contents**

```text
- [IBAN](#iban)
```

* [Proposed Design](https://github.com/ethereum/wiki/wiki/Inter-exchange-Client-Address-Protocol-%28ICAP%29#proposed-design) - [Direct](https://github.com/ethereum/wiki/wiki/Inter-exchange-Client-Address-Protocol-%28ICAP%29#direct) - [Basic](https://github.com/ethereum/wiki/wiki/Inter-exchange-Client-Address-Protocol-%28ICAP%29#basic) - [Indirect](https://github.com/ethereum/wiki/wiki/Inter-exchange-Client-Address-Protocol-%28ICAP%29#indirect)
  * [Notes](https://github.com/ethereum/wiki/wiki/Inter-exchange-Client-Address-Protocol-%28ICAP%29#notes)
  * [Other forms](https://github.com/ethereum/wiki/wiki/Inter-exchange-Client-Address-Protocol-%28ICAP%29#other-forms)
    * [URI](https://github.com/ethereum/wiki/wiki/Inter-exchange-Client-Address-Protocol-%28ICAP%29#uri)
    * [QR Code](https://github.com/ethereum/wiki/wiki/Inter-exchange-Client-Address-Protocol-%28ICAP%29#qr-code)
* [Transaction Semantics](https://github.com/ethereum/wiki/wiki/Inter-exchange-Client-Address-Protocol-%28ICAP%29#transaction-semantics)
  * [Direct](https://github.com/ethereum/wiki/wiki/Inter-exchange-Client-Address-Protocol-%28ICAP%29#direct-1)
  * [Indirect ETH Asset: Simple transfers](https://github.com/ethereum/wiki/wiki/Inter-exchange-Client-Address-Protocol-%28ICAP%29#indirect-eth-asset-simple-transfers)
  * [Indirect XET Asset: Institution transfers](https://github.com/ethereum/wiki/wiki/Inter-exchange-Client-Address-Protocol-%28ICAP%29#indirect-xet-asset-institution-transfers)

Transferring funds between third-party accounts, especially those of exchanges, places considerable burden on the user and is error prone, due to the way in which deposits are identified to the client account. This problem was tackled by the existing banking industry through having a common code known as _IBAN_. This code amalgamated the institution and client account along with a error-detection mechanism practically eliminating trivial errors and providing considerable convenience for the user. Unfortunately, this is a heavily regulated and centralised service accessible only to large, well-established institutions. The present protocol, ICAP, may be viewed as a decentralised version of it suitable for any institutions containing funds on the Ethereum system.

#### IBAN

For a good overview of the IBAN system, please see [Wikipedia's IBAN article](https://en.wikipedia.org/wiki/International_Bank_Account_Number). An IBAN code consists of up to 34 case insensitive alpha-numeric characters. It contains three pieces of information:

* The country code; a top-level identifier for the context of the following \(ISO 3166-1 alpha-2\);
* The error-detection code; uses the _mod-97-10_ checksumming protocol \(ISO/IEC 7064:2003\);
* The basic bank account number \(BBAN\); an identifier of the institution, branch and client account, whose composition is dependent on the aforementioned country.

For the UK, the BBAN is composed of:

* Institution identifier, 4-character alphabetical, e.g. `MIDL` \(ironically\) represents HSBC bank.
* Sort-code \(branch identifier within the institution\), a 6-digit decimal number, e.g. `402702` would be the Lancaster branch of HSBC.
* Account number \(client identifier within the branch\), an 8-digit decimal number.

## Proposed Design

Introduce a new IBAN country code: _XE_, formulated as the Ethereum _E_ prefixed with the "extended" _X_, as used in non-jurisdictional currencies \(e.g. XRP, XCP\).

There will be three BBAN possibilities for this code; _direct_, _basic_ and _indirect_.

**Direct**

The BBAN for this code when direct will be 30 characters and will comprise one field:

* Account identifier, 30 characters alphanumeric \(&lt; 156-bit\). This will be interpreted as a big-endian encoded base-36 integer representing the least significant bits of a 155-bit Ethereum address. As such, these Ethereum addresses will generally begin with first byte 00000xxx.

e.g. XE7338O073KYGTWWZN0F2WZ0R8PX5ZPPZS corresponds to the address `0x00c5496aee77c1ba1f0854206a26dda82a81d6d8`.

**Basic**

\*\*

\*\* The same as the direct encoding, except that the code is 31 characters \(making it non-compliant for IBAN\) and composes the same, single, field:

* Account identifier, 31 characters alphanumeric \(&lt; 161-bit\). This will be interpreted as a big-endian encoded base-36 integer representing a 160-bit Ethereum address.

**Indirect**

The BBAN for this code when indirect will be 16 characters and will comprise three fields:

* Asset identifier, 3-character alphanumeric \(&lt; 16-bit\);
* Institution identifier, 4-character alphanumeric \(&lt; 21-bit\);
* Institution client identifier, 9-character alphanumeric \(&lt; 47-bit\);

Including the four initial characters, this leads to a final client-account address length of 20 characters, of the form:

```text
XE81ETHXREGGAVOFYORK
```

Split into:

* `XE` The country code for Ethereum;
* `81` The checksum;
* `ETH` The asset identifier within the client account - in this case, "ETH" is the only valid asset identifier, since Ethereum's base registry contract supports only this asset;
* `XREG` The institution code for the account - in this case, Ethereum's base registry contract;
* `GAVOFYORK` The client identifier within the institution - in this case, a direct payment with no additional data to whatever primary address is associated with the name "GAVOFYORK" in Ethereum's base registry contract;

### Notes

Institution codes beginning with `X` are reserved for system use.

### Other forms

#### URI

General URIs can be formed though the URI scheme name `iban`, followed by the colon character `:`, followed by the 20-character alphanumeric identifier, thus for the example above, we would use:

```text
iban:XE81ETHXREGGAVOFYORK
```

#### QR Code

A QR code may be generated directly from the URI using standard QR encodings. For example, the example above `iban:XE81ETHXREGGAVOFYORK` would have the corresponding QR code:

![QR code for iban:XE81ETHXREGGAVOFYORK](https://camo.githubusercontent.com/17390df3301d4aa0a594bbcc425ea02dc0486f36/687474703a2f2f6f70656e736563726563792e636f6d2f71722d58453831455448585245474741564f46594f524b2e676966)

## Transaction Semantics

The mechanism for indirect asset transfer over three routing protocols are specified, all of which are specific to the Ethereum domain \(country-code of `XE`\). One is for currency transfers directly to an included address \("direct"\), another is for clients with the system address found through a Registry-lookup system of the client-ID, denoted by asset class `ETH`, whereas the last is for transfers to an intermediary with associated data to specify client, denoted by asset class `XET` \(the latter two are "indirect"\).

### Direct

If the IBAN code is 34 characters, it is a direct address; a direct transfer is made to the address which, when base-36 encoded gives exactly the data segment \(the last 30 characters\) of the IBAN code.

### Indirect ETH Asset: Simple transfers

Within the ETH asset code of Ethereum's country-code \(XE\), i.e. as long as the code begins with `XE**ETH` \(where `**` is the valid checksum\), then we can define the required transaction to be the deposit address given by a call to the _registry contract_ denoted by the institution code. For institutions not beginning with `X`, this corresponds to the primary address associated with the _Ethereum standard name_:

\[institution code\] `/` \[client identifier\]

The _Ethereum standard name_ is simply the normal hierarchical lookup mechanism, as specified in the Ethereum standard interfaces document.

We define a _registry contract_ as a contract fulfilling the Registry interface as specified in the Ethereum standard interfaces document.

**TODO**: JS code for specifying the transfer.

### Indirect XET Asset: Institution transfers

For the `XET` asset code within the Ethereum country code \(i.e. while the code begins XE\*\*XET\), then we can derive the transaction that must be made through a lookup to the Ethereum `iban` registry contract. For a given institution, this contract specifies two values: the deposit call signature hash and the institution's Ethereum address.

At present, only a single such deposit call is defined, which is:

```text
function deposit(uint64 clientAccount)
```

whose signature hash is `0x13765838`. The transaction to transfer the assets should be formed as an ether-laden call to the institution's Ethereum address using the `deposit` method as specified above, with the client account determined through the value of the big-endian, base-36 interpretation of the alpha-numeric _Institution client identifier_, literally using the value of the characters `0` to `9`, then evaluating 'A' \(or 'a'\) as 10, 'B' \(or 'b'\) as 11 and so forth.

**TODO**: JS code for specifying the transfer.`_**![](https://etherscan.io/address/0xc94770007dda54cf92009bff0de90c06f603a09f)**_`





[https://github.com/ethereum/wiki/wiki/Inter-exchange-Client-Address-Protocol-\(ICAP\)](https://github.com/ethereum/wiki/wiki/Inter-exchange-Client-Address-Protocol-%28ICAP%29)


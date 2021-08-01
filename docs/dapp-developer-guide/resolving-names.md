---
part: ENS 中文文档
title: ENS 域名解析 
---

ENS 域名空间包括 `.eth` 域名（ENS 原生域名）和 由 DNS 导入 ENS 的域名。由于 DNS 域名空间会随着时间的推移而增加，因此采用硬编码的域名后缀列表来识别 ENS 域名就会经常过时，导致应用程序不能识别所有有效的 ENS 域名。为了保证长期可用性，要正确集成 ENS，则需要将任何以点分隔的域名视为潜在的 ENS 域名，并将进行查询。

## 解析至加密货币的地址

ENS 域名可以关联至多种数据类型，最常见的是加密货币地址。ENS 支持存储和解析任意区块链的地址。

借助 ENS 库，**将域名解析为以太坊地址**很简单：

采用 **ensjs** 时：

```javascript
var address = await ens.name('resolver.eth').getAddress();
```

采用 **web3.js** 时：

```javascript
var address = ens.getAddress('alice.eth');
```

采用 **ethjs-ens** 时：

```javascript
var address = await ens.lookup('alice.eth');
```

采用 **ethers.js** 时：

```javascript
var address = await provider.resolveName('alice.eth');
```

ethers.js 还能支持在任何需要使用地址的地方也可以使用 ENS 域名，也就是说一般不需要直接调用 `resolveName` 。例如，要查询一个账户的余额，你可以这样做:

```javascript
var balance = await provider.getBalance('alice.eth');
```

或者，实例化一个合约:

```javascript
const abi = [
  "function getValue() view returns (string value)",
  "function setValue(string value)"
];
const contract = new ethers.Contract('contract.alice.eth', abi, provider);
```

采用 **go-ens** 时：

```go
address, err := ens.Resolve(client, "alice.eth")
```

采用 **web3.py** 时：

```text
address = ns.address('alice.eth')
```

采用 **web3j** 时：

```java
String address = ens.resolve("alice.eth");
```

web3j 同样支持在任何需要使用地址的地方也可以使用 ENS 域名，所以你通常不需要直接与 `EnsResolver` 对象交互。例如，要实例化一个合约接口，你可以这样做：

```java
YourSmartContract contract = YourSmartContract.load(
        "contract.alice.eth", web3j, credentials, GAS_PRICE, GAS_LIMIT);
```

如果不借助 ENS 库，解析的过程可以分为三步：

1. 对将要解析的域名进行规范化和哈希，详细信息请参阅 [域名处理](../contract-api-reference/name-processing.html)。
2. 在 ENS 注册表上调用 `resolver()` ，并将第 1 步输出的哈希作为参数传递，然后 `resolver()` 会返回负责解析这个域名的解析器的地址。
3. 使用 [resolver 接口](https://github.com/ensdomains/resolvers/blob/master/contracts/Resolver.sol) ，在第 2 步返回的解析器地址上调用 `addr()` ，并将第1步输出的哈希作为参数传递。

对其他区块链地址的支持是通过重载 `addr()` 来实现的。要解析非以太坊的地址，必须要有相应加密货币的 Namehash 和符合 [SLIP44](https://github.com/satoshilabs/slips/blob/master/slip-0044.html) 规范的链 ID 。例如，要解析一个比特币地址，可以调用 `addr(hash, 0)` 。注意，返回的地址是用二进制表示的，因此要通过解码来得到文本格式的地址，详细内容请参阅 [EIP2304](https://eips.ethereum.org/EIPS/eip-2304) 。

{% note warn %}
使用 `addr()` 进行解析时，必须将来自解析器 0x00…00 的返回值视为未设置的记录。否则，在用户配置了解析器却没有为域名设置解析地址的情况下，可能导致用户的资金被发送到空地址!
{% endnote %}

## 解析至其他资源

除了以太坊地址，ENS 还支持将域名解析至许多其他类型的资源，其中包括其他加密货币的地址、内容哈希（存储在 IPFS、Skynet、Swarm 或 Tor .onion 的内容的哈希）、合约接口（ABIs）和基于文本的元数据。查询这些信息的过程因采用的ENS库而有所差异，相关信息，请参阅所选库的文档。

如果不借助 ENS 库来实现这些类型的解析，同样需要遵循上面介绍过的3步过程，只是在第 3 步中调用的不再是 `addr()` ，而是解析器上与这些类型相对应的函数。

采用 **ensjs** 时：

```javascript
// Getting contenthash
await ens.name('abittooawesome.eth').getContent()
// Setting contenthash
await ens.name('abittooawesome.eth').setContenthash(contentHash)
// Getting other coins
await ens.name('brantly.eth').getAddress('BTC')
// Setting other coins
await ens.name('superawesome.eth').setAddress('ETC', '0x0000000000000000000000000000000000012345')
// Getting text
await ens.name('resolver.eth').getText('url')
// Setting text
await ens.name('superawesome.eth').setText('url', 'http://google.com')
```

采用 **web3.js** 时：

```javascript
// Getting contenthash
web3.eth.ens.getContenthash('ethereum.eth').then(function (result) {
    console.log(result);
});
// Setting contenthash
web3.eth.ens.setContenthash('ethereum.eth', hash);
```

采用 **ethjs-ens** 时：

```javascript
Not supported.
```

采用 **ethers.js** 时：

```javascript
  const contentHash = await resolver.getContentHash();
  const btcAddress = await resolver.getAddress(0);
  const dogeAddress = await resolver.getAddress(3);
  const email = await resolver.getText("email");
```

采用 **go-ens** 时：

```go
// Encoding
bin, err := ens.StringToContenthash("/ipfs/QmayQq2DWCkY3d4x3xKh4suohuRPEXe2fBqMBam5xtDj3t")
// Setting contenthash
resolver.SetContenthash(opts, data)
// Getting contenthash
resolver.Contenthash()
// Decoding
repr, err := ens.ContenthashToString(bin)
// Getting Multicoin
btcAddress, err := resolver.MultiAddress(0)
// Setting Multicoin
resolver.SetMultiAddress(opts, address)
// Setting text
resolver.SetText(opts, name, value)
// Getting text
resolver.Text(name)
```

采用 **web3.py** 时：

```text
Not supported.
```

采用 **web3j** 时：

```java
Not supported.
```

### contenthash（内容哈希）的编码/解码

`contenthash` 用于存储 IPFS 和 Swarm 内容哈希，我们可以将 ENS 地址解析为托管在这些分布式网络上的分布式内容（比如网站）。[content-hash](https://github.com/ensdomains/content-hash) javascript 库提供了一种方便的方式来对这些哈希进行编码/解码。

```javascript
 const contentHash = require('content-hash')
const encoded = 'e3010170122029f2d17be6139079dc48696d1f582a8530eb9805b561eda517e22a892c7e3f1f'
const content = contentHash.decode(encoded)
// 'QmRAQB6YaCyidP37UdDnjFY5vQuiBrcqdyoW1CuDgwxkD4'
const onion = 'zqktlwi4fecvo6ri'
contentHash.encode('onion', onion);
// 'bc037a716b746c776934666563766f367269'
const encoded = 'e40101701b20d1de9994b4d039f6548d191eb26786769f580809256b4685ef316805265ea162'
const codec = contentHash.getCodec(encoded) // 'swarm-ns'
codec === 'ipfs-ns' // false
```

对于 IPFS 需要注意：出于安全原因，IPFS 只能由编解码器 `libp2p-key` 来进行编码。使用其他格式进行编码后，在解码时会显示不可用的警告。更多细节请阅读[这里](https://github.com/ensdomains/content-hash/pull/5)。

### 币种的编码/解码

尽管有些库可以通过符号（例如：`BTC`）查询这类加密货币地址，但其他库却没有内置这类支持，因此必须通过每个币种的 id 进行调用（例如：`0` 代表 `BTC`、`16` 代表 `ETH`），对于 Javascript/Typescript，可以使用 [@ensdomains/address-encoder](https://github.com/ensdomains/address-encoder) 库来进行转换。

```javascript
import { formatsByName, formatsByCoinType } from '@ensdomains/address-encoder';
formatsByName['BTC']
// {
//   coinType: 0,
//   decoder: [Function (anonymous)],
//   encoder: [Function (anonymous)],
//   name: 'BTC'
// }
```

为了节省存储空间并避免用户设置错误的代币地址，该库具有编码 `encoder` 和解码 `decoder`。

```javascript
const data = formatsByName['BTC'].decoder('1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa');
console.log(data.toString('hex')); // 76a91462e907b15cbf27d5425399ebf6f0fb50ebb88f1888ac
const addr = formatsByCoinType[0].encoder(data);
console.log(addr); // 1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa
```

### 获取加密货币地址和文本记录

对于加密货币地址和文本记录，必须通过某个币种的键名来获取它的记录值。如果要获取用户已经设置的全部加密货币的地址和文本记录，则必须从事件中检索或者通过 [ENS 子图](https://thegraph.com/explorer/subgraph/ensdomains/ens)来查询。

例如：

```javascript
{
  domains(where:{name:"vitalik.eth"}) {
    id
    name
    resolver{
      texts
      coinTypes
    }
  }
}
```

会返回以下结果：

```json
{
  "data": {
    "domains": [
      {
        "id": "0xee6c4522aab0003e8d14cd40a6af439055fd2577951148c14b6cea9a53475835",
        "name": "vitalik.eth",
        "resolver": {
          "coinTypes": [
            60
          ],
          "texts": [
            "url"
          ]
        }
      }
    ]
  }
}
```

## 反向解析

“正向” 解析实现了从域名到地址的映射，而反向解析是指从地址映射回域名。ENS 支持反向解析，以便应用程序用 ENS 域名代替显示十六进制地址。

反向解析是通过专用域名 _addr.reverse_ 和解析器的 `name()` 函数实现的。 _addr.reverse_ 的所有权属于一个专用的注册器合约，该合约将子域名分配给相应地址的所有者。例如，地址 _0x314159265dd8dbb310642f98f50c066173c1259b_ 可以要求使用 _314159265dd8dbb310642f98f50c066173c1259b.addr.reverse_ ，并设置解析器和解析记录。通过这个解析器的 `name()` 函数可以取得与该地址关联的域名。

{% note warn %}
ENS 并不强制要求反向记录的准确性。例如，每个人都可以声明其地址的域名为 “alice.eth” 。所以，为了确保声明是准确的，你必须始终对返回的域名执行正向解析，并检查正向解析得到的地址是否与原始地址匹配。
{% endnote %}

大多数 ENS 库提供了执行反向解析的功能：

采用 **ensjs** 时：

```javascript
const address = '0x1234...';
var name = await ens.getName(address)
// Check to be sure the reverse record is correct.
if(address != await ens.name(name).getAddress()) {
  name = null;
}
```

采用 **web3.js** 时：

Not supported.

采用 **ethjs-ens** 时：

```javascript
var address = '0x1234...';
var name = await ens.reverse(address);
// Check to be sure the reverse record is correct.
if(address != await ens.lookup(name)) {
  name = null;
}
```

采用 **ethers.js** 时：

```text
var address = '0x1234...';
var name = await provider.lookupAddress(address);
// ethers.js automatically checks that the forward resolution matches.
```

采用 **go-ens** 时：

```go
name, err := ens.ReverseResolve(client, common.HexToAddress("0x1234...")
```

采用 **web3.py** 时：

```python
address = '0x1234...'
name = ns.reverse(address)
# Check to be sure the reverse record is correct.
if address != ns.address(name):
  name = None
```

采用 **web3j** 时：

```java
String address = "0x1234...";
String name = ens.reverseResolve(address);
// Check to be sure the reverse record is correct.
if(address != ens.resolve(name)) {
  name = null;
}
```

如果不使用库，实现反向解析的过程也是一样的：查询 `1234....addr.reverse`（其中的 _1234..._ 是需要进行反向解析的地址）的解析器并在该解析器上调用 `name()` 函数。然后，执行正向解析以验证记录是否准确。

<!-- lbb: 以下翻译待核实 -->
如果您需要处理许多地址（例如：展示交易历史的反向记录），要为每个域名同时设置反向和正向解析是不实际的。我们有一个单独的智能合约叫做 [`ReverseRecords`](https://github.com/ensdomains/reverse-records)，它可以让你通过一个函数来查询多个域名。

```js
const namehash = require('eth-ens-namehash');
const allnames = await ReverseRecords.getNames(['0x123','0x124'])
const validNames = allnames.filter((n) => namehash.normalize(n) === n )
```

为防止[同形词攻击](https://en.wikipedia.org/wiki/IDN_homograph_attack)，并避免人们仅仅使用大写字母，请确保将返回的域名与规范化域名进行比较。

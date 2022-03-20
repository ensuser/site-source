---
part: ENS 中文文档
subpart: ensip
title: 'ENSIP-7: 内容哈希字段'
description: 引入一个字段，用于在 ENS (原来的 EIP-1577) 中存储内容地址和哈希值。
---

| **作者**  | Dean Eigenmann <[dean@ens.domains](mailto:dean@ens.domains)>, Nick Johnson <[nick@ens.domains](mailto:nick@ens.domains)> |
| ----------- | --------------- |
| **状态**  | 完结     |
| **创建时间** | 2018-11-13   |

### 摘要

这个 ENSIP 为 ENS 解析器引入了新的 `contenthash` 字段，允许更好地定义名称到网络和内容地址的映射系统。此外， `content` 和 `multihash` 字段已弃用。

### 动机

多个应用程序，包括 [Metamask](https://metamask.io) 和一些类似 [Status](https://status.im) 的移动客户端，已经开始将 ENS 名称解析到托管在分布式系统上的内容，例如 [IPFS](https://ipfs.io) 和 [Swarm](https://swarm-guide.readthedocs.io)。由于存储和寻址内容的方式多种多样，因此需要一种标准，以便这些应用程序知道如何解析名称，并让名称所有者知道如何解析其内容。

`contenthash` 字段可以方便地规范 ENS 中的网络和内容地址。

### 规范

The field `contenthash` is introduced, which permits a wide range of protocols to be supported by ENS names. Resolvers supporting this field MUST return `true` when the `supportsInterface` function is called with argument `0xbc1c58d1`.

引入了字段 `contenthash`，它允许 ENS 名称支持广泛的协议。当使用参数 `0xbc1c58d1` 调用 `supportsInterface` 函数时，支持该字段的解析器必须返回 `true`。

字段 `content` 和 `multihash` 已弃用。

`contenthash` 返回的值必须表示为机器可读的 [multicodec](https://github.com/multiformats/multicodec)。该格式标准如下:

```
<protoCode uvarint><value []byte>
```

[multiformats/multicodec](https://github.com/multiformats/multicodec) 仓库中定义了 protoCodes 及其含义。

该值的编码取决于 protoCode 指定的内容类型。编码为 0xe3 和 0xe4 的值表示 IPFS 和 Swarm 内容，这些值被编码为 v1 [CIDs](https://github.com/multiformats/cid)，没有基前缀，这意味着它们的值被格式化如下:

```
<protoCode uvarint><cid-version><multicodec-content-type><multihash-content-address>
```

当解析 `contenthash` 时，应用程序必须使用协议代码来确定被编码的地址类型，并在支持的情况下为该协议解析正确的地址。

#### 示例

**IPFS**

输入数据:

```
storage system: IPFS (0xe3)
CID version: 1 (0x01)
content type: dag-pb (0x70)
hash function: sha2-256 (0x12)
hash length: 32 bytes (0x20)
hash: 29f2d17be6139079dc48696d1f582a8530eb9805b561eda517e22a892c7e3f1f
```

二进制格式:

```
0xe3010170122029f2d17be6139079dc48696d1f582a8530eb9805b561eda517e22a892c7e3f1f
```

文本格式:

```
ipfs://QmRAQB6YaCyidP37UdDnjFY5vQuiBrcqdyoW1CuDgwxkD4
```

#### Swarm

输入数据:

```
storage system: Swarm (0xe4)
CID version: 1 (0x01)
content type: swarm-manifest (0xfa)
hash function: keccak256 (0x1b)
hash length: 32 bytes (0x20)
hash: d1de9994b4d039f6548d191eb26786769f580809256b4685ef316805265ea162
```

二进制格式:

```
0xe40101fa011b20d1de9994b4d039f6548d191eb26786769f580809256b4685ef316805265ea162
```

文本格式:

```
bzz://d1de9994b4d039f6548d191eb26786769f580809256b4685ef316805265ea162
```

使用 swarm 哈希的示例:

```
$ swarm hash ens contenthash d1de9994b4d039f6548d191eb26786769f580809256b4685ef316805265ea162                                 
> e40101fa011b20d1de9994b4d039f6548d191eb26786769f580809256b4685ef316805265ea162
```

#### 回退

为了支持那些将 IPFS 或 Swarm hash 存储在 `content` 字段中的名称，必须设立一个宽限期，为那些名称的持有人提供时间来更新他们的名称。如果一个解析器不支持 `multihash` 接口，它必须检查是否支持 `content` 接口。如果支持，则该字段的值应以上下文相关的方式处理并解析。该状态必须保持到 2019 年 3 月 31日。

#### 实现

为了支持 `contenthash`，已经开发了一个新的解析器，可以在 [这里](https://github.com/ensdomains/resolvers/blob/master/contracts/PublicResolver.sol) 找到，你也可以在下面找到这个智能合约:

* 主网 : [0xd3ddccdd3b25a8a7423b5bee360a42146eb4baf3](https://etherscan.io/address/0xd3ddccdd3b25a8a7423b5bee360a42146eb4baf3)
* Ropsten : [0xde469c7106a9fbc3fb98912bb00be983a89bddca](https://ropsten.etherscan.io/address/0xde469c7106a9fbc3fb98912bb00be983a89bddca)

编码和解码 `contenthash` 已经通过多种语言来实现:

* [JavaScript](https://github.com/pldespaigne/content-hash)
* [Python](https://github.com/filips123/ContentHashPy)

### 版权

通过 [CC0](https://creativecommons.org/publicdomain/zero/1.0/) 放弃版权及相关权利。

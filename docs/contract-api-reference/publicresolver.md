---
part: ENS 中文文档
title: ENS 公共解析器
description: 默认的公共解析器。
---

[源代码](https://github.com/ensdomains/resolvers/blob/master/contracts/PublicResolver.sol)

公共解析器是一个通用的 ENS 解析器，它适用于大多数标准的 ENS 用例。名称的所有者可以使用公共解析器更新相应的 ENS 记录。

公共解析器遵循以下 EIP 提案:

* [EIP137](https://eips.ethereum.org/EIPS/eip-137) - Contract address interface \( `addr()` \).
* [EIP165](https://eips.ethereum.org/EIPS/eip-165)- Interface Detection \( `supportsInterface()` \).
* [EIP181](https://eips.ethereum.org/EIPS/eip-181) - Reverse resolution \( `name()` \).
* [EIP205](https://eips.ethereum.org/EIPS/eip-205) - ABI support \( `ABI()` \).
* [EIP619](https://github.com/ethereum/EIPs/pull/619) - SECP256k1 public keys \( `pubkey()` \).
* [EIP634](https://eips.ethereum.org/EIPS/eip-634) - Text news \( `text()` \).
* [EIP1577](https://eips.ethereum.org/EIPS/eip-1577) - Content hash support \( `contenthash()` \).

{% note warn %}
虽然公共解析器是一个方便的默认解析器，但仍然存在许多其他的解析器实例和版本。调用者**不能**假设名称使用的是公共解析器的最新版本，或是解析器包含了这里描述的所有方法。要检查一个解析器是否支持某个特性，请参见 [接口检查支持](publicresolver.html#接口检查支持) 。
{% endnote %}

## 接口检查支持

```text
function supportsInterface(bytes4 interfaceID) external pure returns (bool)
```

ENS 使用 [ERC165](https://eips.ethereum.org/EIPS/eip-165) 进行接口检测。ERC165 要求支持它的合约实现一个名为 `supportsInterface` 的函数，该函数接收一个接口 ID 并返回一个布尔值，这个布尔值表示是否支持该接口。

接口 ID 由包含在接口中的每个函数的 4 字节函数 ID 通过异或计算而来。例如，`addr(bytes32)` 的函数 ID 为 _0x3b3b57de_ ，因为它是以太坊地址接口中唯一的函数，所以它的接口 ID 也是 _0x3b3b57de_ ，因此调用 `supportsInterface(0x3b3b57de)` 将为任何支持 `addr()` 的解析器返回 _true_ 。

ERC165 的接口 ID 为 _0x01ffc9a7_ ，因此 `supportsInterface(0x01ffc9a7)` 对于任何支持 ERC165 的合约（也就是对于任何解析器）都将返回 true。

注意，公共解析器不公开设置属性值函数的显式接口，因此对于指定的设置属性值函数，目前没有办法对其进行自动检查。

## 获取以太坊地址

```text
function addr(bytes32 node) external view returns (address)
```

返回与给定的 `node` 关联的以太坊地址，如果没有则返回 0 。

这个函数的接口 ID 为 _0x3b3b57de_ 。

这个函数的详细信息请参阅 [EIP137](https://eips.ethereum.org/EIPS/eip-137) 。

## 设置以太坊地址

```text
function setAddr(bytes32 node, address addr) external;
```

将与给定的 `node` 关联的以太坊地址设置为 `addr` 。

只能由 `node` 的所有者调用。

该操作会触发以下事件：

```text
event AddrChanged(bytes32 indexed node, address a);
```

## 获取区块链地址

```text
function addr(bytes32 node, uint coinType) external view returns(bytes memory);
```

根据传入参数 `node` 和 `coinType` 返回相应区块链的地址，如果没有，则返回 0 。

这个函数的接口 ID 为 _0xf1cb7e06_ 。

这个函数的详细信息请参阅 [EIP 2304](https://eips.ethereum.org/EIPS/eip-2304) 。

返回值是加密货币地址的原生二进制格式，而且每种区块链地址都有不同的编码和解码方式。

例如，比特币地址 `1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa` 按照 base58check 算法解码为 21 字节的 `0062e907b15cbf27d5425399ebf6f0fb50ebb88f18` ，再通过 scriptPubkey 算法转换为 25 字节的 `76a91462e907b15cbf27d5425399ebf6f0fb50ebb88f1888ac` ；而 BNB 地址 `bnb1grpf0955h0ykzq3ar5nmum7y6gdfl6lxfn46h2` 按照 Bech32 算法转换为二进制表示得到 `40c2979694bbc961023d1d27be6fc4d21a9febe6` 。

要将二进制表示转换为地址，请使用 [address-encoder](https://github.com/ensdomains/address-encoder)（地址编码器）的 `formatsByCoinType[SYMBOL].encoder(binary)` 方法。

如果 `coinType` 在指定节点 `node` 上不存在，则返回零长度的字符串。

## 设置区块链地址

```text
function setAddr(bytes32 node, uint coinType, bytes calldata addr) external;
```

将与 `node` 和 `coinType` 相应的区块链地址设置为 `addr` 。

`coinType` 是加密货币类型索引，它由 [SLIP44](https://github.com/satoshilabs/slips/blob/master/slip-0044.md) 定义。

要将地址转换为二进制表示，请使用 [address-encoder](https://github.com/ensdomains/address-encoder)（地址编码器）的 `formatsByCoinType[SYMBOL].encoder(binary)` 方法。

只能由 `node` 的所有者调用。

该操作会触发以下事件：

```text
event AddressChanged(bytes32 indexed node, uint coinType, bytes newAddress);
```

## 获取规范名称

```text
function name(bytes32 node) external view returns (string memory);
```

返回与给定的 `node` 关联的规范 ENS 名称，专门用于反向解析。

这个函数的接口 ID 为 _0x691f3431_ 。

这个函数的详细信息请参阅 [EIP181](https://eips.ethereum.org/EIPS/eip-181) 。

## 设置规范名称

```text
function setName(bytes32 node, string calldata name) external;
```

为给定的 `node` 设置规范 ENS 名称 `name` 。

只能由 `node` 的所有者调用。

该操作会触发以下事件：

```text
event NameChanged(bytes32 indexed node, string name);
```

## 获取内容哈希

```text
function contenthash(bytes32 node) external view returns (bytes memory);
```

返回 `node` 的内容哈希（如果存在的话）。涉及的值会被格式化为机器可读的 [multicodecs](https://github.com/multiformats/multicodec) ，详细信息请参阅 [EIP1577](https://eips.ethereum.org/EIPS/eip-1577) 。

`contenthash` 用于存储 IPFS 和 Swarm 内容哈希，可以将 ENS 名称解析到托管在这些分布式网络上的内容（如网站）。

这个函数的接口 ID 为 _0xbc1c58d1_ 。

这个函数的详细信息请参阅 [EIP1577](https://eips.ethereum.org/EIPS/eip-1157) 。

## 设置内容哈希

```text
function setContenthash(bytes32 node, bytes calldata hash) external;
```

将给定 `node` 的内容哈希设置为 `hash` 。

只能由 `node` 的所有者调用。

涉及的值会被格式化为机器可读的 [multicodecs](https://github.com/multiformats/multicodec) ，详细信息请参阅 [EIP1577](https://eips.ethereum.org/EIPS/eip-1577) 。

该操作会触发以下事件：

```text
event ContenthashChanged(bytes32 indexed node, bytes hash);
```

## 获取合约 ABI

```text
ABI(bytes32 node, uint256 contentTypes) external view returns (uint256, bytes memory);
```

返回与给定 `node` 匹配的 ABI 定义（如果存在的话）。`contentTypes` 是调用者可以接受的二进制编码。如果指定了多个内容类型，解析器将选择一个返回。目前支持的内容类型有：

| 内容类型 ID | 描述 |
| :--- | :--- |
| 1 | JSON |
| 2 | zlib 压缩的 JSON |
| 4 | [CBOR](https://cbor.io/) |
| 8 | URI |

`ABI` 返回内容类型 ID 和 ABI 数据的二元组。如果没有找到合适的内容类型 ID 的数据，则内容类型 ID 返回 0，ABI 数据将是空字符串。

这个函数的接口 ID 为 _0x2203ab56_ 。

这个函数的详细信息请参阅 [EIP205](https://eips.ethereum.org/EIPS/eip-205) 。

## 设置合约 ABI

```text
function setABI(bytes32 node, uint256 contentType, bytes calldata data) external
```

为 `node` 设置或更新 ABI 数据。`contentType` 是给定的内容类型 ID ，而且必须给定一个类型 ID ；`data` 包含经过编码的 ABI 数据。如果要清除名称的 ABI 数据，请将 `data` 设置为空字符串。

只能由 `node` 的所有者调用。

该操作会触发以下事件：

```text
event ABIChanged(bytes32 indexed node, uint256 indexed contentType);
```

## 获取公钥

```text
function pubkey(bytes32 node) external view returns (bytes32 x, bytes32 y)
```

以二元组 `(x, y)` 的形式返回 `node` 的 ECDSA SECP256k1 公钥。如果没有设置公钥，则返回 `(0, 0)` 。

这个函数的接口 ID 为 _0xc8690233_ 。

这个函数的详细信息请参阅 [EIP619](https://github.com/ethereum/EIPs/issues/619) 。

## 设置公钥

```text
function setPubkey(bytes32 node, bytes32 x, bytes32 y) external
```

将 `node` 的 ECDSA SECP256k1 公钥设置为 `(x, y)` 。

只能由 `node` 的所有者调用。

该操作会触发以下事件：

```text
event PubkeyChanged(bytes32 indexed node, bytes32 x, bytes32 y);
```

## 获取文本数据

```text
function text(bytes32 node, string calldata key) external view returns (string memory)
```

检索 `node` 的文本元数据。每个名称可能有多个元数据片段，每个片段由一个唯一的键值 `key` 标识。如果 `node` 中由键值 `key` 标识的文本数据不存在，则返回空字符串。

`key` 可选的标准值有：

| 键值 | 含义 |
| :--- | :--- |
| email | 电子邮箱地址 |
| url | 网址（URL） |
| avatar | 用作头像或标识的图像的网址 |
| description | 名称的描述信息 |
| notice | 关于名称的通知 |
| keywords | 逗号分隔的关键字列表，按重要性由高到低排列，与此字段有交互的客户端可以通过设置一个阈值来选择忽略哪些内容 |

此外，任何人都可以指定特定于服务提供商的键值，这些键值必须以 `com.` 作为前缀。目前已知的特定于服务提供商的键值如下:

| 键值 | 含义 |
| :--- | :--- |
| com.twitter | Twitter 标识符 |
| com.github | Github 标识符 |

这个函数的接口 ID 为 _0x59d1d43c_ 。

这个函数的详细信息请参阅 [EIP634](https://eips.ethereum.org/EIPS/eip-634) 。

## 设置文本数据

```text
function setText(bytes32 node, string calldata key, string calldata value) external
```

将 `node` 中由唯一键值 `key` 标识的文本元数据设置为 `value` ，同时会覆盖掉之前 `node` 中由 `key` 标识存储的所有内容。如果要清除文本字段，请将其设置为空字符串。

只能由 `node` 的所有者调用。

该操作会触发以下事件：

```text
event TextChanged(bytes32 indexed node, string indexedKey, string key);
```

## 多重调用

```text
function multicall(bytes[] calldata data) external returns(bytes[] memory results)
```

允许用户在一次操作中设置多条记录。

使用 `encodeABI` 函数对合约调用进行编码，并将其传递给 `data`。

前端使用情况如下:

```javascript
var addrSet = resolver.contract.methods['setAddr(bytes32,address)'](node, accounts[1]).encodeABI();
var textSet = resolver.contract.methods.setText(node, "url", "https://ethereum.org/").encodeABI();
var tx = await resolver.multicall([addrSet, textSet], {from: accounts[0]});
```

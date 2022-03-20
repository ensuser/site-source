---
part: ENS 中文文档
subpart: ensip
title: 'ENSIP-9: 多链地址解析'
description: 为 ENS 解析器的 `addr` 字段引入了新的重载，它允许将 ENS 解析至其他区块链的地址 (原来的 EIP-2304)。
---

| **作者**    | Nick Johnson \<nick@ens.domains> |
| ------------- | -------------------------------- |
| **状态**    | 完结                            |
| **提交时间** | 2019-09-09                       |

### 动机

随着多币钱包越来越多地使用 ENS，钱包开发者开始要求能够在 ENS 中解析非以太坊链的地址，本规范标准化了以跨客户端方式输入和检索这些地址的方法。

### 规范

为解析器指定了一个新的访问器函数:

```solidity
function addr(bytes32 node, uint coinType) external view returns(bytes memory);
```

此函数的 EIP-165 接口 ID 为 0xf1cb7e06。

在解析器上调用时，此函数必须按照指定的 namehash 和代币类型来返回相应的加密货币地址。如果指定的代币 ID 在指定的节点上不存在，则必须返回一个零长度的字符串。

`coinType` 是由 [SLIP44](https://github.com/satoshilabs/slips/blob/master/slip-0044.md) 索引的的加密货币类型。

返回值是原始二进制格式的加密货币地址。下面的地址编码一节详细描述了几种常用链的二进制编码。

为解析器定义了一个新的事件:

```solidity
event AddressChanged(bytes32 indexed node, uint coinType, bytes newAddress);
```

解析器必须在每次更改名称和代币类型的地址时触发此事件。

#### 访问器功能建议

下面介绍修改某个节点对应地址的推荐接口。解析器应该实现这个接口来设置地址，除非它们需要指定一个不同的接口。

```solidity
function setAddr(bytes32 node, uint coinType, bytes calldata addr);
```

`setAddr` 添加或替换指定节点和代币类型的地址。这个函数的参数与上面 `addr()` 中描述的一样。

这个函数触发一个带有新地址的 `AddressChanged` 事件，请参见下面的“向后兼容”部分中同时支持 `addr(bytes32)` 的解析器。

#### 地址编码

一般来说，应该使用地址的原生二进制表示，而不是通常在文本表达式中使用的校验和。

本文提供了一个常见区块链的编码表，然后对每种格式进行了更详细的描述。表中的“编码”列出了该链支持的地址编码，以及相关的参数。这些地址编码的详细信息紧随其后。

| 币名   | 代币类型 | 编码                                           |
| ---------------- | --------- | -------------------------------------------------- |
| Bitcoin          | 0         | P2PKH(0x00), P2SH(0x05), SegWit('bc')              |
| Litecoin         | 2         | P2PKH(0x30), P2SH(0x32), P2SH(0x05), SegWit('ltc') |
| Dogecoin         | 3         | P2PKH(0x1e), P2SH(0x16)                            |
| Monacoin         | 22        | P2PKH(0x32), P2SH(0x05)                            |
| Ethereum         | 60        | ChecksummedHex                                     |
| Ethereum Classic | 61        | ChecksummedHex                                     |
| Rootstock        | 137       | ChecksummedHex(30)                                 |
| Ripple           | 144       | Ripple                                             |
| Bitcoin Cash     | 145       | P2PKH(0x00), P2SH(0x05), CashAddr                  |
| Binance          | 714       | Bech32('bnb')                                      |

**P2PKH(版本)**

用于支付的公钥哈希地址是经过 [base58check](https://en.bitcoin.it/wiki/Base58Check\_encoding) 编码的。解码后，第一个字节是版本字节。例如，比特币地址 `1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa` 经过 base58check 解码为 21 字节的 `0062e907b15cbf27d5425399ebf6f0fb50ebb88f18`。

P2PKH 地址以一个版本字节开始，后面跟着 20 字节的公钥哈希。它们的规范编码是 scriptPubkey 编码，(参见 [这里](https://en.bitcoin.it/wiki/Transaction#Types\_of\_Transaction)) 是 `OP_DUP OP_HASH160 <pubKeyHash> OP_EQUALVERIFY OP_CHECKSIG`。

因此，上面的示例地址被编码为 25 字节的 `76a91462e907b15cbf27d5425399ebf6f0fb50ebb88f1888ac`。

**P2SH(版本)**

P2SH 地址采用和 P2PKH 地址相同的方式进行 base58check 编码。P2SH 地址有一个版本字节，后面跟着 20 字节的哈希值。它们的 scriptPubkey 编码 (参见 [这里](https://en.bitcoin.it/wiki/Transaction#Pay-to-Script-Hash)) 是 `OP_HASH160 <scriptHash> OP_EQUAL`。比特币地址 `3Ai1JZ8pdJb2ksieUV8FsxSNVJCpoPi8W6` 解码为 21 字节的 `0562e907b15cbf27d5425399ebf6f0fb50ebb88f18` 并被编码为 23 字节的 `a91462e907b15cbf27d5425399ebf6f0fb50ebb88f1887`。

**SegWit(hrp)**

SegWit 地址使用 [bech32](https://github.com/bitcoin/bips/blob/master/bip-0173.mediawiki) 进行编码。Bech32 地址包括一个人类可读的部分 (代表比特币主网的“bc”) 和一个机器可读的部分。对于 SegWit 地址，会按照 [BIP141](https://github.com/bitcoin/bips/blob/master/bip-0173.mediawiki) 定义，解码为一个 0 到 15 之间的 “见证版本”，和一个“见证程序”。

由 BIP141 定义的 bech32 地址的 scriptPubkey 编码，是 `OP_n`，其中 `n` 是见证版本，后面是见证程序的 push 指令。注意BIP173 的警告:

> 实现在将地址转换为 scriptPubkey 时应该特别注意，其中见证版本 n 存储为OP\_n。OP\_0 被编码为 0x00，但是 OP\_1 到 OP\_16 被编码为 0x51 到 0x60 (十进制为 81 到 96)。如果 bech32 地址被转换为不正确的 scriptPubKey，结果可能是不可使用的或不安全的。

例如，比特币 SegWit 地址 `BC1QW508D6QEJXTDG4Y5R3ZARVARY0C5XW7KV8F3T4` 解码为版本 `0` 和见证脚本 `751e76e8199196d454941c45d1b3a323f1433bd6`，然后编码为 scriptPubkey `0014751e76e8199196d454941c45d1b3a323f1433bd6`。

**ChecksummedHex(chainId?)**

要将文本格式的校验和十六进制地址转换为二进制格式，只需删除 '0x' 前缀并将其进行十六进制解码。`0x314159265dD8dbb310642f98f50C066173C1259b` 被十六进制解码并存储为 20 字节的 `314159265dd8dbb310642f98f50c066173c1259b`。

校验和格式由 EIP-55 指定，并经过 [RSKIP60](https://github.com/rsksmart/RSKIPs/blob/master/IPs/RSKIP60.md) 扩展，它规定了在校验和中包含链 ID 的方法。要求必须检查文本格式地址上的校验和。如果地址的校验和不是全部大写或全部小写，则必须拒绝该地址并报错。相关实现可以选择是否接受非校验和地址，但其作者建议在这种情况下至少向用户提供警告。

当将一个地址从二进制编码为文本时，必须使用 EIP55/RSKIP60 校验和，所以上述地址的正确编码为 `0x314159265dD8dbb310642f98f50C066173C1259b`。

**Ripple**

Ripple 地址使用 base58check 的一个版本进行编码，这个版本采用另一种字母表，在[这里](https://xrpl.org/base58-encodings.html)有相关介绍。它支持两种类型的 ripple 地址，'r-addresses' 和 'X-addresss'。r-addresses 由一个版本字节后跟 20 个字节的哈希值组成，而 X-addresses 由一个版本字节、20 个字节的哈希值和一个标记组成，参见[这里](https://github.com/xrp-community/standards-drafts/issues/6)。

这两种地址类型都应该通过执行 ripple 版本的 base58check 解码并直接存储在 ENS 中(包括版本字节)。例如，ripple 地址 `rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn` 解码并存储为 `004b4e9c06f24296074f7bc48f92a97916c6dc5ea9`，而地址 `X7qvLs7gSnNoKvZzNWUT2e8st17QPY64PPe7zriLNuJszeg` 解码并存储为 `05444b4e9c06f24296074f7bc48f92a97916c6dc5ea9000000000000000000`。

**CashAddr**

比特币现金定义了一种名为 'CashAddr' 的新地址格式，参见[这里](https://github.com/bitcoincashorg/bitcoincash.org/blob/master/spec/cashaddr.md)。它使用 bech32 编码的变体来编码和解码 (非 segwit) 比特币现金地址，使用前缀'bitcoincash:'。CashAddr 应该使用这种 bech32 变体进行解码，然后根据其类型 (P2PKH 或 P2SH) 进行转换和存储，如上面的相关部分所述。

**Bech32**

[Bech32](https://github.com/bitcoin/bips/blob/master/bip-0173.mediawiki) 地址包括一个人类可读的部分 (例如，币安的 'bnb') 和一个机器可读的部分。编码后的数据只是地址，可以转换成二进制并直接存储。

例如 BNB 地址 `bnb1grpf0955h0ykzq3ar5nmum7y6gdfl6lxfn46h2` 解码为二进制表示形式 `40c2979694bbc961023d1d27be6fc4d21a9febe6`，直接存储在 ENS 中。

#### 示例

下面是一个支持这个 ENSIP 的解析器的实现示例:

```solidity
pragma solidity ^0.5.8;

contract AddrResolver is ResolverBase {
    bytes4 constant private ADDR_INTERFACE_ID = 0x3b3b57de;
    bytes4 constant private ADDRESS_INTERFACE_ID = 0xf1cb7e06;
    uint constant private COIN_TYPE_ETH = 60;

    event AddrChanged(bytes32 indexed node, address a);
    event AddressChanged(bytes32 indexed node, uint coinType, bytes newAddress);

    mapping(bytes32=>mapping(uint=>bytes)) _addresses;

    /**
     * Sets the address associated with an ENS node.
     * May only be called by the owner of that node in the ENS registry.
     * @param node The node to update.
     * @param a The address to set.
     */
    function setAddr(bytes32 node, address a) external authorised(node) {
        setAddr(node, COIN_TYPE_ETH, addressToBytes(a));
    }

    /**
     * Returns the address associated with an ENS node.
     * @param node The ENS node to query.
     * @return The associated address.
     */
    function addr(bytes32 node) public view returns (address) {
        bytes memory a = addr(node, COIN_TYPE_ETH);
        if(a.length == 0) {
            return address(0);
        }
        return bytesToAddress(a);
    }

    function setAddr(bytes32 node, uint coinType, bytes memory a) public authorised(node) {
        emit AddressChanged(node, coinType, a);
        if(coinType == COIN_TYPE_ETH) {
            emit AddrChanged(node, bytesToAddress(a));
        }
        _addresses[node][coinType] = a;
    }

    function addr(bytes32 node, uint coinType) public view returns(bytes memory) {
        return _addresses[node][coinType];
    }

    function supportsInterface(bytes4 interfaceID) public pure returns(bool) {
        return interfaceID == ADDR_INTERFACE_ID || interfaceID == ADDRESS_INTERFACE_ID || super.supportsInterface(interfaceID);
    }
}
```

#### 实现

[ensdomains/resolvers](https://github.com/ensdomains/resolvers/) 仓库提供了该接口的实现。

### 向后兼容性

如果解析器支持 ENSIP-1 中定义的 `addr(bytes32)` 接口，那它必须按照以下方式将其作为新规范的特殊情况处理:

1. ENSIP-1 中 `addr(node)` 返回的值应该始终匹配 `addr(node, 60)` 返回的值 (60 是以太坊的代币类型 ID)。
2. 任何触发 ENSIP-1 的 `AddressChanged` 事件也必须触发本 ENSIP 中的 `AddressChanged` 事件，其中 `coinType` 指定为 60，反之亦然。

### 测试

下表为上述每种加密货币的有效地址编码指定了测试向量。

| 币名  | 代币类型 | 文本  | 链上地址 (十六进制)  |
| -------- | ------ | ------- | ---------------- |
| Bitcoin          | 0         | `1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa`                     | `76a91462e907b15cbf27d5425399ebf6f0fb50ebb88f1888ac`             |
|                  |           | `3Ai1JZ8pdJb2ksieUV8FsxSNVJCpoPi8W6`                     | `a91462e907b15cbf27d5425399ebf6f0fb50ebb88f1887`                 |
|                  |           | `BC1QW508D6QEJXTDG4Y5R3ZARVARY0C5XW7KV8F3T4`             | `0014751e76e8199196d454941c45d1b3a323f1433bd6`                   |
| Litecoin         | 2         | `LaMT348PWRnrqeeWArpwQPbuanpXDZGEUz`                     | `76a914a5f4d12ce3685781b227c1f39548ddef429e978388ac`             |
|                  |           | `MQMcJhpWHYVeQArcZR3sBgyPZxxRtnH441`                     | `a914b48297bff5dadecc5f36145cec6a5f20d57c8f9b87`                 |
|                  |           | `ltc1qdp7p2rpx4a2f80h7a4crvppczgg4egmv5c78w8`            | `0014687c150c26af5493befeed7036043812115ca36c`                   |
| Dogecoin         | 3         | `DBXu2kgc3xtvCUWFcxFE3r9hEYgmuaaCyD`                     | `76a9144620b70031f0e9437e374a2100934fba4911046088ac`             |
|                  |           | `AF8ekvSf6eiSBRspJjnfzK6d1EM6pnPq3G`                     | `a914f8f5d99a9fc21aa676e74d15e7b8134557615bda87`                 |
| Monacoin         | 22        | `MHxgS2XMXjeJ4if2PRRbWYcdwZPWfdwaDT`                     | `76a9146e5bb7226a337fe8307b4192ae5c3fab9fa9edf588ac`             |
| Ethereum         | 60        | `0x314159265dD8dbb310642f98f50C066173C1259b`             | `314159265dd8dbb310642f98f50c066173c1259b`                       |
| Ethereum Classic | 61        | `0x314159265dD8dbb310642f98f50C066173C1259b`             | `314159265dd8dbb310642f98f50c066173c1259b`                       |
| Rootstock        | 137       | `0x5aaEB6053f3e94c9b9a09f33669435E7ef1bEAeD`             | `5aaeb6053f3e94c9b9a09f33669435e7ef1beaed`                       |
| Ripple           | 144       | `rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn`                     | `004b4e9c06f24296074f7bc48f92a97916c6dc5ea9`                     |
|                  |           | `X7qvLs7gSnNoKvZzNWUT2e8st17QPY64PPe7zriLNuJszeg`        | `05444b4e9c06f24296074f7bc48f92a97916c6dc5ea9000000000000000000` |
| Bitcoin Cash     | 145       | `1BpEi6DfDAUFd7GtittLSdBeYJvcoaVggu`                     | `76a91476a04053bda0a88bda5177b86a15c3b29f55987388ac`             |
|                  |           | `bitcoincash:qpm2qsznhks23z7629mms6s4cwef74vcwvy22gdx6a` | `76a91476a04053bda0a88bda5177b86a15c3b29f55987388ac`             |
|                  |           | `3CWFddi6m4ndiGyKqzYvsFYagqDLPVMTzC`                     | `a91476a04053bda0a88bda5177b86a15c3b29f55987387`                 |
|                  |           | `bitcoincash:ppm2qsznhks23z7629mms6s4cwef74vcwvn0h829pq` | `a91476a04053bda0a88bda5177b86a15c3b29f55987387`                 |
| Binance          | 714       | `bnb1grpf0955h0ykzq3ar5nmum7y6gdfl6lxfn46h2`             | `40c2979694bbc961023d1d27be6fc4d21a9febe6`                       |

### 版权

通过 [CC0](https://creativecommons.org/publicdomain/zero/1.0/) 放弃版权及相关权利。

---
part: ENS 中文文档
subpart: ensip
title: 'ENSIP-1: ENS'
description: Documentation of the basic ENS protocol (formerly EIP-137).
---

| **作者**  | Nick Johnson \<arachnid@notdot.net> |
| ----------- | ----------------------------------- |
| **状态**  | 完结 |
| **创建时间** | 2016-04-04 |

## 摘要

这个 ENSIP 描述了以太坊名称服务（Ethereum Name Service）的细节，是一个包含协议及 ABI 定义的提案，它支持将简短的、人类可读的名称灵活地解析至服务和资源标识。用户和开发人员可以引用易读且容易记忆的名称，并允许在底层资源(合约、内容寻址数据等)发生变化时，根据需要更新这些名称。

名称的目标是提供稳定的、人类可读的标识符，用来指向网络资源。通过这种方式，用户可以输入一个容易记住的字符串，例如 `vitalik.wallet` 或 `www.mysite.swarm`，并定向到正确的资源。名称和资源之间的映射可能会随着时间的推移而改变，所以用户可能会更改钱包，网站可能会更改主机，文档可能会更新到新版本，而名称不会改变。此外，名称不一定指定单一的资源，通过不同的记录类型允许相同的名称引用不同的资源。例如，浏览器可以通过获取 A（地址）记录将 `mysite.swarm` 解析至它的服务器 IP 地址，同时邮件客户端可以通过获取邮件服务器的 MX（邮件交换器）记录来将相同的名称解析至邮件服务器。

## 动机

以太坊中现有的名称解析[规范](https://github.com/ethereum/wiki/wiki/Registrar-ABI)和[实现](https://ethereum.gitbooks.io/frontier-guide/content/registrar\_services.html)能够提供基本功能，但存在一些缺陷，会在很大程序上限制其长期可用性: 

* 一个全局命名空间，所有名称都有一个 “集中式” 解析器。
* 有限的支持或完全不支持委托和子名称/子名称。
* 只有一种记录类型，不支持将多种记录关联至同一个名称。
* 由于单一的全局实现，因此不支持多个名称分配系统。
* 合并的职责: 名称解析、注册和 whois 信息。

这些特性支持的用例包括:

* 支持子名称/子名称，如 live.mysite.tld 和 forum.mysite.tld
* 单一名称下的多个服务，如托管在 Swarm 的 DApp、Whisper 地址和邮件服务器。
* 支持 DNS 记录类型，允许区块链托管的“传统”名称。这将允许 Ethereum 客户端（如 Mist）从区块链名称解析至传统网站地址或电子邮件服务器。
* DNS 网关，通过 DNS 对接 ENS 名称，为传统客户端解析和连接到区块链服务提供更便利的方法。

特别是前两个用例，在当今的互联网 DNS 下随处可见，我们相信它们是名称服务的基本特性，随着以太坊平台的发展和成熟，它们将继续发挥作用。

本文件的规范性部分没有规定提议系统的实现，它的目的是记录不同解析器的实现可以遵循的协议，以促进的名称解析的一致性。附录提供了解析器合约和库的实现示例，它们仅作为示例来用。

同样，本文档也不会指定应该如何注册或更新名称，或者系统如何查询某个名称的所有者。注册是注册商的工作，也是一个治理问题，在顶级名称之间必然会有所不同。

名称记录的更新也可以与解析分开处理。一些系统，如 Swarm，可能需要一个定义相对完善的接口来更新名称，鉴于此，我们计划为此开发一个标准。

## 规范

### 概述

ENS 系统包括三个主要部分:

* ENS 注册表
* 解析器
* 注册器

注册表是一个单独的合约，它支持将任意已注册名称映射到负责这个名称的解析器，并允许名称的所有者设置解析器地址，并创建子域，子域的所有者可以与父域不同。

解析器负责查找一个名称对应的资源——例如，返回一个合约地址、一个内容哈希或一个对应的 IP 地址。解析器规范在这里定义并在其他 ensip 中扩展，定义了解析器可以实现哪些方法来支持解析不同类型的记录。

注册器负责向系统用户分配名称，并且是唯一能够更新 ENS 的实体；ENS 注册表中节点的所有者是其注册器。注册器可以是合约或外部帐户，但根域和顶级域的注册器至少要通过合约来实现。

在 ENS 中解析名称需要两个步骤。首先，利用以下方法对要解析的名称进行哈希处理之后，通过这个哈希调用 ENS 注册表，如果记录存在，注册表将返回其解析器的地址。然后，使用与请求资源类型对应的方法来调用解析器，解析器返回所需的结果。

例如，假设您希望找到与 “beercoin.eth” 相关联的合约地址。首先，获取解析器:

```javascript
var node = namehash("beercoin.eth");
var resolver = ens.resolver(node);
```

然后，向解析器请求合约地址:

```javascript
var address = resolver.addr(node);
```

由于 “namehash” 过程只依赖于名称本身，因此可以预先计算并插入到合约中，从而不必再进行字符串操作，并且无论原始名称中有几级组件，都可以对 ENS 记录进行 O(1) 查找。

### 语法

ENS 名称必需符合以下语法

```
<域> ::= <标签> | <域> "." <标签>
<标签> ::= 任意符合 UTS46 标准的字符串标签 
```

简而言之，名称由一系列以点分隔的标签构成。每个标签都必须是有效的标准化标签，符合 `transitional=false` 和 `useSTD3AsciiRules=true` 条件下的 [UTS46](https://unicode.org/reports/tr46/) 规范。对于 Javascript 的实现，可以使用一个[库](https://www.npmjs.com/package/idna-uts46)来规范和检查名称。

需要注意的是，虽然名称中允许使用大写和小写字母，但 UTS46 规范化会在对标签进行哈希之前先对其进行大小写合并（译注，实际上就是会全部转换为小写字母），因此大小写不同但拼写相同的两个名称将产生相同的名称哈希。

标签和域可以是任意长度，但为了与传统 DNS 兼容，建议每个标签限制为不超过 64 个字符，完成的 ENS 名称不超过 255 个字符。出于同样的原因，建议标签不要以连字符开头或结尾，也不要以数字开头。

### namehash 算法

一个名称在 ENS 中使用之前，先使用 `namehash` 算法进行哈希处理。该算法对一个名称的各个组成部分进行递归式哈希，为任何有效的输入域生成一个唯一的、固定长度的字符串。namehash 的输出称为 “节点”。

namehash 算法的伪代码如下:

```
def namehash(name):
  if name == '':
    return '\0' * 32
  else:
    label, _, remainder = name.partition('.')
    return sha3(namehash(remainder) + sha3(label))
```

简单来说，名称被分成一个或多个标签，每个标签都经过哈希处理。然后，从最后一个标签开始，将前一个输出与当前标签的哈希连接并再次进行哈希处理。第一个标签与 32 个 `0` 字节连接。因此，`mysite.swarm` 的处理过程如下

```
node = '\0' * 32
node = sha3(node + sha3('swarm'))
node = sha3(node + sha3('mysite'))
```

namehash 的实现应该匹配以下测试向量:

```
namehash('') = 0x0000000000000000000000000000000000000000000000000000000000000000
namehash('eth') = 0x93cdeb708b7545dc668eb9280176169d1c33cfd8ed6f04690a0bcc88a93fc4ae
namehash('foo.eth') = 0xde9b09fd7c5f901e23a3f19fecc54828e9c848539801e86591bd9801b019f84f
```

### 注册表规范

ENS 注册表合约中公开了以下函数:

```solidity
function owner(bytes32 node) constant returns (address);
```

返回指定节点的所有者（注册器）。

```solidity
function resolver(bytes32 node) constant returns (address);
```

返回指定节点的解析器。

```solidity
function ttl(bytes32 node) constant returns (uint64);
```

返回节点的 TTL，也就是节点信息可以被缓存的最大时长。

```solidity
function setOwner(bytes32 node, address owner);
```

将节点的所有权转移给另一个注册器。此函数只能由 `node` 的当前所有者调用，此函数被调用成功后会记录事件 `Transfer(bytes32 indexed, address)`。

```solidity
function setSubnodeOwner(bytes32 node, bytes32 label, address owner);
```

创建一个新节点，`sha3(node, label)` 并将期所有者设置为 `owner`，而如果该节点已经存在，则会用新的所有者更新这个节点。此函数只能被 `node` 的当前所有者调用。此函数被调用成功后会记录事件 `NewOwner(bytes32 indexed, bytes32 indexed, address)`。

```solidity
function setResolver(bytes32 node, address resolver);
```

为 `node` 设置解析器。此函数只能被 `node` 的当前所有者调用。此函数被调用成功后会记录事件 `NewResolver(bytes32 indexed, address)`。

```solidity
function setTTL(bytes32 node, uint64 ttl);
```

设置节点的 TTL。节点的 TTL 适用于注册表中的“所有者”和“解析器”记录，以及相应解析器返回的任何信息。

### 解析器规范

解析器可以实现这里指定的记录类型的任意子集。如果记录类型规范要求解析器提供多种功能，则解析器必须实现或者放弃所有功能。解析器必须指定一个回调函数。

解析器具有一项强制性功能:

```solidity
function supportsInterface(bytes4 interfaceID) constant returns (bool)
```

`supportsInterface` 函数记录在 EIP-165 中，如果解析器实现了某个 4 字节标识符代表的接口，则返回 `true`。接口标识符由该接口提供的函数的签名哈希的异或组成，在单函数接口情况下，它直接等于该函数的签名哈希。

对于 `0x01ffc9a7`（即 `supportsInterface` 自身的接口 ID），`supportsInterface` 必须返回 true。

下表中指定了当前标准化的解析器接口。

以下接口已经定义:

| Interface name  | Interface hash | Specification     |
| --------- | ------- | -------- |
| `addr`                | 0x3b3b57de     | Contract address                                    |
| `name`                | 0x691f3431     | [ENSIP-3](ensip-3-reverse-resolution.md)            |
| `ABI`                 | 0x2203ab56     | [ENSIP-4](ensip-4-support-for-contract-abis.md)     |
| text                  | 0x59d1d43c     | [ENSIP-5](ensip-5-text-records.md)                  |
| contenthash           | 0xbc1c58d1     | [ENSIP-7](ensip-7-contenthash-field.md)             |
| interfaceImplementer  | 0xb8f2bbb4     | [ENSIP-8](ensip-8-interface-discovery.md)           |
| addr(bytes32,uint256) | 0xf1cb7e06     | [ENSIP-9](ensip-9-multichain-address-resolution.md) |

ENSIP 可以定义要添加到此注册表的新接口。

#### 合约地址接口

期望支持合约地址资源的解析器必须提供以下功能:

```solidity
function addr(bytes32 node) constant returns (address);
```

如果解析器支持 `addr` 查询，但请求的节点没有记录地址，那么解析器必须返回零地址。

解析 `addr` 记录的客户端必须检查零返回值，并将其视为一个未指定解析器的名称——即拒绝向该地址发送资金或与之交互。否则可能会导致用户不小心将资金发送到零地址。

更改地址必须触发以下事件:

```solidity
event AddrChanged(bytes32 indexed node, address a);
```

## 附录 A: 注册表的实现

```solidity
contract ENS {
    struct Record {
        address owner;
        address resolver;
        uint64 ttl;
    }

    mapping(bytes32=>Record) records;

    event NewOwner(bytes32 indexed node, bytes32 indexed label, address owner);
    event Transfer(bytes32 indexed node, address owner);
    event NewResolver(bytes32 indexed node, address resolver);

    modifier only_owner(bytes32 node) {
        if(records[node].owner != msg.sender) throw;
        _
    }

    function ENS(address owner) {
        records[0].owner = owner;
    }

    function owner(bytes32 node) constant returns (address) {
        return records[node].owner;
    }

    function resolver(bytes32 node) constant returns (address) {
        return records[node].resolver;
    }

    function ttl(bytes32 node) constant returns (uint64) {
        return records[node].ttl;
    }

    function setOwner(bytes32 node, address owner) only_owner(node) {
        Transfer(node, owner);
        records[node].owner = owner;
    }

    function setSubnodeOwner(bytes32 node, bytes32 label, address owner) only_owner(node) {
        var subnode = sha3(node, label);
        NewOwner(node, label, owner);
        records[subnode].owner = owner;
    }

    function setResolver(bytes32 node, address resolver) only_owner(node) {
        NewResolver(node, resolver);
        records[node].resolver = resolver;
    }

    function setTTL(bytes32 node, uint64 ttl) only_owner(node) {
        NewTTL(node, ttl);
        records[node].ttl = ttl;
    }
}
```

## 附录 B: 解析器的实现示例

### 内置解析器

最简单的解析器是通过实现合约地址配置来充当自己名称的解析器的合约:

```solidity
contract DoSomethingUseful {
    // Other code

    function addr(bytes32 node) constant returns (address) {
        return this;
    }

    function supportsInterface(bytes4 interfaceID) constant returns (bool) {
        return interfaceID == 0x3b3b57de || interfaceID == 0x01ffc9a7;
    }

    function() {
        throw;
    }
}
```

这样的合约可以直接插入 ENS 注册表，在简单的用例中无需单独的解析器合约。但是，“抛出”未知函数调用的要求可能会干扰某些类型合约的正常运行。

### 独立解析器

一个实现合约地址配置的基本解析器，并且只允许其所有者更新记录:

```solidity
contract Resolver {
    event AddrChanged(bytes32 indexed node, address a);

    address owner;
    mapping(bytes32=>address) addresses;

    modifier only_owner() {
        if(msg.sender != owner) throw;
        _
    }

    function Resolver() {
        owner = msg.sender;
    }

    function addr(bytes32 node) constant returns(address) {
        return addresses[node];    
    }

    function setAddr(bytes32 node, address addr) only_owner {
        addresses[node] = addr;
        AddrChanged(node, addr);
    }

    function supportsInterface(bytes4 interfaceID) constant returns (bool) {
        return interfaceID == 0x3b3b57de || interfaceID == 0x01ffc9a7;
    }

    function() {
        throw;
    }
}
```

部署此合约后，通过更新某个名称在 ENS 注册表中的记录来引用此合约，然后使用同一节点（译注: 这里的同一节点可以理解为上述名称的所有者）调用 `setAddr()` 以设置它将解析到的合约地址。

### 公共解析器

与上面的解析器类似，此合约仅支持合约配置，但使用 ENS 注册来确定由谁负责更新记录条目:

```solidity
contract PublicResolver {
    event AddrChanged(bytes32 indexed node, address a);
    event ContentChanged(bytes32 indexed node, bytes32 hash);

    ENS ens;
    mapping(bytes32=>address) addresses;

    modifier only_owner(bytes32 node) {
        if(ens.owner(node) != msg.sender) throw;
        _
    }

    function PublicResolver(address ensAddr) {
        ens = ENS(ensAddr);
    }

    function addr(bytes32 node) constant returns (address ret) {
        ret = addresses[node];
    }

    function setAddr(bytes32 node, address addr) only_owner(node) {
        addresses[node] = addr;
        AddrChanged(node, addr);
    }

    function supportsInterface(bytes4 interfaceID) constant returns (bool) {
        return interfaceID == 0x3b3b57de || interfaceID == 0x01ffc9a7;
    }

    function() {
        throw;
    }
}
```

## 附录 C: 注册器的实现示例

该注册器支持第一个请求某个名称的人免费注册该名称。

```solidity
contract FIFSRegistrar {
    ENS ens;
    bytes32 rootNode;

    function FIFSRegistrar(address ensAddr, bytes32 node) {
        ens = ENS(ensAddr);
        rootNode = node;
    }

    function register(bytes32 subnode, address owner) {
        var node = sha3(rootNode, subnode);
        var currentOwner = ens.owner(node);
        if(currentOwner != 0 && currentOwner != msg.sender)
            throw;

        ens.setSubnodeOwner(rootNode, subnode, owner);
    }
}
```

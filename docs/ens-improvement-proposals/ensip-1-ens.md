---
part: ENS 中文文档
title: 'ENSIP-1: ENS'
description: Documentation of the basic ENS protocol (formerly EIP-137).
---

| **作者**  | Nick Johnson \<arachnid@notdot.net> |
| ----------- | ----------------------------------- |
| **状态**  | 完结 |
| **创建日期** | 2016-04-04 |

## 摘要

这个 ENSIP 描述了以太坊名称服务（Ethereum Name Service）的细节，是一个包含协议及 ABI 定义的提案，它支持将简短的、人类可读的名称灵活地解析至服务和资源标识。用户和开发人员可以引用易读且容易记忆的名称，并允许在底层资源(合约、内容寻址数据等)发生变化时，根据需要更新这些名称。

域名的目标是提供稳定的、人类可读的标识符，用来指向网络资源。通过这种方式，用户可以输入一个容易记住的字符串，例如 “vitalik.wallet” 或 “www.mysite.swarm”，并定向到正确的资源。域名和资源之间的映射可能会随着时间的推移而改变，所以用户可能会更改钱包，网站可能会更改主机，文档可能会更新到新版本，而域名不会改变。此外，域名不一定指定单一的资源，通过不同的记录类型允许相同的域名引用不同的资源。例如，浏览器可以通过获取 A（地址）记录将 “mysite.swarm” 解析至它的服务器 IP 地址，同时邮件客户端可以通过获取邮件服务器的 MX（邮件交换器）记录来将相同的域名解析至邮件服务器。

## 动机

以太坊中现有的名称解析[规范](https://github.com/ethereum/wiki/wiki/Registrar-ABI)和[实现](https://ethereum.gitbooks.io/frontier-guide/content/registrar\_services.html)能够提供基本功能，但存在一些缺陷，会在很大程序上限制其长期可用性：

* 一个全局命名空间，所有名字都有一个 “集中式” 解析器。
* 有限的支持或完全不支持委托和子名称/子域名。
* 只有一种记录类型，不支持将多种记录关联至同一个域名。
* 由于单一的全局实现，因此不支持多个名称分配系统。
* 合并的职责：名称解析、注册和 whois 信息。

这些特性支持的用例包括：

* 支持子名称/子域名，如 live.mysite.tld 和 forum.mysite.tld
* 单一名称下的多个服务，如托管在 Swarm 的 DApp、Whisper 地址和邮件服务器。
* 支持 DNS 记录类型，允许区块链托管的“遗留”名称。这将允许 Ethereum 客户端（如 Mist）从区块链名称解析至传统网站地址或电子邮件服务器。
* DNS 网关，通过 DNS 对接 ENS 域名，为传统客户端解析和连接到区块链服务提供更便利的方法。

特别是前两个用例，在当今的互联网 DNS 下随处可见，我们相信它们是名称服务的基本特性，随着以太坊平台的发展和成熟，它们将继续发挥作用。

本文件的规范性部分没有规定提议系统的实现，它的目的是记录不同解析器的实现可以遵循的协议，以促进的名称解析的一致性。附录提供了解析器合约和库的实现示例，它们仅作为示例来用。

同样，本文档也不会指定应该如何注册或更新域名，或者系统如何查询某个域名的所有者。注册是注册商的工作，也是一个治理问题，在顶级域名之间必然会有所不同。

域名记录的更新也可以与解析分开处理。一些系统，如 Swarm，可能需要一个定义相对完善的接口来更新域名，鉴于此，我们计划为此开发一个标准。

## 规范

### 概述

ENS 系统包括三个主要部分：

* ENS 注册表
* 解析器
* 注册器

注册表是一个单独的合约，它支持将任意已注册名称映射到负责这个名称的解析器，并允许名称的所有者设置解析器地址，并创建子域，子域的所有者可以与父域不同。

解析器负责查找一个名称对应的资源——例如，返回一个合约地址、一个内容哈希或一个对应的 IP 地址。解析器规范在这里定义并在其他 ensip 中扩展，定义了解析器可以实现哪些方法来支持解析不同类型的记录。

注册器负责向系统用户分配域名，并且是唯一能够更新 ENS 的实体；ENS 注册表中节点的所有者是其注册人（registrar）。注册人可以是合约或外部帐户，但根域和顶级域的注册人至少要通过合约来实现。

在 ENS 中解析名称需要两个步骤。首先，利用以下方法对要解析的名称进行哈希处理之后，通过这个哈希调用 ENS 注册表，如果记录存在，注册表将返回其解析器的地址。然后，使用与请求资源类型对应的方法来调用解析器，解析器返回所需的结果。

例如，假设您希望找到与 “beercoin.eth” 相关联的合约地址。首先，获取解析器：

```javascript
var node = namehash("beercoin.eth");
var resolver = ens.resolver(node);
```

然后，向解析器请求合约地址：

```javascript
var address = resolver.addr(node);
```

由于 “namehash” 过程只依赖于名称本身，因此可以预先计算并插入到合约中，从而不必再进行字符串操作，并且无论原始名称中有几级组件，都可以对 ENS 记录进行 O(1) 查找。

### 语法

ENS 名称必需符合以下语法

```
<域> ::= <标签> | <域> "." <标签>
<标签> ::= 任意符合 [UTS46](https://unicode.org/reports/tr46/) 标准的字符串标签 
```

In short, names consist of a series of dot-separated labels. Each label must be a valid normalised label as described in [UTS46](https://unicode.org/reports/tr46/) with the options `transitional=false` and `useSTD3AsciiRules=true`. For Javascript implementations, a [library](https://www.npmjs.com/package/idna-uts46) is available that normalises and checks names.

Note that while upper and lower case letters are allowed in names, the UTS46 normalisation process case-folds labels before hashing them, so two names with different case but identical spelling will produce the same namehash.

Labels and domains may be of any length, but for compatibility with legacy DNS, it is recommended that labels be restricted to no more than 64 characters each, and complete ENS names to no more than 255 characters. For the same reason, it is recommended that labels do not start or end with hyphens, or start with digits.

### namehash algorithm

Before being used in ENS, names are hashed using the 'namehash' algorithm. This algorithm recursively hashes components of the name, producing a unique, fixed-length string for any valid input domain. The output of namehash is referred to as a 'node'.

Pseudocode for the namehash algorithm is as follows:

```
def namehash(name):
  if name == '':
    return '\0' * 32
  else:
    label, _, remainder = name.partition('.')
    return sha3(namehash(remainder) + sha3(label))
```

Informally, the name is split into labels, each label is hashed. Then, starting with the last component, the previous output is concatenated with the label hash and hashed again. The first component is concatenated with 32 '0' bytes. Thus, 'mysite.swarm' is processed as follows:

```
node = '\0' * 32
node = sha3(node + sha3('swarm'))
node = sha3(node + sha3('mysite'))
```

Implementations should conform to the following test vectors for namehash:

```
namehash('') = 0x0000000000000000000000000000000000000000000000000000000000000000
namehash('eth') = 0x93cdeb708b7545dc668eb9280176169d1c33cfd8ed6f04690a0bcc88a93fc4ae
namehash('foo.eth') = 0xde9b09fd7c5f901e23a3f19fecc54828e9c848539801e86591bd9801b019f84f
```

### Registry specification

The ENS registry contract exposes the following functions:

```solidity
function owner(bytes32 node) constant returns (address);
```

Returns the owner (registrar) of the specified node.

```solidity
function resolver(bytes32 node) constant returns (address);
```

Returns the resolver for the specified node.

```solidity
function ttl(bytes32 node) constant returns (uint64);
```

Returns the time-to-live (TTL) of the node; that is, the maximum duration for which a node's information may be cached.

```solidity
function setOwner(bytes32 node, address owner);
```

Transfers ownership of a node to another registrar. This function may only be called by the current owner of `node`. A successful call to this function logs the event `Transfer(bytes32 indexed, address)`.

```solidity
function setSubnodeOwner(bytes32 node, bytes32 label, address owner);
```

Creates a new node, `sha3(node, label)` and sets its owner to `owner`, or updates the node with a new owner if it already exists. This function may only be called by the current owner of `node`. A successful call to this function logs the event `NewOwner(bytes32 indexed, bytes32 indexed, address)`.

```solidity
function setResolver(bytes32 node, address resolver);
```

Sets the resolver address for `node`. This function may only be called by the owner of `node`. A successful call to this function logs the event `NewResolver(bytes32 indexed, address)`.

```solidity
function setTTL(bytes32 node, uint64 ttl);
```

Sets the TTL for a node. A node's TTL applies to the 'owner' and 'resolver' records in the registry, as well as to any information returned by the associated resolver.

### Resolver specification

Resolvers may implement any subset of the record types specified here. Where a record types specification requires a resolver to provide multiple functions, the resolver MUST implement either all or none of them. Resolvers MUST specify a fallback function that throws.

Resolvers have one mandatory function:

```solidity
function supportsInterface(bytes4 interfaceID) constant returns (bool)
```

The `supportsInterface` function is documented in ENSIP-165, and returns true if the resolver implements the interface specified by the provided 4 byte identifier. An interface identifier consists of the XOR of the function signature hashes of the functions provided by that interface; in the degenerate case of single-function interfaces, it is simply equal to the signature hash of that function. If a resolver returns `true` for `supportsInterface()`, it must implement the functions specified in that interface.

`supportsInterface` must always return true for `0x01ffc9a7`, which is the interface ID of `supportsInterface` itself.

Currently standardised resolver interfaces are specified in the table below.

The following interfaces are defined:

| Interface name        | Interface hash | Specification                                       |
| --------------------- | -------------- | --------------------------------------------------- |
| `addr`                | 0x3b3b57de     | Contract address                                    |
| `name`                | 0x691f3431     | [ENSIP-3](ensip-3-reverse-resolution.md)            |
| `ABI`                 | 0x2203ab56     | [ENSIP-4](ensip-4-support-for-contract-abis.md)     |
| text                  | 0x59d1d43c     | [ENSIP-5](ensip-5-text-records.md)                  |
| contenthash           | 0xbc1c58d1     | [ENSIP-7](ensip-7-contenthash-field.md)             |
| interfaceImplementer  | 0xb8f2bbb4     | [ENSIP-8](ensip-8-interface-discovery.md)           |
| addr(bytes32,uint256) | 0xf1cb7e06     | [ENSIP-9](ensip-9-multichain-address-resolution.md) |

ENSIPs may define new interfaces to be added to this registry.

#### Contract Address Interface <a href="#addr" id="addr"></a>

Resolvers wishing to support contract address resources must provide the following function:

```solidity
function addr(bytes32 node) constant returns (address);
```

If the resolver supports `addr` lookups but the requested node does not have an addr record, the resolver MUST return the zero address.

Clients resolving the `addr` record MUST check for a zero return value, and treat this in the same manner as a name that does not have a resolver specified - that is, refuse to send funds to or interact with the address. Failure to do this can result in users accidentally sending funds to the 0 address.

Changes to an address MUST trigger the following event:

```solidity
event AddrChanged(bytes32 indexed node, address a);
```

## Appendix A: Registry Implementation

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

## Appendix B: Sample Resolver Implementations

#### Built-in resolver

The simplest possible resolver is a contract that acts as its own name resolver by implementing the contract address resource profile:

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

Such a contract can be inserted directly into the ENS registry, eliminating the need for a separate resolver contract in simple use-cases. However, the requirement to 'throw' on unknown function calls may interfere with normal operation of some types of contract.

#### Standalone resolver

A basic resolver that implements the contract address profile, and allows only its owner to update records:

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

After deploying this contract, use it by updating the ENS registry to reference this contract for a name, then calling `setAddr()` with the same node to set the contract address it will resolve to.

#### Public resolver

Similar to the resolver above, this contract only supports the contract address profile, but uses the ENS registry to determine who should be allowed to update entries:

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

## Appendix C: Sample Registrar Implementation

This registrar allows users to register names at no cost if they are the first to request them.

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

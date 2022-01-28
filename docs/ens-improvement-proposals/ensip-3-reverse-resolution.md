---
part: ENS 中文文档
title: 'ENSIP-3: 反向解析'
description: Specifies a TLD, registrar, and resolver interface for reverse resolution of Ethereum addresses using ENS (formerly EIP-181).
---

| **作者**    | Nick Johnson \<nick@ens.domains> |
| ------------- | -------------------------------- |
| **状态**    | 完结                            |
| **提交时间** | 2016-12-01                       |

## 摘要

这个 ENSIP 为 ENS 的反向解析指定 TLD、注册器和解析器接口。它支持将人类可读的名称与任何以太坊区块链地址关联起来。解析器可以确定反向记录是由相关以太坊地址的所有者提交的。

## 动机

虽然名称服务主要用于正向解析——从人类可读的标识符解析到机器可读的标识符——但在许多情况下，反向解析也很有用:

* 在某些应用程序中，用户需要关注自己的账户，那么在应用程序中显示名称会比显示地址更合理，即便这个名称最初是通过地址添加的。
* 通过将元数据（比如描述信息等）关联到一个地址来允许信息读取，而不必关心最初如何找到这个地址。
* 人们不需要取得某个地址的所有权，就可以将名称解析到这个地址。反向记录允许地址的所有者声明一个名称作为该地址的专用名称。

## 规范

反向 ENS 记录与正向记录以相同的方式存储在 ENS 层次结构中，在保留域 `addr.reverse` 之下。要为给定帐户的反向记录生成 ENS 名称，请将帐户转换为小写的十六进制表示，并附加 `addr.reverse`。例如，ENS 注册表地址 "0x112234455c3a32fd11230c42e7bccd4a84e02010" 有反向记录存储在 `112234455c3a32fd11230c42e7bccd4a84e02010.addr.reverse`。

请注意，这意味着想要对地址进行动态反向解析的合约需要在合约中执行十六进制编码。

### 注册器

`addr.reverse` 域的所有者是一个注册器，它允许调用者为自己的地址取得反向记录的所有权。提供如下方法:

#### function claim(address owner) returns (bytes32 node)

当被账户 `x` 调用时，会通知 ENS 注册表将 `hex(x) + '.addr.reverse'` 的所有权转移至给定的地址，并返回本次转移的 ENS 记录的 namehash。

允许调用者为相关节点指定除自己以外的所有者，这有助于需要精确反向 ENS 记录的合约，并在构造函数中以最少的代码将此委托给合约的创建者。

```
reverseRegistrar.claim(msg.sender)
```

#### function claimWithResolver(address owner, address resolver) returns (bytes32 node)

当被账户 `x` 调用时，通知 ENS 注册表为 `hex(x) + '.addr.reverse'` 设置指定的解析器，然后将名称的所有权转移到提供的地址，并返回本次转移的 ENS 记录的 namehash。这个方法便于设置一个自定义的解析器和所有者，同时要比直接调用 `claim` 需要更少的交易次数。

#### function setName(string name) returns (bytes32 node)

当被账户 `x` 调用时，为 `hex(x) + '.addr.reverse'` 这一名设置一个默认解析器，并将名称记录设置为指定名称。这个方法便于用户在单个交易中完成反向记录设置。

### 解析器接口

定义了一个新的解析器接口，由以下方法组成:

```
function name(bytes32 node) constant returns (string);
```

实现此接口的解析器必须为请求的节点返回一个有效的 ENS 名称，如果没有为请求的节点定义名称，则返回空字符串。

该接口的 ID 为 0x691f3431。

未来的 ENSIP 可能会指定晚多适合反向 ENS 记录的类型。

## 附录 1: 注册器的实现

这个注册器是用 Solidity 编写的，实现了上文描述的规范。

```
pragma solidity ^0.4.10;

import "./AbstractENS.sol";

contract Resolver {
    function setName(bytes32 node, string name) public;
}

/**
 * @dev Provides a default implementation of a resolver for reverse records,
 * which permits only the owner to update it.
 */
contract DefaultReverseResolver is Resolver {
    AbstractENS public ens;
    mapping(bytes32=>string) public name;

    /**
     * @dev Constructor
     * @param ensAddr The address of the ENS registry.
     */
    function DefaultReverseResolver(AbstractENS ensAddr) {
        ens = ensAddr;
    }

    /**
     * @dev Only permits calls by the reverse registrar.
     * @param node The node permission is required for.
     */
    modifier owner_only(bytes32 node) {
        require(msg.sender == ens.owner(node));
        _;
    }

    /**
     * @dev Sets the name for a node.
     * @param node The node to update.
     * @param _name The name to set.
     */
    function setName(bytes32 node, string _name) public owner_only(node) {
        name[node] = _name;
    }
}

contract ReverseRegistrar {
    // namehash('addr.reverse')
    bytes32 constant ADDR_REVERSE_NODE = 0x91d1777781884d03a6757a803996e38de2a42967fb37eeaca72729271025a9e2;

    AbstractENS public ens;
    Resolver public defaultResolver;

    /**
     * @dev Constructor
     * @param ensAddr The address of the ENS registry.
     * @param resolverAddr The address of the default reverse resolver.
     */
    function ReverseRegistrar(AbstractENS ensAddr, Resolver resolverAddr) {
        ens = ensAddr;
        defaultResolver = resolverAddr;
    }

    /**
     * @dev Transfers ownership of the reverse ENS record associated with the
     *      calling account.
     * @param owner The address to set as the owner of the reverse record in ENS.
     * @return The ENS node hash of the reverse record.
     */
    function claim(address owner) returns (bytes32 node) {
        return claimWithResolver(owner, 0);
    }

    /**
     * @dev Transfers ownership of the reverse ENS record associated with the
     *      calling account.
     * @param owner The address to set as the owner of the reverse record in ENS.
     * @param resolver The address of the resolver to set; 0 to leave unchanged.
     * @return The ENS node hash of the reverse record.
     */
    function claimWithResolver(address owner, address resolver) returns (bytes32 node) {
        var label = sha3HexAddress(msg.sender);
        node = sha3(ADDR_REVERSE_NODE, label);
        var currentOwner = ens.owner(node);

        // Update the resolver if required
        if(resolver != 0 && resolver != ens.resolver(node)) {
            // Transfer the name to us first if it's not already
            if(currentOwner != address(this)) {
                ens.setSubnodeOwner(ADDR_REVERSE_NODE, label, this);
                currentOwner = address(this);
            }
            ens.setResolver(node, resolver);
        }

        // Update the owner if required
        if(currentOwner != owner) {
            ens.setSubnodeOwner(ADDR_REVERSE_NODE, label, owner);
        }

        return node;
    }

    /**
     * @dev Sets the `name()` record for the reverse ENS record associated with
     * the calling account. First updates the resolver to the default reverse
     * resolver if necessary.
     * @param name The name to set for this address.
     * @return The ENS node hash of the reverse record.
     */
    function setName(string name) returns (bytes32 node) {
        node = claimWithResolver(this, defaultResolver);
        defaultResolver.setName(node, name);
        return node;
    }

    /**
     * @dev Returns the node hash for a given account's reverse records.
     * @param addr The address to hash
     * @return The ENS node hash.
     */
    function node(address addr) constant returns (bytes32 ret) {
        return sha3(ADDR_REVERSE_NODE, sha3HexAddress(addr));
    }

    /**
     * @dev An optimised function to compute the sha3 of the lower-case
     *      hexadecimal representation of an Ethereum address.
     * @param addr The address to hash
     * @return The SHA3 hash of the lower-case hexadecimal encoding of the
     *         input address.
     */
    function sha3HexAddress(address addr) private returns (bytes32 ret) {
        addr; ret; // Stop warning us about unused variables
        assembly {
            let lookup := 0x3031323334353637383961626364656600000000000000000000000000000000
            let i := 40
        loop:
            i := sub(i, 1)
            mstore8(i, byte(and(addr, 0xf), lookup))
            addr := div(addr, 0x10)
            i := sub(i, 1)
            mstore8(i, byte(and(addr, 0xf), lookup))
            addr := div(addr, 0x10)
            jumpi(loop, i)
            ret := sha3(0, 40)
        }
    }
}
```

### 版权

通过 [CC0](https://creativecommons.org/publicdomain/zero/1.0/) 放弃版权及相关权利。

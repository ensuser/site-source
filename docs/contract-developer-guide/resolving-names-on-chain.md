---
part: ENS 中文文档
title: 链上 ENS 名称解析 
---

目前还没有用于链上解析的可靠库，但是 ENS 解析非常简单，不需要库也可以轻松完成。首先，我们定义了一些只包含必要方法的简化接口，:

```text
abstract contract ENS {
    function resolver(bytes32 node) public virtual view returns (Resolver);
}

abstract contract Resolver {
    function addr(bytes32 node) public virtual view returns (address);
}
```

解析时，在 ENS 合约中只需要用到 `resolver` 函数。ENS 合约中的其他方法可以用来在拥有名称的合约中查找所有者和更新 ENS 名称。

根据这些定义，查询一个给定节点哈希的名称非常简单：

```text
contract MyContract {
    // Same address for Mainet, Ropsten, Rinkerby, Gorli and other networks;
    ENS ens = ENS(0x00000000000C2E074eC69A0dFb2997BA6C7d2e1e);

    function MyContract(address ensAddress) public {
        ens = ENS(ensAddress);
    }

    function resolve(bytes32 node) public view returns(address) {
        Resolver resolver = ens.resolver(node)
        return resolver.addr(node);
    }
}
```

虽然合约可以将可读名称转换成节点哈希，但考虑到处理节点哈希会更加便利和高效，我们强烈建议在合约中使用节点哈希来取代名称，同时将规范化名称这项复杂工作交给链下的调用者来执行。如果某个合约总是解析一些相同的名称，则可以将这些名称转换为节点哈希并作为常量存储在合约中。

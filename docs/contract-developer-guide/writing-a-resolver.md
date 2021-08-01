---
part: ENS 中文文档
title: 编写一个 ENS 解析器 
---

[EIP137](https://github.com/ethereum/EIPs/issues/137) 中描述了解析器的详细信息。一个解析器必须实现以下方法：

```text
function supportsInterface(bytes4 interfaceID) constant returns (bool);
```

`supportsInterface` 在 [EIP165](https://github.com/ethereum/EIPs/issues/165) 中定义，它可以被调用者用来确定一个解析器是否支持某个特定的记录类型。记录类型是指解析器必须一起实现的一个或多个方法的集合。当前定义的记录类型包括:

| 记录类型 | 函数 | 接口 ID | 定义文档 |
| :--- | :--- | :--- | :--- |
| 以太坊地址 | addr | 0x3b3b57de | [EIP137](https://github.com/ethereum/EIPs/issues/137) |
| ENS 域名 | name | 0x691f3431 | [EIP181](https://github.com/ethereum/EIPs/issues/181) |
| ABI 规范 | ABI | 0x2203ab56 | [EIP205](https://eips.ethereum.org/EIPS/eip-205) |
| 公钥 | pubkey | 0xc8690233 | [EIP619](https://github.com/ethereum/EIPs/pull/619) |
| 文本记录 | text | 0x59d1d43c | [EIP634](https://eips.ethereum.org/EIPS/eip-634) |
| 内容哈希 | contenthash | 0xbc1c58d1 |  |

`supportsInterface` 本身的接口 ID 为 0x01ffc9a7 ，当 interfaceID 值为 0x01ffc9a7 时，`supportsInterface` 也必须返回 true 。

此外，`content` 接口被用作 Swarm 哈希事实上的标准，它的接口 ID 为 0xd8389dc5 。现在新的内容哈希应该使用 `contenthash` 接口来实现。

## 解析器示例

一个只支持 addr 类型的简易解析器，看起来就像这样:

```text
contract SimpleResolver {
    function supportsInterface(bytes4 interfaceID) constant returns (bool) {
        return interfaceID == 0x3b3b57de;
    }

    function addr(bytes32 nodeID) constant returns (address) {
        return address(this);
    }
}
```

这个简易解析器总是返回自己的地址作为所有查询的结果。尽管解析器采用不同的机制应该返回相同的结果，但实际上解析器可以按照需要采用任何机制来确定返回的结果，并且应该尽可能降低 gas 费用。

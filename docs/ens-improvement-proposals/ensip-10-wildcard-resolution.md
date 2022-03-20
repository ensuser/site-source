---
part: ENS 中文文档
subpart: ensip
title: 'ENSIP-10: 通配符解析'
description: Provides a mechanism to support wildcard resolution of ENS names (formerly EIP-2544).
---

| **作者**    | Nick Johnson \<nick@ens.domains>, 0age (@0age) |
| ------------- | ---------------------------------------------- |
| **状态**    | 草案                                          |
| **提交时间** | 2020-02-28                                     |

### 摘要

以太坊名称服务规范 (ENSIP-1) 确定了名称解析过程分为两个步骤。首先，ENS 客户端对名称执行 namehash 算法，确定名称对应的“节点”，随后将该节点提供给 ENS 注册表合约来找到这个名称的解析器。这时，如果在注册表上已经设置了解析器，客户端将向解析器合约提供同一个节点，解析器就会返回相应的地址或其他记录。

按照当前的规范，如果在 ENS 注册表中没有为给定的节点设置解析器，那么解析过程就会结束。此 ENSIP 将会更改名称解析的过程，它会为那些没有设置解析器的域添加一个额外的步骤。这个步骤从名称中除去最左边的标签，派生出新名称片段的节点，并将该节点提供给 ENS 注册表。如果找到了该节点的解析器，客户端将向该解析器合约提供原始的、完整的节点，以获取相应的记录。此步骤重复执行，直到找到具有解析器的节点。

此外，该规范为解析器定义了一种解析名称的新方法，统一使用能够更灵活地处理名称解析的 `resolve()` 方法。

### 动机

许多应用程序，如钱包提供商、交易所和 dapp 都表示希望通过共享父域上的自定义子域向用户分发 ENS 名称。然而目前，这样做必须在 ENS 注册表上为每个子域设置一个不同的记录，因此对于拥有巨量用户群体的应用程序来说，成本就成了限制因素。

此外，由于为子域节点分配解析器的交易必须先在链上提交和打包，因此用户不能在创建帐户时立即使用这些子域。这给新用户造成了不必要的障碍，而这些新用户将会从 ENS 名称的可用性改进中获益。

通配符的启用能够支持设计更高级的解析器，为未分配的子域确定性地生成地址和其他记录。生成的地址可以映射到反事实的合约部署地址 (即 `CREATE2` 地址) 或指定的“回退”地址，或者其他方案。此外，仍然可以给任何指定的子域分配独立的解析器，这将使用父解析器取代通配符解析。

这项标准的另一个关键动机是以向后兼容的方式支持通配符解析。它不需要修改当前 ENS 注册表合约或任何现有的解析器，并继续支持现有的 ENS 记录，只是旧的 ENS 客户端将无法解析通配符记录。

### 规范

本文档中的关键词 “必须”、“绝对不能”、“必需”、“会”、“不会”、“应该”、“不应”、“推荐”、“可以”和“可选”应按照 RFC 2119 中的描述进行解释。

设:

* `namehash` 是 ENSIP-1 中定义的算法。
* `dnsencode` 是 RFC1035 3.1 节中指定的 DNS 域名的编码过程，但编码后的名称的总长度没有限制。空字符串作为一个 0 长度的 8 位元组，与名称 '.' 编码相同。
* `parent` 是一个函数，用于删除一个名称的第一个标签 (例如，`parent('foo.eth') = 'eth'`)。`parent('tld')` 被定义为空字符串。
* `ens` 是当前网络的 ENS 注册表合约。

兼容 ENSIP-10 的 ENS 解析器**可以**实现以下功能接口:

```
interface ExtendedResolver {
    function resolve(bytes calldata name, bytes calldata data) external view returns(bytes);
}
```

如果一个解析器实现了这个函数，那么在 `supportsInterface()` 被调用且传入 0xTBD 这个接口 ID 时，它**必须**返回 true。

ENS 客户端将调用 `resolve` 并传入需要解析的 DNS 编码名称和用于解析器函数的编码后的调用数据 (比如 ENSIP-1 和其他地方指定的)，该函数**必须**返回有效的数据，或在不支持的情况下进行回退。

兼容 ENSIP-10 的 ENS 客户端在获取给定名称的解析器时**必须**执行以下步骤:

1. 设置 `currentname = name`
2. 设置 `resolver = ens.resolver(namehash(currentname))`
3. 如果 `resolver` 不是零地址，则停止并返回 `resolver`。
4. 如果 `name` 为空 ('' 或 '.')，则停止并返回 null。
5. 其他情况下，设置 `currentname = parent(currentname)` 并进入第 2 步。

如果上面的过程返回 null，名称解析**必须**终止。其他情况下，兼容 ENSIP-10 的 ENS 客户端解析一条记录时**必须**执行以下步骤:

1. 将 `calldata` 设置为解析函数对应的 ABI 编码数据——例如，解析 `addr` 记录时，设置为 `addr(namehash(name))` 的 ABI 编码。
2. 设置 `supports2544 = resolver.supportsInterface(0xTBD)`。
3. 如果 `supports2544` 为 true，则设置 `result = resolver.resolve(dnsencode(name), calldata)`。
4. 其他情况下，将 `result` 设置为：使用 `calldata` 作为参数来调用 `resolver` 的结果。
5. 使用对应的解析函数的返回数据 ABI 解码后，返回 `result` (例如，对于 `addr()`， `resolver.resolve()` 的结果经 ABI 解码为 `address`)。

Return result after decoding it using the return data ABI of the corresponding resolution function (eg, for addr(), ABI-decode the result of resolver.resolve() as an address).

请注意，在所有情况下，解析函数 (`addr()` 等) 和 `resolve` 函数都使用了原来的 `name`， 而不是在解析的第一阶段找到的 `currentname`。

#### 伪代码

```
function getResolver(name) {
    for(let currentname = name; currentname !== ''; currentname = parent(currentname)) {
        const node = namehash(currentname);
        const resolver = ens.resolver(node);
        if(resolver != '0x0000000000000000000000000000000000000000') {
            return resolver;
        }
    }
    return null;
}

function resolve(name, func, ...args) {
    const resolver = getResolver(name);
    if(resolver === null) {
        return null;
    }
    const supports2544 = resolver.supportsInterface('0xTBD');
    let result;
    if(supports2544) {
        const calldata = resolver[func].encodeFunctionCall(namehash(name), ...args);
        result = resolver.resolve(dnsencode(name), calldata);
        return resolver[func].decodeReturnData(result);
    } else {
        return resolver[func](...args);
    }
}
```

### 原理

实现本提案将会以一种对现有系统影响最小化的方式支持通配符解析。它最大程度上重用了现有的算法和过程，从而减轻了各种 ENS 客户端的作者和维护人员的负担。

它也承认当前关于 ENS 通配符解析的共识，通过解决关键的可伸缩性障碍，使原有规范得到更广泛的采用。

为解析器引入可选的 `resolve` 函数，在解析函数中传入了名称和 calldata，这些增加了实现的复杂性，但也为解析器提供了一种方法来获取明文标签并执行相应的程序，使得原本许多不可能实现的通配符相关用例变得可能——例如，通配符解析器可以将 `id.nifty.eth` 指向某个集合中 id 为 `id` 的 NFT 的所有者，而如果只使用名称集，这是不可能的。面向简单需求的解析器可以继续直接实现解析函数，并完全忽略对 `resolve` 函数的支持。

DNS wireformat 用于名称编码，因为它能够快速且高效地进行名称哈希，以及其他类似获取或删除单个标签的常见操作，相反，点分隔的名称需要遍历名称中的每个字符来找到分隔符。

### 向后兼容性

兼容 ENSIP-1 的现有 ENS 客户端将无法解析通配符记录并拒绝与之交互，而符合 ENSIP-10 的客户端将继续正确解析或拒绝现有 ENS 记录。希望为非通配符的用例实现新的 `resolve` 函数 (例如，解析器直接设置在被解析的名称上) 的解析器，应该考虑将什么返回给调用单个解析函数的旧客户端，以获得最大的兼容性。

### 安全注意事项

尽管兼容的 ENS 客户端在没有解析器的情况下会拒绝解析记录，但仍然会存在错误配置的客户端引用错误解析器的风险，或者在无法获取解析器时继续与空地址进行交互。

此外，对于完全支持任意通配符子域解析的解析器来说，由于输入错误而将资金意外发送给其他接收者的可能性将会增加。实现这种解析器的应用程序应该考虑根据上下文为客户提供额外的名称验证，或者实现支持资金找回的功能。

还有一种可能性是，一些应用程序可能需要不给某些子域设置解析器。如果要解决这个问题，父域需要正确解析给定的子域节点——据作者所知，目前没有应用程序支持此特性或期望子域不要解析到某条记录。

### 版权

通过 [CC0](https://creativecommons.org/publicdomain/zero/1.0/) 放弃版权及相关权利。

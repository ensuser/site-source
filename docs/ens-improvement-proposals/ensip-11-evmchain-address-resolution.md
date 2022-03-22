---
part: ENS 中文文档
subpart: ensip
title: 'ENSIP-11: EVM 兼容链的地址解析'
description: 引入 EVM 兼容链的代币类型 (修订 ENSIP9)。
---

| **作者**    | Makoto Inoue \<makoto@ens.domains> |
| ------------- | -------------------------------- |
| **状态**    | 草案                            |
| **提交时间** | 2022-01-13                       |

### 摘要

这个 ENSIP 扩展了 [ENSIP 9 (多链地址解析)](ensip-9-multichain-address-resolution.html)，为兼容 EVM 的链规定了一系列代币类型，并指定了一种将 EVM 链 ID 派生到指定代币类型的方法。

专用范围使用超过 0x80000000 (2147483648)，这是在 ENSIP 9 下保留的，因此代币类型不可能出现冲突，其他非 EVM 代币类型会在未来增加。然而，一些以前分配给 EVM 链 ID 的代币类型将被弃用。

### 动机

现有的 ENSIP 9 依赖于 [SLIP44](https://github.com/satoshilabs/slips/blob/master/slip-0044.md) 上存在的代币类型，它被设计用来为确定性钱包定义地址编码类型。由于大多数 EVM 兼容链继承与以太坊相同的编码类型，因此不断请求将 EVM 兼容链添加到 SLIP 44 中是多余的。本规范标准化了一种基于[链 ID](https://chainlist.org) 派生出代币类型的方法。

### 规范

该规范修正了 ENSIP 9，规定具有最高有效位集的代币类型将被视为 EVM 链 ID。MSB 在 SLIP44 中保留，用于其他与 HD 钱包密钥派生相关的用途，因此在这个范围内不存在代币类型。

计算 EVM 链的新代币类型时，将链 ID 和 `0x80000000` 进行“位-或”计算: `0x80000000 | chainId`。

```typescript
export const convertEVMChainIdToCoinType = (chainId: number) =>{
  return  (0x80000000 | chainId) >>> 0
}
```

反向操作时，将代币类型和 `0x7fffffff` 进行“位-与”计算: `0x7fffffff & coinType`。

```typescript
export const convertCoinTypeToEVMChainId = (coinType: number) =>{
  return  (0x7fffffff & coinType) >> 0
}
```

#### 实现

[ensdomains/address-encoder](https://github.com/ensdomains/address-encoder/) 仓库中提供了该接口的实现。

#### 示例

要为 EVM 链计算新的代币类型，需调用 `convertEVMChainIdToCoinType(chainId)`

```javascript
const encoder = require('@ensdomains/address-encoder')
>  encoder.convertEVMChainIdToCoinType(61)
2147483709
> encoder.convertCoinTypeToEVMChainId(2147483709)
61
```

你也可以使用现有的 formatsByName 和 formatsByCoinType 函数来派生这些链 ID

```javascript
> encoder.formatsByName['XDAI']
{
 coinType: 2147483748,
 decoder: [Function (anonymous)],
 encoder: [Function (anonymous)],
 name: 'XDAI'
}
> encoder.formatsByCoinType[2147483748]
{
 coinType: 2147483748,
 decoder: [Function (anonymous)],
 encoder: [Function (anonymous)],
 name: 'XDAI'
}
```

#### 例外

以下 EVM 链是这个标准的例外。

* AVAX = AVAX 有多链地址格式，只有 c 链兼容 EVM
* RSK = RSK 有自己的额外验证

他们将继续使用在 SLIP44 定义的代币类型

#### 向后兼容性

在引入这个新标准之前，存在以下 EVM 兼容的类型。

* NRG
* POA
* TT
* CELO
* CLO
* TOMO
* EWT
* THETA
* GO
* FTM
* XDAI
* ETC

出于向后兼容的目的显示它们时，将 `_LEGACY` 附加到代币类型并使其为只读。

### 版权

通过 [CC0](https://creativecommons.org/publicdomain/zero/1.0/) 放弃版权及相关权利。

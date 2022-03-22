---
part: ENS 中文文档
title: ENS L2 和链下数据支持
---

## 重要提示

ENS 链外数据支持的开发仍在进行中，下面描述的解决方案还没有在生产环境中使用。因此，本文主要是为了提供信息而编写的，以便 dapp 和钱包开发人员在完全支持集成后可以为集成做准备。

## 摘要

随着以太坊 L2 解决方案的普及，以太坊开始走向成熟，重要的是 ENS 能够在整个生态系统中提供解析服务，并使 ENS 用户能够享受 L2 解决方案所带来的效率。在 [Vitalik](https://ethereum-magicians.org/t/a-general-purpose-l2-friendly-ens-standard/4591) 的一篇文章提出了一种可能的方法之后，ENS 团队以及 ENS 和 L2 社区一直在构建一个通用的“ L2 桥”，它能为 ENS 和其他应用程序提供跨平台的互操作性并提出标准，这些应用程序需要以零信任的方式从各种链下数据源 (存储在以太坊主网之外的任何数据，这包括专有数据库和 L2 解决方案，如 Optimism、Arbitrum、Starkware、ZKSync 等等) 来获取数据。

* [EIP-3668: CCIP Read: 安全地获取链外数据](https://eips.ethereum.org/EIPS/eip-3668)
* [ENSIP 10: 通配符解析](/docs/ens-improvement-proposals/ensip-10-wildcard-resolution.html)

**EIP 3668** 允许以一种对客户透明的方式进行链下 (包括 L2 ) 数据查询，并可以让合约的作者实现任何必要的验证。在许多情况下，如果数据存储在链上，则无需任何额外的信任假设。

**ENSIP 10** 是在 L1 上解析通配符 (如: *.foo.eth) 的通用方法。通过分发子名称并将父名称的解析移至链下，可以让 dapp 在链下创建子名称，然后通过 L1 访问。

## Dapp 和钱包支持链下数据查询所需的步骤

如果你的 dapp 或钱包使用了下面的库，EIP 3668 和 ENSIP 10 支持将被内置，所以只要在准备好时更新库即可。

* [ethersjs](https://github.com/ethers-io/ethers.js)
* [web3js](https://github.com/ethereum/web3.js)

### ethersjs

目前，EIP 3668 是作为 npm 模块实现的: [@chainlink/ethers-ccip-read-provider
](@chainlink/ethers-ccip-read-provider
)([source](https://github.com/smartcontractkit/ccip-read/tree/rewrite/packages/ethers-ccip-read-provider)).

基本的用法示例如下。

```js
import { CCIPReadProvider } from '@chainlink/ethers-ccip-read-provider';
import { abi as IExtendedResolver_abi } from '@ensdomains/offchain-resolver-contracts/artifacts/contracts/IExtendedResolver.sol/IExtendedResolver.json';
const IExtendedResolver = new ethers.utils.Interface(IExtendedResolver_abi);
const baseProvider = ethers.getDefaultProvider(options.provider);
const provider = new CCIPReadProvider(baseProvider);
```

目前还不支持通配符。

详情请参考[链下解析器客户端示例代码](https://github.com/ensdomains/offchain-resolver/blob/main/packages/client/src/index.ts#L46)。

### web3js

开发中。

### 其他库

如果您使用其他库或自定义集成，请向项目仓库提出 github issue，如果项目对应的仓库不存在，请到 [ENS 项目管理仓库](https://github.com/ensdomains/pm/issues) 提出 issue，以便 ENS 团队跟踪进展。

## Dapp 和钱包分发子名称的步骤

如果您希望使用链下数据存储发布子名称，请参考[链下解析器](https://github.com/ensdomains/offchain-resolver)。该示例使用平面文件作为数据源，但可以轻易地替换为数据库调用。

L2 的支持仍然在开发中。

## 常见问题

### 更改是向后兼容的吗?

是的。即使在没有客户端或应用程序支持这些标准的情况下，L1 上现有的名称也可以正常使用。只有在 L1 之外的名称才不会被解析。

### GraphQL 支持 L2 或链下数据吗?

一旦某个 L2 得到官方支持，我们将需要为这个 L2 桥编制一个子图，并且我们会通过模式拼接让调用者可以透明地使用它们。

对于没有托管在受支持的 L2 上的名称，我们将无法获取那些通常只在子图上可用的数据。

### 如何支持其他 EVM 兼容链?

非 L2 链缺乏以零信任方式验证 L1 上数据的方法。替代办法是，跨链桥的运营者作为可信的第三方，托管链下网关，或者由 dapp 托管自己的网关，并使用 ENS 名称的私钥对每条数据进行签名。

### 我能在链下环境中发布新的 tld 吗?

不能。详细信息请看 [为什么 ENS 不创建更多的顶级域：我们在对全球名称空间负责](/news/2019-12-20-why-not-ens-support-more-tld.html)。

### 我可以在链下名称中设置一个主名称吗?

可以。然而，反向注册器 (它是一个以 .addr.reverse 开头的隐藏的顶级名称) 目前存储在 L1 上，所以需要在 L1 上消耗 Gas。我们将来可能会考虑将反向注册器迁移至 L2。

### 我可以在链下注册 .eth 名称吗?

当发现哪个 L2 对 ENS 集成的支持最友好以后，我们会将 .eth 名称迁移到特定的 L2，这会是迁移的最后一步，只有这些完成以后，才可以实现 .eth 名称的链下注册。

### 我如何处理合约地址?

与 EOA (外部帐户) 不同，多签账户等基于合约的帐户可能只能在某些链中访问。

[ENSIP-11](/docs/ens-improvement-proposals/ensip-11-evmchain-address-resolution.html) 允许单个名称在多个 EVM 兼容链中保存不同的地址，建议将合约地址存储到 EVM 链指定地址记录字段。

### 我可以使用其他支持 .eth 名称服务的库吗?

`@unstoppabledomains/resolution` [对 ENS 的支持]((https://github.com/unstoppabledomains/resolution/releases/tag/v7.0.0))已于 2021 年 12 月移除。其他服务往往不支持所有 ENS tld，尤其是基于 DNS 的 tld (.com .net 等)，所以我们建议不要依赖这些库解析 ENS 名称。

## 参考文献和以前的讨论

- [MVP of ENS on L2 with Optimism: Demo Video + How to Try It Yourself](https://medium.com/the-ethereum-name-service/mvp-of-ens-on-l2-with-optimism-demo-video-how-to-try-it-yourself-b44c390cbd67)
- [A general-purpose bridge for Ethereum Layer 2s](https://medium.com/the-ethereum-name-service/a-general-purpose-bridge-for-ethereum-layer-2s-e28810ec1d88)
- [A general-purpose L2-friendly ENS standard](https://ethereum-magicians.org/t/a-general-purpose-l2-friendly-ens-standard/4591)
- [Video: ENS Workshop on 18th Oct 2021](https://www.youtube.com/watch?v=L9N7U_bNmOU)
- [Video: ENS Workshop on 6th April 2021](https://www.youtube.com/watch?v=9DdL7AQgXTM)
- [Video: ENS on Layer 2 meeting #2 on 28th Oct 2020](https://www.youtube.com/watch?v=QwEiAedSNYI)
- [Video: ENS on Layer 2 meeting on 13th Oct 2020](https://www.youtube.com/watch?v=vloI0VT8DXE)
- [Video: ENS workshop on 29th Sep 2020](https://www.youtube.com/watch?v=65z_j4n8mTk&t=2s)

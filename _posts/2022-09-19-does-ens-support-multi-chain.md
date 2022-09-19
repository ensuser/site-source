---
title: 放下傲慢与偏见，看 ENS 是单链还是多链
date: 2022-09-19 13:30:00
categories: 社区交流
original: 
author: liubenben.eth
translator: 
description: 
---

[DID](https://ethereum.org/en/decentralized-identity/)（去中心化身份，它可以有很多种形式和载体，本文中的 DID 特指以 ENS 为代表的去中心化域名/名称/名字）作为 Web3 的一项基础设施，近来由于 ENS 社区的火热和多个同类项目的发布、发展而受到越来越多的关注。在众多关于各个 DID 项目特点的声音中，“单链”和“多链”经常被当作一项重要的比较指标。那么 ENS 是单链的还是多链的？

横看成岭侧成峰。

## 1. 从使用角度来看

从使用的角度来看，ENS 支持多链解析，也可以说是全链解析。

这里提到的“全链”是指在 [SLIP-0044](https://github.com/satoshilabs/slips/blob/master/slip-0044.md) 规范中的所有币种。

通常情况下，我们说一个 DID 是否支持多链，是指从使用的角度来评判。简单地说，当你有一个 ENS 时，你可以为它设置哪些币种的解析记录，收款时可以收哪些币？如果支持多个币种，就可以说其支持多链。

在这个前提下，几乎每个 DID 项目都是支持多链的，因为自从 ENS 实现多链解析以来，这个门槛已经非常低。解析记录多以“键值对”的形式存在，那么以不同链的标识为“键”，地址为“值”，可以轻松地将多个链的信息关联到一个 DID 上。所以，“支持多链”实际上已经是大多数 DID 的基本功能之一。

### 延伸内容：ENS 对多链支持的贡献

没有人能否认 ENS 为 DID 做出的诸多开创性成果，多链支持只是其中并不起眼的贡献之一。

尽管多链支持现在已经非常普遍，但 2017 年的 ENS 确实只支持以太坊这一条公链。对了，那个时候没有 Unstoppabeldomains，更没有 SpaceID。

ENS 的创始人 [Nick](https://twitter.com/nicksdjohnson) 在 2019 年 9 月提出的 [ENSIP-9: 多链地址解析](https://ensuser.com/docs/ens-improvement-proposals/ensip-9-multichain-address-resolution.html)（即 [EIP-2304](https://eips.ethereum.org/EIPS/eip-2304)）对 ENS 的全链解析进行了标准化，同年 10 月份，就有 [15 个主流钱包支持了这一标准](https://medium.com/the-ethereum-name-service/ens-launches-multi-coin-support-15-wallets-to-integrate-92518ab20599)。也就是说 ENS 在 2019 年就已经支持全链解析，并得到行业的广泛支持。

此外，2022 年 1 月，ENS 核心团队成员 [Makoto](https://twitter.com/makoto_inoue) 撰写了 [ENSIP-11: EVM 兼容链的地址解析](https://ensuser.com/docs/ens-improvement-proposals/ensip-11-evmchain-address-resolution.html)，旨在修正前面提到的 ENSIP-9，主要目的是减少向 SLIP-0044 增加不必要的链 ID。通俗点讲就是，不仅要支持全链，还要避免币种标识的泛滥。

## 2. 从产权角度来看

从资产所有权的角度来看，ENS 仅支持单链，即以太坊主网。

DID 作为一种数字资产，尤其是 ENS 名称作为一种数次占据 OpenSea 交易量榜首的 NFT 数字资产，有必要考虑在持有这类资产时，是仅能单链持有，还是可以多链持有，尤其要考虑我们的资产所有权是受哪条链保护。

ENS 作为资产目前仅支持存储在以太坊主网，即所有的 ENS 名称都只能通过以太坊主网来持有、转让，并受以太坊的安全体系的保护。这里有一篇文章说明了 [ENS 为什么选择了以太坊](https://ensuser.com/news/2019-02-12-why-ens-uses-ethereum-and-eth-not-a-bespoke-blockchain-and-token.html)。

那么 ENS 未来会不会针对产权开启多链支持？我认为基本不会，一是因为以太坊提足够安全，二是因为这并不会增加实用性，三是因为会导致系统的过于复杂。

## 3. 从管理角度来看

从管理维护角度来看，ENS 支持多链。

ENS 目前默认支持在以太坊主网对名称进行管理维护（比如解析记录和子域分发等等），同时支持以一种安全的方式在链外进行名称信息的管理。这里的“链外”是指以太坊主网之外，包括以太坊二层网络、其他公链，甚至是中心化的数据源。

起初，ENS 的管理信息，包括解析器和解析记录，都是存储在以太坊主网，管理维护需要通过以太坊主网交互来完成。由于 ENS 以及整个 DID 生态的基础设施属性，这些名称正在产生庞大的管理维护需求，而以太坊主网上的高昂的交互成本则是这些需求的巨大阻力。比如有一些需要大量分发子名称的应用场景，以及动态更新 IPFS 站点地址的应用场景，要么难以实现，要么采取了折衷的办法。

好在 ENS 核心团队很早就开始着手解决这方面的问题，他们的方案是让用户可以选择通过以太坊主网以外的二层网络或其他数据来进行 ENS 名称的管理维护。2020 年 10 月，Nick 为此撰写了 [《一种以太坊 Layer-2 的通用桥》](https://ensuser.com/news/2020-10-30-a-general-purpose-bridge-for-ethereum-layer-2s.html)，这篇文章指出了一种可以安全地从以太坊主网以外获取数据的方法，其主要内容后来形成了 [EIP-3668](https://eips.ethereum.org/EIPS/eip-3668)。ENS 文档中已经增加了 [ENS 从二层和链外获取数据支持的说明](https://ensuser.com/docs/dapp-developer-guide/ens-l2-offchain.html)。

比如，今年 7 月份，[Coinbase 与 ENS 合作上线的 DID 服务](https://help.coinbase.com/en/wallet/managing-account/coinbase-ens-support)，其对 cb.id 子名称的管理维护就是在以太坊主网之外进行的。

### 延伸内容：.bit 跨链管理

如果您一直关注 DID，那您应该知道有个叫 .bit 的 DID 项目，它的一个特点是：跨链（Cross-chain）。这的确它的一大特色，.bit 名称可以由不同链的地址来持有，它的核心合约部署在 Nervos 公链上，但是通过合约内置的算法，你可以通过以太坊（或其他支持的链）的账户来注册、管理和使用 .bit 名称，不必非得拥有 Nervos 账户，这个特点也扩大了 .bit 的受众群体。

我认为 .bit 的这种方式可以称为跨链管理，但如果是从保护资产所有权的角度来评判的话，.bit 也不能完全称得上跨链，因为资产的安全还是由 Nervos 这条公链来保护。

最近 .bit 支持了名称的跨链转移，用户将自己的 .bit 名称跨链存储到以太坊主网，但需要付出管理使用受限的代价，转移到以太坊主网后仅保留了名称转让的功能。至于后续能否支持在以太坊上管理使用，还不得而知。但要同时实现所有权和管理上的多链支持，可能会导致系统极度复杂，带来的更高的开发难度和更多的未知风险，所以，值不值得这样做，需要时间来揭开答案。

## 结语

要实现通俗意义上的多链支持，不难得；要实现严格意义上的多链支持，不值得。

最重要的是，一个 DID 项目的应用范围和集成数量是其生态的基本面，只有拥有强大的生态系统，所有的功能和特色才有意义。许多真正看好 ENS 发展前景的人，我相信他们是将这个基本面作为了关键的评价标准。

本文仅是个人愚见，免不了错误和疏漏，只是给出一点参考，希望能够更加理性地看待 ENS 乃至 Web3。

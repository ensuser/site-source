---
part: ENS 中文文档
title: ENS 介绍 
---

以太坊域名服务（Ethereum Name Service，简称 ENS）是一个基于以太坊区块链的分布式、开放和可扩展的命名系统。

ENS 的工作是将可读的域名（比如 “alice.eth”）解析为计算机可以识别的标识符，如以太坊地址、其他加密货币地址、内容的哈希、元数据等。ENS 还支持 “反向解析”，这使得将元数据(如规范化域名或接口描述)与以太坊地址相关联成为可能。

ENS 的目标与 DNS（互联网域名服务）类似，但由于以太坊区块链的功能特点和限制条件，两者架构有很大的不同。与 DNS 一样，ENS 是一个层次结构的域名系统，不同层次域名之间以点作为分隔符，我们把层次的名称叫做域，一个域的所有者能够完全控制其子域。

顶级域名（比如 “.eth” 和 “.test”）的所有者是一种名为 “注册器（registrar）” 的智能合约，该合约内指定了控制子域名分配的规则。任何人都可以按照这些合约规定的规则，获得一个域名的所有权并为自己所用。ENS 还支持用户将自己拥有的 DNS 域名接入到 ENS 中使用。

由于 ENS 的层次性，不论一个人拥有哪个级别的域名，都可以根据需要为自己或他人配置子域名。例如，如果 Alice 拥有 “alice.eth”，她就可以创建 “pay.alice.eth” 并按需对其进行设置。

ENS 部署在以太坊主网络和几个测试网络上。如果你使用 [ensjs](https://www.npmjs.com/package/@ensdomains/ensjs) Javascript 库或终端用户应用程序，它将自动检测与你交互的网络并在该网络上部署 ENS。

你现在就可以通过 [ENS APP](https://app.ens.domains/) 或 [ENS 官方主页](https://ens.domains/) 上的 ENS 应用程序，来试用 ENS。

## ENS 架构

ENS 有两个主要组件：[注册表](contract-api-reference/ens.html) 和 [解析器](contract-api-reference/publicresolver.html)。

![](/images/docs/ens-architecture.png)

ENS 注册表是一个智能合约，该合约维护所有域名和子域名列表，并存储关于每个域名的三个关键信息:

> * 域名的所有者
> * 域名的解析器
> * 域名下所有记录的缓存存活时间（即 TTL）

域名的所有者可以是外部帐户（用户）或智能合约。注册器就是一个拥有顶级域名的智能合约，并按照合约中的规则将该域名的子域名分发给用户。

ENS 注册表中的域名所有者可以:

> * 为域名设置解析器和 TTL
> * 将域名的所有权转让给另一个地址
> * 更改子域名的所有权

ENS 注册表非常简单，它的存在只是为了将域名映射到负责解析这个域名的解析器。

解析器负责将域名转换为地址。只要是符合解析器相关标准的智能合约，都可以在 ENS 中作为解析器程序。通用解析器服务于需求简单的用户，比如不经常更改地址的用户。

每个记录类型（以太坊地址、内容哈希等）都定义了一个或多个方法，解析器必须实现这些方法才能提供这类记录。新的记录类型可以随时通过 EIP 标准化程序进行定义，因此不需要为了支持它们而对 ENS 注册表或现有的解析器进行更改。

在 ENS 中解析一个域名需要两个步骤：首先，询问注册表是哪个解析器负责解析该域名，然后，向该解析器查询解析结果。

![](/images/docs/ens-resolving.png)

在上面的例子中，我们想找到 “foo.eth” 指向的以太坊地址。首先，我们询问注册表是哪个解析器负责解析 “foo.eth” ；然后，我们向该解析器查询 “foo.eth” 的地址。

### Namehash

智能合约中的资源限制使得直接与可读的域名交互效率低下，因此ENS只使用固定长度的256位加密哈希。为了从域名生成哈希的同时仍然保留其层次性，ENS 使用了名为 Namehash 的算法。例如，“alice.eth” 的 Namehash 为 _0x787192fc5378cc32aa956ddfdedbf26b24e8d78e40109add0eea2c1a012c3dec_ ，Namehash 只是用来在 ENS 内部表示域名。

Namehash 是一个递归过程，可以为任何有效的域名生成唯一的哈希。从任意一个域名的 Namehash 开始（比如 “alice.eth” 的 Namehash）可以推导出任意子域名的 Namehash（比如 “iam.alice.eth” 的 Namehash），而且推导过程中不需要知道或处理 “alice.eth” 这个可读的原始域名。正是这个特性使得 ENS 能够成为一个层次性的系统，且不必在内部处理可读的文本字符串。

在使用 Namehash 进行哈希之前，首先需要借助 UTS-46 标准对域名进行规范化，确保域名中的字母与大小写无关，并禁止使用无效字符。任何对域名进行哈希和解析的操作都 **必须** 首先对其进行规范化，以确保所有用户获得 ENS 的一致性。

有关 Namehash 和规范化如何工作的详细信息，请参阅有关 [域名处理](contract-api-reference/name-processing.html) 的文档。

## 开始使用

ENS 为包括 dapp 开发者和合约开发者在内的各种人员提供参考文档。

#### 我是 dapp 的开发者，我想为我的 dapp 添加 ENS 支持

从 [在 Dapp 中启用 ENS](dapp-developer-guide/ens-enabling-your-dapp.html) 开始，查看 dapp 开发者指南，你可以从众多可用的 [ENS 库](dapp-developer-guide/ens-libraries.html) 中选择一个来开始使用 ENS 。

#### 我是一名合约开发者，希望在我的智能合约中与ENS进行交互

从 [链上域名解析](contract-developer-guide/resolving-names-on-chain.html) 开始，查看合约开发者指南。你还可以 [编写自己的解析器](contract-developer-guide/writing-a-resolver.html)（自定义查询域名的过程）或自己的 [注册器](contract-developer-guide/writing-a-registrar.html)（自定义注册新域名的过程）。

#### 我想查看 ENS 智能合约的参考文档

查看 ENS 智能合约的 API 参考文档，这些文档涵盖了 ENS 的核心合约、[注册表](contract-api-reference/ens.html)、[解析器](contract-api-reference/publicresolver.html) 和常用的注册器，如：[测试注册器](contract-api-reference/testregistrar.html)、[反向注册器](contract-api-reference/reverseregistrar.html) 以及 [.eth注册器](contract-api-reference/eth-permanent-registrar/readme.html)。

---
title: 有没有人能拿走我的 .eth 名称
date: 2022-11-27 13:30:00
categories: 观点与评论
original: https://mirror.xyz/liubenben.eth/l2s-i6kQSuKVe6VbN_LMescNj_FMKLw2ql32bxCVZNI
author: liubenben.eth
translator: 
description: 
---

![](/images/news/2022-11-27-no-one-could-take-your-ens-away/01.jpg)

## 没有人能拿走你的 .eth 名称

很多人可能听说过这样的说法：没有人能拿走你的 .eth 名称。

是的。在我看来，一把“软锁”和一把“硬锁”的双重锁定确保了 **“没有人能拿走你的 .eth 名称”**。

### 软锁

ENS DAO 在发布之初，颁布了 [ENS 章程](https://constitution.ens.domains/)，并且每个参与代币领取的人都对章程中的几条准则进行了投票。

ENS 章程第一条内容如下：

> **Name ownership shall not be infringed**
> ENS governance will not enact any change that infringes on the rights of ENS users to retain names they own, or unfairly discriminate against name owners’ ability to extend, transfer, or otherwise use their names.

主要意思就是：**ENS 名称所有权不受侵犯**，ENS DAO 不会制定任何侵犯 ENS 名称所有权的提案，也不会干涉用户续期、转让或以其他方式使用自己的名称。

这条章程以超高的得票率获得通过。由此给 .eth 名称的所有权加上了一把共识层面的“软锁”。

有趣的是，在少量不支持这项章程的投票中，大多数竟然是由于没有正确理解当时的投票逻辑而导致的误投，没有亲身经历过的朋友可能很难理解当时的状况 —— 时间常常会把过去的事故变成后来的故事。

另外延伸一点，我认为名称所有权不受侵犯，除了要保护这个名称对应的 NFT 实体的产权，还应当保护 ENS 名称作为一种数字身份的声誉不受侵犯。冒充他人的数字身份，同样会对名称的所有权构成挑战。ENS 社区已经在开展这方面的研究，有兴趣的可以看看 [jefflau.eth](https://twitter.com/_jefflau) 的文章《[Decentralised Blue Check](https://mirror.xyz/jefflau.eth/0PMm9s7G_a9KpXiYGnfWPuhNntZqJFlpmuVlSJbCD6s)》和 [luc.computer](https://twitter.com/LucemansNL) 写的应用程序 [vrfd.app](https://vrfd.app/)。

### 硬锁

单单通过 ENS 章程来维护 .eth 名称所有权的软锁，显然还不够可靠，毕竟理论上存在通过 DAO 投票更改章程的可能。所以，还有一把智能合约代码层面的“硬锁”来全面保证 .eth 名称所有权不受侵犯。

ENS 协议相对于普通的 NFT 项目来说，相对复杂得多（协议的可视化解读可以参考 [avsa.eth](https://twitter.com/avsa) 的[这篇帖子](https://discuss.ens.domains/t/ens-proposals-explained-visually/10895)）。这里我们只关注 .eth 二级域名（类似 `alice.eth`）所有权的保护，从协议代码里锁住所有权需要自上而下的几个必要条件：

1. 首先 ENS 根域的管理权要锁定。
2. 其次 .eth 顶级域的管理权要锁定。
3. 最后 .eth 顶级域的管理者无法改变其任何子域的所有权。

#### 1. ENS 根域的管理权限锁定

ENS 系统的根域由 [ENS 根合约](https://etherscan.io/address/0xaB528d626EC275E3faD363fF1393A41F581c5897) 负责管理，这个管理权限是记录在 ENS 注册表里的。通常要想在注册表里更改管理权，有两种方法：1）由父域重置，2）由原管理者转让。但两者都行不通：

- 1）根域是没有父域的，所以不能通过父域来重置。
- 2）ENS 根合约里没有设置委托函数，不能调用注册表里的转让操作，所以也不能转让其对根域的管理权。

#### 2. .eth 顶级域的管理权限锁定

.eth 顶级域的管理权限同样记录在 ENS 注册表里。前面说到，要想在注册表里更改管理权，有两种方法：1）由父域重置，2）由原管理者转让。这两种方法对于 .eth 同样行不通：

- 1）.eth 这个顶级域的父域是由 ENS 根合约管理的根域，ENS 根合约里有一个锁定函数专门用于锁定某个顶级域。2021 年 5 月 ENS 团队在 ENS Root 合约中将 .eth 这个顶级域的管理者进行了[锁定操作](https://etherscan.io/tx/0x636abee238e85eedd8dc3b5e4584ace2e00452e65fca1631f36cf5c4a96bcacc#eventlog)，自此，ENS 根合约作为 .eth 的父域已经无法再更改 .eth 顶级域的管理权，.eth 顶级域的管理者被永远地锁定在了 [.eth 基础合约](https://etherscan.io/address/0x57f1887a8BF19b14fC0dF6Fd9B2acc9Af147eA85)。
- 2）.eth 基础合约里没有设置委托函数，不能调用注册表里的转让操作，所以也不能转让其对 .eth 顶级域的管理权。

#### 3. .eth 基础合约无法改变任何子域的所有权

[.eth 基础合约](https://etherscan.io/address/0x57f1887a8BF19b14fC0dF6Fd9B2acc9Af147eA85)是 .eth 的管理者，同时也是符合 ERC721 的 NFT 合约，它负责分发和管理 .eth 二级域名。作为一个稳定运行多年，且代码开源的智能合约，我们可以从[其合约代码](https://github.com/ensdomains/ens-contracts/blob/master/contracts/ethregistrar/BaseRegistrarImplementation.sol)中轻松验证它和它的管理员都无法改变 .eth 任何子域的所有权。

### 两把锁的关系

ENS 章程的通过于否是 DAO 以投票方式决定的，是软锁，是社区共识；智能合约代码的锁定是写在链上无法变更的，是硬锁，是机器行为。从时间上来说，代码锁定在前（2021.5），DAO 组建在后（2021.11），章程的第一条更像是给 .eth 所有权的锁定争取一个共识结果。无论如何，当前的情况是软锁与硬锁高度一致，因此我们说：“没人能拿走你的 .eth 名称”。

### ENS DAO 可以动摇 .eth 名称的所有权吗

不能。通过 DAO 投票只是有可能解开“软锁”，但解不开“硬锁”。DAO 的治理内容不是无限的，必须在智能合约允许的范围内实施，比如 DAO 可以管理[资金库](https://etherscan.io/address/0xFe89cc7aBB2C4183683ab71653C4cdc9B02D44b7)的使用，可决定名称的注册价格，可以修改基础合约的[控制器合约](https://github.com/ensdomains/ens-contracts/blob/master/contracts/ethregistrar/ETHRegistrarController.sol)，可以治理很多事情，但它不能改变 .eth 名称的所有权。

## 我们需要注意什么

尽管我们可以自信地说：“没有人能拿走你的 .eth 名称”，但过去几年，失去名称的情况并不少见，要避免失去自己的 .eth 名称至少需要注意以下两点：

1. 避免名称过期。几乎每天都有 .eth 名称过期释放，其中不乏一些因为忘记续费而导致过期的优质名称。强烈建议通过 [ENS APP](https://app.ens.domains/) 给自己的名称设置续费提醒，或是通过诸如 [PUSH](https://push.org/) 的服务来添加提醒。
2. 避免名称被盗。.eth 名称是符合 ERC721 标准的 NFT 数字资产，并且价值共识越来越强烈，盗窃 .eth 名称的手法只会越来越多，并且一旦被盗，即使是 ENS DAO 也无能为力。安全管理会是加密生活中永不结束的一堂课，记得重温《[区块链黑暗森林自救手册](https://darkhandbook.io/)》。

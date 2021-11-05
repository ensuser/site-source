---
title: ENS 2020 年回顾展
date: 2021-01-28 13:00:00
categories: 官方文章
original: https://medium.com/the-ethereum-name-service/2020-retrospective-for-ens-7c5364142560
author: Brantly
translator: 刘笨笨
description: 
---

![](/images/news/2021-01-28-2020-retrospective-for-ens/01.jpeg)

2020 年对于以太坊域名服务（ENS）来说是重要的一年！我们修复了一个核心智能合约中的一个错误，扩展了 ENS 域名的功能，并且使用率得到了大幅增长。总结如下：

## 错误修复

ENS 以一次重要事件拉开 2020 年的序幕：我们[公开宣布](https://ensuser.com/news/2020-01-31-ens-registry-migration-bug-fix-new-features.html) Sam Sun 在 ENS 注册表智能合约中发现了一个错误。所有域名都是安全的，据我们所知，该漏洞从未被利用。尽管如此，我们的团队花了三个月的紧张工作才能解决所有问题。我们在三月份发布了完整的修复报告。幸运的是，我们正好借此机会向 ENS [添加了新功能](https://ensuser.com/news/2020-01-31-ens-registry-migration-bug-fix-new-features.html)，从而简化了注册和设置 .ETH 域名的过程。

## 整合

域名系统的作用与使用它的应用程序数量密切相关。2020 年开始时，我们进行了约 86 项集成，而 2019 年 6 月时还不到 30 项集成。到 2020 年 7 月，我们实现了 [150 项集成](https://medium.com/the-ethereum-name-service/150-integrations-what-this-milestone-means-for-ens-7489b1ae405b)，截至本文撰写时，我们已有 172 项集成。感谢所有社区的支持！

您可以在[我们网站](https://ens.domains/)的 “ENS Ecosystem” 部分中看到完整的集成列表。这些集成包括 Coinbase、Trust Wallet 和 My Crypto 等钱包；诸如 Uniswap、Tornado Cash 和 The Graph 等 dapp；以及 Brave、Opera 和 MetaMask 扩展程序之类的浏览器。

![](/images/news/2021-01-28-2020-retrospective-for-ens/02.png)

您可以单击我们网站上的 “See More +” 来浏览完整列表。

## ENS 研讨会

我们在 9 月举行了[年度 ENS 研讨会](https://medium.com/the-ethereum-name-service/ens-online-workshop-2020-recap-ceb73363ef0b)。以前，我们在伦敦（两次）和大阪举行过展览，但今年是虚拟展览。我们的投票率很高，共有约 40 名参与者代表了区块链行业的顶级项目，甚至包括 DNS 界人士。Vitalik 提出了扩展 ENS 的提案，此提案后来演变为我们[当前的第 2 层计划](https://medium.com/the-ethereum-name-service/a-general-purpose-bridge-for-ethereum-layer-2s-e28810ec1d88)。您可以观看讨论的视频，并阅读此处发生的摘要。

![](/images/news/2021-01-28-2020-retrospective-for-ens/03.png)

## 去中心化网络的发展

第一个收录 ENS + IPFS 网站的搜索引擎/目录，叫 [Almonit](http://almonit.eth.link/)，它在 2020 年 1 月推出。您可以通过 almonit.eth（或 [almonit.eth.link](http://almonit.eth.link/)）访问它，并[在其 “Discover” 页面上浏览其去中心化网站的目录](https://almonit.eth.link/#/discover/)。

浏览器支持进展顺利！现在，您可以在 Brave、Opera、Status、MetaMask 手机版，以及支持 MetaMask 扩展的任何浏览器上访问 ENS + IPFS 网站。IPFS 服务现在同样支持 ENS。

[Fleek](https://fleek.co/) 推出了一项出色的服务，使用户能够将新的代码合并后自动部署到 IPFS 并更新其 ENS 域名记录。我们很快会对此提供更多信息！

4 月份，ethereum.org 团队的网站也在 ethereum.eth（或 [ethereum.eth.link](https://ethereum.eth.link/)）上发布了 [ENS + IPFS 版本](https://twitter.com/samonchain/status/1247229402431119360)。

![](/images/news/2021-01-28-2020-retrospective-for-ens/04.jpeg)


## 大规模续费

在 2019 年 5 月，.ETH 域名从拍卖、锁定和返还资金模型转换为即时注册和年费支出模型。在此之前存在的所有 .ETH 域名都将免费获得第一年的使用权，这意味着它们将在 2020 年 5 月到期。此外，系统内还有一个内置的 90 天宽限期，原所有者仍可以在其宽限期内续费，此后该域名最终释放给他人注册，这意味着未更新的域名将在 2020 年 8 月被释放。

为了帮助人们进行这种过渡，我们让 .ETH 域名的[续费](https://ensuser.com/guides/renew.html)（包括批量续费）变得更加容易，而且可以选择添加电子邮件和日历提醒。我们创建了一个可以嵌入 dapp 的小部件，该部件可以提醒用户更新他们的 ENS 域名，甚至可以与 OpenSea 和 imToken 等 dapp 一起向用户发送续费提醒的电子邮件。

2020 年 8 月释放了大约 [28万个.ETH 域名](https://ensuser.com/news/2020-07-21-a-look-at-the-280k-eth-names-set-to-become-available-august-2nd.html)。为防止疯狂抢购和 “Gas 费拍卖”（支付最高 Gas 费的人赢得注册，而且当时的 Gas 费已经很高了），我们为新释放的域名增加了[溢价递减机制](https://ensuser.com/news/2020-07-16-new-decaying-price-premium-for-newly-released-names.html)。

## L2 计划

Vitalik 提出了一种使 ENS 的一部分与 L2 兼容的方法，并在我们的年度研讨会上作了介绍。我们举行了一些社区会议来讨论。然后，我们的首席开发人员尼克·约翰逊（Nick Johnson）根据这些讨论提出了一项计划，希望在 2021 年底之前实施。

## 其他新功能

- 现在，ENS 主页和 [ENS APP](http://app.ens.domains/) 都[支持 10 种语言](https://medium.com/the-ethereum-name-service/ens-is-now-in-10-languages-86e5cb529ddd)。
- 我们现在有[专用的工具](https://medium.com/the-ethereum-name-service/how-to-get-back-an-old-deposit-1e2b1767b930)，可用来回收在 2019 年 5 月之前系统中注册的 .ETH 域名的押金。
- ENS APP 现在支持[自定义文本记录](https://ensuser.com/news/2020-03-20-new-custom-text-records.html)，这意味着用户或项目可以使用 ENS 来存储任意数据。
- 我们将[地址编码库中支持的区块链数量](https://medium.com/the-ethereum-name-service/ens-now-supports-109-blockchain-addresses-5307ce5d2106)大大扩展到 110 多个。我们现在认为，对多币种的支持足够深，可以包含绝大多数用户希望使用其 ENS 域名接收的几乎所有区块链资产。
- 我们扩大了用户将其钱包连接到 ENS APP 的方式，而不仅仅是 MetaMask 这样的注入提供商，现在还包括 WalletConnect、Portis、Torus、Authereum 和 MEW 钱包。
- 我们发布了 JS 库 [ensjs](https://www.npmjs.com/package/@ensdomains/ensjs) 的 2 版本。目前已经有 [12 个顶级库](https://ensuser.com/docs/dapp-developer-guide/ens-libraries.html)支持 ENS，但不是所有的库能够全方位的支持所有 ENS 功能。我们的库支持大多数操作。
- 用户现在使用 ENS APP，可以通过一笔交易来设置多个记录。

## 其他值得注意的事情

- 我们彻底更新 ENS 主页。
- 我们启用了[电子邮件订阅](https://ensdomains.substack.com/p/coming-soon)。通过订阅可以很好地接收 ENS 的定期更新。
- 我们与 MakersPlace 举行了 [ENS 主题的 NFT 艺术竞赛](https://medium.com/the-ethereum-name-service/the-winners-of-the-ens-makersplace-nft-art-competition-how-you-can-get-them-e9ce45c3dda)。
- 我们出席了基于 VR 的会议 ETHVR0。
- Rocket LP DAO 发行了[全球首个 ENS 域名背书的贷款](https://ensuser.com/news/2020-04-18-the-world-first-ens-backed-loan-with-rocket-lp-dao.html)。
- DNS TLD [.KRED 启动了 DNS-ENS 的深度集成](https://medium.com/the-ethereum-name-service/ens-kred-major-integration-of-dns-and-ens-launches-e7efb4dd872a)。
- 我们通过[将 depositcontract.eth 指向 ETH2 存款合同和 Web UI](https://medium.com/the-ethereum-name-service/how-to-access-the-eth2-deposit-contract-with-ens-de9e266cc857) 来支持 ETH2 的启动。
- 我们更新了 [DNS-OARC 的 Blue Membership 资格](https://medium.com/the-ethereum-name-service/ens-joins-dns-operations-analysis-and-research-center-a5781cdde805)，并于 2 月在 COVID-19 封闭之前[出席了他们上一次的现场研讨会 OARC 32](https://youtu.be/5math9Oy97s?t=2917)。
- 我们解释了[为什么 ENS 使用以太坊和 ETH](https://ensuser.com/news/2019-02-12-why-ens-uses-ethereum-and-eth-not-a-bespoke-blockchain-and-token.html)，而不使用定制的区块链和代币。
- 我们将 ENS 使用的[价格预言机切换为 Chainlink 的 ETH-USD 预言机](https://ensuser.com/news/2020-07-15-ens-integrates-chainlink-eth-usd-price-oracle.html)，并且 Chainlink 开始使用 data.eth 作为其价格预言机的去中心化地址表。

![](/images/news/2021-01-28-2020-retrospective-for-ens/05.png)

## 2021 年 1 月

由于我们将于 2021 年 1 月底在发布 2020 年回顾展，因此我也将提及本月的一些更新。

- [我们与 Cloudflare 合作，用其进行 eth.link 的 ENS + IPFS 网关服务的后端管理](https://ensuser.com/news/2021-01-14-ens-partners-with-cloudflare-on-improved-eth-link-service.html)，该服务不仅可以提供更好的正常运行时间和可扩展性，而且通过该服务访问的所有站点现在都将具有 HTTPS。
- 我们更新了[我们的 Twitter 机器人](https://twitter.com/EnsBot)，对于那些在 Twitter 用户名包含 .ETH 域名的用户，[机器人能够在他们的域名急需续费时 @ 他们](https://medium.com/the-ethereum-name-service/ensbot-will-now-tweet-at-you-to-renew-that-eth-name-in-your-twitter-profile-9d51c0502f18)。
- 现在，我们[扩展的 DNS 域名空间集成位于 Ropsten 测试网上](https://ensuser.com/news/2021-01-22-dns-namespace-integration-on-testnet-ethereum-classic-labs-sponsors-with-grant.html)。我们非常感谢[以太坊经典实验室](https://etclabs.org/)为这项功能的研究工作提供了赞助。我们希望尽快部署到主网，敬请期待。

![](/images/news/2021-01-28-2020-retrospective-for-ens/06.jpeg)

## 继续前进

2021 年对于我们来说，一个重要的项目将是实施 L2 计划，这将使用户能够将解析记录和子域名放在他们选择的 L2 上，从而大大降低了这些操作和管理的 Gas 成本。我们还计划改善子域名服务和 ENS APP 的功能，当然还支持集成数量的持续增长。

您可以在我们的论坛上阅读和讨论[我们的 2021 年路线图](/news/2021-01-30-2021-ens-roadmap.html)。ENS 一直是开源和社区驱动的项目。我们非常感谢大家提供的所有帮助，建议和贡献！

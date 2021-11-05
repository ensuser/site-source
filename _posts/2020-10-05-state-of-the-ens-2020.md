---
title: ENS 在 2020 年的各种情况
date: 2020-10-05 13:00:00
categories: 官方文章
original: https://medium.com/the-ethereum-name-service/state-of-the-ens-2020-cd8afa19f59d
translator: 闵敏 & 阿剑
description: 
---

![](/images/news/2020-10-05-state-of-the-ens-2020/01.png)

自 ENS 于 2017 年上线以来，Nick Johnson 依照惯例会在每年的 Devcon 上发表关于 “ENS 现状” 的演讲。遗憾的是，今年的 Devcon 未能举办，因此我们将简要介绍一下自 [DevCon 5](https://slideslive.com/38920137/state-of-the-ens-2019) 以来 ENS 的情况。

## 总体数据

![](/images/news/2020-10-05-state-of-the-ens-2020/02.png)

- 域名总量 = 31 万 → 19 万
- 整合 ENS 的去中心化应用和钱包的数量 = 28 → 158
- 支持币种数量 = 10 → 27
- ENS APP 支持的语种数量 = 1 → 10
- （2019 年 10 月 1 日以来）通过注册和更新以太坊域名获得的总收入 = 5513 ETH（[注册费共计 3982 ETH](https://explore.duneanalytics.com/queries/10007) + [更新费共计 1531 ETH](https://explore.duneanalytics.com/queries/10008)）

你可能已经注意到了，域名总量已经从 31 万骤降至 19 万。这是因为 ENS 变成年费模式后，很多域名已经过期。这使得其他用户可以注册未使用的域名，因此这不一定是件坏事。

你可以在我们的 Dune Analytics 网站上看到其它关键数据：

![](/images/news/2020-10-05-state-of-the-ens-2020/03.png)

- [ENS 控制面板](https://explore.duneanalytics.com/public/dashboards/mCN5vXDfnOMqqpjPnGVgaKsnD1xdrP7eldkyJhRA)
- [.eth 注册失败率](https://explore.duneanalytics.com/public/dashboards/48pBVvSxRNVjSE8Ing1uOrCtjD4r3WmV0v5KpS05)
- [.eth 收入](https://explore.duneanalytics.com/public/dashboards/WggYyQMJZDsO5FgJQFTqQ6H7DnjhQ2PH7D9DVjov)

## 2019 年第 4 季度和 2020 年第一季度

DevCon5 和 Nick 的婚礼结束后，我们马上收到了关于我们的 ENS 注册表的安全漏洞报告，我们也因此必须在 90 天内实行大规模迁移（以符合安全披露标准）。3 个月里面，我们不得不秘密行动，筹备以太坊历史上最大的一次非计划迁移（或许 Augur 那次更大……）。见：

- [ENS 注册器迁移：修复漏洞、增加功能](https://ensuser.com/news/2020-01-31-ens-registry-migration-bug-fix-new-features.html)
- [来谈谈 ENS 迁移](https://medium.com/the-ethereum-name-service/lets-talk-ens-migration-a92d5c21df28)

> 考虑到需要更新的内容，我们将会修改大约 84.7 万个存储 slot。这需要消耗 16,940 Mgas，以 1 Gwei 的价格来算，，迁移成本约为 17 ETH，或 3049 美元（ETH 单价按 180 美元计，不包括间接费用）。最后，我们的成本约为 140 ETH，以每个 ETH 180 美元的价格来算，总迁移成本为 2.52 万美元。
> ——摘自《来谈谈 ENS 迁移》

如果现在进行一场同样规模的迁移，以 ETH 的现价（高达两倍）来算，gas 成本约为 500 gwei。不敢想象。

第一季度正处于新冠疫情爆发时期。我们参加了以太坊在伦敦举办的黑客松以及在巴黎举办的以太坊社区会议（Ethcc），后者是最后仅存的几场线下活动之一。见：《[激进域名、PingENS 还有 ewallet：了解 ETHLondon 2020 的 ENS 优胜奖](https://medium.com/the-ethereum-name-service/radical-domains-pingens-ewallet-meet-the-ens-winners-at-ethlondon-2020-abdf65bc505)》

## 2020 年第二季度

5 月 4 日（愿原力与你们同在！）是 ENS 上线的三周年纪念日，也是 ENS 通过永久注册器迁移从押金模式转变为年收费模式的一周年纪念日。见：《[The Great Renewal：是时候更新你们的 ENS 域名啦](https://medium.com/the-ethereum-name-service/the-great-renewal-its-time-to-renew-your-eth-names-or-else-lose-them-afccea4852cb)》

我们与外部服务提供商合作，帮助人们了解续订并设置通知。见：

- [从 BUIDLHub 的新工具处获得 ENS 域名续订邮件提醒](https://medium.com/the-ethereum-name-service/receive-email-notifications-to-renew-your-eth-names-with-new-tool-from-buidlhub-72aaba226194)
- [把我们的 ENS 续订提醒插件添加到您的网站上，赚取手续费收入](https://medium.com/the-ethereum-name-service/earn-referral-fees-by-adding-our-ens-renewal-reminder-widget-to-your-website-575c23ef6500)

再说回参会情况，当时大多数国家都处于封锁阶段，因此很多线下见面会都改成了虚拟会议。我们参加了 HackMoney 的线上黑客松，使用 Oculus 出席了 ETHVR 见面会，并参加了 CryptoVoxels 的 WIP 见面会。见：

- [Umbra、对 VS Code 的 DeFi 支持，等等：了解 HackMoney 的 ENS 优胜奖](https://medium.com/the-ethereum-name-service/umbra-defi-support-for-vs-code-more-meet-the-ens-winners-of-hackmoney-f1edfdbe8d12)
- [我们在 ETHVR0 上的见闻：这是一次基于 VR 的以太坊见面会](https://medium.com/the-ethereum-name-service/what-i-learned-at-ethvr0-dispatches-from-a-vr-based-ethereum-meetup-46a536d3b5a5)

![](/images/news/2020-10-05-state-of-the-ens-2020/04.png)

再提一件私事，Nick 和他的夫人 Julia 生了个漂亮的女儿！

此后，Nick 开始秀起了令人叹为观止的 DIY 技能。

## 2020 年第三季度

我们预计，到 8 月 2 日会有近 2.8 万个域名被释放。为了防止 “耕者无其田”，我们引入了长达 28 天的 “溢价递减机制”。在这段时间内，已释放的域名按溢价出售，并逐步降低。

- [为最近放出的域名安排递减溢价机制](https://ensuser.com/news/2020-07-16-new-decaying-price-premium-for-newly-released-names.html)
- [放出了 28 万个域名之后，实施溢价递减机制的结果](https://medium.com/the-ethereum-name-service/the-results-of-the-decaying-price-premium-after-releasing-280k-names-7cf57e46b204)

在流动性挖矿进行得如火如荼时，这场域名释放活动开始了。

我一直在密切关注我们的日增长量，尽管 gas 成本很高，但还是有很多人注册并续订域名。

![](/images/news/2020-10-05-state-of-the-ens-2020/05.png)

我们还达到了一些有趣的里程碑。

我们不仅达到了整合量 150 的里程碑，还为我们的 ENS 应用新增了 10 种不同的语言，均由我们的社区成员翻译。

- [150 个服务集成 ENS：ENS 的重大里程碑](https://medium.com/the-ethereum-name-service/150-integrations-what-this-milestone-means-for-ens-7489b1ae405b)
- [ENS 现在支持 10 种语言了](https://medium.com/the-ethereum-name-service/ens-is-now-in-10-languages-86e5cb529ddd)

至于活动方面，ENS 继续参与 ETHGlobal HackFS 活动，以及一些集成了 ENS 的有趣的黑客活动。 见：《[RicLoo、Web3API，等等：了解 HackFS 的 ENS 优胜奖](https://medium.com/the-ethereum-name-service/ricloo-web3api-more-meet-the-ens-winners-of-hackfs-7574975c9efd)》

## 2020 年第四季度及以后

你可能会想知道，我们目前正在开发哪些功能。

关于我们的每日任务，你可以在[我们的 Github 上的双周任务板块上看到](https://github.com/orgs/ensdomains/projects/1)。

![- https://github.com/orgs/ensdomains/projects/1 -](/images/news/2020-10-05-state-of-the-ens-2020/06.png)

我们的重点主要放在 DNSSEC 集成、bug 修复以及改进我们的 ENS APP 应用上。

从更长远的目标来看，我们会在即将举行的 ENS 研讨会上展开讨论。如果有任何你想讨论的话题，请参与我们的[讨论频道](https://discuss.ens.domains/c/workshop2020/8)。

我们作为赞助商参加了 ETHGlobal 即将举办的[以太坊线上黑客松](https://ethonline.org/)。

如果你有任何点子，请在我们的[讨论板](https://discuss.ens.domains/c/ens-hackathon-ideas/9)上留下建议。

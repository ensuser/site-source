---
title: 有 28 万个 .ETH 域名将会在 8 月 2 日被释放 ，让我们一起来看看
date: 2020-07-21 12:00:00
categories: 官方文章
original: https://medium.com/the-ethereum-name-service/a-look-at-the-280k-eth-names-set-to-become-available-august-2nd-e135ad23a05c
translator: 刘笨笨
description: 
---

ENS 域名从今年 5 月 4 日开始过期（开始为期 90 天的宽限期）以来，我们引入了一系列新功能来鼓励人们在 8 月 2 日（过期域名被释放的日期）之前进行续费。自今年年初以来，目前已有超过 2 万个域名进行了续费（总持续时间为 28000 年！），但根据 Dune Analytics 的数据，仍然有将近 28 万个域名（.ETH 域名总共超过35万个）需要续费。

预计会产生大量未续费的域名。早在 2019 年，在过渡到使用年租金的永久注册器之前，我们估计（当时的 27 万个域名中）82％ 的域名已被抢注。

![](/images/news/2020-07-21-a-look-at-the-280k-eth-names-set-to-become-available-august-2nd/01.png)

上图出自我的一个演讲：[ENS 通往永久注册器之路](https://speakerdeck.com/makoto_inoue/ens-the-road-to-the-permanent-registrar?slide=15)

借助 [Dune Analytics](https://www.duneanalytics.com/) 提供的数据，我们分析了大约 2 万 3 千个域名，这些域名的拍卖价格超过了 2017 年首次拍卖时的最低拍卖价。您通过 Dune Analytics 网站可以很容易地 [搜索出哪些域名是以什么价格成交的](https://explore.duneanalytics.com/embed/query/3630/visualization/7042?api_key=XUvG7yoTgPVr45HX7Bv61LWnCpjXonKQgC4js3VE)。

![](/images/news/2020-07-21-a-look-at-the-280k-eth-names-set-to-become-available-august-2nd/02.png)

即将在 8 月 2 日被释放的 ENS 域名清单将按 2017 年的拍卖价格进行排序。

就像 [我在短域名拍卖中](https://medium.com/the-ethereum-name-service/the-most-popular-eth-names-in-the-ens-short-name-auction-final-5d3466dd8837) 所做的那样，我通过分析来了解人们所认可的高价值域名（因此值得抢注）。

### 互联网公司 500 强

我使用了与短域名拍卖的相同数据集，并且确定了 74 个域名，其中出价最高的前 10 个域名如下所示。

``` txt
╔═══════╦═══════════════════════╦════════════╗
║ rank  ║       name            ║  bid (ETH) ║
╠═══════╬═══════════════════════╬════════════╣
║     1 ║ alibaba.eth           ║ 301.0      ║
║     2 ║ dropbox.eth           ║ 206.0      ║
║     3 ║ harvard.eth           ║ 188.0      ║
║     4 ║ android.eth           ║ 132.0      ║
║     5 ║ washington.eth        ║ 111.111    ║
║     6 ║ pinterest.eth         ║ 76.23      ║
║     7 ║ princeton.eth         ║ 57.58      ║
║     8 ║ wordpress.eth         ║ 43.8652    ║
║     9 ║ marriott.eth          ║ 36.5       ║
║    10 ║ huffingtonpost.eth    ║ 30.0       ║
╚═══════╩═══════════════════════╩════════════╝
```

与亚马逊竞标 100 ETH 的短域名拍卖相比，这些域名的价格看起来非常高。但是，以前的拍卖是一种锁定资金的模式，在这种模式下，用户在释放域名时可以取回锁定的资金，而短域名的拍卖则是一种消费。域名所有者在 “释放” 域名时可以收回锁定的 ETH。曾经有超过 17.3 万个以太币（价值 4000 万美元）用于域名拍卖而被锁定，其中 5.3 万个以太币已经被收回。这说明还有 12 万个以太币（价值 2900 万美元）仍被锁定，并等待收回。

### 加密企业

我通过流行的 dapp 注册中心（dap.ps 和 everest.link）提取了 299 个加密公司，并匹配出了 25 个域名，下面我将在此处列出这些域名。

``` txt
╔══════╦═════════════════════╦═══════════╗
║ rank ║        name         ║ bidprice  ║
╠══════╬═════════════════════╬═══════════╣
║    1 ║ polkadot.eth        ║ 16.67     ║
║    2 ║ memefactory.eth     ║ 12.6      ║
║    3 ║ ethlance.eth        ║ 12.16     ║
║    4 ║ makerdao.eth        ║ 2.22      ║
║    5 ║ everest.eth         ║ 2.22      ║
║    6 ║ openzeppelin.eth    ║ 1.24      ║
║    7 ║ blockfolio.eth      ║ 1.12      ║
║    8 ║ paradigm.eth        ║ 1.0       ║
║    9 ║ blockgeeks.eth      ║ 0.9       ║
║   10 ║ ethereumclassic.eth ║ 0.895     ║
║   11 ║ kickback.eth        ║ 0.53      ║
║   12 ║ etherbots.eth       ║ 0.5       ║
║   13 ║ playtime.eth        ║ 0.33      ║
║   14 ║ compound.eth        ║ 0.117     ║
║   15 ║ theblock.eth        ║ 0.1       ║
║   16 ║ ethercards.eth      ║ 0.05      ║
║   17 ║ ethlend.eth         ║ 0.05      ║
║   18 ║ etheremon.eth       ║ 0.05      ║
║   19 ║ gridplus.eth        ║ 0.05      ║
║   20 ║ livepeer.eth        ║ 0.0115    ║
║   21 ║ decentraland.eth    ║ 0.0112    ║
║   22 ║ betoken.eth         ║ 0.0110001 ║
║   23 ║ messari.eth         ║ 0.01      ║
║   24 ║ whiteblock.eth      ║ 0.01      ║
║   25 ║ dragonereum.eth     ║ 0.01      ║
╚══════╩═════════════════════╩═══════════╝
```

实际上，其中有几个域名设置了解析器，表明它们实际上可以使用。如果您是这些域名的所有者，请确保您已经续费（我通知了一些团队，因此在本文章发布之时，它们可能已经续费了）。

### 名人

我从 ["Art of memory"](https://forum.artofmemory.com/t/here-is-a-list-of-famous-people/26898/20) 网站下载了 4000多个名人的名单，并进行了 147 次匹配。该数据集比较老旧（2011 年），因此不包括较新的名人。尽管如此，人们认为可以将域名卖给叫做 `tyalorswift.eth` 和 `donaldtrump.eth` 的人，这很有趣。

``` txt
╔══════╦════════════════════════╦═════════════╗
║ rank ║          name          ║  bidprice   ║
╠══════╬════════════════════════╬═════════════╣
║    1 ║ michael.eth            ║ 50.0        ║
║    2 ║ taylorswift.eth        ║ 34.7623     ║
║    3 ║ justinbieber.eth       ║ 32.8751     ║
║    4 ║ lebronjames.eth        ║ 16.8571     ║
║    5 ║ kimkardashian.eth      ║ 16.852      ║
║    6 ║ mileycyrus.eth         ║ 16.42       ║
║    7 ║ phoenix.eth            ║ 14.0        ║
║    8 ║ magicjohnson.eth       ║ 12.88       ║
║    9 ║ jackson.eth            ║ 12.31       ║
║   10 ║ donaldtrump.eth        ║ 11.0        ║
╚══════╩════════════════════════╩═════════════╝
```

### 常用关键字

由于最初拍卖时字符长度限制，许多人为了使长度超过 6 个字符，便竞拍了单词组合。以下是域名中经常出现的关键字的列表。

``` txt 
╔══════╦════════╦════════════╦════════════════════╗
║ rank ║  name  ║ occurrence ║ combined bid price ║
╠══════╬════════╬════════════╬════════════════════╣
║    1 ║ coin   ║        219 ║ 271.82700          ║
║    2 ║ the    ║        205 ║ 96.70683           ║
║    3 ║ crypto ║        158 ║ 92.45264           ║
║    4 ║ pay    ║        149 ║ 140.08028          ║
║    5 ║ group  ║        135 ║ 69.48181           ║
║    6 ║ chain  ║        132 ║ 81.12947           ║
║    7 ║ bit    ║        114 ║ 293.23015          ║
║    8 ║ eth    ║        110 ║ 332.43770          ║
║    9 ║ smart  ║        109 ║ 299.77311          ║
║   10 ║ block  ║        101 ║ 21.22122           ║
║   11 ║ token  ║        100 ║ 29.34514           ║
║   12 ║ china  ║         98 ║ 47.84436           ║
║   13 ║ world  ║         97 ║ 43.66079           ║
║   14 ║ wallet ║         97 ║ 1321.21824         ║
║   15 ║ market ║         91 ║ 10233.27795        ║
║   16 ║ and    ║         88 ║ 27.96061           ║
║   17 ║ man    ║         86 ║ 17.51550           ║
║   18 ║ ether  ║         84 ║ 137.29072          ║
║   19 ║ online ║         82 ║ 109.11941          ║
║   20 ║ one    ║         81 ║ 292.57249          ║
╚══════╩════════╩════════════╩════════════════════╝
```

如您所料，这些关键字中包括一些加密货币的术语，例如 “coin”, “crypto”, “pay”, chain”, “bit”, ”eth”, “block” 以及 “token” 。

在 [我的 Github 网站上](https://github.com/makoto/expiringnames) 有完整的数据集和用于生成数据的（hacky）脚本，因此您可以随时深入研究数据，如果找到其他有趣的模式也可以通知我。

### 释放后如何获得这些域名

等到 8 月 2 日，90 天的宽限期结束时，这些域名便可以在 [ENS App](https://app.ens.domains/) 或提供类似注册服务的网站上进行注册。

为了防止再次出现通过恶意提高 Gas 价格来抢注这些域名，我们引入了 [一种更温和的方式来处理这些过期的域名](/news/2020-07-16-new-decaying-price-premium-for-newly-released-names.html)：注册价格以溢价开始，然后逐渐下降（类似于荷兰式拍卖）。

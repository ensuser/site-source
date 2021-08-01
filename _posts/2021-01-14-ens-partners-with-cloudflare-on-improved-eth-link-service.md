---
title: ENS 与 Cloudflare 在优化 eth.link 服务上进行合作
date: 2021-01-14 13:00:00
categories: 官方文章
original: https://medium.com/the-ethereum-name-service/ens-partners-with-cloudflare-on-improved-eth-link-service-4801bf9148ff
translator: 刘笨笨
description: 
---

![](/images/news/2021-01-14-ens-partners-with-cloudflare-on-improved-eth-link-service/01.jpg)

我们很荣幸地宣布，我们正在与 Cloudflare 合作，为 eth.link 提供更好的服务。1 月 18 日，我们会将 eth.link 上托管的 ENS + IPFS 网关服务迁移到新版本，这个版本是在 Cloudflare 上开发和运行的。你可以在 [他们的博客](https://blog.cloudflare.com/cloudflare-distributed-web-resolver/) 上了解 Cloudflare 服务的工作方式。

需要明确的是：我们不是直接利用现有的 Cloudflare 产品来优化我们自己的 eth.link 服务。相反，在过去的一年里，我们一直在与 Cloudflare 合作，帮助他们开发 ENS + IPFS 网关服务，在 1 月 18 日，我们将把我们的 DNS 域名 eth.link 指向他们的新服务。这不仅能提高在线率和可伸缩性，而且所有通过该服务访问的站点都将具有 HTTPS。

eth.link 服务的主要目的非常简单：用户可以通过在一个 .eth 域名后面加上 “.link” 的方式来像访问普通网站那样访问 IPFS 网站，不需要特殊的浏览器或扩展。例如，如果您有 [MetaMask、Opera 或其他支持 ENS+IPFS 的浏览器](https://medium.com/the-ethereum-name-service/all-the-ways-you-can-surf-the-decentralized-web-today-bf8e7a42fa27)，那您可以直接访问 almonit.eth ，但如果您没有，你仍然可以利用 eth.link 服务通过 [almonit.eth.link](https://almonit.eth.link/) 打开这个网站。

2019 年，我们在 [Protocol Labs 的帮助下](https://medium.com/the-ethereum-name-service/ethdns-9d56298fa38a) 创建了这项服务。从那时起，使用量快速增长，以至于我们要管理它就有太多的工作要做。如果您经常使用它，你可能已经注意到我们在过去的几个月里有一些服务中断的情况（很抱歉！）。我们专注于开发 ENS ，而不是运行基础设施，所以我们联系了 Cloudflare 。我们一直在与他们合作，帮助他们开发类似的服务，并将 eth.link 指向该服务，我们很高兴见证它的实现。

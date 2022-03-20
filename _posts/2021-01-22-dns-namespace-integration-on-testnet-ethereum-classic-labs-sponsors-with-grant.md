---
title: DNS 域名空间集成到 ENS 已经进入测试阶段
date: 2021-01-22 13:00:00
categories: 官方文章
original: https://medium.com/the-ethereum-name-service/dns-namespace-integration-on-testnet-ethereum-classic-labs-sponsors-with-grant-19d57bf16a8b
author: Brantly
translator: 刘笨笨
description: 
---

![](/images/news/2021-01-22-dns-namespace-integration-on-testnet-ethereum-classic-labs-sponsors-with-grant/01.jpeg)

我们很荣幸地宣布，我们已经在 Ropsten 测试网上部署了用于将 DNS 域名空间集成到 ENS 的智能合约，并且，[以太坊经典实验室](https://etclabs.org/)（ETC Labs）已经向我们提供了一笔赠款，以赞助该新功能的开发。如果在 Ropsten 上一切顺利，我们希望在几周内将其部署到主网。

[Ropsten 上的 DNS 注册器](https://ropsten.etherscan.io/address/0x475e527d54b91b0b011DA573C69Ac54B2eC269ea#code) （[源代码](https://github.com/ensdomains/dnsregistrar)）
[Ropsten 上的 DNS 预言机](https://ropsten.etherscan.io/address/0x476f41A2C337677467af02c6ef297b3BD2414D03#code)（[源代码](https://github.com/ensdomains/dnssec-oracle/)）

## DNS 域名空间集成

将 DNS 域名空间进行集成以便在 ENS 上来使用，这是一个漫长的过程。尽管 ENS 在 2017 年 5 月推出时，是以 ENS 原生顶级名称（TLD）.ETH 开始的，但 ENS 原则上一直能够支持更多 TLD。为了在 ENS 上扩展名称空间，我们决定不采用那种简单创建其他 ENS 原生 TLD 的方式来集成现有的 DNS 域名空间。我们认为这种方法最适合用户，并且有益于实现 ENS 的远期目标。

该计划于 2017 年 11 月在 Devcon3 上首次公布，在过去 3 年多的时间里取得了稳步进展。

此次更新将使大多数二级名称（例如：example.com）的持有者能够在 ENS 中声明并使用相同的名称（example.com，而不是 example.eth）对ENS使用。然后，他们将能够为其 DNS 域名设置 ENS 记录，例如，他们可以通过 example.com 接收 ETH、ETC、BTC、DOGE 等币种的付款。这种模式目前在 ENS 上已经由 .XYZ 名称实现。

不过，对于那些作为二级名称（2LD）使用的三级名称（3LD）来说（比如 `example.co.uk`），尽管我们计划以后会支持它们，但目前还不行。而且一些使用较少的 TLD 不具备 DNSSEC 功能。但是，实际上大多数 DNS 域名都可以在 ENS 上使用。

## 以太坊经典实验室资助

感谢以太坊经典实验室赞助这项新的 DNS 域名空间集成功能。

“ ENS 提供了重要的、公共的、基本的互联网基础设施。DNS 域名空间集成将提高其实用性并推动其应用。” 以太坊经典实验室首席执行官 Terry Culver 说，“我们非常乐意支持 ENS 迈入这一步，很高兴能够在短短几周内即可看到它被部署到主网。”

这种合作意义非凡，因为此功能大大扩展了 ENS 上可用的 TLD 的数量，使其超出了 ENS 原生 TLD `.ETH` 的范围。.ETH 名称可以接收包括 ETC 在内的任何加密货币的付款，而 TLD .ETH 与以太坊联系最为紧密。DNS 域名则与加密货币无关，因此可以作为一种更为中性的名称来让人们接收加密货币。例如，对于 ETC 用户而言，使用 theirname.com 接收 ETH、ETC、BTC、DOGE 等可能更有意义。
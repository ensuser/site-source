---
title: Fleek 让您轻松部署和维护 ENS + IPFS 网站
date: 2021-02-07 13:00:00
categories: 官方文章
original: https://medium.com/the-ethereum-name-service/cloudflare-and-fleek-make-ens-ipfs-site-deployment-as-easy-as-ever-262c990a7514
author: makoto
translator: 刘笨笨
description: 
---

![](/images/news/2021-02-07-fleek-make-ens-ipfs-site-deployment-as-easy-as-ever/01.jpeg)

现在，[eth.link 的 ENS+IPFS 服务已经在 Cloudflare 上运行](/news/2021-01-14-ens-partners-with-cloudflare-on-improved-eth-link-service.html)，通过在 IPFS 上维护 [ENS APP](http://app.ens.domains/) 的副本 https://app.ens.eth.link （如果你有 MetaMask、Opera、Brave 或其他兼容的浏览器，也可以直接访问 app.ens.eth），我们对当前的基础设施以及使用自己开发的程序信心十足。能做到这些，我们借助了 [Fleek](https://fleek.co/) 的服务。

以下是一个简易教程，以便将您的网站部署到 ENS+IPFS。

1. 把需要部署的网站生成单页应用程序（SPA）
2. 在 Fleek 上设定持续集成（CI）并设置 ENS 域名管理员
3. 设置 SSL 证书

## 1. 把需要部署的网站生成单页应用程序（SPA）

将网站部署到 IPFS 时要格外注意：IPFS 不提供动态路由。解决方法是使用 HashRouter (#) 协议，就像 Uniswap 的 ENS+IPFS 网站那样。

![](/images/news/2021-02-07-fleek-make-ens-ipfs-site-deployment-as-easy-as-ever/02.png)

在我们的 dapp 中，我们让它可以根据如下配置进行选择。

![](/images/news/2021-02-07-fleek-make-ens-ipfs-site-deployment-as-easy-as-ever/03.png)

## 2. 在 Fleek 上设定持续集成（CI）并设置 ENS 域名管理

[Fleek](https://fleek.co/) 可以轻松地构建网站、网页应用或本地应用，并集成隐私性、加密性和 p2p 等特性。它们不仅可以与 IPFS 自动集成，而且在将 PR 合并到 master 时，它还能自动将新的 IPFS 内容哈希设置到 ENS 解析记录中。

为了让 Fleek 能够更新您的 ENS 域名的内容哈希记录，您需要将 Fleek 设置为这个 ENS 域名的管理员。

注：ENS 域名的注册人和管理员之间有重要区别。注册人是域名的最终所有者，可以设置谁是管理员。管理员是可以设置域名解析记录等信息的帐户。如果你想让别人为你设置记录，但又不想完全放弃对域名的永久控制，就需要利用这一点。Fleek 充分利用了这一区别：你让 Fleek 成为管理员，这样他们就可以自动为你设置内容记录，但你仍然是注册人，所以仍然是域名的所有者，并可以随时取消 Fleek 的管理权限。（译注：[ENSUser 对此也有专门的解释](https://ensuser.com/guides/manage.html#域名操作角色)）

![](/images/news/2021-02-07-fleek-make-ens-ipfs-site-deployment-as-easy-as-ever/04.png)

如果你并不某个 ENS 域名的注册人（例如，你是在一个项目团队工作），那么你需要让域名的所有者在 ENS APP 中手动设置管理员。

令人惊讶的是，Fleek 每次更新 ENS 的内容记录都要替用户支付 Gas。

![](/images/news/2021-02-07-fleek-make-ens-ipfs-site-deployment-as-easy-as-ever/05.png)

## 3. 设置 SSL 证书（可选项）

当你使用 eth.link 网关链接到您的网站，或是将 IPFS 站点部署到一个 ENS 二级域名（比如 ensuser.eth）时，您完全不需要为 SSL 证书的事情操心：我们已经为 *.eth 通配符设置了 SSL 证书。

如果您我们的 ENS APP 中手动更新子域名上的内容记录，您也不必做任何事情，因为管理器会告诉 Cloudflare 为每个子域名创建一个新的证书。对于那些在 eth.link 迁移到 Cloudflare 之前就已经设置了记录的子域名，我们运行了一个批处理脚本来请求证书，所以在大多数情况下你不需要做什么。如果有遗漏的情况，请按下面步骤操作。

当您让 Fleek（或其他未使用 ENS APP 的方式）在子域名上设置内容哈希记录时，您需要在 ENS APP 上单击 “请求 SSL 证书” 按钮。创建新的 SSL 证书需要的时间一般不超过 30 秒。

![](/images/news/2021-02-07-fleek-make-ens-ipfs-site-deployment-as-easy-as-ever/06.png)

然后，ENS APP 发送一个 PUT 请求到 https://eth.link/names/{subdomain.yourdomain.eth}.link ，如果给定的域名存在并且设置了内容哈希，则创建证书。如果您需要自己自动化这个过程，您也可以这样做。

这就是我要说的！多亏了 Fleek 和 Cloudflare 等公司的努力，部署和访问 ENS+IPFS 网站和以往一样简单。

![](/images/news/2021-02-07-fleek-make-ens-ipfs-site-deployment-as-easy-as-ever/07.png)

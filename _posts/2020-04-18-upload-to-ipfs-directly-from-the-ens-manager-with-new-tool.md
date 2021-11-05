---
title: 通过 ENS 上传文件至 IPFS 图文教程，免费 3GB 空间
date: 2020-04-18 15:32:00
categories: 官方文章
original: https://medium.com/the-ethereum-name-service/upload-to-ipfs-directly-from-the-ens-manager-with-new-tool-ac055db5d2fe
translator: 荒野重生
description: 
---

> 译注：这是 Brantly Millegan 发布的一个教程，目前在 ENS APP 内已经直接整合了 Temporal Cloud 的服务，用户需要注册 Temporal Cloud 帐户，免费帐户可以有 3GB 空间，亲测有效。

![](/images/news/2020-04-18-upload-to-ipfs-directly-from-the-ens-manager-with-new-tool/01.png)

我们上线了一个新的工具，允许用户上传文件到 IPFS 并直接在 [ENS APP](https://app.ens.domains/) 中将此 IPFS 内容的哈希值保存到他的 ENS 记录中。该工具使用 IPFS 固定服务 [Temporal Cloud](https://temporal.cloud/) 。

以前，用户必须在一个地方将文件上传到 IPFS，然后在另一个地方更新他们的 ENS 记录。今天介绍的工具大大简化了这一过程，让人们可以更轻松地部署[去中心化的网站](https://medium.com/the-ethereum-name-service/all-the-ways-you-can-surf-the-decentralized-web-today-bf8e7a42fa27)。

## 如何上传至 IPFS

### 1. 开始之前需要什么

Web3 浏览器：在台式机上，它可以是任何主流浏览器，例如具有 [MetaMask 插件](https://metamask.io/) 的 Chrome 或内置了加密货币钱包的 [Brave](https://brave.com/) 。在移动设备上，可能是 [TrustWallet](https://trustwallet.com/)，[Status](https://status.im/)，[MetaMask Mobile](https://metamask.io/) 或其他应用程序中的浏览器。注意：由于某些原因，该新功能目前无法在 [Coinbase 电子钱包](http://wallet.coinbase.com/) 浏览器中使用，但有望很快修复。

一些以太币：您将需要少量（价值几美分的以太币）加密货币以支付以太坊网络交易费。

ETH 域名：如果您还没有.ETH 名称，则可以在 ENS APP 中注册一个 .ETH 域名（ [注册教程](https://ensuser.com/guides/register.html) ）。

### 1. 选择创建一个新的内容记录

打开 [ENS APP](https://app.ens.domains/)，然后搜索您的 .ETH 域名。如果您还没有内容记录，请单击右侧的 `+` 按钮，然后从下拉菜单中选择 `内容哈希`。

如果您已有内容记录，请跳过此步骤。

![](/images/news/2020-04-18-upload-to-ipfs-directly-from-the-ens-manager-with-new-tool/02.gif)

### 2. 点击 `新建上传` 按钮

如果是第一次创建内容记录，请单击右侧的 `新建上传` 按钮：

![](/images/news/2020-04-18-upload-to-ipfs-directly-from-the-ens-manager-with-new-tool/03.png)

如果您的名字已经有内容哈希记录，则单击右侧的上传图标：

![](/images/news/2020-04-18-upload-to-ipfs-directly-from-the-ens-manager-with-new-tool/04.png)

### 3. 创建一个 Temporal Cloud 帐户

如果您还没有 Temporal Cloud 帐户，请单击 `Sign UP` 按钮并创建一个帐户，创建帐户不需要信用卡或付款。

注意：创建新的 Temporal Cloud 帐户并验证您的电子邮件地址时，您将自动享有 Temporal Cloud 免费套餐。这样一来，您最多可以上传 3GB 的数据，并且您上传的所有数据都会在 IPFS 网络上保留至少 12 个月。如果您想上传更多数据，或确保它们在 IPFS 网络上保留的时间超过 12 个月，则可以在此处升级帐户。

如果您已经有一个 Temporal Cloud 帐户，请跳过此步骤（您可以将单个 Temporal Cloud 帐户用于所需的多个 ENS 域名）。

### 4. 登录

创建 Temporal Cloud 帐户并验证电子邮件地址后，或者如果您已经拥有 Temporal Cloud 帐户，请输入您的 Temporal Cloud 用户名和密码，然后单击 `Login`。

![](/images/news/2020-04-18-upload-to-ipfs-directly-from-the-ens-manager-with-new-tool/05.png)

### 5. 上传文件

您可以使用 `Folder Upload` 按钮上传整个文件夹（例如其中包含您的网站文件的文件夹），也可以使用 `File Upload` 按钮上传单个文件。

![](/images/news/2020-04-18-upload-to-ipfs-directly-from-the-ens-manager-with-new-tool/06.png)

### 6. 将IPFS哈希保存到您的内容记录

您还没有完成！该文件已上传到 IPFS，但 IPFS 哈希尚未保存到您 ENS 域名的内容记录中。为此，请单击 `保存`。

您的 web3 浏览器将要求您确认交易以保存记录。（提示：您可能需要将以太坊网络交易费调整为更高的金额，以帮助更快地确认交易。）

![](/images/news/2020-04-18-upload-to-ipfs-directly-from-the-ens-manager-with-new-tool/07.png)

### 7. 大功告成

确认将 IPFS 哈希保存到内容记录中的交易之后，您就完成了内容上传！您应该在内容记录中看到上传的文件的 IPFS 哈希。

![](/images/news/2020-04-18-upload-to-ipfs-directly-from-the-ens-manager-with-new-tool/08.png)

## 如何访问你的去中心化网站

现在您已经在一个分布式文件存储网络（IPFS）中拥有了一些内容，并使用一个去中心化的域名系统（ENS）指向了这些内容。那么究竟如何使用它们？

你可以[在这篇文章中读到更多关于去中心化的方法](https://medium.com/the-ethereum-name-service/all-the-ways-you-can-surf-the-decentralized-web-today-bf8e7a42fa27)，但是这里有一些快速上手的技巧：

![](/images/news/2020-04-18-upload-to-ipfs-directly-from-the-ens-manager-with-new-tool/09.jpeg)

### 在浏览器中

在你的 .ETH 域名后面添加 `.LINK`（比如：ensuser.eth.link ），就可以在任何浏览器中访问它。例如，你可以去 [almonit.eth.link](http://almonit.eth.link/#/) （一个去中心化网站的导航页面）。你可以在[这里](https://medium.com/the-ethereum-name-service/ethdns-9d56298fa38a)详细了解它的工作原理。

### 利用 MetaMask 插件

如果浏览器安装了 MetaMask 插件，你直接访问 `[你的域名].eth/`（一定要在结尾处加上 `/` ，如：`ensuser.eth/`），它会像普通网站那样正常打开。

### 具备原生支持的浏览器

有一些浏览器原生支持去中心化的网站。它们分别是：Opera（Android 版本）、Brave（电脑版，需要在域名后加上 `/`），MetaMask 手机版，Unstoppable 浏览器（电脑版）。

## 关于上传到其他网络

我们非常感谢 [Temporal Cloud](https://temporal.cloud/) 团队与我们一起努力，将他们的 IPFS 固定服务集成到我们的管理器中。

![](/images/news/2020-04-18-upload-to-ipfs-directly-from-the-ens-manager-with-new-tool/10.png)

我们愿意将其他公司的服务集成到我们的管理器中，无论是 IPFS 还是其他分布式文件网络。如果可以，请随时通过 brantly@ens.domains 与我们联系。

## 结论

这个工具使人们更容易在去中心化的网络上托管文件和网站。

我们希望在未来网络会更加去中心化，而这是实现这一目标的关键一步。

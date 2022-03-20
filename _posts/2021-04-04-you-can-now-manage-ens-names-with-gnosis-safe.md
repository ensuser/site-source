---
title: 现在可以用 Gnosis Safe 来管理 ENS 名称了
date: 2021-04-04 13:00:00
categories: 官方文章
original: 
author: Makoto
translator: 刘笨笨
description: 
---

借助 Gnosis Safe，您可以轻松地与朋友们组建一个 DAO 来管理高价值资产。

![](/images/news/2021-04-04-you-can-now-manage-ens-names-with-gnosis-safe/01.png)

PleaserDAO 购买 [@pplpleasr1](https://twitter.com/pplpleasr1) 臭名昭著的 “x*y=k” NFT 艺术品时，他们也购买了pleaserdao.eth。现在 ENS 是他们为数不多的 NFT 收藏品之一。

![](/images/news/2021-04-04-you-can-now-manage-ens-names-with-gnosis-safe/02.png)

只要某个外部账户（EOA）代表 multisig（多方签名合约） 设置一个 ENS 名称，就可以很容易地将其指向 multisig 账户。

然而，为其设置反向记录（允许 dapp 识别以太坊地址指向的 ENS 名称）一直很困难，因为设置必须由 Gnosis 合约账户本身来实现。

感谢 Gnosis 团队的努力，你现在可以通过 Gnosis Safe 管理你的 ENS 名称了。

以下是简要步骤（注意：我假设你已经把你的 ENS 名称指向了 Gnosis Safe 地址)。

### 第 1 步：从 “APPS” 部分打开 ENS 页面

![](/images/news/2021-04-04-you-can-now-manage-ens-names-with-gnosis-safe/03.png)

如果没有显示 ENS 图标，你需要点击 “Add custom app” 并输入 https://app.ens.domains 

页面开打后，ENS 页面就会自动显示当前连接的 Gnosis Safe 账户，而不是你自己的外部账户。

### 第 2 步：打开 “我的帐户” => “设置反向解析”，选择相应的 ENS 名称，然后点击 “保存”

![](/images/news/2021-04-04-you-can-now-manage-ens-names-with-gnosis-safe/04.png)

这时会弹出一个确认窗口，点击 “Submit”。

### 第 3 步：通知你的多方签名成员在 Gnosis Safe 内打开 “Transactions” 标签，依次点击 “Awaiting your confirmation” => “Confirm” => “Submit”

![](/images/news/2021-04-04-you-can-now-manage-ens-names-with-gnosis-safe/05.png)

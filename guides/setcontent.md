---
part: ENS 使用教程
title: 如何将 ENS 域名解析至 IPFS 上存储的内容并通过 EthLink 访问
keywords: ENS解析, 域名解析, IPFS, 内容哈希, 详细教程
description: 这篇文章中，我们会介绍如何将 ENS 域名解析至 IPFS 上存储的内容，并通过 EthLink 访问这些内容。
---

前面的教程中，我们提到了 [解析记录的多个类型](/guides/setrecords.html#解析记录介绍)。本文会介绍如何将 ENS 域名解析至 IPFS 上存储的内容，并通过 EthLink 访问这些内容。

## 准备工作

完成这项工作的前提是，我们要懂得一些关于 IPFS 的知识，并准备一项存储在 IPFS 上的内容。

我准备了一个测试页面，并将其存储到 IPFS 网络，它在 IPFS 中的哈希地址为 `QmQNKJ...TH6M` 。我们可以先通过 IPFS 的官方网关浏览一下这个网页：[ENSUser 的 IPFS 测试页面](https://gateway.ipfs.io/ipfs/QmQNKJuDHn6RX1hU4Fsbsbu4jaTqmVdrZbsmeHAhvnTH6M/)

{% note info %}
因为某些不便描述的原因，某些人在某些时候位于某些地域时，可能会打不开这个页面，当然，如果你用过 IPFS，那你一定可以找到合适的网关或者其他巧妙的办法来查看这个页面。
{% endnote %}

打开后效果如下：

![](/images/guides/setcontent/setcontent-02.png)

## 将 ENS 域名解析至内容哈希

下面我们以 `ceshi.eth` 为例介绍如何添加一条指向这个网页哈希地址的解析记录。

{% note info %}

1. 在浏览器上打开 [ENS 管理器](https://app.ens.domains/)，并使用域名的**管理员账户** [连接](index.html#在浏览器中连接)。
2. 输入 `ceshi.eth` 并点击 `查询` 按钮，进入 `ceshi.eth` 的管理页面。
3. 在 `解析记录` 区域中点击 `+` 按钮展开记录添加区域。
4. 点击 `选择一种类型` 下拉菜单，从中选择 `内容哈希` 选项。
   > 右侧的文本框中出现一些提示语，这是在提醒我们：文本框里应该输入一个哈希值，并且要按照诸如 `/ipfs/...` `ipfs://...` `bzz://...` `onion://...` `onion3://...` 这样的格式来填写。其中前两项为 IPFS 内容的前缀，中间一项为 Swarm 内容的前缀，后两项为洋葱网络内容的前缀。
5. 这里我们准备的是 IPFS 内容哈希，加上前缀变成：`ipfs://QmQNKJuDHn6RX1hU4Fsbsbu4jaTqmVdrZbsmeHAhvnTH6M`
6. 将其填写到文本框中，并点击文本框右下方的 `保存` 按钮，这时钱包要求确认交易，确认后，等待交易被打包。
7. [该交易](https://cn.etherscan.com/tx/0xa42e1b4e6097c365b986b756770f1ab80e2195058450c542e3a420d102e4e858) 成功被打包后，`解析记录` 区域内就会增加一条 `内容哈希` 类型的记录，解析记录就添加完成了。

{% endnote %}


因为 ENS 管理器自动为 IPFS 内容设置了访问链接，我们可以通过在管理页面上点击这个 IPFS 地址 `ipfs://QmQNKJ...TH6M` 来访问这些内容（打不开该页面的童鞋请自寻巧妙办法）：

![](/images/guides/setcontent/setcontent-02.png)

## 通过 EthLink 访问域名解析到的内容

[EthLink](http://eth.link/) 是一项通过 **DNS**（注意这里不是 ENS）的方式实现访问 .eth 域名关联内容的服务。使用方法是：在 ENS 域名后面追加 `.link` 作为网址，就可以在浏览器中直接访问 .eth 域名关联的内容了。

比如，我们前面在为 `ceshi.eth` 添加了解析到内容哈希的记录，现在就可以通过 `ceshi.eth.link` 这个网址在浏览器中访问这些内容了，效果如下：

![](/images/guides/setcontent/setcontent-03.png)

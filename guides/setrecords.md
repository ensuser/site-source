---
part: ENS 使用教程
title: 如何将 ENS 域名解析至以太坊地址
keywords: ENS解析, 解析记录, 以太坊钱包
description: 本文将介绍如何将 ENS 域名解析至以太坊地址。
---

## 解析记录介绍

解析记录是指 ENS 域名解析到的资源，这些资源类型主要包括：

- 以太坊地址
- [其他币种地址](/guides/setotheraddress.html) ：BTC、LTC、DOGE、MONA、ETC、RSK、XRP、BCH、BNB 等
- [内容哈希](/guides/setcontent.html) ：IPFS、Swarm、BZZ、Onion
- 文本数据：email、url、avatar、description、notice、keywords 等

## 将 ENS 域名解析至以太坊地址

在所有可以设置的记录类型中，最常用的就是以太坊地址，将 ENS 域名解析到以太坊地址以后，其他人就可以通过 ENS 域名来向你转账。

[前面](/guides/setresolver.html#关于解析器的情况说明) 我们提到，你如果成功注册了一个域名，那么注册器已经自动将域名解析至注册时所用的以太坊地址。但有些情况下（比如想要更改接收资金的地址），人们需要将域名解析至另外一个地址，这时候就需要重新设置这条解析记录。

### 注意事项

1. 设置解析记录的前提是该域名已经正确 [设置解析器](/guides/setresolver.html) 。
2. 无论注册人和管理员是哪个账户，以后其他账户向这个 ENS 域名发送的资金都是流入到它的解析记录账户，也就是我现在即将设置的这个账户。

下面我们以 `ceshi.eth` 为例，将其解析到以太坊地址 `0xd55dA...D096` 。

### 操作步骤

{% note info %}

1. 打开 [ENS APP](https://app.ens.domains/) ，并进入 `ceshi.eth` 的管理页面。
2. 点击 `以太坊地址` 最右侧的编辑按钮（一只小铅笔），展开解析地址的编辑区域，出现一个文本框。
3. 将我们要解析到的以太坊地址（`0xd55dA...D096`）复制到这个文本框中（这里的地址必须是一个合法的以太坊地址）。
4. 点击 `保存` 按钮，这时钱包会要求确认交易，确认后，等待交易被打包。
5. [该交易](https://cn.etherscan.com/tx/0x623144dceaf0cf64534f26214aa5a829fbfbbf9c59041fe187ea0847ba48dceb) 被打包成功后，`以太坊地址` 这一行显示的地址会变成我们刚刚设置的地址：`0xd55dA...D096` ，解析记录就设置成功了。

{% endnote %}

现在我们如果向 `ceshi.eth` 转账，资金就会流入 `0xd55dA...D096` 这个账户。

---
part: ENS 使用教程
title: 如何设置 ENS 主名（即反向解析）
keywords: ENS反向解析, ENS主名, 主要名称, 反向解析设置
description: 本文将介绍如何设置你的主要ENS名称的反向解析记录。ENS主名（反向解析）是指将你的以太坊地址解析到一个 ENS 名称的记录。这样，那些支持反向解析的应用，就可以根据需要使用可读性更好的 ENS 名称取代以太坊地址显示给用户。
---

## ENS 主名（反向解析）介绍

本文介绍如何设置主要 ENS 名称（主要 ENS 名称原来称为 “反向解析记录”，相对来说，通常所说的 ENS 解析可称为 “正向解析记录”）。

ENS 解析是将一个 ENS 名称解析到以太坊地址等资源的记录，与其相反，设置主要 ENS 名称（反向解析）是指将一个以太坊地址解析到一个 ENS 名称的记录。这样，那些支持 ENS 主名的应用，就可以根据需要使用可读性更好的 ENS 名称取代以太坊地址显示给用户。

有人可能会觉得，这不是多此一举吗？把 ENS 名称和以太坊地址关联起来不就行了，为啥还要进行双向解析？这个问题涉及到了 ENS 的内部实现机制，如果有兴趣，可以参阅 ENS 中文文档中的 [名称处理](/docs/contract-api-reference/name-processing.html) 和 [反向解析](/docs/dapp-developer-guide/resolving-names.html#反向解析) 等内容。

在前面的教程中，我们将 `ceshi.eth` 的正向解析记录设置为 `0xd55dA...D096`，现在我们为 `0xd55dA...D096` 这个地址设置一个主名，这时就需要将 `0xd55dA...D096` 解析到 `ceshi.eth`。

{% note warn %}

ENS 主名和要它的正向解析互相对应才能生效，设置主名之前必须先为 ENS 名称设置好解析记录，然后再把这个 ENS 名称设置为解析地址的主名，否则主名是无效的。

{% endnote %}

## 操作步骤

下面我们以 `ceshi.eth` 为例，介绍如何设置和修改某个以太坊账户的主要 ENS 名称。

因为 `ceshi.eth` 的解析记录已经设置为 `0xd55dA...D096`，而每个账户只能为自己的地址设置主要名称，所以现在我们需要使用 `0xd55dA...D096` 这个账户连接 [ENS APP](https://app.ens.domains/) 。

{% note info %}

1. 在浏览器上打开 [ENS APP](https://app.ens.domains/)，并[连接](index.html#在浏览器中连接)需要设置主要名称的账户。
2. 连接好之后，在页面的右上角或左侧区域找到并打开 `我的账户` 页面。
3. 在页面上找到主要名称设置区域（其中显示 `主要 ENS 名称（反向记录）：未设置`）。
   > 由于之前没有设置过反向解析记录，所以在反向解析记录区域显示的是：`主要 ENS 名称（反向记录）：未设置` ，如果已经设置过反向解析，这里会显示 `主要 ENS 名称（反向记录）：已解析至 ***.eth` 。
4. 点击 `主要 ENS 名称（反向记录）：未设置` 这一行，会展开反向记录设置区域。该区域内有一段关于反向解析记录的简单说明，说明文字下面的下拉菜单，列出了已经正向解析至当前地址的 ENS 名称，从中选择我们需要设置的名称。
   > 如果没有 ENS 名称正向解析至该地址，则这个下拉菜单中显示为空。
5. 点击区域内的 `保存` 按钮，钱包会要求确认交易，确认后，等待交易被打包。
6. [该交易](https://cn.etherscan.com/tx/0x0e96d7378863ebb015a591e2fddf19243f96670c1377818390a73b71a1f31991)被打包成功后，原来的 `主要 ENS 名称（反向记录）：未设置` 会变成 `主要 ENS 名称（反向记录）：已解析至 ceshi.eth`，主要 ENS 名称就设置完成了。

{% endnote %}

细心的用户可能会发现，反向解析记录设置完成后，ENS APP 页面的左侧区域发生了一些变化：

![](/images/guides/reverse/setreverse-07.png)

本来显示 `0xd55dA...D096` 这个地址的位置变成了简洁的 `ceshi.eth` ！

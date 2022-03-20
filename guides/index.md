---
part: ENS 使用教程
title: ENS 名称注册、使用及管理教程 - 概述
keywords: ENS如何使用, 名称注册, ENS管理, ENS名称使用, 教程
description: 只要是ENS用户，都需要进行ENS注册与管理，本教程对注册与管理中的一些主要操作进行了详细的说明，告诉你如何使用ENS名称。
---

## ENS 介绍

以太坊名称服务（Ethereum Name Service）是一个基于以太坊区块链的分布式、开放和可扩展的命名系统。通俗地说，ENS 就是区块链中的名称系统。ENS 名称让人们没有必要再复制或输入冗长的区块链地址。

<div class="special-wrapper">
    <a class="big-button" href="/about/about-ens.html">了解更多</a>
</div>

## 本系列教程主要包含哪些内容

只要是 ENS 用户，都需要进行 ENS 注册与管理，本教程对注册与管理中的操作进行了详细的说明，主要分为以下几类。

- **ENS 名称注册**：介绍并演示如何 [注册 .eth 名称](register.html)。
- **ENS 名称管理**：介绍 ENS 名称的一些基本功能及其操作步骤，主要面向有个性化设置需求的用户。内容有包括 [设置解析器](setresolver.html#设置解析器)、[设置解析记录](setresolver.html#设置解析记录)、[设置反向解析](setreverse.html)、[名称续费](renew.html)、[更换名称管理员](setcontroller.html)、[名称过户](transfer.html)、[设置多种解析记录](setcontent.html)、[子名称管理](setsubdomain.html) 等。

## ENS APP 是什么

ENS APP 是负责帮助用户操作 ENS 相关智能合约的应用，普通用户要注册与管理 ENS 名称，就需要通过 ENS APP 与相关的智能合约进行交互。

任何人都可以开发并部署 ENS APP ，尽管可以实现类似的功能，但从可靠性和可用性来看，强烈建议您使用由 ENS 官方团队开发并部署的管理器：

<div class="special-wrapper">
    <a class="big-button" href="https://app.ens.domains/">ENS APP</a>
</div>

## 连接 ENS APP

使用 ENS APP 之前需要将你的以太坊账户与其进行连接，告诉管理器是哪个账户要进行操作。下面分别说明在浏览器和手机钱包中连接 ENS APP 的情形。

### 在浏览器中连接

浏览器安装 Metamask 插件后，在 Metamask 中选择需要进行操作的以太坊账户。

在浏览器里打开 [ENS APP](https://app.ens.domains/) ，这时管理器会要求与以太坊账户进行连接，点击 `连接` 按钮后，如果没有其他提示，则表示连接成功。中文用户可以在中间搜索框靠右的位置将显示语言由 `EN` 切换为 `CN`。

![](/images/guides/connect-05.png)

现在你可以进行 ENS 名称的各种操作啦！

{% note info %}
注意：页面左上角显示的 `Main 网络` 表示我们现在连接的是以太坊主网。如果需要连接其他以太坊网络，需要在 Metamask 中切换当前连接的以太坊交易网络。
{% endnote %}

### 在手机钱包中连接

在手机钱包（比如 imToken）选择需要进行操作的以太坊账户，打开 ENS 应用或直接输入 ENS APP 地址，并按照提示建立连接。

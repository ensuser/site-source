---
part: ENS 使用教程
title: ENS 名称注册教程 - 注册 .eth 名称详细教程
keywords: ENS注册, eth名称注册教程, 如何注册eth名称
description: 本文演示了如何一步一步注册一个eth名称。ENS中的 .eth 名称的注册在经历了维克里拍卖式注册、短名称英式拍卖注册的阶段后，现在正式进入了即时注册的时代。相信未来很多人都会拥有自己的 .eth 名称。
---

ENS 名称系统中目前支持多种顶级名称，比如 `.eth` `.xyz` `.luxe`。其中， `.eth` 是 ENS 系统的唯一原生名称，是由一系列智能合约控制的去中心化的名称，也是本站的核心研究内容。另外几种是从互联网名称中接入的，要想使用这类名称可参阅 ENS 中文文档中的 [DNS 注册器教程](/docs/dns-registrar-guide.html) 一文。

.eth 名称的注册在经历了维克里拍卖式注册、短名称英式拍卖注册的阶段后，现在正式进入了即时注册的时代。相信未来很多人都会拥有自己的 ENS 名称。

**友情提示**：目前 .eth 名称资源依然非常丰富，很多优质的名称还没有被注册，比较容易挑选到心仪的名称，建议有意向的童鞋们抓紧吧。

下面我们来演示一下如何在以太坊上注册一个 .eth 名称。

本文将采用 “浏览器+插件钱包” 的方式演示在 ENS 官方管理器上进行注册的过程。您也可以通过手机钱包来注册，比如 imToken 或 TokenPocket（[TP钱包注册教程](https://www.yuque.com/tokenpocket/gz8u7f/vp4cod)），这些手机钱包内置了 ENS 官方应用，就不用再需要单独的浏览器了。

## 注册准备工作

- 浏览器，建议使用 Chrome 或 Firefox 浏览器
- 以太坊钱包，本文中用的是最流行的 MetaMask 插件钱包（在国内的网络环境下，Firefox 上的 Metamask 插件更容易安装）
- 以太坊账户及余额，注册一个 .eth 名称需要大约价值 5 美元的以太币，再加上点 Gas 费

我们选用一个以太坊账户来进行名称注册，等名称注册成功后，.eth 注册器就会自动把这个账户作为新名称的注册人、管理员，并自动将名称解析至这个账户地址，在名称管理部分的 [名称操作角色](/guides/manage.html#名称操作角色)，我们会简单介绍注册人、管理员的功能。

## 查询名称是否已经被注册

在浏览器或钱包中打开 [ENS APP](https://app.ens.domains/)，并 [连接](index.html#在浏览器中连接) 以太坊账户。

在页面中央那个醒目的文本框内输入想要注册的名称（目前只能注册 3 个及以上字符的名称），比如， `nihao.eth` ，点击 `查询` 按钮，查询一下这个名称当前的状态：

![](/images/guides/register/register-01.png)

从查询结果中我们可以看到，`注册人` 一栏是 `0xAb48E...9d17`，说明 `nihao.eth` 已经被账户 `0xAb48E...9d17` 注册了。

返回 ENS APP 首页并重新输入一个名称：`ceshi.eth` ，点击 `查询` 按钮，可以看到 `ceshi.eth` 是可以注册的！

![](/images/guides/register/register-02.png)

上图中的加减号可以调整需要注册的时间（默认是 1 年），后面是根据当前以太坊与美元的汇率自动计算出来的租金。5 个字符及以上的名称价格约等于每年 5 美元，4 个字符的价格约为每年 160 美元，3 个字符的价格约为每年 640 美元。2 个字符和 1 个字符的名称还不能注册。这里我们保持默认，即注册时支付一年的租金。

图中的 `通知我` 按钮可以开启名称准备完毕的通知，点击于否都不影响注册。

## 注册步骤

确认名称可以注册后，可以看到上图中那段提示：

> 注册一个名称需要完成三个步骤：
>
> 1. 请求注册。需要在钱包中确认一笔交易，这是完成名称注册所需的两笔交易中的第一笔。（注：这笔交易没有转账只包含 Gas 费，用于向 .eth 注册器提交一个注册请求。）
> 2. 等一分钟。需要等待一段时间，以确保其他人没有尝试注册相同的名称，同时也是在保护你的注册请求。
> 3. 完成注册。点击 `注册` 按钮，并在钱包中再次确认一笔交易，只有在这次交易确认后，才能确定是不是成功注册了这个名称。

### 1. 请求注册

现在我们开始注册流程，点击页面上的 `请求注册` 按钮发起注册请求，这时钱包会要求确认第一笔交易。确认后，等待交易被打包（一般不超过 30 秒，以太坊网络拥挤时或是 Gas 费偏低时可能要多等一会）。

![](/images/guides/register/register-03.png)

### 2. 等一分钟

[该交易](https://cn.etherscan.com/tx/0xdf14f73bcf975c70eb086ffbba021427d185680641fcd523d4cf9f787d9df461) 被打包成功后，需要再等一分钟。一分钟过后，会显示如下界面，表示名称已经准备好了（如果你之前点击过 `通知我` 按钮，这时浏览器会弹出一个通知，告诉我们名称可以正式注册了）：

![](/images/guides/register/register-05.png)

### 3. 完成注册

点击页面中的 `注册` 按钮，钱包会要求确认第二笔交易（这笔交易中包含了一年的租金）。确认后，等待第二次交易的被打包，[该交易](https://cn.etherscan.com/tx/0x6e4389a2a749906e1f644fec62ee561b90f6a3e65222b77af99400ed7d2542ba)被打包成功后，绿色进度条走完，表示注册成功：

![](/images/guides/register/register-06.png)

这个名称就注册完成了！

自从 eth 注册器合约在 2020 年 2 月初重新部署以后，我们在注册 eth 名称时，注册器合约会自动为我们设置公共解析器并将此名称解析至注册时使用的以太坊地址。也就是说，我们的 .eth 名称注册成功后就可以直接使用了，向名称转账，资金就会流入你的以太坊账户。一句话概括就是：方便多了！而且还可以省下几笔交易费。

所以，赶紧把你注册的名称告诉需要给你转账的朋友们吧！

## ENS 管理引言

名称注册成功后，会显示一个 `管理名称` 按钮，点击它可以进入这个名称的管理页面，在管理页面可以查看或者设置名称的相关信息。如果以后需要 [管理名称](/guides/manage.html)，直接在 ENS APP中搜索这个名称，就会直接进入它的管理页面，不过有没有操作权限，还要看你是用哪个以太坊账户连接了 ENS APP。

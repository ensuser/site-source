---
part: ENS 使用教程
title: 利用 ENSNow 出售子域名，让你的 .eth 域名为你赚钱！
keywords: ENSNow, 子域名出售, eth域名, eth子域名出售
description: 通过将你的域名挂载到 ENS 子域名注册器上，其他人就能以你设定的价格来注册你的子域名，那么如何将域名交给 ENS 子域名注册器来管理呢？
---

## 注意事项

1. 在 [ENS 子域名注册器](subdomain-registrar.html#ENS-子域名注册器是什么) 的介绍中，我们提到过：**一旦域名被转让给子域名注册器，那么该域名原来的主人就失去了控制权，并且无法追回和重置。**
2. ENS 子域名注册器只能管理 .eth 域名，您也可以将其理解为 .eth 子域名注册器。
3. 本教程涉及与以太坊上的智能合约进行交互，请选择您习惯的方式来进行，并确保您的密钥安全。

## 如何利用 ENSNow 出售你的子域名

想要利用 ENSNow 出售你的子域名，有两个必经环节。下面我们以 `enser.eth` 和 `xuyao.eth` 为例来进行详细介绍。

### 第一个环节：将你的域名交给 ENS 子域名注册器合约来管理

1. 使用域名的注册人账户发起一笔交易，在交易中调用 [ENS: Base Registrar Implementation](https://cn.etherscan.com/address/0x57f1887a8bf19b14fc0df6fd9b2acc9af147ea85) 合约中的 approve(address to, uint256 tokenId) 函数，用于批准 ENS 子域名注册器可以转移这个域名。调用函数时，参数设置如下：

- to: 设置为 ENS 子域名注册器合约的地址（[0xe65d8...](https://cn.etherscan.com/address/0xe65d8aaf34cb91087d1598e0a15b582f57f217d9)）。
- tokenID: 设置为域名标签的哈希，比如 `enser.eth` 中的 enser 的哈希值是 `cb0cebe...c496`。

2. 使用域名的注册人账户发起另一笔交易，在交易中调用 [ENS: Migration Subdomain Registrar](https://cn.etherscan.com/address/0xe65d8aaf34cb91087d1598e0a15b582f57f217d9) 合约中的 configureDomain(string name, uint256 price, uint256 referralFeePPM) 函数，用于配置子域名出售的相关参数，该交易被打包成功后，域名就完全由子域名注册器合约控制了。调用函数时，参数设置如下：

- name: 要出售的域名标签。比如，我要出售 `enser.eth` 这个子域名，这里就填写 `enser`。
- price: 每个子域名的注册费，计价单位是 wei，即 10的负18次方以太币。比如，我给 `enser.eth` 设置的子域名价格为 0，用户就可以免费注册 `enser.eth` 的子域名，而给 `xuyao.eth` 设置的子域名价格为 10000000000000000，这样，用户要想注册一个 `xuyao.eth` 的子域名，就需要支付 0.01 个以太币。
- referralFeePPM: 佣金比例，以百万分之一计算。比如，我们希望在注册费中分出 20% 作为佣金付给推荐者，以鼓励其他人推荐我的子域名，那么这里需要设置为 200000。

进行到这里，我们已经完全将域名交给了 ENS 子域名注册器合约来托管，并且再也无法收回这个域名的所有权。

事情还没有结束！如果你想让用户在使用 [ENSNow](https://now.ensuser.com/) 时能够看到你刚刚托管的域名，还需要完成下面一个环节。

### 第二个环节：联系 ENSUser 管理员，在 ENSNow 上架你的域名

ENSNow 是一个帮助用户与 ENS 子域名注册器进行交互的网页应用，是一个用来注册 ENS 子域名的工具。但是，选择在 ENSNow 上显示哪些域名则是由 ENSNow 管理者来控制的。

因此，如果您希望用户能够在 [ENSNow](https://now.ensuser.com/) 上发现并注册您托管的域名，请联系 [刘笨笨](https://ensuser.com/about/#联系我们)。

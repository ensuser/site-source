---
part: ENS 中文文档
title: ENS 部署情况 
---

如果你使用的是 [ENS 库](dapp-developer-guide/ens-libraries.html)，它会自动连接所需的 ENS 部署地址。如果你需要直接与 ENS 交互，下面介绍的是当前所支持的部署情况。

ENS 注册表的部署地址是：`0x00000000000C2E074eC69A0dFb2997BA6C7d2e1e` 。这个部署地址在以太坊[主网络](https://cn.etherscan.com/address/0x00000000000C2E074eC69A0dFb2997BA6C7d2e1e)、[Ropsten](https://ropsten.etherscan.io/address/0x00000000000C2E074eC69A0dFb2997BA6C7d2e1e)、[Rinkeby](https://rinkeby.etherscan.io/address/0x00000000000C2E074eC69A0dFb2997BA6C7d2e1e)、[Goerli](https://goerli.etherscan.io/address/0x00000000000C2E074eC69A0dFb2997BA6C7d2e1e)  测试网络中都是一样的。

主网上部署了以下几个顶级域名的注册器：

* .eth ，使用 .eth 永久注册器。
* .xyz ，通过与 DNS 集成的方式来进行注册。
* .luxe ，通过 [自定义集成](http://join.luxe/) 的方式，允许拥有 .luxe 这一 DNS 域名的人使用 ENS。
* .kred ，通过 [自定义集成](http://domains.kred/) 的方式，根据 DNS 的情况自动地同步和更新 `.kred` ENS 域名。
* .art ，通过自定义集成的方式实现。

顶级域名（TLD）的合约地址，可以通过查询这个 TLD 的“管理员”地址来找到（比如，在 [https://app.ens.domains/name/xyz](https://app.ens.domains/name/xyz) 可以找到 `.xyz` 的合约地址）。

![](/images/docs/ens_deployments_01.png)

Ropsten 测试网络上部署了 .test 注册器，每个人都可以注册 .test 域名用作测试， .test 域名的有效期是28天。

此外，Ropsten 测试网络还部署了 .eth 注册器用于测试。

至于其他的合约地址，比如根域合约、多重签名合约、管理员合约、公共解析器合约等等，你可以在这个页面找到： [https://app.ens.domains/name/ens.eth/subdomains](https://app.ens.domains/name/ens.eth/subdomains)

2020 年 2 月，为了修补安全漏洞（[详情](/docs/ens-migration-february-2020/technical-description.html)），ENS 注册表被迁移到了新的合约地址，迁移之前的注册表地址为:

* 主网络，部署在 [0x314159265dd8dbb310642f98f50c066173c1259b](https://cn.etherscan.com/address/0x314159265dd8dbb310642f98f50c066173c1259b#code)
* Ropsten 测试网络，部署在 [0x112234455c3a32fd11230c42e7bccd4a84e02010](https://ropsten.etherscan.io/address/0x112234455c3a32fd11230c42e7bccd4a84e02010)
* Rinkeby 测试网络，部署在 [0xe7410170f87102df0055eb195163a03b7f2bff4a](https://rinkeby.etherscan.io/address/0xe7410170f87102df0055eb195163a03b7f2bff4a)
* Goerli 测试网络，部署在 [0x112234455c3a32fd11230c42e7bccd4a84e02010](https://goerli.etherscan.io/address/0x112234455c3a32fd11230c42e7bccd4a84e02010)



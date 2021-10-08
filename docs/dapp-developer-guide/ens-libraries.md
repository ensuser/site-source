---
part: ENS 中文文档
title: ENS 库 
---

ENS 支持多种主流语言。如果有些你知道的 ENS 库没有在本页面陈列出来，请 [向我们发起 PR（pull request）](https://github.com/ensdomains/ens/compare)。

### Javascript

* [ensjs](https://www.npmjs.com/package/@ensdomains/ensjs)，由 ENS 开发者维护
* [ethereum-ens](https://www.npmjs.com/package/ethereum-ens)（不推荐）
* [react-ens-address](https://github.com/ensdomains/react-ens-address)
* [ethers.js](https://github.com/ethers-io/ethers.js)
* [web3.js](https://web3js.readthedocs.io/en/1.0/web3-eth-ens.html)
* [embark.io](https://framework.embarklabs.io/docs/naming_configuration.html)
* [waffle.io](https://ethereum-waffle.readthedocs.io/en/latest/ens.html)

#### 我应该使用哪个 Javascript 库

如果你用过 web3.js 或 ethers.js ，并且不需要使用创建子域名、转移所有权或更新解析器等功能，那么你可以使用这些库内置的 ENS 特性。

如果你在用 React ，并且只需要在 UI 界面中对 ENS 域名进行正向和反向解析，那么你可以使用 react-ens-address。

如果你用过 ethers.js ，并且只需要对 ENS 域名进行正向和反向解析，那么你可以使用 ethers.js 库中对 ENS 的支持。

如果你想要将 ENS 实例部署到您的开发环境中，那么你可以使用 embark.io 或 waffle.io ，它们可以让你在以太坊测试实例中配置和部署 ENS 注册表。

其他情况下，建议使用 ensjs 库。

#### 直接访问智能合约

当前所有的 ENS 智能合约都是作为 `@ensdomains/ens-contracts` [npm 模块](https://github.com/ensdomains/ens-contracts) 发布的。

在前端代码中包含 ABI 的方法：

```text
import {
  ENS,
  PublicResolver
} from '@ensdomains/ens-contracts'`
```

在 Solidity 中导入 ENS 智能合约的方法：

```text
import '@ensdomains/ens-contracts/contracts/registry/ENS.sol';
```

### Java

* [web3j](https://github.com/web3j/web3j)

### Kotlin

* [KEthereum](https://github.com/komputing/KEthereum/tree/master/ens)

### Python

* [web3.py](https://github.com/ethereum/web3.py) - also see [web3.py ENS docs](https://web3py.readthedocs.io/en/stable/ens_overview.html)

### Go

* [go-ens](https://github.com/wealdtech/go-ens)

### Command-line

* [ethereal](https://github.com/wealdtech/ethereal)

### Delphi

* [delphereum](https://github.com/svanas/delphereum)

## 后续工作

选定使用哪个库以后，就可以通过阅读 [ENS 的使用](working-with-ens.html) ，来学习如何在应用程序中使用你选择的 ENS 库。

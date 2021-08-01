---
part: ENS 中文文档
title: ENS 的使用
---

在开始与 ENS 交互之前，首先要引用 ENS 注册表，引用 ENS 注册表的方式取决于你使用了哪个 ENS 库。

下面的代码是基于 Javascript 的 API（ensjs、web3.js、ethjs-ens和ethers.js）的使用示例，这些代码适合运行在一个引入了 `ethereum` 对象的 DApp 浏览器中，比如安装了 [metamask](https://metamask.github.io/metamask-docs/Main_Concepts/Getting_Started) 的 Chrome。

采用 **ensjs** 时：

```javascript
import ENS, { getEnsAddress } from '@ensdomains/ensjs'

const ens = new ENS({ provider, ensAddress: getEnsAddress('1') })
```

采用 **web3.js** 时：

```javascript
var Web3 = require("web3")

var accounts = ethereum.enable();
var web3 = new Web3(ethereum);
var ens = web3.eth.ens;
```

采用 **ethjs-ens** 时：

```javascript
const ENS = require('ethjs-ens');
// Currently requires both provider and
// either a network or registryAddress param
var accounts = ethereum.enable();
const ens = new ENS({ ethereum, network: '1' });
```

采用 **ethers.js** 时：

```javascript
var ethers = require('ethers');
var provider = new ethers.providers.Web3Provider(ethereum);
// ENS functionality is provided directly on the core provider object.
```

采用 **go-ens** 时：

```go
import (
  ens "github.com/wealdtech/go-ens/v2"
  ethereum "github.com/ethereum/go-ethereum"
)

// Can dial up a connection through either IPC or HTTP/HTTPS
client, err := ethereum.Dial("/home/ethereum/.ethereum/geth.ipc")
registry, err := ens.Registry(client)
```

采用 **web3.py** 时：

```python
from ens.auto import ns
```

采用 **web3j** 时：

```java
EnsResolver ens = new EnsResolver(web3j, 300 /* sync threshold, seconds */);
```

一些 web3 库（例如 ethers.js 、web3j 和 web3.py）已经内置了对域名解析的支持。在这些库中，只要可以使用地址的地方，都可以直接使用 ENS 域名，也就是说，除非你想手动解析域名或是进行其他特殊的 ENS 操作，否则你根本不需要直接与它们的 ENS API 交互。

如果你的平台没有可用的库，你可以使用 [这里](https://github.com/ensdomains/ens/blob/master/contracts/ENS.sol) 的接口定义直接实例化 ENS 注册表合约。在 [ENS 部署情况](../ens-deployments.html) 页面中可以找到各个网络的 ENS 注册表地址。

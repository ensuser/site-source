---
part: ENS 中文文档
title: 在私有链上部署 ENS 
---

如果你希望在自己的网络上部署 ENS，或者在公共网络上部署自己的 ENS 副本，本指南将向你展示如何进行部署。如果你想使用现有的 ENS 部署，请参阅 [域名解析](dapp-developer-guide/resolving-names.html)、[域名管理](dapp-developer-guide/managing-names.html) 和 [域名注册和续费](dapp-developer-guide/registering-and-renewing-names.html)。

为了简单起见，在本页面我们会用到 Javascript 、Web3 和 npm 上的 [Truffle 框架](https://truffleframework.com/)。完整的部署文件示例可以 [在这个页面的底部](deploying-ens-on-a-private-chain.html#部署文件示例) 查看。

## 引用合约

ENS 的基础合约已经以 npm 模块的方式发布，比如 [ENS 注册表和注册器](https://www.npmjs.com/package/@ensdomains/ens)、[解析器](https://www.npmjs.com/package/@ensdomains/resolver)，你可以在你的 Truffle 或 npm 项目中，通过 `npm install @ensdomains/ens` 和 `npm install @ensdomains/resolver` 这两条命令来安装它们。现在，你还可以在部署脚本中按照下面的方式请求它们（与 npm 和 artifacts 包进行交互的详细信息请参阅 [Truffle Documentation](https://truffleframework.com/docs/truffle/getting-started/package-management-via-npm)）

```javascript
var ENS = artifacts.require("@ensdomains/ens/ENSRegistry");
```

在你的智能合约中引用 ENS 合约的方式如下：

```javascript
import "@ensdomains/ens/contracts/ENS.sol";
```

`ENS` 只包含了接口信息，而 `ENSRegistry` 则包含着实际的实现.

## 部署注册表

注册表是 ENS 的核心组件并存储着 ENS 的关键信息，其中包括谁拥有哪个域名等信息。你可以使用 Truffle 部署脚本来部署注册表。

```javascript
var ENS = artifacts.require("@ensdomains/ens/ENSRegistry");

module.exports = function(deployer) {
  deployer.deploy(ENS);
};
```

完成部署后，将会产生一个新的 ENS 注册表，它的根节点由实施部署事务的帐户拥有。该帐户对 ENS 注册表拥有完全的控制权，它可以创建和替换整个域名树中的任何节点。

进行到这一步，已经可以像 [域名管理](dapp-developer-guide/managing-names.html) 中所描述的那样，通过直接操作注册表来创建和管理域名。但是，你可能还想要 [部署一个解析器](deploying-ens-on-a-private-chain.html#部署解析器)，然后再 [部署一个注册器](deploying-ens-on-a-private-chain.html#部署注册器)，以便于其他用户注册域名。

## 部署解析器

注册表中的记录可以指向解析器合约，而解析器合约中存储着域名的一些相关信息，其中最常见的用例是存储域名的地址，但是也可以存储合约的 ABI 或文本。有一个不受限制的通用解析器，对于私有网络上的大多数用途来说是很方便的。部署一个解析器也很简单:

```javascript
const ENS = artifacts.require("@ensdomains/ens/ENSRegistry");
const PublicResolver = artifacts.require("@ensdomains/resolver/PublicResolver");

module.exports = function(deployer, network, accounts) {
  // Registry
  deployer.deploy(ENS)
  // Resolver
  .then(function(ensInstance) {
    return deployer.deploy(PublicResolver, ens.address);
  })
};
```

公共解析器 [`PublicResolver`](https://github.com/ensdomains/resolvers/blob/master/contracts/PublicResolver.sol) 需要在注册表中查询域名的所有权，因此部署解析器时需要用到注册表的地址信息。

为便于使用，我们可以给这个解析器起个名：

```javascript
const utils = require('web3-utils');
const namehash = require('eth-ens-namehash');

async function setupResolver(ens, resolver, accounts) {
  const resolverNode = namehash.hash("resolver");
  const resolverLabel = utils.sha3("resolver");

  await ens.setSubnodeOwner("0x0000000000000000000000000000000000000000", resolverLabel, accounts[0]);
  await ens.setResolver(resolverNode, resolver.address);
  await resolver.setAddr(resolverNode, resolver.address);
}
```

在上面的代码中，我们首先创建一个新的顶级域名 “resolver” ，然后将其解析器的地址设置为新部署的公共解析器，然后我们为 “resolver” 建立了一个域名解析记录，这个记录又指向了公共解析器地址。实际上，公共解析器返回的是关于其自身地址的查询结果。这样，每个人都可以通过这个专用的 ENS 域名 “resolver” 找到公共解析器。完成公共解析器部署后，我们会在 `.then()` 代码块中调用这个解析器设置函数。

## 部署注册器

进行到这一步，域名还只能由注册表中根节点的所有者来手动注册。幸运的是，合约也可以成为节点的所有者，也就是说我们可以在注册表中将一个注册器合约设置为节点的所有者（如 “test” 的所有者），这样，子域名的分发（如 “mycontract.test”）就可以由注册器合约来自动完成。这种机制使得我们能够以基于分布式的链上控制逻辑来管理域名的分发。在获得一个（子）节点的所有权之后，我们便能够以这种方式为其配置注册器。假设你是 “myorg” 组织的成员，你注册了 “myorg.test” 并将它指向了自定义注册器，这个注册器可以设置为只允许经过组织认证的成员才能申请像 “bob.myorg.test” 这样的子域名。对于示例中的私有网络，我们使用了较为简单的即时注册器 [FIFSRegistrar](/contracts/ens/fifsregistrar.html) ，并在部署脚本中将其设置为顶级域名 “test” 的所有者:

```javascript
...
.then(function() {
  return deployer.deploy(FIFSRegistrar, ens.address, ens.address, namehash.hash("test"));
})
.then(function(registrarInstance) {
  return ens.setSubnodeOwner("0x0000000000000000000000000000000000000000", sha3("test"), registrarInstance.address);
})
...
```

## 部署反向注册器

如果你希望在部署 ENS 时启用反向解析，就需要部署反向注册器:

```javascript
...
  .then(function() {
    return deployer.deploy(ReverseRegistrar, ens.address, resolver.address);
  })
  .then(function(reverseRegistrarInstance) {
    return setupReverseRegistrar(ens, resolver, reverseRegistrarInstance, accounts);
  })
...
})

async function setupReverseRegistrar(ens, resolver, reverseRegistrar, accounts) {
  await ens.setSubnodeOwner("0x0000000000000000000000000000000000000000", utils.sha3("reverse"), accounts[0]);
  await ens.setSubnodeOwner(namehash.hash("reverse"), utils.sha3("addr"), reverseRegistrar.address);
}
```

## 部署文件示例

我们可以将上述步骤合并到一个 Truffle 文件中，以便于一次性完成 ENS 部署:

```javascript
const ENS = artifacts.require("@ensdomains/ens/ENSRegistry");
const FIFSRegistrar = artifacts.require("@ensdomains/ens/FIFSRegistrar");
const ReverseRegistrar = artifacts.require("@ensdomains/ens/ReverseRegistrar");
const PublicResolver = artifacts.require("@ensdomains/resolver/PublicResolver");

const utils = require('web3-utils');
const namehash = require('eth-ens-namehash');

const tld = "test";

module.exports = function(deployer, network, accounts) {
  let ens;
  let resolver;
  let registrar;

  // Registry
  deployer.deploy(ENS)
  // Resolver
  .then(function(ensInstance) {
    ens = ensInstance;
    return deployer.deploy(PublicResolver, ens.address);
  })
  .then(function(resolverInstance) {
    resolver = resolverInstance;
    return setupResolver(ens, resolver, accounts);
  })
  // Registrar
  .then(function() {
    return deployer.deploy(FIFSRegistrar, ens.address, namehash.hash(tld));
  })
  .then(function(registrarInstance) {
    registrar = registrarInstance;
    return setupRegistrar(ens, registrar);
  })
  // Reverse Registrar
  .then(function() {
    return deployer.deploy(ReverseRegistrar, ens.address, resolver.address);
  })
  .then(function(reverseRegistrarInstance) {
    return setupReverseRegistrar(ens, resolver, reverseRegistrarInstance, accounts);
  })
};

async function setupResolver(ens, resolver, accounts) {
  const resolverNode = namehash.hash("resolver");
  const resolverLabel = utils.sha3("resolver");

  await ens.setSubnodeOwner("0x0000000000000000000000000000000000000000", resolverLabel, accounts[0]);
  await ens.setResolver(resolverNode, resolver.address);
  await resolver.setAddr(resolverNode, resolver.address);
}

async function setupRegistrar(ens, registrar) {
  await ens.setSubnodeOwner("0x0000000000000000000000000000000000000000", utils.sha3(tld), registrar.address);
}

async function setupReverseRegistrar(ens, resolver, reverseRegistrar, accounts) {
  await ens.setSubnodeOwner("0x0000000000000000000000000000000000000000", utils.sha3("reverse"), accounts[0]);
  await ens.setSubnodeOwner(namehash.hash("reverse"), utils.sha3("addr"), reverseRegistrar.address);
}
```

### 一次性完成 ENS 部署

在有些情况下，你可能希望一次性完成注册器及其依赖的部署工作，这在单元测试中很有用，因为我们都希望单元测试是个从头开始的完整测试。而且在很多情况下，整体部署也比提交一系列单独的事务要快。

我们可以创建一个新的合约，并在该合约的构造函数中创建和设置所需的ENS合约，然后通过部署这个新合约来实现一次性部署。下面的合约代码中包含了所有必要的 ENS 合约，并将 eth 这一顶级域名的所有权分配给了 FIFS 注册器，这样任何 eth 域名都可以在这个单元测试中进行注册。

```text
pragma solidity ^0.5.0;

import "@ensdomains/ens/contracts/ENSRegistry.sol";
import "@ensdomains/ens/contracts/FIFSRegistrar.sol";
import "@ensdomains/ens/contracts/ReverseRegistrar.sol";
import "@ensdomains/resolvers/contracts/PublicResolver.sol";

// Construct a set of test ENS contracts.
contract TestDependencies {
  bytes32 constant TLD_LABEL = keccak256("eth");
  bytes32 constant RESOLVER_LABEL = keccak256("resolver");
  bytes32 constant REVERSE_REGISTRAR_LABEL = keccak256("reverse");
  bytes32 constant ADDR_LABEL = keccak256("addr");

  ENSRegistry public ens;
  FIFSRegistrar public fifsRegistrar;
  ReverseRegistrar public reverseRegistrar;
  PublicResolver public publicResolver;

  function namehash(bytes32 node, bytes32 label) public pure returns (bytes32) {
    return keccak256(abi.encodePacked(node, label));
  }

  constructor() public {
    ens = new ENSRegistry();
    publicResolver = new PublicResolver(ens);

    // Set up the resolver
    bytes32 resolverNode = namehash(bytes32(0), RESOLVER_LABEL);

    ens.setSubnodeOwner(bytes32(0), RESOLVER_LABEL, address(this));
    ens.setResolver(resolverNode, address(publicResolver));
    publicResolver.setAddr(resolverNode, address(publicResolver));

    // Create a FIFS registrar for the TLD
    fifsRegistrar = new FIFSRegistrar(ens, namehash(bytes32(0), TLD_LABEL));

    ens.setSubnodeOwner(bytes32(0), TLD_LABEL, address(fifsRegistrar));

    // Construct a new reverse registrar and point it at the public resolver
    reverseRegistrar = new ReverseRegistrar(ens, Resolver(address(publicResolver)));

    // Set up the reverse registrar
    ens.setSubnodeOwner(bytes32(0), REVERSE_REGISTRAR_LABEL, address(this));
    ens.setSubnodeOwner(namehash(bytes32(0), REVERSE_REGISTRAR_LABEL), ADDR_LABEL, address(reverseRegistrar));
  }
}
```

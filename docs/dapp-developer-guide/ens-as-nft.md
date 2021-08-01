---
part: ENS 中文文档
title: ENS 作为一种 NFT
---

自从 ENS 的 `.eth` 注册器在 2019 年 5 月完成迁移后，`.eth` 注册器已经成为一个符合 [ERC721](https://github.com/ensdomains/ens/blob/master/docs/ethregistrar.rst#id3) 标准的 NFT（非同质化代币）合约，这意味着 .eth 域名可以像其他 NFT 那样进行转移。

## 从 ENS 域名派生 tokenId

ENS 域名的 tokenId 其实是以 uint256 形式来表示域名标签的哈希值（比如，域名 `vitalik.eth` 的标签是 `vitalik`）。

```javascript
const ethers = require('ethers')
const BigNumber = ethers.BigNumber
const utils = ethers.utils
const name = 'vitalik'
const labelHash = utils.keccak256(utils.toUtf8Bytes('vitalik'))
const tokenId = BigNumber.from(labelHash).toString()
```

在上面的示例中，[`79233663829379634837589865448569342784712482819484549289560981379859480642508`](https://opensea.io/assets/0x57f1887a8bf19b14fc0df6fd9b2acc9af147ea85/79233663829379634837589865448569342784712482819484549289560981379859480642508) 就是域名 `vitalik.eth` 的 tokenId。

## 从 tokenId 派生 ENS 域名

与派生 tokenId 不同，从 tokenId 派生 ENS 域名并不容易。这是因为所有 ENS 域名都存储为固定长度的哈希，以便允许注册长度不限的域名。这种架构的缺点是不能使用 tokenId 直接查询 ENS 智能合约来返回 ENS 域名。

我们推荐的方式是通过 [TheGraph 服务](https://thegraph.com) 中的 ENS 子图来查询。TheGraph 可以通过其索引将哈希值解码为域名。查询的示例代码如下。

```javascript
const ethers = require('ethers')
const BigNumber = ethers.BigNumber
const gr = require('graphql-request')
const { request, gql } = gr
const tokenId = '79233663829379634837589865448569342784712482819484549289560981379859480642508'
// Should return 0xaf2caa1c2ca1d027f1ac823b529d0a67cd144264b2789fa2ea4d63a67c7103cc
const labelHash = BigNumber.from(tokenId).toHexString()
const url = 'https://api.thegraph.com/subgraphs/name/ensdomains/ens'
const GET_LABEL_NAME = gql`
query{
  domains(first:1, where:{labelhash:"${labelHash}"}){
    labelName
  }
}`
request(url, GET_LABEL_NAME).then((data) => console.log(data))
// { domains: [ { labelName: 'vitalik' } ] }
```

如果你不喜欢依赖 TheGraph 这样的第三方，开源的 [ENS-rainbow](https://github.com/graphprotocol/ens-rainbow) 包含了原始数据集（6GB，内含 1.33 亿条数据）的链接，这样你就可以托管自己的 ENS 解码服务。

## 将子域名转换为 NFT

目前，所有的子域名或非 `.eth` 域名都不是 NFT（比如 `dcl.eth` 和 `.kred` ），除非域名注册器本身支持 NFT 标准。如果你想将自己所有的子域名转换成 NFT，你必须创建一个注册器：

1. 创建一个符合 ERC721 标准的注册器合约
2. 设置 ENS 注册表地址（主要是在部署注册器时）
3. 创建 `register` 函数用来调用 `registry.setSubnodeOwner`，然后通过将子域标签哈希转换为 tokenId 来生成 NFT。

```text
contract DCLRegistrar is ERC721Full, Ownable {
    constructor(
        IENSRegistry _registry,
    ) public ERC721Full("DCL Registrar", "DCLENS") {
        // ENS registry
        updateRegistry(_registry);
    }
    function register(
        string memory _subdomain,
        bytes32 subdomainLabelHash,
        address _beneficiary,
        uint256 _createdDate
    ) internal {
        // Create new subdomain and assign the _beneficiary as the owner
        registry.setSubnodeOwner(domainNameHash, subdomainLabelHash, _beneficiary);
        // Mint an ERC721 token with the sud domain label hash as its id
        _mint(_beneficiary, uint256(subdomainLabelHash));
    }
}
```

一旦部署完成，您就必须将控制器地址转移到该合约。

对于非技术用户，我们目前正在升级我们的 `SubdomainRegistrar`，它能让你不用写代码也可以将你的子域名变成 NFT。

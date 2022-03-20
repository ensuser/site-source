---
part: ENS 主要合约
title: ENS 注册表合约 - ENS 的核心合约
---

## 介绍

ENS 注册表合约是 ENS 系统中的核心合约，当前[在用的注册表合约](https://cn.etherscan.com/address/0x00000000000C2E074eC69A0dFb2997BA6C7d2e1e#code)（ENSRegistryWithFallback）是升级版本（带回退功能，兼容[原始版本](https://cn.etherscan.com/address/0x314159265dd8dbb310642f98f50c066173c1259b)），在 2020 年 1 月 30 日部署完成，如果不出意外，这也会是最终版。

ENS 注册表合约的功能是维护所有名称和子名称列表，并存储关于每个名称的三个关键信息：名称的所有者、名称的解析器、名称下所有记录的缓存存活时间（即 TTL）。您可以在 [ENS 架构](/docs/readme.html#ENS-架构)中查看注册表的工作原理。

## ENS 注册表合约的部分关键交易（前 7 笔交易）

| No. | 合约名称 | 相关交易 | 发送方 | 调用函数 | 备注 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | [ENSRegistryWithFallback](https://cn.etherscan.com/address/0x00000000000c2e074ec69a0dfb2997ba6c7d2e1e) | [0x00df8](https://cn.etherscan.com/tx/0x00df88239dcc77e499f4ed3bad25bc58cc30663a26bc7c531decff94e861b9bf "Jan-30-2020 12:37:12 AM") | [Deployer-0x4fe4e](https://cn.etherscan.com/address/0x4fe4e666be5752f1fdd210f4ab5de2cc26e3e0e8) | 无 | 新 ENS 合约创建 |
| 2 | ENSRegistryWithFallback | [0x36a85](https://cn.etherscan.com/tx/0x36a8544bc597d7d74ca66c118345e500cfc1dca0dbac02f340a588c62724f119 "Jan-30-2020 12:44:54 AM") | Deployer-0x4fe4e | setSubnodeRecord | 设置 eth 的解析器为 [OwnedResolver-0x30200](https://cn.etherscan.com/address/0x30200e0cb040f38e474e53ef437c95a1be723b2b#code)、所有者为 [BaseRegImp-0x57f18](https://cn.etherscan.com/address/0x57f1887a8bf19b14fc0df6fd9b2acc9af147ea85#code "BaseRegistrarImplementation-0x57f18") |
| 3 | ENSRegistryWithFallback | [0x09a36](https://cn.etherscan.com/tx/0x09a368aeb586bb6810e5af65a051c33b4d2c1f33c8b636bc3262fcfaeadbbe7d "Jan-30-2020 12:49:03 AM") | [Wallet1-0x0904d](https://cn.etherscan.com/address/0x0904dac3347ea47d208f3fd67402d039a3b99859) | setResolver | 设置 migrated.eth 的解析器为 [OwnedResolver-0x30200](https://cn.etherscan.com/address/0x30200e0cb040f38e474e53ef437c95a1be723b2b#code) |
| 4 | ENSRegistryWithFallback | [0x6f410](https://cn.etherscan.com/tx/0x6f410b83a468197dd0de7eb31e3f18fcfe6bf961080f7520b9a8b7d69ab87bb9 "Jan-30-2020 01:05:30 AM") | [Deployer-0x4fe4e](https://cn.etherscan.com/address/0x4fe4e666be5752f1fdd210f4ab5de2cc26e3e0e8) | setSubnodeOwner | 设置 reverse 的所有者为 [Deployer-0x4fe4e](https://cn.etherscan.com/address/0x30200e0cb040f38e474e53ef437c95a1be723b2b#code "OwnedResolver-0x30200") |
| 5 | ENSRegistryWithFallback | [0x7311c](https://cn.etherscan.com/tx/0x7311cdefc63aaf6226110ec9d682714f878ebdc115d064c491e802e664849e47 "Jan-30-2020 01:05:43 AM") | [Deployer-0x4fe4e](https://cn.etherscan.com/address/0x4fe4e666be5752f1fdd210f4ab5de2cc26e3e0e8) | setSubnodeOwner | 设置 addr.reverse 的所有者为 [ReverseRegistrar-0x084b1](https://cn.etherscan.com/address/0x084b1c3c81545d370f3634392de611caabff8148#code "OwnedResolver-0x30200") |
| 6 | ENSRegistryWithFallback | [0xebc1b](https://cn.etherscan.com/tx/0xebc1b87ad98d8735367c8c60137a998a0fe74be8ee7b107fda835975681aae8f "Jan-30-2020 01:07:04 AM") | [Deployer-0x4fe4e](https://cn.etherscan.com/address/0x4fe4e666be5752f1fdd210f4ab5de2cc26e3e0e8) | setOwner | 设置 reverse 的所有者为 [Dead-0x00000](https://cn.etherscan.com/address/0x0000000000000000000000000000000000000000) |
| 7 | ENSRegistryWithFallback | [0xf296f](https://cn.etherscan.com/tx/0xf296f9b8d5143c6fca65b010cb9d621d0574dca1950ba8115f5c9f9161f661b6 "Jan-30-2020 01:09:41 AM") | [Deployer-0x4fe4e](https://cn.etherscan.com/address/0x4fe4e666be5752f1fdd210f4ab5de2cc26e3e0e8) | setSubnodeOwner | 设置 xyz 的所有者为 [DNSRegistrar-0xa2f42](https://cn.etherscan.com/address/0xa2f428617a523837d4adc81c67a296d42fd95e86#code) |
| 8 | ENSRegistryWithFallback | [0xe120b](https://cn.etherscan.com/tx/0xe120b74ce60f64d6f2c289588f645b83c47b5e0c20c97b0be13adfdd93289b51 "Jan-30-2020 01:13:10 AM") | [Deployer-0x4fe4e](https://cn.etherscan.com/address/0x4fe4e666be5752f1fdd210f4ab5de2cc26e3e0e8) | setOwner | 设置根名称的所有者为 [Root-0xab528](https://cn.etherscan.com/address/0xab528d626ec275e3fad363ff1393a41f581c5897#code) |

### 部分关键名称的相关信息

- "name": "eth",
  - "node": "0x93cdeb708b7545dc668eb9280176169d1c33cfd8ed6f04690a0bcc88a93fc4ae",
  - "labelName": "eth",
  - "labelhash": "0x4f5b812789fc606be1b3b16908db13fc7a9adf7ca72641f84d75b47069d3d7f0",
  - "parent-node": "0x0000000000000000000000000000000000000000000000000000000000000000"
- "name": "reverse",
  - "node": "0xa097f6721ce401e757d1223a763fef49b8b5f90bb18567ddb86fd205dff71d34",
  - "labelName": "reverse",
  - "labelhash": "0xdec08c9dbbdd0890e300eb5062089b2d4b1c40e3673bbccb5423f7b37dcf9a9c",
  - "parent-node": "0x0000000000000000000000000000000000000000000000000000000000000000"
- "name": "addr.reverse"
  - "node": "0x91d1777781884d03a6757a803996e38de2a42967fb37eeaca72729271025a9e2",
  - "labelName": "addr",
  - "labelhash": "0xe5e14487b78f85faa6e1808e89246cf57dd34831548ff2e6097380d98db2504a",
  - "parent-node": "0xa097f6721ce401e757d1223a763fef49b8b5f90bb18567ddb86fd205dff71d34"
- "name": "xyz",
  - "node": "0xa87a11c7f15e38a7398517fda2ae1b40d870aa24b34b4b09aa09afc71f2c9d26",
  - "labelName": "xyz",
  - "labelhash": "0x9dd2c369a187b4e6b9c402f030e50743e619301ea62aa4c0737d4ef7e10a3d49",
  - "parent-node": "0x0000000000000000000000000000000000000000000000000000000000000000"

## 关于原版注册表（ENSRegistry）的故事

现在部署的注册表是在 2020 年 2 月份部署的升级版本，它的上一个版本，也是最早的 [ENS 注册表](https://cn.etherscan.com/address/0x314159265dd8dbb310642f98f50c066173c1259b#code)，是用 LLL 语言编写的，在被更新前的两年多时间里一直稳定地为 ENS 提供可靠支持。关于为什么采用 LLL 语言来编写合约，ENS 首席工程师 Nick 这样回复：

> We wrote ENS back when Solidity was still very new, and fairly inefficient. The LLL implementation was a lot more efficient, as well as producing bytecode that was simple enough it could be decompiled to verify it exactly matched the implementation. These days, Solidity is a lot more mature, so there’s a lot less reason to do this kind of thing.
>
> 我们开始编写 ENS 的时候，Solidity 还是一门新兴语言，而且相当低效。LLL语言则要高效许多，且可以对其进行反编译，以验证它与实现完全匹配。现在，Solidity 已经是一门成熟的语言，所以已经没有太多必要去采用 LLL。

下面我们来看看第一版注册表刚创建时的一些关键交易，回顾一下 ENS 最初的样子：

### 原版注册表的部分关键交易（前 7 笔交易）

| No. | 合约名称 | 相关交易 | 发送方 | 调用函数 | 备注 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | [ENS-0x31415](https://cn.etherscan.com/address/0x314159265dd8dbb310642f98f50c066173c1259b) | [0x40ea7](https://cn.etherscan.com/tx/0x40ea7c00f622a7c6699a0013a26e2399d0cd167f8565062a43eb962c6750f7db "Mar-10-2017 05:05:44 PM") | [0x8a582](https://cn.etherscan.com/address/0x8a582c1a18f7d381bf707cf0b535533016221398) | 无 | ENS 合约创建 |
| 2 | ENS-0x31415 | [0xe120d](https://cn.etherscan.com/tx/0xe120d656744084c3906a59013ec2bcaf35bda6b3cc770f2001acd4c15efbd353 "Mar-10-2017 05:06:48 PM") | 0x8a582 | setOwner | 设置根域所有者为 [0x8472d](https://cn.etherscan.com/address/0x8472d6206f381ebf71a174b9de9e61b0e1962da4) |
| 3 | ENS-0x31415 | [0x057a1](https://cn.etherscan.com/tx/0x057a18943891fc4defd54ff6b18c4fa1e15b822f299f2f08117e4fd11d44f971 "Mar-11-2017 04:22:22 AM") | [0x8472d](https://cn.etherscan.com/address/0x8472d6206f381ebf71a174b9de9e61b0e1962da4) | setSubnodeOwner | 设置 .eth 所有者为 [TempRegistrar-0x01223](https://cn.etherscan.com/address/0x012233b3c8177f0778d910ed88170b82de3bfe57) |
| 4 | ENS-0x31415 | [0x27fbd](https://cn.etherscan.com/tx/0x27fbd8651661cff3c30cdd651381d090e1cfc10ae0b89403b43d6281be6f9c97 "Mar-11-2017 04:23:26 AM") | 0x8472d | setSubnodeOwner | 设置 .reverse 所有者为 [0x8472d](https://cn.etherscan.com/address/0x8472d6206f381ebf71a174b9de9e61b0e1962da4) |
| 5 | ENS-0x31415 | [0x24ae9](https://cn.etherscan.com/tx/0x24ae9be611a5a40c52263bf69090c6beadb7ab106aae768105d23cd16851da23 "Mar-11-2017 04:29:01 AM") | 0x8472d | setSubnodeOwner | 设置 .addr.reverse 所有者为 [ReverseRegistrarOld-0xda7fa](https://cn.etherscan.com/address/0xda7fa6e0b04c76683f54c973931862d7fe474a85) |
| 6 | ENS-0x31415 | [0x8b0bc](https://cn.etherscan.com/tx/0x8b0bc15f3d1f922668c37321e449e47b5dd1cbe1080bffd8a3313cf045a55b73 "Mar-11-2017 04:29:01 AM") | 0x8472d | setOwner | 设置 .reverse 所有者为 [0x00000](https://cn.etherscan.com/address/0x0000000000000000000000000000000000000000) |
| 7 | ENS-0x31415 | [0xf10f3](https://cn.etherscan.com/tx/0xf10f37e848ca26fd12bbe373a1df8f6a96def9b1899c58689fc7c08bb022ad37 "Mar-13-2017 05:37:46 AM") | 0x8472d | setOwner | 设置根域所有者为 [MultiSig-0x91114](https://cn.etherscan.com/address/0x911143d946ba5d467bfc476491fdb235fef4d667) |
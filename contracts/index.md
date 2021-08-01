---
part: ENS 主要合约
title: ENS 主要合约 - 概述
---

![](/images/contracts/screenshot.png)

ENS 是通过部署在以太坊上的一系列智能合约实现的，要深入了解 ENS 的实现和工作原理，不仅要理解[文档内容](/docs/)，还应该学习和研究这些已经部署的智能合约。学习之前，我们应该清楚以下几点：

1. ENS 不是由一个智能合约实现的，而是由一系列互相关联的合约共同构成的；
2. ENS 还没有开发完毕，官方团队还在不断地进行功能的完善和长久计划的制订，所以这些合约未来可能还会发生变化；
3. ENS 中最核心的合约是 [Eth Name Service](/contracts/ensregistry.html) 合约，即 ENS 注册表合约，自 ENS 诞生以来，它一直稳定运行至今，这也是 ENS 整个系统可以长久存在的基础。

## ENS 合约基本信息

我们将部署在以太坊主网的 ENS 主要合约的信息进行了整理，这些合约的基本信息如下。

| No. | 合约地址 | 合约名称 | 中文名称及介绍 |
| :--- | :--- | :--- | :--- |
| 01 | [0x00000](https://cn.etherscan.com/address/0x000000000000000000000000000000000000dead) | Burn Address | 烧毁地址 |
| 02 | [0x31415](https://cn.etherscan.com/address/0x314159265dd8dbb310642f98f50c066173c1259b) | Eth Name Service | ENS 注册表 |
| 03 | [0x00000](https://cn.etherscan.com/address/0x00000000000c2e074ec69a0dfb2997ba6c7d2e1e) | Registry with Fallback | ENS 注册表（带回退） |
| 04 | [0x57f18](https://cn.etherscan.com/address/0x57f1887a8bf19b14fc0df6fd9b2acc9af147ea85) | Base Registrar Implementation | 基本注册器实现 |
| 05 | [0x283af](https://cn.etherscan.com/address/0x283af0b28c62c092c9727f1ee09c02ca627eb7f5) | ETH Registrar Controller | ETH 注册控制器 |
| 06 | [0x084b1](https://cn.etherscan.com/address/0x084b1c3c81545d370f3634392de611caabff8148) | Reverse Registrar | 反向注册器 |
| 07 | [0xa2c12](https://cn.etherscan.com/address/0xa2c122be93b0074270ebee7f6b7292c7deb45047) | Default Reverse Resolver | 默认反向解析器 |
| 08 | [0xdaaf9](https://cn.etherscan.com/address/0xdaaf96c344f63131acadd0ea35170e7892d3dfba) | Public Resolver 1 | 公共解析器 1 |
| 09 | [0x4976f](https://cn.etherscan.com/address/0x4976fb03c32e5b8cfe2b6ccb31c09ba78ebaba41) | Public Resolver 2 | 公共解析器 2 |
| 10 | [0xab528](https://cn.etherscan.com/address/0xab528d626ec275e3fad363ff1393a41f581c5897) | Root | 根域 |
| 11 | [0x91114](https://cn.etherscan.com/address/0x911143d946ba5d467bfc476491fdb235fef4d667) | Multisig | 多重签名 |
| 12 | [0x0904d](https://cn.etherscan.com/address/0x0904dac3347ea47d208f3fd67402d039a3b99859) | Wallet | 官方钱包 |
| 13 | [0xb6e04](https://cn.etherscan.com/address/0xb6e040c9ecaae172a89bd561c5f73e1c48d28cd9) | Wallet 2 | 官方钱包 2 |
| 14 | [0xc3265](https://cn.etherscan.com/address/0xc32659651d137a18b79925449722855aa327231d) | Subdomain Registrar | 子域名注册器 |
| 15 | [0xe65d8](https://cn.etherscan.com/address/0xe65d8aaf34cb91087d1598e0a15b582f57f217d9) | Migration Subdomain Registrar | 迁移子域名注册器 |
| 16 | [0xf7c83](https://cn.etherscan.com/address/0xf7c83bd0c50e7a72b55a39fe0dabf5e3a330d749) | Short Name Claims | 短域名声明 |
| 17 | [0xb9d37](https://cn.etherscan.com/address/0xb9d374d0fe3d8341155663fae31b7beae0ae233a) | Stable Price Oracle | 稳定价格预言机 |
| 18 | [0xff252](https://cn.etherscan.com/address/0xff252725f6122a92551a5fa9a6b6bf10eb0be035) | Bulk Renewal | 批量续费 |
| 19 | [0xa2f42](https://cn.etherscan.com/address/0xa2f428617a523837d4adc81c67a296d42fd95e86) | DNS Registrar | DNS 注册器 |
| 20 | [0x4fe4e](https://cn.etherscan.com/address/0x4fe4e666be5752f1fdd210f4ab5de2cc26e3e0e8) | Deployer | 部署器 |
| 21 | [0x60c7c](https://cn.etherscan.com/address/0x60c7c2a24b5e86c38639fd1586917a8fef66a56d) | Registrar Migration | 注册器迁移 |

如果您能够熟练使用 Etherscan 浏览器，您也可以从 [Etherscan 上的 ENS 标签页](https://cn.etherscan.com/accounts/label/ens) 开始，去进一步发掘它们的详细内容。

## ENS 已经弃用的旧合约

ENS 在迭代的过程中，不可避免地更换了一部分合约，这些旧合约也曾经发挥过重要作用。

| No. | 合约地址 | 合约名称 | 中文名称及介绍 |
| :--- | :--- | :--- | :--- |
| 01 | [0x6090a](https://cn.etherscan.com/address/0x6090a6e47849629b7245dfa1ca21d94cd15878ef) | Old Registrar | 旧 ETH 注册控制器 |
| 02 | [0x31415](https://cn.etherscan.com/address/0x314159265dd8dbb310642f98f50c066173c1259b) | Eth Name Service | ENS 注册表 |


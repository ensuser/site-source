---
part: ENS 主要合约
title: 旧 Root 合约 - 曾经管理过 ENS 根域的合约
---

ENS 官方团队在 2020 年 1 月底的[系统迁移](/docs/ens-migration-february-2020/technical-description.html)过程中，部署了新的 [Root 合约](https://cn.etherscan.com/address/0xab528d626ec275e3fad363ff1393a41f581c5897#code)，这个新合约带 “锁死” 功能。

以下介绍的合约及相关内容已经过时，该内容只作为学习研究 ENS 及其发展过程使用。

## 旧 Root 合约关键交易（所有 3 笔交易）

| No. | 合约名称 | 相关交易 | 发送方 | 调用函数 | 备注 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | [Root-0x28508](https://cn.etherscan.com/address/0x285088c75a8508664ad77df63e2d60a408e5284a#code) | [0x5180e](https://cn.etherscan.com/tx/0x5180eb518fe63bb0b5fe22ed4ad21b4e2eb1cc1caddce9d6089007905742f811 "May-30-2019 11:47:02 PM") | [Wallet-1-0x0904d](https://cn.etherscan.com/address/0x0904dac3347ea47d208f3fd67402d039a3b99859) | 无 | Root 合约创建 |
| 2 | Root-0x28508 | [0x13b3f](https://cn.etherscan.com/tx/0x13b3f93c7b373e39b757d463cac6f9cb496be0cc1cb45214bb24113e2ab48a74 "May-30-2019 11:53:18 PM") | Wallet-1-0x0904d | setController | 设置 [MultiSig-0x91114](https://cn.etherscan.com/address/0x911143d946ba5d467bfc476491fdb235fef4d667) 为 Root 管理员 |
| 3 | Root-0x28508 | [0x664f7](https://cn.etherscan.com/tx/0x664f7946eb29fe3109bdb43bc049273581fc9bdd1316d5b9433178b97f507d6c "May-30-2019 11:53:41 PM") | Wallet-1-0x0904d | transferOwnership | 转让 Root 所有权至 [MultiSig-0x91114](https://cn.etherscan.com/address/0x911143d946ba5d467bfc476491fdb235fef4d667) |

## 附：ENS 根域及顶级域名相关信息

| No. | 域名 | labelhash | node | 所有者 | 备注 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | （根域） | 无 | [0x00000000](#0x0000000000000000000000000000000000000000000000000000000000000000) | [Root-0x28508](https://cn.etherscan.com/address/0x285088c75a8508664ad77df63e2d60a408e5284a#code) | ENS 根域 |
| 2 | .eth | [0x4f5b8127](#0x4f5b812789fc606be1b3b16908db13fc7a9adf7ca72641f84d75b47069d3d7f0) | [0x93cdeb70](#0x93cdeb708b7545dc668eb9280176169d1c33cfd8ed6f04690a0bcc88a93fc4ae) | [BaseRegImp-0xfac7b](https://cn.etherscan.com/address/0xfac7bea255a6990f749363002136af6556b31e04#code) | 以太坊原生顶级域名 |
| 3 | .reverse | [0xdec08c9d](#0xdec08c9dbbdd0890e300eb5062089b2d4b1c40e3673bbccb5423f7b37dcf9a9c) | [0xa097f672](#0xa097f6721ce401e757d1223a763fef49b8b5f90bb18567ddb86fd205dff71d34) | [MultiSig-0x91114](https://cn.etherscan.com/address/0x911143d946ba5d467bfc476491fdb235fef4d667#code) | 反向解析顶级域名 |
| 4 | .addr.reverse | [0xe5e14487](#0xe5e14487b78f85faa6e1808e89246cf57dd34831548ff2e6097380d98db2504a) | [0x91d17777](#0x91d1777781884d03a6757a803996e38de2a42967fb37eeaca72729271025a9e2) | [ReverseRegistrar-0x9062c](https://cn.etherscan.com/address/0x9062c0a6dbd6108336bcbe4593a3d1ce05512069#code) | 用于设置反向解析的二级域名 |
| 5 | .xyz | [0x9dd2c369](#0x9dd2c369a187b4e6b9c402f030e50743e619301ea62aa4c0737d4ef7e10a3d49) | [0xa87a11c7](#0xa87a11c7f15e38a7398517fda2ae1b40d870aa24b34b4b09aa09afc71f2c9d26) | [DNSRegistrar-0xf7004](https://cn.etherscan.com/address/0xf7004095d2d81fe3b5937241c106aace6d6e8e4a#code) | 从 DNS 接入的顶级域名 |
| 6 | .luxe | [0xee84dffa](#0xee84dffab1ae8677065fb36c735dc6a4e040f53be90836b5a5743b6bd981273c) | [0xb6168d4e](#0xb6168d4e6a16769316251939e18834097d5b028bd14398823528e541ac0caa3a) | [OwnedRegistrar-0xa86ba](https://cn.etherscan.com/address/0xa86ba3b6d83139a49b649c05dbb69e0726db69cf#code) | |
| 7 | .kred | [0xe528c3cd](#0xe528c3cd6fdd088c4790dd1fb1db9962d86b4fc900da22c5f459f606ab5bfad2) | [0x4f2e511b](#0x4f2e511b8dd5304e6d9c043002a10e8366c6257f17356787c009cc68e04b78e3) | [0x56ca9](https://cn.etherscan.com/address/0x56ca9514363f68d622931dce1566070f86ce5550) | |
| 8 | .club | [0xe1e1884f](#0xe1e1884f7473923c2fed5da5f5489310ba525a82307b50b511b8963ee9b20113) | [0xd5355203](#0xd53552031df0ddd8f71f29c82a1b2774cca7622b89fe6e927aff0c0ca902f16b) | [0x1eb4b](https://cn.etherscan.com/address/0x1eb4b8506fca65e6b229e346dfbfd349956a66e3) | |
| 9 | .art | [0x5c3967f2](#0x5c3967f2b1d87e07afa360938c88d98d4bf767b9d432ec3eb7ee85774b4faf01) | [0x57137a55](#0x57137a55874a85f753ba591944179c9d5f5b7be9c94d606506fa34450f09e655) | [0xfd438](https://cn.etherscan.com/address/0xfd438c1f260d45387bb1a73fbb7704d3fb09f782#code) | |

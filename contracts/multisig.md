---
part: ENS 主要合约
title: Multisig 多重签名合约 - ENS 的控制者
---


ENS 团队最初是用一套自己编写的 MultiSig 多重签名合约作为 ENS 根域合约 Root 的控制者，但后来 ENS 团队采用了 Gnosis Safe 服务提供的多重签名合约，也就是将 Root 合约的控制权通过[一笔交易](https://cn.etherscan.com/tx/0x78c7eb4db4de4588b7c9abab3cf44c58e58b66aad3157aa146f10f61d00be7a5)转让给了一个 [Gnosis Safe 代理合约](https://cn.etherscan.com/address/0xcf60916b6cb4753f58533808fa610fcbd4098ec0)。在那之后，控制根域的 7 个密钥管理者的就一直使用新的代理合约来签署交易。

尽管如此，ENS 根域最初的管理活动都是通过团队自己编写的那套 MultiSig 多重签名合约来完成的，所以对于 ENS 研究者来说，是有必要了解这个合约的。

## 介绍

关于 [MultiSig-0x91114](https://cn.etherscan.com/address/0x911143d946ba5d467bfc476491fdb235fef4d667) 合约的几个说明：

1. 它是一个多重签名账户合约，并设置了 7 个控制账户；
2. 这 7 个账户中的任一账户均可从该合约发起交易，发起合约交易的账户会同时第 1 个为该交易签名；
3. 合约交易在 4 个控制账户完成签名后才能生效，也就是除了发起交易的账户之外还需要有 3 个账户签名；
4. 第 4 个账户完成签名之后，交易确认完毕，该账户就会自动执行这笔合约交易；
5. 合约交易执行后，如果其他账户再次为该合约交易进行签名，就会签名失败。

## Multisig 合约的部分关键交易

| No. | 合约名称 | 相关交易 | 发送方 | 调用函数 | 备注 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | [MultiSig-0x91114](https://cn.etherscan.com/address/0x911143d946ba5d467bfc476491fdb235fef4d667) | [0xe1224](https://cn.etherscan.com/tx/0xe12246c66ecc4e6b74d3455806e69c0f522063557dd62a9baba9c1bb3d025e38 "Mar-13-2017 05:33:20 AM") | [0x8472d](https://cn.etherscan.com/address/0x8472d6206f381ebf71a174b9de9e61b0e1962da4) | 无 | Multisig 合约创建 |
| 2 | MultiSig-0x91114 | [0x04ee3](https://cn.etherscan.com/tx/0x04ee3beb4642bd711d881557218f1341d352d6b2534cda8dd0ad578b7ed1d6d8 "Mar-14-2017 07:42:09 AM") | [0xfdb33](https://cn.etherscan.com/address/0xfdb33f8ac7ce72d7d4795dd8610e323b4c122fbb) | ENS:setSubnodeOwner | 0#：设置 .eth 的所有者为 [TempRegistrar-0x18c6c](https://cn.etherscan.com/address/0x18c6ce63b7a10daba7cfe5edd375fcef0b83cd39#code) |
| 3 | MultiSig-0x91114 | [0x6294f](https://cn.etherscan.com/tx/0x6294f79fd9893a6466cbf1f3cd11b013126fbeb49b3edea6b9b5d8db3d26ffc3 "Mar-14-2017 01:48:13 PM") | 0xfdb33 | ENS:setSubnodeOwner | 1#：设置 .eth 所有者为 0x00000 |
| 4 | MultiSig-0x91114 | [0x158de](https://cn.etherscan.com/tx/0x158de0666c49884497210921baad8817b7c02184b403a67570becd36e92e98db "Apr-26-2017 07:26:25 PM") | 0xfdb33 | ENS:setSubnodeOwner | 2#：设置 .eth 所有者为 [TempRegistrar-0x6090a](https://cn.etherscan.com/address/0x6090a6e47849629b7245dfa1ca21d94cd15878ef#code) |
| 5 | MultiSig-0x91114 | [0x51589](https://cn.etherscan.com/tx/0x51589fdfaae37961d77596a7cea4871f87b4ca73100b6ae2888d302800d174be "May-29-2017 02:06:25 PM") | 0xfdb33 | ENS:setSubnodeOwner | 3#：设置 .reverse 所有者为 [MultiSig-0x91114](https://cn.etherscan.com/address/0x911143d946ba5d467bfc476491fdb235fef4d667) |
| 6 | MultiSig-0x91114 | [0xb0ec5](https://cn.etherscan.com/tx/0xb0ec55233886f8f3a2a03aed82a81ac55323ffe2b19c3955cf4c1054e0b0f6c0 "May-29-2017 02:14:45 PM") | 0xfdb33 | ENS:setSubnodeOwner | 4#：设置 .addr.reverse 所有者为 [ReverseRegistrar-0x9062c](https://cn.etherscan.com/address/0x9062c0a6dbd6108336bcbe4593a3d1ce05512069#code) |
| 7 | MultiSig-0x91114 | [0xc2c1e](https://cn.etherscan.com/tx/0xc2c1eb50442f2c904957b24d6ec8d4c75f03b53926dfee51d6fc711d17da710e "May-29-2017 02:14:45 PM") | 0xfdb33 | MultiSig:replaceOwner | 5#：将多重账户中 [0x5c359](https://cn.etherscan.com/address/0x5c35939706c8b4c8d8f95801a9c903de9a2af937) 替换为 [0x3ac6c](https://cn.etherscan.com/address/0x3ac6cb2ccfd8c8aae3ba31d7ed44c20d241b16a4) |
| 8 | MultiSig-0x91114 | [0x5a467](https://cn.etherscan.com/tx/0x5a467dec07101b987c9de405059f1b95e7cb2b10dd8fc78837e85f2eae379e31 "Aug-29-2018 03:19:23 PM") | 0xfdb33 | ENS:setSubnodeOwner | 6#：设置 .xyz 所有者为 [DNSRegistrar-0xf7004](https://cn.etherscan.com/address/0xf7004095d2d81fe3b5937241c106aace6d6e8e4a#code) |
| 9 | MultiSig-0x91114 | [0xdad5d](https://cn.etherscan.com/tx/0xdad5de00ada30a9a0091fcba596aa344860c1aec19eab321b9e39e3ae5744c28 "Aug-29-2018 03:20:01 PM") | 0xfdb33 | ENS:setSubnodeOwner | 7#：设置 .luxe 所有者为 [0x765b1](https://cn.etherscan.com/address/0x765b1171c917d842b7536de3155d709f3428c981) |
| 10 | MultiSig-0x91114 | [0x71e94](https://cn.etherscan.com/tx/0x71e94b941236acbc4a483d6e6941bedbafc9cb2b025b123ded66dce6ef1e33bb "Jan-06-2019 10:18:08 PM") | 0xfdb33 | ENS:setSubnodeOwner | 8#：设置 .kred 所有者为 [0x56ca9](https://cn.etherscan.com/address/0x56ca9514363f68d622931dce1566070f86ce5550) |
| 11 | MultiSig-0x91114 | [0xe4a5b](https://cn.etherscan.com/tx/0xe4a5b8a99153ad0ea36d8acf39018c3ecf6808df35f554f8f1cd9ec3358bd6d4 "Feb-11-2019 10:39:54 PM") | 0xfdb33 | ENS:setSubnodeOwner | 9#：设置 .club 所有者为 [0x1eb4b](https://cn.etherscan.com/address/0x1eb4b8506fca65e6b229e346dfbfd349956a66e3) |
| 12 | MultiSig-0x91114 | [0x9f790](https://cn.etherscan.com/tx/0x9f790575492b18f349d16f098b39eec0e5550afee4bb7f4ea43dc9e5e6cbf872 "Apr-30-2019 12:20:56 PM") | [0xec186](https://cn.etherscan.com/address/0xec1867e2597b1499e34210cd0cc086924f0d0ebe) | ENS:setSubnodeOwner | a#：设置 .eth 所有者为 [BaseRegImp-0xfac7b](https://cn.etherscan.com/address/0xfac7bea255a6990f749363002136af6556b31e04#code) |
| 13 | MultiSig-0x91114 | [0x092ba](https://cn.etherscan.com/tx/0x092ba91e4d019dfabf5a182b03b26ee1672e5db30669fe8521a50649ab40beba "Apr-30-2019 12:26:07 PM") | 0xec186 | BaseRegImp:setResolver | b#：设置 .eth 的解析器为 [OwnedResolver-0x97683](https://cn.etherscan.com/address/0x97683a370239817cf33ec2c2ad3b3a1884571f69#code) |
| 14 | MultiSig-0x91114 | [0x4ee5d](https://cn.etherscan.com/tx/0x4ee5d7a187519d6786540fa3dbe2ae5fc434237bc98307802c2a54aec55722ad "May-31-2019 12:31:31 AM") | 0xfdb33 | ENS:setOwner | c#：设置根域的所有者为 [Root-0x28508](https://cn.etherscan.com/address/0x285088c75a8508664ad77df63e2d60a408e5284a#code) |
| 15 | MultiSig-0x91114 | [0xe30c2](https://cn.etherscan.com/tx/0xe30c288d9a3f5b2eb4b717209fec83f9d95a74fd9ffeca021f7508c0571f3e9b "Jun-05-2019 02:32:17 AM") | 0xfdb33 | Root:setController | d#：设置 [NameBazaarRescue-0x2a2a1](https://cn.etherscan.com/address/0x2a2a19b4f47cd6a752dae1bd1096a74fece93342#code) 为 Root 管理员 |
| 16 | MultiSig-0x91114 | [0xb407a](https://cn.etherscan.com/tx/0xb407a385b90af8bdd11c123323f06f0471883b9b5b881c4d4c05ea53947a8253 "Jul-21-2019 10:20:31 PM") | 0xfdb33 | BaseRegImp:addController | e#：添加 [ShortNameClaims-0xf7c83](https://cn.etherscan.com/address/0xf7c83bd0c50e7a72b55a39fe0dabf5e3a330d749#code) 为 [BaseRegImp-0xfac7b](https://cn.etherscan.com/address/0xfac7bea255a6990f749363002136af6556b31e04#code) 管理员 |
| 17 | MultiSig-0x91114 | [0xd7d13](https://cn.etherscan.com/tx/0xd7d1338e2992a91f40481cc8da417206771ca66701f8870c5ea3f83ba006d4f4 "Jul-21-2019 10:24:36 PM") | 0xfdb33 | MultiSig:replaceOwner | f# (!)：将多重账户中 [0xfdb33](https://cn.etherscan.com/address/0xfdb33f8ac7ce72d7d4795dd8610e323b4c122fbb) 替换为 [0xb8c2c](https://cn.etherscan.com/address/0xb8c2c29ee19d8307cb7255e1cd9cbde883a267d5) |
| 18 | MultiSig-0x91114 | [0x57d9f](https://cn.etherscan.com/tx/0x57d9f7e86118cb0cff6df49e1ad57d0161c18506d3ce4580ca2f9bc26d2c00b4 "Aug-13-2019 08:55:54 PM") | 0xfdb33 | MultiSig:replaceOwner | 10# (!)：将多重账户中 [0xfdb33](https://cn.etherscan.com/address/0xfdb33f8ac7ce72d7d4795dd8610e323b4c122fbb) 替换为 [0xb8c2c](https://cn.etherscan.com/address/0xb8c2c29ee19d8307cb7255e1cd9cbde883a267d5) |
| 19 | MultiSig-0x91114 | [0x5e372](https://cn.etherscan.com/tx/0x5e37279847698f60c1b7743404f6c1b7b5b8a06c21dd8018425cbb4de2d930a0 "Aug-13-2019 08:59:00 PM") | 0xfdb33 | Root:setSubnodeOwner | 11#：设置 .art 所有者为 [0xbaf54](https://cn.etherscan.com/address/0xbaf547935ac43004f6926140512fcaefcfd534c5) |
| 20 | MultiSig-0x91114 | [0xbcb42](https://cn.etherscan.com/tx/0xbcb4275bcc94d0d625a1907b69285cd13d63e4d9438de99a8e648e957c0992ae "Sep-05-2019 12:45:34 AM") | 0xfdb33 | BaseRegImp:addController | 12#：添加 [ShortNameAuctionController](https://cn.etherscan.com/address/0x699c7f511c9e2182e89f29b3bfb68bd327919d17#code) 为 [BaseRegImp-0xfac7b](https://cn.etherscan.com/address/0xfac7bea255a6990f749363002136af6556b31e04#code) 管理员 |
| 21 | MultiSig-0x91114 | [0x00acd](https://cn.etherscan.com/tx/0x00acd178479f5f2b0cdd4ab509ccddf0744a85763de77d14f0c694bfb1ce6daf "Nov-04-2019 01:40:18 AM") | 0xfdb33 | BaseRegImp:addController | 13#：添加 [ETHRegistrarController-0xb22c1](https://cn.etherscan.com/address/0xb22c1c159d12461ea124b0deb4b5b93020e6ad16#code) 为 BaseRegImp-0xfac7b 管理员 |
| 22 | MultiSig-0x91114 | [0xe2957](https://cn.etherscan.com/tx/0xe29573870e013b92d26450a9cb0b2c5172a31ac591404fd66053be1f89fd2bf6 "Nov-06-2019 05:48:27 PM") | 0xfdb33 | BaseRegImp:addController | 14# (?)：添加 [ETHRegistrarController-0xd931ab](https://cn.etherscan.com/address/0xd931ab1c9df3eb507edd074c8182017b6f1e672b#code) 为 BaseRegImp 管理员 |

## MultiSig 合约交易解析

账户 [0xfdb33](https://cn.etherscan.com/address/0xfdb33f8ac7ce72d7d4795dd8610e323b4c122fbb) 在 May-29-2017 02:06:25 PM 发起了一笔交易： [0x51589](https://cn.etherscan.com/tx/0x51589fdfaae37961d77596a7cea4871f87b4ca73100b6ae2888d302800d174be) ，交易的数据输入如下：

``` text
Function: submitTransaction(address destination, uint256 value, bytes data)

MethodID: 0xc6427474
[0]:  000000000000000000000000314159265dd8dbb310642f98f50c066173c1259b
[1]:  0000000000000000000000000000000000000000000000000000000000000000
[2]:  0000000000000000000000000000000000000000000000000000000000000060
[3]:  0000000000000000000000000000000000000000000000000000000000000064
[4]:  06ab592300000000000000000000000000000000000000000000000000000000
[5]:  00000000dec08c9dbbdd0890e300eb5062089b2d4b1c40e3673bbccb5423f7b3
[6]:  7dcf9a9c000000000000000000000000911143d946ba5d467bfc476491fdb235
[7]:  fef4d66700000000000000000000000000000000000000000000000000000000
```

[0] 提供 `destination` 参数，即该交易的目标地址是 [0x31415](https://cn.etherscan.com/address/0x314159265dd8dbb310642f98f50c066173c1259b) ，这是 ENS 合约的地址 ；
[1] 提供 `value` 参数，即该交易发送的 ETH 数量为 0 ；
[3] 十六进制 64 转换为十进制为 100，表示负载数据为 100 字节，包括函数 ID（分配 4 字节）及其 3 个参数（每个参数分配 32 字节）；
[4] - [7] 提供 `data` 参数，包含的信息如下：

1. 调用目标合约中 ID 为 [06ab5923](https://github.com/ensdomains/ens/blob/master/contracts/ENS.lll#L25) 的函数，即 [set-subnode-owner](https://github.com/ensdomains/ens/blob/master/contracts/ENS.lll#L223) 函数
2. 该函数需要接收 3 个参数 `node` `label` `new-owner`
3. `node` 参数的值为：`00000...0000`
4. `label` 参数的值为：`dec08...f7b3` ，这是 `reverse` 的哈希
5. `new-owner` 参数的值为：`91114...d667`

从以上信息中，可以判断出来，该交易执行的是将顶级名称 `.reverse` 的所有者设置为 `0x91114...d667`（即 Multisig 多签合约）。

## MultiSig 合约的 7 个密钥管理者账户

通过[这个页面](https://cn.etherscan.com/address/0xcf60916b6cb4753f58533808fa610fcbd4098ec0#readProxyContract)上的 `getOwners` 选项卡可以查看当前 7 个管理者的以太坊地址。

下表中是 ENS 根域最初的 7 个管理员地址。以下介绍的合约及相关内容已经过时，该内容只作为学习研究 ENS 及其发展过程使用。

| No. | 初始账户 | 当前账户 | 备注 |
| :--- | :--- | :--- | :--- |
| 1 | 0xfdb33 | [0xfdb33](https://cn.etherscan.com/address/0xfdb33f8ac7ce72d7d4795dd8610e323b4c122fbb) | |
| 2 | 0x55e27 | [0x55e27](https://cn.etherscan.com/address/0x55e2780588aa5000f464f700d2676fd0a22ee160) | |
| 3 | 0x24b13 | [0x24b13](https://cn.etherscan.com/address/0x24b139e561874594b738c2735a24b8ea52b82571) | |
| 4 | 0x98287 | [0x98287](https://cn.etherscan.com/address/0x98287874532b83ccf25a8539b67530ae8fc1d004) | |
| 5 | 0xc1eaf | [0xc1eaf](https://cn.etherscan.com/address/0xc1eaf60420e382269f510517d5cf80da5db57c52) | |
| 6 | 0xec186 | [0xec186](https://cn.etherscan.com/address/0xec1867e2597b1499e34210cd0cc086924f0d0ebe) | |
| 7 | [0x5c359](https://cn.etherscan.com/address/0x5c35939706c8b4c8d8f95801a9c903de9a2af937) | [0x3ac6c](https://cn.etherscan.com/address/0x3ac6cb2ccfd8c8aae3ba31d7ed44c20d241b16a4) | 变更于 MultiSig 合约的 5# 交易|

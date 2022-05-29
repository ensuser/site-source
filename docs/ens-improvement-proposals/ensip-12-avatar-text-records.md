---
part: ENS 中文文档
subpart: ensip
title: 'ENSIP-12: 头像文本记录'
description: 在 ENS 中存储头像文本记录的标准。
---

| **作者**    | Nick Johnson \<nick@ens.domains>, Makoto Inoue \<makoto@ens.domains> |
| ------------- | -------------------------------------------------------------------- |
| **状态**    | 草案                                                                |
| **提交时间** | 2022-01-18                                                           |

### 摘要

这个 ENSIP 定义了从 ENS 检索头像 URI 的过程、ENS 'avatar' 文本字段的几个 [URI](https://datatracker.ietf.org/doc/html/rfc3986) 方案，以及希望显示用户头像的客户端应该如何解译这些方案。

### 动机

ENS 主名称 (以前称为反向记录) 已经被广泛集成为许多基于以太坊的应用程序的 web3 用户名。随着多个应用程序开始指定头像并允许用户将 NFT 作为头像，现在将头像信息存储在 ENS 中以便在不同应用程序之间共享头像信息的做法已经司空见惯。

该规范使用 [ENSIP-5: 头像文本记录](ensip-5-text-records.html)将存储和检索这些信息的方法进行了标准化。

### 规范

#### 检索头像 URI

检索头像 URI 的过程取决于客户端是否从以太坊地址或 ENS 名称开始。

#### ENS 名称

为了确定一个 ENS 名称的头像 URI，客户端必须首先在解析器中查找这个名称并调用 `.text(namehash(name), 'avatar')` 来检索这个名称的头像 URI。

客户端必须将以下情况视为找不到有效的头像 URI：解析器不存在；在解析器上调用 `addr` 方法时遇到的回退；或者解析器返回空字符串。

#### 以太坊地址

为了确定一个以太坊地址的头像 URI，客户端必须通过在 ENS 注册表中查询 `<address>.addr.reverse` 的解析器来反向解析这个地址，其中 `<address>` 是小写十六进制编码的以太坊地址，不带 '0x'。然后，客户端调用 `.text(namehash('<address>.addr.reverse'), 'avatar')` 来检索该地址的头像 URI。

如果一个解析器返回了反向记录，但是调用 `text` 时发生回退或返回一个空字符串，客户端必须调用 `.name(namehash('<address>.addr.reverse'))`。如果这个方法返回一个有效的 ENS 名称，客户端必须:

1. 通过解析返回的名称并在解析器上调用 `addr`，检查它是否与原始以太坊地址匹配，以此来验证反向记录是有效的。
2. 执行上文中 'ENS 名称' 部分描述的过程，查找名称对应的有效的头像 URI。

这个过程中任何一步遇到失败都必须被客户端视为找不到有效的头像 URI。

#### 通用格式

'avatar' 文本字段必须格式化为 URI。客户端必须忽略他们不能识别的 URI 类型，将它们看作没有设置该字段一样。

#### 图像类型

客户端必须支持 mime 类型为 `image/jpeg`、`image/png` 和 `image/svg+xml` 的图像。客户端可以支持其他的图像类型。

#### URI 类型

所有客户端都应该支持下面定义的 URI 方案。它们可以实现本规范中未定义的附加方案。

**`https`**

如果提供的是 https URI，它必须直接解析为头像图像。https URL 不能解析为 HTML 页面、元数据或其他包含头像的内容。

**`ipfs`**

如果提供的是 [ipfs URI](https://docs.ipfs.io/how-to/address-ipfs-on-web/#native-urls)，它必须直接解析为头像图像。没有内置 IPFS 支持的客户端可以在解析为 https URL 之前，将 URI 重写为引用 IPFS 网关的 https URL，如[这篇文档](https://docs.ipfs.io/how-to/address-ipfs-on-web/)所述。

**`data`**

如果提供的是 [data URL](https://datatracker.ietf.org/doc/html/rfc2397)，它必须直接解析为头像图像。

**`NFT`**

对 NFT 的引用可以作为头像 URI 使用，遵循在 [CAIP-22](https://github.com/ChainAgnostic/CAIPs/blob/master/CAIPs/caip-22.md) 和 [CAIP-29](https://github.com/ChainAgnostic/CAIPs/blob/master/CAIPs/caip-29.md) 中定义的标准。

客户端必须至少支持 ERC721 和 ERC1155 类型的 NFT，并且可以支持其他类型的 NFT。

要解析 NFT URI，客户端遵循以下过程:

1. 检索 `avatar` 字段 URI 中指定的代币的元数据 URI。
2. 解析元数据 URI，获取 ERC721 或 ERC1155 元数据。
3. 提取 NFT 元数据中指定的图像 URL。
4. 解析图像 URL 并使用它作为头像。

客户端必须至少支持 `https` 和 `ipfs` URI 来解析元数据 URI 和头像，并且可以支持其他的方案。客户端可以通过如上所述将 URI 重写为引用 IPFS 网关的 HTTPS URL 来实现 `ifps` 方案的支持。

客户端还应采取以下验证步骤:

1. 在通过正向解析 (从 ENS 名称开始) 检索头像 URI 的位置，在同一个解析器上针对同一个名称调用 `addr` 函数，来检索名称解析到的以太坊地址。否则，如果头像 URI 是通过反向解析 (从以太坊地址开始) 检索的，则使用该地址。
2. 验证步骤 1 中的地址是 URI 中指定的 NFT 的所有者。如果不是，客户端必须将 URI 视为无效，并按照与没有指定头像 URI 时相同的方式进行处理。

客户端可以通过重写为 `https` URI 来支持 NFT 头像解析服务。

### 示例

以下示例均解析至同一个头像图片:

```
eip155:1/erc721:0xbc4ca0eda7647a8ab7c2061c2e118a18a936f13d/0 # BAYC token 0
ipfs://QmRRPWG96cmgTn2qSzjwr2qvfNEuhunv6FNeMFGa9bx6mQ # IPFS hash for BAYC token 0 image
https://ipfs.io/ipfs/QmRRPWG96cmgTn2qSzjwr2qvfNEuhunv6FNeMFGa9bx6mQ # HTTPS URL to IPFS gateway for BAYC token 0 image
```

### 向后兼容性

不适用。

### 安全注意事项

无。

### 版权

通过 [CC0](https://creativecommons.org/publicdomain/zero/1.0/) 放弃版权及相关权利。

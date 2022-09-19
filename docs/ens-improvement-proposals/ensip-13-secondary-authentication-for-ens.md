---
part: ENS 中文文档
subpart: ensip
title: 'ENSIP-13: ENS 安全验证'
description: 利用 ENS 文本记录，让签名操作更安全、更方便。
---

| **作者**    | Wilkins Chung (@wwhchung), Jalil Wahdatehagh (@jwahdatehagh), Cry (@crydoteth), Sillytuna (@sillytuna), Cyberpnk (@CyberpnkWin) |
| ------------- | ---------------- |
| **状态**      | 草案   |
| **提交时间**   | 2022-08-03  |

## 摘要

该 EIP 通过以太坊名称服务规范 ([EIP-137](https://eips.ethereum.org/EIPS/eip-137)) 链接一个或多个签名钱包，以证明对主钱包的控制权和资产所有权。

## 动机

向以太坊生态系统中的第三方应用程序证明资产的所有权是很常见的。在获得执行某些操作的访问权之前，用户为了验证自己的身份，通常会对有效数据进行签名。然而，这种方法类似于让第三方访问一个人的主钱包，既不安全又不方便。

***示例:***

1. 为了编辑您在 OpenSea 上的个人资料，您必须用钱包签署一条消息。
2. 为了访问由 NFT 控制内容，您必须使用持有 NFT 的钱包签署消息，以证明所有权。
3. 为了获得对事件的访问权，您必须使用持有所需 NFT 的钱包签署消息，以证明所有权。
4. 为了申请空投，您必须使用符合条件的钱包与智能合约进行交互。
5. 为了证明 NFT 的所有权，您必须使用持有该 NFT 的钱包签署一条有效数据。

在上面的所有例子中，一个人使用钱包本身与 dApp 或智能合约进行交互，这可能:

- 不方便 (如果采用硬件钱包或多签合约的方式)
- 不安全 (因为上面的操作是只读的，但你却使用了一个有写访问权限的钱包进行签名或交互)

相反，用户应该能够批准其他钱包代表给定的钱包进行身份验证。

### 现有方法和解决方案存在的问题

不幸的是，我们遇到过很多用户意外签署恶意数据的情况，最后往往导致签名地址上损失大量资产。

除此之外，许多用户将他们的大部分资产放在 “冷钱包” 中。鉴于 “冷钱包” 方案代表着更高的安全性，用户会很自然地加强这些钱包的访问难度，因此这种方式的易用性往往会下降。

一些解决方案提出采用专用的注册表合约来创建这些链接，或支持新的协议。从实用的角度来看，这是有问题的，并且也没有任何相关的标准。

### 提案: 使用以太坊名称服务 (EIP-137)

相对于 “重复发明轮子”，该提案旨在使用已经被广泛采用的以太坊名称服务与 ENS 文本记录功能 ([EIP-634](https://eips.ethereum.org/EIPS/eip-634))，来实现更安全、更方便的签名方式和身份验证，并通过一个或多个二级钱包提供对主钱包的 “只读” 访问。

这样做的好处是双方面的。通过将潜在的恶意签名操作外放到更容易访问的钱包 (热钱包)，该 EIP 为用户提供了更高的安全性，同时能够维护不经常用于签名操作的钱包的预期安全性假设。

#### 提高 dApp 交互安全性

许多 dApp 需要用户证明自己控制了钱包才能访问。以目前的情况看，你必须先使用这个钱包与 dApp 交互才行。这是一个安全问题，因为在恶意的 dApp 或钓鱼网站上的恶意代码可能会利用签名信息盗取钱包的资产。

但是，如果在进行这类交互时使用的是二级钱包，这种风险就会降低。交互只能影响到二级钱包中的资产，而二级钱包可以几乎不持有任何有价值的东西。

#### 提高多设备接入安全性

为了让一个非硬件钱包在多个设备上使用，您必须将种子短语导入到每个设备。每在新设备上输入一次种子短语，接触种子短语的设备范围就会增加，钱包被破坏的风险也会随之增加。

相反，每个设备都可以有自己唯一的钱包，它是经过主钱包授权的二级钱包。如果某个设备上的钱包被盗或丢失，您可以直接删除用于身份验证的授权。

此外，可以对钱包的身份验证进行链接，以便二级钱包还可以授权一个或多个三级钱包，然后这些三级钱包对二级地址和主地址都有签名权限。这样每个团队都可以有自己的签名者，而主钱包只需要通过撤销根权限，就可以轻松地使所有层级的钱包失效。

#### 提高便利性

为了在最大程度上确保安全，许多人使用了硬件钱包。不过，因为许多人不想随身携带硬件钱包，所以硬件钱包往往不太方便。

相反，如果您授权了用于身份验证活动的非硬件钱包 (如移动设备)，那么您不需要随身携带硬件钱包就能够使用大多数 dApp。

## 规范

本文档中的关键词 “必须”、“绝对不能”、“必需”、“会”、“不会”、“应该”、“不应”、“推荐”、“可以”和“可选”应按照 RFC 2119 中的描述进行解释。

设:

- `mainAddress` 表示我们将要验证或证明资产所有权的钱包地址。
- `mainENS` 表示 `mainAddress` 的反向解析 ENS 字符串。
- `authAddress` 表示我们想要用来代替 `mainAddress` 签名的地址。
- `authENS` 表示 `authAddress` 的反向解析 ENS 字符串。
- `authKey` 表示一个字符串，格式为 `[0-9A-Za-z]+`。

如果满足以下所有条件，则证明 `authAddress` 能够控制 `mainAddress` 以及 `mainAddress` 的资产:

- `mainAddress` 有一个 ENS 解析记录和一个反向记录设置为 `mainENS`。
- `authAddress` 有一个 ENS 解析记录和一个反向记录设置为 `authENS`。
- `authENS` 有一条 ENS 文本记录 `eip5131:vault`，其格式为 `<authKey>:<mainAddress>`。
- `mainENS` 有一条 ENS 文本记录 `eip5131:<authKey>`。

### 在单个 ENS 域名上设置一个或多个 `authAddress` 记录

`mainAddress` **必须**配置 ENS 解析记录和反向记录。为了自动发现链接的帐户，`authAddress` **应该**配置 ENS 解析记录和反向记录。

1. 选择一个未使用的 `<authKey>`。可以是符合 `[0-0A-Za-z]+` 格式的任意字符串。
2. 在 `mainENS` 上设置一条 文本记录 `eip5131:<authKey>`，其值设置为所需的 `authAddress`。
3. 在 `authENS` 上设置一条 文本记录 `eip5131:vault`，其值设置为 `<authKey>:mainAddress`。

目前这个 EIP 没有限制包含 `authAddress` 条目的数量。用户可以使用任意数量的地址来重复这个过程。

### 通过 `authAddress` 验证 `mainAddress`

如果任何关联的 `authAddress` 是 `msg.sender` 或者已经签署了这条信息，则可证明其能够控制 `mainAddress` 和 `mainAddress` 的资产。

实际上，这可以通过执行以下操作来实现:

1. 获取 `authENS` 的解析器
2. 获取 `authENS` 的 `eip5131:vault` 文本记录
3. 解析 `<authKey>:<mainAddress>` 来确定 `authKey` 和 `mainAddress`。
4. **必须**获取 `mainAddress` 的反向 ENS 记录，并验证它与 `<mainENS>` 能够匹配。
   - 否则，可以设置其他 ENS 节点(带认证)指向 `mainAddress`，并通过这些验证。
5. 获取 `mainENS` 的 `eip5131:<authKey>` 文本记录，并确保它与 `authAddress` 能够匹配。

请注意，该规范同时支持智能合约及客户端/服务器端对签名进行验证。由于它不局限于智能合约，因此也没有提出外部接口定义。

### 撤销 `authAddress`

要撤销 `authAddress` 的权限，只需要删除 `mainENS` 的 `eip5131:<authKey>` 文本记录，或将其更新并指向一个新的 `authAddress`。

## 基本原理

### EIP-137 用例

由于目前 EIP-137 和 ENS 被广泛采用，因此建议使用 EIP-137 规范，而非引入另一个注册表范式。

然而，EIP-137 的缺点是所有关联的 `authAddress` 都必须有一些 ETH，以便设置 `authENS` 反向记录以及 `eip5131:vault` 文本记录。这可以通过一个独立的反向记录注册表来解决，该注册表允许 `mainAddress` 使用一条由 `authAddress` 签名的消息来设置反向记录和文本记录。

随着 L2 和 ENS Layer 2 功能的出现，即使在跨链管理域名的情况下，链外地址验证也是可行的。

### 一对多认证关系

该提案的规范允许一个 (`mainAddress`) 对多个 (`authAddress`) 的认证关系。例如，一个 `mainAddress` 可以授权多个 `authAddress` 进行认证，但 `authAddress` 只能认证自身或单个 `mainAddress`。

选择这种设计的原因是允许通过客户端和智能合约代码进行简单的身份验证，就可以确定 `authAddress` 签名的是哪个 `mainAddress`，而无需用户额外注明。

此外，您可以设计相应的 UX，在用户无需交互的情况下，依据显示出来的 `authAddress` 和 `mainAddress` 资产来 “选择” 交互地址，并根据用户希望验证的资产选用适当的地址。

## 实现参考

### 客户端/服务器端

使用 ether.js 验证函数的 typescript 代码如下:

```
export interface LinkedAddress {
  ens: string,
  address: string,
}

export async function getLinkedAddress(
  provider: ethers.providers.EnsProvider, address: string
): Promise<LinkedAddress | null> {
  const addressENS = await provider.lookupAddress(address);
  if (!addressENS) return null;

  const vaultInfo = await (await provider.getResolver(addressENS))?.getText('eip5131:vault');
  if (!vaultInfo) return null;

  const vaultInfoArray = vaultInfo.split(':');
  if (vaultInfoArray.length !== 2) {
    throw new Error('EIP5131: Authkey and vault address not configured correctly.');
  }

  const [ authKey, vaultAddress ] = vaultInfoArray;

  const vaultENS = await provider.lookupAddress(vaultAddress);
  if (!vaultENS) {
    throw new Error(`EIP5131: No ENS domain with reverse record set for vault.`);
  };

  const expectedSigningAddress = await (
    await provider.getResolver(vaultENS)
  )?.getText(`eip5131:${authKey}`);

  if (expectedSigningAddress?.toLowerCase() !== address.toLowerCase()) {
    throw new Error(`EIP5131: Authentication mismatch.`);
  };

  return {
    ens: vaultENS,
    address: vaultAddress
  };
}
```

### 合约端

#### 有后台的情况

如果你的应用运行着一个安全的后端服务器，你可以运行上面的客户端/服务器代码，然后将结果和 [EIP-1271](https://eips.ethereum.org/EIPS/eip-1271) 规范 `Standard Signature Validation Method for Contracts` 相结合，以一种廉价且安全的方式来验证消息签署者确实是由主地址认证的。

#### 无后台的情况 (仅有 JavaScript)

提供了内部函数的引用实现，用于验证消息发送者是否具有到主地址的身份验证链接。

```
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.0;

/// @author: manifold.xyz

/**
 * ENS Registry Interface
 */
interface ENS {
    function resolver(bytes32 node) external view returns (address);
}

/**
 * ENS Resolver Interface
 */
interface Resolver {
    function addr(bytes32 node) external view returns (address);
    function name(bytes32 node) external view returns (string memory);
    function text(bytes32 node, string calldata key) external view returns (string memory);
}

/**
 * Validate a signing address is associtaed with a linked address
 */
library LinkedAddress {
    /**
     * Validate that the message sender is an authentication address for mainAddress
     *
     * @param ensRegistry    Address of ENS registry
     * @param mainAddress     The main address we want to authenticate for.
     * @param mainENSNodeHash The main ENS Node Hash
     * @param authKey         The TEXT record of the authKey we are using for validation
     * @param authENSNodeHash The auth ENS Node Hash
     */
    function validateSender(
        address ensRegistry,
        address mainAddress,
        bytes32 mainENSNodeHash,
        string calldata authKey,
        bytes32 authENSNodeHash
    ) internal view returns (bool) {
        return validate(ensRegistry, mainAddress, mainENSNodeHash, authKey, msg.sender, authENSNodeHash);
    }

    /**
     * Validate that the authAddress is an authentication address for mainAddress
     *
     * @param ensRegistry     Address of ENS registry
     * @param mainAddress     The main address we want to authenticate for.
     * @param mainENSNodeHash The main ENS Node Hash
     * @param authAddress     The address of the authentication wallet
     * @param authENSNodeHash The auth ENS Node Hash
     */
    function validate(
        address ensRegistry,
        address mainAddress,
        bytes32 mainENSNodeHash,
        string calldata authKey,
        address authAddress,
        bytes32 authENSNodeHash
    ) internal view returns (bool) {
        _verifyMainENS(ensRegistry, mainAddress, mainENSNodeHash, authKey, authAddress);
        _verifyAuthENS(ensRegistry, mainAddress, authKey, authAddress, authENSNodeHash);

        return true;
    }

    // *********************
    //   Helper Functions
    // *********************
    function _verifyMainENS(
        address ensRegistry,
        address mainAddress,
        bytes32 mainENSNodeHash,
        string calldata authKey,
        address authAddress
    ) private view {
        // Check if the ENS nodes resolve correctly to the provided addresses
        address mainResolver = ENS(ensRegistry).resolver(mainENSNodeHash);
        require(mainResolver != address(0), "Main ENS not registered");
        require(mainAddress == Resolver(mainResolver).addr(mainENSNodeHash), "Main address is wrong");

        // Verify the authKey TEXT record is set to authAddress by mainENS
        string memory authText = Resolver(mainResolver).text(mainENSNodeHash, string(abi.encodePacked("eip5131:", authKey)));
        require(
            keccak256(bytes(authText)) == keccak256(bytes(_addressToString(authAddress))),
            "Invalid auth address"
        );
    }

    function _verifyAuthENS(
        address ensRegistry,
        address mainAddress,
        string memory authKey,
        address authAddress,
        bytes32 authENSNodeHash
    ) private view {
        // Check if the ENS nodes resolve correctly to the provided addresses
        address authResolver = ENS(ensRegistry).resolver(authENSNodeHash);
        require(authResolver != address(0), "Auth ENS not registered");
        require(authAddress == Resolver(authResolver).addr(authENSNodeHash), "Auth address is wrong");

        // Verify the TEXT record is appropriately set by authENS
        string memory vaultText = Resolver(authResolver).text(authENSNodeHash, "eip5131:vault");
        require(
            keccak256(abi.encodePacked(authKey, ":", _addressToString(mainAddress))) ==
                keccak256(bytes(vaultText)),
            "Invalid auth text record"
        );
    }

    bytes16 private constant _HEX_SYMBOLS = "0123456789abcdef";

    function sha3HexAddress(address addr) private pure returns (bytes32 ret) {
        uint256 value = uint256(uint160(addr));
        bytes memory buffer = new bytes(40);
        for (uint256 i = 39; i > 1; --i) {
            buffer[i] = _HEX_SYMBOLS[value & 0xf];
            value >>= 4;
        }
        return keccak256(buffer);
    }

    function _addressToString(address addr) private pure returns (string memory ptr) {
        // solhint-disable-next-line no-inline-assembly
        assembly {
            ptr := mload(0x40)

            // Adjust mem ptr and keep 32 byte aligned
            // 32 bytes to store string length; address is 42 bytes long
            mstore(0x40, add(ptr, 96))

            // Store (string length, '0', 'x') (42, 48, 120)
            // Single write by offsetting across 32 byte boundary
            ptr := add(ptr, 2)
            mstore(ptr, 0x2a3078)

            // Write string backwards
            for {
                // end is at 'x', ptr is at lsb char
                let end := add(ptr, 31)
                ptr := add(ptr, 71)
            } gt(ptr, end) {
                ptr := sub(ptr, 1)
                addr := shr(4, addr)
            } {
                let v := and(addr, 0xf)
                // if > 9, use ascii 'a-f' (no conditional required)
                v := add(v, mul(gt(v, 9), 39))
                // Add ascii for '0'
                v := add(v, 48)
                mstore8(ptr, v)
            }

            // return ptr to point to length (32 + 2 for '0x' - 1)
            ptr := sub(ptr, 33)
        }

        return string(ptr);
    }
}
```

## 安全注意事项

该 EIP 的核心目的是增强安全性，在不需要主钱包，也不需要移动主钱包持有的资产时，促进以更安全的方式验证钱包控制和资产所有权。可以将其视为一种执行 “只读” 身份验证的方法。

## 版权

通过 [CC0](https://creativecommons.org/publicdomain/zero/1.0/) 放弃版权及相关权利。

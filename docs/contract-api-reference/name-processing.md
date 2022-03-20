---
part: ENS 中文文档
title: ENS 名称处理
description: 描述如何规范化 ENS 名称以及如何将名称进行哈希.
---

ENS 只使用固定长度的256位加密哈希来代替可读的名称。为了从名称派生哈希的同时仍然保留其层次性，使用了名为 Namehash 的算法。例如，“alice.eth” 的 Namehash 为 _0x787192fc5378cc32aa956ddfdedbf26b24e8d78e40109add0eea2c1a012c3dec_ ，这是名称在 ENS 内部的唯一表示方式。

在使用 Namehash 进行哈希之前，首先使用 UTS-46 标准对名称进行规范化，确保名称中的字母与大小写无关，并禁止使用无效字符。任何对名称进行哈希和解析的操作都**必须**首先对其进行规范化，以确保所有用户获得 ENS 的一致性。

## 规范化名称

在使用 Namehash 将名称转换为哈希节点之前，必须首先对名称进行规范化和有效性检查（比如将 _fOO.eth_ 规范为 _foo.eth_）并屏蔽包含下划线等禁止字符的名称。更为关键的是，所有应用程序都必须遵循相同的规范化和验证规则集，否则相同的字符输入到不同系统上可能会被解析为两个不同的 ENS 名称。

使用ENS和处理可读名称的应用程序在进行规范化和验证时必须遵循 [UTS46](http://unicode.org/reports/tr46/) 。处理过程应该通过设置 `UseSTD3ASCIIRules=true` 采用非过渡规则。

[eth-ens-namehash](https://www.npmjs.com/package/@ensdomains/eth-ens-namehash) 这个 Javascript 库会执行这里描述的规范化和哈希。DApp 开发者指南中涉及的所有 [ENS 库](../dapp-developer-guide/ens-libraries.html) 都会执行规范化和哈希。

## 对名称进行哈希

Namehash 是一个递归过程，可以为任何有效的名称生成唯一的哈希。从任意一个名称的 Namehash 开始（比如 “alice.eth” 的 Namehash）可以推导出任意子名称的 Namehash（比如 “iam.alice.eth” 的 Namehash），而且推导过程中不需要知道或处理 “alice.eth” 这个可读的原始名称。正是这个特性使得 ENS 能够成为一个层次性的系统，且不必在内部处理可读的文本字符串。

### 术语

* 名称（domain）- ENS 标识符的完整且可读的形式，比如 _iam.alice.eth_ 。
* 标签（label）- 名称的独立组成部分 - 比如：_iam_ ，_alice_ 或 _eth_ 。
* 标签哈希（label hash）- 单个标签经过 keccak256 函数计算后的输出值，比如：`keccak256(‘eth’) = 0x4f5b812789fc606be1b3b16908db13fc7a9adf7ca72641f84d75b47069d3d7f0`
* 节点（node）- `namehash` 函数的输出值，用作 ENS 名称的唯一性标识。

### 算法

首先，按点（“.”）分隔将名称划分为标签。所以，“vitalik.wallet.eth” 变成了列表\[“vitalik”, “wallet”, “eth”\]。

然后按递归的方式定义 namehash 函数如下：

```text
namehash([]) = 0x0000000000000000000000000000000000000000000000000000000000000000
namehash([label, …]) = keccak256(namehash(…), keccak256(label))
```

下面是用 Python 实现 namehash 的示例。

```python
def namehash(name):
  if name == '':
    return '\0' * 32
  else:
    label, _, remainder = name.partition('.')
    return sha3(namehash(remainder) + sha3(label))
```

Namehash 在 [EIP137](https://eips.ethereum.org/EIPS/eip-137) 中有详细的说明。

### 如何查询一个名称的标签哈希或 namehash

在某些情况下，你可能需要知道特定 ENS 名称的哈希值。labelhash 表示名称标签的哈希值（例如：`makoto` 是 `makoto.eth` 的标签)， namehash 是标签哈希组合后的哈希值。我们目前正在努力将这些信息呈现在我们的管理应用程序（即 [ENS App](https://app.ens.domains/)）。同时，您可以通过 [TheGraph 中的 ENS 子图](https://thegraph.com/explorer/subgraph/ensdomains/ens) 利用以下代码查询相关信息。

```text
{
  domains(where: {name:"vitalik.eth"}) {
    id
    name
    labelName
    labelhash
  }
}
```

## 不明确名称的处理

由于 unicode 中有大量的字符，而且所表示的脚本种类繁多，因此不可避免地会出现不同的 unicode 字符，这些字符在常用字体中是相似的，甚至是相同的。这一点可能会被用来欺骗用户，让用户以为他们正在访问某个站点或资源，而实际上他们正在访问是另一个站点或资源。即所谓的 “[同形攻击](https://en.wikipedia.org/wiki/Internationalized_domain_name#ASCII_spoofing_concerns)” 。

向用户显示名称的客户端和其他软件应该针对这些攻击采取对策，比如突出显示有问题的字符，或者向用户显示混合脚本的警告。[Chromium 的 IDN 策略](https://www.chromium.org/developers/design-documents/idn-in-google-chrome) 对于呈现 IDN 名称时的客户端行为具备一定的参考价值。

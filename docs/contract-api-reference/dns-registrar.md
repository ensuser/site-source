---
part: ENS 中文文档
title: DNS Registrar
---

在 ENS 上，有两个关于 DNS 的智能合约，[DNSSECOracle](https://github.com/ensdomains/dnssec-oracle) 和 [DNSRegistrar](https://github.com/ensdomains/dnsregistrar)。

<!-- DNSSEC (The Domain Name System Security Extensions) establishes a chain of trust from the root key which signed by ICANN (.) and down through each key. We start off knowing the hash of the root key of DNS (this is hard coded in the smart contract oracle). Given the hashes of that key, we can pass in the actual key, we can verify that it matches the hash and we can add it to the set of the trusted records.

Given that key, we can now verify any record that is signed with that key, so in this case, it’s the hash of the root of the xyz top level domain. Given that, we can recognize the key, and so on and so forth. -->

DNSSEC（名称系统安全扩展）建立了一个信任链，从 ICANN 签名的根密钥（.）向下至每个密钥。我们首先知道 DNS 根密钥的哈希值（这是在 oracle 智能合约中硬编码的）。有了该密钥的哈希值，就可以传入实际的密钥，验证它是否与此哈希值匹配，验证后我们可以将它添加到可信记录集。

有了该密钥，我们现在可以验证任何用该密钥签名的记录，在本例中，它是 xyz 顶级域的根哈希值。这样，我们就能识别密钥，等等。

![](/images/docs/dnsprovejs-diagram.png)

任何人都可以在以太坊区块链上向 DNSSEC oracle 提交经过 DNSSEC 签名的 DNS 记录的证明，只要是采用当前支持的公钥方案和摘要签名的就行。如果一个人能够通过 DNSSEC Oracle 证明他拥有某个 DNS 域名的所有权，那么 DNSRegistrar 会将相应的 ENS 名称分配给他。

## DNSRegistrar 的地址

- Mainnet: 待定
- Ropsten: 0x475e527d54b91b0b011DA573C69Ac54B2eC269ea

注册 ENS 名称时，可以通过查找其上级名称所有者（例如 `.matoken.eth` 的上级名称是 `.eth`）来查找注册器合约地址。但是，当您通过DNSSEC 注册器进行注册时，如果您是在该 TLD下 第一个注册的，那么父名称可能不存在。

## Gas 消耗

向 DNSSEC Oracle 提交证明涉及复杂的计算过程，因此会消耗大量 Gas。如果你是在某个 TLD 下第一个提交名称的，还会消耗更多的 Gas，因为它在同时提交您的名称及其父名称这两项证明（例如：`matoken.live` 与 `.live`）。在 Ropsten 网络上进行测试时，[此过程消耗了 1,663,953 gas](https://ropsten.etherscan.io/tx/0x7ba91728530b2a9f325b330986265fd455639fd3f07e775cf68ee8c767b2637f)。

## Typescript/Javascript 库

To help you interact with DNSSEC data and the DNSRegistrar, we provide two libraries.
为了帮助您与 DNSSEC 数据以及 DNSRegistrar 进行交互，我们提供了两个库。

- [DNSProvejs](https://github.com/ensdomains/dnsprovejs)：用于从 DNS 查询和验证 DNSSEC 数据的库
- [dnssecoraclejs](https://github.com/ensdomains/dnssecoraclejs)：为 ENS DNSSEC Oracle 生成证明数据的一个库。

## 示例

### 从 DNS 中检索证明

```javascript
import { Oracle } from '@ensdomains/dnssecoraclejs'
import { DNSProver } from '@ensdomains/dnsprovejs'
const textDomain = '_ens.matoken.xyz'
const prover = DNSProver.create("https://cloudflare-dns.com/dns-query")
const result = await prover.queryWithProof('TXT', textDomain)
```

### 检索 DNS 文本记录

```javascript
const result = {
  answer: SignedSet {
    records: [{
      name: '_ens.matoken.xyz',
      type: 'TXT',
      ttl: 300,
      class: 'IN',
      flush: false,
      data: [Array]
    }],
    signature: {
      name: '_ens.matoken.xyz',
      type: 'RRSIG',
      ttl: 300,
      class: 'IN',
      flush: false,
      data: [Object]
    }
  },
  proofs: [
    SignedSet { records: [Array], signature: [Object] },
    SignedSet { records: [Array], signature: [Object] },
    SignedSet { records: [Array], signature: [Object] },
    SignedSet { records: [Array], signature: [Object] },
    SignedSet { records: [Array], signature: [Object] }
  ]
}
// Retrieving the text record
result.answer.records[0].data.toString()
// 'a=0xa5313060f9fa6b607ac8ca8728a851166c9f612'
```

`queryWithProof` 返回 `answer` 和 `proofs`。`answer` 包含 DNS 记录的可读记录及其签名（RRSIG）。上面的例子显示了区块链上的一条数据（返回的第一个记录），这条数据包含着一条 `a=$ETHEREUM_ADDRESS` 格式的 `TXT` 记录。

### 向 DNSRegistrar 提交证明

```javascript
import { Oracle } from '@ensdomains/dnssecoraclejs'
import { abi } from '@ensdomains/contracts/abis/dnsregistrar/DNSRegistrar.json'
import { Contract } from 'ethers'
// The registrar address nees to be hard-coded
const registrarAddress = '0x475e527d54b91b0b011DA573C69Ac54B2eC269ea'
const registrar new Contract(registrarAddress, abi, provider)
const oracleAddress = await registrar.oracle()
const oracle = new Oracle(oracleAddress, provider)
const { data, proof } = oracle.getProofData(result)
if(data.length === 0){
    // This happens if someone has submitted the proof directly to DNSSECOracle, hence only claim a name on the registrar.
    return registrar.claim(claim.encodedName, proof)
}else{
    // This submits proof to DNSSECOracle, then claim a name.
    return registrar.proveAndClaim(claim.encodedName, data, proof)
}
```

## 下一步

目前缺少一个 Typescript/JS 库，这个库支持通过提供 NSEC/NSEC3（下一代安全记录）证明来删除 DNSSECOracle 中的记录。

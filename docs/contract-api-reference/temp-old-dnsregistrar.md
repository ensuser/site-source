---
part: ENS 中文文档
title: DNS Registrar
---

## DNS Registrar 介绍

除了可以用于直接调用智能合约的基于 DNSRegistrar Truffle 的工件，我们还提供了一个 javascript 封装器，用于查找 DNS 记录、获取证明、通过 DNSSec Oracle 提交证明，以及通过 DNSRegistrar 利用这个证明来注册 ENS（或是在该证明不存在的情况下删除 ENS）。

示例

``` solidity
    var DNSRegistrarJs = require('@ensdomains/dnsregistrar');
    dnsregistrar = new DNSRegistrarJs(provider, dnsregistraraddress);
    dnsregistrar.claim('foo.test').then((claim)=>{
        claim.submit({from:account});
    })
```

## 库

### DnsRegistrar

DnsRegistrar 为给定的 DNS 类型查找一个域名，并返回一个声明，利用这个声明，你可以向 DNSSEC Oracle 提交证明或从中删除证明，以及通过 DnsRegistrar 合约注册 ENS 域名。

``` solidity
DNSRegistrar.claim(name)

  /**
   * @param {string} name – 您想要声明的域名
   */
```

返回一个声明对象，该对象允许您声明一个给定 ENS 域名的所有权，声明的方式是通过提交证明到 DNSSEC Oracle 并同时在注册器上进行声明。

``` solidity
Claim.getOwner()
```

从 DNS 记录中返回所有者的以太坊地址.

``` solidity
Claim.getOracle()
```

返回 [Oracle](/docs/contract-api-reference/dnsprovejs.html#Oracle-库及其函数) 对象。

``` solidity
Claim.getResult()
```

返回 [DnsResult](/docs/contract-api-reference/dnsprovejs.html#DnsResult-库) 对象。

``` solidity
Claim.submit(params)

  /**
   * @param {Object} params – 可选参数，用于发送至智能合约，如 from、gas 等
   */
```

无论是提交证明，还是删除证明，都取决于 DNSRecord 和 DNSSEC Oracle 的状态。

- 如果 DNS 记录返回 NSEC/NSEC3(Next Secure)，它将删除一个条目；
- 如果合约中的证明不全，则会向 DNSSEC Oracle 合约提交证明；
- 如果 DNSSEC Oracle 合约中的证明不全，则会向 DNSSEC Oracle 合约提交证明并通过 DNSRegistrar 合约进行声明。

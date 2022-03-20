---
part: ENS 中文文档
title: DNSProvejs
---

## DNSProvejs 介绍

DNSProvejs 是一个在以太坊的 DNSSEC oracle 合约上建立可信 DNS 记录内容的工具（[源代码](https://github.com/ensdomains/dnsprovejs)）。

### 功能

- 按照给定的名称和类型获取其 DNS 信息
- 验证 DNS 并构造证明
- 将证明提交到 DNSSEC（名称系统安全扩展）Oracle 智能合约
- 在浏览器和 node.js 中应用

## 快速开始

### 安装

``` console
    npm install '@ensdomains/dnsprovejs' --save
```

### 使用

``` javascript
    var provider  = web3.currentProvider;
    var DnsProve  = require('dnsprove');
    var dnsprove  = new DnsProve(provider);
    var textDomain = '_ens.matoken.xyz';
    var dnsResult = await dnsprove.lookup('TXT', textDomain);
    var oracle    = await dnsprove.getOracle('0x123...');
    var proofs = dnsResult.proofs;

    if(dnsResult.found){
        await oracle.submitAll(dnsResult, {from:nonOwner});
    }else if (dnsResult.nsec){
        await oracle.deleteProof(
            'TXT', textDomain,
            proofs[proofs.length -1],
            proofs[proofs.length -2],
            {from:nonOwner}
        );
    }else{
        throw("DNSSEC is not supported")
    }
```

另外，除了向 Oracle 合约提交证明，如果你还想通过 `dnsregistrar` 来声明它，那么你可以调用 `getAllProofs` 并将结果传递给 `proveAndClaim` 函数。

``` javascript
    let dnsResult = await dnsprove.lookup('TXT', '_ens.matoken.xyz', address);
    let oracle    = await dnsprove.getOracle(address);
    let data = await oracle.getAllProofs(dnsResult, params);
    await registrar.methods
        .proveAndClaim(encodedName, data[0], data[1])
        .send(params)
```

### DnsRegistrar 库

上面的代码演示了直接在智能合约中调用 `proveAndClaim` 函数的情形，此外我们还有一个经过封装的库：[DNSregistrar](https://github.com/ensdomains/dnsregistrar) ，它在一个函数调用中同时调用了 DNSSEC Oracle 和 DNSregistrar。

``` javascript
    var DNSRegistrarJs = require('@ensdomains/dnsregistrar');
    dnsregistrar = new DNSRegistrarJs(provider, dnsregistraraddress);
    dnsregistrar.claim('foo.test').then((claim)=>{
        claim.submit({from:account});
    })
```

## 深入了解

### 工作原理

DNSSEC（名称系统安全扩展）从 ICANN 签名的根密钥（.）开始，逐级向下，建立了一个信任链。我们首先来了解 DNS 的根密钥的哈希值（这个哈希值通过硬编码的方式写在 Oracle 智能合约中）。如果给定了一个密钥的哈希值，我们可以通过传入真正的密钥，来验证它是否匹配这个哈希值，并将其添加到可信记录集。

有了这个密钥，我们现在就可以验证用这个密钥签名的任何记录，在下图的示例中，显示的是 xyz 这一顶级名称的哈希。有了这些，我们就能识别密钥，等等。

![](/images/docs/dnsprovejs-diagram.png)

接下来，您可以识别 ethlab.xyz 的密钥。我们可以先识别密钥的哈希值，再识别密钥本身，最后可以验证包含以太坊地址的签名文本记录。

### 构造证明

To construct a proof to submit to Oracle smart contract, you first need to provide name, rrsig, and rrset.
要构造一个提交给 Oracle 智能合约的证明，您首先需要提供 `name` `rrsig` 和 `rrset` 。

**name**：DNS 记录的名称

``` javascript
let name = '.'
```

**rrsig**：资源记录的数字签名

``` javascript
let sig = { name: '.',
    type: 'RRSIG',
    ttl: 0,
    class: 'IN',
    flush: false,
    data:
    { typeCovered: 'DNSKEY',
        algorithm: 253,
        labels: 0,
        originalTTL: 3600,
        expiration: 2528174800,
        inception: 1526834834,
        keyTag: 5647,
        signersName: '.',
        signature: new Buffer([]) }
    }
```

**rrset**：DNS 资源记录设置

``` javascript
let rrs = [
        {
            name: '.',
            type: 'DNSKEY',
            ttl: 3600,
            class: 'IN',
            flush: false,
            data: { flags: 257, algorithm: 253, key: Buffer.from("1111", "HEX")}
        },
        {
            name: '.',
            type: 'DNSKEY',
            ttl: 3600,
            class: 'IN',
            flush: false,
            data: { flags: 257, algorithm: 253, key: Buffer.from("1111", "HEX")}
        },
        {
            name: '.',
            type: 'DNSKEY',
            ttl: 3600,
            class: 'IN',
            flush: false,
            data: { flags: 257, algorithm: 253, key: Buffer.from("1112", "HEX")}
        }
    ]
```

构建好数据，并将用于建立证明的数据实例化到一个 `Result` 对象，然后 `toSubmit` 就能够生成输入数据。

``` javascript
        const Result = require('@ensdomains/dnsprovejs/dist/dns/result')
        let result = new Result([{name,sig,rrs}])
        result.proofs[0].toSubmit()
        // [ '0x0030fd0000000e1096b0e2d05b01a692160f00000030000100000e100006010103fd1111000030000100000e100006010103fd1111000030000100000e100006010103fd1112',
  '0x' ]
```

## 相关库

### DnsProver 库及其函数

`DnsProver` 允许您查找给定 DNS 类型的名称，并返回可以提交给 DNSSEC Oracle 的 DNS 记录和证明。

``` txt
DnsProver.lookup(type, query)

  /**
   * lookup takes DNS record type and name and returns `DnsResult` object.
   *
   * @param {string} type - eg: TXT
   * @param {string} query - eg: _ens.yourdomain.xyz
   * @returns {Object} DnsResult - contains list of results retrieved from DNS record and proofs which are constructed from the record and used to submit into DNSSEC Oracle
   */
```

``` txt
DnsProver.getOracle(address)

  /**
   * getOracle returns Oracle object
   *
   * @param {string} address - DNSSEC Oracle contract address
   * @returns {Object} Oracle - allows you to call DNSSEC oracle functions
   *
   */
```

### Oracle 库及其函数

`Oracle` 与 DNSSEC Oracle 智能合约进行交互。

``` txt
Oracle.knownProof(proof)

  /**
   * kownProof
   * @param {Object} proof
   * @param {string} proof.name - eg 'ethlab.xyz'
   * @param {type} proof.type - eg 'TXT'
   * @returns {Object} oracle_proof - contains list of results retrieved from DNS record and proofs
   */
```

``` txt
Oracle.submitAll(result, params)

  /**
   * submitAll submits all required proofs into the DNSSEC oracle as one transaction in a batch.
   * @param {Object} result
   * @param {Object} params - from, gas, gasPrice, etc
   */
```

``` txt
Oracle.getAllProofs(result)

  /**
   * getAllProofs returns all the proofs needs to be submitted into DNSSEC Oracle.
   * It traverses from the leaf of the chain of proof to check if proof in DNSSEC Oracle
   * and the one from DNS record matches with valid inception value.
   * This function is used so that it can pass the necessary proof to `dnsregistrar.proveAndClaim` function.
   *
   * @param {Object} result
   * @returns {string} data
   * @returns {Object} prevProof
   */
```

``` txt
Oracle.submitProof(proof, prevProof, params)

  /**
   * submitProof submits a proof to Oracle contract.
   * If `prevProof` is `null`, the oracle contract uses hard-coded root anchor proof to validate the validity of the proof given.
   * `params` is used to pass any params to be sent to transaction, such as `{from:address}`.
   * @param {Object} proof
   * @param {Object} prevProof
   * @param {Object} params - from, gas, gasPrice, etc
   * @returns {boolean} success - returns true unless transaction fails
   */
```

``` txt
Oracle.deleteProof(type, name, proof, prevProof, params)

  /**
   * deleteProof deletes a proof
   * @param {string} type - eg: TXT
   * @param {string} name - eg: _ens.matoken.xyz
   * @param {string} proof
   * @param {string} prevProof
   * @param {Object} params - from, gas, gasPrice, etc
   */
```

``` txt
OracleProof

  /**
   * @constructor
   * @param {Object} proof
   * @param {number} proof.inception - time the signature was generated
   * @param {number} proof.inserted - time the record was inserted into DNSSEC oracle
   * @param {string} proof.hash - hash of proof stored in DNSSEC oracle
   * @param {string} proof.hashToProve - hash of proof constructed from DNS record
   * @param {boolean} proof.validInception - true if inception in DNSSEC oracle is older than the one from DNS record.
   * @param {boolean} proof.matched - true if inception is valid and hash is matched
   */
```

### Proof 库及其函数

`Proof` 包含提交到 DNSSEC Oracle 的 rrset 和签名数据。

``` txt
Proof

  /**
   * @constructor
   * @param {string} name
   * @param {string} type
   * @param {string} sig
   * @param {number} inception
   * @param {string} sigwire
   * @param {string} rrdata
   */
```

``` txt
Proof.toSubmit()

  /**
   * toSubmit returns an array consisting of hex string of sigwiredata (concatinatd string of sigwire and rrdata) and its signature
   * @returns {array} data
   */
```

### DnsResult 库

`DnsResult` 是一个通过调用 `lookup` 函数返回的对象，它包含有关 DNS 记录的信息。

``` txt
DnsResult

  /**
   *
   * @constructor
   * @param {Object} dns_result
   * @param {boolean} dns_result.found - true if the given record exists
   * @param {boolean} dns_result.nsec  - true if the given record does not exist and NSEC/NSEC3 is enabled
   * @param {Array} dns_result.results - an array of SignedSet containing name, signature, and rrs
   * @param {Array} dns_result.proofs  - an array of proofs constructed using results
   * @param {string} dns_result.lastProof - the last proof which you submit into Oracle contruct
   */
```

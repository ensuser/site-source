---
part: ENS 中文文档
title: ENS 测试注册器 
---

[源代码](https://github.com/ensdomains/ens/blob/master/contracts/TestRegistrar.sol)

通过测试注册器可以方便地在以太坊测试网络上测试 ENS 。测试注册器通常部署在 .test TLD 上，它允许用户即时注册一个用于测试目的的名称，该名称在注册 28 天后自动过期。

## 注册一个名称

```text
function register(bytes32 label, address owner) public;
```

注册一个其 `keccak256` 哈希等于 `label` 的子名称，并将其所有者设置为 `owner` 。例如，注册 _myname.test_ ，就用 `keccak256('myname')` 作为第一个参数调用 `register` 函数。

注册有效期为 28 天。

## 获取过期时间

```text
mapping (bytes32 => uint) public expiryTimes;
```

返回指定子名称到期的 unix 时间戳。例如，调用 `expiryTimes(keccak256('myname'))` 可以检查 _myname.test_ 的过期时间。

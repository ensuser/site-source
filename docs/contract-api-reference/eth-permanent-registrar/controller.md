---
part: ENS 中文文档
title: .eth 永久注册器的控制器 
---

[源代码](https://github.com/ensdomains/ethregistrar/blob/master/contracts/ETHRegistrarController.sol)

本节要讲的是 .eth 注册控制器（[ETHRegistrarController](https://github.com/ensdomains/ethregistrar/blob/master/contracts/ETHRegistrarController.sol)）的各个部分，这些内容与那些编写注册控制器交互工具的开发者息息相关。为简洁起见，省略了注册器所有者特有的功能。

控制器只与明文标签一起工作（例如，“alice” 代表 “alice.eth”）。

为了防止名称抢注，.eth 注册控制器需要对新名称注册（但不需要对续费）执行一个 “请求-注册” 的过程。要注册一个名称，用户必须：

1. 利用待注册名称和一个秘密值生成一个固定长度的哈希。
2. 将第 1 步中生成的定长哈希提交给控制器。
3. 等待至少 1 分钟，但别超过 24 小时。
4. 将这个名称的注册请求以及来自第 1 步的秘密值一起提交。

这个过程保证了注册不会被抢注，除非攻击者能够审查至少 1 分钟前用户请求注册名称的交易。

## 示例

### 名称注册

下面的示例演示了注册名称所需的步骤。

采用 **web3.js** 时：

```javascript
const controller = web3.eth.contract(controller_abi).at(controller_address);
async function register(name, owner, duration) {
  // Generate a random value to mask our commitment
  const random = new Uint8Array(32);
  crypto.getRandomValues(random);
  const salt = "0x" + Array.from(random).map(b => b.toString(16).padStart(2, "0")).join("");
  // Submit our commitment to the smart contract
  const commitment = await controller.makeCommitment(name, owner, salt);
  const tx = await controller.commit(commitment);
  // Add 10% to account for price fluctuation; the difference is refunded.
  const price = (await controller.rentPrice(name, duration)) * 1.1;
  // Wait 60 seconds before registering
  setTimeout(async () => {
    // Submit our registration request
    await controller.register(name, owner, duration, salt, {value: price});
  }, 60000);
}
```

{% note info %}
为简明起见，这个例子是使用异步而不是用回调编写的。因此，这个例子可以在 web3 的 1.0.x 版本下正常工作。注意，它不会在 MetaMask 内置的 web3 中工作，因为这是一个较老的版本，缺乏异步支持。
{% endnote %}

## 读取操作

### 获取最短请求时间

```text
uint constant public MIN_COMMITMENT_AGE;
```

这个公共常量表示请求的最短时间（以秒为单位），一次请求只能在它被打包后至少经过这么多秒才能正式注册。

DApps 应该获取这个常量，而不是对当前值进行硬编码，因为这个常量可能会在以后的升级中发生变化。

### 获取最长请求时间

```text
uint constant public MAX_COMMITMENT_AGE;
```

这个公共常量表示请求的最长时间（以秒为单位），一个请求在它被打包后经过这么多秒之后就会失效，不能再用于注册名称。

DApps 应该获取这个常量，而不是对当前值进行硬编码，因为这个常量可能会在以后的升级中发生变化。

### 获取最短注册时间

```text
uint constant public MIN_REGISTRATION_DURATION;
```

这个公共常量表示注册的最短持续时间(以秒为单位)，少于此期限的注册将被拒绝。

DApps 应该获取这个常量，而不是对当前值进行硬编码，因为这个常量可能会在以后的升级中发生变化。

### 获取请求时间戳

```text
mapping(bytes32=>uint) public commitments;
```

`commitments` 存储了从每一份提交的请求到对应请求时间戳的映射。在提交注册交易之前，希望验证请求有效性的调用者应该先检查这个映射。

### 获取租金价格

```text
function rentPrice(string name, uint duration) view public returns(uint);
```

`rentPrice` 按照参数中提供的名称和时长，在几秒钟内返回注册或续费所需的资金（以 wei 为单位）。调用者应该注意到这个价格可能随着时间的推移而变化，特别是价格预言机依赖一种法币来进行定价的时候。

调用者应该使用这个函数来获取注册费用并显示给用户，而不是在应用程序内部计算这些费用，因为以后对价格预言机的变更或升级可能会产生不同的定价方案，而且每年的注册费用取决于名称长度、注册持续时间或其他变量。

### 检查名称的有效性

```text
function valid(string name) public view returns(bool);
```

如果这个名称符合该控制器对注册的有效性要求（比如它满足长度要求），则 `valid` 返回 true 。

### 检查名称的可用性

```text
function available(string name) public view returns(bool);
```

如果这个名称符合该控制器对注册的有效性要求，并且可以注册，则 `available` 返回true。[在这个函数内部](https://github.com/ensdomains/ethregistrar/blob/master/contracts/ETHRegistrarController.sol#L55-L58)，使用了（上面的）`valid` 函数和 [注册器](registrar.html#检查名称的可用性) 合约中的 `available` 函数，`available` 函数同时检查名称在旧版 ENS 注册器和当前 ENS 注册器中的可用性。

调用者**应该**使用这个函数来检查名称是否可以注册，而不要用注册器合约中的 `available` 函数，后者不检查名称的长度。

### 计算请求哈希

```text
function makeCommitment(string name, address owner, bytes32 secret) pure public returns(bytes32);
```

`makeCommitment` 从名称标签（比如 “myname” ，而不是 “myname.eth”）和秘密值生成并返回一个请求哈希。

## 写入操作

### 提交请求

```text
function commit(bytes32 commitment) public;
```

`commit` 用于提交预请求，这个预请求调用了 [makeCommitment](controller.html#计算请求哈希) 生成的请求哈希。

### 注册名称

```text
function register(string name, address owner, uint duration, bytes32 secret) public payable;
```

`register` 用于注册名称，有效的注册请求必须符合下列准则:

1. `available(name) == true`
2. `duration >= MIN_REGISTRATION_DURATION`
3. `secret` 用于判定一个有效的请求（例如，`commitments[makeCommitment(name, secret)]`）存在，并且在 1 分钟到 24 小时之间
4. `msg.value >= rentPrice(name, duration)`

由于租金价格可能会随时间变化，所以建议调用者发送的租金略高于`rentPrice`返回的价格，5-10%的溢价应该就足够了，多余的资金都会返还给调用者。

调用成功会触发以下事件：

```text
event NameRegistered(string name, bytes32 indexed label, address indexed owner, uint cost, uint expires);
```

调用成功还会连带注册器触发一个 [名称注册事件](registrar.html#名称注册事件)，并连带 ENS 注册表触发一个 [NewOwner 事件](../ens.html#设置子名称所有者)。

### 延长名称有效期

```text
function renew(string name, uint duration) external payable;
```

`renew` 用于给一个名称续费，将该名称的有效期延长 `duration` 指定的秒数。只要提供足够的资金，任何人都可以调用这个函数。由于租金价格可能会随时间变化，所以建议调用者发送的租金略高于 `rentPrice` 返回的价格，5-10% 的溢价应该就足够了，多余的资金都会返还给调用者。

调用成功会触发以下事件：

```text
event NameRenewed(string name, bytes32 indexed label, uint cost, uint expires);
```

调用成功还会连带注册器触发一个 [名称续费事件](registrar.html#名称续费事件)。

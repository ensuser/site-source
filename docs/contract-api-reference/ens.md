---
part: ENS 中文文档
title: ENS 注册表
description: ENS 注册表。
---

[源代码](https://github.com/ensdomains/ens/blob/master/contracts/ENS.sol)

ENS 注册表是 ENS 系统中的核心合约，所有的 ENS 查询都从注册表开始。注册表负责管理名称列表，记录每个名称的所有者、解析器和 TTL ，并允许名称的所有者对这些数据进行更改。

ENS 注册表的详细信息请参阅 [EIP137](https://eips.ethereum.org/EIPS/eip-137) 。

### 获取所有者

```text
function owner(bytes32 node) external view returns (address);
```

以上函数返回 `node` 所标识名称的所有者。

### 获取解析器

```text
function resolver(bytes32 node) external view returns (address);
```

以上函数返回 `node` 所标识名称的解析器地址。

### 获取 TTL

```text
function ttl(bytes32 node) external view returns (uint64);
```

以上函数返回 `node` 所标识名称的缓存存活时间（TTL）。希望缓存名称信息(包括所有者、解析器地址和其他记录)的系统应该重视这个值。如果 TTL 为 0 ，那么每次查询都需要获取新数据。

### 设置所有者

```text
function setOwner(bytes32 node, address owner) external;
```

将 `node` 所标识名称的所有权重新分配给 `owner` ，此函数只能由名称当前的所有者调用。

该操作会触发以下事件：

```text
event Transfer(bytes32 indexed node, address owner);
```

### 设置解析器

```text
function setResolver(bytes32 node, address resolver) external;
```

将与 `node` 所标识名称相关联的解析器更新为 `resolver` ，此函数只能由名称当前的所有者调用。`resolver` 必须是一个实现了解析器接口的合约地址。

该操作会触发以下事件：

```text
event NewResolver(bytes32 indexed node, address resolver);
```

### 设置 TTL

```text
function setTTL(bytes32 node, uint64 ttl) external;
```

更新 `node` 所标识名称的缓存存活时间，此函数只能由名称当前的所有者调用。

该操作会触发以下事件：

```text
event NewTTL(bytes32 indexed node, uint64 ttl);
```

### 设置子名称所有者

```text
function setSubnodeOwner(bytes32 node, bytes32 label, address owner) external;
```

创建一个新的子名称 `node` ，将其所有权分配给指定的 `owner` 。如果该子名称已经存在，则重新分配所有权，但解析器和 TTL 保持不变。

`label` 是要创建的子名称标签的 keccak256 哈希。例如，如果你拥有 _alice.eth_ 并想创建子名称 _iam.alice.eth_ ，就需要将 `namehash('alice.eth')` 作为 `node` ，将 `keccak256('iam')` 作为 `label` 。

该操作会触发以下事件：

```text
event NewOwner(bytes32 indexed node, bytes32 indexed label, address owner);
```

### 设置名称记录

```text
function setRecord(bytes32 node, address owner, address resolver, uint64 ttl);
```

通过一次操作在 ENS 记录中设置名称的所有者、解析器和 TTL 。这个函数是为了方便用户操作而提供的，调用这个函数完全等同于按一定的顺序调用 `setResolver` `setTTL` 和 `setOwner` 。

### 设置子名称记录

```text
function setSubnodeRecord(bytes32 node, bytes32 label, address owner, address resolver, uint64 ttl);
```

设置子名称的所有者、解析器和 TTL ，并在必要时创建这个子名称。这个函数是为了方便用户操作而提供的，它能够先将子名称的全部三个字段设置好之后，再在将子名称的所有权转移给调用者。

### 设置授权

```text
function setApprovalForAll(address operator, bool approved);
```

设置或取消授权。得到授权的帐户可以代表该函数调用者执行所有关于 ENS 注册表的操作。

### 检查授权

```text
function isApprovedForAll(address owner, address operator) external view returns (bool);
```

如果 `operator` 被授权代表 `owner` 进行 ENS 注册表的操作，则返回 true 。

### 检查记录是否存在

```text
function recordExists(bytes32 node) public view returns (bool);
```

如果 `node` 存在于 ENS 注册表中，则返回 true 。对于存在于旧 ENS 注册表中且尚未迁移到新注册表的记录，将返回 false 。

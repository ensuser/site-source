---
part: ENS 中文文档
title: .eth 永久注册器的实现
---

[源代码](https://github.com/ensdomains/ethregistrar/blob/master/contracts/BaseRegistrarImplementation.sol)

本合约实现了永久注册器的核心功能，具有以下特点：

* 注册器的所有者可添加或移除 “控制器（controller）”。
* 控制器可以注册新名称和延长现有名称的有效期（即为名称续费），但不能更改现有名称的所有权或缩短其过期时间。
* 名称所有者可以将所有权转让给另一个地址。
* 如果名称所有者丢失了所有权，他们可以收回在 ENS 注册表中记录的所有权。（译注：看到这句是不是有点蒙圈？这是因为 ENS 中名称的所有权可以分为两种，很容易混淆，官方团队未来会统一这些术语。）
* 在 1 年过渡期内，旧版注册器的名称所有者可将其迁移至新版注册器。迁移名称时，会退还全部押金。

本节要讲的是注册器接口的各个部分，这些内容与那些编写注册器交互工具的开发者紧密相关。为简洁起见，省略了注册器所有者或控制器特有的功能。

注册器只处理标签哈希，确切地说是标签的第一个组件的 `keccak256` 值（比如，对 `ens.eth` 来说就是 `keccak256('ens')`）。为了与 ERC721 兼容，这些值表示为 uint256 值，而非 bytes32 值，但是它们之间可以相互转换。通过计算 `keccak256(baseNode, labelHash)` 可以获得名称的namehash，其中的 `baseNode` 是注册器所管理的 TLD 的 namehash ，比如 `namehash('eth')` 。

注册和续费是通过注册器的 [控制器](controller.html) 来处理的。

## 名称和注册

ENS 中的每个名称都有一个所有者，名称所有者可以将名称转让给新所有者、设置解析器、创建和重新分配子名称，这些功能都包含在 [ENS 注册表](../ens.html)中。

直接在 .eth 下分配的名称（例如，以 .eth 结尾的二级名称，如 _alice.eth_）是由前面所说 .eth 永久注册器管理的。当从注册器购买一个名称并合法获取该名称在 ENS 中的所有权时，注册器会自动跟踪谁拥有**注册**，即谁是注册人，这里的**注册人**在 .eth 永久注册器里是唯一的。（译注：这里提到的 “注册” 是个名词，可以理解为 “注册证书” ，注册证书代表了一个名称的 “主权” ，拥有这个注册证书的人就是这个名称的 “注册人” ，而注册人具有最高操作权限，可以收回名称的所有权。简而言之，注册人是一个名称的真正主人，而我们经常提到的所有者其实是这个名称的管理者。下面我们还会多次提到 “注册”，切记它是一个名词，为方便理解，不妨将其自行脑补为 “注册证书” 。）

名称的注册人可以将注册转让给另一个帐户（译注：即名称过户），也可以通过调用 [reclaim](registrar.html#收回-ENS-所有权) 来恢复名称的所有权，该方法将 ENS 名称的所有者重置为注册人的帐户。

将名称的所有权和注册的所有权进行分离，是为了易于构建对 ENS 进行自动更新的系统。注册人可以将名称的所有权转让给其他帐户或是一个用于管理记录、子名称等信息的智能合约，同时仍然保留着恢复所有权的能力，以便于进行版本升级或回退等操作。

当涉及所有权时，很重要的一点是要弄清楚你是在考虑**名称**的所有权还是**注册**的所有权。

## 读取操作

### 获取名称到期时间

```text
function nameExpires(uint256 label) external view returns(uint);
```

返回当前名称到期的 unix 时间戳，未注册的名称或是尚未从旧版注册器迁移的名称将返回 0 。

### 检查名称的可用性

```text
function available(uint256 label) public view returns(bool);
```

如果名称可以注册，则返回 `true` 。考虑到旧版注册器上那些尚未迁移的已注册名称，注册控制器可能会对名称注册增加一些超出注册器合约的限制条件（例如，最小名称长度），所以该函数**不应该**用于检查用户是否可以注册某个名称。要检查用户是否可以注册某个名称，应该 [通过控制器检查名称的可用性](controller.html#检查名称的可用性)。

### 获取迁移截止时间

```text
uint public transferPeriodEnds;
```

`transferPeriodEnds` 记录着一个 unix 的迁移截止时间戳，这个时间到达后，就不能再从旧版注册器进行注册迁移，且所有未迁移注册的名称都可以被重新注册。

### 获取控制器状态

```text
mapping(address=>bool) public controllers;
```

`controllers` 允许调用者检查某个地址是否被授权为注册控制器。

### 检查名称授权

```text
function getApproved(uint256 tokenId) public view returns (address operator);
```

返回该名称的许可操作员的地址。（译注：如果不理解这里的 “许可” ，请查阅有关以太坊 approve 操作的信息。）

这个函数是 ERC721 的一部分。

### 检查所有名称授权

```text
function isApprovedForAll(address owner, address operator) public view returns (bool);
```

如果 `operator` 得到了可以转让 `owner` 的所有名称的授权，则返回 true 。

这个函数是 ERC721 的一部分。

### 获取名称所有者

```text
function ownerOf(uint256 label) external view returns(address);
```

`ownerOf` 返回由标签的哈希标识的注册的所有者的地址（译注：即返回注册人的地址），如果注册不存在则返回 0 。尚未从旧版注册器迁移的注册会被视为不存在的注册。

这个函数是 [ERC721](https://github.com/ensdomains/ens/blob/master/docs/ethregistrar.rst#id7) 的一部分。

## 写入操作

### 转让名称

```text
function transferFrom(address from, address to, uint256 tokenId) public;
function safeTransferFrom(address from, address to, uint256 tokenId) public;
function safeTransferFrom(address from, address to, uint256 tokenId, bytes memory data) public;
```

以上函数转让的是注册（译注：即变更注册人）。

它们按照 [ERC721](https://github.com/ensdomains/ens/blob/master/docs/ethregistrar.rst#id9) 中的规定来执行。

实现一次成功转让会触发以下事件：

```text
event Transfer(address indexed from, address indexed to, uint256 indexed tokenId);
```

### 许可操作

```text
function approve(address to, uint256 tokenId) public;
function setApprovalForAll(address operator, bool _approved) public;
```

[ERC721](https://github.com/ensdomains/ens/blob/master/docs/ethregistrar.rst#id11) 中记录了这些用于许可管理的函数的信息。

### 收回 ENS 所有权

```text
function reclaim(uint256 label) external;
```

将 ENS 注册表中名称所有者的记录设置为该名称注册的所有者，该函数只能由注册的所有者调用。

## 事件

### 名称迁移事件

```text
event NameMigrated(uint256 indexed hash, address indexed owner, uint expires);
```

当名称从旧版注册器迁出时，会触发此事件。

### 名称注册事件

```text
event NameRegistered(uint256 indexed hash, address indexed owner, uint expires);
```

当一个控制器注册一个新名称时会触发此事件。

### 名称续费事件

```text
event NameRenewed(uint256 indexed hash, uint expires);
```

当一个控制器给一个名称续费（或注册）时会触发此事件。

### 转让事件

```text
event Transfer(address indexed from, address indexed to, uint256 indexed tokenId);
```

将注册转移到新所有者时会触发此事件。这与 [ENS 注册表](../ens.html) 的转让事件不同，后者记录的是 ENS 的所有权转移。

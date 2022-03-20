---
part: ENS 中文文档
subpart: ensip
title: 'ENSIP-5: 文本记录'
description: A standard for storage of text records in ENS (formerly EIP-634).
---

| **作者**    | Richard Moore (@ricmoo) |
| ------------- | ----------------------- |
| **状态**    | 完结                   |
| **提交时间** | 2017-05-17              |

### 摘要

这个 ENSIP 为 ENS 定义了一个解析器记录类型，它允许查找任意键值的文本数据，能够支持 ENS 名称持有人将电子邮件地址、URL 和其他信息数据与 ENS 名称关联起来。

### 动机

人们常常希望将人类可读的元数据与机器驱动的数据相关联，用于调试信息、维护信息、报告信息和一般信息。

在这个 ENSIP 中，我们为 ENS 定义了一个简单的解析器记录类型，它允许 ENS 名称与任意键值文本相关联。

### 规范

#### 解析器配置

定义了一个新的解析器接口，该接口由以下方法组成:

```solidity
interface IERC634 {
  /// @notice Returns the text data associated with a key for an ENS name
  /// @param node A nodehash for an ENS name
  /// @param key A key to lookup text data for
  /// @return The text data
  function text(bytes32 node, string key) view returns (string text);
}
```

它在 EIP-165 标准下的接口 ID 是 `0x59d1d43c`.

`text` 数据可以是任意 UTF-8 字符串。如果该键不存在，则必须返回空字符串。

#### 通用键

通用键必须由小写字母、数字和连字符 (-) 组成。

* **avatar** - 用作头像或 logo 的图像的 URL
* **description** - 这个名称的描述信息
* **display** - ENS 名称的一个规范化展示，当它的大小写被折叠时，必须匹配 ENS 的名称，如果不匹配，客户端应该忽略这个值 (例如: `"ricmoo.eth"` 可以将此条目设置为 `"RicMoo.eth"`)
* **email** - 电子邮箱地址
* **keywords** - 用逗号分隔的关键字列表，按照重要性排序；使用这个内容的客户端可以选择一个阈值，从而可以忽略超过这个阈值的关键词
* **mail** - 现实中的邮寄地址
* **notice** - 与这个名称有关的通知
* **location** - 所在位置 (例如 `"Toronto, Canada"`)
* **phone** - E.164 字符串形式的电话号码
* **url** - 网站的 URL

#### 服务键

服务键必须由这项服务拥有的命名空间按照**反向点标记法**组成，例如，DNS 域名 (`.com` 和 `.io`等) 或 ENS 名称 (即 `.eth`)。服务键必须至少包含一个点。

这可以让新服务使用它们自己的键，而不必担心与现有服务发生冲突，也意味着新服务不需要更新此文档。

以下这些服务比较常见，所以在这里专门提出建议，但理想情况下，服务应该声明自己的键。

* **com.github** - GitHub 用户名
* **com.peepeth** - Peepeth 用户名
* **com.linkedin** - LinkedIn 用户名
* **com.twitter** - Twitter 用户名
* **io.keybase** - Keybase 用户名
* **org.telegram** - Telegram 用户名

服务所有者还可以为他们的键指定一个层次结构，例如:

* **com.example.users**
* **com.example.groups**
* **com.example.groups.public**
* **com.example.groups.private**

#### 遗留键

以下键在此 ENSIP 的早期版本中指定。

它们的使用可能不会很广泛，但是如果上述替换键失败，尝试最大兼容性的应用程序有可能希望查询这些键作为后备。

* **vnd.github** - GitHub 用户名 (已更新为 `com.github`)
* **vnd.peepeth** - peepeth 用户名 (已更新为 `com.peepeth`)
* **vnd.twitter** - twitter 用户名 (已更新为 `com.twitter`)

### 原理

#### 应用程序专用记录类型 vs 通用记录类型

我们没有定义大量的专用记录类型 (全部都是人类可读的数据)，而是参照 DNS 的 `TXT` 记录的修正模式，该模式支持通用的键值对，将来无需调整解析器也能够进行扩展，同时支持应用程序根据自己的需求使用自定义键。
而不是定义大量的特定记录类型等一般人类可读的数据)(每个“url”和“电子邮件”,我们遵循一个适应模型DNS的“三种”记录,允许一个通用键和值,使未来的扩展没有调整解析器,同时允许应用程序使用自定义键达到自己的目的。

### 向后兼容

不适用。

### 安全注意事项

无

### 版权

通过 [CC0](https://creativecommons.org/publicdomain/zero/1.0/) 放弃版权及相关权利。

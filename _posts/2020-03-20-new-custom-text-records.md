---
title: 新版自定义文本记录：让每个项目拥有自己的 ENS 记录
date: 2020-03-20 23:02:00
categories: 官方文章
original: https://medium.com/the-ethereum-name-service/new-custom-text-records-means-every-project-can-have-its-own-ens-record-a68022bb8f86
translator: 刘笨笨
description: 
---

很荣幸地宣布，最新上线的 ENS APP 已经能够支持自定义文本记录。这个简单的特性能够为任何项目或个人在 ENS 上提供无限的创新潜力。

去年 10 月，我们首次向 ENS APP 添加了文本记录。文本记录的概念是指人们可以用来设置任意内容的通用字段。这样，人们可以使用 ENS 来存储大量的信息，而不必为每个新的用例添加新的记录类型。

除了存储在文本记录中的数据外，每个文本记录还有一个称为 “键值” 的附加信息，用于标识特定文本记录中的数据用途。例如，您可以有一个文本记录，其键值为 `URL` ，而其数据字段中则存储了一个 URL 地址。

虽然在合约级的操作中，你可以使用任意值作为文本记录的键值，但我们的 ENS APP 最初只支持几个预先确定的文本记录键值，这些都是在 Richard Moore 的 EIP 提案中提及的键值。

在最新版本中，我们为用户添加了在管理器中轻松创建任何他们想要的文本记录键值的功能。

这里有一个示例：

### 为什么这项功能很重要

ENS 已经为诸如以太坊地址、比特币地址、IPFS 哈希等重要用例提供了专用的记录类型。但在很多重面，我们最感兴趣的是那些我们还没有想到的 ENS 用例。

您的项目中是否也需要一种类似 ENS 这样的去中心化命名服务，这样用户就可以与人类可读的名称交互，而不必理会那些难以识别的标识符，也不需要编写 EIP 来请求许可新字段或尝试创建自己的命名系统。只要确定你的 ENS 文本记录键值是什么，并开始使用它！就是这么简单。

我们期待看到人们用它来做什么！如果您的项目采用了文本记录，请通过 brantly@ens.domains 告诉我们，我们很乐意帮忙来推广它。

现在 [ENS APP](https://app.ens.domains/) 上试试吧

如果您的问题没有在 [文档](https://ensuser.com/docs/) 中找到答案，请随时在 [Discord](https://discord.gg/AskZbFx) 或 [ENS 论坛](https://discuss.ens.domains/) 上向我们寻求帮助。



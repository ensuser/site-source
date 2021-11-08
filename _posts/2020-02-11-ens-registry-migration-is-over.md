---
title: ENS 迁移已经完成，现在我们需要了解的几件事儿
date: 2020-02-11 19:01:46
categories: 官方文章
---


我们已经迁移了所有的 .eth 二级域名（比如 ceshi.eth），用户不需要立即做什么操作，但仍然有必要了解一些情况，其中有些人可能需要进行操作。

- 您使用的钱包或 DApp 是否完成更新并使用了新的 ENS 注册表
- 更新解析器以便于更改解析记录
- 迁移子域名
- 其他特殊情况

## 您使用的钱包或 DApp 更新了吗

检查您使用的钱包是否指向新注册表的最简单方法是查询 `migrated.eth` ，看它是否能解析这个域名。

下面我将比较新版本（左）和旧版本（右）。正如你所看到的，旧版本没有显示对应的地址，所以如果钱包或 DApp 指向旧注册表，那它就查询不到这个域名。

![检查是否支持新版 ENS 注册表](/images/news/2020-02-11-ens-registry-migration-is-over/01.png)

我们有一个 [公开的电子表格](https://docs.google.com/spreadsheets/d/1VwFQu1_OtYJJBgeHyth2P7xLh28isUvakqmcZJDx6QE/edit?usp=sharing)，其中包含了我们知道的所有集成了 ENS 的服务，以及它们是否已更新到新的 ENS 注册表。不过有些项目可能在没有通知我们的情况下也已经完成更新。如果你的项目有更新，请发邮件至 brantly@ens.domains ，然后我们可以标记它已经更新了。

## 更新您的解析器

如果你不更新你的解析器，你的域名仍然能正常使用。但如果你想更改解析记录，那就需要先更新解析器。

更新域名的解析器非常简单，只需在 [ENS APP](https://app.ens.domains/) 的解析器区域点击 `迁移` 按钮。

![迁移 ENS 域名](/images/news/2020-02-11-ens-registry-migration-is-over/02.png)

注意：有些旧的解析器会导致一些问题。如果您在迁移过程中遇到任何问题，请 [在 Github 上留言](https://github.com/ensdomains/ens-app/issues/568)。

## 迁移子域名

我们统一迁移了所有的二级域名（比如 ceshi.eth）以及由子域名注册器 [ENSNow](https://now.ens.domains/) 分配的子域名，但是不包括用户自己手动创建的子域名。

对于手动创建的子域名，您必须在 ENS APP的子域名管理页面点击 `Migrate` 按钮来迁移到新的注册表。

![迁移 ENS 子域名](/images/news/2020-02-11-ens-registry-migration-is-over/03.png)

为了能够顺利完成迁移，您必须注意以下两点：

1. 您必须是父域名的所有者
2. 必须先将父域名迁移到新的注册表中

否则，`迁移` 按钮将会是禁用状态。

![按钮禁用](/images/news/2020-02-11-ens-registry-migration-is-over/04.png)

如果您的子域名是由钱包或 DApp 分配给您的，那么在它们迁移域名之前，你可能会收到这类信息。

## 特殊情况

最后，我再介绍一些特殊情况。

### 我在 2017 年的拍卖中获得了一些域名，但一直没有将其迁移到永久注册器。

作为此次迁移的一部分，我们将这类域名从旧的注册器迁移到了新的永久注册器（但域名的有效期将保持不变）。要取回押金，你需要点击 `退还` 按钮。（请注意，如果您有这样的域名，为了确保在2020年5月以后域名还能正常使用，您需要在 ENS APP 中为您的域名进行续费，通过以太币支付每年约 5 美元的费用。）

![取回域名押金](/images/news/2020-02-11-ens-registry-migration-is-over/05.png)

### 我有一个 `.xyz` 域名

我们没有迁移 `.xyz` 域名，所以您需要自己迁移它们。确保 DNS 记录中的关于 `_ens.yourdomain.xyz` 的文本记录是您最新的以太坊地址，然后在 ENS APP 中点击 `同步` 按钮。具体情况可以参考 [这篇文章](/docs/dns-registrar-guide.html)。

{% asset_img 06.png 更新 xyz 域名 %}

其他的顶级域名（ `.luxe` `.art` `.club` `.kred` ）由其域名运营机构管理，所以请参考他们的相关说明。

---
part: ENS 中文文档
title: DNS 注册器指南 
---

## 介绍

DNSSEC（DNS 安全扩展）构建了一个从 ICANN（.）签署的根密钥开始，向下经过各级密钥签署的可信来源认证系统。假设你的 DNS 域名已经启用了 DNSSEC，且这个域名的特定子域名（一般是 `_ens.yourdomain.tld`）已经绑定了一个 ETH 地址，那么 ENS 管理器允许任何人通过向 `DNSSEC Oracle` 智能合约提交这个 DNS 域名的哈希，来获得相应的信息。

## 配置流程

注意：目前只支持 `.xyz` 域名。对于其他域名，请参阅 FAQ 部分。

### 第一步：设置 DNSSEC 签名

第一次登陆 [ENS 管理器](https://app.ens.domains/) 时，你将看到如下内容。

![step1: dnnsec not enabled](/images/docs/dnssec_step1.png)

如果你的 DNS 服务商已经支持 DNSSEC 签名，那么只需在 DNS 管理器上启用该选项。

如果你的 DNS 服务商不支持 DNSSEC 签名，那你就得将域名迁移到其他支持 DNSSEC 签名的服务商。我们推荐 [EasyDNS](https://www.easydns.com) 或者 [Google Cloud DNS](https://cloudplatform.googleblog.com/2017/11/DNSSEC-now-available-in-Cloud-DNS.html)。EasyDNS 的 DNSSEC 配置指南请看 [这里](https://fusion.easydns.com/Knowledgebase/Article/View/18/7/dnssec)，Google 的 DNSSEC 配置指南请看 [这里](https://cloud.google.com/dns/dnssec-config)。

无论你选择了哪家 DNS 服务商，都要确保你使用的签名算法为 RSA，哈希算法为 SHA256。

![sha\|690x468](/images/docs/dnssec_sha.png)

### 第二步：添加一条文本（TXT）记录

为了确认一个 DNS 域名的所有权应该被赋予哪个以太坊地址，ENS 上的 DNS 注册器会查询一条具有特定名称和格式的 TXT 记录。比如要声明 mydomain.xyz 的所有权，就需要在 DNS 管理器中为 \_ens.mydomain.xyz 添加一条 TXT 记录，这条 TXT 记录的文本数据的格式为a=0x1234... ，其中的 0x1234... 就是需要获得 ENS 域名控制权的以太坊地址。

![step2: add text](/images/docs/dnssec_step2.png)

### 第三步：完成 DNS 域名在 ENS 上的注册

到了这一步，你可以在 ENS 管理器中完成其余的操作。现在只需按下注册按钮 “Register” 发送交易，待交易确认便可完成 DNS 域名在 ENS 上的注册。

![step3: owner submit proof](/images/docs/dnssec_step3.png)

### 第四步：打开 ENS 管理器

![step4: owner](/images/docs/dnssec_step4.png)

## 常见问题

### 我可以通过 ENS 管理器注册所有 DNS 域名吗

理论上说，[90% 以上的域名](https://medium.com/the-ethereum-name-service/upcoming-changes-to-the-ens-root-a1b78fd52b38)都可以。还有一些顶级域名（`ceo`, `.art`, `.club`, `.luxe` 和 `.kred`）是通过他们自己的注册器来操作的。

### 如果我拥有 `myname.xyz` 这个 DNS 域名，那我可以声明 `myname.eth` 的所有权吗

你可能把这事儿和 [ENS 短域名预订](https://medium.com/the-ethereum-name-service/timeline-for-3-6-character-name-reservation-auction-and-instant-registrations-e39aa2f89dc9)给搞混了。通过集成 DNSSEC，你只能使用一个顶级域名（TLD）声明对应的完成相同的 ENS 域名，而 `.eth` 是完全独立管理的。

### 如果我注册了一个域名，怎样才能转移或者删除它的所有权

不同于 `.eth` 的永久注册器，在 ENS 上进行注册的 DNS 域名没有注册人（`registrant`）这种可以转移控制权限的角色。如果你想将所有权从当前注册地址转移到其他地址，请从你的 DNS 管理器更新相应的 DNS 记录并在 ENS 管理器中点击转移按钮 “Transfer”。

我们目前还没有启用删除所有权的功能，尽管如此，你可以将所有者设置为 `a = 0x0000000000000000000000000000000000000000` ，然后点击转移按钮 “Transfer”，就可以达到删除所有权的效果。

### 我的 DNS 子域名可以注册吗

不行。DNSSEC 注册仅为二级域名（例如：yourname.xyz）启用。如果要创建 `subdomain.yourname.xyz` ，需要先在 ENS 管理器中打开 “Subdomains” 子域名标签页，然后在 ENS 管理器中创建它，就像在 `.eth` 域名下创建其他子域名一样。

---
title: 激进名称
date: 2020-03-06 10:02:00
categories: 观点与评论
original: https://devpost.com/software/radical-domains
translator: 安仔
description: 
---

>译注：用哈伯格税重塑名称交易是 Vitalik 去年年底提出过的脑洞，在 ETHLondon 黑客松上有团队用这样一个 demo 赢得了 ENS 奖励计划的第一名。ENS 名称本身就是 ERC721 标准的代币，得益于智能合约构建的激进市场，租赁权成为了一直在市场上流动的可交易代币，而使用权则作为租金收益的代币。这样改造 NFT 交易市场的思路虽然还称不上完善，但或许是许多项目可以借鉴的方向。

![用哈伯格税重塑名称交易是 Vitalik 去年年底提出过的](/images/news/2020-03-06-radical-domains/radical-domains-01.png)

## 灵感来源

![激进名称 Radical Domain](/images/news/2020-03-06-radical-domains/radical-domains-02.png)

无论是 ENS 还是 DNS ，名称抢注问题都算得上沉疴痼疾。许多遭抢注的名称被挂以天价，而且通常是掮客们把持着最热门的名称，只有当有人花大价钱收购后，名称才会被投入使用。

这个问题的解决方案之一是哈伯格税（Harberger tax），由 Glen Weyl 和 Eric Posner 在《激进市场》一书中提出。在这种系统下，所有的资产都总是被挂在市场中。资产当前的所有人会对资产进行估价，并根据估价按比例支付一笔预置税款。这样以来，商品和金钱会一直保持流动。

我们的 Dapp **激进名称** 设计了一套由哈伯格税启发的所有权模型，允许个人持有者将 ENS 名称转入其中。我们同时借鉴了英国房地产市场中永久产权（freehold）和租赁产权（leasehold）的概念，毕竟我们的 dapp 是在 ETHLondon 开发的。

## 运行机制

ENS 名称的持有者可以针对自己的名称，创建并出售一个可交易的租约，并且获取全部的租金收入。

当名称持有人把符合 ERC 721 标准的 ENS 代币的所有权转移给激进名称（Radical Domain）智能合约时，他们会收到两个 ERC 721 代币：

* 一个**永久产权（freehold）**代币，它无法对名称进行控制操作，但是能接收未来名称产生的所有租金收入。
* 一个**租赁产权（leasehold）**代币，它能对名称发起控制操作（例如设置解析合约），但主要还是按照哈伯格税的规则，声明自身的售价，并且根据标价支付一定比例的租金。

单独持哪一种代币都无法变更相应 ENS 名称的所有权，但是任何同时持有永久产权代币和租赁产权代币的账户或合约都可以把 ENS 名称从激进名称合约中提走。

租赁产权代币总是在市场中“挂牌出售”（向 [@simondlr](https://twitter.com/simondlr) 致敬），任何时候都能在激进名称的网站或者 OpenSea 一类的市场中买入这个代币。而永久产权代币则由名称持有人决定是否要进行交易。

这样的设计有以下好处：

* 为市场带来流动性，
* 资产能产生持续不断的被动收入，
* 使得急于出售的卖家能先收到一笔钱（最开始售出租赁产权代币的货款），同时在未来也能获取收入（以后的租金）
* 激励了不囤积名称的行为。

当然也可以设计其他的模型，例如固定条款的可交易化租约。

## 实现方案

整个系统非常依赖 ERC721 非同质化代币（NFT）标准，能在代币转账发生时对收款人地址调用 onERC721Received 函数。同时也有赖于 ENS 注册器的设计，名称所有者可以指定其他地址作为控制人（Controller）对名称进行除了转移所有权以外的其他操作。

ENS 名称的持有人可以将自己的 ERC 721 名称代币转移到 RadicalManager 合约中，从而实现名称的“激进化”。这是调用了 ENS 智能合约中的 safeTransferFrom 函数，从而唤起 RadicalManager 合约的 onERC721Received 函数，接着相应地调用 RadicalLeasehold 以及 RadicalFreehold 智能合约完成代币的铸造工作。

我们智能合约的编写受益于 Truffle 框架，OpenZepellin 以及 ENS 的帮助（感谢 [@makoto_inoue](https://twitter.com/makoto_inoue) 对我们网站的协助）。

名称进行激进化的操作中，由最初的所有者设置年租金的比例（例如 10%）以及租赁产权代币的初始售价（例如 1 以太币）。接下来的租赁产权代币的持有人可以设置新的售价（例如 10 以太币），但他必须要按照相同的租金比例支付租金（10% x 10 ETH = 1 ETH/year）。永久产权代币的持有者则可以收到该租金，并且可以随时提走所有的租金。

 租赁产权代币的所有者只要持有该代币，就能一直享有该 ENS 名称的独家控制权。

## 挑战

* Solidity 的各个版本存在较大的变化，同时把两个 uint256 类型的值编码成一个字节类型的参数十分困难。（Evert）
* 团队中的新人（Richard 以及 Kiki）感到学习曲线陡峭，知识量太大。
* 理解 ENS 智能合约，将 ENS 的概念设计（例如 controller ）与 ERC721 标准（例如 operator）区分的过程很艰难。（Rosco）
* 在前端设计上我们切换了几种方案，但都各自对新手或者老手有一定的困难。

## 我们引以为豪的成果

从 BUIDL 的角度看，我们可以非常自豪地表示已经实现了核心合约的大部分功能。

我们对团队合作非常自豪，因为成员的技术背景和层次不一，每个人都做出了独特的贡献。

我们很自豪以太坊社区所构建的生态，从链、到工具、到这场盛会。

## 我们学到的东西

在项目中，我们发现的最有趣的收获在于 ENS 允许 “管理员”（controller）进行除了转移所有权以外所有名称操作的设计。这省去了我们对合约添加追踪调用的工作，其他 ERC721 代币也应当借鉴这样的设计模式，使得激进市场的思路能适用到其他项目中。

## 未来的工作

我们想要将这个项目继续完善，并且期待有人能做出改进、贡献代码。在这个项目的工作中，团队里的开发新手都得到了宝贵的经验。

## 构建工具

* ENS
* ERC721
* [javascript](https://devpost.com/software/built-with/javascript)
* [solidity](https://devpost.com/software/built-with/solidity)
* [typescript](https://devpost.com/software/built-with/typescript)

## 试一试

* [GitHub Repo](https://github.com/rkalis/radical.domains)
* [radical.domains](https://radical.domains)

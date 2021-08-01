---
title: 一种以太坊 Layer-2 的通用桥
date: 2020-10-30 13:00:00
categories: 官方文章
original: https://medium.com/the-ethereum-name-service/a-general-purpose-bridge-for-ethereum-layer-2s-e28810ec1d88
author: Nick Johnson
translator: 曾汨
description: 
---

随着走向成熟的以太坊 Layer-2 解决方案多了起来，ENS 也要能为整个生态系统提供服务，同时让 ENS 用户能够获得 Layer-2 解决方案给他们带来的效率提升。自 [Vitalik 的一篇帖子](https://ethereum-magicians.org/t/a-general-purpose-l2-friendly-ens-standard/4591)提出了一种可能的方法之后，ENS 团队和广大的 ENS 和 L2 社区也一直在开发一种通用的 “Layer-2 桥”，让包括 ENS 在内的应用，能够以免信任的方式在多个链下信源处检索数据，进而使跨平台的互操作性成为可能。

在 10 月 27 号最新的一次工作会议上，我[演示](https://www.youtube.com/watch?v=QwEiAedSNYI&t=270s)了这个想法的一个初步实现。本文中我会详细讲解这种解决方案。

## 目标

概要来说，Layer-2 和其它相关系统的工作原理都是减少与以太坊交互的需要，它们将原本需要在链上保存和访问的 状态 移到了别的地方，同时，保证在以太坊区块链上有足够多的信息能验证数据的正确性。举个例子，在 Rollup 这种常见的方案中，（Rollup 的）状态会存储在另外一个系统中，只有 witness 数据例如默克尔根会存储在以太坊区块链上（译者注：作者此处的举例不够完整，witness 还包括用户交易的原始数据）。有了这些 witness 数据和 Layer-2 解决方案的访问权，一个参与者就可以构建出对任意保护在 Layer-2 系统中的数据的有效性证明，并且可以由以太坊来验证。

这个定义比大多数人所认为的 “Layer-2” 要更加广泛 —— 它还包括了其它一些减少链上数据存储的工具，比如使用账户余额默克尔树的空投（airdrop），以及会触发事件但并不在链上存储余额的代币。

对于 ENS 和其它应用来说，关键问题在于，在一个存在许多互不兼容的 Layer-2 方案的世界里，如何能以信任最小化的方式 —— 也就是不引入任何新的信任假设 —— 从某个系统中检索数据，且不需要变成所有 Layer-2 方案的客户端、自己来存储可能有用的数据 。

一个幼稚的方法是，要求所有的系统都使用同样的 witness 数据格式。但这一点是不可能的，两个原因：第一，witness 数据的格式和类型都高度依赖于相关系统的实现细节，ZK Rollup 和 Optimistic Rollup 使用的元件必定不同；第二，客户端仍然无法实际获得数据。

实用的方法必须满足下列条件：

- 客户端不需要为它们可能与之交互的每一个系统提供显式支持。
- 客户端必须能够验证返回的数据是有效的，最好无需引入除相关 L2 方案自带假设以外的信任模型。
- 解决方案不会要求接入的 L2 平台产生结构性的变更。
- 第三方必须能够为 L2 平台开发接口，无需平台维护者的支持和参与。

## 解决方案概览

我们提议的方案的核心是一种标准化的工具，让客户端能够从一个外部系统 —— 一个网关服务 —— 处检索数据；以及一种标准化的方法，来验证返回的数据是正确的。

相应地，这里有两个主要的组成部分：第一个，是一个放在以太坊 Layer-1 上的智能合约，向客户端提供一个发现网关并验证网关响应正确性的工具；第二个，是一个网关服务，理解如何与给定的 L2 系统交互、以及如何为合约的用途而格式化数据。

在该模型下，获得数据的过程分三步：

![](/images/news/2020-10-30-a-general-purpose-bridge-for-ethereum-layer-2s/01.png)

1. 向合约发出查询数据的请求。合约并不直接返回所需的结果，而是返回两个值：一个 网关 URL，以及一个 calldata 前缀。
2. 向该网关发送一个 HTTP POST 请求，请求与第一步中相同的数据。网关返回一个不透明值（opaque value），resolver（解析器） calldata。验证该 解析器 calldata 的起始位就是第一步中得到的 calldata 前缀。
3. 查询合约，或者与之互动，提供第二步中得到的 解析器 calldata ，合约验证该数据的有效性，如果有效的话，返回 结果 或者执行交易。

因为负责理解如何与 L2 交互的是网关服务，所以这样一种简单的协议就可以让客户端从链下获得数据，并且不需要让客户端理解任何与 L2 相关的东西。为了使用这套系统，每一个应用都需要为自己意向交互的 L2 实现并部署一个网关服务和一个验证合约。在大部分使用，这些网关可以是非常通用的，降低了在不同应用间重复劳动的负担。

重要的是，这三个步骤的流程在调用者处可以完全抽象掉；一个理解这个协议的库就可以让整个流程看起来跟一个常规的 web3 合约调用一般无二，也就是说，不仅应用不需要知道自己在跟哪个 L2 交互，它们甚至完全不知道自己是在跟 L2 交互！

网关返回错误或者误导性结果的能力受到协议本身的限制。合约所实现的验证逻辑保证了任何无效的结果都会在第三步被发现，同时，合约在第一步中返回的前缀，在第二步中得到验证；这些都放置了网关用对某一次查询有效的答案来回应另一次查询。

## 工作案例

我们可以用一个预加载了一组余额的 ERC20 token 合约，以及一个本身是简单静态默克尔树的 “Layer-2” 来演示这条系统在实践中是如何运作的：

```javascript
contract PreloadedToken is ERC20 {
  mapping(address=>uint) preload;
  function claimableBalance(address addr) external view returns(uint) {
    return preload[addr];
  }
  function claim(address addr) external {
    if(preload[addr] > 0) {
      _mint(addr, preload[addr]);
      preload[addr] = 0;
    }
  }
}
```

这个简单的解决方案有一个显而易见的问题：部署者必须在部署时将所有余额填充到 preload 映射中，这是一种非常昂贵的操作。他们会更愿意把数据存储在链下，然后让能够证明自己拥有余额的用户来提取自己的数额。用默克尔树很容易就能实现这一点：

```javascript
contract PreloadedToken is ERC20 {
  bytes32 merkleRoot;
  mapping(address=>bool) claimed;
  function claimableBalanceWithProof(address addr, uint balance, bytes proof) external view returns(uint) {
    require(verifyProof(keccak256(addr, balance), proof));
    if(!claimed[addr]) {
      return balance;
    }
    return 0;
  }
  function claimWithProof(address addr, uint balance, bytes proof) external {
    require(verifyProof(keccak256(addr, balance), proof);
    if(claimed[addr]) {
      return;
    }
    _mint(addr, balance);
    claimed[addr] = true;
  }
}
```

（为了简化，我们省略掉了 `verifyProof` （验证证明功能）的实现）

这个方法非常有效，合约的作者也不再需要花费大量的 eth 来预加载所有余额，一个默克尔根就足够了，而且调用者想申领余额的时候，可以自己支付证明 token 所有权的开销。

不过，现在调用者必须理解生成证明的具体流程，并且知道要到哪儿去获取余额清单来生成自己账户的证明。如果我们可以把第一个方案的接口（方便），与第二个方案的效率结合起来，那就完美了。这就是我们的方案。

首先，我们加入了匹配初始 `claim` 的签名和 `claimbleBalance` 的方法：

```javascript
  string gateway;
  function claimableBalance(address addr) external view returns(bytes prefix, string url) {
    return (abi.encodeWithSelector(claimableBalanceWithProof.selector, addr), gateway);
  }
  function claim(address addr) external view returns(bytes prefix, string url) {
    return (abi.encodeWithSelector(claimWithProof.selector, addr), gateway);
```

这些函数的调用者可以得到两个值：第一个值是一个后续 callback 的前缀；第二个值是一个网关服务的 URL。该前缀保证了两件事： callback 会用相关的 proof 函数来响应，并且其第一个参数会是所提供的地址。这防止了网关用给另一个地址的数据来响应请求。

接下来，我们需要实现一个网关服务来，可以满足客户端的查询请求。以 claim1 为例，很直接就能实现：

```javascript
const args = tokenInterface.decodeFunctionData("claim", data);
const balance = balances[args.addr];
const proof = merkleTree.getProof(addr, balance);
return merkleInterface.encodeFunctionData("claimWithProof", [args.addr, balance, proof]);
```

（再一次，为了简洁，我们假设已经有了包括 `getProof` 函数在内的合适实现）

这里的网关服务只需要为客户端所发送的 `claim` 调用解码函数调用数据，组装一个证明 —— 或者，在一个实际的 L2 方案中，参考 L2 来组装出一个证明 —— 然后将结果编码放在对 `claimWithProof` 的调用中，返回给客户端。

最后，客户端验证返回的 calldata 是否以合约所断言的前缀开始，如果是，则使用交易发送 calldata 给合约。

`claimableBalance` 的实现也差不多，只是客户端使用 calldata 来调用合约，将返回值作为调用的最终结果。

## 安全考虑和信任模型

假设客户端信任了原始合约 —— 我们的意思是，期望该合约会以特定的方式运行，而这可以通过检查它发布的源代码来验证 —— 那么这个系统就不会引入任何新的信任假设。虽然网关的响应是一个外部流程，但其不良行为的范围仅限于拒绝服务。

首先，如果我们信任合约，我们同样也会信任它来制定一个网关 URL 来回应我们的查询请求。其次，我们也可以信任它来实现充分的验证、保证网关的响应是准确的，既可以通过在第一步中指定 calldata 前缀、也可以通过在最后一步中验证网关的响应来保证。

因此，一个尝试用不正确的值来响应的网关 —— 无论是提交了不正确的数据，还是不正确的证明 —— 都会被执行验证步骤的合约发现。一个尝试正确响应、但使用非用户所发出请求的对应结果来响应的网关，会在用户的 calldata 前缀检查中发现。客户端可以通过检查合约的行为来保证这些 —— 或者依赖于某些人对合约的检查 —— 都可以在开始交互前实现。

网关可以完全拒绝响应，也就是拒绝服务，而且这种情况确实可能因为网关恶意或者故障而发生。因为这一点，我们提议，任意最终规范，都应该让用户易于 fork 服务，并提供自己的网关；就像现在用户能够 fork dApp 的前端一样。

## ENS 应用

ENS 使用这套系统也会相对直接一些。解析器可以实现本文所述的协议，用于解析任何的数据字段，然后每一个希望支持 ENS 数据的存储和检索的 L2 都可以部署新的解析器实现和相应的网关。希望使用 L2 的用户只需存储自己的记录到合适的 L2 中，并在以太坊上发送一笔一次性的交易来指定相关的解析器地址，来使用自己的域名。

为了让这个方案更通用，ENS 也应该改进，以支持某种形式的通配符解析（wildcard resolution），使得搜索域名失败时会向解析器咨询该域名的父域名 —— 如果 “foo.example.eth” 不存在，那客户端就会在解析器内搜索 “example.eth”。这一功能使得其它系统可以存储 ENS 的整个子树，而不仅仅是单个域名的记录。

## 未解决的问题

- 虽然某些应用（比如 ENS ）可以从合约指定网关 URL 所创造的额外间接层中获益，另一些应用，比如上文所示的 token 合约，最好把这些编码为该合约 ABI 的一部分来，使得用户更容易 fork。一个终极的解决方案最好能支持两种选择，且不会强加不必要的负担。
- 目前，客户端无法分别出一个返回无效 calldata（例如提供一个无效的证明）的网关和一个无论如何都会回滚的调用。需要作出一些规定来区分这两种情况 —— 举个例子，如果证明数据的验证不通过的话，要求合约使用一个特定的回滚理由。
- 它需要一个比 “以太坊 L2 通用桥” 更吸引人的名字。

### 自己试试

我文章所有 demo 的源代码都可以在[这里](https://github.com/ensdomains/l2gateway-demo)找到。
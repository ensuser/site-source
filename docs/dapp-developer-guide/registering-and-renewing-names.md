---
part: ENS 中文文档
title: ENS 名称注册和续费 
---

如果用户需要注册他的第一个名称，那他必须与注册器进行交互。注册器是一个智能合约，这个合约拥有一个名称的所有权，并在合约中规定了子名称的分发机制。用户想要获得一个名称，就需要向与之对应的注册器申请。例如，用户如果想要一个 .eth 名称，那就必须向 .eth 注册器进行申请。每个注册器都在其内部定义了自己的名称注册 API（以及配套的更新机制）。

目前，还没有能与注册器进行交互的 ENS 库。DApp 需要使用通用的以太坊库（如 web3.js 或 web3.py）与注册器合约进行交互。有关注册器接口的详细信息，请参阅 “合约 API 参考” 部分。

## 注册器部署

* .eth：[永久注册器](../contract-api-reference/eth-permanent-registrar/readme.html)
* .test（仅用于测试网）：[测试注册器](../contract-api-reference/testregistrar.html)
* .addr.reverse：[反向注册器](../contract-api-reference/reverseregistrar.html)

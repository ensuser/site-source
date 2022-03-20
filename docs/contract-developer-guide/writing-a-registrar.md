---
part: ENS 中文文档
title: 编写一个 ENS 注册器 
---

ENS 中的注册器就是一个拥有某个名称所有权的合约，并根据合约代码中定义的一组规则来分配它的子名称。一个简易的即时注册合约如下:

```text
contract FIFSRegistrar {
    ENS ens;
    bytes32 rootNode;

    function FIFSRegistrar(address ensAddr, bytes32 node) {
        ens = ENS(ensAddr);
        rootNode = node;
    }

    function register(bytes32 subnode, address owner) {
        var node = sha3(rootNode, subnode);
        var currentOwner = ens.owner(node);

        if (currentOwner != 0 && currentOwner != msg.sender) throw;

        ens.setSubnodeOwner(rootNode, subnode, owner);
    }
}
```

你可能希望通过设置自定义规则为用户分配新名称，至于设置什么样的规则，这完全由你来决定。

你还应该记住，只要你保留父名称的所有权（无论是直接的还是通过另一个合约的方式），你的用户就无法保证他们拥有的子名称的所有权不会被你收回，也无法保证他们关于子名称的设置不会被你更改。你可能打算将名称的所有权转让给一个能够限制你控制它的合约中。有关示例，请参见 [ENSNow](https://github.com/ensdomains/subdomain-registrar)。


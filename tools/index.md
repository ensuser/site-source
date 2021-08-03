---
title: ENS 便捷查询工具
need: find_app
---

<ul id="find-list" class="find-list">
</ul>

<script>
// 每组数据表示：
// [ ( with input ), 链接前缀, 链接后缀, 需要输入到文本框的内容格式, 查询标题, placeholder]
// [ ( without input ), 链接, 查询标题]
var linkArray = [
    [
        false,
        "https://duneanalytics.com/makoto/ens",
        "ENS 实时情况概览（利用 Dune Analytics）",
    ],
    [
        false,
        "https://duneanalytics.com/makoto/ens-released-to-be-released-names",
        "ENS 即将释放和最新注册的域名（利用 Dune Analytics）",
    ],
    [
        false,
        "https://cn.etherscan.com/enslookup",
        "最新注册的 ENS 域名（利用 Etherscan）"
    ],
    [
        false,
        "https://cn.etherscan.com/token/tokenholderchart/0x57f1887a8bf19b14fc0df6fd9b2acc9af147ea85",
        "ENS 域名持有数量排名（利用 Etherscan，不准确，仅供参考）"
    ],
    [
        false,
        "https://cn.etherscan.com/accounts/label/ens",
        "ENS 智能合约地址列表（利用 Etherscan）"
    ],
    [
        false,
        "https://opensea.io/assets/ens?search[sortAscending]=false&search[sortBy]=LAST_SALE_DATE",
        "最新售出的 ENS 域名（在 OpenSea）"
    ],
    [
        false,
        "https://opensea.io/assets/ens",
        "最新挂单的 ENS 域名（在 OpenSea）"
    ],
    [
        true,
        "https://app.ens.domains/name/",
        "",
        "name",
        "查询某个 ENS 域名的详细信息（利用 ENS 管理器）",
        "请输入 ENS 域名"
    ],
    [
        true,
        "https://cn.etherscan.com/enslookup-search?search=",
        "",
        "name",
        "查询某个 ENS 域名有关的历史交易（利用 Etherscan）",
        "请输入 ENS 域名"
    ],
    [
        true,
        "https://cn.etherscan.com/token/0x57f1887a8bf19b14fc0df6fd9b2acc9af147ea85?a=",
        "#inventory",
        "token",
        "查询某个 TOKENID 对应的 ENS 域名",
        "请输入 token id"
    ],
    [
        true,
        "https://cn.etherscan.com/enslookup-search?search=",
        "",
        "address",
        "查询某个地址的反向解析记录",
        "请输入以太坊地址"
    ],
    [
        true,
        "https://app.ens.domains/address/",
        "",
        "address",
        "查看某个地址持有的 ENS 域名（利用 ENS 管理器）",
        "请输入以太坊地址"
    ],
    [
        true,
        "https://cn.etherscan.com/token/0x57f1887a8bf19b14fc0df6fd9b2acc9af147ea85?a=",
        "#inventory",
        "address",
        "查看某个地址持有的 ENS 域名（利用 Etherscan，不准确，仅供参考）",
        "请输入以太坊地址"
    ]
];
</script>

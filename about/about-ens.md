---
title: ENS 是什么 - ENS 官方介绍告诉你答案
keywords: ENS, ENS是什么, ENS名称, ENS介绍
description: 以太坊名称服务（Ethereum Name Service）是一个基于以太坊区块链的分布式、开放和可扩展的命名系统。通俗地说，ENS 就是区块链中的名称系统。ENS 名称让人们没有必要再复制或输入冗长的区块链地址。
---

<style>
h2,h3,h4 {text-align:center;}
</style>

## ENS 概念

<div class="ens-a ens-a1">
  以太坊名称服务（Ethereum Name Service）是一个基于以太坊区块链的分布式、开放和可扩展的命名系统。
  <br>
  通俗地说，ENS 就是区块链中的名称系统。
  <br>
  ENS 名称让人们没有必要再复制或输入冗长的区块链地址。
</div>

## ENS 用途

<ul class="ens-a ens-a2">
  <li class="ens-a2-li">
    <p><img src="/images/about/resolve-40.svg" alt=""></p>
    <div class="li-title">名称解析</div>
    <p>将名称（例如 “alice.eth”）映射到以太坊地址、内容哈希或其他元数据。</p>
  </li>
  <li class="ens-a2-li">
    <p><img src="/images/about/reverse-32.svg" alt=""></p>
    <div class="li-title">反向解析</div>
    <p>通过 “反向解析” 返回人类可读的名称，取代冗长的哈希值，从而提高Dapp的可用性。</p>
  </li>
</ul>

## 顶级名称（TLD）和子名称

<div class="ens-a ens-a3">
  <img src="/images/about/subdomainexplainer.png" alt=""><br>
  <p>与 DNS 一样，ENS 也是在以点分隔的层次结构名称的系统（称为域）上运行，域的所有者对子域的分配具有完全控制权。</p>
</div>

## 如何使用 ENS 名称

<ul class="ens-a ens-a4">
  <li class="ens-a4-li">
    <p><img src="/images/about/search.svg" alt=""></p>
    <div class="li-title">搜索名称</div>
    <p>查询名称是否可以注册或了解它的相关信息。</p>
  </li>
  <li class="ens-a4-li">
    <p><img src="/images/about/favourite.svg" alt=""></p>
    <div class="li-title">关注喜欢的名称</div>
    <p>关注您拥有或喜欢的名称和子名称。</p>
  </li>
</ul>
<ul class="ens-a ens-a4">
  <li class="ens-a4-li">
    <p><img src="/images/about/register.svg" alt=""></p>
    <div class="li-title">注册名称</div>
    <p>注册 .eth 名称，每年 5 美元，随时可以续费或弃用已注册的名称。</p>
  </li>
  <li class="ens-a4-li">
    <p><img src="/images/about/manage.svg" alt=""></p>
    <div class="li-title">管理名称</div>
    <p>将名称指向您的以太坊地址，将所有权转让给其他人等等。</p>
  </li>
</ul>

## 内部机制

<ul class="ens-a ens-a5">
  <li class="ens-a5-li">
    <p><img src="/images/about/registrar-26.svg" alt=""></p>
    <div class="li-title">注册器</div>
    <p>注册器是一个智能合约，支持注册和转移名称。当前运行的注册器是永久注册器，采用了即时注册的机制，而不再需要像从前那样拍卖注册。</p>
  </li>
  <li class="ens-a5-li">
    <p><img src="/images/about/registry-25.svg" alt=""></p>
    <div class="li-title">注册表</div>
    <p>注册表是一个智能合约，其中存储着所有名称和子名称的列表，并为每个名称记录着两个重要信息：名称的所有者和解析器。</p>
  </li>
</ul>
<ul class="ens-a ens-a5">
  <li class="ens-a5-li">
    <p><img src="/images/about/nametoaddress.png" alt=""></p>
    <div class="li-title">解析器</div>
    <p>解析器是一个智能合约，负责将名称转换为地址或其他类型的哈希和元数据。</p>
  </li>
  <li class="ens-a5-li">
    <p><img src="/images/about/addresstoname.png" alt=""></p>
    <div class="li-title">反向解析器</div>
    <p>反向解析器是一个智能合约，可以实现 “反向解析” ：将地址转换为关联的名称。</p>
  </li>
</ul>
---
description: 介紹Transaction Submission的模式以及如何選擇模式
---

# 總覽

### Transaction Submission是什麼

Transaction Submission 是 BlockRazor 的交易提交能力集合，基於不同交易提交模式滿足用戶交易快速上鏈、Gas贊助、MEV保護、實時返利等需求，為錢包、DEX、Trading Bot、Searcher和量化系統提供更優的交易體驗與執行效果。

### Transaction Submission的模式有哪些

#### RPC

RPC 是 Transaction Submission 的標準化交易提交入口。與普通公共 RPC 不同，BlockRazor RPC 在提升交易速度與穩定性的同時，提供了私有化轉發、MEV 防護與收益返還機制，適合作為大多數業務的默認交易通道。BlockRazor RPC同時提供bundle提交入口，滿足Approve + Swap / Backrun / Copy Trading / Sniping 等場景訴求。

#### Block Builder

Block Builder 是 BlockRazor 在 BSC 上提供的區塊構建基礎設施，支持 bundle submission、private transaction submission 和 bundle trace 等核心能力。它通過全球多地部署、與驗證者之間的低延遲通信以及多種區塊構建算法，提升區塊構建競爭力和出塊勝率。

#### Fast

Fast 是 Transaction Submission 中的“速度優先”模式，適用於對上鏈時延高度敏感的場景。它通過 [BEF](../he-xin-ji-shu/blockchain-edge-fabric.md) 讓交易在更短時間窗口內觸達出塊節點。

#### Gas Sponsor

Gas Sponsor是Transaction Submission 中的“成本優先”模式。通過代付 Gas 費的方式，Gas Sponsor 讓用戶在進行 Swap 時不再需要持有原生代幣（如 ETH、BNB、SOL）即可完成交易，幫助項目降低用戶入場門檻並提升交易體驗。

### 如何選擇Transaction Submission的模式

<table><thead><tr><th width="287">場景</th><th>用戶</th><th width="213">推薦模式</th></tr></thead><tbody><tr><td>防 MEV 並獲取返利</td><td>Wallets / DEX</td><td>RPC - RawTransaction</td></tr><tr><td>極致提速與更低上鏈時延</td><td>Wallets / DEX / Trading Bot</td><td>Fast</td></tr><tr><td>用戶無 gas 體驗</td><td>Wallets / DEX</td><td>Gas Sponsor</td></tr><tr><td>Approve + Swap / Backrun / Copy Trading / Sniping </td><td>Wallets / DEX / Trading Bot / Searcher</td><td>RPC - Bundle</td></tr></tbody></table>

### 常見問題

<details>

<summary>提交Bundle至RPC和提交Bundle至Block Builder有什麼區別</summary>

兩者的核心區別在於 Bundle 的提交路徑和最終到達的目標不同。\
提交 Bundle 至 BlockRazor RPC 時，BlockRazor RPC 會將 Bundle 低延遲轉發給主流 builders。這種方式更適合作為統一接入入口，用戶不需要逐個對接不同 builder即可完成 Bundle 提交。\
提交 Bundle 至 Block Builder 時，Bundle 會直接發送到 BlockRazor Builder。它更適合明確希望使用 BlockRazor Builder 能力與接入路徑的場景。

</details>


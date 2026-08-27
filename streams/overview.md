---
description: 介紹BlockRazor的Streams，Streams提供的能力以及如何選擇Stream
metaLinks:
  canonical: overview.md
  alternates:
    - https://app.gitbook.com/s/jbyfG8gOgcdsK3wVxNdQ/streams/overview
---

# Streams 總覽

### Streams是什麼

Streams 是 BlockRazor 提供的一組高性能實時數據流服務，用於為交易系統、策略系統和基礎設施系統提供低延遲的數據輸入。與交易提交類產品不同，Streams 關注的是“如何更早看到數據、如何更快同步狀態、如何更細地觀察網絡傳播過程”。

對於 Searcher、Trading Bot、Wallet、DEX 和量化交易系統來說，信號獲取速度、狀態同步速度和跨區域數據一致性，都會直接影響策略效果與執行質量。Streams 的核心价值在于帮助用户以更低延迟的方式获取链上关键数据，其主要解决四类问题：

* 更早發現未確認交易與訂單流信號
* 更快獲取最新區塊與已確認交易
* 更及時獲取 Gas Price、Priority Fee 或 Tip 等費用參考
* 更低延遲地同步本地節點與最新 world state

### Streams提供什麼能力

#### Public Mempool

Public Mempool 用於低延遲獲取未確認交易，適合需要盡早捕捉鏈上信號的場景。

* [Public Mempool](public-mempool/bsc/public-mempool.md)**:** 用於低延遲訂閱 pending 交易，適合監控公開交易信號
* [Tx Trace](public-mempool/bsc/tx-trace.md): 用於觀察交易在 Public Mempool 中的傳播路徑和跨區域時延分布，適合做交易延遲排查、多區域部署評估和傳播效果驗證

#### Private Mempool

[Private Mempool](private-mempool.md) 用於訂閱私有交易流數據，適合 Backrun, Sniping 和 Copy Trading 等場景

#### Block Stream

Block Stream 用於低延遲獲取最新區塊和已確認交易，適合關注確認後信號、區塊事件和鏈上結果的場景。與 Mempool 相比，Block Stream 觀察的是“已經進入區塊的數據”；而 Mempool 更關注“尚未確認、但已經進入傳播過程的數據”。對於需要確認交易結果、追蹤區塊事件或做區塊級分析的系統來說，Block Stream 更合適。

#### Network Fee Stream

Network Fee Stream 用於基於最近歷史區塊數據，實時獲取 Gas Price、Priority Fee 或 Tip 等費用參考。這類數據適合：

* 動態調整交易參數
* 優化費用策略
* 輔助交易發送決策

#### Node Stream

[Node Stream](node-stream/bsc/full-node-synchronization.md) 用於低延遲同步最新區塊和 world state，適合依賴本地節點狀態進行決策的系統。

與訂閱 Block Stream 不同，Node Stream 並不是單純推送區塊數據，而是讓用戶自己的全節點通過 [BEF](../he-xin-ji-shu/blockchain-edge-fabric.md) 完成更快同步。對於需要第一時間獲得最新狀態、並直接依賴本地節點運行策略或服務的團隊來說，Node Stream 更適合作為底層基礎設施能力。

### 如何選擇合適的Stream

<table data-search="false"><thead><tr><th>場景</th><th>適用用戶</th><th>推薦能力</th></tr></thead><tbody><tr><td>監聽最新 pending 交易並做 backrun / copy trading / sniping</td><td>Searcher, Trading Bot</td><td><a href="public-mempool/bsc/public-mempool.md">Public Mempool</a></td></tr><tr><td>訂閱私有訂單流並做 backrun / copy trading / sniping</td><td>Searcher, Trading Bot</td><td><a href="private-mempool.md">Private Mempool</a></td></tr><tr><td>動態優化 Gas / Priority Fee / Tip</td><td>Searcher, Trading Bot, Quant Team, Wallets, DEX</td><td><a href="network-fee-stream/">Network Fee Stream</a></td></tr><tr><td>保持本地節點和 world state 最新</td><td>Searcher, Trading Bot, Quant Team, Wallets, DEX</td><td><a href="node-stream/bsc/full-node-synchronization.md">Node Stream</a></td></tr><tr><td>觀察交易傳播路徑與跨區域時延</td><td>Searcher, Trading Bot, Quant Team</td><td><a href="public-mempool/bsc/tx-trace.md">Tx Trace</a></td></tr><tr><td>訂閱Base FlashBlock Stream</td><td>Searcher, Trading Bot, Quant Team, Wallets, DEX</td><td><a href="block-stream/base/get-flashblockstream/">FlashBlock Stream</a></td></tr><tr><td>訂閱 Solana 賬戶、交易、slot、block 數據</td><td>Searcher, Trading Bot, Quant Team, Wallets, DEX</td><td><a href="block-stream/solana/geyser-stream/">Geyser Stream</a></td></tr><tr><td>獲取 Solana 更底層、極低延遲的 shred 數據</td><td>Searcher, Trading Bot</td><td><a href="block-stream/solana/shred-stream.md">Shred Stream</a></td></tr></tbody></table>

### 快速開始

{% stepper %}
{% step %}
**採購Stream**

前往[訂閱](https://blockrazor.io/#/pricing)頁面，採購目標stream
{% endstep %}

{% step %}
**獲取auth**

詳見 [Authentication](../get-started/authentication.md)
{% endstep %}

{% step %}
**接入Stream**

根據目標stream接入文檔，接入目標stream，测试延迟、稳定性表现
{% endstep %}
{% endstepper %}

### 常見問題

<details>

<summary><strong>Public Mempool 和 Private Mempool 有什麼區別？</strong></summary>

Public Mempool 用於訂閱公開傳播的 pending 交易，適合盡早發現公開市場中的交易信號。\
Private Mempool 用於訂閱私有訂單流數據，適合 backrun 等場景。

</details>

<details>

<summary><strong>Mempool 和 Block Stream 有什麼區別？</strong></summary>

Mempool 關注尚未確認、但已經進入傳播過程的交易；Block Stream 關注已經進入區塊、完成確認的區塊和交易。\
如果你希望更早發現機會，優先看 Mempool；如果你更關注結果確認和區塊級分析，優先看 Block Stream。

</details>

<details>

<summary><strong>Block Stream 和 Node Stream 有什麼區別？</strong></summary>

Block Stream 是訂閱型數據流，用於低延遲獲取最新區塊和已確認交易。\
Node Stream 則更偏基礎設施能力，讓用戶自己的全節點通過高性能網絡更快同步最新區塊和 world state。\
如果你只是需要區塊數據，Block Stream 通常更直接；如果你需要依賴本地節點狀態運行系統，Node Stream 更合適。

</details>


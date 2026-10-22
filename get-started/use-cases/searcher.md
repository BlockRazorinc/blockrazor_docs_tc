---
description: 了解 MEV Searcher 如何使用 BlockRazor 获取更早的链上信号并更快地提交交易。
metaLinks:
  canonical: searcher.md
  alternates:
    - >-
      https://app.gitbook.com/s/jbyfG8gOgcdsK3wVxNdQ/get-started/use-cases/searcher
---

# 全鏈 Searcher 基礎設施：從信號獲取到交易提交

我們為多鏈 Backrun 和套利 Searcher 提供從信號獲取、狀態同步到交易提交的 BlockRazor 基礎設施。目前支持 Solana、BSC、Ethereum、Base 和 Robinhood Chain。

### Searcher 的全鏈工作流

Searcher 的 Alpha 策略負責發現機會、計算收益和控制風險；我們提供的基礎設施則用於縮短信號獲取、狀態同步和交易提交鏈路，為策略計算保留更多時間。

我們將 Backrun 和套利工作流拆成四個階段，幫助 Searcher 按實際需求選擇服務：

<table><thead><tr><th width="121.0625">階段</th><th>Searcher 要解決的問題</th><th>不同鏈上的典型數據或接口</th></tr></thead><tbody><tr><td>獲取信號</td><td>如何更早看到可能觸發策略的交易、區塊或排序數據</td><td>Public/Private Mempool、Block Stream、Sequencer Feed、Shred/Geyser Stream</td></tr><tr><td>同步狀態</td><td>如何獲得足以支持模擬和風控的最新狀態</td><td>本地全節點、Node Stream、Node-required Sequencer Feed</td></tr><tr><td>策略計算</td><td>如何完成解析、模擬、機會篩選和交易構建</td><td>Searcher 自有策略、節點、模擬器和風控系統</td></tr><tr><td>提交交易</td><td>如何把 Raw Transaction、Batch 或 Bundle 送到正確的執行路徑</td><td>RPC、Transaction Sending、Bundle API、Block Builder</td></tr></tbody></table>

{% hint style="warning" %}
我們不把信號流等同於可模擬的完整鏈狀態。BSC NewBlocks 和 Robinhood Chain Direct Sequencer Feed 可以提供較新的區塊信號，但不能替代 Searcher 自己維護的完整本地 world state。
{% endhint %}

### 我們為 Searcher 提供的多鏈能力

<table><thead><tr><th width="170.359375">區塊鏈</th><th>信號與狀態能力</th><th>交易提交能力</th></tr></thead><tbody><tr><td>BSC</td><td>Public Mempool、Private Mempool、NewBlocks、Full Node Synchronization、Network Fee Stream</td><td>RPC、Orderflow Auction、Block Builder、Broadcast Tx</td></tr><tr><td>Robinhood Chain</td><td>Node-required Sequencer Feed、Direct Sequencer Feed，均提供 Standard 和 Ultra</td><td><code>eth_sendRawTransaction</code>、<code>eth_sendBatch</code></td></tr><tr><td>Ethereum</td><td>Public Mempool、NewBlocks、CL/EL Client Sync</td><td>RPC、Transaction Sending</td></tr><tr><td>Solana</td><td>Shred Stream、Geyser Stream、Network Fee Stream</td><td>Transaction Sending</td></tr><tr><td>Base</td><td>Block Stream</td><td>RPC、Transaction Sending</td></tr></tbody></table>

### 鏈上場景示例：BSC

在 BSC，我們圍繞三個關鍵環節為 Searcher 提供服務：更早獲取 Pending Transaction、更快同步區塊或本地 world state，以及將 Raw Transaction 或 Bundle 提交到合適的 RPC 或 Builder 路徑。

#### BSC 場景一：基於公開 Pending Transaction 尋找機會

如果你的 Backrun 或套利策略依賴已經在 BSC 公開網絡中傳播的未確認交易，我們建議接入 [BSC Public Mempool](https://docs.blockrazor.io/tc/streams/public-mempool/bsc/public-mempool)。

我們建議將 Public Mempool 用於：

* 監控可能觸發 Backrun 或套利的 Pending Transaction
* 跟蹤目標地址、合約和交易類型，形成套利信號
* 發現公開交易帶來的 Backrun 機會
* 為跨市場、跨協議或狀態變化套利提供實時輸入

```
BSC Public Mempool
      ↓
交易解析與機會識別
      ↓
基於最新狀態進行模擬和風控
      ↓
構建 Raw Transaction 或 Bundle
      ↓
BSC RPC / Block Builder / Broadcast Tx
```

我們的 Public Mempool 只覆蓋公開傳播的 Pending Transaction，不包含通過 BlockRazor 私有 RPC 路徑進入的 Private Orderflow。

#### BSC 場景二：基於 Private Orderflow 執行 Backrun

如果你的 Backrun 策略需要處理通過 BlockRazor RPC 私有路徑進入、尚未公開傳播的訂單流，可以接入 [BSC Private Mempool](https://docs.blockrazor.io/tc/streams/private-mempool)。

我們通過 Private Mempool 提供經授權披露和統一匿名化處理的字段。Searcher 可以解析 Raw Bundle、完成模擬與風控，再通過 [BSC RPC Orderflow Auction](https://docs.blockrazor.io/tc/transaction-submission/rpc/bsc/orderflow-auction) 的 `eth_sendMevBundle` 提交 Backrun Transaction。

```
BSC Private Mempool Raw Bundle
      ↓
解析已披露字段並執行模擬
      ↓
構建 Backrun Transaction
      ↓
eth_sendMevBundle → 同區域 BSC RPC
      ↓
Orderflow Auction 與後續 Builder 提交
```

{% hint style="warning" %}
我們要求 Private Mempool 訂閱與 Bundle 提交保持區域和域名一致。接入前請核對 Private Mempool 和 Orderflow Auction 的最新 Endpoint 文檔。
{% endhint %}

#### BSC 場景三：選擇 NewBlocks 或 Full Node Synchronization

我們根據 Searcher 是否運行本地全節點、是否依賴完整 world state，推薦不同的狀態服務：

| 判斷條件                                      | 推薦服務                                                                                                         | 適用邊界                            |
| ----------------------------------------- | ------------------------------------------------------------------------------------------------------------ | ------------------------------- |
| 只需要低延遲獲取確認區塊、交易列表、區塊頭和 Next Validator 信息  | [NewBlocks](https://docs.blockrazor.io/tc/streams/block-stream/bsc/newblocks)                                | 面向應用的結構化區塊流，不提供完整本地 world state |
| Searcher 運行自己的 BSC 全節點，策略依賴本地 world state | [Full Node Synchronization](https://docs.blockrazor.io/tc/streams/node-stream/bsc/full-node-synchronization) | 通過 P2P 連接加速用戶自己的節點，不是結構化區塊訂閱接口  |
| 同時需要 Pending Transaction 和最新本地狀態          | Public/Private Mempool + Full Node Synchronization                                                           | 前者提供信號，後者加速本地節點狀態更新             |

#### BSC 場景四：根據交易類型選擇提交路徑

我們在 BSC 提供多種提交方式。Searcher 應根據交易是否為 Bundle、是否需要私有路由以及目標 Builder 來選擇：

| 提交需求                                                | 推薦能力                                                                                                            | 需要注意                                 |
| --------------------------------------------------- | --------------------------------------------------------------------------------------------------------------- | ------------------------------------ |
| 將 Raw Bundle 或 Backrun Bundle 通過統一入口轉發給主流 Builders  | [BSC RPC Orderflow Auction](https://docs.blockrazor.io/tc/transaction-submission/rpc/bsc/orderflow-auction)     | 使用 `eth_sendMevBundle`；被轉發不等同於一定被打包  |
| 明確希望直接使用 BlockRazor Builder                         | [BSC Block Builder](https://docs.blockrazor.io/tc/transaction-submission/block-builder)                         | Bundle 直接進入 BlockRazor Builder 的處理路徑 |
| 已有成熟 Builder 提交流程，需要縮短到 BlockRazor Builder 的網絡和請求路徑 | [Fast Submit](https://docs.blockrazor.io/tc/transaction-submission/block-builder/bsc-block-builder-fast-submit) | 優化提交鏈路，不改變交易邏輯，也不保證打包                |
| 發送單筆或批量 Raw Transaction，優先速度且不要求私有傳輸                | [BSC Broadcast Tx](https://docs.blockrazor.io/tc/transaction-submission/transaction-sending/bsc/broadcast-tx)   | 交易進入公開傳播路徑，不提供 MEV Protection        |

### 鏈上場景示例：Robinhood Chain

在 Robinhood Chain，我們通過 Sequencer Feed 為 Backrun 和套利 Searcher 提供區塊信號。這條鏈不使用 BSC 式 Public Mempool；我們提供 Node-required 和 Direct 兩種接入方式，兩者均有 Standard 和 Ultra 版本。

#### 先選擇 Node-required 還是 Direct

<table><thead><tr><th>模式</th><th width="112.6015625">是否需要節點</th><th width="199.05078125">數據行為</th><th>我們推薦的 Searcher 用法</th></tr></thead><tbody><tr><td>Node-required Standard</td><td>是</td><td>按區塊高度連續交付，不跳塊</td><td>節點同步、Backrun、訂單流分析和套利</td></tr><tr><td>Node-required Ultra</td><td>是</td><td>保持連續交付語義，進一步優化延遲</td><td>高競爭 Backrun、Arbitrage 和高頻策略</td></tr><tr><td>Direct Standard</td><td>否</td><td>優先最新區塊，擁堵時可能跳過中間區塊</td><td>不依賴完整本地狀態的套利信號獲取</td></tr><tr><td>Direct Ultra</td><td>否</td><td>通過 Ultra 路徑優先交付最新區塊</td><td>對延遲更敏感的套利信號獲取</td></tr></tbody></table>

#### Robinhood Chain 場景一：依賴完整本地狀態的 Backrun 或 Arbitrage

如果你的 Backrun 或套利策略需要連續區塊、完整本地狀態或本地執行結果，我們建議選擇 [Node-required Sequencer Feed](https://docs.blockrazor.io/tc/streams/node-stream/robinhood-chain/sequencer-feed)。

```
Node-required Sequencer Feed Standard / Ultra
      ↓
Robinhood Chain 本地節點連續同步
      ↓
本地狀態、模擬與策略計算
      ↓
構建並簽名 Raw Transaction 或 Batch
      ↓
Robinhood Chain Transaction Sending
```

當毫秒級差異對你的生產策略具有實際價值時，可以評估我們的 [Node-required Sequencer Feed Ultra](https://docs.blockrazor.io/tc/streams/node-stream/robinhood-chain/sequencer-feed-ultra)。Ultra 不改變 Node-required 的連續交付語義，只優化傳輸路徑。

#### Robinhood Chain 場景二：不運行節點的套利信號獲取

如果你的套利策略只需要盡快獲取最新區塊信號，且不依賴完整連續狀態，可以接入我們的 [Direct Sequencer Feed](https://docs.blockrazor.io/tc/streams/node-stream/robinhood-chain/direct-sequencer-feed)。

```
Direct Sequencer Feed Standard / Ultra
      ↓
客戶端解析最新區塊和交易信號
      ↓
策略判斷與交易構建
      ↓
Robinhood Chain Transaction Sending
```

我們的 Direct Feed 在擁堵或客戶端處理不及時時可能跳過中間區塊，因此不適合要求完整區塊歷史或連續本地狀態的 Backrun 和套利策略。對延遲更敏感時，可以評估 [Direct Sequencer Feed Ultra](https://docs.blockrazor.io/tc/streams/node-stream/robinhood-chain/direct-sequencer-feed-ultra)，並另外配置可靠的狀態與模擬來源。

#### Robinhood Chain 交易提交

我們的 [Robinhood Chain Transaction Sending](https://docs.blockrazor.io/tc/transaction-submission/transaction-sending/robinhood-chain) 提供區域 Endpoint，支持：

* `eth_sendRawTransaction`：提交單筆已簽名 Raw Transaction。
* `eth_sendBatch`：批量提交交易。

### Benchmark 摘要

#### BSC

* Public Mempool：在已公開的多區域測試中，BlockRazor 在絕大多數樣本中比普通 Node 更早向客戶端交付同一筆 Pending Transaction。[查看完整 Benchmark](https://docs.blockrazor.io/tc/streams/public-mempool/bsc/public-mempool#benchmark)
* NewBlocks：在已公開的多區域測試中，BlockRazor 在幾乎全部樣本中更早向客戶端交付同一區塊。[查看完整 Benchmark](https://docs.blockrazor.io/tc/streams/block-stream/bsc/newblocks#benchmark)
* Full Node Synchronization：連接 BlockRazor Relay 的節點在絕大多數樣本中更早收到同一區塊。[查看完整 Benchmark](https://docs.blockrazor.io/tc/streams/node-stream/bsc/full-node-synchronization#benchmark)
* Broadcast Tx：在同 Nonce 的成對交易測試中，BlockRazor 提交路徑更常進入較早區塊，或在同一區塊中獲得更靠前的 Transaction Index。[查看完整 Benchmark](https://docs.blockrazor.io/tc/transaction-submission/transaction-sending/bsc/broadcast-tx#benchmark)
* Block Builder Fast Submit：公開測試顯示，Fast Submit 可以縮短跨區域提交到 BlockRazor Builder 的路徑延遲。[查看完整 Benchmark](https://docs.blockrazor.io/tc/transaction-submission/block-builder/bsc-block-builder-fast-submit#benchmark)

#### Robinhood Chain

* Node-required Sequencer Feed Ultra：在已公開的 AWS Ohio 多可用區測試中，BlockRazor Ultra Feed 比官方 Feed 更早交付匹配區塊。[查看完整 Benchmark](https://docs.blockrazor.io/tc/streams/node-stream/robinhood-chain/sequencer-feed-ultra#benchmark)
* Transaction Sending：在已公開的多區域同 Nonce 成對交易測試中，BlockRazor 區域 Endpoint 的交易獲得了更高的包含比例。[查看完整 Benchmark](https://docs.blockrazor.io/tc/transaction-submission/transaction-sending/robinhood-chain#benchmark)

### 更多支持鏈能力

我們也為 Ethereum、Solana 和 Base 的 Backrun 或套利系統提供相應的數據與提交能力。具體可用性以各產品文檔為準。

#### Ethereum

* 信號：[Ethereum Public Mempool](https://docs.blockrazor.io/tc/streams/public-mempool/ethereum/public-mempool)
* 區塊：[Ethereum NewBlocks](https://docs.blockrazor.io/tc/streams/block-stream/ethereum/newblocks)
* 節點同步：[Ethereum CL/EL Client Sync](https://docs.blockrazor.io/tc/streams/node-stream/ethereum/cl-el-client-sync)
* 提交：[Ethereum RPC](https://docs.blockrazor.io/tc/transaction-submission/rpc/ethereum-rpc) 或 [Ethereum Broadcast Tx](https://docs.blockrazor.io/tc/transaction-submission/transaction-sending/ethereum/broadcast-tx)

#### Solana

* 信號與區塊：[Shred Stream](https://docs.blockrazor.io/tc/streams/block-stream/solana/shred-stream) 或 [Geyser Stream](https://docs.blockrazor.io/tc/streams/block-stream/solana/geyser-stream)
* 費用信號：[Network Fee Stream](https://docs.blockrazor.io/tc/streams/network-fee-stream/solana/get-transactionfee)
* 提交：[Solana Transaction Sending](https://docs.blockrazor.io/tc/transaction-submission/transaction-sending/solana/send-transaction)

#### Base

* 區塊信號：[Base Block Stream](https://docs.blockrazor.io/tc/streams/block-stream/base/get-blockstream)
* 提交：[Base Transaction Sending](https://docs.blockrazor.io/tc/transaction-submission/transaction-sending/base/eth_sendrawtransaction)

我們仍然使用“信號 → 狀態 → 計算 → 提交”框架幫助 Searcher 選型，但每條鏈需要使用自己的 API、數據字段和 Benchmark。

### FAQ

<details>

<summary><strong>Robinhood Chain Node-required 和 Direct Feed 如何選擇？</strong></summary>

如果你的 Backrun 或套利策略需要連續區塊、完整本地狀態或本地執行結果，我們建議選擇 Node-required。沒有節點、只需要最新套利信號時可以選擇 Direct，但 Direct 在擁堵時可能跳過中間區塊。

</details>

<details>

<summary><strong>低延遲是否保證交易被打包或獲得更高收益？</strong></summary>

不保證。我們的服務用於縮短信號、狀態同步或提交鏈路，但交易結果仍受有效性、費用、出價、區塊空間、Builder 或 Sequencer 行為、鏈上狀態和市場競爭影響。

</details>

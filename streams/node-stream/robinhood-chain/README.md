---
description: >-
  比較 Robinhood Chain 的 Node-required、Direct、Standard 與 Ultra Sequencer
  Feed，了解適用場景、數據連續性、性能測試和接入方式。
metaLinks:
  canonical: ./
  alternates:
    - >-
      https://app.gitbook.com/s/jbyfG8gOgcdsK3wVxNdQ/streams/node-stream/robinhood-chain
---

# Robinhood Chain Sequencer Feed 接入指南

Robinhood Chain Sequencer Feed 是由 Robinhood Chain 排序器通過 WebSocket 推送的實時數據流。節點和交易系統可以通過它更早地接收已排序區塊數據、跟蹤最新鏈上狀態，並為套利、訂單流分析、量化交易、Sniping 和 Copy Trading 等延遲敏感型應用爭取更多處理時間。

Robinhood Chain 官方主網 Sequencer Feed 地址為：

```
wss://feed.mainnet.chain.robinhood.com
```

BlockRazor 提供與官方接入方式兼容的低延遲 Sequencer Feed 服務，包括需要本地節點的 Node-required Feed，以及無需運行節點的 Direct Feed。每種接入方式均提供 Standard 和 Ultra 兩個版本。

> **快速選擇**
>
> * 需要連續接收所有區塊並維護完整節點狀態：選擇 **Node-required Sequencer Feed**。
> * 不運行節點，只需要盡快獲取最新區塊：選擇 **Direct Sequencer Feed**。
> * 希望進一步降低傳輸延遲：選擇對應的 **Ultra** 版本。
> * 不確定如何選擇：先查看下面的四種方案對比。

### 選擇適合你的 Robinhood Chain Sequencer Feed

| 方案                     | 是否需要節點 | 數據交付方式                      | 適合場景                            | 接入指南                 |
| ---------------------- | ------ | --------------------------- | ------------------------------- | -------------------- |
| Node-required Standard | 是      | 按區塊高度連續交付，不跳過區塊             | 節點同步、Backrunning、訂單流和量化交易       | 查看 Standard 接入指南     |
| Node-required Ultra    | 是      | 連續交付，通過 Ultra 路徑進一步優化延遲     | 高競爭套利、訂單流和高級量化策略                | 查看 Ultra 接入指南        |
| Direct Standard        | 否      | 優先交付最新區塊；網絡擁堵時可能跳過中間區塊      | Sniping、Copy Trading 和實時信號監測    | 查看 Direct 接入指南       |
| Direct Ultra           | 否      | 優先交付最新區塊，通過 Ultra 路徑進一步優化延遲 | 對時延高度敏感的 Sniping 和 Copy Trading | 查看 Direct Ultra 接入指南 |

### Node-required 與 Direct Sequencer Feed 有什麼區別？

#### Node-required Sequencer Feed

Node-required Sequencer Feed 面向運行 Robinhood Chain 節點的團隊。節點通過 `--node.feed.input.url` 訂閱 Feed，並按區塊高度連續接收區塊數據。

這種方式適合依賴完整節點狀態、連續區塊和本地執行結果的應用，例如：

* Backrunning 和套利策略
* 訂單流分析
* 量化交易系統
* 需要持續同步鏈上狀態的 Searcher
* 依賴本地節點進行交易構建或模擬的系統

接入時需要部署、維護和監控 Robinhood Chain 節點。

#### Direct Sequencer Feed

Direct Sequencer Feed 允許客戶端直接通過 WebSocket 接收最新區塊數據，無需部署 Robinhood Chain 節點。

這種方式部署成本較低，適合主要關注最新鏈上信號的應用，例如：

* Sniping
* Copy Trading
* 地址或交易監測
* 實時信號生成
* 不依賴完整節點狀態的交易系統

Direct Feed 優先交付最新區塊。在網絡擁堵期間，中間區塊可能被跳過，因此不適合要求完整、連續區塊歷史的任務。

### Standard 與 Ultra 有什麼區別？

Standard 版本適合希望改善 Feed 連接速度和穩定性，同時控制基礎設施成本的團隊。

Ultra 版本構建於 Standard 版本之上，通過 BlockRazor 的 Blockchain Edge Fabric 和優化傳輸路徑，進一步降低 Sequencer Feed 數據的交付延遲。

Ultra 更適合以下場景：

* 競爭以毫秒或微秒計算
* 策略必須盡早獲得最新鏈上狀態
* 交易構建和提交窗口非常短
* Standard 版本的性能仍不足以滿足生產需求

Ultra 不會改變 Node-required 或 Direct 的數據交付語義。選擇 Ultra 前，仍應先確定業務需要連續區塊還是只需要最新區塊。

### BlockRazor Sequencer Feed 如何降低延遲？

BlockRazor 通過區域接入點、優化網絡路由和底層傳輸基礎設施，將 Robinhood Chain Sequencer Feed 數據交付給節點或客戶端。

對於 Node-required Feed，節點只需要在啟動配置中使用對應的 BlockRazor Feed URL。Direct Feed 則允許應用通過標準 WebSocket 客戶端直接連接，無需配置 Robinhood Chain 節點。

當前文檔提供 Ohio 和 Tokyo 接入點。具體可用區域、WSS endpoint 和認證方式請查看對應產品的接入指南。

### 性能測試與測試方法

BlockRazor 使用同一個測試客戶端，同時連接 Robinhood Chain 官方 Feed 與 BlockRazor Feed，並比較同一區塊的到達時間。

測試規則如下：

1. 在相同區域和測試環境中建立兩條 WebSocket 連接。
2. 按區塊匹配兩個 Feed 收到的數據。
3. 先收到區塊的 Feed 記為 `0 ms` 相對延遲。
4. 後收到區塊的 Feed 延遲為兩個接收時間戳之差。
5. 彙總 P50、P90、P95、P99、最大值和樣本數量。

在 2026 年 10 月 2 日發布的 Node-required 測試中，測試客戶端部署在 AWS Ohio 的 `use2-az1`、`use2-az2` 和 `use2-az3`，BlockRazor [Node-required Sequencer Feed(Ultra)](sequencer-feed-ultra.md) 的胜率为 100%。

你可以在 GitHub 上利用工具復現測試：

[BlockRazor Robinhood Feed Benchmark Tool](https://github.com/BlockRazorinc/robinhood-feed-speed)

### 開始接入 Robinhood Chain Sequencer Feed

#### 如果你正在運行 Robinhood Chain 節點

從 Node-required Standard 指南開始：

接入 Node-required Sequencer Feed

如果你的策略對延遲極度敏感，可以進一步比較 Ultra：

接入 Node-required Sequencer Feed Ultra

#### 如果你不運行節點

使用 Direct Feed 直接接收最新區塊：

接入 Direct Sequencer Feed

對於延遲競爭更激烈的場景：

接入 Direct Sequencer Feed Ultra

你也可以查看 [Robinhood Chain 產品與性能概覽](https://blockrazor.io/products/robinhood/)，比較 Sequencer Feed 與 Transaction Sending 服務。

### 常見問題

#### Robinhood Chain Sequencer Feed 是什麼？

Robinhood Chain Sequencer Feed 是由排序器通過 WebSocket 推送的實時數據流。它向節點和兼容客戶端提供最新的已排序區塊數據，使系統能夠比等待普通 RPC 查詢結果更早地跟蹤最新鏈上狀態。

#### BlockRazor 是 Robinhood Chain 官方 Feed 嗎？

不是。Robinhood Chain 官方 Feed 由 Robinhood Chain 提供。BlockRazor 提供與官方接入方式兼容的獨立低延遲服務，通過區域接入點和優化傳輸路徑改善數據交付速度與穩定性。

#### 使用 BlockRazor Feed 必須運行節點嗎？

不一定。Node-required Feed 必須通過 Robinhood Chain 節點接收，適合需要連續區塊和完整節點狀態的系統。Direct Feed 無需節點，客戶端可以直接連接，但在網絡擁堵時可能跳過中間區塊。

#### Direct Feed 為什麼可能跳過區塊？

Direct Feed 優先讓客戶端獲得最新區塊。如果客戶端處理速度不足或網絡發生擁堵，服務可能跳過部分中間區塊並繼續交付最新數據。因此，Direct Feed 更適合實時信號，而不是完整歷史同步。

#### Standard 和 Ultra 應該如何選擇？

如果你需要穩定、低延遲的生產級 Feed，可以先選擇 Standard。對於高競爭套利、Sniping 或其他對每一毫秒都敏感的策略，可以根據部署區域和實際 A/B 測試結果評估 Ultra。

#### Benchmark 中的 0 ms 是否表示沒有延遲？

不是。`0 ms` 是相對延遲，表示該 Feed 在同一區塊的兩條測試連接中先到。它不表示數據從 Robinhood Chain Sequencer 到客戶端的絕對傳輸時間為零。

### 下一步

選擇與你的節點架構和數據連續性要求相匹配的 Feed，然後按照對應接入指南配置 WSS endpoint、認證信息和客戶端。

在生產環境切換前，建議從實際部署區域同時連接官方 Feed 與 BlockRazor Feed，運行一段時間的 A/B 測試，並根據區塊先到率、P50/P99 延遲、斷線次數和缺塊情況評估結果。

---
description: 介紹BlockRazor BSC Block Builder的Fast Submit以及接入方式
metaLinks:
  canonical: bsc-block-builder-fast-submit.md
  alternates:
    - >-
      https://app.gitbook.com/s/jbyfG8gOgcdsK3wVxNdQ/transaction-submission/block-builder/fast-submit
---

# BSC Block Builder Fast Submit

### Fast Submit是什麼

Fast Submit 是 BlockRazor 為 BSC Block Builder 提供的一條專用交易提交通道，用於優化交易從客戶端到 Builder 的網絡路徑與請求處理路徑，提升提交速度與穩定性。\
​\
在 BSC Builder 場景下，交易質量不只取決於交易內容、Gas、滑點和發送時機。對於延遲敏感、跨區域部署或追求穩定性的用戶來說，交易以什麼路徑到達 Builder，本身就是提交質量的一部分。\
​\
Fast Submit 不改變交易邏輯，也不要求用戶重寫現有提交流程。它優化的是客戶端到 Builder 之間的提交鏈路，讓交易更快到達Builder，讓提交鏈路在高負載和跨區域場景下更穩定，減少中間轉發、代理處理和公網波動帶來的額外開銷。

### Benchmark

根據 benchmark，Fast Submit 相比標準提交鏈路在跨洋鏈路下可降低約 50ms 延遲，在內陸跨區域鏈路下可降低約 20ms 延遲。

#### 對於交易流項目

例如 DEX、Wallet 等交易流項目，交易發送時機往往不受控制。此時，更短的提交鏈路意味著交易有更高概率更早進入 Builder 的處理窗口，從而為用戶提供極致的 0-block 上鏈體驗。 以當前 BSC 約 450ms 的出塊時間作為近似參考，50ms 和 20ms 的時延縮短，大致對應約 11% 和 4.4% 的時間窗口改善；如果未來區塊時間進一步縮短到約 250ms，對應改善可進一步擴大到約 20% 和 8%。

#### 對於同塊競爭排序的交易系統&#x20;

對於Backrun、Sniping、Copy Trading等策略型交易系統，Fast Submit 的價值不只是“更容易趕上”，還在於在相同競爭條件和相同的Builder 到達時點下，可以為策略計算留出更充分的計算時間。

### 為什麼 Fast Submit 更快、更穩

**1. 專屬 HTTP 域名**

普通提交通道中的交易請求在到达 Block Builder 之前会经过 CloudFront 代理层，承担更多中间转发、网络跳数，以及额外的 TLS 和代理处理开销。在网络请求拥堵或高并发场景下，这些额外环节带来的排队、转发和处理时间往往会被进一步放大，使请求延迟和波动更加明显。

相比之下，Fast Submit 支持通过专用 HTTP 域名提交请求，移除 CloudFront 代理层，压缩客户端到 Block Builder 之间的提交路径，减少中间层带来的额外损耗。

**2. 跨洲專線**

普通提交通道在跨区域场景下依赖公网路径，容易受到路由波动、链路拥塞和网络抖动的影响。尤其在跨洲传输过程中，这些不稳定因素会放大请求时延和波动，降低提交链路的一致性。

Fast Submit 则通过跨洲专线网络将交易提交至 Block Builder，尽量降低公网不稳定因素对请求链路的影响，提供更稳定更低時延的提交体验。

### Fast Submit適合什麼用戶

Fast Submit 更適合已有成熟提交流程，對延遲、穩定性和跨區域表現有明確要求的用戶

* Searcher: 關注交易提交時機、路徑質量和跨區域穩定性的 MEV 參與者
* Trading Bot / Quant Team: 對執行速度、高頻提交和區域一致性有明確要求的策略團隊
* Wallets / DEXs: 希望在不改動現有發送邏輯前提下優化用戶交易提交體驗的產品團隊

### 接入方式

開發者只需將現有的 HTTP 提交請求，切換到專屬的 Fast Submit 域名即可。接入流程如下：

1. 前往[訂閱](https://blockrazor.io/#/pricing)頁面採購極速服務包
2. [聯繫](https://discord.gg/qqJuwRb8Nh)我們，獲取 Fast Submit 專屬接入域名
3. 保持原有請求邏輯不變，將現有交易提交請求發送到該專屬 HTTP 域名
4. 從實際部署區域測試提交時延與穩定性表現，與原有提交通道進行對比評估

### 注意事項

Fast Submit 旨在優化交易到達 BSC Block Builder 的速度與穩定性，但不保證交易一定會被打包。Fast Submit 是一項面向提交鏈路的基礎設施優化能力，而不是打包結果承諾。

最終是否被納入區塊，仍然取決於多種提交鏈路之外的因素，包括但不限於：市場競爭情況、Builder 處理策略、當前區塊空間、交易質量和鏈上實時擁堵狀態等。

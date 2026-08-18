---
description: 介紹BlockRazor BSC RPC的集成方式、接口接入文檔
layout:
  width: wide
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
  metadata:
    visible: true
  tags:
    visible: true
  actions:
    visible: true
metaLinks:
  canonical: ./
  alternates:
    - >-
      https://app.gitbook.com/s/jbyfG8gOgcdsK3wVxNdQ/transaction-submission/rpc/bsc
---

# BSC RPC

### BSC RPC 是什麼

BSC RPC 是 BlockRazor 面向 BNB Smart Chain（BSC）提供的 RPC 服務，支持常用的標準 JSON-RPC 方法。用戶既可以通過它查詢鏈上數據，也可以發送交易，並在交易發送過程中獲得隱私保護、MEV 防護、低延遲上鏈和實時返利等能力。

### 為什麼選擇 BSC RPC

**交易隱私**：交易通過隱私路徑上鏈，減少公開傳播帶來的交易意圖暴露風險，抵禦 sandwich 和 frontrunning 等惡意 MEV 攻擊

**實時返利**：通過可控的數據披露機制支持無害 backrun，默認實時返還 60% 的可分配 backrun 收益

**低延遲上鏈**：基於 BEF 的路徑與拓撲優化，配合全球入口和區域 Endpoint，為延遲敏感、高頻及跨區域部署提供更穩定的提交能力。

**零門檻集成**：支持一鍵添加到錢包，同時保持標準 JSON-RPC 使用方式，無需auth，方便快速替換現有 BSC RPC

### BSC RPC 適合哪些用戶？

**Wallets / DEXs**：希望提升 BSC 交易保護能力、改善成交體驗並為用戶提供返利的團隊

**Trading Bot / Quant Teams**：關注低延遲、交易排序、上鏈穩定性和跨區域執行質量的團隊

**Searchers**：需要提交 Bundle 或參與 Orderflow Auction 的專業用戶

**Project Builders**：需要專屬 RPC、低延遲 Builder 連接及自定義披露和返利配置的項目方

**Individual Traders**：希望在 BSC 上使用更安全交易路徑並獲得潛在 MEV 返利的普通用戶

### 常見問題

<details>

<summary>為什麼交易受到 MEV 保護後仍然可以獲得返利？</summary>

MEV 保護主要阻止 sandwich 和 frontrunning 等會傷害用戶的策略。對於不改變用戶預期執行結果的安全 backrun，BlockRazor 可以在用戶授權的披露範圍內向合格 Searcher 提供必要信息，並把成功產生的部分收益返還給用戶。

</details>

<details>

<summary>BSC RPC在哪些地區提供服務</summary>

BSC RPC目前分佈式部署於弗吉尼亞、東京、法蘭克福和都柏林

</details>

<details>

<summary>BSC RPC 和 BSC Block Builder 有什麼區別？</summary>

BSC RPC 是面向錢包、DEX、交易系統和普通用戶的統一交易入口和標準JSON RPC方法調用入口，也可以低延遲轉發 Bundle 至主流 Builder。BSC Block Builder 適合明確需要其出塊、排序、Bundle 或私有交易能力的專業場景。

</details>

<details>

<summary>返利什麼時候到帳？</summary>

如果交易存在可執行的 backrun 空間並成功產生收益，返利通常通過鏈上交易實時處理，並可能和用戶交易在同一區塊內完成。

</details>

### 隱私聲明

BlockRazor 不以廣告追蹤為目的收集用戶的 IP 地址、位置等個人隱私信息。服務僅在提供產品和改善使用體驗所必需的範圍內處理數據，並保留區塊鏈上已公開的必要信息，例如交易時間戳。具體數據處理方式以 BlockRazor 最新隱私聲明為準。




---
description: >-
  BlockRazor Ethereum RPC 提供標準 JSON-RPC 訪問，以及私有交易路由、MEV 防護、低延遲上鏈和實時 backrun 返利+
  Gas 返利等能力。
metaLinks:
  canonical: ./
  alternates:
    - >-
      https://app.gitbook.com/s/jbyfG8gOgcdsK3wVxNdQ/transaction-submission/rpc/ethereum
---

# Ethereum RPC

### Ethereum RPC 是什麼？

Ethereum RPC 是 BlockRazor 面向 Ethereum 提供的 RPC 服務，支持常用的標準 JSON-RPC 方法。用戶既可以通過它查詢鏈上數據，也可以發送交易，並在交易發送過程中獲得隱私保護、MEV 防護、低延遲上鏈、backrun 返利和 Gas 返利等能力。

### 為什麼選擇 Ethereum RPC

**交易隱私**：交易通過隱私路徑上鏈，減少進入公開 mempool 後帶來的交易意圖暴露風險，抵禦 sandwich 和 frontrunning 等惡意 MEV 攻擊。

**Backrun 返利**：交易按照既定的數據披露規則進入訂單流。符合要求的 Searcher 可以根據被允許披露的信息，自行尋找不損害用戶交易結果的 backrun 機會。如果 Searcher 提交的 backrun 成功上鏈並產生收益，用戶默認可獲得 **90% 的可分配 backrun 收益**。

**Gas 返利**：除 Backrun 返利外，符合Ethereum Builder返利策略的 Ethereum 交易還可以獲得 Gas 返利，降低用戶的實際交易成本。

**低延遲上鏈**：基於 BEF 的路徑和拓撲優化，BlockRazor Ethereum RPC 將交易發送到更有效的處理入口，降低交易傳播延遲和網絡抖動，為延遲敏感、高頻及跨區域部署提供更穩定的上鏈能力。

**零門檻集成**：BlockRazor Ethereum RPC 支持一鍵添加到錢包，同時保持標準 JSON-RPC 使用方式。公共 RPC 無需 auth，方便用戶和項目方快速替換現有 Ethereum RPC。如需配置交易信息披露範圍、返利接收地址、專屬域名或 revert protection，項目方可以申請專屬 RPC。

### Ethereum RPC 適合哪些用戶

**Wallets / DEXs**：希望提升 Ethereum 交易保護能力、改善成交體驗，並為用戶提供 backrun 和 Gas 返利的團隊

**Trading Bot / Quant Teams**：關注低延遲、交易隱私、上鏈穩定性和跨區域執行質量的團隊

**Project Builders**：需要專屬 RPC、自定義數據披露規則、返利地址和 revert protection 配置的項目方

**Individual Traders**：希望使用更安全的 Ethereum 交易路徑，並獲得潛在 backrun 返利和 Gas 返利的普通用戶

### 常見問題

<details>

<summary>Backrun 返利和 Gas 返利有什麼區別？</summary>

Backrun 返利來自 Searcher 對訂單流執行無害 backrun 後產生的可分配收益，而Gas 返利與交易的 Priority Fee 相關，符合Ethereum Builder返利策略的 Ethereum 交易可以獲得 Gas 返利，兩者的返利比例均為90%

</details>

<details>

<summary>每筆交易都能獲得兩種返利嗎？</summary>

不能保證。Backrun 返利要求 Searcher 找到有效機會、成功提交 backrun 並產生收益；Gas 返利則要求交易符合當前 Gas 返利策略。某筆交易可能只獲得其中一種返利、同時獲得兩種返利，或者不產生返利。

</details>

<details>

<summary>使用 Ethereum RPC 需要修改交易代碼嗎？</summary>

不需要。BlockRazor Ethereum RPC 兼容標準 JSON-RPC 方法，包括 `eth_sendRawTransaction`。普通用戶可以直接更換錢包 RPC；應用和交易系統通常只需要替換 RPC Endpoint。

</details>

### 隱私聲明

BlockRazor 不以廣告追蹤為目的收集用戶的 IP 地址、位置等個人隱私信息。服務僅在提供產品和改善使用體驗所必需的範圍內處理數據，並保留區塊鏈上已公開的必要信息，例如交易時間戳。具體數據處理方式以 BlockRazor 最新隱私聲明為準。

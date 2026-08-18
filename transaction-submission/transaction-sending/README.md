---
description: 介紹BlockRazor Transaction Sending模式，以及提供的服務和接口接入文檔
metaLinks:
  canonical: ./
  alternates:
    - >-
      https://app.gitbook.com/s/jbyfG8gOgcdsK3wVxNdQ/transaction-submission/transaction-sending
---

# Transaction Sending

### Transaction Sending 模式是什麼

Transaction Sending 是 BlockRazor 提供的快速交易發送模式，面向對交易上鏈速度有更高要求的用戶，適用於 Solana、BSC 和 Base。

相比 RPC 發送模式，Transaction Sending 更關注交易從客戶端發出後，如何以更快的路徑進入鏈上執行流程。它依託 [BEF](../../he-xin-ji-shu/blockchain-edge-fabric.md) 充分利用不同鏈的底層機制，為交易系統提供更低延遲的發送體驗。

### Transaction Sending 模式適合什麼用戶

* **Wallets / DEXs**：希望為全球用戶提供更快發送體驗的產品團隊
* **Trading Bot / Quant Team**：對交易上鏈速度和執行時機有明確要求的策略團隊

### 常見問題

<details>

<summary>Fast 和 RPC 有什麼區別</summary>

Fast 和 RPC 都屬於交易發送能力，但它們的設計目標不同。

RPC 更側重於交易保護與通用接入能力。它提供標準 JSON-RPC 方法，重點解決交易在公開傳播過程中可能遭遇的 MEV 風險，並支持返利、披露策略配置和定制化 RPC 接入，適合 Wallet、DEX 和項目方作為標準交易發送入口使用。

Fast 更側重於交易上鏈速度。它通過 [BEF](../../he-xin-ji-shu/blockchain-edge-fabric.md) 幫助交易以更低延遲進入鏈上執行流程，適合對上鏈時效有更高要求的 Trading Bot、量化策略和時機敏感型交易場景。

</details>


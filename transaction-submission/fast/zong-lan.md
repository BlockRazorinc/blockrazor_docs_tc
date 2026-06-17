---
description: 介紹BlockRazor Fast模式以及其適用用戶
---

# 總覽

### Fast模式是什麼

Fast 是 BlockRazor 提供的快速交易發送模式，面向對交易上鏈速度有更高要求的用戶，適用於 Solana、BSC 和 Base。

相比 RPC 發送模式，Fast 更關注交易從客戶端發出後，如何以更快的路徑進入鏈上執行流程。它依託 [BEF](../../he-xin-ji-shu/blockchain-edge-fabric.md) 充分利用不同鏈的底層機制，為交易系統提供更低延遲的發送體驗。

在 Fast 模式下，發送交易需要在交易內部向指定地址支付 tip，用於支持更快的交易處理路徑。不同鏈的 tip 規則、接口形式和發送方式有所不同，具體可在對應鏈頁面查看。

### Fast模式適合什麼用戶

* **Wallets / DEXs**：希望為全球用戶提供更快發送體驗的產品團隊
* **Trading Bot / Quant Team**：對交易上鏈速度和執行時機有明確要求的策略團隊

### 常見問題

<details>

<summary>Fast 和 RPC 有什麼區別</summary>

Fast 和 RPC 都屬於交易發送能力，但它們的設計目標不同。

RPC 更側重於交易保護與通用接入能力。它提供標準 JSON-RPC 方法，重點解決交易在公開傳播過程中可能遭遇的 MEV 風險，並支持返利、披露策略配置和定制化 RPC 接入，適合 Wallet、DEX 和項目方作為標準交易發送入口使用。

Fast 更側重於交易上鏈速度。它通過 [BEF](../../he-xin-ji-shu/blockchain-edge-fabric.md) 幫助交易以更低延遲進入鏈上執行流程，適合對上鏈時效有更高要求的 Trading Bot、量化策略和時機敏感型交易場景。

</details>

<details>

<summary>為什麼 Fast 需要 tip</summary>

Fast 的核心目標是幫助交易以更快的路徑進入鏈上執行流程，而實現這一點通常需要額外的發送和執行激勵機制。因此，在 Fast 模式下，交易需要在內部向指定地址支付 tip，用於支持更快的交易處理路徑。

從機制上看，tip 的作用是讓交易在 Fast 模式下獲得更適合低延遲執行的處理方式。不同鏈的 tip 規則並不完全相同，具體金額、計算方式和指定地址也會有所區別，需要以對應鏈的子頁面說明為準。

如果不希望在交易中附加 tip，更適合使用標準 RPC 或其他非 Fast 發送方式。

</details>


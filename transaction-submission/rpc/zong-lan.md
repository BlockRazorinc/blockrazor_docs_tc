---
description: 介紹BlockRazor RPC的服務、用戶和優勢
---

# 總覽

### BlockRazor RPC是什麼

BlockRazor RPC 是面向 Ethereum, BSC 和Base 的交易提交通道，為用戶提供交易隱私保護、MEV 防護和實時返利能力。

在常規公共 RPC 中，用戶提交的交易通常會進入公開傳播路徑，並在節點網絡中廣播，更容易暴露交易意圖，使交易受到 sandwich、frontrunning 等惡意 MEV 攻擊的影響，不僅可能帶來更差的成交價格，也可能讓原本屬於用戶的潛在價值被外部策略搶走。

相比之下，BlockRazor RPC 會將交易保留在隱私鏈路中，降低交易在公開傳播過程中被惡意利用的風險。同時，BlockRazor RPC 支持對交易數據披露範圍進行自定義配置，在保證交易安全的前提下，將適合公開的部分信息提供給 Searcher 執行無害的 backrun 策略，並將由此產生的部分收益實時返還給用戶。

### BlockRazor RPC提供什麼

BlockRazor RPC 主要提供以下能力：

* 交易隱私保護，減少公開傳播帶來的風險
* 抵御 sandwich 和 frontrunning 等惡意 MEV 攻擊
* 通過可控的數據披露機制支持無害 backrun
* 將部分 backrun 收益實時返還給用戶
* 支持項目方快速接入，並可自定義 RPC 域名和相關配置

### BlockRazor RPC適合哪些用戶

* **Wallets / DEXs：**&#x5E0C;望提升交易保護能力、優化用戶成交體驗並引入返利機制的團隊
* **Trading Bot / Quant Team：**&#x5E0C;望在提交交易時減少惡意 MEV 干擾，並兼顧執行質量的團隊
* **Project Builders：**&#x9700;要專屬 RPC 接入能力，並希望自定義交易披露和返利配置的項目方
* **普通交易用戶：**&#x5E0C;望在 Ethereum 和 BSC 上獲得更安全交易路徑的用戶

### 為什麼選擇BlockRazor RPC

高安全性：BlockRazor RPC 為 Ethereum 和 BSC 上的全類型交易提供 MEV 防護能力，重點抵御 sandwich 和 frontrunning 等對用戶危害較大的攻擊方式。同時對Seacher採取高門檻准入策略，結合實時MEV攻擊監控，進一步降低用戶交易風險。

快速打包：BlockRazor RPC 基於 [BEF](../../he-xin-ji-shu/blockchain-edge-fabric.md) 將交易以最低延遲和最高價值發送至出塊節點，以爭取更優的上鏈效果。

實時返利：當用戶交易存在可執行的無害 backrun 空間時，Searcher 可基於被授權披露的交易信息構造 backrun 策略，並將其中一部分收益實時返還給用戶。

極速集成：BlockRazor RPC 保持標準 JSON-RPC 使用方式，便於錢包、DEX、Trading Bot 和項目方快速接入。對於普通用戶來說，可以像使用標準 RPC 一樣直接使用；對於項目方來說，BlockRazor RPC 還支持自定義域名、交易披露策略、返利地址和 revert protection 等配置，方便以較低成本完成集成和上線。

### 常見問題

<details>

<summary><strong>為什麼交易在受到 MEV 保護的同時還能獲得返利？</strong></summary>

因為 BlockRazor RPC 支持按自定義規則披露交易信息。Searchers 可以基於這些被授權披露的數據執行無害的 backrun 策略，而不是對用戶有害的 sandwich 或 frontrunning 攻擊。由 backrun 產生的部分收益會實時返還給用戶。

</details>

<details>

<summary><strong>返利會在什麼時候到賬？以什麼形式完成？</strong></summary>

如果用戶交易存在可執行的 backrun 空間，Searchers 會通過鏈上方式實時完成返利。通常情況下，返利交易會與用戶交易一起在同一區塊內完成執行。

</details>

<details>

<summary><strong>交易會不會revert？</strong></summary>

如果 BlockRazor RPC 檢測到交易會回滾，並且交易所屬渠道開啓了 revert protection，那麼這筆交易將不會被打包上鏈。

</details>

<details>

<summary><strong>收益是如何分配的？</strong></summary>

當交易存在利潤空間並成功打包後，部分收益會返還給用戶，剩餘部分用於支付 BlockRazor RPC 的服務費用，以及支付 builder 為爭取更優打包位置所需的成本。

</details>

### 隱私聲明

BlockRazor RPC 不跟蹤任何類型的用戶信息（例如 IP 地址、位置等）。僅保留區塊鏈上公開的信息，如交易的時間戳。

---
description: 介紹BlockRazor BSC Block Builder，適合什麼用戶，提供什麼能力，以及如何選擇端點集成
---

# BSC Block Builder

### BSC Block Builder是什麼

Block Builder 是 BlockRazor 提供的 BSC 區塊構建服務，基於 [BEF](../../he-xin-ji-shu/blockchain-edge-fabric.md) 和 Flow Coordination Engine 保持區塊構建競爭力與出塊勝率。Block Builder 目前的出塊率排名 BSC 全鏈第一，實時出塊率可查看 [https://dune.com/bnbchain/bnb-smart-chain-mev-stats](https://dune.com/bnbchain/bnb-smart-chain-mev-stats)。

Block Builder 面向BSC交易執行質量有要求的用戶，核心價值包括：

* 更高勝率的區塊構建能力
* 面向全球部署的低延遲接入能力
* 支持多種交易提交模式，適配不同執行需求
* 為時延敏感、隱私敏感和順序敏感場景提供更合適的提交路徑

### BSC Block Builder適合什麼用戶

* **Searcher：**&#x9700;要提交 Bundle、捕捉 MEV 機會，並關注交易順序與執行時機的用戶
* **Trading Bot / Quant Team：**&#x5C0D;延遲、執行穩定性和跨區域表現有明確要求的策略團隊
* **Wallets / DEXs：**&#x9700;要更好交易執行體驗、隱私保護或更優提交路徑的產品團隊

### BSC Block Builder提供什麼能力

Block Builder 當前支持以下幾類核心能力：

* **Send Bundle：**&#x9069;用於對交易順序、原子性和同區塊執行有要求的場景。
* **Send PrivateTransaction：**&#x9069;用於希望避免交易暴露在公開 Mempool 中、降低被搶跑或被惡意觀察風險的場景。
* **Trace Bundle：**&#x9069;用於對 Bundle 提交結果和執行表現進行追蹤與分析的場景，結合Bundle Explorer可以幫助用戶更細粒度地觀察 Bundle 在 Builder 鏈路中的表現，為策略優化、問題排查和效果復盤提供參考。

### 端點

**默認接入**

優先使用全局通用入口，適合快速完成接入並服務全球請求，端點：<mark style="color:$primary;">**https://rpc.blockrazor.builders**</mark>

**區域化優化**

如果你的 Bot 或服務已經部署在特定區域，並且對延遲一致性更敏感，可以在接入全球通用端點的基礎上進一步接入區域入口

<table><thead><tr><th width="127">地區</th><th width="146">可用區（AWS）</th><th>RPC端點</th></tr></thead><tbody><tr><td>東京</td><td>apne1-az4</td><td>https://tokyo.builder.blockrazor.io</td></tr><tr><td>法蘭克福</td><td>euc1-az2</td><td>https://frankfurt.builder.blockrazor.io</td></tr><tr><td>弗吉尼亞</td><td>use1-az4</td><td>https://virginia.builder.blockrazor.io</td></tr><tr><td>都柏林</td><td>euw1-az1</td><td>https://dublin.builder.blockrazor.io</td></tr></tbody></table>

**鏈路質量加強**

如果你已經完成基礎接入，開始關注提交路徑中的額外開銷、跨區域波動和高負載下的穩定性，可以進一步接入 [Fast Submit](bsc-block-builder-fast-submit.md)

### 快速開始

{% stepper %}
{% step %}
**申請 Auth**

詳見 [Authentication](../../get-started/authentication.md)
{% endstep %}

{% step %}
**根据场景和需求选择对应能力和**[**端點**](./#duan-dian)

* [Send Bundle](send-bundle.md)
* [Send PrivateTransaction](send-privatetransaction.md)
* [Trace Bundle](trace-bundle.md)
* [Call Bundle](call-bundle.md)
{% endstep %}

{% step %}
在真實部署區域中驗證時延與穩定性表現
{% endstep %}
{% endstepper %}

### 常見問題

<details>

<summary>提交Bundle至RPC和提交Bundle至Block Builder有什麼區別</summary>

两者的核心区别在于 Bundle 的提交路径和最终到达的目标不同。

提交 [Bundle](/broken/pages/RrHwbLZI5iNwmmY7gGLL) 至 **BlockRazor RPC** 时，BlockRazor RPC 会将 Bundle 低延迟转发给主流 builders。这种方式更适合作为统一接入入口，用户不需要逐个对接不同 builder，即可完成 Bundle 提交。

提交 Bundle 至 **Block Builder** 时，Bundle 会直接发送到 BlockRazor Builder。它更适合明确希望使用 BlockRazor Builder 能力与接入路径的场景。

</details>


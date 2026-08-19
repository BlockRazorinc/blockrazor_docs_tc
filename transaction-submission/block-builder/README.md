---
description: 介紹BlockRazor BSC Block Builder，適合什麼用戶，提供什麼能力，以及如何選擇端點集成
metaLinks:
  canonical: ./
  alternates:
    - >-
      https://app.gitbook.com/s/jbyfG8gOgcdsK3wVxNdQ/transaction-submission/block-builder
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

提交 [Bundle](../rpc/bsc/eth_sendbundle.md) 至 **BlockRazor RPC** 时，BlockRazor RPC 会将 Bundle 低延迟转发给主流 builders。这种方式更适合作为统一接入入口，用户不需要逐个对接不同 builder，即可完成 Bundle 提交。

提交 Bundle 至 **Block Builder** 时，Bundle 会直接发送到 BlockRazor Builder。它更适合明确希望使用 BlockRazor Builder 能力与接入路径的场景。

</details>

<details>

<summary><strong>如何理解bundle的平均gas price需不小於0.05 gwei？</strong></summary>

假設一個 Bundle 中有 3 筆交易 `{tx1, tx2, tx3}`，其中 `tx1` 來自 mempool，BlockRazor Builder 會排除 `tx1`，僅計算 `tx2` 和 `tx3` 的平均 gas price。若 `tx3` 額外支付 tip 給 Builder，則該 tip 金額會加入計算公式的分子，分母仍只計算 `tx2` 和 `tx3` 的 gas used。計算公式為：`(tx2.gas price × tx2.gas used + tx3.gas price × tx3.gas used + tx3.tip) / (tx2.gas used + tx3.gas used)`。

</details>


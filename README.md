---
description: 介紹BlockRazor是誰，BlockRazor面向的用戶以及提供的服務，包括Transaction Submission和Streams
metaLinks:
  canonical: ./
  alternates:
    - https://app.gitbook.com/s/jbyfG8gOgcdsK3wVxNdQ/get-started/about-us
---

# 關於我們

BlockRazor 是一家專注於 Web3 基礎設施與 DeFi 交易的研究機構，聚焦解決真實交易場景中的關鍵問題，並將[研究成果](https://blockrazor.io/zh/blog/)持續沉澱為可落地的基礎設施產品與服務，為追求卓越的構建者打造分佈全球的多鏈高性能基礎設施體系。

通過長期研究和工程實踐，BlockRazor 面向 Wallets、DEXs、Trading Bots、Searchers 和量化交易系統，提供覆蓋 Transaction Submission、Streams 和 Block Builder 的一體化能力，幫助客戶在 Ethereum、BSC、Solana 和 Base 等主流公鏈上獲得更快的區塊交易信號和更優的交易執行結果。

### Transaction Submission

BlockRazor 提供多種交易發送模式，以適配不同業務在交易保護、上鏈速度、交易成本方面的需求：

* [RPC](transaction-submission/rpc/)：提供標準JSON-RPC方法，為交易提供MEV保護，支持實時返利
* [Block Builder](transaction-submission/block-builder/): 面向 BSC 的區塊構建服務，為用戶提供高勝率區塊構建承諾與低延遲接入能力
* [Transaction Sending](transaction-submission/transaction-sending/)：利用 [BEF](he-xin-ji-shu/blockchain-edge-fabric.md) 提升交易上鏈速度，適合對交易速度有極致要求的用戶
* [Gas Sponsor](transaction-submission/gas-sponsor.md)：為原生代幣不足以支付交易費的用戶提供gas贊助，提升用戶交易體驗

### Streams

BlockRazor 提供多種高性能實時數據流能力，幫助交易系統、策略系統和基礎設施系統更早獲取鏈上信號並更快同步狀態：

* [Mempool](streams/mempool/bsc/public-mempool.md)：低延遲推送Mempool pending交易，第一時間監聽Backrun、跟單、狙擊等多場景下的信號交易
* [Block Stream](streams/block-stream/solana/shred-stream.md)：低延遲推送最新區塊與確認後交易，用於監控確認信號、區塊事件和鏈上結果
* [Node Stream](streams/node-stream/bsc/full-node-synchronization.md)：幫助用戶自己的全節點以更低延遲同步最新區塊和 world state，適合作為依賴本地狀態運行的系統底層輸入能力
* [Network Fee Stream](streams/network-fee-stream/solana/get-transactionfee.md)：基於最近歷史區塊數據實時提供 Gas Price、Priority Fee 或 Tip 數據，用於優化交易參數和發送策略。

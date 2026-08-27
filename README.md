---
description: >-
  面向訂單流、交易機器人、Searcher 與量化交易，覆蓋 BSC、Solana、Ethereum、Base 和 Robinhood Chain
  的全球低延遲基礎設施。
metaLinks:
  canonical: ./
  alternates:
    - https://app.gitbook.com/s/jbyfG8gOgcdsK3wVxNdQ/get-started/about-us
---

# 總覽

BlockRazor 是一家專注於 Web3 基礎設施與 DeFi 交易的研究機構，聚焦解決真實交易場景中的關鍵問題，並將研究成果持續沉澱為可落地的基礎設施產品與服務，為追求卓越的構建者打造分佈全球的多鏈高性能基礎設施體系。

通過長期研究和工程實踐，BlockRazor 面向 Wallets、DEXs、Trading Bots、Searchers 和量化交易系統，提供覆蓋 Transaction Submission 和 Streams的一體化能力，幫助客戶在 Solana、Ethereum、BSC、 Robinhood Chain 和 Base 等主流公鏈上獲得更快的區塊交易信號和更優的交易執行結果。

### 快速開始

<mark style="color:$info;">服務</mark>

<table data-card-wrap="false" data-view="cards"><thead><tr><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><strong>獲取Auth Token</strong></td><td><a href="https://blockrazor.io/#/register?redirect=onboarding">https://blockrazor.io/#/register?redirect=onboarding</a></td></tr><tr><td><strong>免費開始</strong></td><td><a href="get-started/start-for-free.md">start-for-free.md</a></td></tr><tr><td><strong>價格</strong></td><td><a href="get-started/subscription-service.md">subscription-service.md</a></td></tr></tbody></table>

<mark style="color:$info;">用戶案例</mark>

<table data-card-wrap="false" data-view="cards"><thead><tr><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><strong>Trading Bot</strong></td><td><a href="get-started/use-cases/trading-bot.md">trading-bot.md</a></td></tr><tr><td><strong>Searcher</strong></td><td><a href="get-started/use-cases/searcher.md">searcher.md</a></td></tr><tr><td><strong>量化交易</strong></td><td><a href="get-started/use-cases/algorithmic-trading.md">algorithmic-trading.md</a></td></tr><tr><td><strong>交易流項目</strong></td><td><a href="get-started/use-cases/wallet-dex.md">wallet-dex.md</a></td></tr><tr><td><strong>個人交易者</strong></td><td><a href="get-started/use-cases/individual-trader.md">individual-trader.md</a></td></tr></tbody></table>

<mark style="color:$info;">鏈</mark>

<table data-card-wrap="false" data-view="cards"><thead><tr><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><strong>BSC</strong></td><td><a href="get-started/supported-chains.md#bsc">#bsc</a></td></tr><tr><td><strong>Robinhood</strong></td><td><a href="get-started/supported-chains.md#robinhood">#robinhood</a></td></tr><tr><td><strong>Solana</strong></td><td><a href="get-started/supported-chains.md#solana">#solana</a></td></tr><tr><td><strong>Ethereum</strong></td><td><a href="get-started/supported-chains.md#ethereum">#ethereum</a></td></tr><tr><td><strong>Base</strong></td><td><a href="get-started/supported-chains.md#base">#base</a></td></tr></tbody></table>

### Streams

<mark style="color:$info;">BlockRazor 提供多種高性能實時數據流能力，幫助交易系統、策略系統和基礎設施系統更早獲取鏈上信號並更快同步狀態</mark>

<table data-card-wrap="false" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><strong>Public Mempool</strong></td><td><mark style="color:$info;">更早掌握 BSC 待處理交易，在聰明錢追蹤、狙擊、跟單與 Backrun 中取得決定性優勢</mark></td><td><a href="streams/public-mempool/bsc/public-mempool.md">public-mempool.md</a></td></tr><tr><td><strong>Private Mempool</strong></td><td><mark style="color:$info;">取得獨家私有訂單流，作為更快狙擊、跟單與 Backrun 機會的早期 Alpha 訊號</mark></td><td><a href="streams/public-mempool/ethereum/private-mempool.md">private-mempool.md</a></td></tr><tr><td><strong>Block Stream</strong></td><td><mark style="color:$info;">低延遲接收最新區塊與已確認交易，讓策略立即回應</mark></td><td><a href="streams/block-stream/">block-stream</a></td></tr><tr><td><strong>Node Stream</strong></td><td><mark style="color:$info;">加速鏈節點世界狀態同步，讓依賴狀態的策略領先競爭對手</mark></td><td><a href="streams/node-stream/">node-stream</a></td></tr><tr><td><strong>Network Fee Stream</strong></td><td><mark style="color:$info;">即時追蹤 Gas Price 和 Tip，兼顧每筆交易的速度、成本與上鏈機率</mark></td><td><a href="streams/network-fee-stream/">network-fee-stream</a></td></tr></tbody></table>

### Transaction Submission

<mark style="color:$info;">BlockRazor 提供多種交易發送模式，以適配不同業務在交易保護、上鏈速度、交易成本方面的需求</mark>

<table data-card-wrap="false" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-cover data-type="image">Cover image</th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><strong>RPC</strong></td><td><mark style="color:$info;">提供標準JSON-RPC方法，為交易提供MEV保護、低延遲上鏈和實時返利能力</mark></td><td></td><td><a href="transaction-submission/rpc/">rpc</a></td></tr><tr><td><strong>Block Builder</strong></td><td><mark style="color:$info;">面向 BSC 的區塊構建服務，為用戶提供高勝率區塊構建承諾與低延遲接入能力</mark></td><td></td><td><a href="transaction-submission/block-builder/">block-builder</a></td></tr><tr><td><strong>Transaction Sending</strong></td><td><mark style="color:$info;">利用</mark> <a href="he-xin-ji-shu/blockchain-edge-fabric.md"><mark style="color:$info;">BEF</mark></a> <mark style="color:$info;">提升交易上鏈速度，適合對交易速度有極致要求的用戶</mark></td><td></td><td><a href="transaction-submission/transaction-sending/">transaction-sending</a></td></tr><tr><td><strong>Gas Sponsor</strong></td><td><mark style="color:$info;">為原生代幣不足以支付交易費的用戶提供gas贊助，提升用戶交易體驗</mark></td><td></td><td><a href="transaction-submission/gas-sponsor.md">gas-sponsor.md</a></td></tr></tbody></table>

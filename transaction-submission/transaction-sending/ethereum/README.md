---
description: 介紹BlockRazor Ethereum Transaction Sending模式以及API接入文檔
---

# Ethereum Transaction Sending

### 什麼是 Broadcast Tx

Broadcast Tx 是 BlockRazor 提供的一種快速交易發送服務，用於幫助用戶以更低延遲將交易發送到鏈上。它屬於 Fast 體系的一部分，但與需要在交易中附加 tip 的標準 Fast 模式不同，Broadcast Tx 不要求用戶在交易內部額外支付 tip，因此更適合作為低門檻的快速發送入口使用。

目前Ethereum Broadcast Tx提供 `SendTx` 方法，用来發送单笔交易。

需要注意的是，Broadcast Tx 雖然屬於 Fast 體系，但它並不等同於具備完整交易保護能力的私有發送通道。Broadcast Tx 發送的交易會進入公開傳播路徑，因此不具備 MEV 防護能力。

### 什麼場景下選擇 Broadcast Tx

* **不需要 tip，接入門檻更低**\
  與標準 Fast 模式不同，Broadcast Tx 不要求在交易中附加 tip，因此更適合希望快速接入、但不想修改交易激勵結構的用戶。
* **適合對速度有要求、但暫不強調 MEV 防護的場景**\
  如果你的重點是盡量更快地把交易發出去，而不是通過私有路徑隱藏交易或抵御 sandwich、frontrunning 等風險，那麼 Broadcast Tx 會是一個更直接的選擇。

### 價格

<table><thead><tr><th width="153.44921875">用戶類型</th><th>限流</th><th>價格</th></tr></thead><tbody><tr><td>新註冊用戶</td><td><p><code>SendTx</code></p><ul><li>TPS：10 Txs / 5s</li><li>每日交易上限：10</li></ul></td><td>免費</td></tr><tr><td>付費用戶</td><td><p><code>SendTx</code></p><ul><li>TPS：100 Txs / 5s</li><li>每日交易上限：100000</li></ul></td><td>$50 / 日<br>$500 / 月<br><br><a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=ethereum&#x26;serviceId=ethereum_fast_tx&#x26;billing=day" class="button primary small">訂閱</a></td></tr></tbody></table>

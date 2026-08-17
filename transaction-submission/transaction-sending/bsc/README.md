---
description: 介紹BlockRazor BSC Transaction Sending模式以及API接入文檔
---

# BSC Transaction Sending

### 什麼是 Broadcast Tx

Broadcast Tx 是 BlockRazor 提供的一種快速交易發送服務，用於幫助用戶以更低延遲將交易發送到鏈上。它屬於 Fast 體系的一部分，但與需要在交易中附加 tip 的標準 Fast 模式不同，Broadcast Tx 不要求用戶在交易內部額外支付 tip，因此更適合作為低門檻的快速發送入口使用。

目前Broadcast Tx提供两种方法：`SendTx` 和 `SendTxs`，分别用来发单笔交易和批量交易。

需要注意的是，Broadcast Tx 雖然屬於 Fast 體系，但它並不等同於具備完整交易保護能力的私有發送通道。Broadcast Tx 發送的交易會進入公開傳播路徑，因此不具備 MEV 防護能力。

### 什麼場景下選擇 Broadcast Tx

* **不需要 tip，接入門檻更低**\
  與標準 Fast 模式不同，Broadcast Tx 不要求在交易中附加 tip，因此更適合希望快速接入、但不想修改交易激勵結構的用戶。
* **適合對速度有要求、但暫不強調 MEV 防護的場景**\
  如果你的重點是盡量更快地把交易發出去，而不是通過私有路徑隱藏交易或抵御 sandwich、frontrunning 等風險，那麼 Broadcast Tx 會是一個更直接的選擇。

### Benchmark

在發交易上鏈延遲的測試中，我們分別在 Dublin、Frankfurt、Tokyo 和 Virginia 四個區域，對 BlockRazor 與普通 Node 進行多輪對比。評估標準分為兩層：首先比較交易是否更早進入鏈上區塊；如果雙方均在同一區塊內完成上鏈，則進一步比較 transaction index 的先後順序。結果如下：

<table><thead><tr><th width="187.37109375">Region</th><th width="511.00390625">BlockRazor Total Lead Rate</th></tr></thead><tbody><tr><td>Dublin</td><td><strong>88.7%</strong><br><strong>-</strong> Same block but better index: 82.9%<br>- Earlier block: 5.8%</td></tr><tr><td>Frankfurt</td><td><strong>85.4%</strong><br><strong>-</strong> Same block but better index: 81.3%<br>- Earlier block: 4.2%</td></tr><tr><td>Tokyo</td><td><strong>85.1%</strong><br><strong>-</strong> Same block but better index: 78.7%<br>- Earlier block: 6.4%</td></tr><tr><td>Virginia</td><td><strong>97.9%</strong><br><strong>-</strong> Same block but better index: 95.7%<br>- Earlier block: 2.1%</td></tr></tbody></table>

從比例結果來看，BlockRazor 在各區域整體保持了更高的領先率。Dublin 區域中，BlockRazor 領先率為 88.7%，Frankfurt 區域領先 85.4%，Tokyo 區域領先 85.1%，Virginia 區域領先率達到 97.9%。

進一步看領先方式，BlockRazor 的優勢主要體現為兩點：一是在大多數領先輪次中，即使與普通 Node 同時進入同一區塊，BlockRazor 仍能獲得更靠前的 transaction index；二是在部分輪次中，BlockRazor 還能直接領先 1 個區塊完成上鏈。這說明 BlockRazor 不僅更容易進入更早的鏈上確認窗口，也更有機會在同區塊競爭中取得更優排序位置，從而形成穩定的提交優勢。

### 價格

<table><thead><tr><th width="153.44921875">用戶類型</th><th>限流</th><th>價格</th></tr></thead><tbody><tr><td>新註冊用戶</td><td><p><code>SendTx</code></p><ul><li>TPS：10 Txs / 5s</li><li>每日交易上限：10</li></ul></td><td>免費</td></tr><tr><td>付費用戶</td><td><p><code>SendTx</code></p><ul><li>TPS：100 Txs / 5s</li><li>每日交易上限：100000<br></li></ul><p><code>SendTxs</code></p><ul><li>BPS：4 batches / 1s</li><li>Txs per Batch：10</li></ul></td><td>$50 / 日<br>$500 / 月<br><br><a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=bsc&#x26;serviceId=bsc_fast_tx&#x26;billing=day" class="button primary small">訂閱</a></td></tr></tbody></table>

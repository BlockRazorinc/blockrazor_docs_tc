---
description: 介紹Trading Bot在信號監聽和交易發送場景下的痛點，以及如何使用Blockrazor的服務來擴展、加速信號監聽和交易發送
---

# Trading Bot

在鏈上交易中，Trading Bot 的競爭力不僅取決於策略本身，也取決於兩個關鍵速度：

1. **信號監聽速度**：能否更早發現新池、開盤事件或目標錢包交易。
2. **交易提交速度**：能否更快將交易送達 Leader、Builder、Validator 或 Sequencer。

完整交易鏈路如下：

> 鏈上事件發生 → 監聽與解析信號 → 策略判斷 → 構建並簽名交易 → 提交交易 → 排序節點接收 → 交易上鏈

信號監聽決定 Bot 何時開始行動，交易提交則決定交易何時到達鏈上。任何一端的延遲，都可能使策略失去原有優勢。

### Trading Bot 的典型場景

#### Sniper

Sniper Bot 監聽新代幣部署、新流動性池創建、開盤交易或其他預設事件，並在信號出現後立即構建及提交交易。

其主要目標是更早發現交易機會，爭取進入目標區塊或 Slot，並獲得更有利的排序位置。

#### Copy Trading

Copy Trading Bot 監聽指定 Leader 錢包的買入、賣出或持倉變化，再按照預設金額、比例及策略規則構建 Follower 交易。

其主要目標是縮短 Leader 與 Follower 的上鏈時間差，減少兩者之間的成交價格偏差。

Sniper 與 Copy Trading 的監聽對象不同：前者關注新池、開盤和合約事件，後者關注指定錢包的交易行為。但兩者都依賴相同的底層鏈路：獲取信號、解析信號、構建交易及快速提交。

### Trading Bot 痛点分析

#### 痛點一：信號監聽速度不足

**等待完整區塊造成信號滯後**

普通 RPC 或 WebSocket 通常需要等待節點接收、處理甚至重建區塊後，才向 Bot 推送交易數據。

這可能導致：

* Sniper 發現新池時，競爭者已經提交交易；
* Copy Trading 發現 Leader 交易時，價格已經改變；
* 原本有效的交易機會在等待期間衰減。

**公共節點的數據鏈路較長**

公共節點可能存在跨區域傳輸、多層代理、共享資源排隊、高峰期限流以及連接抖動等問題。

即使只有數十毫秒的差距，在高頻 Sniper 和 Copy Trading 場景中，也可能直接影響最終成交位置。

**單一數據源的信號覆蓋有限**

在支持 Pending Transactions 的鏈上，公開與私有交易可能通過不同路徑傳播：

* Public Mempool 主要覆蓋公開廣播的 Pending Transactions；
* 私有交易不一定會出現在公共 Mempool；
* 已上鏈信號具有較高確定性，但到達時間也更晚。

Trading Bot 需要根據目標鏈及策略，選擇合適的 Pending、私有訂單流或區塊數據。

#### 痛點二：交易提交速度不足

較早獲取信號不代表一定能較早成交。Bot 完成策略判斷後，交易仍可能經過公共 RPC、代理層、跨區域網絡及中間轉發節點。

這些環節會持續消耗信號端取得的時間優勢，導致：

* Sniper 錯過目標區塊或 Slot；
* Copy Trading 與 Leader 相隔更多區塊；
* Follower 的成交價格明顯劣於 Leader；
* 交易因市場狀態改變而失敗。

不同鏈需要盡快到達的交易接收方也不相同：

<table><thead><tr><th width="188.7265625">鏈</th><th width="232.44140625">交易接收目標</th><th>速度競爭重點</th></tr></thead><tbody><tr><td>Solana</td><td>當前及後續 Leader</td><td>Leader 路由與 SWQoS 傳輸</td></tr><tr><td>Ethereum</td><td>Builder／Validator</td><td>更快進入傳播與打包鏈路</td></tr><tr><td>BSC</td><td>Builder／Validator</td><td>及時進入目標區塊處理窗口</td></tr><tr><td>Base</td><td>Sequencer</td><td>縮短交易到 Sequencer 的路徑</td></tr><tr><td>Robinhood Chain</td><td>官方 Sequencer</td><td>FCFS 排序下的到達時間</td></tr></tbody></table>

注意：Robinhood Chain 採用 First-Come, First-Served 排序，交易順序取決於抵達 Sequencer 的時間，無法僅靠提高交易費越過較早到達的交易。因此，提交路徑本身就是重要的競爭變量。

## 推薦服務

BlockRazor 從信號監聽和交易提交兩端，為 Sniper 與 Copy Trading Bot 提供低延遲基礎設施。

### 信號監聽服務

<table><thead><tr><th width="172.125">鏈</th><th>推薦服務</th><th>服務區別與選擇建議</th></tr></thead><tbody><tr><td>Solana</td><td><a href="../../streams/block-stream/solana/shred-stream.md"><strong>Shred Stream</strong></a><br><a href="../../streams/block-stream/solana/geyser-stream/"><strong>Geyser Stream</strong></a></td><td>Shred Stream 在完整區塊重建前傳輸 Shred；Geyser Stream 提供結構化交易與賬戶數據。</td></tr><tr><td>Ethereum</td><td><a href="../../streams/mempool/ethereum/public-mempool.md"><strong>Public Mempool</strong></a><br><a href="../../streams/block-stream/ethereum/newblocks.md"><strong>Block Stream</strong></a></td><td>Public Mempool 監聽公開 Pending Transactions；Block Stream 用於監聽已上鏈的確定性信號。</td></tr><tr><td>BSC</td><td><a href="../../streams/mempool/bsc/public-mempool.md"><strong>Public Mempool</strong></a><br><a href="../../streams/mempool/bsc/private-mempool.md"><strong>Private Mempool</strong></a><br><a href="../../streams/block-stream/bsc/newblocks.md"><strong>Block Stream</strong></a></td><td>Public／Private Mempool 用於提前監聽 Pending 信號，兩者互為補充；Block Stream 用於監聽已上鏈的確定性信號。</td></tr><tr><td>Base</td><td><a href="../../streams/block-stream/base/get-flashblockstream/"><strong>FlashBlock Stream</strong></a><br><a href="../../streams/block-stream/base/get-blockstream.md"><strong>Block Stream</strong></a></td><td>FlashBlock Stream 提供完整區塊形成前的預確認數據；Block Stream 提供完整區塊數據。</td></tr><tr><td>Robinhood Chain</td><td><a href="../../streams/node-stream/robinhood-chain/sequencer-feed.md"><strong>Sequencer Feed</strong></a></td><td>通過就近接入與傳輸路徑優化，更快、更穩定地接收 Sequencer 推送的區塊數據。</td></tr></tbody></table>

> Sniper 與 Copy Trading 可以使用相同的監聽服務，差異在於 Bot 所過濾和解析的信號對象。

### 交易提交服務

<table><thead><tr><th width="177.00390625">鏈</th><th width="245.10546875">推薦服務</th><th>服務區別與選擇建議</th></tr></thead><tbody><tr><td>Solana</td><td><a href="../../transaction-submission/fast/solana/send-transaction/"><strong>Send Transaction</strong></a></td><td>通過全球高性能網絡及 SWQoS 鏈路，將交易快速發送至當前及後續 Leader。</td></tr><tr><td>Ethereum</td><td><a href="../../transaction-submission/fast/ethereum/broadcast-tx.md"><strong>Broadcast Tx</strong></a></td><td>追求極致廣播速度，但不提供 MEV 保護；如需 MEV 保護，建議使用 BlockRazor RPC。</td></tr><tr><td>BSC</td><td><p><a href="../../transaction-submission/fast/bsc/broadcast-tx.md"><strong>Broadcast Tx</strong></a></p><p><a href="../../transaction-submission/block-builder/fast-submit.md"><strong>Fast Submit</strong></a></p></td><td>Broadcast Tx 用於極速廣播交易，但不提供 MEV 保護；如需 MEV 保護，建議使用 BlockRazor RPC。Fast Submit 通過專用提交入口和優化的跨區域路徑，縮短交易到 BlockRazor Builder 的提交鏈路。</td></tr><tr><td>Base</td><td><a href="../../transaction-submission/rpc/base/eth_sendrawtransaction.md"><strong>eth_sendRawTransaction</strong></a></td><td>提供標準化、多區域的交易提交入口，將已簽名交易快速發送至 Base Sequencer。</td></tr><tr><td>Robinhood Chain</td><td><a href="../../transaction-submission/rpc/robinhood-chain/eth_sendrawtransaction/"><strong>eth_sendRawTransaction</strong></a></td><td>通過多區域入口與優化路徑，更快抵達採用 FCFS 排序的官方 Sequencer。</td></tr></tbody></table>

> 信號監聽服務決定 Bot 何時開始行動，交易提交服務決定交易何時抵達排序節點。建議優先選擇靠近 Bot 部署位置的區域端點。

## Benchmark

以下為 BlockRazor 已公開的相關性能測試：

<table data-search="false"><thead><tr><th width="116.55859375">鏈</th><th width="169.1484375">服務</th><th>Benchmark</th><th>測試內容</th></tr></thead><tbody><tr><td>Solana</td><td>Shred Stream</td><td><a href="https://blockrazor.io/blog/20250818shredbenchmark/">Solana Shred Stream Benchmark</a></td><td>對比 BlockRazor 與 Jito 在多個區域的 Shred 首先到達比例及時間差。</td></tr><tr><td>Solana</td><td>Send Transaction</td><td><a href="https://blockrazor.io/blog/20250801Benchmarking/">Benchmarking Solana Send Transaction Service</a></td><td>使用一致的 Tip、Priority Fee 和 Durable Nonce，比較不同服務在 SWQoS 路徑中的交易競速結果。</td></tr><tr><td>BSC</td><td>Fast Submit</td><td><a href="https://blockrazor.io/blog/20260625BSC-Builder-Fast-Submit/">BSC Fast Submit Benchmark</a></td><td>驗證縮短代理、網絡跳數及跨區域路徑帶來的提交延遲改善。</td></tr><tr><td>Base</td><td>RPC<br>Block Stream<br>FlashBlock Stream</td><td><a href="https://blockrazor.io/blog/20250922basebenchmark/">Base Benchmark</a></td><td>比較交易提交位置，以及 Block Stream、FlashBlock Stream 的數據到達延遲。</td></tr><tr><td>Robinhood Chain</td><td>Sequencer Feed</td><td><a href="https://docs.blockrazor.io/streams/node-stream/robinhood-chain/sequencer-feed">Sequencer Feed Benchmark</a></td><td>比較 BlockRazor 與官方 Feed 在 AWS Ohio 三個可用區的首先到達率及延遲分佈。</td></tr><tr><td>Robinhood Chain</td><td>Robinhood Chain RPC</td><td><a href="https://www.blockrazor.io/blog/RobinhoodChainRPC/">Robinhood Chain RPC Benchmark</a></td><td>使用相同 Nonce 交易，比較 BlockRazor RPC 與官方 RPC 在不同區域的競爭收錄率。</td></tr></tbody></table>

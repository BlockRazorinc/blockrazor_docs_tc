---
description: 介紹BlockRazor為Robinhood Chain提供的Node Stream服務，主要為Sequencer Feed
metaLinks:
  canonical: ./
  alternates:
    - >-
      https://app.gitbook.com/s/jbyfG8gOgcdsK3wVxNdQ/streams/node-stream/robinhood-chain
---

# Robinhood Chain Sequencer Feed

### Sequencer Feed是什麼

Sequencer Feed 是由 Robinhood Chain Sequencer 實時推送的節點數據流。節點通過 WebSocket 訂閱 Feed，可以快速接收 Sequencer 發布的交易和區塊相關數據，及時同步鏈上最新狀態。

對節點運行者比如Searcher而言，Sequencer Feed 的傳輸速度和穩定性會直接影響節點追塊和狀態更新速度。當官方 Feed 端點出現網絡延遲、網絡抖動或連接不穩定等問題時，節點可能無法及時接收最新數據，造成節點高度落後和數據更新延遲。

### Why BlockRazor Sequencer Feed

BlockRazor Sequencer Feed 為 Robinhood Chain 節點提供更加穩定、高效的 Sequencer Feed 接入服務。

相比直接連接官方 Feed 端點，BlockRazor Sequencer Feed 通過就近接入和傳輸路徑優化，降低Feed同步過程中因網絡抖動和連接中斷帶來的延遲影響。

此外 ，BlockRazor Sequencer Feed兼容官方標準接入方式。節點只需替換 Feed URL，即可更快、更穩定地接收最新數據，減少追塊延遲，保持鏈上狀態實時同步。

### 常見問題

<details>

<summary><strong>Node-required Sequencer Feed 和 Direct Sequencer Feed 有什麼區別</strong></summary>

<table><thead><tr><th width="108.69921875">對比項</th><th width="261.37109375">Node-required Sequencer Feed</th><th>Direct Sequencer Feed</th></tr></thead><tbody><tr><td>接入方式</td><td>需要通過節點接收</td><td>普通客戶端可直接接入，無需運行節點</td></tr><tr><td>區塊傳輸</td><td>按区块高度顺序传输，不跳块</td><td>優先提供最新區塊，網絡擁塞時允許跳過中間區塊</td></tr><tr><td>節點狀態依賴</td><td>依赖节点低延遲同步状态</td><td>不依赖本地节点保持完整、连续的状态同步</td></tr><tr><td>部署成本</td><td>需要部署、維護和監控節點</td><td>接入簡單，運維成本較低</td></tr><tr><td>適合場景</td><td>套利、訂單流項目、量化交易</td><td>狙擊、跟單</td></tr></tbody></table>

</details>


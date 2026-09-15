---
description: 介紹Robinhood Chain Sequencer Feed(Ultra)的定義、benchmark、價格和接入方法
---

# Robinhood Chain Node-required Sequencer Feed(Ultra)

### Node-required Sequencer Feed(Ultra)是什麼

Node-required Sequencer Feed（Ultra）是在[標準版](sequencer-feed.md)基礎上推出的超低延遲數據傳輸方案。該方案對網絡傳輸路徑及底層傳輸機制進行了深度優化，進一步降低 Sequencer Feed 從數據源到本地節點的端到端傳輸延遲。\
​\
Ultra 版本底層搭載 BEF 技術，能夠以最低延遲向用戶的本地節點持續交付已排序的區塊數據，縮短數據在網絡傳輸、接收及節點處理鏈路中的等待時間，幫助交易系統更早獲取關鍵的鏈上狀態。\
​\
該方案專為對延遲極為敏感的專業場景打造，包括頂級套利、訂單流分析和量化交易等。在以微秒計的競爭環境中，更快獲取順序區塊數據，意味著擁有更充足的策略計算和交易執行窗口。微秒之間，划定領先者的邊界。

### Benchmark

我們使用同一個測試客戶端，分別與 Robinhood Chain Sequencer Feed 和 BlockRazor Sequencer Feed 建立 WSS 連接。官方端点为wss://[feed.mainnet.chain.robinhood.com](http://feed.mainnet.chain.robinhood.com)，BlockRazor 使用 `/ws/ultra` 端點。

測試客戶端分別部署在 AWS 美國東部（俄亥俄）區域的三個可用區（`use2-az1`、`use2-az2` 和 `use2-az3`），用於比較兩個 Sequencer Feed 接收區塊時的相對延遲。對於每個區塊，最先接收到該區塊的 Sequencer Feed，其相對延遲記為 `0 ms`；另一個 Sequencer Feed 的相對延遲，則根據兩者接收到該區塊的時間戳差值計算。

你可以使用 [robinhood-feed-speed 基準測試工具](https://github.com/BlockRazorinc/robinhood-feed-speed) 復現本次測試。

具體Benchmark數據如下：

{% tabs %}
{% tab title="use2-az1" %}
樣本總數：`4,232`

| Sequencer Feed                 |          P50 |          P90 |          P95 |          P99 |          最大值 |
| ------------------------------ | -----------: | -----------: | -----------: | -----------: | -----------: |
| **BlockRazor Sequencer Feed**  | **0.000 ms** | **0.000 ms** | **0.000 ms** | **0.000 ms** | **0.000 ms** |
| Robinhood Chain Sequencer Feed |    28.026 ms |    45.177 ms |    52.780 ms |    85.805 ms |   818.664 ms |
{% endtab %}

{% tab title="use2-az2" %}
樣本總數：`4,305`

| Sequencer Feed                 |   樣本數 |          P50 |          P90 |          P95 |          P99 |          最大值 |
| ------------------------------ | ----: | -----------: | -----------: | -----------: | -----------: | -----------: |
| **BlockRazor Sequencer Feed**  | 4,305 | **0.000 ms** | **0.000 ms** | **0.000 ms** | **0.000 ms** | **0.000 ms** |
| Robinhood Chain Sequencer Feed | 4,305 |    97.404 ms |   195.406 ms |   301.405 ms |   953.111 ms | 1,616.810 ms |
{% endtab %}

{% tab title="use2-az3" %}
樣本總數：`4,714`

| Sequencer Feed                 |   樣本數 |          P50 |          P90 |          P95 |          P99 |          最大值 |
| ------------------------------ | ----: | -----------: | -----------: | -----------: | -----------: | -----------: |
| **BlockRazor Sequencer Feed**  | 4,714 | **0.000 ms** | **0.000 ms** | **0.000 ms** | **0.000 ms** | **9.580 ms** |
| Robinhood Chain Sequencer Feed | 4,714 |    27.310 ms |    52.828 ms |    66.711 ms |   111.686 ms |   736.300 ms |
{% endtab %}

{% tab title="Tokyo" %}
樣本總數：`3,996`

| Sequencer Feed                 |          P50 |          P90 |          P95 |          P99 |           最大值 |
| ------------------------------ | -----------: | -----------: | -----------: | -----------: | ------------: |
| **BlockRazor Sequencer Feed**  | **0.000 ms** | **0.000 ms** | **0.000 ms** | **0.000 ms** | **68.232 ms** |
| Robinhood Chain Sequencer Feed |    71.365 ms |    108.596ms |    116.049ms |    220.859ms |    1969.576ms |


{% endtab %}
{% endtabs %}

在三個可用區中，BlockRazor Sequencer Feed 的相對延遲從 P50 到 P99 均保持為 `0 ms`。相比之下，Robinhood Chain Sequencer Feed 的 P50 相對延遲介於 `27.310 ms` 至 `97.404 ms` 之間。

其中，`use2-az2` 的延遲差距最為明顯。Robinhood Chain Sequencer Feed 的 P50 相對延遲為 `97.404 ms`，P99 達到 `953.111 ms`，最大相對延遲為 `1,616.810 ms`。

綜合測試結果來看，BlockRazor Sequencer Feed 在三個被測可用區中均能更早、更穩定地完成區塊交付，並具有顯著更低的相對延遲，可為延遲敏感型應用和交易提供更快、更穩定的區塊優先交付窗口。

### 常見問題

<details>

<summary><strong>Node-required Sequencer Feed 和 Direct Sequencer Feed 有什麼區別</strong></summary>

<table><thead><tr><th width="108.69921875">對比項</th><th width="261.37109375">Node-required Sequencer Feed</th><th>Direct Sequencer Feed</th></tr></thead><tbody><tr><td>接入方式</td><td>需要通過節點接收</td><td>普通客戶端可直接接入，無需運行節點</td></tr><tr><td>區塊傳輸</td><td>按区块高度顺序传输，不跳块</td><td>優先提供最新區塊，網絡擁塞時允許跳過中間區塊</td></tr><tr><td>節點狀態依賴</td><td>依赖节点低延遲同步状态</td><td>不依赖本地节点保持完整、连续的状态同步</td></tr><tr><td>部署成本</td><td>需要部署、維護和監控節點</td><td>接入簡單，運維成本較低</td></tr><tr><td>適合場景</td><td>套利、訂單流項目、量化交易</td><td>狙擊、跟單</td></tr></tbody></table>

</details>

### 價格

價格為$200 / stream / 日和$2000 / stream / 月。 <a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=robinhood&#x26;serviceId=robinhood_feed_stream_speedup&#x26;billing=day" class="button primary small">訂閱</a>

### 端點

{% tabs %}
{% tab title="ws" %}
<table><thead><tr><th width="152.515625">地区</th><th>端点</th></tr></thead><tbody><tr><td>俄亥俄</td><td>ws://us.robinhood-feeder.blockrazor.io/ws/{authToken}</td></tr></tbody></table>
{% endtab %}

{% tab title="wss" %}
<table><thead><tr><th width="152.515625">地区</th><th>端点</th></tr></thead><tbody><tr><td>俄亥俄</td><td>wss://us.robinhood-feeder.blockrazor.io/ws/{authToken}</td></tr><tr><td>東京</td><td>wss://jp.robinhood-feeder.blockrazor.io/ws/{authToken}</td></tr></tbody></table>
{% endtab %}
{% endtabs %}

### 使用步驟

{% stepper %}
{% step %}
<a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=robinhood&#x26;serviceId=robinhood_feed_stream_speedup&#x26;billing=day" class="button primary small">訂閱</a> **BlockRazor Sequencer Feed**
{% endstep %}

{% step %}
**在portal獲取auth，將其作為URI拼接於ws url**

ws://us.robinhood-feeder.blockrazor.io/ws/ultra/{authToken}
{% endstep %}

{% step %}
**停止正在運行的 Robinhood Chain 節點**

具體命令取決於當前使用的部署方式，例如 Docker、Docker Compose 或 systemd。停止前建議確認節點數據目錄已正確掛載，避免重新啟動後丟失已有同步數據。
{% endstep %}

{% step %}
**替換Feed URL**

在節點啟動命令中找到以下配置：

```bash
--node.feed.input.url=wss://feed.mainnet.chain.robinhood.com
```

添加 BlockRazor Sequencer Feed：

```bash
--node.feed.input.url=wss://feed.mainnet.chain.robinhood.com
--node.feed.input.url=ws://us.robinhood-feeder.blockrazor.io/ws/{authToken}
```

完整的主網啟動示例如下：

```bash
DATA_DIR="$HOME/rh/robinhood-nitro-data"

docker run --rm -it \
  -v "$DATA_DIR":/home/nitro/.arbitrum \
  -v "$HOME/rh/config":/home/nitro/config \
  -p 8547:8547 \
  -p 8548:8548 \
  offchainlabs/nitro-node:v3.11.2-3599aca \
    --chain.info-files=/home/nitro/config/robinhood-chain-info.json \
    --parent-chain.connection.url=<L1_EXECUTION_RPC_URL> \
    --parent-chain.blob-client.beacon-url=<L1_BEACON_URL> \
    --init.genesis-json-file=/home/nitro/config/robinhood-genesis.json \
    --node.feed.input.url=ws://<BLOCKRAZOR_FEED_URL> \
    --http.addr=0.0.0.0 \
    --http.port=8547 \
    --http.api=net,web3,eth
```
{% endstep %}

{% step %}
**重新啟動節點**

保存配置後重新啟動節點，節點將通過 BlockRazor Sequencer Feed 接收 Robinhood Sequencer 數據。

查看節點日志，確認：

* BlockRazor Feed 連接成功
* 沒有持續重連、超時或 WebSocket 錯誤
* 節點持續接收最新 Sequencer 數據
* 節點高度正常跟進 Robinhood Chain
{% endstep %}

{% step %}
**驗證節點狀態**

檢查同步狀態：

```bash
curl -d '{"id":0,"jsonrpc":"2.0","method":"eth_syncing","params":[]}' \
  -H "Content-Type: application/json" \
  http://localhost:8547
```

完全同步後，`eth_syncing` 應返回：

```bash
false
```
{% endstep %}
{% endstepper %}

---
description: 瞭解 Robinhood Chain Sequencer Feed(Ultra) 服務、Benchmark數據、價格、端點和集成方法
---

# Robinhood Chain Node-required Sequencer Feed(Ultra) 接入指南

### Node-required Sequencer Feed(Ultra)是什麼

Node-required Sequencer Feed（Ultra）是在[標準版](sequencer-feed.md)基礎上推出的超低延遲數據傳輸方案。該方案對網絡傳輸路徑及底層傳輸機制進行了深度優化，進一步降低 Sequencer Feed 從數據源到本地節點的端到端傳輸延遲。\
​\
Ultra 版本底層搭載 BEF 技術，能夠以最低延遲向用戶的本地節點持續交付已排序的區塊數據，縮短數據在網絡傳輸、接收及節點處理鏈路中的等待時間，幫助交易系統更早獲取關鍵的鏈上狀態。\
​\
該方案專為對延遲極為敏感的專業場景打造，包括頂級套利、訂單流分析和量化交易等。在以微秒計的競爭環境中，更快獲取順序區塊數據，意味著擁有更充足的策略計算和交易執行窗口。微秒之間，划定領先者的邊界。

<details>

<summary><strong>Node-required Sequencer Feed 和 Direct Sequencer Feed 有什麼區別</strong></summary>

<table><thead><tr><th width="108.69921875">對比項</th><th width="261.37109375">Node-required Sequencer Feed</th><th>Direct Sequencer Feed</th></tr></thead><tbody><tr><td>接入方式</td><td>需要通過節點接收</td><td>普通客戶端可直接接入，無需運行節點</td></tr><tr><td>區塊傳輸</td><td>按区块高度顺序传输，不跳块</td><td>優先提供最新區塊，網絡擁塞時允許跳過中間區塊</td></tr><tr><td>節點狀態依賴</td><td>依赖节点低延遲同步状态</td><td>不依赖本地节点保持完整、连续的状态同步</td></tr><tr><td>部署成本</td><td>需要部署、維護和監控節點</td><td>接入簡單，運維成本較低</td></tr><tr><td>適合場景</td><td>套利、訂單流項目、量化交易</td><td>狙擊、跟單</td></tr></tbody></table>

</details>

### Benchmark

我們使用同一個測試客戶端，分別與 Robinhood Chain Sequencer Feed 和 BlockRazor Sequencer Feed 建立 WSS 連接。官方端点为wss://[feed.mainnet.chain.robinhood.com](http://feed.mainnet.chain.robinhood.com)，BlockRazor 使用 `/ws/ultra` 端點。

測試客戶端分別部署在 AWS 美國東部（俄亥俄）區域的三個可用區（`use2-az1`、`use2-az2` 和 `use2-az3`），用於比較兩個 Sequencer Feed 接收區塊時的相對延遲。對於每個區塊，最先接收到該區塊的 Sequencer Feed，其相對延遲記為 `0 ms`；另一個 Sequencer Feed 的相對延遲，則根據兩者接收到該區塊的時間戳差值計算。

你可以使用 [robinhood-feed-speed 基準測試工具](https://github.com/BlockRazorinc/robinhood-feed-speed) 復現本次測試。

具體Benchmark數據如下：

{% tabs %}
{% tab title="use2-az1" %}
Snapshot Time: 2026/10/08 02:22:30\
Total Blocks Tested: 2,570

| Sequencer Feed                     |          P50 |          P90 |          P95 |          P99 |      Maximum |
| ---------------------------------- | -----------: | -----------: | -----------: | -----------: | -----------: |
| **BlockRazor Node Required Ultra** | **0.000 ms** | **0.000 ms** | **0.000 ms** | **0.000 ms** | **0.000 ms** |
| Robinhood Chain Sequencer Feed     |    22.271 ms |    23.640 ms |    26.388 ms |    53.690 ms |   471.721 ms |

**BlockRazor Node Required Ultra Win Rate: 100.00%**
{% endtab %}

{% tab title="use2-az2" %}
Snapshot Time: 2026/10/08 02:24:06\
Total Blocks Tested: 250

| Sequencer Feed                     |          P50 |          P90 |          P95 |          P99 |      Maximum |
| ---------------------------------- | -----------: | -----------: | -----------: | -----------: | -----------: |
| **BlockRazor Node Required Ultra** | **0.000 ms** | **0.000 ms** | **0.000 ms** | **0.000 ms** | **0.000 ms** |
| Robinhood Chain Sequencer Feed     |     8.448 ms |     9.054 ms |     9.234 ms |   263.049 ms |   462.267 ms |

**BlockRazor Node Required Ultra Win Rate: 100.00%**
{% endtab %}

{% tab title="use2-az3" %}
Snapshot Time: 2026/10/08 02:22:36\
Total Blocks Tested: 2,583

| Sequencer Feed                     |          P50 |          P90 |          P95 |          P99 |      Maximum |
| ---------------------------------- | -----------: | -----------: | -----------: | -----------: | -----------: |
| **BlockRazor Node Required Ultra** | **0.000 ms** | **0.000 ms** | **0.000 ms** | **0.000 ms** | **0.000 ms** |
| Robinhood Chain Sequencer Feed     |    24.639 ms |    25.396 ms |    29.256 ms |    60.855 ms |   433.240 ms |

**BlockRazor Node Required Ultra Win Rate: 100.00%**
{% endtab %}

{% tab title="Tokyo" %}
Total Blocks Tested：3996

| Sequencer Feed                 |          P50 |          P90 |          P95 |          P99 |       Maximum |
| ------------------------------ | -----------: | -----------: | -----------: | -----------: | ------------: |
| **BlockRazor Sequencer Feed**  | **0.000 ms** | **0.000 ms** | **0.000 ms** | **0.000 ms** | **68.232 ms** |
| Robinhood Chain Sequencer Feed |    71.365 ms |    108.596ms |    116.049ms |    220.859ms |    1969.576ms |
{% endtab %}
{% endtabs %}

從延遲分布來看，BlockRazor 不僅在大多數區塊上率先到達，而且這種領先優勢具有較高的一致性；相比之下，Robinhood Chain Sequencer Feed 更經常處於落後位置，且延遲波動更為明顯。

綜合而言，BlockRazor Sequencer Feed 在區塊傳輸速度、首達率及延遲穩定性方面均展現出明顯優勢，能為延遲敏感型交易提供更可靠的先發窗口。

### 價格

價格為$200 / stream / 日和$2000 / stream / 月。 <a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=robinhood&#x26;serviceId=robinhood_feed_stream_speedup&#x26;billing=day" class="button primary small">訂閱</a>

### 端點

{% tabs %}
{% tab title="ws" %}
<table><thead><tr><th width="152.515625">地区</th><th>端点</th></tr></thead><tbody><tr><td>俄亥俄</td><td>ws://us.robinhood-feeder.blockrazor.io/ws/ultra/{authToken}</td></tr></tbody></table>
{% endtab %}

{% tab title="wss" %}
<table><thead><tr><th width="152.515625">地区</th><th>端点</th></tr></thead><tbody><tr><td>俄亥俄</td><td>wss://us.robinhood-feeder.blockrazor.io/ws/ultra/{authToken}</td></tr><tr><td>東京</td><td>wss://jp.robinhood-feeder.blockrazor.io/ws/ultra/{authToken}</td></tr></tbody></table>
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

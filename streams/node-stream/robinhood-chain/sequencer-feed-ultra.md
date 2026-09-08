---
description: 介紹Robinhood Chain Sequencer Feed(Ultra)的定義、benchmark、價格和接入方法
---

# Robinhood Chain Node-required Sequencer Feed(Ultra)

### Node-required Sequencer Feed(Ultra)是什麼

Node-required Sequencer Feed (Ultra) 在[標準版本](sequencer-feed.md)的基礎上深度優化網絡傳輸路徑與傳輸機制，以進一步降低 Sequencer Feed 的端到端傳輸延遲。

### 價格

價格為$200 / stream / 日和$2000 / stream / 月。 <a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=robinhood&#x26;serviceId=robinhood_feed_stream_speedup&#x26;billing=day" class="button primary small">訂閱</a>

### 端點

<table><thead><tr><th width="152.515625">地区</th><th>端点</th></tr></thead><tbody><tr><td>俄亥俄</td><td>wss://us.robinhood-feeder.blockrazor.io/ws/ultra/{authToken}</td></tr><tr><td>東京</td><td>wss://jp.robinhood-feeder.blockrazor.io/ws/ultra/{authToken}</td></tr></tbody></table>

### 使用步驟

{% stepper %}
{% step %}
<a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=robinhood&#x26;serviceId=robinhood_feed_stream_speedup&#x26;billing=day" class="button primary small">訂閱</a> **BlockRazor Sequencer Feed**
{% endstep %}

{% step %}
**在portal獲取auth，將其作為URI拼接於wss url**

wss://us.robinhood-feeder.blockrazor.io/ws/ultra/{authToken}
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
--node.feed.input.url=wss://us.robinhood-feeder.blockrazor.io/ws/{authToken}
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
    --node.feed.input.url=wss://<BLOCKRAZOR_FEED_URL> \
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

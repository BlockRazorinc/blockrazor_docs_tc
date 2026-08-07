# Sequencer Feed

### Sequencer Feed是什麼

Sequencer Feed 是由 Robinhood Chain Sequencer 實時推送的節點數據流。節點通過 WebSocket 訂閱 Feed，可以快速接收 Sequencer 發布的交易和區塊相關數據，及時同步鏈上最新狀態。

對節點運行者比如Searcher而言，Sequencer Feed 的傳輸速度和穩定性會直接影響節點追塊和狀態更新速度。當官方 Feed 端點出現網絡延遲、網絡抖動或連接不穩定等問題時，節點可能無法及時接收最新數據，造成節點高度落後和數據更新延遲。

### Why BlockRazor Sequencer Feed

BlockRazor Sequencer Feed 為 Robinhood Chain 節點提供更加穩定、高效的 Sequencer Feed 接入服務。

相比直接連接官方 Feed 端點，BlockRazor Sequencer Feed 通過就近接入和傳輸路徑優化，降低Feed同步過程中因網絡抖動和連接中斷帶來的延遲影響。

此外 ，BlockRazor Sequencer Feed兼容官方標準接入方式。節點只需替換 Feed URL，即可更快、更穩定地接收最新數據，減少追塊延遲，保持鏈上狀態實時同步。

### Benchmark

我們在AWS US East (Ohio)的每一個可用區（use2-az1、use2-az2、use2-az3）通過同一個測試客戶端與Robinhood Chain Sequencer Feed和BlockRazor Sequencer Feed分別建立wss連接，比較從兩者接收區塊的相對延遲。先接收到區塊的Sequencer Feed其相對延遲視為0ms，後接收到區塊的Sequencer Feed其相對延遲等於先後接收區塊的時間戳差值。具體數據如下：

{% tabs %}
{% tab title="use2-az1" %}
<table><thead><tr><th>Sequencer Feed</th><th width="104.07421875">Win rate</th><th width="101.1484375">P50</th><th width="110.5859375">P90</th><th width="105.84375">P95</th><th width="117.4296875">P99</th></tr></thead><tbody><tr><td>BlockRazor Sequencer Feed</td><td><strong>90.51%</strong></td><td><strong>0.000 ms</strong></td><td><strong>0.000 ms</strong></td><td><strong>1.095 ms</strong></td><td><strong>3.812 ms</strong></td></tr><tr><td>Robinhood Chain Sequencer Feed</td><td><strong>9.49%</strong></td><td><strong>6.573 ms</strong></td><td><strong>15.456 ms</strong></td><td><strong>19.721 ms</strong></td><td><strong>42.707 ms</strong></td></tr></tbody></table>
{% endtab %}

{% tab title="use2-az2" %}
<table><thead><tr><th width="121.29296875">Sequencer Feed</th><th width="107.6328125">Win rate</th><th width="109.671875">P50</th><th width="102.86328125">P90</th><th width="107.23828125">P95</th><th width="115.046875">P99</th></tr></thead><tbody><tr><td>BlockRazor Sequencer Feed</td><td><strong>69.16%</strong></td><td><strong>0.000 ms</strong></td><td><strong>2.151 ms</strong></td><td><strong>3.230 ms</strong></td><td><strong>5.589 ms</strong></td></tr><tr><td>Robinhood Chain Sequencer Feed</td><td><strong>30.84%</strong></td><td><strong>1.719 ms</strong></td><td><strong>6.754 ms</strong></td><td><strong>9.782 ms</strong></td><td><strong>31.128 ms</strong></td></tr></tbody></table>
{% endtab %}

{% tab title="use2-az3" %}
<table><thead><tr><th width="131.171875">Sequencer Feed</th><th width="108.26953125">Win rate</th><th width="110.5546875">P50</th><th width="111.25">P90</th><th width="105.05078125">P95</th><th width="108.12890625">P99</th></tr></thead><tbody><tr><td>BlockRazor Sequencer Feed</td><td><strong>75.08%</strong></td><td><strong>0.000 ms</strong></td><td><strong>1.764 ms</strong></td><td><strong>2.861 ms</strong></td><td><strong>5.330 ms</strong></td></tr><tr><td>Robinhood Chain Sequencer Feed</td><td><strong>24.92%</strong></td><td><strong>2.524 ms</strong></td><td><strong>12.306 ms</strong></td><td><strong>16.728 ms</strong></td><td><strong>37.805 ms</strong></td></tr></tbody></table>
{% endtab %}
{% endtabs %}

從延遲分布來看，BlockRazor 不僅在大多數區塊上率先到達，而且這種領先優勢具有較高的一致性；相比之下，Robinhood Chain Sequencer Feed 更經常處於落後位置，且延遲波動更為明顯。

綜合而言，BlockRazor Sequencer Feed 在區塊傳輸速度、首達率及延遲穩定性方面均展現出明顯優勢，能為延遲敏感型交易提供更可靠的先發窗口。

### 價格

價格為$80 / stream / 日和$800 / stream / 月。 <a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=robinhood&#x26;serviceId=robinhood_fast_tx&#x26;billing=day" class="button primary medium">訂閱</a>

### 端點

<table><thead><tr><th width="152.515625">地区</th><th>端点</th></tr></thead><tbody><tr><td>美国</td><td>wss://us.robinhood-feeder.blockrazor.io/ws/{authToken}</td></tr><tr><td>日本</td><td>wss://jp.robinhood-feeder.blockrazor.io/ws/{authToken}</td></tr></tbody></table>

### 使用步驟

{% stepper %}
{% step %}
<a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=robinhood&#x26;serviceId=robinhood_fast_tx&#x26;billing=day" class="button primary medium">訂閱</a> **BlockRazor Sequencer Feed**
{% endstep %}

{% step %}
**在portal獲取auth，將其作為URI拼接於wss url**

wss://us.robinhood-feeder.blockrazor.io/ws/{authToken}
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

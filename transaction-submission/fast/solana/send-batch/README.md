# Send Batch

{% hint style="warning" %}
Solana發送batch的服務不和訂閱計劃綁定，可前往 [Authentication](../../../../get-started/authentication.md) 獲取API KEY，默认限流为3 BPS。如需提升限流標準，請[聯繫](https://discord.com/invite/qqJuwRb8Nh)我們，我們會在第一時間處理
{% endhint %}

`Send Batch` 是 BlockRazor 為 Solana 提供的bundle發送接口，用於將已簽名交易以batch形式低延迟發送到鏈上。单个batch中最多可发送 25 笔交易。

### 端點

* `POST /sendBatch`
* `gRPC`

### 交易構建示例

* [Curl](qing-qiu-shi-li/curl.md)
* [Go](qing-qiu-shi-li/go.md)
* [Rust](qing-qiu-shi-li/rust.md)
* [JS](qing-qiu-shi-li/js.md)

### 請求參數

<table><thead><tr><th width="121.40234375">字段</th><th width="77.1875">必填</th><th width="125.62890625">示例</th><th>備注</th></tr></thead><tbody><tr><td>transactions</td><td>是</td><td>string[]</td><td>已簽名的交易，Base64编码，上限25筆</td></tr><tr><td>mode</td><td>否</td><td>"fast"<br>"sandwichMitigation"</td><td>BlockRazor支持fast和sandwichMitigation兩種模式，默認為fast模式。<br><br>在fast模式中，交易會基於全球分布式高性能網絡和高質量SWQoS質押鏈路被飽和式發送，以最低延遲到達Leader節點。<br><br>在sandwichMitigation模式中，交易會被發往BlockRazor高度信任的SWQoS質押鏈路，同時交易會跳過黑名單Leader(經BlockRazor三明治監測機制動態精確識別)的slot。在此模式下，<strong>請不要用</strong>durable nonce發送交易，這會使三明治保護失效。</td></tr><tr><td>safeWindow</td><td>否</td><td>3</td><td>sandwichMitigation模式中用於確定交易發送時機的參數，數字代表從當前slot起連續白名單驗證者的slot數量，比如設定3，則交易會僅在當前起連續3個slot都屬於白名單驗證者時發送。<br><br>safeWindow的參數範圍是3-13，數字越大防治三明治攻擊效果越好，但可能會對上鏈速度有一定影響。如不設定，則默認為3。</td></tr><tr><td>revertProtection</td><td>否</td><td>false</td><td>默認為false。如設置為true，交易不會在鏈上執行失敗，但上鏈速度会受到影响且存在无法上链的可能，請根據實際需求謹慎選擇開啓。</td></tr></tbody></table>

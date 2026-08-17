---
description: 介紹BlockRazor Solana Transaction Sending模式下Send Transaction in Plain Text的集成方法
---

# Solana Transaction Sending in Plain Text

### 介紹

{% hint style="warning" %}
Solana發送交易的服務不和訂閱計劃綁定，可前往 [Authentication](../../../../get-started/authentication.md) 獲取API KEY，默认限流为3 TPS。如需提升限流標準，請[聯繫](https://discord.com/invite/qqJuwRb8Nh)我們，我們會在第一時間處理
{% endhint %}

`Send in Plain Text` 用於在Solana上發送已簽名的交易，支持HTTP協議。與 [Send Transaction](./) 方式相比，它提供了一種更精簡、更迅速的交易提交途徑。

* 繞過 CORS 預檢： 它消除了通常由 OPTIONS 預檢請求所引起的延遲（大約 50-100 毫秒）。
* 純文本而非 JSON： 採用簡單的純文本傳輸，避免了與解析 JSON 相關的計算負擔。此外，由此產生的較小數據包尺寸有助於縮短網路傳輸時間並降低成本。
* Base64 編碼優勢： 與 Base58 相比，Base64 的編碼和解碼操作速度顯著更快，同時其更緊湊的序列化方式能縮小整體的數據體大小。

`Send in Plain Text` 的以上特性更適合擁有全球用戶的前端交易應用項目。

### 端點

* `POST /v2/sendTransaction`

### 流控說明

{% hint style="info" %}
Solana發送交易的服務不和訂閱計劃綁定，可前往 [Authentication](../../../../get-started/authentication.md) 獲取API KEY，默认限流为3 TPS。如需提升限流標準，請[聯繫](https://discord.com/invite/qqJuwRb8Nh)我們，我們會在第一時間處理
{% endhint %}

### 請求示例

{% tabs %}
{% tab title="CURL" %}
```bash
curl -X POST 'http://frankfurt.solana.blockrazor.xyz:443/v2/sendTransaction?auth=<auth_token>' \
-H "Content-Type: text/plain" \
-d "<base64_endcoded_tx>"
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
注意：

* 認證 (auth) 和 請求 (request) 參數必須以 URI 參數的形式填入URL，比如http://frankfurt.solana.blockrazor.xyz:443/v2/sendTransaction?auth=\<auth\_token>\&mode=fast\&revertProtection=true
* 請求中唯一允許的header是 `Content-Type: text/plain`
* 交易必須使用 Base64 進行編碼
{% endhint %}

### 請求參數

<table><thead><tr><th width="103.18359375">字段</th><th width="77.1875">必填</th><th width="125.62890625">示例</th><th>備注</th></tr></thead><tbody><tr><td>transaction</td><td>是</td><td>"4hXTCk……tAnaAT"</td><td>已簽名的交易，採用 Base64 編碼</td></tr><tr><td>mode</td><td>否</td><td>"fast"<br>"sandwichMitigation"</td><td>BlockRazor支持fast和sandwichMitigation兩種模式，默認為fast模式。<br><br>在fast模式中，交易會基於全球分布式高性能網絡和高質量SWQoS質押鏈路被飽和式發送，以最低延遲到達Leader節點。<br><br>在sandwichMitigation模式中，交易會被發往BlockRazor高度信任的SWQoS質押鏈路，同時交易會跳過黑名單Leader(經BlockRazor三明治監測機制動態精確識別)的slot。在此模式下，<strong>請不要用</strong>durable nonce發送交易，這會使三明治保護失效。</td></tr><tr><td>safeWindow</td><td>否</td><td>3</td><td>sandwichMitigation模式中用於確定交易發送時機的參數，數字代表從當前slot起連續白名單驗證者的slot數量，比如設定3，則交易會僅在當前起連續3個slot都屬於白名單驗證者時發送。<br><br>safeWindow的參數範圍是3-13，數字越大防治三明治攻擊效果越好，但可能會對上鏈速度有一定影響。如不設定，則默認為3。</td></tr><tr><td>revertProtection</td><td>否</td><td>false</td><td>默認為false。如設置為true，交易不會在鏈上執行失敗，但上鏈速度会受到影响且存在无法上链的可能，請根據實際需求謹慎選擇開啓。</td></tr></tbody></table>

### 返回參數

<table><thead><tr><th width="100.3515625">狀態碼</th><th width="192.515625">消息</th><th>釋義</th></tr></thead><tbody><tr><td>200</td><td>OK</td><td>請求正常</td></tr><tr><td>400</td><td>BadRequest</td><td>請求參數不合法</td></tr><tr><td>403</td><td>Forbidden</td><td>請求被拒絕，auth為空、無效或過期</td></tr><tr><td>500</td><td>InternalServerError</td><td>內部服務錯誤</td></tr></tbody></table>


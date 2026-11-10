---
description: 瞭解 BlockRazor Elysium eth_sendRawTransaction API 的介紹、價格、請求參數、請求示例和返回示例
tagTitle: Elysium eth_sendRawTransaction API
---

# Elysium eth\_sendRawTransaction API

Elysium 是屬於 Hyperliquid 生態的高性能 Layer 2 網絡，基於 Arbitrum Orbit 開發，在 HyperEVM 上結算，並使用 HYPE 支付 Gas。它兼容 EVM 和主流以太坊開發工具，以約 100 毫秒出塊和高吞吐執行為設計目標，為現貨交易、專業做市、代幣發行及其他 DeFi 應用提供低延遲的鏈上運行環境。

`eth_sendRawTransaction`  是BlockRazor 為 Elysium 提供的交易發送接口，用户可通过该方法將已簽名的原始交易低延迟發送到鏈上，目前支持HTTPS协议。

### 價格

<table><thead><tr><th width="118.61328125">用戶類型</th><th width="309.9453125">價格</th><th width="284.26953125">限流</th></tr></thead><tbody><tr><td>新註冊用戶</td><td>1 Tx / 5s</td><td>免費</td></tr><tr><td>付費用戶</td><td>10 Txs / 1s</td><td>$100 / 日<br>$1000 / 月<br><br><a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=hyperliquid_l2&#x26;serviceId=hyperliquid_l2_transaction_sending&#x26;billing=day" class="button primary medium">訂閱</a></td></tr></tbody></table>

### 端點

<table><thead><tr><th width="115.1171875">地區</th><th width="621.55859375">端點</th></tr></thead><tbody><tr><td>東京</td><td>https://ap.elysium.blockrazor.io</td></tr></tbody></table>

### 請求參數

<table><thead><tr><th width="150.40234375">参数</th><th width="81">必选</th><th width="82">格式</th><th width="106">示例</th><th>描述</th></tr></thead><tbody><tr><td>-</td><td>是</td><td>String</td><td>"0x…4b"</td><td>經過簽名的raw transaction</td></tr></tbody></table>

### 請求示例

{% tabs %}
{% tab title="HTTPS" %}
```bash
curl https://ap.elysium.blockrazor.io \
  -H 'content-type: application/json' \
  -H 'Authorization: Bearer <auth-token>' \
  --data '{
  "jsonrpc": "2.0",
  "id": "1",
  "method": "eth_sendRawTransaction",
  "params": ["0x…9c"]
}'
```
{% endtab %}
{% endtabs %}

### 返回示例

```json
{
 "jsonrpc":"2.0",
 "id":"1",
 "result":"0xa06b……f7e8ec"  // 交易哈希
}‍
```

```json
{
  "jsonrpc":"2.0",
  "id":"1",
  "error":{
    "code":-32000,
    "message":"auth is invalid"
    }
}
```

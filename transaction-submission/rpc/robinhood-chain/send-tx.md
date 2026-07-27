---
description: 介紹BlockRazor Robinhood Chain RPC sendTx的集成方法
hidden: true
---

# Send Tx

`Send Tx`  是BlockRazor 為 Robinhood Chain 提供的交易發送接口，用户可通过该方法將已簽名的原始交易低延迟發送到鏈上，目前支持HTTPS协议。

相比 [`eth_sendRawTransaction`](eth_sendrawtransaction.md) ，`Send Tx` 的交易在發送至Robinhood Chain官方排序器前會經過Pre-flight檢查，包括但不限於nonce是否正確、balance是否足够、contract execution 是否 revert等。

### 價格

<table><thead><tr><th width="155.45703125">用戶類型</th><th width="208.93359375">限流</th><th>價格</th></tr></thead><tbody><tr><td>新註冊用戶</td><td>20 Tx / 1s</td><td>免費</td></tr></tbody></table>

### 請求參數

<table><thead><tr><th width="150.40234375">参数</th><th width="81">必选</th><th width="82">格式</th><th width="106">示例</th><th>描述</th></tr></thead><tbody><tr><td>-</td><td>是</td><td>String</td><td>"0x…4b"</td><td>經過簽名的raw transaction</td></tr></tbody></table>

### 請求示例

{% tabs %}
{% tab title="HTTPS" %}
```bash
curl https://robinhood.blockrazor.io \
  -H 'content-type: application/json' \
  -H 'Authorization: Bearer <auth-token>' \
  --data '{
  "jsonrpc": "2.0",
  "id": "1",
  "method": "sendTx",
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
    "message":"nonce too low: next nonce 57, tx nonce 56"
    }
}
```


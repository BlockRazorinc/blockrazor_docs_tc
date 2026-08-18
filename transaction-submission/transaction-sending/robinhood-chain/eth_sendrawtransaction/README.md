---
description: 介紹BlockRazor Robinhood Chain eth_sendRawTransaction的集成方法
metaLinks:
  canonical: ./
  alternates:
    - >-
      https://app.gitbook.com/s/jbyfG8gOgcdsK3wVxNdQ/transaction-submission/transaction-sending/robinhood-chain/eth_sendrawtransaction
---

# Robinhood Chain eth\_sendRawTransaction

{% hint style="info" %}
Robinhood Chain eth\_sendRawTransaction暫不對用戶開放，如需對接請[聯繫](https://discord.gg/qqJuwRb8Nh)我們
{% endhint %}

`eth_sendRawTransaction`  是BlockRazor 為 Robinhood Chain 提供的交易發送接口，用户可通过该方法將已簽名的原始交易低延迟發送到鏈上，目前支持HTTPS协议。

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


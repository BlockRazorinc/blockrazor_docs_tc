---
description: 介紹BlockRazor Robinhood Chain RPC eth_sendBatch的集成方法
metaLinks:
  canonical: send-batch.md
  alternates:
    - >-
      https://app.gitbook.com/s/jbyfG8gOgcdsK3wVxNdQ/transaction-submission/transaction-sending/robinhood-chain/eth_sendbatch
---

# Robinhood Chain eth\_sendBatch

{% hint style="info" %}
Robinhood Chain eth\_sendBatch 暫不對用戶開放，如需對接請[聯繫](https://discord.gg/qqJuwRb8Nh)我們
{% endhint %}

`Send Batch`  是BlockRazor 為 Robinhood Chain 提供的交易批量發送接口，用户可通过该方法將已簽名的原始交易批量低延迟發送到鏈上，目前支持HTTPS协议。

Batch的交易容量上限為10筆，交易每隔5ms依序發送至Robinhood官方排序器。需要注意的是，Batch不具備原子性，不保證最終上鍊順序和請求預期一致。

### 請求參數

<table><thead><tr><th width="90.5859375">参数</th><th width="81">必选</th><th width="82">格式</th><th width="191.19140625">示例</th><th>描述</th></tr></thead><tbody><tr><td>-</td><td>是</td><td>String[]</td><td>["0x…9c","0x…6a"]</td><td>經過簽名的raw transaction列表</td></tr></tbody></table>

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
  "method": "eth_sendBatch",
  "params": ["0x…9c","0x…6a"]
}'
```
{% endtab %}
{% endtabs %}

### 返回示例

```json
{
 "jsonrpc":"2.0",
 "id":"1",
 "result":"0xa06b……f7e8ec"  // batch哈希
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


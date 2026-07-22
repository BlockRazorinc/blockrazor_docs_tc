# eth\_sendRawTransaction

{% hint style="info" %}
Robinhood Chain RPC暫不對用戶開放，如需對接請[聯繫](https://discord.gg/qqJuwRb8Nh)我們
{% endhint %}

`eth_sendRawTransaction`  是BlockRazor 為 Robinhood Chain 提供的交易發送接口，用户可通过该方法將已簽名的原始交易低延迟發送到鏈上，目前支持HTTPS协议。

### Robinhood Chain是什么 <a href="#what-is-robinhood-chain" id="what-is-robinhood-chain"></a>

Robinhood Chain 是一個以太坊 Layer 2 網絡，基於 Arbitrum 構建，專為 RWA 優化，包括股票和 ETF 等資產。它支持全天候的鏈上交易以及用戶自主托管，讓用戶無需依賴傳統金融中介即可直接在區塊鏈上持有、交易和管理資產。

Robinhood Chain官網：[https://robinhood.com/us/en/chain/](https://robinhood.com/us/en/chain/)

Robinhood Chain交易數據：[https://robinhoodchain.blockscout.com/stats](https://robinhoodchain.blockscout.com/stats)

### Benchmark

我們將測試客戶端部署於AWS的法蘭克福、俄亥俄和日本地區，向對應地區的 BlockRazor 端點和官方 RPC 端點發送 50 組 nonce 相同的交易。每組交易除接收地址（用於區分發送通道）外，其餘所有參數均保持完全一致。

我們通過比較兩種通道最終成功上鏈交易的佔比來評估性能，上鏈佔比越高，代表交易傳播和執行速度越快。基準測試結果如下。

<table><thead><tr><th width="157.453125">地區</th><th>BlockRazor上鍊率</th><th>Robinhood上鏈率</th></tr></thead><tbody><tr><td>法蘭克福</td><td>88%</td><td>12%</td></tr><tr><td>俄亥俄</td><td>50%</td><td>50%</td></tr><tr><td>日本</td><td>96%</td><td>4%</td></tr></tbody></table>

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


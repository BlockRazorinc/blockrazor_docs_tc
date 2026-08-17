---
description: 介紹BlockRazor BSC Fast模式的eth_sendRawTransaction接口以及集成方法
hidden: true
noIndex: true
---

# eth\_sendRawTransaction

`eth_sendRawTransaction` 是 BlockRazor 為 BSC 提供的 Fast 模式交易發送接口，兼容標準 `eth_sendRawTransaction` 調用方式，用於將已簽名交易以更低延遲發送到鏈上。

發往Fast 模式`eth_sendRawTransaction` 的交易不會通過公開 Mempool 廣播，因此在提升發送速度的同時，也能避免交易在公開傳播過程中暴露。

{% hint style="info" %}
`eth_sendRawTransaction`不和訂閱計劃綁定，但每筆交易中需要包含轉賬至0x9D70AC39166ca154307a93fa6b595CF7962fe8e5的tip，金額至少為0.000025 BNB 或 Transaction Fee 的5%
{% endhint %}

### 端點

{% tabs %}
{% tab title="通用域名" %}
```
http://bsc-fast.blockrazor.io
```
{% endtab %}

{% tab title="地理域名" %}
<table><thead><tr><th width="146.78125">地區</th><th>域名</th></tr></thead><tbody><tr><td>Frankfurt</td><td>http://frankfurt.bsc-fast.blockrazor.io</td></tr><tr><td>Japan</td><td>http://japan.bsc-fast.blockrazor.io</td></tr><tr><td>Virginia</td><td>http://virginia.bsc-fast.blockrazor.io</td></tr></tbody></table>
{% endtab %}
{% endtabs %}

### 限流

`eth_sendRawTransaction`不和訂閱計劃綁定，限流默認統一為10 TPS，如需提升TPS，請於我們聯繫

### 請求示例

```json
curl http://bsc-fast.blockrazor.io \
  -X POST \
  -H "Authorization: Bearer <auth>" \
  -H "Content-Type: application/json" \
  --data '
    {
    "jsonrpc": "2.0",
    "method": "eth_sendRawTransaction",
    "params": [
      "Signed Transaction"
    ],
    "id": 1
  }
  '
```

### 返回示例

**正常**

```json
{
 "jsonrpc":"2.0",
 "id":"1",
 "result":"0xa06b……f7e8ec"  // 交易哈希
}‍
```

**異常**

```json
{
  "jsonrpc":"2.0",
  "id":"1",
  "error":{
    "code":-32000,
    "message":"Tip verification failed"
    }
}
```

### Keep Alive

請發送 POST 請求到健康檢查端點以保持連線活躍，建議每隔10s請求一次，請求示例如下：

{% tabs %}
{% tab title="CURL" %}
```bash
curl -X POST 'http://bsc-fast.blockrazor.io/health' \
-H "Content-Type: application/json" \
-d ""
```
{% endtab %}
{% endtabs %}


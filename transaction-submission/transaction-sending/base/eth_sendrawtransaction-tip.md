---
description: 介紹BlockRazor Base Transaction Sending 模式的eth_sendRawTransaction(tip)接口以及集成方法
---

# Base eth\_sendRawTransaction(tip)

`eth_sendRawTransaction(tip)` 用於將已簽名交易以更低延遲發送到鏈上，目前支持標準 JSON-RPC 風格請求，便於用戶在現有發送邏輯基礎上快速接入。

`eth_sendRawTransaction(tip)` 和 `eth_sendRawTransaction` 能力定位存在差異，`eth_sendRawTransaction`更適合作為標準發送入口，強調全球多點部署和跨洲專線，而`eth_sendRawTransaction(tip)` 更適合重點追求單筆交易發送速度和更快進入鏈上執行路徑的場景

{% hint style="info" %}
`eth_sendRawTransaction(tip)` 不和訂閱計劃綁定，但每筆交易中需要包含轉賬至0x9D70AC39166ca154307a93fa6b595CF7962fe8e5的tip，下限金額至少為0.000003 ETH 或 MaxPriorityFee的 5%
{% endhint %}

### 端點

http://base-fast.blockrazor.io

### 限流

Fast模式不和訂閱計劃綁定，限流默認統一為10 TPS，如需提升TPS，請於我們聯繫

### 請求示例

```json
curl http://base-fast.blockrazor.io \
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


---
hidden: true
---

# Send BackBundle

### 接口說明

本接口方法名為`eth_sendBackBundle` ，用於接收塊尾bundle，允許bundle內所有交易的gas price為0 gwei。目前該接口僅支持Virginia端點，建議將服務部署於附近。

### 端點

https://virginia.builder.blockrazor.io

### 請求參數

<table><thead><tr><th width="151.38671875">參數</th><th width="85.73046875">必選</th><th width="136">格式</th><th width="124">示例</th><th>描述</th></tr></thead><tbody><tr><td>txs</td><td>是</td><td>array[hex]</td><td>["0x…4b"]</td><td>經過簽名的raw transaction，仅允许一笔</td></tr><tr><td>blockNumber</td><td>否</td><td>uint64</td><td>106210501</td><td>該bundle期望被包含的目标区块</td></tr></tbody></table>

### 請求示例

```json
curl -X POST \
  -H "Content-Type: application/json" \
  -H "Authorization: <YOUR_TOKEN>" \
  -d '{
    "jsonrpc": "2.0",
    "id": 1,
    "method": "eth_sendBackBundle",
    "params": [
      {
        "txs": ["0x...4b"],
        "blockNumber": 106210501
      }
    ]
  }' \
  https://virginia.builder.blockrazor.io
```

### 返回示例

正常

```json
{
 "jsonrpc":"2.0",
 "id":"1",
 "result":null
}‍
```

異常

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "error": {
    "code": -32000,
    "message": "bundle missing blockNumber"
  }
}
```

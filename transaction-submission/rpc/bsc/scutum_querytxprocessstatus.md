---
description: 介紹如何集成scutum_queryTxProcessStatus方法來查詢BSC RPC交易狀態
---

# BSC RPC交易狀態查詢

`scutum_queryTxProcessStatus`  用於實時查詢BlockRazor RPC對於交易的處理狀態，目前支持查询BSC交易。

### 請求示例

```json
curl -X POST -H "Content-Type: application/json" --data '{
	"id": 1,
	"jsonrpc": "2.0",
	"method": "scutum_queryTxProcessStatus",
	"params": ["0xf84a……e54284"]
}'<ETH_NODE_URL>
```

### 返回示例

正常

```json
{
  "jsonrpc": "2.0",
  "result": "{"msg\":\"tx is included on chain\",\"status\":\"included\"}",
  "id": "1"
} // 交易已經在鏈上執行
```

```json
{
  "jsonrpc": "2.0",
  "result": "{"msg\":\"tx is expired and discarded\",\"status\":\"expired\"}",
  "id": "1"
} // 交易由於已過期被廢棄（交易發出時的區塊高度+100小於最新區塊高度）
```

```json
{
  "jsonrpc": "2.0",
  "result": "{"msg\":\"nonce too high\",\"status\":\"pending\"}",
  "id": "1"
} // Scutum正在處理該筆交易，交易由於nonce過高被放入隊列中
```

```json
{
  "jsonrpc": "2.0",
  "result": "{"msg\":\"tx is pending\",\"status\":\"pending\"}",
  "id": "1"
} // Scutum正在處理該筆交易
```

```json
{
  "jsonrpc": "2.0",
  "result": "{"msg\":\"simulation error: xxxxxxx\",\"status\":\"failed\"}",
  "id": "1"
} // 由於計算失敗，Scutum無法處理該筆交易
```

異常

```json
{
  "jsonrpc": "2.0",
  "error": "{\"code\":-32000,\"message\":\"tx not found\"}",
  "id": "1"
} //交易未發往Scutum或者已超過Scutum處理時限
```

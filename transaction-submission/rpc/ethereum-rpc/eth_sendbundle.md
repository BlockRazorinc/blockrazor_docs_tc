---
description: 介紹集成BlockRazor Ethereum RPC的eth_sendBundle方法
metaLinks:
  canonical: eth_sendbundle.md
  alternates:
    - >-
      https://app.gitbook.com/s/jbyfG8gOgcdsK3wVxNdQ/transaction-submission/rpc/ethereum/eth_sendbundle
---

# Ethereum RPC eth\_sendBundle

### 端點

{% hint style="info" %}
項目RPC可參考 [集成RPC](integration.md) 獲取
{% endhint %}

<table><thead><tr><th width="165.31640625">端點類型</th><th width="483.47265625">URL</th></tr></thead><tbody><tr><td>通用RPC</td><td>https://eth.blockrazor.xyz</td></tr><tr><td>項目默認RPC</td><td>https://eth.blockrazor.xyz/&#x3C;rpc_id></td></tr><tr><td>項目自定義RPC</td><td>https://&#x3C;custom_domain>.eth.blockrazor.xyz</td></tr></tbody></table>

### 請求參數

<table><thead><tr><th width="184">參數</th><th width="74">必選</th><th width="105">格式</th><th width="177.79296875">示例</th><th>備註</th></tr></thead><tbody><tr><td>txs</td><td>是</td><td>[]bytes</td><td>[ "0xf84a……e54284" ]</td><td>raw txs，最多允許設置50筆</td></tr><tr><td>revertingTxHashes</td><td>否</td><td>[]hash</td><td>["0x1f23……0abb1e"]</td><td>允許revert的交易哈希，是txs的子集</td></tr><tr><td>blockNumber</td><td>否</td><td>string</td><td>"0x3C04F81"</td><td>該bundle有效的最大區塊號，默認為當前區塊號+100</td></tr></tbody></table>

### 請求示例

```json
curl -X POST -H "Content-Type: application/json" --data '{
  "id": 1,
  "jsonrpc": "2.0",
  "method": "eth_sendBundle",
  "params": [{
    "Txs": [
"0xf8668203988405f5e100825208942ee393c739036a7660ec11bf2101d537eb52f3ac80808193a06836d5f4052376dc3114794da5fedd7a5b8090ddae0ec45dfa66c234fcabb6efa07cf17dad992c33e8e3e4d82355cfe12d3be2560fc2b0873c36dce398088d8e4f"
    ]
    "revertingTxHashes":[],
    "blockNumber":"0x3C04F81"
  }]
}' https://eth.blockrazor.xyz
```

### 返回示例

正常

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": "0x11111111..."
}
```

異常

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "jsonerror": {
	  "code": -38000,
	  "message": "nonce too low: address 0x9Abae1b279A4Be25AEaE49a33e807cDd3cCFFa0C, tx: 0 state: 45"
  }
}
```


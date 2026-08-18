---
description: 介紹項目方如何集成BlockRazor BSC RPC的eth_sendBundle方法
metaLinks:
  canonical: eth_sendbundle.md
  alternates:
    - >-
      https://app.gitbook.com/s/jbyfG8gOgcdsK3wVxNdQ/transaction-submission/rpc/bsc/eth_sendbundle
---

# BSC RPC eth\_sendBundle

### 端點

{% hint style="info" %}
項目RPC可前往 [集成RPC](integration.md) 獲取
{% endhint %}

<table><thead><tr><th width="165.31640625">端点类型</th><th width="457.984375">URL</th></tr></thead><tbody><tr><td>通用RPC</td><td>https://bsc.blockrazor.xyz</td></tr><tr><td>項目默認RPC</td><td>https://bsc.blockrazor.xyz/&#x3C;rpc_id></td></tr><tr><td>項目自定義RPC</td><td>https://&#x3C;custom_domain>.bsc.blockrazor.xyz</td></tr></tbody></table>

### 請求參數

{% hint style="info" %}
在BSC中，`eth_sendBundle` 允許在bundle中包含0 gwei的交易，但bundle中交易（public mempool中的交易除外）的平均gasPrice仍需不小於0.05 gwei。
{% endhint %}

<table><thead><tr><th width="184">參數</th><th width="74">必選</th><th width="105">格式</th><th width="177.79296875">示例</th><th>備註</th></tr></thead><tbody><tr><td>txs</td><td>是</td><td>[]bytes</td><td>[ "0xf84a……e54284" ]</td><td>raw txs，最多允許設置50筆</td></tr><tr><td>revertingTxHashes</td><td>否</td><td>[]hash</td><td>["0x1f23……0abb1e"]</td><td>允許revert的交易哈希，是txs的子集</td></tr><tr><td>maxBlockNumber</td><td>否</td><td>uint64</td><td>39177941</td><td>該bundle有效的最大區塊號，默認為當前區塊號+100</td></tr></tbody></table>

### 請求示例

```json
curl -X POST -H "Content-Type: application/json" --data '{
  "id": 1,
  "jsonrpc": "2.0",
  "method": "eth_sendBundle",
  "params": [{
    "Txs": [
"0xf8668203988405f5e100825208942ee393c739036a7660ec11bf2101d537eb52f3ac80808193a06836d5f4052376dc3114794da5fedd7a5b8090ddae0ec45dfa66c234fcabb6efa07cf17dad992c33e8e3e4d82355cfe12d3be2560fc2b0873c36dce398088d8e4f"
    ],
    "revertingTxHashes":[],
    "maxBlockNumber":62934913
  }]
}' https://bsc.blockrazor.xyz
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




---
description: 介紹集成BlockRazor Ethereum RPC的eth_sendRawTransaction的方法
---

# eth\_sendRawTransaction

`eth_sendRawTransaction` 兼容原生的JSON-RPC方法，無需進行額外修改。

如需修改交易披露、返利地址和revert保護等參數，可前往[控制台](https://www.blockrazor.io/#/login)配置專屬RPC。

### 端點

{% hint style="info" %}
項目RPC可參考 [集成RPC](ji-cheng-rpc.md) 獲取
{% endhint %}

<table><thead><tr><th width="165.31640625">端點類型</th><th width="483.47265625">URL</th></tr></thead><tbody><tr><td>通用RPC</td><td>https://eth.blockrazor.xyz</td></tr><tr><td>項目默認RPC</td><td>https://eth.blockrazor.xyz/&#x3C;rpc_id></td></tr><tr><td>項目自定義RPC</td><td>https://&#x3C;custom_domain>.eth.blockrazor.xyz</td></tr></tbody></table>

### 請求參數

<table><thead><tr><th width="124">參數</th><th width="65">必選</th><th width="110">格式</th><th width="180">示例</th><th>備註</th></tr></thead><tbody><tr><td>-</td><td>是</td><td>bytes</td><td>"0xd46e……445675"</td><td>raw tx</td></tr></tbody></table>

### 請求示例

```json
curl -X POST -H "Content-Type: application/json" --data '{
    "id": 1,
    "jsonrpc": "2.0",
    "method": "eth_sendRawTransaction",
    "params": [
        "0xd46e……445675"
    ]
}' https://bsc.blockrazor.xyz
```

### 返回示例

**正常**

```json
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0xe670……527331"
}
```

**異常**

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "error": {
	"code": -32000,
	"message": "rlp: element is larger than containing list"
  }
}
```

### JSON-RPC方法

RPC支持以太坊節點原生的JSON-RPC方法，可參考[https://ethereum.org/zh/developers/docs/apis/json-rpc/#json-rpc-methods](https://ethereum.org/zh/developers/docs/apis/json-rpc/#json-rpc-methods)

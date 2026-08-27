---
description: 介紹Searcher集成BlockRazor BSC RPC的eth_sendMevBundle方法來進行訂單流拍賣
metaLinks:
  canonical: orderflow-auction.md
  alternates:
    - >-
      https://app.gitbook.com/s/jbyfG8gOgcdsK3wVxNdQ/transaction-submission/rpc/bsc/orderflow-auction
---

# BSC RPC 訂單流競拍

### 介绍

Searcher可以訂閱 [Private Mempool ](../../../streams/private-mempool.md)執行backrun策略，再將backrun bundle發送至BlockRazor RPC參與競拍獲得收益。

此外，Searcher也可以跳過bundle訂閱，直接將bundle發送至BlockRazor RPC，憑借高性能網絡的網絡加速服務，BlockRazor RPC可以極低延遲將bundle轉發至主流builders，无需重复对接builder接口。

### 競拍機制

<figure><img src="../../../.gitbook/assets/image (62).png" alt=""><figcaption><p>bundle流轉</p></figcaption></figure>

#### **競拍時機**

Searcher可持續提交bundle競拍（不允許重復提交），BlockRazor RPC會根據出塊時間選擇最佳時機將競拍勝出的bundle提交給builder。如果針對同一個被backrun對象構建的bundle，已經被納入區塊或超過有效期，則競拍停止，相應bundle也會在數據推流中停止披露。

#### **競拍規則**

BlockRazor RPC按競拍金額進行英式競拍，競拍金額的接收和分配通過智能合約實現。

#### **競拍方法**

構建backrun交易時，可以通過接口調用競拍代理合約的proxyBid方法，

```json
interface IProxyBid { 
    function proxyBid(address refundAddress, uint256 refundCfg) external payable; 
}
```

競拍代理合約地址（proxyBidContract）、refundAddress和refundCfg可以在[Private Mempool](../../../streams/private-mempool.md)中獲取，msg.value（即競拍金額）必須大於0。

參數正確性會由BlockRazor RPC嚴格校驗。請不要向refundAddress或競拍代理合約地址直接轉賬或執行其他可能引起上述賬戶餘額變化的操作。

### RPC端點

{% hint style="info" %}
請將訂閱Private Mempool的域名與發送bundle的域名保持一致。如訂閱https://jp-bscscutum.blockrazor.xyz/stream，則將bundle發送至https://jp-bscscutum.blockrazor.xyz
{% endhint %}

<table><thead><tr><th width="148.99609375">地區</th><th>端點</th></tr></thead><tbody><tr><td>東京</td><td>https://jp-bscscutum.blockrazor.xyz</td></tr><tr><td>紐約</td><td>https://us-bscscutum.blockrazor.xyz</td></tr><tr><td>法蘭克福</td><td>https://ger-bscscutum.blockrazor.xyz</td></tr><tr><td>都柏林</td><td>https://ire-bscscutum.blockrazor.xyz</td></tr></tbody></table>

### 請求參數

#### Bundle

<table><thead><tr><th width="192">參數</th><th width="73">必選</th><th width="130">格式</th><th>示例</th><th>備注</th></tr></thead><tbody><tr><td>hash</td><td>否</td><td>hash</td><td>"0xa06b……f7e8ec"</td><td>從數據推流中收到的bundle hash，即被backrun的對象</td></tr><tr><td>txs</td><td>是</td><td>[]bytes</td><td>[ "0xf84a……e54284" ]</td><td>raw tx。如<code>hash</code>為空，則最高允許設置50筆raw txs，如<code>hash</code>不為空，則僅允許設置1筆raw tx</td></tr><tr><td>revertingTxHashes</td><td>否</td><td>[]hash</td><td>["0x1f23……0abb1e"]</td><td>允許revert的交易哈希，是txs的子集</td></tr><tr><td>maxBlockNumber</td><td>否</td><td>uint64</td><td>39177941</td><td>該bundle有效的最大區塊號，默認為當前區塊號+100</td></tr><tr><td><a href="orderflow-auction.md#hint">hint</a></td><td>否</td><td><a href="orderflow-auction.md#hint">map[string]bool</a></td><td></td><td>詳見<a href="orderflow-auction.md#hint">hint</a></td></tr><tr><td>refundAddress</td><td>否</td><td>address</td><td>"0x9abae1b279a4be25aeae49a33e807cdd3ccffa0c"</td><td>如hint中存在值為true的交易字段，則需要設置本字段，地址需为EOA。</td></tr></tbody></table>

#### hint

hint針對txs中的交易設置披露信息，如果設為true則視為披露該交易字段，false視為不披露該交易字段，如不設置，則默認為false。

<table data-search="false"><thead><tr><th width="163">參數</th><th width="84">必填</th><th width="89">格式</th><th width="82">示例</th><th>備注</th></tr></thead><tbody><tr><td>hash</td><td>否</td><td>bool</td><td>true</td><td>交易哈希</td></tr><tr><td>from</td><td>否</td><td>bool</td><td>false</td><td>交易的發起方地址</td></tr><tr><td>to</td><td>否</td><td>bool</td><td>true</td><td>交易的接收方地址</td></tr><tr><td>value</td><td>否</td><td>bool</td><td>false</td><td>交易value</td></tr><tr><td>nonce</td><td>否</td><td>bool</td><td>false</td><td>交易nonce</td></tr><tr><td>calldata</td><td>否</td><td>bool</td><td>true</td><td>交易calldata</td></tr><tr><td>functionSelector</td><td>否</td><td>bool</td><td>true</td><td>合約函數簽名哈希的前4個字節</td></tr><tr><td>logs</td><td>否</td><td>bool</td><td>true</td><td>交易在執行過程中拋出的事件日誌(該字段聯動設置是否披露狀態對象的數據變化)</td></tr></tbody></table>

### 請求示例

**Raw Bundle**

由於沒有backrun對象hash字段無需設置。bundle中的txs來自於公開內存池或自行構建，至多可設置50筆裸交易。Searcher可以授權披露raw bundle中的txs允許其他Searcher套利，同時自己收穫返利，也可以不披露，Scutum將轉發raw bundle給主流builders。

```json
curl -X POST -H "Content-Type: application/json" --data'{
	"id": 1,
	"jsonrpc": "2.0",
	"method": "eth_sendMevBundle",
	"params": [{
		"txs": ["0xf84a8080808080808193a0437a5584216e68d1ff5bd7803161865e058f9bf4637fd1391213eac03ae64444a00df12bffe475d5dd8cc1544b72ee280471f1dcb5173827ba41eb25cfc3e54284"],
		"revertingTxHashes": [],
		"maxBlockNumber": 39177941,
		"hint": {
			"hash": true,
			"from": false,
			"to": false,
			"value": false,
			"nonce": false,
			"calldata": false,
			"functionSelector": false,		
			"logs": true
		},
		"refundAddress": "0x9abae1b279a4be25aeae49a33e807cdd3ccffa0c"
	}]
}'<ETH_NODE_URL>
```

**First Backrun Bundle**

Searcher對raw bundle執行backrun策略，構成first backrun bundle。`hash`字段即數據流中接收到的raw bundle hash，`txs`即backrun tx，最多允許設置1筆。Searcher可選擇將first backrun bundle繼續披露給其他Seacher以執行嵌套的backrun策略。first backrun bundle在數據推流中的整體結構一般為\[raw bundle txs…, backrun tx]

```json
curl -X POST -H "Content-Type: application/json" --data '{
	"id": 1,
	"jsonrpc": "2.0",
	"method": "eth_sendMevBundle",
	"params": [{
		"hash": "0x0000000000000000000000000000000000000000000000000000000000000000", //hash of Raw Bundle
		"txs": ["0xf84a8080808080808193a0437a5584216e68d1ff5bd7803161865e058f9bf4637fd1391213eac03ae64444a00df12bffe475d5dd8cc1544b72ee280471f1dcb5173827ba41eb25cfc3e54284"],
		"revertingTxHashes": [],
		"maxBlockNumber": 39177941,
		"hint": {
			"hash": true,
			"from": false,
			"to": false,
			"value": false,
			"nonce": false,
			"calldata": false,
			"functionSelector": false,		
			"logs": true
		},
		"refundAddress": "0x9abae1b279a4be25aeae49a33e807cdd3ccffa0c"
	}]
}'<ETH_NODE_URL>
```

**Second Backrun Bundle**

Searcher可以對其他Searcher提交的first backrun bundle再次執行backrun策略，形成被二次backrun的嵌套bundle。`hash`即first backrun bundle的hash，`txs`即二次套利的backrun tx。second backrun bundle的整體結構一般為\[raw bundle txs…, first backrun tx, second backrun tx]。

{% hint style="info" %}
second backrun bundle不會再披露給其他Searcher，參數hint、refundRecipient和refundPercent會失效。
{% endhint %}

```json
curl -X POST -H "Content-Type: application/json" --data '{
	"id": 1,
	"jsonrpc": "2.0",
	"method": "eth_sendMevBundle",
	"params": [{
		"hash": "0x0000000000000000000000000000000000000000000000000000000000000000", // hash of First Backrun Bundle
		"txs": ["0xf84a8080808080808193a0437a5584216e68d1ff5bd7803161865e058f9bf4637fd1391213eac03ae64444a00df12bffe475d5dd8cc1544b72ee280471f1dcb5173827ba41eb25cfc3e54284"],
		"revertingTxHashes": [],
		"maxBlockNumber": 39177941
	}]
}'<ETH_NODE_URL>
```

### 返回示例

**正常**

```json
{"jsonrpc":"2.0","id":1,"result": "0x11111111..."}
```

**異常**

```json
{"jsonrpc":"2.0","id":1,"jsonerror":{"code":-38000,"message":"nonce too low: address 0x9Abae1b279A4Be25AEaE49a33e807cDd3cCFFa0C, tx: 0 state: 45"}}
```

### 常見問題

<details>

<summary>提交Bundle至RPC和提交Bundle至Block Builder有什麼區別</summary>

两者的核心区别在于 Bundle 的提交路径和最终到达的目标不同。

提交 Bundle 至 **BlockRazor RPC** 时，BlockRazor RPC 会将 Bundle 低延迟转发给主流 builders。这种方式更适合作为统一接入入口，用户不需要逐个对接不同 builder，即可完成 Bundle 提交。

提交 Bundle 至 **Block Builder** 时，Bundle 会直接发送到 BlockRazor Builder。它更适合明确希望使用 BlockRazor Builder 能力与接入路径的场景。

</details>


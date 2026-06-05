# Tx Trace

### 介紹

Tx Trace 是追蹤交易傳播的工具，用於查詢指定交易在全球網絡中的傳播路徑與時間差。它基於 BlockRazor 的 全球高性能網絡構建，提升交易在網絡傳播層的可觀測性，幫助用戶看到交易何時進入網絡、最先進入哪個區域、在各區域傳播用了多久。Tx Trace的適用場景如下：​

* 交易延遲排查
* 多地域節點部署評估
* 高頻交易鏈路優化
* private/public 路徑驗證

相較於傳統 RPC 提交結果，Tx Trace 補充了傳播過程數據，它不能替代鏈上回執查詢，但能提供更靠近網絡層的觀測視角，幫助用戶將交易傳播分析從黑盒判斷轉為基於區域和時間差的數據分析。

Tx Trace的查詢受有效期的限制，不支持查詢歷史全部交易。

### 價格

<table><thead><tr><th width="196.17578125">用戶類型</th><th>限流</th><th>價格</th></tr></thead><tbody><tr><td>新註冊用戶</td><td>20 次請求 / 天</td><td>免費</td></tr><tr><td>付費用戶</td><td>500 次請求 / 天</td><td>$500 / 月</td></tr></tbody></table>

### 端點

http://tx-trace.blockrazor.io

### 請求示例

```bash
curl -H "Authorization: <YOUR_AUTHORIZATION>" \
  http://tx-trace.blockrazor.io/txtrace/0xea4cac1749fcbfd53d798edf795d80c550aa00873d3acb1019000bf74dd18404
```

### 返回示例

**正常**

```json
{
	"txTrace": [{
		"region": "EU Germany",
		"txTime": "2026-06-03 03:18:10.481",
		"diff": "+0ms"
	},
	{
		"region": "NA US Virginia",
		"txTime": "2026-06-03 03:18:10.510",
		"diff": "+29ms" // 該筆交易從EU Germany傳播至NA US Virginia消耗29ms
	},
	{
		"region": "EU Ireland",
		"txTime": "2026-06-03 03:18:10.515",
		"diff": "+34ms" // 該筆交易從EU Germany傳播至EU Ireland消耗34ms
	},
	{
		"region": "AS Japan",
		"txTime": "2026-06-03 03:18:10.574",
		"diff": "+93ms"
	}],
	"txHash": "0xe55b39c4dead92fe956f7ce2d640e0fcf0ce0cd969da9e3f900d493634b64a54",
	"numberOfRegions": 4
}
```

**異常**

```json
{"error":"invalid token"}
```

```json
{"error":"daily limit exceeded"}
```


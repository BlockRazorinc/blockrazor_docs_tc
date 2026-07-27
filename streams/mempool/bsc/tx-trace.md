---
description: 介紹BlockRazor BSC Tx Trace的服務、應用場景以及接入方法
---

# Tx Trace

### BSC Tx Trace是什麼

Tx Trace 是 BlockRazor 提供的交易傳播路徑觀測工具，用於查詢指定交易在全球網絡中的傳播路徑、到達時間和跨區域時延分布。

對於交易系統來說，很多問題並不能只通過鏈上回執判斷。例如，一筆交易雖然最終成功上鏈，但它最早出現在什麼區域、在不同區域之間傳播花了多長時間，這些都無法僅從鏈上結果中直接看出。基於 [BEF](../../../he-xin-ji-shu/blockchain-edge-fabric.md)，Tx Trace 可以幫助用戶從更接近網絡傳播層的視角觀察交易行為，把原本只能依靠經驗判斷的“延遲問題”或“區域差異問題”，轉化為可以進行分析的數據問題。

### BSC Tx Trace的應用場景

**交易延遲排查：**&#x7576;交易發送結果異常、表現不穩定，或實際執行效果與預期不一致時，可以通過 Tx Trace 查看交易在全球網絡中的傳播路徑與時間差，輔助判斷問題是否出在網絡傳播環節。

**多區域部署評估：**&#x7576;團隊在多區域部署Bot 或發送服務時，可以利用 Tx Trace 對比交易在不同區域的進入時間和傳播效果，評估當前部署是否真正帶來更好的網絡覆蓋和傳播表現。

**高頻交易鏈路優化：**&#x5C0D;於依賴時機和速度的交易系統，可以通過 Tx Trace 分析交易在不同區域的擴散表現，為鏈路優化提供參考。

### 常見問題

<details>

<summary><strong>Tx Trace 可以追踪哪些交易？</strong></summary>

Tx Trace 主要適用於查詢已經進入公開傳播過程的交易在不同區域之間的傳播情況、到達時間和時延分布。如果某筆交易沒有進入公共傳播路徑，或者不在可查詢的有效時間窗口內，Tx Trace 無法提供對應的傳播結果。

</details>

<details>

<summary><strong>Tx Trace 和 Public Mempool 有什麼區別？</strong></summary>

兩者的定位不同：

* **Public Mempool** 是實時訂閱服務，用於低延遲接收公開傳播中的 pending 交易，重點是“盡早看到交易”
* **Tx Trace** 是传播观测工具，用于查询指定交易在全球网络中的传播路径与跨区域时延分布，重点是“看清交易是如何传播的”

</details>

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


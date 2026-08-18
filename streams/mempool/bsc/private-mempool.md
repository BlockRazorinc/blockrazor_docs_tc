---
description: 介紹BlockRazor BSC Private Mempool的服務、應用場景、優勢以及接入方法
metaLinks:
  canonical: private-mempool.md
  alternates:
    - >-
      https://app.gitbook.com/s/jbyfG8gOgcdsK3wVxNdQ/streams/mempool/bsc/private-mempool
---

# BSC Private Mempool

### BSC Private Mempool是什麼

BSC Private Mempool 是 BlockRazor 提供的私有交易流訂閱服務，用於獲取 BlockRazor RPC 的私有訂單流數據。

與 Public Mempool 訂閱公開傳播中的 pending 交易不同，Private Mempool 關注的是未進入公開傳播路徑的私有交易數據。這些數據通過 SSE 協議推送，方便用戶直接在策略系統中進行解析、篩選和後續處理。Private Mempool 會對交易內容做統一脫敏，僅披露被授權公開的交易字段。在保留策略分析所需的關鍵信息的同時兼顧數據隱私。

### BSC Private Mempool的應用場景

Private Mempool 適用於希望圍繞私有訂單流進行監控、判斷和策略執行的用戶，常見場景包括 backrunning、copy trading 和 sniping。

* **Backrunning:** 當私有訂單流中出現可能引發套利空間的交易時，用戶可以基於這些信號構造 backrun 交易，並在目標交易之後完成策略執行。
* **Copy Trading:** 當私有訂單流中出現目標地址、策略賬戶或特定類型交易時，用戶可以更早識別這些跟單信號，並圍繞買入、賣出、加倉或調倉等動作構建跟隨策略。
* **Sniping:** 在新池創建、流動性注入、代幣開盤或特定目標交易即將觸發市場變化時，盡早從私有訂單流中識別這些關鍵信號，為快速入場、信號跟隨和其他時機敏感型策略提供更早的響應窗口。

### 為什麼選擇BSC Private Mempool

在 BSC 場景中，很多高價值交易並不會出現在公開 Mempool 中，而是通過 BlockRazor RPC 提供的私有通道上鏈。對於希望圍繞這類交易構建策略的用戶來說，等到交易最終上鏈後再捕捉信號，往往已經錯過更有價值的處理時機。

Private Mempool 依賴 [BEF](../../../he-xin-ji-shu/blockchain-edge-fabric.md) 為不同區域的用戶提供私有訂單流接入能力，讓用戶能夠圍繞私有交易本身做更早的分析與決策。

### 快速開始

{% stepper %}
{% step %}
**採購BSC Private Mempool**

前往[訂閱](https://blockrazor.io/#/pricing)頁面採購
{% endstep %}

{% step %}
**獲取Auth**

詳見 [Authentication](../../../get-started/authentication.md)
{% endstep %}

{% step %}
**訂閱BSC Private Mempool**

詳見 [請求示例](private-mempool.md#qing-qiu-shi-li)
{% endstep %}

{% step %}
**構建 & 提交bundle**

詳見 [Bundle](../../../transaction-submission/rpc/bsc/orderflow-auction.md)
{% endstep %}
{% endstepper %}

### 價格

價格為$100 / 日和$1000 / 月。每個地區允許訂閱2條數據流。<a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=bsc&#x26;serviceId=bsc_private_mempool&#x26;billing=day" class="button primary small">訂閱</a>

### 端點

{% hint style="info" %}
* 請將訂閱BSC Private Mempool的域名與發送bundle的域名保持一致。如訂閱https://jp-bscscutum.blockrazor.xyz/stream，則將bundle發送至https://jp-bscscutum.blockrazor.xyz
* 不同地區推送的隱私數據流不同，建議同時訂閱 4 個端點
{% endhint %}

<table><thead><tr><th width="107.42578125">地區</th><th>端點</th></tr></thead><tbody><tr><td>東京</td><td>https://jp-bscscutum.blockrazor.xyz/stream</td></tr><tr><td>紐約</td><td>https://us-bscscutum.blockrazor.xyz/stream</td></tr><tr><td>法蘭克福</td><td>https://ger-bscscutum.blockrazor.xyz/stream</td></tr><tr><td>都柏林</td><td>https://ire-bscscutum.blockrazor.xyz/stream</td></tr></tbody></table>

### 請求示例

```json
curl -X GET \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer <token>" \
    --data '{}' \
    https://jp-bscscutum.blockrazor.xyz/stream
```

```
https://jp-bscscutum.blockrazor.xyz/stream?token=<token>
```

### **數據流**類型

**Raw Bundle**

Raw Bundle是指尚未被跟隨策略交易的bundle，Raw Bundle中的交易來源於兩個渠道：

* 通過RPC `eth_sendRawTransaction`提交的交易，會由BlockRazor RPC自動構建為bundle推送至Private Mempool，該場景下的Raw Bundle僅包含一筆交易；
* 通過RPC `eth_sendMevBundle`提交的Raw Bundle，交易來自於公開內存池或自行構建，該場景下的Raw Bundle至多可包含50筆交易。

**Followed Bundle**

客戶端在對Raw Bundle執行backrun、跟單或狙擊策略後，可以通過開啟hint繼續將bundle披露至Private Mempool以執行嵌套的backrun策略。此時Private Mempool中的該類Bundle稱為Followed Bundle，包含raw bundle中的全部交易，以及1筆策略交易。

### 數據流結構

**Bundle**

<table><thead><tr><th width="210">參數</th><th width="166">格式</th><th>備注</th></tr></thead><tbody><tr><td>chainID</td><td>string</td><td>ETH: 1, BSC:56</td></tr><tr><td>hash</td><td>string</td><td>bundle hash,Private Mempool數據推流統一以bundle形式呈現</td></tr><tr><td><a href="private-mempool.md#tx">txs</a></td><td><a href="private-mempool.md#tx">[]tx</a></td><td>bundle中包含的交易</td></tr><tr><td>nextBlockNumber</td><td>uint64</td><td>該bundle所在區塊號</td></tr><tr><td>maxBlockNumber</td><td>uint64</td><td>該bundle有效的最大區塊號</td></tr><tr><td>proxyBidContract</td><td>string</td><td>bundle競拍代理合約地址，競拍方法调用詳見 <a href="/broken/pages/9FTJWMwZJ35WZYpzRmdD">Backrun</a></td></tr><tr><td>refundAddress</td><td>string</td><td>競拍方法的入參， 競拍金額將按比例返利至refundAddress</td></tr><tr><td>refundCfg</td><td>int</td><td>競拍方法的入參</td></tr><tr><td>state</td><td><a href="private-mempool.md#state">[]state</a></td><td>虛擬機狀態對象的數據變化，<a href="private-mempool.md#shu-ju-liu-shi-li-bao-han-state">查看數據流示例</a></td></tr></tbody></table>

**txs**

<table><thead><tr><th width="212">參數</th><th width="166">格式</th><th>備注</th></tr></thead><tbody><tr><td>hash</td><td>string</td><td>交易哈希</td></tr><tr><td>from</td><td>string</td><td>交易的發起方地址</td></tr><tr><td>to</td><td>string</td><td>交易的接收方地址</td></tr><tr><td>value</td><td>hex</td><td>交易value</td></tr><tr><td>nonce</td><td>uint64</td><td>交易nonce</td></tr><tr><td>calldata</td><td>string</td><td>交易calldata</td></tr><tr><td>functionSelector</td><td>string</td><td>合約函數簽名哈希的前4個字節</td></tr><tr><td>logs</td><td><a href="private-mempool.md#log">[]log</a></td><td>交易在執行過程中拋出的事件日誌</td></tr></tbody></table>

**log**

<table><thead><tr><th width="210">參數</th><th width="164">格式</th><th>備注</th></tr></thead><tbody><tr><td>address</td><td>string</td><td>触发事件的智能合约地址</td></tr><tr><td>topics</td><td>[]string</td><td>事件日志的topcis</td></tr><tr><td>data</td><td>string</td><td>非索引参数的存储区域</td></tr></tbody></table>

#### **state**

{% hint style="info" %}
默認數據推流中不包含state，如需獲取，請將訂閱地址修改為

Ethereum：https://ethscutum.blockrazor.xyz/stream?state=true

BSC：https://jp-bscscutum.blockrazor.xyz/stream?state=true
{% endhint %}

<table><thead><tr><th width="212">參數</th><th width="166">格式</th><th>備註</th></tr></thead><tbody><tr><td>"0x7C3b……3cb9E2"</td><td>[]string</td><td>數據發生變化的狀態對象地址，可以是一個EOA地址或智能合約地址</td></tr><tr><td>"0x935b……6cf608"</td><td>string</td><td>狀態對象數據發生變化的Key</td></tr><tr><td>"0x0000……3ffc00"</td><td>string</td><td>狀態對象數據變化後的Value</td></tr></tbody></table>

### 數據流示例（默認）

```json
{
    "chainID":"56"
    "hash":"0x2ba4c05436d4a48a0ce30341a3164b34b31c091a28ed62618f7b0512aba41f51" // bundle hash
    "txs":[{
          "hash":"0x2ba4c05436d4a48a0ce30341a3164b34b31c091a28ed62618f7b0512aba41f51"
          "from":"0xB4647b856CB9C3856d559C885Bed8B43e0846a47"
          "to":"0x0000000000000000000000000000000000001000"
          "value":"0x1c4eda9192000"
          "nonce":88036
          "calldata":"0xf340fa01000000000000000000000000b4647b856cb9c3856d559c885bed8b43e0846a47"
          "functionSelector":"0xe47d166c"
          "logs":[
              {
                "address": "0x6c1bcf1b99d9f0819459dad661795802d232437e",
                "topics": ["0xc42079f94a6350d7e6235f29174924f928cc2ac818eb64fed8004e115fbcca67", "0x0000000000000000000000000000000000000000000000000000000000000000", "0x0000000000000000000000000000000000000000000000000000000000000000"],
                "data": "0x"
              }
              {
                "address": "0x6c1bcf1b99d9f0819459dad661795802d232437e",
                "topics": ["0xc42079f94a6350d7e6235f29174924f928cc2ac818eb64fed8004e115fbcca67", "0x0000000000000000000000000000000000000000000000000000000000000000", "0x0000000000000000000000000000000000000000000000000000000000000000"],
                "data": "0x"
              }
          ]
    }]
    "nextBlockNumber":39177841  //該bundle所在區塊號
    "maxBlockNumber":39177941  //該bundle有效的最大區塊號
    "proxyBidContract":"0x74Ce839c6aDff544139f27C1257D34944B794605" //Backrun競拍合約地址，調用合約的proxyBid方法可進行競拍
    "refundAddress":"0x6c1bcf1b99d9f0819459dad661795802d232437e", //返利接收地址，競拍金額將按比例返利至refundAddress
    "refundCfg":10380050 //返利配置
}
```

### 數據流示例（包含state）

{% hint style="info" %}
默認數據推流中不包含state，如需獲取，請將訂閱地址修改為

Ethereum：[https://eth.blockrazor.xyz/stream?state=true](https://eth.blockrazor.xyz/stream?state=true)

BSC：[https://bsc.blockrazor.xyz/stream?state=true](https://bsc.blockrazor.xyz/stream?state=true)
{% endhint %}

```json
{
    "chainID":"56"
    "hash":"0x2ba4c05436d4a48a0ce30341a3164b34b31c091a28ed62618f7b0512aba41f51" // bundle hash
    "txs":[{
          "hash":"0x2ba4c05436d4a48a0ce30341a3164b34b31c091a28ed62618f7b0512aba41f51"
          "from":"0xB4647b856CB9C3856d559C885Bed8B43e0846a47"
          "to":"0x0000000000000000000000000000000000001000"
          "value":"0x1c4eda9192000"
          "nonce":88036
          "calldata":"0xf340fa01000000000000000000000000b4647b856cb9c3856d559c885bed8b43e0846a47"
          "functionSelector":"0xe47d166c"
          "logs":[
              {
                "address": "0x6c1bcf1b99d9f0819459dad661795802d232437e",
                "topics": ["0xc42079f94a6350d7e6235f29174924f928cc2ac818eb64fed8004e115fbcca67", "0x0000000000000000000000000000000000000000000000000000000000000000", "0x0000000000000000000000000000000000000000000000000000000000000000"],
                "data": "0x"
              }
              {
                "address": "0x6c1bcf1b99d9f0819459dad661795802d232437e",
                "topics": ["0xc42079f94a6350d7e6235f29174924f928cc2ac818eb64fed8004e115fbcca67", "0x0000000000000000000000000000000000000000000000000000000000000000", "0x0000000000000000000000000000000000000000000000000000000000000000"],
                "data": "0x"
              }
          ]
    }]
    "nextBlockNumber":39177841  //該bundle所在區塊號
    "maxBlockNumber":39177941  //該bundle有效的最大區塊號
    "proxyBidContract":"0x74Ce839c6aDff544139f27C1257D34944B794605" //Scutum的bundle競拍合約地址，調用合約的proxyBid方法可進行競拍
    "refundAddress":"0x6c1bcf1b99d9f0819459dad661795802d232437e", //返利接收地址，競拍金額將按比例返利至refundAddress
    "refundCfg":10380050 //返利配置
    "state": {
	"0x7C3b00CB3B40Cc77d88329A58574E29cFA3cb9E2": { //數據發生變化的狀態對象地址，可以是一個EOA地址或智能合約地址      
	      "0x935b605129a438014d6ae0692623c5e1fbf83d5a631f5a0f8489a301966cf608": "0x00000000000000000000000000000000000000000000010c86a7e418723ffc00"
	      //"狀態對象數據發生變化的Key":"狀態對象數據變化後的Value"
            }      
      }
}
```


---
description: 介紹BlockRazor Robinhood Chain Transaction Sending、Benchmark和價格
metaLinks:
  canonical: ./
  alternates:
    - >-
      https://app.gitbook.com/s/jbyfG8gOgcdsK3wVxNdQ/transaction-submission/transaction-sending/robinhood-chain
---

# Robinhood Chain Transaction Sending

### Robinhood Chain是什么 <a href="#what-is-robinhood-chain" id="what-is-robinhood-chain"></a>

Robinhood Chain 是一個以太坊 Layer 2 網絡，基於 Arbitrum 構建，專為 RWA 優化，包括股票和 ETF 等資產。它支持全天候的鏈上交易以及用戶自主托管，讓用戶無需依賴傳統金融中介即可直接在區塊鏈上持有、交易和管理資產。

### Benchmark

我們將測試客戶端部署於AWS的法蘭克福、俄亥俄和日本地區，向對應地區的 BlockRazor 端點和官方 RPC 端點發送 50 組 nonce 相同的交易。每組交易除接收地址（用於區分發送通道）外，其餘所有參數均保持完全一致。

我們通過比較兩種通道最終成功上鏈交易的佔比來評估性能，上鏈佔比越高，代表交易傳播和執行速度越快。基準測試結果如下。

<table><thead><tr><th width="157.453125">地區</th><th>BlockRazor上鍊率</th><th>Robinhood上鏈率</th></tr></thead><tbody><tr><td>法蘭克福</td><td>88%</td><td>12%</td></tr><tr><td>俄亥俄</td><td>50%</td><td>50%</td></tr><tr><td>日本</td><td>96%</td><td>4%</td></tr></tbody></table>

### 價格

<table><thead><tr><th width="155.45703125">用戶類型</th><th width="244.66796875">限流</th><th>價格</th></tr></thead><tbody><tr><td>新註冊用戶</td><td><p><code>eth_sendRawTransaction</code></p><p>1 Tx / 5s<br><br><code>eth_sendBatch</code><br>1 Tx / 5s</p></td><td>免費</td></tr><tr><td>付費用戶</td><td><code>eth_sendRawTransaction</code><br>5 Txs / 1s<br><br><code>eth_sendBatch</code><br>5 Txs / 1s</td><td>$100 / 日<br>$1000 / 月<br><br><a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=robinhood&#x26;serviceId=robinhood_rpc_send_tx&#x26;billing=day" class="button primary small">訂閱</a></td></tr></tbody></table>




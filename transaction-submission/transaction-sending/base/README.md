---
description: 介紹BlockRazor Base Transaction Sending 模式以及API接入文檔
metaLinks:
  canonical: ./
  alternates:
    - >-
      https://app.gitbook.com/s/jbyfG8gOgcdsK3wVxNdQ/transaction-submission/transaction-sending/base
---

# Base Transaction Sending

Base Transaction Sending 是 BlockRazor 為 Base 提供的交易發送接口，用於將已簽名的原始交易發送到鏈上。目前提供 gRPC 與 HTTPS 兩種接入方式。

### 價格

<table><thead><tr><th width="155.45703125">用戶類型</th><th width="208.93359375">限流</th><th>價格</th></tr></thead><tbody><tr><td>新註冊用戶</td><td>1 Tx / 5s</td><td>免費</td></tr><tr><td>付費用戶</td><td>5 Txs / 1s</td><td>$100 / 日<br>$1000 / 月<br><br><a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=base&#x26;serviceId=base_rpc_send_tx&#x26;billing=day" class="button primary small">訂閱</a></td></tr></tbody></table>

### 為什麼選擇BlockRazor Base Transaction Sending

對於 Wallets 和 DEX 來說，交易發送能力不只是“能夠發出交易”，還關係到不同地區用戶的訪問質量、發送鏈路的穩定性，以及高峰時段下的整體體驗。直接連接 Base 官方 Sequencer 雖然能夠滿足基礎發送需求，但對於服務全球用戶、關注跨區域表現和生產環境穩定性的業務來說，發送路徑本身仍然存在進一步優化空間。BlockRazor Base Transaction Sending 和官網原生交易發送服務的對比詳見 [Benchmark](https://blockrazor.io/zh/blog/20250922basebenchmark/)

#### **全球多點部署**

相比直接連接 Base 官方 Sequencer，BlockRazor Base RPC 在多個核心區域提供發送入口，適合 Wallet 和 DEX 根據自身服務部署位置選擇更接近的 endpoint。對於服務全球用戶的業務來說，更接近業務服務器和核心用戶區域的發送入口，通常有助於縮短請求路徑，提升交易發送效率。

#### **跨洲專線**

BlockRazor Base RPC 的優化重點不僅在入口節點部署，也在區域 relay 之間的數據傳輸鏈路。相比完全依賴普通公網路由的區域間轉發方式，跨洲專線能夠大幅降低 relay 間傳輸的路由波動、擁塞和鏈路抖動影響，提升交易請求在整體發送鏈路中的穩定性和一致性。

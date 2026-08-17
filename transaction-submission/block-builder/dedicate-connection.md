---
hidden: true
noIndex: true
---

# Dedicate Connection

Dedicate Connection 是一項面向低時延交易提交場景的專用連接服務，支持將客戶端與服務端部署在同機房，並向客戶開放 Block Builder 的 `ip:port` 進行裸調。相比通過 HTTP 域名入口提交交易，Dedicate Connection 提供的是一條更直接、更底層的 Builder 接入方式，盡可能減少共享入口、代理層和額外封裝帶來的開銷，縮短客戶端到 Builder 的實際網絡路徑，從而提升提交鏈路的穩定性、確定性和可控性。

### 和 Fast Submit 的區別

Dedicate Connection 和 Fast Submit 都用於提升向 BSC Block Builder 提交交易的效率與穩定性，但定位不同。Fast Submit 是優化後的標準接入方案，開發者通常只需要使用專屬 HTTP 域名，即可通過更直接的網絡路徑、優化後的跨區域鏈路以及服務端請求處理優化，獲得更快、更穩的提交體驗，適合希望在較低接入成本下提升提交鏈路質量的場景。

Dedicate Connection 則更強調最短路徑和鏈路控制，開放 Builder 的 ip:port，支持客戶端與服務端同機房部署，適合對時延極度敏感、希望自行控制底層連接策略、並具備較強基礎設施能力的團隊。

### 價格

$1500 / 月，請前往[訂閱](https://blockrazor.io/#/pricing)頁面採購，訂閱完成後請[聯繫](https://discord.com/invite/qqJuwRb8Nh)我們開始接入

### 接入方式

1. 前往[訂閱](https://blockrazor.io/#/pricing)頁面採購，訂閱完成後請[聯繫](https://discord.com/invite/qqJuwRb8Nh)我們
2. 獲取 Block Builder ip:port，並按要求完成機房部署或網絡連通準備。
3. 完成後，客戶端即可直接連接對應地址進行請求發送，並在實際部署環境中測試時延、穩定性和提交效果。

### 注意事项

Dedicate Connection 提升的是客戶端到 Builder 的連接質量，不保證交易一定會被打包。最終結果仍然會受到市場競爭、Builder 處理策略、區塊空間、鏈上擁堵以及交易本身質量等因素影響。此外，Dedicate Connection 更適合對網絡路徑和連接控制有明確要求的場景，接入前建議結合自身部署條件、請求模式和運維能力進行評估。

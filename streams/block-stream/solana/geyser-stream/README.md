---
description: 介紹BlockRazor Solana Geyser Stream的服務、應用場景、關鍵特性以及接入方法
---

# Geyser Stream

### **什麼是Geyser Stream**

Geyser 是 Solana 驗證者的插件機制，可將鏈上的 `account`、`slot`、`block` 和 `transaction` 等數據實時輸出到外部系統。Geyser Stream 是 BlockRazor 基於 Yellowstone gRPC 推出的 Solana 高性能數據流服務，支持客戶端通過 gRPC 以極低延遲訂閱結構化的實時鏈上數據。

### **Geyser Stream的應用場景**

* **交易分析：** 監聽指定賬戶的交易，用於跟單聰明錢賬戶交易，或捕捉 pump.fun 新幣等早期信號
* **賬戶分析：** 監聽指定賬戶餘額變化，用於跟蹤 Raydium、Orca、Jupiter 等 DEX 池子賬戶狀態，進一步計算代幣對價格
* **區塊與槽位分析：** 監聽區塊和槽位更新，用於觀察網絡共識進度與節點健康狀態

### **Geyser Stream的關鍵特性**

* **高性能：** 基於高性能服務器和低延遲鏈路，實時向客戶端推送 gRPC 數據流
* **數據完整性：** 支持最近 500 個 slot（約 200 秒）的交易重放，斷連後可快速補齊數據
* **高穩定性：** 服務採用多雲多實例部署，支持無縫故障轉移，保障長時間穩定運行

### 常见问题

<details>

<summary>Geyser Stream和Shred Stream有什麼區別</summary>

兩者的核心區別在於**數據層級**和**使用門檻**。Shred Stream 傳輸的是更底層的原始 `shred`，鏈路更短，延遲更低，適合極度追求時效的 Bots、RPC 和 Validators，但接入方需要自己做重組和解析；Geyser Stream 傳輸的是結構化的 `account`、`slot`、`block` 和 `transaction` 數據，客戶端可直接通過 gRPC 訂閱，接入更簡單，也更適合賬戶監聽、交易分析和穩定生產環境。若你要的是**更早的交易信號**，優先選 [Shred Stream](../shred-stream.md)；若你要的是**更完整、易消費的實時鏈上數據**，優先選 [Geyser Stream](./)。

</details>

### 快速开始

{% stepper %}
{% step %}
**采购Geyser Stream**

前往[订阅](https://blockrazor.io/#/pricing)页面采购Geyser Stream
{% endstep %}

{% step %}
**获取Auth**

详见 [Authentication](../../../../get-started/authentication.md)
{% endstep %}

{% step %}
**集成Geyser Stream**

多語言客戶端集成詳見：

* [CLI](cli.md)
* [Go](go.md)
* [Rust](rust.md)
* [JS](js.md)
{% endstep %}
{% endstepper %}

### 端點

<table><thead><tr><th width="171.1796875">地區</th><th>端點</th></tr></thead><tbody><tr><td>東京</td><td>geyserstream-tokyo.blockrazor.xyz:443</td></tr></tbody></table>

### 價格

{% hint style="info" %}
Geyser Stream按週期（單位：月）流量收費，價格在新採購、續費、新增流量等場景下一致。<a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=solana&#x26;serviceId=solana_geyser_stream&#x26;billing=month" class="button primary small">訂閱</a>
{% endhint %}

<table data-search="false"><thead><tr><th>週期流量</th><th>折扣</th><th>折後價 / 週期</th></tr></thead><tbody><tr><td>5 TiB</td><td>100%</td><td>$250</td></tr><tr><td>10 TiB</td><td>100%</td><td>$500</td></tr><tr><td>50 TiB</td><td>100%</td><td>$2500</td></tr><tr><td>100 TiB</td><td>95%</td><td>$4750</td></tr><tr><td>150 TiB</td><td>90%</td><td>$6750</td></tr><tr><td>200 TiB</td><td>85%</td><td>$8500</td></tr><tr><td>250 TiB</td><td>80%</td><td>$10000</td></tr></tbody></table>

#### 採購說明

<table><thead><tr><th width="101.15625"></th><th>新採購</th><th>續費</th><th>增加週期流量</th></tr></thead><tbody><tr><td>場景</td><td>首次採購或流量過期後再次採購Geyser Stream</td><td>在Geyser Stream流量週期內續費</td><td>Geyser Stream週期流量提前消耗殆盡，需補充流量</td></tr><tr><td>結果</td><td>根據採購時長生成週期流量。</td><td>延遲流量週期</td><td>不延長有效期，只增加週期內的流量</td></tr><tr><td>例子</td><td>新採購5 TiB 1個週期，則可用流量為：<br>- 起始週期：5 TiB</td><td>採購5 TiB 1個週期，在起始週期內續費1個週期，則可用流量為：<br>- 起始週期：5 TiB<br>- 第2週期：10 TiB</td><td>採購5 TiB 1個週期，同時續費10 Tib 1個週期，起始週期內發現流量消耗殆盡，選擇在起始週期新增流量 5 Tib<br>- 起始週期：5 Tib + 5 Tib<br>- 第2週期：10 Tib</td></tr></tbody></table>

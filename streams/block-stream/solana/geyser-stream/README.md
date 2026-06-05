# Geyser Stream

### 產品介紹

**什麼是Geyser Stream**

Geyser是Solana驗證者的插件機制，通過該插件，Solana的賬戶、槽位、區塊和交易數據可以實時傳輸到外部數據存儲介質。Geyser Stream是BlockRazor基於Yellowstone gRPC(Geyser插件)推出的Solana高性能數據流服務，支持客戶端通過gRPC協議以極低延遲訂閱到實時Solana數據流。

**Geyser Stream主要有什麼用**

交易分析：監聽指定賬戶的交易，可用於跟單聰明錢賬戶交易或狙擊pump.fun新幣

賬戶分析：監聽指定賬戶餘額變化，可用於監聽DEX(如Raydium、Orca 或 Jupiter)指定池子賬戶的餘額，以計算代幣對價格

區塊 & 槽位分析：監聽區塊和槽位，可用於獲取網絡共識和健康狀態

**Geyser Stream的關鍵特性**

高性能：Geyser Stream部署於高性能服務器以極低延遲實時向客戶端推送gRPC數據流

數據完整性：支持最近500個slot(200 s)的交易重放，保證“斷連續傳”

高穩定性：Geyser Stream服務多雲多實例運行，支持無縫故障轉移，保障長時間穩定

### 端點

<table><thead><tr><th width="171.1796875">地區</th><th>端點</th></tr></thead><tbody><tr><td>法蘭克福</td><td>geyserstream-frankfurt.blockrazor.xyz:443</td></tr><tr><td>東京</td><td>geyserstream-tokyo.blockrazor.xyz:443</td></tr></tbody></table>

### 價格

{% hint style="info" %}
Geyser Stream按週期（單位：月）流量收費，價格在新採購、續費、新增流量等場景下一致
{% endhint %}

| 週期流量    | 折扣   | 折後價 / 週期 |
| ------- | ---- | -------- |
| 5 TiB   | 100% | $250     |
| 10 TiB  | 100% | $500     |
| 50 TiB  | 100% | $2500    |
| 100 TiB | 95%  | $4750    |
| 150 TiB | 90%  | $6750    |
| 200 TiB | 85%  | $8500    |
| 250 TiB | 80%  | $10000   |

#### 採購說明

<table><thead><tr><th width="101.15625"></th><th>新採購</th><th>續費</th><th>增加週期流量</th></tr></thead><tbody><tr><td>場景</td><td>首次採購或流量過期後再次採購Geyser Stream</td><td>在Geyser Stream流量週期內續費</td><td>Geyser Stream週期流量提前消耗殆盡，需補充流量</td></tr><tr><td>結果</td><td>根據採購時長生成週期流量。</td><td>延遲流量週期</td><td>不延長有效期，只增加週期內的流量</td></tr><tr><td>例子</td><td>新採購5 TiB 1個週期，則可用流量為：<br>- 起始週期：5 TiB</td><td>採購5 TiB 1個週期，在起始週期內續費1個週期，則可用流量為：<br>- 起始週期：5 TiB<br>- 第2週期：10 TiB</td><td>採購5 TiB 1個週期，同時續費10 Tib 1個週期，起始週期內發現流量消耗殆盡，選擇在起始週期新增流量 5 Tib<br>- 起始週期：5 Tib + 5 Tib<br>- 第2週期：10 Tib</td></tr></tbody></table>

### 集成Geyser Stream

Auth獲取步驟詳見[Authentication](../../../../authentication.md)

多語言客戶端集成詳見：

* [CLI](cli.md)
* [Go](go.md)
* [Rust](rust.md)
* [JS](js.md)


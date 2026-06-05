---
description: 本頁主要介紹BlockRazor服務的定價
---

# 價格

{% hint style="info" %}
BlockRazor定價體系全面升級，將原先多鏈多等級體系中的功能特性拆分為獨立服務，支持單獨訂閱或與其他服務組合式採購，在降低訂閱門檻的同時精准匹配用戶需求，详见[付费订阅服务](jia-ge.md#fu-fei-ding-yue-fu-wu)。此外，新注册用户仍可零门槛享有Solana, BSC, Etherem和Base上的多模式交易发送服务，详见[新注册用户](jia-ge.md#xin-zhu-ce-yong-hu)。
{% endhint %}

## 付費订阅服務

#### 自選服務

{% hint style="info" %}
用戶可跨鏈自由組合採購服務，Stream類服務支持配置數量或流量包規格，同時支持按天購買，詳見[訂閱](https://blockrazor.io/#/pricing)
{% endhint %}

{% tabs %}
{% tab title="BSC" %}
<table><thead><tr><th width="194.09765625">服務</th><th>描述</th><th>價格</th></tr></thead><tbody><tr><td>Fast-Tx</td><td>基於高性能網絡傳播交易和交易batch</td><td>$500 / 月</td></tr><tr><td>Public Mempool</td><td>訂閱高性能網絡交易數據</td><td>$300 / 條 / 月</td></tr><tr><td>Private Mempool</td><td>訂閱BlockRazor RPC的訂單流數據</td><td>$1000 / 月</td></tr><tr><td>Block Stream</td><td>訂閱高性能網絡區塊數據</td><td>$500 / 條 / 月</td></tr><tr><td>Node Stream</td><td>低延遲同步世界狀態</td><td>$800 / 個 / 月</td></tr><tr><td>Network Fee Stream</td><td>獲取BSC gas price數據</td><td>$500 / 月</td></tr><tr><td>Dedicate Connection to Builder</td><td>直連BlockRazor Block Builder</td><td>$1500 / 月</td></tr><tr><td>Bundle Tracing &#x26; Explorer</td><td>Block Builder bundle追蹤與瀏覽</td><td>$1500 / 月</td></tr><tr><td>Tx Trace</td><td>監控交易傳播路徑和跨區域延遲分佈</td><td>$500 / 月</td></tr></tbody></table>
{% endtab %}

{% tab title="Solana" %}
<table><thead><tr><th width="175.34765625">服务</th><th>描述</th><th>價格</th></tr></thead><tbody><tr><td>Shred Stream</td><td>低延遲傳輸shred</td><td>$500 / 條 / 月</td></tr><tr><td>Geyser Stream</td><td>實時傳輸Solana鏈上數據，包含account, slot, block和transaction等</td><td>5 TiB - $250 / 月<br>10 TiB - $500 / 月<br>50 TiB - $250 / 月<br>100 TiB - $4750 / 月<br>150 TiB - $6750 / 月<br>200 TiB - $8500 / 月<br>250 TiB - $10000 / 月</td></tr><tr><td>Network Fee Stream</td><td>獲取Solana的priority fee和tip數據</td><td>$300 / 月</td></tr></tbody></table>
{% endtab %}

{% tab title="Base" %}
<table><thead><tr><th width="145.37890625">服務</th><th>描述</th><th>價格</th></tr></thead><tbody><tr><td>FlashBlock Stream</td><td>低延遲獲取Base FlashBlock數據</td><td>$250 / 條 / 月</td></tr><tr><td>Block Stream</td><td>低延遲獲取Base Block數據</td><td>$300 / 條 / 月</td></tr><tr><td>RPC發送交易</td><td>低延遲高TPS發送Base交易上鏈</td><td>$1000 / 月</td></tr></tbody></table>
{% endtab %}

{% tab title="General" %}
| 服務                | 描述     | 價格        |
| ----------------- | ------ | --------- |
| Dedicated Channel | 專屬技術支持 | $1500 / 月 |
{% endtab %}
{% endtabs %}

#### 極速服務包

{% hint style="info" %}
相比單項服務的組合式採購，用戶可通過極速服務包以更低價格完成打包採購。目前極速服務包主要包含BSC服務，整體訂閱價格&#x70BA;**$1250 / 月**，具體服務如下：
{% endhint %}

<table><thead><tr><th width="177.390625">服務</th><th>描述</th></tr></thead><tbody><tr><td>Public Mempool</td><td>訂閱高性能網絡交易數據</td></tr><tr><td>Block Stream</td><td>訂閱高性能網絡區塊數據</td></tr><tr><td>Node Stream</td><td>低延遲同步世界狀態</td></tr><tr><td>Call Bundle</td><td>向Block Builder提交請求接收bundle模擬結果</td></tr><tr><td>Fast Submit</td><td>以更低延遲、更高穩定性向Block Builder提交交易</td></tr><tr><td>Tx Trace</td><td>監控交易傳播路徑和跨區域延遲分佈</td></tr></tbody></table>

#### 折扣說明

折扣和訂閱週期的關係如下：

<table><thead><tr><th width="314.41796875">訂閱週期</th><th width="328.90234375">折扣</th></tr></thead><tbody><tr><td>1個月</td><td>無折扣</td></tr><tr><td>3個月</td><td>5%折扣</td></tr><tr><td>6個月</td><td>10%折扣</td></tr><tr><td>9個月</td><td>15%折扣</td></tr><tr><td>12個月及以上</td><td>20%折扣</td></tr></tbody></table>



## 新註冊用戶

{% hint style="info" %}
BlockRazor面向新註冊用戶提供Solana, BSC, Etherem和Base上的多模式交易发送服务，包括RPC、Fast、Bundle、Block Builder等模式。
{% endhint %}

#### RPC

RPC模式提供鏈原生的`eth_sendRawTransaction`方法和其他標準JSON RPC方法

<table><thead><tr><th width="125.16015625">鏈</th><th>方法</th><th>限流</th></tr></thead><tbody><tr><td>BSC</td><td><ul><li><code>eth_sendRawTransaction</code></li><li>其他JSON RPC方法</li></ul></td><td>-</td></tr><tr><td>Ethereum</td><td><ul><li><code>eth_sendRawTransaction</code></li><li>其他JSON RPC方法</li></ul></td><td>-</td></tr><tr><td>Base</td><td><ul><li><code>eth_sendRawTransaction</code></li></ul></td><td>1 Tx / 5s</td></tr></tbody></table>

#### Fast

Fast模式基於BlockRazor的全球分佈式加速網絡和高質押驗證者提供交易快速上鏈服務。Fast模式下發送交易時需要在交易內部將tip轉至指定地址。

<table><thead><tr><th width="113.1328125">鏈</th><th>方法</th><th>限流</th></tr></thead><tbody><tr><td>Solana</td><td><ul><li><code>Send Transaction</code></li><li><code>Send Transaction</code> v2</li></ul></td><td>默認為3 TPS</td></tr><tr><td>BSC</td><td><ul><li><code>eth_sendRawTransaction</code></li><li><code>eth_sendRawTransaction</code> v2</li></ul></td><td>默認為10 TPS</td></tr><tr><td>Base</td><td><ul><li><code>eth_sendRawTransaction</code></li></ul></td><td>默認為10 TPS</td></tr></tbody></table>

_<mark style="color:$primary;">**注：新注冊用戶也可以使用不需要Tip的交易發送服務，即Fast-Tx服務中的**</mark><mark style="color:$primary;">`SendTx`</mark><mark style="color:$primary;">**，**</mark><mark style="color:$primary;">**TPS為10 txs / 5s以及10 txs / day。**</mark>_

#### Bundle

Bundle模式支持項目方發送交易捆綁包，支持Searcher在訂單流拍賣中發送bid。

<table><thead><tr><th width="119.2890625">鏈</th><th>方法</th><th>限流</th></tr></thead><tbody><tr><td>BSC</td><td>eth_sendMevBundle(項目方)<br>eth_sendMevBundle(Searcher)</td><td>-</td></tr><tr><td>Ethereum</td><td>eth_sendBundle(項目方)<br>eth_sendBid(Searcher)</td><td>-</td></tr></tbody></table>

#### Block Builder

Block Builder是BlockRazor的BSC區塊構建服務。Block Builder基於全球多地部署和驗證者建立低延遲通信，運行多種區塊構建算法，實現高勝率出塊。新註冊用戶可免費向Block Builder提交bundle和隱私交易。

<table><thead><tr><th width="125.484375">鏈</th><th width="288.10546875">方法</th><th>限流</th></tr></thead><tbody><tr><td>BSC</td><td><ul><li>eth_sendBundle</li><li>eth_sendPrivateTransaction</li></ul></td><td>-</td></tr></tbody></table>


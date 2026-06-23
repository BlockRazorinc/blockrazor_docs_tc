---
description: 介紹BlockRazor服務的定價
---

# 價格

{% hint style="info" %}
* BlockRazor目前支持組合式訂購多鏈服務或以優惠價格訂購極速服務包。
* 此外，新注册用户仍可零门槛享有Solana, BSC, Etherem和Base上的多模式交易发送服务，详见[新注册用户](jia-ge.md#xin-zhu-ce-yong-hu)。
{% endhint %}

## 付費订阅服務

#### 自選服務

{% tabs %}
{% tab title="BSC" %}
<table><thead><tr><th width="194.09765625">服務</th><th>描述</th><th>按月購買</th><th>按日購買</th></tr></thead><tbody><tr><td><a href="../transaction-submission/fast/bsc/broadcast-tx.md">Broadcast Tx</a></td><td>低延遲傳播交易和交易batch</td><td>$500 / 月</td><td>$50 / 月</td></tr><tr><td><a href="../streams/mempool/bsc/public-mempool.md">Public Mempool</a></td><td>低延遲訂閱pending交易數據</td><td>$300 / 條 / 月</td><td>$30 / 條 / 月</td></tr><tr><td><a href="../streams/mempool/bsc/private-mempool.md">Private Mempool</a></td><td>訂閱BlockRazor RPC的隱私訂單流數據</td><td>$1000 / 月</td><td>$100 / 月</td></tr><tr><td><a href="../streams/block-stream/bsc/newblocks.md">Block Stream</a></td><td>低延遲訂閱BSC區塊數據</td><td>$500 / 條 / 月</td><td>$50 / 條 / 月</td></tr><tr><td><a href="../streams/node-stream/bsc/quan-jie-dian-tong-bu.md">Node Stream</a></td><td>低延遲同步世界狀態</td><td>$800 / 個 / 月</td><td>$80 / 個 / 月</td></tr><tr><td><a href="../streams/network-fee-stream/bsc/getgaspricestream.md">Network Fee Stream</a></td><td>獲取BSC gas price數據</td><td>$500 / 月</td><td>$50 / 月</td></tr><tr><td><a href="../transaction-submission/block-builder/trace-bundle.md">Bundle Tracing &#x26; Explorer</a></td><td>Block Builder bundle追蹤與瀏覽</td><td>$1500 / 月</td><td>$150 / 月</td></tr></tbody></table>
{% endtab %}

{% tab title="Solana" %}
<table><thead><tr><th width="150.8203125">服务</th><th width="149.66015625">描述</th><th>按月購買</th><th>按日購買</th></tr></thead><tbody><tr><td><a href="../streams/block-stream/solana/shred-stream.md">Shred Stream</a></td><td>低延遲傳輸shred</td><td>$500 / 條 / 月</td><td>$50 / 條 / 月</td></tr><tr><td><a href="../streams/block-stream/solana/geyser-stream/">Geyser Stream</a></td><td>實時傳輸Solana鏈上數據，包含account, slot, block和transaction等</td><td>5 TiB - $250 / 月<br>10 TiB - $500 / 月<br>50 TiB - $250 / 月<br>100 TiB - $4750 / 月<br>150 TiB - $6750 / 月<br>200 TiB - $8500 / 月<br>250 TiB - $10000 / 月</td><td>-</td></tr><tr><td><a href="../streams/network-fee-stream/solana/get-transactionfee.md">Network Fee Stream</a></td><td>獲取Solana的priority fee和tip數據</td><td>$300 / 月</td><td>$30 / 月</td></tr></tbody></table>
{% endtab %}

{% tab title="Base" %}
<table><thead><tr><th width="145.37890625">服務</th><th>描述</th><th>按月購買</th><th>按日購買</th></tr></thead><tbody><tr><td><a href="../streams/block-stream/base/get-flashblockstream/">FlashBlock Stream</a></td><td>低延遲獲取Base FlashBlock數據</td><td>$250 / 條 / 月</td><td>$25 / 條 / 月</td></tr><tr><td><a href="../streams/block-stream/base/get-blockstream.md">Block Stream</a></td><td>低延遲獲取Base Block數據</td><td>$300 / 條 / 月</td><td>$30 / 條 / 月</td></tr><tr><td><a href="../transaction-submission/rpc/base/eth_sendrawtransaction.md">RPC發送交易</a></td><td>低延遲高TPS發送Base交易上鏈</td><td>$1000 / 月</td><td>$100 / 月</td></tr></tbody></table>
{% endtab %}

{% tab title="General" %}
<table><thead><tr><th width="140.84765625">服務</th><th width="160.96484375">描述</th><th>按月購買</th><th>按日購買</th></tr></thead><tbody><tr><td>Dedicated Channel</td><td>專屬技術支持</td><td>$1000 / 月</td><td>$100 / 月</td></tr></tbody></table>
{% endtab %}
{% endtabs %}

#### 極速服務包

{% hint style="info" %}
相比單項服務的組合式採購，用戶可通過極速服務包以更低價格完成打包採購。目前極速服務包主要包含BSC服務，整體訂閱價格&#x70BA;**$1250 / 月**，具體服務如下：
{% endhint %}

<table><thead><tr><th width="177.390625">服務</th><th>描述</th></tr></thead><tbody><tr><td><a href="../streams/mempool/bsc/public-mempool.md">Public Mempool</a></td><td>訂閱高性能網絡交易數據</td></tr><tr><td><a href="../streams/block-stream/bsc/newblocks.md">Block Stream</a></td><td>訂閱高性能網絡區塊數據</td></tr><tr><td><a href="../streams/node-stream/bsc/quan-jie-dian-tong-bu.md">Node Stream</a></td><td>低延遲同步世界狀態</td></tr><tr><td><a href="../transaction-submission/block-builder/call-bundle.md">Call Bundle</a></td><td>向Block Builder提交請求接收bundle模擬結果</td></tr><tr><td><a href="../transaction-submission/block-builder/fast-submit.md">Fast Submit</a></td><td>以更低延遲、更高穩定性向Block Builder提交交易</td></tr><tr><td><a href="../streams/mempool/bsc/tx-trace.md">Tx Trace</a></td><td>監控交易傳播路徑和跨區域延遲分佈</td></tr></tbody></table>

#### 折扣說明

折扣和訂閱週期的關係如下：

<table><thead><tr><th width="314.41796875">訂閱週期</th><th width="328.90234375">折扣</th></tr></thead><tbody><tr><td>1個月</td><td>無折扣</td></tr><tr><td>3個月</td><td>5%折扣</td></tr><tr><td>6個月</td><td>10%折扣</td></tr><tr><td>9個月</td><td>15%折扣</td></tr><tr><td>12個月及以上</td><td>20%折扣</td></tr></tbody></table>

#### **常見問題**

<details>

<summary>極速服務包和自選服務是否可以同時訂購</summary>

可以同時訂購

</details>



## 新註冊用戶

{% hint style="info" %}
BlockRazor面向新註冊用戶提供Solana, BSC, Etherem和Base上的多模式交易發送服務，包括RPC、Fast、Bundle、Block Builder等模式。
{% endhint %}

#### RPC

<table><thead><tr><th width="125.16015625">鏈</th><th>方法</th><th>限流</th></tr></thead><tbody><tr><td>BSC</td><td><ul><li><a href="../transaction-submission/rpc/bsc/eth_sendrawtransaction.md"><code>eth_sendRawTransaction</code></a></li><li><a href="../transaction-submission/rpc/bsc/eth_sendmevbundle/"><code>eth_sendMevBundle</code></a></li><li>其他JSON RPC方法</li></ul></td><td>-</td></tr><tr><td>Ethereum</td><td><ul><li><a href="../transaction-submission/rpc/ethereum/eth_sendrawtransaction.md"><code>eth_sendRawTransaction</code></a></li><li><a href="../transaction-submission/rpc/ethereum/eth_sendbundle.md"><code>eth_sendBundle</code></a></li><li>其他JSON RPC方法</li></ul></td><td>-</td></tr><tr><td>Base</td><td><ul><li><a href="../transaction-submission/rpc/base/eth_sendrawtransaction.md"><code>eth_sendRawTransaction</code></a></li></ul></td><td>1 Tx / 5s</td></tr></tbody></table>

#### Block Builder

<table><thead><tr><th width="125.484375">鏈</th><th width="288.10546875">方法</th><th>限流</th></tr></thead><tbody><tr><td>BSC</td><td><ul><li><a href="../transaction-submission/rpc/ethereum/eth_sendbundle.md"><code>eth_sendBundle</code></a></li><li><a href="../transaction-submission/block-builder/send-privatetransaction.md"><code>eth_sendPrivateTransaction</code></a></li></ul></td><td>-</td></tr></tbody></table>

#### Fast

<table><thead><tr><th width="113.1328125">鏈</th><th>方法</th><th>限流</th></tr></thead><tbody><tr><td>Solana</td><td><ul><li><a href="../transaction-submission/fast/base/eth_sendrawtransaction.md"><code>Send Transaction</code></a></li><li><a href="../transaction-submission/fast/solana/send-transaction-v2.md"><code>Send Transaction</code> v2</a></li></ul></td><td>默認為3 TPS</td></tr><tr><td>BSC</td><td><ul><li><a href="../transaction-submission/fast/bsc/eth_sendrawtransaction.md"><code>eth_sendRawTransaction</code></a></li><li><a href="../transaction-submission/fast/bsc/eth_sendrawtransaction-v2.md"><code>eth_sendRawTransaction</code> v2</a></li></ul></td><td>默認為10 TPS</td></tr><tr><td>Base</td><td><ul><li><a href="../transaction-submission/rpc/base/eth_sendrawtransaction.md"><code>eth_sendRawTransaction</code></a></li></ul></td><td>默認為10 TPS</td></tr></tbody></table>




---
description: 介紹訂閱服務（自選服務和極速服務包）的價格，新註冊用戶可零門檻享有的服務
layout:
  width: default
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
  metadata:
    visible: true
  tags:
    visible: true
  actions:
    visible: true
---

# 訂閱服務

{% hint style="info" %}
BlockRazor目前支持組合式訂購多鏈服務，以優惠價格訂購極速服務包，支持**按日採購**
{% endhint %}

### 自選服務

{% tabs %}
{% tab title="BSC" %}
<table data-search="false"><thead><tr><th width="173.05859375">服務</th><th>描述</th><th>价格</th><th>动作</th></tr></thead><tbody><tr><td><a href="../transaction-submission/fast/bsc/broadcast-tx.md">Broadcast Tx</a></td><td>低延遲傳播交易和交易batch</td><td>$50 / 日<br>$500 / 月</td><td><a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=bsc&#x26;serviceId=bsc_fast_tx&#x26;billing=day" class="button primary small">訂閱</a></td></tr><tr><td><a href="../streams/mempool/bsc/public-mempool.md">Public Mempool</a></td><td>低延遲訂閱pending交易數據</td><td>$30 / 條 / 日<br>$300 / 條 / 月</td><td><a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=bsc&#x26;serviceId=bsc_public_mempool&#x26;billing=day" class="button primary small">訂閱</a></td></tr><tr><td><a href="../streams/mempool/bsc/private-mempool.md">Private Mempool</a></td><td>訂閱BlockRazor RPC的隱私訂單流數據</td><td>$100 / 日<br>$1000 / 月</td><td><a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=bsc&#x26;serviceId=bsc_private_mempool&#x26;billing=day" class="button primary small">訂閱</a></td></tr><tr><td><a href="../streams/block-stream/bsc/newblocks.md">Block Stream</a></td><td>低延遲訂閱BSC區塊數據</td><td>$50 / 條 / 日<br>$500 / 條 / 月</td><td><a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=bsc&#x26;serviceId=bsc_block_stream&#x26;billing=day" class="button primary small">訂閱</a></td></tr><tr><td><a href="../streams/node-stream/bsc/quan-jie-dian-tong-bu.md">Node Stream</a></td><td>低延遲同步世界狀態</td><td>$80 / 個 / 日<br>$800 / 個 / 月</td><td><a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=bsc&#x26;serviceId=bsc_enode&#x26;billing=day" class="button primary small">訂閱</a></td></tr><tr><td><a href="../streams/network-fee-stream/bsc/getgaspricestream.md">Network Fee Stream</a></td><td>獲取BSC gas price數據</td><td>$30 / 日<br>$300 / 月</td><td><a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=bsc&#x26;serviceId=bsc_fee_stream&#x26;billing=day" class="button primary small">訂閱</a></td></tr><tr><td><a href="../transaction-submission/block-builder/trace-bundle.md">Bundle Tracing &#x26; Explorer</a></td><td>Block Builder bundle追蹤與瀏覽</td><td>$150 / 日<br>$1500 / 月</td><td><a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=bsc&#x26;serviceId=bsc_bundle_tracing&#x26;billing=day" class="button primary small">訂閱</a></td></tr></tbody></table>
{% endtab %}

{% tab title="Robinhood" %}
<table><thead><tr><th width="136.76171875">服務</th><th>描述</th><th>價格</th><th>動作</th></tr></thead><tbody><tr><td><a href="../streams/node-stream/robinhood-chain/sequencer-feed.md">Node Stream</a></td><td>低延遲接收Robinhood Chain Sequencer Feed</td><td>$80 / 個 / 日<br>$800 / 個 / 月</td><td> <a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=robinhood&#x26;serviceId=robinhood_fast_tx&#x26;billing=day" class="button primary medium">訂閱</a></td></tr></tbody></table>
{% endtab %}

{% tab title="Solana" %}
<table><thead><tr><th width="150.8203125">服务</th><th width="149.66015625">描述</th><th>價格</th><th>動作</th></tr></thead><tbody><tr><td><a href="../streams/block-stream/solana/shred-stream.md">Shred Stream</a></td><td>低延遲傳輸shred</td><td>$50 / 條 / 日<br>$500 / 條 / 月</td><td><a href="https://blockrazor.io/#/portal/pricing?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=solana&#x26;serviceId=solana_shreds_stream&#x26;billing=day" class="button primary small">訂閱</a></td></tr><tr><td><a href="../streams/block-stream/solana/geyser-stream/">Geyser Stream</a></td><td>實時傳輸Solana鏈上數據，包含account, slot, block和transaction等</td><td>5 TiB - $250 / 月<br>10 TiB - $500 / 月<br>50 TiB - $250 / 月<br>100 TiB - $4750 / 月<br>150 TiB - $6750 / 月<br>200 TiB - $8500 / 月<br>250 TiB - $10000 / 月</td><td><a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=solana&#x26;serviceId=solana_geyser_stream&#x26;billing=month" class="button primary small">訂閱</a></td></tr><tr><td><a href="../streams/network-fee-stream/solana/get-transactionfee.md">Network Fee Stream</a></td><td>獲取Solana的priority fee和tip數據</td><td>$30 / 日<br>$300 / 月</td><td><a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=solana&#x26;serviceId=solana_network_fee_stream&#x26;billing=day" class="button primary small">訂閱</a></td></tr></tbody></table>
{% endtab %}

{% tab title="Ethereum" %}
| 服務                                                                        | 描述                | 價格                                 | 動作                                                                                                                                                                                                      |
| ------------------------------------------------------------------------- | ----------------- | ---------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Broadcast Tx](../transaction-submission/fast/ethereum/broadcast-tx.md)   | 低延遲傳播交易和交易batch   | <p>$50 / 日<br>$500 / 月</p>         | <a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=ethereum&#x26;serviceId=ethereum_fast_tx&#x26;billing=day" class="button primary small">訂閱</a>        |
| [Public Mempool](../streams/mempool/ethereum/public-mempool.md)           | 低延遲訂閱pending交易數據  | <p>$30 / 條 / 日<br>$300 / 條 / 月</p> | <a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=ethereum&#x26;serviceId=ethereum_public_mempool&#x26;billing=day" class="button primary small">訂閱</a> |
| [Block Stream](../streams/block-stream/ethereum/newblocks.md)             | 低延遲訂閱Ethereum區塊數據 | <p>$50 / 條 / 日<br>$500 / 條 / 月</p> | <a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=ethereum&#x26;serviceId=ethereum_block_stream&#x26;billing=day" class="button primary small">訂閱</a>   |
| [Node Stream](../streams/node-stream/ethereum/clel-ke-hu-duan-tong-bu.md) | 低延遲同步世界狀態         | <p>$80 / 個 / 日<br>$800 / 個 / 月</p> | <a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=ethereum&#x26;serviceId=ethereum_enode&#x26;billing=day" class="button primary small">訂閱</a>          |
{% endtab %}

{% tab title="Base" %}
<table><thead><tr><th width="145.37890625">服務</th><th>描述</th><th>價格</th><th>動作</th></tr></thead><tbody><tr><td><a href="../streams/block-stream/base/get-flashblockstream/">FlashBlock Stream</a></td><td>低延遲獲取Base FlashBlock數據</td><td>$25 / 條 / 日<br>$250 / 條 / 月</td><td><a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=base&#x26;serviceId=base_flashblock&#x26;billing=day" class="button primary small">訂閱</a></td></tr><tr><td><a href="../streams/block-stream/base/get-blockstream.md">Block Stream</a></td><td>低延遲獲取Base Block數據</td><td>$30 / 條 / 日<br>$300 / 條 / 月</td><td><a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=base&#x26;serviceId=base_blockstream&#x26;billing=day" class="button primary small">訂閱</a></td></tr><tr><td><a href="../transaction-submission/rpc/base/eth_sendrawtransaction.md">RPC發送交易</a></td><td>低延遲高TPS發送Base交易上鏈</td><td>$100 / 日<br>$1000 / 月</td><td><a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=base&#x26;serviceId=base_rpc_send_tx&#x26;billing=day" class="button primary small">訂閱</a></td></tr></tbody></table>
{% endtab %}

{% tab title="General" %}
<table><thead><tr><th width="140.84765625">服務</th><th width="160.96484375">描述</th><th>價格</th><th>動作</th></tr></thead><tbody><tr><td>Dedicated Channel</td><td>專屬技術支持</td><td>$100 / 日<br>$1000 / 月</td><td><a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=common&#x26;serviceId=common_dedicated_channel&#x26;billing=day" class="button primary small">訂閱</a></td></tr></tbody></table>
{% endtab %}
{% endtabs %}

### 極速服務包

{% hint style="info" %}
相比單項服務的組合式採購，用戶可通過極速服務包以更低價格完成打包採購BSC和Ethereum服務，訂閱價格&#x70BA;**$1250 / 月，**&#x7ACB;即前往 <a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=package&#x26;billing=month" class="button primary small">訂閱</a>。
{% endhint %}

<table><thead><tr><th width="206.2421875">服務</th><th width="86.25">數量</th><th width="422.9609375">描述</th></tr></thead><tbody><tr><td><a href="../streams/mempool/bsc/public-mempool.md">Public Mempool - BSC</a><br><a href="../streams/mempool/ethereum/public-mempool.md">Public Mempool - ETH</a></td><td>2</td><td>低延遲訂閱BSC和Ethereum公開mempool交易數據，訂閱額度跨鏈共享</td></tr><tr><td><a href="../streams/block-stream/bsc/newblocks.md">Block Stream - BSC</a><br><a href="../streams/block-stream/ethereum/newblocks.md">Block Stream - ETH</a></td><td>2</td><td>低延迟订阅BSC和Ethereum區塊數據，，訂閱額度跨鏈共享</td></tr><tr><td><a href="../streams/node-stream/bsc/quan-jie-dian-tong-bu.md">Node Stream - BSC</a><br><a href="../streams/node-stream/ethereum/clel-ke-hu-duan-tong-bu.md">Node Stream - ETH</a></td><td>1</td><td>低延遲同步BSC和Ethereum的世界狀態，同步额度跨链共享</td></tr><tr><td><a href="../transaction-submission/block-builder/call-bundle.md">Call Bundle</a></td><td>1</td><td>向Block Builder提交請求接收bundle模擬結果</td></tr><tr><td><a href="../transaction-submission/block-builder/fast-submit.md">Fast Submit</a></td><td>1</td><td>以更低延遲、更高穩定性向Block Builder提交交易</td></tr><tr><td><a href="../streams/mempool/bsc/tx-trace.md">Tx Trace</a></td><td>1</td><td>監控交易傳播路徑和跨區域延遲分佈</td></tr></tbody></table>

### 折扣說明

折扣和訂閱週期的關係如下：

<table><thead><tr><th width="314.41796875">訂閱週期</th><th width="416.4765625">折扣</th></tr></thead><tbody><tr><td>1個月</td><td>無折扣</td></tr><tr><td>3個月</td><td>5%折扣</td></tr><tr><td>6個月</td><td>10%折扣</td></tr><tr><td>9個月</td><td>15%折扣</td></tr><tr><td>12個月及以上</td><td>20%折扣</td></tr></tbody></table>

### **常見問題**

<details>

<summary>極速服務包和自選服務是否可以同時訂購</summary>

可以同時訂購

</details>

<details>

<summary>自選服務中的數據流服務額度是指什麼</summary>

數據流服務額度是指允許連接的gRPC數據流數量，額度為多地區共享，如購買1條Public Mempool，則僅允許在一個地區連接一條數據流

</details>

<details>

<summary>極速服務包中的額度共享是指什麼</summary>

在極速服務包中，Public Mempool, Block Stream和Node Stream在BSC和Ethereum上共享數據流額度，比如採購極速服務包後獲取2條Public Mempool額度，則允許在BSC和Ethereum上一共訂閱2條數據流。如已訂閱2條BSC Public Mempool，則無法在Ethereum上訂閱Public Mempool

</details>


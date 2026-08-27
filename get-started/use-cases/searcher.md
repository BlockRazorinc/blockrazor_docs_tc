---
description: 介紹Searcher在處理bundle過程中的痛點以及如何使用Blockrazor的服務來提升競爭力
metaLinks:
  canonical: searcher.md
  alternates:
    - >-
      https://app.gitbook.com/s/jbyfG8gOgcdsK3wVxNdQ/get-started/use-cases/searcher
---

# Searcher

隨著競爭日益激烈，Searcher憑借Alpha策略已無法在MEV競爭中完勝，必須具備Beta競爭力以進一步提升勝率。

什麼是Beta競爭力？如果將Searcher競爭比喻為一場戰爭，Alpha策略是戰略和戰術，Beta競爭力就是裝備、補給等基礎設施，是競爭的前提條件。

Searcher的Beta競爭力包括但不限於速度、成本等，分別可以從不同環節提高競爭勝率。

### 速度

在Ethereum或BSC中，區塊出塊的時間間隔是固定的。在這條生產區塊的流水線上，為了將套利的bundle及時發送給Builder，處理bundle的速度需要得到提升。Searcher處理bundle的步驟如下：

1. 訂閱目標交易，同步最新區塊
2. 基於最新區塊的世界狀態，根據Alpha策略計算每筆交易的套利機會
3. 篩選套利機會，將篩選出的目標交易和策略交易按指定順序組裝為bundle
4. 將bundle發送給Builder

從上述步驟可知，bundle處理時長由交易訂閱延遲、套利計算時長、bundle發送延遲決定。

此外，最新世界狀態的同步是計算套利機會的前提條件（基於舊狀態計算會導致套利動作變形，失去Alpha策略競爭力），如果狀態同步延遲高，會嚴重擠壓套利計算的時長，尤其在網絡交易擁堵期間，會對計算性能造成巨大壓力。因此，世界狀態同步的時延也是bundle處理速度的關鍵。

在Searcher處理bundle的第4步，Searcher需要將由目標交易和套利交易組成的bundle發給鏈上的所有主流Builder，確保參與競拍的區塊中都包含bundle，以提升bundle的上鏈速度。

### 成本

成本直接影響Searcher的利潤。在勢均力敵的Searcher競爭中，如果每個套利機會的平均成本相差較多，長期來看，成本高的Searcher由於利潤微薄，將逐漸在競爭中趨向弱勢。

Searcher的成本主要分為Alpha策略執行成本、速度提升成本和交易成本，提升成本可以提升Beta競爭力，但利潤也會受到影響，需做好權衡取捨。

**Alpha策略執行**

由於Alpha策略是Searcher的核心競爭力，同時執行速度也直接影響bundle處理時長 ，本項成本不能降低。

**bundle處理速度提升**

為了降低交易訂閱延遲、bundle發送延遲和最新世界狀態同步延遲，需自建分布式的高速骨幹網絡，對於Searcher而言成本過高，使用專業的第三方服務是高性價比的選擇。

bundle上鏈速度的提升涉及Builder API限流解除成本。Searcher可以免費對接RPC的[Bundle](../../transaction-submission/rpc/bsc/orderflow-auction.md)，RPC會將bundle轉發給鏈上的主流Builder，在節省成本的同時保障上鏈速度。

**交易成本**

在計算套利機會時，套利利潤 = 套利空間-交易成本，套利利潤>0才被視為是一個套利機會。如果交易成本被壓縮到極致，市場極小波動產生的套利機會也可以被捕捉，可擴展Alpha策略的覆蓋範圍。目前BSC上的Builder和RPC支持0 gwei交易，Searcher可嘗試對接。

### BlockRazor如何提升Searcher的Beta競爭力

#### 速度

BlockRazor為Searcher提供[Public Mempool](../../streams/public-mempool/bsc/public-mempool.md)和[Block Stream](../../streams/block-stream/)服務，Searcher能夠以極低延遲訂閱交易同步區塊。在與業界領先的高性能網絡提供商bloXroute的對比中，BlockRazor能夠以更低延遲接收到最新交易，詳細數據對比如下

<figure><img src="../../.gitbook/assets/image (7).png" alt="" width="563"><figcaption></figcaption></figure>

同時，在最新世界狀態同步等時延指標上，BlockRazor同樣擁有優秀的性能表現，可以更好地滿足速度敏感型用戶的需求。全部指標對比結果，請詳見[benchmark分析](https://blockrazor.io/#/blogs/20240914benchmark)。

在bundle上鍊速度方面，Searcher可直接對接RPC的[Bundle](../../transaction-submission/rpc/bsc/orderflow-auction.md)，RPC會在第一時間將bundle轉發給鏈上的主流Builder，同時基於 [BEF](../../he-xin-ji-shu/blockchain-edge-fabric.md)，RPC可以在網絡層面實現交易端到端的低延遲轉發。數據表明，提交至RPC的交易在下個區塊內上鏈的概率高達95%，100%的交易可以在兩個區塊內上鏈，交易上鏈的速度和穩定性遠超其他RPC。

<figure><img src="../../.gitbook/assets/image (21).png" alt=""><figcaption><p>benchmark客戶端分別向BlockRazor RPC、A RPC和B RPC發送交易，記錄交易發送時的最新區塊號和上鏈區塊號的差值，差值越小，表示交易上鏈速度越快。</p></figcaption></figure>

#### 成本

BlockRazor為Searcher提供極具性價比的訂閱計劃，一經訂閱，Searcher以極低延遲訂閱Public Mempool交易，向BSC上出塊率第一的Block Builder發送0 gwei交易，同時訂閱[Private Mempool](../../streams/private-mempool.md)的隱私交易流執行backrun策略以拓展套利機會範圍。

### 如何使用BlockRazor的服务

**隱私交易流套利**

1. [註冊](https://www.blockrazor.io/#/register)BlockRazor
2. [登錄](https://www.blockrazor.io/#/login)BlockRazor，采购Private Mempool，前往賬戶模塊獲取token
3. ​訂閱 [Private Mempool](../../streams/private-mempool.md)
4. 執行套利策略，將隱私流交易和backrun策略交易以[Bundle](../../transaction-submission/rpc/bsc/orderflow-auction.md)提交至RPC

**公開交易流套利**

1. [註冊](https://www.blockrazor.io/#/register)BlockRazor
2. [登錄](https://www.blockrazor.io/#/login)BlockRazor，采购Public Mempool，前往賬戶模塊獲取token
3. 對接[Public Mempool](../../streams/public-mempool/bsc/public-mempool.md)方法，低延遲訂閱最新交易；如果本地有節點，則採購[Node Stream](../../streams/node-stream/)低延遲同步節點數據。
4. 執行套利策略，將Mempool交易和策略交易[Bundle](../../transaction-submission/rpc/bsc/orderflow-auction.md)提交至Scutum。

**塊尾0 gwei**

1. [註冊](https://www.blockrazor.io/#/register)BlockRazor
2. [登錄](https://www.blockrazor.io/#/login)BlockRazor，採購極速服務包，前往賬戶模塊獲取token
3. 請加入[Discord](https://discord.com/invite/qqJuwRb8Nh)與我們聯繫，對接BlockRazor Builder的塊尾0 gwei接口

### **常見問題**

<details>

<summary>提交Bundle至RPC和提交Bundle至Block Builder有什麼區別</summary>

两者的核心区别在于 Bundle 的提交路径和最终到达的目标不同。

提交 [Bundle](../../transaction-submission/rpc/bsc/eth_sendbundle.md) 至 **BlockRazor RPC** 时，BlockRazor RPC 会将 Bundle 低延迟转发给主流 builders。这种方式更适合作为统一接入入口，用户不需要逐个对接不同 builder，即可完成 Bundle 提交。

提交 Bundle 至 **Block Builder** 时，Bundle 会直接发送到 BlockRazor Builder。它更适合明确希望使用 BlockRazor Builder 能力与接入路径的场景。

</details>

<details>

<summary><strong>如何理解bundle的平均gas price需不小於0.05 gwei？</strong></summary>

假設一個 Bundle 中有 3 筆交易 `{tx1, tx2, tx3}`，其中 `tx1` 來自 mempool，BlockRazor Builder 會排除 `tx1`，僅計算 `tx2` 和 `tx3` 的平均 gas price。若 `tx3` 額外支付 tip 給 Builder，則該 tip 金額會加入計算公式的分子，分母仍只計算 `tx2` 和 `tx3` 的 gas used。計算公式為：`(tx2.gas price × tx2.gas used + tx3.gas price × tx3.gas used + tx3.tip) / (tx2.gas used + tx3.gas used)`。

</details>


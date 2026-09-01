---
description: 介紹BlockRazor Ethereum Public Mempool的服務、應用場景以及接入方法
metaLinks:
  canonical: public-mempool.md
  alternates:
    - >-
      https://app.gitbook.com/s/jbyfG8gOgcdsK3wVxNdQ/streams/public-mempool/ethereum/public-mempool
---

# Ethereum Public Mempool

### Ethereum Public Mempool是什麼

Public Mempool 是 BlockRazor 基于 [BEF](../../../he-xin-ji-shu/blockchain-edge-fabric.md) 提供的高性能 pending 交易數據流服務，用於低延遲訂閱公開傳播中的未確認交易。

在 EVM 網絡中，交易在進入區塊之前，通常會先在 Mempool 中傳播。Public Mempool 可以幫助用戶更早獲取公開的 pending 交易信號，並將這些信號以更低延遲接入自己的策略系統。它常用於監控公開交易活動、跟蹤 Smart Money 行為、識別新機會，以及為 backrun、copy trading、sniping 等策略提供更快的信號輸入。對於依賴 pending 信號驅動交易決策的系統來說，更早看到交易，往往意味著：

* 更早進入策略判斷流程
* 更充足的時間完成參數計算與風險控制
* 更高概率在競爭場景中獲得更優執行位置

### Ethereum Public Mempool的應用場景

* **Pending交易監控：**&#x5BE6;時監控公開傳播中的 pending 交易，用於識別活躍地址、熱門合約或異常交易行為
* **Smart Money追蹤：**&#x76E1;早跟蹤目標地址的交易活動，為 copy trading 或策略跟隨提供信號輸入
* **Backrun機會發現：**&#x767C;現可能觸發 backrun 機會的公開交易，為後續策略判斷和交易提交爭取更多時間
* **Sniping機會發現：**&#x5728;新池上線、流動性注入或目標交易出現時，盡早捕捉公開市場中的首輪信號
* **策略實時數據輸入**：作為交易系統的實時輸入源，與 Block Stream、Node Stream、RPC 或 Block Builder 等能力配合使用，構建更完整的監控與執行鏈路

### 價格

<table><thead><tr><th width="167.296875">支付方式</th><th>價格</th><th>操作</th></tr></thead><tbody><tr><td>Personalized</td><td>$30 / stream / 日<br>$300 / stream / 月</td><td><a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=ethereum&#x26;serviceId=ethereum_public_mempool&#x26;billing=day" class="button primary small">訂閱</a></td></tr><tr><td>Package</td><td>$1250 / 月<br>和其他9項服務打包購買</td><td><a href="https://blockrazor.io/#/portal/pricing?redirect=pricing&#x26;purchaseMode=package&#x26;billing=month" class="button primary small">訂閱</a></td></tr></tbody></table>

{% hint style="info" %}
The number of data streams that can be subscribed to is calculated on a shared basis across all regions. For example, if you purchase one stream, you can only subscribe in one region; you will not be able to subscribe in other regions.
{% endhint %}

### 端點

<table><thead><tr><th width="154">地區</th><th>Relay IP:Port</th></tr></thead><tbody><tr><td>法蘭克福</td><td>64.130.47.75:50061</td></tr><tr><td>東京</td><td>63.254.162.18:50061</td></tr><tr><td>弗吉尼亞</td><td>208.91.105.204:50061</td></tr></tbody></table>

### 請求示例

[https://github.com/BlockRazorinc/eth\_relay\_example](https://github.com/BlockRazorinc/eth_relay_examplehttps:/github.com/BlockRazorinc/eth_relay_example)

{% tabs %}
{% tab title="Go" %}
```go
package main

import (
	"context"
	"encoding/hex"
	"fmt"

	// directory of the generated code using the provided relay.proto file
	pb "github.com/BlockRazorinc/eth_relay_example/protobuf"
	"google.golang.org/grpc"
	"google.golang.org/grpc/credentials/insecure"
)

// auth will be used to verify the credential
type Authentication struct {
	apiKey string
}

func (a *Authentication) GetRequestMetadata(context.Context, ...string) (map[string]string, error) {
	return map[string]string{"apikey": a.apiKey}, nil
}

func (a *Authentication) RequireTransportSecurity() bool {
	return false
}

func main() {

	// BlockRazor relay endpoint address
	blzrelayEndPoint := "ip:port"

	// auth will be used to verify the credential
	auth := Authentication{
		"your auth token",
	}

	// open gRPC connection to BlockRazor relay
	var err error
	conn, err := grpc.NewClient(blzrelayEndPoint, grpc.WithTransportCredentials(insecure.NewCredentials()), grpc.WithPerRPCCredentials(&auth), grpc.WithWriteBufferSize(0), grpc.WithInitialConnWindowSize(128*1024))
	if err != nil {
		fmt.Println("error: ", err)
		return
	}
	defer conn.Close()

	// use the Relay client connection interface
	client := pb.NewRelayClient(conn)

	// create context and defer cancel of context
	ctx, cancel := context.WithCancel(context.Background())
	defer cancel()

	// create a subscription using the stream-specific method and request
	stream, err := client.NewTxs(ctx, &pb.NewTxsRequest{IncludeRawTx: true})
	if err != nil {
		fmt.Println("failed to subscribe new tx: ", err)
		return
	}

	for {
		reply, err := stream.Recv()
		if err != nil {
			fmt.Println("stream receive error: ", err)
			return
		}

		fmt.Println("receive new tx, tx hash is ", reply.TxHash, " source is ", reply.Source)
		if len(reply.RawTx) > 0 {
			fmt.Println("raw tx is 0x" + hex.EncodeToString(reply.RawTx))
		}
	}
}

```
{% endtab %}
{% endtabs %}

#### Proto

```go
syntax = "proto3";

package relay.v1;

option go_package = "github.com/BlockRazorinc/eth_relay_example/protobuf";

service Relay {
  rpc SendTx(SendTxRequest) returns (SendTxReply);
  rpc NewTxs(NewTxsRequest) returns (stream NewTx);
  rpc NewBlocks(NewBlocksRequest) returns (stream NewBlock);
}

message SendTxRequest {
  bytes raw_tx = 1;
}

message SendTxReply {
  string tx_hash = 1;
}

message NewTxsRequest {
  bool include_raw_tx = 1;
}

message NewTx {
  string tx_hash = 1;
  bytes raw_tx = 2;
  int64 first_seen_unix_ns = 3;
  string source = 4;
}

message NewBlocksRequest {
  bool parsed_txs = 1;
}

message NewBlock {
  string hash = 1;
  BlockHeader header = 2;
  repeated BlockTransaction transactions = 3;
  repeated Withdrawal withdrawals = 4;
}

message BlockHeader {
  string parent_hash = 1;
  string sha3_uncles = 2;
  string miner = 3;
  string state_root = 4;
  string transactions_root = 5;
  string receipts_root = 6;
  string logs_bloom = 7;
  string difficulty = 8;
  string number = 9;
  string gas_limit = 10;
  string gas_used = 11;
  string timestamp = 12;
  string extra_data = 13;
  string mix_hash = 14;
  string nonce = 15;
  string base_fee_per_gas = 16;
  string withdrawals_root = 17;
  string blob_gas_used = 18;
  string excess_blob_gas = 19;
  string parent_beacon_block_root = 20;
}

message BlockTransaction {
  bytes raw_tx = 1;
  bytes from = 2;
}

message Withdrawal {
  string address = 1;
  string amount = 2;
  string index = 3;
  string validator_index = 4;
}

```

### 返回示例

**正常**

```json
{
  "tx":[
     {
	 "raw_tx":"+QH0gjOthDuaygCDBrbAlKoP7P6dEOH8IzwtDAw9whDVeHKRhwFrzEHpAAC5AYTVQ9H9AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAACAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAmsrt4HBPm7jWohanh7XmbX9S/gRLrth87fXF3H2gC0FAAAAAAAAAAAAAAAAbsa1rd5IJ6lr43ixr1+LWmT/OhgAAAAAAAAAAAAAAABV05gyb5kFn/d1SFJGmZAnsxl5VQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABVkLNFR0SQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAGZ7qkBM09jlPtkQprbOV2bITVAfdbvzTltwBYjUJu6OIzF3aAAAAAAAAAAAAAAAAHqXLqcmW4qO1ZEAZXn2nYI/dKV1AAAAAAAAAAAAAAAAVdOYMm+ZBZ/3dUhSRpmQJ7MZeVUAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAASvCnY7scAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABme6pAgZOgy2LsKlIqPeeM7d520T3eAwIVk9O+vY4wT+zifYp0GGOgTY7Z5J3zs/YCj1HvVXOZF9Q2rj5x421GBG9CrKmxVGo="
     }
   ]
}
```

**异常**

```
rpc error: code = Unknown desc = data streams have exceeded its max limit [5]
```






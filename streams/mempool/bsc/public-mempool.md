---
description: 介紹BSC Public Mempool的服務、優勢、應用場景以及接入方法
---

# Public Mempool

### BSC Public Mempool是什麼

Public Mempool 是 BlockRazor 基于 [BEF](../../../he-xin-ji-shu/blockchain-edge-fabric.md) 提供的高性能 pending 交易數據流服務，用於低延遲訂閱公開傳播中的未確認交易。

在 EVM 網絡中，交易在進入區塊之前，通常會先在 Mempool 中傳播。Public Mempool 可以幫助用戶更早獲取公開的 pending 交易信號，並將這些信號以更低延遲接入自己的策略系統。它常用於監控公開交易活動、跟蹤 Smart Money 行為、識別新機會，以及為 backrun、copy trading、sniping 等策略提供更快的信號輸入。對於依賴 pending 信號驅動交易決策的系統來說，更早看到交易，往往意味著：

* 更早進入策略判斷流程
* 更充足的時間完成參數計算與風險控制
* 更高概率在競爭場景中獲得更優執行位置

### BSC Public Mempool的應用場景

* **Pending交易監控：**&#x5BE6;時監控公開傳播中的 pending 交易，用於識別活躍地址、熱門合約或異常交易行為
* **Smart Money追蹤：**&#x76E1;早跟蹤目標地址的交易活動，為 copy trading 或策略跟隨提供信號輸入
* **Backrun機會發現：**&#x767C;現可能觸發 backrun 機會的公開交易，為後續策略判斷和交易提交爭取更多時間
* **Sniping機會發現：**&#x5728;新池上線、流動性注入或目標交易出現時，盡早捕捉公開市場中的首輪信號
* **策略實時數據輸入**：作為交易系統的實時輸入源，與 Block Stream、Node Stream、RPC 或 Block Builder 等能力配合使用，構建更完整的監控與執行鏈路

### Benchmark

在接收交易的延遲測試中，我們分別在 Dublin、Frankfurt、Tokyo 和 Virginia 四個區域，對 BlockRazor 與普通 Node 進行了對比。評估方式基於雙方對同一筆交易的首次接收時間差，統計在可比樣本中由哪一方更早收到交易。

<table><thead><tr><th width="134.609375">Region</th><th>BlockRazor Lead Rate</th><th>Avg Lead</th><th>P90 Lead</th></tr></thead><tbody><tr><td>Dublin</td><td>84.0%</td><td>102.4 ms</td><td>221.0 ms</td></tr><tr><td>Frankfurt</td><td>99.7%</td><td>30.4 ms</td><td>67.7 ms</td></tr><tr><td>Tokyo</td><td>99.9%</td><td>148.6 ms</td><td>264.4 ms</td></tr><tr><td>Virginia</td><td>99.7%</td><td>62.0 ms</td><td>190.7 ms</td></tr></tbody></table>

從結果來看，BlockRazor 在四個區域均保持了明顯領先。Frankfurt、Tokyo 和 Virginia 三個區域的交易接收領先率均超過 99%，Dublin 區域的領先率為 84.0%。從領先幅度看，BlockRazor 在不同區域的平均領先時間約為 102.4ms、30.4ms、148.6ms 和 62.0ms；在 P90 維度下，領先幅度分別達到 221.0ms、67.7ms、264.4ms 和 190.7ms。

以上結果表明，BlockRazor 在交易傳播鏈路上展現出穩定的優先接收能力，能夠在絕大多數可比樣本中早於普通 Node 捕獲交易。

### 端點

<table><thead><tr><th width="154">地區</th><th width="218">可用區（AWS）</th><th>Relay地址</th></tr></thead><tbody><tr><td>法蘭克福</td><td>euc1-az2</td><td>64.130.47.75:50051</td></tr><tr><td>東京</td><td>apne1-az4</td><td>63.254.162.18:50051</td></tr><tr><td>都柏林</td><td>euw1-az1</td><td>141.98.217.82:50051</td></tr><tr><td>弗吉尼亞</td><td>use1-az4</td><td>208.91.105.204:50051</td></tr></tbody></table>

### 價格

每月每條數據流的價格為$300，請前往[訂閱](https://blockrazor.io/#/pricing)頁面採購。

{% hint style="info" %}
數據流的可訂閱數量按所有地區共享計算。比如購買 1 條，則僅可在 1 個地區訂閱，其他地區將無法訂閱。
{% endhint %}

### 請求參數

<table><thead><tr><th width="170">參數</th><th width="68">必選</th><th width="111">格式</th><th width="81">示例</th><th>備注</th></tr></thead><tbody><tr><td>NodeValidation</td><td>是</td><td>boolean</td><td>false</td><td>該字段目前僅支持設置為false，relay會以更低延遲推送全部新交易（未經校驗）</td></tr></tbody></table>

### 請求示例

[https://github.com/BlockRazorinc/relay\_example](https://github.com/BlockRazorinc/relay_example)

{% tabs %}
{% tab title="Go" %}
```go
package main

import (
	"context"
	"fmt"

	// directory of the generated code using the provided relay.proto file
	pb "github.com/BlockRazorinc/relay_example/protobuf"
	"github.com/ethereum/go-ethereum/core/types"
	"github.com/ethereum/go-ethereum/rlp"
	"google.golang.org/grpc"
)

// auth will be used to verify the credential
type Authentication struct {
	apiKey string
}

func (a *Authentication) GetRequestMetadata(context.Context, ...string) (map[string]string, error) {
	return map[string]string{"apiKey": a.apiKey}, nil
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
	conn, err := grpc.Dial(blzrelayEndPoint, grpc.WithInsecure(), grpc.WithPerRPCCredentials(&auth), grpc.WithWriteBufferSize(0), grpc.WithInitialConnWindowSize(128*1024))
	if err != nil {
		fmt.Println("error: ", err)
		return
	}

	// use the Gateway client connection interface
	client := pb.NewGatewayClient(conn)

	// create context and defer cancel of context
	ctx, cancel := context.WithCancel(context.Background())
	defer cancel()

	// create a subscription using the stream-specific method and request
	stream, err := client.NewTxs(ctx, &pb.TxsRequest{NodeValidation: false})
	if err != nil {
		fmt.Println("failed to subscribe new tx: ", err)
		return
	}

	for {
		reply, err := stream.Recv()
		if err != nil {
			fmt.Println("stream recieve error: ", err)
		}
		tx := &types.Transaction{}

		err = rlp.DecodeBytes(reply.Tx.RawTx, tx)
		if err != nil {
			continue
		}

		fmt.Println("recieve new tx, tx hash is ", tx.Hash().String())

	}
}
```
{% endtab %}
{% endtabs %}

#### Proto

The code of `relay.proto` is as follows:

```
syntax = "proto3";

package blockchain;

option go_package = "/Users/code/relay/grpcServer"; 
service Gateway {
  rpc SendTx (SendTxRequest) returns (SendTxReply) {}
  rpc SendTxs (SendTxsRequest) returns (SendTxsReply) {}
  rpc NewTxs (TxsRequest) returns (stream TxsReply){}
  rpc NewBlocks (BlocksRequest) returns (stream BlocksReply){}
}

message TxsRequest{
  bool node_validation = 1;
}

message Tx{
  bytes from = 1;
  int64 timestamp = 2;
  bytes raw_tx = 3;
}

message TxsReply{
   Tx tx = 1;
}

message BlocksRequest{
  bool node_validation = 1;
}

message BlockHeader{
  string parent_hash = 1;
  string sha3_uncles = 2;
  string miner = 3;
  string state_root = 4;
  string transactions_root = 5;
  string receipts_root = 6;
  string logs_bloom = 7;
  string difficulty = 8;
  string number = 9;
  uint64 gas_limit = 10;
  uint64 gas_used = 11;
  uint64 timestamp = 12;
  bytes extra_data = 13;
  string mix_hash = 14;
  uint64 nonce = 15;
  uint64 base_fee_per_gas = 16;
  string withdrawals_root = 17;
  uint64 blob_gas_used = 18;
  uint64 excess_blob_gas = 19;
  string parent_beacon_block_root = 20;
}

message NextValidator{
  string block_height = 1;
  string coinbase = 2;
}

message BlocksReply{
  string hash = 1;
  BlockHeader header = 2;
  repeated NextValidator nextValidator = 3;
  repeated Tx txs = 4;
}

message Transaction {
  string content = 1;
}

message Transactions {
  repeated Transaction transactions = 1;
}

message SendTxRequest {
  string transaction = 1;
}

message SendTxsRequest {
  string transactions = 1;
}

message SendTxReply {
  string tx_hash = 1;
}

message SendTxsReply {
  repeated string tx_hashs = 1;
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






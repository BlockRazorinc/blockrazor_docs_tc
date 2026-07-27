---
description: 介紹BlockRazor Base Get BlockStream的服務、優勢以及接入方法
---

# Get BlockStream

### Base Get BlockStream是什麼

Get BlockStream 是 BlockRazor 為 Base 提供的實時區塊數據訂閱接口，用於以低延遲方式持續獲取 Base 上最新生成的區塊數據。該接口基於 gRPC 協議提供，適合需要持續消費區塊數據的交易系統、數據系統和基礎設施系統接入。

### 為什麼選擇Base Get BlockStream

對於交易系統、監控系統和數據系統來說，區塊數據獲取能力不只是“能夠拿到新區塊”，還關係到不同地區服務的接收時效、數據鏈路的穩定性，以及長時間運行場景下的整體表現。BlockRazor 基於全球多點部署和跨洲專線在 Base 上提供 Frankfurt、Virginia 和 Tokyo 等多個區域入口，根據 [Base Benchmark](https://blockrazor.io/zh/blog/20250922basebenchmark/)，BlockRazor 在多個地區的區塊接收延遲相較 Base官方服務均表現出優勢，尤其在中高分位區間領先更明顯。

### 常見問題

<details>

<summary>Get BlockStream 和 Get FlashBlockStream 有什麼區別</summary>

兩者的核心區別在於 數據粒度、時間點和適用場景 不同。

* **Get BlockStream**\
  用於獲取 Base 上已經形成的正式區塊數據，關注的是已確認區塊和其中的交易內容。它更適合確認結果監控、區塊級分析、鏈上數據處理和需要穩定消費正式區塊的數據系統。
* **Get FlashBlockStream**\
  用於獲取 Base 上的 FlashBlock 數據。FlashBlock 是 Base 每約 200ms 推送一次的“子區塊”數據，相比標準約 2s 的正式區塊時間，能夠更早提供交易預確認信息。它更適合對低延遲更敏感、希望盡早看到鏈上變化的場景。

</details>

### 端點

{% tabs %}
{% tab title="gRPC" %}
<table><thead><tr><th width="136.83203125">地區</th><th>端點</th></tr></thead><tbody><tr><td>法蘭克福</td><td>frankfurt.grpc.base.blockrazor.xyz:80</td></tr><tr><td>弗吉尼亞</td><td>virginia.grpc.base.blockrazor.xyz:80</td></tr><tr><td>東京</td><td>tokyo.grpc.base.blockrazor.xyz:80</td></tr></tbody></table>
{% endtab %}
{% endtabs %}

### 價格

BlockStream每月每條數據流的價格為$300，請前往[訂閱](https://blockrazor.io/#/pricing)頁面採購。

{% hint style="info" %}
數據流的可訂閱數量按所有地區共享計算。比如購買 1 條，則僅可在 1 個地區訂閱，其他地區將無法訂閱。
{% endhint %}

### 請求示例

{% tabs %}
{% tab title="gRPC" %}
[查看](https://github.com/BlockRazorinc/base-api-client-go/blob/c4bec3d65e55ffb0da07253fa78aefe1b1c07e33/main.go#L42)示例

```javascript
// GetBlockStream provides a simplified example of subscribing to and processing the regular block stream.
// Note: This function attempts to connect and subscribe only once. For production use, implement your own reconnection logic.
func GetBlockStream(authToken string) {
	log.Printf("[BlockStream] Attempting to connect to gRPC server at %s...", grpcAddr)

	// Establish a connection to the gRPC server with a timeout.
	ctx, cancel := context.WithTimeout(context.Background(), 30*time.Second)
	defer cancel()
	conn, err := grpc.DialContext(ctx, grpcAddr,
		grpc.WithTransportCredentials(insecure.NewCredentials()))
	if err != nil {
		log.Printf("[BlockStream] Failed to connect to gRPC server: %v", err)
		return
	}
	defer conn.Close()

	log.Println("[BlockStream] Successfully connected to gRPC server.")
	client := basepb.NewBaseApiClient(conn)

	// Create a new context with authentication metadata for the stream subscription.
	streamCtx := metadata.NewOutgoingContext(context.Background(), metadata.Pairs("authorization", authToken))
	stream, err := client.GetBlockStream(streamCtx, &basepb.GetBlockStreamRequest{})
	if err != nil {
		log.Printf("[BlockStream] Failed to subscribe to stream: %v", err)
		return
	}

	log.Println("[BlockStream] Subscription successful. Waiting for new blocks...")

	// Loop indefinitely to receive messages from the stream.
	for {
		block, err := stream.Recv()
		if err != nil {
			if err == io.EOF {
				log.Println("[BlockStream] Stream closed by the server (EOF).")
			} else {
				log.Printf("[BlockStream] An error occurred while receiving data: %v", err)
			}
			break // Exit the loop on error or stream closure.
		}

		// Process the received block data.
		log.Printf("=> [BlockStream] Received new block: Number=%d, Hash=%s, TransactionCount=%d",
			block.GetBlockNumber(),
			block.GetBlockHash(),
			len(block.GetTransactions()),
		)
		// To decode transactions, you can call DecodeTransactions(block.Transactions).
	}
}
```
{% endtab %}
{% endtabs %}

#### [proto](https://github.com/BlockRazorinc/base-api-client-go/blob/1d46c2983420d6da645992a9f3ed51688f7dac88/proto/BaseApi.proto)

```go
syntax = "proto3";

option go_package = "./basepb";


import "google/protobuf/wrappers.proto";

message BaseBlock {
  string parent_hash = 1;
  string fee_recipient = 2;
  bytes state_root = 3;
  bytes receipts_root = 4;
  bytes logs_bloom = 5;
  bytes prev_randao = 6;
  uint64 block_number = 7;
  uint64 gas_limit = 8;
  uint64 gas_used = 9;
  uint64 timestamp = 10;
  bytes extra_data = 11;
  repeated uint64 base_fee_per_gas = 12;
  string block_hash = 13;
  repeated bytes transactions = 14;

  repeated Withdrawal withdrawals = 15;
  google.protobuf.UInt64Value blob_gas_used = 16;
  google.protobuf.UInt64Value excess_blob_gas = 17;
  google.protobuf.BytesValue withdrawals_root = 18;
}

message Withdrawal {
  uint64 index = 1;
  uint64 validator = 2;
  bytes address = 3;
  uint64 amount = 4;
}

message GetRawFlashBlocksStreamRequest {
}

message GetBlockStreamRequest {
}

message SendTransactionRequest {
  string rawTransaction = 1;
}

message SendTransactionResponse {
  string txHash = 1;
}

message FlashBlockStrRequest {
}

message RawFlashBlockStrResponse {
  bytes message = 1;
}

service BaseApi {
  rpc SendTransaction(SendTransactionRequest) returns (SendTransactionResponse);
  rpc GetBlockStream(GetBlockStreamRequest) returns (stream BaseBlock);
  rpc GetRawFlashBlockStream(GetRawFlashBlocksStreamRequest) returns (stream RawFlashBlockStrResponse);
}
```

### 返回示例

**正常**

```
Number=35347872, Hash=0x1c1dd5911cf8fe47c227159f5e3dad08d289879a225b6d51840f4ecd0b212dd8, TransactionCount=252
```

**異常**

```
rpc error: code = Unknown desc = Authentication information is missing. Please provide a valid auth token
```


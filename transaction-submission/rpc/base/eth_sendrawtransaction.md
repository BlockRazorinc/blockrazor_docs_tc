---
description: 介紹集成BlockRazor Base RPC的eth_sendRawTransaction的方法
---

# eth\_sendRawTransaction

Base RPC 是 BlockRazor 為 Base 提供的交易發送接口，用於將已簽名的原始交易發送到鏈上。目前僅支持 `eth_sendRawTransaction` 方法，並同時提供 gRPC 與 HTTPS 兩種接入方式。

### 為什麼選擇BlockRazor Base RPC

對於 Wallets 和 DEX 來說，交易發送能力不只是“能夠發出交易”，還關係到不同地區用戶的訪問質量、發送鏈路的穩定性，以及高峰時段下的整體體驗。直接連接 Base 官方 Sequencer 雖然能夠滿足基礎發送需求，但對於服務全球用戶、關注跨區域表現和生產環境穩定性的業務來說，發送路徑本身仍然存在進一步優化空間。BlockRazor Base RPC和官網原生交易發送服務的對比詳見 [Benchmark](https://blockrazor.io/zh/blog/20250922basebenchmark/)

**全球多點部署**

相比直接連接 Base 官方 Sequencer，BlockRazor Base RPC 在多個核心區域提供發送入口，適合 Wallet 和 DEX 根據自身服務部署位置選擇更接近的 endpoint。對於服務全球用戶的業務來說，更接近業務服務器和核心用戶區域的發送入口，通常有助於縮短請求路徑，提升交易發送效率。

**跨洲專線**

BlockRazor Base RPC 的優化重點不僅在入口節點部署，也在區域 relay 之間的數據傳輸鏈路。相比完全依賴普通公網路由的區域間轉發方式，跨洲專線能夠大幅降低 relay 間傳輸的路由波動、擁塞和鏈路抖動影響，提升交易請求在整體發送鏈路中的穩定性和一致性。

### 端點

{% tabs %}
{% tab title="gRPC" %}
<table><thead><tr><th width="137.03515625">地區</th><th>端點</th></tr></thead><tbody><tr><td>法蘭克福</td><td>frankfurt.grpc.base.blockrazor.xyz:80</td></tr><tr><td>弗吉尼亞</td><td>virginia.grpc.base.blockrazor.xyz:80</td></tr><tr><td>東京</td><td>tokyo.grpc.base.blockrazor.xyz:80</td></tr></tbody></table>
{% endtab %}

{% tab title="HTTPS" %}
<table><thead><tr><th width="117.50390625">地區</th><th>端點</th></tr></thead><tbody><tr><td>法蘭克福</td><td>https://frankfurt-base.blockrazor.io:10101/eth_sendRawTransaction</td></tr><tr><td>弗吉尼亞</td><td>https://virginia-base.blockrazor.io:10101/eth_sendRawTransaction</td></tr><tr><td>東京</td><td>https://tokyo-base.blockrazor.io:10101/eth_sendRawTransaction</td></tr></tbody></table>
{% endtab %}
{% endtabs %}

### 價格

<table><thead><tr><th width="155.45703125">用戶類型</th><th width="208.93359375">限流</th><th>價格</th></tr></thead><tbody><tr><td>新註冊用戶</td><td>1 Tx / 5s</td><td>免費</td></tr><tr><td>付費用戶</td><td>5 Txs / 1s</td><td>$1000 / 月</td></tr></tbody></table>

### 請求參數

<table><thead><tr><th width="142.265625">參數</th><th width="84.64453125">必選</th><th width="104.48046875">格式</th><th width="230.7109375">示例</th><th>備註</th></tr></thead><tbody><tr><td>rawTransaction</td><td>是</td><td>string[hex]</td><td>"0xd46e8dd67c5d32be8d24c6b0afe7c5c3f4e9c3b2dae18d0c6b0cf5c8f3e8b2c1"</td><td>已簽名的raw transaction</td></tr></tbody></table>

### 請求示例

{% tabs %}
{% tab title="gRPC" %}
[查看](https://github.com/BlockRazorinc/base-api-client-go/blob/c4bec3d65e55ffb0da07253fa78aefe1b1c07e33/main.go#L150)示例

```go
// sendTransactions is an example function for sending a transaction.
// It's designed to reuse an existing gRPC client connection to avoid connection latency.
func sendTransactions(client basepb.BaseApiClient, authToken string, rawTxString string) {
	log.Println("[SendTx] Sending transaction...")
	ctx, cancel := context.WithTimeout(context.Background(), 3*time.Second)
	defer cancel()

	// Add the authentication token to the outgoing request's metadata.
	md := metadata.Pairs("authorization", authToken)
	ctx = metadata.NewOutgoingContext(ctx, md)

	req := &basepb.SendTransactionRequest{
		RawTransaction: rawTxString,
	}

	res, err := client.SendTransaction(ctx, req)
	if err != nil {
		log.Printf("[SendTx] Failed to send transaction: %v", err)
	} else {
		log.Printf("[SendTx] Transaction sent successfully. Hash: %s", res.GetTxHash())
	}
}
```
{% endtab %}

{% tab title="HTTPS" %}
```shellscript
curl -X POST \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <auth>" \
  -d '{
    "jsonrpc": "2.0",
    "method": "eth_sendRawTransaction",
    "params": [
        "0xf8668206bf831e988e825208940d287d6c8e5f8ad086f90d1933de060fdc578c2d808082422ea082d586bdef05fd6af532ca2943740f092cc5275731f175c0e0538a3e6a9b3c0ba060d2454b3b7679cc6c31ec70d6a8d926dcc40cf8cd4442776e018bd54787befb"
    ],
    "id": 67
  }' \
https://virginia-base.blockrazor.io:10101/eth_sendRawTransaction
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

### 返回

**正常**

```go
res: txHash:"0xaf430540d20eae2448947ffb254b03180b82333ef0c56bd526f7047489c195b5"
```

**異常**

```go
res: txHash:"Unknown TxHash"
```


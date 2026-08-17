---
description: 介紹集成BlockRazor Base Transaction Sending 的 eth_sendRawTransaction的方法
---

# Base eth\_sendRawTransaction

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

<table><thead><tr><th width="155.45703125">用戶類型</th><th width="208.93359375">限流</th><th>價格</th></tr></thead><tbody><tr><td>新註冊用戶</td><td>1 Tx / 5s</td><td>免費</td></tr><tr><td>付費用戶</td><td>5 Txs / 1s</td><td>$100 / 日<br>$1000 / 月<br><br><a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=base&#x26;serviceId=base_rpc_send_tx&#x26;billing=day" class="button primary small">訂閱</a></td></tr></tbody></table>

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


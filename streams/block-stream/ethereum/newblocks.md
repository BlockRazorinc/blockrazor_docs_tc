# NewBlocks

### Ethereum NewBlocks是什麼

NewBlocks 是 BlockRazor 提供的高性能區塊數據流服務，用於低延遲訂閱鏈上最新區塊與已確認交易。NewBlocks 幫助用戶更早接收到最新區塊內容，並將其中的區塊頭、交易列表和後續出塊者信息，以更低延遲接入自己的監控或策略系統。

NewBlocks 基於 [BEF](../../../he-xin-ji-shu/blockchain-edge-fabric.md) 分發最新區塊數據。當區塊在網絡中產生並開始傳播時，BlockRazor 會在多個核心區域盡早接收區塊，再通過低延遲鏈路轉發給訂閱方，縮短用戶接收區塊數據的時間。

### Ethereum NewBlocks的應用場景

* **Confirmed交易監控：** 實時接收最新區塊中的已確認交易，用於監控目標地址、熱門合約或異常交易行為
* **區塊級數據分析：** 獲取區塊頭、交易列表與後續出塊者信息，用於區塊研究、節點觀測與網絡狀態分析
* **策略數據輸入：** 作為交易系統的 confirmed 數據源，與 Public Mempool、Transaction Submission 等能力配合使用，構建更完整的監控與執行鏈路

### 端點

<table><thead><tr><th width="154">地區</th><th width="218">可用區（AWS）</th><th>Relay IP:Port</th></tr></thead><tbody><tr><td>法蘭克福</td><td>euc1-az2</td><td>64.130.47.75:50061</td></tr><tr><td>東京</td><td>apne1-az4</td><td>63.254.162.18:50061</td></tr><tr><td>弗吉尼亞</td><td>use1-az4</td><td>208.91.105.204:50061</td></tr></tbody></table>

### 價格

NewBlocks每月每條數據流的價格為$500，請前往[訂閱](https://blockrazor.io/#/pricing)頁面採購。

{% hint style="info" %}
數據流的可訂閱數量按所有地區共享計算。比如購買 1 條，則僅可在 1 個地區訂閱，其他地區將無法訂閱。
{% endhint %}

### 請求示例

[https://github.com/BlockRazorinc/eth\_relay\_example](https://github.com/BlockRazorinc/eth_relay_example)

{% tabs %}
{% tab title="Go" %}
```go
package main

import (
	"context"
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
	stream, err := client.NewBlocks(ctx, &pb.NewBlocksRequest{ParsedTxs: true})
	if err != nil {
		fmt.Println("failed to subscribe new block: ", err)
		return
	}

	for {
		reply, err := stream.Recv()
		if err != nil {
			fmt.Println("stream receive error: ", err)
			return
		}

		header := reply.GetHeader()
		fmt.Println("receive new block, block hash is ", reply.Hash, " number is ", header.GetNumber(), " txs are ", len(reply.Transactions))
	}
}

```
{% endtab %}
{% endtabs %}

**Proto**

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
  "hash": "0xaf1f...4116",
  "header": {
    "parentHash": "0x4603...cfee",
    "sha3Uncles": "0x1dcc...9347",
    "miner": "0x0e33b1c214463062753ad849a28e54667e0c87c2",
    "stateRoot": "0x4c26...1c96",
    "transactionsRoot": "0xb6b8...731e",
    "receiptsRoot": "0xd9ae...75ea",
    "logsBloom": "0x263c...59fdb",
    "difficulty": "0x0",
    "number": "0x1845661",
    "gasLimit": "0x3938700",
    "gasUsed": "0x14c4d26",
    "timestamp": "0x6a475117",
    "extraData": "0x",
    "mixHash": "0xb048...3154",
    "nonce": "0x0000000000000000",
    "baseFeePerGas": "71769385",
    "withdrawalsRoot": "0xe5de...f2f2",
    "blobGasUsed": "0x20000",
    "excessBlobGas": "0xa8d8c53",
    "parentBeaconBlockRoot": "0x0319...7a00"
  },
  "transactions": [
    {
      "rawTx": "<base64 encoded raw transaction>",
      "from": "<base64 encoded sender>"
    }
  ],
  "withdrawals": [
    {
      "address": "0x1135fa96848f34bff9d003f4c1699ae97418de29",
      "amount": "0xda361b",
      "index": "0x80678fb",
      "validatorIndex": "0x1b1fc1"
    }
  ]
}
```

**异常**

```
rpc error: code = Unknown desc = data streams have exceeded its max limit [5]
```

---
description: 介紹BlockRazor Ethereum Fast模式的 Broadcast Tx 接口以及集成方法
---

# Broadcast Tx

### 什麼是 Broadcast Tx

Broadcast Tx 是 BlockRazor 提供的一種快速交易發送服務，用於幫助用戶以更低延遲將交易發送到鏈上。它屬於 Fast 體系的一部分，但與需要在交易中附加 tip 的標準 Fast 模式不同，Broadcast Tx 不要求用戶在交易內部額外支付 tip，因此更適合作為低門檻的快速發送入口使用。

目前Ethereum Broadcast Tx提供 `SendTx` 方法，用来發送单笔交易。

需要注意的是，Broadcast Tx 雖然屬於 Fast 體系，但它並不等同於具備完整交易保護能力的私有發送通道。Broadcast Tx 發送的交易會進入公開傳播路徑，因此不具備 MEV 防護能力。

### 什麼場景下選擇 Broadcast Tx

* **不需要 tip，接入門檻更低**\
  與標準 Fast 模式不同，Broadcast Tx 不要求在交易中附加 tip，因此更適合希望快速接入、但不想修改交易激勵結構的用戶。
* **適合對速度有要求、但暫不強調 MEV 防護的場景**\
  如果你的重點是盡量更快地把交易發出去，而不是通過私有路徑隱藏交易或抵御 sandwich、frontrunning 等風險，那麼 Broadcast Tx 會是一個更直接的選擇。

### 端點

<table><thead><tr><th width="154">地區</th><th>Relay IP:Port</th></tr></thead><tbody><tr><td>法蘭克福</td><td>64.130.47.75:50061</td></tr><tr><td>東京</td><td>63.254.162.18:50061</td></tr><tr><td>弗吉尼亞</td><td>208.91.105.204:50061</td></tr></tbody></table>

### 價格

<table><thead><tr><th width="153.44921875">用戶類型</th><th>限流</th><th>價格</th></tr></thead><tbody><tr><td>新註冊用戶</td><td><p><code>SendTx</code></p><ul><li>TPS：10 Txs / 5s</li><li>每日交易上限：10</li></ul></td><td>免費</td></tr><tr><td>付費用戶</td><td><p><code>SendTx</code></p><ul><li>TPS：100 Txs / 5s</li><li>每日交易上限：100000</li></ul></td><td>$50 / 日<br>$500 / 月<br><br><a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=ethereum&#x26;serviceId=ethereum_fast_tx&#x26;billing=day" class="button primary small">訂閱</a></td></tr></tbody></table>

### SendTx

#### **請求參數**

<table><thead><tr><th width="147">參數</th><th width="66">必選</th><th width="91">格式</th><th>示例</th><th>描述</th></tr></thead><tbody><tr><td>Transaction</td><td>是</td><td>String</td><td>"0xf8……8219"</td><td>經過簽名的rawtx</td></tr></tbody></table>

#### **請求示例**

{% tabs %}
{% tab title="gRPC" %}
```go
package main

import (
	"context"
	"fmt"
	"math/big"
	"time"

	// directory of the generated code using the provided relay.proto file
	pb "github.com/BlockRazorinc/eth_relay_example/protobuf"
	"github.com/ethereum/go-ethereum/common"
	"github.com/ethereum/go-ethereum/core/types"
	"github.com/ethereum/go-ethereum/crypto"
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

	// use the Relay client connection interface.
	client := pb.NewRelayClient(conn)

	// create context
	ctx, cancel := context.WithTimeout(context.Background(), 10*time.Second)
	defer cancel()

	// replace with your address
	fromPrivateAddress := "42b565......44d05c"
	toPublicAddress := "0x4321......3f1c66"

	// replace with your transaction data
	nonce := uint64(1)
	toAddress := common.HexToAddress(toPublicAddress)
	var data []byte
	gasLimit := uint64(21000)
	value := big.NewInt(0)
	chainID := big.NewInt(1)
	gasTipCap := big.NewInt(2_000_000_000)
	gasFeeCap := big.NewInt(30_000_000_000)

	// create new EIP-1559 transaction
	tx := types.NewTx(&types.DynamicFeeTx{
		ChainID:   chainID,
		Nonce:     nonce,
		GasTipCap: gasTipCap,
		GasFeeCap: gasFeeCap,
		Gas:       gasLimit,
		To:        &toAddress,
		Value:     value,
		Data:      data,
	})

	privateKey, err := crypto.HexToECDSA(fromPrivateAddress)
	if err != nil {
		fmt.Println("fail to casting private key to ECDSA")
		return
	}

	// sign transaction by private key
	signedTx, err := types.SignTx(tx, types.LatestSignerForChainID(chainID), privateKey)
	if err != nil {
		fmt.Println("fail to sign transaction")
		return
	}

	// marshal signed transaction to raw binary bytes
	rawTx, err := signedTx.MarshalBinary()
	if err != nil {
		fmt.Println("fail to marshal signed transaction")
		return
	}

	// send raw tx by BlockRazor
	res, err := client.SendTx(ctx, &pb.SendTxRequest{RawTx: rawTx})
	if err != nil {
		fmt.Println("failed to send raw tx: ", err)
		return
	}
	fmt.Println("raw tx sent by BlockRazor, tx hash is ", res.TxHash)
}

```
{% endtab %}
{% endtabs %}

#### 返回示例

**正常**

```json
{
 "tx_hash":"0x2944……b2188f"
}
```

**异常**

```
rpc error: code = Unknown desc = invalid transaction format
```

### Proto

`relay.proto`文件代碼如下：

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

### 代码示例

[https://github.com/BlockRazorinc/eth\_relay\_example](https://github.com/BlockRazorinc/eth_relay_example)

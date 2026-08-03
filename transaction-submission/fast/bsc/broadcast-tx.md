---
description: 介紹BlockRazor BSC Fast模式的 Broadcast Tx 接口以及集成方法
---

# Broadcast Tx

### 什麼是 Broadcast Tx

Broadcast Tx 是 BlockRazor 提供的一種快速交易發送服務，用於幫助用戶以更低延遲將交易發送到鏈上。它屬於 Fast 體系的一部分，但與需要在交易中附加 tip 的標準 Fast 模式不同，Broadcast Tx 不要求用戶在交易內部額外支付 tip，因此更適合作為低門檻的快速發送入口使用。

目前Broadcast Tx提供两种方法：`SendTx` 和 `SendTxs`，分别用来发单笔交易和批量交易。

需要注意的是，Broadcast Tx 雖然屬於 Fast 體系，但它並不等同於具備完整交易保護能力的私有發送通道。Broadcast Tx 發送的交易會進入公開傳播路徑，因此不具備 MEV 防護能力。

### 什麼場景下選擇 Broadcast Tx

* **不需要 tip，接入門檻更低**\
  與標準 Fast 模式不同，Broadcast Tx 不要求在交易中附加 tip，因此更適合希望快速接入、但不想修改交易激勵結構的用戶。
* **適合對速度有要求、但暫不強調 MEV 防護的場景**\
  如果你的重點是盡量更快地把交易發出去，而不是通過私有路徑隱藏交易或抵御 sandwich、frontrunning 等風險，那麼 Broadcast Tx 會是一個更直接的選擇。

### Benchmark

在發交易上鏈延遲的測試中，我們分別在 Dublin、Frankfurt、Tokyo 和 Virginia 四個區域，對 BlockRazor 與普通 Node 進行多輪對比。評估標準分為兩層：首先比較交易是否更早進入鏈上區塊；如果雙方均在同一區塊內完成上鏈，則進一步比較 transaction index 的先後順序。結果如下：

<table><thead><tr><th width="187.37109375">Region</th><th width="511.00390625">BlockRazor Total Lead Rate</th></tr></thead><tbody><tr><td>Dublin</td><td><strong>88.7%</strong><br><strong>-</strong> Same block but better index: 82.9%<br>- Earlier block: 5.8%</td></tr><tr><td>Frankfurt</td><td><strong>85.4%</strong><br><strong>-</strong> Same block but better index: 81.3%<br>- Earlier block: 4.2%</td></tr><tr><td>Tokyo</td><td><strong>85.1%</strong><br><strong>-</strong> Same block but better index: 78.7%<br>- Earlier block: 6.4%</td></tr><tr><td>Virginia</td><td><strong>97.9%</strong><br><strong>-</strong> Same block but better index: 95.7%<br>- Earlier block: 2.1%</td></tr></tbody></table>

從比例結果來看，BlockRazor 在各區域整體保持了更高的領先率。Dublin 區域中，BlockRazor 領先率為 88.7%，Frankfurt 區域領先 85.4%，Tokyo 區域領先 85.1%，Virginia 區域領先率達到 97.9%。

進一步看領先方式，BlockRazor 的優勢主要體現為兩點：一是在大多數領先輪次中，即使與普通 Node 同時進入同一區塊，BlockRazor 仍能獲得更靠前的 transaction index；二是在部分輪次中，BlockRazor 還能直接領先 1 個區塊完成上鏈。這說明 BlockRazor 不僅更容易進入更早的鏈上確認窗口，也更有機會在同區塊競爭中取得更優排序位置，從而形成穩定的提交優勢。

### 端點

<table><thead><tr><th width="154">地區</th><th>Relay地址</th></tr></thead><tbody><tr><td>法蘭克福</td><td>64.130.47.75:50051</td></tr><tr><td>東京</td><td>63.254.162.18:50051</td></tr><tr><td>都柏林</td><td>141.98.217.82:50051</td></tr><tr><td>弗吉尼亞</td><td>208.91.105.204:50051</td></tr></tbody></table>

### 價格

<table><thead><tr><th width="153.44921875">用戶類型</th><th>限流</th><th>價格</th></tr></thead><tbody><tr><td>新註冊用戶</td><td><p><code>SendTx</code></p><ul><li>TPS：10 Txs / 5s</li><li>每日交易上限：10</li></ul></td><td>免費</td></tr><tr><td>付費用戶</td><td><p><code>SendTx</code></p><ul><li>TPS：100 Txs / 5s</li><li>每日交易上限：100000<br></li></ul><p><code>SendTxs</code></p><ul><li>BPS：4 batches / 1s</li><li>Txs per Batch：10</li></ul></td><td>$50 / 日<br>$500 / 月<br><br><a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=bsc&#x26;serviceId=bsc_fast_tx&#x26;billing=day" class="button primary small">訂閱</a></td></tr></tbody></table>

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
	"encoding/hex"
	"fmt"
	"math/big"

	// directory of the generated code using the provided relay.proto file
	pb "github.com/BlockRazorinc/relay_example/protobuf"
	"github.com/ethereum/go-ethereum/common"
	"github.com/ethereum/go-ethereum/core/types"
	"github.com/ethereum/go-ethereum/crypto"
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

	// blockrazor relay endpoint address
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

	// use the Gateway client connection interface.
	client := pb.NewGatewayClient(conn)

	// create context
	ctx := context.Background()

	// replace with your address
	from_private_address := "42b565……44d05c"
	to_public_address := "0x4321……3f1c66"

	// replace with your transaction data
	nonce := uint64(1)
	toAddress := common.HexToAddress(to_public_address)
	var data []byte
	gasPrice := big.NewInt(1e9)
	gasLimit := uint64(22000)
	value := big.NewInt(0)
	chainid := types.NewEIP155Signer(big.NewInt(56))

	// create new transaction
	tx := types.NewTransaction(nonce, toAddress, value, gasLimit, gasPrice, data)

	privateKey, err := crypto.HexToECDSA(from_private_address)
	if err != nil {
		fmt.Println("fail to casting private key to ECDSA")
		return
	}

	// sign transaction by private key
	signedTx, err := types.SignTx(tx, chainid, privateKey)
	if err != nil {
		fmt.Println("fail to sign transaction")
		return
	}

	// use rlp to encode your transaction
	body, _ := rlp.EncodeToBytes([]types.Transaction{*signedTx})

	// encode []byte to string
	encode_tx := hex.EncodeToString(body)

	// send raw tx by BlockRazor
	res, err := client.SendTx(ctx, &pb.SendTxRequest{Transaction: encode_tx})

	if err != nil {
		fmt.Println("failed to send raw tx: ", err)
		return
	} else {
		fmt.Println("raw tx sent by BlockRazor, tx hash is ", res.TxHash)
	}

}
```
{% endtab %}

{% tab title="HTTP" %}
```bash
curl <Endpoint_URL> \
    -X POST \
    -H "Content-Type: application/json" \
    -d '{"jsonrpc":"2.0","method":"eth_sendRawTransaction","params":["0x02f8……869514"],"id":1}'
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



### SendTxs

#### 請求參數

<table><thead><tr><th width="148">參數</th><th width="59">必選</th><th width="137">格式</th><th width="215">示例</th><th>描述</th></tr></thead><tbody><tr><td>Transactions</td><td>是</td><td>Array[String]</td><td>["0xf8……8219", "0xcb……2ac0"]</td><td>經過簽名的raw tx列表</td></tr></tbody></table>

#### 請求示例

{% tabs %}
{% tab title="Go" %}
```go
package main

import (
	"context"
	"encoding/hex"
	"fmt"
	"math/big"

	// directory of the generated code using the provided relay.proto file
	pb "github.com/BlockRazorinc/relay_example/protobuf"
	"github.com/ethereum/go-ethereum/common"
	"github.com/ethereum/go-ethereum/core/types"
	"github.com/ethereum/go-ethereum/crypto"
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

	// create context
	ctx := context.Background()

	// replace with your address
	from_private_address1 := "6c0456……8b8003"
	from_private_address2 := "42b565……44d05c"
	to_public_address := "0x4321……3f1c66"

	// replace with your transaction data
	nonce1 := uint64(1)
	nonce2 := uint64(1)
	toAddress := common.HexToAddress(to_public_address)
	var data []byte
	gasPrice := big.NewInt(1e9)
	gasLimit := uint64(22000)
	value := big.NewInt(0)
	chainid := types.NewEIP155Signer(big.NewInt(56))

	// create new transaction
	tx1 := types.NewTransaction(nonce1, toAddress, value, gasLimit, gasPrice, data)
	tx2 := types.NewTransaction(nonce2, toAddress, value, gasLimit, gasPrice, data)

	privateKey1, err := crypto.HexToECDSA(from_private_address1)
	if err != nil {
		fmt.Println("fail to casting private key to ECDSA")
		return
	}

	privateKey2, err := crypto.HexToECDSA(from_private_address2)
	if err != nil {
		fmt.Println("fail to casting private key to ECDSA")
		return
	}

	// sign transaction by private key
	signedTx1, err := types.SignTx(tx1, chainid, privateKey1)
	if err != nil {
		fmt.Println("fail to sign transaction")
		return
	}

	signedTx2, err := types.SignTx(tx2, chainid, privateKey2)
	if err != nil {
		fmt.Println("fail to sign transaction")
		return
	}

	// use rlp to encode your transaction
	body, _ := rlp.EncodeToBytes([]types.Transaction{*signedTx1, *signedTx2})

	// encode []byte to string
	encode_txs := hex.EncodeToString(body)

	// send raw tx batch by BlockRazor
	res, err := client.SendTxs(ctx, &pb.SendTxsRequest{Transactions: encode_txs})

	if err != nil {
		fmt.Println("failed to send raw tx batch: ", err)
		return
	} else {
		fmt.Println("raw tx batch sent by BlockRazor, tx hashes are ", res.TxHashs)
	}

}
```
{% endtab %}
{% endtabs %}

#### 返回示例

**正常**

```json
{
 "tx_hash":["0xdb95……584813", "0x887d……7aba19"]
}
```

**异常**

```
rpc error: code = Unknown desc = invalid transaction format
```

### Proto

`relay.proto`文件代碼如下：

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

### 代码示例

[https://github.com/BlockRazorinc/relay\_example](https://github.com/BlockRazorinc/relay_example)


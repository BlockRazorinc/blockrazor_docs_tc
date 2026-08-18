---
description: 介紹BlockRazor BSC Block Stream的服務、應用場景以及接入方法
metaLinks:
  canonical: newblocks.md
  alternates:
    - >-
      https://app.gitbook.com/s/jbyfG8gOgcdsK3wVxNdQ/streams/block-stream/bsc/newblocks
---

# BSC Block Stream

### BSC NewBlocks是什麼

NewBlocks 是 BlockRazor 提供的高性能區塊數據流服務，用於低延遲訂閱鏈上最新區塊與已確認交易。NewBlocks 幫助用戶更早接收到最新區塊內容，並將其中的區塊頭、交易列表和後續出塊者信息，以更低延遲接入自己的監控或策略系統。

NewBlocks 基於 [BEF](../../../he-xin-ji-shu/blockchain-edge-fabric.md) 分發最新區塊數據。當區塊在網絡中產生並開始傳播時，BlockRazor 會在多個核心區域盡早接收區塊，再通過低延遲鏈路轉發給訂閱方，縮短用戶接收區塊數據的時間。

### BSC NewBlocks的應用場景

* **Confirmed交易監控：** 實時接收最新區塊中的已確認交易，用於監控目標地址、熱門合約或異常交易行為
* **區塊級數據分析：** 獲取區塊頭、交易列表與後續出塊者信息，用於區塊研究、節點觀測與網絡狀態分析
* **策略數據輸入：** 作為交易系統的 confirmed 數據源，與 Public Mempool、Transaction Submission 等能力配合使用，構建更完整的監控與執行鏈路

### Benchmark

在接收新區塊的延遲測試中，我們分別在 Dublin、Frankfurt、Tokyo 和 Virginia 四個區域，對 BlockRazor 與普通 Node 進行了對比。評估方式基於雙方對同一區塊的接收時間差，統計在可比樣本中由哪一方更早收到新區塊。

<table><thead><tr><th width="124.60546875">Region</th><th width="193.2421875">BlockRazor Lead Rate</th><th>Avg Lead</th><th>P50 Lead</th><th>P90 Lead</th></tr></thead><tbody><tr><td>Dublin</td><td><strong>100.0%</strong></td><td>60.4 ms</td><td>50.5 ms</td><td>90.7 ms</td></tr><tr><td>Frankfurt</td><td><strong>100.0%</strong></td><td>61.5 ms</td><td>54.7 ms</td><td>92.3 ms</td></tr><tr><td>Tokyo</td><td><strong>100.0%</strong></td><td>239.1 ms</td><td>145.7 ms</td><td>622.9 ms</td></tr><tr><td>Virginia</td><td><strong>99.5%</strong></td><td>69.0 ms</td><td>58.3 ms</td><td>121.6 ms</td></tr></tbody></table>

從結果來看，BlockRazor 在四個區域均表現出穩定領先。Frankfurt、Tokyo 和 Dublin 三個區域中，BlockRazor 的領先率均達到 100%；Virginia 區域的領先率也達到 99.5%。從領先幅度看，BlockRazor 在不同區域的平均領先時間約為 60.4 ms、61.5 ms、239.1 ms 和 69.0 ms；在 P90 維度下，領先幅度分別達到 90.7 ms、92.3 ms、622.9 ms 和 121.6 ms。

這表明在新區塊到達這一關鍵鏈路上，BlockRazor 能夠更穩定地搶先於普通 Node 接收到區塊數據。

### 端點

<table><thead><tr><th width="154">地區</th><th>Relay地址</th></tr></thead><tbody><tr><td>法蘭克福</td><td>64.130.47.75:50051</td></tr><tr><td>東京</td><td>63.254.162.18:50051</td></tr><tr><td>都柏林</td><td>141.98.217.82:50051</td></tr><tr><td>弗吉尼亞</td><td>208.91.105.204:50051</td></tr></tbody></table>

### 價格

價格為$50 / 條 / 日和$500 / 條 / 月。<a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=bsc&#x26;serviceId=bsc_block_stream&#x26;billing=day" class="button primary small">訂閱</a>

{% hint style="info" %}
數據流的可訂閱數量按所有地區共享計算。比如購買 1 條，則僅可在 1 個地區訂閱，其他地區將無法訂閱。
{% endhint %}

### 請求參數

<table><thead><tr><th width="165">參數</th><th width="74">必選</th><th width="103">格式</th><th width="87">示例</th><th>描述</th></tr></thead><tbody><tr><td>NodeValidation</td><td>是</td><td>boolean</td><td>false</td><td>該字段目前僅支持設置為false，relay會以更低延遲推送全部新區塊（未經校驗）</td></tr></tbody></table>

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
	stream, err := client.NewBlocks(ctx, &pb.BlocksRequest{NodeValidation: false})
	if err != nil {
		fmt.Println("failed to subscribe new block: ", err)
		return
	}

	for {

		reply, err := stream.Recv()
		if err != nil {
			fmt.Println("stream receive error: ", err)
		}

		fmt.Println("recieve new block, block hash is ", reply.Hash)
	}
}
```
{% endtab %}
{% endtabs %}

#### Proto

`relay.proto`文件代碼如下：

```go
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
  string requests_hash = 21;
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
	"hash": "0xe4a85aaa8cf4c85c4abf59c06b744ae680941e7ffba351fd4c166f0264e860de",
	"header": {
		"parent_hash": "0x0105992a6c305b0bbfbf7b4eebbbc92c4dca1bb2a18c1f93c551afc2c73c0668",
		"sha3_uncles": "0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347",
		"miner": "0x9f1b7FAE54BE07F4FEE34Eb1aaCb39A1F7B6FC92",
		"state_root": "0xce2a7e35a643e5840e510df9eea8ba9177dd62fe743439ef159d7d9e783afd28",
		"transactions_root": "0x63c952adbe28ef60d8e723acec299ca62a915b51d05b060cb6fe97bb28814ab3",
		"receipts_root": "0xd3232a5308a69591dda132bd4ce1c1dbd5968ed9feee5f1385a5e7ad1b10527b",
		"logs_bloom": "8477149424673580296520836103527692456522284192111573121045681184670018737794069194486118998020237483301054022136573687133364882576385281526094331485603954025070865625599001965016471857986082361337851986790329901990002923474000909032395056767065187980257808021700240863783968353544053840361314138235944346718686049569138609472702141575196319415411746602993576034439427711191239874622732325947862558346553652593648034520192193214092215911630192819139737571343595880627533215841996399050061528845101804940660169259391681335118771766679784629118424769368884717750281985023526825099358014255162966726322082309693440030370",
		"difficulty": "2",
		"number": "40370748",
		"gas_limit": 139997863,
		"gas_used": 12180159,
		"timestamp": 1720674742,
		"extra_data": "2IMBBAqEZ2V0aIhnbzEuMjEuNYVsaW51eAAAAEj6ewX4tYMf//+4YKyfFdynrjSSLQKJj9FdhVSwaDFtnAdMnqNzKeVm2agkWE79uSpjZ2Wbrxt2eCFSvBRuPLIhONzRYpQgx/DeH/Xrhsz/A+EZw4BFaOVV3ch76i1QdJy1CADUqoc/XEaBHvhMhAJoAjqg3GksrNaxsaC5pxx8JS0YFpA6OQh7ofh7pTmra2Ve7lOEAmgCO6ABBZkqbDBbC7+/e07ru8ksTcobsqGMH5PFUa/CxzwGaIBHk8oYx40FGg4hnpMSQsTiOEAObH2DV3ytkUGOdEk4KENXgVf0/xsCA56w2r/VIqP7ux7HCD9vrh7H7fjxdU3xAA==",
		"mix_hash": "0x0000000000000000000000000000000000000000000000000000000000000000",
		"noncpe":0,
		"base_fee_per_gas":0,
		"withdrawals_root":"0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
		"blob_gas_used": 131072,
		"excess_blob_gas":0,
		"parent_beacon_block_root":""
	
	},
	"nextValidator": [{
		"block_height": "40370749",
		"coinbase": "0xbdcc079bbb23c1d9a6f36aa31309676c258abac7"
	}, {
		"block_height": "40370750",
		"coinbase": "0xc2d534f079444e6e7ff9dabb3fd8a26c607932c8"
	}],
	"txs": [{
		"raw_tx": "+K6DzSWShQEqBfIAgwEwY5R2021E3EWV6NLrOtdF8XXtoTQoT4C4RKkFnLsAAAAAAAAAAAAAAAAFWjs3lXv70zRb7Zlo5+jdVtZwZgAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAGBV8GygaywAAgZOguQQg9l/3+bmldXAcm9lsSsgxwq8m+wqcXh/KwbKJ7E+gAS8g6ZuII8KJWFBFpVzTcyptzqfro00WDKR3oi1ly7g="
	}, {
		"raw_tx": "+QVPgxUyKIUBKgXyAIMHoSCUE3kk18NoFuDcrwFuthfMLJLAV4KAuQTkyYB1OQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAACAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAyAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAEAAAAAQEBAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAoAAAAAAAAAAAAAAAGQptag/qdv1AH5JjIvJ4fgACqZ4AQoECA4FDA0HAQsPAgAJBgMAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAGAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAEAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAw9aP+EAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAADD2B6d4AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAMPYNaEAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAw9jajbAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAADD2a1DgAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAMPZrUOAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAw9qNqnAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAADD2o2qcAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAMPajapwAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAw9u7fAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAADD3ePNwAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAMPiGlgAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAw+IaWAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAADD4n5xAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAMPqOuWAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAw+o8GuAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAbIQBTEAMmDL3iUlt3D7Hrj6ACjQhWYvzwIOV06fd5+CxHWRbBH4sYJEbXQ/y6+cimsuGJSFGXoSkH5dZf7RDlpIA584nEMYIrJUVhB3PQZWCiY951T9xxDCPwvuqG40jEYJR68n788tw7y1nUnZwIJVWAoko+4Z/immcv2IcTzZ53J2RNPMiqAlb21gAwDfKQtEF7HpWEwn422R1b9VDfEey+3vxMAzZcisrwfRh49shbMLJpz4CnFt5FyAS9eI9AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABhy0jMwJkMmVvzRghVWVS2LwKSffduFZtzpO03R2sucDE1hqfyr8bpMWOG7oHhhdknlcQ/+leSGQf9X8GF1g7MBIWrGp/Ri03p4GYSY3Rj6RaN+SWlHEPySIll217WvNyHf3dtjfz9ic4gXWSc4l+vP5fXff/59EgoqDMhgEOP/iOfCAJsJt5Uisq/0Cpz/+2fzV4KknIckM8W1VsH8S34kLRauuV1UKaY7AU0K0BIA1dr8HMkOm/Ci+fDyuSFJY94GToC30fN+eoJ0+scIcdj9XpEmHUxWAT6TtSwk1zVm14HyboHCzcmyYjRIcHxPeMrJiwXSKXkgCTggJw9V0ETOLYuZL"
	}, {
		"raw_tx": "+K6DzSWThQEqBfIAgwEw4JRV05gyb5kFn/d1SFJGmZAnsxl5VYC4RKkFnLsAAAAAAAAAAAAAAADt2wDQgCLygqAXnLTDVpf1DvOxOAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAALA2UyUvg7vwAgZOgReRi2miA5qA+ERtjgmiOmqwmtCDhvtj0TFKrtUAuQWegP7xd7Gj89uQM03KMcfceKvAKkxwwajcJB1M8xHQlR4g="
	}, {
		"raw_tx": "+QFrBYTodUcAgwQ7j5Td7QSbsOJzaTkPQPjEolIpjjUwjoC5AQS39rSuAAAAAAAAAAAAAAAAVdOYMm+ZBZ/3dUhSRpmQJ7MZeVUAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAHP5UVLi+/8gAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAcWBJiX8HPgAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAoAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABAAAAAAAAAAAAAAAAisdqUcyVDZgi1ouD/hrZezLNWA0AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAIGUoDDbxwrT1VjxX+u9C70/BMIgFYEATNA/P4NaAIu8AYupoFgRK+X8L9sYxgVNJobVpne44NbM/wQTYLLDGqanoAeH"
	}]
}
```

**异常**

```
rpc error: code = Unknown desc = data streams have exceeded its max limit [5]
```

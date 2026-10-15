---
description: 瞭解 BlockRazor Solana Transaction Sending模式下Send Transaction的 Go 請求示例
metaLinks:
  canonical: go.md
  alternates:
    - >-
      https://app.gitbook.com/s/jbyfG8gOgcdsK3wVxNdQ/transaction-submission/transaction-sending/solana/send-transaction/request-example/go
---

# Solana Send Transaction Go Example

{% hint style="info" %}
Solana發送交易的服務不和訂閱計劃綁定，可前往 [Authentication](../../../../../get-started/authentication.md) 獲取API KEY，默认限流为3 TPS。如需提升限流標準，請[聯繫](https://discord.com/invite/qqJuwRb8Nh)我們，我們會在第一時間處理
{% endhint %}

[代碼示例](https://github.com/BlockRazorinc/solana-trader-client-go/tree/main/example)

### 請求示例

{% tabs %}
{% tab title="HTTP" %}
```go
package main

import (
	"bytes"
	"context"
	"encoding/json"
	"fmt"
	"io"
	"net/http"
	"time"

	examples "github.com/BlockRazorinc/solana-trader-client-go/example"
	"github.com/gagliardetto/solana-go"
	"github.com/gagliardetto/solana-go/programs/system"
	"github.com/gagliardetto/solana-go/rpc"
)

const (
	// Example endpoints only. Always use the current endpoints published on the BlockRazor website.
	httpEndpoint        = "http://frankfurt.solana.blockrazor.xyz:443/sendTransaction"
	healthEndpoint      = "http://frankfurt.solana.blockrazor.xyz:443/health"
	tipAccountsEndpoint = "http://frankfurt.solana.blockrazor.xyz:443/getTipAccounts"
	mainNetRPC          = ""
	authKey             = ""
	privateKey          = ""
	publicKey           = ""
	amount              = 200_000
	tipAmount           = 100_000
	mode                = "fast"
)

var httpClient = &http.Client{
	Timeout: 10 * time.Second,
}

type SendRequest struct {
	Transaction string `json:"transaction"`
	Mode        string `json:"mode"`
}

type SendResponse struct {
	Signature string `json:"signature"`
}
type HealthResponse struct {
	Result string `json:"result"`
}

func main() {
	// Pre-warm: perform an initial health check to establish the HTTP connection
	err := pingHealth()
	if err != nil {
		fmt.Printf("health check failed: %v\n", err)
	}
	// Start a background goroutine to periodically send /health requests
	// For low-frequency users, this keeps the HTTP connection alive (warm)
	go func() {
		for {
			err := pingHealth()
			if err != nil {
				fmt.Printf("health check failed: %v\n", err)
			}
			time.Sleep(30 * time.Second)
		}
	}()
	// send transactions
	if err := sendTx(); err != nil {
		fmt.Printf("send tx failed: %v\n", err)
	}
}

func pingHealth() error {
	req, err := http.NewRequest("POST", healthEndpoint, bytes.NewBufferString("{}"))
	if err != nil {
		return err
	}
	req.Header.Set("Content-Type", "application/json")
	req.Header.Set("apikey", authKey)
	resp, err := httpClient.Do(req)
	if err != nil {
		return err
	}
	defer resp.Body.Close()
	// ⚠️ Important Note:
	// According to the Go net/http documentation, in order for the underlying TCP connection
	// to be reused (i.e. kept alive), the response body must be fully read and closed.
	// Otherwise, the Transport may not reuse the connection for future requests.
	// Reference: https://pkg.go.dev/net/http#Response
	// > "The default HTTP client's Transport may not reuse HTTP/1.x 'keep-alive' TCP connections
	//    if the Body is not read to completion and closed."

	// Read the full response body to enable connection reuse
	bodyBytes, _ := io.ReadAll(resp.Body)
	var healthRes HealthResponse
	if err := json.Unmarshal(bodyBytes, &healthRes); err != nil {
		return fmt.Errorf("decode error: %v", err)
	}

	return nil
}

func sendTx() error {
	account, err := solana.WalletFromPrivateKeyBase58(privateKey)
	if err != nil {
		return err
	}
	receivePub := solana.MustPublicKeyFromBase58(publicKey)
	tipPub, err := examples.HTTPTipAccount(context.Background(), httpClient, tipAccountsEndpoint)
	if err != nil {
		return err
	}

	rpcClient := rpc.New(mainNetRPC)
	blockhash, err := rpcClient.GetLatestBlockhash(context.TODO(), rpc.CommitmentFinalized)
	if err != nil {
		return fmt.Errorf("[get blockhash] %v", err)
	}

	transferIx := system.NewTransferInstruction(amount, account.PublicKey(), receivePub).Build()
	tipIx := system.NewTransferInstruction(tipAmount, account.PublicKey(), tipPub).Build()

	tx, err := solana.NewTransaction(
		[]solana.Instruction{tipIx, transferIx},
		blockhash.Value.Blockhash,
		solana.TransactionPayer(account.PublicKey()),
	)
	if err != nil {
		return fmt.Errorf("build tx error: %v", err)
	}

	_, err = tx.Sign(func(key solana.PublicKey) *solana.PrivateKey {
		if account.PublicKey().Equals(key) {
			return &account.PrivateKey
		}
		return nil
	})
	if err != nil {
		return fmt.Errorf("sign tx error: %v", err)
	}

	txBase64, err := tx.ToBase64()
	if err != nil {
		return err
	}

	reqBody := SendRequest{
		Transaction: txBase64,
		Mode:        mode,
	}
	jsonBody, _ := json.Marshal(reqBody)

	httpReq, err := http.NewRequest("POST", httpEndpoint, bytes.NewBuffer(jsonBody))
	if err != nil {
		return err
	}
	httpReq.Header.Set("Content-Type", "application/json")
	httpReq.Header.Set("apikey", authKey)
	resp, err := httpClient.Do(httpReq)
	if err != nil {
		return fmt.Errorf("send http error: %v", err)
	}
	defer resp.Body.Close()

	bodyBytes, _ := io.ReadAll(resp.Body)
	var sendRes SendResponse
	if err := json.Unmarshal(bodyBytes, &sendRes); err != nil {
		return fmt.Errorf("decode error: %v", err)
	}
	fmt.Printf("[send tx] response: %+v\n", sendRes)
	return nil
}
```
{% endtab %}

{% tab title="gRPC" %}
```go
package main

import (
	"context"
	"fmt"
	"time"

	examples "github.com/BlockRazorinc/solana-trader-client-go/example"
	pb "github.com/BlockRazorinc/solana-trader-client-go/pb/serverpb"

	"github.com/gagliardetto/solana-go"
	"github.com/gagliardetto/solana-go/programs/system"
	"github.com/gagliardetto/solana-go/rpc"
	"google.golang.org/grpc"
	"google.golang.org/grpc/credentials/insecure"
)

const (
	// This example endpoint may change. Always use the current endpoint published on the BlockRazor website.
	blzRelayEndpoint = "frankfurt.solana-grpc.blockrazor.xyz:80"
	// replace your solana rpc endpoint
	mainNetRPC = ""
	// replace your authKey
	authKey = ""
	// relace your private key(base58)
	privateKey = ""
	// publicKey(base58)
	publicKey = ""
	// transfer amount
	amount = 200_000
	// send mode
	mode = "fast"

	// tip amount
	tipAmount = 100_000
)

func main() {
	var err error
	account, err := solana.WalletFromPrivateKeyBase58(privateKey)
	receivePub := solana.MustPublicKeyFromBase58(publicKey)

	// setup grpc connect
	conn, err := grpc.NewClient(blzRelayEndpoint,
		grpc.WithTransportCredentials(insecure.NewCredentials()),
		grpc.WithPerRPCCredentials(&Authentication{authKey}),
	)
	if err != nil {
		panic(fmt.Sprintf("connect error: %v", err))
	}

	// use the Gateway client connection interface
	client := pb.NewServerClient(conn)

	// Pre-warm: perform an initial health check to establish the grpc connection
	err = pingHealth(client)
	if err != nil {
		fmt.Printf("health check failed: %v\n", err)
	}
	// Start a background goroutine to periodically send /health requests
	// For low-frequency users, this keeps the grpc connection alive (warm)
	go func() {
		for {
			err := pingHealth(client)
			if err != nil {
				fmt.Printf("health check failed: %v\n", err)
			}
			time.Sleep(30 * time.Second)
		}
	}()

	// new rpc client and get latest block hash
	rpcClient := rpc.New(mainNetRPC)
	blockhash, err := rpcClient.GetLatestBlockhash(context.TODO(), rpc.CommitmentFinalized)
	if err != nil {
		panic(fmt.Sprintf("[get latest block hash] error: %v", err))
	}

	tipAccount, err := examples.GRPCTipAccount(context.Background(), client)
	if err != nil {
		panic(fmt.Sprintf("[get tip accounts] error: %v", err))
	}

	// construct instruction
	transferIx := system.NewTransferInstruction(amount, account.PublicKey(), receivePub).Build()
	tipIx := system.NewTransferInstruction(tipAmount, account.PublicKey(), tipAccount).Build()

	// construct transation, replace your transation
	tx, err := solana.NewTransaction(
		[]solana.Instruction{tipIx, transferIx},
		blockhash.Value.Blockhash,
		solana.TransactionPayer(account.PublicKey()),
	)
	if err != nil {
		panic(fmt.Sprintf("new tx error: %v", err))
	}

	// transaction sign
	_, err = tx.Sign(
		func(key solana.PublicKey) *solana.PrivateKey {
			if account.PublicKey().Equals(key) {
				return &account.PrivateKey
			}
			return nil
		},
	)
	if err != nil {
		panic(fmt.Sprintf("sign tx error: %v", err))
	}

	txBase64, err := tx.ToBase64()
	if err != nil {
		panic(fmt.Sprintf("encode tx error: %v", err))
	}

	sendRes, err := client.SendTransaction(context.TODO(), &pb.SendRequest{
		Transaction: txBase64,
		Mode:        mode,
	})
	if err != nil {
		panic(fmt.Sprintf("[send tx] error: %v", err))
	}

	fmt.Printf("[send tx] response: %+v \n", sendRes)
	return
}

type Authentication struct {
	apiKey string
}

func (a *Authentication) GetRequestMetadata(context.Context, ...string) (map[string]string, error) {
	return map[string]string{"apikey": a.apiKey}, nil
}

func (a *Authentication) RequireTransportSecurity() bool {
	return false
}

func pingHealth(client pb.ServerClient) error {
	// grpc request warmup
	healthRes, err := client.GetHealth(context.Background(), &pb.HealthRequest{})
	if err != nil {
		return err
	}
	fmt.Printf("[health] response: %+v \n", healthRes.Status)
	return err
}
```
{% endtab %}

{% tab title="Proto" %}
```go
syntax = "proto3";

package serverpb;

option go_package = "./pb/serverpb";

service Server {
    rpc SendTransaction(SendRequest) returns(SendResponse) {};

    rpc SendBatch(SendBatchRequest) returns(SendBatchResponse) {};

    rpc SendBundle(SendBundleRequest) returns(SendBundleResponse) {};

    rpc GetHealth(HealthRequest) returns(HealthResponse) {};

    rpc GetTipAccounts(GetTipAccountsRequest) returns(GetTipAccountsResponse) {};
}

message SendRequest {
    string transaction = 1;
    string mode = 2;
    int32 safeWindow = 3;
    bool revertProtection = 4;
}

message SendResponse {
    string signature = 1;
}

message SendBundleRequest {
    repeated string transactions = 1;
}

message SendBundleResponse {
    string signature = 1;
}

message SendBatchRequest {
    repeated string transactions = 1;
    string mode = 2;
    int32 safeWindow = 3;
    bool revertProtection = 4;
}

message SendBatchResponse {
    repeated BatchResponse result = 1;
}

message BatchResponse {
    string signature = 1;
    string error = 2;
}

message HealthRequest {
}

message HealthResponse {
    string status = 1;
}

message GetTipAccountsRequest {
}

message GetTipAccountsResponse {
    repeated string tipAccounts = 1;
}
```
{% endtab %}
{% endtabs %}


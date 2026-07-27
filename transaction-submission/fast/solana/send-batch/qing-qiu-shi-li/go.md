---
description: 介紹BlockRazor Solana Fast模式下Send Batch的Go請求示例
---

# Go

### 請求示例

{% tabs %}
{% tab title="HTTP" %}
{% code overflow="wrap" %}
```go
package main

import (
	"bytes"
	"context"
	"encoding/json"
	"fmt"
	"io"
	"math/rand"
	"net/http"
	"time"

	"github.com/gagliardetto/solana-go"
	"github.com/gagliardetto/solana-go/programs/system"
	"github.com/gagliardetto/solana-go/rpc"
)

const (
	httpEndpoint     = "http://frankfurt.solana.blockrazor.xyz:443/sendBatch"
	healthEndpoint   = "http://frankfurt.solana.blockrazor.xyz:443/health"
	mainNetRPC       = ""
	authKey          = ""
	privateKey       = ""
	publicKey        = ""
	amount           = 200_000
	tipAmount        = 1_000_000
	mode             = "fast"
	safeWindow       = 5
	revertProtection = false
)

var tipAccounts = []string{
	"Gywj98ophM7GmkDdaWs4isqZnDdFCW7B46TXmKfvyqSm",
	"FjmZZrFvhnqqb9ThCuMVnENaM3JGVuGWNyCAxRJcFpg9",
	"6No2i3aawzHsjtThw81iq1EXPJN6rh8eSJCLaYZfKDTG",
	"A9cWowVAiHe9pJfKAj3TJiN9VpbzMUq6E4kEvf5mUT22",
	"68Pwb4jS7eZATjDfhmTXgRJjCiZmw1L7Huy4HNpnxJ3o",
	"4ABhJh5rZPjv63RBJBuyWzBK3g9gWMUQdTZP2kiW31V9",
	"B2M4NG5eyZp5SBQrSdtemzk5TqVuaWGQnowGaCBt8GyM",
	"5jA59cXMKQqZAVdtopv8q3yyw9SYfiE3vUCbt7p8MfVf",
	"5YktoWygr1Bp9wiS1xtMtUki1PeYuuzuCF98tqwYxf61",
	"295Avbam4qGShBYK7E9H5Ldew4B3WyJGmgmXfiWdeeyV",
	"EDi4rSy2LZgKJX74mbLTFk4mxoTgT6F7HxxzG2HBAFyK",
	"BnGKHAC386n4Qmv9xtpBVbRaUTKixjBe3oagkPFKtoy6",
	"Dd7K2Fp7AtoN8xCghKDRmyqr5U169t48Tw5fEd3wT9mq",
	"AP6qExwrbRgBAVaehg4b5xHENX815sMabtBzUzVB4v8S",
}

var httpClient = &http.Client{
	Timeout: 10 * time.Second,
}

type SendRequest struct {
	Transactions     []string `json:"transactions"`
	Mode             string   `json:"mode"`
	SafeWindow       int      `json:"safeWindow"`
	RevertProtection bool     `json:"revertProtection"`
}

// BatchResponse .
type BatchResponse struct {
	Result []BatchResponseItem `json:"result"`
	Error  string              `json:"error"`
}

// BatchResponseItem .
type BatchResponseItem struct {
	Signature string `json:"signature"`
	Error     string `json:"error"`
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
	// send batch
	if err := sendBatch(); err != nil {
		fmt.Printf("send tx failed: %v\n", err)
	}
}

func pingHealth() error {
	req, err := http.NewRequest("GET", healthEndpoint, nil)
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

func sendBatch() error {
	account, err := solana.WalletFromPrivateKeyBase58(privateKey)
	if err != nil {
		return err
	}
	receivePub := solana.MustPublicKeyFromBase58(publicKey)
	tipPub := solana.MustPublicKeyFromBase58(tipAccounts[rand.Intn(len(tipAccounts))])

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
		Transactions:     []string{txBase64},
		Mode:             mode,
		SafeWindow:       safeWindow,
		RevertProtection: revertProtection,
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
		return fmt.Errorf("send batch error: %v", err)
	}
	defer resp.Body.Close()

	bodyBytes, _ := io.ReadAll(resp.Body)
	var sendRes BatchResponse
	if err := json.Unmarshal(bodyBytes, &sendRes); err != nil {
		return fmt.Errorf("decode error: %v", err)
	}
	fmt.Printf("[send batch] response: %+v\n", sendRes)
	return nil
}
```
{% endcode %}
{% endtab %}

{% tab title="gRPC" %}
{% code overflow="wrap" %}
```go
package main

import (
	"context"
	"fmt"
	"math/rand"
	"time"

	pb "github.com/BlockRazorinc/solana-trader-client-go/pb/serverpb"

	"github.com/gagliardetto/solana-go"
	"github.com/gagliardetto/solana-go/programs/system"
	"github.com/gagliardetto/solana-go/rpc"
	"google.golang.org/grpc"
	"google.golang.org/grpc/credentials/insecure"
)

const (
	// BlockRazor relay endpoint address
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
	// safeWindow
	safeWindow = 5
	// revertProtection
	revertProtection = false
	// tip amount
	tipAmount = 1_000_000
)

var tipAccounts = []string{
	"Gywj98ophM7GmkDdaWs4isqZnDdFCW7B46TXmKfvyqSm",
	"FjmZZrFvhnqqb9ThCuMVnENaM3JGVuGWNyCAxRJcFpg9",
	"6No2i3aawzHsjtThw81iq1EXPJN6rh8eSJCLaYZfKDTG",
	"A9cWowVAiHe9pJfKAj3TJiN9VpbzMUq6E4kEvf5mUT22",
	"68Pwb4jS7eZATjDfhmTXgRJjCiZmw1L7Huy4HNpnxJ3o",
	"4ABhJh5rZPjv63RBJBuyWzBK3g9gWMUQdTZP2kiW31V9",
	"B2M4NG5eyZp5SBQrSdtemzk5TqVuaWGQnowGaCBt8GyM",
	"5jA59cXMKQqZAVdtopv8q3yyw9SYfiE3vUCbt7p8MfVf",
	"5YktoWygr1Bp9wiS1xtMtUki1PeYuuzuCF98tqwYxf61",
	"295Avbam4qGShBYK7E9H5Ldew4B3WyJGmgmXfiWdeeyV",
	"EDi4rSy2LZgKJX74mbLTFk4mxoTgT6F7HxxzG2HBAFyK",
	"BnGKHAC386n4Qmv9xtpBVbRaUTKixjBe3oagkPFKtoy6",
	"Dd7K2Fp7AtoN8xCghKDRmyqr5U169t48Tw5fEd3wT9mq",
	"AP6qExwrbRgBAVaehg4b5xHENX815sMabtBzUzVB4v8S",
}

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

	tipAccount := tipAccounts[rand.Intn(len(tipAccounts))]

	// construct instruction
	transferIx := system.NewTransferInstruction(amount, account.PublicKey(), receivePub).Build()
	tipIx := system.NewTransferInstruction(tipAmount, account.PublicKey(), solana.MustPublicKeyFromBase58(tipAccount)).Build()

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

	sendRes, err := client.SendBatch(context.TODO(), &pb.SendBatchRequest{
		Transactions:     []string{txBase64},
		Mode:             mode,
		SafeWindow:       safeWindow,
		RevertProtection: revertProtection,
	})
	if err != nil {
		panic(fmt.Sprintf("[send batch] error: %v", err))
	}

	fmt.Printf("[send batch] response: %+v \n", sendRes)
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
{% endcode %}
{% endtab %}

{% tab title="Proto" %}
{% code overflow="wrap" %}
```go
syntax = "proto3";

package serverpb;

option go_package = "./pb/serverpb";

service Server {
    rpc SendTransaction(SendRequest) returns(SendResponse) {};

    rpc SendBinaryTransaction(SendBinaryRequest) returns(SendResponse) {};

    rpc SendBatch(SendBatchRequest) returns(SendBatchResponse) {};

    rpc SendBundle(SendBundleRequest) returns(SendBundleResponse) {};

    rpc GetHealth(HealthRequest) returns(HealthResponse) {};
}

message SendRequest {
    string transaction = 1;
    string mode = 2;
    int32 safeWindow = 3;
    bool revertProtection = 4;
}

message SendBinaryRequest {
    bytes binaryTransaction = 1;
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
```
{% endcode %}
{% endtab %}
{% endtabs %}

### 返回示例

正常

```json
{"signature":"$first_tx_signature","error":""}
```

異常

{% code overflow="wrap" %}
```json
{"signature":"","error":"error: Invalid authentication credentials. Please ensure your auth token is correct and try again"}
```
{% endcode %}

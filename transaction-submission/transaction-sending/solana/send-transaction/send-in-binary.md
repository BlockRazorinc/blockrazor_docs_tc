---
description: >-
  瞭解 BlockRazor Solana Transaction Sending模式下Send Transaction in Binary
  API、端點、請求示例、請求參數和返回參數
metaLinks:
  canonical: send-in-binary.md
  alternates:
    - >-
      https://app.gitbook.com/s/jbyfG8gOgcdsK3wVxNdQ/transaction-submission/transaction-sending/solana/send-transaction/send-in-binary
---

# Solana Send Transaction in Binary API

### 介紹

{% hint style="warning" %}
Solana發送交易的服務不和訂閱計劃綁定，可前往 [Authentication](../../../../get-started/authentication.md) 獲取API KEY，默认限流为3 TPS。如需提升限流標準，請[聯繫](https://discord.com/invite/qqJuwRb8Nh)我們，我們會在第一時間處理.
{% endhint %}

`Send in Binary` 用於在Solana上發送已簽名的交易。與 [Send Transaction](./) 方式相比，它支持已簽名交易以binary形式提交，而不用先轉成Base64。這樣做的好處是少了一層編碼與解碼開銷，而且由於數據包更小交易在傳輸時不容易分包，可以降低因此導致的高延遲。

### 端點

* `POST /v2/sendBinaryTransaction`

### 請求示例

{% tabs %}
{% tab title="CURL" %}
```bash
curl --location 'http://frankfurt.solana.blockrazor.xyz:443/v2/sendBinaryTransaction?auth=<auth_token>' \
--data-binary @transaction.bin
```
{% endtab %}

{% tab title="Go" %}
```go
package main

import (
	"bytes"
	"context"
	"encoding/json"
	"fmt"
	"io"
	"net/http"
	"net/url"
	"strings"
	"time"

	examples "github.com/BlockRazorinc/solana-trader-client-go/example"
	"github.com/gagliardetto/solana-go"
	"github.com/gagliardetto/solana-go/programs/system"
	"github.com/gagliardetto/solana-go/rpc"
)

const (
	// Example endpoints only. Always use the current endpoints published on the BlockRazor website.
	httpBinaryEndpoint  = "http://frankfurt.solana.blockrazor.xyz:443/v2/sendBinaryTransaction"
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

	txBin, err := tx.MarshalBinary()
	if err != nil {
		return err
	}

	u, err := url.Parse(httpBinaryEndpoint)
	if err != nil {
		return err
	}
	query := u.Query()
	query.Set("auth", authKey)
	query.Set("mode", mode)
	u.RawQuery = query.Encode()

	httpReq, err := http.NewRequest("POST", u.String(), bytes.NewReader(txBin))
	if err != nil {
		return err
	}
	httpReq.Header.Set("Content-Type", "application/octet-stream")
	resp, err := httpClient.Do(httpReq)
	if err != nil {
		return fmt.Errorf("send http error: %v", err)
	}
	defer resp.Body.Close()

	bodyBytes, err := io.ReadAll(resp.Body)
	if err != nil {
		return fmt.Errorf("read response body: %w", err)
	}
	if resp.StatusCode < http.StatusOK || resp.StatusCode >= http.StatusMultipleChoices {
		return fmt.Errorf("send transaction failed: status=%s body=%s", resp.Status, strings.TrimSpace(string(bodyBytes)))
	}
	signature := strings.TrimSpace(string(bodyBytes))
	if signature == "" {
		return fmt.Errorf("send transaction returned an empty signature")
	}
	fmt.Printf("[send tx] signature: %s\n", signature)
	return nil
}
```
{% endtab %}

{% tab title="Rust" %}
```rust
use bincode;
use reqwest::header::{HeaderMap, HeaderValue, CONTENT_TYPE};
use serde::Deserialize;
use solana_client::rpc_client::RpcClient;
use solana_sdk::{
    commitment_config::CommitmentConfig,
    pubkey::Pubkey,
    signature::{Keypair, Signer},
    transaction::Transaction,
};
use solana_system_interface::instruction::transfer;
use solana_trader_client_rust::get_http_tip_account;
use std::str::FromStr;
use std::time::Duration;
use tokio::time::sleep;

#[derive(Deserialize, Debug)]
struct HealthResponse {
    result: String,
}

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    // Configuration values
    // Example endpoints only. Always use the current endpoints published on the BlockRazor website.
    let http_endpoint = "http://frankfurt.solana.blockrazor.xyz:443/v2/sendBinaryTransaction";
    let health_endpoint = "http://frankfurt.solana.blockrazor.xyz:443/health";
    let tip_accounts_endpoint = "http://frankfurt.solana.blockrazor.xyz:443/getTipAccounts";
    let mainnetrpc = "";
    // replace your authKey
    let authkey = "";
    // relace your private key
    let privatekey = "";
    // relace your target public key
    let publickey = "";
    // transaction amount
    let amount: u64 = 200_000;
    // tip amount
    let tipamount: u64 = 100_000;
    // send mode
    let mode = "fast";

    // Create a shared HTTP client with keep-alive support
    let client = reqwest::Client::new();

    // Perform initial health check to warm up the connection
    ping_health(&client, health_endpoint, authkey).await?;

    // Start background health pinger to keep the connection alive
    let health_endpoint_clone = health_endpoint.to_string();
    let authkey_clone = authkey.to_string();
    let client_clone = client.clone();
    tokio::spawn(async move {
        loop {
            if let Err(e) = ping_health(&client_clone, &health_endpoint_clone, &authkey_clone).await
            {
                eprintln!("Health check failed: {:?}", e);
            }
            sleep(Duration::from_secs(30)).await;
        }
    });

    // Send Solana transaction
    send_transaction(
        &client,
        mainnetrpc,
        authkey,
        privatekey,
        publickey,
        tipamount,
        amount,
        mode,
        &http_endpoint,
        tip_accounts_endpoint,
    )
    .await?;

    Ok(())
}

/// Perform POST /health to keep the HTTP connection alive.
async fn ping_health(
    client: &reqwest::Client,
    health_endpoint: &str,
    authkey: &str,
) -> Result<(), Box<dyn std::error::Error>> {
    let mut headers = HeaderMap::new();
    headers.insert("apikey", HeaderValue::from_str(authkey)?);
    headers.insert(CONTENT_TYPE, HeaderValue::from_static("application/json"));

    let res = client
        .post(health_endpoint)
        .headers(headers)
        .json(&serde_json::json!({}))
        .send()
        .await?;

    let body = res.text().await?;
    let parsed: Result<HealthResponse, _> = serde_json::from_str(&body);
    if let Ok(hr) = parsed {
        println!("Health result: {}", hr.result);
    } else {
        return Err("Failed to parse health response".into());
    }
    Ok(())
}

/// Build and send a binary transaction via HTTP POST
async fn send_transaction(
    client: &reqwest::Client,
    mainnetrpc: &str,
    authkey: &str,
    privatekey: &str,
    publickey: &str,
    tipamount: u64,
    amount: u64,
    mode: &str,
    http_endpoint: &str,
    tip_accounts_endpoint: &str,
) -> Result<(), Box<dyn std::error::Error>> {
    let from = Keypair::from_base58_string(privatekey);
    let receiver = Pubkey::from_str(publickey)?;
    let tip = get_http_tip_account(client, tip_accounts_endpoint).await?;

    let rpcurl = String::from(mainnetrpc);
    let connection = RpcClient::new_with_commitment(rpcurl, CommitmentConfig::confirmed());
    let recent_blockhash = connection
        .get_latest_blockhash()
        .expect("Failed to get recent blockhash.");

    let ix_tip = transfer(&from.pubkey(), &tip, tipamount);
    let ix_main = transfer(&from.pubkey(), &receiver, amount);

    let tx = Transaction::new_signed_with_payer(
        &[ix_tip, ix_main],
        Some(&from.pubkey()),
        &[&from],
        recent_blockhash,
    );

    let serialized = bincode::serialize(&tx)?;
    let res = client
        .post(http_endpoint)
        .query(&[("auth", authkey), ("mode", mode)])
        .header(CONTENT_TYPE, "application/octet-stream")
        .body(serialized)
        .send()
        .await?;

    let status = res.status();
    let text = res.text().await?;
    if !status.is_success() {
        return Err(format!(
            "send transaction failed: status={} body={}",
            status,
            text.trim()
        )
        .into());
    }
    let signature = text.trim();
    if signature.is_empty() {
        return Err("send transaction returned an empty signature".into());
    }
    println!("Transaction Signature: {}", signature);

    Ok(())
}
```
{% endtab %}

{% tab title="JS" %}
```javascript
const axios = require('axios');
const web3 = require('@solana/web3.js');
const bs58 = require('bs58');
const { getHttpTipAccount } = require('./get_tip_accounts');

// ------------------ Configuration Constants ------------------
// Example endpoints only. Always use the current endpoints published on the BlockRazor website.
const httpEndpoint = "http://frankfurt.solana.blockrazor.xyz:443/v2/sendBinaryTransaction";
const healthEndpoint = "http://frankfurt.solana.blockrazor.xyz:443/health";
const tipAccountsEndpoint = "http://frankfurt.solana.blockrazor.xyz:443/getTipAccounts";
// Replace with your Solana RPC endpoint
const mainNetRPC = "";
// Replace with your authKey
const authKey = "";
// Replace with your private key (base58)
const privateKey = "";
// Replace with your target public key
const publicKey = "";
// Send mode
const mode = "fast";
// Transaction amount
const amount = 200_000;
// Tip amount
const tipAmount = 100_000;

// ------------------ Axios HTTP Client (Connection Reuse Enabled) ------------------
const httpClient = axios.create({
    timeout: 10000,
    httpAgent: new (require('http').Agent)({ keepAlive: true }),
    httpsAgent: new (require('https').Agent)({ keepAlive: true }),
});

// ------------------ Periodic Health Ping to Keep Connection Alive ------------------
async function pingHealth() {
    try {
        const res = await httpClient.post(healthEndpoint, {}, {
            headers: { 'Content-Type': 'application/json', apikey: authKey },
        });
        console.log(`Health result:`, res.data);
    } catch (err) {
        console.error('Health check failed:', err.message);
    }
}

// ------------------ Build and Send Transaction ------------------
async function sendTx() {
    const senderPrivateKey = new Uint8Array(bs58.decode(privateKey));
    const senderKeypair = web3.Keypair.fromSecretKey(senderPrivateKey);
    const receiver = new web3.PublicKey(publicKey);
    const tipAccount = new web3.PublicKey(await getHttpTipAccount(tipAccountsEndpoint));

    const connection = new web3.Connection(mainNetRPC);
    const { blockhash } = await connection.getLatestBlockhash('finalized');

    const tx = new web3.Transaction()
        .add(web3.SystemProgram.transfer({
            fromPubkey: senderKeypair.publicKey,
            toPubkey: tipAccount,
            lamports: tipAmount,
        }))
        .add(web3.SystemProgram.transfer({
            fromPubkey: senderKeypair.publicKey,
            toPubkey: receiver,
            lamports: amount,
        }));

    tx.recentBlockhash = blockhash;
    tx.feePayer = senderKeypair.publicKey;
    tx.sign(senderKeypair);

    const serialized = tx.serialize();

    try {
        const url = new URL(httpEndpoint);
        url.searchParams.set('auth', authKey);
        url.searchParams.set('mode', mode);
        const res = await httpClient.post(url.toString(), serialized, {
            headers: {
                'Content-Type': 'application/octet-stream',
            },
            responseType: 'text',
        });
        const signature = res.data.trim();
        if (!signature) {
            throw new Error('send transaction returned an empty signature');
        }
        console.log('[send tx] signature:', signature);
    } catch (err) {
        console.error('SendTx failed:', err.response?.data || err.message);
    }
}

// ------------------ Main Entry ------------------
(async () => {
    // Initial health check (establish connection)
    await pingHealth();

    // Periodically send /health to keep connection alive
    setInterval(pingHealth, 30 * 1000);
    sendTx().catch(console.error);
})();
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

{% hint style="info" %}
注意：

* 認證 (auth) 和 請求 (request) 參數必須以 URI 參數的形式填入URL，比如http://frankfurt.solana.blockrazor.xyz:443/v2/sendBinaryTransaction?auth=\<auth\_token>\&mode=fast\&revertProtection=true
{% endhint %}

### 請求參數

<table><thead><tr><th width="121.40234375">字段</th><th width="77.1875">必填</th><th width="107.32421875">示例</th><th>備注</th></tr></thead><tbody><tr><td>binaryTransaction</td><td>是</td><td>transaction.bin</td><td>已簽名的交易，binary格式</td></tr><tr><td>mode</td><td>否</td><td>"fast"<br>"sandwichMitigation"</td><td>BlockRazor支持fast和sandwichMitigation兩種模式，默認為fast模式。<br><br>在fast模式中，交易會基於全球分布式高性能網絡和高質量SWQoS質押鏈路被飽和式發送，以最低延遲到達Leader節點。<br><br>在sandwichMitigation模式中，交易會被發往BlockRazor高度信任的SWQoS質押鏈路，同時交易會跳過黑名單Leader(經BlockRazor三明治監測機制動態精確識別)的slot。在此模式下，<strong>請不要用</strong>durable nonce發送交易，這會使三明治保護失效。</td></tr><tr><td>safeWindow</td><td>否</td><td>3</td><td>sandwichMitigation模式中用於確定交易發送時機的參數，數字代表從當前slot起連續白名單驗證者的slot數量，比如設定3，則交易會僅在當前起連續3個slot都屬於白名單驗證者時發送。<br><br>safeWindow的參數範圍是3-13，數字越大防治三明治攻擊效果越好，但可能會對上鏈速度有一定影響。如不設定，則默認為3。</td></tr><tr><td>revertProtection</td><td>否</td><td>false</td><td>默認為false。如設置為true，交易不會在鏈上執行失敗，但上鏈速度会受到影响且存在无法上链的可能，請根據實際需求謹慎選擇開啓。</td></tr></tbody></table>

### 返回參數

<table><thead><tr><th width="100.3515625">狀態碼</th><th width="192.515625">消息</th><th>釋義</th></tr></thead><tbody><tr><td>200</td><td>OK</td><td>請求正常</td></tr><tr><td>400</td><td>BadRequest</td><td>請求參數不合法</td></tr><tr><td>403</td><td>Forbidden</td><td>請求被拒絕，auth為空、無效或過期</td></tr><tr><td>500</td><td>InternalServerError</td><td>內部服務錯誤</td></tr></tbody></table>


---
description: 介紹BlockRazor Solana Transaction Sending 模式下Send Batch in Binary的集成方法
---

# Solana Batch Sending in Binary

{% hint style="warning" %}
Solana發送batch的服務不和訂閱計劃綁定，可前往 [Authentication](../../../../get-started/authentication.md) 獲取API KEY，默认限流为3 BPS。如需提升限流標準，請[聯繫](https://discord.com/invite/qqJuwRb8Nh)我們，我們會在第一時間處理
{% endhint %}

`Send Batch(binary)` 是 BlockRazor 為 Solana 提供的batch發送接口，用於將已簽名交易以batch形式低延迟發送到鏈上。单个batch中最多可发送 25 笔交易。

相比`Send Batch` ，`Send Batch(binary)`支持以二進制格式發送batch交易。一筆交易是一个原始二进制流

{% code overflow="wrap" %}
```
[ 2 bytes: tx length (big-endian u16) ][ N bytes: bincode-serialized transaction ]
```
{% endcode %}

batch交易首尾相連，沒有分隔符

{% code overflow="wrap" %}
```
+--------+----------+--------+----------+-----+
| len_0  |   tx_0   | len_1  |   tx_1   | ... |
| u16 BE | bincode  | u16 BE | bincode  |     |
+--------+----------+--------+----------+-----+
```
{% endcode %}

### 端點

* `POST v2/sendBinaryBatch`

### 請求參數

<table><thead><tr><th width="121.40234375">字段</th><th width="77.1875">必填</th><th width="125.62890625">示例</th><th>備注</th></tr></thead><tbody><tr><td>transactions</td><td>是</td><td>binary</td><td>二進制格式的batch交易</td></tr><tr><td>mode</td><td>否</td><td>"fast"<br>"sandwichMitigation"</td><td>BlockRazor支持fast和sandwichMitigation兩種模式，默認為fast模式。<br><br>在fast模式中，交易會基於全球分布式高性能網絡和高質量SWQoS質押鏈路被飽和式發送，以最低延遲到達Leader節點。<br><br>在sandwichMitigation模式中，交易會被發往BlockRazor高度信任的SWQoS質押鏈路，同時交易會跳過黑名單Leader(經BlockRazor三明治監測機制動態精確識別)的slot。在此模式下，<strong>請不要用</strong>durable nonce發送交易，這會使三明治保護失效。</td></tr><tr><td>safeWindow</td><td>否</td><td>3</td><td>sandwichMitigation模式中用於確定交易發送時機的參數，數字代表從當前slot起連續白名單驗證者的slot數量，比如設定3，則交易會僅在當前起連續3個slot都屬於白名單驗證者時發送。<br><br>safeWindow的參數範圍是3-13，數字越大防治三明治攻擊效果越好，但可能會對上鏈速度有一定影響。如不設定，則默認為3。</td></tr><tr><td>revertProtection</td><td>否</td><td>false</td><td>默認為false。如設置為true，交易不會在鏈上執行失敗，但上鏈速度会受到影响且存在无法上链的可能，請根據實際需求謹慎選擇開啓。</td></tr></tbody></table>

### 請求示例

{% hint style="info" %}
* 認證 (auth) 和 請求 (request) 參數必須以 URI 參數的形式填入URL，比如`http://frankfurt.solana.blockrazor.xyz:443/v2/sendBinaryBatch?auth=<auth_token>&mode=fast&revertProtection=true`
* 請求中唯一允許的header是 `Content-Type: application/octet-stream`
{% endhint %}

{% tabs %}
{% tab title="Go" %}
{% code overflow="wrap" %}
```go
package main

import (
	"bytes"
	"context"
	"encoding/binary"
	"encoding/json"
	"fmt"
	"io"
	"math/rand"
	"net/http"
	"net/url"
	"strconv"
	"time"

	"github.com/gagliardetto/solana-go"
	"github.com/gagliardetto/solana-go/programs/system"
	"github.com/gagliardetto/solana-go/rpc"
)

const (
	httpBinaryEndpoint = "http://frankfurt.solana.blockrazor.xyz:443/v2/sendBinaryBatch"
	healthEndpoint     = "http://frankfurt.solana.blockrazor.xyz:443/health"
	mainNetRPC         = ""
	authKey            = ""
	privateKey         = ""
	publicKey          = ""
	amount             = 200_000
	tipAmount          = 1_000_000
	mode               = "fast"
	safeWindow         = 5
	revertProtection   = false
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
	// send binary batch
	if err := sendBinaryBatch(); err != nil {
		fmt.Printf("send binary batch failed: %v\n", err)
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

func sendBinaryBatch() error {
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

	txBin, err := tx.MarshalBinary()
	if err != nil {
		return err
	}

	txBinBody, err := buildBinaryBody([][]byte{txBin})
	if err != nil {
		return err
	}

	u, err := url.Parse(httpBinaryEndpoint)
	if err != nil {
		return err
	}
	q := u.Query()
	q.Set("mode", mode)
	q.Set("safeWindow", strconv.Itoa(safeWindow))
	q.Set("revertProtection", strconv.FormatBool(revertProtection))
	q.Set("auth", authKey)
	u.RawQuery = q.Encode()

	httpReq, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(txBinBody))
	if err != nil {
		return err
	}
	httpReq.Header.Set("Content-Type", "application/octet-stream")
	resp, err := httpClient.Do(httpReq)
	if err != nil {
		return fmt.Errorf("send http error: %v", err)
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

func buildBinaryBody(binaryTransactions [][]byte) ([]byte, error) {
	var body bytes.Buffer

	for i, txBytes := range binaryTransactions {
		if len(txBytes) > 65535 {
			return nil, fmt.Errorf("tx[%d] too large: %d bytes", i, len(txBytes))
		}

		txLen := uint16(len(txBytes))

		if err := binary.Write(&body, binary.BigEndian, txLen); err != nil {
			return nil, fmt.Errorf("write tx[%d] length failed: %w", i, err)
		}

		if _, err := body.Write(txBytes); err != nil {
			return nil, fmt.Errorf("write tx[%d] bytes failed: %w", i, err)
		}
	}

	return body.Bytes(), nil
}
```
{% endcode %}
{% endtab %}

{% tab title="Rust" %}
{% code overflow="wrap" %}
```rust
use base64::{engine::general_purpose, Engine as _};
use bincode;
use rand::Rng;
use reqwest::header::{HeaderMap, HeaderValue, CONTENT_TYPE};
use serde::{Deserialize, Serialize};
use solana_client::rpc_client::RpcClient;
use solana_sdk::{
    commitment_config::CommitmentConfig,
    pubkey::Pubkey,
    signature::{Keypair, Signer},
    transaction::Transaction,
};
use solana_system_interface::instruction::transfer;
use std::str::FromStr;
use std::time::Duration;
use tokio::time::sleep;


#[derive(Deserialize, Debug)]
struct SendBatchResponse {
    result: Vec<BatchResponseItem>,
    error: String,
}

#[derive(Deserialize, Debug)]
struct BatchResponseItem {
    signature: String,
    error: String,
}

#[derive(Deserialize, Debug)]
struct HealthResponse {
    result: String,
}

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    // Configuration values
    let http_endpoint = "http://frankfurt.solana.blockrazor.xyz:443/v2/sendBinaryBatch";
    let health_endpoint = "http://frankfurt.solana.blockrazor.xyz:443/health";
    let mainnetrpc = "";
    // replace your authKey
    let authkey = "";
    // relace your private key
    let privatekey ="";
    // relace your target public key
    let publickey = "";
    // transaction amount
    let amount: u64 = 200_000;
    // tip amount
    let tipamount: u64 = 1_000_000;
    // safe window
    let safe_window: u32 = 5;
    // revert protection
    let revert_protection = false;
    // send mode
    let mode = "fast";

    let tip_accounts = [
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
    ];
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

    // Send Solana Batch
    send_batch(
        &client,
        mainnetrpc,
        authkey,
        privatekey,
        publickey,
        &tip_accounts,
        tipamount,
        amount,
        mode,
        safe_window,
        revert_protection,
        http_endpoint,
    )
    .await?;

    Ok(())
}

/// Perform GET /health to keep HTTP connection alive
async fn ping_health(
    client: &reqwest::Client,
    health_endpoint: &str,
    authkey: &str,
) -> Result<(), Box<dyn std::error::Error>> {
    let mut headers = HeaderMap::new();
    headers.insert("apikey", HeaderValue::from_str(authkey)?);
    headers.insert(CONTENT_TYPE, HeaderValue::from_static("application/json"));

    let res = client.get(health_endpoint).headers(headers).send().await?;

    let body = res.text().await?;
    let parsed: Result<HealthResponse, _> = serde_json::from_str(&body);
    if let Ok(hr) = parsed {
        println!("Health result: {}", hr.result);
    } else {
        return Err("Failed to parse health response".into());
    }
    Ok(())
}

fn build_binary_body(transactions: &[Transaction]) -> Result<Vec<u8>, Box<dyn std::error::Error>> {
    let mut body = Vec::new();

    for tx in transactions {
        let tx_bytes = bincode::serialize(tx)?;

        if tx_bytes.len() > u16::MAX as usize {
            return Err(format!("transaction too large: {} bytes", tx_bytes.len()).into());
        }

        let len = tx_bytes.len() as u16;
        body.extend_from_slice(&len.to_be_bytes());
        body.extend_from_slice(&tx_bytes);
    }

    Ok(body)
}

/// Build and send base64-encoded transactions via HTTP POST
async fn send_batch(
    client: &reqwest::Client,
    mainnetrpc: &str,
    authkey: &str,
    privatekey: &str,
    publickey: &str,
    tip_accounts: &[&str],
    tipamount: u64,
    amount: u64,
    mode: &str,
    safe_window: u32,
    revert_protection: bool,
    http_endpoint: &str,
) -> Result<(), Box<dyn std::error::Error>> {
    let from = Keypair::from_base58_string(privatekey);
    let receiver = Pubkey::from_str(publickey)?;
    let tip = Pubkey::from_str(tip_accounts[rand::thread_rng().gen_range(0..tip_accounts.len())])?;

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

    let transactions = vec![tx];
    let binary_body = build_binary_body(&transactions)?;

    let request_url = format!(
        "{}?auth={}&mode={}&safeWindow={}&revertProtection={}",
        http_endpoint,
        authkey,
        mode,
        safe_window,
        revert_protection
    );

    let mut headers = HeaderMap::new();
    headers.insert(CONTENT_TYPE, HeaderValue::from_static("application/octet-stream"));

    let res = client
        .post(&request_url)
        .headers(headers)
        .body(binary_body)
        .send()
        .await?;

    let text = res.text().await?;
    let parsed: Result<SendBatchResponse, _> = serde_json::from_str(&text);
    match parsed {
        Ok(r) => {
            println!("Result: {:?}", r.result); // use field
        }
        Err(_) => println!("RAW RESPONSE: {}", text),
    }

    Ok(())
}
```
{% endcode %}
{% endtab %}

{% tab title="JS" %}
{% code overflow="wrap" %}
```javascript
const axios = require('axios');
const web3 = require('@solana/web3.js');
const bs58 = require('bs58');

// ------------------ Configuration Constants ------------------
// BlockRazor relay endpoint address
const httpEndpoint = "http://frankfurt.solana.blockrazor.xyz:443/v2/sendBinaryBatch";
const healthEndpoint = "http://frankfurt.solana.blockrazor.xyz:443/health";
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
// Safe window
const safeWindow = 5;
// Revert protection
const revertProtection = false;
// Transaction amount
const amount = 200_000;
// Tip amount
const tipAmount = 1000000;

const tipAccounts = [
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
];

// ------------------ Axios HTTP Client (Connection Reuse Enabled) ------------------
const httpClientHealth = axios.create({
		timeout: 10000,
		headers: {
				'Content-Type': 'application/octet-stream',
				'apikey': authKey,
		},
		httpAgent: new (require('http').Agent)({ keepAlive: true }),
		httpsAgent: new (require('https').Agent)({ keepAlive: true }),
});

const httpClient = axios.create({
		timeout: 10000,
		headers: {
				'Content-Type': 'application/octet-stream',
		},
		httpAgent: new (require('http').Agent)({ keepAlive: true }),
		httpsAgent: new (require('https').Agent)({ keepAlive: true }),
});

// ------------------ Periodic Health Ping to Keep Connection Alive ------------------
async function pingHealth() {
		try {
				const res = await httpClientHealth.get(healthEndpoint);
				console.log(`Health result:`, res.data);
		} catch (err) {
				console.error('Health check failed:', err.message);
		}
}

function buildBinaryBatchBody(serializedTxs) {
  const parts = [];

  for (let i = 0; i < serializedTxs.length; i++) {
    const txBytes = Buffer.from(serializedTxs[i]);

    if (txBytes.length > 65535) {
      throw new Error(`tx[${i}] too large: ${txBytes.length} bytes`);
    }

    const lenBuf = Buffer.alloc(2);
    lenBuf.writeUInt16BE(txBytes.length, 0);

    parts.push(lenBuf);
    parts.push(txBytes);
  }

  return Buffer.concat(parts);
}

// ------------------ Build and Send Binary Batch ------------------
async function sendBinaryBatch() {
		const senderPrivateKey = new Uint8Array(bs58.decode(privateKey));
		const senderKeypair = web3.Keypair.fromSecretKey(senderPrivateKey);
		const receiver = new web3.PublicKey(publicKey);
		const tipAccount = new web3.PublicKey(tipAccounts[Math.floor(Math.random() * tipAccounts.length)]);

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

		const serializedTxs = [];
		serializedTxs.push(serialized);
		const binaryBody = buildBinaryBatchBody(serializedTxs);

		const url = new URL(httpEndpoint);
		url.searchParams.set("auth", authKey);
		url.searchParams.set("mode", mode);
		url.searchParams.set("safeWindow", safeWindow);
		url.searchParams.set("revertProtection", revertProtection);

		const fullEndpoint = url.toString();
		
		try {
				const res = await httpClient.post(fullEndpoint, binaryBody);
				console.log('[send binary batch] response:', res.data);
		} catch (err) {
				console.error('sendBinaryBatch failed:', err.response?.data || err.message);
		}
}

// ------------------ Main Entry ------------------
(async () => {
		// Initial health check (establish connection)
		await pingHealth();

		// Periodically send /health to keep connection alive
		setInterval(pingHealth, 30 * 1000);
		sendBinaryBatch().catch(console.error);
})();
```
{% endcode %}
{% endtab %}
{% endtabs %}

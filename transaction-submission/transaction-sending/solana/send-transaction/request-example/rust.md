---
description: 瞭解 BlockRazor Solana Transaction Sending 模式下 Send Transaction 的 Rust 請求示例
metaLinks:
  canonical: rust.md
  alternates:
    - >-
      https://app.gitbook.com/s/jbyfG8gOgcdsK3wVxNdQ/transaction-submission/transaction-sending/solana/send-transaction/request-example/rust
---

# Solana Send Transaction Rust Example

{% hint style="info" %}
Solana發送交易的服務不和訂閱計劃綁定，可前往 [Authentication](../../../../../get-started/authentication.md) 獲取API KEY，默认限流为3 TPS。如需提升限流標準，請[聯繫](https://discord.com/invite/qqJuwRb8Nh)我們，我們會在第一時間處理
{% endhint %}

[代碼示例](https://github.com/BlockRazorinc/solana-trader-client-rust/tree/main/src)

### 請求示例

{% tabs %}
{% tab title="HTTP" %}
```rust
use base64::{engine::general_purpose, Engine as _};
use bincode;
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
use solana_trader_client_rust::get_http_tip_account;
use std::str::FromStr;
use std::time::Duration;
use tokio::time::sleep;

#[derive(Serialize)]
struct SendRequest {
    transaction: String,
    mode: String,
}

#[derive(Deserialize, Debug)]
struct SendResponse {
    signature: String,
}

#[derive(Deserialize, Debug)]
struct HealthResponse {
    result: String,
}

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    // Configuration values
    // Example endpoints only. Always use the current endpoints published on the BlockRazor website.
    let http_endpoint = "http://frankfurt.solana.blockrazor.xyz:443/sendTransaction";
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

/// Build and send a base64-encoded transaction via HTTP POST
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
    let base64_encoded = general_purpose::STANDARD.encode(serialized);
    let request = SendRequest {
        transaction: base64_encoded,
        mode: mode.to_string(),
    };
    let mut headers = HeaderMap::new();
    headers.insert("apikey", HeaderValue::from_str(authkey)?);
    headers.insert(CONTENT_TYPE, HeaderValue::from_static("application/json"));

    let res = client
        .post(http_endpoint)
        .headers(headers)
        .json(&request)
        .send()
        .await?;

    let text = res.text().await?;
    let parsed: Result<SendResponse, _> = serde_json::from_str(&text);
    match parsed {
        Ok(r) => {
            println!("Transaction Signature: {}", r.signature); // use field
        }
        Err(_) => println!("RAW RESPONSE: {}", text),
    }

    Ok(())
}
```
{% endtab %}

{% tab title="gRPC" %}
```rust
use base64::{engine::general_purpose, Engine as _};
use bincode;
use server::server_client::ServerClient;
use server::{GetTipAccountsRequest, HealthRequest, SendRequest};
use solana_client::rpc_client::RpcClient;
use solana_sdk::commitment_config::CommitmentConfig;
use solana_sdk::signature::{Keypair, Signer};
use solana_sdk::transaction::Transaction;
use solana_system_interface::instruction::transfer;
use solana_trader_client_rust::random_tip_account;
use std::error::Error;
use std::time::Duration;
use tokio::time::interval;
use tonic::metadata::AsciiMetadataValue;
use tonic::transport::Channel;

pub mod server {
    tonic::include_proto!("serverpb");
}

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    // This example endpoint may change. Always use the current endpoint published on the BlockRazor website.
    let blzendpoint = "http://frankfurt.solana-grpc.blockrazor.xyz:80";
    // replace your solana rpc endpoint
    let mainnetrpc = "";
    // replace your authKey
    let authkey = "";
    // relace your private key
    let privatekey = "";
    // send mode
    let mode = "fast";
    // safe window
    let safe_window = 0;
    // revert protection
    let revert_protection = false;
    // tip amount
    let tipamount = 100_000;

    let channel = Channel::from_shared(blzendpoint.to_string())
        .map_err(|e| Box::<dyn Error>::from(format!("Invalid URI: {}", e)))?
        .connect()
        .await
        .map_err(|e| Box::<dyn Error>::from(format!("Connection error: {}", e)))?;

    let apikeyvalue = AsciiMetadataValue::try_from(authkey)
        .map_err(|e| Box::<dyn Error>::from(format!("Invalid API key format: {}", e)))?;

    let mut client = ServerClient::new(channel.clone());
    let tip_accounts = client
        .get_tip_accounts(tonic::Request::new(GetTipAccountsRequest {}))
        .await?
        .into_inner()
        .tip_accounts;
    let tip_account = random_tip_account(&tip_accounts)?;
    let mut request = tonic::Request::new(HealthRequest {});
    request.metadata_mut().insert("apikey", apikeyvalue.clone());
    let response = client.get_health(request).await?;
    println!("health check response: {:?}", response.into_inner().status);

    // Start a background goroutine to periodically send /health requests
    // For low-frequency users, this keeps the grpc connection alive (warm)
    let mut health_client = ServerClient::new(channel);
    let health_apikey = apikeyvalue.clone();
    tokio::spawn(async move {
        let mut interval = interval(Duration::from_secs(30));
        loop {
            interval.tick().await;
            let mut request = tonic::Request::new(HealthRequest {});
            request
                .metadata_mut()
                .insert("apikey", health_apikey.clone());
            match health_client.get_health(request).await {
                Ok(response) => {
                    println!(
                        "[Health Check] Response: {:?}",
                        response.into_inner().status
                    );
                }
                Err(e) => {
                    eprintln!("[Health Check] Failed: {}", e);
                }
            }
        }
    });

    let from = Keypair::from_base58_string(privatekey);
    let frompubkey = Signer::pubkey(&from);
    let topubkey = tip_account;

    let rpcurl = String::from(mainnetrpc);
    let connection = RpcClient::new_with_commitment(rpcurl, CommitmentConfig::confirmed());

    let ix = transfer(&frompubkey, &topubkey, tipamount);
    let recent_blockhash = connection
        .get_latest_blockhash()
        .expect("Failed to get recent blockhash.");
    let tx =
        Transaction::new_signed_with_payer(&[ix], Some(&frompubkey), &[&from], recent_blockhash);

    // tx base64
    let serialized = bincode::serialize(&tx)?;
    let base64_encoded = general_purpose::STANDARD.encode(serialized);
    let mut tx_request: tonic::Request<SendRequest> = tonic::Request::new(SendRequest {
        transaction: base64_encoded,
        mode: mode.to_string(),
        safe_window: safe_window,
        revert_protection: revert_protection,
    });
    tx_request
        .metadata_mut()
        .insert("apikey", apikeyvalue.clone());
    let response = client.send_transaction(tx_request).await?;
    println!("SEND TX RESPONSE={:?}", response);

    Ok(())
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

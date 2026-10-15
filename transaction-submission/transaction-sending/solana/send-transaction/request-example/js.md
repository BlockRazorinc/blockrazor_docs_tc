---
description: 瞭解 BlockRazor Solana Fast 模式下 Send Transaction 的 JS 請求示例
metaLinks:
  canonical: js.md
  alternates:
    - >-
      https://app.gitbook.com/s/jbyfG8gOgcdsK3wVxNdQ/transaction-submission/transaction-sending/solana/send-transaction/request-example/js
---

# Solana Send Transaction JS Example

{% hint style="info" %}
Solana發送交易的服務不和訂閱計劃綁定，可前往 [Authentication](../../../../../get-started/authentication.md) 獲取API KEY，默认限流为3 TPS。如需提升限流標準，請[聯繫](https://discord.com/invite/qqJuwRb8Nh)我們，我們會在第一時間處理
{% endhint %}

[代碼示例](https://github.com/BlockRazorinc/solana-trader-client-js)

### 請求示例

{% tabs %}
{% tab title="HTTP" %}
```javascript
const axios = require('axios');
const web3 = require('@solana/web3.js');
const bs58 = require('bs58');
const { getHttpTipAccount } = require('./get_tip_accounts');

// ------------------ Configuration Constants ------------------
// Example endpoints only. Always use the current endpoints published on the BlockRazor website.
const httpEndpoint = "http://frankfurt.solana.blockrazor.xyz:443/sendTransaction";
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
    headers: {
        'Content-Type': 'application/json',
        'apikey': authKey,
    },
    httpAgent: new (require('http').Agent)({ keepAlive: true }),
    httpsAgent: new (require('https').Agent)({ keepAlive: true }),
});

// ------------------ Periodic Health Ping to Keep Connection Alive ------------------
async function pingHealth() {
    try {
        const res = await httpClient.post(healthEndpoint, {});
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
    const base64Tx = serialized.toString('base64');

    const payload = {
        transaction: base64Tx,
        mode: mode,
    };

    try {
        const res = await httpClient.post(httpEndpoint, payload);
        console.log('[send tx] response:', res.data);
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

{% tab title="gRPC" %}
```go
const web3 = require("@solana/web3.js");
const bs58 = require("bs58");
const { getGrpcTipAccount } = require('./get_tip_accounts');

const grpc = require('@grpc/grpc-js');
const protoLoader = require('@grpc/proto-loader');

const PROTO_PATH = __dirname + '/server.proto';
const packageDefinition = protoLoader.loadSync(
    PROTO_PATH,
    {
        keepCase: true,
        longs: String,
        enums: String,
        defaults: true,
        oneofs: true
    }
);

const serverProto = grpc.loadPackageDefinition(packageDefinition).serverpb;

// This example endpoint may change. Always use the current endpoint published on the BlockRazor website.
const blzRelayEndpoint = "frankfurt.solana-grpc.blockrazor.xyz:80";
// replace your solana rpc endpoint
const mainNetRPC = "";
// replace your authKey
const authKey = "";
// relace your private key(base58)
const privateKey = "";
// send mode
const mode = "fast";

// tip amount
const tipAmount = 100_000;

const client = new serverProto.Server(
    blzRelayEndpoint,
    grpc.credentials.createInsecure()
);

var meta = new grpc.Metadata();

// ------------------ Periodic Health Ping to Keep Connection Alive ------------------
async function pingHealth() {
    client.getHealth({}, meta, (err, response) => {
        if (err) {
            console.error('[get health] error:', err);
            return;
        }

        console.log('[get health] response:', response);
    });
}

(async () => {
    meta.add('apikey', authKey);

    // Initial health check (establish connection)
    await pingHealth();

    // Periodically send /health to keep connection alive
    setInterval(pingHealth, 30 * 1000);

    const senderPrivateKey = new Uint8Array(bs58.decode(privateKey));
    const senderKeypair = web3.Keypair.fromSecretKey(senderPrivateKey);

    const tipAccount = await getGrpcTipAccount(client);
    const recipientPublicKey = new web3.PublicKey(tipAccount);

    const transaction = new web3.Transaction().add(
        web3.SystemProgram.transfer({
            fromPubkey: senderKeypair.publicKey,
            toPubkey: recipientPublicKey,
            lamports: tipAmount,
        })
    );

    const connection = new web3.Connection(mainNetRPC);

    transaction.recentBlockhash = (await connection.getLatestBlockhash()).blockhash;
    transaction.feePayer = senderKeypair.publicKey;
    transaction.sign(senderKeypair);

    const serializedTransaction = transaction.serialize();
    const base64Tx = serializedTransaction.toString('base64');

    client.SendTransaction({ transaction: base64Tx, mode: mode }, meta, (err, response) => {
        if (err) {
            console.error('[send tx] error:', err);
            return;
        }

        console.log('[send tx] response:', response);
    });
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


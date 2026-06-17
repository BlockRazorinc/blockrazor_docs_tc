---
description: 介紹BlockRazor Solana Fast模式的Send Transaction v2接口以及集成方法
---

# Send Transaction v2(binary)

### 介紹

{% hint style="warning" %}
Solana發送交易的服務不和訂閱計劃綁定，可前往 [Authentication](../../../get-started/authentication.md) 獲取API KEY，默认限流为3 TPS。如需提升限流標準，請[聯繫](https://discord.com/invite/qqJuwRb8Nh)我們，我們會在第一時間處理.
{% endhint %}

Send Transaction v2(binary) 用於在Solana上發送已簽名的交易，支持HTTP協議。與 [Send Transaction](send-transaction/) 方式相比，它支持已簽名交易以binary形式提交，而不用先轉成Base64。這樣做的好處是少了一層編碼與解碼開銷，而且由於數據包更小交易在傳輸時不容易分包，可以降低因此導致的高延遲。

### 端點

{% tabs %}
{% tab title="HTTP" %}
<table><thead><tr><th width="97.328125">地區</th><th>端點</th></tr></thead><tbody><tr><td>法蘭克福</td><td>http://frankfurt.solana.blockrazor.xyz:443/v2/sendBinaryTransaction</td></tr><tr><td>紐約</td><td>http://newyork.solana.blockrazor.xyz:443/v2/sendBinaryTransaction</td></tr><tr><td>東京</td><td>http://tokyo.solana.blockrazor.xyz:443/v2/sendBinaryTransaction</td></tr><tr><td>阿姆斯特丹</td><td>http://amsterdam.solana.blockrazor.xyz:443/v2/sendBinaryTransaction</td></tr><tr><td>倫敦</td><td>http://london.solana.blockrazor.xyz:443/v2/sendBinaryTransaction</td></tr></tbody></table>
{% endtab %}

{% tab title="HTTPS" %}
<table><thead><tr><th width="136.9765625">地區</th><th>端點</th></tr></thead><tbody><tr><td>法蘭克福</td><td>https://frankfurt.solana.blockrazor.io/v2/sendBinaryTransaction</td></tr><tr><td>紐約</td><td>https://newyork.solana.blockrazor.io/v2/sendBinaryTransaction</td></tr><tr><td>東京</td><td>https://tokyo.solana.blockrazor.io/v2/sendBinaryTransaction</td></tr></tbody></table>
{% endtab %}
{% endtabs %}

### 流控說明

{% hint style="info" %}
Solana發送交易的服務不和訂閱計劃綁定，可前往 [Authentication](../../../get-started/authentication.md) 獲取API KEY，默认限流为3 TPS。如需提升限流標準，請[聯繫](https://discord.com/invite/qqJuwRb8Nh)我們，我們會在第一時間處理
{% endhint %}

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
	httpEndpoint = "http://frankfurt.solana.blockrazor.xyz:443/v2/sendBinaryTransaction?auth=<auth_token>"
	mainNetRPC   = ""
	privateKey   = ""
	publicKey    = ""
	amount       = 200_000
	tipAmount    = 1_000_000
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

func main() {
	if err := sendTx(); err != nil {
		fmt.Printf("send tx failed: %v\n", err)
	}
}

func sendTx() error {
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

	binTx, err := tx.MarshalBinary()
	if err != nil {
		return fmt.Errorf("marshal tx error: %v", err)
	}

	req, err := http.NewRequest("POST", httpEndpoint, bytes.NewReader(binTx))
	if err != nil {
		return err
	}
	req.Header.Set("Content-Type", "application/octet-stream")

	resp, err := httpClient.Do(req)
	if err != nil {
		return fmt.Errorf("send http error: %v", err)
	}
	defer resp.Body.Close()

	bodyBytes, _ := io.ReadAll(resp.Body)
	fmt.Printf("[send tx] response: %s\n", string(bodyBytes))
	return nil
}
```
{% endtab %}

{% tab title="Rust" %}
```rust
use bincode;
use rand::Rng;
use reqwest::header::{HeaderMap, HeaderValue, CONTENT_TYPE};
use solana_client::rpc_client::RpcClient;
use solana_sdk::{
    commitment_config::CommitmentConfig,
    pubkey::Pubkey,
    signature::{Keypair, Signer},
    transaction::Transaction,
};
use solana_system_interface::instruction::transfer;
use std::str::FromStr;

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let http_endpoint = "http://frankfurt.solana.blockrazor.xyz:443/v2/sendBinaryTransaction?auth=<auth_token>";
    let mainnetrpc = "";
    let privatekey = "";
    let publickey = "";
    let amount: u64 = 200_000;
    let tipamount: u64 = 1_000_000;

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

    let client = reqwest::Client::new();

    send_transaction(
        &client,
        mainnetrpc,
        privatekey,
        publickey,
        &tip_accounts,
        tipamount,
        amount,
        http_endpoint,
    )
    .await?;

    Ok(())
}

async fn send_transaction(
    client: &reqwest::Client,
    mainnetrpc: &str,
    privatekey: &str,
    publickey: &str,
    tip_accounts: &[&str],
    tipamount: u64,
    amount: u64,
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

    let serialized = bincode::serialize(&tx)?;

    let mut headers = HeaderMap::new();
    headers.insert(CONTENT_TYPE, HeaderValue::from_static("application/octet-stream"));

    let res = client
        .post(http_endpoint)
        .headers(headers)
        .body(serialized)
        .send()
        .await?;

    let text = res.text().await?;
    println!("[send tx] response: {}", text);

    Ok(())
}
```
{% endtab %}

{% tab title="JS" %}
```javascript
const axios = require("axios");
const web3 = require("@solana/web3.js");
const bs58 = require("bs58");
const http = require("http");
const https = require("https");

const httpEndpoint = "http://frankfurt.solana.blockrazor.xyz:443/v2/sendBinaryTransaction?auth=<auth_token>";
const mainNetRPC = "";
const privateKey = "";
const publicKey = "";
const amount = 200_000;
const tipAmount = 1_000_000;

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

const httpClient = axios.create({
  timeout: 10000,
  httpAgent: new http.Agent({ keepAlive: true }),
  httpsAgent: new https.Agent({ keepAlive: true }),
});

async function sendTx() {
  const senderPrivateKey = new Uint8Array(bs58.decode(privateKey));
  const senderKeypair = web3.Keypair.fromSecretKey(senderPrivateKey);
  const receiver = new web3.PublicKey(publicKey);
  const tipAccount = new web3.PublicKey(
    tipAccounts[Math.floor(Math.random() * tipAccounts.length)]
  );

  const connection = new web3.Connection(mainNetRPC);
  const { blockhash } = await connection.getLatestBlockhash("finalized");

  const tx = new web3.Transaction()
    .add(
      web3.SystemProgram.transfer({
        fromPubkey: senderKeypair.publicKey,
        toPubkey: tipAccount,
        lamports: tipAmount,
      })
    )
    .add(
      web3.SystemProgram.transfer({
        fromPubkey: senderKeypair.publicKey,
        toPubkey: receiver,
        lamports: amount,
      })
    );

  tx.recentBlockhash = blockhash;
  tx.feePayer = senderKeypair.publicKey;
  tx.sign(senderKeypair);

  const serialized = tx.serialize();

  try {
    const res = await httpClient.post(httpEndpoint, Buffer.from(serialized), {
      headers: {
        "Content-Type": "application/octet-stream",
      },
      responseType: "text",
    });

    console.log("[send tx] response:", res.data);
  } catch (err) {
    console.error("SendTx failed:", err.response?.data || err.message);
  }
}

sendTx().catch(console.error);
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
注意：

* 認證 (auth) 和 請求 (request) 參數必須以 URI 參數的形式填入URL，比如http://frankfurt.solana.blockrazor.xyz:443/v2/sendTransaction?auth=\<auth\_token>\&mode=fast\&revertProtection=true
{% endhint %}

### 請求參數

<table><thead><tr><th width="121.40234375">字段</th><th width="77.1875">必填</th><th width="125.62890625">示例</th><th>備注</th></tr></thead><tbody><tr><td>binaryTransaction</td><td>是</td><td>transaction.bin</td><td>已簽名的交易，binary格式</td></tr><tr><td>mode</td><td>否</td><td>"fast"<br>"sandwichMitigation"</td><td>BlockRazor支持fast和sandwichMitigation兩種模式，默認為fast模式。<br><br>在fast模式中，交易會基於全球分布式高性能網絡和高質量SWQoS質押鏈路被飽和式發送，以最低延遲到達Leader節點。<br><br>在sandwichMitigation模式中，交易會被發往BlockRazor高度信任的SWQoS質押鏈路，同時交易會跳過黑名單Leader(經BlockRazor三明治監測機制動態精確識別)的slot。在此模式下，<strong>請不要用</strong>durable nonce發送交易，這會使三明治保護失效。</td></tr><tr><td>safeWindow</td><td>否</td><td>3</td><td>sandwichMitigation模式中用於確定交易發送時機的參數，數字代表從當前slot起連續白名單驗證者的slot數量，比如設定3，則交易會僅在當前起連續3個slot都屬於白名單驗證者時發送。<br><br>safeWindow的參數範圍是3-13，數字越大防治三明治攻擊效果越好，但可能會對上鏈速度有一定影響。如不設定，則默認為3。</td></tr><tr><td>revertProtection</td><td>否</td><td>false</td><td>默認為false。如設置為true，交易不會在鏈上執行失敗，但上鏈速度会受到影响且存在无法上链的可能，請根據實際需求謹慎選擇開啓。</td></tr></tbody></table>

### **Priority** Fee

Priority Fee是Solana在Base Fee（發送交易的最低成本，交易中每包含一個簽名花費5000 Lamports）基礎上的額外交易費用。由於計算資源有限，Leader節點在出塊時主要按交易價值對交易進行排序，Priority Fee越高的交易被優先納入下個區塊的概率越高。建議在發送交易時將CU Price至少設置為1,000,000。

### Tip

在構建交易時，需在交易中添加Tip轉賬指令（建議將Tip指令放在靠前位置），用於進一步加速交易上鍊。BlockRazor不從Tip中收取服務費。Tip指令轉賬金額至少為100,000 Lamports（0.0001 Sol），建議將Tip置為[`getTransactionfee`](../../../streams/network-fee-stream/solana/get-transactionfee.md)返回的推薦值，接收Tip的账户地址為：

```
"FjmZZrFvhnqqb9ThCuMVnENaM3JGVuGWNyCAxRJcFpg9",
"6No2i3aawzHsjtThw81iq1EXPJN6rh8eSJCLaYZfKDTG",
"A9cWowVAiHe9pJfKAj3TJiN9VpbzMUq6E4kEvf5mUT22",
"Gywj98ophM7GmkDdaWs4isqZnDdFCW7B46TXmKfvyqSm",
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
```

{% hint style="info" %}
為盡量避免因地址佔用引起交易處理性能下降，導致交易延遲，請盡量在發交易時輪換Tip賬戶地址。
{% endhint %}

### Keep Alive

請發送 POST 請求到健康檢查端點以保持連線活躍，請求示例如下：

{% tabs %}
{% tab title="CURL" %}
```bash
curl -X POST 'http://frankfurt.solana.blockrazor.xyz:443/v2/health?auth=<auth_token>' \
-H "Content-Type: text/plain" \
-d ""
```
{% endtab %}
{% endtabs %}

### 返回參數

<table><thead><tr><th width="100.3515625">狀態碼</th><th width="192.515625">消息</th><th>釋義</th></tr></thead><tbody><tr><td>200</td><td>OK</td><td>請求正常</td></tr><tr><td>400</td><td>BadRequest</td><td>請求參數不合法</td></tr><tr><td>403</td><td>Forbidden</td><td>請求被拒絕，auth為空、無效或過期</td></tr><tr><td>500</td><td>InternalServerError</td><td>內部服務錯誤</td></tr></tbody></table>


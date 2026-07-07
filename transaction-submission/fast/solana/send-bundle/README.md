# Send Bundle

{% hint style="warning" %}
Solana發送bundle的服務不和訂閱計劃綁定，可前往 [Authentication](../../../../get-started/authentication.md) 獲取API KEY，默认限流为3 TPS。如需提升限流標準，請[聯繫](https://discord.com/invite/qqJuwRb8Nh)我們，我們會在第一時間處理
{% endhint %}

`Send Bundle` 是 BlockRazor 為 Solana 提供的bundle發送接口，用於將已簽名交易以bundle形式低延迟發送到鏈上。

单个bundle中最多可发送 4 笔交易 。这些交易**按顺序**执行，如果任何一笔交易失败，整个bundle将无法执行。

为了增强数据包的 MEV 保护，请在bundle第一笔交易的指令中使用以 `jitodontfront` 和 `dontbund1e` 开头的任何有效的 Solana 公钥，如该笔交易遭遇frontrun攻击则将被 Jito/Harmonic 区块引擎拒绝。

### 端點

{% tabs %}
{% tab title="HTTP" %}
<table data-search="false"><thead><tr><th width="108.7109375">地区</th><th>URL</th></tr></thead><tbody><tr><td>法蘭克福</td><td>http://frankfurt.solana.blockrazor.xyz:443/sendBundle</td></tr><tr><td>法蘭克福</td><td>http://frankfurt-allnodes.solana.blockrazor.xyz:443/sendBundle</td></tr><tr><td>法蘭克福</td><td>http://frankfurt-cherryservers.solana.blockrazor.xyz:443/sendBundle</td></tr><tr><td>紐約</td><td>http://newyork.solana.blockrazor.xyz:443/sendBundle</td></tr><tr><td>東京</td><td>http://tokyo.solana.blockrazor.xyz:443/sendBundle</td></tr><tr><td>阿姆斯特丹</td><td>http://amsterdam.solana.blockrazor.xyz:443/sendBundle</td></tr><tr><td>阿姆斯特丹</td><td>http://amsterdam-cherryservers.solana.blockrazor.xyz:443/sendBundle</td></tr><tr><td>倫敦</td><td>http://london.solana.blockrazor.xyz:443/sendBundle</td></tr><tr><td>多倫多</td><td>http://toronto.solana.blockrazor.xyz:443/sendBundle</td></tr><tr><td>新加坡</td><td>http://singapore.solana.blockrazor.xyz:443/sendBundle</td></tr><tr><td>洛杉磯</td><td>http://losangeles.solana.blockrazor.xyz:443/sendBundle</td></tr></tbody></table>
{% endtab %}

{% tab title="gRPC" %}
<table><thead><tr><th width="115.80859375">地区</th><th width="544.58984375">URL</th></tr></thead><tbody><tr><td>法蘭克福</td><td>frankfurt.solana-grpc.blockrazor.xyz:80</td></tr><tr><td>法蘭克福</td><td>frankfurt-allnodes.solana-grpc.blockrazor.xyz:80</td></tr><tr><td>法蘭克福</td><td>frankfurt-cherryservers.solana-grpc.blockrazor.xyz:80</td></tr><tr><td>紐約</td><td>newyork.solana-grpc.blockrazor.xyz:80</td></tr><tr><td>東京</td><td>tokyo.solana-grpc.blockrazor.xyz:80</td></tr><tr><td>阿姆斯特丹</td><td>amsterdam.solana-grpc.blockrazor.xyz:80</td></tr><tr><td>阿姆斯特丹</td><td>amsterdam-cherryservers.solana-grpc.blockrazor.xyz:80</td></tr><tr><td>倫敦</td><td>london.solana-grpc.blockrazor.xyz:80</td></tr><tr><td>多倫多</td><td>toronto.solana-grpc.blockrazor.xyz:80</td></tr><tr><td>新加坡</td><td>singapore.solana-grpc.blockrazor.xyz:80</td></tr><tr><td>洛杉磯</td><td>losangeles.solana-grpc.blockrazor.xyz:80</td></tr></tbody></table>
{% endtab %}
{% endtabs %}

### 交易構建示例

* [Curl](curl.md)
* [Go](go.md)
* [Rust](rust.md)
* [JS](js.md)

### 请求参数

<table><thead><tr><th width="103.18359375">字段</th><th width="81.4765625">必填</th><th width="100.2734375">格式</th><th width="173.57421875">示例</th><th>備注</th></tr></thead><tbody><tr><td>transactions</td><td>是</td><td>string[]</td><td>["$base64_tx_1","$base64_tx_2","$base64_tx_3"]</td><td>已完成簽名的交易列表，Base64编码</td></tr></tbody></table>

### **Priority** Fee

Priority Fee是Solana在Base Fee（發送交易的最低成本，交易中每包含一個簽名花費5000 Lamports）基礎上的額外交易費用。由於計算資源有限，Leader節點在出塊時主要按交易價值對交易進行排序，Priority Fee越高的交易被優先納入下個區塊的概率越高。建議在發送交易時將CU Price至少設置為1,000,000。

### Tip

在構建交易時，需在交易中添加Tip轉賬指令（建議將Tip指令放在靠前位置），用於進一步加速交易上鍊。BlockRazor不從Tip中收取服務費。Tip指令轉賬金額至少為100,000 Lamports（0.0001 Sol），建議將Tip置為[`getTransactionfee`](../../../../streams/network-fee-stream/solana/get-transactionfee.md)返回的推薦值，接收Tip的账户地址為：

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
curl -X POST 'http://frankfurt.solana.blockrazor.xyz:443/health' \
-H "Content-Type: application/json" \
-H "apikey: <auth_token>" \
-d ""
```
{% endtab %}
{% endtabs %}

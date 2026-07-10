# Priority Fee & Tip

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

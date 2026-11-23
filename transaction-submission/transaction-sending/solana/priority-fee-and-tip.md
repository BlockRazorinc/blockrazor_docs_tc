---
description: 瞭解 BlockRazor Solana Transaction Sending 模式的Priority Fee & Tip 的配置和注意事項
metaLinks:
  canonical: priority-fee-and-tip.md
  alternates:
    - >-
      https://app.gitbook.com/s/jbyfG8gOgcdsK3wVxNdQ/transaction-submission/transaction-sending/solana/priority-fee-and-tip
---

# Solana Transaction Sending Priority Fee & Tip 配置

### **Priority** Fee

Priority Fee是Solana在Base Fee（發送交易的最低成本，交易中每包含一個簽名花費5000 Lamports）基礎上的額外交易費用。由於計算資源有限，Leader節點在出塊時主要按交易價值對交易進行排序，Priority Fee越高的交易被優先納入下個區塊的概率越高。建議在發送交易時將CU Price至少設置為1,000,000。

### Tip

在構建交易時，需在交易中添加Tip轉賬指令（建議將Tip指令放在靠前位置），用於進一步加速交易上鍊。BlockRazor不從Tip中收取服務費。Tip指令轉賬金額至少為100,000 Lamports（0.0001 Sol），建議將Tip置為[`getTransactionfee`](../../../streams/network-fee-stream/solana/get-transactionfee.md)返回的推薦值，接收Tip的账户地址為：

```
"GipkevdwsTrwf2PoGJZqcXXtjEP31DQbqM18QrFe5BDX",
"2TcMjZhDP2pjfYtBc6ZCrPEqvWYKZufptodQYMzhxxoG",
"FAYtM5hk3Dhon7ijDLbkSSsUqvxzhHCKoVCyHrn1Qtjf",
"DZVgBnByLL9MrR2fvZv1TdzXAUvED8EWaGPt3oehWKnm",
"Bym6AE2pEyH5mguycx3ja7ogMcByb9LYkDcHyrPDDzG5",
"Dxrxedq7FrDPfVU7fUADvSQADuzawsquYGmSrTafRRfH",
"4xmdnDV5xA59BydbUFPCBApTLZHn2L8vn6JRJHPYkWNp",
"8uCEKeEYEcrUkH9gKvRSc3ZjkJGsBtM5FGwZDU9pxtc",
"5mBQQcTxNwBgRuX4cab21hjrB116KvWLWinesEZTzy15",
"CSocujMUGBZ2Wb67FjB3VLFtdBTnnPEVmLmDD3FQbmHU",
"8EpzWhsr7uyZkfVD7MTHoWqupM14xngzMeUcCkWbY1UG",
"56XRik27PddNnR7JkHKqd5pWdMHeqje7TCm1gsdmvnTP",
"2DJNtR1tT7vfub7sDJ6v9NGmrzGVFUer5FvjhEcWEpvv",
"3AveZTS3PJo5qKEqHJci5bjW8neufriirr9mJXfsv4iH",
"9DC798ezxU2f2i8ggQ4vsqrsNxdDWJWAFg4cT1xLDR1X"
```

你也可以動態獲取Tip地址，以防Tip地址更新時無法及時同步

```bash
curl http://frankfurt.solana.blockrazor.xyz:443/getTipAccounts
```

{% hint style="info" %}
為盡量避免因地址佔用引起交易處理性能下降，導致交易延遲，請盡量在發交易時輪換Tip賬戶地址。
{% endhint %}


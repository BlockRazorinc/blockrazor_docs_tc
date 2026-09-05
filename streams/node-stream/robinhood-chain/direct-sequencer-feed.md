---
description: 介紹Robinhood Chain Sequencer Feed的定義、優勢、benchmark、價格和接入方法
---

# Robinhood Chain Direct Sequencer Feed

### Direct Sequencer Feed是什麼

相比Node-required Sequencer Feed，Direct Sequencer Feed 無需部署節點，普通客戶端即可直接接收最新區塊，以更低延遲追蹤鏈上信號，適合狙擊、跟單等不依賴完整節點狀態同步的場景。

需要注意的是，Direct Sequencer Feed在網絡擁堵期間可能出現跳塊現象。

### 價格

價格為$80 / stream / 日和$800 / stream / 月。 <a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=robinhood&#x26;serviceId=robinhood_direct_feed_stream&#x26;billing=day" class="button primary small">訂閱</a>

### 端點

<table><thead><tr><th width="152.515625">地区</th><th>端点</th></tr></thead><tbody><tr><td>俄亥俄</td><td>wss://us.robinhood-feeder.blockrazor.io/ws/direct/{authToken}</td></tr><tr><td>東京</td><td>wss://jp.robinhood-feeder.blockrazor.io/ws/direct/{authToken}</td></tr></tbody></table>

### 使用步驟

{% stepper %}
{% step %}
<a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=robinhood&#x26;serviceId=robinhood_direct_feed_stream&#x26;billing=day" class="button primary small">訂閱</a> **BlockRazor Sequencer Feed**
{% endstep %}

{% step %}
**在portal獲取auth，將其作為URI拼接於wss url**

wss://us.robinhood-feeder.blockrazor.io/ws/direct/{authToken}
{% endstep %}

{% step %}
**選擇 WebSocket 客戶端，建立 WSS 連線**

使用普通 WebSocket 客戶端即可，例如 Node.js、Go、Python 或 Rust 的 WebSocket Library；無需部署 Robinhood Chain 節點，也不需要配置 `--node.feed.input.url`
{% endstep %}

{% step %}
**接收並解析數據，解析後的數據結構如下：**

{% code overflow="wrap" %}
```json
{
  "version": 1,
  "messages": [
    {
      "sequenceNumber": 50543784,
      "message": {
        "message": {
          "header": {
            "kind": 3,
            "sender": "0xa4b000000000000000000073657175656e636572",
            "blockNumber": 25872577,
            "timestamp": 1788146984,
            "requestId": null,
            "baseFeeL1": 0
          },
          "l2Msg": {
            "encoding": "Nitro L2 message",
            "kind": 3,
            "kindName": "Batch",
            "decodedByteLength": 3487,
            "transactionCount": 10,
            "items": [
              {
                "index": 0,
                "messageKind": 4,
                "messageKindName": "SignedTx",
                "messageLength": 244,
                "transaction": {
                  "hash": "0xc4d8935f3e63b7b4…1e46fd37",
                  "from": "0x0dd10d651d18bc70594cb070e1117b6cd5e4a8a4",
                  "type": 2,
                  "typeName": "EIP-1559",
                  "chainId": 4663,
                  "nonce": 901,
                  "maxPriorityFeePerGas": 1,
                  "maxFeePerGas": 466288000,
                  "gasLimit": 120000,
                  "to": "0x000000000022d473030f116ddee9f6b43ac78ba3",
                  "value": 0,
                  "input": "0x87517c4500000000…6a94fc2f",
                  "accessList": [],
                  "yParity": 0,
                  "r": "0x1a6881f8f30266eb…090c7b15",
                  "s": "0x15e0901b330dfdba…0e9eb53a",
                  "rawTransaction": "0x02f8f08212378203…0e9eb53a"
                }
              },
              {
                "index": 2,
                "messageKind": 4,
                "messageKindName": "SignedTx",
                "messageLength": 341,
                "transaction": {
                  "hash": "0xef96fbbeb7072b85…6c67ee47",
                  "from": "0x137fe0e0fdbfe6487588f0ef842f418bbb738b8e",
                  "type": 0,
                  "typeName": "Legacy",
                  "chainId": 4663,
                  "nonce": 14,
                  "gasPrice": 250000000,
                  "gasLimit": 400000,
                  "to": "0xcaf681a66d020601342297493863e78c959e5cb2",
                  "value": 40000000000000,
                  "input": "0x04e45aaf00000000…00000000",
                  "v": 9362,
                  "yParity": 1,
                  "r": "0xe65d56b7f3eb63d7…4656bb9c",
                  "s": "0x4362c24ab37d7590…88c290ce",
                  "rawTransaction": "0xf901510e840ee6b2…88c290ce"
                }
              },
              {
                "index": 4,
                "messageKind": 4,
                "messageKindName": "SignedTx",
                "messageLength": 119,
                "transaction": {
                  "hash": "0x00f4bb0661c05fe8…3938c8bf",
                  "from": "0x6ae3de978e63b63e8486d1f495339633495d8264",
                  "type": 2,
                  "typeName": "EIP-1559",
                  "chainId": 4663,
                  "nonce": 3,
                  "maxPriorityFeePerGas": 234192000,
                  "maxFeePerGas": 542634600,
                  "gasLimit": 35010,
                  "to": "0xc45aa399aa75ace0ae56c372898abcb36d33161f",
                  "value": 330170416874734,
                  "input": "0x",
                  "accessList": [],
                  "yParity": 1,
                  "r": "0xdda460f047c07f2a…67caa4a0",
                  "s": "0x5f82128ed043cc80…4d2f6024",
                  "rawTransaction": "0x02f8738212370384…4d2f6024"
                }
              }
            ]
          },
          "l2MsgBase64": "AwAAAAAAAAD0BAL48I…jqF1iw=="
        },
        "delayedMessagesRead": 193164
      },
      "blockHash": "0x220c2e737333e10d…2e261230",
      "signatureV2": "Rg3J2yhBQ8pxYUqc6e…xkNk7wA=",
      "blockMetadata": null
    }
  ]
}
```
{% endcode %}


{% endstep %}
{% endstepper %}


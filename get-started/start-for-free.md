---
description: >-
  BlockRazor面向新註冊用戶免费提供Solana, BSC, Etherem和Base上的多模式交易發送服務，包括RPC、Block
  Builder、Transaction Sending等模式。
metaLinks:
  canonical: start-for-free.md
  alternates:
    - https://app.gitbook.com/s/jbyfG8gOgcdsK3wVxNdQ/get-started/start-for-free
---

# 免費發送交易

### RPC

<table><thead><tr><th width="125.16015625">鏈</th><th>方法</th><th>限流</th></tr></thead><tbody><tr><td>BSC</td><td><ul><li><a href="../transaction-submission/rpc/bsc/eth_sendrawtransaction.md"><code>eth_sendRawTransaction</code></a></li><li><a href="../transaction-submission/rpc/bsc/eth_sendbundle.md"><code>eth_sendBundle</code></a></li><li>其他JSON RPC方法</li></ul></td><td>-</td></tr><tr><td>Ethereum</td><td><ul><li><a href="../transaction-submission/rpc/ethereum-rpc/eth_sendrawtransaction.md"><code>eth_sendRawTransaction</code></a></li><li><a href="../transaction-submission/rpc/ethereum-rpc/eth_sendbundle.md"><code>eth_sendBundle</code></a></li><li>其他JSON RPC方法</li></ul></td><td>-</td></tr><tr><td>Base</td><td><ul><li><a href="../transaction-submission/transaction-sending/base/eth_sendrawtransaction.md"><code>eth_sendRawTransaction</code></a></li></ul></td><td><ul><li>1 Tx / 5s</li></ul></td></tr></tbody></table>

### Block Builder

<table><thead><tr><th width="125.484375">鏈</th><th width="288.10546875">方法</th><th>限流</th></tr></thead><tbody><tr><td>BSC</td><td><ul><li><a href="../transaction-submission/rpc/ethereum-rpc/eth_sendbundle.md"><code>eth_sendBundle</code></a></li><li><a href="../transaction-submission/block-builder/send-privatetransaction.md"><code>eth_sendPrivateTransaction</code></a></li></ul></td><td>-</td></tr></tbody></table>

### Transaction Sending

<table><thead><tr><th width="113.1328125">鏈</th><th>方法</th><th>限流</th></tr></thead><tbody><tr><td>Solana</td><td><ul><li><a href="../transaction-submission/transaction-sending/base/eth_sendrawtransaction-tip.md"><code>Send Transaction</code></a></li><li><a href="../transaction-submission/transaction-sending/solana/send-bundle/"><code>Send Bundle</code></a></li><li><a href="../transaction-submission/transaction-sending/solana/send-batch/"><code>Send Batch</code></a></li></ul></td><td>-</td></tr><tr><td>BSC</td><td><ul><li><a href="../transaction-submission/transaction-sending/bsc/broadcast-tx.md"><code>Broadcast Tx</code></a></li></ul></td><td><ul><li>TPS：10 Txs / 5s</li><li>每日交易上限：10</li></ul></td></tr><tr><td>Ethereum</td><td><ul><li><a href="../transaction-submission/transaction-sending/ethereum/broadcast-tx.md"><code>Broadcast Tx</code></a></li></ul></td><td><ul><li>TPS：10 Txs / 5s</li><li>每日交易上限：10</li></ul></td></tr><tr><td>Base</td><td><ul><li><a href="../transaction-submission/transaction-sending/base/eth_sendrawtransaction.md"><code>eth_sendRawTransaction</code></a></li></ul></td><td><ul><li>默認為10 TPS</li></ul></td></tr></tbody></table>


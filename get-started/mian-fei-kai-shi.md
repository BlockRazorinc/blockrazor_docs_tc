# 免費開始

{% hint style="info" %}
BlockRazor面向新註冊用戶免费提供Solana, BSC, Etherem和Base上的多模式交易發送服務，包括RPC、Fast、Bundle、Block Builder等模式。
{% endhint %}

### RPC

<table><thead><tr><th width="125.16015625">鏈</th><th>方法</th><th>限流</th></tr></thead><tbody><tr><td>BSC</td><td><ul><li><a href="../transaction-submission/rpc/bsc/eth_sendrawtransaction.md"><code>eth_sendRawTransaction</code></a></li><li><a href="../transaction-submission/rpc/bsc/eth_sendmevbundle/"><code>eth_sendMevBundle</code></a></li><li>其他JSON RPC方法</li></ul></td><td>-</td></tr><tr><td>Ethereum</td><td><ul><li><a href="../transaction-submission/rpc/ethereum/eth_sendrawtransaction.md"><code>eth_sendRawTransaction</code></a></li><li><a href="../transaction-submission/rpc/ethereum/eth_sendbundle.md"><code>eth_sendBundle</code></a></li><li>其他JSON RPC方法</li></ul></td><td>-</td></tr><tr><td>Base</td><td><ul><li><a href="../transaction-submission/rpc/base/eth_sendrawtransaction.md"><code>eth_sendRawTransaction</code></a></li></ul></td><td>1 Tx / 5s</td></tr></tbody></table>

### Block Builder

<table><thead><tr><th width="125.484375">鏈</th><th width="288.10546875">方法</th><th>限流</th></tr></thead><tbody><tr><td>BSC</td><td><ul><li><a href="../transaction-submission/rpc/ethereum/eth_sendbundle.md"><code>eth_sendBundle</code></a></li><li><a href="../transaction-submission/block-builder/send-privatetransaction.md"><code>eth_sendPrivateTransaction</code></a></li></ul></td><td>-</td></tr></tbody></table>

### Fast

<table><thead><tr><th width="113.1328125">鏈</th><th>方法</th><th>限流</th></tr></thead><tbody><tr><td>Solana</td><td><ul><li><a href="../transaction-submission/fast/base/eth_sendrawtransaction.md"><code>Send Transaction</code></a></li><li><a href="../transaction-submission/fast/solana/send-transaction/send-in-plain-text.md"><code>Send Transaction</code> v2</a></li></ul></td><td>默認為3 TPS</td></tr><tr><td>BSC</td><td><ul><li><a href="../transaction-submission/fast/bsc/eth_sendrawtransaction.md"><code>eth_sendRawTransaction</code></a></li><li><a href="../transaction-submission/fast/bsc/eth_sendrawtransaction-v2.md"><code>eth_sendRawTransaction</code> v2</a></li></ul></td><td>默認為10 TPS</td></tr><tr><td>Base</td><td><ul><li><a href="../transaction-submission/rpc/base/eth_sendrawtransaction.md"><code>eth_sendRawTransaction</code></a></li></ul></td><td>默認為10 TPS</td></tr></tbody></table>


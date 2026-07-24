# Send Bundle

{% hint style="warning" %}
Solana發送bundle的服務默認不對外開放，如需提升限流標準，請[聯繫](https://discord.com/invite/qqJuwRb8Nh)我們，我們會在第一時間處理
{% endhint %}

`Send Bundle` 是 BlockRazor 為 Solana 提供的bundle發送接口，用於將已簽名交易以bundle形式低延迟發送到鏈上。

单个bundle中最多可发送 4 笔交易 。这些交易**按顺序**执行，如果任何一笔交易失败，整个bundle将无法执行。

为了增强数据包的 MEV 保护，请在bundle第一笔交易的指令中使用以 `jitodontfront` 和 `dontbund1e` 开头的任何有效的 Solana 公钥，如该笔交易遭遇frontrun攻击则将被 Jito/Harmonic 区块引擎拒绝。

### 端點

* `POST /sendBundle`
* `gRPC`

### 交易構建示例

* [Curl](curl.md)
* [Go](go.md)
* [Rust](rust.md)
* [JS](js.md)

### 请求参数

<table><thead><tr><th width="103.18359375">字段</th><th width="81.4765625">必填</th><th width="100.2734375">格式</th><th width="173.57421875">示例</th><th>備注</th></tr></thead><tbody><tr><td>transactions</td><td>是</td><td>string[]</td><td>["$base64_tx_1","$base64_tx_2","$base64_tx_3"]</td><td>已完成簽名的交易列表，Base64编码</td></tr></tbody></table>

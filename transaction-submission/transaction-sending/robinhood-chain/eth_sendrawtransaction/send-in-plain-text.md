---
description: 介紹BlockRazor Robinhood Chain eth_sendRawTransaction(send in plain text)的集成方法
metaLinks:
  canonical: send-in-plain-text.md
  alternates:
    - >-
      https://app.gitbook.com/s/jbyfG8gOgcdsK3wVxNdQ/transaction-submission/transaction-sending/robinhood-chain/eth_sendrawtransaction/send-in-plain-text
---

# Robinhood Chain Transaction Sending in Plain Text

`Send in Plain Text` 用於在 Robinhood 上發送已簽名的交易，與 [eth\_sendRawTransaction](./) 方式相比，它提供了一種更精簡、更迅速的交易提交途徑。

* 繞過 CORS 預檢： 它消除了通常由 OPTIONS 預檢請求所引起的延遲。
* 純文本而非 JSON： 採用簡單的純文本傳輸，避免了與解析 JSON 相關的計算負擔。此外，由此產生的較小數據包尺寸有助於縮短網路傳輸時間並降低成本。

`Send in Plain Text` 的以上特性更適合擁有全球用戶的前端交易應用項目。

### 請求參數

<table><thead><tr><th width="150.40234375">参数</th><th width="81">必选</th><th width="82">格式</th><th width="106">示例</th><th>描述</th></tr></thead><tbody><tr><td>-</td><td>是</td><td>String</td><td>"0x…4b"</td><td>經過簽名的raw transaction</td></tr></tbody></table>

### 請求示例

{% tabs %}
{% tab title="HTTPS" %}
```bash
curl -X POST 'https://robinhood.blockrazor.io/v2/eth_sendRawTransaction?auth=<auth_token>' \
-H "Content-Type: text/plain" \
-d "<RawTx>"
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
注意：

* auth和方法名必須以 URI 參數的形式填入URL，比如https://robinhood.blockrazor.io/v2/eth\_sendRawTransaction?auth=\<auth\_token>
* 請求中唯一允許的header是 `Content-Type: text/plain`
{% endhint %}

### 返回參數

<table><thead><tr><th width="100.3515625">狀態碼</th><th width="192.515625">消息</th><th>釋義</th></tr></thead><tbody><tr><td>200</td><td>OK</td><td>請求正常</td></tr><tr><td>400</td><td>BadRequest</td><td>請求參數不合法</td></tr><tr><td>403</td><td>Forbidden</td><td>請求被拒絕</td></tr><tr><td>500</td><td>InternalServerError</td><td>內部服務錯誤</td></tr></tbody></table>


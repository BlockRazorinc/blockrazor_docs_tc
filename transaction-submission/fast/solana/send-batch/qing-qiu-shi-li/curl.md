# Curl

### 請求示例

{% code overflow="wrap" %}
```bash
curl --request POST \
  --url http://frankfurt.solana.blockrazor.xyz:443/sendBatch \
  --header 'Content-Type: application/json' \
  --header 'apikey: $auth_token' \
  --data '{
  "transactions":["$base64_tx_1","$base64_tx_2","$base64_tx_3"],
  "mode": "fast"
}'
```
{% endcode %}

### 返回示例

正常

{% code overflow="wrap" %}
```json
{
  "result": [{
    "signature": "58pw71yy2LDSw......Kdx5akJeRGUTS9bwGctXcHPAu",
    "error": ""
  }],
  "error": ""
}
```
{% endcode %}

異常

{% code overflow="wrap" %}
```json
{"result":[],"error":"error: Invalid authentication credentials. Please ensure your auth token is correct and try again"}
```
{% endcode %}

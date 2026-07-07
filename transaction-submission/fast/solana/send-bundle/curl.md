# Curl

### 請求示例

{% code overflow="wrap" %}
```bash
curl --request POST \
  --url http://frankfurt.solana.blockrazor.xyz:443/sendBundle \
  --header 'Content-Type: application/json' \
  --header 'apikey: $auth_token' \
  --data '{
  "transactions":["$base64_tx_1","$base64_tx_2","$base64_tx_3"]
}'
```
{% endcode %}

### 返回示例

正常

```json
{"signature":"$first_tx_signature","error":""}
```

異常

{% code overflow="wrap" %}
```json
{"signature":"","error":"error: Invalid authentication credentials. Please ensure your auth token is correct and try again"}
```
{% endcode %}

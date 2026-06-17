---
description: 本文檔介紹如何通過Curl構建、發送Solana交易
---

# Curl

### Send Transaction

```json
// 以下是fast模式的请求示例

curl --request POST \
  --url http://frankfurt.solana.blockrazor.xyz:443/sendTransaction \
  --header 'Content-Type: application/json' \
  --header 'apikey: $auth_token' \
  --data '{
  "transaction":"$base64_tx",
  "mode":"fast",
  "revertProtection":false
}'
```

### Send Binary Transaction

```json
// 以下是fast模式的请求示例

curl --request POST \
  --url http://frankfurt.solana.blockrazor.xyz:443/sendBinaryTransaction \
  --header 'Content-Type: application/json' \
  --header 'apikey: $auth_token' \
  --data '{
  "binaryTransaction":"$binary_tx",
  "mode":"fast",
  "revertProtection":false
}'
```

---
description: 介紹BlockRazor Solana Fast模式的Keep Alive集成方法
---

# Keep Alive

請發送 POST 請求到健康檢查端點以保持連線活躍，請求示例如下：

{% tabs %}
{% tab title="CURL" %}
```bash
curl -X POST 'http://frankfurt.solana.blockrazor.xyz:443/health' \
-H "Content-Type: application/json" \
-H "apikey: <auth_token>" \
-d ""
```
{% endtab %}
{% endtabs %}


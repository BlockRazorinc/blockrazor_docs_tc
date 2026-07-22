# 健康檢查

{% hint style="info" %}
Robinhood Chain RPC暫不對用戶開放，如需對接請[聯繫](https://discord.gg/qqJuwRb8Nh)我們
{% endhint %}

請發送 POST 請求到健康檢查端點以保持連線活躍，請求示例如下：

{% tabs %}
{% tab title="CURL" %}
```bash
curl -X POST 'https://robinhood.blockrazor.io/health' \
-H "Content-Type: application/json" \
-H "apikey: <auth_token>" \
-d ""
```
{% endtab %}
{% endtabs %}

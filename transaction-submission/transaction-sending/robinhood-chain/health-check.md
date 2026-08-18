---
description: 介紹BlockRazor Robinhood Chain Transaction Sending 健康檢查的集成方法
metaLinks:
  canonical: health-check.md
  alternates:
    - >-
      https://app.gitbook.com/s/jbyfG8gOgcdsK3wVxNdQ/transaction-submission/transaction-sending/robinhood-chain/health-check
---

# Robinhood Chain Transaction Sending 健康檢查

{% hint style="info" %}
Robinhood Chain Transaction Sending 暫不對用戶開放，如需對接請[聯繫](https://discord.gg/qqJuwRb8Nh)我們
{% endhint %}

請發送 POST 請求到健康檢查端點以保持連線活躍，請求示例如下：

{% tabs %}
{% tab title="CURL" %}
```bash
curl -X POST 'https://robinhood.blockrazor.io/health' \
-H "Content-Type: application/json" \
-H "Authorization: Bearer <auth-token>" \
-d ""
```
{% endtab %}
{% endtabs %}

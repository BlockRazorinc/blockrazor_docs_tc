---
layout:
  width: wide
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
  metadata:
    visible: true
  tags:
    visible: true
  actions:
    visible: true
metaLinks:
  canonical: endpoint.md
---

# BSC RPC 端點

### 通用端點

<table data-search="false"><thead><tr><th width="127.3359375"></th><th width="186">default</th><th width="196">fullprivacy</th><th>maxbackrun</th></tr></thead><tbody><tr><td>URL</td><td>https://bsc.blockrazor.xyz</td><td>https://bsc.blockrazor.xyz/fullprivacy</td><td>https://bsc.blockrazor.xyz/maxbackrun</td></tr><tr><td>MEV保護</td><td>保護</td><td>保護</td><td>保護</td></tr><tr><td>交易隱私</td><td>最小程度披露</td><td>全隱私</td><td>最大程度披露</td></tr><tr><td>返利可能性</td><td>中等</td><td>無返利</td><td>高</td></tr><tr><td>返利比例</td><td>支持</td><td>無返利</td><td>支持</td></tr><tr><td>Revert保護</td><td>不保護</td><td>保護</td><td>保護</td></tr></tbody></table>

<details>

<summary><strong>default模式</strong></summary>

default模式下，提交至BSC RPC的交易，僅向Searcher披露必要的交易數據（hash和logs & state），以在最大程度保護交易隱私的前提下贏得返利機會。同時，為保證交易納入區塊的速度，交易不做revert保。

</details>

<details>

<summary><strong>fullprivacy模式</strong></summary>

fullprivacy模式下，提交至BSC RPC的交易，不會披露任何交易數據，BSC RPC會直接轉發交易至主流builder。由於未披露任何交易數據，交易也不會收到返利，因此無需設置返利比例。該模式下的交易同時會受到revert保護。

</details>

<details>

<summary><strong>maxbackrun模式</strong></summary>

maxbackrun模式下，提交至BSC RPC的交易，會在隱私保護的前提下披露必要的交易數據（hash、to、calldata、functionSelector、logs & state），以最大可能獲得返利。該模式下的交易同時會受到revert保護。

</details>

{% openapi-operation spec="BlockRazor-BSC-RPC" path="/" method="post" %}
[OpenAPI BlockRazor-BSC-RPC](https://4401d86825a13bf607936cc3a9f3897a.r2.cloudflarestorage.com/gitbook-x-prod-openapi/raw/a53bb0011f3e056eb9dbaa43acef1894d8608f9612627f3b40b58963a37f0849.yaml?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=dce48141f43c0191a2ad043a6888781c%2F20260818%2Fauto%2Fs3%2Faws4_request&X-Amz-Date=20260818T065653Z&X-Amz-Expires=172800&X-Amz-Signature=a524c35928a200b51709c643783f7d0be740c54fc327b7c53a17393c0e70f385&X-Amz-SignedHeaders=host&x-amz-checksum-mode=ENABLED&x-id=GetObject)
{% endopenapi-operation %}

### 專屬端點

專屬端點是BlockRazor為用戶提供的專屬交易渠道端點。用戶可在BlockRazor控制台配置專屬端點，比如自定義專屬端點讓URL變得更具識別性、設置交易披露參數和返利地址以接收返利等，在將專屬端點添加到錢包或集成到項目後，可前往控制台查看該專屬端點的交易和返利情況。

<table><thead><tr><th width="148.8359375">端點</th><th width="551.25390625">URL示例</th></tr></thead><tbody><tr><td>專屬端點URL</td><td>https://bsc.blockrazor.xyz/&#x3C;rpc_id></td></tr><tr><td>自定義端點URL</td><td>https://&#x3C;custom_content>.bsc.blockrazor.xyz</td></tr></tbody></table>

### 區域端點

<table><thead><tr><th width="148.99609375">地區</th><th>端點</th></tr></thead><tbody><tr><td>東京</td><td>https://jp-bscscutum.blockrazor.xyz</td></tr><tr><td>紐約</td><td>https://us-bscscutum.blockrazor.xyz</td></tr><tr><td>法蘭克福</td><td>https://ger-bscscutum.blockrazor.xyz</td></tr><tr><td>都柏林</td><td>https://ire-bscscutum.blockrazor.xyz</td></tr></tbody></table>

區域端點適合對交易延遲極為敏感且交易源相對集中的項目方




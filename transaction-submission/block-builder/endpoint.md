# BSC Block Builder 端點

### **默認接入**

優先使用全局通用入口，適合快速完成接入並服務全球請求，端點：<mark style="color:$primary;">**https://rpc.blockrazor.builders**</mark>

### **區域化優化**

如果你的 Bot 或服務已經部署在特定區域，並且對延遲一致性更敏感，可以在接入全球通用端點的基礎上進一步接入區域入口

<table><thead><tr><th width="127">地區</th><th width="146">可用區（AWS）</th><th>RPC端點</th></tr></thead><tbody><tr><td>東京</td><td>apne1-az4</td><td>https://tokyo.builder.blockrazor.io</td></tr><tr><td>法蘭克福</td><td>euc1-az2</td><td>https://frankfurt.builder.blockrazor.io</td></tr><tr><td>弗吉尼亞</td><td>use1-az4</td><td>https://virginia.builder.blockrazor.io</td></tr><tr><td>都柏林</td><td>euw1-az1</td><td>https://dublin.builder.blockrazor.io</td></tr></tbody></table>

### **鏈路質量加強**

如果你已經完成基礎接入，開始關注提交路徑中的額外開銷、跨區域波動和高負載下的穩定性，可以進一步接入 [Fast Submit](bsc-block-builder-fast-submit.md)

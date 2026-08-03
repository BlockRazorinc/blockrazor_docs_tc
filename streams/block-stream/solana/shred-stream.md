---
description: 介紹BlockRazor Solana Shred Stream的服務、目標用戶、優勢以及接入方法
---

# Shred Stream

### Shred Stream是什麼

Shred Stream 是 BlockRazor 為 Solana 提供的shred數據訂閱服務，已部署在 Frankfurt、Amsterdam、Tokyo 和 New York 等多個區域，面向全球用戶提供更低延遲的 shred 分發能力。

### Shred Stream適合哪些用戶

* **Validators / RPCs:** 對於需要盡早接收 Solana Shred的驗證者、RPC節點來說，Shred Stream 可以作為更低延遲的數據輸入來源。
* **Trading Bots:** 對 Shred 到達時間高度敏感的交易機器人，可以利用 Shred Stream 更早接收 Shred 數據，為後續交易解析和策略執行爭取更多響應時間
* **DeFi Builders:** 對需要快速感知鏈上變化、縮短數據處理路徑的 DeFi 系統建設者來說，Shred Stream 提供更低延遲的鏈上交易獲取方式

### 為什麼選擇BlockRazor Shred Stream

Shred Stream 通過全球分布式高性能網絡，直接對接 Solana 高質押驗證者獲取 shreds，基於 UDP 以最小跳數轉發，整體鏈路更短、速度更快，適合對Solana交易數據獲取延遲要求極高的場景。

### 常見問題

<details>

<summary>接收到Shred後，我如何做shred解析，可以解析出什麼數據</summary>

Shred Stream接收到的是 Solana 網絡中的原始 `shred` UDP 數據流，不能直接當作結構化交易或區塊數據使用。通常需要經過 **接收、恢復、解析** 3 個步驟，才能提取出可用的交易內容。

可直接參考 [shreds-subscribe](https://github.com/BlockRazorinc/shreds-subscribe) 這個測試工具。它提供了一條完整的解析鏈路：

1. **UDP 接收：** 在指定端口持續接收 shred 數據
2. **FEC Recovery：** 使用 Reed-Solomon 算法恢復部分丟失的 shred
3. **Deshred + 交易解析：** 將 shred 還原為完整 block entries，並進一步解析其中的交易

完成這條鏈路後，通常可以拿到以下資料：

* 交易以及和指定賬戶相關的交易事件
* Block entries

</details>

<details>

<summary>Shred Stream和Geyser Stream有什麼區別</summary>

兩者的核心區別在於**數據層級**和**使用門檻**。Shred Stream 傳輸的是更底層的原始 `shred`，鏈路更短，延遲更低，適合極度追求時效的 Bots、RPC 和 Validators，但接入方需要自己做重組和解析；Geyser Stream 傳輸的是結構化的 `account`、`slot`、`block` 和 `transaction` 數據，客戶端可直接通過 gRPC 訂閱，接入更簡單，也更適合賬戶監聽、交易分析和穩定生產環境。若你要的是**更早的交易信號**，優先選 Shred Stream；若你要的是**更完整、易消費的實時鏈上數據**，優先選 [Geyser Stream](geyser-stream/)。

</details>

### 價格

價格為$50 / 條 / 日和$500 / 條 / 月。<a href="https://blockrazor.io/#/portal/pricing?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=solana&#x26;serviceId=solana_shreds_stream&#x26;billing=day" class="button primary small">訂閱</a>

### 使用說明

1. 前往[https://www.blockrazor.io/](https://www.blockrazor.io/)，點擊右上角的【註冊】，完成註冊
2. 前往[訂閱](https://blockrazor.io/#/pricing)頁面完成採購
3. 登錄控制台，前往【Solana】 - 【Shred Stream】，點擊【編輯】，输入IP:Port或domain:Port，選擇距離你的服务器最近的區域

<figure><img src="../../../.gitbook/assets/image (79).png" alt=""><figcaption></figcaption></figure>

{% code overflow="wrap" %}
```bash
# 請確認端口已開放。如果你的服務器使用AWS等雲服務，需在雲環境中額外配置安全組（security group）的入端(inbound)規則
sudo ufw allow <port>/udp
sudo ufw reload
```
{% endcode %}

4. 創建完成，点击【抓包】，复制抓包命令

<figure><img src="../../../.gitbook/assets/enode4.png" alt="" width="563"><figcaption></figcaption></figure>

6. 訪問服務器，執行抓包命令，查看Shred Stream

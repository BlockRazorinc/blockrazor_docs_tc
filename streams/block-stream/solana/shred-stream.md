# Shred Stream

### 介紹

Shred Stream以最低延遲向Validators, RPCs, Bots, DeFi項目方等傳輸shreds.

基於全球分布式高性能網絡，Shred Stream的relay直接對接高質押量驗證者獲取shreds，通過UDP協議以最小跳數轉發shreds數據包。目前BlockRazor的Shred Relay分布於4個區域：法蘭克福、阿姆斯特丹、東京和紐約。

### 價格

每月每條數據流的價格為$500，請前往[訂閱](https://blockrazor.io/#/pricing)頁面採購。

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


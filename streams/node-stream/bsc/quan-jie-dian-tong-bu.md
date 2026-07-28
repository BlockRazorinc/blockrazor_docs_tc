---
description: 介紹BlockRazor BSC全節點同步的服務、優勢、目標用戶以及接入方法
---

# 全節點同步

### 全節點同步是什麼

全節點同步是 BlockRazor 在 Node Stream 下提供的低延遲節點同步服務，用於幫助用戶自己的 BSC 全節點更快同步到最新區塊和 world state。

### 為什麼選擇全節點同步

對於很多高頻交易系統和基礎設施系統來說，“本地節點能不能足夠快地同步到最新狀態”是一個共性問題，即使應用本身邏輯再快，後續策略判斷、數據分析和交易執行計算也仍然會受到滯後狀態的影響。

全節點同步的優勢，不只是“幫助節點連上網絡”，而在於它為依賴本地節點狀態的系統提供了更適合生產環境的低延遲同步方式。與單純訂閱區塊流不同，全節點同步並不是向用戶持續推送一份結構化數據，而是讓用戶自己的全節點直接與 BlockRazor 的高性能網絡節點建立 P2P 連接，利用BlockRazor的全球高性能網絡更快從高質量節點接收最新區塊和狀態更新。

### 全節點同步適合哪些用戶

* **Quant Team / Trading Bot / Searcher**\
  **依賴本地節點狀態做策略判斷、交易準備或鏈上分析的量化和交易系統。**
* **Infra / Node Teams**\
  **負責節點部署、狀態同步和底層鏈路優化的工程團隊。**

如果你的目標只是低延遲獲取確認後的區塊數據，Block Stream 通常就已經足夠。\
如果你的系統需要自己的本地節點盡快同步到最新區塊和 world state，那麼全節點同步會更合適。

### Benchmark

在本次測試中，我們分別在 Dublin、Frankfurt、Tokyo 和 Virginia 四個區域，對“已連接 BlockRazor Relay 的節點”與“未連接 Relay 的節點”進行了對比。評估方式基於節點的 block 接收日誌，比較兩者接收同一區塊的時間差，以驗證全節點同步對本地全節點同步速度的提升效果。

<table><thead><tr><th width="117.09765625">Region</th><th width="264.55859375">Relay-Connected Node Lead Rate</th><th>P50 Lead</th><th>P90 Lead</th></tr></thead><tbody><tr><td>Dublin</td><td>98.92%</td><td>32 ms</td><td>66 ms</td></tr><tr><td>Frankfurt</td><td>98.92%</td><td>44 ms</td><td>63 ms</td></tr><tr><td>Tokyo</td><td>99.51%</td><td>113 ms</td><td>654 ms</td></tr><tr><td>Virginia</td><td>98.64%</td><td>33 ms</td><td>98 ms</td></tr></tbody></table>

從結果來看，接入 Relay 的節點在四個區域都表現出穩定優勢。按 matched block 樣本計算，連接 Relay 的節點在 Dublin、Frankfurt、Tokyo 和 Virginia 的領先比例分別達到 98.92%、98.92%、99.51% 和 98.64%。這說明在絕大多數可比樣本中，接入 BlockRazor Relay 的節點都能更早同步到新區塊，從而更快獲得最新鏈上狀態。

從領先幅度看，連接 Relay 的節點在四個區域的 P50 領先時間分別約為 32ms、44ms、113ms 和 33ms；在 P90 維度下，領先幅度分別達到 66ms、63ms、654ms 和 98ms。這表明全節點同步不僅提升了領先概率，也在更高分位場景下維持了可觀的同步優勢。

### 價格

每月每條數據流的價格為$800，請前往[訂閱](https://blockrazor.io/#/pricing)頁面採購。

### Relay IP

<table><thead><tr><th width="154">地區</th><th>Relay地址</th></tr></thead><tbody><tr><td>法蘭克福</td><td>64.130.47.75:50051</td></tr><tr><td>東京</td><td>63.254.162.18:50051</td></tr><tr><td>都柏林</td><td>141.98.217.82:50051</td></tr><tr><td>弗吉尼亞</td><td>208.91.105.204:50051</td></tr></tbody></table>

### 使用說明

#### 步驟1：採購Node Stream

1. 前往[https://www.blockrazor.io/](https://www.blockrazor.io/)，點擊右上角的【註冊】，完成註冊
2. 登錄控制台，前往【訂閱】- Node Stream，完成採購
3.  前往【服務】 - 【Streams】-【Node Stream】，點擊【編輯】

    <figure><img src="../../../.gitbook/assets/image (85).png" alt=""><figcaption></figcaption></figure>
4.  輸入需要連接relay的Ethereum客戶端Enode，選擇離Ethereum客戶端最近的地區，點擊【確認】，完成添加

    <figure><img src="../../../.gitbook/assets/image (81).png" alt="" width="375"><figcaption></figcaption></figure>
5. 回到Enode列表，點擊【複製Relay Enode】

#### 步驟2：向relay開放端口

{% hint style="info" %}
如果你的Ethereum客戶端部署於AWS等雲服務，需在雲環境中額外配置安全組（security group）的入端(inbound)規則。
{% endhint %}

1. 進入自己的Ethereum客戶端所在服务器，設置防火牆允許Relay訪問

```
sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="63.254.162.18" port port="30311" protocol="tcp" accept'
```

* source address是Relay的IP地址，可以在 [Relay IP](quan-jie-dian-tong-bu.md#relay-ip) 中查詢
* port是Ethereum客戶端允許Relay訪問的端口，一般默認為30311，用戶可根據自己節點配置修改

2. 重載防火牆配置，以使配置生效

```
sudo firewall-cmd --reload
```

#### 步骤3：设置Relay为TrustedNode（以Geth節點為例）

為確保Geth節點和Relay可以保持持續連接，建議在Geth節點的config.toml中添加Relay Enode

1. 在 config.toml文件中，找到 Node.P2P中的TrustedNodes字段，添加在步驟2中獲取的Relay Enode

```scheme
[Node.P2P]
TrustedNodes = ["enode://b5b4e5aa8d8f4568af755af6da0d4642b6475d8d87c3470632bdecab8f54e4e2936ec8ae0d6f34cff8b052235e81a281912c17dfcdbf40d6d3c281b78ada4134"]
```

2. 重啓Geth節點，指定config.toml啓動，`--config config.toml`

#### 步驟4：查詢連接狀態（以在Geth節點中開啓admin namespace為例）

1. 等待10分鐘，進入Geth節點， 執行命令，查看連接狀態

```
curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"admin_peers","params":[],"id":1}' http://localhost:8545
```

2. 在返回的数据中查詢相應地區的Relay Enode地址（可前往控制台複製獲取），如查詢到地址存在則證明連接成功

```json
[
    {
        "enode": "enode://9ddacbcca0dc1d1b112d470552acc795fce5c3e9f50983fcd5cee7b47289914295acaef3163bea819bcc967461978425def13595deb7de4063295c40e593f320@52.205.173.134:53754",
        "id": "8be29a75ac2cf81e3aa37ccc119630a9dfc43c88d7b5200398a466f5ef9097c4",
        "name": "Geth/v1.4.5/linux-amd64/go1.21.7",
        "caps": [
            "eth/68"
        ],
        "network": {
            "localAddress": "127.0.0.1:30311",
            "remoteAddress": "52.205.173.134:53754",
            "inbound": true,
            "trusted": false,
            "static": false
        },
        "protocols": {
            "eth": {
                "version": 68
            }
        }
    }
]
```

{% hint style="info" %}
如經查詢發現連接狀態異常，有可能是因為節點間的網絡通信出現問題，請前往[Discord](https://discord.com/invite/qqJuwRb8Nh)與我們取得聯系。
{% endhint %}

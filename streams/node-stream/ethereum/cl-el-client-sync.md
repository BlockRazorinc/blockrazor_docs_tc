---
description: 介紹BlockRazor Ethereum CL/EL 客戶端同步的服務、優勢、目標用戶以及接入方法
metaLinks:
  canonical: cl-el-client-sync.md
  alternates:
    - >-
      https://app.gitbook.com/s/jbyfG8gOgcdsK3wVxNdQ/streams/node-stream/ethereum/cl-el-client-sync
---

# Ethereum CL/EL 客戶端同步

### CL/EL 客戶端同步是什麼

CL/EL 客戶端同步是 BlockRazor 在 Node Stream 場景下，面向以太坊節點架構設計的低延遲節點同步能力，用於幫助用戶自己的 Ethereum 節點更快同步最新鏈上狀態。

與 BSC 全節點同步主要面向單一 EVM 執行節點不同，以太坊在 The Merge 後採用 CL/EL 雙客戶端架構。因此，Ethereum 的Node Stream不只是讓 EL 更快執行新區塊，也支持讓 CL 更快跟進正確 head、safe / finalized block 和共識層消息。

### 為什麼選擇 CL/EL 客戶端同步

對於很多高頻交易系統、Searcher、量化策略和基礎設施團隊來說，“本地 Ethereum 節點能不能足夠快地同步最新 head、執行最新區塊並更新 world state”是一個核心問題。即使策略系統、撮合邏輯或交易構造本身很快，只要本地節點看到的鏈頭或狀態落後，後續的策略判斷、模擬、風控、交易替換和執行決策仍然會受到影響。

CL/EL 客戶端同步的價值，不只是“幫助節點連上網絡”，而是為依賴本地 Ethereum 節點狀態的生產系統提供更低延遲、更穩定的同步入口，降低本地節點在策略判斷、交易模擬和 MEV 場景中的同步滯後對交易和分析系統造成的影響。

### CL/EL 客戶端同步適合哪些用戶

* **Quant Team / Trading Bot / Searcher**\
  **依賴本地節點狀態做策略判斷、交易準備或鏈上分析的量化和交易系統。**
* **Infra / Node Teams**\
  **負責節點部署、狀態同步和底層鏈路優化的工程團隊。**

如果你的目標只是低延遲獲取確認後的區塊數據，Block Stream 通常就已經足夠。\
如果你的系統需要自己的本地節點盡快同步到最新區塊和 world state，那麼CL/EL 客戶端同步會更合適。

### 價格

<table><thead><tr><th width="177.73046875">支付方式</th><th>價格</th><th>操作</th></tr></thead><tbody><tr><td>Personalized</td><td>$80 / client / 日<br>$800 / client / 月</td><td><a href="https://blockrazor.io/#/login?redirect=pricing&#x26;purchaseMode=personalized&#x26;chain=ethereum&#x26;serviceId=ethereum_enode&#x26;billing=day" class="button primary small">訂閱</a></td></tr><tr><td>Package</td><td>$1250 / month<br>和其他9項服務打包購買</td><td><a href="https://blockrazor.io/#/portal/pricing?redirect=pricing&#x26;purchaseMode=package&#x26;billing=month" class="button primary small">訂閱</a></td></tr></tbody></table>

### Relay IP

<table><thead><tr><th width="154">地區</th><th>Relay IP</th></tr></thead><tbody><tr><td>法蘭克福</td><td>64.130.47.75</td></tr><tr><td>東京</td><td>63.254.162.18</td></tr><tr><td>弗吉尼亞</td><td>208.91.105.204</td></tr></tbody></table>

### EL 客戶端使用說明

#### 步驟1：採購Node Stream

1. 前往[https://www.blockrazor.io/](https://www.blockrazor.io/)，點擊右上角的【註冊】，完成註冊
2. 登錄控制台，前往【訂閱】- Node Stream，完成採購
3. 前往【服務】 - 【Streams】-【Node Stream】，點擊【編輯】
4. 選擇離 EL 客戶端最近的地區，輸入需要連接relay的 EL 客戶端Enode，點擊【確認】，完成添加
5. 回到Node列表，點擊【複製Relay地址】

#### 步驟2：向relay開放端口

{% hint style="info" %}
如果你的 EL 客戶端部署於AWS等雲服務，需在雲環境中額外配置安全組（security group）的入端(inbound)規則。
{% endhint %}

1. 進入自己的 EL 客戶端所在服务器，設置防火牆允許Relay訪問

```bash
sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="63.254.162.18" port port="30303" protocol="tcp" accept'
```

* source address是Relay的IP地址，可以在 [Relay IP](cl-el-client-sync.md#relay-ip)中查詢
* port是 EL 客戶端允許Relay訪問的端口，一般默認為30303，用戶可根據自己節點配置修改

2. 重載防火牆配置，以使配置生效

```bash
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

```bash
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

### CL 客戶端使用說明

#### 步驟1：採購Node Stream

1. 前往[https://www.blockrazor.io/](https://www.blockrazor.io/)，點擊右上角的【註冊】，完成註冊
2. 登錄控制台，前往【訂閱】- Node Stream，完成採購
3. 前往【服務】 - 【Streams】-【Node Stream】，點擊【編輯】
4. 選擇離 CL 客戶端最近的地區，輸入需要連接relay的 CL 客戶端 ENR，點擊【確認】，完成添加
5. 回到Node列表，點擊【複製Relay地址】，獲取Relay的Multiaddr

#### 步驟2：向relay開放端口

{% hint style="info" %}
如果你的 CL 客戶端部署於AWS等雲服務，需在雲環境中額外配置安全組（security group）的入端(inbound)規則。
{% endhint %}

1. 進入自己的 CL 客戶端所在服务器，設置防火牆允許Relay訪問

```bash
sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="63.254.162.18" port port="9000" protocol="tcp" accept'

sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="63.254.162.18" port port="9000" protocol="udp" accept'
```

* source address是Relay的IP地址，可以在 [Relay IP](cl-el-client-sync.md#relay-ip) 中查詢
* port是 CL 客戶端允許Relay訪問的端口，用戶可根據客戶端類型默認值自行修改

2. 重載防火牆配置，以使配置生效

```bash
sudo firewall-cmd --reload
```

#### 步骤3：设置Relay为TrustedNode

在步驟1中获取 Relay Multiaddr `/ip4/<Relay_IP>/tcp/<Relay_P2P_PORT>/p2p/${Relay_Peer_ID}`，配置啟動參數，並重啟CL 客戶端

{% tabs %}
{% tab title="Lighthouse" %}
{% code overflow="wrap" %}
```bash
--boot-nodes <'Relay_Multiaddr','Bootnodes_ENR'> --trusted-peers "$Relay_Peer_ID"

## 由于boot-nodes参数在重启后会覆盖更新，为保证连接稳定性，请在Relay_Multiaddr后携带默认的bootnodes ENR参数，可参考 https://github.com/sigp/lighthouse/blob/120c3c6dac9df8ee4d83f055919bd3488abae4f6/common/eth2_network_config/built_in_network_configs/mainnet/bootstrap_nodes.yaml#L16
```
{% endcode %}
{% endtab %}

{% tab title="Prysm" %}
```bash
--peer "$Relay_Multiaddr"
```
{% endtab %}

{% tab title="Nimbus" %}
```bash
--netkey-file /path/to/netkey --direct-peer "$Relay_Multiaddr"
```
{% endtab %}

{% tab title="Teku" %}
```bash
--p2p-direct-peers "$Relay_Multiaddr"
```
{% endtab %}
{% endtabs %}

#### 步驟4：查詢連接狀態

1. 等待10分鐘，進入節點， 執行命令，查看Relay的`Peer ID`是否出現在state=connected 的 peers 裡

```bash
Relay_Peer_ID="16Uiu2..."
REST_PORT="<CLIENT_REST_PORT>"

curl -s "http://127.0.0.1:${REST_PORT}/eth/v1/node/peers?state=connected" \
  | jq --arg id "$Relay_Peer_ID" '.data[] | select(.peer_id == $id) | {
      peer_id,
      state,
      direction,
      last_seen_p2p_address,
      enr
    }'
```

2. 如果有輸出且看到 `"state": "connected"` ，就表示已經連上目標節點。

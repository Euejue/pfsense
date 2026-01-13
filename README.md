# pfSense 與 Wazuh 自動防禦系統整合

## 專案目的

本專案目標在於展示如何將 **pfSense 防火牆** 與 **Wazuh SIEM** 透過 **Agent 與 Active Response** 整合，  
實現從「被動紀錄」到「主動阻擋」的自動化防禦流程。  

當 pfSense 偵測到惡意流量時，Wazuh 透過日誌分析判斷威脅等級，並即時回傳阻擋指令，完成自動防禦。

---

## 系統架構說明

整體系統由三個主要元件組成：

### pfSense 防火牆
- 負責封包過濾、網路防護與日誌產生  
- 將日誌透過 Wazuh Agent 傳送至 Wazuh Manager  

### Wazuh Agent
- 部署於 pfSense 上  
- 監控防火牆核心日誌 `/var/log/filter.log`  
- 將日誌送至 Wazuh Manager 進行解碼與關聯分析  

### Wazuh Manager
- 接收 Agent 日誌並執行規則引擎  
- 偵測威脅事件，觸發 **Active Response** 指令  
- 將指令下達給 pfSense，實現自動阻擋惡意 IP  

---

## 技術重點

- **Active Response**：自動化阻擋流程，減少人工介入  
- **日誌監控**：即時抓取 pfSense 防火牆日誌，提取來源 IP、目標端口等關鍵資訊  
- **規則引擎**：設定單一 IP 在短時間內連續觸發多次阻擋事件時，判定為惡意攻擊  
- **防禦自動化**：pfSense 從單純封鎖升級為主動防禦節點  

---
## pfSense 運行狀態
<img width="2608" height="1384" alt="image" src="https://github.com/user-attachments/assets/75418112-6b4e-4f5c-a577-d3523581420f" />

## 操作情境

### 偵測並阻擋惡意掃描

假設 pfSense 遭受暴力掃描攻擊，Wazuh Manager 回傳 Active Response 指令，流程如下：

```json
{
  "event": "pfSense firewall alert",
  "level": 12,
  "src_ip": "203.0.113.45",
  "description": "Detected multiple unauthorized connection attempts",
  "action": "block",
  "target": "pfSense-LAN"
}
```
## 流程
pfSense -> Wazuh Agent -> Wazuh Manager -> Active Response -> pfSense

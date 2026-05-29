# **軟體安全管理政策**

**文件編號：** ISMS-SEC-DEV-001  
**版本：** v1.0  
**生效日期：** YYYY/MM/DD  
**文件等級：** 內部文件

***

## **目的（Purpose）**

為確保公司於軟體開發生命週期中之資訊安全，降低供應鏈攻擊、未授權軟體使用及憑證外洩等風險，建立一致性與可稽核之安全控管機制，並符合 ISO/IEC 27001:2022 標準要求，特訂定本政策。

***

## **適用範圍（Scope）**

本政策適用於所有涉及軟體開發、測試、建置及部署之相關活動，包含公司內部人員、外包人員及合作夥伴於公司開發環境中之操作行為。

***

## **角色與責任（Roles and Responsibilities）**

| 角色        | 職責                            |
| --------- | ----------------------------- |
| 開發人員      | 遵循安全開發規範與套件管理要求，確保程式碼與依賴元件安全性 |
| 資訊處／資安單位  | 負責軟體審核、風險監控與資安事件處理            |
| 部門主管      | 督導政策執行及落實情形                   |
| ISMS 管理代表 | 負責政策維護、更新及對應內外部稽核             |

***

## **政策內容（Policy Statements）**

### **軟體與套件管理**

**控制要求**

* 開發所使用之軟體、工具、套件與服務須經資訊處審核並核准後方可使用
* 僅允許使用官方來源或經驗證之可信發布者
* 使用之軟體須具備安全政策與隱私保護聲明
* 優先選用具備國際資安認證之產品（如 ISO 27001、SOC 2）
* 未列入核准清單之軟體，須完成正式審查與風險評估程序

**ISO 控制對應**

* A.8.9 Configuration Management
* A.8.19 Installation of Software on Operational Systems
* A.5.9 Inventory of Information and Other Associated Assets
* A.5.31 Legal, Statutory, Regulatory and Contractual Requirements

***

### **開發依賴與安裝安全**

**控制要求**

* 所有專案須採用版本鎖定機制（Lock File）
* 禁止安裝未經驗證之最新版本套件
* 套件安裝過程不得出現異常行為（如非預期網路連線）
* 應使用安全安裝方式（例如 npm ci、pip lock）
* 預設應停用第三方自動執行腳本機制

**ISO 控制對應**

* A.8.28 Secure Coding
* A.8.25 Secure Development Lifecycle
* A.8.23 Web Filtering
* A.8.7 Protection Against Malware

***

### **敏感資料保護**

**控制要求**

* 禁止於本機儲存敏感資訊（如密碼、金鑰、憑證、Token）
* 憑證應透過安全機制管理（如環境變數或秘密管理系統）
* 應使用短效或動態憑證以降低風險
* 禁止使用本機預設憑證儲存路徑（如 `~ /.aws`、`~/.ssh` 等）

**ISO 控制對應**

* A.8.12 Data Leakage Prevention
* A.5.34 Privacy and Protection of PII
* A.8.2 Privileged Access Rights
* A.5.15 Access Control

***

### **開發環境隔離**

**控制要求**

* 開發作業須於隔離環境中進行（如 Container、VM 或 Sandbox）
* 禁止於宿主系統（Host）直接執行未經控管之程式
* 開發、測試與佈署環境須明確區隔

**ISO 控制對應**

* A.8.31 Separation of Development, Test and Production
* A.8.20 Network Security
* A.5.23 Information Security for Use of Cloud Services

***

### **安全測試與掃描**

**控制要求**

* 程式碼在 Commit 與 Pull Request 前須執行安全掃描
* 每次版本發布前須完成完整安全檢測
* CI/CD 流程應整合自動化安全檢查機制（如 SCA、Secret Scan）

**ISO 控制對應**

* A.8.29 Security Testing in Development
* A.8.8 Management of Technical Vulnerabilities
* A.8.16 Monitoring Activities

***

### **資安事件應變**

**控制要求**
當發現或懷疑開發環境遭受攻擊時，應立即執行以下措施：

* 停止使用受影響之系統或工具
* 立即隔離網路連線
* 通報主管及資訊處
* 撤銷所有存取憑證與 Token
* 更換相關帳號密碼與金鑰
* 重建乾淨執行環境
* 檢查是否影響 CI/CD、版本與產品發布

**ISO 控制對應**

* A.5.24 Information Security Incident Management Planning
* A.5.25 Assessment and Decision on Information Security Events
* A.5.26 Response to Information Security Incidents
* A.5.27 Learning from Information Security Incidents

***

### **安全認知與訓練**

**控制要求**

* 人員須具備供應鏈攻擊與安全開發之基本認知
* 應定期辦理資訊安全教育訓練
* 應強化對常見錯誤觀念之認知修正（如過度信任開源套件）

**ISO 控制對應**

* A.6.3 Information Security Awareness, Education and Training

***

## **控制要求與稽核證據**

### **安全控制與證據對應**

| 控制編號  | 控制項目        | 控制要求              | ISO 對應 | 稽核證據                | 責任單位   |
| ----- | ----------- | ----------------- | ------ | ------------------- | ------ |
| SC-01 | 軟體白名單管理     | 所有工具須經核准          | A.8.19 | 軟體清冊、核准紀錄           | 資訊處 / 開發單位   |
| SC-02 | 套件來源驗證      | 僅允許官方來源           | A.8.7  | SCA 掃描報告            | 開發單位   |
| SC-03 | 套件版本鎖定      | 使用 lock file      | A.8.25 | 原始碼版本庫              | 開發單位   |
| SC-04 | 安裝安全控制      | 禁止執行 scripts      | A.8.28 | 設定截圖或設定檔            | 開發單位   |
| SC-05 | 憑證保護        | 禁止本機存放            | A.8.12 | Secret 掃描報告         | 資訊處    |
| SC-06 | 環境隔離        | 使用 VM / Container | A.8.31 | 環境設定文件              | 開發單位   |
| SC-07 | 軟體成分分析（SCA） | 使用安全掃描工具          | A.8.29 | Snyk / BlackDuck 報告 | 開發單位     |
| SC-08 | CI/CD 安全控制  | 實施 security gate  | A.8.16 | Pipeline 紀錄         | 開發單位 |
| SC-09 | 事件應變        | 執行通報流程            | A.5.26 | 事件通報紀錄              | 資訊處    |
| SC-10 | 發布安全        | 發布前完成掃描           | A.8.8  | Release 安全報告        | 開發單位   |

***

## **風險說明（Risk Considerations）**

本政策主要因應以下資訊安全風險：

* 開源套件供應鏈攻擊
* 惡意第三方程式執行
* 憑證外洩及存取權限濫用
* CI/CD 流程遭植入惡意程式

***

## **稽核與遵循（Compliance and Audit）**

* 本政策納入公司資訊安全管理制度（ISMS）稽核範圍
* 各開發單位須提供以下稽核證據：
  * 套件來源與使用清單
  * 安全掃描與測試報告
* 若發現不符合事項，應依規定提出矯正與預防措施（CAPA）
* H15、資訊處與開發單位應定期執行抽查與監控

***

## **文件控管（Document Control）**

| 項目     | 說明           |
| ------ | ------------ |
| 文件擁有單位 | H15         |
| 審核單位   | H15 / 資訊處          |
| 核准單位   | 管理階層         |
| 審查週期   | 每年至少一次或重大變更時 |

***


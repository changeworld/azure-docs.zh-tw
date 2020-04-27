---
title: Azure 安全性效能評定藍圖範例控制項
description: Azure 安全性效能評定藍圖範例與 Azure 原則的控制項對應。
ms.date: 04/16/2020
ms.topic: sample
ms.openlocfilehash: 0f2d24d7d8c9f7ce2568f11b15e65ed1fcd02afb
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538660"
---
# <a name="control-mapping-of-the-azure-security-benchmark-blueprint-sample"></a>Azure 安全性效能評定藍圖範例的控制項對應

下列文章詳述 Azure 藍圖 Azure 安全性效能評定藍圖範例與 Azure 安全性效能評定控制項的對應。 如需關於控制的詳細資訊，請參閱 [Azure 安全性效能評定](https://docs.microsoft.com/azure/security/benchmarks/overview)。

以下是 **Azure 安全性效能評定**控制項的對應。 使用右側的導覽區可直接跳到特定的控制項對應。 對應的控制項是以 [Azure 原則](../../../policy/overview.md)方案進行實作的。 若要檢閱完整方案，請在 Azure 入口網站中開啟 [原則]  ，然後選取 [定義]  頁面。 然後，找出並選取 **\[預覽\]：稽核 Azure 安全性效能評定建議，以及部署特定的受支援 VM 延伸模組**內建原則方案。

> [!IMPORTANT]
> 下列每個控制措施都與一或多個 [Azure 原則](../../../policy/overview.md)定義相關聯。 這些原則可協助您使用工具[存取合規性](../../../policy/how-to/get-compliance-data.md)；不過，控制措施和一或多個原則之間，通常不是 1：1 或完整對應。 因此，Azure 原則中的**符合規範**只是指原則本身，這不保證您符合控制措施所有需求的規範。 此外，合規性標準可能目前未由任何 Azure 原則定義解決的控制措施。 因此，Azure 原則中的合規性只是整體合規性狀態的部分觀點。 此合規性藍圖範例的控制措施與 Azure 原則定義之間的關聯，可能會隨著時間而改變。 若要檢視變更歷程記錄，請參閱 [GitHub 認可歷程記錄](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/azure-security-benchmark/control-mapping.md) \(英文\)。

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1 在虛擬網路上使用網路安全性群組或 Azure 防火牆來保護資源

- 子網路應該與網路安全性群組建立關聯
- 應在網際網路對應虛擬機器中套用自適性網路強化建議
- 虛擬機器應該連線到已核准的虛擬網路
- 應使用網路安全性群組保護網際網路對應的虛擬機器
- 服務匯流排應該使用虛擬網路服務端點
- App Service 應該使用虛擬網路服務端點
- SQL Server 應該使用虛擬網路服務端點
- 事件中樞應該使用虛擬網路服務端點
- Cosmos DB 應該使用虛擬網路服務端點
- Key Vault 應該使用虛擬網路服務端點
- 稽核不受限制的儲存體帳戶網路存取
- 儲存體帳戶應該使用虛擬網路服務端點
- Container Registry 應該使用虛擬網路服務端點
- 虛擬網路應該使用指定的虛擬網路閘道
- Kubernetes Services 上應定義授權 IP 範圍
- \[預覽\]：應停用虛擬機器上的 IP 轉送
- 應使用網路安全性群組保護網際網路對應的虛擬機器
- Just-In-Time 網路存取控制應套用在虛擬機器上
- 應關閉虛擬機器上的管理連接埠

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2 監視和記錄 VNet、子網路與 NIC 的設定和流量

- 應啟用網路監看員

## <a name="13-protect-critical-web-applications"></a>1.3 保護重要的 Web 應用程式

- 確定 Web 應用程式已將 [用戶端憑證 (傳入用戶端憑證)] 設定為 [開啟]
- CORS 不應允許每項資源存取您的 Web 應用程式
- CORS 不應允許每項資源存取函式應用程式
- CORS 不應允許每項資源存取您的 API 應用程式
- 應關閉 Web 應用程式的遠端偵錯
- 函式應用程式的遠端偵錯應關閉
- 應關閉 API 應用程式的遠端偵錯

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4 拒絕與已知惡意 IP 位址的通訊

- 應啟用 DDoS 保護標準
- Just-In-Time 網路存取控制應套用在虛擬機器上
- 應在網際網路對應虛擬機器中套用自適性網路強化建議

## <a name="15-record-network-packets-and-flow-logs"></a>1.5 記錄網路封包和流量記錄

- 應啟用網路監看員

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11 使用自動化工具來監視網路資源設定並偵測變更

- 部署必要條件，以稽核 [安全性選項 - 網路存取] 中的 Windows VM 組態
- 顯示 [安全性選項 - Microsoft 網路用戶端] 中的 Windows VM 設定稽核結果
- 部署必要條件，以稽核 [安全性選項 - 網路安全性] 中的 Windows VM 設定
- 顯示 [安全性選項 - 網路安全性] 中的 Windows VM 設定稽核結果
- 部署必要條件，以稽核 [安全性選項 - Microsoft 網路伺服器] 中的 Windows VM 設定
- 顯示 [安全性選項 - Microsoft 網路伺服器] 中的 Windows VM 設定稽核結果
- 部署必要條件，以稽核 [系統管理範本 - 網路] 中的 Windows VM 設定
- 顯示 [系統管理範本 - 網路] 中的 Windows VM 設定稽核結果

## <a name="22-configure-central-security-log-management"></a>2.2 設定中央安全性記錄管理

- Log Analytics 代理程式應該安裝在虛擬機器上
- Log Analytics 代理程式應該安裝在虛擬機器擴展集上
- 部署必要條件，以稽核 Log Analytics 代理程式未如預期連線的 Windows VM
- 顯示 Log Analytics 代理程式未如預期連線的 Windows VM 稽核結果
- Azure 監視器記錄設定檔應收集 [寫入]、[刪除] 和 [動作] 分類的記錄
- Azure 監視器應從所有區域收集活動記錄
- 您的訂用帳戶上應啟用 Log Analytics 監視代理程式的自動化佈建

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3 啟用 Azure 資源的稽核記錄

- 應在 Azure Data Lake Store 中啟用診斷記錄
- 應在 Logic Apps 中啟用診斷記錄
- 應在 IoT 中樞內啟用診斷記錄
- 應啟用 Batch 帳戶中的診斷記錄
- 應在虛擬機器擴展集中啟用診斷記錄
- 應啟用事件中樞內的診斷記錄
- 應在搜尋服務中啟用診斷記錄
- 應在 App Service 中啟用診斷記錄
- 應在 Data Lake Analytics 中啟用診斷記錄
- 應啟用 Key Vault 中的診斷記錄
- 應在服務匯流排中啟用診斷記錄
- 應啟用 Azure 串流分析中的診斷記錄
- 應啟用 SQL 伺服器上的稽核
- 稽核診斷設定

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4 從作業系統收集安全性記錄

- 您的訂用帳戶上應啟用 Log Analytics 監視代理程式的自動化佈建
- Log Analytics 代理程式應該安裝在虛擬機器上
- Log Analytics 代理程式應該安裝在虛擬機器擴展集上
- 部署必要條件，以稽核 Log Analytics 代理程式未如預期連線的 Windows VM
- 顯示 Log Analytics 代理程式未如預期連線的 Windows VM 稽核結果

## <a name="27-enable-alerts-for-anomalous-activity"></a>2.7 啟用異常活動的警示

- 應選取資訊安全中心標準定價層
- 應在 SQL 伺服器上啟用進階資料安全性
- 應在 SQL 受控執行個體上啟用進階資料安全性
- 在 SQL 伺服器的進階資料安全性設定中，進階威脅防護類型應設定為 [全部]
- 在 SQL 受控執行個體的進階資料安全性設定中，進階威脅防護類型應設定為 [全部]

## <a name="28-centralize-anti-malware-logging"></a>2.8 集中化反惡意程式碼記錄

- Azure 的 Microsoft Antimalware 應設定為自動更新保護簽章
- 在 Azure 資訊安全中心中監視缺少的 Endpoint Protection
- 應在虛擬機器擴展集上安裝端點保護解決方案

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1 維護系統管理帳戶的詳細目錄

- 應針對您的訂用帳戶指定最多 3 位擁有者
- 應將一個以上的擁有者指派給您的訂用帳戶
- 具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除
- 具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除

## <a name="33-use-dedicated-administrative-accounts"></a>3.3 使用專用的系統管理帳戶

- 部署必要條件以稽核 Administrators 群組不只包含指定成員的 Windows VM
- 顯示其中 Administrators 群組不只包含指定成員的 Windows VM 稽核結果
- 部署必要條件以稽核其中 Administrators 群組不包含任何指定成員的 Windows VM
- 從其中 Administrators 群組不包含任何指定成員的 Windows VM 顯示稽核結果
- 部署必要條件，以稽核其中 Administrator 群組包含任何指定成員的 Windows VM
- 顯示其中 Administrators 群組包含了任一指定成員的 Windows VM 稽核結果
- 應針對您的訂用帳戶指定最多 3 位擁有者
- 應將一個以上的擁有者指派給您的訂用帳戶

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5 針對所有以 Azure Active Directory 為基礎的存取使用多重要素驗證

- 應在您訂用帳戶上具有擁有者權限的帳戶上啟用 MFA
- 應在您訂用帳戶上具有寫入權限的帳戶上啟用 MFA
- 應在您訂用帳戶上具有讀取權限的帳戶上啟用 MFA

## <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7 針對來自系統管理帳戶的可疑活動留下記錄和發出警示

- 應選取資訊安全中心標準定價層

## <a name="39-use-azure-active-directory"></a>3.9 使用 Azure Active Directory

- 應針對 SQL 伺服器佈建 Azure Active Directory 管理員
- Service Fabric 叢集應只能使用 Azure Active Directory 進行用戶端驗證
- 確定已在 API 應用程式上啟用 [向 Azure Active Directory 註冊]
- 確定已在 Web 應用程式上啟用 [向 Azure Active Directory 註冊]
- 確定已在函數應用程式上啟用 [向 Azure Active Directory 註冊]

## <a name="310-regularly-review-and-reconcile-user-access"></a>3.10 定期檢閱並協調使用者存取

- 已取代帳戶應該從您的訂用帳戶中移除
- 具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除
- 具有讀取權限的外部帳戶應該從您的訂用帳戶中移除
- 應從訂用帳戶移除具有寫入權限的外部帳戶
- 具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1 維護敏感性資訊的詳細目錄

- 應分類 SQL 資料庫中的敏感性資料

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4 加密所有傳輸中的敏感性資訊

- 應啟用儲存體帳戶的安全傳輸
- 您的 API 應用程式應使用最新的 TLS 版本
- 您的 Web 應用程式應使用最新的 TLS 版本
- 您的函式應用程式應使用最新的 TLS 版本
- 函式應用程式應只可經由 HTTPS 存取
- Web 應用程式應只可經由 HTTPS 存取
- API 應用程式應只可經由 HTTPS 存取
- 應為 MySQL 資料庫伺服器啟用 [強制執行 SSL 連線]
- 應為 PostgreSQL 資料庫伺服器啟用 [強制執行 SSL 連線]
- 應該只允許對 Redis Cache 的安全連線

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5 使用作用中探索工具來識別敏感性資料

- 應分類 SQL 資料庫中的敏感性資料
- 應在 SQL 伺服器上啟用進階資料安全性
- 應在 SQL 受控執行個體上啟用進階資料安全性

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6 使用 Azure RBAC 來控制資源的存取權

- 應在 Kubernetes 服務上使用角色型存取控制 (RBAC)
- 稽核自訂 RBAC 規則的使用方式

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8 加密待用的敏感性資訊

- 應在 SQL 資料庫上啟用透明資料加密
- 應在虛擬機器上套用磁碟加密
- 未連結的磁碟應加密
- SQL 伺服器的 TDE 保護裝置應以您自己的金鑰加密
- SQL 受控執行個體的 TDE 保護裝置應以您自己的金鑰加密
- 應加密自動化帳戶變數
- Service Fabric 叢集應將 ClusterProtectionLevel 屬性設定為 EncryptAndSign

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9 針對重要 Azure 資源的變更留下記錄和發出警示

- Azure 監視器應從所有區域收集活動記錄

## <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1 執行自動化弱點掃描工具

- 弱點評定應於您的 SQL 伺服器上啟用
- SQL 受控執行個體上應啟用弱點評定
- \[預覽\] 應在虛擬機器上啟用弱點評定
- SQL 伺服器的弱點評估設定應包含接收掃描報告的電子郵件地址

## <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2 部署自動化的作業系統修補程式管理解決方案

- 您應在機器上安裝系統更新
- 應在虛擬機器擴展集上安裝系統更新
- 確定 '.NET Framework' 在作為函式應用程式的一部分時，版本是最新的
- 確定 '.NET Framework' 在作為 Web 應用程式的一部分時，版本是最新的
- 確定 '.NET Framework' 在作為 API 應用程式的一部分時，版本是最新的

## <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3 部署自動化的第三方軟體修補程式管理解決方案

- 確定 'PHP' 在作為 API 應用程式的一部分時，版本是最新的
- 確定 'PHP' 在作為 Web 應用程式的一部分時，版本是最新的
- 確定 'PHP' 在作為函式應用程式的一部分時，版本是最新的
- 確定 'Java' 在作為 Web 應用程式的一部分時，版本是最新的
- 確定 'Java' 在作為函式應用程式的一部分時，版本是最新的
- 確定 'Java' 在作為 API 應用程式的一部分時，版本是最新的
- 確定 'Python' 在作為 Web 應用程式的一部分時，版本是最新的
- 確定 'Python' 在作為函式應用程式的一部分時，版本是最新的
- 確定 'Python' 在作為 API 應用程式的一部分時，版本是最新的
- Kubernetes 服務應升級為不易受攻擊的 Kubernetes 版本

## <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5 使用風險評等程序來排定所發現弱點的補救優先順序

- 弱點評量解決方案應修復弱點
- 您應在機器上修復安全性組態的弱點
- 應修復虛擬機器擴展集上安全性組態的弱點
- 應補救容器安全性設定中的弱點
- 應修復 SQL 資料庫的弱點

## <a name="68-use-only-approved-applications"></a>6.8 僅使用已核准的應用程式

- 應選取資訊安全中心標準定價層
- 應在虛擬機器上啟用自適性應用程式控制

## <a name="69-use-only-approved-azure-services"></a>6.9 僅使用已核准的 Azure 服務

- 虛擬機器應移轉到新的 Azure Resource Manager 資源
- 儲存體帳戶應移轉至新的 Azure Resource Manager 資源

## <a name="610-implement-approved-application-list"></a>6.10 實作已核准的應用程式清單

- 應選取資訊安全中心標準定價層
- 應在虛擬機器上啟用自適性應用程式控制

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3 維護安全的 Azure 資源設定

- \[預覽\]：Kubernetes Services 上應定義 Pod 安全性原則

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4 維護安全的作業系統設定

- 您應在機器上修復安全性組態的弱點
- 應補救容器安全性設定中的弱點
- 應修復虛擬機器擴展集上安全性組態的弱點

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9 為 Azure 服務實作自動化的設定監視

- \[預覽\]：Kubernetes Services 上應定義 Pod 安全性原則

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10 為作業系統實作自動化的設定監視

- 您應在機器上修復安全性組態的弱點
- 應補救容器安全性設定中的弱點
- 應修復虛擬機器擴展集上安全性組態的弱點

## <a name="711-manage-azure-secrets-securely"></a>7.11 安全地管理 Azure 秘密

- Key Vault 物件應該要可復原

## <a name="712-manage-identities-securely-and-automatically"></a>7.12 安全且自動地管理身分識別

- 函式應用程式應該使用受控識別
- Web 應用程式應該使用受控識別
- API 應用程式應該使用受控識別

## <a name="81-use-centrally-managed-anti-malware-software"></a>8.1 使用集中管理的反惡意程式碼軟體

- 在 Azure 資訊安全中心中監視缺少的 Endpoint Protection
- 應在虛擬機器擴展集上安裝端點保護解決方案

## <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2 預先掃描要上傳至非計算 Azure 資源的檔案

- 應選取資訊安全中心標準定價層

## <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3 確保會更新反惡意程式碼軟體和簽章

- Azure 的 Microsoft Antimalware 應設定為自動更新保護簽章

## <a name="91-ensure-regular-automated-back-ups"></a>9.1 確保會定期自動備份

- 應為 Azure SQL Database 啟用長期異地備援備份
- 應為適用於 MySQL 的 Azure 資料庫啟用異地備援備份
- 應為適用於 PostgreSQL 的 Azure 資料庫啟用異地備援備份
- 應為適用於 MariaDB 的 Azure 資料庫啟用異地備援備份
- 應該為虛擬機器啟用 Azure 備份

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2 執行完整的系統備份，並備份客戶管理的任何金鑰

- 應為 Azure SQL Database 啟用長期異地備援備份
- 應為適用於 MySQL 的 Azure 資料庫啟用異地備援備份
- 應為適用於 PostgreSQL 的 Azure 資料庫啟用異地備援備份
- 應為適用於 MariaDB 的 Azure 資料庫啟用異地備援備份
- 應該為虛擬機器啟用 Azure 備份

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4 確保備份和客戶管理金鑰受到保護

- Key Vault 物件應該要可復原

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2 建立事件評分和優先順序程序

- 應選取資訊安全中心標準定價層

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4 提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

- 應為您的訂用帳戶提供安全性連絡人電子郵件地址
- 應為您的訂用帳戶提供安全性連絡人電話號碼
- SQL 伺服器的進階資料安全性設定應包含用來接收安全性警示的電子郵件地址
- SQL 受控執行個體的進階資料安全性設定應包含用來接收安全性警示的電子郵件地址
- 應在 SQL 伺服器進階資料安全性設定中啟用傳給系統管理員和訂用帳戶擁有者的通知
- 應在 SQL 受控執行個體進階資料安全性設定中，啟用傳給系統管理員和訂用帳戶擁有者的電子郵件通知

## <a name="next-steps"></a>後續步驟

您已複習過 Azure 安全性效能評定藍圖的控制項對應，接下來請造訪 Azure 入口網站中的 Azure 原則來指派方案：

> [!div class="nextstepaction"]
> [Azure 原則](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)

有關藍圖及其使用方式的其他文件：

- 了解[藍圖生命週期](../../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../../concepts/resource-locking.md)。
- 了解如何[更新現有的指派](../../how-to/update-existing-assignments.md)。
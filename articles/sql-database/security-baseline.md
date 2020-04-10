---
title: Azure SQL 資料庫的 Azure 安全基線
description: Azure SQL 資料庫的 Azure 安全基線
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: d51cf22d7be167501927e54ce159e0b732209b0d
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011373"
---
# <a name="azure-security-baseline-for-azure-sql-database"></a>Azure SQL 資料庫的 Azure 安全基線

Azure SQL 資料庫的 Azure 安全基線包含有助於改進部署安全狀態的建議。

此服務的基線取自[Azure 安全基準版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview),它提供了有關如何使用最佳實務指南在 Azure 上保護雲端解決方案的建議。

有關詳細資訊,請參閱[Azure 安全基線概述](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>網路安全性

*有關詳細資訊,請參閱[安全控制:網路安全](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1:使用虛擬網路上的網路安全組或 Azure 防火牆保護資源

**指南**:您可以啟用 Azure 專用連結,以允許透過虛擬網路中的專用終結點存取 Azure PaaS 服務(例如 SQL 資料庫)和 Azure 託管的客戶/合作夥伴服務。 虛擬網路和服務間的流量會在通過 Microsoft 骨幹網路時隨之減少，降低資料在網際網路中公開的風險。 

若要允許流量觸達 Azure SQL Database，請使用 SQL服務標籤，允許透過網路安全性群組輸出流量。

虛擬網路規則使 Azure SQL 資料庫只能接受從虛擬網路中的選定子網發送的通信。

如何為 Azure SQL 資料庫設定專用連結:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

如何為資料庫伺服器使用虛擬網路服務終結點和規則:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: 監視和紀錄 Vnet、子網和 NIC 的配置和流量

**指南**:使用 Azure 安全中心並修正 Azure SQL 資料庫伺服器部署到的子網的網路保護建議。 

對於將連接到 Azure SQL 資料庫伺服器實例的 Azure 虛擬機器 (VM),為保護這些 VM 的 NSG 啟用網路安全組 (NSG) 流日誌,並將日誌發送到 Azure 儲存帳戶以進行流量審核。 

您還可以將 NSG 串流日誌發送到日誌分析工作區,並使用流量分析提供有關 Azure 雲中的流量流的見解。 流量分析的一些優點是能夠可視化網路活動並識別熱點、識別安全威脅、瞭解流量模式和查明網路錯誤配置。

如何啟用 NSG 串流紀錄:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

瞭解 Azure 安全中心提供的網路安全:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

如何啟用與使用流量分析:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

瞭解 Azure 安全中心提供的網路安全:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="13-protect-critical-web-applications"></a>1.3: 保護關鍵 Web 應用程式

**指導**:不適用;此建議適用於託管 Web 應用程式的 Azure 應用服務或計算資源。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 拒絕與已知惡意 IP 位址的通訊

**指南**:在與 SQL Server 實例關聯的虛擬網路上啟用 DDoS 保護標準,以保護免受分散式拒絕服務攻擊。 使用 Azure 安全中心整合威脅智慧拒絕與已知惡意或未使用的 Internet IP 位址的通訊。

如何設定 DDoS 保護:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

瞭解 Azure 安全中心整合威脅情報:

https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: 記錄網路封包與串流紀錄

**指南**:對於將連接到 Azure SQL 資料庫實例的 Azure 虛擬機器 (VM),為保護這些 VM 的 NSG 啟用網路安全組 (NSG) 串流日誌,並將日誌發送到 Azure 儲存帳戶以進行流量審核。 如果需要調查異常活動,則啟用網路觀察程序數據包捕獲。

如何啟用 NSG 串流紀錄:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

如何啟用網路觀察程式:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 部署基於網路的入侵偵測/入侵防禦系統(IDS/IPS)

**指南**:為 Azure SQL 資料庫啟用進階威脅保護 (ATP)。  一旦有可疑活動、潛在弱點、SQL 插入式攻擊以及異常的資料庫存取和查詢模式發生時，使用者就會收到警示。 高級威脅防護還將警報與 Azure 安全中心集成。 

瞭解並使用 Azure SQL 資料庫的高級威脅防護:https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview


**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 管理 Web 應用程式的流量

**指導**:不適用;此建議適用於託管 Web 應用程式的 Azure 應用服務或計算資源。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 最大限度降低網路安全規則的複雜性和管理開銷

**指南**:使用虛擬網路服務標記定義網路安全組或 Azure 防火牆上的網路存取控制項。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 通過在規則的相應源或目標欄位中指定服務標記名稱(例如 ApiManagement),可以允許或拒絕相應服務的流量。 Microsoft 管理服務標記包含的位址首碼,並在位址更改時自動更新服務標記。

為 Azure SQL 資料庫使用服務終結點時,需要向 Azure SQL 資料庫公共 IP 位址進行出站:必須向 Azure SQL 資料庫 IP 打開網路安全組 (NSG) 以允許連接。 可以通過使用 Azure SQL 資料庫的 NSG 服務標記來執行此操作。

使用 Azure SQL 資料庫的服務終結點瞭解服務標記:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations

瞭解並使用服務標記:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 維護網路設備的標準安全設定

**指南**:使用 Azure 策略定義和實現 Azure SQL 資料庫伺服器實例的網路安全配置。 您可以使用「Microsoft.Sql」命名空間來定義自訂策略定義,或使用為 Azure SQL 資料庫伺服器網路保護設計的任何內建策略定義。 Azure SQL 資料庫伺服器適用的內建網路安全策略的一個範例是:「SQL 伺服器應使用虛擬網路服務終結點」。。

 

使用 Azure 藍圖透過在單個藍圖定義中打包關鍵環境工件(如 Azure 資源管理範本、基於角色的存取控制 (RBAC) 和策略,簡化大規模 Azure 部署。 輕鬆將藍圖應用於新訂閱和環境,並通過版本控制微調控制和管理。

如何設定與管理 Azure 政策:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何建立 Azure 藍圖:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10: 文件流量設定規則

**指南**:對網路安全組 (NSG) 和其他與網路安全和流量流相關的資源使用標記。 對於單個 NSG 規則,使用「描述」欄位指定允許流量從網路傳輸的任何規則的業務需求和/或持續時間(等)。

使用與標記相關的任何內置 Azure 策略定義(如"要求標記及其值")確保所有資源都使用標記創建,並通知您現有的未標記資源。

您可以使用 Azure PowerShell 或 Azure CLI 查找資源或根據資源標記執行操作。

如何建立與使用標籤:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 使用自動工具監控網路資源設定並偵測變更

**指南**:使用 Azure 活動日誌監視網路資源配置,並檢測與 Azure SQL 資料庫伺服器實例相關的網路資源的更改。 在 Azure 監視器中創建警報,這些警報將在對關鍵網路資源的更改時觸發。

如何檢視和檢索 Azure 活動日誌事件:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

如何在 Azure 監視器中建立警報:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 安全中心監視**:不適用

**責任**: 客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*有關詳細資訊,請參閱[安全控制:日誌記錄和監視](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 使用批准的時間同步來源

**指南**:Microsoft 維護 Azure 資源的時間源。 您可以更新計算部署的時間同步。

如何為 Azure 計算資源設定時間同步:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Azure 安全中心監視**:不適用

**責任**: 微軟

### <a name="22-configure-central-security-log-management"></a>2.2: 配置中央安全日誌管理

**指南**:啟用 Azure SQL 資料庫的審核以追蹤資料庫事件並將其寫入 Azure 儲存帳戶、日誌分析工作區或事件中心中的審核日誌。

此外,您可以將 Azure SQL 診斷遙測流入 Azure SQL 分析,這是一個雲端解決方案,用於大規模和跨多個訂閱監控 Azure SQL 資料庫、彈性池和託管實例的性能。 可協助您收集 Azure SQL Database 效能計量，並以視覺效果方式呈現，而且有內建智慧可以執行效能疑難排解。

如何設定 Azure SQL 資料庫的審核:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

如何使用 Azure 監視器收集平台日誌和指標:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging

如何將診斷流入 Azure SQL 分析:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging#stream-into-azure-sql-analytics

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:為 Azure 資源啟用稽核紀錄記錄

**指導**:在 Azure SQL 資料庫伺服器實例上啟用審核,並為審核日誌(Azure 存儲、日誌分析或事件中心)選擇儲存位置。

如何為 Azure SQL 伺服器啟用稽核:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 從作業系統收集安全日誌

**指導**:不適用;此基準測試用於計算資源。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="25-configure-security-log-storage-retention"></a>2.5: 設定安全紀錄儲存保留

**指南**:在日誌分析工作區中存儲 Azure SQL 資料庫日誌時,請根據組織的合規性法規設置日誌保留期。

如何設定紀錄保留參數:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="26-monitor-and-review-logs"></a>2.6: 監視與檢視紀錄

**指導**:分析並監控日誌是否存在異常行為,並定期查看結果。 使用 Azure 安全中心的高級威脅保護來提醒與 Azure SQL 資料庫實例相關的異常活動。 或者,根據指標值或與 Azure SQL 資料庫實例相關的 Azure 活動日誌條目配置警報。

瞭解 Azure SQL 伺服器的進階威脅保護和警報:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

如何為 Azure SQL 資料庫設定自訂警報:

https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal?view=azps-1.4.0

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: 啟用例外活動的警示

**指南**:使用 Azure SQL 資料庫的 Azure 安全中心高級威脅保護來監視和警報異常活動。 為 SQL 資料庫啟用進階數據安全性。 高級數據安全包括用於發現和分類敏感數據、顯示和緩解潛在資料庫漏洞以及檢測可能指示資料庫受到威脅的異常活動的功能。

瞭解 Azure SQL 資料庫的進階威脅保護和警報:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

如何為 Azure SQL 資料庫啟用進階資料安全性:

https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security

如何管理 Azure 安全中心的警報:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8: 集中反惡意軟體紀錄記錄

**指導**:不適用;對於 Azure SQL 伺服器,反惡意軟體解決方案由 Microsoft 在基礎平臺上管理。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="29-enable-dns-query-logging"></a>2.9: 啟用 DNS 查詢紀錄記錄

**指導**:不適用;DNS 紀錄記錄不適用於 Azure SQL 伺服器。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="210-enable-command-line-audit-logging"></a>2.10: 啟用命令列稽核紀錄記錄

**指導**:不適用;命令列稽核不適用於 Azure SQL Server。


**Azure 安全中心監視**:不適用

**責任**:不適用

## <a name="identity-and-access-control"></a>身分識別與存取控制

*有關詳細資訊,請參閱[安全控制:識別和存取控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 維持管理帳戶的清單

**指南**: Azure 活動目錄 (AAD) 具有必須顯式分配且可查詢的內建角色。 使用 AAD PowerShell 模組執行臨時查詢以發現屬於管理組成員的帳戶。

如何使用 PowerShell 在 Azure AD 中取得目錄角色:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

如何使用 PowerShell 取得 Azure AD 中的目錄角色成員:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 在適用的情況下變更預設密碼

**指南**:Azure 活動目錄沒有預設密碼的概念。 預配 Azure SQL 資料庫實例時,建議選擇將身份驗證與 Azure 活動目錄整合。

如何使用 Azure SQL 設定與管理 Azure 活動目錄身份驗證:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 使用專用管理帳戶

**指導**:圍繞使用專用管理帳戶創建政策和程式。 使用 Azure 安全中心識別和訪問管理監視管理帳戶數。

瞭解 Azure 安全中心識別和存取:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: 使用 Azure 的目錄的單一登入 (SSO)

**指導**:不適用;雖然可以將 Azure 活動目錄身份驗證配置為與 Azure SQL 伺服器整合,但不支援單一登錄。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5:對所有基於 Azure 活動目錄的存取使用多重身份驗證

**指南**:啟用 Azure 活動目錄 (AAD) 多重身份驗證 (MFA),並遵循 Azure 安全中心標識和存取管理建議。

如何在 Azure 中啟用 MFA:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

如何在 Azure 安全中心內監視標識和訪問:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 對所有管理工作使用專用電腦 (特權存取工作站)

**指南**:使用特權訪問工作站 (PAW) 配置了多因素身份驗證 MFA 以登錄和配置 Azure 資源。

瞭解特權存取工作站:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

如何在 Azure 中啟用 MFA:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: 從管理帳戶記錄和警報可疑活動

**指南**:當環境中發生可疑或不安全活動時,請使用 Azure 活動目錄安全報告生成日誌和警報。

對 Azure SQL 資料庫使用進階威脅保護來檢測異常活動,指示存取或利用資料庫的異常和潛在有害嘗試。

如何辨識標記為有風險活動的 Azure AD 使用者:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

如何監視 Azure 安全中心中的使用者識別和訪問活動:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

檢視進階威脅防護和潛在警報:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview#advanced-threat-protection-alerts


**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:僅從已批准的位置管理 Azure 資源

**指南**:使用條件訪問命名位置允許門戶和 Azure 資源管理僅從 IP 位址範圍或國家/地區的特定邏輯分組進行訪問。

如何在 Azure 中設定命名位置:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="39-use-azure-active-directory"></a>3.9: 使用 Azure 的項目

**指導**:為 Azure SQL 資料庫伺服器實例創建 Azure 活動目錄 (AAD) 管理員。

如何使用 Azure SQL 設定與管理 Azure 活動目錄身份驗證:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

如何建立與設定 AAD 實體:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 定期審查和協調使用者存取

**指南**: Azure 活動目錄 (AAD) 提供日誌以幫助發現陳舊的帳戶。 此外,使用 Azure 標識存取審核可高效地管理組成員身份、對企業應用程式的訪問和角色分配。 可以定期查看使用者的訪問許可權,以確保只有正確的使用者才能繼續訪問。

如何使用 Azure 識別存取稽核:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 監視訪問已停用帳戶的嘗試

**指導**:使用 Azure SQL 設定 Azure 活動目錄 (AAD) 身份驗證,並為 Azure 活動目錄使用者帳戶創建診斷設定,將審核日誌和登錄日誌發送到日誌分析工作區。 在日誌分析工作區中配置所需的警報。

如何使用 Azure SQL 設定與管理 Azure 活動目錄身份驗證:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

如何將 Azure 活動紀錄整合到 Azure 監視器中:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 帳戶登錄行為偏差警報

**指南**:使用 Azure 活動目錄 (AAD) 標識保護和風險檢測配置自動回應,以檢測與使用者身份相關的可疑操作。 此外,您可以將數據引入 Azure Sentinel 以進行進一步調查。

如何檢視 Azure AD 風險登入:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

如何設定與啟用身份保護風險原則:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 在支援方案期間向 Microsoft 提供對相關客戶資料的存取

**指南**:在 Microsoft 需要存取客戶資料的支援方案中,Azure 客戶密碼箱提供了一個介面,供客戶查看和批准或拒絕客戶數據訪問請求。

瞭解客戶密碼箱:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Azure 安全中心監視**:不適用

**責任**: 客戶

## <a name="data-protection"></a>資料保護

*有關詳細資訊,請參閱[安全控制:資料保護](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 維護敏感資訊的清單

**指南**:使用標記來協助追蹤記憶體或處理敏感資訊的 Azure 資源。

如何建立與使用標籤:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 隔離記憶體或處理敏感資訊的系統

**指導**:為開發、測試和生產實施單獨的訂閱和/或管理組。 資源應由 Vnet/子網分隔,在 NSG 或 Azure 防火牆中正確標記和保護。 應隔離存儲或處理敏感數據的資源。 使用專用連結;在 Vnet 中部署 Azure SQL 伺服器,並使用專用終結點進行私下連接。

如何建立其他 Azure 訂閱:

https://docs.microsoft.com/azure/billing/billing-create-subscription

如何建立管理群組:

https://docs.microsoft.com/azure/governance/management-groups/create

如何建立與使用標籤:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

如何為 Azure SQL 資料庫設定專用連結:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 監控和阻止未經授權傳輸敏感資訊

**指南**:對於存儲或處理敏感資訊的 Azure SQL 資料庫,請使用標記將資料庫和相關資源標記為敏感。 將專用連結與 Azure SQL 資料庫實例上的網路安全組服務標記結合使用,以防止敏感資訊的滲漏。

對於由 Microsoft 管理的基礎平臺,Microsoft 將所有客戶內容視為敏感內容,並竭盡全力防止客戶數據丟失和暴露。 為了確保 Azure 中的客戶數據保持安全,Microsoft 已經實施並維護了一套強大的數據保護控制和功能。

如何配置私有鏈路和 NSG 以防止 Azure SQL 資料庫實例上的數據滲漏:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview

瞭解 Azure 的客戶資料保護:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:加密傳輸中的所有敏感資訊

**指南**:Azure SQL 資料庫通過使用傳輸層安全性加密正在啟動的數據來保護數據。 SQL Server 對所有連接的所有時間強制加密 (SSL/TLS)。 這可確保在用戶端和伺服器之間"傳輸"所有資料,而不考慮連接字串中的加密或 TrustServer 證書設置。

瞭解傳輸中的 Azure SQL 加密:

https://docs.microsoft.com/azure/sql-database/sql-database-security-overview#information-protection-and-encryption

**Azure 安全中心監視**:不適用

**責任**: 微軟

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 使用活動探索工具辨識敏感資料

**指導**:使用 Azure SQL 資料庫數據發現和分類功能。 數據發現和分類提供了 Azure SQL 資料庫中內建的高級功能,用於發現、&amp;分類和保護資料庫中的敏感數據。

如何使用 Azure SQL 伺服器的資料發現與分類:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: 使用 Azure RBAC 控制對資源的存取

**指南**:使用 Azure 活動目錄 (AAD) 對 Azure SQL 資料庫實例的訪問進行身份驗證和控制。

如何將 Azure SQL 伺服器與 Azure 活動目錄整合以進行身份驗證:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication

如何控制 Azure SQL 伺服器中的存取:

https://docs.microsoft.com/azure/sql-database/sql-database-control-access

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 使用基於主機的數據丟失防護來實施訪問控制

**指南**:Microsoft 管理 Azure SQL 資料庫的基礎基礎結構,並實施了嚴格的控制,以防止客戶數據丟失或暴露。

瞭解 Azure 的客戶資料保護:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 靜態加密敏感資訊

**指南**:透明資料加密 (TDE) 通過加密靜態數據,有助於保護 Azure SQL 資料庫、Azure SQL 託管實例和 Azure 數據倉儲免受惡意離線活動的威脅。 它會對資料庫、相關聯的備份和待用的交易記錄檔執行即時加密和解密，而不需變更應用程式。 根據預設，會為所有新部署的 Azure SQL 資料庫啟用 TDE。 TDE 加密金鑰可以由 Microsoft 或客戶管理。

如何管理透明資料加密並使用您自己的加密金鑰:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal#manage-transparent-data-encryption

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 對關鍵 Azure 資源的更改進行紀錄和警報

**指南**:將 Azure 監視器與 Azure 活動日誌一起用於創建對 Azure SQL 資料庫和其他關鍵或相關資源的生產實例進行更改時的警報。

如何為 Azure 活動紀錄事件建立警報:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Azure 安全中心監視**:是

**責任**: 客戶

## <a name="vulnerability-management"></a>弱點管理

*有關詳細資訊,請參閱[安全控制:漏洞管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 執行自動漏洞掃描工具

**指南**:為 Azure SQL 資料庫啟用進階資料安全性,並按照 Azure 安全中心的建議對 Azure SQL 伺服器執行漏洞評估。

如何在 Azure SQL 資料庫執行漏洞評估:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment

如何啟用進階資料安全:

https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security

如何實施 Azure 安全中心漏洞評估建議:

https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2:部署自動化作業系統修補程式管理解決方案

**指導**:不適用;此建議用於計算資源。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3:部署自動化的第三方軟體修補程式管理解決方案

**指導**:不適用;此基準測試用於計算資源。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 比較背對背漏洞掃描

**指南**:為 Azure SQL 資料庫實例啟用定期定期掃描;這將配置漏洞評估,以便每周在資料庫上自動運行一次掃描。 掃描結果摘要將傳送到您所提供的電子郵件地址。 比較結果以驗證漏洞是否已修復。

如何在 Azure 安全中心中匯出漏洞評估報告:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment#implementing-vulnerability-assessment

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 使用風險評級流程確定修復已發現漏洞的優先順序

**指南**:使用 Azure 安全中心提供的預設風險評級(安全分數)。

瞭解 Azure 安全中心安全分數:

https://docs.microsoft.com/azure/security-center/security-center-secure-score

**Azure 安全中心監視**:是

**責任**: 客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*有關詳細資訊,請參閱[安全控制:庫存和資產管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-use-azure-asset-discovery"></a>6.1: 使用 Azure 資產發現

**指導**:使用 Azure 資源圖查詢和發現訂閱中的所有資源(包括 Azure SQL Server 實例)。  確保租戶中具有適當的(讀取)許可權,並能夠枚舉訂閱中的所有 Azure 訂閱和資源。

儘管可以通過資源圖發現經典 Azure 資源,但強烈建議今後創建和使用 Azure 資源管理器資源。

如何使用 Azure 圖形建立查詢:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

如何檢視 Azure 訂閱:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

瞭解 Azure RBAC:https://docs.microsoft.com/azure/role-based-access-control/overview


**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="62-maintain-asset-metadata"></a>6.2: 維護資產元資料

**指南**:將標記應用於 Azure 資源,使元數據以邏輯方式將它們組織到分類中。

如何建立與使用標籤:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 刪除未經授權的 Azure 資源

**指南**:在適當情況下,使用標記、管理組和單獨的訂閱來組織和跟蹤資產。 定期協調庫存,確保及時從訂閱中刪除未經授權的資源。

如何建立其他 Azure 訂閱:

https://docs.microsoft.com/azure/billing/billing-create-subscription

如何建立管理群組:

https://docs.microsoft.com/azure/governance/management-groups/create

如何建立與使用標籤:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4:維護已批准的 Azure 資源和軟體標題的清單

**指南**:為計算資源定義已批准的 Azure 資源和已批准的軟體的清單

**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 監視未經批准的 Azure 資源

**指南**:使用 Azure 政策對客戶訂閱中可建立的資源類型使用以下內建策略定義進行限制:

- 不允許的資源類型

- 允許的資源類型

使用 Azure 資源圖查詢/發現訂閱中的資源。 確保環境中的所有 Azure 資源都獲得批准。

如何設定與管理 Azure 政策:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure 圖形建立查詢:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 監控計算資源中未經批准的軟體應用程式

**指導**:不適用;此建議用於計算資源。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 刪除未經批准的 Azure 資源和軟體應用程式

**指導**:不適用;此建議用於計算資源。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="68-use-only-approved-applications"></a>6.8: 僅使用已批准的應用程式

**指導**:不適用;此建議用於計算資源。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="69-use-only-approved-azure-services"></a>6.9: 僅使用已批准的 Azure 服務

**指南**:使用 Azure 策略對可在客戶訂閱中使用以下內建策略定義建立的資源類型施加限制:

- 不允許的資源類型

- 允許的資源類型

使用 Azure 資源圖查詢/發現訂閱中的資源。 確保環境中的所有 Azure 資源都獲得批准。

如何設定與管理 Azure 政策:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure 政策拒絕特定資源類型:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="610-implement-approved-application-list"></a>6.10: 實施已批准的申請清單

**指導**:不適用;此建議適用於在計算資源上運行的應用程式。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11:限制使用者透過文稿與 Azure 資源管理員互動的能力

**指南**:使用 Azure 條件訪問通過為"Microsoft Azure 管理"應用配置"阻止存取"來限制使用者與 Azure 資源管理員互動的能力。

如何設定條件存取以封鎖對 Azure 資源管理員的存取:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 限制使用者在計算資源中執行文稿的能力

**指導**:不適用;此建議用於計算資源。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 物理或邏輯上隔離高風險應用程式

**指導**:不適用;此建議適用於託管桌面或 Web 應用程式的應用服務或計算資源。


**Azure 安全中心監視**:不適用

**責任**:不適用

## <a name="secure-configuration"></a>安全設定

*有關詳細資訊,請參閱[安全控制:安全設定](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:為所有 Azure 資源建立安全配置

**指南**:使用 Azure SQL 伺服器/資料庫的 Azure 策略或 Azure 安全中心建議來維護所有 Azure 資源的安全配置。

如何設定與管理 Azure 政策:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: 建立安全的作業系統設定

**指導**:不適用;此建議用於計算資源。

**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:維護安全的 Azure 資源配置

**指導**:使用 Azure 策略 [拒絕] 和 [部署(如果不存在))在整個 Azure 資源中強制實施安全設置。

如何設定與管理 Azure 政策:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

瞭解 Azure 政策效果:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 維護安全的作業系統設定

**指導**:不適用;此建議用於計算資源。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: 安全儲存 Azure 資源的設定

**指南**:如果使用自定義 Azure 策略定義,請使用 Azure DevOps 或 Azure 儲存庫安全地存儲和管理代碼。

如何在 Azure DevOps 中儲存代碼:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure 儲存函式庫文件:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 安全地儲存自訂作業系統映像

**指導**:不適用;此建議用於計算資源。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: 部署系統設定管理工具

**指南**:使用「Microsoft.SQL」命名空間中的 Azure 策略別名創建自訂策略以警報、審核和強制執行系統配置。 此外,開發用於管理策略異常的流程和管道。

如何設定與管理 Azure 政策:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8:為作業系統部署系統設定管理工具

**指導**:不適用;此建議用於計算資源。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9:為 Azure 服務實現自動化配置監視

**指南**: 利用 Azure 安全中心對 Azure SQL 伺服器和資料庫執行基線掃描。

如何修復 Azure 安全中心中的建議:

https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 對作業系統執行自動設定監控

**指導**:不適用;此建議用於計算資源。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="711-manage-azure-secrets-securely"></a>7.11: 安全管理 Azure 機密

**指導**:使用 Azure 金鑰保管庫為 Azure SQL 資料庫透明資料加密 (TDE) 儲存加密金鑰。

如何保護儲存在 Azure SQL Server 中的敏感資料,並將加密金鑰儲存在 Azure 金鑰保管庫中:

https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 安全、自動地管理身份

**指導**:使用託管標識在 Azure 活動目錄 (AAD) 中為 Azure 服務提供自動託管標識。 託管標識允許您對支援 AAD 身份驗證的任何服務(包括 Azure 密鑰保管庫)進行身份驗證,而無需在代碼中進行任何認證。

教學:使用 Windows VM 系統分配的託管識別存取 Azure SQL:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql

如何設定託管識別:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 消除意外的認證

**指南**:實現憑據掃描程式以標識代碼中的憑據。 憑據掃描程式還將鼓勵將發現的憑據移動到更安全的位置,如 Azure 密鑰保管庫。 

如何設定認證程式:https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure 安全中心監視**:不適用

**責任**: 客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*有關詳細資訊,請參閱[安全控制:惡意軟體防禦](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: 使用集中管理的反惡意軟體

**指導**:不適用;此建議用於計算資源。 Microsoft 處理底層平臺的反惡意軟體。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:要上傳到非計算 Azure 資源的預入檔案

**指南**:在支援 Azure 服務的基礎主機上啟用 Microsoft 反惡意軟體(例如,Azure 應用服務),但它不在客戶內容上運行。

預掃描上載到非計算 Azure 資源的任何內容,如應用服務、資料湖存儲、Blob 存儲、Azure SQL 伺服器等。在這種情況下,Microsoft 無法訪問您的數據。

瞭解適用於 Azure 雲端服務和虛擬機器的 Microsoft 反惡意軟體:https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: 確保更新反惡意軟體和簽名

**指導**:不適用;此建議用於計算資源。 Microsoft 處理底層平臺的反惡意軟體。


**Azure 安全中心監視**:不適用

**責任**:不適用

## <a name="data-recovery"></a>資料復原

*有關詳細資訊,請參閱[安全控制:資料恢復](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 確保定期自動備份

**指南**:為了保護您的業務免受數據丟失,Azure SQL 資料庫每周自動創建完整的資料庫備份,每 12 小時創建一次差異資料庫備份,每 5 - 10 分鐘創建一次事務日誌備份。 備份存儲在 RA-GRS 存儲中,所有服務層至少存儲 7 天。 除基本支援外,所有服務層都可配置時間點還原的備份保留期,最多 35 天。

為了滿足不同的合規性要求,您可以選擇不同的每周、每月和/或每年備份的保留期。 儲存體耗用量取決於選取的備份頻率和保留期間。

使用 Azure SQL Server 瞭解備份和業務連續性:

https://docs.microsoft.com/azure/sql-database/sql-database-business-continuity

**Azure 安全中心監視**:是

**責任**: 共用

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 執行完整的系統備份並備份任何客戶託管金鑰

**指南**:Azure SQL 資料庫會自動創建保存 7 到 35 天的資料庫備份,並使用 Azure 讀取存取異地冗餘存儲 (RA-GRS) 來確保即使資料中心不可用,它們也會保留。 這些備份是自動創建的。 如果需要,請為 Azure SQL 資料庫啟用長期異地冗餘備份。

如果使用客戶管理的金鑰進行透明資料加密,請確保正在備份密鑰。

瞭解 Azure SQL Server 中的備份:

https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups?tabs=single-database

如何在 Azure 中備份金鑰保管庫金鑰:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:驗證所有備份,包括客戶託管金鑰

**指南**:確保能夠在 Azure 備份中定期執行內容的數據還原。 如有必要,測試將內容還原到隔離的 VLAN。 測試備份的客戶管理金鑰的恢復。

如何在 Azure 中還原金鑰保管庫金鑰:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

如何使用時間點還原復原 Azure SQL 資料庫備份:

https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#point-in-time-restore

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4:確保保護備份和客戶管理金鑰

**指南**:在 Azure 密鑰保管庫中啟用軟刪除,以保護密鑰免受意外或惡意刪除。

如何在金鑰保存庫中開啟軟刪除:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Azure 安全中心監視**:是

**責任**: 客戶

## <a name="incident-response"></a>事件回應

*有關詳細資訊,請參閱[安全控制:事件回應](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-an-incident-response-guide"></a>10.1:建立事件回應指南

**指導**:確保有書面的事件回應計劃,定義人員的角色以及事件處理/管理的各個階段。

如何在 Azure 安全中心內配置工作流自動化:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 建立事件評分與優先順序處理程序

**指南**:安全中心為警報分配嚴重性,以説明您確定處理每個警報的順序,以便在資源受到威脅時,您可以馬上到達它。 嚴重性基於安全中心在查找中的信心程度或用於發出警報的分析,以及導致警報的活動背後存在惡意的置信度。

Azure 安全中心中的安全警報:https://docs.microsoft.com/azure/security-center/security-center-alerts-overview



**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="103-test-security-response-procedures"></a>10.3: 測試安全回應程式

**指導**:進行練習,以常規節奏測試系統的事件回應能力。 找出薄弱環節和差距,並根據需要修訂計劃。

您可以參考 NIST 的出版物:IT 計劃和功能測試、培訓和鍛煉計劃指南:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:提供安全事件聯繫人詳細資訊,並配置安全事件的警報通知

**指南**:如果 Microsoft 安全回應中心 (MSRC) 發現您的數據已被非法或未經授權的方訪問,Microsoft 將使用安全事件聯繫資訊與您聯繫。

如何設置 Azure 安全中心安全連絡人:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 將安全警報納入事件回應系統

**指南**:使用「連續匯出」功能匯出 Azure 安全中心警報和建議。 "連續匯出"允許您手動或持續、持續的方式匯出警報和建議。 您可以使用 Azure 安全中心數據連接器將警報流式傳輸到 Sentinel。

如何設定連續匯出:

https://docs.microsoft.com/azure/security-center/continuous-export

如何將警報流式傳輸到 Azure 哨兵:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 自動回應安全警報

**指南**:使用 Azure 安全中心的工作流自動化功能,通過安全警報和建議上的「邏輯應用」自動觸發回應。

如何設定工作流自動化和邏輯應用:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*有關詳細資訊,請參閱[安全控制:滲透測試和紅隊練習](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1:對 Azure 資源進行定期滲透測試,並確保在 60 天內補救所有關鍵安全發現

**指南**:請遵循 Microsoft 的接用規則,以確保您的滲透測試不違反 Microsoft 策略:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

您可以在此處找到有關 Microsoft 針對 Microsoft 託管雲基礎架構、服務和應用程式進行紅色團隊和即時網站滲透測試的策略和實施的詳細資訊:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure 安全中心監視**:不適用

**責任**: 共用

## <a name="next-steps"></a>後續步驟

- 請參閱[Azure 安全基準](https://docs.microsoft.com/azure/security/benchmarks/overview)
- 瞭解有關[Azure 安全基線的更多資訊](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)

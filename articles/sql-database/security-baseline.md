---
title: 適用于 Azure SQL Database 的 Azure 安全性基準
description: 適用于 Azure SQL Database 的 Azure 安全性基準
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: ca8d0daf5b6d9bbad0d8fa24b4b150c5e6cd6b73
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78300905"
---
# <a name="azure-security-baseline-for-azure-sql-database"></a>適用于 Azure SQL Database 的 Azure 安全性基準

適用于 Azure SQL Database 的 Azure 安全性基準包含可協助您改善部署之安全性狀態的建議。

此服務的基準取自[Azure 安全性基準測試版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)，其中提供有關如何在 Azure 上使用最佳作法指引來保護雲端解決方案的建議。

如需詳細資訊，請參閱[Azure 安全性基準總覽](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱[安全性控制：網路安全性](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：在您的虛擬網路上使用網路安全性群組或 Azure 防火牆來保護資源

**指引**：您可以啟用 azure 私用連結，以允許透過虛擬網路中的私人端點存取 Azure PaaS 服務（例如 SQL Database）和 azure 託管的客戶/合作夥伴服務。 虛擬網路和服務間的流量會在通過 Microsoft 骨幹網路時隨之減少，降低資料在網際網路中公開的風險。 若要允許流量觸達 Azure SQL Database，請使用 SQL 服務標籤，以允許透過網路安全性群組的輸出流量。


虛擬網路規則可讓 Azure SQL Database 只接受虛擬網路內所選子網所傳送的通訊。


如何設定 Azure SQL Database 的私人連結：

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database


如何使用資料庫伺服器的虛擬網路服務端點和規則：

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2：監視並記錄 Vnet、子網和 Nic 的設定和流量

**指導**方針：使用 Azure 資訊安全中心並修復您的 Azure SQL Database 伺服器所部署之子網的網路保護建議。 針對將連接到您 Azure SQL Database 伺服器實例的 Azure 虛擬機器（VM），請啟用 Nsg 保護這些 Vm 的網路安全性群組（NSG）流量記錄，並將記錄傳送到 Azure 儲存體帳戶以進行流量審核。 您也可以將 NSG 流量記錄傳送到 Log Analytics 工作區，並使用「分析」來提供 Azure 雲端中流量的深入解析。 流量分析的一些優點是能夠將網路活動視覺化，並識別作用點、識別安全性威脅、瞭解流量模式，以及找出網路錯誤配置。


如何啟用 NSG 流量記錄：

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


瞭解 Azure 資訊安全中心所提供的網路安全性：

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations


如何啟用和使用流量分析：

https://docs.microsoft.com/azure/network-watcher/traffic-analytics


瞭解 Azure 資訊安全中心所提供的網路安全性：

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 web 應用程式

**指導**方針：不適用;這項建議適用于裝載 web 應用程式的 Azure 應用程式服務或計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址的通訊

**指引**：在與您的 SQL Server 實例相關聯的虛擬網路上啟用 DDoS 保護標準，以防止分散式阻斷服務攻擊。 使用 Azure 資訊安全中心整合式威脅情報來拒絕與已知惡意或未使用的網際網路 IP 位址的通訊。


如何設定 DDoS 保護：

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection


瞭解 Azure 資訊安全中心整合式威脅情報：

https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="15-record-network-packets-and-flow-logs"></a>1.5：記錄網路封包和流量記錄

**指引**：若要連線到您的 Azure SQL Database 實例的 Azure 虛擬機器（vm），請啟用 nsg 保護這些 vm 的網路安全性群組（NSG）流量記錄，並將記錄傳送到 Azure 儲存體帳戶以進行流量審核。 如果需要調查異常活動，請啟用網路監看員封包捕捉。


如何啟用 NSG 流量記錄：

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


如何啟用網路監看員：

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署以網路為基礎的入侵偵測/入侵預防系統（IDS/IPS）

**指導**方針：為 Azure SQL Database 啟用先進的威脅防護（ATP）。  一旦有可疑活動、潛在弱點、SQL 插入式攻擊以及異常的資料庫存取和查詢模式發生時，使用者就會收到警示。 Advanced 威脅防護也會整合警示與 Azure 資訊安全中心。

瞭解和使用適用于 Azure SQL Database 的先進威脅防護： https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理 web 應用程式的流量

**指導**方針：不適用;這項建議適用于裝載 web 應用程式的 Azure 應用程式服務或計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負擔降至最低

**指導**方針：使用虛擬網路服務標籤來定義網路安全性群組或 Azure 防火牆上的網路存取控制。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 藉由在規則的適當 [來源] 或 [目的地] 欄位中指定服務標籤名稱（例如，ApiManagement），您可以允許或拒絕對應服務的流量。 Microsoft 會管理服務標籤所包含的位址前置詞，並隨著位址變更自動更新服務標記。


使用 Azure SQL Database 的服務端點時，需要輸出至 Azure SQL Database 的公用 IP 位址：必須開啟網路安全性群組（Nsg），才能 Azure SQL Database Ip 以允許連線。 您可以使用 Azure SQL Database 的 NSG 服務標記來執行這項操作。


瞭解 Azure SQL Database 服務端點的服務標記：

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations


瞭解和使用服務標記：

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指導**方針：使用 Azure 原則為您的 Azure SQL Database 伺服器實例定義和執行網路安全性設定。 您可以使用 "Microsoft .Sql" 命名空間來定義自訂原則定義，或使用針對 Azure SQL Database server 網路保護所設計的任何內建原則定義。 Azure SQL Database 伺服器適用的內建網路安全性原則範例如下：「SQL Server 應該使用虛擬網路服務端點」。
 

使用 Azure 藍圖，藉由在單一藍圖定義中封裝金鑰環境成品（例如 Azure 資源管理範本、角色型存取控制（RBAC）和原則），以簡化大規模的 Azure 部署。 輕鬆地將藍圖套用至新的訂用帳戶和環境，並透過版本控制微調控制和管理。


如何設定和管理 Azure 原則： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


如何建立 Azure 藍圖： https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：檔流量設定規則

**指導**方針：使用網路安全性群組（NSG）的標記，以及與網路安全性和流量相關的其他資源。 針對個別的 NSG 規則，請使用 [描述] 欄位來指定允許進出網路流量的任何規則的商務需求和（或）持續時間（等）。


使用與標記相關的任何內建 Azure 原則定義，例如「需要標記和其值」，以確保所有資源都是以標籤建立，並通知您現有的未標記資源。


您可以使用 Azure PowerShell 或 Azure CLI，根據其標記來查閱或執行資源的動作。


如何建立和使用標記：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導**方針：使用 Azure 活動記錄來監視網路資源設定，以及偵測與您的 Azure SQL Database 伺服器實例相關的網路資源變更。 在 Azure 監視器中建立警示，以在重大網路資源的變更發生時觸發。


如何查看和取出 Azure 活動記錄事件：

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view


如何在 Azure 監視器中建立警示：

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱[安全性控制：記錄和監視](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源

**指引**： Microsoft 會維護 Azure 資源的時間來源。 您可以更新計算部署的時間同步處理。



如何設定 Azure 計算資源的時間同步處理：

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Azure 資訊安全中心監視**：不適用

**責任**： Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指引**：啟用 Azure SQL Database 的審核來追蹤資料庫事件，並將它們寫入您 Azure 儲存體帳戶、log Analytics 工作區或事件中樞中的 audit 記錄。


此外，您可以將 Azure SQL 診斷遙測串流至 Azure SQL 分析，這是一種雲端解決方案，可大規模監視 Azure SQL 資料庫、彈性集區和受控實例的效能，以及跨多個訂用帳戶。 可協助您收集 Azure SQL Database 效能計量，並以視覺效果方式呈現，而且有內建智慧可以執行效能疑難排解。


如何設定 Azure SQL Database 的審核：

https://docs.microsoft.com/azure/sql-database/sql-database-auditing


如何使用 Azure 監視器收集平臺記錄和計量：

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging


如何將診斷串流至 Azure SQL 分析：

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging#stream-diagnostic-telemetry-into-sql-analytics

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的審核記錄

**指引**：在您的 Azure SQL Database 伺服器實例上啟用審核，並為 audit 記錄（Azure 儲存體、Log Analytics 或事件中樞）選擇儲存位置。


如何啟用 Azure SQL Server 的審核：

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：從作業系統收集安全性記錄

**指導**方針：不適用;此基準測試適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留

**指導**方針：將您的 Azure SQL Database 記錄儲存在 Log Analytics 工作區時，請根據貴組織的合規性法規來設定記錄保留期限。



如何設定記錄檔保留參數：

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和審核記錄

**指引**：分析和監視異常行為的記錄，並定期審查結果。 使用 Azure 資訊安全中心的先進威脅防護，針對與您的 Azure SQL Database 實例相關的異常活動發出警示。 或者，根據計量值或與您 Azure SQL Database 實例相關的 Azure 活動記錄專案來設定警示。


瞭解 Azure SQL Server 的先進威脅防護和警示：

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview


如何設定 Azure SQL Database 的自訂警示：

https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal?view=azps-1.4.0

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：啟用異常活動的警示

**指導**方針：使用適用于 Azure SQL database 的 Azure 資訊安全中心 Advanced 威脅防護，來監視及警示異常活動。 為您的 SQL 資料庫啟用 Advanced Data Security。 Advanced Data Security 包含探索和分類敏感性資料、呈現和減輕潛在資料庫弱點，以及偵測可能表示對資料庫有威脅之異常活動的功能。



瞭解 Azure SQL Database 的先進威脅防護和警示：

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview



如何啟用 Azure SQL Database 的先進資料安全性：

https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security



如何管理 Azure 資訊安全中心中的警示：

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中反惡意程式碼記錄

**指導**方針：不適用;針對 Azure SQL Server，反惡意程式碼解決方案是由 Microsoft 在基礎平臺上管理。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄

**指導**方針：不適用;DNS 記錄不適用於 Azure SQL Server。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列審核記錄

**指導**方針：不適用;命令列審核不適用於 Azure SQL Server。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱[安全性控制：身分識別和存取控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的清查

**指導**方針： AZURE ACTIVE DIRECTORY （AAD）具有必須明確指派且可查詢的內建角色。 使用 AAD PowerShell 模組來執行臨機操作查詢，以探索屬於系統管理群組成員的帳戶。


如何使用 PowerShell 在 Azure AD 中取得目錄角色：

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0


如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員：

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：變更預設密碼（若適用）

**指導**方針： Azure Active Directory 沒有預設密碼的概念。 布建 Azure SQL Database 實例時，建議您選擇整合驗證與 Azure Active Directory。


如何使用 Azure SQL 設定和管理 Azure Active Directory 驗證：

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指導**方針：建立使用專用系統管理帳戶的原則和程式。 使用 Azure 資訊安全中心的身分識別與存取管理來監視系統管理帳戶的數目。



瞭解 Azure 資訊安全中心身分識別和存取：

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：搭配 Azure Active Directory 使用單一登入（SSO）

**指導**方針：不適用;雖然您可以設定 Azure Active Directory Authentication 與 Azure SQL Server 整合，但不支援單一登入。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：針對所有以 Azure Active Directory 為基礎的存取使用多重要素驗證

**指引**：啟用 AZURE ACTIVE DIRECTORY （AAD）多重要素驗證（MFA），並遵循 Azure 資訊安全中心身分識別和存取管理建議。


如何在 Azure 中啟用 MFA：

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


如何監視 Azure 資訊安全中心內的身分識別和存取：

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦（特殊許可權存取工作站）進行所有系統管理工作

**指導**方針：搭配使用特殊許可權存取工作站（PAW）與多重要素驗證 MFA （設定為登入和設定 Azure 資源）。


瞭解特殊許可權存取工作站：

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations


如何在 Azure 中啟用 MFA：

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7：從系統管理客戶紀錄和警示可疑活動

**指導**方針：當環境中發生可疑或不安全的活動時，請使用 Azure Active Directory 的安全性報告來產生記錄檔和警示。



針對 Azure SQL Database 使用 Advanced 威脅防護來偵測異常活動，指出不尋常且可能有害的嘗試存取或惡意探索資料庫。



如何識別標示有風險活動 Azure AD 使用者：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk



如何在 Azure 資訊安全中心中監視使用者的身分識別和存取活動：

https://docs.microsoft.com/azure/security-center/security-center-identity-access



審查先進的威脅防護和可能的警示：

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview#advanced-threat-protection-alerts


**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指導**方針：使用名為「位置」的條件式存取，只允許來自 IP 位址範圍或國家/地區特定邏輯群組的入口網站和 Azure 資源管理存取權。


如何在 Azure 中設定命名位置： https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導**方針：為您的 Azure SQL Database 伺服器實例建立 AZURE ACTIVE DIRECTORY （AAD）系統管理員。


如何使用 Azure SQL 設定和管理 Azure Active Directory 驗證：

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure


如何建立和設定 AAD 實例：

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期審查並協調使用者存取

**指導**方針： AZURE ACTIVE DIRECTORY （AAD）提供記錄檔，以協助探索過時的帳戶。 此外，您可以使用 Azure 身分識別存取審查來有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 使用者的存取權可以定期審查，以確保只有適當的使用者才能繼續存取。


如何使用 Azure 身分識別存取權審查：

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11：監視嘗試存取停用的帳戶

**指導**方針：使用 Azure SQL 設定 AZURE ACTIVE DIRECTORY （AAD）驗證，並建立 Azure Active Directory 使用者帳戶的診斷設定，並將審核記錄和登入記錄傳送到 Log Analytics 工作區。 在 Log Analytics 工作區中設定所需的警示。


如何使用 Azure SQL 設定和管理 Azure Active Directory 驗證：

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure


如何將 Azure 活動記錄整合到 Azure 監視器：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帳戶登入行為偏差的警示

**指導**方針：使用 AZURE ACTIVE DIRECTORY （AAD）身分識別保護和風險偵測，為偵測到與使用者身分識別相關的可疑動作設定自動回應。 此外，您可以將資料內嵌到 Azure Sentinel 中，以供進一步調查。


如何查看 Azure AD 的風險登入：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins


如何設定和啟用身分識別保護風險原則：

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：提供 Microsoft 在支援案例期間存取相關的客戶資料

**指引**：在 Microsoft 需要存取客戶資料的支援案例中，Azure 客戶加密箱會提供介面供客戶審查及核准或拒絕客戶資料存取要求。


瞭解客戶加密箱：

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱[安全性控制：資料保護](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護機密資訊的清查

**指引**：使用標記來協助追蹤儲存或處理敏感資訊的 Azure 資源。


如何建立和使用標記：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感資訊的系統

**指引**：在開發、測試和生產環境中，執行不同的訂用帳戶和/或管理群組。 資源應該以 Vnet/子網分隔、在 NSG 或 Azure 防火牆內適當地加以標記，並受到保護。 儲存或處理敏感性資料的資源應加以隔離。 使用私用連結;在您的 Vnet 內部部署 Azure SQL Server，並使用私用端點私下連接。



如何建立額外的 Azure 訂用帳戶：

https://docs.microsoft.com/azure/billing/billing-create-subscription



如何建立管理群組：

https://docs.microsoft.com/azure/governance/management-groups/create



如何建立和使用標記：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



如何設定 Azure SQL Database 的私人連結：

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的機密資訊傳輸

**指引**：針對儲存或處理敏感資訊的 Azure SQL 資料庫，請使用標記將資料庫和相關資源標示為機密。 將私人連結與 Azure SQL Database 實例上的網路安全性群組服務標籤搭配，以防止機密資訊的外泄。



針對由 Microsoft 管理的基礎平臺，Microsoft 會將所有客戶內容視為機密，並移至絕佳的長度，以防範客戶資料遺失和暴露。 為了確保 Azure 中的客戶資料保持安全，Microsoft 已實行並維護一套強大的資料保護控制和功能。



如何設定私用連結和 Nsg，以防止在 Azure SQL Database 實例上外泄資料：

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview



瞭解 Azure 中的客戶資料保護：

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有機密資訊

**指導**方針： Azure SQL Database 使用傳輸層安全性來加密移動中的資料，以保護您的資料。 SQL Server 會隨時對所有連線強制執行加密（SSL/TLS）。 這可確保所有資料都會在用戶端與伺服器之間「傳輸中」加密，而不論連接字串中的 [加密] 或 [TrustServerCertificate] 設定。



瞭解傳輸中的 Azure SQL 加密：

https://docs.microsoft.com/azure/sql-database/sql-database-security-overview#information-protection-and-encryption

**Azure 資訊安全中心監視**：不適用

**責任**： Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用 active discovery 工具來識別敏感性資料

**指引**：使用 Azure SQL Database 資料探索和分類功能。 資料探索和分類提供了內建于 Azure SQL Database 的先進功能，可用於探索、分類、標記 &amp; 保護資料庫中的敏感性資料。



如何使用 Azure SQL Server 的資料探索與分類：

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 來控制資源的存取權

**指引**：使用 AZURE ACTIVE DIRECTORY （AAD）來驗證和控制 Azure SQL Database 實例的存取權。


如何整合 Azure SQL Server 與 Azure Active Directory 以進行驗證：

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication


如何在 Azure SQL Server 中控制存取：

https://docs.microsoft.com/azure/sql-database/sql-database-control-access

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用以主機為基礎的資料遺失防護來強制存取控制

**指導**方針： Microsoft 會管理 Azure SQL Database 的基礎結構，並已實行嚴格的控制，以避免遺失或公開客戶資料。

瞭解 Azure 中的客戶資料保護：

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密機密資訊待用

**指引**：透明資料加密（TDE）可協助保護 Azure SQL Database、azure SQL 受控實例和 Azure 資料倉儲，使其免于透過加密待用資料的惡意離線活動威脅。 它會對資料庫、相關聯的備份和待用的交易記錄檔執行即時加密和解密，而不需變更應用程式。 根據預設，會為所有新部署的 Azure SQL 資料庫啟用 TDE。 TDE 加密金鑰可由 Microsoft 或客戶管理。


如何管理透明資料加密，並使用您自己的加密金鑰：

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal#manage-transparent-data-encryption

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：對重要 Azure 資源的變更進行記錄和警示

**指導**方針：使用 Azure 監視器搭配 Azure 活動記錄，以針對 Azure SQL 資料庫的生產實例和其他重要或相關資源進行變更時，建立警示。


如何建立 Azure 活動記錄事件的警示：

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="vulnerability-management"></a>弱點管理

*如需詳細資訊，請參閱[安全性控制：弱點管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具

**指引**：啟用 Azure SQL Database 的先進資料安全性，並遵循在 Azure SQL server 上執行弱點評定 Azure 資訊安全中心的建議。



如何在 Azure SQL 資料庫上執行弱點評定：

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment



如何啟用 Advanced Data Security：

https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security



如何實行 Azure 資訊安全中心弱點評估建議：

https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化的作業系統修補程式管理解決方案

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署自動化的協力廠商軟體修補程式管理解決方案

**指導**方針：不適用;此基準測試適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比較回溯弱點掃描

**指引**：為您的 Azure SQL Database 實例啟用週期性週期性掃描;這會將弱點評定設定為每週自動在您的資料庫上執行掃描。 掃描結果摘要將傳送到您所提供的電子郵件地址。 比較結果，以確認已補救弱點。



如何在 Azure 資訊安全中心中匯出弱點評定報告：

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment#implementing-vulnerability-assessment

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程式來排定所發現弱點的補救優先順序

**指導**方針：使用 Azure 資訊安全中心所提供的預設風險評等（安全分數）。

瞭解 Azure 資訊安全中心安全分數： https://docs.microsoft.com/azure/security-center/security-center-secure-score

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱[安全性控制：清查和資產管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-use-azure-asset-discovery"></a>6.1：使用 Azure 資產探索

**指引**：使用 Azure Resource Graph 來查詢和探索訂用帳戶內的所有資源（包括 Azure SQL Server 實例）。  請確定您的租使用者中有適當的（讀取）許可權，而且能夠列舉所有的 Azure 訂用帳戶以及您訂用帳戶內的資源。


雖然可透過 Resource Graph 探索傳統的 Azure 資源，但強烈建議您建立並使用 Azure Resource Manager 的資源。


如何使用 Azure Graph 建立查詢： https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


如何查看您的 Azure 訂用帳戶： https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0


瞭解 Azure RBAC： https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指引**：將標籤套用至 Azure 資源，讓中繼資料以邏輯方式將其組織成分類法。



如何建立和使用標記：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指引**：使用標記、管理群組，以及個別的訂用帳戶（適當時）來組織和追蹤資產。 定期協調清查，並確保未經授權的資源會及時從訂用帳戶中刪除。



如何建立額外的 Azure 訂用帳戶：

https://docs.microsoft.com/azure/billing/billing-create-subscription



如何建立管理群組：

https://docs.microsoft.com/azure/governance/management-groups/create



如何建立和使用標記：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：維護已核准 Azure 資源和軟體標題的清查

**指導**方針：定義已核准的 Azure 資源清單，以及適用于您計算資源的已核准軟體

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：未核准的 Azure 資源的監視

**指引**：使用 Azure 原則來對可使用下列內建原則定義在客戶訂用帳戶中建立的資源類型進行限制：

- 不允許的資源類型
- 允許的資源類型

使用 Azure Resource Graph 來查詢/探索訂用帳戶內的資源。 請確定已核准環境中的所有 Azure 資源。

如何設定和管理 Azure 原則： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure Graph 建立查詢： https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：監視計算資源內未經核准的軟體應用程式

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未核准的 Azure 資源和軟體應用程式

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指引**：使用下列內建原則定義，利用 Azure 原則來對可在客戶訂用帳戶中建立的資源類型施加限制：

- 不允許的資源類型
- 允許的資源類型

使用 Azure Resource Graph 來查詢/探索訂用帳戶內的資源。 請確定已核准環境中的所有 Azure 資源。

如何設定和管理 Azure 原則： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何拒絕具有 Azure 原則的特定資源類型： https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="610-implement-approved-application-list"></a>6.10：執行核准的應用程式清單

**指導**方針：不適用;這項建議適用于在計算資源上執行的應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11：限制使用者透過腳本與 Azure 資源管理員互動的能力

**指引**：使用 Azure 條件式存取，藉由設定「Microsoft Azure 管理」應用程式的「封鎖存取」，限制使用者與 Azure Resource Manager 互動的能力。


如何設定條件式存取以封鎖對 Azure Resource Manager 的存取： https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制使用者在計算資源內執行腳本的能力

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：實際或邏輯上隔離高風險應用程式

**指導**方針：不適用;這項建議適用于主控桌面或 web 應用程式的 App Service 或計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱[安全性控制：安全](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)設定。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指引**：使用 Azure SQL 伺服器/資料庫的 Azure 原則或 Azure 資訊安全中心建議，以維護所有 Azure 資源的安全性設定。


如何設定和管理 Azure 原則：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的作業系統設定

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指引**：使用 azure 原則 [拒絕] 和 [不存在時部署]，在您的 Azure 資源上強制執行安全設定。



如何設定和管理 Azure 原則：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



瞭解 Azure 原則效果：

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：維護安全的作業系統設定

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導**方針：如果使用自訂的 Azure 原則定義，請使用 Azure DevOps 或 Azure Repos 來安全地儲存和管理您的程式碼。



如何將程式碼儲存在 Azure DevOps：

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops



Azure Repos 檔：

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂的作業系統映射

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系統設定管理工具

**指引**：在 "Microsoft .sql" 命名空間中使用 Azure 原則別名來建立自訂原則，以警示、審查和強制執行系統設定。 此外，開發處理常式和管線來管理原則例外狀況。



如何設定和管理 Azure 原則：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的系統建構管理工具

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：執行 Azure 服務的自動化設定監視

**指引**：利用 Azure 資訊安全中心來執行 Azure SQL 伺服器和資料庫的基準掃描。



如何修復 Azure 資訊安全中心中的建議：

https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統執行自動化設定監視

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 秘密

**指導**方針：使用 Azure Key Vault 來儲存 Azure SQL Database 透明資料加密（TDE）的加密金鑰。



如何保護儲存在 Azure SQL Server 中的機密資料，並將加密金鑰儲存在 Azure Key Vault 中：

https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

**指引**：在 AZURE ACTIVE DIRECTORY （AAD）中使用受控識別，以自動管理的身分識別來提供 Azure 服務。 受控識別可讓您向任何支援 AAD 驗證的服務進行驗證，包括 Azure Key Vault，而不需要您程式碼中的任何認證。


教學課程：使用 Windows VM 系統指派的受控識別來存取 Azure SQL：

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql


如何設定受控識別：

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證暴露

**指導**方針：執行認證掃描器來識別程式碼中的認證。 認證掃描器也鼓勵將探索到的認證移至更安全的位置，例如 Azure Key Vault。 

如何設定認證掃描器： https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*如需詳細資訊，請參閱[安全性控制：惡意程式碼防護](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反惡意程式碼軟體

**指導**方針：不適用;這項建議適用于計算資源。 Microsoft 會處理基礎平臺的反惡意程式碼。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指導**方針：支援 Azure 服務的基礎主機（例如 Azure App Service）已啟用 Microsoft 反惡意程式碼，但不會在客戶內容上執行。


預先掃描要上傳至非計算 Azure 資源的任何內容，例如 App Service、Data Lake Storage、Blob 儲存體、Azure SQL Server 等等。Microsoft 無法存取您在這些實例中的資料。


瞭解 Azure 雲端服務和虛擬機器的 Microsoft Antimalware： https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3：確認反惡意程式碼軟體和簽章已更新

**指導**方針：不適用;這項建議適用于計算資源。 Microsoft 會處理基礎平臺的反惡意程式碼。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱[安全性控制：資料](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)復原。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確保週期性自動備份

**指導**方針：為了保護您的企業免于資料遺失，Azure SQL Database 會每週自動建立完整資料庫備份、每12小時的差異資料庫備份，以及每 5-10 分鐘執行一次交易記錄備份。 所有服務層級的備份會儲存在 GRS 儲存體中至少7天。 所有服務層級（基本支援可設定的時間點還原備份保留期限），最多35天。


若要符合不同的合規性需求，您可以針對每週、每月和/或每年備份選取不同的保留週期。 儲存體耗用量取決於選取的備份頻率和保留期間。


瞭解 Azure SQL Server 的備份和商務持續性：

https://docs.microsoft.com/azure/sql-database/sql-database-business-continuity

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份，並備份任何客戶管理的金鑰

**指導**方針： Azure SQL Database 會自動建立保留7到35天的資料庫備份，並使用 Azure 讀取權限異地冗余儲存體（RA-GRS）來確保即使資料中心無法使用，也會保留它們。 這些備份會自動建立。 如有需要，請針對您的 Azure SQL 資料庫啟用長期異地多餘備份。


如果透明資料加密使用客戶管理的金鑰，請確定您的金鑰已備份。


瞭解 Azure SQL Server 中的備份：

https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups?tabs=single-database


如何在 Azure 中備份金鑰保存庫金鑰：

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指導**方針：確保能夠在 Azure 備份內定期執行內容的資料還原。 如有必要，請測試將內容還原至隔離的 VLAN。 已備份客戶管理金鑰的測試還原。


如何在 Azure 中還原金鑰保存庫金鑰：

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0


如何使用時間點還原來復原 Azure SQL Database 備份：

https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#point-in-time-restore

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理金鑰的保護

**指引**：在 Azure Key Vault 中啟用虛刪除，以防止意外或惡意刪除的金鑰。


如何在 Key Vault 中啟用虛刪除：

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱[安全性控制：事件回應](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指導**方針：確定有撰寫的事件回應計畫會定義人員的角色，以及事件處理/管理的階段。



如何設定 Azure 資訊安全中心內的工作流程自動化：

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程式

**指導**方針：資訊安全中心指派警示的嚴重性，協助您排定參與每個警示的順序，如此一來，當資源遭到入侵時，您就可以立即開始使用。 嚴重性是根據資訊安全中心在尋找中的信心，或用於發出警示的分析，以及導致警示的活動背後有惡意意圖的信賴等級。
Azure 資訊安全中心中的安全性警示： https://docs.microsoft.com/azure/security-center/security-center-alerts-overview


**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程式

**指導**方針：執行練習以定期測試系統的事件回應功能。 識別弱式點和間距，並視需要修訂計畫。



您可以參考 NIST 的發行集：適用于 IT 計畫和功能的測試、訓練和練習程式指南：

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指導**方針：如果 Microsoft 安全性回應中心（MSRC）發現您的資料已由非法或未經授權的合作物件存取，microsoft 將會使用安全性事件連絡人資訊來與您聯繫。



如何設定 Azure 資訊安全中心安全性連絡人：

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示納入事件回應系統中

**指引**：使用「連續匯出」功能來匯出您的 Azure 資訊安全中心警示和建議。 「連續匯出」可讓您以手動或持續的方式來匯出警示和建議。 您可以使用 Azure 資訊安全中心資料連線器，將警示串流至 Sentinel。


如何設定連續匯出：

https://docs.microsoft.com/azure/security-center/continuous-export


如何將警示串流至 Azure Sentinel：

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指引**：使用 Azure 資訊安全中心中的工作流程自動化功能，透過「Logic Apps」安全性警示和建議，自動觸發回應。



如何設定工作流程自動化和 Logic Apps：

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*如需詳細資訊，請參閱[安全性控制：滲透測試和 Red Team 練習](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1：進行 Azure 資源的定期滲透測試，並確保在60天內補救所有重大安全性結果

**指導**方針：請遵循 Microsoft Engagement 規則，以確保您的滲透測試不會違反 microsoft 原則：

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1第 1 課：建立 Windows Azure 儲存體物件{2}。



您可以在這裡找到有關 Microsoft 受管理的雲端基礎結構、服務和應用程式的 Microsoft 策略和執行的 Red 小組和即時網站滲透測試的詳細資訊： https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱[Azure 安全性基準測試](https://docs.microsoft.com/azure/security/benchmarks/overview)
- 深入瞭解[Azure 安全性基準](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)

---
title: 適用于 Synapse 分析的 Azure 安全性基準
description: Synapse 分析安全性基準提供的程式指引和資源，可讓您執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: bbec83c655d568b43b319f30a96da0a8fc1effe8
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230648"
---
# <a name="azure-security-baseline-for-synapse-analytics"></a>適用于 Synapse 分析的 Azure 安全性基準

適用于 Synapse 分析的 Azure 安全性基準包含可協助您改善部署安全性狀態的建議。

此服務的基準取自 [Azure 安全性效能評定 1.0 版](https://docs.microsoft.com/azure/security/benchmarks/overview)，其會提供如何在 Azure 上使用最佳做法指引來保護雲端解決方案的建議。

如需詳細資訊，請參閱 [Azure 安全性基準概觀](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview) (機器翻譯)。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱[安全性控制：網路安全性](/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保護虛擬網路內的 Azure 資源

**指導**方針：透過 Private Link 將 Azure SQL Server 保護到虛擬網路。 Azure Private Link 可讓您透過虛擬網路中的私人端點來存取 Azure PaaS 服務。 您的虛擬網路與服務之間的流量會經由 Microsoft 骨幹網路傳輸。

或者，當您連接到 Synapse SQL 集區時，請使用網路安全性群組，將傳出連線的範圍縮小到 SQL database。 藉由將 [允許 Azure 服務] 設為 [關閉]，以停用透過公用端點的所有 Azure 服務流量至 SQL database。 確定防火牆規則中不允許公用 IP 位址。

* [瞭解 Azure Private Link](https://docs.microsoft.com/azure/private-link/private-link-overview)

* [瞭解 Azure Synapse SQL 的 Private Link](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)

* [如何建立虛擬網路](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [如何建立具有安全性設定的 NSG](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2：監視和記錄虛擬網路、子網和網路介面的設定和流量

**指導**方針：連線至您的 AZURE Synapse SQL 集區時，您已啟用網路安全性群組 (NSG) 流量記錄，將記錄傳送到 Azure 儲存體帳戶以進行流量審核。

您也可將 NSG 流量記錄傳送到 Log Analytics 工作區，並使用流量分析來提供對 Azure 雲端流量的見解。 流量分析的優點包括能將網路活動視覺化並找出作用點、識別安全性威脅、了解流量模式並找到錯誤的網路設定。

* [如何啟用 NSG 流量記錄](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [瞭解 Azure 資訊安全中心所提供的網路安全性](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

* [如何啟用及使用流量分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [瞭解 Azure 資訊安全中心所提供的網路安全性](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 Web 應用程式

**指導**方針：不適用;這項建議適用于裝載 web 應用程式的 Azure 應用程式服務或計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址的通訊

**指導**方針：使用 AZURE Synapse SQL 的 Advanced 威脅防護 (ATP) 。 ATP 會偵測異常活動，指出有不尋常且可能有害的存取或惡意探索資料庫，而且可能會觸發各種警示，例如「潛在的 SQL 插入式」和「從不尋常的位置存取」。 ATP 是 Advanced data security (ADS) 供應專案的一部分，可透過中央 SQL ADS 入口網站存取及管理。

在與 Azure Synapse SQL 相關聯的虛擬網路上啟用 DDoS 保護標準，以防止分散式阻絕服務攻擊。 使用 Azure 資訊安全中心的整合式威脅情報，以拒絕與已知為惡意或未使用的網際網路 IP 位址通訊。

* [瞭解 Azure Synapse SQL 的 ATP](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)

* [如何啟用 Azure SQL Database 的 Advanced Data Security](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [ADS 總覽](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [如何設定 DDoS 保護](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [了解 Azure 資訊安全中心的整合式威脅情報](https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="15-record-network-packets"></a>1.5：記錄網路封包

**指導**方針：連線至您的 AZURE Synapse SQL 集區時，您已啟用網路安全性群組 (NSG) 流量記錄，將記錄傳送到 Azure 儲存體帳戶以進行流量審核。 您也可以將流量記錄傳送至 Log Analytics 工作區，或將它們串流至事件中樞。 如果需要調查異常活動，請啟用網路監看員封包捕獲。

* [如何啟用 NSG 流量記錄](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [如何啟用網路監看員](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署以網路為基礎的入侵偵測/入侵防護系統 (IDS/IPS) 

**指導**方針：使用 AZURE Synapse SQL 的 Advanced 威脅防護 (ATP) 。 ATP 會偵測異常活動，指出有不尋常且可能有害的存取或惡意探索資料庫，而且可能會觸發各種警示，例如「潛在的 SQL 插入式」和「從不尋常的位置存取」。 ATP 是 Advanced data security (ADS) 供應專案的一部分，可透過中央 SQL ADS 入口網站存取及管理。 ATP 也會整合警示與 Azure 資訊安全中心。

* [瞭解 Azure Synapse SQL 的 ATP](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理 Web 應用程式的流量

**指導**方針：不適用;這項建議適用于裝載 web 應用程式的 Azure 應用程式服務或計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負擔降至最低

**指導**方針：使用虛擬網路服務標籤來定義網路安全性群組或 Azure 防火牆上的網路存取控制。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 在規則的適當來源或目的地欄位中指定服務標籤名稱 (例如 ApiManagement)，即可允許或拒絕對應服務的流量。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。

使用 Azure Synapse SQL 集區的服務端點時，需要輸出至 Azure SQL database 公用 IP 位址：必須開啟網路安全性群組 (Nsg) ，才能 Azure SQL Database Ip 以允許連線。 您可以使用 NSG 服務標記進行 Azure SQL Database。

* [瞭解 Azure SQL Database 的服務端點服務標記](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations)

* [瞭解和使用服務標記](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指導**方針：使用 Azure 原則定義和執行與您的 SQL 集區相關的資源的網路安全性設定。 您可以使用 "Microsoft .Sql" 命名空間來定義自訂原則定義，或使用針對 Azure SQL database/伺服器網路保護所設計的任何內建原則定義。 Azure SQL Database server 適用的內建網路安全性原則範例如下：「SQL Server 應該使用虛擬網路服務端點」。

使用 Azure 藍圖，藉由在單一藍圖定義中封裝關鍵環境成品（例如 Azure 資源管理範本、azure 角色型存取控制 (Azure RBAC) 和原則）來簡化大規模的 Azure 部署。 輕鬆地將藍圖套用至新的訂閱、環境，以及透過版本控制來微調控制和管理。

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何建立 Azure 藍圖](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

**指導**方針：將標記用於網路安全性群組 (NSG) 以及與網路安全性和流量相關的其他資源。 對於個別的 NSG 規則，使用 [描述] 欄位，針對允許進出網路流量的任何規則指定商務需求和/或持續時間 (等等)。

使用任何與標記相關的內建 Azure 原則定義，例如「需要標籤和其值」，以確保所有資源都是使用標籤建立的，並會通知您現有的未標記資源。

您可使用 Azure PowerShell 或 Azure CLI，根以據資源的標籤對資源進行查詢或執行動作。

* [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導**方針：使用 Azure 活動記錄來監視網路資源設定，並偵測與您的 AZURE Synapse SQL 集區相關的網路資源變更。 在 Azure 監視器中建立警示，以在重要網路資源發生變更時觸發。

* [如何檢視及擷取 Azure 活動記錄事件](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [如何在 Azure 監視器中建立警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱[安全性控制：記錄和監視](/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源

**指導**方針： Microsoft 會維護 Azure 資源的時間來源。 您可以更新計算部署的時間同步處理。

* [如何設定 Azure 計算資源的時間同步處理](https://docs.microsoft.com/azure/virtual-machines/windows/time-sync)

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導**方針：可以針對特定資料庫定義稽核原則，或在裝載 azure Synapse) 的 azure (中作為預設伺服器原則。 伺服器原則會套用至伺服器上所有現有和新建立的資料庫。

如果已啟用伺服器審核，它一律會套用到資料庫。 不論資料庫稽核資料是什麼，都會稽核資料庫。

當您啟用審核功能時，您可以將它們寫入 Azure 儲存體帳戶、Log Analytics 工作區或事件中樞的 audit 記錄檔中。

或者，您也可啟用 Azure Sentinel 或第三方 SIEM，並讓資料上線。

* [如何設定 Azure SQL 資源的審核](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#server-vs-database-level)

* [如何使 Azure Sentinel 上線](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指導**方針：在 Azure sql server 層級上針對您的 Synapse SQL 集區啟用審核，並針對 (Azure 儲存體、Log Analytics 或事件中樞) 的記錄，選擇儲存位置。

您可以在資料庫或伺服器層級上啟用審核，而且建議只能在伺服器層級上啟用，除非您需要為特定資料庫設定個別的資料接收或保留。

* [如何啟用 Azure SQL Database 的審核](https://docs.microsoft.com/azure/sql-database/sql-database-auditing)

* [如何啟用伺服器的審核](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#setup-auditing)

* [伺服器層級與資料庫層級稽核原則的差異](https://docs.microsoft.com/azure/sql-database/sql-database-auditing#server-vs-database-level)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：從作業系統收集安全性記錄

**指導**方針：不適用;此基準測試適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指導**方針：將與 Synapse SQL 集區相關的記錄儲存在儲存體帳戶、Log Analytics 工作區或事件中樞時，請根據您組織的合規性法規來設定記錄保留期限。

* [管理 Azure Blob 儲存體生命週期](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal)

* [如何在 Log Analytics 工作區中設定記錄保留參數](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

* [在事件中樞中捕獲串流事件](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和審核記錄

**指導**方針：分析和監視異常行為的記錄，並定期查看結果。 針對 Azure SQL Database 使用 Advanced 威脅防護搭配 Azure 資訊安全中心，以警示與您 SQL Database 相關的異常活動。 或者，根據計量值或與您的 SQL database 相關的 Azure 活動記錄專案來設定警示。

或者，您也可啟用 Azure Sentinel 或第三方 SIEM，並讓資料上線。

* [瞭解 Azure SQL Database 的 Advanced 威脅防護和警示](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)

* [如何啟用 Azure SQL Database 的 Advanced Data Security](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [如何設定 Azure SQL Database 的自訂警示](https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal?view=azps-1.4.0)

* [如何使 Azure Sentinel 上線](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：啟用異常活動的警示

**指導**方針：使用 Advanced 威脅防護 (ATP) 的 Azure SQL Database 搭配 Azure 資訊安全中心來監視異常活動併發出警示。 ATP 是 Advanced data security (ADS) 供應專案的一部分，可透過入口網站中的中央 SQL ADS 來存取及管理。 ADS 包含探索和分類敏感性資料、呈現和減緩潛在資料庫弱點，以及偵測可能表示對資料庫有威脅之異常活動的功能。

或者，您也可以啟用和麵板上的資料以 Azure Sentinel。

* [瞭解 Azure SQL Database 的 Advanced 威脅防護和警示](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)

* [如何啟用 Azure SQL Database 的 Advanced Data Security](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [如何在 Azure 資訊安全中心中管理警示](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

* [如何使 Azure Sentinel 上線](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中化反惡意程式碼記錄

**指導**方針：不適用;針對與 Synapse SQL 集區相關的資源，反惡意程式碼解決方案是由 Microsoft 在基礎平臺上管理。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄

**指導**方針：不適用;與您的 Synapse SQL 集區相關的資源，不會產生任何 DNS 記錄。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列稽核記錄

**指導**方針：不適用;命令列的審核功能不適用於 Azure Synapse SQL。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱[安全性控制：身分識別與存取控制](/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的詳細目錄

**指導**方針：使用 AZURE ACTIVE DIRECTORY 或 SQL 驗證來驗證使用者。

當您第一次部署 Azure SQL 時，您會為該登入指定管理員登入和相關聯的密碼。 此系統管理帳戶稱為「伺服器管理員」。您可以藉由開啟 Azure 入口網站並流覽至伺服器或受控實例的 [內容] 索引標籤，來識別資料庫的系統管理員帳戶。 您也可以使用完整的系統管理許可權來設定 Azure AD 管理員帳戶，如果您想要啟用 Azure Active Directory 驗證，這是必要的。

針對管理作業，請使用必須明確指派的 Azure 內建角色。 使用 Azure AD PowerShell 模組執行臨機操作查詢，以探索屬於系統管理群組成員的帳戶。

* [SQL Database 的驗證](https://docs.microsoft.com/azure/azure-sql/database/security-overview#authentication)

* [為非系統管理使用者建立帳戶](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-accounts-for-non-administrator-users)

* [使用 Azure Active Directory 帳戶進行驗證](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-additional-logins-and-users-having-administrative-permissions)

* [如何使用 PowerShell 在 Azure AD 中取得目錄角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [如何在 Azure SQL 中管理現有的登入和系統管理員帳戶](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

* [Azure 內建角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在適用的情況下變更預設密碼

**指導**方針： Azure Active Directory 沒有預設密碼的概念。 布建 Azure Synapse SQL 集區時，建議您選擇整合驗證與 Azure Active Directory。 使用此驗證方法，使用者會提交使用者帳戶名稱，並要求服務使用儲存在 Azure Active Directory (Azure AD) 中的認證資訊。

* [如何使用 Azure SQL 設定及管理 Azure Active Directory authentication](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure?tabs=azure-powershell#active-directory-password-authentication)

* [瞭解 Azure SQL 中的驗證](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指導**方針：使用專用的系統管理帳戶來建立原則和程式。 使用 Azure 資訊安全中心身分識別和存取管理來監視透過 Azure Active Directory 登入的系統管理帳戶數目。

若要識別資料庫的系統管理員帳戶，請開啟 Azure 入口網站，然後流覽至伺服器或受控實例的 [屬性] 索引標籤。

* [瞭解 Azure 資訊安全中心身分識別和存取權](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [如何在 Azure SQL 中管理現有的登入和系統管理員帳戶](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory 單一登入 (SSO) 

**指導**方針：使用 Azure 應用程式註冊 (服務主體) 來取得權杖，此權杖可在控制平面上用來與您的資料倉儲互動 (Azure 入口網站) 透過 API 呼叫。

* [如何呼叫 Azure REST Api](https://docs.microsoft.com/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

* [如何使用 Azure AD 向 (服務主體) 註冊用戶端應用程式](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad)

* [Azure Synapse SQL REST API 資訊](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：所有以 Azure Active Directory 為基礎的存取都使用多重要素驗證

**指引**：啟用 Azure Active Directory (AD) 多重要素驗證 (MFA)，並遵循 Azure 資訊安全中心的身分識別與存取管理建議。

* [如何在 Azure 中啟用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [如何在 Azure 資訊安全中心監視身分識別和存取](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [瞭解 Azure SQL 中的 MFA](https://docs.microsoft.com/azure/azure-sql/database/authentication-mfa-ssms-overview)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6：使用安全、受 Azure 管理的工作站進行系統管理工作

**指導**方針：使用特殊許可權存取工作站 (PAW) ，MULTI-FACTOR AUTHENTICATION (MFA) 設定為登入和設定 Azure 資源。

* [瞭解特殊權限存取工作站](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [如何在 Azure 中啟用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：來自系統管理帳戶的可疑活動記錄和警示

**指導**方針：當環境中發生可疑或不安全的活動時，請使用 Azure Active Directory 的安全性報告來產生記錄和警示。

使用 Advanced 威脅 Azure SQL Database 防護搭配 Azure 資訊安全中心來偵測異常活動併發出警示，以指出存取或惡意探索資料庫的不尋常且可能有害的嘗試。

SQL Server 稽核可讓您建立伺服器稽核，其中可能包含伺服器等級事件的伺服器稽核規格，以及資料庫等級事件的資料庫稽核規格。 稽核的事件可以寫入事件記錄或稽核檔案。

* [如何識別已標示為有風險活動的 Azure AD 使用者](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [如何在 Azure 資訊安全中心中監視使用者身分識別和存取活動](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [查看 Advanced 威脅防護和潛在警示](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview#advanced-threat-protection-alerts)

* [瞭解 Azure SQL 中的登入和使用者帳戶](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

* [瞭解 SQL Server 的審核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-ver15)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指導**方針：使用名為「位置」的條件式存取，只允許來自特定 IP 位址範圍或國家/地區之邏輯群組的入口網站和 Azure 資源管理存取權。

* [如何在 Azure 中設定具名位置](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導**方針：為 Synapse SQL 集區中的 Azure SQL Database 伺服器建立 AZURE ACTIVE DIRECTORY (AD) 系統管理員。

* [如何使用 Azure SQL 設定及管理 Azure AD authentication](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)

* [如何建立和設定 Azure AD 實例](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指導**方針： Azure Active Directory 提供記錄檔，以協助您探索過時的帳戶。 此外，使用 Azure Active Directory 存取評論，有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 您可以定期審核使用者的存取權，以確保只有適當的使用者可以繼續存取。

使用 SQL 驗證時，請在資料庫中建立自主資料庫使用者。 確定您將一或多個資料庫使用者放入具有適合該使用者群組之特定許可權的自訂資料庫角色。

* [如何使用存取權審核](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [瞭解 Azure SQL 中的登入和使用者帳戶](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：監視嘗試存取已停用的認證

**指導**方針：使用 Azure SQL 設定 AZURE ACTIVE DIRECTORY (AD) 驗證，並為 Azure Active Directory 使用者帳戶啟用診斷設定，並將審核記錄和登入記錄傳送至 Log Analytics 工作區。 在 Log Analytics 中設定所需的警示。

使用 SQL 驗證時，請在資料庫中建立自主資料庫使用者。 確定您將一或多個資料庫使用者放入具有適合該使用者群組之特定許可權的自訂資料庫角色。

* [如何使用存取權審核](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [如何使用 Azure SQL Database 來設定和管理 Azure AD authentication](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)

* [如何將 Azure 活動記錄整合到 Azure 監視器中](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [瞭解 Azure SQL 中的登入和使用者帳戶](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：帳戶登入行為偏差的警示

**指導**方針：使用 Azure Active Directory (Azure AD) Identity Protection 和風險偵測功能，以針對偵測到與使用者身分識別相關的可疑動作，設定自動回應。 此外，您可以將資料上線，並將資料內嵌到 Azure Sentinel 以進行進一步的調查。

使用 SQL 驗證時，請在資料庫中建立自主資料庫使用者。 確定您將一或多個資料庫使用者放入具有適合該使用者群組之特定許可權的自訂資料庫角色。

* [如何查看 Azure AD 的風險登入](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [如何設定和啟用身分識別保護風險原則](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [如何讓 Azure Sentinel 上線](https://docs.microsoft.com/azure/sentinel/connect-data-sources)

* [瞭解 Azure SQL 中的登入和使用者帳戶](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支援案例期間為 Microsoft 提供相關客戶資料的存取權

**指導**方針：在 Microsoft 需要存取 Synapse SQL 集區中 Azure SQL Database 相關資料的支援案例中，Azure 客戶加密箱會提供一個介面，供您用來檢查和核准或拒絕資料存取要求。

* [瞭解客戶加密箱](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱[安全性控制：資料保護](/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的詳細目錄

**指引**：使用標籤協助追蹤可儲存或處理敏感性資訊的 Azure 資源。

資料探索 &amp; 分類已內建于 Azure SYNAPSE SQL 中。 它提供探索、分類、標記和報告資料庫中敏感性資料的先進功能。

* [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [瞭解資料探索 &amp; 分類](https://docs.microsoft.com/azure/azure-sql/database/data-discovery-and-classification-overview)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指引**：針對開發、測試和生產，實作不同的訂用帳戶及/或管理群組。 資源應以虛擬網路/子網分隔、正確標記，並在網路安全性群組或 Azure 防火牆內受到保護。 儲存或處理敏感性資料的資源應該是隔離的。 使用 Private Link;將您的 Azure SQL Server 部署在虛擬網路內，並使用 Private Link 安全地連線。

* [如何建立額外的 Azure 訂閱](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [如何建立管理群組](https://docs.microsoft.com/azure/governance/management-groups/create)

* [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [如何設定適用於 Azure SQL Database 的 Private Link](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的敏感性資訊傳輸

**指導**方針：針對 Synapse SQL 集區中儲存或處理敏感性資訊的任何 Azure SQL Database，請使用標記將資料庫和相關資源標記為機密。 設定 Private Link 搭配網路安全性群組 (NSG Azure SQL Database 實例上) 的服務標籤，以防止機密資訊的遭到外泄。

此外，Azure SQL Database 的 Advanced 威脅防護、Azure SQL 受控執行個體和 Azure Synapse 會偵測出異常活動，指出有不尋常且可能有害的存取或惡意探索資料庫。

針對 Microsoft 管理的基礎平台，Microsoft 會將所有客戶內容視為敏感性資訊，並竭盡全力防範客戶資料外洩和暴露。 為確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制和功能。

* [如何設定 Private Link 和 Nsg 以防止 Azure SQL Database 實例上的資料遭到外泄](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)

* [瞭解 Azure SQL Database 的 Advanced 威脅防護](https://docs.microsoft.com/azure/azure-sql/database/threat-detection-overview)

* [瞭解 Azure 中的客戶資料保護](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導**方針： Azure SQL Database 透過傳輸層安全性來加密移動中的資料，以保護您的資料。 SQL Server 針對所有連線強制執行加密 (SSL/TLS) 。 這可確保不論連接字串中的加密或 TrustServerCertificate 設定，所有資料都會在用戶端與伺服器之間加密「傳輸中」。

* [瞭解傳輸中的 Azure SQL 加密](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview#information-protection-and-encryption)

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

**指導**方針：使用 AZURE Synapse SQL 資料探索 &amp; 分類功能。 資料探索 &amp; 分類提供內建于 Azure SQL Database 的先進功能，可用於探索、分類、標記 &amp; 保護資料庫中的敏感性資料。

資料探索 &amp; 分類是 Advanced Data Security 供應專案的一部分，這是 ADVANCED SQL 安全性功能的整合套件。 資料探索 &amp; 分類可以透過中央 SQL ADS 入口網站存取及管理。

此外，您可以在 Azure 入口網站中將動態資料遮罩設定 (DDM) 原則。 DDM 建議引擎會將您資料庫中的特定欄位標示為潛在的敏感性欄位，這可能是遮罩的理想候選項目。

* [如何使用 Azure SQL Server 的資料探索和分類](https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification)

* [瞭解 Azure Synapse SQL 的動態資料遮罩](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 來控制資源的存取權

**指導**方針：使用 azure 角色型存取控制 (azure RBAC) 來管理 Synapse SQL 集區中的 azure SQL 資料庫存取權。

授權是由使用者帳戶的資料庫角色成員資格和物件層級許可權所控制。 最好的作法是，您應該授與使用者所需的最低權限。

* [如何整合 Azure SQL Server 與 Azure Active Directory 以進行驗證](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)

* [如何在 Azure SQL Server 中控制存取權](https://docs.microsoft.com/azure/sql-database/sql-database-control-access)

* [瞭解 Azure SQL 中的授權和驗證](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用主機型資料外洩防護來強制執行存取控制

**指導**方針：不適用;Microsoft 會管理 Azure Synapse SQL 的基礎結構，並已實行嚴格的控制，以防止客戶資料遺失或洩漏。

* [瞭解 Azure 中的客戶資料保護](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指導**方針：透明資料加密 (TDE) 可透過加密待用資料，協助保護 AZURE Synapse SQL 免于惡意離線活動的威脅。 它會對資料庫、相關聯的備份和待用的交易記錄檔執行即時加密和解密，而不需變更應用程式。 在 Azure 中，TDE 的預設設定是由內建的伺服器憑證所保護的 DEK。 或者，您也可以使用客戶管理的 TDE，也稱為攜帶您自己的金鑰 (BYOK) 支援 TDE。 在此案例中，加密 DEK 的 TDE 保護裝置是由客戶管理的非對稱金鑰，該金鑰會儲存在客戶擁有及管理的 Azure Key Vault (Azure 的雲端式外部金鑰管理系統) ，且永遠不會離開金鑰保存庫。

* [瞭解服務管理的透明資料加密](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview?tabs=azure-portal)

* [瞭解客戶管理的透明資料加密](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview?tabs=azure-portal#customer-managed-transparent-data-encryption---bring-your-own-key)

* [如何使用您自己的金鑰開啟 TDE](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-byok-configure)

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導**方針：使用 Azure 監視器搭配 Azure 活動記錄，以建立在變更 Synapse SQL 集區和其他重要或相關資源的生產實例時所發生的警示。

此外，您也可以使用 Azure 入口網站設定 SQL Synapse 集區中的資料庫警示。 當某些計量 (例如，資料庫大小或 CPU 使用量) 閾值時，警示可傳送電子郵件給您或呼叫 Webhook。

* [如何建立 Azure 活動記錄事件的警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

* [如何建立 Azure SQL Synapse 的警示](https://docs.microsoft.com/azure/azure-sql/database/alerts-insights-configure-portal)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="vulnerability-management"></a>弱點管理

*如需詳細資訊，請參閱[安全性控制：弱點管理](/azure/security/benchmarks/security-control-vulnerability-management)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具

**指導**方針：啟用先進的資料安全性，並遵循 Azure 資訊安全中心在 Azure SQL 資料庫上執行弱點評定的建議。

* [如何在 Azure SQL 資料庫上執行弱點評定](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)

* [如何啟用 Advanced Data Security](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [如何實行 Azure 資訊安全中心弱點評定建議](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化的作業系統修補程式管理解決方案

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：為協力廠商軟體專案部署自動化的修補程式管理解決方案

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比較連續性弱點掃描

**指導**方針：弱點評定是 AZURE Synapse SQL 內建的掃描服務。 此服務會採用規則的知識庫，以標示安全性弱點。 它會反白顯示與最佳作法的偏差，例如錯誤配置、過多的許可權，以及未受保護的敏感性資料。 您可以透過中央 SQL Advanced Data Security (ADS) 入口網站來存取和管理弱點評定。

* [在 SQL ADS 入口網站中管理和匯出弱點評定掃描](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程序來排定所發現弱點的補救優先順序

**指導**方針：使用 (Azure 資訊安全中心提供的安全分數) 的預設風險評等。

資料探索 &amp; 分類已內建于 Azure SYNAPSE SQL 中。 它提供探索、分類、標記和報告資料庫中敏感性資料的先進功能。

* [瞭解 Azure 資訊安全中心安全分數](https://docs.microsoft.com/azure/security-center/security-center-secure-score)

* [瞭解資料探索 &amp; 分類](https://docs.microsoft.com/azure/azure-sql/database/data-discovery-and-classification-overview)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱[安全性控制：清查和資產管理](/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自動化資產探索解決方案

**指導**方針：使用 Azure Resource Graph 來查詢及探索您的訂用帳戶 () 中與 Synapse SQL 集區相關的所有資源。 確保租用戶中有適當的 (讀取) 權限，且能列舉所有 Azure 訂用帳戶以及訂用帳戶中的資源。

雖然可以透過 Azure Resource Graph 探索傳統的 Azure 資源，但強烈建議您建立並使用 Azure Resource Manager 的資源。

* [如何使用 Azure Resource Graph 建立查詢](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [如何查看您的 Azure 訂用帳戶](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [了解 Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指引**：將標籤套用至提供中繼資料的 Azure 資源，以邏輯方式依分類組織這些資源。

* [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指導**方針：使用標記、管理群組和個別訂用帳戶（如果適用）來組織和追蹤資產。 請定期調節清查，並確保會及時刪除訂用帳戶中未經授權的資源。

* [如何建立額外的 Azure 訂閱](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [如何建立管理群組](https://docs.microsoft.com/azure/governance/management-groups/create)

* [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定義和維護已核准 Azure 資源的清查

**指導**方針：定義與您的 Synapse SQL 集區相關的已核准 Azure 資源清單。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指導**方針：使用 Azure 原則對可使用下列內建原則定義在客戶訂用帳戶中建立的資源類型進行限制：
- 不允許的資源類型
- 允許的資源類型

使用 Azure Resource Graph 來查詢/探索訂用帳戶內的資源。 確保已核准環境中的所有 Azure 資源。

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何使用 Azure Resource Graph 建立查詢](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：監視計算資源內未經核准的軟體應用程式

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未經核准的 Azure 資源和軟體應用程式

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指導**方針：使用 Azure 原則針對可在客戶訂用帳戶 (s 中建立的資源類型，使用下列內建原則定義來限制其) ：
- 不允許的資源類型
- 允許的資源類型

使用 Azure Resource Graph 來查詢/探索您的訂用帳戶 (s) 中的資源。 確保已核准環境中的所有 Azure 資源。

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何使用 Azure 原則拒絕特定的資源類型](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：維護已核准軟體標題的清查

**指導**方針：不適用;這項建議適用于在計算資源上執行的應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制使用者與 Azure Resource Manager 互動的能力

**指引**：使用 Azure 條件式存取，藉由對「Microsoft Azure 管理」應用程式設定「封鎖存取」，以限制使用者與 Azure Resource Manager 互動的能力。

* [如何設定條件式存取以封鎖 Azure Resource Manager 的存取](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制使用者在計算資源內執行指令碼的能力

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以實體或邏輯方式隔離高風險的應用程式

**指導**方針：任何與您的 Synapse SQL 集區相關的資源，不論是商務營運所需的資源，但可能會對組織產生更高的風險，應該隔離在自己的虛擬機器和（或）虛擬網路內，並使用 Azure 防火牆或網路安全性群組來充分保護。

* [如何建立虛擬網路](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [如何使用安全性設定建立 NSG](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱[安全性控制：安全設定](/azure/security/benchmarks/security-control-secure-configuration)。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導**方針：在 "Synapse" 命名空間中使用 Azure 原則別名來建立自訂原則，以審核或強制設定與 Sql 集區相關的資源。 您也可以使用 Azure 資料庫/伺服器的內建原則定義，例如：
- 在 SQL 伺服器上部署威脅偵測
- SQL Server 應該使用虛擬網路服務端點

* [如何檢視可用的 Azure 原則別名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的作業系統設定

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指引**：使用 Azure 原則 [拒絕] 和 [在不存在時部署]，以對 Azure 資源強制使用安全設定。

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [瞭解 Azure 原則效果](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：維護安全的作業系統設定

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導**方針：如果使用自訂 Azure 原則定義，請使用 Azure DevOps 或 Azure Repos 安全地儲存和管理您的程式碼。

* [如何在 Azure DevOps 中儲存程式碼](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos 文件](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂作業系統映像

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署適用于 Azure 資源的設定管理工具

**指導**方針：不適用;Azure Synapse SQL 沒有可設定的安全性設定。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的設定管理工具

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：執行 Azure 資源的自動化設定監視

**指導**方針：利用 Azure 資訊安全中心針對任何與您 Synapse SQL 集區相關的資源執行基準掃描。

* [如何修復 Azure 資訊安全中心中的建議](https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統實作自動化的設定監視

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 祕密

**指導**方針：在 Azure Key Vault 中透明資料加密 (TDE) 與客戶管理的金鑰，可讓您使用稱為 TDE 保護裝置的客戶管理非對稱金鑰，將自動產生的資料庫加密金鑰加密 (DEK) 。 這通常也稱為透明資料加密的攜帶您自己的金鑰 (BYOK) 支援。 在 BYOK 案例中，TDE 保護裝置會儲存在客戶擁有和受控 Azure Key Vault 中。 此外，請確定已在 Azure Key Vault 中啟用虛刪除。

* [如何從 Azure Key Vault 使用客戶管理的金鑰啟用 TDE](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-byok-configure?tabs=azure-powershell)

* [如何在 Azure Key Vault 中啟用虛刪除](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

**指導**方針：使用受控識別，在 AZURE ACTIVE DIRECTORY (AD) 中，為 Azure 服務提供自動管理的身分識別。 受控識別可讓您向任何支援 Azure AD 驗證的服務進行驗證，包括 Azure Key Vault，而不需要在您的程式碼中提供任何認證。

* [教學課程：使用 Windows VM 系統指派的受控識別來存取 Azure SQL](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql)

* [如何設定受控識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指導**方針：執行認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。

* [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*如需詳細資訊，請參閱[安全性控制：惡意程式碼防禦](/azure/security/benchmarks/security-control-malware-defense)。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反惡意程式碼軟體

**指引**：不適用，這項建議主要用於計算資源。 Microsoft 會處理基礎平臺的反惡意程式碼。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指導**方針：支援 azure 服務 (基礎主機上已啟用 Microsoft 反惡意程式碼，例如 AZURE Synapse SQL) ;不過，它不會在客戶內容上執行。

預先掃描即將上傳至非計算 Azure 資源的任何內容，例如 App Service、Data Lake Storage、Blob 儲存體、Azure SQL Server 等等。Microsoft 無法在這些實例中存取您的資料。

* [瞭解 Azure 雲端服務和虛擬機器的 Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3：確保更新反惡意程式碼軟體和簽章

**指引**：不適用，這項建議主要用於計算資源。 Microsoft 會處理基礎平臺的反惡意程式碼。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱[安全性控制：資料復原](/azure/security/benchmarks/security-control-data-recovery)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確定定期自動備份

**指導**方針： Synapse SQL 集區的快照集會在一天內自動執行，並建立可供七天使用的還原點。 此保留期無法變更。 SQL 集區支援八小時的復原點目標 (RPO) 。 您可以透過過去七天內所建立的任一個快照集，在主要區域中還原資料倉儲。 請注意，您也可以視需要手動觸發快照集。

* [Azure Synapse SQL 集區中的備份與還原](/azure/synapse-analytics/sql-data-warehouse/backup-and-restore)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份並備份任何客戶管理的金鑰

**指導**方針：您的資料倉儲快照集會在一天內自動進行，並建立可供七天使用的還原點。 此保留期無法變更。 SQL 集區支援八小時的復原點目標 (RPO) 。 您可以透過過去七天內所建立的任一個快照集，在主要區域中還原資料倉儲。 請注意，您也可以視需要手動觸發快照集。

如果您使用客戶管理的金鑰來加密資料庫加密金鑰，請確定您的金鑰已備份。

* [Azure Synapse SQL 集區中的備份與還原](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/backup-and-restore)

* [如何備份 Azure Key Vault 金鑰](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指導**方針：定期測試您的還原點，以確保您的快照集有效。 若要從還原點還原現有的 SQL 集區，您可以使用 Azure 入口網站或 PowerShell。 測試已備份之客戶管理金鑰的還原。

* [如何還原 Azure Key Vault 金鑰](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [Azure Synapse SQL 集區中的備份與還原](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/backup-and-restore)

* [如何還原現有的 SQL 集區](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-restore-active-paused-dw)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰的保護

**指導**方針：在 Azure SQL Database 中，您可以使用長期備份保留原則來設定單一或集區資料庫 (LTR) 以自動將資料庫備份保留在不同的 Azure Blob 儲存體容器中，最多10年的時間。 Azure 儲存體中的資料會以透明的方式使用256位 AES 加密（可用的最強區塊編碼器之一）進行加密和解密，並符合 FIPS 140-2 規範。

根據預設，儲存體帳戶中的資料會使用 Microsoft 管理的金鑰進行加密。 您可以依賴 Microsoft 管理的金鑰來加密您的資料，也可以使用您自己的金鑰管理加密。 如果您要使用 Key Vault 管理您自己的金鑰，請確定已啟用虛刪除。

* [管理 Azure SQL Database 長期備份保留](https://docs.microsoft.com/azure/sql-database/sql-database-long-term-backup-retention-configure)

* [待用資料的 Azure 儲存體加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

* [如何在 Key Vault 中啟用虛刪除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure 資訊安全中心監視**：N/A

**責任**：客戶

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱[安全性控制：事件回應](/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指導**方針：確定有撰寫的事件回應計畫，可定義人員角色以及事件處理/管理階段。

* [如何設定 Azure 資訊安全中心內的工作流程自動化](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序

**指導**方針：資訊安全中心會將嚴重性指派給警示，以協助您優先處理每個警示的順序，如此一來，當資源遭到入侵時，您就可以立即取得。 嚴重性的依據，在於資訊安全中心對於據以發出警示的發現結果或分析結果有多少信心，以及認定導致警示的活動背後存在惡意意圖的把握程度。

* [Azure 資訊安全中心的安全性警示](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指引**：進行練習以定期測試系統的事件回應功能。 找出弱點和落差，並視需要修訂計畫。

* [您可以參考 NIST 的發行集：適用于 IT 方案和功能的測試、訓練和練習程式指南](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引**：如果 Microsoft 安全性回應中心 (MSRC) 發現您的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡資訊來連絡您。

* [如何設定 Azure 資訊安全中心的安全性連絡人](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指引**：使用「連續匯出」功能來匯出 Azure 資訊安全中心的警示和建議。 「連續匯出」可供以手動或持續不斷的方式來匯出警示和建議。 您可以使用 Azure 資訊安全中心資料連線器，將警示串流至 Sentinel。

* [如何設定連續匯出](https://docs.microsoft.com/azure/security-center/continuous-export)

* [如何將警示串流至 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指引**：利用 Azure 資訊安全中心的工作流程自動化功能，透過「Logic Apps」自動觸發對安全性警示和建議的回應。

* [如何設定工作流程自動化和 Logic Apps](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*如需詳細資訊，請參閱[安全性控制：滲透測試和 Red Team 練習](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：進行 Azure 資源的定期滲透測試，並確保修復所有重要的安全性結果

**指引**： * [請遵循 Microsoft 參與規則，確保您的滲透測試不會違反 Microsoft 原則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.)

* [對於 Microsoft 管理的雲端基礎結構、服務和應用程式，您可在此找到 Microsoft 對於紅隊演練(Red Teaming) 和即時網站滲透測試的策略與執行詳細資訊：](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](/azure/security/benchmarks/overview)
- 深入了解 [Azure 資訊安全性基準](/azure/security/benchmarks/security-baselines-overview)

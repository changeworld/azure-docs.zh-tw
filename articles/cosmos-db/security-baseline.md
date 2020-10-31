---
title: 適用于 Cosmos DB 的 Azure 安全性基準
description: 適用于 Cosmos DB 的 Azure 安全性基準
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 21e69cf8d90f004d397778c2eeb6b5f774196574
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098734"
---
# <a name="azure-security-baseline-for-cosmos-db"></a>適用于 Cosmos DB 的 Azure 安全性基準
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

適用于 Cosmos DB 的 Azure 安全性基準包含可協助您改善部署安全性狀態的建議。

此服務的基準取自 [Azure 安全性效能評定 1.0 版](../security/benchmarks/overview.md)，其會提供如何在 Azure 上使用最佳做法指引來保護雲端解決方案的建議。

如需詳細資訊，請參閱 [Azure 安全性基準概觀](../security/benchmarks/security-baselines-overview.md) (機器翻譯)。

## <a name="network-security"></a>網路安全性

如需詳細資訊，請參閱[安全性控制：網路安全性](../security/benchmarks/security-control-network-security.md) (機器翻譯)。

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：在虛擬網路上使用網路安全性群組或 Azure 防火牆來保護資源

**指導** 方針：藉由使用 Azure Private Link，您可以透過私人端點連接至 Azure Cosmos 帳戶。 虛擬網路和服務間的流量會在通過 Microsoft 骨幹網路時隨之減少，降低資料在網際網路中公開的風險。 您也可以在網路安全性群組上設定一組嚴格的輸出規則 (NSG) ，並將該 NSG 與您的子網產生關聯，以降低資料遭到外泄的風險。

您也可以使用服務端點來保護您的 Azure Cosmos 帳戶。 藉由啟用服務端點，您可以設定 Azure Cosmos 帳戶，以允許從 Azure 虛擬網路的特定子網進行存取。 一旦啟用 Azure Cosmos DB 服務端點，您就可以使用來自虛擬網路子網的連線來限制對 Azure Cosmos 帳戶的存取。

您也可以使用 IP 防火牆來保護 Azure Cosmos 帳戶中儲存的資料。 Azure Cosmos DB 支援 IP 型存取控制，以提供輸入防火牆支援。 您可以使用 Azure 入口網站、Azure Resource Manager 範本，或透過 Azure CLI 或 Azure PowerShell，在 Azure Cosmos 帳戶上設定 IP 防火牆。

Azure Private Link 總覽： https://docs.microsoft.com/azure/private-link/private-link-overview

如何設定 Azure Cosmos DB 的私人端點：  https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints 

如何使用安全性設定建立網路安全性群組：  https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

如何在 Cosmos DB 中設定 IP 防火牆： https://docs.microsoft.com/azure/cosmos-db/how-to-configure-firewall

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2：監視和記錄 VNet、子網路與 NIC 的設定和流量

**指導** 方針：使用 Azure 資訊安全中心，並遵循網路保護建議來協助保護與您 Azure Cosmos 帳戶相關的網路資源。

當虛擬機器部署在與您 Azure Cosmos 帳戶相同的虛擬網路時，您可以使用網路安全性群組 (NSG) ，以降低資料遭到外泄的風險。 啟用 NSG 流量記錄，並將記錄傳送至 Azure 儲存體帳戶以進行流量審核。 您也可將 NSG 流量記錄傳送到 Log Analytics 工作區，並使用流量分析來提供對 Azure 雲端流量的見解。 流量分析的優點包括能將網路活動視覺化並找出作用點、識別安全性威脅、了解流量模式並找到錯誤的網路設定。

瞭解 Azure 資訊安全中心所提供的網路安全性： https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

如何啟用 NSG 流量記錄： https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

如何啟用及使用流量分析： https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 Web 應用程式

**指導** 方針：使用跨原始資源分享 (CORS) 功能，讓在一個網域下執行的 web 應用程式能夠存取另一個網域中的資源。 網頁瀏覽器會實作稱為相同原始原則的安全性限制，它可防止網頁呼叫不同網域中的 API。 不過，CORS 提供了安全的方式來允許來源網域呼叫其他網域中的 API。 在為 Azure Cosmos 帳戶啟用 CORS 支援後，就只會對已通過驗證的要求進行評估，以根據您指定的規則來判斷是否允許這些要求。

設定跨原始來源資源分享： https://docs.microsoft.com/azure/cosmos-db/how-to-configure-cross-origin-resource-sharing

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址的通訊

**指導** 方針：使用 Advanced 威脅防護 (ATP) 進行 Azure Cosmos DB。 Azure Cosmos DB 的 ATP 提供額外一層的安全性情報，可偵測不尋常且可能有害的存取或惡意探索 Azure Cosmos 帳戶的嘗試。 這一層保護可讓您解決威脅，並將其與中央安全性監視系統整合。

在與您的 Azure Cosmos DB 實例相關聯的虛擬網路上啟用 DDoS 保護標準，以防止 DDoS 攻擊。 使用 Azure 資訊安全中心的整合式威脅情報，以拒絕與已知為惡意或未使用的網際網路 IP 位址通訊。

如何設定 Azure Cosmos DB Advanced 威脅防護： https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

如何設定 DDoS 保護： https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

瞭解 Azure 資訊安全中心整合威脅情報： https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="15-record-network-packets-and-flow-logs"></a>1.5：記錄網路封包和流量記錄

**指導** 方針：啟用網路安全性群組 (NSG) 流量記錄，並將記錄傳送至儲存體帳戶以進行流量審核。 您可以將 NSG 流量記錄傳送至 Log Analytics 工作區，並使用「流量分析」來提供 Azure 雲端中流量流程的見解。 流量分析的優點包括能將網路活動視覺化並找出作用點、識別安全性威脅、了解流量模式並找到錯誤的網路設定。

如何啟用 NSG 流量記錄： https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

如何啟用及使用流量分析： https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署網路型入侵偵測/入侵預防系統 (IDS/IPS)

**指導** 方針：使用 Advanced 威脅防護 (ATP) 進行 Azure Cosmos DB。 Azure Cosmos DB 的 ATP 提供額外一層的安全性情報，可偵測不尋常且可能有害的存取或惡意探索 Azure Cosmos 帳戶的嘗試。 這一層保護可讓您解決威脅，並將其與中央安全性監視系統整合。 

如何設定 Cosmos DB Advanced 威脅防護： https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理 Web 應用程式的流量

**指導** 方針：不適用;建議適用于在 Azure App Service 或計算資源上執行的 web 應用程式。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負擔降至最低

**指導** 方針：對於需要存取您 Azure Cosmos 帳戶的資源，請使用虛擬網路服務標籤來定義網路安全性群組或 Azure 防火牆上的網路存取控制。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 藉由在規則的適當來源或目的地欄位中指定服務標記名稱 (例如，AzureCosmosDB) ，您可以允許或拒絕對應服務的流量。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。

使用服務標籤的詳細資訊： https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指導** 方針：使用 Azure 原則定義和執行網路資源的標準安全性設定。 使用 "Microsoft.DocumentDB" 和 "Microsoft. Network" 命名空間中 Azure 原則別名來建立自訂原則，以對 Azure Cosmos DB 實例的網路設定進行審核或強制執行。 您也可以使用 Azure Cosmos DB 的內建原則定義，例如：

- 為 Cosmos DB 帳戶部署進階威脅防護

- Cosmos DB 應該使用虛擬網路服務端點

您也可以使用 Azure 藍圖，在單一藍圖定義中封裝金鑰環境成品 (例如 Azure Resource Manager 範本、角色型存取控制 (RBAC) 和原則)，以簡化大規模的 Azure 部署。 您可以輕鬆地將藍圖套用至新的訂用帳戶、環境，以及透過版本控制來微調控制和管理。

如何設定和管理 Azure 原則： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何建立 Azure 藍圖： https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

**指導** 方針：針對與您 Azure Cosmos DB 部署相關聯的網路資源使用標籤，以便以邏輯方式將它們組織成分類法。

如何建立和使用標籤： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導** 方針：使用 Azure 活動記錄監視網路資源設定，並偵測與您的 Azure Cosmos DB 實例相關之網路資源的變更。 在 Azure 監視器中建立警示，以在重要網路資源發生變更時觸發。 

如何檢視及擷取 Azure 活動記錄事件： https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

如何在 Azure 監視器中建立警示： https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

如需詳細資訊，請參閱[安全性控制：記錄和監視](../security/benchmarks/security-control-logging-monitoring.md) (機器翻譯)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源

**指導** 方針： Microsoft 會維護用於 Azure 資源的時間來源，例如記錄中時間戳記的 Azure Cosmos DB。


**Azure 資訊安全中心監視** ：不適用

**責任** ：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導** 方針：透過 Azure 監視器內嵌記錄，以匯總 Azure Cosmos DB 所產生的安全性資料。 在 Azure 監視器中，使用 Log Analytics 工作區 (s) 來查詢和執行分析，並使用儲存體帳戶來儲存長期/封存儲存體。 或者，您可以啟用 Azure Sentinel 或第三方安全性事件和事件管理 (SIEM)，然後使資料上線。 

如何啟用 Azure Cosmos DB 的診斷記錄： https://docs.microsoft.com/azure/cosmos-db/logging

如何讓 Azure Sentinel 上線： https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指導** 方針：啟用 Azure Cosmos DB 的診斷設定，並將記錄傳送至 Log Analytics 工作區或儲存體帳戶。 Azure Cosmos DB 中的診斷設定會用來收集資源記錄。 每個要求都會捕獲這些記錄，而且它們也稱為「資料平面記錄」。 資料平面作業的一些範例包括刪除、插入和讀取。 您也可以啟用 Azure 活動記錄診斷設定，並將其傳送至相同的 Log Analytics 工作區。

如何啟用 Azure Cosmos DB 的診斷設定： https://docs.microsoft.com/azure/cosmos-db/logging

如何啟用 Azure 活動記錄的診斷設定： https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：從作業系統收集安全性記錄

**指引** ：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指導** 方針：在 Azure 監視器中，根據您組織的合規性法規，設定與您 Azure Cosmos DB 實例相關聯的 log Analytics 工作區記錄保留期限。

如何設定記錄保留參數： https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和檢閱記錄

**指導** 方針：您可以在 Log Analytics 中執行查詢，以搜尋詞彙、識別趨勢、分析模式，以及根據您收集的 Azure Cosmos DB 記錄提供許多其他見解。

如何針對 Log Analytics 工作區中的 Azure Cosmos DB 執行查詢： https://docs.microsoft.com/azure/cosmos-db/monitor-cosmos-db

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：啟用異常活動的警示

**指導** 方針：在 Azure 資訊安全中心中，啟用 Azure Cosmos DB 的 Advanced 威脅防護，以監視安全性記錄檔和事件中的異常活動。 啟用 Azure Cosmos DB 中的診斷設定，並將記錄傳送至 Log Analytics 工作區。

 

您也可以將 Log Analytics 工作區上架到 Azure Sentinel，因為它提供安全性協調流程自動化回應 (SOAR) 解決方案。 如此可建立劇本 (自動化解決方案)，並用於修復安全性問題。 此外，您可以在 Log Analytics 工作區中使用 Azure 監視器建立自訂記錄警示。

Azure Cosmos DB 的威脅防護警示清單： https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos

如何讓 Azure Sentinel 上線： https://docs.microsoft.com/azure/sentinel/quickstart-onboard

使用 Azure 監視器建立、查看和記錄管理警示： https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中化反惡意程式碼記錄

**指導** 方針：不適用;Azure Cosmos DB 不會處理或產生反惡意程式碼的相關記錄。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄

**指導** 方針：不適用;Azure Cosmos DB 不會處理或產生 DNS 相關的記錄。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列稽核記錄

**指引** ：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

## <a name="identity-and-access-control"></a>身分識別與存取控制

如需詳細資訊，請參閱[安全性控制：身分識別與存取控制](../security/benchmarks/security-control-identity-access-control.md) (機器翻譯)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的清查

**指導** 方針：您可以使用 Azure 入口網站中的身分識別和存取控制 (IAM) 窗格來設定角色型存取控制 (RBAC) ，以及維護 Azure Cosmos DB 資源上的清查。 角色會套用至 Active Directory 中的使用者、群組、服務主體和受控識別。 您可以針對個人和群組使用內建角色或自訂角色。

Azure Cosmos DB 針對 Azure Cosmos DB 中的常見管理案例提供內建的 RBAC。 在 Azure Active Directory (AD) 中具有設定檔的個人可以將這些 Azure 角色指派給使用者、群組、服務主體或受控識別，以授與或拒絕存取 Azure Cosmos DB 資源的資源和作業。

您也可以使用 Azure AD PowerShell 模組來執行臨機操作查詢，以探索屬於系統管理群組成員的帳戶。 

此外，Azure Cosmos DB 中的某些動作可以透過 Azure Active Directory 和帳戶特定的主鍵來控制。  使用 ' disableKeyBasedMetadataWriteAccess ' 帳戶設定來控制金鑰存取權。

瞭解 Azure Cosmos DB 中的角色型存取控制： https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

使用 Azure Cosmos DB 動作 ( # B0 umentDB 命名空間) 來建立您自己的自訂角色： https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdocumentdb

在 Azure Active Directory 中建立新角色： https://docs.microsoft.com/azure/role-based-access-control/custom-roles

如何使用 PowerShell 在 Azure Active Directory 中取得目錄角色： https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&preserve-view=true

如何使用 PowerShell 在 Azure Active Directory 中取得目錄角色的成員： https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&preserve-view=true

限制使用者只能存取資料作業： https://docs.microsoft.com/azure/cosmos-db/how-to-restrict-user-data

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在適用的情況下變更預設密碼

**指導** 方針：預設或空白密碼的概念不會與 Azure AD 或 Azure Cosmos DB 存在。 相反地，Azure Cosmos DB 會使用兩種類型的金鑰來驗證使用者，並提供其資料和資源的存取權;主鍵和資源權杖。 您可以隨時重新產生金鑰。

瞭解 Azure Cosmos DB 中資料的安全存取： https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data

如何重新產生 Azure Cosmos DB 金鑰： https://docs.microsoft.com/azure/cosmos-db/manage-with-powershell#regenerate-keys

如何使用 Azure Active Directory 以程式設計方式存取金鑰： https://docs.microsoft.com/azure/cosmos-db/certificate-based-authentication

**Azure 資訊安全中心監視** ：不適用

**責任** ：共用

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指導** 方針：不適用;Azure Cosmos DB 不支援系統管理員帳戶。  所有存取都與 Azure Active Directory 和 Azure 角色型存取控制整合 (Azure RBAC) 。



**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用單一登入 (SSO) 搭配 Azure Active Directory

**指導** 方針： Azure Cosmos DB 會使用兩種類型的金鑰來授權使用者，而且不支援在資料平面層級 (SSO) 單一 Sign-On。 Cosmos DB 的控制平面存取權可透過 REST API 取得，並支援 SSO。 若要驗證，請將要求的授權標頭設定為從 Azure Active Directory 取得的 JSON Web 權杖。

瞭解適用于 Cosmos DB REST API 的 Azure 資料庫： https://docs.microsoft.com/rest/api/cosmos-db/

瞭解具有 Azure Active Directory 的 SSO： https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：針對所有以 Azure Active Directory 為基礎的存取使用多重要素驗證

**指導** 方針：啟用 Azure Active Directory Multi-Factor Authentication，並遵循 Azure 資訊安全中心身分識別和存取管理建議。

如何在 Azure 中啟用 MFA： https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

如何在 Azure 資訊安全中心監視身分識別與存取： https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦 (特殊權限存取工作站) 進行所有系統管理工作

**指導** 方針：使用特殊許可權存取工作站 (PAW) ，並將 Multi-Factor Authentication 設定為登入和設定 Azure 資源。

了解特殊權限存取工作站： https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

如何在 Azure 中啟用 MFA： https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7：針對來自系統管理帳戶的可疑活動留下記錄和發出警示

**指導** 方針：使用 Advanced 威脅防護 (ATP) 進行 Azure Cosmos DB。 Azure Cosmos DB 的 ATP 提供額外一層的安全性情報，可偵測不尋常且可能有害的存取或惡意探索 Azure Cosmos 帳戶的嘗試。 這一層保護可讓您解決威脅，並將其與中央安全性監視系統整合。 

此外，您也可以在環境中發生可疑或不安全的活動時，使用 Azure Active Directory (AD) Privileged Identity Management (PIM) 來產生記錄和警示。

使用 Azure AD 風險偵測來檢視風險性使用者行為的相關警示和報告。

如何部署 Privileged Identity Management (PIM)： https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

了解 Azure AD 風險偵測： https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指導** 方針：設定條件式存取原則的位置條件，以及管理您的命名位置。 使用命名位置時，您可以建立 IP 位址範圍或國家/地區和區域的邏輯分組。 您可以將敏感資源的存取權（例如 Azure Cosmos DB 實例）限制為已設定的命名位置。

如何在 Azure 中設定具名位置： https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指引** ：使用 Azure Active Directory (AD) 作為中央驗證和授權系統。 Azure AD 會對待用資料和傳輸中資料使用增強式加密，以保護資料安全。 Azure AD 也會對使用者認證進行 Salt 處理、雜湊處理並安全儲存資料。

如何建立和設定 Azure Active Directory 實例： https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

如何使用 Azure SQL 設定及管理 Azure Active Directory authentication： https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並調節使用者存取權

**指引** ：Azure Active Directory 會提供記錄來協助探索過時的帳戶。 此外，您可以使用 Azure 身分識別存取審核來有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 您可以定期審核使用者的存取權，以確保只有適當的使用者可以繼續存取。

如何使用 Azure 身分識別存取權檢閱： https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11：監視對已停用帳戶的存取嘗試

**指導** 方針：您可以建立 Azure Active Directory 使用者帳戶的診斷設定，將審核記錄和登入記錄傳送到 Log Analytics 工作區，您可以在其中設定所需的警示。

如何將 Azure 活動記錄整合到 Azure 監視器中： https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帳戶登入行為偏差警示

**指導** 方針：使用 Advanced 威脅防護 (ATP) 進行 Azure Cosmos DB。 Azure Cosmos DB 的 ATP 提供額外一層的安全性情報，可偵測不尋常且可能有害的存取或惡意探索 Azure Cosmos 帳戶的嘗試。 這一層保護可讓您解決威脅，並將其與中央安全性監視系統整合。 

您也可以使用 Azure AD Identity Protection 和風險偵測功能，對偵測到與使用者身分識別相關的可疑動作，設定自動回應。 此外，您可以將記錄內嵌至 Azure Sentinel，以供進一步調查。

如何查看 Azure Active Directory 具風險的登入： https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

如何設定及啟用 Identity Protection 風險原則： https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

如何讓 Azure Sentinel 上線： https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支援案例期間為 Microsoft 提供相關客戶資料的存取權

**指導** 方針：目前無法使用;Azure Database for Cosmos DB 尚不支援客戶加密箱。

客戶加密箱支援服務清單： https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：不適用

## <a name="data-protection"></a>資料保護

如需詳細資訊，請參閱[安全性控制：資料保護](../security/benchmarks/security-control-data-protection.md) (機器翻譯)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的清查

**指導** 方針：使用標記來協助追蹤儲存或處理機密資訊 Azure Cosmos DB 實例。

如何建立和使用標籤： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指引** ：針對開發、測試和生產，實作不同的訂用帳戶及/或管理群組。 Azure Cosmos DB 實例會以虛擬網路/子網分隔、適當標記，並在網路安全性群組內受到保護， (NSG) 或 Azure 防火牆。 儲存機密資料的 Azure Cosmos DB 實例應予以隔離。 藉由使用 Azure Private Link，您可以透過私人端點連接至 Azure Cosmos DB 實例帳戶。 私人端點是虛擬網路內子網路中的一組私人 IP 位址。 然後，您可以限制對選取的私人 IP 位址的存取。 

如何建立其他 Azure 訂用帳戶： https://docs.microsoft.com/azure/billing/billing-create-subscription

如何建立管理群組： https://docs.microsoft.com/azure/governance/management-groups/create

如何建立和使用標籤： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

如何設定 Azure Cosmos DB 的私人端點： https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints

如何使用安全性設定建立網路安全性群組： https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的敏感性資訊傳輸

**指導** 方針：您可以部署 Azure Cosmos DB 的 Advanced 威脅防護，這會偵測異常活動，指出有不尋常且可能有害的嘗試存取或惡意探索資料庫。 這些活動目前會觸發下列警示：

- 從不尋常的位置存取

- 不尋常的資料解壓縮

此外，使用虛擬機器存取您的 Azure Cosmos DB 實例時，請使用 Private Link、防火牆、網路安全性群組和服務標籤來減輕資料遭到外泄的可能性。 Microsoft 管理 Azure Cosmos DB 的基礎結構，並已實行嚴格的控制，以防止客戶資料遺失或洩漏。

如何設定 Cosmos DB Advanced 威脅防護： https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

了解 Azure 中的客戶資料保護： https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 資訊安全中心監視** ：是

**責任** ：共用

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導** 方針： Azure Cosmos DB 的所有連接都支援 HTTPS。 Azure Cosmos DB 也支援 TLS 1.2。 您可以強制執行最低的 TLS 版本伺服器端。 若要這樣做，請聯絡 [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com) 。

Cosmos DB 安全性的總覽： https://docs.microsoft.com/azure/cosmos-db/database-security

**Azure 資訊安全中心監視** ：不適用

**責任** ：Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

**指導** 方針： Azure Cosmos DB 還無法使用自動資料識別、分類和遺失防護功能。 不過，您可以使用 Azure 認知搜尋整合進行分類和資料分析。 您也可以視需要執行協力廠商解決方案，以符合合規性需求。

針對 Microsoft 管理的基礎平台，Microsoft 會將所有客戶內容視為敏感性資訊，並竭盡全力防範客戶資料外洩和暴露。 為確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制和功能。

使用 Azure 認知搜尋來編制 Azure Cosmos DB 資料的索引： https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb?toc=/azure/cosmos-db/toc.json&amp ; bc =/azure/cosmos-db/breadcrumb/toc.json

了解 Azure 中的客戶資料保護： https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：共用

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 來控制資源的存取權

**指導** 方針： Azure Cosmos DB 針對 Azure Cosmos DB 中常見的管理案例，提供內建的角色型存取控制 (RBAC) 。 在 Azure Active Directory 中具有設定檔的個人可以將這些 Azure 角色指派給使用者、群組、服務主體或受控識別，以授與或拒絕對 Azure Cosmos DB 資源的資源和作業的存取權。 角色指派的範圍僅限於控制平面存取，其中包括存取 Azure Cosmos 帳戶、資料庫、容器，以及 (輸送量) 的優惠。

如何在 Azure Cosmos DB 中執行 RBAC： https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用主機型資料外洩防護來強制執行存取控制

**指引** ：不適用，這項準則主要用於計算資源。

Microsoft 管理 Cosmos DB 的基礎結構，並已實行嚴格的控制，以防止客戶資料遺失或洩漏。

了解 Azure 中的客戶資料保護： https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指導** 方針：儲存在 Cosmos DB 中的所有使用者資料預設都會加密。 沒有可關閉的控制項。 Azure Cosmos DB 在帳戶執行所在的所有區域中都會使用 AES-256 加密。

根據預設，Microsoft 會管理用來加密 Azure Cosmos 帳戶中資料的金鑰。 您可以選擇性地選擇使用您自己的金鑰來新增第二層的加密。

使用 Azure Cosmos DB 瞭解待用加密： https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

瞭解使用 Azure Cosmos DB 進行靜態加密的金鑰管理： https://docs.microsoft.com/azure/cosmos-db/cosmos-db-security-controls

如何為您的 Azure Cosmos DB 帳戶設定客戶管理的金鑰： https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk

**Azure 資訊安全中心監視** ：不適用

**責任** ：共用

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導** 方針：使用 Azure 監視器搭配 Azure 活動記錄檔，以建立 Azure Cosmos DB 生產實例的變更時所產生的警示。

如何建立 Azure 活動記錄事件的警示： https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

如何建立 Azure 活動記錄事件的警示： https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

## <a name="vulnerability-management"></a>弱點管理

如需詳細資訊，請參閱[安全性控制：弱點管理](../security/benchmarks/security-control-vulnerability-management.md) (機器翻譯)。

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具

**指導** 方針：請針對您 Azure Cosmos DB 實例的 Azure 資訊安全中心遵循建議。 

Microsoft 會在支援您 Azure Cosmos DB 實例的基礎主機上執行系統修補和弱點管理。 為確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制和功能。

Azure 資訊安全中心提供支援的功能： https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows

**Azure 資訊安全中心監視** ：是

**責任** ：共用

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化的作業系統修補程式管理解決方案

**指引** ：不適用，這項準則主要用於計算資源。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署自動化的第三方軟體修補程式管理解決方案

**指引** ：不適用，這項準則主要用於計算資源。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比較連續性弱點掃描

**指引** ：不適用，這項準則主要用於計算資源。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程序來排定所發現弱點的補救優先順序

**指引** ：不適用，這項準則主要用於計算資源。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

## <a name="inventory-and-asset-management"></a>清查和資產管理

如需詳細資訊，請參閱[安全性控制：清查和資產管理](../security/benchmarks/security-control-inventory-asset-management.md) (機器翻譯)。

### <a name="61-use-azure-asset-discovery"></a>6.1：使用 Azure 資產探索

**指導** 方針：使用 Azure 入口網站或 Azure Resource Graph 來探索 (不限於 Azure Cosmos DB 的所有資源，但也包括計算、其他儲存體、網路、埠及通訊協定等資源 )  () 中。  確定您的租使用者具有適當的許可權，而且能夠列舉訂用帳戶中的所有 Azure 訂用帳戶及資源。

雖然可透過 Resource Graph 探索傳統 Azure 資源，但強烈建議您從現在開始建立並使用 Azure Resource Manager 資源。

如何使用 Azure Resource Graph 建立查詢： https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

如何檢視 Azure 訂用帳戶： https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0&preserve-view=true

瞭解 Azure 角色型存取控制： https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指導** 方針：使用中繼資料將標記套用至您的 Azure Cosmos DB 實例和相關資源，以邏輯方式將它們組織成分類法。

如何建立和使用標籤： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

哪些 Azure Cosmos DB 資源支援標記： https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support#microsoftdocumentdb

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指導** 方針：使用標記、管理群組和個別訂用帳戶（如果適用）來組織和追蹤資產，包括但不限於 Azure Cosmos DB 資源。 請定期調節清查，並確保會及時刪除訂用帳戶中未經授權的資源。

如何建立其他 Azure 訂用帳戶： https://docs.microsoft.com/azure/billing/billing-create-subscription

如何建立管理群組： https://docs.microsoft.com/azure/governance/management-groups/create

如何建立和使用標籤： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：維護受核准 Azure 資源和軟體標題的清查

**指導** 方針：不適用;此指導方針適用于計算資源和整個 Azure。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指引** ：使用下列內建原則定義，以利用 Azure 原則對可在客戶訂用帳戶中建立的資源類型施加限制：

- 不允許的資源類型

- 允許的資源類型

此外，使用 Azure Resource Graph 來查詢/探索其訂用帳戶內的資源。

如何設定和管理 Azure 原則： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure Graph 建立查詢： https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：監視計算資源內未經核准的軟體應用程式

**指導** 方針：不適用;此基準適用于計算資源。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未經核准的 Azure 資源和軟體應用程式

**指導** 方針：不適用;此指導方針適用于計算資源和整個 Azure。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式

**指引** ：不適用，這項準則主要用於計算資源。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指引** ：使用下列內建原則定義，以利用 Azure 原則對可在客戶訂用帳戶中建立的資源類型施加限制：

- 不允許的資源類型 

- 允許的資源類型

如何設定和管理 Azure 原則： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure 原則拒絕特定的資源類型： https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="610-implement-approved-application-list"></a>6.10：實作已核准的應用程式清單

**指引** ：不適用，這項準則主要用於計算資源。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11：限制使用者透過腳本與 AzureResources Manager 互動的能力

**指引** ：使用 Azure 條件式存取，藉由設定「Microsoft Azure 管理」應用程式的「封鎖存取」，限制使用者與 Azure Resource Manager 互動的能力。 這可防止在高安全性環境中建立和變更資源。

如何設定條件式存取以封鎖 Azure Resource Manager 的存取： https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制使用者在計算資源內執行指令碼的能力

**指引** ：不適用，這項準則主要用於計算資源。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以實體或邏輯方式隔離高風險的應用程式

**指導** 方針：不適用;此指導方針適用于在 Azure App Service 或計算資源上執行的 web 應用程式。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

## <a name="secure-configuration"></a>安全設定

如需詳細資訊，請參閱[安全性控制：安全設定](../security/benchmarks/security-control-secure-configuration.md) (機器翻譯)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導** 方針：使用 Azure 原則定義和執行 Cosmos DB 實例的標準安全性設定。 使用 "Microsoft.DocumentDB" 命名空間中 Azure 原則別名來建立自訂原則，以對 Cosmos DB 實例的設定進行審核或強制執行。 您也可以使用 Azure Cosmos DB 的內建原則定義，例如：

- 為 Cosmos DB 帳戶部署進階威脅防護

- Cosmos DB 應該使用虛擬網路服務端點

如何檢視可用的 Azure 原則別名： https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0&preserve-view=true

如何設定和管理 Azure 原則： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的作業系統設定

**指引** ：不適用，這項準則主要用於計算資源。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指引** ：使用 Azure 原則 [拒絕] 和 [在不存在時部署]，以對 Azure 資源強制使用安全設定。

如何設定和管理 Azure 原則： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

了解 Azure 原則效果： https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：維護安全的作業系統設定

**指引** ：不適用，這項準則主要用於計算資源。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導** 方針：如果您 Cosmos DB 或相關資源使用自訂 Azure 原則定義，請使用 Azure Repos 安全地儲存和管理您的程式碼。

Azure Repos 文件： https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops&preserve-view=true https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂作業系統映像

**指引** ：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系統設定管理工具

**指導** 方針：使用 "Microsoft.DocumentDB" 命名空間中 Azure 原則別名來建立自訂原則，以警示、審核和強制執行系統組態。 此外，開發流程和管線以管理原則例外狀況。

如何設定和管理 Azure 原則： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的系統設定管理工具

**指引** ：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：為 Azure 服務實作自動化的設定監視

**指導** 方針：使用 "Microsoft.DocumentDB" 命名空間中 Azure 原則別名來建立自訂原則，以警示、審核和強制執行系統組態。 使用 Azure 原則 [audit]、[deny] 和 [deploy if not 存在]，自動強制執行 Azure Cosmos DB 實例和相關資源的設定。 

如何設定和管理 Azure 原則： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統實作自動化的設定監視

**指引** ：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 祕密

**指導** 方針：適用于在 Azure App Service 上執行的 Azure 虛擬機器或 web 應用程式可用來存取您的 Azure Cosmos DB 實例，請使用受控服務識別搭配 Azure Key Vault 以簡化 Azure Cosmos DB 秘密管理的安全。 確保已啟用 Key Vault 虛刪除。

如何與 Azure 受控識別整合： https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

如何建立 Key Vault： https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

如何驗證 Key Vault： https://docs.microsoft.com/azure/key-vault/general/authentication

如何指派 Key Vault 存取原則： https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

**指導** 方針：適用于在 Azure App Service 上執行的 Azure 虛擬機器或 web 應用程式可用來存取您的 Azure Cosmos DB 實例，請使用受控服務識別搭配 Azure Key Vault 以簡化 Azure Cosmos DB 秘密管理的安全。

使用受控識別，在 Azure Active Directory (AD) 中為 Azure 服務提供自動受控識別。 受控識別可供對支援 Azure AD 驗證的任何服務進行驗證 (包括 Key Vault)，不需要程式碼中的任何認證。

如何設定受控識別： https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

如何與 Azure 受控識別整合： https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指引** ：實作認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。

如何設定認證掃描器： https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

如需詳細資訊，請參閱[安全性控制：惡意程式碼防禦](../security/benchmarks/security-control-malware-defense.md) (機器翻譯)。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反惡意程式碼軟體

**指引** ：不適用，這項準則主要用於計算資源。 Microsoft Antimalware 在支援 Azure 服務的基礎主機上啟用 (例如 Azure App Service) ，不過它不會在客戶內容上執行。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指導** 方針： Microsoft Antimalware 會在支援 Azure 服務的基礎主機上啟用 (例如 Azure App Service) ，不過它不會在客戶內容上執行。

您必須負責預先掃描即將上傳至非計算 Azure 資源的任何檔案，包括 Azure Cosmos DB。 Microsoft 無法存取客戶資料，因此無法代表您執行客戶內容的反惡意程式碼掃描。


**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3：確保更新反惡意程式碼軟體和簽章

**指導** 方針：不適用;基準測試適用于計算資源。 支援 Azure 服務的基礎主機上已啟用 Microsoft Antimalware，但不會對客戶內容執行。


**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

## <a name="data-recovery"></a>資料復原

如需詳細資訊，請參閱[安全性控制：資料復原](../security/benchmarks/security-control-data-recovery.md) (機器翻譯)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確保會定期自動備份

**指導** 方針： Azure Cosmos DB 每四小時會取得資料的快照集。 所有備份會儲存在另外的儲存體服務中，而且系統會全域複寫這些備份，以便為區域性災害提供復原功能。 任何時候都只會保留最後兩個快照集。 不過，如果容器或資料庫已刪除，Azure Cosmos DB 只會將指定容器或資料庫的現有快照集保留 30 天。 請聯絡 Azure 支援以從備份還原。

瞭解 Azure Cosmos DB 自動備份： https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

**Azure 資訊安全中心監視** ：不適用

**責任** ：Microsoft

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份，並備份任何客戶管理的金鑰

**指導** 方針： Azure Cosmos DB 自動定期備份您的資料。 如果刪除了資料庫或容器，您可以提出支援票證或呼叫 Azure 支援，以從自動線上備份還原資料。 Azure 支援僅適用于所選的方案，例如標準、開發人員和方案高於這些方案。 若要還原特定的備份快照集，Azure Cosmos DB 需要該資料在該快照的備份週期持續時間內為可用狀態。 

如果使用 Key Vault 儲存 Cosmos DB 實例的認證，請務必定期自動備份您的金鑰。

瞭解 Azure Cosmos DB 自動備份： https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

如何還原 Azure Cosmos DB 中的資料： https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

如何備份 Key Vault 金鑰： https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：共用

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指導** 方針：如果刪除資料庫或容器，您可以提出支援票證或呼叫 Azure 支援，以從自動線上備份還原資料。 Azure 支援僅適用于所選的方案，例如標準、開發人員和方案高於這些方案。 若要還原特定的備份快照集，Azure Cosmos DB 需要該資料在該快照的備份週期持續時間內為可用狀態。

使用 PowerShell 來測試儲存在 Azure Key Vault 中的秘密還原。 Restore-AzureKeyVaultKey Cmdlet 會在指定的金鑰保存庫中建立金鑰。 此索引鍵是輸入檔中備份金鑰的複本，且名稱與原始金鑰相同。

瞭解 Azure Cosmos DB 自動備份：

https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

如何還原 Azure Cosmos DB 中的資料：

https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

如何還原 Azure Key Vault 秘密：

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0&preserve-view=true

**Azure 資訊安全中心監視** ：不適用

**責任** ：共用

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰受到保護

**指導** 方針：由於儲存在 Cosmos DB 中的所有使用者資料都會在待用和傳輸中加密，因此您不需要採取任何動作。 換句話說，待用加密預設便會「開啟」。 沒有關閉或開啟的控制項。 Azure Cosmos DB 在帳戶執行所在的所有區域中都會使用 AES-256 加密。

在 Key Vault 中啟用虛刪除，以防止金鑰遭到意外或惡意刪除。

瞭解 Azure Cosmos DB 中的資料加密： https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

如何在 Key Vault 中啟用 Soft-Delete： https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Azure 資訊安全中心監視** ：是

**責任** ：共用

## <a name="incident-response"></a>事件回應

如需詳細資訊，請參閱[安全性控制：事件回應](../security/benchmarks/security-control-incident-response.md) (機器翻譯)。

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指引** ：為組織製作事件回應指南。 請確定有書面的事件回應計畫，其中定義人員的所有角色，以及從偵測到事件後檢討的事件處理/管理階段。

您也可以利用 NIST 的電腦安全性性事件處理指南來協助建立您自己的事件回應計畫： https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

如何設定 Azure 資訊安全中心內的工作流程自動化： https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

建立自有安全性事件回應程序的指引： https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Microsoft 安全性回應中心的事件剖析： https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/


**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序

**指引** ：資訊安全中心會指派每個警示的嚴重性，以協助設定應優先調查哪些警示。 嚴重性取決於資訊安全中心的尋找方式，或用來發出警示的分析，以及導致警示的活動背後有惡意意圖的信賴等級。

此外，清楚地標示訂用帳戶 (例如 生產、非生產) 並建立命名系統，以清楚識別和分類 Azure 資源。

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指導** 方針：進行練習以定期測試系統的事件回應功能。 找出弱點和落差，並視需要修訂計畫。

請參閱 NIST 的出版物：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (IT 計畫和能力的測試、訓練和練習方案指南)： https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引** ：如果 Microsoft 安全性回應中心 (MSRC) 發現客戶的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡人資訊來連絡您。  事後檢事件，以確保問題已解決。

如何設定 Azure 資訊安全中心的安全性連絡人： https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指引** ：使用「連續匯出」功能來匯出 Azure 資訊安全中心的警示和建議。 「連續匯出」可供以手動或持續不斷的方式來匯出警示和建議。 您可使用 Azure 資訊安全中心的資料連接器，將警示串流至 Sentinel。

如何設定連續匯出： https://docs.microsoft.com/azure/security-center/continuous-export

如何將警示串流至 Azure Sentinel： https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指引** ：使用 Azure 資訊安全中心的工作流程自動化功能，以透過 "Logic Apps" 自動觸發對安全性警示和建議的回應。

如何設定工作流程自動化和 Logic Apps： https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

如需詳細資訊，請參閱[安全性控制：滲透測試和 Red Team 練習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md) (機器翻譯)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1：進行 Azure 資源的定期滲透測試，並確保在 60 天內補救所有重大安全性發現

**指引** ：遵循 Microsoft 參與規則，以確保滲透測試不會違反 Microsoft 原則： https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

您可以在以下位置找到 Microsoft 針對 Microsoft 管理的雲端基礎結構、服務和應用程式進行的 Microsoft 策略、Red 小組和即時網站滲透測試的詳細資訊：  https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure 資訊安全中心監視** ：不適用

**責任** ：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](../security/benchmarks/overview.md) (機器翻譯)
- 深入了解 [Azure 安全性基準](../security/benchmarks/security-baselines-overview.md) (機器翻譯)
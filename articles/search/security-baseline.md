---
title: 適用于 Azure 認知搜尋的 Azure 安全性基準
description: Azure 認知搜尋安全性基準提供的程式指引和資源，可讓您執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: search
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: f4782b923222208bbf759ba8415162621a55e0e1
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92631150"
---
# <a name="azure-security-baseline-for-azure-cognitive-search"></a>適用于 Azure 認知搜尋的 Azure 安全性基準

此安全性基準會將 [Azure 安全性基準測試版本 1.0](../security/benchmarks/overview.md) 的指引套用至 Azure 認知搜尋。 Azure 安全性基準提供如何在 Azure 上保護雲端解決方案的建議。 內容會依 Azure 安全性基準測試所定義的 **安全性控制** ，以及適用于 Azure 認知搜尋的相關指引來分組。 不適用 Azure 認知搜尋的 **控制項** ，或已排除的客戶。

若要查看 Azure 認知搜尋如何完全對應至 Azure 安全性基準測試，請參閱 [完整的 Azure 認知搜尋安全性基準對應](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)檔案。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱 [Azure 安全性基準測試：網路安全性](../security/benchmarks/security-control-network-security.md)。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保護虛擬網路內的 Azure 資源

**指導** 方針：確定所有 Microsoft Azure 虛擬網路子網部署都已套用具有規則的網路安全性群組，以實行「最低許可權」存取配置。 只允許存取您應用程式的受信任埠和 IP 位址範圍。 在可行的情況下，使用 Azure 私人端點部署 Azure 認知搜尋，以從您的虛擬網路啟用對您服務的私用存取。

認知搜尋也支援額外的網路安全性功能，以管理網路存取控制清單。 將您的搜尋服務設定為只允許與信任的來源進行通訊，方法是使用防火牆功能來限制特定公用 IP 位址範圍的存取。

- [如何設定 Azure 認知搜尋的私人端點](./service-create-private-endpoint.md)

- [如何設定 Azure 認知搜尋防火牆](./service-configure-firewall.md)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2：監視和記錄虛擬網路、子網和 Nic 的設定和流量

**指導** 方針：認知搜尋無法直接部署到虛擬網路中。 但是，如果您的用戶端應用程式或資料來源位於虛擬網路中，您可以監視並記錄這些網路內元件的流量，包括傳送至雲端中搜尋服務的要求。 標準建議包括啟用網路安全性群組流量記錄，以及將記錄傳送至 Azure 儲存體或 Log Analytics 工作區。 您可以選擇性地使用流量分析取得流量模式的見解。

- [如何啟用網路安全性群組流量記錄](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何啟用和使用流量分析](../network-watcher/traffic-analytics.md)

- [瞭解 Azure 資訊安全中心所提供的網路安全性](../security-center/security-center-network-recommendations.md)

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 Web 應用程式

**指導** 方針：不適用認知搜尋。 這項建議適用于在 Azure App Service 或計算資源上執行的 web 應用程式。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址通訊

**指導** 方針：認知搜尋不提供特定功能來對抗分散式阻絕服務攻擊，但您可以在與認知搜尋服務相關聯的虛擬網路上啟用 DDoS 保護標準，以進行一般保護。

- [如何設定 DDoS 保護](../virtual-network/manage-ddos-protection.md)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="15-record-network-packets"></a>1.5：記錄網路封包

**指導** 方針：啟用網路安全性群組的網路安全性群組流量記錄，以保護將連接至認知搜尋服務的 Azure 虛擬機器 (VM) 。 將記錄傳送至 Azure 儲存體帳戶以進行流量審核。 

如果需要調查異常活動，請啟用網路監看員封包捕獲。

- [如何啟用 NSG 流量記錄](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何啟用網路監看員](../network-watcher/network-watcher-create.md)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署以網路為基礎的入侵偵測/入侵防護系統 (IDS/IPS) 

**指導** 方針：認知搜尋不支援網路入侵偵測，但作為入侵緩和措施，您可以設定防火牆規則，以指定認知搜尋服務所接受的 IP 位址。 設定私人端點，讓搜尋流量遠離公用網際網路。

- [如何設定客戶管理的金鑰進行資料加密](./search-security-manage-encryption-keys.md)

- [如何從索引和同義字地圖取得客戶管理的金鑰資訊](./search-security-get-encryption-keys.md)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理 Web 應用程式的流量

**指導** 方針：不適用認知搜尋。 這項建議適用于在 Azure App Service 或計算資源上執行的 web 應用程式。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負擔降至最低

**指導** 方針：如果您要利用認知搜尋中的索引子和技能集，來代表有權連接到外部資源的 IP 位址範圍，請使用服務標記。 

藉由指定服務標籤名稱 (來允許或拒絕資源的流量，例如，在規則的適當來源或目的地欄位中 AzureCognitiveSearch) 。 

- [虛擬網路服務標籤](../virtual-network/service-tags-overview.md)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指導** 方針：認知搜尋不會依設計而依賴網路資源。 與您的搜尋應用程式相關的用戶端應用程式和資料來源可能位於虛擬網路上，但是搜尋服務本身不會部署在網路中。 

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

**指導** 方針：您可以使用 Azure 私人端點設定認知搜尋，以將您的搜尋服務與虛擬網路整合。  針對網路安全性群組和其他與網路安全性和流量相關的資源使用資源標記。 針對個別的網路安全性群組規則，請使用 [描述] 欄位來記錄允許流量進出網路的規則。 

使用任何與標記相關的內建 Azure 原則定義，例如「需要標籤和其值」效果，以確保所有資源都是使用標籤建立的，並通知您現有未標記的資源。 

您可以使用 Azure PowerShell 或 Azure CLI，根據其標記查閱或執行資源的動作。 

- [如何建立認知搜尋的私人端點](./service-create-private-endpoint.md)

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

- [如何建立 Azure 虛擬網路](../virtual-network/quick-create-portal.md)

- [如何使用網路安全性群組規則來篩選網路流量](../virtual-network/tutorial-filter-network-traffic.md)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導** 方針：認知搜尋沒有或依賴任何網路元件，所以無法監視這些資源的設定。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱 [Azure 安全性基準測試：記錄和監視](../security/benchmarks/security-control-logging-monitoring.md)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源

**指導** 方針：認知搜尋不支援設定您自己的時間同步處理來源。 搜尋服務依賴 Microsoft 時間同步處理來源，而不會向客戶公開進行設定。

**Azure 資訊安全中心監視** ：不適用

**責任** ：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導** 方針：透過 Azure 監視器內嵌與認知搜尋相關的記錄，以匯總端點裝置、網路資源和其他安全性系統所產生的安全性資料。 在 Azure 監視器中，請使用 Log Analytics 工作區來查詢和執行分析，並使用 Azure 儲存體帳戶來長期和封存儲存體。
或者，您可以將此資料啟用並在面板上 Azure Sentinel 或協力廠商 SIEM。

- [如何開始使用 Azure 監視器和第三方 SIEM 整合](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [如何使用 Azure 監視器收集平臺記錄和計量](../azure-monitor/platform/diagnostic-settings.md)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指導** 方針：診斷和作業記錄可讓您深入瞭解認知搜尋的詳細作業，而且適用于監視服務和存取服務的工作負載。  若要捕獲診斷資料，請指定儲存記錄資訊的位置來啟用記錄。

- [如何收集和分析 Azure 認知搜尋的記錄資料](./search-monitor-logs.md)

- [如何使用 Azure 監視器收集平臺記錄和計量](../azure-monitor/platform/diagnostic-settings.md) 

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：從作業系統收集安全性記錄

**指導** 方針：不適用認知搜尋。 這項建議適用于計算資源。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指導** 方針：認知搜尋預設會保留摘要至診斷計量的歷程記錄資料，並保留30天。 針對較長的保留，請務必啟用指定儲存選項的設定，以保存記錄的事件和計量。

在 Azure 監視器中，根據您組織的合規性法規來設定 Log Analytics 工作區保留期限。 使用 Azure 儲存體帳戶來取得長期和封存儲存體。 

- [變更 Log Analytics 中的資料保留期限](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [如何設定 Azure 儲存體客戶紀錄的保留原則](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和檢閱記錄

**指導** 方針：分析和監視認知搜尋服務的記錄，以瞭解異常行為。 使用 Azure 監視器的 Log Analytics 檢閱記錄，並對記錄資料執行查詢。 或者，您也可啟用 Azure Sentinel 或第三方 SIEM，並讓資料上線。

- [如何收集和分析認知搜尋的記錄資料](./search-monitor-logs.md)

- [如何在 Power BI 中將搜尋記錄資料視覺化](./search-monitor-logs-powerbi.md)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

- [了解 Log Analytics](../azure-monitor/log-query/get-started-portal.md)

- [如何在 Azure 監視器中執行自訂查詢](../azure-monitor/log-query/get-started-queries.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：啟用異常活動的警示

**指導** 方針：搭配使用資訊安全中心與 Log Analytics 工作區來監視和警示在安全性記錄和事件中找到的異常活動。 或者，您可以啟用和內部資料以 Azure Sentinel。

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

- [如何在 Azure 資訊安全中心中管理警示](../security-center/security-center-managing-and-responding-alerts.md)

- [如何對 log analytics 記錄資料發出警示](../azure-monitor/learn/tutorial-response.md)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中化反惡意程式碼記錄

**指導** 方針：不適用認知搜尋。 Microsoft 會管理基礎平臺的反惡意程式碼解決方案。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄

**指導** 方針：不適用認知搜尋。 它不會產生或使用 DNS 記錄。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列稽核記錄

**指導** 方針：不適用認知搜尋。 無法使用命令列審核進行認知搜尋。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱 [Azure 安全性基準測試：身分識別與存取控制](../security/benchmarks/security-control-identity-access-control.md)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的清查

**指導** 方針： azure 角色型存取控制 (azure RBAC) 可讓您透過角色指派來管理 Azure 資源的存取權。 您可以將這些角色指派給使用者、群組服務主體和受控識別。 某些資源有預先定義的內建角色，而這些角色可透過 Azure CLI、Azure PowerShell 或 Azure 入口網站等工具進行清查或查詢。

認知搜尋角色會與支援服務等級管理工作的許可權相關聯。  這些角色不會授與服務端點的存取權。 存取端點的作業， (例如索引管理、索引擴展和搜尋資料) 的查詢，請使用 API 金鑰來驗證要求。

- [設定 Azure 認知搜尋系統管理存取權的角色](./search-security-rbac.md)

- [建立及管理 Azure 認知搜尋服務的 api 金鑰](./search-security-api-keys.md)

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色](/powershell/module/azuread/get-azureaddirectoryrole)
- [如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在適用的情況下變更預設密碼

**指導** 方針：不適用認知搜尋。 它並沒有預設密碼的概念。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指導** 方針：認知搜尋沒有任何本機層級或 Azure Active Directory (Azure AD 可用來管理索引和作業的) 系統管理員帳戶的概念。 

使用必須明確指派以進行管理作業的 Azure AD 內建角色。 叫用 Azure AD PowerShell 模組來執行臨機操作查詢，以探索屬於系統管理群組成員的帳戶。

- [如何在認知搜尋中使用角色進行系統管理存取](./search-security-rbac.md)

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色](/powershell/module/azuread/get-azureaddirectoryrole)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用單一登入 (SSO) 搭配 Azure Active Directory

**指導** 方針：使用 SSO 驗證搭配 Azure Active Directory (Azure AD) ，以存取透過 Azure Resource Manager 支援的管理作業的搜尋服務資訊。 

使用您組織的預先存在身分識別，為服務啟用 SSO，以建立可減少身分識別和認證數目的程式。

- [瞭解 Azure AD 的 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：針對所有以 Azure Active Directory 為基礎的存取使用多重要素驗證

**指導** 方針：啟用 Azure Active Directory 的 (Azure AD) MULTI-FACTOR AUTHENTICATION (MFA) 功能，並遵循資訊安全中心的身分識別和存取建議。

- [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md) 

- [如何在 Azure 資訊安全中心監視身分識別和存取](../security-center/security-center-identity-access.md) 

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦 (特殊權限存取工作站) 進行所有系統管理工作

**指導** 方針：使用特殊許可權存取工作站 (PAW) ，MULTI-FACTOR AUTHENTICATION (MFA) 設定為登入和存取 Azure 資源。

- [瞭解安全、受 Azure 管理的工作站](../active-directory/devices/concept-azure-managed-workstation.md)
 

- [如何啟用 Azure AD MFA](../active-directory/authentication/howto-mfa-getstarted.md)
 

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：來自系統管理帳戶的可疑活動記錄和警示

**指導** 方針：使用 Azure Active Directory (Azure AD) 安全性報告和監視，來偵測環境中發生可疑或不安全的活動。 使用資訊安全中心來監視身分識別和存取活動。

- [如何識別已標示為有風險活動的 Azure AD 使用者](../active-directory/identity-protection/overview-identity-protection.md)

- [如何在 Azure 資訊安全中心監視使用者的身分識別和存取活動](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指導** 方針：不適用認知搜尋。 它不支援使用已核准的位置作為存取的條件。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導** 方針：使用 Azure Active Directory (Azure AD) 做為 Azure 認知搜尋中服務層級管理工作的中央驗證和授權系統。 Azure AD 身分識別不會授與搜尋服務端點的存取權。  您可以透過 API 金鑰存取索引管理、索引填入和搜尋資料查詢等作業。

- [如何建立和設定 Azure AD 實例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [建立及管理 Azure 認知搜尋服務的 api 金鑰](./search-security-api-keys.md)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指導** 方針： Azure Active Directory (Azure AD) 提供記錄以協助探索過時的帳戶。 使用 Azure AD 的身分識別和存取評論，有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 您可以定期檢查使用者存取權，以確定只有適當的使用者可以繼續存取。 

查看搜尋服務端點中活動的認知搜尋診斷記錄，例如索引管理、索引填入和查詢。

- [瞭解 Azure AD 報告](../active-directory/reports-monitoring/index.yml)

- [如何使用 Azure AD 身分識別和存取權評論](../active-directory/governance/access-reviews-overview.md)

- [監視 Azure 認知搜尋的作業和活動](./search-monitor-usage.md)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：監視嘗試存取已停用的認證

**指導** 方針：存取 Azure Active Directory (Azure AD) 登入活動、audit 和風險事件記錄檔來源，可讓您整合任何 SIEM 或監視工具。

建立 Azure AD 使用者帳戶的診斷設定，並將審核記錄和登入記錄傳送至 Log Analytics 工作區，以簡化此程式。 在 Log Analytics 工作區中設定所需的警示。

- [如何整合 Azure 活動記錄與 Azure 監視器](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) 

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帳戶登入行為偏差警示

**指導** 方針：使用 Azure Active Directory (Azure AD) Identity Protection 功能，以對偵測到與使用者身分識別相關的可疑動作設定自動回應。 視需要將資料內嵌到 Azure Sentinel 以進行進一步的調查。

- [如何檢視有風險的 Azure AD 登入](../active-directory/identity-protection/overview-identity-protection.md) 

- [如何設定和啟用身分識別保護風險原則](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md) 

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支援案例期間為 Microsoft 提供相關客戶資料的存取權

**指導** 方針：不適用認知搜尋。 客戶加密箱不支援認知搜尋。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資料保護](../security/benchmarks/security-control-data-protection.md)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的詳細目錄

**指引** ：使用標籤協助追蹤可儲存或處理敏感性資訊的 Azure 資源。

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指引** ：針對開發、測試和生產，實作不同的訂用帳戶及/或管理群組。 資源應以虛擬網路/子網分隔、正確標記，並在網路安全性群組或 Azure 防火牆內受到保護。 儲存或處理敏感性資料的資源應該是隔離的。 使用 Private Link 將私人端點設定為認知搜尋。

- [如何建立額外的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md) 

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

- [如何建立認知搜尋的私人端點](./service-create-private-endpoint.md)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的敏感性資訊傳輸

**指導** 方針：從網路周邊的 Azure Marketplace 使用協力廠商解決方案，以監視是否有未經授權的機密資訊傳輸，並在警示資訊安全專業人員時封鎖這類傳輸。

Microsoft 會管理基礎平臺，並將所有客戶內容視為機密資料，並防止客戶資料遺失和公開。 為了確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制項和功能。

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md) 

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導** 方針：認知搜尋會使用傳輸層安全性1.2 來加密傳輸中的資料，並在所有連線的任何時間都強制加密 (SSL/TLS) 。 這可確保在用戶端與服務之間「傳輸中」會加密所有資料。

- [瞭解 Azure 中的傳輸加密](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

**Azure 資訊安全中心監視** ：不適用

**責任** ：Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

**指導** 方針：認知搜尋尚無法使用資料識別、分類和遺失防護功能。 視需要執行協力廠商解決方案，以符合合規性需求。 

Microsoft 會管理基礎平臺，並將所有客戶內容視為機密資料，並防止客戶資料遺失和公開。 為了確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制項和功能。

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6：使用 Azure RBAC 來管理資源的存取權

**指導** 方針：若要進行服務管理，請使用 azure 角色型存取控制 (azure RBAC) 來管理金鑰和設定的存取權。 針對內容作業（例如編制索引和查詢），認知搜尋會使用金鑰，而不是以身分識別為基礎的存取控制模型。 使用 Azure RBAC 來控制金鑰的存取權。
- [如何在 Azure 中設定 RBAC](../role-based-access-control/role-assignments-portal.md) 

 
- [如何使用角色進行認知搜尋的系統管理存取](./search-security-rbac.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用主機型資料外洩防護來強制執行存取控制

**指導** 方針：不適用認知搜尋。 此指導方針適用于計算資源。 

Microsoft 會管理認知搜尋的基礎結構，並已實行嚴格的控制，以防止遺失或洩漏客戶資料。

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指導** 方針：認知搜尋會使用 Microsoft 管理的金鑰自動加密待用的索引內容。 如果需要更多保護，您可以使用您在 Azure Key Vault 中建立和管理的金鑰，以第二個加密層補充預設加密。

- [在 Azure 認知搜尋中設定客戶管理的金鑰進行資料加密](./search-security-manage-encryption-keys.md)

- [瞭解 Azure 中的待用加密](../security/fundamentals/encryption-atrest.md)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：共用

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導** 方針：使用 Azure 監視器搭配 Azure 活動記錄，以建立在認知搜尋的生產實例和其他重要或相關資源發生變更時的警示。

- [如何建立 Azure 活動記錄事件的警示](../azure-monitor/platform/alerts-activity-log.md)

- [如何建立認知搜尋活動的警示](./search-monitor-logs.md)

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

## <a name="vulnerability-management"></a>弱點管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：弱點管理](../security/benchmarks/security-control-vulnerability-management.md)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具

**指導** 方針：目前無法供認知搜尋使用。  針對儲存搜尋服務內容的叢集，Microsoft 會負責這些叢集的弱點管理。

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化的作業系統修補程式管理解決方案

**指導** 方針：不適用認知搜尋。 這項建議適用于計算資源。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：為協力廠商軟體專案部署自動化的修補程式管理解決方案

**指導** 方針：不適用認知搜尋。 這項建議適用于計算資源。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比較連續性弱點掃描

**指導** 方針：不適用認知搜尋。 Microsoft 會在支援認知搜尋服務的基礎系統上執行弱點管理。

**Azure 資訊安全中心監視** ：不適用

**責任** ：Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程序來排定所發現弱點的補救優先順序

**指導** 方針：不適用認知搜尋。 它沒有任何標準風險評等或計分系統可用於弱點掃描結果。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：清查和資產管理](../security/benchmarks/security-control-inventory-asset-management.md)。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自動化資產探索解決方案

**指導** 方針：使用 Azure Resource Graph 來查詢和探索所有資源 (例如，您訂用帳戶中) 的計算、儲存體、網路、埠、通訊協定等。  

確定您的租使用者中有適當的 (讀取) 許可權，並列舉所有 Azure 訂用帳戶以及訂用帳戶中的資源。  

- [如何使用 Azure Resource Graph Explorer 建立查詢](../governance/resource-graph/first-query-portal.md) 

- [如何查看您的 Azure 訂用帳戶](/powershell/module/az.accounts/get-azsubscription) 

- [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指導** 方針：使用中繼資料將標記套用至 Azure 資源，以邏輯方式將它們組織成分類法。

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指導** 方針：在適當的情況下使用標記、管理群組和個別訂用帳戶來組織和追蹤資產。 請定期調節清查，並確保會及時刪除訂用帳戶中未經授權的資源。
- [如何建立額外的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md) 

- [如何建立管理群組](../governance/management-groups/create-management-group-portal.md) 

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md) 

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定義和維護已核准 Azure 資源的清查

**指導** 方針：在認知搜尋中定義與編制索引和技能集處理相關的已核准 Azure 資源清單。

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指導** 方針：建議您根據組織的原則和標準來定義經核准使用的 Azure 資源清查，然後使用 Azure 原則或 Azure Resource Graph 監視未核准的 azure 資源。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md) 

- [如何使用 Azure Graph 建立查詢](../governance/resource-graph/first-query-portal.md) 

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：監視計算資源內未經核准的軟體應用程式

**指導** 方針：不適用認知搜尋。 本指南適用于計算資源。

建議您根據您的組織原則和安全性標準，針對已依據您的組織原則和安全性標準進行核准的軟體應用程式清查，並監視您的 Azure 計算資源上安裝的任何未核准的軟體標題。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未經核准的 Azure 資源和軟體應用程式

**指導** 方針：不適用認知搜尋。 這項建議適用于計算資源。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式

**指導** 方針：不適用認知搜尋。 它不會公開任何計算資源，也不會允許在其任何資源上安裝軟體應用程式。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指導** 方針：使用 Azure 原則來對可在客戶訂用帳戶中使用下列內建原則定義建立的資源類型進行限制：

- 不允許的資源類型
- 允許的資源類型

使用 Azure Resource Graph 來查詢或探索您的訂用帳戶 () 中的資源。 確保已核准環境中的所有 Azure 資源。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md) 

- [如何使用 Azure 原則拒絕特定的資源類型](../governance/policy/samples/index.md) 

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：維護已核准軟體標題的清查

**指導** 方針：不適用認知搜尋。 這項建議適用于在計算資源上執行的應用程式。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制使用者與 Azure Resource Manager 互動的能力

**指導** 方針：若要進行服務管理，請使用 Azure 條件式存取，藉由設定「Microsoft Azure 管理」應用程式的「封鎖存取」，來限制使用者與 Azure Resource Manager 互動的能力。 

控制用來驗證所有其他作業之要求的金鑰存取權，特別是與認知搜尋內容相關的金鑰。

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12：限制使用者在計算資源中執行腳本的能力

**指導** 方針：不適用認知搜尋。 這項建議適用于計算資源。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以實體或邏輯方式隔離高風險的應用程式

**指導** 方針：不適用認知搜尋。 這項建議適用于在 Azure App Service 或計算資源上執行的 web 應用程式。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱 [Azure 安全性基準測試：安全](../security/benchmarks/security-control-secure-configuration.md)設定。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導** 方針：使用 "Microsoft. Search" 命名空間中 Azure 原則別名來建立自訂原則，以對 Azure 認知搜尋資源的設定進行審核或強制執行。 您也可以針對認知搜尋服務使用內建的 Azure 原則定義，例如：

- 啟用 Azure 資源的稽核記錄

Azure Resource Manager 能夠在 JavaScript 物件標記法 (的 JSON) 中匯出範本，這應該經過檢查以確保設定符合您組織的安全性需求。 

您也可以使用 Azure 資訊安全中心中的建議作為 Azure 資源的安全設定基準。 

- [適用於 Azure 認知搜尋的 Azure 原則法規合規性控制項](./security-controls-policy.md)

- [如何查看可用 Azure 原則別名](/powershell/module/az.resources/get-azpolicyalias)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的作業系統設定

**指導** 方針：不適用認知搜尋。 這項建議適用于計算資源。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指導** 方針：使用 Azure 原則 [拒絕] 和 [部署是否不存在] 效果，在您的認知搜尋服務資源上強制執行安全設定。 

Azure Resource Manager 範本可以用來維護您組織所需的 Azure 資源安全性設定。 

- [瞭解 Azure 原則效果](../governance/policy/concepts/effects.md)

- [適用於 Azure 認知搜尋的 Azure 原則法規合規性控制項](./security-controls-policy.md)

- [建立和管理原則以強制執行合規性](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager 範本總覽](../azure-resource-manager/templates/overview.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：維護安全的作業系統設定

**指導** 方針：不適用認知搜尋。 這項建議適用于計算資源。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導** 方針：如果使用自訂 Azure 原則定義，請使用 Azure DevOps 或 Azure Repos 安全地儲存和管理您的程式碼。

- [如何在 Azure DevOps 中儲存程式碼](/azure/devops/repos/git/gitworkflow)

- [Azure Repos 文件](/azure/devops/repos/index)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂作業系統映像

**指導** 方針：不適用認知搜尋。 這項建議適用于計算資源。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署適用于 Azure 資源的設定管理工具

**指導** 方針：使用 Azure 原則定義和實行認知搜尋服務資源的標準安全性設定。 

使用別名來建立自訂原則，以進行網路設定的審核或強制執行。 您也可以利用與特定資源相關的內建原則定義。 

此外，您可以使用 Azure 自動化部署設定變更和管理原則例外狀況。 

- [適用於 Azure 認知搜尋的 Azure 原則法規合規性控制項](./security-controls-policy.md)

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的設定管理工具

**指導** 方針：不適用認知搜尋。 這項建議適用于計算資源。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：執行 Azure 資源的自動化設定監視

**指導** 方針：使用 [安全性中心] 來執行認知搜尋服務資源的基準掃描。  此外，請使用 Azure 原則來警示和審核您的資源設定。 

- [如何修復 Azure 資訊安全中心中的建議](../security-center/security-center-remediate-recommendations.md)

- [適用於 Azure 認知搜尋的 Azure 原則法規合規性控制項](./security-controls-policy.md)

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統實作自動化的設定監視

**指導** 方針：不適用認知搜尋。 這項建議適用于計算資源。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 祕密

**指導** 方針：使用 Azure 受控識別搭配 Azure Key Vault，以簡化雲端應用程式的秘密管理。
- [如何使用適用于 Azure 資源的受控識別](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 
- [如何建立 Key Vault](../key-vault/secrets/quick-create-portal.md) 

- [如何使用受控識別來提供 Key Vault authentication](../key-vault/general/assign-access-policy-portal.md) 

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

**指導** 方針：使用 Azure 受控識別，以 Azure Active Directory (Azure AD) 中使用自動管理的身分識別，為其他 Azure 服務（例如 Key Vault 和索引子資料來源）提供認知搜尋存取權。 受控識別可讓您向支援 Azure AD authentication 的任何服務進行驗證，包括 Azure Key Vault，而不需要在您的程式碼中提供任何認證。 

- [使用受控識別設定與資料來源的索引子連接](./search-howto-managed-identities-data-sources.md)

- [使用受控識別設定客戶管理的金鑰進行資料加密](./search-security-manage-encryption-keys.md)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指導** 方針：不適用認知搜尋。 它不會裝載程式碼，也不會有任何要識別的認證。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

## <a name="malware-defense"></a>惡意程式碼防禦

*如需詳細資訊，請參閱 [Azure 安全性基準測試：惡意程式碼防護](../security/benchmarks/security-control-malware-defense.md)。*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1：使用集中管理的反惡意程式碼軟體

**指導** 方針：不適用認知搜尋。 這項建議適用于計算資源。

支援 Azure 服務的基礎主機上已啟用 Microsoft 反惡意程式碼 (例如 Azure 認知搜尋) ，但不會對客戶內容執行。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指導** 方針：預先掃描任何上傳至非計算 Azure 資源的內容，例如認知搜尋、Blob 儲存體、Azure SQL Database 等等。 

您必須負責預先掃描即將上傳至非計算 Azure 資源的任何內容。 Microsoft 無法存取客戶資料，因此無法代表您執行客戶內容的反惡意程式碼掃描。

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8.3：確定已更新反惡意程式碼軟體和簽章

**指導** 方針：不適用認知搜尋。 它不允許在其資源上安裝反惡意程式碼解決方案。 針對基礎平臺，Microsoft 會處理更新任何反惡意程式碼軟體和簽章。 

針對您的組織所擁有並用於搜尋解決方案的任何計算資源，請遵循安全性中心的建議、計算 &amp; 應用程式，以確保所有端點都是最新的簽章。 針對 Linux，請使用協力廠商反惡意程式碼解決方案。

**Azure 資訊安全中心監視** ：不適用

**責任** ：共用

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資料](../security/benchmarks/security-control-data-recovery.md)復原。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確保會定期自動備份

**指導** 方針：儲存在搜尋服務中的內容無法透過 Azure 備份或任何其他內建機制來進行備份，但您可以從應用程式原始程式碼和主要資料來源重建索引，或建立自訂工具來取得和儲存已編制索引的內容。

- [GitHub 索引-備份-還原範例](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/index-backup-restore)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份並備份任何客戶管理的金鑰

**指導** 方針：認知搜尋目前不支援自動備份搜尋服務中的資料，而且必須透過手動程式進行備份。  您也可以在 Azure Key Vault 中備份客戶管理的金鑰。 

- [備份和還原 Azure 認知搜尋索引](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [如何在 Azure 中備份 Key Vault 金鑰](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指導** 方針：認知搜尋目前不支援自動備份搜尋服務中的資料，而且必須透過手動程式來備份和還原。  定期對您手動備份的內容執行資料還原，以確保備份程式的端對端完整性。

- [備份和還原 Azure 認知搜尋索引](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [如何在 Azure 中還原 Key Vault 金鑰](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰的保護

**指導** 方針：認知搜尋目前不支援自動備份搜尋服務中的資料，而且必須透過手動程式進行備份。  您也可以在 Azure Key Vault 中備份客戶管理的金鑰。 

啟用 Key Vault 中的虛刪除和清除保護，以防止金鑰遭到意外或惡意刪除。 如果使用 Azure 儲存體來儲存手動備份，請在刪除 blob 或 blob 快照集時，啟用虛刪除來儲存及復原您的資料。 

- [備份和還原 Azure 認知搜尋索引](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [如何啟用 Key Vault 中的虛刪除和清除保護](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Azure Blob 儲存體的虛刪除](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱 [Azure 安全性基準測試：事件回應](../security/benchmarks/security-control-incident-response.md)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指導** 方針：為您的組織開發事件回應指南。 請確定有撰寫的事件回應計畫，可定義人員的所有角色，以及事件處理和管理的階段，以進行事件後的評論。

- [建立自有安全性事件回應程序的指引](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 安全性回應中心的事件剖析](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [客戶也可以利用 NIST 的電腦安全性性事件處理指南來協助建立自己的事件回應計畫](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序

**指引** ：資訊安全中心會指派每個警示的嚴重性，以協助設定應優先調查哪些警示。 嚴重性是以資訊安全中心在尋找或 analytically 中的信心，以及導致警示的活動背後有惡意意圖的信賴等級為基礎。

此外，使用標記標記訂用帳戶，並建立命名系統來識別和分類 Azure 資源，尤其是處理敏感性資料。 您必須負責根據發生事件的 Azure 資源和環境的重要性來排定補救警示的優先順序。

- [使用標記來組織 Azure 資源](../azure-resource-manager/management/tag-resources.md)

- [Azure 資訊安全中心的安全性警示](../security-center/security-center-alerts-overview.md)

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指引** ：進行練習以定期測試系統的事件回應功能。 找出弱點和落差，並視需要修訂計畫。

- [請參閱 NIST 的發行集、「測試、訓練和練習適用于 IT 計畫和功能的指南」。](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引** ：如果 Microsoft 安全性回應中心 (MSRC) 發現您的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡資訊來連絡您。 事後檢討事件，確保問題已解決。

- [如何設定 Azure 資訊安全中心的安全性連絡人](../security-center/security-center-provide-security-contact-details.md)

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指導** 方針：使用連續匯出功能來匯出您的安全性中心警示和建議。 「連續匯出」可讓您手動或連續匯出警示和建議。 您可以使用「安全性中心」資料連線器，將警示串流至 Azure Sentinel。

- [如何設定連續匯出](../security-center/continuous-export.md)

- [如何將警示串流至 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指引** ：利用 Azure 資訊安全中心的工作流程自動化功能，透過「Logic Apps」自動觸發對安全性警示和建議的回應。

- [如何設定工作流程自動化和 Logic Apps](../security-center/workflow-automation.md)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*如需詳細資訊，請參閱 [Azure 安全性基準測試：滲透測試和 red team 練習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：進行 Azure 資源的定期滲透測試，並確保修復所有重要的安全性結果

**指導** 方針：遵循 Microsoft Cloud 滲透測試的參與規則，以確保您的滲透測試不違反 Microsoft 原則。 針對受 Microsoft 管理的雲端基礎結構、服務和應用程式，使用 Microsoft 的策略和執行的 Red 小組和即時網站滲透測試。
- [滲透測試的參與規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)
- [Microsoft Cloud Red 小組](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視** ：不適用

**責任** ：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](../security/benchmarks/overview.md)
- 深入了解 [Azure 資訊安全性基準](../security/benchmarks/security-baselines-overview.md)
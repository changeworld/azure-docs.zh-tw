---
title: Azure 防火牆的 azure 安全性基準
description: Azure 防火牆安全性基準提供程式指引和資源，可讓您執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: firewall
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 4fccb0b2e382e56e83fbbe54c0f0afa50a5c9590
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202465"
---
# <a name="azure-security-baseline-for-azure-firewall"></a>Azure 防火牆的 azure 安全性基準

此安全性基準會將 [Azure 安全性基準測試](../security/benchmarks/overview.md) 的指引套用至 azure 防火牆。 Azure 安全性基準提供如何在 Azure 上保護雲端解決方案的建議。 內容會將 Azure 安全性基準測試所定義的 **安全性控制** ，以及適用于 azure 防火牆的相關指引分組。 已排除不適用 Azure 防火牆的 **控制項**。 若要瞭解 Azure 防火牆如何完全對應至 Azure 安全性基準測試，請參閱 [完整的 Azure 防火牆安全性基準對應](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)檔案。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱 [Azure 安全性基準測試：網路安全性](../security/benchmarks/security-control-network-security.md)。*

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2：監視和記錄虛擬網路、子網和網路介面的設定和流量

**指導** 方針： Azure 防火牆已與 Azure 監視器整合，以記錄防火牆所處理的流量。

此外，請使用 Azure 資訊安全中心並遵循網路保護建議，以協助保護與 Azure 防火牆相關的網路資源。

- [瞭解 Azure 資訊安全中心所提供的網路安全性](../security-center/security-center-network-recommendations.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址的通訊

**指導** 方針：啟用以威脅情報為基礎的篩選，以警示及拒絕從已知惡意 IP 位址和網域的流量。 您可以為您的防火牆啟用以威脅情報為基礎的篩選，以警示及拒絕來自/傳至已知惡意 IP 位址和網域的流量。

- [以 Azure 防火牆威脅情報為主的篩選](threat-intel.md)

- [了解 Azure 資訊安全中心的整合式威脅情報](../security-center/azure-defender.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負荷降至最低

**指導** 方針：在 Azure 防火牆中，服務標籤代表一組 IP 位址首碼，以協助將建立安全性規則的複雜性降至最低。

Azure 防火牆服務標籤可用於網路規則目的地欄位，並定義 Azure 防火牆上的網路存取控制。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。

此外，也支援客戶定義的標籤（例如 IP 群組），並可用於網路規則或應用程式規則。 支援應用程式規則中的 FQDN 標記，以允許必要的輸出網路流量通過防火牆。

請注意，您無法建立自己的服務標籤，也無法指定標籤中包含哪些 IP 位址。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。

 

- [Azure 防火牆服務標籤](service-tags.md)

- [可用的服務標籤](../virtual-network/service-tags-overview.md#available-service-tags)

- [Azure 防火牆中的 IP 群組](ip-groups.md)

- [FQDN 標籤概觀](fqdn-tags.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指導** 方針： azure 防火牆尚未完全支援 azure 原則。 Azure 防火牆管理員可以用來達成安全性設定的標準化。

您也可以使用 Azure 藍圖，藉由在單一藍圖定義中封裝重要環境成品（例如 Azure 資源管理員範本、Azure RBAC 控制項和原則）來簡化大規模的 Azure 部署。 您可以將藍圖套用至新的訂用帳戶，並透過版本控制來微調控制和管理。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [適用于網路的 Azure 原則範例](../governance/policy/samples/built-in-policies.md#network)

- [如何建立 Azure 藍圖](../governance/blueprints/create-blueprint-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導** 方針：使用 Azure 活動記錄來監視資源設定，以及偵測 Azure 防火牆資源的變更。 在 Azure 監視器中建立警示，在發生重大資源變更時將會觸發此警示。

- [監視 Azure 防火牆記錄和計量](./firewall-diagnostics.md)

- [如何檢視及擷取 Azure 活動記錄事件](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [如何在 Azure 監視器中建立警示](../azure-monitor/platform/alerts-activity-log.md) 

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱 [Azure 安全性基準測試：記錄和監視](../security/benchmarks/security-control-logging-monitoring.md)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源

**指導** 方針： Microsoft 會為 azure 防火牆維護 azure 資源的時間來源。 客戶必須建立網路規則，以允許此存取，或您在環境中使用的時間伺服器。

- [NTP 伺服器存取](protect-windows-virtual-desktop.md#additional-considerations)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導** 方針：您可以啟用和麵板上的記錄資料，以 Azure Sentinel 或針對各種記錄集中安全性記錄檔管理的協力廠商 SIEM。

活動記錄可以用來在 Azure 防火牆上審核作業，並監視對資源的動作。 活動記錄包含 (PUT、POST、DELETE) 資源的所有寫入作業，但讀取作業 (取得) 。 當您進行疑難排解時，可以使用活動記錄來尋找錯誤，或是監視組織中的使用者修改資源的方式。

Azure 防火牆也會提供下列診斷記錄，以提供客戶應用程式和網路規則的相關資訊。

應用程式規則記錄：符合其中一個已設定應用程式規則的每個新連線，都會產生接受/拒絕連線的記錄。

網路規則記錄：符合您設定的其中一個網路規則的每個新連線，都會產生接受/拒絕連線的記錄檔

注意：這兩個記錄都可以儲存至儲存體帳戶、串流至事件中樞及/或僅在環境中針對每個 Azure 防火牆啟用時，才會傳送至 Azure 監視器記錄。

- [Azure 防火牆記錄](logs-and-metrics.md)

活動記錄中的資源動作清單： Azure Resource Manager 資源提供者作業

- [如何使用 Azure 監視器收集平臺記錄和計量 ](../azure-monitor/platform/diagnostic-settings.md)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

- [如何開始使用 Azure 監視器和第三方 SIEM 整合](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指導** 方針：活動記錄可以用來在 Azure 防火牆上審核作業，並監視對資源的動作。 活動記錄包含所有的寫入作業， (PUT、POST、DELETE) for Azure 資源，但讀取作業 (取得) 。 Azure 防火牆也會提供下列診斷記錄，以提供客戶應用程式和網路規則的相關資訊。 

應用程式規則記錄：符合其中一個已設定應用程式規則的每個新連線，都會產生接受/拒絕連線的記錄。

網路規則記錄：符合您設定的其中一個網路規則的每個新連線，都會產生接受/拒絕連線的記錄檔

請注意，這兩個記錄都可以儲存至儲存體帳戶、串流至事件中樞及/或傳送至 Azure 監視器記錄，但僅適用于環境中的每個 Azure 防火牆啟用時。

- [Azure 防火牆記錄](logs-and-metrics.md)

- [活動記錄中的資源動作清單](../role-based-access-control/resource-provider-operations.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指導** 方針：您可以根據 Azure 監視器內的組織合規性法規來設定 Log Analytics 工作區保留期限。 您可以根據所選的定價層，為所有工作區將資料保留設定為30到730天 (2 年) 。

儲存記錄儲存體保留有3個選項：

如果記錄會儲存一段較長的持續期間，並在需要時加以檢閱，則最好針對記錄使用儲存體帳戶。

如果要整合其他安全性資訊和事件管理 (SEIM) 工具以便在資源上取得警示，則事件中樞是絕佳的選項。

Azure 監視器記錄最適合用來進行應用程式的一般即時監視，或查看趨勢。

- [Azure 防火牆記錄和計量](logs-and-metrics.md)

- [變更 Log Analytics 中的資料保留期限](../azure-monitor/platform/manage-cost-storage.md)

- [如何設定 Azure 儲存體客戶紀錄的保留原則](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和審核記錄

**指導** 方針： Azure 防火牆已與 Azure 監視器整合，可供您用來查看和分析防火牆記錄。 記錄可以傳送至 Log Analytics、「Azure 儲存體」或「事件中樞」。 這些記錄可以在 Log Analytics 中進行分析，或透過不同的工具 (例如 Excel 和 Power BI) 進行分析。 有幾種不同類型的 Azure 防火牆記錄。

活動記錄可以用來在 Azure 防火牆上審核作業，並監視對資源的動作。 活動記錄包含 (PUT、POST、DELETE) 資源的所有寫入作業， () 的讀取作業除外。 當您進行疑難排解時，可以使用活動記錄來尋找錯誤，或是監視組織中的使用者修改資源的方式。

Azure 防火牆也會提供診斷記錄，以提供客戶應用程式和網路規則的相關資訊。

當符合您設定的其中一個應用程式規則的每個新連線都產生可接受/拒絕連線的記錄檔時，就會建立應用程式規則記錄。 

當符合您設定的其中一個網路規則的每個新連線都產生可接受/拒絕連線的記錄檔時，就會建立網路規則記錄檔。

請注意，這兩個記錄都可以儲存至儲存體帳戶、串流至事件中樞及/或傳送至 Azure 監視器記錄，但僅限在環境中的每個 Azure 防火牆啟用時。

Azure 監視器記錄可以用來進行應用程式的一般即時監視，或查看趨勢。

- [Azure 防火牆記錄和計量](logs-and-metrics.md)

- [診斷記錄](logs-and-metrics.md#diagnostic-logs)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：啟用異常活動的警示

**指導** 方針：搭配使用 Azure 資訊安全中心與 Log Analytics 工作區來監視和警示在安全性記錄和事件中找到的異常活動。 

或者，您也可以啟用和麵板上的資料以 Azure Sentinel。 

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

- [如何在 Azure 資訊安全中心中管理警示](../security-center/security-center-managing-and-responding-alerts.md)

- [如何對 log analytics 記錄資料發出警示](../azure-monitor/learn/tutorial-response.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱 [Azure 安全性基準測試：身分識別與存取控制](../security/benchmarks/security-control-identity-access-control.md)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的清查

**指導** 方針： Azure AD 有必須明確指派且可供查詢的內建角色。 使用 Azure AD PowerShell 模組執行臨機操作查詢，以探索屬於系統管理群組成員的帳戶。

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指引**：建立使用專用系統管理帳戶的標準作業程序。 使用 Azure 資訊安全中心的身分識別與存取管理來監視系統管理帳戶的數目。

您也可以使用適用于 Microsoft 服務 Azure AD Privileged Identity Management 特殊許可權角色，並 Azure Resource Manager，來啟用及時且足夠的存取權。

- [深入瞭解 Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory 單一登入 (SSO) 

**指導** 方針：盡可能使用 Azure Active Directory SSO，而不是針對個別服務設定個別的獨立認證。 使用 Azure 資訊安全中心身分識別和存取管理建議。

- [瞭解 Azure AD 的 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：所有以 Azure Active Directory 為基礎的存取都使用多重要素驗證

**指導** 方針： (MFA) 啟用 Azure Active Directory 多重要素驗證，並遵循 Azure 資訊安全中心身分識別和存取管理建議。

- [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

- [如何在 Azure 資訊安全中心監視身分識別和存取](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦 (特殊權限存取工作站) 進行所有系統管理工作

**指導** 方針：使用 paw (特殊許可權的存取工作站) 搭配多重要素驗證 (MFA) 設定為登入及設定 Azure 防火牆和相關資源。 

- [瞭解特殊權限存取工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：來自系統管理帳戶的可疑活動記錄和警示

**指導** 方針：當環境中發生可疑或不安全的活動時，請使用 Azure Active Directory 的安全性報告來產生記錄和警示。 使用 Azure 資訊安全中心來監視身分識別和存取活動。 

- [如何識別已標示為有風險活動的 Azure AD 使用者](../active-directory/identity-protection/overview-identity-protection.md) 

- [如何在 Azure 資訊安全中心監視使用者的身分識別和存取活動](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指引**：使用條件式存取具名位置，只允許從 IP 位址範圍或國家/區域的特定邏輯群組存取。 

- [如何在 Azure 中設定具名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導** 方針：使用 Azure Active Directory (Azure AD) 作為中央驗證和授權系統。 Azure AD 會對待用資料和傳輸中資料使用增強式加密，以保護資料安全。 Azure AD 也會對使用者認證進行 Salt 處理、雜湊處理並安全儲存資料。 

- [如何建立及設定 Azure AD 執行個體](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指引**：Azure AD 會提供記錄來協助探索過時的帳戶。 此外，使用 Azure 身分識別存取審核來有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 您可以定期檢閱使用者的存取權，以確定只有適當的使用者具有持續存取權。 

- [瞭解 Azure AD 報告](../active-directory/reports-monitoring/index.yml)

- [如何使用 Azure 身分識別存取權檢閱](../active-directory/governance/access-reviews-overview.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：監視嘗試存取已停用的認證

**指引**：您可以存取 Azure AD 登入活動、稽核和風險事件記錄檔來源，讓您可以與任何 SIEM/監視工具整合。 

您可以建立 Azure Active Directory 使用者帳戶的診斷設定，並將審核記錄和登入記錄傳送至 Log Analytics 工作區，以簡化此程式。 您可以在 Log Analytics 工作區中設定所需的警示。 

- [如何將 Azure 活動記錄整合到 Azure 監視器中](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：帳戶登入行為偏差的警示

**指引**：使用 Azure AD 風險和身分識別保護功能設定自動回應，以偵測與使用者身分識別相關的可疑動作。 您也可將資料內嵌到 Azure Sentinel 中，以便進一步調查。 

- [如何檢視有風險的 Azure AD 登入](../active-directory/identity-protection/overview-identity-protection.md)

- [如何設定和啟用身分識別保護風險原則](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資料保護](../security/benchmarks/security-control-data-protection.md)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的清查

**指導** 方針：使用標籤協助追蹤 Azure 防火牆和儲存或處理敏感性資訊的相關資源。 

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指導** 方針：針對個別的安全性網域（例如環境類型和資料敏感度層級），使用不同的訂用帳戶和管理群組來執行隔離。 您可以限制您的應用程式和企業環境所需的 Azure 防火牆資源存取層級。 您可以透過 Azure 角色型存取控制來控制對 Azure 資源的存取。 

- [如何建立額外的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md)

- [如何建立管理群組](../governance/management-groups/create-management-group-portal.md)

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的敏感性資訊傳輸

**指導** 方針：利用從網路周邊 Azure Marketplace 的協力廠商解決方案，監視是否有未經授權的機密資訊傳輸並封鎖這類傳輸，同時警示資訊安全專業人員。 

針對 Microsoft 所管理的基礎平臺，Microsoft 會將所有客戶內容視為機密，並防止客戶資料遺失和公開。 為了確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制項和功能。 

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導** 方針：加密傳輸中的所有機密資訊。 確定任何連線至 Azure 防火牆和相關資源的用戶端都能夠協商 TLS 1.2 或更高的版本。 

遵循 Azure 資訊安全中心待用加密及傳輸中加密的建議（適用時）。 

- [瞭解 Azure 中的傳輸加密](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

**指導** 方針：使用協力廠商主動式探索工具，以使用 azure 防火牆和相關資源來識別儲存在 Azure 資源中的所有機密資訊，並更新組織的機密資訊清查。

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 來控制資源的存取權

**指導** 方針：使用 azure 角色型存取控制 (azure RBAC) 來控制 azure 防火牆和相關資源的存取權。

- [如何設定 Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指導** 方針：使用 azure 防火牆和相關資源，在所有 Azure 資源上使用待用加密。 Microsoft 建議讓 Azure 管理您的加密金鑰，但在某些情況下，您可以選擇管理您自己的金鑰。 

- [了解 Azure 中的待用加密](../security/fundamentals/encryption-atrest.md)

- [如何設定客戶管理的加密金鑰](../storage/common/customer-managed-keys-configure-key-vault.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導** 方針：使用 Azure 監視器搭配 Azure 活動記錄來建立 azure 防火牆中發生變更時的警示。

- [如何建立 Azure 活動記錄事件的警示](../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：清查和資產管理](../security/benchmarks/security-control-inventory-asset-management.md)。*

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指導** 方針：將標記套用至 Azure 防火牆，以及提供中繼資料的相關資源，以邏輯方式將它們組織成分類法。 

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指導** 方針：使用標記、管理群組和個別訂用帳戶（如果適用）來組織和追蹤 Azure 防火牆和相關資源。 請定期調節清查，並確保會及時刪除訂用帳戶中未經授權的資源。

- [如何建立額外的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md)

- [如何建立管理群組](../governance/management-groups/create-management-group-portal.md)

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定義和維護已核准 Azure 資源的清查

**指導** 方針：根據您的組織需求，建立核准的 Azure 防火牆資源（包括設定）的清查。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指導** 方針：使用 Azure 原則對可在訂用帳戶中建立的資源類型（ (s) ）施加限制。

使用 Azure Resource Graph 來查詢/探索其訂用帳戶 () 中的 Azure 防火牆資源。 確認環境中的所有 Azure 防火牆和相關資源都已核准。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未經核准的 Azure 資源和軟體應用程式

**指導** 方針：執行您自己的流程，以移除未授權的 Azure 防火牆和相關資源。 您也可以使用協力廠商解決方案來識別未核准的 Azure 防火牆和相關資源

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指導** 方針：使用 Azure 原則來限制您可以在環境中布建的服務。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure 原則拒絕特定的資源類型](../governance/policy/samples/index.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制使用者與 Azure Resource Manager 互動的能力

**指導** 方針：使用 azure 條件式存取，藉由設定「Microsoft Azure 管理」應用程式的「封鎖存取」，來限制使用者與 Azure 資源管理員互動的能力。 

- [如何設定條件式存取以封鎖對 Azure 資源管理員的存取](../role-based-access-control/conditional-access-azure-management.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以實體或邏輯方式隔離高風險的應用程式

**指導** 方針：商務作業可能需要的應用程式，或組織的風險設定檔不同的環境，應該隔離並與個別的 Azure 防火牆實例分開。

- [使用 Azure 入口網站部署和設定 Azure 防火牆](deploy-cli.md)

- [如何建立虛擬網路](../virtual-network/quick-create-portal.md)

- [如何使用安全性設定建立 NSG](../virtual-network/tutorial-filter-network-traffic.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱 [Azure 安全性基準測試：安全](../security/benchmarks/security-control-secure-configuration.md)設定。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導** 方針： Azure Resource Manager 能夠在 JAVASCRIPT 物件標記法 (JSON) 中匯出範本，您應該檢查這些設定，以確保設定符合/超過組織的安全性需求。

您也可以使用 Azure 資訊安全中心中的建議作為 Azure 資源的安全設定基準。

目前並未完全支援 Azure 原則。 

- [Azure 入口網站中的單一和多重資源匯出至範本](../azure-resource-manager/templates/export-template-portal.md)

- [安全性建議 - 參考指南](../security-center/recommendations-reference.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指導** 方針：使用 azure 原則 [拒絕] 和 [部署是否不存在]，在您的 Azure 防火牆和相關資源上強制執行安全設定。  此外，您可以使用 Azure Resource Manager 範本，維護您的組織所需的 Azure 防火牆和相關資源的安全性設定。

- [瞭解 Azure 原則效果](../governance/policy/concepts/effects.md)

- [建立和管理原則以強制執行合規性](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager 範本總覽](../azure-resource-manager/templates/overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導** 方針：使用 Azure DevOps 安全地儲存和管理您的程式碼，例如自訂 Azure 原則和 Azure Resource Manager 範本。 若要存取您在 Azure DevOps 中管理的資源，您可以授與或拒絕特定使用者、內建安全性群組或 Azure Active Directory (Azure AD) （如果與 Azure DevOps 整合）中定義的群組，或與 TFS 整合的 Active Directory。

- [如何在 Azure DevOps 中儲存程式碼](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [關於 Azure DevOps 中的許可權和群組](/azure/devops/organizations/security/about-permissions)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署適用于 Azure 資源的設定管理工具

**指導** 方針：使用 Azure 原則定義和實行 Azure 防火牆和相關資源的標準安全性設定。 使用 Azure 原則別名來建立自訂原則，以針對 Azure 防火牆資源的網路設定進行審核或強制執行。 您也可以使用與特定資源相關的內建原則定義。  

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用別名](../governance/policy/concepts/definition-structure.md#aliases)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

**指導** 方針：使用受控識別，在 Azure AD 中為 Azure 服務提供自動管理的身分識別。 受控識別可讓您向支援 Azure Resource Manager Azure AD 驗證的任何服務進行驗證，並可搭配 API/Azure 入口網站/CLI/PowerShell 使用。

- [如何設定受控識別](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指引**：實作認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。 

- [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資料](../security/benchmarks/security-control-data-recovery.md)復原。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確定定期自動備份

**指導** 方針：使用 Azure Resource Manager，在 JAVASCRIPT 物件標記法 (JSON) 範本中匯出 azure 防火牆和相關資源，此範本可用來做為 Azure 防火牆和相關設定的備份。  您也可以從 Azure 入口網站使用 Azure 防火牆的「匯出範本」功能來匯出 Azure 防火牆設定。  使用 Azure 自動化自動執行備份腳本。

- [Azure 入口網站中的單一和多重資源匯出至範本](../azure-resource-manager/templates/export-template-portal.md)

- [使用範本來部署 Azure 防火牆](deploy-template.md)

- [Microsoft Network Azure 防火牆範本參考](/azure/templates/microsoft.network/azurefirewalls)

- [關於 Azure 自動化](../automation/automation-intro.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份並備份任何客戶管理的金鑰

**指導** 方針：使用 Azure Resource Manager，在 JAVASCRIPT 物件標記法 (JSON) 範本中匯出 azure 防火牆和相關資源，此範本可用來做為 Azure 防火牆和相關設定的備份。  您也可以從 Azure 入口網站使用 Azure 防火牆的「匯出範本」功能來匯出 Azure 防火牆設定。

- [使用範本來部署 Azure 防火牆](deploy-template.md)

- [Microsoft Network Azure 防火牆範本參考](/azure/templates/microsoft.network/azurefirewalls)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指導** 方針：確定能夠使用 Azure Resource Manager 範本支援的檔案定期執行還原。  

- [使用範本來部署 Azure 防火牆](deploy-template.md)

- [Microsoft Network Azure 防火牆範本參考](/azure/templates/microsoft.network/azurefirewalls)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰的保護

**指導** 方針：使用 Azure DevOps 安全地儲存和管理您的程式碼，例如自訂 Azure 原則、Azure Resource Manager 範本。 若要保護您在 Azure DevOps 中管理的資源，您可以授與或拒絕特定使用者、內建安全性群組或 Azure Active Directory (Azure AD) （如果與 Azure DevOps 整合）中定義的群組，或與 TFS 整合的 Active Directory。

- [如何在 Azure DevOps 中儲存程式碼](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [關於 Azure DevOps 中的許可權和群組](/azure/devops/organizations/security/about-permissions)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱 [Azure 安全性基準測試：事件回應](../security/benchmarks/security-control-incident-response.md)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指引**：為組織製作事件回應指南。 請確定有書面的事件回應計畫，其中定義人員的所有角色，以及從偵測到事件後檢討的事件處理/管理階段。 

- [建立自有安全性事件回應程序的指引](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 安全性回應中心的事件剖析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [利用 NIST 的電腦安全性事件處理指南，協助建立自己的事件回應計畫](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序

**指引**：資訊安全中心會指派每個警示的嚴重性，以協助設定應優先調查哪些警示。 嚴重性會依據資訊安全中心對用於發出警示的發現或分析其信心程度，以及信賴等級具有活動背後會導致警示的惡意意圖。 

此外，使用標記清楚地標示訂用帳戶 (例如， 生產、非生產) 並建立命名系統，以清楚地識別及分類 Azure 資源，尤其是處理敏感性資料的資源。  您需負責根據發生事件的 Azure 資源和環境的重要性，設定警示的補救優先順序。 

- [Azure 資訊安全中心的安全性警示](../security-center/security-center-alerts-overview.md) 

- [使用標記來組織 Azure 資源](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指引**：進行練習以定期測試系統的事件回應功能，進而協助保護您的 Azure 資源。 找出弱點和落差，並視需要修訂計畫。 

- [NIST 的出版品 - Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引**：如果 Microsoft 安全性回應中心 (MSRC) 發現您的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡資訊來連絡您。 事後檢討事件，確保問題已解決。 

- [如何設定 Azure 資訊安全中心的安全性連絡人](../security-center/security-center-provide-security-contact-details.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指引**：使用「連續匯出」功能來匯出 Azure 資訊安全中心警示和建議，協助找出 Azure 資源的風險。

「連續匯出」可讓您以手動或持續不斷的方式來匯出警示和建議。 您可使用 Azure 資訊安全中心的資料連接器，將警示串流至 Azure Sentinel。

- [如何設定連續匯出](../security-center/continuous-export.md)

- [如何將警示串流至 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指導** 方針：使用 Azure 資訊安全中心中的工作流程自動化功能，透過「Logic Apps」安全性警示和建議來自動觸發回應，以保護您的 Azure 資源。 

- [如何設定工作流程自動化和 Logic Apps](../security-center/workflow-automation.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*如需詳細資訊，請參閱 [Azure 安全性基準測試：滲透測試和 red team 練習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：進行 Azure 資源的定期滲透測試，並確保修復所有重要的安全性結果

**指導** 方針：遵循 Microsoft 的 Engagement 規則，以確保您的滲透測試不違反 Microsoft 原則。 針對受 Microsoft 管理的雲端基礎結構、服務和應用程式，使用 Microsoft 的策略和執行的 Red 小組和即時網站滲透測試。 

- [滲透測試運作規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](../security/benchmarks/overview.md)
- 深入了解 [Azure 資訊安全性基準](../security/benchmarks/security-baselines-overview.md)
---
title: 適用于 Azure 監視器的 Azure 安全性基準
description: Azure 監視器安全性基準提供的程式指引和資源，可讓您執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 15026c378c4c399915e99b0910f1e6518f5adfc8
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151854"
---
# <a name="azure-security-baseline-for-azure-monitor"></a>適用于 Azure 監視器的 Azure 安全性基準

此安全性基準會將 [Azure 安全性基準測試](../security/benchmarks/overview.md) 的指引套用至 Azure 監視器。 Azure 安全性基準提供如何在 Azure 上保護雲端解決方案的建議。 內容會依 Azure 安全性基準測試所定義的 **安全性控制** ，以及適用于 Azure 監視器的相關指引來分組。 已排除不適用 Azure 監視器的**控制項**。 若要查看 Azure 監視器如何完全對應至 Azure 安全性基準測試，請參閱 [完整的 Azure 監視器安全性基準對應](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)檔案。

Azure 監視器是 Azure 核心服務的一部分，且 Azure 監視器服務無法另外部署為服務。 Azure 監視器元件可能會隨您的資源一起部署，而這可能會影響這些資源的安全性狀態。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱 [Azure 安全性基準測試：網路安全性](../security/benchmarks/security-control-network-security.md)。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保護虛擬網路內的 Azure 資源

**指導**方針：啟用 Azure Private Link 以允許存取 Azure SaaS 服務 (例如，透過虛擬網路中的私人端點，Azure 監視器) 和 azure 裝載的客戶/合作夥伴服務。 虛擬網路和服務間的流量會在通過 Microsoft 骨幹網路時隨之減少，降低資料在網際網路中公開的風險。

若要允許流量達到 Azure 監視器，請使用 "AzureMonitor" 服務標籤，以允許透過網路安全性群組的輸入和輸出流量。 若要允許可用性監視測試流量達到 Azure 監視器，請使用 "ApplicationInsightsAvailability" 服務標記透過網路安全性群組的所有輸入流量。

虛擬網路規則可讓 Azure 監視器只接受從虛擬網路內所選子網傳送的通訊。

使用 Log Analytics 閘道將資料傳送至 Azure 監視器中的 Log Analytics 工作區，而這些電腦無法直接連線至網際網路，而導致電腦不需要連線到網際網路。 

- [如何設定 Azure 監視器的 Private Link](platform/private-link-security.md)

- [在 Azure 監視器中使用 Log Analytics 閘道連接電腦，而不需要網際網路存取](platform/gateway.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2：監視和記錄虛擬網路、子網和網路介面的設定和流量

**指導**方針： Azure 監視器是核心服務，而且不支援直接部署至虛擬網路，其基礎結構是由 Microsoft 處理。 不過，對於讓網路連線到 Azure 監視器供應專案的資源，請使用網路安全性群組來保護其網路。 啟用網路安全性群組流量記錄，並將記錄傳送至儲存體帳戶，以進行流量審核。 您也可以將流量記錄傳送至 Log Analytics 工作區，並使用「流量分析」來提供 Azure 雲端中流量流程的見解。 流量分析的優點包括能將網路活動視覺化並找出作用點、識別安全性威脅、了解流量模式並找到錯誤的網路設定。

搭配 Private Link 使用 Azure 監視器時，您可以存取網路記錄，例如「私用端點所處理的資料 (IN/OUT) 」。

- [Azure 監視器代理程式的網路需求](platform/log-analytics-agent.md#network-requirements)

- [在 Azure 監視器中使用 Log Analytics 閘道連接電腦，而不需要網際網路存取](platform/gateway.md)

- [如何啟用網路安全性群組流量記錄](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何啟用和使用流量分析](../network-watcher/traffic-analytics.md)

- [瞭解 Azure 資訊安全中心所提供的網路安全性](../security-center/security-center-network-recommendations.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負荷降至最低

**指導**方針：若要允許流量達到 Azure 監視器，請使用 "AzureMonitor" 服務標籤，以允許透過網路安全性群組的輸入和輸出流量。 若要允許可用性監視測試流量達到 Azure 監視器，請使用 "ApplicationInsightsAvailability" 服務標記透過網路安全性群組的所有輸入流量。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。

- [瞭解和使用服務標記](../virtual-network/service-tags-overview.md) 

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

**指導**方針： Azure 監視器是 Azure 核心服務的一部分，而且無法另外部署為服務。 Azure 監視器元件（包括 Azure 監視器代理程式和 Application Insights SDK）可能會與您的資源一起部署，而這可能會影響這些資源的安全性狀態。

- [Azure 監視器代理程式的網路需求](platform/log-analytics-agent.md#network-requirements)

- [在 Azure 監視器中使用 Log Analytics 閘道連接電腦，而不需要網際網路存取](platform/gateway.md) 

- [請參閱開始使用 Application Insights](./app/app-insights-overview.md#get-started)

- [如何設定可用性 web 測試](app/monitor-web-app-availability.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導**方針：使用 Azure 活動記錄來監視資源設定，以及偵測與 Azure 監視器相關的網路資源變更。 在 Azure 監視器中建立警示，這些警示會在這些重要網路資源的變更發生時觸發。

- [如何檢視及擷取 Azure 活動記錄事件](./platform/activity-log.md#view-the-activity-log)

- [如何在 Azure 監視器中建立警示](platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱 [Azure 安全性基準測試：記錄和監視](../security/benchmarks/security-control-logging-monitoring.md)。*

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導**方針： Azure 監視器使用活動記錄來記錄其資源的變更。 您可以將這些記錄匯出至 Azure 儲存體、事件中樞或 Log Analytics 工作區。 透過 Azure 監視器內嵌記錄，以匯總端點裝置、網路資源和其他安全性系統所產生的安全性資料。 在 Azure 監視器中，您可以對資料進行查詢和執行分析，並將 Azure 儲存體帳戶用於記錄的長期/封存儲存體。

或者，您也可啟用 Azure Sentinel 或第三方 SIEM，並讓資料上線。

- [如何使用 Azure 監視器收集平臺記錄和計量](platform/diagnostic-settings.md)

- [如何使用 Azure 監視器收集 Azure 虛擬機器內部主機記錄檔](learn/quick-collect-azurevm.md)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

- [如何開始使用 Azure 監視器和第三方 SIEM 整合](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指導**方針： Azure 監視器使用活動記錄，會自動啟用活動記錄檔，並記錄 Azure 監視器資源上所採取的作業，例如：啟動作業的人員、作業發生的時間、作業的狀態和其他有用的審核資訊。 

- [如何使用 Azure 監視器收集平臺記錄和計量](platform/diagnostic-settings.md)

- [瞭解 Azure 中的記錄和不同的記錄類型](platform/platform-logs-overview.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指導**方針：在 Azure 監視器中，根據您組織的合規性法規來設定 Log Analytics 工作區保留期限。 在記錄的任何長期/封存儲存體中使用 Azure 儲存體帳戶。

- [變更 Log Analytics 中的資料保留期限](platform/manage-cost-storage.md#change-the-data-retention-period)

- [如何設定 Azure 儲存體客戶紀錄的保留原則](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和審核記錄

**指導**方針：分析和監視記錄中的異常行為，並定期查看結果。 使用 Azure 監視器與 Log Analytics 工作區來檢查記錄，並對記錄資料執行查詢。

或者，您可以啟用和內部資料來 Azure Sentinel 或協力廠商 SIEM。

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

- [開始使用 Log Analytics 查詢](log-query/get-started-portal.md)

- [如何在 Azure 監視器中執行自訂查詢](log-query/get-started-queries.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：啟用異常活動的警示

**指導**方針：搭配使用 Azure 資訊安全中心與 Log Analytics 工作區來監視和警示在安全性記錄和事件中找到的異常活動。 或者，您可以啟用和內部資料以 Azure Sentinel。

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

- [如何在 Azure 資訊安全中心中管理警示](../security-center/security-center-managing-and-responding-alerts.md)

- [如何對 log analytics 記錄資料發出警示](learn/tutorial-response.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱 [Azure 安全性基準測試：身分識別與存取控制](../security/benchmarks/security-control-identity-access-control.md)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的清查

**指導**方針： azure 角色型存取控制 (azure RBAC) 可讓您透過角色指派來管理 Azure 資源的存取權。 您可以將這些角色指派給使用者、群組服務主體和受控識別。 某些資源有預先定義的內建角色，而這些角色可透過 Azure CLI、Azure PowerShell 或 Azure 入口網站等工具進行清查或查詢。

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指引**：建立使用專用系統管理帳戶的標準作業程序。 使用 Azure 資訊安全中心的身分識別與存取管理來監視系統管理帳戶的數目。

您也可以使用適用于 Microsoft 服務 Azure AD Privileged Identity Management 特殊許可權角色，並 Azure Resource Manager，來啟用及時且足夠的存取權。 

- [Azure AD Privileged Identity Management 總覽](../active-directory/privileged-identity-management/pim-configure.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory 單一登入 (SSO) 

**指導**方針：盡可能使用 Azure Active Directory SSO，而不是針對個別服務設定個別的獨立認證。 使用 Azure 資訊安全中心身分識別和存取管理建議。

- [瞭解 Azure AD 的 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：所有以 Azure Active Directory 為基礎的存取都使用多重要素驗證

**指導**方針：啟用 Azure AD MFA，並遵循 Azure 資訊安全中心身分識別和存取建議。

- [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

- [如何在 Azure 資訊安全中心監視身分識別和存取](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦 (特殊權限存取工作站) 進行所有系統管理工作

**指導**方針：使用安全且受 Azure 管理的工作站 (也稱為「特殊許可權存取工作站」，或 PAW 需要較高許可權的系統管理工作) 。

- [瞭解安全、受 Azure 管理的工作站](../active-directory/devices/concept-azure-managed-workstation.md)

- [如何啟用 Azure AD MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：來自系統管理帳戶的可疑活動記錄和警示

**指導**方針：使用 Azure Active Directory 安全性報告和監視，來偵測環境中發生可疑或不安全的活動時。 使用 Azure 資訊安全中心來監視身分識別和存取活動。 

- [如何識別已標示為有風險活動的 Azure AD 使用者](../active-directory/identity-protection/overview-identity-protection.md)

- [如何在 Azure 資訊安全中心監視使用者的身分識別和存取活動](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指引**：使用條件式存取具名位置，只允許從 IP 位址範圍或國家/區域的特定邏輯群組存取。

- [如何在 Azure 中設定具名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導**方針：使用 Azure Active Directory (Azure AD) 作為中央驗證和授權系統。 Azure AD 會對待用資料和傳輸中資料使用增強式加密，以保護資料安全。 Azure AD 也會對使用者認證進行 Salt 處理、雜湊處理並安全儲存資料。

- [如何建立和設定 Azure AD 實例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指引**：Azure AD 會提供記錄來協助探索過時的帳戶。 此外，使用 Azure 身分識別存取審核來有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 您可以定期檢閱使用者的存取權，以確定只有適當的使用者具有持續存取權。 

- [瞭解 Azure AD 報告](../active-directory/reports-monitoring/index.yml)

- [如何使用 Azure 身分識別存取權檢閱](../active-directory/governance/access-reviews-overview.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：監視嘗試存取已停用的認證

**指引**：您可以存取 Azure AD 登入活動、稽核和風險事件記錄檔來源，讓您可以與任何 SIEM/監視工具整合。 若要簡化此流程，您可以建立 Azure Active Directory 使用者帳戶的診斷設定，並將稽核記錄和登入記錄傳送到 Log Analytics 工作區。 您可以在 Log Analytics 工作區中設定所需的警示。

- [如何將 Azure 活動記錄整合到 Azure 監視器中](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure 資訊安全中心監視**：未設定。 請在工作專案中提供值。

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

**指導**方針：可能的話，請使用標記來協助追蹤儲存或處理敏感資訊的 Azure 監視器資源，例如 Log Analytics 工作區。

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

- [管理 Azure 監視器中記錄資料和工作區的存取](platform/manage-access.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指導**方針：針對個別的安全性網域（例如環境類型和資料敏感度層級），使用不同的訂用帳戶和管理群組來執行隔離。 您可以限制對您的應用程式和企業環境要求的 Azure 監視器和相關資源的存取層級。 您可以透過 Azure Active Directory 角色型存取控制來控制對 Azure 監視器的存取。

- [如何建立額外的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md)

- [如何建立管理群組](../governance/management-groups/create-management-group-portal.md)

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導**方針： Azure 監視器預設會協商 TLS 1.2。 確定任何連線至 Azure 資源的用戶端都能夠協商 TLS 1.2 或更新版本。 

Application Insights 和 Log Analytics 都會繼續允許內嵌 TLS 1.1 和 TLS 1.0 資料。 在用戶端上設定，可將資料限制為 TLS 1.2。

- [如何使用 TLS 1.2 安全地傳送資料](platform/data-security.md#sending-data-securely-using-tls-12)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

**指導**方針： Azure 監視器尚無法使用資料識別、分類和遺失防護功能。 若需要達到合規性目標，請實作協力廠商解決方案。
針對 Microsoft 管理的基礎平台，Microsoft 會將所有客戶內容視為敏感性資訊，並竭盡全力防範客戶資料外洩和暴露。 為了確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制項和功能。

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6：使用角色型存取控制來控制資源的存取權

**指導**方針：使用 Azure 角色型存取控制 (RBAC) 來管理 Azure 監視器的存取權。

- [Azure 監視器中的角色、權限與安全性](platform/roles-permissions-security.md)

- [如何設定 Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指導**方針： Azure 監視器使用 Microsoft 管理的金鑰 (MMK) ，確保所有資料和儲存的查詢都會加密。 Azure 監視器也提供使用您自己的金鑰進行加密的選項，該金鑰會儲存在您的 Azure Key Vault 中，並使用系統指派的受控識別驗證來由儲存體存取。 此客戶管理的金鑰 (CMK) 可以是軟體或硬體 HSM 保護的金鑰。

- [Azure 監視器客戶管理的金鑰](platform/customer-managed-keys.md)

- [Log Analytics 資料金安全性](platform/data-security.md)

- [Application Insights 中的資料收集、保留和儲存](app/data-retention-privacy.md)

- [瞭解 Azure 中的待用加密](../security/fundamentals/encryption-atrest.md) 

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導**方針：使用 Azure 監視器搭配 Azure 活動記錄檔，以建立 Azure 監視器和相關資源中發生變更時的警示。

- [如何建立 Azure 活動記錄事件的警示](platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="vulnerability-management"></a>弱點管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：弱點管理](../security/benchmarks/security-control-vulnerability-management.md)。*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程序來排定所發現弱點的補救優先順序

**指導**方針：使用常見的風險評分方案 (例如，常見的弱點評分系統) ，或協力廠商掃描工具所提供的預設風險評等。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：清查和資產管理](../security/benchmarks/security-control-inventory-asset-management.md)。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自動化資產探索解決方案

**指導**方針：使用 Azure CLI 來查詢及探索訂用帳戶中 Azure 監視器資源。 確保您的租用戶中有適當的 (讀取) 權限，並列舉所有 Azure 訂用帳戶以及訂用帳戶內的資源。

- [Azure 監視器 CLI](/cli/azure/monitor?view=azure-cli-latest)

- [如何檢視您的 Azure 訂用帳戶](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [了解 Azure RBAC](../role-based-access-control/overview.md)

- [Azure 監視器中的角色、權限與安全性](platform/roles-permissions-security.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指導**方針：將標記套用至 Azure 監視器資源，以邏輯方式將這些資源組織成分類法。

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指導**方針：使用標記、管理群組和個別的訂用帳戶（如果適當）來組織和追蹤 Azure 監視器的相關資源。 請定期調節清查，並確保會及時刪除訂用帳戶中未經授權的資源。

- [如何建立額外的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md)

- [如何建立管理群組](../governance/management-groups/create-management-group-portal.md)

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定義和維護已核准 Azure 資源的清查

**指導**方針：根據您的組織需求，建立已核准 Azure 資源的清查，以及適用于計算資源的已核准軟體。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指導**方針：使用 Azure 原則對可在訂用帳戶中建立的資源類型施加限制。

使用 Azure Resource Graph 來查詢及探索其訂用帳戶內的資源。  確保已核准環境中的所有 Azure 資源。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Resource Graph Explorer 建立查詢](../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未經核准的 Azure 資源和軟體應用程式

**指導**方針：定期協調清查，並確保及時從訂用帳戶中刪除未經授權的 Azure 監視器相關資源。  

- [刪除 Azure Log Analytics 工作區](platform/delete-workspace.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指導**方針：使用 Azure 原則來限制您可以在環境中布建的 Azure 監視器相關資源。 

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md) 

- [如何使用 Azure 原則拒絕特定的資源類型](../governance/policy/samples/index.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制使用者與 Azure Resource Manager 互動的能力

**指導**方針：使用 azure 條件式存取，藉由設定「Microsoft Azure 管理」應用程式的「封鎖存取」，來限制使用者與 Azure 資源管理員互動的能力。

- [如何設定條件式存取以封鎖對 Azure 資源管理員的存取](../role-based-access-control/conditional-access-azure-management.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱 [Azure 安全性基準測試：安全](../security/benchmarks/security-control-secure-configuration.md)設定。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導**方針：使用自訂 Azure 原則定義來審核或強制執行 Azure 監視器相關資源的設定。 您也可以使用內建的 Azure 原則定義。

此外，Azure Resource Manager 能夠在 JavaScript 物件標記法 (的 JSON) 中匯出範本，您應該檢查這些設定，以確保設定符合/超過組織的安全性需求。

您也可以使用 Azure 資訊安全中心中的建議作為 Azure 資源的安全設定基準。

如果使用即時串流 APM 功能，除了檢測金鑰之外，請使用秘密 API 金鑰來保護通道。

- [保護 APM 即時計量資料流](app/live-stream.md#secure-the-control-channel)

- [如何檢視可用的 Azure 原則別名](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [教學課程：建立和管理原則來強制執行相容性](../governance/policy/tutorials/create-and-manage.md)

- [Azure 入口網站中的單一和多重資源匯出至範本](../azure-resource-manager/templates/export-template-portal.md)

- [安全性建議 - 參考指南](../security-center/recommendations-reference.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指導**方針：使用 Azure 原則 [拒絕] 和 [部署是否不存在]，在您的 Azure 監視器相關資源之間強制進行安全設定。  此外，您也可以使用 Azure Resource Manager 範本，維護您的組織所需的 Azure 監視器相關資源的安全性設定。

- [瞭解 Azure 原則效果](../governance/policy/concepts/effects.md)

- [建立和管理原則以強制執行合規性](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager 範本總覽](../azure-resource-manager/templates/overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導**方針：使用 Azure DevOps 安全地儲存和管理您的程式碼，例如自訂 Azure 原則和 Azure Resource Manager 範本。 若要存取您在 Azure DevOps 中管理的資源，您可以授與或拒絕特定使用者、內建安全性群組或 Azure Active Directory (Azure AD) （如果與 Azure DevOps 整合）中定義的群組，或與 TFS 整合的 Active Directory。

- [如何在 Azure DevOps 中儲存程式碼](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [關於 Azure DevOps 中的許可權和群組](/azure/devops/organizations/security/about-permissions)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署適用于 Azure 資源的設定管理工具

**指導**方針：使用 Azure 原則來定義和執行 Azure 監視器相關資源的標準安全性設定。 使用自訂 Azure 原則定義來審核或強制執行 Azure 監視器相關資源的安全性設定。 您也可以使用與特定資源相關的內建原則定義。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [Azure 原則別名](../governance/policy/concepts/definition-structure.md#aliases)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：執行 Azure 資源的自動化設定監視

**指導**方針：使用 Azure 資訊安全中心針對您 Azure 監視器的相關資源執行基準掃描。  此外，請使用 Azure 原則來警示和審核 Azure 資源設定。

- [如何修復 Azure 資訊安全中心中的建議](../security-center/security-center-remediate-recommendations.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 秘密

**指導**方針：使用受控服務識別搭配 Azure Key Vault，以針對支援的 Azure 監視器相關資源簡化和保護秘密管理。

- [如何與 Azure 受控識別整合](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [支援適用於 Azure 資源的受控識別服務](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [如何建立 Key Vault](../key-vault/secrets/quick-create-portal.md)

- [如何使用受控識別來提供 Key Vault authentication](../key-vault/general/assign-access-policy-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

**指導**方針：使用受控識別，在 Azure AD 中為 Azure 服務提供自動管理的身分識別。 受控識別可讓您向支援 Azure AD authentication 的任何服務進行驗證，包括 Azure 監視器資源，而不需要在您的程式碼中提供任何認證。 

- [如何設定 Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指引**：實作認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。

- [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*如需詳細資訊，請參閱 [Azure 安全性基準測試：惡意程式碼防護](../security/benchmarks/security-control-malware-defense.md)。*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指導**方針：支援 Azure 服務的基礎主機上已啟用 Microsoft 反惡意程式碼 (例如，Azure 監視器相關的資源) ，不過它不會在您的內容上執行。 

預先掃描要上傳至適用 Azure 監視器相關資源（例如 Log Analytics 工作區）的任何檔案。

使用 Azure 資訊安全中心的資料服務威脅偵測來偵測上傳至儲存體帳戶的惡意程式碼。 

- [瞭解 Azure 雲端服務和虛擬機器的 Microsoft 反惡意程式碼](../security/fundamentals/antimalware.md)

- [瞭解 Azure 資訊安全中心的資料服務威脅偵測](../security-center/azure-defender.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資料](../security/benchmarks/security-control-data-recovery.md)復原。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確定定期自動備份

**指導**方針：使用 Azure Resource Manager，在 JAVASCRIPT 物件標記法 (JSON) 範本中匯出 Azure 監視器和相關的資源，此範本可用來做為 Azure 監視器和相關設定的備份。  使用 Azure 自動化自動執行備份腳本。 

- [使用 Azure Resource Manager 範本管理 Log Analytics 工作區](./samples/resource-manager-workspace.md)

- [Azure 入口網站中的單一和多重資源匯出至範本](../azure-resource-manager/templates/export-template-portal.md)

- [關於 Azure 自動化](../automation/automation-intro.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份並備份任何客戶管理的金鑰

**指導**方針：使用 Azure Resource Manager，在 JAVASCRIPT 物件標記法 (JSON) 範本中匯出 Azure 監視器和相關的資源，此範本可用來做為 Azure 監視器和相關設定的備份。  如果 Azure 監視器相關資源使用客戶管理的金鑰，請在 Azure Key Vault 中備份客戶管理的金鑰。 

- [使用 Azure Resource Manager 範本管理 Log Analytics 工作區](./samples/resource-manager-workspace.md)

- [Azure 入口網站中的單一和多重資源匯出至範本](../azure-resource-manager/templates/export-template-portal.md)

- [如何在 Azure 中備份金鑰保存庫金鑰](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指導**方針：確定能夠使用 Azure Resource Manager 支援的範本檔案定期執行還原。  測試已備份之客戶管理金鑰的還原。

- [使用 Azure Resource Manager 範本管理 Log Analytics 工作區](./samples/resource-manager-workspace.md)

- [如何在 Azure 中還原金鑰保存庫金鑰](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰的保護

**指導**方針：使用 Azure DevOps 安全地儲存和管理您的程式碼，例如自訂 Azure 原則、Azure Resource Manager 範本。 若要保護您在 Azure DevOps 中管理的資源，您可以授與或拒絕特定使用者、內建安全性群組或 Azure Active Directory (Azure AD) （如果與 Azure DevOps 整合）中定義的群組，或與 TFS 整合的 Active Directory。   使用以角色為基礎的存取控制來保護客戶管理的金鑰。 

此外，請在 Key Vault 中啟用 Soft-Delete 和清除保護，以防止金鑰遭到意外或惡意刪除。 如果使用 Azure 儲存體儲存 Azure Resource Manager 範本備份，請在刪除 blob 或 blob 快照集時，啟用虛刪除來儲存及復原您的資料。 

- [如何在 Azure DevOps 中儲存程式碼](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [關於 Azure DevOps 中的許可權和群組](/azure/devops/organizations/security/about-permissions)

- [如何在 Key Vault 中啟用 Soft-Delete 和清除保護](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Azure 儲存體 Blob 的虛刪除](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

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

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指引**：使用「連續匯出」功能來匯出 Azure 資訊安全中心警示和建議，協助找出 Azure 資源的風險。 「連續匯出」可讓您以手動或持續不斷的方式來匯出警示和建議。 您可使用 Azure 資訊安全中心的資料連接器，將警示串流至 Azure Sentinel。 

- [如何設定連續匯出](../security-center/continuous-export.md) 

- [如何將警示串流至 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指導**方針：使用 Azure 資訊安全中心中的工作流程自動化功能，透過「Logic Apps」安全性警示和建議來自動觸發回應，以保護您的 Azure 資源。

- [如何設定工作流程自動化和 Logic Apps](../security-center/workflow-automation.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*如需詳細資訊，請參閱 [Azure 安全性基準測試：滲透測試和 red team 練習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：進行 Azure 資源的定期滲透測試，並確保修復所有重要的安全性結果

**指導**方針：遵循 Microsoft 的 Engagement 規則，以確保您的滲透測試不違反 Microsoft 原則。 針對受 Microsoft 管理的雲端基礎結構、服務和應用程式，使用 Microsoft 的策略和執行的 Red 小組和即時網站滲透測試。

- [滲透測試的參與規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red 小組](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](../security/benchmarks/overview.md)
- 深入了解 [Azure 資訊安全性基準](../security/benchmarks/security-baselines-overview.md)
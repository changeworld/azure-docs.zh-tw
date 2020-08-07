---
title: 適用于資訊安全中心的 Azure 安全性基準
description: 資訊安全中心的安全性基準提供程式指引和資源，可用於執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: security-center
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 9bd0f1cbe1f4797a0187952b94ca48077bb3134c
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87854237"
---
# <a name="azure-security-baseline-for-security-center"></a>適用于資訊安全中心的 Azure 安全性基準

此安全性基準會將[Azure 安全性基準測試](../security/benchmarks/overview.md)的指引套用至 Azure 資訊安全中心。 Azure 安全性基準提供如何在 Azure 上保護雲端解決方案的建議。 內容是依 Azure 安全性基準測試所定義的**安全性控制**，以及適用于 Azure 資訊安全中心的相關指引來分組。 不適用 Azure 資訊安全中心的**控制項**已排除。 若要查看 Azure 資訊安全中心如何完全對應到 Azure 安全性基準，請參閱[完整的 Azure 資訊安全中心安全性基準對應](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)檔案。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱[Azure 安全性基準測試：網路安全性](/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保護虛擬網路中的 Azure 資源

**指引**： Azure 資訊安全中心是核心 Azure 供應專案。 您無法將虛擬網路、子網或網路安全性群組直接關聯到資訊安全中心。 如果您啟用計算資源的資料收集，則資訊安全中心會將它收集的資料儲存在 Log Analytics 工作區中，您可以將該工作區設定為使用私人連結，以透過虛擬網路中的私人端點存取您的工作區資料。 此外，如果使用資料收集資訊安全中心會依賴部署至伺服器的 Log Analytics 代理程式來收集安全性資料，並為這些計算資源提供保護。 Log Analytics 代理程式需要使用特定埠和通訊協定來開啟資訊安全中心的適當操作。 鎖定您的網路，只允許這些必要的埠和通訊協定，並且只新增應用程式透過網路安全性群組運作所需的其他規則。

- [Azure 資訊安全中心的資料收集](security-center-enable-data-collection.md)

- [具有網路安全性群組的檔案管理工具網路流量](../virtual-network/tutorial-filter-network-traffic.md)

- [使用 Log Analytics 代理程式的防火牆需求](../azure-monitor/platform/log-analytics-agent.md#firewall-requirements)

- [瞭解 Azure 私人連結](../private-link/private-link-overview.md) 

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指導**方針： Azure 資訊安全中心供應專案不會直接與虛擬網路整合，但它可以從您網路上部署 Log Analytics 代理程式所設定的伺服器收集資料。 設定為將資料傳送至資訊安全中心的伺服器，需要允許特定埠和通訊協定才能正常進行通訊。 使用 Azure 原則定義和強制執行這些網路資源的標準安全性設定。

您也可以使用 Azure 藍圖，藉由在單一藍圖定義中封裝金鑰環境成品（例如 Azure Resource Manager 範本、角色指派和 Azure 原則指派），以簡化大規模的 Azure 部署。 您可以將藍圖套用至新的訂用帳戶，以一致且安全的方式部署資訊安全中心設定和相關的網路資源。

- [Azure 資訊安全中心的資料收集](security-center-enable-data-collection.md)

- [使用 Log Analytics 代理程式的防火牆需求](../azure-monitor/platform/log-analytics-agent.md#firewall-requirements)

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md) 

- [網路的 Azure 原則範例](../governance/policy/samples/built-in-policies.md#network)

- [如何建立 Azure 藍圖](../governance/blueprints/create-blueprint-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

**指導**方針： Azure 資訊安全中心供應專案不會直接與虛擬網路整合，但它可以從您網路上部署 Log Analytics 代理程式所設定的伺服器收集資料。 設定為將資料傳送至資訊安全中心的伺服器，需要允許特定埠和通訊協定才能正常進行通訊。 使用 Azure 原則定義和強制執行這些網路資源的標準安全性設定。

使用網路安全性群組和其他資源（例如網路上的伺服器，設定為將安全性記錄檔傳送至 Azure 資訊安全中心）的資源標記。 針對個別的網路安全性群組規則，請使用 [描述] 欄位來記錄允許進出網路流量的規則。 

使用與標記相關的任何內建 Azure 原則定義（例如「需要標記和其值」），以確保所有資源都是以標籤建立，並通知您現有的未標記資源。

您可以使用 Azure PowerShell 或 Azure CLI，根據其標記來查閱或執行資源的動作。 

- [Azure 資訊安全中心的資料收集](security-center-enable-data-collection.md)

- [使用 Log Analytics 代理程式的防火牆需求](../azure-monitor/platform/log-analytics-agent.md#firewall-requirements)

- [如何建立和使用標籤](/azure/azure-resource-manager/resource-group-using-tags) 

- [如何建立 Azure 虛擬網路](../virtual-network/quick-create-portal.md) 

- [如何使用網路安全性群組規則來篩選網路流量](../virtual-network/tutorial-filter-network-traffic.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導**方針：使用 Azure 活動記錄來監視資源設定，並偵測與 Azure 資訊安全中心相關之網路資源的變更。 在 Azure 監視器中建立警示，以在重大資源變更發生時通知您。

- [如何查看和取出 Azure 活動記錄事件](/azure/azure-monitor/platform/activity-log-view) 

- [如何在 Azure 監視器中建立警示](../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱[Azure 安全性基準測試：記錄和監視](/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指引**：使用中央 Log Analytics 工作區，匯總 Azure 資訊安全中心及其連線來源所產生的安全性資料。 

設定資訊安全中心的資料收集，以便將安全性資料和事件從連線的 Azure 計算資源傳送至中央 Log Analytics 工作區。 除了資料收集之外，請使用「連續匯出」功能，將資訊安全中心所產生的安全性警示和建議串流至您的中央 Log Analytics 工作區。 在 Azure 監視器中，您可以查詢和執行從資訊安全中心產生的安全性資料和已連線的 Azure 資源分析。 

或者，您可以將資訊安全中心所產生的資料傳送給 Azure Sentinel 或協力廠商 SIEM。

- [匯出安全性警訊和建議](continuous-export.md)

- [Azure 資訊安全中心的資料收集](security-center-enable-data-collection.md)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md) 

- [如何使用 Azure 監視器收集平臺記錄和計量](../azure-monitor/platform/diagnostic-settings.md) 

- [如何使用 Azure 監視器收集 Azure 虛擬機器內部主機記錄檔](../azure-monitor/learn/quick-collect-azurevm.md)

- [如何開始使用 Azure 監視器和第三方 SIEM 整合](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指導**方針： Azure 監視器活動記錄會自動提供使用，這些記錄包含資源的所有寫入作業，例如 Azure 資訊安全中心，包括哪些作業已執行、誰開始操作，以及發生的時間。 將您的 Azure 活動記錄傳送至 Log Analytics 工作區，以進行記錄匯總並增加保留期。

- [如何使用 Azure 監視器收集平臺記錄和計量](../azure-monitor/platform/diagnostic-settings.md) 

- [瞭解 Azure 中的記錄和不同的記錄類型](../azure-monitor/platform/platform-logs-overview.md)

- [將活動記錄傳送至 Log Analytics 工作區](../azure-monitor/platform/activity-log.md#send-to-log-analytics-workspace)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指引**：在 Azure 監視器中，根據貴組織的合規性法規來設定您的 Log Analytics 工作區保留期間。 針對長期和封存儲存體使用 Azure 儲存體帳戶。 

- [變更 Log Analytics 中的資料保留期限](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period) 

- [如何設定 Azure 儲存體客戶紀錄的保留原則](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和審核記錄

**指引**：分析和監視 Azure 資訊安全中心及其連線來源所產生的記錄，以瞭解異常行為，並定期檢查結果。 使用 Azure 監視器和 Log Analytics 工作區來審查記錄，並對記錄資料執行查詢。

或者，您可以啟用和內部資料，以 Azure Sentinel 或協力廠商 SIEM。 

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md) 

- [開始使用 Log Analytics 查詢](../azure-monitor/log-query/get-started-portal.md) 

- [如何在 Azure 監視器中執行自訂查詢](../azure-monitor/log-query/get-started-queries.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：啟用異常活動的警示

**指導**方針：設定 Azure 監視器記錄警示，以查詢您的活動記錄檔或 Azure 資訊安全中心所產生的資料所記錄的不必要或異常活動。 設定動作群組，讓您的組織獲得通知，並可在針對異常活動起始記錄警示時採取動作。 使用資訊安全中心工作流程自動化功能，針對安全性警示和建議觸發邏輯應用程式。 資訊安全中心工作流程可以用來通知使用者事件回應，或採取動作以根據警示資訊來補救資源。

或者，您也可以啟用和所產生的和 Azure 資訊安全中心的相關資料，以 Azure Sentinel。 Azure Sentinel 支援可針對安全性相關問題進行自動威脅回應的腳本。

- [Azure 資訊安全中心工作流程自動化](workflow-automation.md)

- [如何管理 Azure 資訊安全中心中的警示](security-center-managing-and-responding-alerts.md) 

- [如何對 log analytics 記錄資料發出警示](../azure-monitor/learn/tutorial-response.md)

- [在 Azure Sentinel 中設定自動化威脅回應](../sentinel/tutorial-respond-threats-playbook.md)

- [Azure 監視器中的記錄警示](../azure-monitor/platform/alerts-unified-log.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱[Azure 安全性基準測試：身分識別和存取控制](/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的清查

**指引**： Azure 角色型存取控制 (RBAC) 可讓您透過角色指派來管理 azure 資源的存取權。 您可以將這些角色指派給使用者、群組服務主體和受控識別。 某些資源有預先定義的內建角色，而且這些角色可以透過 Azure CLI、Azure PowerShell 或 Azure 入口網站之類的工具進行清查或查詢。 Azure 資訊安全中心具有「安全性讀取者」或「安全性」 Admin 的內建角色，可讓使用者讀取或更新安全性原則，以及解除警示和建議。

- [Azure 資訊安全中心的權限](security-center-permissions.md)

- [如何使用 PowerShell 取得 Azure AD 中的目錄角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0) 

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指導**方針：建立適用于 Azure 平臺的專用系統管理帳戶，或特定于 Azure 資訊安全中心供應專案的標準操作程式。 使用 Azure 資訊安全中心身分識別與存取管理來監視 Azure Active Directory 中的系統管理帳戶數目。 資訊安全中心也有「安全性管理員」的內建角色，可讓使用者更新安全性原則，並關閉警示和建議，確保您可以定期檢查並協調具有此角色指派的任何使用者。

此外，為了協助您追蹤專用系統管理帳戶，您可使用來自 Azure 資訊安全中心或內建 Azure 原則的建議，例如：

- 應將一個以上的擁有者指派給您的訂用帳戶
- 具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除
- 具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除

- [Azure 資訊安全中心的權限](security-center-permissions.md)

- [如何使用 Azure 資訊安全中心來監視身分識別和存取 (預覽)](security-center-identity-access.md)

- [如何使用 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用單一登入 (SSO) 搭配 Azure Active Directory

**指導**方針：盡可能使用 Azure Active Directory SSO，而不是設定個別服務的獨立認證。 使用 Azure 資訊安全中心身分識別和存取建議。

- [瞭解使用 Azure AD 的 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：針對所有以 Azure Active Directory 為基礎的存取使用多重要素驗證

**指引**：啟用 Azure Active Directory MFA 來存取 Azure 資訊安全中心和 Azure 入口網站，請遵循任何資訊安全中心身分識別和存取建議。 

- [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md) 

- [如何在 Azure 資訊安全中心監視身分識別和存取](security-center-identity-access.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦 (特殊權限存取工作站) 進行所有系統管理工作

**指導**方針：使用安全、受 Azure 管理的工作站 (也稱為特殊許可權存取工作站，或 PAW) ，以執行需要提高許可權的系統管理工作。

- [瞭解安全、受 Azure 管理的工作站](../active-directory/devices/concept-azure-managed-workstation.md)

- [如何啟用 Azure AD MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：從系統管理客戶紀錄及警示可疑活動

**指導**方針：使用 Azure Active Directory 安全性報告和監視，來偵測環境中發生可疑或不安全的活動。 使用 Azure 資訊安全中心來監視身分識別和存取活動。

- [如何識別已標示為有風險活動的 Azure AD 使用者](/azure/active-directory/reports-monitoring/concept-user-at-risk) 

- [如何在 Azure 資訊安全中心監視使用者的身分識別和存取活動](security-center-identity-access.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指導**方針：使用 Azure AD 命名位置，只允許來自 IP 位址範圍或國家/地區的特定邏輯群組進行存取。 

- [如何設定 Azure AD 命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導**方針：使用 Azure 資訊安全中心時，Azure Active Directory (Azure AD) 作為中央驗證和授權系統。 Azure AD 會對待用資料和傳輸中資料使用增強式加密，以保護資料安全。 Azure AD 也會對使用者認證進行 Salt 處理、雜湊處理並安全儲存資料。 Azure 資訊安全中心具有可指派的內建角色，例如「安全性 Admin」，可讓使用者更新安全性原則，並關閉警示和建議。

- [Azure 資訊安全中心的權限](security-center-permissions.md)

- [如何建立和設定 Azure AD 實例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並調節使用者存取權

**指引**：Azure Active Directory 會提供記錄來協助探索過時的帳戶。 此外，您還可以使用 Azure AD 身分識別和存取審查，有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 與 Azure 資訊安全中心相關的使用者存取權可以定期檢查，以確保只有適當的使用者才能繼續存取。 

- [瞭解 Azure AD 報告](/azure/active-directory/reports-monitoring/) 

- [如何使用 Azure AD 身分識別和存取評論](../active-directory/governance/access-reviews-overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：監視嘗試存取已停用的認證

**指引**：您可以存取 Azure AD 登入活動、audit 和風險事件記錄檔來源，讓您可以與任何 SIEM/監視工具整合。 

若要簡化此程式，您可以建立 Azure AD 使用者帳戶的診斷設定，並將 audit 記錄和登入記錄傳送到 Log Analytics 工作區。 您可以在 Log Analytics 工作區中設定所需的警示。  

- [如何整合 Azure 活動記錄與 Azure 監視器](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帳戶登入行為偏差警示 

**指引**：使用 Azure AD Identity Protection 功能，為偵測到與使用者身分識別相關的可疑動作設定自動回應。 您也可將資料內嵌到 Azure Sentinel 中，以便進一步調查。 

- [如何檢視有風險的 Azure AD 登入](/azure/active-directory/reports-monitoring/concept-risky-sign-ins) 

- [如何設定和啟用身分識別保護風險原則](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱[Azure 安全性基準測試：資料保護](/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的清查

**指引**：使用標籤協助追蹤 Azure 資源，例如 Log Analytics 工作區，其會儲存 Azure 資訊安全中心的機密安全性資訊。

- [如何建立和使用標籤](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指導**方針：使用個別的訂用帳戶和管理群組來執行隔離，以用於環境類型和資料敏感度層級等個別的安全性網域。 您可以限制您的應用程式和企業環境所需的 Azure 資源存取層級。 您可以透過 Azure Active Directory RBAC 來控制對 Azure 資源的存取。

根據預設，Azure 資訊安全中心的資料會儲存在資訊安全中心後端服務中。 如果您的組織已新增在您自己的資源中儲存此資料的需求，您可以設定 Log Analytics 工作區來儲存資訊安全中心資料、警示和建議。 使用您自己的工作區時，您可以根據資料的來源環境設定不同的工作區，以增加進一步的分隔。

- [匯出安全性警訊和建議](continuous-export.md)

- [Azure 資訊安全中心的資料收集](security-center-enable-data-collection.md)

- [如何建立額外的 Azure 訂閱](/azure/billing/billing-create-subscription) 

- [如何建立管理群組](../governance/management-groups/create.md) 

- [如何建立和使用標籤](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指引**：加密傳輸中的所有機密資訊。 請確定任何連線到您 Azure 資源的用戶端都能協商 TLS 1.2 或更高版本。 使用 Log Analytics 代理程式設定並將資料傳送到 Azure 安全性中心的任何虛擬機器，都應設定為使用 TLS 1.2。

遵循 Azure 資訊安全中心的待用加密建議和傳輸中的加密（如果適用）。 

- [將資料安全地傳送到 Log Analytics](../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12)

- [瞭解使用 Azure 傳輸加密](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="46-use-role-based-access-controls-to-control-access-to-resources"></a>4.6：使用以角色為基礎的存取控制來控制對資源的存取 

**指導**方針：使用 Azure 角色型存取控制來管理 Azure 資訊安全中心相關資料和資源的存取權。 Azure 資訊安全中心具有「安全性讀取者」或「安全性」 Admin 的內建角色，可讓使用者讀取或更新安全性原則，以及解除警示和建議。 儲存資訊安全中心所收集之資料的 Log Analytics 工作區，也具有您可以指派的內建角色，例如「Log Analytics 讀取者」、「Log Analytics 參與者」和其他。 指派使用者完成其必要工作所需的最寬鬆角色。 例如，將「讀取者」角色指派給只需要檢視資源安全性狀態的相關資訊，但不採取行動的使用者，例如套用建議或編輯原則。

- [Azure Log Analytics 工作區的許可權](../role-based-access-control/built-in-roles.md#log-analytics-reader)

- [Azure 資訊安全中心的權限](security-center-permissions.md)

- [如何在 Azure 中設定 RBAC](../role-based-access-control/role-assignments-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指導**方針： Azure 資訊安全中心使用已設定的 Log Analytics 工作區來儲存所產生的資料、警示和建議。 針對您為資訊安全中心資料收集所設定的工作區，設定客戶管理的金鑰 (CMK) 。 CMK 可讓所有儲存或傳送至工作區的資料，使用您所建立和擁有的 Azure Key Vault 金鑰進行加密。 

- [Azure 監視器客戶管理的金鑰](../azure-monitor/platform/customer-managed-keys.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導**方針：使用 Azure 監視器，在發生變更時，建立警示與 Azure 資訊安全中心相關的重要 Azure 資源。 這些變更可能包括修改與安全性中心相關之設定的任何動作，例如停用警示或建議，或是更新或刪除資料存放區。

- [如何建立 Azure 活動記錄事件的警示](../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="vulnerability-management"></a>弱點管理

*如需詳細資訊，請參閱[Azure 安全性基準測試：弱點管理](/azure/security/benchmarks/security-control-vulnerability-management)。*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程序來排定所發現弱點的補救優先順序

**指導**方針：使用常見的風險評分計畫 (例如，常見的弱點評分系統) 或協力廠商掃描工具所提供的預設風險評等。

- [NIST 發行--常見的弱點評分系統](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱[Azure 安全性基準測試：清查和資產管理](/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自動化資產探索解決方案

**指引**：使用 Azure Resource Graph 來查詢和探索與您的訂用帳戶中 Azure 資訊安全中心相關的所有資源。 請確定您的租使用者中有適當的 (讀取) 許可權，並列舉所有 Azure 訂用帳戶以探索資訊安全中心資源。 

- [如何使用 Azure Resource Graph Explorer 建立查詢](../governance/resource-graph/first-query-portal.md) 

- [如何查看您的 Azure 訂用帳戶](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0) 

- [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指引**：使用標籤協助追蹤 Azure 資源，例如 Log Analytics 工作區，其會儲存 Azure 資訊安全中心的機密安全性資訊。

- [如何建立和使用標籤](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指引**：適當地使用標記、管理群組和個別訂用帳戶來組織和追蹤 Azure 資訊安全中心資源。 請定期調節清查，並確保會及時刪除訂用帳戶中未經授權的資源。

此外，使用下列內建原則定義，透過 Azure 原則對可在客戶訂用帳戶中建立的資源類型施加限制：

- 不允許的資源類型
- 允許的資源類型

- [如何建立額外的 Azure 訂閱](/azure/billing/billing-create-subscription)

- [如何建立管理群組](../governance/management-groups/create.md)

- [如何建立和使用標籤](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定義並維護已核准 Azure 資源的清查

**指導**方針：根據您的組織需求，建立已核准 Azure 資源的清查和適用于計算資源的已核准軟體。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指導**方針：使用 Azure 原則來對可在訂用帳戶中建立的資源類型施加限制。 

使用 Azure Resource Graph 來查詢和探索其訂用帳戶內的資源。  確保已核准環境中的所有 Azure 資源。 

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md) 

- [如何使用 Azure Resource Graph Explorer 建立查詢](../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未經核准的 Azure 資源和軟體應用程式

**指引**：當您的組織清查和審核程式中不再需要與 Azure 資訊安全中心相關的 Azure 資源時，請將其移除。

- [Azure 資源群組和資源刪除](../azure-resource-manager/management/delete-resource-group.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指引**：使用下列內建原則定義，利用 Azure 原則來限制可在訂用帳戶中建立的資源類型：

- 不允許的資源類型
- 允許的資源類型

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure 原則拒絕特定的資源類型](/azure/governance/policy/samples/not-allowed-resource-types)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制使用者與 Azure Resource Manager 互動的能力

**指引**：設定 Azure 條件式存取，以藉由對「Microsoft Azure 管理」應用程式設定「封鎖存取」，限制使用者與 Azure Resource Manager 互動的能力。

- [如何設定條件式存取以封鎖 Azure Resource Manager 的存取](../role-based-access-control/conditional-access-azure-management.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱[Azure 安全性基準測試：安全](/azure/security/benchmarks/security-control-secure-configuration)設定。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指引**：透過 Azure 原則定義和執行 Azure 資訊安全中心及其連線工作區的標準安全性設定。 在 "Microsoft.operationalinsights" 和 "Microsoft. Security" 命名空間中使用 Azure 原則別名，以建立自訂 Azure 原則定義來審查或強制執行資訊安全中心及其 Log Analytics 工作區的設定。

- [如何檢視可用的 Azure 原則別名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指引**：使用「拒絕」和「如果不存在時部署」的 Azure 原則效果，在您的 Azure 資源上強制執行安全設定。 此外，您可以使用 Azure Resource Manager 範本來維護貴組織所需之 Azure 資源的安全性設定。 

- [瞭解 Azure 原則效果](../governance/policy/concepts/effects.md) 

- [建立和管理原則以強制執行合規性](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Resource Manager 範本總覽](../azure-resource-manager/templates/overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指引**：使用 Azure DevOps 安全地儲存和管理您的程式碼，例如自訂 Azure 原則定義、Azure Resource Manager 範本和預期狀態設定腳本。 若要存取您在 Azure DevOps 中管理的資源，您可以授與或拒絕特定使用者、內建安全性群組或 Azure Active Directory (Azure AD) （如果與 Azure DevOps 整合）中所定義之群組的許可權，或 Active Directory （如果與 TFS 整合）。 

- [如何在 Azure DevOps 中儲存程式碼](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops) 

- [關於 Azure DevOps 中的許可權和群組](/azure/devops/organizations/security/about-permissions)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署適用于 Azure 資源的設定管理工具

**指引**：使用 Azure 原則定義和執行 Azure 資源的標準安全性設定。 使用 Azure 原則別名來建立自訂原則，以審核或強制執行 Azure 資訊安全中心相關資源的設定。 此外，您可以使用 Azure 自動化來部署設定變更。 

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md) 

- [如何使用別名](../governance/policy/concepts/definition-structure.md#aliases)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：執行 Azure 資源的自動化設定監視

**指引**：使用內建的 Azure 原則定義以及 "microsoft.operationalinsights" 和 "microsoft" 中 Azure 原則別名。 安全性「命名空間」，用來建立自訂原則以警示、audit 及強制執行 Azure 資源設定。 使用 Azure 原則效果「audit」、「拒絕」和「如果不存在即部署」，自動強制執行 Azure 資源的設定。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 秘密

**指導**方針： Azure 資訊安全中心使用已設定的 Log Analytics 工作區來儲存所產生的資料、警示和建議。 針對您為資訊安全中心資料收集所設定的工作區，設定客戶管理的金鑰 (CMK) 。 CMK 可讓所有儲存或傳送至工作區的資料，使用您所建立和擁有的 Azure Key Vault 金鑰進行加密。 

- [Azure 監視器客戶管理的金鑰](../azure-monitor/platform/customer-managed-keys.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指引**：實作認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。

- [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*如需詳細資訊，請參閱[Azure 安全性基準測試：惡意程式碼防護](/azure/security/benchmarks/security-control-malware-defense)。*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指導**方針： Azure 資訊安全中心不適合用來儲存或處理檔案。 您必須負責預先掃描上傳至非計算 Azure 資源的任何內容，包括 Log Analytics 工作區。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱[Azure 安全性基準測試：資料](/azure/security/benchmarks/security-control-data-recovery)復原。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確保會定期自動備份 

**指導**方針：遵循基礎結構即程式碼 (IAC) 方法，並使用 Azure Resource Manager 在 JAVASCRIPT 物件標記法的 JSON (範本中部署您 Azure 資訊安全中心相關的資源，這可用來做為資源相關設定的備份。

- [單一和多重資源匯出至 Azure 入口網站中的範本](../azure-resource-manager/templates/export-template-portal.md)

- [Azure Resource Manager 安全性資源的範本](/azure/templates/microsoft.security/allversions)

- [關於 Azure 自動化](../automation/automation-intro.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份，並備份任何客戶管理的金鑰

**指導**方針： Azure 資訊安全中心使用 Log Analytics 工作區來儲存所產生的資料、警示和建議。 您可以設定 Azure 監視器，以及資訊安全中心用來啟用客戶管理金鑰的工作區。 如果您使用 Key Vault 來儲存客戶管理的金鑰，請確保金鑰的定期自動備份。

- [如何備份 Key Vault 金鑰](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指導**方針：確保能夠使用 Azure Resource Manager 支援的範本檔案定期執行還原。 已備份客戶管理金鑰的測試還原。

- [使用 Azure Resource Manager 範本管理 Log Analytics 工作區](../azure-monitor/platform/template-workspace-configuration.md)

- [如何在 Azure 中還原金鑰保存庫金鑰](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理金鑰的保護

**指引**：使用 Azure DevOps 安全地儲存和管理您的程式碼，例如自訂 Azure 原則定義和 Azure Resource Manager 範本。 若要保護您在 Azure DevOps 中管理的資源，您可以授與或拒絕特定使用者、內建安全性群組或 Azure Active Directory (Azure AD 中定義之群組的許可權) 如果與 Azure DevOps 整合，或 Active Directory 與 TFS 整合。 使用以角色為基礎的存取控制來保護客戶管理的金鑰。

此外，在 Key Vault 中啟用虛刪除和清除保護，以防止金鑰遭到意外或惡意刪除。  如果 Azure 儲存體用來儲存 Azure Resource Manager 範本備份，請啟用虛刪除，以在 blob 或 blob 快照集遭到刪除時儲存並復原您的資料。 

- [如何在 Azure DevOps 中儲存程式碼](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [關於 Azure DevOps 中的許可權和群組](/azure/devops/organizations/security/about-permissions)

- [如何在 Key Vault 中啟用虛刪除和清除保護](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal) 

- [Azure 儲存體 Blob 的虛刪除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱[Azure 安全性基準測試：事件回應](/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指導**方針：為您的組織開發事件回應指南。 請確定有寫入的事件回應計畫，可定義人員的所有角色，以及事件處理和管理的階段，以偵測到後續事件審查。 

- [建立自有安全性事件回應程序的指引](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft 安全性回應中心的事件剖析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [使用 NIST 的電腦安全性性事件處理指南，協助建立您自己的事件回應計畫](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序

**指導**方針： Azure 資訊安全中心指派每個警示的嚴重性，以協助您排定應先調查哪些警示。 嚴重性的依據，在於資訊安全中心對於據以發出警示的發現結果或分析結果有多少信心，以及認定導致警示的活動背後存在惡意意圖的把握程度。

此外，使用標記來標示訂用帳戶，並建立命名系統來識別和分類 Azure 資源，特別是那些處理敏感性資料的帳戶。  您必須負責根據發生事件的 Azure 資源和環境的重要性排定警示補救的優先順序。 

- [Azure 資訊安全中心的安全性警示](security-center-alerts-overview.md) 

- [使用標記來組織 Azure 資源](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指導**方針：執行練習以定期測試系統的事件回應功能，以協助保護您的 Azure 資源。 找出弱式點和差距，然後視需要修改您的回應計畫。 

- [NIST 發行--適用于 IT 計畫和功能的測試、訓練和練習計劃指南](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引**：如果 Microsoft 安全性回應中心 (MSRC) 發現您的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡資訊來連絡您。 事後檢討事件，確保問題已解決。 

- [如何設定 Azure 資訊安全中心安全性連絡人](security-center-provide-security-contact-details.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指引**：使用「連續匯出」功能來匯出您的 Azure 資訊安全中心警示和建議，以協助找出 Azure 資源的風險。 「連續匯出」可讓您以手動或持續的方式來匯出警示和建議。 您可以使用 Azure 資訊安全中心資料連線器，將警示串流至 Azure Sentinel。 

- [如何設定連續匯出](continuous-export.md) 

- [如何將警示串流至 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指引**：使用工作流程自動化功能 Azure 資訊安全中心自動觸發對安全性警示的回應，以及保護您的 Azure 資源的建議。 

- [如何在資訊安全中心中設定工作流程自動化](workflow-automation.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*如需詳細資訊，請參閱[Azure 安全性基準測試：滲透測試和 red team 練習](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期滲透測試您的 Azure 資源，並確保修復所有重要的安全性結果

**指導**方針：請遵循 Engagement 的 Microsoft Cloud 滲透測試規則，以確保您的滲透測試不會違反 Microsoft 原則。 針對 Microsoft 管理的雲端基礎結構、服務和應用程式，使用 Microsoft 的 Red 小組和即時網站滲透測試策略和執行。 

- [Engagement 的滲透測試規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red 小組](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](/azure/security/benchmarks/overview)
- 深入了解 [Azure 資訊安全性基準](/azure/security/benchmarks/security-baselines-overview)

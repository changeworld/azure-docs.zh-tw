---
title: 適用于 Azure Private Link 的 Azure 安全性基準
description: Azure Private Link 安全性基準提供的程式指引和資源，可讓您執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: private-link
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 654fc453f0b4167ae91afcab811de321925c6bf5
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89614642"
---
# <a name="azure-security-baseline-for-azure-private-link"></a>適用于 Azure Private Link 的 Azure 安全性基準

此安全性基準會將 [Azure 安全性基準測試](../security/benchmarks/overview.md) 的指引套用至 Azure Private Link。 Azure 安全性基準提供如何在 Azure 上保護雲端解決方案的建議。 內容會依 Azure 安全性基準測試所定義的 **安全性控制** ，以及適用于 Azure Private Link 的相關指引來分組。 已排除不適用 Azure Private Link 的**控制項**。 若要查看 Azure Private Link 如何完全對應至 Azure 安全性基準測試，請參閱 [完整的 Azure 虛擬網路安全性基準對應](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)檔案。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱 [Azure 安全性基準測試：網路安全性](../security/benchmarks/security-control-network-security.md)。*

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導**方針：使用 Azure 活動記錄來監視資源設定，以及偵測與 Private Link 相關的網路資源變更。 

在 Azure 監視器中建立警示，在發生重大資源變更時將會觸發此警示。

- [如何檢視及擷取 Azure 活動記錄事件](/azure/azure-monitor/platform/activity-log-view)

- [如何在 Azure 監視器中建立警示](../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱 [Azure 安全性基準測試：記錄和監視](../security/benchmarks/security-control-logging-monitoring.md)。*

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導**方針：透過 Azure 監視器內嵌記錄，以匯總網路資源所產生的安全性資料，例如 Private Link 端點、虛擬網路和網路安全性群組。 

在 Azure 監視器中，使用 Log Analytics 工作區來查詢和執行分析，並將 Azure 儲存體帳戶用於長期/封存儲存體。

此外，也可以根據組織的商務需求，啟用和使用中的資料來 Azure Sentinel 或協力廠商 SIEM。

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

- [如何使用 Azure 監視器收集平臺記錄和計量](../azure-monitor/platform/diagnostic-settings.md)

- [Private Link 的記錄和監視](private-link-overview.md#logging-and-monitoring)

- [適用於網路安全性群組的診斷記錄](../virtual-network/virtual-network-nsg-manage-log.md)

- [如何開始使用 Azure 監視器和第三方 SIEM 整合](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指導**方針：啟用 Azure 監視器活動記錄檔，這是在 Private Link 資源上所採取的記錄作業，例如啟動作業的人員、作業發生的時間、作業的狀態，以及其他有用的審核資訊。 

- [如何使用 Azure 監視器收集平臺記錄和計量](../azure-monitor/platform/diagnostic-settings.md)

- [查看和取出 Azure 活動記錄事件](/azure/azure-monitor/platform/activity-log-view)

- [Private Link 的記錄和監視](private-link-overview.md#logging-and-monitoring)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指導**方針：對於與 Private Link 相關的記錄，請根據組織在 Azure 監視器內的合規性法規來設定 Log Analytics 工作區保留期限。 針對記錄的任何長期/封存儲存體使用 Azure 儲存體帳戶。

- [變更 Log Analytics 中的資料保留期限](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [如何設定 Azure 儲存體客戶紀錄的保留原則](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和審核記錄

**指導**方針：分析和監視記錄中的異常行為，並定期查看結果。 使用 Azure 監視器的 Log Analytics 工作區來檢查記錄，並對記錄資料執行查詢。

另一個選項是啟用和內部資料來 Azure Sentinel 或協力廠商 SIEM。

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

- [了解 Log Analytics 工作區](../azure-monitor/log-query/get-started-portal.md)

- [如何在 Azure 監視器中執行自訂查詢](../azure-monitor/log-query/get-started-queries.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：啟用異常活動的警示

**指導**方針：使用以 Log Analytics 工作區設定的資訊安全中心，以在安全性記錄檔和事件中找到的異常活動進行監視和警示。

根據您的組織商務需求，讓 Azure Sentinel 或協力廠商 SIEM 的資料。

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

- [如何在 Azure 資訊安全中心中管理警示](../security-center/security-center-managing-and-responding-alerts.md)

- [如何對 log analytics 記錄資料發出警示](../azure-monitor/learn/tutorial-response.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱 [Azure 安全性基準測試：身分識別與存取控制](../security/benchmarks/security-control-identity-access-control.md)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的清查

**指導**方針：使用 Azure Active Directory (Azure AD) 內建的系統管理員角色，這些角色可以明確指派和查詢。 執行 Azure AD PowerShell 模組來執行臨機操作查詢，以探索屬於系統管理群組成員的帳戶。

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色](/powershell/module/azuread/get-azureaddirectoryrole)

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指引**：建立使用專用系統管理帳戶的標準作業程序。 使用資訊安全中心的身分識別和存取管理功能來監視系統管理帳戶的數目。

此外，您也可以使用 Azure Active Directory (Azure AD) Privileged Identity Management Microsoft 服務的特殊許可權角色，以及 Azure Resource Manager，來啟用及時/足夠的存取權。

- [深入瞭解 Privileged Identity Management](/azure/active-directory/privileged-identity-management/)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory 單一登入 (SSO) 

**指導**方針：盡可能搭配 Azure Active Directory 使用單一登入，而不是為每個服務設定個別的獨立認證。

- [Azure Active Directory 中的應用程式單一登入](../active-directory/manage-apps/what-is-single-sign-on.md)

- [如何在 Azure 資訊安全中心監視身分識別和存取](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：所有以 Azure Active Directory 為基礎的存取都使用多重要素驗證

**指導**方針：啟用 Azure Active Directory (AZURE AD) MFA (的多重要素驗證，並遵循資訊安全中心的身分識別和存取管理建議。

- [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

- [如何在 Azure 資訊安全中心監視身分識別和存取](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6：使用安全、受 Azure 管理的工作站進行系統管理工作

**指導**方針：使用特殊許可權存取工作站 (PAW) ，並將 Multi-Factor Authentication 設定為登入和設定 Azure 網路資源。

- [瞭解特殊權限存取工作站](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：來自系統管理帳戶的可疑活動記錄和警示

**指導**方針：利用 Azure Active Directory (Azure AD) 風險偵測功能來查看警示和報告有風險的使用者行為。 使用動作群組將安全中心的風險偵測警示內嵌至 Azure 監視器並設定自訂警示/通知。

- [瞭解 Azure 資訊安全中心風險偵測 (可疑活動) ](/azure/active-directory/reports-monitoring/concept-risk-events)

- [如何將 Azure 活動記錄整合到 Azure 監視器中](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [如何設定自訂警示和通知的動作群組](../azure-monitor/platform/action-groups.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指導**方針：使用名為「位置」的條件式存取，只允許從 IP 位址範圍或國家/地區的特定邏輯群組進行存取。

- [如何在 Azure 中設定命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導**方針：使用 Azure Active Directory (Azure AD) 作為中央驗證和授權系統。 Azure AD 使用強式加密來保護待用和傳輸中的資料，以及 salts、雜湊及安全地儲存使用者認證，藉此保護資料。  

- [如何建立和設定 Azure AD 實例](../active-directory-domain-services/tutorial-create-instance.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並調節使用者存取權

**指引**：Azure Active Directory 會提供記錄來協助探索過時的帳戶。 此外，您也可以使用 Azure 身分識別存取審核來有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 您可以定期檢查使用者存取權，以確定只有適當的使用者可以繼續存取。

- [瞭解 Azure AD 報告](/azure/active-directory/reports-monitoring/)

- [如何使用 Azure 身分識別存取權檢閱](../active-directory/governance/access-reviews-overview.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：監視嘗試存取已停用的認證

**指導**方針：使用 Azure Active Directory (Azure AD) 登入活動、Audit 和風險事件記錄檔來源，以與任何 SIEM/監視工具整合。

建立 Azure AD 使用者帳戶的診斷設定，並將審核記錄和登入記錄傳送至 Log Analytics 工作區，以簡化此程式。 在 Log Analytics 工作區中設定所需的警示。

- [如何將 Azure 活動記錄整合到 Azure 監視器中](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：帳戶登入行為偏差的警示

**指導**方針：使用 Azure Active Directory (Azure AD) 風險和身分識別保護功能來設定自動回應，以偵測與網路資源的使用者身分識別相關的可疑動作。 

將資料內嵌到 Azure Sentinel 以進行進一步的調查。

- [如何檢視有風險的 Azure AD 登入](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [如何設定和啟用身分識別保護風險原則](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資料保護](../security/benchmarks/security-control-data-protection.md)。*

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指導**方針：針對個別的安全性網域（例如環境類型和資料敏感度層級），使用不同的訂用帳戶和管理群組來執行隔離。 

根據商務需求，使用您的應用程式和企業環境來限制對 Azure 資源的存取層級。 

透過 Azure Active Directory 角色型存取控制來控制對 Azure 資源的存取。

- [如何建立額外的 Azure 訂閱](/azure/billing/billing-create-subscription)

- [如何建立管理群組](/azure/governance/management-groups/create)

- [如何建立和使用標籤](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導**方針：加密傳輸中的所有機密資訊。 確定任何連線至虛擬網路中 Azure 資源的用戶端都能夠協商 TLS 1.2 或更新版本。 Private Link 不會干擾基礎通訊協定。 使用客戶所使用的其他供應專案的最佳做法。

遵循針對待用加密和傳輸中加密的安全性中心建議（適用時）。

- [瞭解 Azure 中的傳輸加密](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6：使用角色型存取控制來控制資源的存取權

**指導**方針：使用 azure 角色型存取控制 (azure RBAC) 來控制資料和資源的存取權，否則請使用服務特定的存取控制方法。

- [在這裡熟悉 Azure 內建角色的簡短描述和唯一識別碼](../role-based-access-control/built-in-roles.md)

叫用 PowerShell cmd ">get-azroledefinition" 或 "az role definition list" 以取得您環境中的角色清單。

請參閱您的選項，以透過「可見度」設定來控制您的服務暴露情形。 在 Private Link。 這些可見度設定會決定取用者是否可以連接到您的服務。 

將您的服務設為私用，以便從其他虛擬網路取用 (僅) 的 Azure RBAC 許可權。 將曝光限制在一組有限的受信任訂用帳戶，或將其設為公用，讓所有 Azure 訂用帳戶都能要求 Private Link 服務的連線。

- [如何設定 Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Private Link 服務的屬性](private-link-service-overview.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導**方針：使用 Azure 監視器活動記錄警示，以建立在 Azure 資源（例如 Private Link 服務和端點）發生變更時的警示。 

- [適用於網路安全性群組的診斷記錄](private-link-overview.md#logging-and-monitoring)

- [如何建立 Azure 活動記錄事件的警示](../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：清查和資產管理](../security/benchmarks/security-control-inventory-asset-management.md)。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自動化資產探索解決方案

**指導**方針：使用 Azure Resource Graph 來查詢及探索所有網路資源，例如 Private Link 服務和訂用帳戶內的端點。 

確保租用戶中有適當的 (讀取) 權限，且能列舉所有 Azure 訂用帳戶以及訂用帳戶中的資源。

- [如何使用 Azure Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

- [如何檢視您的 Azure 訂用帳戶](/powershell/module/az.accounts/get-azsubscription)

- [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指導**方針：使用中繼資料將標記套用至 Azure 資源，以邏輯方式將它們組織成分類法。

- [如何建立和使用標籤](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指導**方針：使用標記、管理群組和個別的訂用帳戶（如果適當）來組織和追蹤 Private Link 和相關的資源。 

請定期調節清查，並確保會及時刪除訂用帳戶中未經授權的資源。

- [如何建立額外的 Azure 訂閱](/azure/billing/billing-create-subscription)

- [如何建立管理群組](/azure/governance/management-groups/create)

- [如何建立和使用標籤](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定義和維護已核准 Azure 資源的清查

**指導**方針：根據您的組織需求，建立適用于計算資源的已核准 Azure 資源和軟體的清查。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指導**方針：使用 Azure 原則對可使用下列內建原則定義在客戶訂用帳戶中建立的資源類型進行限制：

- 不允許的資源類型

- 允許的資源類型

也可以使用 Azure Resource Graph 來查詢/探索訂用帳戶內的資源。 這有助於以高安全性為基礎的環境，例如具有儲存體帳戶的環境。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

- [私人連結的 Azure 原則範例內建](../governance/policy/samples/built-in-policies.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="67-remove-unapproved-azure-resources"></a>6.7：移除未核准的 Azure 資源

**指導**方針：客戶可能會在客戶的公司原則要求時，防止資源建立或使用 Azure 原則。 您可以執行自己的流程，以移除未經授權的資源。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指導**方針：使用 Azure 原則對可使用下列內建原則定義在客戶訂用帳戶中建立的資源類型進行限制：

- 不允許的資源類型

- 允許的資源類型

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure 原則拒絕特定的資源類型](/azure/governance/policy/samples/not-allowed-resource-types)

- [私人連結的 Azure 原則範例內建](../governance/policy/samples/built-in-policies.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制使用者與 Azure Resource Manager 互動的能力

**指引**：使用 Azure 條件式存取，藉由對「Microsoft Azure 管理」應用程式設定「封鎖存取」，以限制使用者與 Azure Resource Manager 互動的能力。

- [如何設定條件式存取以封鎖 Azure Resource Manager 的存取](../role-based-access-control/conditional-access-azure-management.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱 [Azure 安全性基準測試：安全](../security/benchmarks/security-control-secure-configuration.md)設定。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導**方針：使用 Azure 原則別名來建立自訂原則，以對 Azure 網路資源的設定進行審核或強制執行，以及內建的 Azure 原則定義。

Azure Resource Manager 能夠在 JavaScript 物件標記法 (JSON) 中匯出範本。 您應檢查此成品，以確保這些設定符合或超過組織的安全性需求。

針對您的 Azure 資源，以安全的設定基準來執行來自「安全性中心」的建議。

- [如何檢視可用的 Azure 原則別名](/powershell/module/az.resources/get-azpolicyalias)

- [教學課程：建立和管理原則來強制執行相容性](../governance/policy/tutorials/create-and-manage.md)

- [私人連結的 Azure 原則範例內建](../governance/policy/samples/built-in-policies.md)

- [Azure 入口網站中的單一和多重資源匯出至範本](../azure-resource-manager/templates/export-template-portal.md)

- [安全性建議 - 參考指南](../security-center/recommendations-reference.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指導**方針：使用 Azure Resource Manager 範本和 Azure 原則，安全地設定與 Private Link 和相關資源相關聯的 Azure 資源。  

Azure Resource Manager 範本是 JavaScript 物件標記法 (用來部署 Azure 資源的 JSON) 檔案，則任何自訂範本都需要在程式碼存放庫中安全地儲存和維護。 

使用 Azure 原則 [拒絕] 和 [在不存在時部署]，對您的 Azure 資源強制使用安全設定。

- [建立 Azure Resource Manager 範本的資訊](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [了解 Azure 原則效果](../governance/policy/concepts/effects.md)

- [私人端點的 Azure Resource Manager 範本範例](/azure/templates/microsoft.network/2019-11-01/privateendpoints)

- [私人連結的 Azure 原則範例內建](../governance/policy/samples/built-in-policies.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導**方針：使用 Azure DevOps 安全地儲存和管理您的程式碼，例如自訂 Azure 原則、Azure Resource Manager 範本和 Desired State Configuration 腳本。 

在 Azure Active Directory (Azure AD) 中定義的特定使用者、內建安全性群組或群組的授與或拒絕許可權（如果與 Azure DevOps 整合以進行存取），或與 Active Directory 整合時的 Team Foundation Server。

- [如何在 Azure DevOps 中儲存程式碼](/azure/devops/repos/git/gitworkflow)

- [關於 Azure DevOps 中的許可權和群組](/azure/devops/organizations/security/about-permissions)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署適用于 Azure 資源的設定管理工具

**指導**方針：使用 Azure 原則定義和實行適用于 Azure 資源的標準安全性設定。 

使用 Azure 原則別名來建立自訂原則，以對 Azure 資源的網路設定進行審核或強制執行。 

也可以使用與您的訂用帳戶下管理之任何特定資源相關的內建原則定義。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用別名](../governance/policy/concepts/definition-structure.md#aliases)

- [私人連結的 Azure 原則範例內建](../governance/policy/samples/built-in-policies.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：執行 Azure 資源的自動化設定監視

**指導**方針：使用 Azure 原則來審核 Azure 資源設定。 例如，您可以使用 Azure 原則來偵測未以私人端點設定的資源。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [私人連結的 Azure 原則範例內建](../governance/policy/samples/built-in-policies.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資料](../security/benchmarks/security-control-data-recovery.md)復原。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確定定期自動備份

**指導**方針：使用 Azure Resource Manager 部署 Private Link 的服務、端點和相關資源。 Azure Resource Manager 提供匯出範本的功能，可以用來做為還原 Private Link 端點和相關資源的備份。 

使用 Azure 自動化定期呼叫 Azure Resource Manager 範本匯出 API。

- [Azure Resource Manager 的概觀](../azure-resource-manager/management/overview.md)

- [私人端點的 Azure Resource Manager 範本範例](/azure/templates/microsoft.network/2019-11-01/privateendpoints)

- [Azure 入口網站中的單一和多重資源匯出至範本](../azure-resource-manager/templates/export-template-portal.md)

- [資源群組-匯出範本](/rest/api/resources/resourcegroups/exporttemplate)

- [Azure 自動化簡介](../automation/automation-intro.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份並備份任何客戶管理的金鑰

**指導**方針：使用 Azure Resource Manager 部署 Private Link 的服務、端點和相關資源。 Azure Resource Manager 提供匯出範本的功能，可以用來做為還原 Private Link 端點和相關資源的備份。  

使用 Azure 自動化定期呼叫 Azure Resource Manager 範本匯出 API。  

Azure Key Vault 內備份客戶管理的金鑰。

- [Azure Resource Manager 的概觀](../azure-resource-manager/management/overview.md)

- [私人端點的 Azure Resource Manager 範本範例](/azure/templates/microsoft.network/2019-11-01/privateendpoints)

- [Azure 入口網站中的單一和多重資源匯出至範本](../azure-resource-manager/templates/export-template-portal.md)

- [資源群組-匯出範本](/rest/api/resources/resourcegroups/exporttemplate)

- [Azure 自動化簡介](../automation/automation-intro.md)

- [如何在 Azure 中備份金鑰保存庫金鑰](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指導**方針：依您的商務需求，驗證定期定期執行 Azure Resource Manager 範本部署至隔離式訂用帳戶的能力。 

此外，也請驗證備份客戶管理金鑰的還原。

- [使用 ARM 範本和 Azure 入口網站部署資源](../azure-resource-manager/templates/deploy-portal.md)

- [如何在 Azure 中還原金鑰保存庫金鑰](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰的保護

**指導**方針：使用 Azure DevOps 安全地儲存和管理您的程式碼，例如 Azure Resource Manager 範本。 

如果與 Azure DevOps 整合以存取這些資源，或在與 Active Directory 整合的 Team Foundation Server 中，授與或拒絕特定使用者、內建安全性群組或 Azure Active Directory (Azure AD) 中定義之群組的許可權。

- [如何在 Azure DevOps 中儲存程式碼](/azure/devops/repos/git/gitworkflow)

- [關於 Azure DevOps 中的許可權和群組](/azure/devops/organizations/security/about-permissions)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱 [Azure 安全性基準測試：事件回應](../security/benchmarks/security-control-incident-response.md)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指引**：為組織製作事件回應指南。 

確定已撰寫事件回應計畫，以定義人員的所有角色以及事件處理的階段，或從偵測到事件後審查的管理。

- [建立自有安全性事件回應程序的指引](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 安全性回應中心的事件剖析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [客戶也可以利用 NIST 的電腦安全性性事件處理指南來協助建立自己的事件回應計畫](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序

**指導**方針：資訊安全中心會將嚴重性指派給每個警示，以協助排列應先調查哪些警示的優先順序。 嚴重性是以「資訊安全中心」在「尋找」或用於發出警示的分析，以及導致警示的活動背後有惡意意圖的信賴等級為基礎。

使用標記清楚地標示訂用帳戶 (例如生產、非生產) ，以及建立命名系統來清楚識別和分類 Azure 資源，特別是處理敏感性資料。  

客戶須負責根據發生事件的 Azure 資源和環境的重要性，排定補救警示的優先順序。

- [Azure 資訊安全中心的安全性警示](../security-center/security-center-alerts-overview.md)

- [使用標記來組織 Azure 資源](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指引**：進行練習以定期測試系統的事件回應功能，進而協助保護您的 Azure 資源。 找出弱點和落差，並視需要修訂計畫。

- [NIST 的出版品 - Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引**：如果 Microsoft 安全性回應中心 (MSRC) 發現您的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡資訊來連絡您。 

檢查事件、張貼次數，以確保問題已獲得解決。

- [如何設定 Azure 資訊安全中心的安全性連絡人](../security-center/security-center-provide-security-contact-details.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指導**方針：使用連續匯出功能來匯出資訊安全中心警示和建議，以協助找出 Azure 資源的風險。 「連續匯出」可供以手動或持續不斷的方式來匯出警示和建議。 

此外，您也可以根據您的商務營運需求，使用「安全性中心」資料連線器將警示串流至 Azure Sentinel。

- [如何設定連續匯出](../security-center/continuous-export.md)

- [如何將警示串流至 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指導**方針：使用資訊安全中心的工作流程自動化功能，自動觸發與安全性警示和建議相關 Logic Apps 的回應，以保護您的 Azure 資源。

- [如何設定工作流程自動化和 Logic Apps](../security-center/workflow-automation.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*如需詳細資訊，請參閱 [Azure 安全性基準測試：滲透測試和 red team 練習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：進行 Azure 資源的定期滲透測試，並確保修復所有重要的安全性結果

**指導**方針：遵循 Microsoft 的 Engagement 規則，以確保您的滲透測試不違反 Microsoft 原則。 

針對受 Microsoft 管理的雲端基礎結構、服務和應用程式，使用 Microsoft 的策略和執行的 Red 小組和即時網站滲透測試。

- [滲透測試的參與規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red 小組](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](/azure/security/benchmarks/overview)
- 深入了解 [Azure 資訊安全性基準](/azure/security/benchmarks/security-baselines-overview)

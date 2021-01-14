---
title: 適用于 Azure Resource Manager 的 Azure 安全性基準
description: Azure Resource Manager 安全性基準提供的程式指引和資源，可讓您執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: b4f0c64abf150165e3239e78e8cf9b824b34eed6
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202363"
---
# <a name="azure-security-baseline-for-azure-resource-manager"></a>適用于 Azure Resource Manager 的 Azure 安全性基準

此安全性基準會將 [Azure 安全性基準測試版本 1.0](../../security/benchmarks/overview-v1.md) 的指引套用至 Microsoft Azure Resource Manager。 Azure 安全性基準提供如何在 Azure 上保護雲端解決方案的建議。
內容會依 Azure 安全性基準測試所定義的 **安全性控制** ，以及適用于 Azure Resource Manager 的相關指引來分組。 已排除不適用 Azure Resource Manager 的 **控制項**。

 若要查看 Azure Resource Manager 如何完全對應至 Azure 安全性基準測試，請參閱 [完整的 Azure Resource Manager 安全性基準對應](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)檔案。

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱 [Azure 安全性基準測試：記錄和監視](../../security/benchmarks/security-control-logging-monitoring.md)。*

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導** 方針：透過 Azure 監視器內嵌 Azure 原則活動記錄。 在 Azure 監視器中，請使用 Log Analytics 工作區來查詢和執行分析，並使用 Azure 儲存體帳戶來取得長期或封存儲存體。 或者，您也可啟用 Azure Sentinel 或第三方 SIEM，並讓資料上線。

- [如何使 Azure Sentinel 上線](../../sentinel/quickstart-onboard.md) 

- [如何使用 Azure 監視器收集平臺記錄和計量](../../azure-monitor/platform/diagnostic-settings.md) 

- [如何使用 Azure 監視器收集 Azure 虛擬機器內部主機記錄檔](../../azure-monitor/learn/quick-collect-azurevm.md) 

- [如何開始使用 Azure 監視器和第三方 SIEM 整合](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指導** 方針： Azure Resource Manager 使用自動啟用的活動記錄，以包含事件來源、日期、使用者、時間戳記、來源位址、目的地位址和其他實用元素。

- [如何使用 Azure 監視器收集平臺記錄和計量](../../azure-monitor/platform/diagnostic-settings.md) 

- [瞭解 Azure 中的記錄和不同的記錄類型](../../azure-monitor/platform/platform-logs-overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和檢閱記錄

**指導** 方針：分析和監視記錄中的異常行為，並定期查看結果。 使用 Azure 監視器與 Log Analytics 工作區來檢查記錄，並對記錄資料執行查詢。 

或者，您可以啟用和內部資料來 Azure Sentinel 或協力廠商 SIEM。 

- [如何使 Azure Sentinel 上線](../../sentinel/quickstart-onboard.md) 

- [開始使用 Log Analytics 查詢](../../azure-monitor/log-query/log-analytics-tutorial.md) 

- [如何在 Azure 監視器中執行自訂查詢](../../azure-monitor/log-query/get-started-queries.md)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：啟用異常活動的警示

**指導** 方針：使用 Azure 資訊安全中心與 Log Analytics 進行監視，並針對在活動記錄中找到的異常活動發出警示。 或者，您也可以啟用和麵板上的資料以 Azure Sentinel。 

- [如何使 Azure Sentinel 上線](../../sentinel/quickstart-onboard.md) 

- [如何在 Azure 資訊安全中心中管理警示](../../security-center/security-center-managing-and-responding-alerts.md) 

- [如何對 Log Analytics 記錄資料發出警示](../../azure-monitor/learn/tutorial-response.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱 [Azure 安全性基準測試：身分識別與存取控制](../../security/benchmarks/security-control-identity-access-control.md)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的清查

**指導** 方針： Azure 角色型存取控制 (RBAC) 可讓您透過角色指派來管理 azure 資源的存取權。 您可以將這些角色指派給使用者、群組服務主體和受控識別。 某些資源有預先定義的內建角色，這些角色可透過 Azure CLI、Azure PowerShell 或 Azure 入口網站等工具進行清查或查詢。

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指引**：建立使用專用系統管理帳戶的標準作業程序。 使用 Azure 資訊安全中心的身分識別與存取管理來監視系統管理帳戶的數目。

此外，為了協助您追蹤專屬的系統管理帳戶，您可以使用來自 Azure 資訊安全中心或內建 Azure 原則的建議，例如：

- 應將一個以上的擁有者指派給您的訂用帳戶
- 具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除
- 具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除

您也可以使用 Azure AD Privileged Identity Management 和 Azure Resource Manager 來啟用即時存取。 

- [深入瞭解 Privileged Identity Management](../../active-directory/privileged-identity-management/index.yml)

- [如何使用 Azure 原則](../../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用單一登入 (SSO) 搭配 Azure Active Directory

**指導** 方針：盡可能使用 Azure Active Directory SSO，而不是針對個別服務設定個別的獨立認證。 使用 Azure 資訊安全中心身分識別和存取建議。 

- [瞭解 Azure AD 的 SSO](../../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：針對所有以 Azure Active Directory 為基礎的存取使用多重要素驗證

**指導** 方針：啟用 Azure Active Directory Multi-Factor Authentication，並遵循 Azure 資訊安全中心身分識別和存取管理建議。

- [如何在 Azure 中啟用 MFA](../../active-directory/authentication/howto-mfa-getstarted.md)

- [如何在 Azure 資訊安全中心監視身分識別和存取](../../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦 (特殊權限存取工作站) 進行所有系統管理工作

**指導** 方針：使用安全且受 Azure 管理的工作站 (也稱為「特殊許可權存取工作站」，或 PAW 需要較高許可權的系統管理工作) 。

- [瞭解安全、受 Azure 管理的工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [如何啟用 Azure AD MFA](../../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：來自系統管理帳戶的可疑活動記錄和警示

**指導** 方針：使用 Azure Active Directory 安全性報告和監視，來偵測環境中發生可疑或不安全的活動時。 使用 Azure 資訊安全中心來監視身分識別和存取活動。

- [如何識別已標示為有風險活動的 Azure AD 使用者](../../active-directory/identity-protection/overview-identity-protection.md)

- [如何在 Azure 資訊安全中心監視使用者的身分識別和存取活動](../../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指導** 方針：使用 Azure AD 命名位置，只允許來自 IP 位址範圍或國家/地區之特定邏輯群組的存取。

- [如何設定 Azure AD 命名位置](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導** 方針：使用 Azure AD 命名位置，只允許來自 IP 位址範圍或國家/地區之特定邏輯群組的存取。

- [如何設定 Azure AD 命名位置](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指引**：Azure AD 會提供記錄來協助探索過時的帳戶。 此外，使用 Azure AD 身分識別和存取評論，有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 您可以定期檢查使用者存取權，以確定只有適當的使用者可以繼續存取。 

- [瞭解 Azure AD 報告](../../active-directory/reports-monitoring/index.yml)

- [如何使用 Azure AD 身分識別和存取權檢閱](../../active-directory/governance/access-reviews-overview.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：監視嘗試存取已停用的認證

**指導** 方針：您可以存取 Azure AD 登入活動、audit 和風險事件記錄檔來源，讓您可以與任何 SIEM/監視工具整合。

您可以建立 Azure AD 使用者帳戶的診斷設定，並將審核記錄和登入記錄傳送至 Log Analytics 工作區，以簡化此程式。 您可以在 Log Analytics 工作區中設定所需的警示。

- [如何整合 Azure 活動記錄與 Azure 監視器](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帳戶登入行為偏差警示

**指導** 方針：使用 Azure AD Identity Protection 功能，對偵測到與使用者身分識別相關的可疑動作設定自動回應。 您也可將資料內嵌到 Azure Sentinel 中，以便進一步調查。

- [如何檢視有風險的 Azure AD 登入](../../active-directory/identity-protection/overview-identity-protection.md)

- [如何設定和啟用身分識別保護風險原則](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [如何使 Azure Sentinel 上線](../../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資料保護](../../security/benchmarks/security-control-data-protection.md)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的詳細目錄

**指引**：使用標籤協助追蹤可儲存或處理敏感性資訊的 Azure 資源。

- [如何建立和使用標籤](./tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6：使用 Azure RBAC 來管理資源的存取權

**指導** 方針：使用 Azure AD RBAC 來控制資料和資源的存取權，否則請使用服務特定的存取控制方法。

- [如何在 Azure 中設定 RBAC](../../role-based-access-control/role-assignments-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指導** 方針：如果是伺服器端待用加密，Azure Resource Manager 支援 Microsoft 管理的金鑰。

- [瞭解 Azure Resource Manager 中的資料保護](azure-resource-manager-security-controls.md#data-protection)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導** 方針：使用 Azure 監視器搭配 Azure 活動記錄，以在對重要 Azure 資源進行變更時建立警示。

- [如何建立 Azure 活動記錄事件的警示](../../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：清查和資產管理](../../security/benchmarks/security-control-inventory-asset-management.md)。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自動化資產探索解決方案

**指導** 方針：使用 Azure Resource Graph 來查詢及探索所有資源 (例如計算、儲存體、網路、埠和通訊協定，以及訂用帳戶) 。 確定您的租使用者中有適當的 (讀取) 許可權，並列舉所有 Azure 訂用帳戶以及訂用帳戶中的資源。

雖然可以透過 Azure Resource Graph Explorer 探索傳統的 Azure 資源，但強烈建議您建立和使用 Azure Resource Manager 資源。

- [如何使用 Azure Resource Graph 建立查詢](../../governance/resource-graph/first-query-portal.md)

- [如何查看您的 Azure 訂用帳戶](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [了解 Azure RBAC](../../role-based-access-control/overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指導** 方針：使用原則名稱、描述和類別，根據分類法以邏輯方式組織資產。

- [如需標記資產的詳細資訊，請參閱資源命名和標記決策指南](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指引**：在適當的情況下，使用標籤、管理群組和個別訂閱來組織及追蹤 Azure 資源。 請定期調節清查，並確保會及時刪除訂閱中未經授權的資源。

此外，您也可以使用下列內建原則定義，利用 Azure 原則對可在客戶訂用帳戶中建立的資源類型施加限制：

- 不允許的資源類型
- 允許的資源類型

以下提供更多相關的詳細資料：

- [如何建立額外的 Azure 訂閱](../../cost-management-billing/manage/create-subscription.md)

- [如何建立管理群組](../../governance/management-groups/create-management-group-portal.md)

- [如何建立和使用標籤](./tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定義和維護已核准 Azure 資源的清查

**指導** 方針：根據您的組織需求，建立已核准 Azure 資源的清查，以及適用于計算資源的已核准軟體。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指導** 方針：使用 Azure 原則對可在訂用帳戶中建立的資源類型施加限制。

使用 Azure Resource Graph 則可查詢及探索其訂用帳戶內的資源。  確保已核准環境中的所有 Azure 資源。

- [如何設定和管理 Azure 原則](../../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Resource Graph 建立查詢](../../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指導** 方針：使用 Azure 原則對可使用下列內建原則定義在客戶訂用帳戶中建立的資源類型進行限制：

- 不允許的資源類型
- 允許的資源類型

以下提供更多相關的詳細資料：

- [如何設定和管理 Azure 原則](../../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure 原則拒絕特定的資源類型](../../governance/policy/samples/built-in-policies.md#general)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制使用者與 Azure Resource Manager 互動的能力

**指導** 方針：使用 Azure AD 條件式存取，藉由設定「Microsoft Azure 管理」應用程式的「封鎖存取」，來限制使用者與 Azure 資源管理員互動的能力。

- [如何設定條件式存取以封鎖對 Azure 資源管理員的存取](../../role-based-access-control/conditional-access-azure-management.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12：限制使用者在計算資源中執行腳本的能力

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱 [Azure 安全性基準測試：安全](../../security/benchmarks/security-control-secure-configuration.md)設定。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導** 方針：使用 Azure 原則別名來建立自訂原則，以對 Azure 資源的設定進行審核或強制執行。 您也可以使用內建的 Azure 原則定義。

Azure Resource Manager 能夠在 JavaScript 物件標記法 (的 JSON) 中匯出範本，這應該經過檢查以確保設定符合您組織的安全性需求。

您也可以使用 Azure 資訊安全中心中的建議作為 Azure 資源的安全設定基準。

- [如何查看可用 Azure 原則別名](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [教學課程：建立和管理原則來強制執行相容性](../../governance/policy/tutorials/create-and-manage.md)

- [Azure 入口網站中的單一和多重資源匯出至範本](../templates/export-template-portal.md)

- [安全性建議 - 參考指南](../../security-center/recommendations-reference.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指引**：使用 Azure 原則 [拒絕] 和 [在不存在時部署]，以對 Azure 資源強制使用安全設定。  此外，您可以使用 Azure Resource Manager 範本，維護您的組織所需的 Azure 資源安全性設定。 

此外，以系統管理員身分，您可能需要鎖定訂用帳戶、資源群組或資源，以防止組織中的其他使用者不小心刪除或修改重要資源。 您可以將鎖定層級設定為 CanNotDelete 或 ReadOnly。

- [瞭解 Azure 原則效果](../../governance/policy/concepts/effects.md)

- [建立和管理原則以強制執行合規性](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager 範本總覽](../templates/overview.md)

- [如何鎖定資源以防止非預期的變更](lock-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導** 方針：使用 Azure DevOps 安全地儲存和管理您的程式碼，例如自訂 Azure 原則定義、Azure Resource Manager 範本和 desired state configuration 腳本。 若要存取您在 Azure DevOps 中管理的資源，您可以授與或拒絕特定使用者、內建安全性群組或 Azure Active Directory (Azure AD) （如果與 Azure DevOps 整合）中定義的群組，或與 TFS 整合的 Active Directory。

- [如何在 Azure DevOps 中儲存程式碼](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [關於 Azure DevOps 中的許可權和群組](/azure/devops/organizations/security/about-permissions)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署適用于 Azure 資源的設定管理工具

**指導** 方針：使用 Azure 原則定義和實行適用于 Azure 資源的標準安全性設定。 使用 Azure 原則別名來建立自訂原則，以對 Azure 資源的網路設定進行審核或強制執行。 您也可以利用與特定資源相關的內建原則定義。  此外，您可以使用 Azure 自動化來部署設定變更。

- [如何設定和管理 Azure 原則](../../governance/policy/tutorials/create-and-manage.md)

- [如何使用別名](../../governance/policy/concepts/definition-structure.md#aliases)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：執行 Azure 資源的自動化設定監視

**指導** 方針：使用內建的 Azure 原則定義和自訂原則來警示、審核和強制執行系統設定。 使用 Azure 原則 [audit]、[拒絕] 和 [部署（如果不存在）]，自動強制執行 Azure 資源的設定。

- [如何設定和管理 Azure 原則](../../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指導** 方針：當您在建立 arm 範本時使用建議的做法，這些建議可協助您避免使用 ARM 範本部署解決方案時遇到的常見問題。

執行認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。 

- [實施範本安全性最佳作法](../templates/template-best-practices.md)

- [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資料](../../security/benchmarks/security-control-data-recovery.md)復原。*

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指導** 方針：如有必要，請確保定期定期執行 Azure Resource Manager 範本部署至隔離式訂用帳戶的能力。

- [使用 ARM 範本和 Azure 入口網站部署資源](../templates/deploy-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰的保護

**指導** 方針：使用 Azure DevOps 安全地儲存和管理您的程式碼，例如自訂 Azure 原則定義、Azure Resource Manager 範本和 desired state configuration 腳本。 若要存取您在 Azure DevOps 中管理的資源，您可以授與或拒絕特定使用者、內建安全性群組或 Azure Active Directory (Azure AD) （如果與 Azure DevOps 整合）中定義的群組，或與 TFS 整合的 Active Directory。

- [如何在 Azure DevOps 中儲存程式碼](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [關於 Azure DevOps 中的許可權和群組](/azure/devops/organizations/security/about-permissions)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱 [Azure 安全性基準測試：事件回應](../../security/benchmarks/security-control-incident-response.md)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指導** 方針：為您的組織開發事件回應指南。 請確定有撰寫的事件回應計畫，可定義人員的所有角色，以及事件處理和管理的階段，以進行事件後的評論。 

- [建立自有安全性事件回應程序的指引](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 安全性回應中心的事件剖析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [使用 NIST 的電腦安全性性事件處理指南來協助建立您自己的事件回應計畫](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指導** 方針：進行練習以定期測試系統的事件回應功能，以協助保護您的 Azure 資源。 識別弱式點數和間隙，然後視需要修改您的回應計畫。

- [NIST 的發行--測試、訓練和練習適用于 IT 計畫和功能的指南](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指導** 方針：使用連續匯出功能來匯出您的 Azure 資訊安全中心警示和建議，以協助找出 Azure 資源的風險。 「連續匯出」可讓您以手動方式或持續持續的方式來匯出警示和建議。 您可以使用 Azure 資訊安全中心資料連線器，將警示串流至 Azure Sentinel。

- [如何設定連續匯出](../../security-center/continuous-export.md)

- [如何將警示串流至 Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指導** 方針：使用工作流程自動化功能 Azure 資訊安全中心自動觸發對安全性警示和建議的回應，以保護您的 Azure 資源。

- [如何在安全性中心設定工作流程自動化](../../security-center/workflow-automation.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*如需詳細資訊，請參閱 [Azure 安全性基準測試：滲透測試和 red team 練習](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：進行 Azure 資源的定期滲透測試，並確保修復所有重要的安全性結果

**指導** 方針：遵循 Microsoft Cloud 滲透測試的參與規則，以確保您的滲透測試不違反 Microsoft 原則。 針對 Microsoft 管理的雲端基礎結構、服務和應用程式，使用 Microsoft 對於紅隊和即時網站滲透測試的策略和執行方法。

- [滲透測試運作規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](../../security/benchmarks/overview.md)
- 深入了解 [Azure 資訊安全性基準](../../security/benchmarks/security-baselines-overview.md)
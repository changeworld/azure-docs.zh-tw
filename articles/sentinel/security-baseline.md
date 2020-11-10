---
title: 適用于 Azure Sentinel 的 Azure 安全性基準
description: Azure Sentinel 安全性基準提供的程式指引和資源，可讓您執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: b1fbedf45514455f0e19461e5ee0558bb2914c58
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427712"
---
# <a name="azure-security-baseline-for-azure-sentinel"></a>適用于 Azure Sentinel 的 Azure 安全性基準

此安全性基準會將 [Azure 安全性基準測試版本 1.0](../security/benchmarks/overview.md) 的指引套用至 Azure Sentinel。 Azure 安全性基準提供如何在 Azure 上保護雲端解決方案的建議。 內容會依 Azure 安全性基準測試所定義的 **安全性控制** ，以及適用于 Azure Sentinel 的相關指引來分組。 已排除不適用 Azure Sentinel 的 **控制項** 。 若要查看 Azure Sentinel 如何完全對應至 Azure 安全性基準測試，請參閱 [完整的 Azure Sentinel 安全性基準對應](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)檔案。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱 [Azure 安全性基準測試：網路安全性](../security/benchmarks/security-control-network-security.md)。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保護虛擬網路內的 Azure 資源

**指導** 方針：您無法直接將虛擬網路、子網或網路安全性群組與 Azure Sentinel 產生關聯。 不過，您可以為與 Azure Sentinel 相關聯的 Log Analytics 工作區啟用 Azure 私人端點，以限制對您私人網路的通訊。

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導** 方針：使用 Azure 活動記錄來監視網路資源設定，並偵測與您的 Azure Sentinel 工作區相關的網路資源變更。 在 Azure 監視器中建立警示，在發生重大資源變更時將會觸發此警示。

- [如何在 Azure Sentinel 中啟用審核](resources.md)

- [如何檢視及擷取 Azure 活動記錄事件](/azure/azure-monitor/platform/activity-log-view)

- [如何在 Azure 監視器中建立警示](../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱 [Azure 安全性基準測試：記錄和監視](../security/benchmarks/security-control-logging-monitoring.md)。*

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導** ： Azure Sentinel 的 Audit 記錄會保留在 Azure 活動記錄中。 您可以從 Azure 活動記錄將資料串流至 Azure Sentinel，然後對其執行研究和分析，以查看此資料。

- [如何在 Azure Sentinel 中啟用審核](resources.md)

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指導** ： Azure Sentinel 的 Audit 記錄會保留在 Azure 活動記錄中。 您可以從 Azure 活動記錄將資料串流至 Azure Sentinel，然後對其執行研究和分析，以查看此資料。

- [如何在 Azure Sentinel 中啟用審核](resources.md)

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指導** 方針：在 Azure 監視器中，根據您組織的合規性法規，為與您的 Azure Sentinel 工作區相關聯的 log Analytics 工作區設定記錄保留期限。

- [如何設定記錄保留參數](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和審核記錄

**指導** ： Azure Sentinel 的 Audit 記錄會保留在 Azure 活動記錄中。 您可以從 Azure 活動記錄將資料串流至 Azure Sentinel，然後對其執行搜尋和分析，以查看此資料。 分析和監視來自 Azure Sentinel 活動記錄實例的記錄，以取得異常行為。 使用 Azure Sentinel 工作區中的 [記錄] 區段來執行查詢，或根據您的 Sentinel 記錄來建立警示。

- [如何在 Azure Sentinel 中啟用審核](resources.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：啟用異常活動的警示

**指導** ： Azure Sentinel 的 Audit 記錄會保留在 Azure 活動記錄中。 您可以從 Azure 活動記錄將資料串流至 Azure Sentinel，然後對其執行搜尋和分析，以查看此資料。 分析和監視來自 Azure Sentinel 活動記錄實例的記錄，以取得異常行為。 使用 Azure Sentinel 工作區中的 [記錄] 區段來執行查詢，或根據您的 Sentinel 記錄來建立警示。

- [如何在 Azure Sentinel 中啟用審核](resources.md)

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱 [Azure 安全性基準測試：身分識別與存取控制](../security/benchmarks/security-control-identity-access-control.md)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的清查

**指導** 方針：維護具有控制平面之系統管理存取權限的使用者帳戶清查 (例如 Azure 入口網站 Azure Sentinel 工作區) 。 

您可以使用訂用帳戶 Azure 入口網站中的身分識別和存取控制 (IAM) 窗格，來設定 Azure 角色型存取控制 (Azure RBAC) 。 角色會套用至 Active Directory 中的使用者、群組、服務主體和受控識別。  Azure Sentinel 也會使用 Azure RBAC 來提供內建的系統管理角色（例如 Azure Sentinel 參與者），而這些角色可指派給 Azure 中的使用者、群組和服務。 

- [瞭解自訂角色](../role-based-access-control/custom-roles.md)

- [瞭解 Azure Sentinel 中的 Azure RBAC](roles.md)

- [如何設定活頁簿的 Azure RBAC](quickstart-get-visibility.md)

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指引** ：建立使用專用系統管理帳戶的標準作業程序。 使用 Azure 資訊安全中心的身分識別與存取管理來監視系統管理帳戶的數目。

此外，為了協助您追蹤專用系統管理帳戶，您可使用來自 Azure 資訊安全中心或內建 Azure 原則的建議，例如：

- 應將一個以上的擁有者指派給您的訂用帳戶
- 具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除
- 具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除

- [如何使用 Azure 資訊安全中心來監視身分識別和存取 (預覽)](../security-center/security-center-identity-access.md)

- [如何使用 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory 單一登入 (SSO) 

**指導** 方針：您 Azure Sentinel 工作區的控制平面存取 (例如，Azure 入口網站) 可透過 REST API 取得並支援 SSO。 若要驗證，請將要求的授權標頭設定為從 Azure Active Directory 取得的 JSON Web 權杖。

- [瞭解 Azure Log Analytics REST API](/rest/api/loganalytics/)

- [瞭解 Azure AD 的 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：所有以 Azure Active Directory 為基礎的存取都使用多重要素驗證

**指導** 方針：啟用 Azure Active Directory Multi-Factor Authentication，並遵循 Azure 資訊安全中心身分識別和存取管理建議。

- [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

- [如何在 Azure 資訊安全中心監視身分識別和存取](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6：使用安全、受 Azure 管理的工作站進行系統管理工作

**指導** 方針：使用具特殊許可權的存取工作站 (PAW) 搭配 Azure MULTI-FACTOR AUTHENTICATION (MFA) 已啟用，可登入及設定 Azure Sentinel 相關的資源。 

- [特殊權限存取工作站](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [規劃雲端式 Azure Multi-Factor Authentication 部署](../active-directory/authentication/howto-mfa-getstarted.md) \(部分機器翻譯\)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：來自系統管理帳戶的可疑活動記錄和警示

**指引** ：當環境中發生可疑或不安全的活動時，請使用 Azure Active Directory (AD) 的 Privileged Identity Management (PIM) 來產生記錄和警示。

此外，使用 Azure AD 風險偵測來檢視有風險的使用者行為相關警示和報告。

- [如何部署 Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [了解 Azure AD 風險偵測](/azure/active-directory/reports-monitoring/concept-risk-events)

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指導** 方針：使用名為「位置」的條件式存取，只允許從 IP 位址範圍或國家/地區的特定邏輯群組存取 Azure 入口網站。

- [如何在 Azure 中設定具名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導** 方針：使用 AZURE ACTIVE DIRECTORY (AD) 作為 Azure Sentinel 實例的中央驗證和授權系統。 Azure AD 會對待用資料和傳輸中資料使用增強式加密，以保護資料安全。 Azure AD 也會對使用者認證進行 Salt 處理、雜湊處理並安全儲存資料。

- [如何建立及設定新的 Azure AD 租使用者](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指導** 方針： AZURE ACTIVE DIRECTORY (AD) 提供記錄檔，以協助您探索過時的帳戶。 此外，使用 Azure 身分識別存取審核來有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 您可以定期檢閱使用者的存取權，以確定只有適當的使用者具有持續存取權。 

- [瞭解 Azure AD 報告](/azure/active-directory/reports-monitoring/)

- [如何使用 Azure 身分識別存取權檢閱](../active-directory/governance/access-reviews-overview.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：監視嘗試存取已停用的認證

**指導** 方針：使用 AZURE ACTIVE DIRECTORY (AD) 作為 Azure Sentinel 工作區的中央驗證和授權系統。 Azure AD 會對待用資料和傳輸中資料使用增強式加密，以保護資料安全。 Azure AD 也會對使用者認證進行 Salt 處理、雜湊處理並安全儲存資料。

您可以存取 Azure AD 登入活動、audit 和風險事件記錄檔來源，讓您可以與 Azure Sentinel 或協力廠商 SIEM 整合。

您可以建立 Azure AD 使用者帳戶的診斷設定，並將審核記錄和登入記錄傳送至 Log Analytics 工作區，以簡化此程式。 您可以在 Log Analytics 中設定所需的記錄警示。

- [如何將 Azure 活動記錄整合到 Azure 監視器中](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [如何讓 Azure Sentinel 上線](quickstart-onboard.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：帳戶登入行為偏差的警示

**指導** 方針：對於控制項平面上的帳戶登入行為偏差 (例如 Azure 入口網站) ，請使用 Azure AD Identity Protection 和風險偵測功能，來設定偵測到與使用者身分識別相關的可疑動作的自動回應。 您也可將資料內嵌到 Azure Sentinel 中，以便進一步調查。

- [如何查看 Azure AD 具風險的登入](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [如何設定和啟用身分識別保護風險原則](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [如何使 Azure Sentinel 上線](quickstart-onboard.md)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支援案例期間為 Microsoft 提供相關客戶資料的存取權

**指導** 方針：目前無法使用;Azure Sentinel 或 Log Analytics 工作區尚不支援客戶加密箱。

- [客戶加密箱支援的服務清單](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：目前無法使用

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資料保護](../security/benchmarks/security-control-data-protection.md)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的詳細目錄

**指引** ：使用標籤協助追蹤可儲存或處理敏感性資訊的 Azure 資源。

- [如何建立和使用標籤](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指導** 方針：針對開發、測試和生產 Sentinel 工作區，執行不同的訂用帳戶和/或管理群組。

- [如何建立額外的 Azure 訂閱](/azure/billing/billing-create-subscription)

- [如何建立管理群組](/azure/governance/management-groups/create)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導** 方針：如果您使用 Azure 或內部部署虛擬機器作為 Syslog 轉寄站，您將需要設定 Syslog daemon (rsyslog 或 syslog-ng) 以在 TLS 中進行通訊。

- [使用常見事件格式連接您的外部解決方案](connect-common-event-format.md)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：共用

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

**指導** 方針： Azure 中尚無法使用資料識別、分類和遺失防護功能。 若需要達到合規性目標，請實作協力廠商解決方案。

針對 Microsoft 管理的基礎平台，Microsoft 會將所有客戶內容視為敏感性資訊，並竭盡全力防範客戶資料外洩和暴露。 為了確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制項和功能。

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：共用

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6：使用角色型存取控制來控制資源的存取權

**指導** 方針：您可以使用 Azure 入口網站中的身分識別和存取控制 (IAM) 窗格，來設定 azure RBAC)  (的 azure 角色型存取控制。 角色會套用至 Active Directory 中的使用者、群組、服務主體和受控識別。 您可以針對個人和群組使用內建的 Azure 角色或自訂角色。 

Azure Sentinel 使用 Azure RBAC 來提供內建角色，這些角色可指派給 Azure 中的使用者、群組和服務。 使用 Azure RBAC，您可以在安全性作業小組中使用和建立角色，以授與 Azure Sentinel 的適當存取權。 根據這些角色，您可以更精細地控制可存取 Azure Sentinel 的使用者可以看到的內容。 您可以直接在 Azure Sentinel 工作區中指派 Azure 角色，或指派給工作區所屬的訂用帳戶或資源群組。 有三個特定的內建 Azure Sentinel 角色：

- Azure Sentinel 讀者
- Azure Sentinel 回應程式
- Azure Sentinel 參與者

除了 Azure Sentinel 專屬的 Azure 角色之外，還有 Azure 與 Log Analytics 內建的 Azure 角色，可授與一組更廣泛的許可權，包括存取您的 Azure Sentinel 工作區和其他資源：

Azure 角色包括擁有者、參與者和讀者。 Azure 角色會授與所有 Azure 資源的存取權，包括 Log Analytics 工作區和 Azure Sentinel 資源。

Log Analytics 角色包含 Log Analytics 參與者和 Log Analytics 讀取程式。 Log Analytics 角色會授與所有 Log Analytics 工作區的存取權。

此外，每個 Sentinel 活頁簿都是 Azure 資源，您可以將角色指派給使用者來管理存取權。

- [如何設定 Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [瞭解自訂角色](../role-based-access-control/custom-roles.md)

- [瞭解 Sentinel 與 Log Analytics 中的角色](roles.md)

- [如何設定活頁簿的 Azure RBAC](quickstart-get-visibility.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指導** 方針： Azure Sentinel 和 Azure 監視器 Log Analytics 工作區目前使用 Microsoft 管理的金鑰來加密任何包含的資料。 Sentinel 尚未完全支援攜帶您自己的金鑰的能力，但在不久的未來將會提供。

- [Azure 監視器客戶管理的金鑰總覽](../azure-monitor/platform/customer-managed-keys.md#customer-managed-key-overview)

- [瞭解 Azure Sentinel (Preview) 中客戶管理的金鑰 ](customer-managed-keys.md)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：共用

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導** ： Azure Sentinel 的 Audit 記錄會保留在 Azure 活動記錄中。 您可以從 Azure 活動記錄將資料串流至 Azure Sentinel，然後對其執行研究和分析，以查看此資料。 在 Azure Sentinel 工作區的 [記錄] 區段中，您可能會在生產 Azure Sentinel 工作區以及其他重要或相關資源發生變更時，建立警示。

- [如何在 Azure Sentinel 中啟用審核](resources.md)

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：清查和資產管理](../security/benchmarks/security-control-inventory-asset-management.md)。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自動化資產探索解決方案

**指引** ：使用 Azure Resource Graph 來查詢/探索訂用帳戶內的所有資源 (例如計算、儲存體、網路、連接埠及通訊協定等)。  確保您的租用戶中有適當的 (讀取) 權限，並列舉所有 Azure 訂用帳戶以及訂用帳戶內的資源。

雖然可以透過 Resource Graph 探索傳統的 Azure 資源，但強烈建議您建立並使用 Azure Resource Manager 的資源。

- [如何使用 Azure Resource Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

- [如何檢視您的 Azure 訂用帳戶](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指引** ：將標籤套用至提供中繼資料的 Azure 資源，以邏輯方式依分類組織這些資源。

- [如何建立和使用標籤](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指引** ：在適當的情況下，使用標籤、管理群組和個別訂閱來組織及追蹤 Azure 資源。 請定期調節清查，並確保會及時刪除訂閱中未經授權的資源。

此外，使用下列內建原則定義，利用 Azure 原則對可在客戶訂閱中建立的資源類型施加限制：

- 不允許的資源類型
- 允許的資源類型

- [如何建立額外的 Azure 訂閱](/azure/billing/billing-create-subscription)

- [如何建立管理群組](/azure/governance/management-groups/create)

- [如何建立和使用標籤](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指導** 方針：使用 Azure 原則對可在訂用帳戶中建立的資源類型（ (s) ）施加限制。 

使用 Azure Resource Graph 來查詢/探索其訂閱內的資源。  確保已核准環境中的所有 Azure 資源。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指引** ：使用下列內建原則定義，以利用 Azure 原則對可在客戶訂用帳戶中建立的資源類型施加限制：

- 不允許的資源類型
- 允許的資源類型

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure 原則拒絕特定的資源類型](/azure/governance/policy/samples/not-allowed-resource-types)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制使用者與 Azure Resource Manager 互動的能力

**指引** ：設定 Azure 條件式存取，以藉由對「Microsoft Azure 管理」應用程式設定「封鎖存取」，限制使用者與 Azure Resource Manager 互動的能力。

- [如何設定條件式存取以封鎖 Azure Resource Manager 的存取](../role-based-access-control/conditional-access-azure-management.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱 [Azure 安全性基準測試：安全](../security/benchmarks/security-control-secure-configuration.md)設定。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導** 方針：使用 Azure 原則定義和執行與您的 Sentinel 工作區相關聯之 Log Analytics 工作區的標準安全性設定。 使用 "OperationalInsights" 命名空間中 Azure 原則別名來建立自訂原則，以對 Log Analytics 工作區的設定進行審核或強制執行。

- [如何檢視可用的 Azure 原則別名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指導** 方針：使用 azure 原則 [拒絕] 和 [部署是否不存在]，在您的 Azure 資源上強制執行安全設定。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [瞭解 Azure 原則效果](../governance/policy/concepts/effects.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指引** ：如果使用自訂 Azure 原則定義，請使用 Azure DevOps 或 Azure Repos 安全地儲存及管理您的程式碼。

- [如何在 Azure DevOps 中儲存程式碼](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure Repos 文件](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署適用于 Azure 資源的設定管理工具

**指導** 方針：使用內建的 Azure 原則定義以及 "OperationalInsights" 命名空間中的 Azure 原則別名來建立自訂原則，以警示、審核和強制執行系統組態。 此外，開發流程和管線以管理原則例外狀況。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：執行 Azure 資源的自動化設定監視

**指導** 方針：使用內建的 Azure 原則定義以及 "OperationalInsights" 命名空間中的 Azure 原則別名來建立自訂原則，以警示、審核和強制執行系統組態。 使用 Azure 原則 [audit]、[拒絕] 和 [部署（如果不存在）]，自動強制執行 Azure 資源的設定。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 秘密

**指導** 方針： Azure Sentinel 支援從使用各種連接器的許多來源收集記錄。 其中有些連接器需要使用 Log Analytics 工作區金鑰進行安裝。 設定這些連接器時，請使用 Azure Key Vault 儲存您的金鑰以簡化秘密管理，並避免發生意外的認證洩漏。

- [將 Sentinel 連接至資料來源](connect-data-sources.md)

- [如何建立 Key Vault](/azure/key-vault/quick-create-portal) 

- [如何使用受控識別來提供 Key Vault authentication](/azure/key-vault/managed-identity)

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指引** ：實作認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。

- [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*如需詳細資訊，請參閱 [Azure 安全性基準測試：惡意程式碼防護](../security/benchmarks/security-control-malware-defense.md)。*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指導** 方針：在支援 Azure 服務的基礎主機上啟用 Microsoft Antimalware (例如 Azure Sentinel 和 Log Analytics) ，不過它不會在客戶內容上執行。 

您必須負責預先掃描任何上傳至非計算 Azure 資源的內容，包括 Log Analytics 工作區。 Microsoft 無法存取客戶資料，因此無法代表您執行客戶內容的反惡意程式碼掃描。

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資料](../security/benchmarks/security-control-data-recovery.md)復原。*

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰的保護

**指導** 方針：啟用 Key Vault 中的 Soft-Delete，以防止遭到意外或惡意刪除的金鑰。 如果使用者撤銷金鑰加密金鑰 (不管是藉由刪除或移除 Azure Sentinel 的存取權)，在一小時內，Azure Sentinel 會接受此變更，而且其行為會如同資料已無法再使用一樣。 此時，將會防止任何使用持續性儲存體資源的作業執行，例如資料擷取、持續性設定變更、事件建立。 先前儲存的資料不會被刪除，但仍將無法存取。 無法存取的資料是由資料保留原則所控管，並會根據該原則加以清除。

撤銷或刪除加密金鑰之後，唯一可能的作業就是刪除帳戶。

如果在撤銷之後還原存取，Azure Sentinel 會在一小時內還原資料的存取權。

- [如何金鑰保存庫中啟用虛刪除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

- [瞭解 Azure Sentinel 中客戶管理的金鑰](customer-managed-keys.md)

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱 [Azure 安全性基準測試：事件回應](../security/benchmarks/security-control-incident-response.md)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指引** ：為組織製作事件回應指南。 請確定有書面的事件回應計畫，其中定義人員的所有角色，以及從偵測到事件後檢討的事件處理/管理階段。

- [如何設定 Azure 資訊安全中心內的工作流程自動化](../security-center/security-center-planning-and-operations-guide.md)

- [建立自有安全性事件回應程序的指引](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 安全性回應中心的事件剖析](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [客戶也可以利用 NIST 的電腦安全性性事件處理指南來協助建立自己的事件回應計畫](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序

**指引** ：資訊安全中心會指派每個警示的嚴重性，以協助設定應優先調查哪些警示。 嚴重性會依據資訊安全中心對用於發出警示的發現或分析其信心程度，以及信賴等級具有活動背後會導致警示的惡意意圖。

此外，清楚地標示訂用帳戶 (例如 生產、非生產) 並建立命名系統，以清楚識別和分類 Azure 資源。

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指引** ：進行練習以定期測試系統的事件回應功能。 找出弱點和落差，並視需要修訂計畫。

- [請參閱 NIST 的發行集：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引** ：如果 Microsoft 安全性回應中心 (MSRC) 發現客戶的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡人資訊來連絡您。  事後檢討事件，確保問題已解決。

- [如何設定 Azure 資訊安全中心的安全性連絡人](../security-center/security-center-provide-security-contact-details.md)

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指引** ：使用「連續匯出」功能來匯出 Azure 資訊安全中心的警示和建議。 「連續匯出」可供以手動或持續不斷的方式來匯出警示和建議。 您可使用 Azure 資訊安全中心的資料連接器，將警示串流至 Sentinel。

- [如何設定連續匯出](../security-center/continuous-export.md)

- [如何將警示串流至 Azure Sentinel](connect-azure-security-center.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指引** ：利用 Azure 資訊安全中心的工作流程自動化功能，透過「Logic Apps」自動觸發對安全性警示和建議的回應。

- [如何設定工作流程自動化和 Logic Apps](../security-center/workflow-automation.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*如需詳細資訊，請參閱 [Azure 安全性基準測試：滲透測試和 red team 練習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：進行 Azure 資源的定期滲透測試，並確保修復所有重要的安全性結果

**指導** 方針：遵循 Microsoft Cloud 滲透測試的參與規則，以確保您的滲透測試不違反 Microsoft 原則。 針對受 Microsoft 管理的雲端基礎結構、服務和應用程式，使用 Microsoft 的策略和執行的 Red 小組和即時網站滲透測試。 

- [滲透測試運作規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red 小組](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視** ：不適用

**責任** ：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](/azure/security/benchmarks/overview)
- 深入了解 [Azure 資訊安全性基準](/azure/security/benchmarks/security-baselines-overview)

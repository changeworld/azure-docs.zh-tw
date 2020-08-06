---
title: 適用于 Azure DDoS 保護 Standard 的 Azure 安全性基準
description: Azure DDoS 保護標準安全性基準提供程式指引和資源，可用於執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: daff4d1e43f5cc27e52e8bf26f48f30147800ae8
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87839862"
---
# <a name="azure-security-baseline-for-azure-ddos-protection-standard"></a>適用于 Azure DDoS 保護 Standard 的 Azure 安全性基準

此安全性基準會將[Azure 安全性基準測試](../benchmarks/overview.md)的指引套用至 Azure DDoS 保護 standard。 Azure 安全性基準提供如何在 Azure 上保護雲端解決方案的建議。 內容是依 Azure 安全性基準測試所定義的**安全性控制**，以及適用于 DDoS 保護的相關指引來分組。 不適用 DDoS 保護的**控制項**已排除。 若要查看 DDoS 保護如何完全對應到 Azure 安全性基準測試，請參閱[完整的 DDoS 保護安全性基準對應](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)檔案。

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱[安全性控制：記錄和監視](/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指引**：啟用 Azure 活動記錄診斷設定，並將記錄傳送到 Log Analytics 工作區、Azure 事件中樞或 Azure 儲存體帳戶進行封存。 活動記錄可讓您深入瞭解在控制平面層級的 Azure DDoS 保護計畫上執行的作業。 使用 Azure 活動記錄資料，您可以判斷任何寫入作業的「內容、物件和時間」， (PUT、POST、DELETE) 在 Azure DDoS 保護實例的控制平面層級執行。

- [如何啟用 Azure 活動記錄的診斷設定](/azure/azure-monitor/platform/diagnostic-settings-legacy)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指導**方針：您可以選取任何可用的 DDoS 保護計量，以在攻擊期間使用 Azure 監視器警示設定時，向您發出警示。 當條件符合時，指定的地址會收到警示電子郵件。

啟用 Azure 活動記錄診斷設定，並將記錄傳送到 Log Analytics 工作區、Azure 事件中樞或 Azure 儲存體帳戶進行封存。 活動記錄可讓您深入瞭解在控制平面層級的 Azure Cache for Redis 實例上執行的作業。 使用 Azure 活動記錄資料，您可以判斷任何寫入作業的「內容、物件和時間」， (PUT、POST、DELETE) 在 Azure DDoS 保護實例的控制平面層級執行。

- [如何設定 DDoS 保護計量的警示](../../virtual-network/manage-ddos-protection.md#configure-alerts-for-ddos-protection-metrics)

- [如何啟用 Azure 活動記錄的診斷設定](/azure/azure-monitor/platform/diagnostic-settings-legacy)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指引**：在 Azure 監視器中，根據貴組織的合規性規定，設定與您的 Azure DDoS 保護計畫相關聯之 log Analytics 工作區的記錄保留週期。

- [如何設定記錄檔保留參數](../../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和審核記錄

**指引**：啟用 Azure 活動記錄診斷設定，並將記錄傳送到 Log Analytics 工作區。 在 Log Analytics 中執行查詢，以搜尋詞彙、識別趨勢、分析模式，並根據復原服務保存庫可能收集到的活動記錄資料，提供許多其他深入解析。

- [如何存取適用于 DDoS 保護標準服務的遙測、記錄和攻擊分析的相關資訊](../../virtual-network/manage-ddos-protection.md#configure-alerts-for-ddos-protection-metrics)

- [如何啟用 Azure 活動記錄的診斷設定](/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [如何在 Azure 監視器中收集和分析 Log Analytics 工作區中的 Azure 活動記錄](/azure/azure-monitor/platform/activity-log-collect)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：啟用異常活動的警示

**指導**方針：設定警示和攻擊分析。 Azure DDoS 保護識別並減少 DDoS 攻擊，而不需要使用者介入。

將 Log Analytics 工作區上架到 Azure Sentinel，因為它提供安全性協調流程自動化回應 (攀升情況) 解決方案。 如此可建立劇本 (自動化解決方案)，並用於修復安全性問題。 此外，您可以使用 Azure 監視器，在 Log Analytics 工作區中建立自訂記錄警示。

- [如何設定 DDoS 計量的警示](https://azure.microsoft.com/blog/holiday-season-is-ddos-season/)

- [如何使 Azure Sentinel 上線](../../sentinel/quickstart-onboard.md)

- [使用 Azure 監視器來建立、檢視及管理記錄警示](../../azure-monitor/platform/alerts-log.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱[安全性控制：身分識別與存取控制](/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的詳細目錄

**指導**方針：若要使用 DDoS 保護計劃，您的帳戶必須指派為網路參與者角色，或為已指派適當動作的自訂角色。

此外，Azure Active Directory (AD) 具有必須明確指派且可供查詢的內建角色。 使用 Azure AD PowerShell 模組來執行臨機操作查詢，以探索屬於系統管理群組成員的帳戶。

- [瞭解 Azure DDoS 保護中的許可權](../../virtual-network/manage-ddos-protection.md#permissions)

- [如何使用 PowerShell 取得 Azure AD 中的目錄角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在適用的情況下變更預設密碼

**指引**：Azure AD 沒有預設密碼的概念。 需要密碼的其他 Azure 資源會強制以複雜性需求和最小密碼長度來建立密碼，這會因服務而有所不同。 您必須負責可使用預設密碼的協力廠商應用程式和 marketplace 服務。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指引**：建立使用專用系統管理帳戶的標準作業程序。 使用 Azure 資訊安全中心的身分識別與存取管理來監視系統管理帳戶的數目。

此外，為了協助您追蹤專用的系統管理帳戶，您可以使用來自 Azure 資訊安全中心或內建 Azure 原則的建議，例如：

- 應將一個以上的擁有者指派給您的訂用帳戶

- 具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除

- 具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除

- [如何使用 Azure 資訊安全中心來監視身分識別和存取 (預覽)](../../security-center/security-center-identity-access.md)

- [如何使用 Azure 原則](../../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory 單一登入 (SSO) 

**指引**：使用 Azure 應用程式註冊 (服務主體) 來抓取權杖，以透過 API 呼叫來與您的 DDoS 保護計劃進行互動。

- [如何呼叫 Azure REST Api](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [如何向 Azure AD 註冊用戶端應用程式 (服務主體) ](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Azure DDos 保護 API 資訊](/rest/api/virtual-network/)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：所有以 Azure Active Directory 為基礎的存取都使用多重要素驗證

**指引**：啟用 Azure Active Directory 多重要素驗證，並遵循 Azure 資訊安全中心身分識別和存取管理建議。

- [如何在 Azure 中啟用 MFA](../../active-directory/authentication/howto-mfa-getstarted.md)

- [如何在 Azure 資訊安全中心監視身分識別和存取](../../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦 (特殊權限存取工作站) 進行所有系統管理工作 

**指導**方針：使用安全、受 azure 管理的工作站搭配 Azure 多重要素驗證 (MFA) 啟用以登入及設定您的 Azure 客戶加密箱要求。

- [部署安全且受 Azure 管理的工作站](../../active-directory/devices/howto-azure-managed-workstation.md)

- [規劃雲端式 Azure Multi-Factor Authentication 部署](../../active-directory/authentication/howto-mfa-getstarted.md) \(部分機器翻譯\)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：從系統管理客戶紀錄及警示可疑活動

**指引**：在環境中發生可疑或不安全的活動時，使用 Azure Active Directory (Azure AD) PRIVILEGED IDENTITY MANAGEMENT (PIM) 產生記錄檔和警示。

此外，您可以使用 Azure AD 風險偵測來查看警示和報告有風險的使用者行為。

- [如何部署 Privileged Identity Management](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [瞭解 Azure AD 風險偵測](/azure/active-directory/reports-monitoring/concept-risk-events)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指導**方針：使用 Azure AD 命名位置，以允許只從 IP 位址範圍或國家/地區的特定邏輯群組存取 Azure 入口網站。

- [如何設定 Azure AD 命名位置](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指引**：在適用的情況下，使用 Azure Active Directory (Azure AD) 作為中央驗證和授權系統。 Azure AD 會對待用資料和傳輸中資料使用增強式加密，以保護資料安全。 Azure AD 也會對使用者認證進行 Salt 處理、雜湊處理並安全儲存資料。

- [如何建立和設定 Azure AD 實例](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指導**方針： Azure Active Directory (Azure AD) 提供記錄，協助您探索過時的帳戶。 此外，您還可以使用 Azure AD 存取審查，有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 您應該定期檢查使用者存取權，以確保只有適當的使用者可以繼續進行存取。

- [瞭解 Azure AD 報告](/azure/active-directory/reports-monitoring/)

- [如何使用 Azure AD 存取評論](../../active-directory/governance/access-reviews-overview.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：監視嘗試存取已停用的認證

**指引**：在適用的情況下，使用 Azure Active Directory (Azure AD) 作為中央驗證和授權系統。 Azure AD 會對待用資料和傳輸中資料使用增強式加密，以保護資料安全。 Azure AD 也會對使用者認證進行 Salt 處理、雜湊處理並安全儲存資料。

您可以存取 Azure AD 登入活動、audit 和風險事件記錄檔來源，讓您能夠與 Azure Sentinel 或協力廠商 SIEM 整合。

若要簡化此程式，您可以建立 Azure AD 使用者帳戶的診斷設定，並將 audit 記錄和登入記錄傳送到 Log Analytics 工作區。 您可以在 Log Analytics 中設定所需的記錄警示。

- [如何將 Azure 活動記錄整合到 Azure 監視器中](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [如何讓 Azure Sentinel 上線](../../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：帳戶登入行為偏差的警示

**指導**方針：針對控制平面上的帳戶登入行為偏差 (例如 Azure 入口網站) ，請使用 Azure AD Identity Protection 和風險偵測功能，設定對偵測到與使用者身分識別相關之可疑動作的自動回應。 您也可將資料內嵌到 Azure Sentinel 中，以便進一步調查。

- [如何觀看 Azure AD 有風險的登入](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [如何設定和啟用身分識別保護風險原則](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [如何使 Azure Sentinel 上線](../../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱[安全性控制：資料保護](/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的詳細目錄

**指引**：使用標籤協助追蹤可儲存或處理敏感性資訊的 Azure 資源。

- [如何建立和使用標籤](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6：使用以角色為基礎的存取控制來控制對資源的存取

**指導**方針：若要使用 Azure DDoS 保護方案，您的帳戶必須指派為網路參與者角色或已指派特定動作的自訂角色。

- [在 Azure DDoS 保護中管理角色型存取控制](../../virtual-network/manage-ddos-protection.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導**方針：使用 Azure 監視器搭配 Azure 活動記錄，以針對 Azure DDoS 保護計畫以及其他重要或相關資源進行變更時，建立警示。

- [如何建立 Azure 活動記錄事件的警示](../../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱[安全性控制：清查和資產管理](/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自動化資產探索解決方案

**指導**方針：使用 Azure Resource Graph 來查詢/探索所有資源 (例如計算、儲存體、網路、埠和通訊協定等，並在您的訂用帳戶內 ) 。  確保您的租用戶中有適當的 (讀取) 權限，並列舉所有 Azure 訂用帳戶以及訂用帳戶內的資源。

雖然可透過 Resource Graph 探索傳統的 Azure 資源，但強烈建議您建立並使用 Azure Resource Manager 的資源。

- [如何使用 Azure Resource Graph 建立查詢](../../governance/resource-graph/first-query-portal.md)

- [如何查看您的 Azure 訂用帳戶](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指引**：將標籤套用至 Azure 資源，讓中繼資料根據分類法以邏輯方式組織它們。

- [如何建立和使用標籤](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指引**：在適當的情況下，使用標籤、管理群組和個別訂閱來組織及追蹤 Azure 資源。 請定期調節清查，並確保會及時刪除訂閱中未經授權的資源。

此外，使用 Azure 原則來對可使用下列內建原則定義在客戶訂用帳戶中建立的資源類型施加限制：

- 不允許的資源類型

- 允許的資源類型

- [如何建立額外的 Azure 訂閱](/azure/billing/billing-create-subscription)

- [如何建立管理群組](../../governance/management-groups/create.md)

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

- [如何設定和管理 Azure 原則](../../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Resource Graph 建立查詢](../../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指導**方針：使用 Azure 原則，對可使用下列內建原則定義在客戶訂用帳戶中建立的資源類型施加限制：

- 不允許的資源類型

- 允許的資源類型

- [如何設定和管理 Azure 原則](../../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure 原則拒絕特定的資源類型](/azure/governance/policy/samples/not-allowed-resource-types)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制使用者與 Azure Resource Manager 互動的能力

**指引**：設定 Azure 條件式存取，以藉由對「Microsoft Azure 管理」應用程式設定「封鎖存取」，限制使用者與 Azure Resource Manager 互動的能力。

- [如何設定條件式存取以封鎖 Azure Resource Manager 的存取](../../role-based-access-control/conditional-access-azure-management.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱[安全性控制：安全設定](/azure/security/benchmarks/security-control-secure-configuration)。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指引**：使用 Azure 原則定義和執行 Azure DDos 保護的標準安全性設定。 使用 "Microsoft. Network" 命名空間中 Azure 原則別名來建立自訂原則，以審核或強制執行復原服務保存庫的設定。

- [如何查看可用的 Azure 原則別名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [如何設定和管理 Azure 原則](../../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指引**：使用 Azure 原則 [拒絕] 和 [在不存在時部署]，以對 Azure 資源強制使用安全設定。

- [如何設定和管理 Azure 原則](../../governance/policy/tutorials/create-and-manage.md)

- [瞭解 Azure 原則效果](../../governance/policy/concepts/effects.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導**方針：如果使用自訂 Azure 原則定義，請使用 Azure DevOps 或 Azure Repos 安全地儲存和管理您的程式碼。

- [如何在 Azure DevOps 中儲存程式碼](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure Repos 文件](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署適用于 Azure 資源的設定管理工具

**指引**：使用內建的 Azure 原則定義以及 "Microsoft. Network" 命名空間中 Azure 原則別名來建立自訂原則，以警示、audit 及強制執行系統設定。 此外，開發流程和管線以管理原則例外狀況。

- [如何設定和管理 Azure 原則](../../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：執行 Azure 資源的自動化設定監視

**指引**：使用內建的 Azure 原則定義以及 "Microsoft. Network" 命名空間中 Azure 原則別名來建立自訂原則，以警示、audit 及強制執行系統設定。 使用 Azure 原則 [audit]、[deny] 和 [deploy if not 存在] 自動強制執行 Azure 資源的設定。

- [如何設定和管理 Azure 原則](../../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指引**：實作認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。

- [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*如需詳細資訊，請參閱[安全性控制：惡意程式碼防禦](/azure/security/benchmarks/security-control-malware-defense)。*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指引**：在支援 Azure 服務 (的基礎主機上啟用 Microsoft 反惡意程式碼，例如 Azure DDoS 保護) ，但不會對客戶內容執行。

您必須負責預先掃描任何上傳至非計算 Azure 資源的內容。 Microsoft 無法存取客戶資料，因此無法代表您執行客戶內容的反惡意程式碼掃描。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱[安全性控制：事件回應](/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指引**：為組織製作事件回應指南。 請確定有書面的事件回應計畫，其中定義人員的所有角色，以及從偵測到事件後檢討的事件處理/管理階段。

- [建立自有安全性事件回應程序的指引](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 安全性回應中心 SSIRP 事件的剖析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [利用 NIST 的電腦安全性事件處理指南，協助建立自己的事件回應計畫](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序

**指引**：資訊安全中心會指派每個警示的嚴重性，以協助設定應優先調查哪些警示。 嚴重性會依據資訊安全中心對用於發出警示的發現或分析其信心程度，以及信賴等級具有活動背後會導致警示的惡意意圖。

此外，使用標記清楚地標示訂用帳戶 (例如， 生產、非生產) 並建立命名系統，以清楚地識別及分類 Azure 資源，尤其是處理敏感性資料的資源。  您需負責根據發生事件的 Azure 資源和環境的重要性，設定警示的補救優先順序。

- [Azure 資訊安全中心的安全性警示](../../security-center/security-center-alerts-overview.md)

- [使用標記來組織 Azure 資源](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指導**方針：執行練習以定期測試系統的事件回應功能，以協助保護您的 Azure 資源。 找出弱點和落差，並視需要修訂計畫。

針對您的應用程式產生流量來模擬 DDoS 攻擊，以測試您的服務如何回應攻擊的假設。 請勿等待實際的攻擊發生。 Microsoft 與 Ixia （一家 Keysight 公司）合作，提供自助流量產生器 (BreakingPoint 雲端) ，讓 Azure DDoS 保護客戶能夠模擬其 Azure 公用端點的 DDoS 測試流量。

- [Microsoft Azure DDoS 保護驗證](https://www.ixiacom.com/products/breakingpoint-cloud)

- [NIST 的出版品 - Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引**：如果 Microsoft 安全性回應中心 (MSRC) 發現您的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡資訊來連絡您。 事後檢討事件，確保問題已解決。

- [如何設定 Azure 資訊安全中心安全性連絡人](../../security-center/security-center-provide-security-contact-details.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指引**：使用「連續匯出」功能來匯出 Azure 資訊安全中心警示和建議，協助找出 Azure 資源的風險。 「連續匯出」可讓您以手動或持續不斷的方式來匯出警示和建議。 您可以使用 Azure 資訊安全中心資料連線器，將警示串流至 Azure Sentinel。

選取任何可用的 DDoS 保護計量，以在攻擊期間使用 Azure 監視器警示設定時，向您發出警示。 符合條件時，指定的位址會收到警示電子郵件

- [設定 DDoS 保護計量的警示](../../virtual-network/manage-ddos-protection.md#configure-alerts-for-ddos-protection-metrics)

- [如何設定連續匯出](../../security-center/continuous-export.md)

- [如何將警示串流至 Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指導**方針：使用 Azure 資訊安全中心中的工作流程自動化功能，透過「Logic Apps」來自動觸發安全性警示和建議的回應，以保護您的 Azure 資源。

- [如何設定工作流程自動化和 Logic Apps](../../security-center/workflow-automation.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*如需詳細資訊，請參閱[安全性控制：滲透測試和 Red Team 練習](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期滲透測試您的 Azure 資源，並確保修復所有重要的安全性結果

**指導**方針：遵循 Microsoft 滲透測試規則來確保您的滲透測試不會違反 microsoft 原則。 針對 Microsoft 管理的雲端基礎結構、服務和應用程式，使用 Microsoft 的 Red 小組和即時網站滲透測試策略和執行。

- [Engagement 的滲透測試規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red 小組](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](/azure/security/benchmarks/overview)
- 深入了解 [Azure 資訊安全性基準](/azure/security/benchmarks/security-baselines-overview)

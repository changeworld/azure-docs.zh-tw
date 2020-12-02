---
title: 適用於成本管理的 Azure 安全性基準
description: 成本管理安全性基準提供在實作指定於 Azure 安全性效能評定中的安全性建議時所需的程序指引和資源。
author: msmbaldwin
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 3308ab77f4c81b1908abf7f04157ccac7ab2529e
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353641"
---
# <a name="azure-security-baseline-for-cost-management"></a>適用於成本管理的 Azure 安全性基準

此安全性基準會將 [Azure 安全性效能評定 2.0 版](../security/benchmarks/overview.md)中的指引套用至 Azure 成本管理。 Azure 安全性基準提供如何在 Azure 上保護雲端解決方案的建議。 其內容會依據 Azure 安全性效能評定所定義的 **安全性控制** 以及適用於成本管理的相關指引進行分類。 我們排除了不適用成本管理的 **控制項**。

若要查看成本管理對應至 Azure 安全性效能評定的詳細情形，請參閱[完整的成本管理安全性基準對應檔案](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="identity-management"></a>身分識別管理

如需詳細資訊，請參閱 [Azure 安全性效能評定：身分識別管理](../security/benchmarks/security-controls-v2-identity-management.md)。

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1：將 Azure Active Directory 標準化為中央身分識別和驗證系統

**指引**：Azure 成本管理可與作為 Azure 預設身分識別和存取管理服務的 Azure Active Directory (Azure AD) 整合。 您應對 Azure AD 進行標準化，以在下列位置控管組織的身分識別和存取管理：

- Microsoft 雲端資源，例如 Azure 入口網站、Azure 儲存體、Azure 虛擬機器 (Linux 和 Windows)、Azure Key Vault、PaaS 和 SaaS 應用程式。

- 您的組織資源，例如 Azure 上的應用程式或您的公司網路資源。

在組織的雲端安全性實務中，保護 Azure AD 應具有高優先順序。 Azure AD 會提供身分識別安全分數，以協助您根據 Microsoft 的最佳做法建議評估您的身分識別安全性態勢。 您可以使用分數來衡量您的設定與最佳做法建議的貼近程度，並改善安全性態勢。

注意：Azure AD 支援外部識別提供者，可讓沒有 Microsoft 帳戶的使用者透過外部身分識別登入其應用程式和資源。

- [Azure AD 中的租用](../active-directory/develop/single-and-multi-tenant-apps.md)

- [如何建立和設定 Azure AD 執行個體](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [定義 Azure AD 租用戶](https://azure.microsoft.com/resources/securing-azure-environments-with-azure-active-directory/)  

- [對應用程式使用外部識別提供者](../active-directory/external-identities/identity-providers.md)

- [什麼是 Azure AD 中的身分識別安全分數](../active-directory/fundamentals/identity-secure-score.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3：使用 Azure AD 單一登入 (SSO) 進行應用程式存取

**指引**：Azure 成本管理會使用 Azure Active Directory，提供 Azure 資源、雲端應用程式和內部部署應用程式的身分識別和存取管理。 其中包括企業身分識別 (例如員工) 以及外部身分識別 (例如合作夥伴、廠商和供應商)。 這可讓單一登入 (SSO) 管理及保護內部部署和雲端中的組織資料與資源存取。 請將您的所有使用者、應用程式和裝置連線到 Azure AD，以進行順暢且安全的存取，並提供更高的可見度和控制。

- [了解 Azure AD 的應用程式 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4：對所有以 Azure Active Directory 為基礎的存取使用增強式驗證控制項

**指引**：Azure 成本管理可與 Azure AD 整合，以透過多重要素驗證 (MFA) 和增強式無密碼方法支援增強式驗證控制項。

- 多重要素驗證 - 啟用 Azure AD MFA，並依照 Azure 資訊安全中心身分識別和存取管理建議，在您的 MFA 設定中執行某些最佳做法。 MFA 可以根據登入條件和風險因素，在所有、選取使用者或每個使用者層級上施行。

- 無密碼驗證 – 有三種可用的無密碼驗證選項：Windows Hello 企業版、Microsoft Authenticator 應用程式和內部部署驗證方法 (例如智慧卡)。

對於系統管理員和具特殊權限的使用者，請務必使用最高層級的增強式驗證方法，然後向其他使用者推出適當的增增強式驗證原則。

- [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Azure Active Directory 的無密碼驗證選項簡介](../active-directory/authentication/concept-authentication-passwordless.md) 

- [Azure AD 預設密碼原則](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [使用 Azure AD 密碼保護排除不正確的密碼](../active-directory/authentication/concept-password-ban-bad.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5：帳戶異常的監視和警示

**指引**：Azure 成本管理可與提供下列資料來源的 Azure Active Directory 整合：

- 登入 - 登入報表會提供受控應用程式和使用者登入活動的使用情況相關資訊。

- 稽核記錄 - 可針對各種功能在 Azure AD 內進行的所有變更，提供記錄追蹤功能。 稽核記錄的範例包括對 Azure AD 中任何資源所做的變更，像是新增或移除使用者、應用程式、群組、角色和原則。

- 有風險的登入 - 有風險的登入表示非使用者帳戶合法擁有者的某人嘗試登入。

- 標幟為有風險的使用者 - 有風險的使用者表示可能被盜用的使用者帳戶。

這些資料來源可與 Azure 監視器、Azure Sentinel 或第三方 SIEM 系統整合。

Azure 資訊安全中心也可對特定的可疑活動發出警示，例如驗證嘗試失敗次數過多，訂用帳戶中已淘汰的帳戶。

Azure 進階威脅防護 (ATP) 是一項安全性解決方案，可使用 Active Directory 訊號來識別、偵測及調查進階威脅、遭入侵的身分識別，以及惡意的內部人員動作。

- [Azure Active Directory 中的稽核活動報表](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [如何檢視有風險的 Azure AD 登入](../active-directory/identity-protection/overview-identity-protection.md)

- [如何識別已標示為有風險活動的 Azure AD 使用者](../active-directory/identity-protection/overview-identity-protection.md) 

- [如何在 Azure 資訊安全中心監視使用者的身分識別和存取活動](../security-center/security-center-identity-access.md) 

- [Azure 資訊安全中心的威脅情報保護模組中的警示](../security-center/alerts-reference.md) 

- [如何將 Azure 活動記錄整合到 Azure 監視器中](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="privileged-access"></a>特殊權限存取

如需詳細資訊，請參閱 [Azure 安全性效能評定：特殊權限存取](../security/benchmarks/security-controls-v2-privileged-access.md)。

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1：保護和限制高權限使用者

**指引**：Enterprise 合約 (EA) 的成本管理具有下列高權限帳戶。

- 企業系統管理員

建議您定期檢閱為 Enterprise 合約 (EA) 中的角色指派的使用者。

此外，也建議以規則限制高權限帳戶或角色的數目，並以更高的層級保護這些帳戶，因為具有此權限的使用者可以直接或間接讀取和修改 Azure 環境中的每項資源。

您可以使用 Azure AD Privileged Identity Management (PIM)，啟用對 Azure 資源和 Azure AD 的 Just-In-Time (JIT) 特殊權限存取。 JIT 只有在使用者需要時，才會授與暫時性權限以執行特殊權限的工作。 當您的 Azure AD 組織中有可疑或不安全的活動時，PIM 也會產生安全性警示。

- [管理 Azure 企業角色](manage/understand-ea-roles.md) 

- [Azure AD 中的系統管理員角色權限](../active-directory/roles/permissions-reference.md) 

- [使用 Azure Privileged Identity Management 安全性警示](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md) 

- [在 Azure AD 中保護混合式部署和雲端部署的特殊權限存取](../active-directory/roles/security-planning.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3：定期檢閱和協調使用者存取

**指引**：在 Azure 成本管理中，必須具備適當的存取權才能檢視和管理組織成本資料。 存取權可透過訂用帳戶層級的 Azure 角色型存取控制 (Azure RBAC) 來控制，以及透過 Enterprise 合約 (EA) 或 Microsoft 客戶合約 (MCA) 計費範圍的管理角色來控制。 請定期稽核並檢閱授與的存取權，以確定使用者或群組具有必要的存取權。

- [管理對 Azure 帳單資訊的存取](manage/manage-billing-access.md)

- [指派成本管理資料的存取權](costs/assign-access-acm-data.md)

- [管理 Azure 企業角色](manage/understand-ea-roles.md)

- [了解 Azure 中的 Microsoft 客戶合約管理角色](manage/understand-mca-roles.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7：遵循恰到好處的系統管理 (最低權限原則) 

**指引**：Azure 成本管理可與 Azure 角色型存取控制 (RBAC) 整合，以管理資源 (例如預算、已儲存的報表等等)。 Azure RBAC 可讓您透過角色指派來管理 Azure 資源存取。 您可以將這些角色指派給使用者、群組和服務主體。 某些資源有預先定義的內建角色，這些角色可透過 Azure CLI、Azure PowerShell 或 Azure 入口網站之類的工具進行清查或查詢。 透過 Azure RBAC 指派給資源的權限，應一律限定為角色所需的權限。 這類權限可補強 Azure AD Privileged Identity Management (PIM) 的 Just-In-Time (JIT) 方法，且您應定期加以檢閱。

請使用內建角色來配置權限，且僅在必要時才建立自訂角色。

Azure 成本管理提供內建角色、讀取者和參與者。

- [Azure 成本管理讀取者](../role-based-access-control/built-in-roles.md#cost-management-reader)

- [Azure 成本管理參與者](../role-based-access-control/built-in-roles.md#cost-management-contributor)

什麼是 Azure 角色型存取控制 (Azure RBAC) ../role-based-access-control/overview.md 

- [如何在 Azure 中設定 RBAC](../role-based-access-control/role-assignments-portal.md) 

- [如何使用 Azure AD 身分識別和存取權檢閱](../active-directory/governance/access-reviews-overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="data-protection"></a>資料保護

如需詳細資訊，請參閱 [Azure 安全性效能評定：資料保護](../security/benchmarks/security-controls-v2-data-protection.md)。

### <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1：探索、分類和標記敏感資料

**指引**：建議您探索、分類和標記您的敏感資料，以便設計適當的控制項，確保敏感資訊能夠由組織的技術系統安全地儲存、處理及傳輸。

在 Azure、內部部署、Office 365 和其他位置，請對 Office 文件中的敏感資訊使用 Azure 資訊保護 (及其相關聯的掃描工具)。

您可以利用 Azure SQL 資訊保護來分類和標示儲存在 Azure SQL 資料庫中的資訊。

- [使用 Azure 資訊保護標記敏感資訊](/azure/information-protection/what-is-information-protection) 

- [如何實作 Azure SQL 資料探索](../azure-sql/database/data-discovery-and-classification-overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="dp-2-protect-sensitive-data"></a>DP-2：保護敏感資料

**指引**：建議您使用 Azure 角色型存取控制 (Azure RBAC)、網路型存取控制，以及 Azure 服務中的特定控制項 (例如 SQL 和其他資料庫中的加密) 來限制存取，以保護敏感資料。

為確保存取控制的一致性，所有類型的存取控制都應與您的企業分割策略相符。 企業分割策略也應傳達至敏感性或業務關鍵資料和系統的所在位置。

針對 Microsoft 管理的基礎平台，Microsoft 會將所有客戶內容視為敏感性資訊，並防範客戶資料外洩和暴露。 為確保 Azure 中的客戶資料安全無虞，Microsoft 已實作某些預設的資料保護控制項和功能。

- [指派成本管理資料的存取權](costs/assign-access-acm-data.md)

- [Azure 角色型存取控制 (RBAC)](../role-based-access-control/overview.md) 

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3：監視未經授權的敏感資料傳輸

**指引**：監視是否有未經授權者將資料傳輸至企業可見度和控制範圍外的位置。 這通常牽涉到監視是否有異常活動 (大規模或不尋常的傳輸) 可能意味著未經授權的資料外洩。

Azure 儲存體進階威脅防護 (ATP) 和 Azure SQL ATP，可針對可能表示有未經授權者傳輸敏感資訊的異常資訊傳輸發出警示。

Azure 資訊保護 (AIP) 提供對已分類和標示的資訊進行監視的功能。

為了符合資料外洩防護 (DLP) 的合規性，您可以使用主機型 DLP 解決方案來強制執行偵測和/或預防控制措施，以防止資料外洩。

- [啟用 Azure SQL ATP](../azure-sql/database/threat-detection-overview.md) 

- [啟用 Azure 儲存體 ATP](../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5：將待用的敏感資料加密

**指引**：為了補強存取控制，Azure 成本管理的匯出功能支援對待用資料進行 Azure 儲存體加密，以使用 Microsoft 管理的金鑰加密防止「頻外」的攻擊 (例如存取基礎儲存體)。 這種預設設定有助於確保攻擊者無法輕易讀取或修改資料。

如需詳細資訊，請參閱：

- [待用資料的 Azure 儲存體加密](../storage/common/storage-service-encryption.md)

- [了解 Azure 中的待用加密](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="asset-management"></a>資產管理

如需詳細資訊，請參閱 [Azure 安全性效能評定：資產管理](../security/benchmarks/security-controls-v2-asset-management.md)。

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1：確保安全性小組能夠看到資產的風險

**指引**：確保安全性小組會獲得 Azure 租用戶和訂用帳戶中的「安全性讀取者」權限，而能夠使用 Azure 資訊安全中心來監視安全性風險。 

根據安全性小組的權責架構，監視安全性風險可能是由中央安全性小組或區域小組負責。 然而，安全性見解和風險務必要在組織內部集中彙總。 

「安全性讀取者」權限可以廣泛套用至整個租用戶 (根管理群組)，或將範圍限定於管理群組或特定訂用帳戶。 

注意：若要取得工作負載和服務的可見度，可能需要其他權限。 

- [安全性讀取者角色概觀](../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 管理群組概觀](../governance/management-groups/overview.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4：確保資產生命週期管理的安全性

**指引**：建立或更新安全性原則，以處理資產生命週期管理程序，進行可能具高影響力的修改。 這些修改包括對下列項目的變更：識別提供者和存取、資料敏感度、網路設定和系統管理權限指派。

不再需要 Azure 資源時，請將其移除。

- [刪除 Azure 資源群組和資源](../azure-resource-manager/management/delete-resource-group.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="logging-and-threat-detection"></a>記錄與威脅偵測

如需詳細資訊，請參閱 [Azure 安全性效能評定：記錄與威脅偵測](../security/benchmarks/security-controls-v2-logging-threat-detection.md)。

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2：啟用 Azure 身分識別和存取管理的威脅偵測

**指引**：Azure AD 提供下列使用者記錄，可在 Azure AD 報告中檢視，或與 Azure 監視器、Azure Sentinel 或其他 SIEM/監視工具整合，以用於較複雜的監視和分析使用案例：

- 登入 – 登入報告會提供受控應用程式和使用者登入活動的使用情況相關資訊。

- 稽核記錄 - 可針對各種功能在 Azure AD 內進行的所有變更，提供記錄追蹤功能。 稽核記錄的範例包括對 Azure AD 中任何資源所做的變更，像是新增或移除使用者、應用程式、群組、角色和原則。

- 有風險的登入 - 有風險的登入表示非使用者帳戶合法擁有者的某人嘗試登入。

- 標幟為有風險的使用者 - 有風險的使用者表示可能被盜用的使用者帳戶。

Azure 資訊安全中心也可對特定的可疑活動發出警示，例如驗證嘗試失敗次數過多，訂用帳戶中已淘汰的帳戶。 除了基本的安全性檢查監視以外，Azure 資訊安全中心的威脅防護模組也可以從個別的 Azure 計算資源 (虛擬機器、容器、App Service)、資料資源 (SQL DB 和儲存體) 與 Azure 服務層收集更深入的安全性警示。 這項功能可讓您了解個別資源內的帳戶異常。

此外，也建議您定期檢閱為 Enterprise 合約 (EA) 中的角色指派的使用者。 

- [管理 Azure 企業角色](manage/understand-ea-roles.md)

- [Azure Active Directory 中的稽核活動報表](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [啟用 Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure 資訊安全中心內的威脅防護](../security-center/azure-defender.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="incident-response"></a>事件回應

如需詳細資訊，請參閱 [Azure 安全性效能評定：事件回應](../security/benchmarks/security-controls-v2-incident-response.md)。

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1：準備 – 更新 Azure 的事件回應流程

**指引**：確定您的組織具有回應安全性事件的流程、已更新 Azure 的這些處理序，而且會定期執行以確保就緒。

- [在企業環境中實作安全性](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud) (機器翻譯)

- [事件回應參考指南](/microsoft-365/downloads/IR-Reference-Guide.pdf) (英文)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2：準備 – 設定事件通知

**指引**：在 Azure 資訊安全中心中設定安全性事件連絡人資訊。 當 Microsoft 安全回應中心 (MSRC) 發現您的資料已遭非法或未經授權的合作對象存取時，Microsoft 會使用此連絡人資訊來與您連絡。 您也可以選擇根據事件回應需求，在不同的 Azure 服務中自訂事件警示和通知。 

- [如何設定 Azure 資訊安全中心的安全性連絡人](../security-center/security-center-provide-security-contact-details.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3：偵測和分析 – 根據高品質警示建立事件

**指引**：確定您具有建立高品質警示和測量警示品質的程序。 這可讓您從過去的事件中汲取經驗，並且為分析師設定警示的優先順序，以避免浪費時間處理誤報。 

您可以根據從過去的事件獲取的經驗、已驗證的社群來源，以及可藉由融合和相互關聯不同訊號來源而產生和清除警示的工具，來建立高品質的警示。 

Azure 資訊安全中心可在許多 Azure 資產間提供高品質的警示。 您可以使用 ASC 資料連接器將警示串流至 Azure Sentinel。 Azure Sentinel 可讓您建立進階警示規則，以自動產生事件供調查之用。 

使用匯出功能匯出 Azure 資訊安全中心警示和建議，以利找出 Azure 資源的風險。 以手動或持續不斷的方式匯出警示和建議。

- [如何設定匯出](../security-center/continuous-export.md)

- [如何將警示串流至 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4：偵測和分析 – 調查事件

**指引**：確定分析師在調查潛在的事件時可查詢和使用各種資料來源，以全面觀照發生的狀況。 您應收集各種記錄，以追蹤整個攻擊鏈中潛在攻擊者的活動，而避免產生盲點。  您也應確實獲取深入解析和知識，以供其他分析師和未來的歷程記錄參考使用。  

調查的資料來源包括從範圍內的服務和執行中的系統收集到的集中式記錄來源，但也可包括：

- 網路資料 – 使用網路安全性群組的流量記錄、Azure 網路監看員和 Azure 監視器，擷取網路流量記錄和其他分析資訊。 

- 執行中系統的快照集： 

    - 使用 Azure 虛擬機器的快照集功能，建立執行中系統磁碟的快照集。 

    - 使用作業系統的原生記憶體傾印功能，建立執行中系統記憶體的快照集。

    - 使用 Azure 服務的快照集功能或軟體本身的功能，建立執行中系統的快照集。

Azure Sentinel 可讓您對絕大多數的記錄來源進行廣泛的資料分析，並提供案例管理入口網站來管理事件的完整生命週期。 調查期間的情報資訊可以與事件相關聯，以供追蹤和報告之用。 

- [為 Windows 機器的磁碟建立快照集](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [為 Linux 機器的磁碟建立快照集](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure 支援診斷資訊和記憶體傾印收集](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [使用 Azure Sentinel 調查事件](../sentinel/tutorial-investigate-cases.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5：偵測與分析 – 設定事件的優先順序

**指引**：根據警示嚴重性和資產敏感度，為分析師提供應優先關注哪些事件的內容。 

Azure 資訊安全中心會指派每個警示的嚴重性，以協助您設定應優先調查哪些警示。 嚴重性的依據，在於資訊安全中心對於據以發出警示的發現結果或分析結果有多少信心，以及認定導致警示的活動背後存在惡意意圖的把握程度。

此外，請使用標籤來標示資源，並建立命名系統以識別及分類 Azure 資源，尤其是處理敏感資料的資源。  您需負責根據發生事件的 Azure 資源和環境的重要性，設定警示的補救優先順序。

- [Azure 資訊安全中心的安全性警示](../security-center/security-center-alerts-overview.md)

- [使用標記來組織 Azure 資源](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6：圍堵、根除及復原 – 將事件處理自動化

**指引**：將手動重複的工作自動化，以縮短回應時間並降低分析師的負擔。 手動工作需要較長的時間來執行，進而降低每個事件的速度，以及減少分析師可以處理的事件數目。 手動工作也會使分析師更加疲勞，而增加人為錯誤導致延遲的風險，並降低分析師有效專注處理複雜工作的能力。 請使用 Azure 資訊安全中心和 Azure Sentinel 中的工作流程自動化功能來自動觸發動作，或執行劇本以回應傳入的安全性警示。 劇本會採取動作，例如傳送通知、停用帳戶，以及隔離有問題的網路。 

- [在資訊安全中心設定工作流程自動化](../security-center/workflow-automation.md)

- [在 Azure 資訊安全中心設定自動化威脅回應](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [在 Azure Sentinel 中設定自動化威脅回應](../sentinel/tutorial-respond-threats-playbook.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="posture-and-vulnerability-management"></a>狀況和弱點管理

*如需詳細資料，請參閱 [Azure 安全性效能評定：態勢和弱點管理](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)。*

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7：快速自動補救軟體弱點

**指引**：快速部署軟體更新，以補救作業系統和應用程式中的軟體弱點。

優先使用常見的風險評分計畫 (例如，常見弱點評分系統) 或第三方掃描工具所提供的預設風險評等，並使用有關於哪些應用程式呈現高安全性風險和需要高運作時間的內容，自行設定您的環境。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8：執行一般攻擊模擬

**指引**：如有需要，請對您的 Azure 資源執行滲透測試或紅隊活動，並確保補救所有重要的安全性結果。
請遵循 Microsoft 雲端滲透測試參與規則，以確保您的滲透測試不會違反 Microsoft 原則。 針對 Microsoft 管理的雲端基礎結構、服務和應用程式，使用 Microsoft 對於紅隊和即時網站滲透測試的策略和執行方法。

- [Azure 中的滲透測試](../security/fundamentals/pen-testing.md) (機器翻譯)

- [滲透測試運作規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="governance-and-strategy"></a>控管與策略

如需詳細資訊，請參閱 [Azure 安全性效能評定：治理和策略](../security/benchmarks/security-controls-v2-governance-strategy.md)。

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1：定義資產管理和資料保護策略 

**指引**：務必記載並傳達明確的策略，以持續監視及保護系統和資料。 請優先探索、評估、保護及監視業務關鍵資料和系統。 

此策略應該包含下列項目的已記載指引、原則和標準： 

-   符合商業風險的資料分類標準

-   安全性組織對風險和資產清查的可見度 

-   安全性組織核准 Azure 服務以供使用的程序 

-   資產在其生命週期中的安全性

-   符合組織資料分類的必要存取控制策略

-   Azure 原生和第三方資料保護功能的使用

-   傳輸中和待用使用案例的資料加密需求

-   適當的密碼編譯標準

如需詳細資訊，請參閱下列參考資料：
- [Azure 安全性架構建議 - 儲存體、資料和加密](/azure/architecture/framework/security/storage-data-encryption?amp;bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json) (機器翻譯)

- [Azure 安全性基礎觀念 - Azure 資料安全性、加密和儲存體](../security/fundamentals/encryption-overview.md) (機器翻譯)

- [雲端採用架構 - Azure 資料安全性和加密最佳做法](../security/fundamentals/data-encryption-best-practices.md?amp;bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json) (機器翻譯)

- [Azure 安全性效能評定 - 資產管理](../security/benchmarks/security-controls-v2-asset-management.md)

- [Azure 安全性效能評定 - 資料保護](../security/benchmarks/security-controls-v2-data-protection.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2：定義企業分割策略 

**指引**：使用身分識別、網路、應用程式、訂用帳戶、管理群組和其他控制項的組合，建立企業範圍的策略來分割對產的存取。

審慎權衡安全性區隔的需求，以及需要與彼此通訊並存取資料的啟用每日作業需求。

確保在各種控制項類型上一致地實作分割策略，這些控制項類型包括網路安全性、身分識別和存取模型，以及應用程式權限/存取模型與人力流程控制。

- [Azure 中的分割策略指引 (影片)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) (機器翻譯)

- [Azure 中的分割策略指引 (文件)](/security/compass/governance#enterprise-segmentation-strategy) (機器翻譯)

- [使用企業分割策略來調整網路分割](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy) (機器翻譯)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3：定義安全性狀態管理策略

**指引**：持續測量並降低個別資產及其裝載環境的風險。 優先處理高價值資產和高度公開的攻擊面，例如已發佈的應用程式、網路輸入和輸出點、使用者和系統管理員端點等等。

- [Azure 安全性效能評定 - 狀態和弱點管理](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4：協調組織角色、責任及權責

**指引**：務必為安全性組織中的角色和責任記載並傳達清楚的策略。 優先為安全性決策提供清楚的權責、讓每個人熟知共同責任模型，並讓技術團隊熟知保護雲端的技術。

- [Azure 安全性最佳做法 1 – 人員：讓小組熟知雲端安全性旅程](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey) (機器翻譯)

- [Azure 安全性最佳做法 2 - 人員：讓小組熟知雲端安全性技術](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology) (機器翻譯)

- [Azure 安全性最佳做法 3 - 流程：指派雲端安全性決策的權責](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud) (機器翻譯)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-5-define-network-security-strategy"></a>GS-5：定義網路安全性策略

**指引**：建立 Azure 網路安全性方法，作為組織整體安全性存取控制策略的一部分。  

此策略應該包含下列項目的已記載指引、原則和標準： 

-   集中式網路管理和安全性責任

-   與企業分割策略一致的虛擬網路分割模型

-   不同威脅和攻擊案例的補救策略

-   網際網路邊緣和輸入與輸出策略

-   混合式雲端和內部部署互連能力策略

-   最新的網路安全性成品 (例如網路圖、參考網路架構)

如需詳細資訊，請參閱下列參考資料：
- [Azure 安全性最佳做法 11 - 架構。單一整合的安全性策略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy) (機器翻譯)

- [Azure 安全性效能評定 - 網路安全性](../security/benchmarks/security-controls-v2-network-security.md)

- [Azure 網路安全性概觀](../security/fundamentals/network-overview.md)

- [企業網路架構策略](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture) (機器翻譯)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6：定義身分識別和特殊權限存取策略

**指引**：建立 Azure 身分識別和特殊權限存取方法，作為組織整體安全性存取控制策略的一部分。  

此策略應該包含下列項目的已記載指引、原則和標準： 

-   集中式身分識別和驗證系統，以及其與其他內部和外部身分識別系統的互連能力

-   不同使用案例和條件中的增強式驗證方法

-   高權限使用者的保護

-   異常使用者活動監視和處理  

-   使用者身分識別、存取權檢閱與核對流程

如需詳細資訊，請參閱下列參考資料：

- [Azure 安全性效能評定 - 身分識別管理](../security/benchmarks/security-controls-v2-identity-management.md)

- [Azure 安全性效能評定 - 特殊權限存取](../security/benchmarks/security-controls-v2-privileged-access.md)

- [Azure 安全性最佳做法 11 - 架構。單一整合的安全性策略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy) (機器翻譯)

- [Azure 身分識別管理安全性概觀](../security/fundamentals/identity-management-overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7：定義記錄和威脅回應策略

**指引**：建立記錄和威脅回應策略，以快速偵測並補救威脅，同時符合合規性需求。 優先為分析師提供高品質的警示和順暢的體驗，使其能夠全心處理威脅，而非整合和手動步驟。 

此策略應包含針對下列元素而記載的指引、原則和標準： 

-   安全性作業 (SecOps) 組織的角色和責任 

-   妥善定義且與 NIST 或其他產業架構一致的事件回應流程 

-   記錄擷取和保留，以支援威脅偵測、事件回應及合規性需求

-   使用 SIEM、原生 Azure 功能及其他來源，集中顯示及相互關聯威脅的相關資訊 

-   與客戶、供應商和相關公開合作對象之間的溝通和通知計畫

-   使用 Azure 原生和協力廠商平台進行事件處理，例如記錄和威脅偵測、鑑定，以及攻擊補救和根除

-   處理事件和事件後活動的流程，例如吸取的經驗和證據保留

如需詳細資訊，請參閱下列參考資料：

- [Azure 安全性效能評定 - 記錄與威脅偵測](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure 安全性效能評定 - 事件回應](../security/benchmarks/security-controls-v2-incident-response.md)

- [Azure 安全性最佳做法 4 - 流程。更新雲端的事件回應流程](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud) (機器翻譯)

- [Azure 採用架構、記錄與報告決策指南](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure 企業規模、管理與監視](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring) (機器翻譯)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定 V2 概觀](../security/benchmarks/overview.md)
- 深入了解 [Azure 資訊安全性基準](../security/benchmarks/security-baselines-overview.md)
---
title: 適用于 Azure Lighthouse 的 Azure 安全性基準
description: Azure Lighthouse 安全性基準提供的程式指引和資源，可讓您執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: lighthouse
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: cd43b6c7b11cde17bc9f9923374baf3416e5b370
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791857"
---
# <a name="azure-security-baseline-for-azure-lighthouse"></a>適用于 Azure Lighthouse 的 Azure 安全性基準

此安全性基準會將 [Azure 安全性基準測試版本 2.0](../security/benchmarks/overview.md) 的指引套用至 Azure Lighthouse。 Azure 安全性基準提供如何在 Azure 上保護雲端解決方案的建議。 內容會依 Azure 安全性基準測試所定義的 **安全性控制** ，以及適用于 Azure Lighthouse 的相關指引來分組。 已排除不適用 Azure Lighthouse 的 **控制項**。

若要查看 Azure Lighthouse 如何完全對應至 Azure 安全性基準測試，請參閱 [完整的 Azure Lighthouse 安全性基準對應](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)檔案。

## <a name="identity-management"></a>身分識別管理

如需詳細資訊，請參閱 [Azure 安全性效能評定：身分識別管理](../security/benchmarks/security-controls-v2-identity-management.md)。

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1：將 Azure Active Directory 標準化為中央身分識別與驗證系統

**指導** 方針： Azure Lighthouse 使用 Azure Active Directory (Azure AD) 作為預設身分識別和存取管理服務。 將 Azure AD 的標準化，以管理組織的身分識別與存取管理：
- Microsoft 雲端資源，例如 Azure 入口網站、Azure 儲存體、Azure 虛擬機器 (Linux 和 Windows) 、Azure Key Vault、PaaS 和 SaaS 應用程式。
- 您的組織資源，例如 Azure 上的應用程式或您的公司網路資源。

使用 Azure Lighthouse，管理租使用者中指定的使用者具有 Azure 內建角色，可讓他們存取客戶租使用者中的委派訂用帳戶和/或資源群組。 除非擁有者或任何內建角色具有 DataActions 許可權，否則目前支援所有內建角色。 只有在將角色指派給受控識別時，才支援「使用者存取系統管理員」角色的有限用途。 此外，不支援自訂角色與傳統訂用帳戶管理員角色 \(部分機器翻譯\)。

- [Azure Active Directory 中的租用](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [如何建立及設定 Azure AD 執行個體](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [使用應用程式的外部識別提供者](../active-directory/external-identities/identity-providers.md) (機器翻譯) 

- [Azure Active Directory 中的身分識別安全分數為何](../active-directory/fundamentals/identity-secure-score.md) (機器翻譯)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2：安全且自動地管理應用程式識別碼

**指導** 方針： azure 受控識別可以向支援 Azure AD Authentication 的 azure 服務和資源進行驗證。 透過預先定義的存取授與規則來啟用驗證，以避免在原始程式碼或設定檔中使用硬式編碼的認證。 使用 Azure Lighthouse，在客戶訂用帳戶上具有「使用者存取系統管理員」角色的使用者，可以在該客戶的租使用者中建立受控識別。 雖然 Azure Lighthouse 通常不支援此角色，但可在此特定案例中使用，讓具有此許可權的使用者可以將一或多個特定內建角色指派給受控識別。

針對不支援受控識別的服務，請使用 Azure AD，改為在資源層級建立具有限制許可權的服務主體。 Azure Lighthouse 允許服務主體根據在上線過程中授與的角色，來存取客戶資源。 建議您設定具有憑證認證的服務主體，並切換回用戶端密碼。 在這兩種情況下，Azure Key Vault 可以搭配使用 Azure 受控識別，讓執行時間環境 (例如 Azure function) 可以從金鑰保存庫取得認證。

- [Azure 受控識別](../active-directory/managed-identities-azure-resources/overview.md)

- [Azure 服務主體](/powershell/azure/create-azure-service-principal-azureps)

- [使用 Azure Key Vault 來註冊安全性主體](../key-vault/general/authentication.md#authorize-a-security-principal-to-access-key-vault)

- [建立可將角色指派給客戶租使用者中受控身分識別的使用者](how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4：針對所有以 Azure Active Directory 為基礎的存取，使用增強式驗證控制項

**指導** 方針：需要您管理租使用者中所有使用者的 MULTI-FACTOR AUTHENTICATION (MFA) ，包括可存取委派客戶資源的使用者。 建議您要求您的客戶在其租使用者中也執行 MFA。

- [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5：監視及警示帳戶異常

**指導** 方針： Azure AD 提供下列資料來源： 

-   登入 – 登入報告會提供受控應用程式和使用者登入活動的使用情況相關資訊。

-   Audit logs-透過 Azure AD 中各種功能所進行的所有變更，為記錄提供可追蹤性。 記錄變更審核記錄的範例包括新增或移除使用者、應用程式、群組、角色和原則。

-   有風險的登入 - 有風險的登入表示非使用者帳戶合法擁有者的某人嘗試登入。

-   標幟為有風險的使用者 - 有風險的使用者表示可能被盜用的使用者帳戶。

這些資料來源可以與 Azure 監視器、Azure Sentinel 或協力廠商 SIEM 系統整合。

使用 Azure Lighthouse 的服務提供者可以將 Azure AD 記錄轉送到 Azure Sentinel，以及在租使用者之間查看/設定警示，以監視及警示帳戶異常。

- [Azure AD 中的審核活動報告](../active-directory/reports-monitoring/concept-audit-logs.md)

- [如何檢視有風險的 Azure AD 登入](../active-directory/identity-protection/overview-identity-protection.md)

- [大規模管理 Azure Sentinel 工作區](how-to/manage-sentinel-workspaces.md)

- [將資料從 Azure AD 連線到 Azure Sentinel](../sentinel/connect-azure-active-directory.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6：根據條件限制 Azure 資源存取

**指導** 方針： Azure Lighthouse 不支援委派客戶資源的條件式存取功能。 在管理租使用者中，根據使用者定義的條件使用 Azure AD 條件式存取，以取得更細微的存取控制，例如要求特定 IP 範圍的使用者登入使用 Multi-Factor Authentication (MFA) 。 您也可以在不同的使用案例中，透過 Azure AD 條件式存取原則來使用細微的驗證會話管理。 

您應該對管理租使用者中的所有使用者要求 MFA，包括可存取委派客戶資源的使用者。 建議您要求您的客戶在其租使用者中也執行 MFA。

- [Azure 條件式存取總覽](../active-directory/conditional-access/overview.md)

- [一般條件式存取原則](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [使用條件式存取來設定驗證工作階段管理](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="privileged-access"></a>特殊權限存取

如需詳細資訊，請參閱 [Azure 安全性效能評定：特殊權限存取](../security/benchmarks/security-controls-v2-privileged-access.md)。

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1：保護及限制高權限使用者

**指導** 方針：限制高許可權使用者帳戶的數目，並在提高許可權的層級保護這些帳戶。 不需要全域系統管理員帳戶，就能啟用和使用 Azure Lighthouse。

若要存取租使用者層級的活動記錄資料，必須在根範圍 (/) 將「監視讀者」 Azure 內建角色指派給帳戶。 由於根範圍的「監視讀者」角色是廣泛的存取層級，因此我們建議您將此角色指派給服務主體帳戶，而不是指派給個別使用者或群組。 這項指派必須由具有「全域管理員」角色的使用者執行，並具有更高許可權的存取權。 在進行角色指派之前，應該立即新增此提高許可權的存取權，然後在指派完成時將其移除。

- [Azure AD 中的系統管理員角色權限](../active-directory/roles/permissions-reference.md)

- [指派存取權來監視租使用者層級的活動記錄資料](how-to/monitor-delegation-changes.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2：限制對業務關鍵系統的系統管理存取

**指導** 方針： Azure Lighthouse 使用 azure 角色型存取控制 (azure RBAC) 藉由限制哪些帳戶被授與存取權給他們所在的訂用帳戶和管理群組，來隔離對商務關鍵性系統的存取。

請務必遵循最低許可權的原則，讓使用者只具備執行其特定工作所需的許可權。

確定您也會限制對管理、身分識別和安全性系統的存取權，這些系統具有您商務關鍵存取權的系統管理存取權，例如 Active Directory 網網域控制站 (Dc) 、安全性工具和系統管理工具，以及安裝在商務關鍵性系統上的代理程式。 入侵這些管理和安全性系統的攻擊者，可以立即 weaponize 它們來入侵商務關鍵資產。

所有類型的存取控制都應符合您的企業分割策略，以確保存取控制的一致性。

- [Azure 元件和參考模型](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [管理群組存取](../governance/management-groups/overview.md#management-group-access)

- [Azure 訂用帳戶管理員](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [為 Azure Lighthouse 定義使用者和角色的最佳作法](concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3：定期檢閱及協調使用者存取

**指導** 方針： Azure Lighthouse 使用 Azure Active Directory (Azure AD) 帳戶來管理其資源、定期審核使用者帳戶和存取指派，以確保帳戶及其存取權都有效。 您可以使用 Azure AD 存取評論來查看群組成員資格、企業應用程式的存取權，以及角色指派。 Azure AD 報告可以提供記錄以協助探索過時的帳戶。 您也可以使用 Azure AD Privileged Identity Management 建立存取權審核報表工作流程，以加速審核程式。

客戶可以透過 Azure 入口網站中的 Azure Lighthouse，在管理租使用者中檢查授與使用者的存取層級。 他們可以隨時移除此存取權。

此外，Azure Privileged Identity Management 也可以設定為在建立過多的系統管理員帳戶時發出警示，以及識別已過時或設定不正確的系統管理員帳戶。

注意：某些 Azure 服務支援本機使用者和不是透過 Azure AD 管理的角色。 您將需要分開管理這些使用者。

- [在 Privileged Identity Management (PIM) 中建立 Azure 資源角色的存取權審核 ](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [移除委派的存取權](how-to/remove-delegation.md)

- [如何使用 Azure AD 身分識別和存取權檢閱](../active-directory/governance/access-reviews-overview.md)

- [查看 Azure 入口網站中的 [服務提供者] 頁面](how-to/view-manage-service-providers.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4：在 Azure AD 中設定緊急存取

**指導** 方針： Azure Lighthouse 與 Azure Active Directory 整合以管理其資源。 若要避免不小心遭到 Azure AD 的組織封鎖，請在無法使用一般系統管理帳戶時，設定緊急存取帳戶以進行存取。 緊急存取帳戶通常具有高權限，不應將其指派給特定個人。 緊急存取帳戶僅限用於無法使用一般系統管理帳戶的緊急或「急用」狀況。

您應該確保緊急存取帳戶的認證 (例如密碼、憑證或智慧卡) 受到保護，而且只有在緊急情況下有權使用這些認證的個人才會知道。

- [在 Azure AD 中管理緊急存取帳戶](../active-directory/roles/security-emergency-access.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="pa-5-automate-entitlement-management"></a>PA-5：自動化權利管理 

**指導** 方針： Azure Lighthouse 與 Azure Active Directory (Azure AD) 整合，以管理其資源。 使用 Azure AD 權利管理功能來自動化存取要求工作流程，包括存取權指派、評論和到期日。 也支援雙重或多階段核准。

- [什麼是 Azure AD 存取權評論](../active-directory/governance/access-reviews-overview.md) 

- [什麼是 Azure AD 權利管理](../active-directory/governance/entitlement-management-overview.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6：使用特殊權限存取工作站

**指引**：安全、隔離的工作站對於敏感性角色 (例如系統管理員、開發人員及重要服務操作員) 的安全性來說至關重要。 視您的需求而定，您可以使用高度安全的使用者工作站及/或 Azure 防禦，在生產環境中使用 Azure Lighthouse 來執行系統管理工作。 請使用 Azure Active Directory、Microsoft Defender 進階威脅防護 (ATP) 和/或 Microsoft Intune，以部署安全且受控的使用者工作站來進行系統管理工作。 受保護的工作站可以集中管理以強制執行安全的設定，包括強式驗證、軟體和硬體基準，以及受限的邏輯和網路存取。 

- [瞭解特殊許可權的存取工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [部署特殊權限存取工作站](/security/compass/privileged-access-deployment) (機器翻譯)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7：遵循恰到好處的系統管理 (最低權限原則) 

**指導** 方針： Azure Lighthouse 已與 Azure 角色型存取控制整合 (RBAC) 來管理其資源。 Azure RBAC 可讓您透過角色指派來管理 Azure 資源存取。 您可以將這些內建角色指派給使用者、群組、服務主體和受控識別。 某些資源有預先定義的內建角色，這些角色可透過 Azure CLI、Azure PowerShell 或 Azure 入口網站之類的工具進行清查或查詢。 透過 Azure RBAC 指派給資源的權限，應一律限定為角色所需的權限。 這類權限可補強 Azure AD Privileged Identity Management (PIM) 的 Just-In-Time (JIT) 方法，且您應定期加以檢閱。 使用內建角色來配置許可權，而且只在必要時才建立自訂角色。

Azure Lighthouse 可讓您使用 Azure 內建角色來存取委派的客戶資源。 在大部分情況下，您會想要將這些角色指派給群組或服務主體，而不是指派給許多個別的使用者帳戶。 如此一來，當您的存取需求變更時，就可以新增或移除個別使用者的存取權，而不需要更新並重新發佈方案。

若要將客戶資源委派給管理租使用者，必須由客戶租使用者中的非來賓帳戶完成部署，而該租使用者擁有 (上線訂用帳戶的擁有者內建角色，或其中包含上線) 的資源群組。

- [瞭解 Azure Lighthouse 中的租使用者、使用者和角色](concepts/tenants-users-roles.md)

- [如何將最低許可權原則套用至 Azure Lighthouse](concepts/recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege)

- [什麼是 Azure 角色型存取控制 (Azure RBAC) ](../role-based-access-control/overview.md) 

- [如何使用 Azure AD 身分識別和存取權檢閱](../active-directory/governance/access-reviews-overview.md)

**Azure 資訊安全中心監視**：是

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

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2：確保安全性小組可以存取資產清查和中繼資料

**指導** 方針：客戶的安全性小組可以審核活動記錄，以查看使用 Azure Lighthouse 的服務提供者所採取的活動。 

如果服務提供者想要允許其安全性小組審核委派的客戶資源，安全性小組的授權應包含讀取器內建角色。

- [客戶可如何審核服務提供者活動](how-to/view-service-provider-activity.md)

- [如何在將客戶上架至 Azure Lighthouse 時指定角色](how-to/onboard-customer.md#define-roles-and-permissions)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="am-3-use-only-approved-azure-services"></a>AM-3：僅使用已核准的 Azure 服務

**指導** 方針：使用 Azure 原則來審核和限制使用者可以在您的環境中布建的服務。 使用 Azure Resource Graph 則可查詢及探索其訂用帳戶內的資源。 您也可以使用 Azure 監視器來建立規則，以在偵測到未核准的服務時觸發警示。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md) 

- [如何使用 Azure 原則拒絕特定的資源類型](../governance/policy/samples/built-in-policies.md#general) 

- [如何使用 Azure Resource Graph Explorer 建立查詢](../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4：確保資產生命週期管理的安全性

**指導** 方針：移除已透過 Azure Lighthouse 委派的資源的存取權，不再需要這些資源，因此服務提供者不再具有存取權。 客戶或服務提供者可以移除存取。 

- [移除委派的存取權](how-to/remove-delegation.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>上午-5：限制使用者與 Azure Resource Manager 互動的能力

**指導** 方針： Azure Lighthouse 已與 Azure Active Directory 的 (Azure AD) 整合，以進行身分識別和驗證。 您可以使用 Azure 條件式存取，藉由設定「Microsoft Azure 管理」應用程式的「封鎖存取」，來限制使用者與 Azure 資源管理員互動的能力。

- [如何設定條件式存取以封鎖對 Azure 資源管理員的存取](../role-based-access-control/conditional-access-azure-management.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="logging-and-threat-detection"></a>記錄與威脅偵測

如需詳細資訊，請參閱 [Azure 安全性效能評定：記錄與威脅偵測](../security/benchmarks/security-controls-v2-logging-threat-detection.md)。

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1：啟用 Azure 資源的威脅偵測

**指導** 方針：透過 Azure Lighthouse，您可以監視客戶的 Azure 資源是否有潛在威脅和異常。 專注于取得高品質的警示，以減少因分析師排序的誤報。 警示可能源自于記錄資料、代理程式或其他資料。

使用 Azure 資訊安全中心內建的威脅偵測功能，這項功能是以監視 Azure 服務遙測和分析服務記錄為基礎。 您可以使用 Log Analytics 代理程式來收集資料，該代理程式會從系統讀取各種安全性相關設定和事件記錄檔，並將資料複製到您的工作區以進行分析。 

此外，請使用 Azure Sentinel 來建立分析規則，以在客戶的環境中尋找符合特定準則的威脅。 規則會在符合準則時產生事件，讓您可以調查每個事件。 Azure Sentinel 也可以匯入協力廠商的威脅情報，以增強其威脅偵測功能。 

- [Azure 資訊安全中心內的威脅防護](../security-center/azure-defender.md)

- [Azure 資訊安全中心安全性警示參考指南](../security-center/alerts-reference.md)

- [建立自訂分析規則來偵測威脅](../sentinel/tutorial-detect-threats-custom.md)

- [大規模管理 Azure Sentinel 工作區](how-to/manage-sentinel-workspaces.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2：啟用 Azure 身分識別與存取權管理的威脅偵測

**指導** 方針：透過 Azure Lighthouse，您可以使用 Azure 資訊安全中心對您管理的客戶租使用者中的某些可疑活動發出警示，例如過多的失敗驗證嘗試次數，以及訂用帳戶中已淘汰的帳戶。

Azure Active Directory (Azure AD) 提供下列使用者記錄，可在 Azure AD 報告中查看或與 Azure 監視器、Azure Sentinel 或其他 SIEM/監視工具整合，以取得更精密的監視和分析使用案例：
- 登入-登入報告提供受控應用程式使用方式和使用者登入活動的相關資訊。
- 稽核記錄 - 可針對各種功能在 Azure AD 內進行的所有變更，提供記錄追蹤功能。 稽核記錄的範例包括對 Azure AD 中任何資源所做的變更，像是新增或移除使用者、應用程式、群組、角色和原則。
- 有風險的登入-具風險的登入是指登入嘗試的指標，該嘗試可能是使用者帳戶的合法擁有者所執行。
- 標幟為有風險的使用者 - 有風險的使用者表示可能被盜用的使用者帳戶。

Azure 資訊安全中心也可對特定的可疑活動發出警示，例如驗證嘗試失敗次數過多，訂用帳戶中已淘汰的帳戶。 除了基本的安全性檢查監視以外，Azure 資訊安全中心的威脅防護模組也可以從個別的 Azure 計算資源 (虛擬機器、容器、App Service)、資料資源 (SQL DB 和儲存體) 與 Azure 服務層收集更深入的安全性警示。 這項功能可讓您瞭解個別資源內的帳戶異常。 

- [Azure Lighthouse 的增強服務和案例](concepts/cross-tenant-management-experience.md#enhanced-services-and-scenarios)

- [Azure Active Directory 中的稽核活動報表](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [啟用 Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) 

- [Azure 資訊安全中心內的威脅防護](../security-center/azure-defender.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4：啟用 Azure 資源的記錄功能

**指導** 方針：自動提供的活動記錄會包含 Azure Lighthouse 資源 (PUT、POST、DELETE) 的所有寫入作業， () 的讀取作業除外。 當您進行疑難排解時，可以使用活動記錄來尋找錯誤，或是監視組織中的使用者修改資源的方式。

有了 Azure Lighthouse，您就可以在您所管理的客戶租使用者之間，以可擴充的方式使用 Azure 監視器記錄。 直接在客戶租使用者中建立 Log Analytics 工作區，讓客戶資料保留在其租使用者中，而不是匯出到您的租使用者中。 這也可讓您集中監視 Log Analytics 所支援的任何資源或服務，讓您在監視的資料類型上有更大的彈性。

有 Azure Lighthouse 委派訂用帳戶的客戶可以查看 Azure 活動記錄資料，以查看所採取的所有動作。 這可讓客戶完全掌握服務提供者所執行的作業，以及客戶本身 Azure Active Directory (Azure AD) 租使用者中的使用者所執行的作業。

- [如何使用 Azure 監視器收集平臺記錄和計量](../azure-monitor/platform/diagnostic-settings.md) 

- [瞭解 Azure 中的記錄和不同的記錄類型](../azure-monitor/platform/platform-logs-overview.md)

- [大規模監視委派的資源](how-to/monitor-at-scale.md)

- [檢視服務提供者活動](how-to/view-service-provider-activity.md)

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5：將安全性記錄的管理與分析集中

**指導** 方針：集中記錄儲存體和分析以啟用相互關聯。 針對每個記錄來源，請確定您已指派資料擁有者、存取指引、儲存位置、使用哪些工具來處理和存取資料，以及資料保留需求。

確定您要將 Azure 活動記錄整合到您的集中記錄。 透過 Azure 監視器內嵌記錄，以匯總端點裝置、網路資源和其他安全性系統所產生的安全性資料。 在 Azure 監視器中，請使用 Log Analytics 工作區來查詢和執行分析，並使用 Azure 儲存體帳戶來長期和封存儲存體。

此外，請啟用資料並將其上架至 Azure Sentinel 或協力廠商 SIEM。

有了 Azure Lighthouse，您就可以在您所管理的客戶租使用者之間，以可擴充的方式使用 Azure 監視器記錄。 直接在客戶租使用者中建立 Log Analytics 工作區，讓客戶資料保留在其租使用者中，而不是匯出到您的租使用者中。 這也可讓您集中監視 Log Analytics 所支援的任何資源或服務，讓您在監視的資料類型上有更大的彈性。

有 Azure Lighthouse 委派訂用帳戶的客戶可以查看 Azure 活動記錄資料，以查看所採取的所有動作。 這可讓客戶完全掌握服務提供者所執行的作業，以及客戶本身 Azure Active Directory (Azure AD) 租使用者中的使用者所執行的作業。

許多組織選擇使用 Azure Sentinel 「經常性存取」資料，而這些資料經常使用，並 Azure 儲存體使用較不常使用的「冷」資料。

- [如何使用 Azure 監視器收集平臺記錄和計量](../azure-monitor/platform/diagnostic-settings.md)

- [大規模監視委派的資源](how-to/monitor-at-scale.md)

- [客戶可以如何查看服務提供者活動](how-to/view-service-provider-activity.md)

- [大規模管理 Azure Sentinel 工作區](how-to/manage-sentinel-workspaces.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="lt-6-configure-log-storage-retention"></a>LT-6：設定記錄儲存保留期

**指導** 方針： Azure Lighthouse 目前未產生任何與安全性相關的記錄。 想要查看服務提供者活動的客戶可以根據合規性、法規和商務需求來設定記錄保留期。 

在 Azure 監視器中，您可以根據組織的合規性法規來設定 Log Analytics 工作區保留期限。 使用 Azure 儲存體、Data Lake 或 Log Analytics 工作區帳戶來取得長期和封存儲存體。

- [變更 Log Analytics 中的資料保留期限](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [如何設定 Azure 儲存體客戶紀錄的保留原則](../storage/common/storage-monitor-storage-account.md#configure-logging)

- [Azure 資訊安全中心警示和建議的匯出](../security-center/continuous-export.md) 設定，而且客戶無法設定任何記錄保留期。

- [客戶可如何審核服務提供者的活動記錄資料](how-to/view-service-provider-activity.md)

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

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3：偵測與分析 – 根據高品質警示建立事件

**指引**：確定您具有建立高品質警示並測量警示品質的流程。 這可讓您從過去的事件吸取教訓，並為分析師設定警示優先順序，這樣他們就不會浪費時間在誤報上。 

您可以根據過去事件的經驗、已驗證的社區來源，以及設計成透過融合和相互關聯不同信號來源以產生和清除警示的工具，來建置高品質警示。 

Azure 資訊安全中心在許多 Azure 資產之間提供高品質的警示。 您可以使用 ASC 資料連接器將警示串流至 Azure Sentinel。 Azure Sentinel 可讓您建立進階警示規則，以自動產生事件供調查之用。 

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

如需詳細資訊，請參閱 [Azure 安全性效能評定：狀況和弱點管理](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)。

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1：建立 Azure 服務的安全設定 

**指導** 方針： Azure Lighthouse 支援下列可在 Azure 資訊安全中心中使用的服務特定原則，以審查並強制執行 Azure 資源的設定。 這可以在 Azure 資訊安全中心或 Azure 原則方案中設定。

- 允許透過 Azure Lighthouse 管理租使用者識別碼

- 稽核管理租用戶的範圍委派

您可以使用 Azure 藍圖，在單一藍圖定義中自動化服務和應用程式環境的部署和設定，包括 Azure Resource Manager 範本、Azure RBAC 控制項和原則。

- [Azure Lighthouse 原則](samples/policy-reference.md)

- [教學課程-建立和管理原則以強制執行合規性](../governance/policy/tutorials/create-and-manage.md) 

- [Azure 藍圖](../governance/blueprints/overview.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2：維持 Azure 服務的安全設定

**指導** 方針： Azure Lighthouse 支援下列可在 Azure 資訊安全中心中使用的服務特定原則，以審查並強制執行 Azure 資源的設定。 這可以在 Azure 資訊安全中心或 Azure 原則方案中設定。

- [Azure Lighthouse 原則](samples/policy-reference.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3：建立計算資源的安全設定

**指導** 方針：使用 Azure 資訊安全中心和 Azure 原則來建立所有計算資源的安全設定，包括 vm、容器及其他資源。

- [如何監視 Azure 資訊安全中心建議](../security-center/security-center-recommendations.md) 

- [安全性建議 - 參考指南](../security-center/recommendations-reference.md)

**Azure 資訊安全中心監視**：是

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

-   使用 Azure 原生和協力廠商資料保護功能

-   傳輸中和待用使用案例的資料加密需求

-   適當的密碼編譯標準

如需詳細資訊，請參閱下列參考資料：
- [Azure 安全性架構建議 - 儲存體、資料和加密](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json) (機器翻譯)

- [Azure 安全性基礎觀念 - Azure 資料安全性、加密和儲存體](../security/fundamentals/encryption-overview.md) (機器翻譯)

- [雲端採用架構 - Azure 資料安全性和加密最佳做法](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json) (機器翻譯)

- [Azure 安全性效能評定 - 資產管理](../security/benchmarks/security-controls-v2-incident-response.md)

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

-   網際網路邊緣與輸入和輸出策略/azure/security/benchmarks/security-controls-v2-logging-threat-detection
-   混合式雲端和內部部署互連能力策略

-   最新的網路安全性成品 (例如網路圖、參考網路架構)

如需詳細資訊，請參閱下列參考資料：
- [Azure 安全性最佳做法 11 - 架構。單一整合的安全性策略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy) (機器翻譯)

- [Azure 安全性效能評定 - 網路安全性](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

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

**指引**：建立記錄和威脅回應策略，以快速偵測威脅並進行補救，同時符合合規性需求。 優先為分析師提供高品質警示和順暢體驗，讓他們能夠專注處理威脅，而非整合和手動步驟。 

此策略應該包含下列項目的已記載指引、原則和標準： 

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
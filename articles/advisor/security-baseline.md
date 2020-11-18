---
title: 適用于 Azure Advisor 的 Azure 安全性基準
description: Azure Advisor 安全性基準提供的程式指引和資源，可讓您執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: advisor
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 9ca8fbfb7f339c18f32379c64b40bc86e2bc47a6
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94810969"
---
# <a name="azure-security-baseline-for-azure-advisor"></a>適用于 Azure Advisor 的 Azure 安全性基準

此安全性基準會將 [Azure 安全性基準測試版本 2.0](../security/benchmarks/overview.md) 的指引套用至 Azure Advisor。 Azure 安全性基準提供如何在 Azure 上保護雲端解決方案的建議。 內容會依 Azure 安全性基準測試所定義的 **安全性控制** ，以及適用于 Azure Advisor 的相關指引來分組。 已排除不適用 Azure Advisor 的 **控制項**。

若要查看 Azure Advisor 如何完全對應至 Azure 安全性基準測試，請參閱 [完整的 Azure Advisor 安全性基準對應](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)檔案。

## <a name="identity-management"></a>身分識別管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：身分識別管理](/azure/security/benchmarks/security-controls-v2-identity-management)。*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1：將 Azure Active Directory 標準化為中央身分識別和驗證系統

**指導** 方針： Azure Advisor 使用 Azure Active Directory (Azure AD) 作為預設身分識別和存取管理服務。 將 Azure AD 的標準化，以管理組織的身分識別與存取管理：

- Microsoft 雲端資源，例如 Azure 入口網站、Azure 儲存體、Azure 虛擬機器 (Linux 和 Windows) 、Azure Key Vault、PaaS 和 SaaS 應用程式
- 您組織的資源，例如 Azure 或公司網路資源上的應用程式

確保 Azure AD 在貴組織的雲端安全性實務中具有高優先順序的保障。 Azure AD 也會提供身分識別安全分數，以協助您評估與 Microsoft 最佳作法建議相關的身分識別安全性狀態。 您可以使用分數來量測您的設定如何符合最佳做法建議，以及改善安全性狀態。

請注意，Azure AD 支援外部身分識別，讓沒有 Microsoft 帳戶的使用者可以使用其外部身分識別登入其應用程式和資源。

- [Azure Active Directory 中的租用](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [如何建立和設定 Azure AD 實例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [使用應用程式的外部識別提供者](/azure/active-directory/b2b/identity-providers) 

- [Azure Active Directory 中的身分識別安全分數為何](../active-directory/fundamentals/identity-secure-score.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3：使用 Azure AD 單一登入 (SSO) 來存取應用程式

**指導** 方針： Azure Advisor 使用 Azure Active Directory (Azure AD) 來提供 Azure 資源、雲端應用程式和內部部署應用程式的身分識別與存取權管理。 這包括企業身分識別，例如員工，以及外部身分識別，例如合作夥伴、廠商和供應商。 

使用單一登入來管理及保護您組織的資料與內部部署和雲端中的資源存取。 將所有使用者、應用程式和裝置連接到 Azure AD，以進行無縫、安全的存取，以及更高的可見度和控制。

- [瞭解 Azure AD 的應用程式 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4：針對所有以 Azure Active Directory 為基礎的存取，使用強式驗證控制項

**指導** 方針： Azure Advisor 使用 Azure Active Directory (Azure AD) ，可透過多重要素驗證和強式無密碼方法來支援強式驗證控制項。
- 多重要素驗證-啟用 Azure AD 多重要素驗證，並遵循 Azure 資訊安全中心的身分識別和存取管理建議，以取得多重要素驗證設定中的一些最佳作法。 您可以根據登入條件和風險因素，針對所有、選取的使用者或依使用者的層級強制執行多重要素驗證。
- 無密碼 authentication –有三個無密碼 authentication 選項可供使用。 它們是、Windows Hello 企業版、Microsoft Authenticator 應用程式，以及內部部署驗證方法（例如智慧卡）。

針對系統管理員和特殊許可權的使用者，請確定已使用強式驗證方法的最高層級，接著向其他使用者推出適當的增強式驗證原則。

- [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Azure Active Directory 的無密碼 authentication 選項簡介](../active-directory/authentication/concept-authentication-passwordless.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5：監視和警示帳戶異常

**指導** 方針： Azure Advisor 與 Azure Active Directory (Azure AD) 整合，其中提供下列資料來源：
- 登入-[登入] 報表會提供受控應用程式使用方式和使用者登入活動的相關資訊。
- 稽核記錄 - 可針對各種功能在 Azure AD 內進行的所有變更，提供記錄追蹤功能。 稽核記錄的範例包括對 Azure AD 中任何資源所做的變更，像是新增或移除使用者、應用程式、群組、角色和原則。
- 有風險的登入-具風險的登入是指登入嘗試的指標，該嘗試可能是使用者帳戶的合法擁有者所執行。
- 標幟為有風險的使用者 - 有風險的使用者表示可能被盜用的使用者帳戶。

您可以使用這些資料來源，與 Azure 監視器、Azure Sentinel 或協力廠商 SIEM 系統整合。 在 Azure 資訊安全中心上設定警示，以找出某些可疑的活動，例如失敗的驗證嘗試次數過多、訂用帳戶中已淘汰的帳戶。

Azure 進階威脅防護 (ATP) 是一種安全性解決方案，可以使用 Active Directory 信號來識別、偵測及調查 Advanced 威脅、遭盜用的身分識別，以及惡意的內部動作。

- [Azure Active Directory 中的審核活動報告](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [如何在 Azure 資訊安全中心監視使用者的身分識別和存取活動](../security-center/security-center-identity-access.md) 

- [Azure 資訊安全中心的威脅情報保護模組中的警示](../security-center/alerts-reference.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6：根據條件限制 Azure 資源存取

**指導** 方針：根據使用者定義的條件，Azure Advisor 支援 Azure Active Directory 的 (Azure AD) 條件式存取功能，以取得更細微的存取控制。 例如，來自特定 IP 範圍的使用者登入，將需要使用多重要素驗證來進行登入。 細微驗證會話管理原則也可以用於不同的使用案例。

- [Azure 條件式存取總覽](../active-directory/conditional-access/overview.md) 

- [一般條件式存取原則](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [使用條件式存取設定驗證會話管理](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="privileged-access"></a>特殊許可權存取

*如需詳細資訊，請參閱 [Azure 安全性基準測試：](/azure/security/benchmarks/security-controls-v2-privileged-access)特殊許可權存取。*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2：限制對商務關鍵性系統的系統管理存取

**指導** 方針： Azure Advisor 使用 azure 角色型存取控制 (azure RBAC) 藉由限制哪些帳戶被授與存取權給他們所屬的訂用帳戶和管理群組，來隔離對商務關鍵系統的存取。

限制對管理、身分識別和安全性系統的存取權，這些系統具有您商務關鍵性存取權的系統管理存取權，例如 Active Directory 網網域控制站 (Dc) 、安全性工具和系統管理工具，以及安裝在商務關鍵性系統上的代理程式。 入侵這些管理和安全性系統的攻擊者，可以立即 weaponize 它們來入侵商務關鍵資產。

所有類型的存取控制都應符合您的企業分割策略，以確保存取控制的一致性。

- [Azure 元件和參考模型](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [管理群組存取](../governance/management-groups/overview.md#management-group-access)

- [Azure 訂用帳戶管理員](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3：定期審核和協調使用者存取

**指導** 方針： Azure Advisor 使用 Azure Active Directory (Azure AD) 帳戶來管理其資源、定期審核使用者帳戶和存取指派，以確保帳戶及其存取權都有效。 執行 Azure AD 存取評論，以審查群組成員資格、企業應用程式的存取權，以及角色指派。 Azure AD 報告可以提供記錄以協助探索過時的帳戶。 

此外，請使用 Azure AD 的 Privileged Identity Management 功能來建立存取權審核報表工作流程，以協助進行審核程式。 Privileged Identity Management 也可以設定為在建立過多的系統管理員帳戶時發出警示，以及識別已過時或設定不正確的系統管理員帳戶。

請注意，有些 Azure 服務支援不是透過 Azure AD 管理的本機使用者和角色。 客戶必須分別管理這些使用者。

- [在 Privileged Identity Management (PIM) 中建立 Azure 資源角色的存取權審核 ](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [如何使用 Azure AD 身分識別和存取權評論](/azure/active-directory/governance/access-reviews-overvie)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6：使用特殊許可權的存取工作站

**指導** 方針：安全、隔離的工作站對於機密角色（例如系統管理員、開發人員和重要服務操作員）的安全性至關重要。 

使用高度安全的使用者工作站及/或 Azure 防禦來進行系統管理工作。 選擇 Azure Active Directory (Azure AD) ，Microsoft Defender 進階威脅防護 (ATP) ，包括部署安全且受管理的使用者工作站以進行系統管理工作的 Microsoft Intune。 

集中管理安全的工作站以強制執行安全的設定，包括強式驗證、軟體和硬體基準、受限的邏輯和網路存取。

- [瞭解特殊許可權的存取工作站](../active-directory/devices/concept-azure-managed-workstation.md) 
 
- [部署特殊許可權存取工作站](../active-directory/devices/howto-azure-managed-workstation.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7：遵循足夠的系統管理 (最低許可權準則)  

**指導** 方針： Azure Advisor 已與 azure 角色型存取控制整合 (azure RBAC) 來管理其資源。 使用 Azure RBAC 透過角色指派來管理 Azure 資源存取權。 

將角色指派給使用者、群組服務主體和受控識別。 某些資源有預先定義的內建角色，可透過 Azure CLI、Azure PowerShell 或 Azure 入口網站等工具進行清查或查詢。 透過 Azure RBAC 指派給資源的許可權應該一律限制為角色所需的許可權。 這可補充 Azure AD Privileged Identity Management (PIM) 的及時 (JIT) 方法，並應定期檢查。

使用內建角色來配置許可權，而且只在必要時才建立自訂角色。

什麼是 Azure 角色型存取控制 (Azure RBAC) 。/role-based-access-control/overview.md 

- [如何在 Azure 中設定 RBAC](../role-based-access-control/role-assignments-portal.md) 

- [如何使用 Azure AD 身分識別和存取權評論](/azure/active-directory/governance/access-reviews-overview)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="asset-management"></a>資產管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資產管理](/azure/security/benchmarks/security-controls-v2-asset-management)。*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>上午-1：確保安全性小組能看到資產的風險

**指導** 方針：確定安全性小組已獲得 Azure 租使用者和訂用帳戶中的安全性讀取者許可權，讓他們可以使用 Azure 資訊安全中心監視安全性風險。 

根據安全性小組責任的結構，監視安全性風險可能是中央安全性小組或本地小組的責任。 話雖如此，安全性見解和風險一律必須在組織內集中匯總。 

安全性讀取者許可權可以廣泛地套用到整個租使用者， (根管理群組) 或限定于管理群組或特定訂用帳戶。 

注意：若要瞭解工作負載和服務，可能需要其他許可權。 

- [安全性讀取者角色總覽](../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 管理群組總覽](../governance/management-groups/overview.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>上午-5：限制使用者與 Azure Resource Manager 互動的能力

**指導** 方針： Azure Advisor 使用 Azure Active Directory (Azure AD) 進行身分識別和驗證，而 Azure 入口網站和 Azure Resource Manager 用來管理 Advisor。 

根據商務需求，使用 Azure 條件式存取來限制使用者與 Azure 資源管理員互動的能力，其方式是根據商務需求設定「封鎖存取」作為「Microsoft Azure 管理」應用程式。 

- [如何設定條件式存取以封鎖對 Azure 資源管理員的存取](../role-based-access-control/conditional-access-azure-management.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="logging-and-threat-detection"></a>記錄和威脅偵測

*如需詳細資訊，請參閱 [Azure 安全性基準測試：記錄和威脅偵測](/azure/security/benchmarks/security-controls-v2-logging-threat-protection)。*

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4：啟用 Azure 資源的記錄

**指導** 方針：活動記錄會自動提供，並包含 Azure Advisor 資源 (PUT、POST、DELETE) 的所有寫入作業， () 的讀取作業除外。 

當您進行疑難排解時，可以使用活動記錄來尋找錯誤，或是監視組織中的使用者修改資源的方式。

- [瞭解 Azure 中的記錄和不同的記錄類型](../azure-monitor/platform/platform-logs-overview.md)

- [如何使用 Azure 監視器收集平臺記錄和計量](../azure-monitor/platform/diagnostic-settings.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5：集中安全性記錄檔管理和分析

**指導** 方針：集中記錄儲存體和分析以啟用相互關聯。 針對每個記錄來源，請確定您已指派資料擁有者、存取指引、儲存位置、使用哪些工具來處理和存取資料，以及資料保留需求。

確定您要將 Azure 活動記錄整合到您的集中記錄。 透過 Azure 監視器內嵌記錄，以匯總端點裝置、網路資源和其他安全性系統所產生的安全性資料。 在 Azure 監視器中，請使用 Log Analytics 工作區來查詢和執行分析，並使用 Azure 儲存體帳戶來長期和封存儲存體。

此外，請啟用資料並將其上架至 Azure Sentinel 或協力廠商 SIEM。 許多組織選擇使用 Azure Sentinel 「經常性存取」資料，而這些資料經常使用，並 Azure 儲存體使用較不常使用的「冷」資料。

- [如何使用 Azure 監視器收集平臺記錄和計量](../azure-monitor/platform/diagnostic-settings.md) 

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="lt-6-configure-log-storage-retention"></a>LT-6：設定記錄儲存體保留期

**指導** 方針：確定用於儲存 Azure Advisor 記錄的任何儲存體帳戶或 log Analytics 工作區都已根據您組織的合規性法規設定記錄保留期限。
在 Azure 監視器中，您可以根據組織的合規性法規來設定 Log Analytics 工作區保留期限。 使用 Azure 儲存體、Data Lake 或 Log Analytics 工作區帳戶來取得長期和封存儲存體。

- [如何設定 Log Analytics 工作區保留期限](../azure-monitor/platform/manage-cost-storage.md) 

- [在 Azure 儲存體帳戶中儲存資源記錄](/azure/azure-monitor/platform/resource-logs-collect-storage)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱 [Azure 安全性基準測試：事件回應](/azure/security/benchmarks/security-controls-v2-incident-response)。*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1：準備–更新 Azure 的事件回應程式

**指導** 方針：確定您的組織有處理常式來回應安全性事件、已更新 Azure 的這些處理常式，並會定期執行這些程式以確保其就緒。

- [跨企業環境執行安全性](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [事件回應參考指南](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2：準備–安裝事件通知

**指導** 方針：在 Azure 資訊安全中心中設定安全性事件連絡人資訊。 如果 Microsoft 安全性回應中心 (MSRC) 發現您的資料已被非法或未經授權的物件存取，Microsoft 會使用此連絡人資訊來與您聯繫。 您也可以選擇根據您的事件回應需求，在不同的 Azure 服務中自訂事件警示和通知。 

- [如何設定 Azure 資訊安全中心安全性連絡人](../security-center/security-center-provide-security-contact-details.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3：偵測和分析–根據高品質警示建立事件

**指導** 方針：確定您有建立高品質警示和測量警示品質的流程。 這可讓您學習過去事件的課程，並設定分析師的警示優先順序，讓他們不會浪費時間誤報。 

高品質的警示可以根據過去事件的體驗、驗證的社區來源，以及設計來藉由融合和關聯各種信號來源來產生和清除警示的工具來建立。 

Azure 資訊安全中心在許多 Azure 資產之間提供高品質的警示。 您可以使用 ASC 資料連線器，將警示串流至 Azure Sentinel。 Azure Sentinel 可讓您建立 advanced 警示規則，以自動產生調查的事件。 

使用「匯出」功能來匯出您的 Azure 資訊安全中心警示和建議，以協助找出 Azure 資源的風險。 以手動方式或持續持續的方式匯出警示和建議。

- [如何設定匯出](../security-center/continuous-export.md)

- [如何將警示串流至 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4：偵測和分析–調查事件

**指導** 方針：確定分析師可以在調查潛在事件時查詢及使用不同的資料來源，以全面瞭解發生的情況。 您應收集不同的記錄檔，以追蹤潛在攻擊者在整個終止鏈上的活動，以避免點。  您也應該確定已針對其他分析師和未來的歷程記錄參考來捕捉見解和學習。  

用於調查的資料來源包括已從範圍內服務和執行中的系統收集的集中式記錄來源，但也可以包括：

- 網路資料–使用網路安全性群組的流量記錄、Azure 網路監看員和 Azure 監視器來捕捉網路流量記錄和其他分析資訊。 

- 執行中系統的快照集： 

    - 使用 Azure 虛擬機器的快照集功能來建立執行中系統磁片的快照集。 

    - 使用作業系統的原生記憶體傾印功能來建立執行中系統記憶體的快照。

    - 您可以使用 Azure 服務的快照集功能，或您軟體本身的功能來建立執行中系統的快照集。

Azure Sentinel 在幾乎任何記錄來源和案例管理入口網站中提供廣泛的資料分析，以管理事件的完整生命週期。 調查期間的智慧資訊可以與事件產生關聯，以供追蹤和報告之用。 

- [建立 Windows 機器磁片的快照集](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [建立 Linux 機器磁片的快照集](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure 支援診斷資訊和記憶體傾印集合](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [使用 Azure Sentinel 調查事件](../sentinel/tutorial-investigate-cases.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5：偵測和分析-設定事件優先順序

**指導** 方針：提供內容給分析人員，根據警示嚴重性和資產敏感度先將焦點放在哪個事件上。 

Azure 資訊安全中心會將嚴重性指派給每個警示，以協助您排列應先調查哪些警示的優先順序。 嚴重性取決於資訊安全中心在尋找或用來發出警示的分析中的可信度，以及導致警示的活動背後有惡意意圖的信賴等級。

此外，請使用標記來標示資源，並建立命名系統來識別和分類 Azure 資源，尤其是處理敏感性資料的資源。  您需負責根據發生事件的 Azure 資源和環境的重要性，設定警示的補救優先順序。

- [Azure 資訊安全中心的安全性警示](../security-center/security-center-alerts-overview.md)

- [使用標記來組織 Azure 資源](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6：內含專案、根除和復原–將事件處理自動化

**指導** 方針：自動化手動重複的工作，以加快回應時間並降低分析師的負擔。 手動工作需要較長的時間來執行、讓每個事件變慢，並減少分析師可以處理的事件數目。 手動工作也會增加分析師疲勞，這樣會增加造成延遲的人為錯誤的風險，並降低分析師在複雜工作上有效地專注的能力。 使用 Azure 資訊安全中心和 Azure Sentinel 中的工作流程自動化功能，自動觸發動作或執行腳本，以回應傳入的安全性警示。 腳本會採取動作，例如傳送通知、停用帳戶，以及隔離有問題的網路。 

- [在安全中心設定工作流程自動化](../security-center/workflow-automation.md)

- [在 Azure 資訊安全中心中設定自動化威脅回應](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [在 Azure Sentinel 中設定自動化威脅回應](../sentinel/tutorial-respond-threats-playbook.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="posture-and-vulnerability-management"></a>狀況和弱點管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：狀態和弱點管理](/azure/security/benchmarks/security-controls-v2-vulnerability-management)。*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8：進行定期攻擊模擬

**指導** 方針：如有需要，請在您的 Azure 資源上進行滲透測試或 red team 活動，並確保修復所有重要的安全性結果。
遵循 Microsoft Cloud 滲透測試的參與規則，以確保您的滲透測試不違反 Microsoft 原則。 針對受 Microsoft 管理的雲端基礎結構、服務和應用程式，使用 Microsoft 的策略和執行的 Red 小組和即時網站滲透測試。

- [Azure 中的滲透測試](../security/fundamentals/pen-testing.md)

- [滲透測試運作規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red 小組](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="governance-and-strategy"></a>控管與策略

*如需詳細資訊，請參閱 [Azure 安全性基準測試：治理和策略](/azure/security/benchmarks/security-controls-v2-governance-strategy)。*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1：定義資產管理與資料保護策略 

**指導** 方針：確保您可以記錄並傳達明確的策略，以持續監視和保護系統和資料。 優先探索、評估、保護及監視商務關鍵資料和系統。 

此策略應該包含已記載的指導方針、原則，以及下列元素的標準： 

-   以商務風險為依據的資料分類標準

-   安全性組織風險和資產清查的可見度 

-   安全性組織核准 Azure 服務以供使用 

-   資產在其生命週期內的安全性

-   根據組織資料分類所需的存取控制策略

-   使用 Azure 原生和協力廠商資料保護功能

-   傳輸中和待用使用案例的資料加密需求

-   適當的密碼編譯標準

如需詳細資訊，請參閱下列參考資料：
- [Azure 安全性架構建議-儲存體、資料和加密](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Azure 安全性基礎-Azure 資料安全性、加密和儲存體](../security/fundamentals/encryption-overview.md)

- [雲端採用架構-Azure 資料安全性和加密最佳作法](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure 安全性基準測試-資產管理](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Azure 安全性基準測試-資料保護](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2：定義企業分割策略 

**指導** 方針：使用身分識別、網路、應用程式、訂用帳戶、管理群組和其他控制項之間的組合，建立企業級的策略來分割資產的存取權。

請仔細平衡安全性分隔的需求，以及針對需要彼此通訊並存取資料的系統，啟用每日作業的需求。

請確定跨控制項類型（包括網路安全性、身分識別和存取模型、應用程式許可權或存取模型，以及人力流程式控制件）一致地實行分割策略。

- [Azure 中的分割策略指引 (影片) ](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Azure (檔中的分割策略指引) ](/security/compass/governance#enterprise-segmentation-strategy)

- [利用企業分割策略來調整網路分割](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3：定義安全性狀態管理原則

**指導** 方針：持續測量並減輕個別資產及其託管環境的風險。 設定高價值資產的優先順序以及高度公開的攻擊面，例如已發佈的應用程式、網路輸入和輸出點、使用者和系統管理員端點等等。

- [Azure 安全性基準測試-狀態與弱點管理](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4：讓組織角色、職責和責任保持一致

**指導** 方針：確保您可以記錄安全性組織中角色和責任的明確策略，並對其進行溝通。 優先提供安全性決策的明確責任、教育所有人共同責任模型，以及教育技術小組來保護雲端。

- [Azure 安全性最佳作法 1-人員：教育小組雲端安全性旅程](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure 安全性最佳作法 2-人員：教育小組雲端安全性技術](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure 安全性最佳作法 3-處理：指派雲端安全性決策的責任](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-5-define-network-security-strategy"></a>GS-5：定義網路安全性策略

**指導** 方針：建立 Azure 網路安全性方法作為組織整體安全性存取控制策略的一部分。  

此策略應該包含已記載的指導方針、原則，以及下列元素的標準： 

-   集中式網路管理和安全性責任

-   虛擬網路分割模型與企業分割策略一致

-   不同威脅和攻擊案例中的補救策略

-   網際網路邊緣與輸入和輸出策略

-   混合式雲端和內部部署互連能力策略

-   最新的網路安全性構件 (例如網狀圖、參考網路架構) 

如需詳細資訊，請參閱下列參考資料：
- [Azure 安全性最佳做法 11-架構。單一整合的安全性策略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure 安全性基準測試-網路安全性](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Azure 網路安全性概觀](../security/fundamentals/network-overview.md)

- [商業網路架構策略](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6：定義身分識別和特殊許可權存取策略

**指導** 方針：建立 Azure 身分識別和特殊許可權的存取方法，作為組織整體企業存取控制策略的一部分。  

此策略應該包含已記載的指導方針、原則，以及下列元素的標準： 

-   集中式身分識別和驗證系統，以及其與其他內部和外部身分識別系統的互連能力

-   不同使用案例和條件中的強式驗證方法

-   保護高許可權的使用者

-   異常使用者活動監視和處理  

-   使用者身分識別和存取權檢查和協調流程

如需詳細資訊，請參閱參考的連結。

- [Azure 安全性基準測試-身分識別管理](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure 安全性基準測試-特殊許可權存取](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Azure 安全性最佳做法 11-架構。單一整合的安全性策略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure 身分識別管理安全性概觀](../security/fundamentals/identity-management-overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7：定義記錄和威脅回應策略

**指導** 方針：建立記錄和威脅回應策略，以快速偵測和補救威脅，同時符合合規性需求。 排定為分析師提供高品質的警示和順暢的體驗，讓他們可以專注于威脅，而不是整合和手動步驟。 

此策略應該包含已記載的指導方針、原則，以及下列元素的標準： 

-   安全性作業 (SecOps) 組織的角色和責任 

-   妥善定義的事件回應程式，與 NIST 或其他產業架構一致 

-   記錄檔捕捉和保留，以支援威脅偵測、事件回應和合規性需求

-   使用 SIEM、原生 Azure 功能和其他來源，集中查看威脅的相關資訊以及相關資訊 

-   與您的客戶、供應商和感興趣的公開方相關的通訊和通知計畫

-   使用 Azure 原生和協力廠商平臺進行事件處理，例如記錄和威脅偵測、取證和攻擊補救和根除

-   處理事件和事件後活動的流程，例如經驗教訓和辨識項保留

如需詳細資訊，請參閱下列參考資料：

- [Azure 安全性基準測試-記錄和威脅偵測](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure 安全性基準測試-事件回應](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure 安全性最佳作法 4-處理。更新雲端的事件回應程式](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure 採用架構、記錄和報告決策指南](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure 企業規模、管理及監視](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性基準測試 V2 總覽](/azure/security/benchmarks/overview)
- 深入了解 [Azure 資訊安全性基準](/azure/security/benchmarks/security-baselines-overview)

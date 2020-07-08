---
title: Azure Active Directory 治理作業參考指南
description: 此操作參考指南說明保護治理管理時應採取的檢查和動作
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 4826bcdc85e0c6189c51aa262014fe154bb479b1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74535453"
---
# <a name="azure-active-directory-governance-operations-reference-guide"></a>Azure Active Directory 治理作業參考指南

[Azure AD 作業參考指南](active-directory-ops-guide-intro.md)的這一節說明您應採取的檢查和動作，以評估並證明授與存取非許可權和特殊許可權身分識別、audit 及控制環境變更的許可權。

> [!NOTE]
> 這些建議是在發行日期之後的最新版本，但可能會隨著時間而改變。 組織應持續評估其治理實務，因為 Microsoft 產品和服務會隨著時間而演進。

## <a name="key-operational-processes"></a>關鍵操作程式

### <a name="assign-owners-to-key-tasks"></a>將擁有者指派給主要工作

管理 Azure Active Directory 需要持續執行重要的作業工作和進程，這可能不是首度發行專案的一部分。 您必須設定這些工作來優化您的環境，這仍然很重要。 主要工作和其建議的擁有者包括：

| Task | 擁有者 |
| :- | :- |
| 封存 Azure AD SIEM 系統中的 audit 記錄 | InfoSec 營運小組 |
| 探索管理不相容的應用程式 | IAM 作業小組 |
| 定期審查應用程式的存取權 | InfoSec 架構團隊 |
| 定期審查外部身分識別的存取權 | InfoSec 架構團隊 |
| 定期審查誰有特殊許可權角色 | InfoSec 架構團隊 |
| 定義安全性閘道以啟用特殊許可權角色 | InfoSec 架構團隊 |
| 定期審查同意授權 | InfoSec 架構團隊 |
| 針對組織中的員工設計應用程式和資源的類別目錄和存取套件 | 應用程式擁有者 |
| 定義安全性原則以指派使用者存取套件 | InfoSec 小組 + 應用程式擁有者 |
| 如果原則包含核准工作流程，請定期審查工作流程核准 | 應用程式擁有者 |
| 使用存取審查來檢查安全性原則中的例外狀況，例如條件式存取原則 | InfoSec 營運小組 |

當您檢查清單時，您可能會發現需要為遺漏擁有者的工作指派擁有者，或為擁有者未與上述建議一致的工作調整擁有權。

#### <a name="owner-recommended-reading"></a>擁有者建議閱讀

- [在 Azure Active Directory 中指派系統管理員角色](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Azure 中的治理](https://docs.microsoft.com/azure/security/governance-in-azure)

### <a name="configuration-changes-testing"></a>設定變更測試

在測試時，有一些需要特殊考慮的變更，從簡單的技術（例如，將使用者的目標子集推出以在平行測試租使用者中部署變更）。 如果您尚未實行測試策略，您應該根據下表中的指導方針來定義測試方法：

| 狀況| 建議 |
|-|-|
|將驗證類型從同盟變更為 PHS/PTA，反之亦然| 使用[分段推出](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-staged-rollout)來測試變更驗證類型的影響。|
|推出新的條件式存取（CA）原則或身分識別保護原則|建立新的 CA 原則並指派給測試使用者。|
|上架應用程式的測試環境|將應用程式新增至生產環境，並將其從 [MyApps] 面板中隱藏，並在品質保證（QA）階段將它指派給測試使用者。|
|變更同步處理規則|使用目前在生產環境中的相同設定（也稱為「預備模式」）來執行測試 Azure AD Connect 中的變更，並分析 CSExport 結果。 如果滿意，請在準備好時切換到生產環境。|
|變更商標|在個別的測試租使用者中測試。|
|推出新功能|如果功能支援推出目標使用者集合，請識別試驗使用者並建立。例如，自助式密碼重設和多重要素驗證可以鎖定特定使用者或群組。|
|將應用程式從內部部署身分識別提供者（IdP）（例如 Active Directory）切換為 Azure AD|如果應用程式支援多個 IdP 設定（例如 Salesforce），請在變更期間（如果應用程式引進 HRD 頁面）進行和測試 Azure AD。 如果應用程式不支援多個 Idp，請在變更控制視窗和程式停機時間排程測試。|
|更新動態群組規則|使用新的規則建立平行動態群組。 比較計算結果，例如，使用相同的條件來執行 PowerShell。<br>如果測試成功，請交換使用舊群組的位置（如果可行）。|
|遷移產品授權|請參閱[在 Azure Active Directory 中變更授權群組中單一使用者的授權](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-change-licenses)。|
|變更 AD FS 規則，例如授權、發行、MFA|使用群組宣告將目標設為使用者的子集。|
|變更 AD FS 驗證體驗或類似的整個伺服器陣列變更|建立具有相同主機名稱的平行伺服器陣列、執行設定變更、使用 HOSTS 檔案、NLB 路由規則或類似的路由從用戶端進行測試。<br>如果目標平臺不支援主機檔案（例如行動裝置），則控制變更。|

## <a name="access-reviews"></a>存取權檢閱

### <a name="access-reviews-to-applications"></a>存取應用程式的評論

經過一段時間後，使用者在不同的小組和職位移動時，可能會累積資源的存取權。 資源擁有者必須定期審查應用程式的存取權，並移除使用者生命週期中不再需要的許可權。 Azure AD[存取審查](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)可讓組織有效地管理群組成員資格、企業應用程式的存取權，以及角色指派。 資源擁有者應該定期審查使用者的存取權，以確保只有適當的人員可以繼續存取。 在理想的情況下，您應該考慮使用 Azure AD 存取審查來進行這項工作。

![存取權檢閱啟動頁面](./media/active-directory-ops-guide/active-directory-ops-img15.png)

> [!NOTE]
> 與存取評論互動的每個使用者都必須具有付費 Azure AD Premium P2 授權。

### <a name="access-reviews-to-external-identities"></a>存取外部身分識別的評論

在所需的時間內，請務必繼續存取僅限於所需資源的外部身分識別。 使用 Azure AD 的[存取權審查](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)，針對所有外部身分識別和應用程式存取建立週期性自動化存取權審查流程。 如果進程已經存在於內部部署中，請考慮使用 Azure AD 存取評論。 一旦應用程式已淘汰或不再使用，請移除所有具有應用程式存取權的外部身分識別。

> [!NOTE]
> 與存取評論互動的每個使用者都必須具有付費 Azure AD Premium P2 授權。

## <a name="privileged-account-management"></a>具有特殊權限的帳戶管理

### <a name="privileged-account-usage"></a>特殊許可權帳戶的使用方式

駭客通常會以系統管理員帳戶和特殊許可權存取的其他元素為目標，以快速取得敏感性資料和系統的存取權。由於具有特殊許可權角色的使用者通常會在一段時間後累積，因此請務必定期審查和管理系統管理員存取權，並提供 Azure AD 和 Azure 資源的即時特殊許可權存取。

如果您的組織中沒有任何程式可管理許可權帳戶，或您目前有使用其一般使用者帳戶來管理服務和資源的系統管理員，您應該立即開始使用不同的帳戶，例如一個用於週期性日常活動;另一個用於特殊許可權存取，並使用 MFA 進行設定。 更棒的是，如果您的組織有 Azure AD Premium P2 訂用帳戶，您應該立即部署[Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure#license-requirements) （PIM）。 在相同的權杖中，您也應該檢查這些特殊許可權帳戶，並[指派較少許可權的角色](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure)（如果適用的話）。

應實行的特殊許可權帳戶管理的另一個層面是定義這些帳戶的[存取權審查](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)，不論是[透過 PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-perform-security-review)手動或自動化。

#### <a name="privileged-account-management-recommended-reading"></a>特殊許可權帳戶管理建議閱讀

- [Azure AD Privileged Identity Management 中的角色](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-roles)

### <a name="emergency-access-accounts"></a>緊急存取帳戶

組織必須建立[緊急帳戶](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)，以準備管理 Azure AD，例如像是驗證中斷的情況，例如：

- 驗證基礎結構的中斷元件（AD FS、內部部署 AD、MFA 服務）
- 系統管理人員流失

為了避免因為您無法以系統管理員身分登入或啟動現有個別使用者的帳戶，而不小心鎖定您的租使用者，您應該建立兩個或多個緊急帳戶，並確保其已實行並配合[Microsoft 的最佳作法](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure)和[中斷玻璃程式](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#break-glass-what-to-do-in-an-emergency)。

### <a name="privileged-access-to-azure-ea-portal"></a>Azure EA 入口網站的特殊許可權存取

[Azure Enterprise 合約（AZURE EA）入口網站](https://azure.microsoft.com/blog/create-enterprise-subscription-experience-in-azure-portal-public-preview/)可讓您針對主要 Enterprise 合約建立 Azure 訂用帳戶，這是企業內的強大角色。 建立此入口網站之前，通常會先將其啟動，然後再取得 Azure AD，因此必須使用 Azure AD 身分識別將其鎖定，從入口網站移除個人帳戶，確保適當的委派已就緒，並降低鎖定的風險。

若要清楚來說，如果 EA 入口網站授權層級目前設定為「混合模式」，您必須從 EA 入口網站中的所有特殊許可權存取移除任何[Microsoft 帳戶](https://support.skype.com/en/faq/FA12059/what-is-a-microsoft-account)，並將 ea 入口網站設定為僅使用 Azure AD 帳戶。 如果未設定 EA 入口網站委派的角色，您也應該尋找並執行部門和帳戶的委派角色。

#### <a name="privileged-access-recommended-reading"></a>建議閱讀的特殊許可權存取

- [Azure Active Directory 中的系統管理員角色權限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

## <a name="entitlement-management"></a>權利管理

[權利管理（EM）](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview)可讓應用程式擁有者將資源組合在一起，並將其指派給組織中的特定角色（內部和外部）。 EM 允許對企業擁有者進行自助式註冊和委派，同時保留治理原則來授與存取權、設定存取持續時間，以及允許核准工作流程。 

> [!NOTE]
> Azure AD 權利管理需要 Azure AD Premium P2 授權。

## <a name="summary"></a>摘要

安全身分識別管理有八個層面。 這份清單可協助您找出您應該採取的動作，以評估和證明授與對非特殊許可權和特殊許可權身分識別的存取權、audit 及控制環境的變更。

- 將擁有者指派給主要工作。
- 實行測試策略。
- 使用 Azure AD 存取審查，有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。
- 針對所有類型的外部身分識別和應用程式存取，建立一般的自動化存取審查程式。
- 建立存取權審查程式來定期審查和管理系統管理員存取權，並提供 Azure AD 和 Azure 資源的即時特殊許可權存取。
- 布建緊急帳戶以準備管理 Azure AD 非預期的中斷。
- 鎖定 Azure EA 入口網站的存取權。
- 執行權利管理，以提供資源集合的受控存取權。

## <a name="next-steps"></a>後續步驟

開始使用[Azure AD 操作檢查和動作](active-directory-ops-guide-ops.md)。

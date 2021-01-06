---
title: Azure Active Directory 治理作業參考指南
description: 此操作參考指南說明保護治理管理所應採取的檢查和動作
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
ms.openlocfilehash: ba3ca140abe36a31ffa03422420ea537bbe4f39a
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935745"
---
# <a name="azure-active-directory-governance-operations-reference-guide"></a>Azure Active Directory 治理作業參考指南

[Azure AD 作業參考指南](active-directory-ops-guide-intro.md)的這一節說明您應採取的檢查和動作，以評估和證明存取權授與非特殊許可權和特殊許可權的身分識別、audit 及控制環境的變更。

> [!NOTE]
> 這些建議是從發行日期開始的最新狀態，但可能會隨著時間而改變。 當 Microsoft 產品和服務隨著時間演進時，組織應該持續評估其治理實務。

## <a name="key-operational-processes"></a>重要的操作流程

### <a name="assign-owners-to-key-tasks"></a>將擁有者指派給重要工作

管理 Azure Active Directory 需要持續執行重要的作業工作和進程，這可能不是首度發行專案的一部分。 您也必須設定這些工作來優化您的環境。 主要工作和其建議的擁有者包括：

| 工作 | 擁有者 |
| :- | :- |
| 封存 Azure AD SIEM 系統中的 audit 記錄 | InfoSec 作業小組 |
| 探索管理不符合規範的應用程式 | IAM 作業小組 |
| 定期檢查應用程式的存取權 | InfoSec 架構小組 |
| 定期審核外部身分識別的存取權 | InfoSec 架構小組 |
| 定期檢查具有特殊許可權角色的人員 | InfoSec 架構小組 |
| 定義安全性閘道以啟動特殊許可權角色 | InfoSec 架構小組 |
| 定期審核同意授權 | InfoSec 架構小組 |
| 根據組織中的員工設計應用程式和資源的目錄和存取套件 | 應用程式擁有者 |
| 定義安全性原則以指派使用者存取套件 | InfoSec 團隊 + 應用程式擁有者 |
| 如果原則包含核准工作流程，請定期審核工作流程核准 | 應用程式擁有者 |
| 使用存取權審核來檢查安全性原則中的例外狀況，例如條件式存取原則 | InfoSec 作業小組 |

當您複習清單時，可能會發現您需要為缺少擁有者的工作指派擁有者，或針對擁有者未與上述建議一致的工作，調整其擁有權。

#### <a name="owner-recommended-reading"></a>擁有者建議閱讀

- [在 Azure Active Directory 中指派系統管理員角色](../roles/permissions-reference.md)
- [Azure 中的治理](../../governance/index.yml)

### <a name="configuration-changes-testing"></a>設定變更測試

在測試時，有一些需要特殊考慮的變更，從簡單的技術（例如，在平行測試租使用者中推出使用者的目標子集）部署變更。 如果您尚未實行測試策略，您應該根據下表中的指導方針來定義測試方法：

| 案例| 建議 |
|-|-|
|將驗證類型從同盟變更為 PHS/PTA 或反之亦然| 使用 [分段推出](../hybrid/how-to-connect-staged-rollout.md) 來測試變更驗證類型的影響。|
| (CA) 原則或 Identity Protection 原則中推出新的條件式存取|建立新的條件式存取原則，並指派給測試使用者。|
|上架應用程式的測試環境|將應用程式新增到生產環境、從 [MyApps] 面板中隱藏它，然後將它指派給測試使用者，以在品質保證 (QA) 階段進行測試。|
|變更同步處理規則|使用目前在生產環境中的相同設定（也稱為「預備模式」）來執行測試 Azure AD Connect 中的變更，並分析 CSExport 結果。 如果滿意，請在準備就緒時切換至生產環境。|
|商標的變更|在不同的測試租使用者中進行測試。|
|推出新功能|如果功能支援向一組目標使用者推出，請找出試驗使用者並建立。例如，「自助式密碼重設」和「多重要素驗證」可以鎖定特定使用者或群組。|
|將應用程式從內部部署身分識別提供者轉換 (IdP) ，例如 Active Directory）來 Azure AD|如果應用程式支援多個 IdP 設定（例如 Salesforce），請在變更期間設定和測試 Azure AD (以防應用程式引進 HRD 頁面) 。 如果應用程式不支援多個 Idp，請在變更控制視窗和程式停機期間排程測試。|
|更新動態群組規則|使用新規則建立平行動態群組。 針對計算結果進行比較，例如，以相同的條件執行 PowerShell。<br>如果測試成功，請交換使用舊群組的地方 (是否可行) 。|
|遷移產品授權|請參閱 [Azure Active Directory 中的「變更授權群組中單一使用者的授權](../enterprise-users/licensing-groups-change-licenses.md)」。|
|變更 AD FS 的規則，例如授權、發行、MFA|使用群組宣告將目標設為使用者的子集。|
|變更 AD FS 驗證體驗或類似的全伺服器陣列變更|使用相同的主機名稱建立平行伺服器陣列、執行設定變更、使用主機檔案、NLB 路由規則或類似路由從用戶端進行測試。<br>如果目標平臺不支援主機檔案 (例如行動裝置) 、控制變更。|

## <a name="access-reviews"></a>存取權檢閱

### <a name="access-reviews-to-applications"></a>存取應用程式的評論

經過一段時間之後，使用者在將資源移到不同的小組和位置時，可能會累積其存取權。 資源擁有者務必定期檢查應用程式的存取權，並移除在使用者的整個生命週期中不再需要的許可權。 Azure AD [存取評論](../governance/access-reviews-overview.md) ，可讓組織有效地管理群組成員資格、企業應用程式的存取權，以及角色指派。 資源擁有者應定期審核使用者的存取權，以確保只有適當的人員才能繼續存取。 在理想的情況下，您應該考慮使用 Azure AD 存取權評論來進行這項工作。

![存取權檢閱啟動頁面](./media/active-directory-ops-guide/active-directory-ops-img15.png)

> [!NOTE]
> 與存取權評論互動的每個使用者都必須有付費的 Azure AD Premium P2 授權。

### <a name="access-reviews-to-external-identities"></a>存取外部身分識別的評論

在需要的時間內，請務必只將存取許可權制在所需資源的外部身分識別。 使用 Azure AD [存取權評論](../governance/access-reviews-overview.md)，為所有外部身分識別和應用程式存取建立週期性自動化存取權審核程式。 如果進程已經存在於內部部署環境，請考慮使用 Azure AD 存取權評論。 一旦應用程式淘汰或不再使用，請移除所有具有應用程式存取權的外部身分識別。

> [!NOTE]
> 與存取權評論互動的每個使用者都必須有付費的 Azure AD Premium P2 授權。

## <a name="privileged-account-management"></a>具有特殊權限的帳戶管理

### <a name="privileged-account-usage"></a>特殊許可權帳戶使用方式

駭客通常會以系統管理員帳戶和其他特殊許可權存取元素為目標，以快速取得敏感性資料和系統的存取權。 由於具有特殊許可權角色的使用者通常會累積一段時間，因此請務必定期審核並管理系統管理員存取權，並提供 Azure AD 和 Azure 資源的即時特殊許可權存取。

如果您的組織中沒有可管理特殊許可權帳戶的進程，或您目前有系統管理員使用他們的一般使用者帳戶來管理服務和資源，您應該立即開始使用不同的帳戶，例如一個用於一般日常活動;另一個用於特殊許可權存取，並設定了 MFA。 更好的是，如果您的組織有 Azure AD Premium P2 訂用帳戶，則您應該立即部署 [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md#license-requirements) (PIM) 。 在相同的權杖中，您也應該檢查這些特殊許可權帳戶，並且 [指派較少的特殊許可權角色](../roles/security-planning.md) （如果適用）。

應執行之特殊許可權帳戶管理的另一個層面是定義這些帳戶的 [存取權評論](../governance/access-reviews-overview.md) ，不論是 [透過 PIM](../privileged-identity-management/pim-how-to-perform-security-review.md)手動或自動進行。

#### <a name="privileged-account-management-recommended-reading"></a>建議閱讀的特殊許可權帳戶管理

- [Azure AD Privileged Identity Management 中的角色](../privileged-identity-management/pim-roles.md)

### <a name="emergency-access-accounts"></a>緊急存取帳戶

組織必須建立 [緊急帳戶](../roles/security-emergency-access.md) ，以準備管理 Azure AD，例如像這樣的驗證中斷：

- 驗證基礎結構的中斷元件 (AD FS、內部部署 AD、MFA 服務) 
- 系統管理人員營業額

若要避免因為您無法以系統管理員身分登入或啟動現有個別使用者的帳戶，而不小心鎖定您的租使用者，您應該建立兩個或更多的緊急帳戶，並確定其已實行並符合 [Microsoft 的最佳作法](../roles/security-planning.md) 並 [中斷玻璃程式](../roles/security-planning.md#break-glass-what-to-do-in-an-emergency)。

### <a name="privileged-access-to-azure-ea-portal"></a>Azure EA 入口網站的特殊許可權存取

[Azure Enterprise 合約 (AZURE EA) 入口網站](https://azure.microsoft.com/blog/create-enterprise-subscription-experience-in-azure-portal-public-preview/)可讓您針對主要 Enterprise 合約建立 azure 訂用帳戶，這是企業內的強大角色。 先啟動建立此入口網站的程式通常是在取得 Azure AD 之前，您必須使用 Azure AD 身分識別將其鎖定、從入口網站移除個人帳戶、確保適當的委派已就緒，並減輕鎖定的風險。

明確地來說，如果 EA 入口網站授權層級目前設定為「混合模式」，您必須從 EA 入口網站中的所有特殊許可權存取移除任何 [Microsoft 帳戶](https://support.skype.com/en/faq/FA12059/what-is-a-microsoft-account) ，並設定 ea 入口網站僅使用 Azure AD 帳戶。 如果未設定 EA 入口網站委派的角色，您也應該尋找並執行部門與帳戶的委派角色。

#### <a name="privileged-access-recommended-reading"></a>建議閱讀的特殊許可權存取

- [Azure Active Directory 中的系統管理員角色權限](../roles/permissions-reference.md)

## <a name="entitlement-management"></a>權利管理

[權利管理 (EM) ](../governance/entitlement-management-overview.md) 可讓應用程式擁有者將資源配套，並將其指派給組織內的特定角色 (內部和外部) 。 EM 允許對企業擁有者進行自助式註冊和委派，同時保留治理原則來授與存取權、設定存取持續時間，以及允許核准工作流程。 

> [!NOTE]
> Azure AD 權利管理需要 Azure AD Premium P2 授權。

## <a name="summary"></a>摘要

安全身分識別治理有八個層面。 這份清單將協助您找出評估與證明授與非特殊許可權和特殊許可權身分識別、audit 及控制環境變更的存取權時，所應採取的動作。

- 將擁有者指派給主要工作。
- 實行測試策略。
- 使用 Azure AD 存取評論，有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。
- 針對所有類型的外部身分識別和應用程式存取，建立週期性自動化存取權審核程式。
- 建立存取權審核程式以定期審核及管理系統管理員存取權，並提供 Azure AD 和 Azure 資源的即時特殊許可權存取。
- 布建緊急帳戶以準備管理 Azure AD 非預期的中斷。
- 鎖定 Azure EA 入口網站的存取權。
- 實行權利管理，以提供資源集合的控管存取。

## <a name="next-steps"></a>後續步驟

開始使用 [Azure AD 操作檢查和動作](active-directory-ops-guide-ops.md)。
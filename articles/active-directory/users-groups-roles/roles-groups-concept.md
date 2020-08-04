---
title: 使用雲端群組來管理 Azure Active Directory 中的角色指派 |Microsoft Docs
description: 預覽用來委派身分識別管理的自訂 Azure AD 角色。 在 Azure 入口網站、PowerShell 或圖形 API 中管理 Azure 角色。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68d40e9dbc2c4da6b298f7245318c95de983edcc
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87530141"
---
# <a name="use-cloud-groups-to-manage-role-assignments-in-azure-active-directory-preview"></a>在 Azure Active Directory 中使用雲端群組來管理角色指派（預覽）

Azure Active Directory （Azure AD）引進公開預覽，您可以在其中指派雲端群組來 Azure AD 內建角色。 有了這項功能，您就可以使用群組在 Azure AD 中授與系統管理員存取權，而不受您的全域和特殊許可權角色管理員的工作。

請考慮下列範例： Contoso 已雇用跨地理位置的人員來管理和重設其 Azure AD 組織中員工的密碼。 他們不會要求特殊許可權角色管理員或全域管理員將技術服務管理員角色個別指派給每個人，而是可以建立 Contoso_Helpdesk_Administrators 群組，並將其指派給該角色。 當人員加入群組時，會間接指派角色給他們。 您現有的治理工作流程可以處理核准程式並審核群組的成員資格，以確保只有合法的使用者是群組的成員，因此會指派給技術服務管理員角色。

## <a name="how-this-feature-works"></a>這項功能的運作方式

建立新的 Office 365 或安全性群組，並將 ' isAssignableToRole ' 屬性設定為 ' true '。 在 Azure 入口網站中建立群組時，您也可以啟用此屬性，方法是開啟**Azure AD 角色可指派給群組**。 不論是哪一種方式，您都可以將群組指派給一個或多個 Azure AD 角色，就像將角色指派給使用者一樣。 最多可以在單一 Azure AD 組織（租使用者）中建立200角色可指派的群組。

如果您不想讓群組的成員擁有角色的存取權，您可以使用 Azure AD Privileged Identity Management。 將群組指派為 Azure AD 角色的合格成員。 然後，群組的每個成員都有資格為指派給該群組的角色啟用其指派。 然後，他們可以在固定時間內啟用其角色指派。

> [!Note]
> 您必須是 Privileged Identity Management 的更新版本，才能透過 PIM 將群組指派給 Azure AD 角色。 因為您的 Azure AD 組織會利用 Privileged Identity Management API，所以您可以在較舊版本的 PIM 上。 請聯繫別名 pim_preview@microsoft.com 以移動您的組織並更新您的 API。 若要深入瞭解，請[AZURE AD PIM 中的角色和功能](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-ad-roles-features)。

## <a name="why-we-enforce-creation-of-a-special-group-for-assigning-it-to-a-role"></a>為什麼我們會強制建立特殊群組來將它指派給角色

如果將角色指派給群組，任何可管理群組成員資格的 IT 系統管理員也可以間接管理該角色的成員資格。 例如，假設群組 Contoso_User_Administrators 指派給使用者帳戶管理員角色。 可以修改群組成員資格的 Exchange 系統管理員可能會將自己新增至 Contoso_User_Administrators 群組，如此一來，就會成為使用者帳戶管理員。如您所見，系統管理員可以使用您不想要的方式來提升其許可權。

Azure AD 可讓您使用名為 isAssignableToRole 的新屬性來保護指派給角色的群組。 只有在建立時將 isAssignableToRole 屬性設定為 ' true ' 的雲端群組，才能指派給角色。 這個屬性是不可變的;一旦建立群組，並將此屬性設定為 ' true '，就無法變更。 您無法在現有的群組上設定屬性。
我們設計了如何將群組指派給角色，以避免發生這類可能的缺口：

- 只有全域管理員和特殊許可權角色管理員可以建立可指派角色的群組（已啟用 "isAssignableToRole" 屬性）。
- 它不能是 Azure AD 動態群組;也就是，它的成員資格類型必須是「已指派」。 自動擴展動態群組可能會導致不想要的帳戶新增至群組，因此會指派給該角色。
- 根據預設，只有全域管理員和特殊許可權角色管理員可以管理角色可指派群組的成員資格，但是您可以藉由新增群組擁有者來委派角色可指派群組的管理。
- 為避免權限提高，可指派給角色的群組之成員和擁有者的認證只能由有許可權的驗證管理員或全域管理員變更。
- 沒有任何嵌套。 群組無法新增為角色可指派群組的成員。

## <a name="limitations"></a>限制

目前不支援下列案例：  

- 將雲端群組指派給 Azure AD 的自訂角色
- 透過管理單位將雲端群組指派給 Azure AD 角色（內建或自訂）。
- 將內部部署群組指派給 Azure AD 角色（內建或自訂）

## <a name="known-issues"></a>已知問題

- 透過群組指派角色時，您無法建立或修改動態群組。
- 「**啟用受控使用者登入的分段推出**」功能不支援透過群組進行指派。
- *僅 Azure AD P2 授權客戶*：不要透過 Azure AD 和 Privileged Identity Management，將群組指派為作用中的角色。 這會導致使用者無法在 PIM 中看到其作用中角色指派，以及無法移除該 PIM 指派的問題。 符合資格的指派在此案例中不會受到影響。 如果您嘗試進行這種指派，可能會看到非預期的行為，例如：
  - 角色指派的結束時間可能會不正確地顯示。
  - 在 PIM 入口網站中，**我的角色**只會顯示一個角色指派，而不論授與指派的方法有多少（透過一或多個群組，以及直接執行）。
- *僅 Azure AD P2 授權客戶*即使在刪除群組之後，仍會在 PIM UI 中顯示為該角色的合格成員。 功能沒問題，這只是 Azure 入口網站中的快取問題。  
- Exchange 系統管理中心尚未透過群組辨識角色成員資格，但 PowerShell Cmdlet 會正常執行。
- Azure 資訊保護入口網站（傳統入口網站）尚未透過群組辨識角色成員資格。 您可以[遷移至統一的敏感度標籤平臺](https://docs.microsoft.com/azure/information-protection/configure-policy-migrate-labels)，然後使用 Office 365 安全性 & 合規性中心來使用群組指派來管理角色。

我們正在修正這些問題。

## <a name="required-license-plan"></a>必要授權方案

若要使用這項功能，您必須在 Azure AD 組織中擁有可用的 Azure AD Premium P1 授權。 若要同時使用 Privileged Identity Management 以進行即時角色啟用，您必須擁有可用的 Azure AD Premium P2 授權。 若要尋找您需求的正確授權，請參閱[比較免費和 Premium 方案的正式推出功能](../fundamentals/active-directory-whatis.md#what-are-the-azure-ad-licenses)。

## <a name="next-steps"></a>後續步驟

- [建立可指派角色的群組](roles-groups-create-eligible.md)
- [將角色指派給角色可指派的群組](roles-groups-assign-role.md)

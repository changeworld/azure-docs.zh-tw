---
title: 使用雲端群組來管理 Azure Active Directory 中的角色指派 |Microsoft Docs
description: 預覽用來委派身分識別管理的自訂 Azure AD 角色。 管理 Azure 入口網站、PowerShell 或圖形 API 中的 Azure 角色指派。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54988c8bbc4a9c3d448ac35f31e97e2d20228209
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007563"
---
# <a name="use-cloud-groups-to-manage-role-assignments-in-azure-active-directory-preview"></a>使用雲端群組來管理 Azure Active Directory (預覽中的角色指派) 

Azure Active Directory (Azure AD) 推出公開預覽，您可以在其中指派雲端群組以 Azure AD 內建角色。 利用這項功能，您可以使用群組來授與 Azure AD 中的系統管理員存取權，並與您的全域和特殊許可權角色管理員進行極少的工作

請考慮此範例： Contoso 已雇用跨地理位置的人員，來管理和重設其 Azure AD 組織中員工的密碼。 他們不會要求特殊許可權角色管理員或全域系統管理員個別將技術服務系統管理員角色指派給每個人，而是可以建立 Contoso_Helpdesk_Administrators 群組，並將其指派給角色。 當人員加入群組時，會間接將角色指派給他們。 然後，您現有的治理工作流程就可以處理核准程式和審核群組的成員資格，以確保只有合法的使用者是群組的成員，因此會指派給技術支援管理員角色。

## <a name="how-this-feature-works"></a>這項功能的運作方式

建立新的 Microsoft 365 或安全性群組，並將 ' isAssignableToRole ' 屬性設定為 ' true '。 您也可以藉由開啟 **Azure AD 角色指派給群組**，在 Azure 入口網站中建立群組時啟用此屬性。 無論何種方式，您都可以將群組指派給一或多個 Azure AD 角色，就像您將角色指派給使用者的方式一樣。 您可以在單一 Azure AD 組織 (租使用者) 中建立最多200個可指派角色的群組。

如果您不想讓群組成員擁有角色的持續存取權，您可以使用 Azure AD Privileged Identity Management。 將群組指派為 Azure AD 角色的合格成員。 然後，群組的每個成員都有資格針對指派給群組的角色啟用其指派。 然後，他們可以在固定的時間內啟動其角色指派。

> [!Note]
> 您必須在更新版的 Privileged Identity Management，才能透過 PIM 將群組指派給 Azure AD 角色。 因為您的 Azure AD 組織會利用 Privileged Identity Management API，所以您可以在較舊版本的 PIM 上。 請與別名聯繫 pim_preview@microsoft.com ，以移動您的組織並更新您的 API。 若要深入瞭解，請參閱 [PIM 中 Azure AD 角色和功能](../privileged-identity-management/azure-ad-roles-features.md)。

## <a name="why-we-enforce-creation-of-a-special-group-for-assigning-it-to-a-role"></a>為什麼我們會強制建立特殊群組以將它指派給角色

如果群組已獲指派角色，任何可以管理群組成員資格的 IT 系統管理員也可以間接管理該角色的成員資格。 例如，假設將群組 Contoso_User_Administrators 指派給使用者帳戶管理員角色。 可以修改群組成員資格的 Exchange 系統管理員可以將自己新增至 Contoso_User_Administrators 群組，如此一來，就會成為使用者帳戶管理員。如您所見，系統管理員可以用您不想要的方式提升其許可權。

Azure AD 可讓您使用名為 isAssignableToRole 的新屬性（稱為群組）來保護指派給角色的群組。 只有在建立時將 isAssignableToRole 屬性設定為 ' true ' 的雲端群組，才能指派給角色。 這個屬性是不可變的;一旦建立群組，並將此屬性設定為 ' true '，就無法變更。 您無法在現有的群組上設定屬性。
我們設計了如何將群組指派給角色，以避免發生這類潛在缺口：

- 只有全域管理員和特殊許可權角色管理員可以建立角色可指派的群組 (並) 啟用 "isAssignableToRole" 屬性。
- 它不能是 Azure AD 動態群組;也就是說，它必須具有「已指派」的成員資格類型。 動態群組的自動化擴展可能會導致不想要的帳戶新增至群組，因而指派給該角色。
- 依預設，只有全域管理員和特殊許可權角色管理員可以管理角色可指派群組的成員資格，但您可以藉由新增群組擁有者來委派角色可指派群組的管理。
- 若要防止權限提高，只能由特殊許可權驗證管理員或全域管理員變更角色可指派群組的成員和擁有者的認證。
- 無嵌套。 無法將群組新增為角色可指派群組的成員。

## <a name="limitations"></a>限制

目前不支援下列案例：  

- 將內部部署群組指派給 (內建或自訂) 的 Azure AD 角色

## <a name="known-issues"></a>已知問題

- **啟用受管理使用者登入功能的分段推出** 不支援透過群組進行指派。
- *僅 Azure AD P2 授權客戶*：不要透過 Azure AD 和 PRIVILEGED IDENTITY MANAGEMENT (PIM) ，將群組指派為使用中角色。 具體而言，在建立角色時，請勿將角色指派給角色可指派的群組， *並* 在稍後使用 PIM 將角色指派給群組。 這會導致使用者無法在 PIM 中看到其作用中角色指派，以及無法移除該 PIM 指派的問題。 符合資格的指派在此案例中不會受到影響。 如果您嘗試進行此指派，您可能會看到非預期的行為，例如：
  - 角色指派的結束時間可能會不正確地顯示。
  - 在 PIM 入口網站中，[ **我的角色** ] 只會顯示一個角色指派，不論指派 (透過一或多個群組授與的方法數目，以及直接) 。
- *僅 Azure AD P2 授權客戶* 即使在刪除群組之後，它仍會在 PIM UI 中顯示為該角色的合格成員。 功能沒問題;這只是 Azure 入口網站的快取問題。  
- 使用新的 [Exchange 系統管理中心](https://admin.exchange.microsoft.com/) 透過群組成員資格指派角色。 舊的 Exchange 系統管理中心尚未支援這項功能。 Exchange PowerShell Cmdlet 將會如預期般運作。
- Azure 資訊保護入口網站 (傳統入口網站) 無法透過群組辨識角色成員資格。 您可以 [遷移至統一的敏感度標籤平臺](/azure/information-protection/configure-policy-migrate-labels) ，然後使用 Office 365 安全性 & 合規性中心來使用群組指派來管理角色。

我們正在修正這些問題。

## <a name="required-license-plan"></a>必要授權方案

使用此功能時，您的 Azure AD 組織中必須有可用的 Azure AD Premium P1 授權。 若要同時使用 Privileged Identity Management 進行即時角色啟用，您必須擁有可用的 Azure AD Premium P2 授權。 若要尋找您需求的正確授權，請參閱 [比較免費和 Premium 方案的正式運作功能](../fundamentals/active-directory-whatis.md#what-are-the-azure-ad-licenses)。

## <a name="next-steps"></a>後續步驟

- [建立可指派角色的群組](groups-create-eligible.md)
- [將角色指派給角色可指派的群組](groups-assign-role.md)

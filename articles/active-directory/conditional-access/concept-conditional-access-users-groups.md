---
title: 條件式存取原則中的使用者和群組-Azure Active Directory
description: Azure AD 條件式存取原則中的使用者和群組是誰
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: d14c9330977296630ee58bc2b508f4304472044c
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92366355"
---
# <a name="conditional-access-users-and-groups"></a>條件式存取：使用者和群組

條件式存取原則必須包含使用者指派作為決策流程中的其中一個信號。 您可以在條件式存取原則中包含或排除使用者。 Azure Active Directory 會評估所有原則，並確保在授與使用者存取權之前符合所有需求。

![使用者作為條件式存取所做的決策中的信號](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups.png)

## <a name="include-users"></a>包含使用者

這份使用者清單通常包含組織以條件式存取原則為目標的所有使用者。 

建立條件式存取原則時，可包含下列選項。

- 無
   - 未選取任何使用者
- 所有使用者
   - 存在於目錄中的所有使用者，包括 B2B 來賓。
- 選取使用者和群組
   - 所有來賓和外部使用者
      - 此選項包括任何 B2B 來賓和外部使用者，包括 `user type` 屬性設定為的任何使用者 `guest` 。 這項選擇也適用于從不同組織登入的任何外部使用者，例如雲端解決方案提供者 (CSP) 。 
   - 目錄角色
      - 可讓系統管理員選取用來判斷指派的特定 Azure AD 目錄角色。 例如，組織可能會在獲指派全域管理員角色的使用者上建立更嚴格的原則。
   - 使用者和群組
      - 允許特定使用者集合的目標。 例如，當將 HR 應用程式選取為雲端應用程式時，組織可以選取包含 HR 部門所有成員的群組。 群組可以是 Azure AD 中任何類型的群組，包括動態或已指派的安全性與通訊群組。 原則會套用到嵌套的使用者和群組。

> [!WARNING]
> 如果使用者或群組是超過2048個群組的成員，其存取權可能會遭到封鎖。 這項限制適用于直接和嵌套群組成員資格。

> [!WARNING]
> 條件式存取原則不支援使用者將目錄角色指派給範圍直接指向物件的 [管理單位](../roles/admin-units-assign-roles.md) 或目錄角色，例如透過 [自訂角色](../roles/custom-create.md)。

## <a name="exclude-users"></a>排除使用者

當組織包含和排除使用者或群組時，系統會將使用者或群組從原則中排除，因為排除動作會覆寫包含在原則中。 排除專案通常用於緊急存取或半透明的帳戶。 如需緊急存取帳戶的詳細資訊，以及其重要性，請參閱下列文章： 

* [在 Azure AD 中管理緊急存取帳戶](../roles/security-emergency-access.md)
* [使用 Azure Active Directory 來建立具彈性的存取控制管理策略](../authentication/concept-resilient-controls.md)

建立條件式存取原則時，可以使用下列選項來排除。

- 所有來賓和外部使用者
   - 此選項包括任何 B2B 來賓和外部使用者，包括 `user type` 屬性設定為的任何使用者 `guest` 。 這項選擇也適用于從不同組織登入的任何外部使用者，例如雲端解決方案提供者 (CSP) 。 
- 目錄角色
   - 可讓系統管理員選取用來判斷指派的特定 Azure AD 目錄角色。 例如，組織可能會在獲指派全域管理員角色的使用者上建立更嚴格的原則。
- 使用者和群組
   - 允許特定使用者集合的目標。 例如，當將 HR 應用程式選取為雲端應用程式時，組織可以選取包含 HR 部門所有成員的群組。 群組可以是 Azure AD 中任何類型的群組，包括動態或已指派的安全性與通訊群組。

### <a name="preventing-administrator-lockout"></a>防止系統管理員鎖定

若要在建立套用至 **所有使用者** 和 **所有應用程式**的原則時，防止系統管理員鎖定其目錄，他們將會看到下列警告。

> 別鎖住！ 建議您先將原則套用至一小組使用者，以確認其行為如預期般運作。 我們也建議您從此原則排除至少一個系統管理員。 這可確保您仍然具有存取權，並可在需要變更時更新原則。 請檢查受影響的使用者和應用程式。

原則預設會提供選項，以從原則中排除目前的使用者，但系統管理員可以覆寫此預設值，如下圖所示。 

![警告，不要鎖定自己！](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups-lockout-warning.png)

[如果您被鎖定 Azure 入口網站，該怎麼辦？](troubleshoot-conditional-access.md#what-to-do-if-you-are-locked-out-of-the-azure-portal)

## <a name="next-steps"></a>後續步驟

- [條件式存取：雲端應用程式或動作](concept-conditional-access-cloud-apps.md)

- [條件式存取一般原則](concept-conditional-access-policy-common.md)

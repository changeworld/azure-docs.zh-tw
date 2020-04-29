---
title: 條件式存取原則中的使用者和群組-Azure Active Directory
description: Azure AD 條件式存取原則中的使用者和群組
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17312e44714c8bdb20e22ad9aeb950e46eb71e3e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80755278"
---
# <a name="conditional-access-users-and-groups"></a>條件式存取：使用者和群組

條件式存取原則必須包含使用者指派，做為決策程式中的其中一個信號。 可以在條件式存取原則中包含或排除使用者。 

![條件式存取所做出的決策中的使用者做為信號](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups.png)

## <a name="include-users"></a>包含使用者

此使用者清單通常包含組織在條件式存取原則中的目標所有使用者。 

建立條件式存取原則時，可以包含下列選項。

- 無
   - 未選取任何使用者
- 所有使用者
   - 存在於目錄中的所有使用者，包括 B2B 來賓。
- 選取使用者和群組
   - 所有來賓和外部使用者
      - 此選項包括任何 B2B 來賓和外部使用者，包括將屬性設定`user type`為`guest`的任何使用者。 此選項也適用于從不同組織（例如雲端解決方案提供者（CSP））登入的任何外部使用者。 
   - 目錄角色
      - 可讓系統管理員選取用來判斷指派的特定 Azure AD 目錄角色。 例如，組織可能會在指派全域管理員角色的使用者上建立限制更嚴格的原則。
   - 使用者和群組
      - 允許特定使用者集合的目標。 例如，組織可以選取一個群組，其中包含 hr 部門的所有成員（在選取 HR 應用程式作為雲端應用程式時）。 群組可以是 Azure AD 中任何類型的群組，包括動態或已指派的安全性與通訊群組。

## <a name="exclude-users"></a>排除使用者

當組織包含和排除使用者或群組時，會從原則中排除使用者或群組，因為排除動作會覆寫原則中的 include。 排除專案通常用於緊急存取或中斷玻璃帳戶。 如需緊急存取帳戶和其重要性的詳細資訊，請參閱下列文章： 

* [在 Azure AD 中管理緊急存取帳戶](../users-groups-roles/directory-emergency-access.md)
* [使用 Azure Active Directory 來建立具彈性的存取控制管理策略](../authentication/concept-resilient-controls.md)

建立條件式存取原則時，有下列選項可供排除。

- 所有來賓和外部使用者
   - 此選項包括任何 B2B 來賓和外部使用者，包括將屬性設定`user type`為`guest`的任何使用者。 此選項也適用于從不同組織（例如雲端解決方案提供者（CSP））登入的任何外部使用者。 
- 目錄角色
   - 可讓系統管理員選取用來判斷指派的特定 Azure AD 目錄角色。 例如，組織可能會在指派全域管理員角色的使用者上建立限制更嚴格的原則。
- 使用者和群組
   - 允許特定使用者集合的目標。 例如，組織可以選取一個群組，其中包含 hr 部門的所有成員（在選取 HR 應用程式作為雲端應用程式時）。 群組可以是 Azure AD 中任何類型的群組，包括動態或已指派的安全性與通訊群組。

### <a name="preventing-administrator-lockout"></a>防止系統管理員鎖定

若要防止系統管理員在建立套用至**所有使用者**和**所有應用程式**的原則時，自行鎖定其目錄，他們會看到下列警告。

> 不要將自己鎖在外！ 我們建議您先將原則套用至一小組使用者，以確認其行為如預期般運作。 我們也建議您至少排除此原則中的一位系統管理員。 這可確保您仍然擁有存取權，並可在需要變更時更新原則。 請檢查受影響的使用者和應用程式。

根據預設，原則會提供一個選項，從原則中排除目前的使用者，但系統管理員可以覆寫此預設值，如下圖所示。 

![警告，請勿將自己鎖在外！](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups-lockout-warning.png)

## <a name="next-steps"></a>後續步驟

- [條件式存取：雲端應用程式或動作](concept-conditional-access-cloud-apps.md)

- [條件式存取的一般原則](concept-conditional-access-policy-common.md)

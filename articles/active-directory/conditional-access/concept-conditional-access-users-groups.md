---
title: 條件存取原則中的使用者與群組 - Azure 的動作目錄
description: Azure AD 條件存取原則的使用者和群組是誰
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43ae866959dd2112bacbb6b56e5683e7b3b851a0
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631932"
---
# <a name="conditional-access-users-and-groups"></a>條件存取:使用者和群組

條件訪問策略必須將使用者分配作為決策過程中的信號之一。 用戶可以包含在條件訪問策略中或排除。 

![條件存取決策中使用者作為訊號](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups.png)

## <a name="include-users"></a>包括使用者

此使用者清單通常包括組織在條件訪問策略中定位的所有使用者。 

創建條件訪問策略時,可以使用以下選項進行包括。

- None
   - 沒有選擇任何使用者
- 所有使用者
   - 目錄中存在的所有使用者,包括 B2B 來賓。
- 選取使用者和群組
   - 所有訪客和外部使用者(預覽版)
      - 此選項包括任何 B2B 來賓和外部使用者`user type`,包括`guest`屬性設置為的任何使用者。 此選項也適用於來自其他組織(如雲端解決方案供應商 (CSP) 的任何外部使用者登錄。 
   - 目錄角色(預覽)
      - 允許管理員選擇用於確定分配的特定 Azure AD 目錄角色。 例如,組織可能會對分配全域管理員角色的用戶創建更嚴格的策略。
   - 使用者和群組
      - 允許定位特定使用者集。 例如,當將HR應用選擇為雲應用時,組織可以選擇包含人力資源部門所有成員的組。 群組可以是 Azure AD 中任何類型的群組，包括動態或已指派的安全性與通訊群組。

## <a name="exclude-users"></a>排除使用者

當組織同時包括和排除使用者或組時,使用者或組將從策略中排除,因為排除操作將覆蓋策略中的包含。 排除通常用於緊急訪問或碎玻璃帳戶。 有關緊急訪問帳戶及其重要性的詳細資訊,請參閱以下文章: 

* [在 Azure AD 中管理緊急存取帳戶](../users-groups-roles/directory-emergency-access.md)
* [使用 Azure Active Directory 來建立具彈性的存取控制管理策略](../authentication/concept-resilient-controls.md)

創建條件訪問策略時,可以使用以下選項進行排除。

- 所有訪客和外部使用者(預覽版)
   - 此選項包括任何 B2B 來賓和外部使用者`user type`,包括`guest`屬性設置為的任何使用者。 此選項也適用於來自其他組織(如雲端解決方案供應商 (CSP) 的任何外部使用者登錄。 
- 目錄角色(預覽)
   - 允許管理員選擇用於確定分配的特定 Azure AD 目錄角色。 例如,組織可能會對分配全域管理員角色的用戶創建更嚴格的策略。
- 使用者和群組
   - 允許定位特定使用者集。 例如,當將HR應用選擇為雲應用時,組織可以選擇包含人力資源部門所有成員的組。 群組可以是 Azure AD 中任何類型的群組，包括動態或已指派的安全性與通訊群組。

### <a name="preventing-administrator-lockout"></a>防止管理員鎖定

為了防止管理員在創建應用於**所有使用者****和所有應用**的策略時將自己鎖定在目錄中,他們將看到以下警告。

> 別把自己鎖起來! 我們建議首先將策略應用於一小群使用者,以驗證其按預期執行。 我們還建議將至少一個管理員排除在此策略之外。 這可確保您仍然具有訪問許可權,並且如果需要更改,可以更新策略。 請查看受影響的用戶和應用。

預設情況下,策略將提供一個選項,將當前使用者從策略中排除,但管理員可以覆蓋此預設值,如下圖所示。 

![警告,不要把自己鎖起來!](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups-lockout-warning.png)

## <a name="next-steps"></a>後續步驟

- [條件存取:雲應用或操作](concept-conditional-access-cloud-apps.md)

- [條件存取一般原則](concept-conditional-access-policy-common.md)

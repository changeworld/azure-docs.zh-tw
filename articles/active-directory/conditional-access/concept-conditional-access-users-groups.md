---
title: 條件訪問策略中的使用者和組 - Azure 活動目錄
description: Azure AD 條件訪問策略中的使用者和組是誰
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36898e75680771a9cb084fa142bb635ddbf51c70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77192125"
---
# <a name="conditional-access-users-and-groups"></a>條件訪問：使用者和組

條件訪問策略必須將使用者分配作為決策過程中的信號之一。 使用者可以包含在條件訪問策略中或排除。 

![條件訪問決策中使用者作為信號](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups.png)

## <a name="include-users"></a>包括使用者

此使用者清單通常包括組織在條件訪問策略中定位的所有使用者。 

創建條件訪問策略時，可以使用以下選項進行包括。

- None
   - 未選擇任何使用者
- 所有使用者
   - 目錄中存在的所有使用者，包括 B2B 來賓。
- 選取使用者和群組
   - 所有訪客和外部使用者（預覽版）
      - 此選項包括任何 B2B 來賓和外部使用者，包括`user type`屬性設置為`guest`的任何使用者。 此選項也適用于來自其他組織（如雲解決方案供應商 （CSP） 的任何外部使用者登錄。 
   - 目錄角色（預覽）
      - 允許管理員選擇用於確定分配的特定 Azure AD 目錄角色。 例如，組織可能會對分配全域管理員角色的使用者創建更嚴格的策略。
   - 使用者和群組
      - 允許定位特定使用者集。 例如，當將 HR 應用選擇為雲應用時，組織可以選擇包含人力資源部門所有成員的組。 群組可以是 Azure AD 中任何類型的群組，包括動態或已指派的安全性與通訊群組。

## <a name="exclude-users"></a>排除使用者

排除通常用於緊急訪問或碎玻璃帳戶。 有關緊急存取帳戶及其重要性的詳細資訊，請參閱以下文章： 

* [在 Azure AD 中管理緊急存取帳戶](../users-groups-roles/directory-emergency-access.md)
* [使用 Azure Active Directory 來建立具彈性的存取控制管理策略](../authentication/concept-resilient-controls.md)

創建條件訪問策略時，可以使用以下選項進行排除。

- 所有訪客和外部使用者（預覽版）
   - 此選項包括任何 B2B 來賓和外部使用者，包括`user type`屬性設置為`guest`的任何使用者。 此選項也適用于來自其他組織（如雲解決方案供應商 （CSP） 的任何外部使用者登錄。 
- 目錄角色（預覽）
   - 允許管理員選擇用於確定分配的特定 Azure AD 目錄角色。 例如，組織可能會對分配全域管理員角色的使用者創建更嚴格的策略。
- 使用者和群組
   - 允許定位特定使用者集。 例如，當將 HR 應用選擇為雲應用時，組織可以選擇包含人力資源部門所有成員的組。 群組可以是 Azure AD 中任何類型的群組，包括動態或已指派的安全性與通訊群組。

## <a name="next-steps"></a>後續步驟

- [條件訪問：雲應用或操作](concept-conditional-access-cloud-apps.md)

- [條件訪問通用策略](concept-conditional-access-policy-common.md)

---
title: 動態群組和 B2B 共同作業-Azure Active Directory |Microsoft Docs
description: 說明如何搭配 Azure Active Directory B2B 共同作業使用 Azure AD 動態群組
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41e8b81bc3594c6a378757636f70058510a38cc7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "78226873"
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>動態群組與 Azure Active Directory B2B 共同作業

## <a name="what-are-dynamic-groups"></a>什麼是動態群組？
[Azure 入口網站](https://portal.azure.com)允許您動態設定 Azure Active Directory (Azure AD) 的安全性群組成員資格。 系統管理員可以設定規則，以根據使用者屬性（例如 userType、部門或國家/地區）填入在 Azure AD 中建立的群組。 在安全性群組中，可以根據成員的屬性而自動新增或移除成員。 這些群組可以提供對應用程式或雲端資源 (SharePoint 網站和文件) 的存取，以及將授權指派給成員。 若要深入了解動態群組，請參閱 [Azure Active Directory 中的專用群組](../active-directory-accessmanagement-dedicated-groups.md)。

需要有適當的 [Azure AD Premium P1 或 P2 授權](https://azure.microsoft.com/pricing/details/active-directory/)，才能建立和使用動態群組。 請參閱[在 Azure Active Directory 中針對動態群組成員資格建立以屬性為基礎的規則](../users-groups-roles/groups-dynamic-membership.md)一文，以深入了解。

## <a name="creating-an-all-users-dynamic-group"></a>建立「所有使用者」動態群組
您建立的群組可以包含使用成員資格規則的租用戶內的所有使用者。 未來在租用戶中新增或移除使用者時，系統會自動調整群組的成員資格。

1. 使用在租使用者中獲指派全域管理員或使用者系統管理員角色的帳戶登入[Azure 入口網站](https://portal.azure.com)。
1. 選取 **Azure Active Directory**。
2. 在 [**管理**] 底下，選取 [**群組**]，然後選取 [**新增群組**]。
1. 在 [**新增群組**] 頁面的 [**群組類型**] 底下，選取 [**安全性**]。 為新群組輸入 [群組名稱]  與 [群組描述]  。 
2. 在 [**成員資格類型**] 底下，選取 [**動態使用者**]，然後選取 [**新增動態查詢**]。 
4. 在 [**規則語法**] 文字方塊的上方，選取 [**編輯**]。 在 [**編輯規則語法**] 頁面上，于文字方塊中輸入下列運算式：

   ```
   user.objectId -ne null
   ```
1. 選取 [確定]  。 規則會出現在 [規則語法] 方塊中：

   ![所有使用者動態群組的規則語法](media/use-dynamic-groups/all-user-rule-syntax.png)

1.  選取 [儲存]  。 新的動態群組現在會包含 B2B 來賓使用者和成員使用者。


1. 選取 [**新增群組**] 頁面上的 [**建立**] 來建立群組。

## <a name="creating-a-group-of-members-only"></a>僅建立成員群組

如果您想要讓群組排除來賓使用者，而且只包含租使用者的成員，請建立如上面所述的動態群組，但在 [**規則語法**] 方塊中，輸入下列運算式：

```
(user.objectId -ne null) and (user.userType -eq "Member")
```

下圖顯示已修改為僅包含成員和排除來賓之動態群組的規則語法。

![顯示使用者類型 equals 成員的規則](media/use-dynamic-groups/all-member-user-rule-syntax.png)

## <a name="creating-a-group-of-guests-only"></a>僅建立來賓群組

您可能也會發現，建立一個只包含來賓使用者的新動態群組很有用，這樣可讓您對他們套用原則 (例如 Azure AD 條件式存取原則)。 如上面所述建立動態群組，但在 [**規則語法**] 方塊中，輸入下列運算式：

```
(user.objectId -ne null) and (user.userType -eq "Guest")
```

下圖顯示已修改為僅包含來賓和排除成員使用者之動態群組的規則語法。

![顯示使用者類型等於來賓的規則](media/use-dynamic-groups/all-guest-user-rule-syntax.png)

## <a name="next-steps"></a>後續步驟

- [B2B 共同作業使用者屬性](user-properties.md)
- [將 B2B 共同作業使用者新增至角色](add-guest-to-role.md)
- [適用于 B2B 共同作業使用者的條件式存取](conditional-access.md)


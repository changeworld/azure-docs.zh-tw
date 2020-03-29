---
title: 動態組和 B2B 協作 - Azure 活動目錄 |微軟文檔
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78226873"
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>動態群組與 Azure Active Directory B2B 共同作業

## <a name="what-are-dynamic-groups"></a>什麼是動態群組？
[Azure 入口網站](https://portal.azure.com)允許您動態設定 Azure Active Directory (Azure AD) 的安全性群組成員資格。 管理員可以設置規則以填充基於使用者屬性（如使用者類型、部門或國家/地區）在 Azure AD 中創建的組。 在安全性群組中，可以根據成員的屬性而自動新增或移除成員。 這些群組可以提供對應用程式或雲端資源 (SharePoint 網站和文件) 的存取，以及將授權指派給成員。 若要深入了解動態群組，請參閱 [Azure Active Directory 中的專用群組](../active-directory-accessmanagement-dedicated-groups.md)。

需要有適當的 [Azure AD Premium P1 或 P2 授權](https://azure.microsoft.com/pricing/details/active-directory/)，才能建立和使用動態群組。 請參閱[在 Azure Active Directory 中針對動態群組成員資格建立以屬性為基礎的規則](../users-groups-roles/groups-dynamic-membership.md)一文，以深入了解。

## <a name="creating-an-all-users-dynamic-group"></a>創建"所有使用者"動態組
您建立的群組可以包含使用成員資格規則的租用戶內的所有使用者。 未來在租用戶中新增或移除使用者時，系統會自動調整群組的成員資格。

1. 使用在租戶中分配了全域管理員或使用者管理員角色的帳戶登錄到[Azure 門戶](https://portal.azure.com)。
1. 選擇**Azure 活動目錄**。
2. 在 **"管理**"下，選擇 **"組**"，然後選擇 **"新群組**"。
1. 在新**組**頁上，在 **"組類型"** 下，選擇 **"安全**"。 為新群組輸入 [群組名稱]**** 與 [群組描述]****。 
2. 在 **"成員"類型**下，選擇 **"動態使用者**"，然後選擇 **"添加動態查詢**"。 
4. 在 **"規則語法**"文字方塊上方，選擇 **"編輯**"。 在 **"編輯規則語法**"頁上，在文字方塊中鍵入以下運算式：

   ```
   user.objectId -ne null
   ```
1. 選取 [確定]****。 規則將顯示在"規則"語法框中：

   ![所有使用者動態組的規則語法](media/use-dynamic-groups/all-user-rule-syntax.png)

1.  選取 [儲存]****。 新的動態組現在將包括 B2B 來賓使用者以及成員使用者。


1. 在 **"新建"組**頁上選擇 **"創建**"以創建組。

## <a name="creating-a-group-of-members-only"></a>僅創建成員組

如果希望組排除來賓使用者，並且僅包括租戶的成員，請按照上述創建動態組，但在 **"規則"語法**框中，輸入以下運算式：

```
(user.objectId -ne null) and (user.userType -eq "Member")
```

下圖顯示了經過修改的動態組的規則語法，該組僅包括成員並排除來賓。

![顯示使用者類型等於成員的規則](media/use-dynamic-groups/all-member-user-rule-syntax.png)

## <a name="creating-a-group-of-guests-only"></a>僅創建一組來賓

您可能也會發現，建立一個只包含來賓使用者的新動態群組很有用，這樣可讓您對他們套用原則 (例如 Azure AD 條件式存取原則)。 按照上述身份創建動態組，但在 **"規則"語法**框中，輸入以下運算式：

```
(user.objectId -ne null) and (user.userType -eq "Guest")
```

下圖顯示了經過修改的動態組的規則語法，該組僅包括來賓並排除成員使用者。

![顯示使用者類型等於來賓的規則](media/use-dynamic-groups/all-guest-user-rule-syntax.png)

## <a name="next-steps"></a>後續步驟

- [B2B 共同作業使用者屬性](user-properties.md)
- [將 B2B 共同作業使用者新增至角色](add-guest-to-role.md)
- [B2B 協作使用者的條件訪問](conditional-access.md)


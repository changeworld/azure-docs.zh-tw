---
title: 在 Azure AD 授權管理中查看、添加和刪除訪問包的分配 - Azure 活動目錄
description: 瞭解如何在 Azure 活動目錄授權管理中查看、添加和刪除訪問包的分配。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5a2107974cd63c0d02aaeb555430453c39990bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262018"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 授權管理中查看、添加和刪除訪問包的分配

在 Azure AD 授權管理中，您可以看到已分配到訪問包的人員、策略和狀態。 如果訪問包具有適當的策略，也可以直接將使用者分配給訪問包。 本文介紹如何查看、添加和刪除訪問包的分配。

## <a name="view-who-has-an-assignment"></a>查看具有分配的人員

**先決條件角色：** 全域管理員、使用者管理員、目錄擁有者或訪問包管理器

1. 在 Azure 入口網站中按一下 [Azure Active Directory]****，然後按一下 [身分識別治理]****。

1. 在左側功能表中，按一下 **"訪問包**"，然後打開訪問包。

1. 按一下 **"分配"** 以查看活動分配的清單。

    ![訪問包的分配清單](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. 按一下特定分配以查看其他詳細資訊。

1. 要查看未正確預配所有資源角色的分配清單，請按一下篩選器狀態並選擇"**傳遞**"。

    通過在 **"請求"** 頁上查找使用者的相應請求，可以查看有關傳遞錯誤的其他詳細資訊。

1. 要查看過期的分配，請按一下篩選器狀態並選擇 **"過期**"。

1. 要下載篩選清單的 CSV 檔，請按一下"**下載**"。

### <a name="viewing-assignments-programmatically"></a>以程式設計方式查看分配

您還可以使用 Microsoft 圖形檢索訪問包中的分配。  具有委派`EntitlementManagement.ReadWrite.All`許可權的應用程式具有適當角色的使用者可以調用 API 來[列出訪問包分配](https://docs.microsoft.com/graph/api/accesspackageassignment-list?view=graph-rest-beta)。

## <a name="directly-assign-a-user"></a>直接分配使用者

在某些情況下，您可能希望將特定使用者直接分配給訪問包，以便使用者不必經歷請求訪問包的過程。 要直接分配使用者，訪問包必須具有允許管理員直接分配的策略。

**先決條件角色：** 全域管理員、使用者管理員、目錄擁有者或訪問包管理器

1. 在 Azure 入口網站中按一下 [Azure Active Directory]****，然後按一下 [身分識別治理]****。

1. 在左側功能表中，按一下 **"訪問包**"，然後打開訪問包。

1. 在左側功能表中，按一下 **"分配**"。

1. 按一下 **"新建分配**"以打開"添加使用者以訪問包"。

    ![分配 - 將使用者添加到訪問包](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. 按一下"**添加使用者**"以選擇要為此訪問包分配給的使用者。

1. 在 **"選擇策略**"清單中，選擇使用者未來請求和生命週期將由該策略進行管理和跟蹤的策略。 如果希望所選使用者具有不同的策略設置，可以按一下 **"創建新策略**"以添加新策略。

1. 設置您希望所選使用者分配的開始和結束的日期和時間。 如果未提供結束日期，將使用策略的生命週期設置。

1. 或者，提供直接記錄保存的理由。

1. 按一下"**添加**"可直接將所選使用者分配給訪問包。

    幾分鐘後，按一下 **"刷新"** 以查看"分配"清單中的使用者。

### <a name="directly-assigning-users-programmatically"></a>以程式設計方式直接分配使用者

您還可以使用 Microsoft 圖形將使用者直接分配給訪問包。  具有委派`EntitlementManagement.ReadWrite.All`許可權的應用程式具有適當角色的使用者可以調用 API[以創建訪問包分配請求](https://docs.microsoft.com/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta)。

## <a name="remove-an-assignment"></a>刪除分配

**先決條件角色：** 全域管理員、使用者管理員、目錄擁有者或訪問包管理器

1. 在 Azure 入口網站中按一下 [Azure Active Directory]****，然後按一下 [身分識別治理]****。

1. 在左側功能表中，按一下 **"訪問包**"，然後打開訪問包。

1. 在左側功能表中，按一下 **"分配**"。
 
1. 按一下要從訪問包中刪除其分配的使用者旁邊的核取方塊。 

1. 按一下左側窗格頂部附近的 **"刪除**"按鈕。 
 
    ![分配 - 從訪問包中刪除使用者](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    將顯示一條通知，通知您已刪除分配。 

## <a name="next-steps"></a>後續步驟

- [更改訪問包的請求和設置](entitlement-management-access-package-request-policy.md)
- [檢視報告和記錄](entitlement-management-reports.md)

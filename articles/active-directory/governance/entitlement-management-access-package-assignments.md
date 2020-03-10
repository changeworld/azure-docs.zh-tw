---
title: 在 Azure AD 權利管理-中，查看、新增和移除存取套件的指派-Azure Active Directory
description: 瞭解如何在 Azure Active Directory 權利管理中查看、新增和移除存取套件的指派。
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78376730"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 權利管理中查看、新增和移除存取套件的指派

在 Azure AD 權利管理 中，您可以看到誰已獲指派存取封裝、其原則和狀態。 如果存取套件具有適當的原則，您也可以直接將使用者指派給存取封裝。 本文說明如何查看、新增和移除存取封裝的指派。

## <a name="view-who-has-an-assignment"></a>查看誰具有指派

**先決條件角色：** 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中，按一下 [**存取套件**]，然後開啟存取封裝。

1. 按一下 [**指派**]，以查看作用中的指派清單。

    ![存取套件的指派清單](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. 按一下特定的指派以查看其他詳細資料。

1. 若要查看未正確布建所有資源角色的指派清單，請按一下 [篩選狀態]，然後選取 [**傳遞**]。

    您可以在 [**要求**] 頁面上尋找使用者的對應要求，以查看傳遞錯誤的其他詳細資料。

1. 若要查看過期的指派，請按一下篩選狀態，然後選取 [已**過期**]。

1. 若要下載已篩選清單的 CSV 檔案，請按一下 [**下載**]。

### <a name="viewing-assignments-programmatically"></a>以程式設計方式查看指派

您也可以使用 Microsoft Graph 來抓取存取套件中的指派。  具有具有委派 `EntitlementManagement.ReadWrite.All` 許可權之應用程式的適當角色使用者，可以呼叫 API 來[列出 accessPackageAssignments](https://docs.microsoft.com/graph/api/accesspackageassignment-list?view=graph-rest-beta)。

## <a name="directly-assign-a-user"></a>直接指派使用者

在某些情況下，您可能會想要將特定使用者直接指派給存取套件，讓使用者不必完成要求存取套件的程式。 若要直接指派使用者，存取套件必須具有允許系統管理員直接指派的原則。

**先決條件角色：** 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中，按一下 [**存取套件**]，然後開啟存取封裝。

1. 在左側功能表中，按一下 [**指派**]。

1. 按一下 [**新增指派**] 以開啟 [將使用者新增至存取套件]。

    ![指派-將使用者新增至存取封裝](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. 按一下 [**新增使用者**] 以選取您想要指派此存取套件的使用者。

1. 在 [**選取原則**] 清單中，選取使用者的未來要求和生命週期將由其進行控管和追蹤的原則。 如果您想要讓選取的使用者擁有不同的原則設定，您可以按一下 [**建立新原則**] 來新增原則。

1. 設定您想要選取的使用者指派開始和結束的日期和時間。 如果未提供結束日期，則會使用原則的生命週期設定。

1. 選擇性地為記錄保留的直接指派提供理由。

1. 按一下 [**新增**]，將選取的使用者直接指派給存取封裝。

    幾分鐘後 **，按一下 [** 重新整理] 以查看 [指派] 清單中的使用者。

### <a name="directly-assigning-users-programmatically"></a>以程式設計方式直接指派使用者

您也可以使用 Microsoft Graph 直接將使用者指派給存取封裝。  具有具有委派 `EntitlementManagement.ReadWrite.All` 許可權之應用程式的適當角色使用者，可以呼叫 API 來[建立 accessPackageAssignmentRequest](https://docs.microsoft.com/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta)。

## <a name="remove-an-assignment"></a>移除指派

**先決條件角色：** 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中，按一下 [**存取套件**]，然後開啟存取封裝。

1. 在左側功能表中，按一下 [**指派**]。
 
1. 按一下您要從存取套件中移除其指派之使用者旁邊的核取方塊。 

1. 按一下左窗格頂端附近的 [**移除**] 按鈕。 
 
    ![指派-從存取套件移除使用者](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    系統會顯示通知，告知您已移除指派。 

## <a name="next-steps"></a>後續步驟

- [存取套件的變更要求和設定](entitlement-management-access-package-request-policy.md)
- [查看報表和記錄](entitlement-management-reports.md)

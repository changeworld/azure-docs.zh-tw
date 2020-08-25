---
title: 在 Azure AD 權利管理-Azure Active Directory 中查看、新增及移除存取套件的指派
description: 瞭解如何在 Azure Active Directory 權利管理中查看、新增及移除存取套件的指派。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 355f0127829b709d82127fb340381830793c93ff
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/24/2020
ms.locfileid: "88783547"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 權利管理中查看、新增及移除存取套件的指派

在 Azure AD 權利管理中，您可以看到誰已獲指派存取套件、其原則和狀態。 如果存取套件具有適當的原則，您也可以直接將使用者指派給存取套件。 本文說明如何查看、新增及移除存取套件的指派。

## <a name="prerequisites"></a>先決條件

若要使用 Azure AD 權利管理並指派使用者來存取套件，您必須具備下列其中一個授權：

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5 授權

## <a name="view-who-has-an-assignment"></a>查看誰有指派

**必要角色：** 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表中，按一下 [ **存取套件** ]，然後開啟存取套件。

1. 按一下 [ **指派** ] 以查看作用中指派的清單。

    ![存取套件的指派清單](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. 按一下特定指派以查看其他詳細資料。

1. 若要查看未正確布建所有資源角色的指派清單，請按一下 [篩選] 狀態，然後選取 [ **傳遞**]。

    您可以在 [ **要求** ] 頁面上找出使用者的對應要求，以查看有關傳遞錯誤的其他詳細資料。

1. 若要查看過期的指派，請按一下 [篩選] 狀態，然後選取 [已 **過期**]。

1. 若要下載篩選清單的 CSV 檔案，請按一下 [ **下載**]。

### <a name="viewing-assignments-programmatically"></a>以程式設計方式查看指派

您也可以使用 Microsoft Graph 取出存取套件中的指派。  具有具有委派許可權之應用程式的適當角色中的使用者 `EntitlementManagement.ReadWrite.All` 可以呼叫 API 來 [列出 accessPackageAssignments](/graph/api/accesspackageassignment-list?view=graph-rest-beta)。

## <a name="directly-assign-a-user"></a>直接指派使用者

在某些情況下，您可能會想要將特定使用者直接指派給存取套件，讓使用者不需要經歷要求存取套件的程式。 若要直接指派使用者，存取套件必須有允許系統管理員直接指派的原則。

**必要角色：** 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表中，按一下 [ **存取套件** ]，然後開啟存取套件。

1. 在左側功能表中，按一下 [ **指派**]。

1. 按一下 [ **新增指派** ] 以開啟 [新增使用者以存取套件]。

    ![指派-將使用者新增至存取套件](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. 按一下 [ **新增使用者** ]，選取您要指派此存取套件的使用者。

1. 在 [ **選取原則** ] 清單中，選取使用者的未來要求和生命週期將受管理及追蹤的原則。 如果您希望選取的使用者具有不同的原則設定，您可以按一下 [ **建立新原則** ] 來新增原則。

1. 設定您想要選取的使用者指派開始和結束的日期和時間。 如果未提供結束日期，則會使用原則的生命週期設定。

1. 選擇性地提供您直接指派以保留記錄的理由。

1. 按一下 [ **新增** ]，將選取的使用者直接指派給存取套件。

    幾分鐘後 **，按一下 [** 重新整理] 以查看指派清單中的使用者。

### <a name="directly-assigning-users-programmatically"></a>以程式設計方式直接指派使用者

您也可以使用 Microsoft Graph，直接將使用者指派給存取套件。  具有具有委派許可權之應用程式的適當角色中的使用者 `EntitlementManagement.ReadWrite.All` 可以呼叫 API 來 [建立 accessPackageAssignmentRequest](/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta)。

## <a name="remove-an-assignment"></a>移除指派

**必要角色：** 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表中，按一下 [ **存取套件** ]，然後開啟存取套件。

1. 在左側功能表中，按一下 [ **指派**]。
 
1. 按一下您想要從存取套件移除其指派之使用者旁的核取方塊。 

1. 按一下左窗格頂端附近的 [ **移除** ] 按鈕。 
 
    ![指派-從存取套件中移除使用者](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    將會出現通知，告知您已移除指派。 

## <a name="next-steps"></a>後續步驟

- [變更存取套件的要求和設定](entitlement-management-access-package-request-policy.md)
- [檢視報告和記錄](entitlement-management-reports.md)
---
title: 在 Azure AD 授權管理中檢視、新增及移除存取套件的分配 - Azure 活動目錄
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
ms.openlocfilehash: 12e3b86f41e7188778393ab717554907ef5d44ec
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631735"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 授權管理中檢視、新增及移除存取套件的分配

在 Azure AD 授權管理中,您可以看到已分配到訪問包的人員、策略和狀態。 如果訪問包具有適當的策略,也可以直接將使用者分配給訪問包。 本文介紹如何查看、添加和刪除訪問包的分配。

## <a name="prerequisites"></a>Prerequisites

要使用 Azure AD 授權管理並將使用者分配給存取套件,您必須具有以下授權之一:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5 授權

## <a name="view-who-has-an-assignment"></a>檢視具有配置的人員

**先決條件角色:** 全域管理員、使用者管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]****，然後按一下 [身分識別治理]****。

1. 在左側功能表中,按下 **「訪問包**」,然後打開訪問包。

1. 按下 **「分配」** 以查看活動分配的清單。

    ![存取套件的配置清單](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. 按一下特定分配以查看其他詳細資訊。

1. 要查看未正確預配所有資源角色的分配清單,請按下篩選器狀態並選擇「**傳遞**」。

    通過在 **「請求」** 頁上查找使用者的相應請求,可以查看有關傳遞錯誤的其他詳細資訊。

1. 要查看過期的分配,請單擊篩選器狀態並選擇 **「過期**」。

1. 要下載篩選清單的 CSV 檔,請按下「 下載」**。**

### <a name="viewing-assignments-programmatically"></a>以程式設計方式檢視配置

您還可以使用 Microsoft 圖形檢索訪問包中的分配。  具有委派`EntitlementManagement.ReadWrite.All`權限的應用程式具有適當角色的使用者可以呼叫 API 來[列出存取套件分配](https://docs.microsoft.com/graph/api/accesspackageassignment-list?view=graph-rest-beta)。

## <a name="directly-assign-a-user"></a>直接配置使用者

在某些情況下,您可能希望將特定使用者直接分配給訪問包,以便使用者不必經歷請求訪問包的過程。 要直接分配使用者,訪問包必須具有允許管理員直接分配的策略。

**先決條件角色:** 全域管理員、使用者管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]****，然後按一下 [身分識別治理]****。

1. 在左側功能表中,按下 **「訪問包**」,然後打開訪問包。

1. 在左側功能表中,按下 **「分配**」。

1. 按下 **「新建分配**」以打開「添加使用者以存取套件」。

    ![配置 - 將使用者加入到存取套件](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. 按下「**添加使用者**」以選擇要為此存取包分配給的使用者。

1. 在 **「選擇策略**」清單中,選擇使用者未來請求和生命週期將由該策略進行管理和追蹤的策略。 如果希望所選使用者具有不同的策略設置,可以單擊 **「創建新策略**」以添加新策略。

1. 設置您希望所選使用者分配的開始和結束的日期和時間。 如果未提供結束日期,將使用策略的生命週期設置。

1. 或者,提供直接記錄保存的理由。

1. 按下「**添加**」可直接將所選使用者分配給訪問套件。

    幾分鐘后,按一下 **「刷新」** 以查看"分配"清單中的使用者。

### <a name="directly-assigning-users-programmatically"></a>以程式設計方式直接配置使用者

您還可以使用 Microsoft 圖形將使用者直接分配給訪問包。  具有委派`EntitlementManagement.ReadWrite.All`權限的應用程式具有適當角色的使用者可以呼叫 API[以建立存取套件分配請求](https://docs.microsoft.com/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta)。

## <a name="remove-an-assignment"></a>刪除配置

**先決條件角色:** 全域管理員、使用者管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]****，然後按一下 [身分識別治理]****。

1. 在左側功能表中,按下 **「訪問包**」,然後打開訪問包。

1. 在左側功能表中,按下 **「分配**」。
 
1. 按下要從訪問包中刪除其分配的使用者旁邊的複選框。 

1. 按這裏視窗格頂部附近的 **「刪除**」按鈕。 
 
    ![配置 - 從存取套件中移除使用者](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    將顯示一條通知,通知您已刪除分配。 

## <a name="next-steps"></a>後續步驟

- [變更存取套件的要求與設定](entitlement-management-access-package-request-policy.md)
- [檢視報告和記錄](entitlement-management-reports.md)

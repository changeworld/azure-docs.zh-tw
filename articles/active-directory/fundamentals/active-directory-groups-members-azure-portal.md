---
title: 新增或移除群組成員 - Azure Active Directory | Microsoft Docs
description: 關於如何使用 Azure Active Directory 從群組新增或移除成員的指示。
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 08/23/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbb7b7da4ed5268e66260e48d6367bbe02f0845a
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87797571"
---
# <a name="add-or-remove-group-members-using-azure-active-directory"></a>使用 Azure Active Directory 新增或移除群組成員
使用 Azure Active Directory，您可以繼續新增和移除群組成員。

## <a name="to-add-group-members"></a>新增群組成員

1. 使用目錄的全域系統管理員帳戶登入 [Azure 入口網站](https://portal.azure.com)。

2. 選取 [Azure Active Directory]****，然後選取 [群組]****。

3. 從 [群組 - 所有群組]**** 頁面，搜尋並選取您要新增成員的群組。 在此案例中，請使用我們先前建立的群組 [MDM 原則 - 西部]****。

    ![群組-所有群組頁面，已反白顯示群組名稱](media/active-directory-groups-members-azure-portal/group-all-groups-screen.png)

4. 從 [MDM 原則 - 西部概觀]**** 頁面的 [管理]**** 區域中，選取 [成員]****。

    ![已醒目提示 [成員] 選項的 [MDM 原則 - 西部概觀] 頁面](media/active-directory-groups-members-azure-portal/group-overview-blade.png)

5. 選取 [新增成員]****，然後搜尋並選取您要新增至群組的每個成員，然後選擇 [選取]****。

    您會收到一則訊息，指出已成功新增成員。

    ![已顯示所搜尋成員的 [新增成員] 頁面](media/active-directory-groups-members-azure-portal/update-members.png)

6. 重新整理畫面，以查看所有新增至群組的成員名稱。

## <a name="to-remove-group-members"></a>移除群組成員

1. 從 [群組 - 所有群組]**** 頁面，搜尋並選取您要移除成員的群組。 我們會再次使用 [MDM 原則 - 西部]****。

2. 從 [管理]**** 區域中選取 [成員]****，搜尋並選取要移除的成員名稱，然後選取 [移除]****。

    ![具有 [移除] 選項的成員資訊頁面](media/active-directory-groups-members-azure-portal/remove-members-from-group.png)

## <a name="next-steps"></a>後續步驟

- [檢視群組和成員](active-directory-groups-view-azure-portal.md)

- [編輯群組設定](active-directory-groups-settings-azure-portal.md)

- [使用群組管理對資源的存取](active-directory-manage-groups.md)

- [管理群組中使用者的動態規則](../users-groups-roles/groups-create-rule.md)

- [將 Azure 訂用帳戶關聯或新增至 Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

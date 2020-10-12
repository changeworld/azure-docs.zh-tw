---
title: 新增或移除群組擁有者 - Azure Active Directory | Microsoft Docs
description: 有關如何使用 Azure Active Directory 新增或移除群組擁有者的指示。
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 09/11/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30b547ecd4ff76e650d7be837fa5bce03046da50
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89318585"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>在 Azure Active Directory 中新增或移除群組擁有者
Azure Active Directory (Azure AD) 群組是由群組擁有者所擁有及管理。 群組擁有者可以是使用者或服務主體，而且能夠管理包含成員資格的群組。 只有現有的群組擁有者或群組管理系統管理員可以指派群組擁有者。 群組擁有者不需要是該群組的成員。

當群組沒有擁有者時，群組管理的系統管理員仍然可以管理群組。 建議每個群組至少要有一個擁有者。 一旦擁有者指定至群組之後，就無法移除該群組的最後一個擁有者。 從群組中移除最後一個擁有者之前，請務必先選取另一個擁有者。

## <a name="add-an-owner-to-a-group"></a>將擁有者新增至群組
以下指示說明如何使用 Azure AD 入口網站將使用者新增為群組的擁有者。 若要將服務主體新增為群組的擁有者，請依照指示使用 [PowerShell](/powershell/module/Azuread/Add-AzureADGroupOwner?view=azureadps-2.0)來執行此作業。

### <a name="to-add-a-group-owner"></a>新增群組擁有者
1. 使用目錄的全域系統管理員帳戶登入 [Azure 入口網站](https://portal.azure.com)。

2. 選取 [Azure Active Directory]**** 並選取 [群組]****，然後選取您要新增擁有者的群組 (針對此案例，是 *MDM policy - West*)。

3. 在 [MDM 原則- 西部概觀]**** 頁面上，選取 [擁有者]****。

    ![已反白顯示 [擁有者] 選項的 [MDM 原則 - 西部概觀] 頁面](media/active-directory-accessmanagement-managing-group-owners/add-owners-option-overview-blade.png)

4. 在 [MDM 原則 - 西部 - 擁有者]**** 頁面上，選取 [新增擁有者]****，然後搜尋並選取將成為新群組擁有者的使用者，接著選擇 [選取]****。

    ![已反白顯示 [新增擁有者] 選項的 [MDM 原則 - 西部 - 擁有者] 頁面](media/active-directory-accessmanagement-managing-group-owners/add-owners-owners-blade.png)

    選取新擁有者之後，重新整理 [擁有者]**** 頁面即可看到該名稱已新增到擁有者清單。

## <a name="remove-an-owner-from-a-group"></a>移除群組中的擁有者
使用 Azure AD 從群組移除擁有者。

### <a name="to-remove-an-owner"></a>移除擁有者
1. 使用目錄的全域系統管理員帳戶登入 [Azure 入口網站](https://portal.azure.com)。

2. 選取 [Azure Active Directory]**** 並選取 [群組]****，然後選取您要移除擁有者的群組 (在此案例中是「MDM 原則 - 西部」**)。

3. 在 [MDM 原則- 西部概觀]**** 頁面上，選取 [擁有者]****。

    ![已反白顯示 [擁有者] 選項的 [MDM 原則 - 西部概觀] 頁面](media/active-directory-accessmanagement-managing-group-owners/remove-owners-option-overview-blade.png)

4. 在 [MDM 原則 - 西部 - 擁有者]**** 頁面上，選取您要移除其群組擁有者身分的使用者，從使用者的資訊頁面選取 [移除]****，然後選取 [是]**** 以確認您的決定。

    ![已反白顯示 [移除] 選項的使用者資訊頁面](media/active-directory-accessmanagement-managing-group-owners/remove-owner-info-blade.png)

    移除擁有者之後，返回 [擁有者]**** 頁面即可看到該名稱已從擁有者清單移除。

## <a name="next-steps"></a>後續步驟
- [使用 Azure Active Directory 群組管理資源的存取權](active-directory-manage-groups.md)

- [設定群組設定的 Azure Active Directory Cmdlet](../users-groups-roles/groups-settings-cmdlets.md)

- [使用群組來指派對整合 SaaS 應用程式的存取權](../users-groups-roles/groups-saasapps.md)

- [整合內部部署身分識別與 Azure Active Directory](../hybrid/whatis-hybrid-identity.md)

- [設定群組設定的 Azure Active Directory Cmdlet](../users-groups-roles/groups-settings-v2-cmdlets.md)
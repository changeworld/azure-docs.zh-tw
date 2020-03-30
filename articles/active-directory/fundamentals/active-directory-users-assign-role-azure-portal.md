---
title: 將目錄角色指派給使用者 - Azure Active Directory | Microsoft Docs
description: 有關如何使用 Azure Active Directory 將系統管理員和非系統管理員角色指派給使用者的指示。
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2df52969ea79e5d1af132aa82c2ec1ceedb92b82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422924"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>使用 Azure Active Directory 將系統管理員和非系統管理員角色指派給使用者
如果貴組織的使用者需要管理 Azure Active Directory (Azure AD) 資源的權限，您必須根據使用者需要權限才能執行的動作，在 Azure AD 中為使用者指派適當的角色。

有關可用角色的詳細資訊，請參閱[在 Azure 活動目錄中分配管理員角色](../users-groups-roles/directory-assign-admin-roles.md)。 如需新增使用者的詳細資訊，請參閱[將新的使用者新增至 Azure Active Directory](add-users-azure-active-directory.md)。

## <a name="assign-roles"></a>指派角色
指派 Azure AD 角色給使用者的常用方式，是在使用者的 [目錄角色]**** 頁面上指派。

您也可以使用 Privileged Identity Management (PIM) 來指派角色。 如需更多 PIM 使用方式的詳細資訊，請參閱 [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management)。

### <a name="to-assign-a-role-to-a-user"></a>若要將角色指派給使用者
1. 轉到[Azure 門戶](https://portal.azure.com/)並使用目錄的全域管理員帳戶登錄。 

2. 搜尋並選取 [Azure Active Directory]****。

      ![Azure 門戶搜索 Azure 活動目錄](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)


3. 選擇**使用者**。

4. 搜索並選擇獲取角色指派的使用者。 例如 _Alain Charon_。

      ![所有使用者頁面 - 選擇使用者](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. 在**阿蘭查隆 - 設定檔**頁上，選擇 **"已分配的角色**"。

    隨即會顯示 [Alain Charon - 目錄角色]**** 頁面。

6. 選擇 **"添加分配**"，選擇要分配給阿蘭的角色（例如，_應用程式管理員_），**然後選擇"**

    ![分配的角色頁面 - 顯示所選角色](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    應用程式系統管理員角色隨即會指派給 Alain Charon，並顯示在 [Alain Charon - 目錄角色] **** 頁面上。

## <a name="remove-a-role-assignment"></a>移除角色指派
如需移除使用者的角色指派，您也可以從 [Alain Charon - 目錄角色] **** 頁面操作。

### <a name="to-remove-a-role-assignment-from-a-user"></a>若要移除使用者的角色指派

1. 選取 [Azure Active Directory]**** 並選取 [使用者]****，然後搜尋並選取要移除角色指派的使用者。 例如 _Alain Charon_。

2. 選擇 **"分配的角色**"，選擇 **"應用程式管理員**"，然後選擇 **"刪除分配**"。

    ![分配的角色頁面，顯示所選角色和刪除選項](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    Alain Charon 的應用程式系統管理員角色隨即會移除，並消失在 [Alain Charon - 目錄角色] **** 頁面上。

## <a name="next-steps"></a>後續步驟
- [新增或刪除使用者](add-users-azure-active-directory.md)

- [新增或變更設定檔資訊](active-directory-users-profile-azure-portal.md)

- [從另一個目錄中新增來賓使用者](../b2b/what-is-b2b.md)

或者，您也可以執行其他使用者管理工作，例如指派委派、使用原則及共用使用者帳戶。 如需其他可用動作的詳細資訊，請參閱 [Azure Active Directory 使用者管理文件](../users-groups-roles/index.yml)。



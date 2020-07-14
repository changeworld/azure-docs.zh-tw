---
title: 將 Azure AD 角色指派給使用者-Azure Active Directory |Microsoft Docs
description: 有關如何使用 Azure Active Directory 將系統管理員和非系統管理員角色指派給使用者的指示。
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 06/15/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5dc42d8308b8e20a647f5f64867fd78a1a09c96
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2020
ms.locfileid: "86223993"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>使用 Azure Active Directory 將系統管理員和非系統管理員角色指派給使用者

在 Azure Active Directory (Azure AD) 中，如果您的其中一個使用者需要管理 Azure AD 資源的許可權，您必須將它們指派給提供所需許可權的角色。 如需管理 Azure 資源的角色，以及哪些角色會管理 Azure AD 資源的相關資訊，請參閱[傳統訂用帳戶管理員角色、Azure 角色和 Azure AD 角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)。

如需可用 Azure AD 角色的詳細資訊，請參閱[在 Azure Active Directory 中指派系統管理員角色](../users-groups-roles/directory-assign-admin-roles.md)。 若要新增使用者，請參閱[將新的使用者新增至 Azure Active Directory](add-users-azure-active-directory.md)。

## <a name="assign-roles"></a>指派角色

將 Azure AD 角色指派給使用者的常見方式是在使用者的 [**指派的角色**] 頁面上。 您也可以使用 Privileged Identity Management (PIM) ，及時提升為角色的資格。 如需如何使用 PIM 的詳細資訊，請參閱[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management)。

> [!Note]
> 如果您有 Azure AD Premium P2 授權方案，並已使用 PIM，則所有角色管理工作都會在[Privileged Identity Management 體驗](../users-groups-roles/directory-manage-roles-portal.md)中執行。
>
> ![在 PIM 中為已經使用 PIM 並具有 Premium P2 授權的使用者，Azure AD 管理的角色](./media/active-directory-users-assign-role-azure-portal/pim-manages-roles-for-p2.png)

## <a name="assign-a-role-to-a-user"></a>將角色指派給使用者

1. 移至[Azure 入口網站](https://portal.azure.com/)，然後使用目錄的全域管理員帳戶登入。

2. 搜尋並選取 [Azure Active Directory]。

      ![Azure 入口網站搜尋 Azure Active Directory](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)

3. 選取 [使用者]。

4. 搜尋並選取取得角色指派的使用者。 例如 _Alain Charon_。

      ![[所有使用者] 頁面-選取使用者](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. 在 [ **Alain Charon-設定檔**] 頁面上，選取 [**指派的角色**]。

    [ **Alain Charon-系統管理角色**] 頁面隨即出現。

6. 選取 [**新增指派**]，選取要指派給 Alain (的角色，例如_應用程式系統管理員_) ，然後選擇 [**選取**]。

    ![[指派的角色] 頁面-顯示選取的角色](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    應用程式系統管理員角色會指派給 Alain Charon，並出現在 [ **Alain Charon-系統管理角色**] 頁面上。

## <a name="remove-a-role-assignment"></a>移除角色指派

如果您需要從使用者移除角色指派，您也可以從 [ **Alain Charon-系統管理角色**] 頁面執行此動作。

### <a name="to-remove-a-role-assignment-from-a-user"></a>若要移除使用者的角色指派

1. 選取 [Azure Active Directory]**** 並選取 [使用者]****，然後搜尋並選取要移除角色指派的使用者。 例如 _Alain Charon_。

2. 選取 [**指派的角色**]，選取 [**應用程式系統管理員**]，然後選取 [**移除指派**]。

    ![[指派的角色] 頁面，顯示選取的角色和移除選項](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    [應用程式系統管理員] 角色會從 Alain Charon 移除，且不再出現在 [ **Alain Charon-系統管理角色**] 頁面上。

## <a name="next-steps"></a>後續步驟

- [新增或刪除使用者](add-users-azure-active-directory.md)

- [新增或變更設定檔資訊](active-directory-users-profile-azure-portal.md)

- [從另一個目錄中新增來賓使用者](../b2b/what-is-b2b.md)

您可以查看的其他使用者管理工作可在[Azure Active Directory 使用者管理檔](../users-groups-roles/index.yml)中取得。

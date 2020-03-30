---
title: 使用 RBAC 和 Azure 門戶添加或刪除角色指派
description: 瞭解如何使用基於 Azure 角色的存取控制 （RBAC） 和 Azure 門戶為使用者、組、服務主體或託管標識授予對 Azure 資源的訪問。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/25/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: e2458b07602bca3b6f12b2f486b509c11d705461
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246353"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-azure-portal"></a>使用 Azure RBAC 和 Azure 門戶添加或刪除角色指派

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]本文介紹如何使用 Azure 門戶分配角色。

如果需要在 Azure 活動目錄中分配管理員角色，請參閱[在 Azure 活動目錄中查看和分配管理員角色](../active-directory/users-groups-roles/directory-manage-roles-portal.md)。

## <a name="prerequisites"></a>Prerequisites

要添加或刪除角色指派，您必須具有：

- `Microsoft.Authorization/roleAssignments/write` 和 `Microsoft.Authorization/roleAssignments/delete` 權限，例如[使用者存取系統管理員](built-in-roles.md#user-access-administrator)或[擁有者](built-in-roles.md#owner)

## <a name="access-control-iam"></a>存取控制 (IAM)

**存取控制 （IAM）** 是用於分配角色以授予對 Azure 資源的訪問的邊欄選項卡。 它也稱為標識和訪問管理，並出現在 Azure 門戶中的多個位置。 下面顯示某訂用帳戶之 [存取控制 (IAM)] 刀鋒視窗的範例。

![訂用帳戶的存取控制 (IAM) 刀鋒視窗](./media/role-assignments-portal/access-control-subscription.png)

要使用 Access 控制 （IAM） 邊欄選項卡最為有效，如果您在嘗試分配角色時可以回答以下三個問題，則會有所説明：

1. **誰需要訪問？**

    誰引用使用者、組、服務主體或託管標識。 這也稱為*安全主體*。

1. **他們需要什麼角色？**

    許可權被分組到角色中。 您可以從多個[內置角色](built-in-roles.md)清單中選擇，也可以使用自己的自訂角色。

1. **他們需要訪問的位置？**

    其中是指訪問應用於的資源集。 可以管理組、訂閱組、資源組或單個資源（如存儲帳戶）。 這稱為*作用域*。

## <a name="add-a-role-assignment"></a>新增角色指派

在 Azure RBAC 中，要授予對 Azure 資源的存取權限，可以添加角色指派。 按照以下步驟分配角色。

1. 在 Azure 門戶中，按一下 **"所有服務**"，然後選擇要授予存取權限的範圍。 例如，您可以選取 [管理群組]****、[訂用帳戶]****、[資源群組]**** 或資源。

1. 按一下該範圍的特定資源。

1. 按一下**存取控制 （IAM）。**

1. 按一下"**角色指派**"選項卡以查看此範圍的角色指派。

    ![存取控制 （IAM） 和角色指派選項卡](./media/role-assignments-portal/role-assignments.png)

1. 按一下**Add** > "**添加添加角色指派**"。

   若您沒有指派角色的權限，[新增角色指派] 選項將會被停用。

   ![[新增] 功能表](./media/role-assignments-portal/add-menu.png)

    [新增角色指派] 窗格隨即開啟。

   ![[新增角色指派] 窗格](./media/role-assignments-portal/add-role-assignment.png)

1. 在 [角色]**** 下拉式清單中選取角色，例如 [虛擬機器參與者]****。

1. 在 [選取]**** 清單中，選取使用者、群組、服務主體或受控識別。 如果在清單中未看到安全性主體，您可以在 [選取]**** 方塊中輸入，以在目錄中搜尋顯示名稱、電子郵件地址和物件識別碼。

1. 按一下 [儲存]**** 以指派角色。

   在幾分鐘之後，即會在所選範圍中指派安全性主體的角色。

    ![添加已保存的角色指派](./media/role-assignments-portal/add-role-assignment-save.png)

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>指派使用者做為訂用帳戶的系統管理員

若要將使用者設定為 Azure 訂用帳戶的系統管理員，請在訂用帳戶範圍為其指派[擁有者](built-in-roles.md#owner)角色。 擁有者角色允許使用者完全訪問訂閱中的所有資源，包括授予他人存取權限的許可權。 針對任何其他角色指派，這些步驟都相同。

1. 在 Azure 入口網站中，按一下 [所有服務]****，然後按一下 [訂用帳戶]****。

1. 選擇您想要授與存取權的訂用帳戶。

1. 按一下**存取控制 （IAM）。**

1. 按一下"**角色指派**"選項卡以查看此訂閱的角色指派。

    ![存取控制 （IAM） 和角色指派選項卡](./media/role-assignments-portal/role-assignments.png)

1. 按一下**Add** > "**添加添加角色指派**"。

   若您沒有指派角色的權限，[新增角色指派] 選項將會被停用。

   ![[新增] 功能表](./media/role-assignments-portal/add-menu.png)

    [新增角色指派] 窗格隨即開啟。

   ![[新增角色指派] 窗格](./media/role-assignments-portal/add-role-assignment.png)

1. 在 [角色]**** 下拉式清單中，選取 [擁有者]**** 角色。

1. 在 [選取]**** 清單中，選取使用者。 如果在清單中未看到使用者，您可以在 [選取]**** 方塊中輸入，以在目錄中搜尋顯示名稱與電子郵件地址。

1. 按一下 [儲存]**** 以指派角色。

   在幾分鐘之後，即會在訂用帳戶範圍將「擁有者」角色指派給使用者。

## <a name="remove-a-role-assignment"></a>移除角色指派

在 Azure RBAC 中，要從 Azure 資源中刪除存取權限，請刪除角色指派。 按照以下步驟刪除角色指派。

1. 針對您要移除存取權的範圍 (例如管理群組、訂用帳戶、資源群組或資源) 開啟 [存取控制 (IAM)]****。

1. 按一下 [角色指派]**** 索引標籤以檢視此訂用帳戶的所有角色指派。

1. 在角色指派清單中，在具有您要移除的角色指派安全性主體旁加上核取記號。

   ![移除角色指派訊息](./media/role-assignments-portal/remove-role-assignment-select.png)

1. 按一下 [移除]****。

   ![移除角色指派訊息](./media/role-assignments-portal/remove-role-assignment.png)

1. 在顯示的移除角色指派訊息中，按一下 [是]****。

    如果看到無法刪除繼承的角色指派的消息，則嘗試刪除子作用域中的角色指派。 您應該在分配角色的範圍打開存取控制 （IAM），然後重試。 在正確的作用域打開訪問控制項 （IAM） 的一種快速方法是查看 **"範圍"** 列並按一下 **（繼承）** 旁邊的連結。

   ![移除角色指派訊息](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>後續步驟

- [使用 Azure RBAC 和 Azure 門戶列出角色指派](role-assignments-list-portal.md)
- [教程：使用 RBAC 和 Azure 門戶授予使用者對 Azure 資源的存取權限](quickstart-assign-role-user-portal.md)
- [針對適用於 Azure 資源的 RBAC 進行疑難排解](troubleshooting.md)
- [使用 Azure 管理組組織資源](../governance/management-groups/overview.md)

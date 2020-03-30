---
title: 使用 Azure RBAC 和 Azure 門戶列出角色指派
description: 瞭解如何確定使用者、組、服務主體或託管標識可以使用基於 Azure 的角色存取控制 （RBAC） 和 Azure 門戶訪問哪些資源。
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
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 03a3d3c7d572d7ec5b8d3ac3d527d0d59e649bc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062234"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-azure-portal"></a>使用 Azure RBAC 和 Azure 門戶列出角色指派

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]本文介紹如何使用 Azure 門戶列出角色指派。

> [!NOTE]
> 如果您的組織將管理功能外包給使用[Azure 委派資源的](../lighthouse/concepts/azure-delegated-resource-management.md)服務提供者，則該服務提供者授權的角色指派將不會在此處顯示。

## <a name="list-role-assignments-for-a-user-or-group"></a>列出使用者或組的角色指派

查看分配給訂閱中的使用者或組的角色的最簡單方法是使用**Azure 資源**窗格。

1. 在 Azure 門戶中，按一下 **"所有服務**"，然後選擇 **"使用者**"或 **"組**"。

1. 按一下要列出角色指派的使用者或組。

1. 按一下 [Azure 資源]****。

    您將看到分配給不同作用域（如管理組、訂閱、資源組或資源）的選定使用者或組的角色清單。 此清單包括您有權閱讀的所有角色指派。

    ![使用者的角色指派](./media/role-assignments-list-portal/azure-resources-user.png)    

1. 要更改訂閱，請按一下 **"訂閱"** 清單。

## <a name="list-owners-of-a-subscription"></a>列出訂閱的擁有者

已為訂閱分配了["擁有者"](built-in-roles.md#owner)角色的使用者可以管理訂閱中的所有內容。 按照以下步驟列出訂閱的擁有者。

1. 在 Azure 入口網站中，按一下 [所有服務]****，然後按一下 [訂用帳戶]****。

1. 按一下要列出其擁有者的訂閱。

1. 按一下**存取控制 （IAM）。**

1. 按一下 [角色指派]**** 索引標籤以檢視此訂用帳戶的所有角色指派。

1. 滾動到 **"擁有者"** 部分以查看已為此訂閱分配的擁有者角色的所有使用者。

   ![訂閱存取控制 - 角色指派選項卡](./media/role-assignments-list-portal/access-control-role-assignments-subscription.png)

## <a name="list-role-assignments-at-a-scope"></a>在作用域中列出角色指派

1. 在 Azure 門戶中，按一下 **"所有服務**"，然後選擇範圍。 例如，您可以選取 [管理群組]****、[訂用帳戶]****、[資源群組]**** 或資源。

1. 按一下特定資源。

1. 按一下**存取控制 （IAM）。**

1. 按一下 [角色指派]**** 索引標籤以檢視此範圍中的所有角色指派。

   ![存取控制︰[角色指派] 索引標籤](./media/role-assignments-list-portal/access-control-role-assignments.png)

   在 [角色指派] 索引標籤上，您可以看到誰在此範圍中有存取權。 請注意，某些角色的範圍限於**此資源**，而有些角色則是來自 **(繼承自)** 另一個範圍。 存取權不是特別指派給此資源群組，就是繼承自父範圍的指派。

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>列出作用域內使用者的角色指派

要列出使用者、組、服務主體或託管標識的存取權限，可以列出其角色指派。 按照以下步驟列出特定作用域中的單個使用者、組、服務主體或託管標識的角色指派。

1. 在 Azure 門戶中，按一下 **"所有服務**"，然後選擇範圍。 例如，您可以選取 [管理群組]****、[訂用帳戶]****、[資源群組]**** 或資源。

1. 按一下特定資源。

1. 按一下**存取控制 （IAM）。**

1. 按一下 [檢查存取權]**** 索引標籤。

    ![存取控制 - [檢查存取權] 索引標籤](./media/role-assignments-list-portal/access-control-check-access.png)

1. 在 [尋找]**** 清單中，選取您要檢查其存取權的安全性主體類型。

1. 在搜尋方塊中，輸入字串以在目錄中搜尋顯示名稱、電子郵件地址或物件識別碼。

    ![檢查存取權選取清單](./media/role-assignments-list-portal/check-access-select.png)

1. 按一下安全性主體以開啟 [指派]**** 窗格。

    ![[指派] 窗格](./media/role-assignments-list-portal/check-access-assignments.png)

    在此窗格上，您可以看到已指派所選安全性主體的角色與範圍。 若此範圍中有任何拒絕指派，或有任何拒絕指派繼承到此範圍，它們將會被列出。

## <a name="list-role-assignments-for-a-system-assigned-managed-identity"></a>列出系統分配的託管標識的角色指派

1. 在 Azure 門戶中，打開系統分配的託管標識。

1. 在左側功能表中，按一下 **"標識**"。

    ![系統指派的受控識別](./media/role-assignments-list-portal/identity-system-assigned.png)

1. 在**角色指派**下，按一下 **"顯示分配給此託管標識的 Azure RBAC 角色**"。

    您將看到分配給所選系統分配的託管標識的角色清單，這些角色位於管理組、訂閱、資源組或資源等各種作用域中。 此清單包括您有權閱讀的所有角色指派。

    ![系統分配的託管標識的角色指派](./media/role-assignments-list-portal/azure-resources-system-assigned.png)

## <a name="list-role-assignments-for-a-user-assigned-managed-identity"></a>列出使用者分配的託管標識的角色指派

1. 在 Azure 門戶中，打開使用者分配的託管標識。

1. 按一下 [Azure 資源]****。

    您將看到分配給不同作用域（如管理組、訂閱、資源組或資源）的選定使用者分配託管標識的角色清單。 此清單包括您有權閱讀的所有角色指派。

    ![系統分配的託管標識的角色指派](./media/role-assignments-list-portal/azure-resources-user-assigned.png)

1. 要更改訂閱，請按一下 **"訂閱"** 清單。

## <a name="list-number-of-role-assignments"></a>角色指派清單數

每個訂閱中最多可以分配**2000**個角色。 為了説明您跟蹤此限制，"**角色指派**"選項卡包含一個圖表，其中列出了當前訂閱的角色指派數。

![存取控制 - 角色指派圖表數量](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

如果您接近最大數量並嘗試添加更多角色指派，您將在 **"添加角色指派**"窗格中看到一條警告。 有關減少角色指派數量的方法，請參閱[排除 Azure RBAC 故障](troubleshooting.md#azure-role-assignments-limit)。

![存取控制 - 添加角色指派警告](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="next-steps"></a>後續步驟

- [使用 Azure RBAC 和 Azure 門戶添加或刪除角色指派](role-assignments-portal.md)
- [針對適用於 Azure 資源的 RBAC 進行疑難排解](troubleshooting.md)

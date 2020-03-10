---
title: 使用 Azure RBAC 和 Azure 入口網站列出角色指派
description: 瞭解如何使用 Azure 角色型存取控制（RBAC）和 Azure 入口網站，判斷哪些資源的使用者、群組、服務主體或受控識別可以存取。
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
ms.date: 01/23/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 63aef8d2036e442659145e031d384e9677c779a1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393980"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-azure-portal"></a>使用 Azure RBAC 和 Azure 入口網站列出角色指派

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] 本文說明如何使用 Azure 入口網站列出角色指派。

> [!NOTE]
> 如果您的組織具有外包管理功能給使用[Azure 委派資源管理](../lighthouse/concepts/azure-delegated-resource-management.md)的服務提供者，該服務提供者所授權的角色指派將不會顯示在這裡。

## <a name="list-role-assignments-for-a-user-or-group"></a>列出使用者或群組的角色指派

若要查看指派給訂用帳戶中使用者或群組的角色，最簡單的方式是使用 [ **Azure 資源**] 窗格。

1. 在 Azure 入口網站中，按一下 **所有服務**，然後選取 **使用者**或**群組**。

1. 按一下您要列出角色指派的使用者或群組。

1. 按一下 [Azure 資源]。

    您會在各種範圍（例如管理群組、訂用帳戶、資源群組或資源）上，看到指派給所選使用者或群組的角色清單。 這份清單包含您有權讀取的所有角色指派。

    ![使用者的角色指派](./media/role-assignments-list-portal/azure-resources-user.png)    

1. 若要變更訂用帳戶，請按一下 [**訂閱**] 清單。

## <a name="list-owners-of-a-subscription"></a>列出訂用帳戶的擁有者

已獲派訂用帳戶之「[擁有](built-in-roles.md#owner)者」角色的使用者可以管理訂用帳戶中的所有專案。 請遵循下列步驟來列出訂用帳戶的擁有者。

1. 在 Azure 入口網站中，按一下 [所有服務]，然後按一下 [訂用帳戶]。

1. 按一下您要列出其擁有者的訂用帳戶。

1. 按一下 [存取控制 (IAM)]。

1. 按一下 [角色指派] 索引標籤以檢視此訂用帳戶的所有角色指派。

1. 請前往 [**擁有**者] 區段，查看所有已獲指派此訂用帳戶之擁有者角色的使用者。

   ![[訂用帳戶存取控制-角色指派] 索引標籤](./media/role-assignments-list-portal/access-control-role-assignments-subscription.png)

## <a name="list-role-assignments-at-a-scope"></a>列出範圍中的角色指派

1. 在 Azure 入口網站中，按一下 **所有服務**，然後選取範圍。 例如，您可以選取 [管理群組]、[訂用帳戶]、[資源群組]或資源。

1. 按一下特定的資源。

1. 按一下 [存取控制 (IAM)]。

1. 按一下 [角色指派] 索引標籤以檢視此範圍中的所有角色指派。

   ![存取控制︰[角色指派] 索引標籤](./media/role-assignments-list-portal/access-control-role-assignments.png)

   在 [角色指派] 索引標籤上，您可以看到誰在此範圍中有存取權。 請注意，某些角色的範圍限於**此資源**，而有些角色則是來自 **(繼承自)** 另一個範圍。 存取權不是特別指派給此資源群組，就是繼承自父範圍的指派。

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>列出使用者在範圍中的角色指派

若要列出使用者、群組、服務主體或受控識別的存取權，您可以列出其角色指派。 請遵循下列步驟，列出特定範圍內的單一使用者、群組、服務主體或受控識別的角色指派。

1. 在 Azure 入口網站中，按一下 **所有服務**，然後選取範圍。 例如，您可以選取 [管理群組]、[訂用帳戶]、[資源群組]或資源。

1. 按一下特定的資源。

1. 按一下 [存取控制 (IAM)]。

1. 按一下 [檢查存取權] 索引標籤。

    ![存取控制 - [檢查存取權] 索引標籤](./media/role-assignments-list-portal/access-control-check-access.png)

1. 在 [尋找] 清單中，選取您要檢查其存取權的安全性主體類型。

1. 在搜尋方塊中，輸入字串以在目錄中搜尋顯示名稱、電子郵件地址或物件識別碼。

    ![檢查存取權選取清單](./media/role-assignments-list-portal/check-access-select.png)

1. 按一下安全性主體以開啟 [指派] 窗格。

    ![[指派] 窗格](./media/role-assignments-list-portal/check-access-assignments.png)

    在此窗格上，您可以看到已指派所選安全性主體的角色與範圍。 若此範圍中有任何拒絕指派，或有任何拒絕指派繼承到此範圍，它們將會被列出。

## <a name="list-role-assignments-for-a-system-assigned-managed-identity"></a>列出系統指派受控識別的角色指派

1. 在 Azure 入口網站中，開啟系統指派的受控識別。

1. 在左側功能表中，按一下 [身分**識別**]。

    ![系統指派的受控識別](./media/role-assignments-list-portal/identity-system-assigned.png)

1. 在 [**角色指派**] 底下，按一下 **[顯示指派給此受控識別的 Azure RBAC 角色**]。

    您會在各種範圍（例如管理群組、訂用帳戶、資源群組或資源）上，看到指派給所選系統指派受控識別的角色清單。 這份清單包含您有權讀取的所有角色指派。

    ![系統指派受控識別的角色指派](./media/role-assignments-list-portal/azure-resources-system-assigned.png)

## <a name="list-role-assignments-for-a-user-assigned-managed-identity"></a>列出使用者指派的受控識別的角色指派

1. 在 Azure 入口網站中，開啟使用者指派的受控識別。

1. 按一下 [Azure 資源]。

    您會在各種範圍（例如管理群組、訂用帳戶、資源群組或資源）上，看到指派給所選使用者指派受控識別的角色清單。 這份清單包含您有權讀取的所有角色指派。

    ![系統指派受控識別的角色指派](./media/role-assignments-list-portal/azure-resources-user-assigned.png)

1. 若要變更訂用帳戶，請按一下 [**訂閱**] 清單。

## <a name="list-number-of-role-assignments"></a>列出角色指派的數目

您在每個訂用帳戶中最多可以有**2000**個角色指派。 為了協助您追蹤這些限制，[**角色指派**] 索引標籤包含的圖表會列出目前的角色指派數目。

![存取控制-角色指派的數目圖表](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

如果您接近最大數目，而且您嘗試新增更多角色指派，您會在 [**新增角色指派**] 窗格中看到警告。 您可以刪除不再需要的角色指派，或將角色指派給群組而非個別使用者，以減少角色指派的數目。

![存取控制-新增角色指派警告](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="next-steps"></a>後續步驟

- [使用 Azure RBAC 和 Azure 入口網站新增或移除角色指派](role-assignments-portal.md)
- [針對適用於 Azure 資源的 RBAC 進行疑難排解](troubleshooting.md)

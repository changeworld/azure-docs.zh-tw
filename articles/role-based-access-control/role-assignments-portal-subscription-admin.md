---
title: 將使用者指派為 Azure 訂用帳戶的系統管理員-Azure RBAC
description: 瞭解如何使用 Azure 入口網站和 Azure 角色型存取控制 (Azure RBAC) ，讓使用者成為 Azure 訂用帳戶的系統管理員。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/11/2021
ms.author: rolyon
ms.openlocfilehash: b15238a4308fe055f914d1a684b79b38a2c64870
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98122097"
---
# <a name="assign-a-user-as-an-administrator-of-an-azure-subscription"></a>將使用者指派為 Azure 訂用帳戶的系統管理員

若要將使用者設定為 Azure 訂用帳戶的系統管理員，請在訂用帳戶範圍為其指派[擁有者](built-in-roles.md#owner)角色。 擁有者角色提供使用者訂用帳戶中所有資源的完整存取權，包括將存取權授與其他人的許可權。 針對任何其他角色指派，這些步驟都相同。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="step-1-open-the-subscription"></a>步驟1：開啟訂用帳戶

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在頂端的搜尋方塊中，搜尋訂閱。

    ![Azure 入口網站搜尋資源群組](./media/shared/sub-portal-search.png)

1. 按一下您想要使用的訂用帳戶。

    以下顯示範例訂用帳戶。

    ![資源群組概觀](./media/shared/sub-overview.png)

## <a name="step-2-open-the-add-role-assignment-pane"></a>步驟2：開啟 [新增角色指派] 窗格

**存取控制 (IAM)** 是您通常用來指派角色以授與 Azure 資源存取權的頁面。 它也稱為身分識別和存取管理 (IAM) ，而且會出現在 Azure 入口網站中的數個位置。

1. 按一下 [存取控制 (IAM)]。

    以下顯示訂用帳戶的存取控制 (IAM) 頁面的範例。

    ![資源群組的存取控制 (IAM) 頁面](./media/shared/sub-access-control.png)

1. 按一下 [ **角色指派** ] 索引標籤，以查看此範圍的角色指派。

1. 按一下 [**新增**  >  **新增角色指派**]。
   若您沒有指派角色的權限，[新增角色指派] 選項將會被停用。

   ![新增角色指派功能表](./media/shared/add-role-assignment-menu.png)

    [新增角色指派] 窗格隨即開啟。

   ![[新增角色指派] 窗格](./media/shared/add-role-assignment.png)

## <a name="step-3-select-the-owner-role"></a>步驟3：選取擁有者角色

[擁有](built-in-roles.md#owner)者角色會授與管理所有資源的完整存取權，包括在 Azure RBAC 中指派角色的能力。 您應擁有最多3個訂用帳戶擁有者，以降低遭盜用的擁有者可能發生缺口。

- 在 [ **角色** ] 清單中，選取 [ **擁有** 者] 角色。

   ![[新增角色指派] 窗格中的 [選取擁有者角色]](./media/role-assignments-portal-subscription-admin/add-role-assignment-role-owner.png)

## <a name="step-4-select-who-needs-access"></a>步驟4：選取需要存取的人員

1. 在 [ **指派存取權給** ] 清單中，選取 [ **使用者]、[群組] 或 [服務主體**]。

1. 在 [ **選取** ] 區段中，輸入字串或在清單中滾動，以搜尋使用者。

   ![在 [新增角色指派] 中選取使用者](./media/role-assignments-portal-subscription-admin/add-role-assignment-user-admin.png)

1. 找到使用者之後，請按一下加以選取。

## <a name="step-5-assign-role"></a>步驟5：指派角色

1. 若要指派角色，請按一下 [ **儲存**]。

   在幾分鐘之後，就會在選取的範圍中將角色指派給使用者。

1. 在 [ **角色指派** ] 索引標籤上，確認您在清單中看到角色指派。

    ![已儲存新增角色指派](./media/role-assignments-portal-subscription-admin/sub-role-assignments-owner.png)

## <a name="next-steps"></a>後續步驟

- [使用 Azure 入口網站新增或移除 Azure 角色指派](role-assignments-portal.md)
- [使用 Azure 入口網站列出 Azure 角色指派](role-assignments-list-portal.md)
- [使用 Azure 管理群組來組織資源](../governance/management-groups/overview.md)

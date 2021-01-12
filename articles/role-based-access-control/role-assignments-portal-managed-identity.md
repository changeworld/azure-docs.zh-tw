---
title: 為受控識別新增角色指派 (預覽版) -Azure RBAC
description: 瞭解如何新增角色指派，方法是從受控識別開始，然後使用 Azure 入口網站和 Azure 角色型存取控制 (Azure RBAC) 來選取範圍和角色。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/11/2021
ms.author: rolyon
ms.openlocfilehash: a01246c0cf35653f4d13262183cf9df28b056c69
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98122019"
---
# <a name="add-a-role-assignment-for-a-managed-identity-preview"></a>為受控識別新增角色指派 (預覽版) 

您可以使用 **存取控制 (IAM)** 頁面來新增受控識別的角色指派，如 [使用 Azure 入口網站新增或移除 Azure 角色指派](role-assignments-portal.md)中所述。 當您使用存取控制 (IAM) ] 頁面時，您會從範圍開始，然後選取受控識別和角色。 本文說明新增受控識別之角色指派的另一種方式。 使用這些步驟，您可以從受控識別開始，然後選取範圍和角色。

> [!IMPORTANT]
> 使用這些替代步驟來新增受控識別的角色指派目前為預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="system-assigned-managed-identity"></a>系統指派的受控識別

請遵循下列步驟，從受控識別開始將角色指派給系統指派的受控識別。

1. 在 Azure 入口網站中，開啟系統指派的受控識別。

1. 在左側功能表中，按一下 [身分 **識別**]。

    ![系統指派的受控識別](./media/shared/identity-system-assigned.png)

1. 在 [ **許可權**] 底下，按一下 [ **Azure 角色指派**]。

    如果角色已指派給選取的系統指派受控識別，您會看到角色指派清單。 這份清單包含您有權讀取的所有角色指派。

    ![系統指派的受控識別的角色指派](./media/shared/role-assignments-system-assigned.png)

1. 若要變更訂用帳戶，請按一下 **訂** 用帳戶清單。

1. 按一下 [ **新增角色指派] (預覽)**。

1. 您可以使用下拉式清單來選取角色指派套用的資源集，例如 **訂** 用帳戶、 **資源群組** 或資源。

    如果您沒有所選範圍的角色指派寫入權限，則會顯示內嵌訊息。 

1. 在 [角色] 下拉式清單中選取角色，例如 [虛擬機器參與者]。

   ![為系統指派的受控識別新增角色指派窗格](./media/role-assignments-portal-managed-identity/add-role-assignment-with-scope.png)

1. 按一下 [儲存] 以指派角色。

   經過幾分鐘之後，受控識別會在選取的範圍指派角色。

## <a name="user-assigned-managed-identity"></a>使用者指派的受控識別

請遵循下列步驟，從受控識別開始將角色指派給使用者指派的受控識別。

1. 在 Azure 入口網站中，開啟使用者指派的受控識別。

1. 在左側功能表中，按一下 [ **Azure 角色指派**]。

    如果已將角色指派給所選使用者指派的受控識別，您會看到角色指派清單。 這份清單包含您有權讀取的所有角色指派。

    ![使用者指派的受控識別的角色指派](./media/shared/role-assignments-user-assigned.png)

1. 若要變更訂用帳戶，請按一下 **訂** 用帳戶清單。

1. 按一下 [ **新增角色指派] (預覽)**。

1. 您可以使用下拉式清單來選取角色指派套用的資源集，例如 **訂** 用帳戶、 **資源群組** 或資源。

    如果您沒有所選範圍的角色指派寫入權限，則會顯示內嵌訊息。 

1. 在 [角色] 下拉式清單中選取角色，例如 [虛擬機器參與者]。

   ![為使用者指派的受控識別新增角色指派窗格](./media/role-assignments-portal-managed-identity/add-role-assignment-with-scope.png)

1. 按一下 [儲存] 以指派角色。

   經過幾分鐘之後，受控識別會在選取的範圍指派角色。

## <a name="next-steps"></a>後續步驟

- [什麼是 Azure 資源受控識別？](../active-directory/managed-identities-azure-resources/overview.md)
- [使用 Azure 入口網站新增或移除 Azure 角色指派](role-assignments-portal.md)
- [使用 Azure 入口網站列出 Azure 角色指派](role-assignments-list-portal.md)

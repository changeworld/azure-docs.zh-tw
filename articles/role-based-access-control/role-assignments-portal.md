---
title: 使用 Azure 入口網站新增或移除 Azure 角色指派-Azure RBAC
description: 瞭解如何使用 Azure 入口網站和 Azure 角色型存取控制 (Azure RBAC) ，為使用者、群組、服務主體或受控識別授與 Azure 資源的存取權。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 09/30/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 7c58641f0039982f05be14d0f24ba89c62273d4b
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964298"
---
# <a name="add-or-remove-azure-role-assignments-using-the-azure-portal"></a>使用 Azure 入口網站新增或移除 Azure 角色指派

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] 本文說明如何使用 Azure 入口網站指派角色。

如果您需要在 Azure Active Directory 中指派系統管理員角色，請參閱 [Azure Active Directory 中的 [查看和指派系統管理員角色](../active-directory/roles/manage-roles-portal.md)]。

## <a name="prerequisites"></a>先決條件

若要新增或移除角色指派，您必須具有：

- `Microsoft.Authorization/roleAssignments/write` 和 `Microsoft.Authorization/roleAssignments/delete` 權限，例如[使用者存取系統管理員](built-in-roles.md#user-access-administrator)或[擁有者](built-in-roles.md#owner)

## <a name="access-control-iam"></a>存取控制 (IAM)

**存取控制 (IAM)** 是您通常用來指派角色以授與 Azure 資源存取權的頁面。 它也稱為身分識別和存取管理，並且會出現在 Azure 入口網站的數個位置中。 以下顯示訂用帳戶的存取控制 (IAM) 頁面的範例。

![訂用帳戶的存取控制 (IAM) 頁面](./media/role-assignments-portal/access-control-subscription.png)

若要在存取控制 (IAM) 頁面上獲得最高效益，可協助遵循下列步驟來指派角色。

1. 判斷誰需要存取權。 您可以將角色指派給使用者、群組、服務主體或受控識別。

1. 尋找適當的角色。 許可權會群組在一起成為角色。 您可以從數個 [Azure 內建角色](built-in-roles.md) 的清單中選取，也可以使用自己的自訂角色。

1. 識別所需的範圍。 Azure 提供四個範圍層級： [管理群組](../governance/management-groups/overview.md)、訂用帳戶、 [資源群組](../azure-resource-manager/management/overview.md#resource-groups)和資源。 如需範圍的詳細資訊，請參閱[了解範圍](scope-overview.md)。

1. 執行下列其中一節中的步驟來指派角色。

## <a name="add-a-role-assignment"></a>新增角色指派

在 Azure RBAC 中，若要授與 Azure 資源的存取權，您可以新增角色指派。 請遵循下列步驟來指派角色。

1. 在 [Azure 入口網站中，按一下 [ **所有服務** ]，然後選取您想要授與存取權的範圍。 例如，您可以選取 [管理群組]、[訂用帳戶]、[資源群組]或資源。

1. 按一下該範圍的特定資源。

1. 按一下 [存取控制 (IAM)]。

1. 按一下 [ **角色指派** ] 索引標籤，以查看此範圍的角色指派。

    ![存取控制 (IAM) 和角色指派索引標籤](./media/role-assignments-portal/role-assignments.png)

1. 按一下 [**新增**  >  **新增角色指派**]。

   若您沒有指派角色的權限，[新增角色指派] 選項將會被停用。

   ![新增角色指派功能表](./media/shared/add-role-assignment-menu.png)

    [新增角色指派] 窗格隨即開啟。

   ![[新增角色指派] 窗格](./media/role-assignments-portal/add-role-assignment.png)

1. 在 [角色] 下拉式清單中選取角色，例如 [虛擬機器參與者]。

1. 在 [選取] 清單中，選取使用者、群組、服務主體或受控識別。 如果在清單中未看到安全性主體，您可以在 [選取] 方塊中輸入，以在目錄中搜尋顯示名稱、電子郵件地址和物件識別碼。

1. 按一下 [儲存] 以指派角色。

   在幾分鐘之後，即會在所選範圍中指派安全性主體的角色。

    ![已儲存新增角色指派](./media/role-assignments-portal/add-role-assignment-save.png)

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>將使用者指派為訂用帳戶的系統管理員

若要將使用者設定為 Azure 訂用帳戶的系統管理員，請在訂用帳戶範圍為其指派[擁有者](built-in-roles.md#owner)角色。 擁有者角色提供使用者訂用帳戶中所有資源的完整存取權，包括將存取權授與其他人的許可權。 針對任何其他角色指派，這些步驟都相同。

1. 在 Azure 入口網站中，按一下 [所有服務]  ，然後按一下 [訂用帳戶]  。

1. 選擇您想要授與存取權的訂用帳戶。

1. 按一下 [存取控制 (IAM)]。

1. 按一下 [ **角色指派** ] 索引標籤，以查看此訂用帳戶的角色指派。

    ![存取控制 (IAM) 和角色指派索引標籤](./media/role-assignments-portal/role-assignments.png)

1. 按一下 [**新增**  >  **新增角色指派**]。

   若您沒有指派角色的權限，[新增角色指派] 選項將會被停用。

   ![新增訂用帳戶的角色指派功能表](./media/shared/add-role-assignment-menu.png)

    [新增角色指派] 窗格隨即開啟。

   ![新增訂用帳戶的角色指派窗格](./media/role-assignments-portal/add-role-assignment.png)

1. 在 [角色] 下拉式清單中，選取 [擁有者] 角色。

1. 在 [選取] 清單中，選取使用者。 如果在清單中未看到使用者，您可以在 [選取] 方塊中輸入，以在目錄中搜尋顯示名稱與電子郵件地址。

1. 按一下 [儲存] 以指派角色。

   在幾分鐘之後，即會在訂用帳戶範圍將「擁有者」角色指派給使用者。

## <a name="add-a-role-assignment-for-a-managed-identity-preview"></a>為受控識別新增角色指派 (預覽版) 

如本文稍早所述，您可以使用 **存取控制 (IAM)** 頁面來新增受控識別的角色指派。 當您使用存取控制 (IAM) ] 頁面時，您會從範圍開始，然後選取受控識別和角色。 本節說明新增受控識別之角色指派的另一種方式。 使用這些步驟，您可以從受控識別開始，然後選取範圍和角色。

> [!IMPORTANT]
> 使用這些替代步驟來新增受控識別的角色指派目前為預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

### <a name="system-assigned-managed-identity"></a>系統指派的受控識別

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

   ![為系統指派的受控識別新增角色指派窗格](./media/role-assignments-portal/add-role-assignment-with-scope.png)

1. 按一下 [儲存] 以指派角色。

   經過幾分鐘之後，受控識別會在選取的範圍指派角色。

### <a name="user-assigned-managed-identity"></a>使用者指派的受控識別

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

   ![為使用者指派的受控識別新增角色指派窗格](./media/role-assignments-portal/add-role-assignment-with-scope.png)

1. 按一下 [儲存] 以指派角色。

   經過幾分鐘之後，受控識別會在選取的範圍指派角色。

## <a name="remove-a-role-assignment"></a>移除角色指派

在 Azure RBAC 中，若要移除 Azure 資源的存取權，請移除角色指派。 請遵循下列步驟來移除角色指派。

1. 針對您要移除存取權的範圍 (例如管理群組、訂用帳戶、資源群組或資源) 開啟 [存取控制 (IAM)]。

1. 按一下 [角色指派] 索引標籤以檢視此訂用帳戶的所有角色指派。

1. 在角色指派清單中，在具有您要移除的角色指派安全性主體旁加上核取記號。

   ![已選取要移除的角色指派](./media/role-assignments-portal/remove-role-assignment-select.png)

1. 按一下 **[移除]** 。

   ![移除角色指派訊息](./media/role-assignments-portal/remove-role-assignment.png)

1. 在顯示的移除角色指派訊息中，按一下 [是]。

    如果您看到無法移除繼承角色指派的訊息，則表示您嘗試移除子範圍的角色指派。 您應該在指派角色的範圍中開啟存取控制 (IAM) ，然後再試一次。 在正確的範圍中，快速開啟存取控制 (IAM) 的方法是查看 [ **領域** ] 資料行，然後按一下 [ **(繼承的)**] 旁的連結。

   ![移除繼承角色指派的角色指派訊息](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>後續步驟

- [使用 Azure 入口網站列出 Azure 角色指派](role-assignments-list-portal.md)
- [教學課程：使用 Azure 入口網站為使用者授與 Azure 資源的存取權](quickstart-assign-role-user-portal.md)
- [針對 Azure RBAC 進行疑難排解](troubleshooting.md)
- [使用 Azure 管理群組來組織資源](../governance/management-groups/overview.md)

---
title: 使用 Azure 入口網站列出 Azure 角色指派-Azure RBAC
description: 瞭解如何使用 Azure 入口網站和 Azure 角色型存取控制 (Azure RBAC) ，判斷使用者、群組、服務主體或受控識別有哪些資源可以存取哪些資源。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 10/16/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 280e5012f7c41a906b5b2e266840ea3c0cc315ba
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151688"
---
# <a name="list-azure-role-assignments-using-the-azure-portal"></a>使用 Azure 入口網站列出 Azure 角色指派

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] 本文說明如何使用 Azure 入口網站列出角色指派。

> [!NOTE]
> 如果您的組織具有外包管理功能給使用 [Azure 委派資源管理](../lighthouse/concepts/azure-delegated-resource-management.md)的服務提供者，此服務提供者所授權的角色指派將不會顯示在此處。

## <a name="list-role-assignments-for-a-user-or-group"></a>列出使用者或群組的角色指派

若要查看在訂用帳戶中指派給使用者或群組的角色，最簡單的方式是使用 [ **Azure 角色指派** ] 窗格。

1. 在 [Azure 入口網站中，從 [Azure 入口網站] 功能表選取 [ **所有服務** ]。

1. 選取 **Azure Active Directory** 然後選取 [ **使用者** 或 **群組**]。

1. 按一下您想要列出角色指派的使用者或群組。

1. 按一下 [ **Azure 角色指派**]。

    您會在不同範圍（例如管理群組、訂用帳戶、資源群組或資源）上看到指派給所選使用者或群組的角色清單。 這份清單包含您有權讀取的所有角色指派。

    ![使用者的角色指派](./media/role-assignments-list-portal/azure-role-assignments-user.png)    

1. 若要變更訂用帳戶，請按一下 [ **訂閱** ] 清單。

## <a name="list-owners-of-a-subscription"></a>列出訂用帳戶的擁有者

已獲指派訂用帳戶 [擁有](built-in-roles.md#owner) 者角色的使用者，可以管理訂用帳戶中的所有專案。 遵循下列步驟來列出訂用帳戶的擁有者。

1. 在 Azure 入口網站中，按一下 [所有服務]  ，然後按一下 [訂用帳戶]  。

1. 按一下您要列出其擁有者的訂用帳戶。

1. 按一下 [存取控制 (IAM)]。

1. 按一下 [角色指派] 索引標籤以檢視此訂用帳戶的所有角色指派。

1. 滾動至 [ **擁有** 者] 區段，以查看已獲指派此訂用帳戶擁有者角色的所有使用者。

   ![[訂用帳戶存取控制-角色指派] 索引標籤](./media/role-assignments-list-portal/access-control-role-assignments-subscription.png)

## <a name="list-role-assignments-at-a-scope"></a>列出範圍內的角色指派

1. 在 [Azure 入口網站中，按一下 [ **所有服務** ]，然後選取範圍。 例如，您可以選取 [管理群組]****、[訂用帳戶]****、[資源群組]**** 或資源。

1. 按一下特定資源。

1. 按一下 [存取控制 (IAM)]。

1. 按一下 [角色指派]**** 索引標籤以檢視此範圍中的所有角色指派。

   ![存取控制︰[角色指派] 索引標籤](./media/role-assignments-list-portal/access-control-role-assignments.png)

   在 [角色指派] 索引標籤上，您可以看到誰在此範圍中有存取權。 請注意，某些角色的範圍限於**此資源**，而有些角色則是來自 **(繼承自)** 另一個範圍。 存取權不是特別指派給此資源群組，就是繼承自父範圍的指派。

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>列出範圍內使用者的角色指派

若要列出使用者、群組、服務主體或受控識別的存取權，您可以列出其角色指派。 遵循下列步驟來列出特定範圍內單一使用者、群組、服務主體或受控識別的角色指派。

1. 在 [Azure 入口網站中，按一下 [ **所有服務** ]，然後選取範圍。 例如，您可以選取 [管理群組]****、[訂用帳戶]****、[資源群組]**** 或資源。

1. 按一下特定資源。

1. 按一下 [存取控制 (IAM)]  。

1. 按一下 [檢查存取權]  索引標籤。

    ![存取控制 - [檢查存取權] 索引標籤](./media/role-assignments-list-portal/access-control-check-access.png)

1. 在 [尋找]  清單中，選取您要檢查其存取權的安全性主體類型。

1. 在搜尋方塊中，輸入字串以在目錄中搜尋顯示名稱、電子郵件地址或物件識別碼。

    ![檢查存取權選取清單](./media/role-assignments-list-portal/check-access-select.png)

1. 按一下安全性主體以開啟 [指派]  窗格。

    ![[指派] 窗格](./media/role-assignments-list-portal/check-access-assignments.png)

    在此窗格上，您可以看到已指派所選安全性主體的角色與範圍。 若此範圍中有任何拒絕指派，或有任何拒絕指派繼承到此範圍，它們將會被列出。

## <a name="list-role-assignments-for-a-managed-identity"></a>列出受控識別的角色指派

如先前所述，您可以使用 **存取控制 (IAM) ** 分頁，在特定範圍列出系統指派和使用者指派受控識別的角色指派。 本節說明如何只列出受控識別的角色指派。

### <a name="system-assigned-managed-identity"></a>系統指派的受控識別

1. 在 Azure 入口網站中，開啟系統指派的受控識別。

1. 在左側功能表中，按一下 [身分 **識別**]。

    ![系統指派的受控識別](./media/shared/identity-system-assigned.png)

1. 在 [ **許可權**] 底下，按一下 [ **Azure 角色指派**]。

    您會在不同範圍（例如管理群組、訂用帳戶、資源群組或資源）上看到指派給所選系統指派受控識別的角色清單。 這份清單包含您有權讀取的所有角色指派。

    ![系統指派的受控識別的角色指派](./media/shared/role-assignments-system-assigned.png)

1. 若要變更訂用帳戶，請按一下 **訂** 用帳戶清單。

### <a name="user-assigned-managed-identity"></a>使用者指派的受控識別

1. 在 Azure 入口網站中，開啟使用者指派的受控識別。

1. 按一下 [ **Azure 角色指派**]。

    您會在不同範圍（例如管理群組、訂用帳戶、資源群組或資源）上看到指派給所選使用者指派受控識別的角色清單。 這份清單包含您有權讀取的所有角色指派。

    ![系統指派的受控識別的角色指派](./media/shared/role-assignments-user-assigned.png)

1. 若要變更訂用帳戶，請按一下 **訂** 用帳戶清單。

## <a name="list-number-of-role-assignments"></a>列出角色指派數目

您在每個訂用帳戶中可以有最多 **2000** 個角色指派。 此限制包括訂用帳戶、資源群組和資源範圍的角色指派。 為了協助您追蹤這項限制，[ **角色指派** ] 索引標籤包含的圖表會列出目前訂用帳戶的角色指派數目。

![存取控制-角色指派的數目圖表](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

如果接近最大數目，而您嘗試新增更多角色指派，您將會在 [ **新增角色指派** ] 窗格中看到警告。 如需可減少角色指派數目的方法，請參閱 [疑難排解 AZURE RBAC](troubleshooting.md#azure-role-assignments-limit)。

![存取控制-新增角色指派警告](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="download-role-assignments"></a>下載角色指派

您可以在 CSV 或 JSON 格式的範圍下載角色指派。 如果您需要檢查試算表中的清單，或在遷移訂用帳戶時進行清查，這會很有説明。

當您下載角色指派時，請記住下列準則：

- 如果您沒有讀取目錄的許可權，例如目錄讀取者角色，DisplayName、SignInName 和 ObjectType 資料行會是空白的。
- 未包含已刪除安全性主體的角色指派。
- 未包含授與給傳統系統管理員的存取權。

請遵循下列步驟來下載範圍中的角色指派。

1. 在 [Azure 入口網站中，按一下 [ **所有服務** ]，然後選取您想要下載角色指派的範圍。 例如，您可以選取 [管理群組]****、[訂用帳戶]****、[資源群組]**** 或資源。

1. 按一下特定資源。

1. 按一下 [存取控制 (IAM)]。

1. 按一下 [ **下載角色指派** ] 以開啟 [下載角色指派] 窗格。

    ![存取控制-下載角色指派](./media/role-assignments-list-portal/download-role-assignments.png)

1. 使用核取方塊來選取您想要包含在所下載檔案中的角色指派。

    - **繼承** -包含繼承的角色指派給目前的範圍。
    - **在目前的範圍中** -包含目前範圍的角色指派。
    - **子** 系-在目前範圍底下的層級包含角色指派。 管理群組範圍已停用此核取方塊。

1. 選取檔案格式，它可以是以逗號分隔的值 (CSV) 或 JavaScript 物件標記法 (JSON) 。

1. 指定檔案名。

1. 按一下 [ **開始** ] 以開始下載。

    以下顯示每個檔案格式的輸出範例。

    ![將角色指派下載為 CSV](./media/role-assignments-list-portal/download-role-assignments-csv.png)

    ![將角色指派下載為 CSV](./media/role-assignments-list-portal/download-role-assignments-json.png)

## <a name="next-steps"></a>後續步驟

- [使用 Azure 入口網站新增或移除 Azure 角色指派](role-assignments-portal.md)
- [針對 Azure RBAC 進行疑難排解](troubleshooting.md)

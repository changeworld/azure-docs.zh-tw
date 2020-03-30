---
title: 提高存取權以管理所有 Azure 訂用帳戶和管理群組
description: 描述如何使用 Azure 入口網站或 REST API 提高 Azure Active Directory 中全域管理員的存取權，以管理所有訂用帳戶和管理群組。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: bagovind
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/03/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1731c630cb98ac542ebcdc7aedf07f7bb63eaec0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137468"
---
# <a name="elevate-access-to-manage-all-azure-subscriptions-and-management-groups"></a>提升管理所有 Azure 訂閱和管理組的訪問

身為 Azure Active Directory (Azure AD) 中的全域管理員，您可能沒有目錄中所有訂用帳戶與管理群組的存取權。 本文將說明您可以如何提高所有訂用帳戶和管理群組的存取權。

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="why-would-you-need-to-elevate-your-access"></a>為什麼您需要提高存取權？

如果您是全域管理員，有時可能需要執行下列作業：

- 當使用者失去存取權時，重新取得 Azure 訂用帳戶或管理群組的存取權
- 授權其他使用者或您自己存取 Azure 訂用帳戶或管理群組
- 查看組織中的所有 Azure 訂用帳戶或管理群組
- 允許自動化應用程式 (例如發票處理或稽核應用程式) 存取所有 Azure 訂用帳戶或管理群組

## <a name="how-does-elevated-access-work"></a>提升的訪問如何工作？

Azure AD 和 Azure 資源會獨立地受到保護。 也就是說，Azure AD 角色指派不會將存取權授與 Azure 資源，而 Azure 角色指派不會將存取權授與 Azure AD。 但是，如果您是 Azure AD 中的[全域管理員](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator-permissions)，則可以將自己存取權限分配給目錄中的所有 Azure 訂閱和管理組。 如果您沒有 Azure 訂用帳戶資源 (例如虛擬機器或儲存體帳戶) 的存取權，而且想要使用全域管理員權限來取得這些資源的存取權，您可以使用這項功能來達成。

當您提高存取權時，您會在根範圍上 (`/`) 獲派 Azure 中的[使用者存取管理員](built-in-roles.md#user-access-administrator)角色。這可讓您檢視所有資源，並指派目錄中任何訂用帳戶或管理群組的存取權。 可以使用 Azure PowerShell、Azure CLI 或 REST API 刪除使用者訪問管理員角色分配。

一旦您在根範圍上進行所需的變更後，您應該移除此提高的存取權。

![提高存取權](./media/elevate-access-global-admin/elevate-access.png)

## <a name="azure-portal"></a>Azure 入口網站

### <a name="elevate-access-for-a-global-administrator"></a>提高全域系統管理員的存取權

遵循這些步驟，使用 Azure 入口網站提高全域系統管理員的存取權。

1. 以全域管理員的身分登入 [Azure 入口網站](https://portal.azure.com)或 [Azure Active Directory 系統管理中心](https://aad.portal.azure.com)。

1. 搜尋並選取 [Azure Active Directory]****。

   ![選擇 Azure 活動目錄 - 螢幕截圖](./media/elevate-access-global-admin/search-for-azure-active-directory.png)

1. 在 **"管理**"下，選擇**屬性**。

   ![選擇 Azure 活動目錄屬性的屬性 - 螢幕截圖](./media/elevate-access-global-admin/azure-active-directory-properties.png)

1. 在 [Azure 資源的存取管理]**** 下，將切換開關設為 [是]****。

   ![Azure 資源的存取管理 - 螢幕擷取畫面](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   將切換設置為 **"是**"時，將為其在 Azure RBAC 中的根作用域 （/） 中分配使用者訪問管理員角色。 這會授與您權限，讓您可以在所有與 Azure AD 目錄相關聯的 Azure 訂用帳戶和管理群組中指派角色。 只有在 Azure AD 中獲派全域管理員角色的使用者可使用此切換開關。

   當您將切換開關設定為 [否]**** 時，Azure RBAC 中的使用者存取管理員角色會從使用者帳戶中移除。 您將無法在所有與 Azure AD 目錄相關聯的 Azure 訂用帳戶和管理群組中指派角色。 您只能檢視和管理已取得其存取權的 Azure 訂用帳戶和管理群組。

    > [!NOTE]
    > 如果使用[Azure AD 特權標識管理 （PIM），](../active-directory/privileged-identity-management/pim-configure.md)停用角色指派不會將此切換更改為 **"否**"。 為了保持最低特權存取權限，我們建議您在停用角色指派之前將此切換設置為 **"否**"。
    
1. 按一下 [儲存]**** 儲存您的設定。

   這個設定不是全域屬性，而且只會套用至目前登入的使用者。 您無法為全域管理員角色的所有成員提高存取權。

1. 登出再重新登入，以重新整理您的存取權。

    您現在應該有目錄中所有訂用帳戶和管理群組的存取權。 查看訪問控制項 （IAM） 窗格時，您會注意到已在根作用域中分配了"使用者訪問管理員"角色。

   ![根範圍的訂用帳戶角色指派 - 螢幕擷取畫面](./media/elevate-access-global-admin/iam-root.png)

1. 執行您需要以更高存取權執行的變更。

    如需有關指派角色的資訊，請參閱[使用 RBAC 和 Azure 入口網站來管理存取權](role-assignments-portal.md)。 如果您使用 Azure AD Privileged Identity Management (PIM)，請參閱[在 PIM 中探索要管理的 Azure 資源](../active-directory/privileged-identity-management/pim-resource-roles-discover-resources.md)或[在 PIM 中指派 Azure 資源角色](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md)。

### <a name="remove-elevated-access"></a>移除已提高的存取權

要刪除根作用域中的"使用者訪問管理員"角色指派`/`（），請按照以下步驟操作。

1. 與用於提升存取權限的同一使用者登錄。

1. 在瀏覽清單中，按一下 [Azure Active Directory]****，然後按一下 [屬性]****。

1. 將**Azure 資源的訪問管理**設置回**No**。 由於這是專屬於每個使用者的設定，因此您必須以用來提高存取權的相同使用者身分來等入。

    如果嘗試刪除"存取控制 （IAM）"窗格上的"訪問管理員"角色指派，您將看到以下消息。 要刪除角色指派，必須將切換設置為 **"否**"或使用 Azure PowerShell、Azure CLI 或 REST API。

    ![刪除具有根範圍的角色指派](./media/elevate-access-global-admin/iam-root-remove.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

### <a name="list-role-assignment-at-root-scope-"></a>在根作用域（/）列出角色指派

要列出根作用域 （）`/`上的使用者的使用者訪問管理員角色分配，請使用[Get-AzRole 分配](/powershell/module/az.resources/get-azroleassignment)命令。

```azurepowershell
Get-AzRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" `
  -and $_.SignInName -eq "<username@example.com>" -and $_.Scope -eq "/"}
```

```Example
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111
Scope              : /
DisplayName        : username
SignInName         : username@example.com
RoleDefinitionName : User Access Administrator
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9
ObjectId           : 22222222-2222-2222-2222-222222222222
ObjectType         : User
CanDelegate        : False
```

### <a name="remove-elevated-access"></a>移除已提高的存取權

要刪除您自己或根作用域中的`/`其他使用者的使用者訪問管理員角色分配 ，請按照以下步驟操作。

1. 以可移除所提高存取權的使用者身分登入。 這可以是用於提升存取權限的同一使用者，也可以是根作用域中具有提升存取權限的另一個全域管理員。

1. 請使用 [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) 命令來移除「使用者存取系統管理員」角色指派。

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName <username@example.com> `
      -RoleDefinitionName "User Access Administrator" -Scope "/"
    ```

## <a name="azure-cli"></a>Azure CLI

### <a name="list-role-assignment-at-root-scope-"></a>在根作用域（/）列出角色指派

要列出根作用域 （）`/`上的使用者的使用者訪問管理員角色分配，請使用[az 角色指派清單](/cli/azure/role/assignment#az-role-assignment-list)命令。

```azurecli
az role assignment list --role "User Access Administrator" --scope "/"
```

```Example
[
  {
    "canDelegate": null,
    "id": "/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
    "name": "11111111-1111-1111-1111-111111111111",
    "principalId": "22222222-2222-2222-2222-222222222222",
    "principalName": "username@example.com",
    "principalType": "User",
    "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "roleDefinitionName": "User Access Administrator",
    "scope": "/",
    "type": "Microsoft.Authorization/roleAssignments"
  }
]

```

### <a name="remove-elevated-access"></a>移除已提高的存取權

要刪除您自己或根作用域中的`/`其他使用者的使用者訪問管理員角色分配 ，請按照以下步驟操作。

1. 以可移除所提高存取權的使用者身分登入。 這可以是用於提升存取權限的同一使用者，也可以是根作用域中具有提升存取權限的另一個全域管理員。

1. 使用[az 角色指派刪除](/cli/azure/role/assignment#az-role-assignment-delete)命令刪除使用者訪問管理員角色分配。

    ```azurecli
    az role assignment delete --assignee username@example.com --role "User Access Administrator" --scope "/"
    ```

## <a name="rest-api"></a>REST API

### <a name="elevate-access-for-a-global-administrator"></a>提高全域系統管理員的存取權

若要使用 REST API 提高全域管理員的存取權，請使用下列基本步驟。

1. 使用 REST`elevateAccess`調用 ，它授予在根作用域 （）`/`中的使用者訪問管理員角色。

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. 建立[角色指派](/rest/api/authorization/roleassignments)以在任何範圍的指派任何角色。 下面的示例顯示了在根作用域 （） 中分配 {角色定義 ID}`/`角色的屬性 （ ：

   ```json
   { 
     "properties": {
       "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionID}",
       "principalId": "{objectID}",
       "scope": "/"
     },
     "id": "providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
     "type": "Microsoft.Authorization/roleAssignments",
     "name": "11111111-1111-1111-1111-111111111111"
   }
   ```

1. 在使用者訪問管理員時，您還可以刪除根作用域 （ ）`/`中的角色指派。

1. 移除您的「使用者存取系統管理員」權限，直到再次需要這些權限為止。

### <a name="list-role-assignments-at-root-scope-"></a>在根作用域（/）列出角色指派

您可以在根作用域 （）`/`列出使用者的所有角色指派。

- 呼叫 [GET roleAssignments](/rest/api/authorization/roleassignments/listforscope)，其中 `{objectIdOfUser}` 是您想要其擷取角色指派之使用者的物件識別碼。

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### <a name="list-deny-assignments-at-root-scope-"></a>在根作用域（/） 上列出拒絕分配

您可以在根作用域 （）`/`列出使用者的所有拒絕分配。

- 呼叫 GET denyAssignments，其中 `{objectIdOfUser}` 是您想要其擷取拒絕指派之使用者的物件識別碼。

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### <a name="remove-elevated-access"></a>移除已提高的存取權

調用`elevateAccess`時，您為自己創建角色指派，因此要撤銷這些許可權，您需要在根作用域中刪除自己的使用者訪問管理員角色分配 （`/`）

1. 呼叫 [GET roleDefinitions](/rest/api/authorization/roledefinitions/get)，其中 `roleName` 等於使用者存取系統管理員，以判斷使用者存取系統管理員角色的名稱識別碼。

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User Access Administrator'
    ```

    ```json
    {
      "value": [
        {
          "properties": {
      "roleName": "User Access Administrator",
      "type": "BuiltInRole",
      "description": "Lets you manage user access to Azure resources.",
      "assignableScopes": [
        "/"
      ],
      "permissions": [
        {
          "actions": [
            "*/read",
            "Microsoft.Authorization/*",
            "Microsoft.Support/*"
          ],
          "notActions": []
        }
      ],
      "createdOn": "0001-01-01T08:00:00.0000000Z",
      "updatedOn": "2016-05-31T23:14:04.6964687Z",
      "createdBy": null,
      "updatedBy": null
          },
          "id": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
          "type": "Microsoft.Authorization/roleDefinitions",
          "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"
        }
      ],
      "nextLink": null
    }
    ```

    儲存 `name` 參數中的識別碼，在本例中為 `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9`。

1. 您也必須列出目錄系統管理員在目錄範圍的角色指派。 請針對目錄系統管理員 (發出提高存取權呼叫者) 的 `principalId`，列出目錄範圍上的所有指派。 這會列出 objectid 目錄中的所有指派。

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
        
    >[!NOTE] 
    >目錄系統管理員不應該有許多指派，如果先前的查詢傳回太多指派，您也可以查詢只在目錄範圍層級的所有指派，然後篩選結果：`GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
            
1. 先前的呼叫會傳回角色指派清單。 請尋找符合下列條件的角色指派：範圍是 `"/"`，且 `roleDefinitionId` 結尾是您在步驟 1 中找到的角色名稱識別碼，以及 `principalId` 與目錄系統管理員的 objectId 相符。 
    
    範例角色指派：
    
    ```json
    {
      "value": [
        {
          "properties": {
            "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
            "principalId": "{objectID}",
            "scope": "/",
            "createdOn": "2016-08-17T19:21:16.3422480Z",
            "updatedOn": "2016-08-17T19:21:16.3422480Z",
            "createdBy": "22222222-2222-2222-2222-222222222222",
            "updatedBy": "22222222-2222-2222-2222-222222222222"
          },
          "id": "/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
          "type": "Microsoft.Authorization/roleAssignments",
          "name": "11111111-1111-1111-1111-111111111111"
        }
      ],
      "nextLink": null
    }
    ```
    
    同樣，從`name`參數中保存 ID，在這種情況下，1111111-1111-1111-1111-111111111111111。

1. 最後，使用角色指派識別碼來移除 `elevateAccess` 所新增的指派：

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111?api-version=2015-07-01
    ```

## <a name="next-steps"></a>後續步驟

- [了解 Azure 中的不同角色](rbac-and-directory-admin-roles.md)
- [使用 RBAC 和 REST API 管理對 Azure 資源的存取](role-assignments-rest.md)

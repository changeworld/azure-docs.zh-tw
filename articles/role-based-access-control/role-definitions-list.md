---
title: 使用 Azure 門戶、Azure 電源外殼、Azure CLI 或 REST API 在 Azure RBAC 中列出角色定義 |微軟文檔
description: 瞭解如何使用 Azure 門戶、Azure PowerShell、Azure CLI 或 REST API 在 Azure RBAC 中列出內置和自訂角色。
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
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: aa888eedc81ceb3188f801e273c70722207bf512
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062991"
---
# <a name="list-role-definitions-in-azure-rbac"></a>在 Azure RBAC 中列出角色定義

角色定義是可以執行的許可權的集合，例如讀取、寫入和刪除。 一般會直接稱之為角色。 [基於 Azure 角色的存取控制 （RBAC）](overview.md)具有 120 多個[內置角色](built-in-roles.md)，也可以創建自己的自訂角色。 本文介紹如何列出可用於授予對 Azure 資源存取權限的內置和自訂角色。

要查看 Azure 活動目錄的管理員角色清單，請參閱[Azure 活動目錄中的管理員角色許可權](../active-directory/users-groups-roles/directory-assign-admin-roles.md)。

## <a name="azure-portal"></a>Azure 入口網站

### <a name="list-all-roles"></a>列出所有角色

按照以下步驟列出 Azure 門戶中的所有角色。

1. 在 Azure 門戶中，按一下 **"所有服務**"，然後選擇任何範圍。 例如，您可以選取 [管理群組]****、[訂用帳戶]****、[資源群組]**** 或資源。

1. 按一下特定資源。

1. 按一下**存取控制 （IAM）。**

1. 按一下 [角色]**** 索引標籤以查看所有內建與自訂角色清單。

   您可以看到在當前作用域中分配給每個角色的使用者和組數。

   ![角色清單](./media/role-definitions-list/roles-list.png)

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="list-all-roles"></a>列出所有角色

要列出 Azure PowerShell 中的所有角色，請使用[獲取-AzRole定義](/powershell/module/az.resources/get-azroledefinition)。

```azurepowershell
Get-AzRoleDefinition | FT Name, Description
```

```Example
AcrImageSigner                                    acr image signer
AcrQuarantineReader                               acr quarantine data reader
AcrQuarantineWriter                               acr quarantine data writer
API Management Service Contributor                Can manage service and the APIs
API Management Service Operator Role              Can manage service but not the APIs
API Management Service Reader Role                Read-only access to service and APIs
Application Insights Component Contributor        Can manage Application Insights components
Application Insights Snapshot Debugger            Gives user permission to use Application Insights Snapshot Debugge...
Automation Job Operator                           Create and Manage Jobs using Automation Runbooks.
Automation Operator                               Automation Operators are able to start, stop, suspend, and resume ...
...
```

### <a name="list-a-role-definition"></a>列出角色定義

要列出特定角色的詳細資訊，請使用[獲取-AzRole定義](/powershell/module/az.resources/get-azroledefinition)。

```azurepowershell
Get-AzRoleDefinition <role_name>
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor"

Name             : Contributor
Id               : b24988ac-6180-42a0-ab88-20f7382dd24c
IsCustom         : False
Description      : Lets you manage everything except access to resources.
Actions          : {*}
NotActions       : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
                   Microsoft.Authorization/elevateAccess/Action}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
```

### <a name="list-a-role-definition-in-json-format"></a>以 JSON 格式列出角色定義

要以 JSON 格式列出角色，請使用[獲取-AzRole定義](/powershell/module/az.resources/get-azroledefinition)。

```azurepowershell
Get-AzRoleDefinition <role_name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | ConvertTo-Json

{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

### <a name="list-permissions-of-a-role-definition"></a>列出角色定義的許可權

要列出特定角色的許可權，請使用[獲取-AzRole定義](/powershell/module/az.resources/get-azroledefinition)。

```azurepowershell
Get-AzRoleDefinition <role_name> | FL Actions, NotActions
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | FL Actions, NotActions

Actions    : {*}
NotActions : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
             Microsoft.Authorization/elevateAccess/Action,
             Microsoft.Blueprint/blueprintAssignments/write...}
```

```azurepowershell
(Get-AzRoleDefinition <role_name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Contributor").Actions

Microsoft.Authorization/*/read
Microsoft.Compute/availabilitySets/*
Microsoft.Compute/locations/*
Microsoft.Compute/virtualMachines/*
Microsoft.Compute/virtualMachineScaleSets/*
Microsoft.DevTestLab/schedules/*
Microsoft.Insights/alertRules/*
Microsoft.Network/applicationGateways/backendAddressPools/join/action
Microsoft.Network/loadBalancers/backendAddressPools/join/action
...
```

## <a name="azure-cli"></a>Azure CLI

### <a name="list-all-roles"></a>列出所有角色

要列出 Azure CLI 中的所有角色，請使用[az 角色定義清單](/cli/azure/role/definition#az-role-definition-list)。

```azurecli
az role definition list
```

下列範例會列出所有可用角色定義的名稱和描述：

```azurecli
az role definition list --output json | jq '.[] | {"roleName":.roleName, "description":.description}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs"
}

...
```

下面的示例列出了所有內置角色。

```azurecli
az role definition list --custom-role-only false --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs",
  "roleType": "BuiltInRole"
}

...
```

### <a name="list-a-role-definition"></a>列出角色定義

要列出角色的詳細資訊，請使用[az 角色定義清單](/cli/azure/role/definition#az-role-definition-list)。

```azurecli
az role definition list --name <role_name>
```

下列範例會列出「參與者」** 的角色定義：

```azurecli
az role definition list --name "Contributor"
```

```Output
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="list-permissions-of-a-role-definition"></a>列出角色定義的許可權

下面的示例僅列出*參與者*角色*的操作**而不是操作*。

```azurecli
az role definition list --name "Contributor" --output json | jq '.[] | {"actions":.permissions[0].actions, "notActions":.permissions[0].notActions}'
```

```Output
{
  "actions": [
    "*"
  ],
  "notActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ]
}
```

下面的示例僅列出了*虛擬機器參與者*角色的操作。

```azurecli
az role definition list --name "Virtual Machine Contributor" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
  "Microsoft.Authorization/*/read",
  "Microsoft.Compute/availabilitySets/*",
  "Microsoft.Compute/locations/*",
  "Microsoft.Compute/virtualMachines/*",
  "Microsoft.Compute/virtualMachineScaleSets/*",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
  "Microsoft.Network/loadBalancers/backendAddressPools/join/action",

  ...

  "Microsoft.Storage/storageAccounts/listKeys/action",
  "Microsoft.Storage/storageAccounts/read"
]
```

## <a name="rest-api"></a>REST API

### <a name="list-role-definitions"></a>列出角色定義

要列出角色定義，請使用[角色定義 - 列出](/rest/api/authorization/roledefinitions/list)REST API。 若要精簡您的結果，請指定範圍和選擇性篩選條件。

1. 從下列要求著手：

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?$filter={$filter}&api-version=2015-07-01
    ```

1. 在 URI 中，將 *[作用域]* 替換為要列出角色定義的範圍。

    > [!div class="mx-tableFixed"]
    > | 影響範圍 | 類型 |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理群組 |
    > | `subscriptions/{subscriptionId1}` | 訂用帳戶 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | 資源群組 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | 資源 |

    在前面的示例中，microsoft.web 是引用應用服務實例的資來源提供者。 同樣，您可以使用任何其他資來源提供者並指定作用域。 有關詳細資訊，請參閱[Azure 資來源提供者和類型](../azure-resource-manager/management/resource-providers-and-types.md)以及支援的[Azure 資源管理器資源管理器資源管理器操作](resource-provider-operations.md)。  
     
1. 將 *[篩選器]* 替換為要應用於篩選角色定義清單的條件。

    > [!div class="mx-tableFixed"]
    > | Filter | 描述 |
    > | --- | --- |
    > | `$filter=atScopeAndBelow()` | 列出指定作用域和任何子作用域的角色定義。 |
    > | `$filter=type+eq+'{type}'` | 列出指定類型的角色定義。 角色類型可以是`CustomRole`或`BuiltInRole`。 |

### <a name="list-a-role-definition"></a>列出角色定義

要列出特定角色的詳細資訊，請使用[角色定義 - 獲取](/rest/api/authorization/roledefinitions/get)或[角色定義 - 獲取 ID](/rest/api/authorization/roledefinitions/getbyid) REST API。

1. 從下列要求著手：

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    對於目錄級角色定義，可以使用此請求：

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. 在 URI 中，將 *[作用域]* 替換為要列出角色定義的範圍。

    > [!div class="mx-tableFixed"]
    > | 影響範圍 | 類型 |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理群組 |
    > | `subscriptions/{subscriptionId1}` | 訂用帳戶 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | 資源群組 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | 資源 |
     
1. 將 *{roleDefinitionId}* 取代為角色定義識別碼。

## <a name="next-steps"></a>後續步驟

- [適用於 Azure 資源的內建角色](built-in-roles.md)
- [適用於 Azure 資源的自訂角色](custom-roles.md)
- [使用 Azure RBAC 和 Azure 門戶列出角色指派](role-assignments-list-portal.md)
- [使用 Azure RBAC 和 Azure 門戶添加或刪除角色指派](role-assignments-portal.md)

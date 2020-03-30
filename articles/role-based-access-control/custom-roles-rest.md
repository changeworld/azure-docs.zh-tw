---
title: 使用 REST API 為 Azure 資源創建或更新自訂角色
description: 瞭解如何使用 REST API 使用基於角色的存取控制 （RBAC） 列出、創建、更新或刪除自訂角色。
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: fda0400310f46da64322654c42af75521746d679
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062198"
---
# <a name="create-or-update-custom-roles-for-azure-resources-using-the-rest-api"></a>使用 REST API 為 Azure 資源創建或更新自訂角色

> [!IMPORTANT]
> 將管理組添加到`AssignableScopes`當前處於預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

如果[Azure 資源的內置角色](built-in-roles.md)不能滿足組織的特定需求，則可以創建自己的自訂角色。 本文介紹如何使用 REST API 列出、創建、更新或刪除自訂角色。

## <a name="list-custom-roles"></a>列出自訂角色

要列出目錄中的所有自訂角色，請使用[角色定義 - 列出](/rest/api/authorization/roledefinitions/list)REST API。

1. 從下列要求著手：

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. 將 *[篩選器]* 替換為角色類型。

    > [!div class="mx-tableFixed"]
    > | Filter | 描述 |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | 基於自訂角色類型的篩選器 |

## <a name="list-custom-roles-at-a-scope"></a>在作用域中列出自訂角色

要在作用域中列出自訂角色，請使用[角色定義 - 列出](/rest/api/authorization/roledefinitions/list)REST API。

1. 從下列要求著手：

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. 在 URI 內，將 *{scope}* 取代為要列出角色的範圍。

    > [!div class="mx-tableFixed"]
    > | 影響範圍 | 類型 |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | 訂用帳戶 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | 資源群組 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | 資源 |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理群組 |

1. 將 *[篩選器]* 替換為角色類型。

    > [!div class="mx-tableFixed"]
    > | Filter | 描述 |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | 基於自訂角色類型的篩選器 |

## <a name="list-a-custom-role-definition-by-name"></a>按名稱列出自訂角色定義

要按自訂角色的顯示名稱獲取有關該角色的資訊，請使用[角色定義 - 獲取](/rest/api/authorization/roledefinitions/get)REST API。

1. 從下列要求著手：

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. 在 URI 內，將 *{scope}* 取代為要列出角色的範圍。

    > [!div class="mx-tableFixed"]
    > | 影響範圍 | 類型 |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | 訂用帳戶 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | 資源群組 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | 資源 |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理群組 |

1. 將 *[篩選器]* 替換為角色的顯示名稱。

    > [!div class="mx-tableFixed"]
    > | Filter | 描述 |
    > | --- | --- |
    > | `$filter=roleName+eq+'{roleDisplayName}'` | 使用角色確切顯示名稱的 URL 編碼型式。 例如 `$filter=roleName+eq+'Virtual%20Machine%20Contributor'` |

## <a name="list-a-custom-role-definition-by-id"></a>按 ID 列出自訂角色定義

要通過其唯一識別碼獲取有關自訂角色的資訊，請使用[角色定義 - 獲取](/rest/api/authorization/roledefinitions/get)REST API。

1. 使用[角色定義 - 列出](/rest/api/authorization/roledefinitions/list) REST API 來取得角色的 GUID 識別碼。

1. 從下列要求著手：

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. 在 URI 內，將 *{scope}* 取代為要列出角色的範圍。

    > [!div class="mx-tableFixed"]
    > | 影響範圍 | 類型 |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | 訂用帳戶 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | 資源群組 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | 資源 |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理群組 |

1. 將 *{角色定義 Id}* 替換為角色定義的 GUID 識別碼。

## <a name="create-a-custom-role"></a>建立自訂角色

若要建立自訂角色，請使用[角色定義 - 建立或更新](/rest/api/authorization/roledefinitions/createorupdate) REST API。 要調用此 API，必須與已分配的角色`Microsoft.Authorization/roleDefinitions/write`具有所有 許可權的使用者登錄。 `assignableScopes` 在內置角色中，只有[擁有者](built-in-roles.md#owner)和[使用者訪問管理員](built-in-roles.md#user-access-administrator)包含此許可權。

1. 檢閱可用來為自訂角色建立權限的[資源提供者作業](resource-provider-operations.md)清單。

1. 使用 GUID 工具來產生將用來作為自訂角色識別碼的唯一識別碼。 此識別碼的格式：`00000000-0000-0000-0000-000000000000`

1. 從下列要求和本文著手：

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId1}",
          "/subscriptions/{subscriptionId2}",
          "/subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}",
          "/subscriptions/{subscriptionId2}/resourceGroups/{resourceGroup2}",
          "/providers/Microsoft.Management/managementGroups/{groupId1}"
        ]
      }
    }
    ```

1. 在 URI 內，將 *{scope}* 取代為自訂角色的第一個 `assignableScopes`。

    > [!div class="mx-tableFixed"]
    > | 影響範圍 | 類型 |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | 訂用帳戶 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | 資源群組 |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理群組 |

1. 將 *{角色定義 Id}* 替換為自訂角色的 GUID 識別碼。

1. 在請求正文中，將 *[角色定義 Id]* 替換為 GUID 識別碼。

1. 如果是`assignableScopes`訂閱或資源組，請將 *[訂閱 Id]* 或 *[資源組]* 實例替換為識別碼。

1. 如果是`assignableScopes`管理組，請將 *{groupId}* 實例替換為管理組識別碼。 將管理組添加到`assignableScopes`當前處於預覽狀態。

1. 在 `actions` 屬性中，新增角色允許執行的作業。

1. 在 `notActions` 屬性中，新增從允許的 `actions` 中排除的作業。

1. 在 `roleName` 和 `description` 屬性中，指定唯一角色名稱和描述。 如需有關屬性的資訊，請參閱[自訂角色](custom-roles.md)。

    以下顯示要求本文範例：

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.ResourceHealth/availabilityStatuses/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="update-a-custom-role"></a>更新自訂角色

若要更新自訂角色，請使用[角色定義 - 建立或更新](/rest/api/authorization/roledefinitions/createorupdate) REST API。 要調用此 API，必須與已分配的角色`Microsoft.Authorization/roleDefinitions/write`具有所有 許可權的使用者登錄。 `assignableScopes` 在內置角色中，只有[擁有者](built-in-roles.md#owner)和[使用者訪問管理員](built-in-roles.md#user-access-administrator)包含此許可權。

1. 使用[角色定義 - 列出](/rest/api/authorization/roledefinitions/list)或[角色定義 - 取得](/rest/api/authorization/roledefinitions/get) REST API 來取得自訂角色的相關資訊。 有關詳細資訊，請參閱前面的["清單自訂角色](#list-custom-roles)"部分。

1. 從下列要求著手：

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. 在 URI 內，將 *{scope}* 取代為自訂角色的第一個 `assignableScopes`。

    > [!div class="mx-tableFixed"]
    > | 影響範圍 | 類型 |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | 訂用帳戶 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | 資源群組 |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理群組 |

1. 將 *{角色定義 Id}* 替換為自訂角色的 GUID 識別碼。

1. 根據自訂角色的相關資訊，以下列格式建立要求本文：

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId1}",
          "/subscriptions/{subscriptionId2}",
          "/subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}",
          "/subscriptions/{subscriptionId2}/resourceGroups/{resourceGroup2}",
          "/providers/Microsoft.Management/managementGroups/{groupId1}"
        ]
      }
    }
    ```

1. 以您想要對自訂角色進行的變更更新要求本文。

    以下顯示已新增診斷設定動作的要求本文範例：

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.ResourceHealth/availabilityStatuses/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Insights/diagnosticSettings/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="delete-a-custom-role"></a>刪除自訂角色

若要刪除自訂角色，請使用[角色定義 - 刪除](/rest/api/authorization/roledefinitions/delete) REST API。 要調用此 API，必須與已分配的角色`Microsoft.Authorization/roleDefinitions/delete`具有所有 許可權的使用者登錄。 `assignableScopes` 在內置角色中，只有[擁有者](built-in-roles.md#owner)和[使用者訪問管理員](built-in-roles.md#user-access-administrator)包含此許可權。

1. 使用[角色定義 - 列出](/rest/api/authorization/roledefinitions/list)或[角色定義 - 取得](/rest/api/authorization/roledefinitions/get) REST API 來取得自訂角色的 GUID 識別碼。 有關詳細資訊，請參閱前面的["清單自訂角色](#list-custom-roles)"部分。

1. 從下列要求著手：

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. 在 URI 內，將 *{scope}* 取代為要刪除自訂角色的範圍。

    > [!div class="mx-tableFixed"]
    > | 影響範圍 | 類型 |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | 訂用帳戶 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | 資源群組 |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理群組 |

1. 將 *{角色定義 Id}* 替換為自訂角色的 GUID 識別碼。

## <a name="next-steps"></a>後續步驟

- [適用於 Azure 資源的自訂角色](custom-roles.md)
- [使用 RBAC 和 REST API 管理對 Azure 資源的存取](role-assignments-rest.md)
- [Azure REST API 參考](/rest/api/azure/)

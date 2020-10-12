---
title: 使用 REST API 建立或更新 Azure 自訂角色-Azure RBAC
description: 瞭解如何使用 REST API 和 Azure 角色型存取控制來列出、建立、更新或刪除 azure 自訂角色 (Azure RBAC) 。
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
ms.topic: how-to
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b459f44308827308c28687db3c3fc33df470ea8d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84790181"
---
# <a name="create-or-update-azure-custom-roles-using-the-rest-api"></a>使用 REST API 建立或更新 Azure 自訂角色

> [!IMPORTANT]
> 將管理群組新增至的 `AssignableScopes` 功能目前為預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

如果 [Azure 內建的角色](built-in-roles.md)無法滿足您組織的特定需求，您可以建立自己的自訂角色。 本文說明如何使用 REST API 列出、建立、更新或刪除自訂角色。

## <a name="list-custom-roles"></a>列出自訂角色

若要列出目錄中的所有自訂角色，請使用 [角色定義清單](/rest/api/authorization/roledefinitions/list) REST API。

1. 從下列要求著手：

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. 將 *{filter}* 取代為角色類型。

    > [!div class="mx-tableFixed"]
    > | Filter | 說明 |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | 根據 CustomRole 類型篩選 |

## <a name="list-custom-roles-at-a-scope"></a>列出範圍內的自訂角色

若要列出某個範圍的自訂角色，請使用 [角色定義清單](/rest/api/authorization/roledefinitions/list) REST API。

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

1. 將 *{filter}* 取代為角色類型。

    > [!div class="mx-tableFixed"]
    > | Filter | 說明 |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | 根據 CustomRole 類型篩選 |

## <a name="list-a-custom-role-definition-by-name"></a>依名稱列出自訂角色定義

若要依據自訂角色的顯示名稱取得相關資訊，請使用 [角色定義-get](/rest/api/authorization/roledefinitions/get) REST API。

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

1. 將 *{filter}* 取代為角色的顯示名稱。

    > [!div class="mx-tableFixed"]
    > | Filter | 說明 |
    > | --- | --- |
    > | `$filter=roleName+eq+'{roleDisplayName}'` | 使用角色確切顯示名稱的 URL 編碼型式。 例如 `$filter=roleName+eq+'Virtual%20Machine%20Contributor'` |

## <a name="list-a-custom-role-definition-by-id"></a>依識別碼列出自訂角色定義

若要根據自訂角色的唯一識別碼取得相關資訊，請使用 [角色定義-get](/rest/api/authorization/roledefinitions/get) REST API。

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

1. 將 *{roleDefinitionId}* 取代為角色定義的 GUID 識別碼。

## <a name="create-a-custom-role"></a>建立自訂角色

若要建立自訂角色，請使用[角色定義 - 建立或更新](/rest/api/authorization/roledefinitions/createorupdate) REST API。 若要呼叫此 API，您必須使用已指派具有擁有權限之角色的使用者登入 `Microsoft.Authorization/roleDefinitions/write` `assignableScopes` 。 在內建角色中，只有「 [擁有](built-in-roles.md#owner) 者」和「 [使用者存取系統管理員](built-in-roles.md#user-access-administrator) 」才會包含此許可權。

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

1. 以自訂角色的 GUID 識別碼取代 *{roleDefinitionId}* 。

1. 在要求主體中，將 *{roleDefinitionId}* 取代為 GUID 識別碼。

1. 如果 `assignableScopes` 是訂用帳戶或資源群組，請將 *{subscriptionId}* 或 *{resourceGroup}* 實例取代為您的識別碼。

1. 如果 `assignableScopes` 是管理群組，請將 *{groupId}* 實例取代為您的管理群組識別碼。 將管理群組新增至的 `assignableScopes` 功能目前為預覽狀態。

1. 在 `actions` 屬性中，新增角色允許執行的作業。

1. 在 `notActions` 屬性中，新增從允許的 `actions` 中排除的作業。

1. 在 `roleName` 和 `description` 屬性中，指定唯一角色名稱和描述。 如需屬性的詳細資訊，請參閱 [Azure 自訂角色](custom-roles.md)。

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

若要更新自訂角色，請使用[角色定義 - 建立或更新](/rest/api/authorization/roledefinitions/createorupdate) REST API。 若要呼叫此 API，您必須使用已指派具有擁有權限之角色的使用者登入 `Microsoft.Authorization/roleDefinitions/write` `assignableScopes` 。 在內建角色中，只有「 [擁有](built-in-roles.md#owner) 者」和「 [使用者存取系統管理員](built-in-roles.md#user-access-administrator) 」才會包含此許可權。

1. 使用[角色定義 - 列出](/rest/api/authorization/roledefinitions/list)或[角色定義 - 取得](/rest/api/authorization/roledefinitions/get) REST API 來取得自訂角色的相關資訊。 如需詳細資訊，請參閱先前的 [清單自訂角色](#list-custom-roles) 一節。

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

1. 以自訂角色的 GUID 識別碼取代 *{roleDefinitionId}* 。

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

若要刪除自訂角色，請使用[角色定義 - 刪除](/rest/api/authorization/roledefinitions/delete) REST API。 若要呼叫此 API，您必須使用已指派具有擁有權限之角色的使用者登入 `Microsoft.Authorization/roleDefinitions/delete` `assignableScopes` 。 在內建角色中，只有「 [擁有](built-in-roles.md#owner) 者」和「 [使用者存取系統管理員](built-in-roles.md#user-access-administrator) 」才會包含此許可權。

1. 使用[角色定義 - 列出](/rest/api/authorization/roledefinitions/list)或[角色定義 - 取得](/rest/api/authorization/roledefinitions/get) REST API 來取得自訂角色的 GUID 識別碼。 如需詳細資訊，請參閱先前的 [清單自訂角色](#list-custom-roles) 一節。

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

1. 以自訂角色的 GUID 識別碼取代 *{roleDefinitionId}* 。

## <a name="next-steps"></a>接下來的步驟

- [Azure 自訂角色](custom-roles.md) (機器翻譯)
- [使用 REST API 新增或移除 Azure 角色指派](role-assignments-rest.md)
- [Azure REST API 參考](/rest/api/azure/)

---
title: 使用 REST API 建立或更新 Azure 資源的自訂角色
description: 瞭解如何使用 REST API，為 Azure 資源的角色型存取控制（RBAC）列出、建立、更新或刪除自訂角色。
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
ms.date: 04/18/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 145bc45e1b7faeddc23cf5f0662337e15ab51c29
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78392468"
---
# <a name="create-or-update-custom-roles-for-azure-resources-using-the-rest-api"></a>使用 REST API 建立或更新 Azure 資源的自訂角色

如果[適用於 Azure 資源的內建角色](built-in-roles.md)無法滿足您組織的特定需求，您可以建立自己的自訂角色。 本文說明如何使用 REST API 列出、建立、更新或刪除自訂角色。

## <a name="list-custom-roles"></a>列出自訂角色

若要列出目錄中的所有自訂角色，請使用[角色定義-list](/rest/api/authorization/roledefinitions/list) REST API。

1. 從下列要求著手：

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. 將 *{filter}* 取代為角色類型。

    | Filter | 描述 |
    | --- | --- |
    | `$filter=type%20eq%20'CustomRole'` | 根據 CustomRole 類型篩選 |

## <a name="list-custom-roles-at-a-scope"></a>列出範圍中的自訂角色

若要列出範圍中的自訂角色，請使用[角色定義-list](/rest/api/authorization/roledefinitions/list) REST API。

1. 從下列要求著手：

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. 在 URI 內，將 *{scope}* 取代為要列出角色的範圍。

    | 影響範圍 | 類型 |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | 訂用帳戶 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | 資源群組 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | 資源 |

1. 將 *{filter}* 取代為角色類型。

    | Filter | 描述 |
    | --- | --- |
    | `$filter=type%20eq%20'CustomRole'` | 根據 CustomRole 類型篩選 |

## <a name="list-a-custom-role-definition-by-name"></a>依名稱列出自訂角色定義

若要依其顯示名稱取得自訂角色的相關資訊，請使用[角色定義-get](/rest/api/authorization/roledefinitions/get) REST API。

1. 從下列要求著手：

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. 在 URI 內，將 *{scope}* 取代為要列出角色的範圍。

    | 影響範圍 | 類型 |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | 訂用帳戶 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | 資源群組 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | 資源 |

1. 將 *{filter}* 取代為角色的顯示名稱。

    | Filter | 描述 |
    | --- | --- |
    | `$filter=roleName%20eq%20'{roleDisplayName}'` | 使用角色確切顯示名稱的 URL 編碼型式。 例如 `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

## <a name="list-a-custom-role-definition-by-id"></a>依識別碼列出自訂角色定義

若要依其唯一識別碼取得自訂角色的相關資訊，請使用[角色定義-get](/rest/api/authorization/roledefinitions/get) REST API。

1. 使用[角色定義 - 列出](/rest/api/authorization/roledefinitions/list) REST API 來取得角色的 GUID 識別碼。

1. 從下列要求著手：

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. 在 URI 內，將 *{scope}* 取代為要列出角色的範圍。

    | 影響範圍 | 類型 |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | 訂用帳戶 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | 資源群組 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | 資源 |

1. 將 *{roleDefinitionId}* 取代為角色定義的 GUID 識別碼。

## <a name="create-a-custom-role"></a>建立自訂角色

若要建立自訂角色，請使用[角色定義 - 建立或更新](/rest/api/authorization/roledefinitions/createorupdate) REST API。 若要呼叫此 API，您必須使用已指派角色的使用者登入，而該角色具有所有 `assignableScopes`的 [`Microsoft.Authorization/roleDefinitions/write`] 許可權。 在內建角色中，只有[擁有](built-in-roles.md#owner)者和[使用者存取系統管理員](built-in-roles.md#user-access-administrator)才會包含此許可權。

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
          "/subscriptions/{subscriptionId}"
        ]
      }
    }
    ```

1. 在 URI 內，將 *{scope}* 取代為自訂角色的第一個 `assignableScopes`。

    | 影響範圍 | 類型 |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | 訂用帳戶 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | 資源群組 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | 資源 |

1. 將 *{roleDefinitionId}* 取代為自訂角色的 GUID 識別碼。

1. 在要求本文內的 `assignableScopes` 屬性中，將 *{roleDefinitionId}* 取代為角色定義的 GUID 識別碼。

1. 將 *{subscriptionId}* 取代為您的訂用帳戶識別碼。

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
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ]
      }
    }
    ```

## <a name="update-a-custom-role"></a>更新自訂角色

若要更新自訂角色，請使用[角色定義 - 建立或更新](/rest/api/authorization/roledefinitions/createorupdate) REST API。 若要呼叫此 API，您必須使用已指派角色的使用者登入，而該角色具有所有 `assignableScopes`的 [`Microsoft.Authorization/roleDefinitions/write`] 許可權。 在內建角色中，只有[擁有](built-in-roles.md#owner)者和[使用者存取系統管理員](built-in-roles.md#user-access-administrator)才會包含此許可權。

1. 使用[角色定義 - 列出](/rest/api/authorization/roledefinitions/list)或[角色定義 - 取得](/rest/api/authorization/roledefinitions/get) REST API 來取得自訂角色的相關資訊。 如需詳細資訊，請參閱先前的[清單自訂角色](#list-custom-roles)一節。

1. 從下列要求著手：

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. 在 URI 內，將 *{scope}* 取代為自訂角色的第一個 `assignableScopes`。

    | 影響範圍 | 類型 |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | 訂用帳戶 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | 資源群組 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | 資源 |

1. 將 *{roleDefinitionId}* 取代為自訂角色的 GUID 識別碼。

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
          "/subscriptions/{subscriptionId}"
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
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ]
      }
    }
    ```

## <a name="delete-a-custom-role"></a>刪除自訂角色

若要刪除自訂角色，請使用[角色定義 - 刪除](/rest/api/authorization/roledefinitions/delete) REST API。 若要呼叫此 API，您必須使用已指派角色的使用者登入，而該角色具有所有 `assignableScopes`的 [`Microsoft.Authorization/roleDefinitions/delete`] 許可權。 在內建角色中，只有[擁有](built-in-roles.md#owner)者和[使用者存取系統管理員](built-in-roles.md#user-access-administrator)才會包含此許可權。

1. 使用[角色定義 - 列出](/rest/api/authorization/roledefinitions/list)或[角色定義 - 取得](/rest/api/authorization/roledefinitions/get) REST API 來取得自訂角色的 GUID 識別碼。 如需詳細資訊，請參閱先前的[清單自訂角色](#list-custom-roles)一節。

1. 從下列要求著手：

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. 在 URI 內，將 *{scope}* 取代為要刪除自訂角色的範圍。

    | 影響範圍 | 類型 |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | 訂用帳戶 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | 資源群組 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | 資源 |

1. 將 *{roleDefinitionId}* 取代為自訂角色的 GUID 識別碼。

## <a name="next-steps"></a>後續步驟

- [適用於 Azure 資源的自訂角色](custom-roles.md)
- [使用 RBAC 和 REST API 管理對 Azure 資源的存取](role-assignments-rest.md)
- [Azure REST API 參考](/rest/api/azure/)

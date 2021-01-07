---
title: 使用 REST API 新增或移除 Azure 角色指派-Azure RBAC
description: 瞭解如何使用 REST API 和 Azure 角色型存取控制 (Azure RBAC) ，為使用者、群組、服務主體或受控識別授與 Azure 資源的存取權。
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
ms.date: 05/06/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: e4f230663e0eeddcf874c24e5041653f241f481c
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964264"
---
# <a name="add-or-remove-azure-role-assignments-using-the-rest-api"></a>使用 REST API 新增或移除 Azure 角色指派

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] 本文說明如何使用 REST API 指派角色。

## <a name="prerequisites"></a>先決條件

若要新增或移除角色指派，您必須具有：

- `Microsoft.Authorization/roleAssignments/write` 和 `Microsoft.Authorization/roleAssignments/delete` 權限，例如[使用者存取系統管理員](built-in-roles.md#user-access-administrator)或[擁有者](built-in-roles.md#owner)

## <a name="add-a-role-assignment"></a>新增角色指派

在 Azure RBAC 中，若要授與存取權，您可以新增角色指派。 若要新增角色指派，請使用 [角色指派-建立](/rest/api/authorization/roleassignments/create) REST API 並指定安全性主體、角色定義和範圍。 若要呼叫這個 API，您必須具有 `Microsoft.Authorization/roleAssignments/write` 作業的存取權。 在內建角色中，只有[擁有者](built-in-roles.md#owner)和[使用者存取系統管理員](built-in-roles.md#user-access-administrator)會獲得這項作業的存取權。

1. 使用[角色定義 - 列出](/rest/api/authorization/roledefinitions/list) REST API 或參閱[內建角色](built-in-roles.md)，以取得您要指派角色定義的識別碼。

1. 使用 GUID 工具來產生將用於角色指派識別碼的唯一識別碼。 此識別碼的格式：`00000000-0000-0000-0000-000000000000`

1. 從下列要求和本文著手：

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```

1. 在 URI 中，將 *{scope}* 取代為角色指派的範圍。

    > [!div class="mx-tableFixed"]
    > | 影響範圍 | 類型 |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理群組 |
    > | `subscriptions/{subscriptionId1}` | 訂用帳戶 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | 資源群組 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | 資源 |

    在先前的範例中，microsoft 是參考 App Service 實例的資源提供者。 同樣地，您可以使用任何其他資源提供者，並指定範圍。 如需詳細資訊，請參閱 [Azure 資源提供者和類型](../azure-resource-manager/management/resource-providers-and-types.md) ，以及支援的 [azure 資源提供者作業](resource-provider-operations.md)。  

1. 以角色指派的 GUID 識別碼取代 *{roleAssignmentId}* 。

1. 在要求主體中，將 *{scope}* 取代為角色指派的範圍。

    > [!div class="mx-tableFixed"]
    > | 影響範圍 | 類型 |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理群組 |
    > | `subscriptions/{subscriptionId1}` | 訂用帳戶 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | 資源群組 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | 資源 |

1. 將 *{roleDefinitionId}* 取代為角色定義識別碼。

1. 將 *{principalId}* 取代為使用者、群組或服務主體 (將獲得角色指派) 的物件識別碼。

下列要求和主體會將「 [備份讀者](built-in-roles.md#backup-reader) 」角色指派給訂用帳戶範圍中的使用者：

```http
PUT https://management.azure.com/subscriptions/{subscriptionId1}/providers/microsoft.authorization/roleassignments/{roleAssignmentId1}?api-version=2015-07-01
```

```json
{
  "properties": {
    "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
    "principalId": "{objectId1}"
  }
}
```

以下顯示輸出的範例：

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
        "principalId": "{objectId1}",
        "scope": "/subscriptions/{subscriptionId1}",
        "createdOn": "2020-05-06T23:55:23.7679147Z",
        "updatedOn": "2020-05-06T23:55:23.7679147Z",
        "createdBy": null,
        "updatedBy": "{updatedByObjectId1}"
    },
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "{roleAssignmentId1}"
}
```

## <a name="remove-a-role-assignment"></a>移除角色指派

在 Azure RBAC 中，若要移除存取權，您可以移除角色指派。 若要移除角色指派，請使用[角色指派 - 刪除](/rest/api/authorization/roleassignments/delete) REST API。 若要呼叫這個 API，您必須具有 `Microsoft.Authorization/roleAssignments/delete` 作業的存取權。 在內建角色中，只有[擁有者](built-in-roles.md#owner)和[使用者存取系統管理員](built-in-roles.md#user-access-administrator)會獲得這項作業的存取權。

1. 取得角色指派識別碼 (GUID)。 您第一次建立角色指派時會傳回這個識別碼，或者可藉由列出角色指派來取得它。

1. 從下列要求著手：

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2015-07-01
    ```

1. 在 URI 中，將 *{scope}* 取代為要移除角色指派的範圍。

    > [!div class="mx-tableFixed"]
    > | 影響範圍 | 類型 |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理群組 |
    > | `subscriptions/{subscriptionId1}` | 訂用帳戶 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | 資源群組 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | 資源 |

1. 以角色指派的 GUID 識別碼取代 *{roleAssignmentId}* 。

下列要求會在訂用帳戶範圍中移除指定的角色指派：

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId1}/providers/microsoft.authorization/roleassignments/{roleAssignmentId1}?api-version=2015-07-01
```

以下顯示輸出的範例：

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
        "principalId": "{objectId1}",
        "scope": "/subscriptions/{subscriptionId1}",
        "createdOn": "2020-05-06T23:55:24.5379478Z",
        "updatedOn": "2020-05-06T23:55:24.5379478Z",
        "createdBy": "{createdByObjectId1}",
        "updatedBy": "{updatedByObjectId1}"
    },
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "{roleAssignmentId1}"
}
```

## <a name="next-steps"></a>後續步驟

- [使用 REST API 列出 Azure 角色指派](role-assignments-list-rest.md)
- [使用 Resource Manager 範本和 Resource Manager 部署資源 REST API](../azure-resource-manager/templates/deploy-rest.md)
- [Azure REST API 參考](/rest/api/azure/)
- [使用 REST API 建立或更新 Azure 自訂角色](custom-roles-rest.md)

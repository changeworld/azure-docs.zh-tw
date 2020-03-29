---
title: 使用 Azure RBAC 和 REST API 列出角色指派
description: 瞭解如何確定使用者、組、服務主體或託管標識可以使用基於 Azure 的角色存取控制 （RBAC） 和 REST API 訪問哪些資源。
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
ms.openlocfilehash: a494e7fd4c9fb79faa6a1d8cb2c3c871796ccdc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062163"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-rest-api"></a>使用 Azure RBAC 和 REST API 列出角色指派

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]本文介紹如何使用 REST API 列出角色指派。

> [!NOTE]
> 如果您的組織將管理功能外包給使用[Azure 委派資源的](../lighthouse/concepts/azure-delegated-resource-management.md)服務提供者，則該服務提供者授權的角色指派將不會在此處顯示。

## <a name="list-role-assignments"></a>列出角色指派

在 RBAC 中，若要列出存取權，您可以列出角色指派。 若要列出角色指派，請使用其中一個[角色指派 - 列出](/rest/api/authorization/roleassignments/list) REST API。 若要精簡您的結果，請指定範圍和選擇性篩選條件。

1. 從下列要求著手：

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. 在 URI 中，將 *{scope}* 取代為要列出角色指派的範圍。

    > [!div class="mx-tableFixed"]
    > | 影響範圍 | 類型 |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理群組 |
    > | `subscriptions/{subscriptionId1}` | 訂用帳戶 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | 資源群組 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | 資源 |

    在前面的示例中，microsoft.web 是引用應用服務實例的資來源提供者。 同樣，您可以使用任何其他資來源提供者並指定作用域。 有關詳細資訊，請參閱[Azure 資來源提供者和類型](../azure-resource-manager/management/resource-providers-and-types.md)以及支援的[Azure 資源管理器資源管理器資源管理器操作](resource-provider-operations.md)。  
     
1. 將 *{filter}* 取代為您要針對角色指派清單篩選套用的條件。

    > [!div class="mx-tableFixed"]
    > | Filter | 描述 |
    > | --- | --- |
    > | `$filter=atScope()` | 僅列出指定作用域的角色指派，不包括子作用域中的角色指派。 |
    > | `$filter=assignedTo('{objectId}')` | 列出指定使用者或服務主體的角色指派。<br/>如果使用者是具有角色指派的組的成員，則還會列出該角色指派。 此篩選器對於組是傳遞的，這意味著如果使用者是組的成員，並且該組是具有角色指派的另一個組的成員，則還會列出該角色指派。<br/>此篩選器僅接受使用者或服務主體的物件識別碼。 不能傳遞組的物件識別碼。 |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | 列出指定使用者或服務主體和指定作用域的角色指派。 |
    > | `$filter=principalId+eq+'{objectId}'` | 列出指定使用者、組或服務主體的角色指派。 |

## <a name="next-steps"></a>後續步驟

- [使用 Azure RBAC 和 REST API 添加或刪除角色指派](role-assignments-rest.md)
- [Azure REST API 參考](/rest/api/azure/)

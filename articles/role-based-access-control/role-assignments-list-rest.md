---
title: 使用 REST API 列出 Azure 角色指派-Azure RBAC
description: 瞭解如何使用 REST API 和 Azure 角色型存取控制（Azure RBAC）來判斷哪些資源的使用者、群組、服務主體或受控識別可以存取。
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
ms.openlocfilehash: 043c91e5c1ecd86951ac3f9423558a3ab369d506
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735669"
---
# <a name="list-azure-role-assignments-using-the-rest-api"></a>使用 REST API 列出 Azure 角色指派

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]本文說明如何使用 REST API 列出角色指派。

> [!NOTE]
> 如果您的組織具有外包管理功能給使用[Azure 委派資源管理](../lighthouse/concepts/azure-delegated-resource-management.md)的服務提供者，該服務提供者所授權的角色指派將不會顯示在這裡。

## <a name="list-role-assignments"></a>列出角色指派

在 Azure RBAC 中，若要列出存取權，您可以列出角色指派。 若要列出角色指派，請使用其中一個[角色指派 - 列出](/rest/api/authorization/roleassignments/list) REST API。 若要精簡您的結果，請指定範圍和選擇性篩選條件。

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

    在上述範例中，microsoft web 是參考 App Service 實例的資源提供者。 同樣地，您可以使用任何其他資源提供者並指定範圍。 如需詳細資訊，請參閱[Azure 資源提供者和類型](../azure-resource-manager/management/resource-providers-and-types.md)和支援的[Azure Resource Manager 資源提供者作業](resource-provider-operations.md)。  
     
1. 將 *{filter}* 取代為您要針對角色指派清單篩選套用的條件。

    > [!div class="mx-tableFixed"]
    > | Filter | 描述 |
    > | --- | --- |
    > | `$filter=atScope()` | 只會列出指定範圍的角色指派，不包括指派的角色指派。 |
    > | `$filter=assignedTo('{objectId}')` | 列出所指定使用者或服務主體的角色指派。<br/>如果使用者是具有角色指派之群組的成員，也會列出該角色指派。 此篩選器可轉移群組，這表示如果使用者是群組的成員，而該群組是具有角色指派之另一個群組的成員，則也會列出該角色指派。<br/>此篩選器只接受使用者或服務主體的物件識別碼。 您無法傳遞群組的物件識別碼。 |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | 列出指定之使用者或服務主體的角色指派，以及指定的範圍。 |
    > | `$filter=principalId+eq+'{objectId}'` | 列出指定之使用者、群組或服務主體的角色指派。 |

## <a name="next-steps"></a>後續步驟

- [使用 REST API 新增或移除 Azure 角色指派](role-assignments-rest.md)
- [Azure REST API 參考](/rest/api/azure/)

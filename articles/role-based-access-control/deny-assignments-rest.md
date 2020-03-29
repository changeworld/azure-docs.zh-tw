---
title: 使用 REST API 列出 Azure 資源的拒絕分配
description: 瞭解如何使用 Azure 資源和 REST API 的基於角色的存取控制 （RBAC） 列出使用者、組和應用程式的拒絕分配。
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 0f648405a3d71bf27c64dacbb3fd78f3e9801137
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063021"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-rest-api"></a>使用 REST API 列出 Azure 資源的拒絕指派

[拒絕分配](deny-assignments.md)會阻止使用者執行特定的 Azure 資源操作，即使角色指派授予他們存取權限也是如此。 本文介紹如何使用 REST API 列出拒絕分配。

> [!NOTE]
> 不能直接創建自己的拒絕分配。 有關如何創建拒絕分配的資訊，請參閱[拒絕分配](deny-assignments.md)。

## <a name="prerequisites"></a>Prerequisites

要獲取有關拒絕分配的資訊，您必須具有：

- `Microsoft.Authorization/denyAssignments/read`許可權，它包含在 Azure[資源的大多數內置角色](built-in-roles.md)中。

## <a name="list-a-single-deny-assignment"></a>列出單一拒絕指派

1. 從下列要求著手：

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. 在 URI 中，將 *{scope}* 取代為您想要列出拒絕指派的範圍。

    > [!div class="mx-tableFixed"]
    > | 影響範圍 | 類型 |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | 訂用帳戶 |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | 資源群組 |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | 資源 |

1. 將 *{deny-assignment-id}* 取代為您想要擷取的拒絕指派識別碼。

## <a name="list-multiple-deny-assignments"></a>列出多個拒絕指派

1. 從下列其中一個要求開始：

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview
    ```

    使用選擇性參數：

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. 在 URI 中，將 *{scope}* 取代為您想要列出拒絕指派的範圍。

    > [!div class="mx-tableFixed"]
    > | 影響範圍 | 類型 |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | 訂用帳戶 |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | 資源群組 |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | 資源 |

1. 將 *{filter}* 取代為您想要套用來篩選拒絕指派清單的條件。

    > [!div class="mx-tableFixed"]
    > | Filter | 描述 |
    > | --- | --- |
    > | (無篩選條件) | 列出所有在指定範圍、上面和下方的拒絕分配。 |
    > | `$filter=atScope()` | 清單僅拒絕指定範圍及以上範圍的分配。 不包含子範圍內的拒絕指派。 |
    > | `$filter=assignedTo('{objectId}')` | 列出指定使用者或服務主體的拒絕分配。<br/>如果使用者是具有拒絕分配組的一個成員，則還會列出該拒絕分配。 此篩選器對於組是傳遞的，這意味著如果使用者是組的成員，並且該組是另一個具有拒絕分配的成員，則還會列出拒絕分配。<br/>此篩選器僅接受使用者或服務主體的物件識別碼。 不能傳遞組的物件識別碼。 |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | 清單拒絕指定使用者或服務主體的分配，並在指定的作用域內。 |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | 列出具有指定名稱的拒絕分配。 |
    > | `$filter=principalId+eq+'{objectId}'` | 列出指定使用者、組或服務主體的拒絕分配。 |

## <a name="list-deny-assignments-at-the-root-scope-"></a>列出根範圍 (/) 內的拒絕指派

1. 以[提高 Azure Active Directory 中全域管理員的存取權](elevate-access-global-admin.md)中所述的方式來提高您的存取權。

1. 使用下列要求：

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. 將 *{filter}* 取代為您想要套用來篩選拒絕指派清單的條件。 需要篩選條件。

    > [!div class="mx-tableFixed"]
    > | Filter | 描述 |
    > | --- | --- |
    > | `$filter=atScope()` | 僅列出根範圍的拒絕指派。 不包含子範圍內的拒絕指派。 |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | 列出具有指定名稱的拒絕指派。 |

1. 移除已提高的存取權。

## <a name="next-steps"></a>後續步驟

- [了解 Azure 資源的拒絕指派](deny-assignments.md)
- [提升 Azure 活動目錄中全域管理員的存取權限](elevate-access-global-admin.md)
- [Azure REST API 參考](/rest/api/azure/)
